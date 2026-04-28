---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] FE-001: App Router console shell, authenticated layout, tenant context, role module navigation 구현"
labels: 'feature, frontend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [FE-001] App Router console shell, authenticated layout, tenant context, role module navigation 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-048, REQ-NF-036, 6.5` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] App Router 기반 Partner Console shell을 구성하고 authenticated layout, tenant context provider, role-aware navigation을 구현한다.
- [ ] Console Home에 `tenant_id`, user role, accessible module list, current environment를 표시한다.
- [ ] module navigation은 API Key Manager, API Docs, Web API Playground, PoC Report Console, Ops Monitoring, User/Consent Admin만 허용한다.
- [ ] 인증되지 않은 접근과 권한 없는 module 접근은 protected route guard로 차단한다.
- [ ] 보호자용 완성 앱, 기관용 완성 앱, 어르신용 앱 UI route가 navigation 또는 route tree에 포함되지 않도록 scope guard test를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 인증된 사용자는 tenant context와 허용 모듈을 확인함
- Given: 선행 태스크 `CT-006, UX-001, MOCK-001`가 완료되고 인증된 role/tenant context가 있음
- When: 사용자가 Console Home에 접속함
- Then: 현재 `tenant_id`, user role, accessible module list, environment가 표시된다.

Scenario 2: 권한 없는 module navigation은 차단됨
- Given: 사용자의 role이 특정 module 접근 권한을 갖지 않음
- When: 사용자가 해당 module route에 직접 접근함
- Then: 접근이 거부되고 다른 tenant 또는 금지 module 데이터가 표시되지 않는다.

Scenario 3: Partner Console 범위 밖 앱 UI는 제공하지 않음
- Given: route tree와 navigation config를 검토함
- When: guardian app, institution dashboard, elder app 목적의 화면을 검색함
- Then: 해당 route와 navigation item은 존재하지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- UI Stack: Tailwind CSS와 shadcn/ui를 사용하고 Console shell의 layout, nav, empty/error/loading state를 일관되게 구현한다.
- 보안: `REQ-NF-036`에 따라 100% protected route가 authenticated session과 role check를 요구해야 한다.
- 개인정보: shell과 navigation에서 다른 tenant 정보가 표시되지 않아야 한다.
- UI 범위: Partner Console만 포함하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-006, UX-001, MOCK-001
- Blocks: FE-002, FE-003, FE-004, FE-008, FE-009, FE-010, FE-011, TEST-014
