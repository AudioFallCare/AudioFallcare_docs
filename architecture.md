# AudioFallCare 아키텍처 설계서

> 소리 기반 낙상 감지 및 보호자 알림 시스템

---

## 1. 시스템 개요

### 1.1 프로젝트 목적
노약자 및 환자가 있는 공간에서 마이크를 통해 실시간으로 소리를 수집하고, AI가 낙상을 감지하면 보호자에게 즉시 알림을 전송하는 시스템

### 1.2 주요 기능
| 기능 | 설명 |
|------|------|
| 실시간 오디오 스트리밍 | 리코더에서 서버로 실시간 오디오 전송 |
| AI 낙상 감지 | CNN 기반 오디오 분류 모델로 낙상 판단 |
| 실시간 알림 | 낙상 감지 시 보호자에게 즉시 푸시 알림 |
| 연결 코드 시스템 | 보호자-리코더 간 안전한 연결 |
| 119 신고 연동 | 긴급 상황 시 119 신고 기능 |
| 이력 관리 | 낙상 감지 이력 조회 및 통계 |

---

## 2. 시스템 아키텍처

### 2.1 전체 아키텍처
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              Client Layer                                    │
├───────────────────────────────────┬─────────────────────────────────────────┤
│        Recorder Client            │           Guardian Web App              │
│      (React + Web Audio API)      │         (React + JavaScript)            │
│                                   │                                         │
│  • 마이크 입력 캡처                │  • 로그인/회원가입                        │
│  • 실시간 오디오 스트리밍           │  • 연결 코드 발급                         │
│  • 연결 코드 입력                  │  • 실시간 알림 수신                       │
│  • 연결 상태 표시                  │  • 낙상 이력 조회                         │
│                                   │  • 119 신고                              │
└───────────────┬───────────────────┴────────────────────┬────────────────────┘
                │                                        │
                │ WebSocket                              │ HTTPS (REST API)
                │ (Audio Stream)                         │ + WebSocket (알림)
                ▼                                        ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              API Gateway                                     │
│                           (Spring Cloud Gateway)                             │
│                                                                             │
│  • 라우팅 • 로드밸런싱 • Rate Limiting • CORS                                 │
└───────────────────────────────────┬─────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                            Backend Layer                                     │
│                        Spring Boot 3.x (Java 17)                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   Auth      │  │   Audio     │  │   Alert     │  │  Recorder   │        │
│  │  Service    │  │  Service    │  │  Service    │  │  Service    │        │
│  │             │  │             │  │             │  │             │        │
│  │ • JWT 발급  │  │ • 스트림수신│  │ • 알림 생성 │  │ • 코드 관리 │        │
│  │ • 토큰 검증 │  │ • AI 연동   │  │ • 푸시 전송 │  │ • 기기 관리 │        │
│  │ • 회원 관리 │  │ • 결과 처리 │  │ • 이력 저장 │  │ • 상태 관리 │        │
│  └─────────────┘  └──────┬──────┘  └─────────────┘  └─────────────┘        │
│                          │                                                  │
└──────────────────────────┼──────────────────────────────────────────────────┘
                           │ HTTP (REST)
                           ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              AI Layer                                        │
