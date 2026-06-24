---
number: 005
title: 중복 예약 방지 로직 & 단위 테스트
type: AFK
blocked_by:
  - 001
user_stories:
  - US-8: 예약 생성 시 시간대 충돌 자동 검사
labels:
  - ready-for-agent
---

## What to build

핵심 알고리즘: `checkConflict(room_id, start_at, end_at)` 구현 및 100% 단위 테스트.

```typescript
function checkConflict(room_id: string, start_at: Date, end_at: Date): {
  conflict: boolean;
  booking?: { id, title, start_at, end_at, user: { name } };
}
```

이미 예약된 시간대와 1초라도 겹치면 conflict = true 반환.

## Acceptance criteria

- [ ] `checkConflict()` 함수 구현 (utils/conflict.ts)
- [ ] 데이터베이스 쿼리: `start_at < ? AND end_at > ? AND status != 'cancelled'`
- [ ] 시간대 겹침 검사 로직 (1초라도 겹치면 충돌)
- [ ] 충돌 케이스별 단위 테스트:
  - [ ] 완전히 겹치는 경우
  - [ ] 시작 시간만 겹치는 경우
  - [ ] 종료 시간만 겹치는 경우
  - [ ] 중간에 포함되는 경우
  - [ ] 취소된 예약은 무시
  - [ ] 겹치지 않는 경우
- [ ] 테스트 커버리지 100%

## Blocked by

- 001-db-schema-migration
