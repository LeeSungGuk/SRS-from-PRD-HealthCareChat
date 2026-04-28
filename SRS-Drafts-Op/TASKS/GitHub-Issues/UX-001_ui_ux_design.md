---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] UX-001: Partner Console IA와 navigation flow 상세화: Home, Key, Docs, Playground, Report, Ops, Consent"
labels: 'feature, design, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [UX-001] Partner Console IA와 navigation flow 상세화: Home, Key, Docs, Playground, Report, Ops, Consent
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.4, 6.5, REQ-FUNC-048~060` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Partner Console Home에서 `tenant_id`, user role, module list, environment를 첫 화면 정보 구조로 정의한다.
- [ ] navigation module을 API Key Manager, API Docs, Web API Playground, PoC Report Console, Ops Monitoring, User & Consent Admin으로 제한한다.
- [ ] B2B-DEV, B2B-PM, 운영자 역할별로 표시 가능한 module과 숨김/disabled/permission denied 상태를 정의한다.
- [ ] API Key Manager, Docs, Playground, Report, Ops, Consent 화면의 진입/복귀 경로와 주요 action을 flow로 정리한다.
- [ ] 보호자용 완성 앱, 기관용 완성 앱, 어르신용 앱 UI route가 navigation에 포함되지 않도록 prohibited route 목록을 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: Partner Console IA가 필수 모듈을 포함함
- Given: UX IA 산출물을 검토함
- When: navigation module 목록을 확인함
- Then: Home, Key, Docs, Playground, Report, Ops, Consent 모듈이 포함되어야 한다.

Scenario 2: 역할별 module visibility가 정의됨
- Given: B2B-DEV, B2B-PM, 운영자 역할이 주어짐
- When: IA 권한 matrix를 확인함
- Then: 각 역할의 접근 가능 module, 숨김 상태, permission denied 상태가 명시되어야 한다.

Scenario 3: Out-of-Scope UI route가 제외됨
- Given: 보호자용/기관용/어르신용 완성 앱 UI가 Out-of-Scope임
- When: navigation과 route 목록을 검토함
- Then: guardian app, institution dashboard, elder app 목적 route가 포함되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- UI 범위: Partner Console만 포함하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.
- 디자인 시스템: Tailwind CSS와 shadcn/ui 기준으로 구현 가능한 component/state checklist를 작성한다.
- 접근 제어: 모든 protected surface는 authenticated session과 role check를 전제로 설계한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-006
- Blocks: UX-002, UX-003, FE-001