│                         FastAPI (Python 3.11)                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐             │
│  │   Preprocess    │  │   Inference     │  │   Model         │             │
│  │                 │  │                 │  │   Manager       │             │
│  │ • 리샘플링      │  │ • 낙상 분류     │  │                 │             │
│  │ • Mel Spectro   │  │ • 신뢰도 계산   │  │ • 모델 로드     │             │
│  │ • 정규화        │  │ • 결과 반환     │  │ • 버전 관리     │             │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘             │
│                                                                             │
│                        CNN Model (PyTorch)                                  │
│                        F1 Score: 96.55%                                     │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                            Data Layer                                        │
├──────────────────────────────┬──────────────────────────────────────────────┤
│          MySQL 8.0           │              Redis 7.0                       │
│                              │                                              │
│  • 사용자 정보               │  • JWT 토큰 저장                             │
│  • 리코더 정보               │  • 세션 캐시                                 │
│  • 연결 코드                 │  • Rate Limiting                            │
│  • 알림 이력                 │  • 실시간 연결 상태                          │
│  • 낙상 감지 로그            │                                              │
└──────────────────────────────┴──────────────────────────────────────────────┘
```

### 2.2 배포 아키텍처
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              Cloud (AWS / NCP)                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────┐     ┌─────────────────────────────────────────────────┐   │
│  │   Route53   │────▶│              Load Balancer (ALB)                │   │
│  │   (DNS)     │     └─────────────────────┬───────────────────────────┘   │
│  └─────────────┘                           │                               │
│                                            ▼                               │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                        ECS / Kubernetes                             │   │
│  │  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐           │   │
│  │  │   Frontend    │  │   Backend     │  │   AI Server   │           │   │
│  │  │   Container   │  │   Container   │  │   Container   │           │   │
│  │  │   (Nginx)     │  │   (Spring)    │  │   (FastAPI)   │           │   │
│  │  │   x 2         │  │   x 3         │  │   x 2         │           │   │
│  │  └───────────────┘  └───────────────┘  └───────────────┘           │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                            │                               │
│                                            ▼                               │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                         Data Services                               │   │
│  │  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐           │   │
│  │  │   RDS         │  │   ElastiCache │  │   S3          │           │   │
│  │  │   (MySQL)     │  │   (Redis)     │  │   (Storage)   │           │   │
│  │  └───────────────┘  └───────────────┘  └───────────────┘           │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. 기술 스택

### 3.1 Frontend
| 기술 | 버전 | 용도 |
|------|------|------|
| React | 18.x | UI 프레임워크 |
| JavaScript | ES6+ | 클라이언트 로직 |
| Vite | 5.x | 빌드 도구 |
| React Router | 6.x | 클라이언트 라우팅 |
| Axios | 1.x | HTTP 클라이언트 |
| Socket.io Client | 4.x | WebSocket 통신 |
| Web Audio API | - | 실시간 오디오 처리 |
| Zustand | 4.x | 상태 관리 |
| Tailwind CSS | 3.x | 스타일링 |

### 3.2 Backend
| 기술 | 버전 | 용도 |
|------|------|------|
| Java | 17 LTS | 백엔드 언어 |
| Spring Boot | 3.2.x | 웹 프레임워크 |
| Spring Security | 6.x | 인증/인가 |
| Spring WebSocket | - | 실시간 통신 |
| Spring Data JPA | - | ORM |
| JWT (jjwt) | 0.12.x | 토큰 기반 인증 |
| Gradle | 8.x | 빌드 도구 |
| Lombok | - | 보일러플레이트 감소 |
| MapStruct | - | DTO 매핑 |

### 3.3 AI Server
| 기술 | 버전 | 용도 |
|------|------|------|
| Python | 3.11 | AI 서버 언어 |
| FastAPI | 0.109.x | API 프레임워크 |
| PyTorch | 2.x | 딥러닝 프레임워크 |
| librosa | 0.10.x | 오디오 처리 |
| NumPy | 1.26.x | 수치 연산 |
| Uvicorn | 0.27.x | ASGI 서버 |
| Pydantic | 2.x | 데이터 검증 |

### 3.4 Database
| 기술 | 버전 | 용도 |
|------|------|------|
| MySQL | 8.0 | 메인 데이터베이스 |
| Redis | 7.0 | 캐시/세션 저장소 |

### 3.5 Infrastructure
| 기술 | 용도 |
|------|------|
| Docker | 컨테이너화 |
| Docker Compose | 로컬 개발 환경 |
| GitHub Actions | CI/CD |
| Nginx | 리버스 프록시 |
| AWS / NCP | 클라우드 호스팅 |

---

## 4. API 설계

### 4.1 인증 API
| Method | Endpoint | 설명 | 인증 |
|--------|----------|------|------|
| POST | `/api/auth/signup` | 회원가입 | X |
| POST | `/api/auth/login` | 로그인 (JWT 발급) | X |
| POST | `/api/auth/refresh` | 토큰 갱신 | O |
| POST | `/api/auth/logout` | 로그아웃 | O |
| GET | `/api/auth/me` | 내 정보 조회 | O |

### 4.2 연결 코드 API
| Method | Endpoint | 설명 | 인증 |
|--------|----------|------|------|
| POST | `/api/code/generate` | 연결 코드 발급 | O |
| POST | `/api/code/verify` | 연결 코드 검증 | X |
| DELETE | `/api/code/{codeId}` | 연결 코드 삭제 | O |

### 4.3 리코더 API
| Method | Endpoint | 설명 | 인증 |
|--------|----------|------|------|
| GET | `/api/recorders` | 연결된 리코더 목록 | O |
| DELETE | `/api/recorders/{id}` | 리코더 연결 해제 | O |
| GET | `/api/recorders/{id}/status` | 리코더 상태 조회 | O |

### 4.4 알림 API
| Method | Endpoint | 설명 | 인증 |
|--------|----------|------|------|
| GET | `/api/alerts` | 알림 목록 조회 | O |
| PATCH | `/api/alerts/{id}/read` | 알림 읽음 처리 | O |
| GET | `/api/alerts/unread/count` | 읽지 않은 알림 수 | O |

### 4.5 이력 API
| Method | Endpoint | 설명 | 인증 |
|--------|----------|------|------|
| GET | `/api/history` | 낙상 이력 조회 | O |
| GET | `/api/history/{id}` | 이력 상세 조회 | O |
| GET | `/api/history/stats` | 통계 조회 | O |

### 4.6 신고 API
| Method | Endpoint | 설명 | 인증 |
|--------|----------|------|------|
| POST | `/api/report/119` | 119 신고 요청 | O |

### 4.7 WebSocket
| Endpoint | 설명 | 인증 |
|----------|------|------|
| `/ws/audio/stream` | 오디오 스트리밍 (리코더→서버) | 코드 기반 |
| `/ws/notifications` | 실시간 알림 (서버→보호자) | JWT |

### 4.8 AI Server API
| Method | Endpoint | 설명 |
|--------|----------|------|
| POST | `/ai/predict` | 낙상 감지 요청 |
| GET | `/ai/health` | 서버 상태 확인 |
| GET | `/ai/model/info` | 모델 정보 조회 |

---

## 5. 데이터베이스 설계

### 5.1 ERD
```
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│     users       │       │    recorders    │       │  connect_codes  │
├─────────────────┤       ├─────────────────┤       ├─────────────────┤
│ id (PK)         │──┐    │ id (PK)         │       │ id (PK)         │
│ email           │  │    │ user_id (FK)    │──┐    │ user_id (FK)    │──┐
│ password        │  │    │ name            │  │    │ code            │  │
│ name            │  │    │ status          │  │    │ expires_at      │  │
│ phone           │  │    │ last_active_at  │  │    │ used            │  │
│ created_at      │  │    │ created_at      │  │    │ created_at      │  │
│ updated_at      │  │    │ updated_at      │  │    └────────┬────────┘  │
└────────┬────────┘  │    └────────┬────────┘                │           │
         │           │             │                         │           │
         │           └─────────────┼─────────────────────────┘           │
         │                         │                                     │
         │    ┌────────────────────┘                                     │
         │    │                                                          │
         ▼    ▼                                                          │
