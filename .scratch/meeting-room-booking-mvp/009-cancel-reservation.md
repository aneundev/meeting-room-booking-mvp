---
number: 009
title: 예약 취소
type: AFK
blocked_by:
  - 008
user_stories:
  - US-15: 예약을 취소하기
  - US-16: 예약 취소 시 확인 팝업
labels:
  - ready-for-agent
---

## What to build

예약을 취소하고, 해당 시간대를 다시 열어줍니다. 취소 시 확인 팝업으로 실수 방지.

**API:** `DELETE /api/reservations/:id`
- 예약 상태를 'booked' → 'cancelled'로 변경
- 권한 검증: 예약 소유자 또는 admin만 가능

**UI:**
- 내 예약 목록/상세에서 "취소" 버튼
- 클릭 → 확인 팝업 ("정말 취소하시겠습니까?")
- 취소 → 목록에서 제거 또는 상태 변경

## Acceptance criteria

- [ ] `DELETE /api/reservations/:id` API 구현
- [ ] 예약 상태 변경: booked → cancelled
- [ ] 권한 검증 (소유자 또는 admin)
- [ ] 취소 확인 팝업 UI
- [ ] 취소 후 목록 즉시 업데이트
- [ ] 취소된 예약은 시간대 가용 상태로 변경
- [ ] 통합 테스트 (권한 검증 포함)

## Blocked by

- 008-my-reservations-list
