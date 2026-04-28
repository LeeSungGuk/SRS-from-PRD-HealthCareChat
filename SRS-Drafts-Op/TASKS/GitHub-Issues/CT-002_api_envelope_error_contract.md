---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CT-002: 공통 API Envelope 및 오류 응답 구조 정의"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CT-002] 공통 API envelope, `schema_version`, `request_id`, 성공/오류 응답 구조 정의
- 목적: 모든 API 응답과 오류 응답이 동일한 추적성과 schema validation 기준을 갖도록 공통 응답 계약을 정의한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`/SRS/실버케어_SRS_v0.5_단일구조.md#33-api-overview`](../../SRS/실버케어_SRS_v0.5_단일구조.md#33-api-overview)
- 시퀀스 다이어그램: [`/SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`/SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`/SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] 성공 응답 공통 필드인 `schema_version`과 `request_id`의 타입과 생성 규칙을 정의한다.
- [ ] 오류 응답 공통 필드인 `error_code`, `message`, `request_id`, `retryable`의 타입과 필수성을 정의한다.
- [ ] `retry_after_seconds`처럼 특정 오류에서만 필요한 optional field 처리 규칙을 정의한다.
- [ ] Route Handler에서 성공/오류 응답을 생성할 공통 helper 함수 시그니처를 정의한다.
- [ ] response schema validation 테스트에서 재사용할 fixture 구조를 정의한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 성공 응답에 추적 필드가 포함됨
- Given: 임의의 Phase A API 성공 응답이 주어짐
- When: response schema validation을 수행함
- Then: `schema_version`과 `request_id`가 필수 필드로 존재해야 한다.

Scenario 2: 오류 응답이 표준 schema를 따름
- Given: 인증 실패, 필드 누락, rate limit, LLM timeout 중 하나의 오류가 발생함
- When: 오류 응답을 반환함
- Then: `error_code`, `message`, `request_id`, `retryable`이 포함된 표준 오류 응답을 반환해야 한다.

Scenario 3: 동일 요청의 추적성이 유지됨
- Given: 단일 API 요청이 성공 또는 실패함
- When: 응답, usage event, audit log, risk event를 조회함
- Then: 동일한 `request_id`로 연결될 수 있어야 한다.

## :gear: Technical & Non-Functional Constraints
- API 호환성: 모든 Phase A API는 `schema_version`을 포함해야 한다.
- 추적성: `request_id`는 성공, 오류, 로그, 리포트, 알림에서 동일 요청을 연결할 수 있어야 한다.
- 안정성: 오류 응답은 SRS의 standard error code와 HTTP status mapping을 벗어나지 않아야 한다.
- 보안: 오류 메시지는 민감정보, 원문 발화, API Key, 내부 stack trace를 포함하지 않아야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 공통 성공/오류 응답 타입 또는 schema가 정의되었는가?
- [ ] 표준 오류 응답 fixture가 작성되었는가?
- [ ] API 명세서 또는 OpenAPI 생성 작업에서 참조 가능한 구조인가?
- [ ] 단위 테스트(Unit Test) 및 schema validation 테스트 계획이 포함되었는가?

## :construction: Dependencies & Blockers
- Depends on: CT-001
- Blocks: CT-003, CT-004, CT-007, CT-008, PLAT-001, SEC-004