┌─────────────────┐       ┌─────────────────┐                           │
│     alerts      │       │  fall_history   │                           │
├─────────────────┤       ├─────────────────┤                           │
│ id (PK)         │       │ id (PK)         │                           │
│ user_id (FK)    │◀──────│ user_id (FK)    │◀──────────────────────────┘
│ recorder_id(FK) │       │ recorder_id(FK) │
│ type            │       │ confidence      │
│ message         │       │ sound_type      │
│ is_read         │       │ audio_url       │
│ created_at      │       │ detected_at     │
└─────────────────┘       │ created_at      │
                          └─────────────────┘
```

### 5.2 테이블 명세

#### users (사용자)
| 컬럼 | 타입 | 제약조건 | 설명 |
|------|------|----------|------|
| id | BIGINT | PK, AUTO_INCREMENT | 사용자 ID |
| email | VARCHAR(255) | UNIQUE, NOT NULL | 이메일 |
| password | VARCHAR(255) | NOT NULL | 비밀번호 (BCrypt) |
| name | VARCHAR(100) | NOT NULL | 이름 |
| phone | VARCHAR(20) | NOT NULL | 전화번호 |
| created_at | DATETIME | NOT NULL | 생성일시 |
| updated_at | DATETIME | NOT NULL | 수정일시 |

#### recorders (리코더)
| 컬럼 | 타입 | 제약조건 | 설명 |
|------|------|----------|------|
| id | BIGINT | PK, AUTO_INCREMENT | 리코더 ID |
| user_id | BIGINT | FK, NOT NULL | 보호자 ID |
| name | VARCHAR(100) | NOT NULL | 리코더 이름 |
| status | ENUM | NOT NULL | CONNECTED, DISCONNECTED |
| last_active_at | DATETIME | NULL | 마지막 활성 시간 |
| created_at | DATETIME | NOT NULL | 생성일시 |
| updated_at | DATETIME | NOT NULL | 수정일시 |

#### connect_codes (연결 코드)
| 컬럼 | 타입 | 제약조건 | 설명 |
|------|------|----------|------|
| id | BIGINT | PK, AUTO_INCREMENT | 코드 ID |
| user_id | BIGINT | FK, NOT NULL | 보호자 ID |
| code | VARCHAR(10) | UNIQUE, NOT NULL | 연결 코드 |
| expires_at | DATETIME | NOT NULL | 만료일시 |
| used | BOOLEAN | DEFAULT FALSE | 사용 여부 |
| created_at | DATETIME | NOT NULL | 생성일시 |

#### alerts (알림)
| 컬럼 | 타입 | 제약조건 | 설명 |
|------|------|----------|------|
| id | BIGINT | PK, AUTO_INCREMENT | 알림 ID |
| user_id | BIGINT | FK, NOT NULL | 보호자 ID |
| recorder_id | BIGINT | FK, NOT NULL | 리코더 ID |
| type | ENUM | NOT NULL | FALL, WARNING, INFO |
| message | VARCHAR(500) | NOT NULL | 알림 메시지 |
| is_read | BOOLEAN | DEFAULT FALSE | 읽음 여부 |
| created_at | DATETIME | NOT NULL | 생성일시 |

#### fall_history (낙상 이력)
| 컬럼 | 타입 | 제약조건 | 설명 |
|------|------|----------|------|
| id | BIGINT | PK, AUTO_INCREMENT | 이력 ID |
| user_id | BIGINT | FK, NOT NULL | 보호자 ID |
| recorder_id | BIGINT | FK, NOT NULL | 리코더 ID |
| confidence | FLOAT | NOT NULL | 신뢰도 (0~1) |
| sound_type | VARCHAR(50) | NOT NULL | 소리 유형 |
| audio_url | VARCHAR(500) | NULL | 오디오 파일 URL |
| detected_at | DATETIME | NOT NULL | 감지 시간 |
| created_at | DATETIME | NOT NULL | 생성일시 |

---

## 6. 보안 설계

### 6.1 인증/인가
```
┌─────────────────────────────────────────────────────────────────────┐
│                        JWT 인증 흐름                                │
└─────────────────────────────────────────────────────────────────────┘

