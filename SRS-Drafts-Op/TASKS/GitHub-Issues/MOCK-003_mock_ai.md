---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] MOCK-003: Gemini adapter mock response fixture 작성: 정상 응답, 안전 응답, timeout, upstream error, prompt injection"
labels: 'feature, mock, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [MOCK-003] Gemini adapter mock response fixture 작성: 정상 응답, 안전 응답, timeout, upstream error, prompt injection
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.1 API-001~002, REQ-FUNC-012, REQ-FUNC-045, REQ-NF-014~015` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails`](../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Gemini adapter mock fixture에 normal chat response, safety response, timeout, upstream error, prompt injection case를 작성한다.
- [ ] normal/safety response는 API-001/API-002 response schema와 `emotion_tags`, `risk_level`, `recommended_action` enum 계약을 만족해야 한다.
- [ ] timeout fixture는 `LLM_TIMEOUT`, retryable 여부, latency metadata를 포함한다.
- [ ] upstream error fixture는 `UPSTREAM_ERROR`, retryable 여부, provider metadata를 포함하되 API secret을 포함하지 않는다.
- [ ] prompt injection fixture는 HTTP 422 `SAFETY_FILTERED` 경로를 재현할 수 있게 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: Gemini adapter success/error fixture를 로드할 수 있음
- Given: UI/API/Test 작업자가 백엔드 완성을 기다리지 않고 개발해야 함
- When: `MOCK-003` fixture를 로드함
- Then: 정상 응답, 안전 응답, timeout, upstream error, prompt injection 시나리오를 재현할 수 있어야 한다.

Scenario 2: 고위험 안전 응답 fixture는 정책 발동을 검증함
- Given: 응급/자해/의료 고위험 sample utterance가 있음
- When: safety response fixture를 사용함
- Then: `safety_policy_applied=true`, 적절한 `risk_level`, `recommended_action`이 포함되어야 한다.

Scenario 3: fixture가 계약과 일치함
- Given: CT/DB 계약이 변경됨
- When: mock seed 또는 fixture를 schema validation에 통과시킴
- Then: API DTO, enum, error code, tenant/consent 필드가 최신 계약과 일치해야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안: Gemini API secret, 실제 provider payload, 실제 elder 개인정보를 fixture에 포함하지 않는다.
- 안전성: `REQ-NF-014~015` 검증에 사용할 수 있도록 의료/응급/자해 safety fixture를 포함한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-007, MOCK-002
- Blocks: CMD-005
