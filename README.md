# 사내 회의실 예약 웹앱 (Meeting Room Booking MVP)

> 사내 AI 해커톤 출품 / 데모용 프로젝트
> 
> **개발 기간:** 3주 | **팀 규모:** 1명 | **가용 시간:** 2시간/일(평일)

## 개요

사내 회의실 예약을 **한 곳에서 관리**하고, **중복 예약을 자동으로 방지**하는 웹앱입니다.

기존의 엑셀·메신저로 흩어진 회의실 예약 정보를 통합하여, 직원들이 빈 회의실을 빠르게 찾고 예약할 수 있게 합니다.

### 핵심 기능

- 🏢 **회의실 목록 & 실시간 상태** — 각 회의실의 사용 중/비어 있음 상태를 한 화면에서 확인
- 📅 **안전한 예약 생성** — 중복 예약 자동 차단
- 📝 **내 예약 관리** — 예약 조회·수정·취소
- ⚙️ **관리자 기능** — 회의실 CRUD, 전체 현황 조회

## 성공 기준

다음 흐름이 **정상 작동**하면 성공:

```
회의실 확인 → 예약(중복 차단) → 취소
```

1. ✅ 회의실 목록에서 상태(사용 중/비어 있음)로 구분 표시
2. ✅ 날짜·회의실·시간으로 예약 생성
3. ✅ **시간대 겹침 → 자동 차단** ⭐ 가장 중요
4. ✅ 취소 후 해당 시간대 다시 열림
5. ✅ 관리자의 회의실 추가가 즉시 반영됨

## 기술 스택

| 구분 | 선택 | 이유 |
|------|------|------|
| **프레임워크** | Next.js (App Router) | TypeScript 지원, 빠른 개발 |
| **스타일** | Tailwind CSS | 빠른 UI 프로토타이핑 |
| **DB** | PostgreSQL (Supabase) | 관계형 데이터 최적, 인증 통합 |
| **인증** | Supabase Auth | 이메일 기반 로그인 |
| **배포** | Vercel | Next.js 최적화 |

## 데이터 모델

```sql
-- 사용자
users (
  id, email, name, role: 'employee' | 'admin'
)

-- 회의실
rooms (
  id, name, floor, capacity, equipment[]
)

-- 예약 (핵심: 중복 방지)
reservations (
  id, room_id, user_id, title, attendees,
  start_at, end_at,
  status: 'booked' | 'checked_in' | 'cancelled' | 'no_show'
)
```

**중복 예약 방지:**
```
room_id + 겹치는 시간대(start_at ~ end_at) 검사 후 INSERT
```

## 개발 일정 (3주)

| 주차 | 마일스톤 | 작업 | 완료 기준 |
|------|----------|------|----------|
| **1주** | 기반 구축 | DB 스키마, 인증, 회의실 목록 UI | 회의실 목록 표시 |
| **2주** | 예약 핵심 | 예약 폼, 중복 검사, 내 예약 관리 | 예약 흐름 완성 |
| **3주** | 관리자·완성 | 회의실 CRUD, 테스트, 시연 | 전체 시연 성공 |

## 제외 범위 (v2로 미룸)

❌ 캘린더/타임라인 뷰  
❌ 예약 알림 (이메일)  
❌ 체크인 & 자동 노쇼 취소  
❌ 외부 캘린더 연동  
❌ 화상회의 링크 자동 생성  
❌ 모바일 네이티브 앱

## 프로젝트 구조

```
.
├── CLAUDE.md                          # 프로젝트 문서 & Agent skills 설정
├── README.md                          # 이 파일
├── docs/
│   └── agents/                        # Agent skills 설정 문서
│       ├── issue-tracker.md           # 이슈 추적 설정
│       ├── triage-labels.md           # Triage 레이블 정의
│       └── domain.md                  # 도메인 문서 레이아웃
├── .scratch/
│   └── meeting-room-booking-mvp/
│       └── prd.md                     # 상세 PRD (User Stories, Implementation Decisions 등)
├── final_spec.md                      # 최종 스펙 문서
└── [기타 스펙 문서들]
```

## PRD 문서

**상세 PRD는 여기서 확인:**
- 📄 **로컬:** `.scratch/meeting-room-booking-mvp/prd.md`
- 🌐 **GitHub Issue:** [#1 MVP: 사내 회의실 예약 웹앱 구현](https://github.com/aneundev/meeting-room-booking-mvp/issues/1)

PRD에는 다음이 포함됩니다:
- Problem Statement & Solution
- 27개의 User Stories
- Implementation Decisions (데이터 모델, API, 충돌 검사 알고리즘)
- Testing Decisions (단위/통합/E2E 테스트 전략)
- Out of Scope & v2 로드맵

## 개발 시작

```bash
# 저장소 클론
git clone https://github.com/aneundev/meeting-room-booking-mvp.git
cd meeting-room-booking-mvp

# PRD 읽기
cat .scratch/meeting-room-booking-mvp/prd.md

# 개발 시작...
```

## v2 로드맵

- 반복 예약 (매주 정기 회의)
- 외부 캘린더 연동 (Google / Outlook)
- 화상회의 링크 자동 생성
- 회의실 사용률 통계·리포트
- 모바일 앱
- 다중 건물/지점 지원

---

**작성자:** castl  
**프로젝트 시작:** 2026-06-24
