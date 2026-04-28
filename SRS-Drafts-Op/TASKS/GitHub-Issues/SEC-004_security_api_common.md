---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] SEC-004: 표준 오류 응답 mapper 구현: INVALID_REQUEST, UNAUTHORIZED, FORBIDDEN, LLM_TIMEOUT, UPSTREAM_ERROR, SAFETY_FILTERED, INTERNAL_ERROR"
labels: 'feature, security, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [SEC-004] 표준 오류 응답 mapper 구현: INVALID_REQUEST, UNAUTHORIZED, FORBIDDEN, LLM_TIMEOUT, UPSTREAM_ERROR, SAFETY_FILTERED, INTERNAL_ERROR
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.1 Standard Error Codes, REQ-FUNC-015, REQ-FUNC-045` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] 표준 오류 response schema를 `error_code`, `message`, `request_id`, `retryable` 필수 필드로 구현한다.
- [ ] `INVALID_REQUEST`, `UNAUTHORIZED`, `FORBIDDEN`, `TENANT_ACCESS_DENIED`, `CONSENT_REQUIRED`, `RATE_LIMIT_EXCEEDED`, `LLM_TIMEOUT`, `UPSTREAM_ERROR`, `SAFETY_FILTERED`, `INTERNAL_ERROR`의 HTTP status와 retryable 값을 매핑한다.
- [ ] validation, auth, RBAC, tenant guard, consent guard, rate limit, LLM timeout/provider error, safety policy error를 mapper input으로 통합한다.
- [ ] `SAFETY_FILTERED`는 API 오남용, 정책 위반 입력, 프롬프트 주입 시도 등 처리 거부 상황에만 사용하도록 guard를 둔다.
- [ ] 오류 message에는 stack trace, 원문 대화, API Key, 직접 PII가 포함되지 않도록 sanitizer를 적용한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 인증 실패 오류가 표준 schema를 충족함
- Given: API Key가 누락된 요청이 들어옴
- When: error mapper가 실행됨
- Then: HTTP 401, `error_code=UNAUTHORIZED`, `message`, `request_id`, `retryable=false`가 반환되어야 한다.

Scenario 2: LLM timeout 오류는 재시도 가능함
- Given: LLM provider timeout이 발생함
- When: error mapper가 실행됨
- Then: HTTP 504, `error_code=LLM_TIMEOUT`, `retryable=true`가 반환되어야 한다.

Scenario 3: prompt injection은 SAFETY_FILTERED로 처리됨
- Given: 프롬프트 주입 시도 입력이 감지됨
- When: safety policy error가 mapper로 전달됨
- Then: HTTP 422, `error_code=SAFETY_FILTERED`, `retryable=false`가 반환되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 보안/개인정보: 오류 응답과 server log에는 stack trace, secret, raw transcript, 직접 PII를 포함하지 않는다.
- 호환성: 표준 오류 코드는 API Docs와 OpenAPI contract의 enum 정의와 동기화되어야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-002, CT-003
- Blocks: CMD-003, CMD-005, TEST-002
