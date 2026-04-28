---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-005: Gemini provider adapter 호출, 저비용 모델 기본 선택, token cap, retry cap, timeout/error mapping 구현"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-005] Gemini provider adapter 호출, 저비용 모델 기본 선택, token cap, retry cap, timeout/error mapping 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `C-TEC-005~006, REQ-NF-001, REQ-NF-042~044` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Vercel AI SDK 기반 server-only Gemini provider adapter interface를 구현하고 별도 Python LLM 서버 의존성을 만들지 않는다.
- [ ] 기본 모델을 `gemini-2.5-flash-lite` 또는 동등한 저비용 모델로 설정하고 환경 변수만으로 모델 교체가 가능하게 한다.
- [ ] 입력 1,200 tokens 이하, 출력 350 tokens 이하 목표를 적용하고 초과 시 truncation, summary, policy override 중 하나를 기록한다.
- [ ] provider timeout, retry cap, retryable 여부, 표준 error mapping을 구현해 retry storm과 무한 재시도를 방지한다.
- [ ] 모든 LLM 호출에 selected model, routing reason, token usage, latency, request_id를 usage/metrics로 기록한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 기본 LLM 호출은 저비용 Gemini 모델을 사용함
- Given: `CT-007, MOCK-003, SEC-004`가 완료되고 별도 모델 override가 없음
- When: Gemini provider adapter가 응답 생성을 요청함
- Then: 기본 모델은 `gemini-2.5-flash-lite` 또는 동등 저비용 모델로 선택되고 selected model과 routing reason이 기록된다.

Scenario 2: token budget 초과 시 통제된 방식으로 처리됨
- Given: 입력 context가 기본 입력 1,200 tokens 목표를 초과함
- When: provider adapter가 요청 payload를 준비함
- Then: summary 또는 truncation을 적용하거나 policy override를 기록하고 출력 목표 350 tokens 이하 설정을 전달한다.

Scenario 3: timeout 또는 provider 오류가 표준 오류로 매핑됨
- Given: Gemini API timeout, rate limit, provider 오류 중 하나가 발생함
- When: provider adapter가 재시도 정책을 수행함
- Then: retry cap을 초과하지 않고 표준 오류, retryable 여부, request_id, latency metric을 반환한다.

Scenario 4: 환경 변수 변경으로 모델 교체가 가능함
- Given: 허용된 모델 환경 변수가 다른 Gemini 모델로 설정됨
- When: adapter smoke test가 실행됨
- Then: 코드 변경 없이 모델이 교체되고 routing reason에 override 근거가 기록된다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 실행 경계: LLM provider 호출은 server-only module에서만 수행하고 Client Component가 API secret을 import하지 않도록 한다.
- 비용: `REQ-NF-042~044`에 따라 모델 선택, token budget, tenant quota, retry cap을 모두 계측 가능하게 남긴다.
- 개인정보: Gemini로 전달되는 payload는 최소 필요 대화 context와 비식별/마스킹 텍스트를 기본으로 한다.
- 성능: `REQ-NF-001`에 따라 LLM latency와 비LLM latency를 분리 측정할 수 있는 metric hook을 제공한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-007, MOCK-003, SEC-004
- Blocks: CMD-006, CMD-009, TEST-007, NFR-001, NFR-008
