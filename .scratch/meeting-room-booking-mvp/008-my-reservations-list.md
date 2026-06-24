---
number: 008
title: 내 예약 목록 조회
type: AFK
blocked_by:
  - 002
  - 006
user_stories:
  - US-11: 내가 만든 모든 예약을 목록에서 보기
  - US-12: 다가오는 예약(24시간, 7일 등) 우선 표시
  - US-13: 예약을 클릭하여 상세 정보 확인
labels:
  - ready-for-agent
---

## What to build

현재 사용자의 모든 예약을 조회하고, 우선순위(임박한 예약 먼저)로 정렬하여 표시합니다.

**API:** `GET /api/reservations?status=booked&sort=upcoming`
- 현재 사용자(인증 토큰)의 예약 목록
- 정렬: upcoming → cancelled

**UI:** `/my-reservations` 페이지
- 예약 목록 (카드 또는 테이블)
- 우선순위 배지: "24시간 내", "7일 내" 등
- 각 카드: 회의실명, 시간, 상태, 상세보기 버튼

## Acceptance criteria

- [ ] `GET /api/reservations` API 구현 (현재 사용자 필터)
- [ ] 우선순위 정렬 로직 (upcoming 먼저)
- [ ] `/my-reservations` 페이지 UI
- [ ] 예약 카드/리스트 렌더링
- [ ] 우선순위 배지 (24h, 7d)
- [ ] 각 예약 → 상세 페이지 링크
- [ ] 통합 테스트

## Blocked by

- 002-supabase-auth
- 006-create-reservation-api-form
