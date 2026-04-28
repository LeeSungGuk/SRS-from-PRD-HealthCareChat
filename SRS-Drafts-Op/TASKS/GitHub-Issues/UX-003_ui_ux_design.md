---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] UX-003: 운영/개인정보 UI 표시 정책 정의: masked evidence, prohibited display, role별 module visibility"
labels: 'feature, design, priority:medium, frontend'
assignees: ''
---

## :dart: Summary
- 기능명: [UX-003] 운영/개인정보 UI 표시 정책 정의: masked evidence, prohibited display, role별 module visibility
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.4, REQ-NF-036~038, REQ-FUNC-057~060` 요구를 구현 가능한 작업 단위로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `운영/개인정보 UI 표시 정책 정의: masked evidence, prohibited display, role별 module visibility`의 사용자, 목적, 화면 범위, 금지 표시 정보를 정의한다.
- [ ] loading, empty, error, success, permission denied 상태를 설계한다.
- [ ] B2B Partner Console 범위 안에서만 IA와 navigation을 작성한다.
- [ ] 보호자용/기관용/어르신용 완성 앱으로 오해될 route나 copy를 제외한다.
- [ ] FE 태스크가 바로 구현할 수 있도록 component/state checklist를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 화면 범위가 명확함
- Given: SRS 관련 섹션 `6.4, REQ-NF-036~038, REQ-FUNC-057~060`이 주어짐
- When: `UX-003` UX 산출물을 검토함
- Then: 대상 사용자, 화면 범위, 표시 필드, 금지 표시 항목이 명확해야 한다.

Scenario 2: 구현자가 바로 사용할 수 있음
- Given: FE 태스크가 `운영/개인정보 UI 표시 정책 정의: masked evidence, prohibited display, role별 module visibility`를 구현하려고 함
- When: UX 문서를 참조함
- Then: loading, empty, error, success, permission state 기준을 확인할 수 있어야 한다.

## :gear: Technical & Non-Functional Constraints
- UI: Tailwind CSS와 shadcn/ui를 사용하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test) 또는 문서 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec 또는 운영 문서가 최신화되었는가?
- [ ] `TASKS/실버케어_SRS_v0.5_단일구조_TASKS.md`의 의존성 기준과 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-006, CT-008, UX-001
- Blocks: 후속 태스크는 TASKS 원본의 Dependencies 기준으로 연결
