# 사내 회의실 예약 웹앱 MVP — 기술 스택 최신 문서 조사 보고서

> 작성일: 2026-06-24  
> 조사 범위: Next.js, Tailwind CSS, PostgreSQL/Supabase, Supabase Auth, Cron, Resend, Vercel  
> 목적: MVP 개발 시 최신 문서 기준 기술 가이드

---

## 📋 목차

1. [Next.js 15+ (App Router + TypeScript)](#1-nextjs-15-app-router--typescript)
2. [Tailwind CSS v4](#2-tailwind-css-v4)
3. [PostgreSQL / Supabase (Database)](#3-postgresql--supabase)
4. [Supabase Auth](#4-supabase-auth)
5. [Supabase Cron vs Vercel Cron](#5-스케줄러-cron)
6. [Resend / Nodemailer (Email)](#6-resend--nodemailer)
7. [Vercel (Deployment)](#7-vercel-deployment)
8. [MVP 구현 체크리스트](#8-mvp-구현-체크리스트)
9. [주요 주의사항 (Gotchas)](#9-주요-주의사항)

---

## 1. Next.js 15+ (App Router + TypeScript)

### 최신 상태
- **현재 버전:** Next.js 15+ 
- **권장 구조:** App Router (Pages Router는 legacy)

### 회의실 예약 시스템에 적용할 핵심 기능

#### ✅ Server Actions로 예약 CRUD
```typescript
// app/actions/bookings.ts
'use server';

import { auth } from '@/lib/auth';
import { db } from '@/lib/db';

export async function createBooking(formData: FormData) {
  const user = await auth.getUser();
  if (!user) throw new Error('Unauthorized');

  const roomId = formData.get('roomId');
  const startTime = new Date(formData.get('startTime'));
  const endTime = new Date(formData.get('endTime'));

  // DB 제약 조건이 자동으로 중복 검사
  const booking = await db.bookings.create({
    room_id: roomId,
    user_id: user.id,
    start_at: startTime,
    end_at: endTime,
  });

  return booking;
}
```
- API 엔드포인트 작성 불필요
- 폼 `action` 속성에 직접 연결 가능

#### ✅ Server Components에서 직접 데이터 fetch
```typescript
// app/rooms/page.tsx
export default async function RoomsPage() {
  const rooms = await db.rooms.findMany();
  
  return (
    <div>
      {rooms.map(room => (
        <RoomCard key={room.id} room={room} />
      ))}
    </div>
  );
}
```
- 클라이언트 로딩 스피너 불필요
- 초기 렌더링에 데이터 포함됨

#### ✅ 인증 가드 (Middleware + Server Action 재검증)
```typescript
// middleware.ts
import { createServerClient } from '@supabase/ssr';
import { NextResponse } from 'next/server';

export async function middleware(request: NextRequest) {
  const supabase = createServerClient(...);
  const { data: { user } } = await supabase.auth.getUser();

  if (!user && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*', '/api/cron/:path*'],
};
```

**⚠️ 중요:** Server Action 내부에서도 `await auth.getUser()` 다시 호출하여 권한 재검증 필수
```typescript
export async function createBooking(...) {
  const user = await auth.getUser(); // ❌ 다시 확인
  if (!user) throw new Error('Unauthorized');
  // ...
}
```

#### ✅ 캐싱 전략
- **Static (기본값):** 회의실 메타데이터 (이름, 수용 인원, 장비)
- **Dynamic:** 회의실 가용성 (실시간)
  ```typescript
  export const revalidate = 0; // 캐싱 없음
  
  export default async function AvailabilityPage() {
    const availability = await getRealtimeAvailability();
    // ...
  }
  ```
- **Revalidation:** 예약 생성 후
  ```typescript
  export async function createBooking(...) {
    // ...
    revalidatePath('/availability'); // 캐시 무효화
    return booking;
  }
  ```

### 주의사항
| 항목 | 내용 |
|------|------|
| **Authorization** | 미들웨어만으로는 불충분 → Server Action에서 재검증 필수 |
| **Double-booking** | App logic이 아닌 DB 제약으로만 방지 가능 |
| **Caching** | 가용성은 항상 dynamic으로 설정 |
| **API Routes** | Legacy → 새로운 코드는 Route Handlers 사용 |

---

## 2. Tailwind CSS v4

### 최신 상태
- **현재 버전:** v4 (Lightning CSS 통합)
- **이전 패러다임:** PostCSS 플러그인 → **현재:** 독립형 CSS 도구체인

### 설치 및 설정

```bash
npm install tailwindcss @tailwindcss/postcss postcss
```

**postcss.config.mjs**
```javascript
/** @type {import('postcss-load-config').Config} */
export default {
  plugins: {
    '@tailwindcss/postcss': {},
  },
};
```

**app/globals.css** (⚠️ v3와 완전히 다름)
```css
@import "tailwindcss";
```
- `@tailwind base`, `@tailwind components`, `@tailwind utilities` 제거 (v3 방식)
- 한 줄 `@import` 로 통합됨

### 회의실 예약 UI에 활용

#### ✅ 상태 기반 색상 토큰 (`@theme`)
```css
/* app/globals.css */
@import "tailwindcss";

@theme {
  --color-available: #10b981; /* 초록 - 가능 */
  --color-booked: #ef4444;    /* 빨강 - 예약됨 */
  --color-pending: #f59e0b;   /* 주황 - 대기 */
  --color-past: #6b7280;      /* 회색 - 지난 예약 */
}
```

#### ✅ 상태 뱃지 컴포넌트
```tsx
export function RoomStatusBadge({ status }: { status: 'available' | 'booked' | 'pending' }) {
  const colorMap = {
    available: 'bg-available text-white',
    booked: 'bg-booked text-white',
    pending: 'bg-pending text-white',
  };

  return (
    <span className={`px-3 py-1 rounded-full text-sm font-medium ${colorMap[status]}`}>
      {status === 'available' && '사용 가능'}
      {status === 'booked' && '예약됨'}
      {status === 'pending' && '대기 중'}
    </span>
  );
}
```

#### ✅ 반응형 그리드 (회의실 카드)
```tsx
export default function RoomsGrid({ rooms }) {
  return (
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 p-6">
      {rooms.map(room => (
        <div key={room.id} className="bg-white rounded-lg shadow-md p-6 hover:shadow-lg transition">
          <h3 className="text-lg font-bold mb-2">{room.name}</h3>
          <p className="text-sm text-gray-600 mb-4">{room.floor} / 수용: {room.capacity}명</p>
          <RoomStatusBadge status={getStatus(room)} />
        </div>
      ))}
    </div>
  );
}
```

### v3 → v4 마이그레이션 (주의!)

| 제거할 항목 | 이유 |
|-----------|------|
| `postcss-import` | v4에 자동 포함 |
| `autoprefixer` | v4에 자동 포함 (Lightning CSS) |
| `@tailwind base/components/utilities` | 단일 `@import "tailwindcss"` 로 통합 |
| `tailwindcss.config.js` (JS 기반) | CSS-first `@theme` 로 변경 |

**❌ 흔한 실수**
```javascript
// 이전 설정을 그대로 두면 안 됨
export default {
  plugins: {
    'postcss-import': {},
    'tailwindcss': {},
    'autoprefixer': {},  // 제거!
  },
};
```

---

## 3. PostgreSQL / Supabase

### 최신 상태
- **현재 버전:** 안정적 & 프로덕션 ready
- **핵심 기능:** RLS, Realtime, 배제 제약 조건

### 🚨 중복 예약 방지 (가장 중요)

**문제:** 앱 로직만으로는 동시 요청 시 중복 예약 가능
```
User A: "14:00-15:00 예약 체크 → 비어있음 → INSERT"
User B: "14:00-15:00 예약 체크 → 비어있음 → INSERT"
결과: 같은 시간대 중복 예약 ❌
```

**해결:** DB 배제 제약 조건 (Exclusion Constraint)
```sql
-- 1. btree_gist 확장 활성화
create extension if not exists btree_gist;

-- 2. 시간 범위 자료형 지원
create table reservations (
  id uuid primary key default gen_random_uuid(),
  room_id uuid not null references rooms(id),
  user_id uuid not null references auth.users(id),
  title varchar not null,
  attendees integer,
  start_at timestamp with time zone not null,
  end_at timestamp with time zone not null,
  status varchar default 'booked',
  created_at timestamp with time zone default now()
);

-- 3. 배제 제약: 같은 회의실에서 겹치는 시간대 금지
alter table reservations add constraint no_overlap_same_room
  exclude using gist (
    room_id with =,
    tstzrange(start_at, end_at) with &&
  );
```

**동작 원리:**
- `room_id with =` : 같은 회의실
- `tstzrange(start_at, end_at) with &&` : 겹치는 시간대 (`&&` = overlap operator)
- Postgres가 INSERT 시점에 원자적으로 검사 → 동시성 안전

### 🔐 Row Level Security (RLS)

**기본값:** RLS는 OFF (활성화하지 않으면 모든 사용자가 모든 데이터 접근 가능!)

```sql
-- 1. 예약 테이블에 RLS 활성화
alter table reservations enable row level security;

-- 2. 정책: 모두 SELECT 가능 (가용성 확인용)
create policy "anyone_can_see_reservations"
  on reservations for select
  to authenticated
  using (true);

-- 3. 정책: 본인 예약만 INSERT 가능
create policy "users_can_create_own_reservations"
  on reservations for insert
  to authenticated
  with check (
    auth.uid() = user_id
  );

-- 4. 정책: 본인 예약만 UPDATE/DELETE 가능
create policy "users_can_modify_own_reservations"
  on reservations for update
  to authenticated
  using (auth.uid() = user_id)
  with check (auth.uid() = user_id);

-- 5. 관리자는 모든 예약 관리 가능
create policy "admin_can_manage_all"
  on reservations for all
  to authenticated
  using (
    (select role from auth.users where id = auth.uid()) = 'admin'
  );
```

**성능 팁:** `auth.uid()` 를 함수로 감싸기
```sql
-- ❌ 느림 (매번 평가)
where user_id = auth.uid()

-- ✅ 빠름 (Postgres 캐싱)
where user_id = (select auth.uid())
```

### 📡 Realtime (실시간 업데이트)

**기본값:** Publication에 테이블이 없으면 이벤트가 조용히 버려짐

```sql
-- 예약 테이블을 realtime publication에 추가 (필수!)
alter publication supabase_realtime add table reservations;
```

**클라이언트 코드**
```typescript
// app/components/ReservationLive.tsx
'use client';

import { useEffect, useState } from 'react';
import { createBrowserClient } from '@supabase/ssr';

export function ReservationLive({ roomId }: { roomId: string }) {
  const supabase = createBrowserClient(...);
  const [reservations, setReservations] = useState([]);

  useEffect(() => {
    // 초기 데이터
    supabase
      .from('reservations')
      .select('*')
      .eq('room_id', roomId)
      .then(({ data }) => setReservations(data));

    // 실시간 구독
    const channel = supabase
      .channel(`reservations:${roomId}`)
      .on(
        'postgres_changes',
        {
          event: '*', // INSERT, UPDATE, DELETE
          schema: 'public',
          table: 'reservations',
          filter: `room_id=eq.${roomId}`,
        },
        (payload) => {
          if (payload.eventType === 'INSERT') {
            setReservations([...reservations, payload.new]);
          } else if (payload.eventType === 'UPDATE') {
            setReservations(
              reservations.map(r => r.id === payload.new.id ? payload.new : r)
            );
          } else if (payload.eventType === 'DELETE') {
            setReservations(reservations.filter(r => r.id !== payload.old.id));
          }
        }
      )
      .subscribe();

    return () => {
      supabase.removeChannel(channel);
    };
  }, [roomId]);

  return (
    <div>
      {reservations.map(res => (
        <div key={res.id} className="p-4 bg-gray-100 rounded mb-2">
          <p className="font-bold">{res.title}</p>
          <p className="text-sm text-gray-600">
            {new Date(res.start_at).toLocaleTimeString()} - {new Date(res.end_at).toLocaleTimeString()}
          </p>
        </div>
      ))}
    </div>
  );
}
```

### 데이터 모델 (전체)

```sql
-- 회의실
create table rooms (
  id uuid primary key default gen_random_uuid(),
  name varchar not null,           -- "대회의실 A"
  floor varchar,                   -- "3층"
  capacity integer not null,       -- 10
  equipment text[],                -- ['projector', 'video_conference']
  is_active boolean default true,
  created_at timestamp with time zone default now(),
  updated_at timestamp with time zone default now()
);

alter table rooms enable row level security;

create policy "anyone_can_see_rooms"
  on rooms for select
  to authenticated
  using (is_active = true);

create policy "admin_can_manage_rooms"
  on rooms for all
  to authenticated
  using (
    (select role from auth.users where id = auth.uid()) = 'admin'
  );

-- 예약
create table reservations (
  id uuid primary key default gen_random_uuid(),
  room_id uuid not null references rooms(id),
  user_id uuid not null references auth.users(id),
  title varchar not null,
  attendees integer,
  start_at timestamp with time zone not null,
  end_at timestamp with time zone not null,
  status varchar not null default 'booked', -- booked, cancelled, completed, no_show
  reminder_sent boolean default false,      -- 중복 리마인더 방지
  checked_in_at timestamp with time zone,
  created_at timestamp with time zone default now(),
  updated_at timestamp with time zone default now()
);

alter table reservations enable row level security;

-- 시간대 겹침 방지 (핵심!)
create extension if not exists btree_gist;
alter table reservations add constraint no_overlap
  exclude using gist (
    room_id with =,
    tstzrange(start_at, end_at) with &&
  );

-- RLS 정책들...
```

---

## 4. Supabase Auth

### 최신 상태
- **현재 권장 패키지:** `@supabase/ssr` (v0.0.10+)
- **Deprecated:** `@supabase/auth-helpers-nextjs` (마이그레이션 권장)

### 설치

```bash
npm install @supabase/ssr @supabase/supabase-js
```

### 클라이언트 분리

```typescript
// lib/supabase/server.ts
import { createServerClient } from '@supabase/ssr';
import { cookies } from 'next/headers';

export const createClient = () => {
  const cookieStore = await cookies();
  
  return createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY!,
    {
      cookies: {
        getAll() {
          return cookieStore.getAll();
        },
        setAll(cookiesToSet) {
          cookiesToSet.forEach(({ name, value, options }) =>
            cookieStore.set(name, value, options)
          );
        },
      },
    }
  );
};
```

```typescript
// lib/supabase/client.ts
'use client';

import { createBrowserClient } from '@supabase/ssr';

export const createClient = () =>
  createBrowserClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY!
  );
```

### Middleware (필수!)

```typescript
// middleware.ts
import { createServerClient } from '@supabase/ssr';
import { NextResponse, type NextRequest } from 'next/server';

export async function middleware(request: NextRequest) {
  const supabase = createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY!,
    {
      cookies: {
        getAll() {
          return request.cookies.getAll();
        },
        setAll(cookiesToSet) {
          const response = NextResponse.next();
          cookiesToSet.forEach(({ name, value, options }) =>
            response.cookies.set(name, value, options)
          );
          return response;
        },
      },
    }
  );

  // 🔐 세션 갱신 (필수! getUser 호출 전에 다른 코드 금지)
  const { data: { user } } = await supabase.auth.getUser();

  // 로그인 필요한 경로 보호
  if (!user && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/((?!_next/static|_next/image|favicon.ico).*)'],
};
```

**⚠️ 중요:** `createServerClient` 다음 바로 `getUser()` 호출
```typescript
const supabase = createServerClient(...);
// ❌ 다른 코드 금지!
const user = await supabase.auth.getUser(); // ✅ 바로 호출
return NextResponse.next(); // ✅ 쿠키 동기화
```

### Server Action에서 권한 확인

```typescript
// app/actions/bookings.ts
'use server';

import { createClient } from '@/lib/supabase/server';

export async function createReservation(formData: FormData) {
  const supabase = await createClient();
  const { data: { user }, error: authError } = await supabase.auth.getUser();

  if (!user) {
    throw new Error('로그인이 필요합니다');
  }

  const roomId = formData.get('roomId');
  const startAt = new Date(formData.get('startAt'));
  const endAt = new Date(formData.get('endAt'));

  // DB 배제 제약이 동시성 보장
  const { data: reservation, error } = await supabase
    .from('reservations')
    .insert({
      room_id: roomId,
      user_id: user.id,
      title: formData.get('title'),
      attendees: parseInt(formData.get('attendees')),
      start_at: startAt,
      end_at: endAt,
    })
    .select()
    .single();

  if (error) {
    if (error.code === '23P01') { // 배제 제약 위반
      throw new Error('이미 예약된 시간대입니다');
    }
    throw error;
  }

  return reservation;
}
```

---

## 5. 스케줄러 (Cron)

### Vercel Cron vs Supabase Cron 비교

| 항목 | Vercel Cron | Supabase Cron |
|------|-----------|---------------|
| **설정 방식** | `vercel.json` | SQL `pg_cron` |
| **트리거** | GET 요청 → Route Handler | SQL 함수 실행 |
| **빈도 제한** | Hobby: 1회/일, Pro: 더 높음 | 제한 없음 |
| **복잡한 쿼리** | 부담스러움 | 자연스러움 |
| **추천 대상** | MVP (간단함) | 복잡한 배치 작업 |

### Vercel Cron (권장: MVP)

**1. `vercel.json` 설정**
```json
{
  "crons": [
    {
      "path": "/api/cron/send-reminders",
      "schedule": "0 9 * * *"  // 매일 09:00 UTC
    }
  ]
}
```

**2. Route Handler 작성**
```typescript
// app/api/cron/send-reminders/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { createClient } from '@/lib/supabase/server';
import { Resend } from 'resend';

const resend = new Resend(process.env.RESEND_API_KEY);

export async function GET(request: NextRequest) {
  // 🔐 Cron 보안: 헤더 검증 필수!
  const authHeader = request.headers.get('authorization');
  if (authHeader !== `Bearer ${process.env.CRON_SECRET}`) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }

  const supabase = await createClient();

  // 2시간 뒤 예약 찾기
  const now = new Date();
  const twoHoursLater = new Date(now.getTime() + 2 * 60 * 60 * 1000);

  const { data: upcomingReservations } = await supabase
    .from('reservations')
    .select(`
      id, title, start_at, end_at,
      user_id,
      room:room_id(name, floor)
    `)
    .gte('start_at', now.toISOString())
    .lte('start_at', twoHoursLater.toISOString())
    .eq('reminder_sent', false)
    .eq('status', 'booked');

  // 리마인더 이메일 발송
  for (const reservation of upcomingReservations || []) {
    const { data: user } = await supabase.auth.admin.getUserById(
      reservation.user_id
    );

    if (user?.email) {
      await resend.emails.send({
        from: 'noreply@example.com',
        to: user.email,
        subject: `📅 회의실 예약 리마인더: ${reservation.title}`,
        html: `
          <h2>${reservation.title}</h2>
          <p><strong>장소:</strong> ${reservation.room.name} (${reservation.room.floor})</p>
          <p><strong>시간:</strong> ${new Date(reservation.start_at).toLocaleString()}</p>
          <p>2시간 뒤 회의가 있습니다!</p>
        `,
        idempotencyKey: `reminder-${reservation.id}`, // 중복 방지
      });
    }

    // reminder_sent 플래그 업데이트
    await supabase
      .from('reservations')
      .update({ reminder_sent: true })
      .eq('id', reservation.id);
  }

  return NextResponse.json(
    { success: true, sent: upcomingReservations?.length || 0 },
    { status: 200 }
  );
}
```

**3. 환경 변수 설정**
```bash
# .env.local
CRON_SECRET=your-secret-here
RESEND_API_KEY=re_xxx
```

### Supabase Cron (복잡한 경우)

```sql
-- 1. Edge Function 생성 (Deno)
create or replace function send_reminders()
returns void as $$
  -- Edge Function 트리거
  select
    net.http_post(
      url := 'https://your-project.supabase.co/functions/v1/send-reminders',
      headers := jsonb_build_object(
        'Authorization', 'Bearer ' || current_setting('app.secrets.cron_secret'),
        'Content-Type', 'application/json'
      )
    );
$$ language sql;

-- 2. Cron 작업 스케줄
select cron.schedule(
  'send-booking-reminders',
  '0 9 * * *', -- 매일 09:00
  'select send_reminders();'
);
```

---

## 6. Resend / Nodemailer

### Resend (권장: MVP)

**이유:** 
- 0 SMTP 설정 (트랜잭셔널 이메일 서비스)
- React Email 템플릿 통합
- `idempotencyKey` 로 중복 방지
- Vercel 친화적

**설치**
```bash
npm install resend react-email
```

**기본 사용**
```typescript
// lib/email.ts
import { Resend } from 'resend';

const resend = new Resend(process.env.RESEND_API_KEY);

export async function sendReservationConfirm(
  email: string,
  reservation: {
    id: string;
    title: string;
    roomName: string;
    startAt: Date;
    endAt: Date;
  }
) {
  const { data, error } = await resend.emails.send({
    from: 'bookings@yourdomain.com',  // 검증된 도메인 필수
    to: email,
    subject: `✅ 회의실 예약 확정: ${reservation.title}`,
    react: (
      <ReservationConfirmEmail
        title={reservation.title}
        room={reservation.roomName}
        startAt={reservation.startAt}
        endAt={reservation.endAt}
      />
    ),
    idempotencyKey: `confirm-${reservation.id}`, // 중복 방지
  });

  if (error) {
    console.error('Email send failed:', error);
    throw error;
  }

  return data;
}
```

**React Email 템플릿**
```typescript
// emails/ReservationConfirm.tsx
import { Button, Container, Head, Html, Section, Text } from 'react-email';

interface Props {
  title: string;
  room: string;
  startAt: Date;
  endAt: Date;
}

export function ReservationConfirmEmail({
  title,
  room,
  startAt,
  endAt,
}: Props) {
  return (
    <Html>
      <Head />
      <Container style={{ padding: '20px' }}>
        <Section>
          <Text style={{ fontSize: '20px', fontWeight: 'bold' }}>
            ✅ 회의실 예약이 확정되었습니다
          </Text>
        </Section>

        <Section style={{ backgroundColor: '#f5f5f5', padding: '20px' }}>
          <Text>
            <strong>회의명:</strong> {title}
          </Text>
          <Text>
            <strong>장소:</strong> {room}
          </Text>
          <Text>
            <strong>시간:</strong> {startAt.toLocaleString()} ~ {endAt.toLocaleString()}
          </Text>
        </Section>

        <Section>
          <Button
            href={`https://yourdomain.com/reservations/${title}`}
            style={{
              backgroundColor: '#007bff',
              color: '#fff',
              padding: '10px 20px',
              borderRadius: '5px',
              textDecoration: 'none',
            }}
          >
            예약 보기
          </Button>
        </Section>
      </Container>
    </Html>
  );
}
```

### 프로덕션 체크리스트

1. **도메인 검증** (필수)
   - Resend 대시보드에서 `yourdomain.com` 추가
   - SPF/DKIM 레코드 DNS에 추가
   - "Auto Configure" 옵션으로 자동화 가능

2. **테스트**
   ```typescript
   // 검증 전: onboarding@resend.dev 사용
   from: 'onboarding@resend.dev',
   
   // 검증 후: 검증된 도메인
   from: 'bookings@yourdomain.com',
   ```

3. **Rate Limiting**
   - 기본: 5 requests/sec
   - 대량 리마인더: Batch API 사용
   ```typescript
   const emails = reservations.map(res => ({
     from: 'bookings@yourdomain.com',
     to: user.email,
     subject: '...',
     html: '...',
   }));

   await resend.batch.send(emails); // 최대 100개
   ```

4. **Idempotency**
   ```typescript
   {
     idempotencyKey: `reminder-${reservation.id}`,
   }
   // 같은 key로 재시도해도 한 번만 발송
   ```

### Nodemailer (대안: 기존 SMTP 있을 때)

```typescript
import nodemailer from 'nodemailer';

const transporter = nodemailer.createTransport({
  host: process.env.SMTP_HOST,
  port: 587,
  secure: false,
  auth: {
    user: process.env.SMTP_USER,
    pass: process.env.SMTP_PASS,
  },
});

export async function sendEmail(to: string, subject: string, html: string) {
  return transporter.sendMail({
    from: 'bookings@yourdomain.com',
    to,
    subject,
    html,
  });
}
```

**Nodemailer vs Resend**

| 항목 | Nodemailer | Resend |
|------|-----------|---------|
| **설정** | SMTP 설정 필요 | API 키만 필요 |
| **Vercel Serverless** | ❌ (SMTP 콜드스타트 느림) | ✅ (HTTP 기반, 빠름) |
| **템플릿** | 수동 HTML | React Email |
| **배치** | 없음 | ✅ 지원 |
| **추천** | 기존 SMTP 있을 때 | MVP/Vercel 배포 |

---

## 7. Vercel (Deployment)

### 설치 및 배포

```bash
# Vercel CLI 설치
npm i -g vercel

# 배포
vercel deploy --prod
```

### 환경 변수 설정

**대시보드 → Settings → Environment Variables**

```
NEXT_PUBLIC_SUPABASE_URL=https://xxx.supabase.co
NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY=eyJ...
SUPABASE_SERVICE_ROLE_KEY=eyJ...  # 서버 전용
RESEND_API_KEY=re_xxx
CRON_SECRET=your-secret-here
```

### Vercel Cron 설정 (재확인)

**vercel.json**
```json
{
  "crons": [
    {
      "path": "/api/cron/send-reminders",
      "schedule": "0 9 * * *"
    },
    {
      "path": "/api/cron/cleanup-noshows",
      "schedule": "0 0 * * *"  // 매일 자정
    }
  ]
}
```

**주의:**
- Crons는 **production 배포(`--prod`)** 에서만 실행
- Preview deployments에서는 실행 안 됨

### Route Handler 보안

```typescript
// 모든 cron 핸들러에 필수
const authHeader = request.headers.get('authorization');
const secret = process.env.CRON_SECRET;

if (!secret || authHeader !== `Bearer ${secret}`) {
  return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
}
```

---

## 8. MVP 구현 체크리스트

### Phase 1: 기반 구축 (1주)

- [ ] Supabase 프로젝트 생성
- [ ] PostgreSQL 스키마 작성
  - [ ] `rooms` 테이블 (RLS 활성화)
  - [ ] `reservations` 테이블 (RLS + 배제 제약)
  - [ ] `btree_gist` 확장 설치
- [ ] Supabase Auth 설정
  - [ ] 사용자 모델 (`admin` / `employee` role)
  - [ ] 로그인 페이지 UI
- [ ] Next.js 프로젝트 생성
  - [ ] Tailwind CSS v4 설정
  - [ ] Supabase 클라이언트 설정 (server/client/middleware)
  - [ ] 미들웨어 작성 (auth gating)
- [ ] 회의실 목록 페이지
  - [ ] Server Component에서 데이터 fetch
  - [ ] 카드 UI (Tailwind)
  - [ ] 상태 뱃지 (available/booked)

### Phase 2: 예약 핵심 기능 (2주)

- [ ] 예약 생성 폼
  - [ ] 날짜/시간 선택 (input type="datetime-local")
  - [ ] 회의실 선택
  - [ ] 제목, 참석자 입력
- [ ] Server Action: `createReservation`
  - [ ] 권한 확인
  - [ ] DB 배제 제약 오류 처리 (중복 예약 메시지)
  - [ ] `revalidatePath` 로 캐시 무효화
- [ ] 내 예약 관리 페이지
  - [ ] 예약 목록 조회
  - [ ] 취소 기능
- [ ] Realtime 구독
  - [ ] `supabase_realtime` publication 추가
  - [ ] 클라이언트에서 실시간 업데이트 구독
- [ ] 예약 확인 이메일 (Resend)
  - [ ] 예약 생성 후 이메일 발송
  - [ ] React Email 템플릿

### Phase 3: 관리 기능 & 완성 (3주)

- [ ] 관리자 대시보드
  - [ ] 회의실 CRUD
  - [ ] 전체 예약 현황 (조회만)
- [ ] 리마인더 Cron
  - [ ] Vercel Cron 설정 (`vercel.json`)
  - [ ] Route Handler 작성
  - [ ] `reminder_sent` 플래그로 중복 방지
- [ ] Vercel 배포
  - [ ] 환경 변수 설정
  - [ ] Cron 테스트 (production)
- [ ] 샘플 데이터 추가
- [ ] 통합 테스트 & 시연 리허설

---

## 9. 주요 주의사항

### 🔴 Critical (반드시 해결)

| 항목 | 문제 | 해결책 |
|------|------|--------|
| **중복 예약** | 동시 요청 시 중복 가능 | DB 배제 제약 (`tstzrange`) |
| **RLS OFF** | 모든 사용자가 모든 데이터 접근 | `enable row level security` 명시적 활성화 |
| **Realtime 안 됨** | Publication에 테이블 없음 | `alter publication supabase_realtime add table` |
| **세션 불안정** | 미들웨어 쿠키 동기화 실패 | `createServerClient` 직후 `getUser()` 호출 |
| **Cron 실행 안 됨** | Preview 배포에서 실행 | `vercel deploy --prod` 사용 |
| **이메일 못 보냄** | 도메인 미검증 | Resend 대시보드에서 SPF/DKIM 설정 |

### 🟡 Important (놓치기 쉬움)

| 항목 | 문제 | 해결책 |
|------|------|--------|
| **Tailwind v4 충돌** | v3 설정 + v4 라이브러리 | `autoprefixer`, `postcss-import` 제거 |
| **캐시 갱신** | 예약 후 목록이 업데이트 안 됨 | `revalidatePath('/availability')` 호출 |
| **Rate limit** | Resend 5 req/sec 초과 | Batch API 또는 큐 사용 |
| **Cron 헤더 검증** | 엔드포인트 공개 노출 | `CRON_SECRET` 헤더 검증 필수 |
| **중복 리마인더** | 같은 리마인더 여러 번 발송 | `idempotencyKey` + `reminder_sent` 플래그 |

### 🟢 Nice to Have

- [ ] 에러 로깅 (Sentry)
- [ ] 분석 (Vercel Analytics)
- [ ] 다크 모드 (Tailwind `dark:`)
- [ ] 캘린더 뷰 (v2)
- [ ] 모바일 반응형 (이미 Tailwind 기본값)

---

## 10. 참고 자료

| 기술 | 공식 문서 | 핵심 페이지 |
|------|---------|-----------|
| **Next.js** | https://nextjs.org | App Router, Server Actions, Middleware |
| **Tailwind v4** | https://tailwindcss.com | Installation, @theme, Breaking Changes |
| **Supabase** | https://supabase.com/docs | RLS, Realtime, pgcrypto |
| **Supabase Auth** | https://supabase.com/docs/guides/auth | @supabase/ssr, Middleware |
| **Resend** | https://resend.com/docs | React Email, API Reference |
| **Vercel** | https://vercel.com/docs | Crons, Environment Variables |

---

## 📝 최종 요약

**MVP 성공의 5가지 황금 법칙:**

1. **DB 배제 제약** → 중복 예약은 데이터베이스에서 원자적으로 차단
2. **Server Action 재검증** → 미들웨어 + Server Action 이중 확인
3. **RLS 활성화** → 모든 테이블에 명시적 활성화 & 정책 작성
4. **Realtime Publication** → 테이블 추가 명시 (자동 아님)
5. **Cron 보안** → CRON_SECRET 헤더 검증 필수

**배포 순서:**
1. Supabase 스키마 정의 (RLS, 제약 포함)
2. Supabase Auth 로그인 페이지
3. 회의실 목록 + 예약 UI
4. 예약 생성 로직 (중복 차단 테스트)
5. Resend 이메일 + Vercel Cron
6. 관리자 기능 (선택)
7. Vercel 프로덕션 배포

---

**작성일:** 2026-06-24  
**조사 도구:** context7 MCP  
**대상 프로젝트:** 사내 회의실 예약 시스템 MVP
