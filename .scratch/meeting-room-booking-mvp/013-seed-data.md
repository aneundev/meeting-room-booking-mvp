---
number: 013
title: 종자 데이터 & 초기화 스크립트
type: AFK
blocked_by:
  - 001
  - 011
user_stories:
  - 모든 기능의 테스트·시연을 위한 샘플 데이터
labels:
  - ready-for-agent
---

## What to build

개발·테스트·시연을 위한 샘플 데이터를 자동 생성합니다.

**스크립트:** `scripts/seed.ts`
- 관리자 계정 생성 (admin@company.com)
- 샘플 직원 계정 5명 생성
- 샘플 회의실 5개 생성 (각각 다른 용도·장비)
- 향후 7일간의 샘플 예약 20개 생성 (다양한 상태: booked, cancelled)

**데이터:**
- 회의실: 대회의실 A/B/C, 소회의실 1/2
- 직원: user1@, user2@, ... user5@company.com
- 예약: 다양한 시간대, 참석인원, 상태

## Acceptance criteria

- [ ] `scripts/seed.ts` 작성
- [ ] DB 초기화 명령 구현 (기존 데이터 삭제)
- [ ] 관리자 계정 1개 생성
- [ ] 직원 계정 5개 생성
- [ ] 회의실 5개 생성 (다양한 용도·장비)
- [ ] 향후 7일간 예약 20개 생성
- [ ] 스크립트 실행 가능 (npm run seed)
- [ ] README에 사용법 문서화

## Blocked by

- 001-db-schema-migration
- 011-rooms-crud-admin
