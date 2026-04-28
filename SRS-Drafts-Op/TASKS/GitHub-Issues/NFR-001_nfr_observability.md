---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] NFR-001: endpoint별 latency, error rate, token usage, LLM timeout, safety activation metrics 수집 구현"
labels: 'feature, nfr, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [NFR-001] endpoint별 latency, error rate, token usage, LLM timeout, safety activation metrics 수집 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-NF-001, REQ-NF-005` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Next.js Route Handler 공통 래퍼에서 `request_id`, `tenant_id`, `endpoint`, `status`, `latency_ms`, `error_code`를 수집한다.
- [ ] `/api/v1/chat/reply`는 비LLM 서버 처리 시간과 LLM 생성 시간을 분리 계측하고 PoC baseline을 `non_llm_p95 <= 800ms`, `llm_total_p95 <= 5s`로 산출한다.
- [ ] Gemini provider adapter에서 `model`, `input_tokens`, `output_tokens`, `timeout`, `retry_count`, `routing_reason`을 usage event와 metric 양쪽에 남긴다.
- [ ] safety policy adapter에서 `SAFETY_FILTERED`, 위험 등급, policy activation 여부를 metric으로 수집한다.
- [ ] 전체 API endpoint의 metric emission coverage가 100%인지 검증하는 audit script 또는 dashboard query를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: chat latency가 구간별로 측정됨
- Given: `/api/v1/chat/reply` 요청이 정상 처리됨
- When: observability middleware와 LLM adapter가 metric을 기록함
- Then: 동일한 `request_id`에 대해 `non_llm_latency_ms`, `llm_latency_ms`, `total_latency_ms`가 분리 저장되어야 한다.

Scenario 2: endpoint metric coverage가 100%임
- Given: SRS Appendix 6.1의 API endpoint 목록이 주어짐
- When: metrics coverage audit을 실행함
- Then: 모든 endpoint가 latency, error rate, request count metric을 방출해야 하며 누락 endpoint가 있으면 실패해야 한다.

Scenario 3: LLM 사용량과 safety activation이 추적됨
- Given: LLM timeout 또는 `SAFETY_FILTERED` 응답이 발생함
- When: 요청 처리가 종료됨
- Then: `model`, token usage, timeout 여부, safety activation 여부가 PII 없이 조회 가능해야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안/개인정보: metric tag와 log payload에는 원문 대화, 주민등록번호, 주소, 전화번호 등 직접 PII를 저장하지 않는다.
- 성능: metric 수집 오버헤드는 API 응답시간 측정값을 왜곡하지 않도록 경량 동기 기록 또는 비동기 기록으로 제한한다.
- 비용: token usage와 retry count는 NFR-008 cost guardrail에서 재사용 가능한 구조로 남긴다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: PLAT-001, CMD-005, CMD-010
- Blocks: NFR-002
