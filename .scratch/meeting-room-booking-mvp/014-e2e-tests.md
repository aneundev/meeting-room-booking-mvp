---
number: 014
title: 통합 E2E 테스트 & 시연 시나리오
type: AFK
blocked_by:
  - 003
  - 006
  - 009
user_stories:
  - 시연 성공 기준 전체 5가지
labels:
  - ready-for-agent
---

## What to build

Playwright를 사용한 E2E 테스트: PRD의 5가지 성공 기준을 자동화합니다.

**테스트 시나리오:**
1. 직원 로그인 → 회의실 목록 → 비어 있는 방 확인
2. 방 선택 → 날짜·시간 입력 → 예약 생성
3. 같은 방·같은 시간 재예약 시도 → 차단 메시지 확인
4. 다른 시간 재예약 → 성공
5. 내 예약 목록 → 방금 예약 표시 확인
6. 예약 취소 → 해당 시간대 다시 비어 있음 확인
7. (admin) 회의실 추가 → 직원 화면에 즉시 반영

**프레임워크:** Playwright

## Acceptance criteria

- [ ] Playwright 설정 완료 (e2e 테스트 디렉토리)
- [ ] 시나리오 1: 로그인 + 회의실 목록 확인 테스트
- [ ] 시나리오 2: 예약 생성 테스트
- [ ] 시나리오 3: 충돌 차단 테스트
- [ ] 시나리오 4: 다른 시간 예약 성공 테스트
- [ ] 시나리오 5: 내 예약 조회 테스트
- [ ] 시나리오 6: 예약 취소 테스트
- [ ] 시나리오 7: 관리자 회의실 추가 테스트
- [ ] 모든 테스트 성공 실행 (CI/CD)

## Blocked by

- 003-rooms-list-status-badge
- 006-create-reservation-api-form
- 009-cancel-reservation
