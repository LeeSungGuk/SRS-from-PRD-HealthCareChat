---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] MOCK-002: sandbox elder profile, consent state, sample utterance, 정상/안전/오류 시나리오 seed 작성"
labels: 'feature, mock, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [MOCK-002] sandbox elder profile, consent state, sample utterance, 정상/안전/오류 시나리오 seed 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.4 Sandbox Scenario Picker, REQ-FUNC-003, REQ-FUNC-053` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails`](../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] sandbox elder profile fixture를 synthetic `elder_id`, `device_id`, locale, memory_enabled 값으로 작성한다.
- [ ] consent_state fixture는 basic_processing, sensitive_processing, memory_storage, guardian_sharing, institution_sharing, quality_improvement 6개 dimension을 포함한다.
- [ ] sample utterance fixture를 normal, safety, error scenario별로 작성한다.
- [ ] normal 200, safety policy 200, invalid request/error response를 재현하는 request payload preset을 작성한다.
- [ ] Sandbox Scenario Picker와 Playground가 같은 fixture를 사용하도록 import path와 scenario key를 문서화한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: Sandbox Scenario Picker는 synthetic profile과 발화를 사용할 수 있음
- Given: UI/API/Test 작업자가 백엔드 완성을 기다리지 않고 개발해야 함
- When: `MOCK-002` fixture를 로드함
- Then: sample elder profile, consent state, sample utterance, normal/safety/error preset을 실제 개인정보 없이 사용할 수 있어야 한다.

Scenario 2: 동의 상태 fixture는 6개 dimension을 포함함
- Given: sandbox consent fixture를 검토함
- When: consent_state 필드를 확인함
- Then: 6개 consent dimension이 모두 존재하고 memory consent on/off 케이스가 포함되어야 한다.

Scenario 3: fixture가 계약과 일치함
- Given: CT/DB 계약이 변경됨
- When: mock seed 또는 fixture를 schema validation에 통과시킴
- Then: API DTO, enum, error code, tenant/consent 필드가 최신 계약과 일치해야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 개인정보: 실제 elder profile, 실제 발화 원문, production tenant 데이터는 fixture에 포함하지 않는다.
- Sandbox: normal/safety/error response 재현을 위한 request payload는 API-001/API-002 계약을 따라야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-003, CT-004
- Blocks: MOCK-003, QRY-003
