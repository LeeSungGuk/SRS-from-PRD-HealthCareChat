---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-006: 의료/응급/자해 안전 응답 후처리와 `SAFETY_FILTERED` 거부 케이스 분리 구현"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-006] 의료/응급/자해 안전 응답 후처리와 `SAFETY_FILTERED` 거부 케이스 분리 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-012, REQ-FUNC-044~045, REQ-NF-014~015` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] 의료, 복약, 병원, 응급, 자해 발화를 식별하는 chat safety policy service를 구현한다.
- [ ] 위험 발화 자체는 `SAFETY_FILTERED` 오류가 아니라 HTTP 200 안전 응답으로 처리하고 `safety_policy_applied=true`를 반환한다.
- [ ] 안전 응답에는 진단, 처방, 복약 지시를 포함하지 않고 가족/의료진/긴급 연락 권고와 적절한 `risk_level`, `recommended_action`을 포함한다.
- [ ] API 오남용, 정책 위반 입력, 프롬프트 주입 시도는 HTTP 422 `SAFETY_FILTERED`로 분리한다.
- [ ] LLM 생성 응답에 의료 판단·처방 문구가 포함된 경우 안전 응답으로 대체하는 후처리 테스트를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 응급 또는 자해 발화는 안전 응답 200으로 처리됨
- Given: 어르신 발화에 응급, 자해, 극심한 통증 신호가 포함됨
- When: chat safety policy가 적용됨
- Then: HTTP 200 응답에 안전 응답, `safety_policy_applied=true`, `risk_level=critical` 또는 `high`, 적절한 `recommended_action`이 포함된다.

Scenario 2: 의료 판단·처방 문구는 응답에서 제거됨
- Given: LLM 후보 응답에 진단, 처방, 복약 지시 문구가 포함됨
- When: safety postprocessor가 후보 응답을 검토함
- Then: 진단/처방 문구가 없는 안전 응답으로 대체되고 의료/처방 금지 위반은 0건이어야 한다.

Scenario 3: 프롬프트 주입 또는 API 오남용은 SAFETY_FILTERED로 거부됨
- Given: 시스템 지시 우회, 정책 회피, 비정상 자동화 요청 등 정책 위반 입력이 들어옴
- When: chat safety policy가 처리 거부 대상으로 판단함
- Then: HTTP 422, `error_code=SAFETY_FILTERED`, `retryable=false`, `request_id`를 반환하고 위험 발화 안전 응답 경로와 구분된다.

Scenario 4: 고위험 평가셋은 안전 정책 발동률 100%를 충족함
- Given: 응급/자해/의료 고위험 평가셋이 주어짐
- When: safety test suite가 실행됨
- Then: 모든 고위험 케이스에서 안전 정책이 발동하고 금지 문구가 생성되지 않는다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 안전성: `REQ-NF-014`에 따라 의료/처방 금지 위반은 평가셋과 PoC 리뷰에서 0건이어야 한다.
- 안전성: `REQ-NF-015`에 따라 응급/자해/의료 고위험 케이스의 안전 정책 발동률은 100%여야 한다.
- 오류 정책: 위험 발화 안전 응답과 `SAFETY_FILTERED` 거부 케이스를 코드와 테스트에서 분리한다.
- 개인정보: safety log와 test fixture에는 원문 PII를 저장하지 않고 마스킹 텍스트를 사용한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CMD-005, CT-003
- Blocks: CMD-007, CMD-009, TEST-006, TEST-007
