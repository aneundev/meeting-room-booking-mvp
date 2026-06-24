---
number: 001
title: DB 스키마 & 마이그레이션
type: HITL
blocked_by: []
user_stories: []
labels:
  - ready-for-human
---

## What to build

Supabase에 사용자(users), 회의실(rooms), 예약(reservations) 테이블을 생성하고, 필요한 인덱스와 제약조건을 설정합니다.

```sql
-- 사용자
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT auth.uid(),
  email VARCHAR UNIQUE NOT NULL,
  name VARCHAR NOT NULL,
  role VARCHAR NOT NULL DEFAULT 'employee',  -- 'employee' | 'admin'
  created_at TIMESTAMP DEFAULT now()
);

-- 회의실
CREATE TABLE rooms (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR NOT NULL,
  floor VARCHAR,
  capacity INTEGER,
  equipment TEXT[],
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now()
);

-- 예약
CREATE TABLE reservations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  room_id UUID NOT NULL REFERENCES rooms(id),
  user_id UUID NOT NULL REFERENCES users(id),
  title VARCHAR NOT NULL,
  attendees INTEGER,
  start_at TIMESTAMP NOT NULL,
  end_at TIMESTAMP NOT NULL,
  status VARCHAR NOT NULL DEFAULT 'booked',  -- 'booked' | 'checked_in' | 'cancelled' | 'no_show'
  checked_in_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now()
);

-- 인덱스
CREATE INDEX idx_reservations_room_conflict ON reservations(room_id, start_at, end_at) WHERE status != 'cancelled';
CREATE INDEX idx_reservations_user ON reservations(user_id);
CREATE INDEX idx_rooms_active ON rooms(is_active);
```

TypeScript 타입 정의도 생성합니다.

## Acceptance criteria

- [ ] Supabase 프로젝트가 생성되고 PostgreSQL 데이터베이스 연결됨
- [ ] users, rooms, reservations 테이블이 생성됨
- [ ] 모든 인덱스가 생성됨
- [ ] 외래키 제약조건이 설정됨
- [ ] TypeScript 타입 정의 파일 생성 (`types/database.ts`)

## Blocked by

None - can start immediately
