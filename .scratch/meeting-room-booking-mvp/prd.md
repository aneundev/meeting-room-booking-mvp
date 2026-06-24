---
title: 사내 회의실 예약 웹앱 — MVP PRD
labels:
  - ready-for-agent
---

# 사내 회의실 예약 웹앱 — MVP PRD

> 사내 AI 해커톤 출품 / 데모용 프로젝트
> 작성일: 2026-06-24 | 개발 기간: 3주 | 팀 규모: 1명 | 가용 시간: 2시간/일(평일)

---

## Problem Statement

현재 사내 회의실 예약이 체계적으로 관리되지 않고 있습니다:

- 회의실 예약 정보가 **엑셀 시트, 메신저, 구두 전달**로 분산되어 관리 중
- 어느 회의실이 **언제 비어 있는지 한눈에 파악하기 어려움**
- **중복 예약**이 자주 발생하여 예약 시간 직전에 충돌 발견
- **노쇼**로 인해 회의실이 낭비되고 있음
- 총무 담당자가 **전체 회의실 사용 현황을 조회할 방법이 없음**

이로 인해 직원들은 매번 여러 채널을 확인해야 하고, 중복 예약으로 인한 분쟁과 시간 낭비가 발생합니다.

---

## Solution

**한 곳에서 회의실 상태를 확인하고, 중복 없이 예약할 수 있는 웹앱**을 제공합니다:

1. **실시간 회의실 현황 보기** — 각 회의실의 비어 있음/사용 중 상태를 한 화면에서 확인
2. **안전한 예약 생성** — 예약 시점에 시간대 충돌을 자동으로 검사하고 차단
3. **예약 관리** — 내가 만든 예약을 조회하고, 수정·취소 가능
4. **관리 기능** — 총무가 회의실 정보를 직접 관리하고 전체 현황을 조회

MVP에서는 **예약 흐름(조회 → 생성 → 취소)과 중복 방지 로직**에 집중합니다.

---

## User Stories

### 직원(예약자) — 회의실 조회 및 예약

1. As a 직원, I want to 회의실 목록을 한 화면에서 보기, so that 어느 방이 비어 있는지 빨리 파악할 수 있다
2. As a 직원, I want to 각 회의실의 이름, 위치(층), 수용 인원, 보유 장비를 확인하기, so that 내 회의에 맞는 방을 선택할 수 있다
3. As a 직원, I want to 현재 시각 기준 각 회의실의 상태(사용 중 / 비어 있음)를 배지로 확인하기, so that 한눈에 예약 가능 여부를 판단할 수 있다
4. As a 직원, I want to 특정 날짜와 시간대에 비어 있는 회의실만 필터링해서 보기, so that 예약 가능한 선택지를 빨리 찾을 수 있다
5. As a 직원, I want to 회의실을 클릭하여 그 방의 상세 정보(예약 이력, 장비 상세)를 보기, so that 예약하기 전에 충분히 검토할 수 있다

### 직원(예약자) — 예약 생성

6. As a 직원, I want to 날짜 선택 → 회의실 선택 → 시간대 선택하는 단계적 예약 폼을 사용하기, so that 예약 프로세스가 직관적이고 빠르다
7. As a 직원, I want to 회의 제목, 참석 인원, 특별 요청사항(장비·음식 등)을 입력하기, so that 예약 정보가 충분히 기록된다
8. As a 직원, I want to 예약 생성 시 이미 예약된 시간대와 겹치는지 자동으로 검사받기, so that 중복 예약을 사전에 방지할 수 있다
9. As a 직원, I want to 중복 예약을 시도했을 때 "이 시간대는 이미 예약됨 (다른 시간대는 어떤가요?)" 메시지를 받기, so that 대체 시간대를 빠르게 찾을 수 있다
10. As a 직원, I want to 예약 완료 후 예약 번호·시간·장소를 확인하는 화면을 보기, so that 예약이 성공했음을 확실히 알 수 있다

### 직원(예약자) — 내 예약 관리