1. 로그인 요청
   Client ──────────────────────────────────────────────▶ Server
           POST /api/auth/login
           { email, password }

2. JWT 토큰 발급
   Client ◀────────────────────────────────────────────── Server
           {
             accessToken: "eyJhbG...",    (유효기간: 30분)
             refreshToken: "eyJhbG..."    (유효기간: 7일)
           }

3. API 요청 (인증 필요)
   Client ──────────────────────────────────────────────▶ Server
           GET /api/alerts
           Authorization: Bearer eyJhbG...

4. 토큰 만료 시 갱신
   Client ──────────────────────────────────────────────▶ Server
           POST /api/auth/refresh
           { refreshToken: "eyJhbG..." }
```

### 6.2 보안 정책
| 항목 | 정책 |
|------|------|
| 비밀번호 | BCrypt 해시 (강도 12) |
| Access Token | 유효기간 30분 |
| Refresh Token | 유효기간 7일, Redis 저장 |
| HTTPS | TLS 1.3 필수 |
| CORS | 허용 도메인 화이트리스트 |
| Rate Limiting | 100 req/min per IP |
| SQL Injection | JPA Parameterized Query |
| XSS | React 자동 이스케이프 |

### 6.3 연결 코드 보안
- 6자리 영숫자 조합 (대문자 + 숫자)
- 유효기간: 24시간
- 1회 사용 후 만료
- 브루트포스 방지: 5회 실패 시 10분 잠금

---

## 7. AI 모델 설계

### 7.1 모델 아키텍처
```
Input: Mel Spectrogram (1, 64, 94)
           │
           ▼
