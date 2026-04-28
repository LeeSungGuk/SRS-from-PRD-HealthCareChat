---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] FE-007: 현재 request payload 기반 cURL/TypeScript snippet 생성 UI 구현"
labels: 'feature, frontend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [FE-007] 현재 request payload 기반 cURL/TypeScript snippet 생성 UI 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-055` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] 현재 Playground endpoint와 request payload를 기반으로 cURL snippet을 생성한다.
- [ ] 동일 endpoint와 payload를 사용하는 TypeScript fetch 또는 SDK-style snippet을 생성한다.
- [ ] snippet에는 API Key 원문 대신 placeholder를 사용하고 production tenant 값은 sandbox 값으로 대체한다.
- [ ] request payload validation을 통과한 경우에만 snippet generation action을 활성화한다.
- [ ] copy button, copied state, invalid payload state, empty payload state를 E2E 테스트로 검증한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 유효한 payload에서 cURL과 TypeScript snippet을 생성함
- Given: 선행 태스크 `FE-004, QRY-002`가 완료되고 인증된 role/tenant context가 있음
- When: 사용자가 유효한 request payload에서 snippet 생성을 실행함
- Then: 동일 endpoint와 payload를 사용하는 cURL 및 TypeScript snippet이 생성된다.

Scenario 2: snippet은 API Key 원문을 포함하지 않음
- Given: 사용자가 API Key Manager에서 발급한 Key가 있음
- When: snippet을 생성함
- Then: snippet에는 실제 API Key가 아니라 `<SILVERCARE_API_KEY>` 같은 placeholder가 표시된다.

Scenario 3: invalid payload에서는 snippet을 생성하지 않음
- Given: 필수 필드 누락 또는 enum validation 오류가 있음
- When: 사용자가 snippet 생성을 시도함
- Then: UI는 validation error를 표시하고 snippet copy action을 비활성화한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- API 계약: snippet은 API Docs/OpenAPI schema와 동일한 endpoint, method, payload field를 사용해야 한다.
- 보안: 실제 API Key와 production tenant data를 snippet에 직접 삽입하지 않는다.
- 개발자 경험: snippet은 B2B 개발자의 30분 이내 첫 성공 호출 목표를 지원해야 한다.
- UI 범위: Partner Console만 포함하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: FE-004, QRY-002
- Blocks: TEST-014, NFR-011