11. As a 직원, I want to 내가 만든 모든 예약을 "내 예약" 목록에서 보기, so that 내 일정을 한 곳에서 관리할 수 있다
12. As a 직원, I want to 다가오는 예약(앞으로 24시간, 7일 등)을 우선 표시받기, so that 임박한 회의를 놓치지 않는다
13. As a 직원, I want to 예약을 클릭하여 상세 정보를 확인하기, so that 회의 직전에 장소·시간을 다시 확인할 수 있다
14. As a 직원, I want to 예약을 수정하기(시간·인원·제목 변경), so that 회의 일정이 변경되어도 유연하게 대응할 수 있다
15. As a 직원, I want to 예약을 취소하기, so that 불필요한 예약으로 회의실을 낭비하지 않는다
16. As a 직원, I want to 예약 취소 시 확인 팝업을 받기, so that 실수로 취소하는 것을 방지할 수 있다

### 총무/관리자 — 회의실 관리

17. As a 총무, I want to 회의실 정보(이름, 위치, 수용 인원, 장비)를 추가하기, so that 새로운 회의실이 시스템에 등록된다
18. As a 총무, I want to 기존 회의실의 정보를 수정하기, so that 회의실 정보가 항상 최신 상태로 유지된다
19. As a 총무, I want to 회의실을 비활성화/삭제하기, so that 폐지된 회의실이 목록에서 제거된다
20. As a 총무, I want to 각 회의실의 장비 목록(프로젝터, 화상회의 시스템, 화이트보드 등)을 태그로 관리하기, so that 직원이 필요한 장비가 있는 방을 찾을 수 있다

### 총무/관리자 — 현황 조회

21. As a 총무, I want to 전체 회의실의 예약 현황을 시간대별로 보기, so that 어느 방이 자주 쓰이는지, 어느 방이 낭비되는지 파악할 수 있다
22. As a 총무, I want to 각 회의실별 예약 률(사용률)을 통계로 보기, so that 회의실 운영 효율성을 판단할 수 있다
23. As a 총무, I want to 특정 기간(주, 월)의 예약 현황을 필터링해서 보기, so that 경향을 분석할 수 있다

### 시스템 관리

24. As a 사용자, I want to 이메일·사번으로 로그인하기, so that 보안이 유지되면서도 빠르게 접근할 수 있다
25. As a 사용자, I want to 로그아웃하기, so that 다른 사람이 내 계정으로 접근하지 못한다
26. As a 직원, I want to 내 프로필(이름, 이메일, 부서)을 확인하기, so that 예약 정보가 올바르게 기록되는지 알 수 있다
27. As a 직원, I want to 내 정보를 수정하기, so that 프로필이 항상 최신 상태다

---

## Implementation Decisions

### 1. 데이터 모델 및 스키마

**핵심 엔티티:**

```sql
-- 사용자 (users)
users (
  id             uuid PRIMARY KEY,
  email          varchar UNIQUE NOT NULL,
  name           varchar NOT NULL,
  role           varchar NOT NULL,        -- 'employee' | 'admin'
  created_at     timestamp DEFAULT now()
)

-- 회의실 (rooms)
rooms (
  id             uuid PRIMARY KEY,
  name           varchar NOT NULL,        -- 예: "대회의실 A"
  floor          varchar,                 -- 위치(층 등)
  capacity       integer,                 -- 수용 인원
  equipment      text[],                  -- 보유 장비 배열
  is_active      boolean DEFAULT true,
  created_at     timestamp DEFAULT now(),
  updated_at     timestamp DEFAULT now()
)

-- 예약 (reservations)
reservations (
  id             uuid PRIMARY KEY,
  room_id        uuid NOT NULL REFERENCES rooms(id),
  user_id        uuid NOT NULL REFERENCES users(id),
  title          varchar NOT NULL,        -- 회의 제목
  attendees      integer,                 -- 참석 인원
  start_at       timestamp NOT NULL,      -- 시작 일시
  end_at         timestamp NOT NULL,      -- 종료 일시
  status         varchar NOT NULL,        -- 'booked' | 'checked_in' | 'cancelled' | 'no_show'
  checked_in_at  timestamp,
  created_at     timestamp DEFAULT now(),
  updated_at     timestamp DEFAULT now()
)
```

**제약 조건:**
- `reservations(room_id, start_at, end_at)` 복합 인덱스로 빠른 충돌 검사
- `users.email` UNIQUE 제약으로 중복 가입 방지
- 예약 생성 시 `start_at < end_at` 검증

### 2. 중복 예약 방지 로직

**핵심 알고리즘:**

