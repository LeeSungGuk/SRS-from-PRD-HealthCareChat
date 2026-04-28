---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-014: Partner Console protected route, module visibility, API Docs, Key Manager, Playground 핵심 E2E 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-014] Partner Console protected route, module visibility, API Docs, Key Manager, Playground 핵심 E2E 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-048~055, REQ-NF-036, REQ-NF-039` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Partner Console 모든 protected route가 authenticated session과 role check를 요구하는 E2E 테스트를 작성한다.
- [ ] Navigation이 역할별 module visibility를 적용하고 guardian/institution/elder 완성 앱 route를 노출하지 않는지 검증한다.
- [ ] API Docs 화면에서 endpoint schema, enum, error code, cURL/TypeScript sample이 표시되는지 E2E를 작성한다.
- [ ] API Key Manager에서 발급/폐기/원문 key 1회 표시/재표시 금지를 검증한다.
- [ ] Web API Playground가 sandbox mode 기본값, scenario picker, request validation, JSON response viewer, snippet 생성을 제공하는지 검증한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 비인증 사용자는 protected route에 접근할 수 없음
- Given: authenticated session이 없음
- When: Partner Console protected route에 접근함
- Then: 접근은 차단되고 로그인 또는 권한 오류 상태가 표시되어야 한다.

Scenario 2: API Docs와 Key Manager 핵심 흐름이 동작함
- Given: 권한 있는 개발자 session이 있음
- When: API Docs를 열고 API Key를 발급/폐기함
- Then: 문서 contract가 표시되고 원문 key는 최초 발급 시 1회만 표시되어야 한다.

Scenario 3: Playground는 sandbox 기본 실행을 보장함
- Given: 권한 있는 개발자가 Playground를 열람함
- When: sample scenario를 실행함
- Then: sandbox mode로 실행되고 JSON response viewer와 cURL/TypeScript snippet이 표시되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- UI 범위: Partner Console만 테스트하고 보호자/기관/어르신용 완성 앱 UI는 실패 조건으로 둔다.
- 보안/개인정보: E2E screenshot과 trace에는 원문 API Key나 실제 PII가 남지 않도록 synthetic data를 사용한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: FE-001~FE-007
- Blocks: TEST-018, NFR-011
