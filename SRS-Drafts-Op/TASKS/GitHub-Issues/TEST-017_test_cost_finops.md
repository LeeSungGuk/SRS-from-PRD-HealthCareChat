---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-017: usage event reconciliation, monthly active device replay, quota/playground limit/budget alert 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-017] usage event reconciliation, monthly active device replay, quota/playground limit/budget alert 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-NF-029~031, REQ-NF-044` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] 모든 billable API 호출이 tenant, endpoint, device, timestamp, usage unit을 포함한 usage event를 남기는지 테스트한다.
- [ ] raw usage event에서 월 활성 디바이스와 기본 제공량 초과 사용량을 재계산하는 replay 테스트를 작성한다.
- [ ] 정확한 원화 가격표 없이 active device, 호출량, 초과 가능성 지표가 생성되는지 검증한다.
- [ ] tenant quota와 Playground 일일 실행 제한 초과 시 차단되는 테스트를 작성한다.
- [ ] LLM retry cap과 Gemini/Vercel/Supabase 월 예산 50%, 75%, 90% 알림 이벤트 테스트를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: billable call은 usage event를 생성함
- Given: `/api/v1/chat/reply`가 성공 처리됨
- When: usage event를 조회함
- Then: tenant, endpoint, device, timestamp, usage unit이 기록되어야 한다.

Scenario 2: 월 활성 디바이스 replay가 재현 가능함
- Given: 한 달치 usage event fixture가 있음
- When: billing replay test를 실행함
- Then: 월 활성 디바이스와 초과 사용량이 raw usage event에서 재계산되어야 한다.

Scenario 3: quota와 budget alert가 동작함
- Given: tenant quota 또는 월 예산 threshold에 도달함
- When: cost guardrail test를 실행함
- Then: 초과 요청은 제한되고 50%, 75%, 90% budget alert 이벤트가 생성되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 비용: 테스트는 실제 과금 폭증을 만들지 않도록 synthetic usage event와 mock provider를 우선 사용한다.
- 개인정보: usage event에는 원문 대화와 직접 PII를 포함하지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CMD-010, NFR-008
- Blocks: TEST-018