```
함수 checkConflict(room_id, start_at, end_at):
  기존_예약_목록 = SELECT * FROM reservations 
                WHERE room_id = ? 
                AND status != 'cancelled'
                AND start_at < ? AND end_at > ?
  
  IF 기존_예약_목록.length > 0:
    RETURN { conflict: true, booking: 기존_예약_목록[0] }
  ELSE:
    RETURN { conflict: false }
```

- 예약 생성 시 **트랜잭션 내에서** 충돌 검사 후 INSERT
- 이미 예약된 시간대와 **1초라도 겹치면** 차단
- 에러 응답에는 충돌하는 예약의 시간과 예약자 정보 포함 (프라이버시 검토 필요)

### 3. 인증 및 권한

- **인증:** Supabase Auth (이메일 기반, MFA 선택사항)
- **권한:** `users.role` 컬럼으로 관리
  - `employee`: 회의실 조회, 예약 생성·수정·취소
  - `admin`: 위 + 회의실 CRUD, 전체 현황 조회
- **관리자 등록:** 초기 시드 데이터로 설정 (UI 자동 승격 불가)

### 4. 시간대 표시 및 타임존

- 모든 시간을 **UTC로 저장**
- 프론트엔드에서 **사용자의 로컬 타임존으로 변환** 표시
- 타임존 설정: 초기는 한국 표준시(KST)만 지원, 향후 다국어 대응

### 5. UI/UX 흐름

**예약 생성 흐름:**
1. 홈 → 회의실 목록 (현재 시각 기준 상태 배지)
2. 회의실 클릭 → 상세 정보 + 날짜 선택
3. 날짜 선택 → 시간대 선택 (30분 단위, 또는 자유 입력)
4. 예약 폼 (제목, 참석 인원, 특별 요청)
5. 예약 확인 화면

**상태 배지:**
- 🟢 Available (비어 있음)
- 🔴 In Use (사용 중)
- ⚪ Later (오늘 이후 예약 있음, 현재 비어 있음)

### 6. API 설계

**주요 엔드포인트:**

```
GET    /api/rooms                     # 회의실 목록 + 현재 상태
GET    /api/rooms/:id                # 회의실 상세 + 예약 이력
POST   /api/rooms                    # 회의실 추가 (admin only)
PUT    /api/rooms/:id                # 회의실 수정 (admin only)
DELETE /api/rooms/:id                # 회의실 삭제 (admin only)

GET    /api/reservations             # 내 예약 목록
GET    /api/reservations/conflicts   # 충돌 검사 (폼 작성 중 실시간)
POST   /api/reservations             # 예약 생성
PUT    /api/reservations/:id         # 예약 수정
DELETE /api/reservations/:id         # 예약 취소

GET    /api/analytics/rooms/:id      # 회의실별 사용률 (admin only)
```

### 7. 기술 스택 선택 근거

| 항목 | 선택 | 이유 |
|------|------|------|
| 프레임워크 | Next.js (App Router) | 빠른 개발, TypeScript 지원, API 라우트 통합 |
| DB | PostgreSQL (Supabase) | 관계형 데이터에 최적, 복합 조건 쿼리 효율적 |
| 인증 | Supabase Auth | Supabase와 통합, 빠른 구현 |
| 스타일 | Tailwind CSS | 빠른 UI 프로토타이핑 |
| 배포 | Vercel | Next.js 최적화, 자동 배포 |

---

## Testing Decisions

### 테스트 전략

**좋은 테스트의 기준:**
- **외부 동작만 테스트** — 구현 세부사항이 아닌 사용자가 관찰 가능한 결과만 검증
- **명확한 Given-When-Then** — 초기 상태, 액션, 기대 결과가 명확
- **격리성** — 각 테스트가 독립적으로 실행되고, 데이터 정리 필수 (teardown)
- **신속성** — 각 테스트는 < 100ms (단위 테스트), < 1s (통합 테스트)

### 테스트 계층

#### 1. 단위 테스트 (Unit Tests)

**테스트 대상:**
- 충돌 검사 로직 (`checkConflict`)
- 타임존 변환 유틸리티
- 상태 배지 결정 로직 (비어 있음/사용 중/예약됨)
- 권한 검증 로직 (admin 여부)

**프레임워크:** Jest + supertest (API 엔드포인트 테스트)

#### 2. 통합 테스트 (Integration Tests)

