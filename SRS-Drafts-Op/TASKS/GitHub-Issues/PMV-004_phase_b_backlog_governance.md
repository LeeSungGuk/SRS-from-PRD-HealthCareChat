---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] PMV-004: Phase B 보호자/기관 직접 UI가 필요할 경우 PRD, SRS, UX/UI Spec, OpenAPI, Privacy, Test Plan 변경 태스크 등록"
labels: 'feature, post-mvp, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [PMV-004] Phase B 보호자/기관 직접 UI가 필요할 경우 PRD, SRS, UX/UI Spec, OpenAPI, Privacy, Test Plan 변경 태스크 등록
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `1.2 Out-of-Scope, 6.6, REQ-FUNC-060` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails`](../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] 보호자용 완성 앱, 기관용 완성 앱, 어르신용 앱 UI는 Phase A MVP Out-of-Scope임을 backlog에 명시한다.
- [ ] 직접 UI를 실버케어 범위로 포함하려면 PRD scope, persona, Story/AC, KPI를 먼저 수정해야 함을 체크리스트화한다.
- [ ] PRD 변경 이후 SRS, UX/UI Spec, OpenAPI, Privacy/Data Policy, Test Plan을 갱신하는 후속 태스크를 정의한다.
- [ ] 현재 Partner Console navigation과 route에는 guardian app, institution dashboard, elder app 목적 화면이 포함되지 않아야 함을 검증 항목으로 둔다.
- [ ] Phase B/C로 전환될 때 필요한 RBAC, consent, accessibility, notification, report UX 검토 항목을 정리한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: MVP 범위를 차단하지 않는 backlog로 유지됨
- Given: Phase A MVP P0/P1/P2 구현 계획을 검토함
- When: `PMV-004` 항목을 확인함
- Then: 보호자/기관/어르신 직접 UI는 Post-MVP governance backlog로 표시되고 MVP 완료 조건을 막지 않아야 한다.

Scenario 2: 범위 변경 시 수정 문서가 명확함
- Given: 보호자 또는 기관 직접 UI를 실버케어가 제공하는 방안을 검토함
- When: `PMV-004` backlog를 조회함
- Then: PRD, SRS, UX/UI Spec, OpenAPI, Privacy/Data Policy, Test Plan 변경 순서가 확인되어야 한다.

Scenario 3: 현재 Partner Console에 직접 사용자 앱 화면이 없음
- Given: Phase A Partner Console route와 navigation을 검토함
- When: guardian app, institution dashboard, elder app 목적 화면을 검색함
- Then: 해당 화면은 포함되지 않아야 하며 필요 시 scope guard 또는 backlog 링크만 제공해야 한다.

## :gear: Technical & Non-Functional Constraints
- 범위: Post-MVP 항목은 MVP P0/P1/P2 완료 조건을 차단하지 않는다.
- 제품 거버넌스: 보호자/기관 직접 UI 추가는 SRS 단독 수정으로 진행하지 않고 PRD를 먼저 변경해야 한다.
- 개인정보: 직접 UI 추가 시 제공받는 자, 목적, 보유기간, 동의 철회, 원문 열람 권한을 재정의해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: None
- Blocks: None
