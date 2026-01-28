# GitHub 워크플로우 가이드

AudioFallCare 프로젝트의 GitHub Actions 자동화 워크플로우 가이드입니다.

## 적용된 레포지토리

| 레포지토리 | 설명 |
|-----------|------|
| [AudioFallCare_web](https://github.com/AudioFallCare/AudioFallCare_web) | 프론트엔드 (React + Vite) |
| [AudioFallCare_was](https://github.com/AudioFallCare/AudioFallCare_was) | 백엔드 (Spring Boot) |
| [AudioFallcare_docs](https://github.com/AudioFallCare/AudioFallcare_docs) | 문서 |

---

## 1. CI (Continuous Integration)

### 트리거
- `main`, `develop` 브랜치에 push 또는 PR 시 실행
- 소스 파일 변경 시에만 실행 (`.github/` 폴더만 변경 시 스킵)

### 프론트엔드 (web)
```
npm ci → npm run lint → npm run build → npm run test
```

### 백엔드 (was)
```
./gradlew build → ./gradlew test
```

### 문서 (docs)
```
마크다운 링크 검사
```

---

## 2. PR 자동 라벨링

PR 제목에 prefix를 붙이면 자동으로 라벨이 추가됩니다.

### 사용 가능한 라벨

| Prefix | 라벨 | 설명 | 색상 |
|--------|------|------|------|
| `feat:` | feat | 새로운 기능 추가 | 🟣 |
| `fix:` | fix | 버그 수정 | 🔴 |
| `docs:` | docs | 문서 수정 | 🔵 |
| `style:` | style | 스타일만 변경 | 🟡 |
| `refactor:` | refactor | 코드 리팩토링 | 🟠 |
| `test:` | test | 테스트 관련 | 🔵 |
| `chore:` | chore | 설정 변경 | 🟣 |
| `ci:` | ci | CI 설정 수정 | 🟡 |
| `rename:` | rename | 파일/폴더명 수정 | 🟢 |
| `remove:` | remove | 파일 삭제 | 🔴 |
| `design:` | design | UI 디자인 변경 | 🩷 |
| `build:` | build | 빌드 파일 수정 | 🟢 |
| `perf:` | perf | 성능 개선 | 🔵 |

### 예시
```
feat: 로그인 기능 추가
fix: 버튼 클릭 버그 수정
docs(readme): README 업데이트
refactor: 코드 구조 개선
```

---

## 3. PR 자동 리뷰

PR이 생성되면 자동으로 리뷰 코멘트가 달립니다.

### 리뷰 내용
- 변경된 파일 수
- 추가/삭제된 줄 수
- 파일 유형별 분류 (컴포넌트, 서비스, 테스트 등)
- 주의사항 (변경량 초과, 테스트 누락 등)

### 예시 출력
```
## 📋 PR 자동 리뷰

### 📊 변경 사항 요약
- 📁 변경된 파일: 5개
- ➕ 추가된 줄: 120줄
- ➖ 삭제된 줄: 30줄

### 📂 변경된 파일 분류
- 🧩 컴포넌트: 2개
- 🧪 테스트: 1개
- 📦 기타: 2개

### ⚠️ 주의사항
💡 컴포넌트 변경이 있지만 테스트 파일이 없습니다.
```

---

## 4. PR 템플릿

PR 생성 시 자동으로 템플릿이 표시됩니다.

### 프론트엔드 템플릿
- PR 요약
- 변경 내용
- 스크린샷 (Before/After)
- 관련 이슈

### 백엔드 템플릿
- PR 요약
- 변경 내용
- API 변경 사항
- DB 변경 사항
- 관련 이슈

### 문서 템플릿
- PR 요약
- 변경 내용
- 문서 유형
- 관련 이슈

---


## 참고

- 모든 워크플로우는 `.github/workflows/` 폴더에 있습니다
- PR 템플릿은 `.github/PULL_REQUEST_TEMPLATE.md`에 있습니다
- 문의사항은 Issue로 남겨주세요
