---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] FE-006: Response Viewer UI 구현: JSON, HTTP status, latency, request_id, risk/safety/error fields"
labels: 'feature, frontend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [FE-006] Response Viewer UI 구현: JSON, HTTP status, latency, request_id, risk/safety/error fields
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-054, REQ-NF-040` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Playground 실행 결과를 표시하는 Response Viewer component를 구현한다.
- [ ] JSON response, HTTP status, latency, `request_id`, `risk_level`, `safety_policy_applied`, 표준 오류 필드를 표시한다.
- [ ] success, validation error, `SAFETY_FILTERED`, `LLM_TIMEOUT`, upstream error 상태를 구분해 표시한다.
- [ ] JSON viewer는 raw transcript와 직접 식별정보를 기본적으로 마스킹하고 production tenant 데이터 표시를 차단한다.
- [ ] baseline dataset 기준 초기 결과 표시 p95 2초 이내 측정을 위한 browser performance hook 또는 E2E assertion을 추가한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: Playground 실행 결과를 필수 필드와 함께 표시함
- Given: 선행 태스크 `FE-004, CMD-003, CMD-008, CMD-009`가 완료되고 인증된 role/tenant context가 있음
- When: 유효한 Playground request 실행이 완료됨
- Then: JSON payload, HTTP status, latency, `request_id`, risk/safety field 또는 error field가 표시된다.

Scenario 2: 표준 오류 응답을 구조화해 표시함
- Given: API 실행 결과가 `INVALID_REQUEST`, `SAFETY_FILTERED`, `LLM_TIMEOUT` 중 하나임
- When: Response Viewer가 렌더링됨
- Then: `error_code`, `message`, `request_id`, `retryable`이 표시되고 성공 응답 field와 혼합되지 않는다.

Scenario 3: 응답 viewer는 민감정보를 노출하지 않음
- Given: response payload에 원문 또는 직접 식별정보가 포함될 수 있음
- When: 렌더링 결과와 network payload를 검토함
- Then: Response Viewer는 원문 transcript, 직접 식별정보, API Key 원문, 다른 tenant payload를 표시하지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 성능: `REQ-NF-040`에 따라 baseline dataset의 초기 조회 결과는 p95 2초 이내 표시되어야 한다.
- 개인정보: Response Viewer는 기본적으로 raw transcript와 직접 식별정보를 마스킹한다.
- API 계약: 성공/오류 응답 field는 API endpoint별 response schema를 따라야 한다.
- UI 범위: Partner Console만 포함하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: FE-004, CMD-003, CMD-008, CMD-009
- Blocks: TEST-014, TEST-016, NFR-011
