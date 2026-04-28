---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-004: `/api/v1/chat/reply` 필수 필드 누락 400, 성공 schema, request_id 추적 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-004] `/api/v1/chat/reply` 필수 필드 누락 400, 성공 schema, request_id 추적 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-007~008, REQ-FUNC-014` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `/api/v1/chat/reply` 필수 필드(`tenant_id`, `device_id`, `elder_id`, `utterance`) 누락 시 400 테스트를 작성한다.
- [ ] 정상 요청 성공 response가 `schema_version`, `request_id`, `reply_text`, `emotion_tags`, `risk_level`, `recommended_action`, `memory_used`, `safety_policy_applied`를 포함하는지 검증한다.
- [ ] 응답, message 저장, usage event, audit/log 항목이 동일 `request_id`로 연결되는지 검증한다.
- [ ] Gemini provider는 mock/stub으로 대체해 결정적 response fixture를 사용한다.
- [ ] LLM provider 오류와 timeout은 TEST-006/SEC-004와 중복되지 않도록 성공 schema와 validation 중심으로 제한한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 필수 필드 누락은 400을 반환함
- Given: `utterance`가 없는 chat request가 주어짐
- When: `/api/v1/chat/reply`를 호출함
- Then: HTTP 400과 `INVALID_REQUEST` 표준 오류가 반환되어야 한다.

Scenario 2: 정상 응답 schema가 충족됨
- Given: 유효한 chat request와 mock LLM 응답이 준비됨
- When: `/api/v1/chat/reply`를 호출함
- Then: response는 SRS API-001 필수 필드를 모두 포함해야 한다.

Scenario 3: request_id가 응답과 저장 이벤트를 연결함
- Given: 정상 chat request가 처리됨
- When: response, message, usage event, log를 조회함
- Then: 동일 `request_id`가 모든 항목에 포함되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 테스트 안정성: LLM 응답은 mock provider를 사용해 재현 가능하게 만든다.
- 보안/개인정보: fixture utterance는 합성 데이터만 사용한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CMD-003, CMD-007
- Blocks: TEST-018
