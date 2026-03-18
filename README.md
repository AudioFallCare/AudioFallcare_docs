# 🛡️ 낙상 지킴이 (AudioFallCare)

> **소리 기반 낙상 감지 및 보호자 알림 시스템**  
> 소중한 사람을 낙상 사고로부터 지키세요

![낙상 지킴이 소개](https://github.com/FAITRUEE/AudioFallcare_docs/blob/docs/PPT/images/PPT_1.png?raw=true)

---

## 🚀 배포 주소

| 서비스 | 링크 |
|--------|------|
| 🌐 프론트엔드 웹앱 | [audio-fall-care-web.vercel.app](https://audio-fall-care-web.vercel.app/) |
| 📄 백엔드 Swagger | [audiofallcare-was-test.onrender.com](https://audiofallcare-was-test.onrender.com/swagger-ui/index.html#/) |
| 🤖 AI 서버 | [audiofallcare-ai-test.onrender.com](https://audiofallcare-ai-test.onrender.com/ai/) |

---

## 👥 팀 멤버

<table>
  <tr>
    <td align="center">
      <a href="https://github.com/chominseo0723">
        <img src="https://github.com/chominseo0723.png" width="100px;" alt="chominseo0723"/><br />
        <sub><b>chominseo0723</b></sub>
      </a>
    </td>
    <td align="center">
      <a href="https://github.com/FAITRUEE">
        <img src="https://github.com/FAITRUEE.png" width="100px;" alt="FAITRUEE"/><br />
        <sub><b>FAITRUEE</b></sub>
      </a>
    </td>
    <td align="center">
      <a href="https://github.com/LENA2610">
        <img src="https://github.com/LENA2610.png" width="100px;" alt="LENA2610"/><br />
        <sub><b>LENA2610</b></sub>
      </a>
    </td>
    <td align="center">
      <a href="https://github.com/ming920">
        <img src="https://github.com/ming920.png" width="100px;" alt="ming920"/><br />
        <sub><b>ming920</b></sub>
      </a>
    </td>
    <td align="center">
      <a href="https://github.com/ojm6135">
        <img src="https://github.com/ojm6135.png" width="100px;" alt="ojm6135"/><br />
        <sub><b>ojm6135</b></sub>
      </a>
    </td>
  </tr>
</table>

---

## 📌 문제 정의

### 고령자 안전사고 현황

고령자가 증가함에 따라 고령자 안전사고도 증가하고 있으며, 그 중 가장 많은 사고는 **낙상사고**입니다.

![고령자 안전사고 현황](https://github.com/FAITRUEE/AudioFallcare_docs/blob/docs/PPT/images/PPT_2.png?raw=true)

- 65세 이상 고령자 안전사고 매년 증가 (2014년 4,453건 → 2016년 5,795건)
- 안전사고의 **47.4%가 낙상사고** (2,746건)

| 시기 | 고령인구 비중 | 사회 구분 |
|------|-------------|----------|
| 2000년 11월 | 7.31% | 고령화사회 |
| 2017년 8월 | 14.02% | 고령사회 |
| 2024년 12월 | 20% | **초고령사회** |

### ⏱️ 낙상사고는 시간이 생존을 좌우합니다

![골든타임](https://github.com/FAITRUEE/AudioFallcare_docs/blob/docs/PPT/images/PPT_3.png?raw=true)

| 시간 | 상태 | 설명 |
|------|------|------|
| **0~30분 (골든타임)** | 생존율 90% 이상 | 빠른 구조 필수 |
| **30분~1시간** | 위험 신호 시작 | 저체온증 및 합병증 발생 |
| **1~6시간** | 생명 위독 | 횡문근융해증, 장기 손상 |
| **6시간 이후** | 생존율 50% 미만 | 회복 불가능한 손상 |

---

## 💡 주요 기능

![주요 기능](https://github.com/FAITRUEE/AudioFallcare_docs/blob/docs/PPT/images/PPT_4.png?raw=true)

### 🔗 보호자-피보호자 연동
- 리코더 코드를 통한 간편 연동
- 보호자가 코드 발급 → 피보호자 기기에 입력 → 즉시 연결

### 🎙️ 실시간 낙상 감지
- 마이크를 통해 소리를 수집하고 AI가 낙상 여부를 실시간 분석
- 보호자는 소리를 통해 피보호자의 낙상 발생 여부를 즉시 확인

### 🔔 낙상 사고 발생 알림
- 낙상 감지 시 보호자가 버튼을 누르면 피보호자 기기로 지속적 알림 전송
- 피보호자가 직접 알림을 종료하여 정상 상태 여부 전달 가능
- 피보호자의 응답 여부로 실제 사고 / 오탐 판별 가능

### 📋 사고 이력 관리

![사고 이력 관리](https://github.com/FAITRUEE/AudioFallcare_docs/blob/docs/PPT/images/PPT_5.png?raw=true)

- 낙상 의심 날짜·시간 자동 기록
- 위험도 함께 제공 → **위험도 70% 이상** 시 보호자에게 자동 알림

| 날짜 | 감지된 시간 | 위험도 |
|------|-----------|--------|
| 2026.01.27 | 16:21 | 97% 🔴 |
| 2026.01.26 | 08:58 | 40% 🟡 |
| 2026.01.01 | 01:11 | 20% 🟢 |

---

## 🏆 차별성

![차별성 비교](https://github.com/FAITRUEE/AudioFallcare_docs/blob/docs/PPT/images/PPT_6.png?raw=true)

| 항목 | 주식회사 엔티에스 | 사이렌 케어 | 낙상 지킴이 |
|------|:--------------:|:---------:|:---------:|
| 사생활 보호 | ❌ | 🔺 | ✅ |
| 공간 적응성 | ❌ | ❌ | ✅ |
| 소리 인식 | ❌ | ❌ | ✅ |
| 장기 사용 적합성 | ❌ | ❌ | ✅ |
| 사용자 피드백 기반 적응형 학습 구조 | ❌ | ❌ | ✅ |

---

## 🏗️ 시스템 구성

![시스템 구성](https://github.com/FAITRUEE/AudioFallcare_docs/blob/docs/PPT/images/PPT_7.png?raw=true)

본 시스템은 **리코더 클라이언트 웹앱**과 **보호자 웹앱**으로 구성됩니다.

- **리코더 클라이언트**: 노약자가 있는 공간에서 동작하는 웹앱. 마이크를 통해 소리를 수집하고 서버로 전송합니다.
- **보호자 웹앱**: 보호자가 리코더 연결 코드를 발급하고, 낙상 알림을 확인하는 웹앱입니다.

```
┌──────────────────────┐                    ┌──────────────────────┐
│  👨‍👩‍👧 보호자 웹앱       │                    │  📱 리코더 클라이언트  │
│  (보호자 스마트폰)     │                    │  (노약자 공간)        │
├──────────────────────┤                    ├──────────────────────┤
│  1. 로그인            │                    │                      │
│  2. 리코더 추가        │      코드 전달      │  3. 코드 입력 (최초 1회) │
│     [코드 발급]       │  ───────────────▶  │  ┌────────────┐      │
│  ┌────────────────┐  │   (전화/문자)       │  │  ABC123    │      │
│  │  코드: ABC123  │  │                    │  └────────────┘      │
│  └────────────────┘  │                    │  [연결하기]           │
└──────────────────────┘                    └──────────────────────┘
          │                                            │
          └────────────────────┬───────────────────────┘
                               ▼
          ┌────────────────────────────────────────────┐
          │                  🖥️ 서버                   │
          │  오디오 수신 → AI 분석 → 낙상 감지 → 알림   │
          └────────────────────────────────────────────┘
```

---

## ⚙️ 작동 방식

### 1단계: 보호자가 코드 발급
1. 보호자가 회원가입 / 로그인
2. "리코더 추가" 클릭 후 별칭 입력 (예: "할머니 집")
3. **영구 연결 코드 발급** → 설치자에게 전달

### 2단계: 리코더에서 코드 입력
1. 노약자 공간에 노트북 / 태블릿 설치
2. 브라우저로 리코더 웹앱 접속
3. 전달받은 **연결 코드 입력** (최초 1회만)
4. 자동으로 모니터링 시작 / 재시작 시 자동 재연결

### 3단계: 실시간 모니터링
- WebSocket으로 오디오 실시간 전송
- 연결 끊김 시 자동 재연결

### 4단계: 낙상 감지 알림
```
🔴 할머니 집에서 낙상이 감지되었습니다

시간: 2026-01-27 14:32:05
신뢰도: 87%
소리 유형: 충격음

[확인]   [119 신고]
```

---

## 🤖 AI 분류

낙상 감지 시 다음 정보를 제공합니다:

```json
{
  "fall": true,
  "confidence": 0.87,
  "soundType": "Impact"
}
```

| 항목 | 설명 |
|------|------|
| `fall` | 낙상 여부 |
| `confidence` | 신뢰도 (0.0 ~ 1.0) |
| `soundType` | 소리 유형 (Impact, Scream, Thud 등) |

---

## 📡 API 구조

| 구분 | 엔드포인트 | 인증 | 설명 |
|------|-----------|:----:|------|
| 보호 | `POST /api/code/generate` | ✅ | 연결 코드 발급 |
| 공개 | `POST /api/code/verify` | ❌ | 연결 코드 검증 |
| 공개 | `WS /ws/audio/stream` | ❌ | 실시간 오디오 스트리밍 |
| 보호 | `GET /api/recorders` | ✅ | 연결된 리코더 목록 |
| 보호 | `GET /api/alerts` | ✅ | 알림 목록 조회 |
| 보호 | `GET /api/history` | ✅ | 낙상 이력 조회 |
| 보호 | `POST /api/report` | ✅ | 119 신고 요청 |

---

## 🛠️ 기술 스택

![기술스택](https://github.com/FAITRUEE/AudioFallcare_docs/blob/docs/PPT/images/PPT_8.png?raw=true)

**리코더 클라이언트**
- Web Audio API — 마이크 입력 처리
- WebSocket — 실시간 서버 전송

**서버**
- WebSocket — 오디오 수신
- AI 모델 — 낙상 분류
- DB — 코드 / 연결 정보 관리

**보호자 웹앱**
- 로그인 / 회원가입
- 실시간 알림

---

## 📁 디렉토리 구조

```
AudioFallCare/
├── README.md
├── docs/                # 문서
├── ai/                  # AI 모델
│   ├── model/           # 학습된 모델
│   └── preprocessing/   # 데이터 전처리
├── server/              # 백엔드 서버
│   ├── api/             # API 엔드포인트
│   ├── websocket/       # WebSocket 핸들러
│   └── services/        # 비즈니스 로직
└── client/              # 통합 프론트엔드
    ├── src/
    │   ├── pages/
    │   │   ├── Entry/       # 진입 화면 (로그인 + 코드입력)
    │   │   ├── Guardian/    # 보호자 웹앱
    │   │   └── Recorder/    # 리코더 클라이언트
    │   └── components/      # 공통 컴포넌트
    └── public/
```

---

## 🔐 연결 코드 보안

| 항목 | 설명 |
|------|------|
| 코드 생성 | 보호자 요청 시 서버에서 생성 |
| 코드 저장 | 서버 DB + 리코더 localStorage |
| 유효 시간 | 영구 (만료 없음) |
| 검증 | 서버에서 검증 |
| 재시작 시 | 저장된 코드로 자동 연결 |

---

## 📄 라이선스

> TBD

---

*Last Updated: 2026-01-27*
