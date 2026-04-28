---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] FE-008: PoC Report Console 구현: 기간 필터, 지표 표시, CSV/JSON export"
labels: 'feature, frontend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [FE-008] PoC Report Console 구현: 기간 필터, 지표 표시, CSV/JSON export
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-056, REQ-NF-040~041` 요구를 구현 가능한 작업 단위로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `PoC Report Console 구현: 기간 필터, 지표 표시, CSV/JSON export` 화면 또는 component를 Tailwind CSS와 shadcn/ui 기준으로 구현한다.
- [ ] Server Action 또는 Query service와 연결하고 loading/empty/error/success 상태를 처리한다.
- [ ] 역할/tenant/sandbox 권한에 따라 action과 field 표시를 제한한다.
- [ ] raw transcript, API Key 원문 재표시, 직접 식별정보 노출을 방지한다.
- [ ] Playwright 또는 E2E 테스트에서 핵심 사용자 흐름을 검증한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 권한 있는 사용자가 화면을 사용함
- Given: 인증된 사용자가 허용된 role과 tenant context를 가지고 있음
- When: `PoC Report Console 구현: 기간 필터, 지표 표시, CSV/JSON export` 화면을 열람하거나 action을 실행함
- Then: SRS에 정의된 데이터와 action만 표시되어야 한다.

Scenario 2: 비허용 정보가 노출되지 않음
- Given: 화면에 대화, 리포트, 로그, key 또는 동의 정보가 포함됨
- When: UI payload와 렌더링 결과를 검토함
- Then: raw transcript, 직접 식별정보, 재표시 금지 API Key가 노출되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 보안: tenant 격리, RBAC, audit log, PII masking 기준을 위반하지 않는다.
- UI: Tailwind CSS와 shadcn/ui를 사용하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test) 또는 문서 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec 또는 운영 문서가 최신화되었는가?
- [ ] `TASKS/실버케어_SRS_v0.5_단일구조_TASKS.md`의 의존성 기준과 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: FE-001, QRY-004, QRY-005
- Blocks: 후속 태스크는 TASKS 원본의 Dependencies 기준으로 연결
