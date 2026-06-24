---
number: 004
title: 회의실 상세 조회 & 예약 이력
type: AFK
blocked_by:
  - 003
user_stories:
  - US-5: 회의실을 클릭하여 상세 정보 보기
labels:
  - ready-for-agent
---

## What to build

특정 회의실의 상세 정보(이름, 층, 수용인원, 장비, 이후 예약 이력)를 조회합니다.

**API:** `GET /api/rooms/:id` - 회의실 상세 + 향후 7일 예약 이력  
**UI:** `/rooms/:id` 페이지 - 상세 정보 및 예약 타임라인

## Acceptance criteria

- [ ] `GET /api/rooms/:id` API 구현 (향후 7일 예약 포함)
- [ ] `/rooms/:id` 상세 페이지 UI
- [ ] 회의실 정보 표시 (이름, 층, 수용인원, 장비)
- [ ] 예약 이력 타임라인 또는 목록 렌더링
- [ ] "예약하기" 버튼 → 예약 폼으로 이동
- [ ] 통합 테스트: API + UI

## Blocked by

- 003-rooms-list-status-badge
