---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-002: 인증 실패 401, tenant 불일치 403, rate limit 429, 표준 오류 schema 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-002] 인증 실패 401, tenant 불일치 403, rate limit 429, 표준 오류 schema 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-004~006, REQ-FUNC-015` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] API Key 누락/invalid key 요청이 HTTP 401 `UNAUTHORIZED`를 반환하는 통합 테스트를 작성한다.
- [ ] cross-tenant 리소스 접근이 HTTP 403 `TENANT_ACCESS_DENIED`와 no data payload를 반환하는 테스트를 작성한다.
- [ ] rate limit 초과가 HTTP 429 `RATE_LIMIT_EXCEEDED`, `retryable=true`, `retry_after_seconds`를 반환하는 테스트를 작성한다.
- [ ] 모든 오류 응답이 `error_code`, `message`, `request_id`, `retryable` 필수 필드를 포함하는지 공통 assertion을 작성한다.
- [ ] 오류 응답과 log에 raw API Key, stack trace, 직접 PII가 없는지 negative assertion을 추가한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 인증 실패는 401 표준 오류를 반환함
- Given: API Key가 누락된 요청이 주어짐
- When: 보호된 API를 호출함
- Then: HTTP 401과 `error_code=UNAUTHORIZED`, `request_id`, `retryable=false`가 반환되어야 한다.

Scenario 2: tenant 불일치는 403 no data를 반환함
- Given: tenant A 인증으로 tenant B 리소스를 요청함
- When: API를 호출함
- Then: HTTP 403과 `TENANT_ACCESS_DENIED`가 반환되고 데이터 payload는 없어야 한다.

Scenario 3: rate limit 초과는 retry 정보를 포함함
- Given: tenant가 rate limit을 초과함
- When: 추가 API 호출을 수행함
- Then: HTTP 429, `RATE_LIMIT_EXCEEDED`, `retryable=true`, `retry_after_seconds`가 반환되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안/개인정보: 테스트 fixture는 실제 API Key나 실제 사용자 PII를 포함하지 않는다.
- 범위: 본 테스트는 API 공통 보안/오류 contract만 검증하고 개별 비즈니스 로직은 각 기능 테스트에서 검증한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: SEC-001, SEC-002, SEC-003, SEC-004
- Blocks: TEST-018, NFR-009
