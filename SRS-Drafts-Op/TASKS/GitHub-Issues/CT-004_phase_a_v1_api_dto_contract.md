---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CT-004: Phase A v1 API DTO 계약 정의"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CT-004] Phase A v1 API Request/Response DTO 정의
- 목적: `/api/v1/chat/reply`, `/api/v1/analyze/emotion`, `/api/v1/schedule/proactive`, `/api/v1/report/poc`의 요청/응답 DTO를 고정하여 API, UI Playground, 테스트, OpenAPI 문서의 기준 계약을 만든다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`/SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`/SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`/SRS/실버케어_SRS_v0.5_단일구조.md#626-api-payload-schema-cross-reference`](../../SRS/실버케어_SRS_v0.5_단일구조.md#626-api-payload-schema-cross-reference)
- API 명세: [`/SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `/api/v1/chat/reply` request DTO를 정의한다: `tenant_id`, `device_id`, `elder_id`, `utterance`, optional `conversation_id`, `use_memory`, `request_context`, `locale`.
- [ ] `/api/v1/chat/reply` response DTO를 정의한다: `schema_version`, `request_id`, `reply_text`, `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action`, `memory_used`, `safety_policy_applied`.
- [ ] `/api/v1/analyze/emotion` request/response DTO를 정의한다.
- [ ] `/api/v1/schedule/proactive` request/response DTO를 정의한다.
- [ ] `/api/v1/report/poc` request query와 JSON/CSV response DTO를 정의한다.
- [ ] endpoint별 필수 필드, optional field, enum validation, invalid request error mapping을 정의한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: chat reply 필수 필드 누락 검증
- Given: `tenant_id`, `device_id`, `elder_id`, `utterance` 중 하나가 누락된 요청이 주어짐
- When: `/api/v1/chat/reply` DTO validation을 수행함
- Then: HTTP 400과 `error_code=INVALID_REQUEST`로 매핑 가능한 validation error가 생성되어야 한다.

Scenario 2: analyze emotion 응답 schema 검증
- Given: 분석 대상 텍스트가 포함된 유효 요청이 주어짐
- When: `/api/v1/analyze/emotion` response DTO validation을 수행함
- Then: `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action`, optional `detected_keywords` 필드가 정의된 schema를 따라야 한다.

Scenario 3: PoC report 응답에 PII 필드가 없음
- Given: 기간과 tenant가 포함된 report 요청이 주어짐
- When: `/api/v1/report/poc` response DTO를 검토함
- Then: `active_devices`, `active_elders`, `conversation_count`, `avg_conversation_per_elder`, `p95_latency_ms`, `error_rate`, `risk_event_count`만 포함하고 대화 원문 또는 직접 식별정보 필드는 포함하지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- API 계약: SRS 6.1의 Request Fields와 Response Fields를 벗어난 필드를 임의로 추가하지 않는다.
- 호환성: 모든 주요 API 응답은 `schema_version`을 포함해야 한다.
- 개인정보: report DTO와 analysis keyword DTO에는 직접 식별정보가 포함되지 않아야 한다.
- 테스트 가능성: DTO는 unit test와 API integration test에서 동일하게 검증 가능해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 4개 Phase A API의 request/response DTO가 정의되었는가?
- [ ] DTO validation 실패가 standard error code와 연결되는가?
- [ ] API Docs와 Playground request builder가 참조할 수 있는 구조인가?
- [ ] schema validation 단위 테스트 항목이 정의되었는가?

## :construction: Dependencies & Blockers
- Depends on: CT-002, CT-003
- Blocks: CT-005, MOCK-002, CMD-003, CMD-008, CMD-009, UX-002, TEST-001, PMV-001
