---
number: 010
title: 예약 수정
type: AFK
blocked_by:
  - 006
  - 008
user_stories:
  - US-14: 예약을 수정하기 (시간·인원·제목 변경)
labels:
  - ready-for-agent
---

## What to build

이미 생성한 예약의 시간·인원·제목을 수정합니다. 수정 시 새로운 시간대의 충돌 검사를 재실행합니다.

**API:** `PUT /api/reservations/:id`
- 요청: { start_at, end_at, title, attendees }
- 새로운 시간대 충돌 검사 (자신의 예약 제외)
- 충돌 시 에러, 성공 시 업데이트

**UI:**
- 예약 상세 페이지에서 "수정" 버튼
- 수정 폼 (시간·인원·제목)
- 충돌 검사 (실시간 또는 제출 시)
- 수정 완료 → 목록으로 돌아가기

## Acceptance criteria

- [ ] `PUT /api/reservations/:id` API 구현
- [ ] 새로운 시간대 충돌 검사 (자신의 예약 제외)
- [ ] 권한 검증 (소유자 또는 admin)
- [ ] 수정 폼 UI (시간·인원·제목 입력)
- [ ] 실시간 충돌 검사 표시
- [ ] 수정 완료 → 목록 업데이트
- [ ] 통합 테스트 (충돌 검사, 권한 포함)

## Blocked by

- 006-create-reservation-api-form
- 008-my-reservations-list
