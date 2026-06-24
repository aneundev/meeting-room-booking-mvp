---
number: 011
title: 회의실 CRUD (관리자)
type: AFK
blocked_by:
  - 001
  - 002
user_stories:
  - US-17: 회의실 정보 추가
  - US-18: 회의실 정보 수정
  - US-19: 회의실 비활성화/삭제
  - US-20: 장비 목록을 태그로 관리
labels:
  - ready-for-agent
---

## What to build

관리자 전용 회의실 관리 페이지: 회의실 추가·수정·삭제·비활성화.

**API:**
- `POST /api/rooms` (admin only)
- `PUT /api/rooms/:id` (admin only)
- `DELETE /api/rooms/:id` (admin only)

**UI:** `/admin/rooms` 페이지
- 회의실 목록 (테이블)
- "+ 회의실 추가" 버튼 → 폼
- 각 회의실 행: 편집·삭제 버튼
- 폼: 이름, 층, 수용인원, 장비(태그)

## Acceptance criteria

- [ ] `POST /api/rooms` API 구현 (admin only)
- [ ] `PUT /api/rooms/:id` API 구현 (admin only)
- [ ] `DELETE /api/rooms/:id` API 구현 (soft delete: is_active = false)
- [ ] 권한 검증 (admin role만 접근)
- [ ] `/admin/rooms` 페이지 UI
- [ ] 회의실 추가 폼 (이름, 층, 수용인원, 장비 태그)
- [ ] 회의실 편집 폼
- [ ] 삭제 확인 팝업
- [ ] 추가/수정 후 목록 즉시 업데이트
- [ ] 통합 테스트 (권한 검증 포함)

## Blocked by

- 001-db-schema-migration
- 002-supabase-auth
