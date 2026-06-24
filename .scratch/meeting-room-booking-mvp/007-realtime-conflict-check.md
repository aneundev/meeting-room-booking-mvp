---
number: 007
title: 실시간 충돌 검사 (선택사항)
type: AFK
blocked_by:
  - 006
user_stories:
  - US-9: 중복 예약 시도 시 메시지 + 대체 시간대 제안
labels:
  - ready-for-agent
---

## What to build

예약 폼 작성 중 실시간으로 시간대 충돌을 검사하고, 충돌 시 대체 시간대를 제안합니다.

**API:** `GET /api/reservations/conflicts?room_id=X&start_at=Y&end_at=Z`
- 충돌 여부 반환
- 충돌 시: 충돌하는 예약 정보 + 가능한 시간대 제안

**UI:**
- 폼의 시간 필드 변경 → 실시간 충돌 검사
- 충돌 시: 경고 메시지 + 대체 시간대 버튼
- 사용자가 대체 시간 선택 가능

## Acceptance criteria

- [ ] `GET /api/reservations/conflicts` API 구현
- [ ] 충돌 검사 결과 반환 (충돌 여부, 기존 예약 정보)
- [ ] 가능한 시간대 제안 로직 (same day, ±1 hours)
- [ ] 폼의 시간 필드 onchange → API 호출 (디바운싱)
- [ ] 충돌 시 경고 메시지 표시
- [ ] 대체 시간대 버튼 클릭 → 폼 자동 채우기
- [ ] 통합 테스트

## Blocked by

- 006-create-reservation-api-form
