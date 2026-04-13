# 현솔의 사진 콘테스트 — Claude Code 작업 프롬프트

> 이 파일을 Claude Code 세션 시작 시 컨텍스트로 제공하세요.
> 예: `claude "아래 프롬프트를 읽고 작업을 시작해줘" < CLAUDE_PROMPT.md`

---

## 프로젝트 컨텍스트

나는 서울 산책 투어 가이드 '현솔'이야.  
서울의 숨겨진 골목길·명소를 사람들에게 안내하는 투어를 운영하고 있어.  
투어 참여자들이 사진 미션을 수행하고, 서로 교류하며, 상품도 받아가는  
**"현솔의 사진 콘테스트"** 웹앱을 운영 중이야.

---

## 현재 앱 상태

### 배포 파일
- `hyunsol-photo-contest.html` — 단일 HTML 파일, 서버 불필요

### 기술 스택
- **프론트엔드**: 순수 HTML / CSS / Vanilla JS (프레임워크 없음)
- **데이터 저장**: `localStorage` (key: `hyunsol_v2`), 차단 시 메모리 폴백
- **사진 처리**: `FileReader` API → Base64 인코딩 → 로컬 저장
- **이벤트 처리**: 모든 이벤트는 `addEventListener` 방식 (inline onclick 사용 금지)
- **IIFE 구조**: 전체 JS를 `(function(){ 'use strict'; ... })()` 로 감쌈

### 데이터 구조
```json
{
  "users": {
    "닉네임": {
      "missions": [null, "data:image/jpeg;base64,...", null, null, null]
    }
  },
  "reviews": [
    { "nickname": "홍길동", "text": "후기 내용", "time": "3/25 14:30" }
  ]
}
```

### 5개 미션
1. 우리팀의 단합된 모습
2. 우리팀의 인물사진
3. 오늘의 색깔을 찍어봐요
4. 사물과 풍경 사진
5. 자연과 동물 사진

### 사용자 유형
| 유형 | 접근 | 권한 |
|------|------|------|
| 참여자 | 닉네임 입력 | 미션 수행, 후기 작성 |
| 관리자 | 비밀번호 `adgjl7` | 순위·현황·후기 조회 |

---

## 코딩 규칙 (반드시 준수)

### ✅ 해야 할 것
- 모든 이벤트는 `addEventListener`로 바인딩
- JS 전체는 `(function(){ 'use strict'; ... })()` IIFE로 감쌀 것
- `localStorage` 접근은 항상 try-catch로 감쌀 것
- `FileReader` 사용 시 `onerror` 핸들러 반드시 포함
- `file input`은 HTML에 미리 고정 선언 (동적 생성 금지)
- 문자열 출력 전 반드시 XSS 이스케이프: `esc()` 함수 사용
- 폰트는 시스템 폰트만 사용: `-apple-system, BlinkMacSystemFont, 'Apple SD Gothic Neo', sans-serif`

### ❌ 하면 안 되는 것
- `onclick=""`, `onchange=""` 등 inline 이벤트 핸들러 사용 금지
- `localStorage.setItem()` 직접 호출 금지 (반드시 래퍼 함수 사용)
- 외부 CDN 폰트/라이브러리 로드 금지 (카카오 인앱브라우저 차단됨)
- `template literal` 내부에서 복잡한 3항 연산자 중첩 금지
- 전역 변수 선언 금지 (IIFE 내부에서만 선언)

### 카카오 인앱브라우저 호환 필수 체크리스트
- [ ] localStorage try-catch 래퍼 적용됨
- [ ] FileReader onerror 핸들러 있음
- [ ] file input이 HTML에 고정 선언됨 (`id="file-0"` ~ `id="file-4"`)
- [ ] `capture="environment"` 속성 있음
- [ ] inline onclick 없음
- [ ] 외부 폰트 없음

---

## 디자인 시스템

```css
:root {
  --gold: #c8a96e;
  --gold-light: #e8d5b0;
  --dark-brown: #2c1f0e;
  --mid-brown: #6b4c2a;
  --cream: #f5f0e8;
  --text-muted: #8a7a68;
  --clear-green: #2d6a4f;
  --clear-light: #d8f3dc;
  --radius: 16px;
  --radius-sm: 8px;
}
```

**색상 사용 원칙**
- 배경/강조: `--dark-brown` (짙은 갈색)
- 포인트 컬러: `--gold` (골드)
- 기본 배경: `--cream` (크림)
- 완료 상태: `--clear-green` (초록)

---

## 작업 요청 템플릿

새 기능을 요청할 때 아래 형식으로 작성해줘:

```
## 요청 기능: [기능명]

### 목적
[이 기능이 왜 필요한지]

### 요구사항
- [ ] 요구사항 1
- [ ] 요구사항 2

### 주의사항
- 카카오 인앱브라우저 호환 필요 여부: Yes / No
- 데이터 저장 필요 여부: Yes / No
- 기존 데이터 구조 변경 여부: Yes / No
```

---

## 자주 쓰는 작업 예시

### 미션 추가/수정
```
미션 목록에 6번 미션 "야경 사진"을 추가해줘.
기존 MISSIONS 배열에 추가하고, localStorage 데이터 구조도 missions 배열 길이를 6으로 맞춰줘.
```

### 관리자 기능 추가
```
관리자 대시보드에 "데이터 초기화" 버튼을 추가해줘.
클릭 시 confirm 다이얼로그로 한 번 더 확인하고, 확인 시 localStorage hyunsol_v2 키를 삭제해줘.
```

### UI 수정
```
미션 카드에 미션별 힌트 텍스트를 추가해줘.
각 미션 카드 제목 아래에 작은 회색 글씨로 힌트를 표시하고,
디자인 시스템의 --text-muted 색상을 사용해줘.
```

### Firebase 연동 (향후 확장)
```
현재 localStorage 기반 저장을 Firebase Realtime Database로 교체해줘.
기존 store.get / store.set 래퍼 함수의 인터페이스는 유지하고,
내부 구현만 Firebase SDK로 교체해줘.
Firebase config는 아래와 같아:
[config 붙여넣기]
```

---

## 현재 알려진 이슈 / 향후 개선 사항

- [ ] 기기 간 데이터 동기화 없음 → Firebase 연동으로 해결 가능
- [ ] 사진 용량이 크면 localStorage 용량 초과 가능 → 이미지 리사이즈 전처리 필요
- [ ] 카카오 인앱브라우저에서 localStorage 비활성화 시 새로고침하면 데이터 초기화됨
- [ ] 관리자 비밀번호가 클라이언트에 하드코딩됨 → 서버 연동 시 개선 필요

---

*PRD 전체 내용: `현솔의사진콘테스트_PRD.md` 참고*  
*최종 업데이트: 2026-03-25*