┌─────────────────────────┐
│ Conv2d(1, 32, 3x3)      │
│ BatchNorm2d + ReLU      │
│ MaxPool2d(2x2)          │
│ Dropout(0.2)            │
└───────────┬─────────────┘
            ▼
┌─────────────────────────┐
│ Conv2d(32, 64, 3x3)     │
│ BatchNorm2d + ReLU      │
│ MaxPool2d(2x2)          │
│ Dropout(0.2)            │
└───────────┬─────────────┘
            ▼
┌─────────────────────────┐
│ Conv2d(64, 128, 3x3)    │
│ BatchNorm2d + ReLU      │
│ MaxPool2d(2x2)          │
│ Dropout(0.3)            │
└───────────┬─────────────┘
            ▼
┌─────────────────────────┐
│ Conv2d(128, 256, 3x3)   │
│ BatchNorm2d + ReLU      │
│ AdaptiveAvgPool2d(1,1)  │
└───────────┬─────────────┘
            ▼
┌─────────────────────────┐
│ Flatten                 │
│ Linear(256, 128)        │
│ ReLU + Dropout(0.5)     │
│ Linear(128, 1)          │
│ Sigmoid                 │
└───────────┬─────────────┘
            ▼
Output: 낙상 확률 (0~1)
```

### 7.2 오디오 전처리
| 단계 | 설명 | 파라미터 |
|------|------|----------|
| 1. 로드 | 오디오 파일 로드 | - |
| 2. 리샘플링 | 16kHz로 변환 | sample_rate=16000 |
| 3. 모노 변환 | 스테레오→모노 | mono=True |
| 4. 길이 조정 | 3초로 패딩/자르기 | max_length=3s |
| 5. Mel Spectrogram | 시간-주파수 변환 | n_mels=64, n_fft=1024 |
| 6. dB 변환 | Power to dB | ref=np.max |
| 7. 정규화 | 평균 0, 표준편차 1 | - |

### 7.3 모델 성능
| 메트릭 | 값 |
|--------|-----|
| F1 Score | 96.55% |
| Accuracy | 96.84% |
| Precision | 96.55% |
| Recall | 96.55% |
| 학습 데이터 | SAFE Dataset (950 samples) |

---

## 8. 실시간 처리 흐름

### 8.1 오디오 스트리밍 흐름
```
┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
│ Recorder│    │ Backend │    │   AI    │    │ Backend │    │Guardian │
│ Client  │    │ Server  │    │ Server  │    │ Server  │    │  App    │
└────┬────┘    └────┬────┘    └────┬────┘    └────┬────┘    └────┬────┘
     │              │              │              │              │
     │  WebSocket   │              │              │              │
     │  Connect     │              │              │              │
     │─────────────▶│              │              │              │
     │              │              │              │              │
     │  Audio       │              │              │              │
     │  Chunk (3s)  │              │              │              │
     │─────────────▶│              │              │              │
     │              │  POST        │              │              │
     │              │  /ai/predict │              │              │
     │              │─────────────▶│              │              │
     │              │              │              │              │
     │              │  {fall:true, │              │              │
     │              │  conf:0.95}  │              │              │
     │              │◀─────────────│              │              │
     │              │              │              │              │
     │              │         [낙상 감지됨]        │              │
     │              │              │              │              │
     │              │──────────────────────────────────────────▶│
     │              │                    WebSocket Push         │
     │              │                    "낙상이 감지되었습니다" │
     │              │              │              │              │
