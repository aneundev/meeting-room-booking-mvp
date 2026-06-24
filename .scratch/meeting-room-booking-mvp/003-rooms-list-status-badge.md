---
number: 003
title: 회의실 목록 & 실시간 상태 배지
type: AFK
blocked_by:
  - 001
  - 002
user_stories:
  - US-1: 회의실 목록을 한 화면에서 보기
  - US-2: 각 회의실의 이름, 위치(층), 수용 인원, 보유 장비 확인
  - US-3: 현재 시각 기준 각 회의실의 상태 배지로 확인
labels:
  - ready-for-agent
---

## What to build

회의실 목록을 조회하고, 현재 시각 기준 각 회의실의 상태(Available, In Use, Later)를 배지로 표시합니다.

**API:** `GET /api/rooms` - 현재 상태 포함 반환  
**UI:** `/rooms` 페이지 - 카드 레이아웃, 상태 배지 표시

## Acceptance criteria

- [ ] `GET /api/rooms` API 구현 (활성 회의실만, 현재 상태 포함)
- [ ] 상태 판단 로직: Available/In Use/Later
- [ ] `/rooms` 페이지 UI 구성 (카드 또는 그리드)
- [ ] 상태 배지 렌더링 (🟢/🔴/⚪)
- [ ] 회의실 카드 → 상세 페이지 링크
- [ ] 단위 테스트: 상태 판단 로직
- [ ] 통합 테스트: API + UI

## Blocked by

- 001-db-schema-migration
- 002-supabase-auth
