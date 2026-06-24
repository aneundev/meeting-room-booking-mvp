---
number: 002
title: Supabase 인증 통합 & 로그인/로그아웃
type: AFK
blocked_by:
  - 001
user_stories:
  - US-24: 이메일·사번으로 로그인
  - US-25: 로그아웃
labels:
  - ready-for-agent
---

## What to build

Supabase Auth를 사용하여 이메일 기반 로그인/로그아웃을 구현합니다. 사용자 세션을 관리하고, 인증이 필요한 라우트를 보호합니다.

- **로그인 페이지** (`/login`): 이메일 입력 → Supabase 인증 → 대시보드 리다이렉트
- **로그아웃**: 헤더 메뉴에서 로그아웃 버튼
- **인증 미들웨어**: 보호된 라우트 접근 시 로그인 상태 확인
- **세션 관리**: LocalStorage / 쿠키로 토큰 유지

## Acceptance criteria

- [ ] Supabase Auth 설정 완료 (이메일 가입/로그인 활성화)
- [ ] `/login` 페이지 생성 및 UI 구성
- [ ] `POST /api/auth/login` API 라우트 구현
- [ ] `POST /api/auth/logout` API 라우트 구현
- [ ] Next.js 미들웨어로 보호된 라우트 설정
- [ ] 로그인 후 대시보드(`/rooms`)로 리다이렉트
- [ ] 로그아웃 후 로그인 페이지(`/login`)로 리다이렉트
- [ ] 통합 테스트 작성 (로그인 플로우)

## Blocked by

- 001-db-schema-migration
