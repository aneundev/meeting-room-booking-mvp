---
number: 006
title: 예약 생성 API & 예약 폼 UI
type: AFK
blocked_by:
  - 001
  - 003
  - 005
user_stories:
  - US-6: 날짜 → 회의실 → 시간대 선택하는 단계적 폼
  - US-7: 회의 제목, 참석 인원, 특별 요청사항 입력
  - US-8: 예약 생성 시 자동 시간대 충돌 검사
  - US-10: 예약 완료 후 확인 화면
labels:
  - ready-for-agent
---

## What to build

예약 생성의 핵심: 트랜잭션 내에서 충돌 검사 후 INSERT.

**API:** `POST /api/reservations`
- 요청: { room_id, start_at, end_at, title, attendees, notes? }
- 충돌 검사 → INSERT (트랜잭션)
- 응답: { id, success, message, booking }

**UI:** 
1. 회의실 선택 → 날짜 선택 → 시간대 선택 (다단계)
2. 제목, 참석인원, 특별요청 입력
3. 예약 확인 화면 (번호·시간·장소)

## Acceptance criteria

- [ ] `POST /api/reservations` API 구현
- [ ] 트랜잭션 내 checkConflict() 호출 후 INSERT
- [ ] 충돌 시 에러 응답: { conflict: true, message, existingBooking }
- [ ] 성공 시: { id, success: true, booking }
- [ ] 예약 폼 UI (다단계: 회의실 → 날짜 → 시간)
- [ ] 제목·참석인원·특별요청 입력 필드
- [ ] 예약 완료 화면 (예약번호, 시간, 장소 확인)
- [ ] 통합 테스트 (성공/충돌 케이스)

## Blocked by

- 001-db-schema-migration
- 003-rooms-list-status-badge
- 005-conflict-check-logic
