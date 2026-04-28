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
- SRS 문서: [`/SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`/SRS/실버케어_SRS_v0.5_단일구조.md#developer-onboarding-and-sandbox`](../../SRS/실버케어_SRS_v0.5_단일구조.md#developer-onboarding-and-sandbox)
- 데이터 모델 (ERD): [`/SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`/SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

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
- Blocks: QRY-002
