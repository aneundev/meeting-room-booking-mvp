---
number: 012
title: 관리자 현황 조회 & 분석 대시보드
type: AFK
blocked_by:
  - 011
user_stories:
  - US-21: 전체 회의실의 예약 현황을 시간대별로 보기
  - US-22: 각 회의실별 예약률(사용률) 통계로 보기
  - US-23: 특정 기간(주, 월)의 예약 현황 필터링
labels:
  - ready-for-agent
---

## What to build

관리자 전용 대시보드: 모든 회의실의 사용률, 예약 현황, 통계를 시각화합니다.

**API:**
- `GET /api/analytics/rooms/:id?period=week|month`
  - 특정 회의실의 사용률, 예약 수
- `GET /api/analytics/overview?period=week|month`
  - 전체 회의실의 사용률 비교, 가장 인기있는 회의실 등

**UI:** `/admin/dashboard` 페이지
- 기간 필터 (이번주, 이번달, 커스텀)
- 전체 사용률 요약 (차트)
- 회의실별 사용률 비교 (막대 차트)
- 시간대별 예약 현황 (히트맵 또는 테이블)

## Acceptance criteria

- [ ] `GET /api/analytics/rooms/:id` API 구현
- [ ] `GET /api/analytics/overview` API 구현
- [ ] 사용률 계산 로직 (예약 시간 / 전체 운영 시간)
- [ ] 권한 검증 (admin only)
- [ ] `/admin/dashboard` 페이지 UI
- [ ] 기간 필터 (주/월/커스텀)
- [ ] 차트/그래프 렌더링 (Chart.js 등)
- [ ] 회의실별 사용률 비교
- [ ] 통합 테스트

## Blocked by

- 011-rooms-crud-admin
