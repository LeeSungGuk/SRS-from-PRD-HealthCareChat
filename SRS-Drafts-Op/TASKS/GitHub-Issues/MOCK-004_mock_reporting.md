---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] MOCK-004: PoC report, ops log, review queue UI 개발용 usage/risk/audit/evaluation seed 작성"
labels: 'feature, mock, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [MOCK-004] PoC report, ops log, review queue UI 개발용 usage/risk/audit/evaluation seed 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-022~026, REQ-FUNC-033~035, REQ-FUNC-056~058` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails`](../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] PoC report 개발용 `usage_event`와 `poc_report_aggregate` fixture를 작성한다.
- [ ] Ops log 개발용 `audit_log`, masked conversation log, latency/error/safety policy fixture를 작성한다.
- [ ] Review Queue 개발용 `risk_event`와 `evaluation_sample` fixture를 작성하되 `utterance_masked`만 포함한다.
- [ ] normal, high/critical risk, `SAFETY_FILTERED`, LLM timeout, empty report, export scenario를 포함한다.
- [ ] PoC Report Console, Ops Monitoring, Review Queue 테스트가 같은 fixture를 참조하도록 경로와 scenario key를 문서화한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: reporting/ops UI fixture가 핵심 상태를 재현함
- Given: UI/API/Test 작업자가 백엔드 완성을 기다리지 않고 개발해야 함
- When: `MOCK-004` fixture를 로드함
- Then: PoC metric, ops log, review queue, empty state, export scenario를 실제 개인정보 없이 재현할 수 있어야 한다.

Scenario 2: review fixture는 원문 없이 평가 후보를 제공함
- Given: review queue fixture를 검토함
- When: `evaluation_sample` payload를 확인함
- Then: `utterance_masked`, expected labels, labeling rationale, review_status만 포함하고 raw transcript는 포함하지 않는다.

Scenario 3: fixture가 계약과 일치함
- Given: CT/DB 계약이 변경됨
- When: mock seed 또는 fixture를 schema validation에 통과시킴
- Then: API DTO, enum, error code, tenant/consent 필드가 최신 계약과 일치해야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 개인정보: report, ops, review fixture에는 직접 식별정보와 raw transcript를 포함하지 않는다.
- 재사용성: fixture는 FE-008, FE-009, TEST-015, TEST-016에서 동일하게 참조 가능해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-005, DB-006
- Blocks: None
