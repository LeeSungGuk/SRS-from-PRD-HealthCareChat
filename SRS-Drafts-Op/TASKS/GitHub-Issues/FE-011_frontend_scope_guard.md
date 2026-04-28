---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] FE-011: Guardian app, institution dashboard, elder app 목적 route/navigation 미생성 검증용 UI route inventory 작성"
labels: 'feature, frontend, priority:low'
assignees: ''
---

## :dart: Summary
- 기능명: [FE-011] Guardian app, institution dashboard, elder app 목적 route/navigation 미생성 검증용 UI route inventory 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-060, 1.2 Out-of-Scope, 6.6` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] App Router route inventory를 작성해 Partner Console에 포함된 route와 navigation item을 문서화한다.
- [ ] guardian app, institution dashboard, elder app 목적 route, page, nav item, sidebar item이 없는지 정적 검증 기준을 작성한다.
- [ ] 보호자/기관/어르신 완성 앱이 필요한 경우 PRD/SRS scope change가 필요하다는 note를 route inventory에 남긴다.
- [ ] route inventory는 FE-001 navigation config와 연결해 누락/범위 초과 route를 CI 또는 문서 검증에서 확인 가능하게 한다.
- [ ] scope guard test는 Partner Console 기능을 제거하지 않고 Out-of-Scope route 추가만 탐지한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: route inventory가 Partner Console route만 포함함
- Given: 선행 태스크 `FE-001`가 완료되고 인증된 role/tenant context가 있음
- When: route inventory를 검토함
- Then: API Key, API Docs, Playground, Sandbox, PoC Report, Ops, Consent Admin route만 Partner Console 범위로 분류된다.

Scenario 2: Out-of-Scope app route 추가를 탐지함
- Given: guardian app, institution dashboard, elder app 목적의 route 또는 navigation item이 추가됨
- When: scope guard 검증을 실행함
- Then: 검증은 실패하고 해당 route가 PRD/SRS 범위 변경 없이는 허용되지 않음을 보고한다.

Scenario 3: scope guard는 existing Partner Console route를 허용함
- Given: Partner Console의 허용 route 목록이 존재함
- When: route inventory 검증을 실행함
- Then: 허용 route는 실패로 표시되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 범위: `REQ-FUNC-060`에 따라 보호자용 완성 앱, 기관용 완성 앱, 어르신용 앱 UI는 포함하지 않는다.
- 문서화: 범위 초과 UI가 필요할 경우 PRD, SRS, TASKS를 모두 scope change로 갱신해야 한다.
- 검증: route inventory는 App Router route tree와 navigation config를 함께 확인해야 한다.
- UI 범위: Partner Console만 포함하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: FE-001
- Blocks: TEST-015