```

### 8.2 알림 우선순위
| 우선순위 | 신뢰도 | 알림 방식 |
|----------|--------|-----------|
| 긴급 | 90% 이상 | 푸시 + 소리 + 진동 |
| 경고 | 70~90% | 푸시 + 진동 |
| 주의 | 50~70% | 푸시만 |

---

## 9. 에러 처리

### 9.1 에러 응답 형식
```json
{
  "success": false,
  "error": {
    "code": "AUTH_001",
    "message": "인증 토큰이 만료되었습니다.",
    "timestamp": "2025-01-28T12:00:00Z"
  }
}
```

### 9.2 에러 코드
| 코드 | HTTP Status | 설명 |
|------|-------------|------|
| AUTH_001 | 401 | 토큰 만료 |
| AUTH_002 | 401 | 유효하지 않은 토큰 |
| AUTH_003 | 403 | 권한 없음 |
| CODE_001 | 400 | 잘못된 연결 코드 |
| CODE_002 | 400 | 만료된 연결 코드 |
| AI_001 | 503 | AI 서버 연결 실패 |
| AI_002 | 500 | 모델 추론 실패 |

---

## 10. 모니터링

### 10.1 로깅
| 레벨 | 용도 |
|------|------|
| ERROR | 시스템 오류, AI 추론 실패 |
| WARN | 인증 실패, 연결 코드 오류 |
| INFO | API 요청, 낙상 감지 |
| DEBUG | 상세 처리 과정 |

### 10.2 메트릭
| 메트릭 | 설명 |
|--------|------|
| api_request_total | API 요청 수 |
| api_request_duration | API 응답 시간 |
| websocket_connections | WebSocket 연결 수 |
| ai_inference_duration | AI 추론 시간 |
| fall_detection_total | 낙상 감지 횟수 |

### 10.3 알림
| 조건 | 알림 |
|------|------|
| API 응답시간 > 3s | Slack 알림 |
| AI 서버 다운 | 긴급 알림 |
| 에러율 > 5% | Slack 알림 |

---

## 11. 버전 관리

### 11.1 API 버전
- URL 경로 방식: `/api/v1/...`
- 하위 호환성 유지 원칙

### 11.2 모델 버전
- 시맨틱 버전: `v1.0.0`
- 모델 파일명: `model_v1.0.0.pt`
- 롤백 가능한 구조

---

## 12. 향후 확장 계획

| 단계 | 내용 |
|------|------|
| Phase 1 | 현재 구현 (낙상/비낙상 2분류) |
| Phase 2 | 다중 분류 (낙상, 비명, 도움요청, 일반) |
| Phase 3 | 모바일 앱 (React Native) |
| Phase 4 | 음성 인식 연동 ("도와주세요" 감지) |
| Phase 5 | IoT 센서 연동 (움직임 감지) |

---

*문서 버전: 1.0*
*최종 수정: 2025-01-28*