**테스트 대상:**
- 예약 생성 → 충돌 검사 → DB 저장 전체 흐름
- 예약 수정 (시간 변경 시 새로운 충돌 검사)
- 예약 취소 → 해당 시간대 다시 열림 확인
- 회의실 CRUD → 예약 목록 즉시 반영
- 사용자 로그인 → 권한에 따른 UI 변경

**DB 전략:** 각 테스트마다 별도 트랜잭션, 테스트 종료 후 롤백

#### 3. E2E 테스트 (End-to-End Tests)

**시연 시나리오:**
1. 직원 로그인 → 회의실 목록 조회 → 비어 있는 방 확인
2. 방 선택 → 날짜·시간 입력 → 예약 생성
3. 같은 방·같은 시간 재예약 시도 → 차단 메시지 확인
4. 다른 시간 재예약 → 성공
5. 내 예약 목록 → 방금 예약 표시 확인
6. 예약 취소 → 해당 시간대 다시 비어 있음 확인
7. (admin) 회의실 추가 → 직원 화면에 즉시 반영

**프레임워크:** Playwright 또는 Cypress

### 테스트 커버리지 목표

- **핵심 로직 (충돌 검사):** 100%
- **API 엔드포인트:** > 80%
- **UI 컴포넌트:** > 60% (시연 시나리오만 E2E로)

---

## Out of Scope

이 MVP에서는 다음을 포함하지 않습니다:

### 3주 일정 제약으로 미룬 기능
- ❌ **캘린더/타임라인 뷰** — UI 복잡도 높음 (v2)
- ❌ **예약 알림 (이메일)** — 스케줄러 별도 설정 필요 (v2)
- ❌ **체크인 & 자동 노쇼 취소** — 복잡한 상태 전이 + Cron 필요 (v2)

### 기타 제외 사항
- ❌ 외부 캘린더 연동 (Google Calendar, Outlook)
- ❌ 화상회의 솔루션 연동 (Zoom, Teams 자동 링크)
- ❌ 모바일 네이티브 앱 (웹 반응형만)
- ❌ 비품·다과·케이터링 예약
- ❌ 다중 지점/건물 (단일 사옥만)
- ❌ 결제·유료 회의실
- ❌ 반복 예약 (매주 정기 회의 자동 등록)
- ❌ 예약 승인 워크플로우 (자유 예약만)

---

## Further Notes

### 성공 기준

이 PRD는 다음 한 가지 시연 흐름으로 **성공**을 정의합니다:

> **"회의실 확인 → 예약(중복 차단) → 취소"**

구체적으로:
1. ✅ 회의실 목록에서 비어 있는 방과 사용 중인 방이 상태로 구분되어 표시됨
2. ✅ 날짜·회의실·시간을 선택해 예약을 생성하고, 목록에 즉시 반영됨
3. ✅ **이미 예약된 시간대와 겹치는 예약을 시도하면 차단됨** ⭐ 가장 중요
4. ✅ 내 예약 목록에서 예약을 취소하면 해당 시간대가 다시 열림
5. ✅ (관리자) 회의실을 추가하면 예약 화면에 즉시 반영됨

### 개발 일정

| 주차 | 마일스톤 | 주요 작업 | 완료 기준 |
|------|----------|---------|----------|
| 1주 | 기반 구축 | DB 스키마·Supabase 설정, 인증(로그인), 회의실 목록 조회 UI | 회의실이 목록에 떠야 함 |
| 2주 | 예약 핵심 | 예약 생성 폼, 중복 검사 로직(백엔드), 내 예약 관리, 취소 기능 | 예약 흐름이 완성되어야 함 |
| 3주 | 관리자·완성 | 관리자 회의실 관리(CRUD), 샘플 데이터, 통합 테스트, 시연 리허설 | 실제로 동작하는 전체 시연 |

### 프라이버시 및 보안 고려사항

- 예약 충돌 시 기존 예약자 정보 노출 수준 검토 필요 (이름? 부서?)
- 관리자의 전체 예약 현황 조회 시 개인정보 처리 방침 확인 필요
- Supabase Row Level Security (RLS) 정책: 직원은 자신의 예약만 수정·삭제 가능

### v2 로드맵

- 반복 예약 (매주 정기 회의)
- 외부 캘린더 연동 (Google / Outlook)
- 화상회의 링크 자동 생성
- 회의실 사용률 통계·리포트
- 모바일 앱
- 다중 건물/지점 지원
- 예약 승인 워크플로우
- Slack 연동 (예약 확인·리마인더)
