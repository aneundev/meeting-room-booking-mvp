---
number: 015
title: 배포 & 성능 최적화
type: AFK
blocked_by:
  - 014
user_stories:
  - 모든 기능의 프로덕션 배포
labels:
  - ready-for-agent
---

## What to build

프로덕션 배포 준비: Vercel 배포, 성능 최적화, 보안 검토.

**배포:**
- Vercel 자동 배포 설정 (GitHub 연동)
- 환경 변수 설정 (Supabase URL, API 키)
- 프로덕션 DB 마이그레이션

**성능:**
- 회의실 목록 쿼리 성능 검증 (인덱스 활용)
- 예약 충돌 검사 성능 (< 100ms)
- UI 번들 크기 최적화

**보안:**
- Supabase RLS 정책 검증 (직원은 자신의 예약만 수정·삭제)
- 관리자 권한 검증 (API 및 UI)
- 환경 변수 노출 확인

## Acceptance criteria

- [ ] Vercel 배포 설정 완료
- [ ] GitHub 연동 자동 배포 활성화
- [ ] 프로덕션 환경 변수 설정 (보안)
- [ ] 프로덕션 DB 마이그레이션
- [ ] 성능 테스트:
  - [ ] 회의실 목록 조회 < 500ms
  - [ ] 충돌 검사 < 100ms
  - [ ] 페이지 로드 < 2s
- [ ] RLS 정책 검증 (unit test)
- [ ] 관리자 권한 검증 (unit test)
- [ ] 배포 URL 작동 확인

## Blocked by

- 014-e2e-tests
