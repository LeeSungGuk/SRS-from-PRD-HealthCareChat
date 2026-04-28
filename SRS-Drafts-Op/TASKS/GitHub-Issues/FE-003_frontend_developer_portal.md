---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] FE-003: API Docs UI 구현: Swagger 또는 API schema, enum, error code, cURL/TypeScript sample"
labels: 'feature, frontend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [FE-003] API Docs UI 구현: Swagger 또는 API schema, enum, error code, cURL/TypeScript sample
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-002, REQ-FUNC-050, 6.4` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] API Docs 화면에 endpoint, method, request fields, response fields, enum, error code를 OpenAPI 호환 schema 기준으로 표시한다.
- [ ] `/api/v1/chat/reply`, `/api/v1/analyze/emotion`, `/api/v1/report/poc`의 cURL 및 TypeScript 샘플을 제공한다.
- [ ] `risk_level`, `recommended_action`, `error_code` 등 공통 enum 정의를 한 곳에서 확인할 수 있게 구성한다.
- [ ] endpoint별 표준 오류 응답 schema인 `error_code`, `message`, `request_id`, `retryable`을 표시한다.
- [ ] 문서 샘플에는 실제 elder 원문 데이터와 production tenant 데이터가 포함되지 않도록 fixture를 사용한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 개발자는 endpoint schema와 샘플 코드를 확인함
- Given: 선행 태스크 `FE-001, QRY-002`가 완료되고 인증된 role/tenant context가 있음
- When: 사용자가 API Docs에서 endpoint를 선택함
- Then: request/response schema, enum, error code, cURL sample, TypeScript sample이 표시된다.

Scenario 2: 표준 오류 코드 표를 확인할 수 있음
- Given: 개발자가 API Docs의 error code section을 열람함
- When: 오류 코드를 선택함
- Then: HTTP status, `retryable`, 의미, 표준 오류 response shape가 표시된다.

Scenario 3: 문서 샘플은 실제 개인정보를 포함하지 않음
- Given: API Docs의 request/response sample을 렌더링함
- When: 샘플 데이터를 검토함
- Then: 실제 elder 원문, 직접 식별정보, production tenant 데이터가 포함되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- API 계약: 외부 파트너 API와 Playground schema는 OpenAPI 호환 계약으로 관리해야 한다.
- 개발자 경험: B2B 개발자의 첫 성공 API 호출 30분 이내 목표를 방해하지 않도록 schema와 샘플을 한 화면 흐름에서 접근 가능하게 한다.
- 개인정보: docs sample은 synthetic 또는 redacted data만 사용한다.
- UI 범위: Partner Console만 포함하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: FE-001, QRY-002
- Blocks: TEST-014, NFR-011
