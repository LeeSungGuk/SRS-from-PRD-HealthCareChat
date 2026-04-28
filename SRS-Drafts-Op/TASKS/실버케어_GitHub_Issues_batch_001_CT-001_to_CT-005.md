---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CT-001: Next.js 단일 풀스택 모듈 경계 정의"
labels: 'feature, architecture, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CT-001] Next.js App Router 단일 풀스택 모듈 경계 정의
- 목적: Phase A MVP를 단일 Next.js 애플리케이션으로 구현하되, 향후 프론트엔드/백엔드 분리가 가능하도록 UI, API Route, Server Action, domain service, repository, LLM provider adapter, audit/logging 경계를 명확히 고정한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../SRS/실버케어_SRS_v0.5_단일구조.md#15-assumptions--constraints`](../SRS/실버케어_SRS_v0.5_단일구조.md#15-assumptions--constraints)
- 시퀀스 다이어그램: [`../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Next.js App Router 기준 권장 디렉터리 구조를 정의한다.
- [ ] `app` UI route, `app/api` Route Handler, Server Action, domain service, repository, provider adapter, audit/logging 모듈의 책임을 문서화한다.
- [ ] UI 계층이 repository 또는 LLM provider adapter를 직접 import하지 못하도록 의존성 규칙을 정의한다.
- [ ] API Route Handler와 Server Action이 domain service를 통해서만 business rule을 호출하도록 호출 방향을 정의한다.
- [ ] 향후 분리 가능성을 위해 domain service와 repository의 공개 함수 또는 typed interface naming 규칙을 정의한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 단일 Next.js 애플리케이션 구조가 정의됨
- Given: SRS의 C-TEC-001~010 제약조건이 주어짐
- When: 개발자가 프로젝트 구조 문서를 확인함
- Then: Web UI, Route Handler, Server Action, domain service, repository, LLM provider adapter, audit/logging 모듈의 책임과 호출 방향을 확인할 수 있다.

Scenario 2: UI 계층의 직접 데이터 접근이 금지됨
- Given: Web UI 컴포넌트 또는 route가 주어짐
- When: 모듈 의존성 규칙을 검토함
- Then: UI가 Prisma repository 또는 LLM provider adapter를 직접 호출하지 않고 Server Action 또는 typed interface를 통해서만 접근해야 한다는 규칙이 명시되어 있다.

Scenario 3: 향후 분리 가능성이 보장됨
- Given: Phase B 이후 프론트엔드/백엔드 분리 가능성 제약이 주어짐
- When: API/domain/repository 경계 문서를 검토함
- Then: domain service와 repository가 typed interface를 통해 호출되어 별도 backend service로 이동 가능한 구조임을 확인할 수 있다.

## :gear: Technical & Non-Functional Constraints
- 기술 스택: Next.js App Router, Server Actions, Route Handlers, Prisma, Tailwind CSS, shadcn/ui 기준을 따른다.
- 아키텍처: Phase A MVP에서는 별도 백엔드 서버를 만들지 않는다.
- 유지보수성: UI, API, domain, repository, provider adapter, logging 책임은 하나의 모듈에 중복 구현하지 않는다.
- 확장성: 향후 분리 가능성을 위해 공개 interface와 DTO는 UI 내부 타입에 종속되지 않아야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 모듈 경계와 호출 방향이 문서화되었는가?
- [ ] CT-002~CT-005가 참조할 수 있는 공통 architecture decision이 명확한가?
- [ ] ESLint 또는 dependency rule로 검증 가능한 규칙 후보가 정리되었는가?
- [ ] SRS의 C-TEC-001~010과 충돌하는 구조가 없는가?

## :construction: Dependencies & Blockers
- Depends on: None
- Blocks: CT-002, CT-006, DB-001, PLAT-002

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
- SRS 문서: [`../SRS/실버케어_SRS_v0.5_단일구조.md#33-api-overview`](../SRS/실버케어_SRS_v0.5_단일구조.md#33-api-overview)
- 시퀀스 다이어그램: [`../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

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

---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CT-003: 공통 Enum 계약 정의"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CT-003] 공통 enum 계약 정의
- 목적: `risk_level`, `emotion_tags`, `recommended_action`, `trigger_type`, `error_code` 값의 단일 진실 공급원을 만들어 API, DB, UI, 테스트가 동일한 값 집합을 사용하도록 한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../SRS/실버케어_SRS_v0.5_단일구조.md#627-enumerations`](../SRS/실버케어_SRS_v0.5_단일구조.md#627-enumerations)
- 시퀀스 다이어그램: [`../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `risk_level` enum 값을 `none`, `low`, `medium`, `high`, `critical`로 정의한다.
- [ ] `emotion_tags` enum 값을 SRS 6.2.7 기준으로 정의한다.
- [ ] `recommended_action` enum 값을 SRS 6.2.7 기준으로 정의한다.
- [ ] `trigger_type` enum 값을 `morning`, `meal`, `sleep`, `check_in`, `custom`으로 정의한다.
- [ ] `error_code` enum 값을 standard error code와 동일하게 정의한다.
- [ ] enum 추가·변경 시 하위 호환성을 유지하는 versioning 규칙을 정의한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 정의되지 않은 `risk_level` 반환 방지
- Given: 위험 분석 결과가 생성됨
- When: API response schema validation을 수행함
- Then: `risk_level`은 `none`, `low`, `medium`, `high`, `critical` 중 하나여야 한다.

Scenario 2: 정의되지 않은 `recommended_action` 반환 방지
- Given: 분석 또는 안전 응답 결과가 생성됨
- When: API response schema validation을 수행함
- Then: `recommended_action`은 SRS 6.2.7에 정의된 값 중 하나여야 한다.

Scenario 3: enum 확장 시 하위 호환성 유지
- Given: 새로운 enum 후보가 추가되어야 하는 요구가 발생함
- When: enum 계약 변경 절차를 검토함
- Then: 기존 enum 값을 제거하거나 의미를 변경하지 않고 schema version 또는 partner notice 기준이 명시되어 있어야 한다.

## :gear: Technical & Non-Functional Constraints
- 호환성: 기존 enum 값 제거는 금지하고, 추가 시 하위 호환성을 유지해야 한다.
- 테스트 가능성: 모든 enum은 schema validation과 unit test에서 재사용 가능해야 한다.
- 문서화: API Docs와 Playground request builder가 동일 enum 정의를 참조해야 한다.
- 보안: 오류 코드 enum은 내부 시스템 세부사항을 노출하는 값을 포함하지 않아야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] API, DB, UI, 테스트에서 재사용 가능한 enum 정의가 명시되었는가?
- [ ] SRS 6.2.7과 불일치하는 enum 값이 없는가?
- [ ] enum 추가·변경 versioning 규칙이 문서화되었는가?
- [ ] schema validation 테스트 항목이 정의되었는가?

## :construction: Dependencies & Blockers
- Depends on: CT-002
- Blocks: CT-004, CT-007, DB-005, CMD-006, CMD-008, TEST-001

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
- SRS 문서: [`../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../SRS/실버케어_SRS_v0.5_단일구조.md#626-api-payload-schema-cross-reference`](../SRS/실버케어_SRS_v0.5_단일구조.md#626-api-payload-schema-cross-reference)
- API 명세: [`../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

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
- Blocks: CT-005, MOCK-002, CMD-003, CMD-008, CMD-009, QRY-004, FE-004, TEST-001

---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CT-005: Developer Portal/API Docs 계약 산출물 구조 정의"
labels: 'feature, frontend, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [CT-005] Developer Portal/API Docs용 OpenAPI 또는 동등한 API 계약 산출물 생성 구조 정의
- 목적: B2B 개발자가 API Key, Swagger 또는 동등한 API Docs, cURL/TypeScript 샘플, 표준 오류 코드만으로 첫 API 호출을 30분 이내 성공할 수 있도록 문서 산출물 구조를 정의한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../SRS/실버케어_SRS_v0.5_단일구조.md#developer-onboarding-and-sandbox`](../SRS/실버케어_SRS_v0.5_단일구조.md#developer-onboarding-and-sandbox)
- 데이터 모델 (ERD): [`../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] API Docs 산출 방식으로 OpenAPI 또는 동등한 typed schema 기반 문서 생성 방식을 결정한다.
- [ ] `/api/v1/chat/reply`, `/api/v1/analyze/emotion`, `/api/v1/report/poc` 샘플 요청/응답 문서 구조를 정의한다.
- [ ] `/api/v1/schedule/proactive`는 Phase A Should/P2임을 명시하고 문서 포함 범위를 정의한다.
- [ ] 표준 오류 코드 표와 HTTP status, `retryable` mapping 표시 구조를 정의한다.
- [ ] cURL 및 TypeScript snippet 생성에 필요한 endpoint, header, payload, sandbox tenant placeholder 규칙을 정의한다.
- [ ] API Docs와 Web API Playground가 동일 DTO/enum 계약을 참조하도록 연결 기준을 정의한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: Developer Portal에서 핵심 API 문서를 확인함
- Given: 인증된 B2B 개발자가 Developer Portal에 접근함
- When: API Docs 화면을 열람함
- Then: `/api/v1/chat/reply`, `/api/v1/analyze/emotion`, `/api/v1/report/poc`의 request/response schema, enum, error code, cURL/TypeScript sample을 확인할 수 있어야 한다.

Scenario 2: 표준 오류 코드가 문서화됨
- Given: 개발자가 오류 처리 방식을 확인하려고 함
- When: API Docs의 error code section을 열람함
- Then: `INVALID_REQUEST`, `UNAUTHORIZED`, `FORBIDDEN`, `TENANT_ACCESS_DENIED`, `CONSENT_REQUIRED`, `RATE_LIMIT_EXCEEDED`, `LLM_TIMEOUT`, `UPSTREAM_ERROR`, `SAFETY_FILTERED`, `INTERNAL_ERROR`가 HTTP status와 `retryable` 값과 함께 표시되어야 한다.

Scenario 3: Playground와 문서가 같은 계약을 사용함
- Given: CT-004 DTO와 CT-003 enum 계약이 확정됨
- When: API Docs와 Web API Playground request builder를 비교함
- Then: 필수 필드, optional field, enum 값, 오류 코드가 서로 불일치하지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- Developer Experience: 첫 성공 API 호출 30분 이내 목표를 지원해야 한다.
- 계약 일관성: API Docs, Playground, 테스트 fixture는 동일 DTO와 enum 계약을 참조해야 한다.
- 개인정보: 문서 샘플에는 실제 elder 원문 데이터나 직접 식별정보를 포함하지 않는다.
- 유지보수성: enum과 DTO 변경 시 문서가 수동으로 어긋나지 않도록 생성 또는 단일 소스 참조 구조를 우선한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] API Docs 산출물 생성 방식이 결정되었는가?
- [ ] 핵심 Phase A API 샘플과 표준 오류 코드 표시 구조가 정의되었는가?
- [ ] cURL/TypeScript snippet 생성 기준이 정의되었는가?
- [ ] CT-004 DTO와 CT-003 enum을 단일 소스로 참조하는가?

## :construction: Dependencies & Blockers
- Depends on: CT-004
- Blocks: QRY-002, FE-003, FE-007, NFR-011
