---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-012: 위험/오류/timeout/신고 대상 이벤트를 review queue/evaluation_sample 후보로 생성하는 command 구현"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-012] 위험/오류/timeout/신고 대상 이벤트를 review queue/evaluation_sample 후보로 생성하는 command 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-035, REQ-NF-011~013, REQ-NF-035` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] review queue 대상 조건을 구현한다: `high`, `critical`, `SAFETY_FILTERED`, LLM timeout, 보호자/기관 신고, 운영자 신고 케이스.
- [ ] 대상 이벤트를 `request_id`, tenant, risk_level, masked evidence, review_status 기준으로 review 대상에 연결한다.
- [ ] 오탐/미탐 리뷰용 `evaluation_sample` 후보를 생성하되 `utterance_masked`, expected labels, labeling rationale, `expires_at`만 저장한다.
- [ ] 평가셋 후보는 별도 동의 또는 가명/익명 처리 가능 조건을 만족하지 않으면 생성하지 않거나 즉시 폐기 상태로 표시한다.
- [ ] 중복 `request_id` 또는 동일 risk_event 재처리 시 review/evaluation candidate가 중복 생성되지 않도록 idempotency를 보장한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: high/critical 위험 이벤트는 리뷰 대상으로 수집됨
- Given: `DB-005, CMD-007, CMD-008`가 완료되고 `risk_level=high` 또는 `critical`인 이벤트가 있음
- When: review queue command가 실행됨
- Then: 동일 `request_id` 기준으로 리뷰 대상이 생성되고 `review_status=pending` 상태와 마스킹된 근거가 저장된다.

Scenario 2: SAFETY_FILTERED와 LLM timeout은 리뷰 대상으로 수집됨
- Given: API 요청이 `SAFETY_FILTERED` 또는 `LLM_TIMEOUT`으로 종료됨
- When: review queue command가 실행됨
- Then: request_id, error code, masked evidence, model/prompt metadata가 리뷰 대상에 연결된다.

Scenario 3: 평가셋 후보는 PII 제거 후 분리 저장됨
- Given: 운영자가 오탐/미탐 리뷰 데이터 추출을 요청함
- When: evaluation sample 후보가 생성됨
- Then: `utterance_masked`, expected risk/emotion/action label, labeling rationale이 저장되고 raw transcript는 저장되지 않는다.

Scenario 4: 평가셋 후보 보존기간은 30일을 넘지 않음
- Given: evaluation sample 후보가 생성됨
- When: 후보의 `expires_at`을 확인함
- Then: 생성 시점 기준 최대 30일 이내로 설정되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- AI 품질: `REQ-NF-011~013`의 위험 recall, critical recall, false-positive review 추적에 필요한 평가 로그를 남긴다.
- Review Loop: `REQ-NF-035`에 따라 qualifying event의 리뷰 큐 수집률은 100%여야 한다.
- 개인정보: evaluation sample과 review evidence에는 원문과 직접 식별정보를 저장하지 않는다.
- 보존: 평가셋 후보는 `REQ-NF-027`에 따라 최대 30일 임시 보관한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-005, CMD-007, CMD-008
- Blocks: QRY-007, TEST-013, NFR-010
