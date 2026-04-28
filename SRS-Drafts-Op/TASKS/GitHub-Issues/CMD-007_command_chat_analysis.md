---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-007: chat 응답의 emotion/risk/action 분석 결과 저장, medium 이상 risk_event 승격, usage_event 저장 구현"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-007] chat 응답의 emotion/risk/action 분석 결과 저장, medium 이상 risk_event 승격, usage_event 저장 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-008, REQ-FUNC-013~014, REQ-FUNC-026, REQ-NF-029` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] chat reply 결과를 `schema_version`, `reply_text`, `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action`, `memory_used`, `request_id`, `safety_policy_applied` schema로 조립한다.
- [ ] `emotion_tags`, `risk_level`, `recommended_action`이 SRS enum 외 값을 포함하지 않도록 validation을 적용한다.
- [ ] chat 분석 결과를 `analysis_result`에 tenant, elder, request_id 기준으로 저장하고 raw transcript를 저장하지 않는다.
- [ ] `risk_level`이 `medium`, `high`, `critical`이면 `risk_event`를 `review_status=pending`으로 승격 저장한다.
- [ ] chat reply 호출의 `usage_event`를 tenant, endpoint, device, request_id, usage_unit, latency_ms, status_code 기준으로 저장한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: chat reply 성공 응답 schema가 필수 필드를 모두 포함함
- Given: `CMD-003, CMD-006, DB-005, DB-006`가 완료되고 유효한 chat reply 결과가 있음
- When: chat response assembler가 실행됨
- Then: 응답 JSON에는 `schema_version`, `reply_text`, `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action`, `memory_used`, `request_id`, `safety_policy_applied`가 모두 포함된다.

Scenario 2: 분석 enum은 SRS 정의 값만 허용함
- Given: 분석 결과에 `risk_level` 또는 `recommended_action` 값이 포함됨
- When: 분석 결과 validation이 실행됨
- Then: `risk_level`은 `none`, `low`, `medium`, `high`, `critical` 중 하나이고 `recommended_action`은 SRS enum 중 하나여야 한다.

Scenario 3: medium 이상 위험은 risk_event로 승격됨
- Given: chat 분석 결과의 `risk_level`이 `medium`, `high`, `critical` 중 하나임
- When: 분석 결과 저장 command가 실행됨
- Then: 동일 `request_id`, `tenant_id`, `elder_id`를 가진 `analysis_result`와 `risk_event(review_status=pending)`가 저장된다.

Scenario 4: billable chat 호출은 usage_event를 남김
- Given: chat reply 호출이 성공 또는 표준 오류로 종료됨
- When: usage 기록 처리가 실행됨
- Then: `usage_event`에는 tenant, endpoint, device, request_id, usage_unit, latency_ms, status_code가 누락 없이 저장된다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: `analysis_result`, `risk_event`, `usage_event` 저장은 동일 `request_id`와 tenant scope를 유지해야 한다.
- 개인정보: 분석 결과와 위험 이벤트에는 원문 대화가 아닌 마스킹 또는 요약 근거만 저장한다.
- 비용/FinOps: `REQ-NF-029`에 따라 billable chat 호출의 usage event 누락률은 0%여야 한다.
- 추적성: `REQ-FUNC-014`에 따라 응답, 분석, 위험 이벤트, 사용량 이벤트가 동일 `request_id`로 연결되어야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CMD-003, CMD-006, DB-005, DB-006
- Blocks: CMD-012, CMD-013, TEST-004, TEST-016
