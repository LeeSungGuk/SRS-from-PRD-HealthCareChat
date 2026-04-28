---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] PMV-003: `/api/v2/report/kpi` 주간 정서, 수면 분석, 기관 성과 JSON 구현 백로그 등록"
labels: 'feature, post-mvp, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [PMV-003] `/api/v2/report/kpi` 주간 정서, 수면 분석, 기관 성과 JSON 구현 백로그 등록
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `API-008, REQ-FUNC-027, REQ-FUNC-030~032, REQ-FUNC-042` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails`](../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `/api/v2/report/kpi`를 Phase B/Post-MVP backlog로 등록하고 Phase A MVP 구현 대상에서 제외한다.
- [ ] request contract 초안에 `tenant_id`, `from`, `to`, optional `scope=guardian/institution/partner`를 명시한다.
- [ ] response contract 초안에 weekly sentiment report, sleep analysis result, institution performance data, `schema_version`, `request_id`를 명시한다.
- [ ] source data로 `analysis_result`, `risk_event`, `sensor_event`, `poc_report_aggregate`를 연결하고 수면 분석은 sensor_event 확정 이후로 제한한다.
- [ ] 보호자/기관 공유 동의, 원문 미노출, 기관 소속/권한 검사, 정렬 기준 변경을 Phase B privacy/test 변경 항목으로 남긴다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: MVP 범위를 차단하지 않는 backlog로 유지됨
- Given: Phase A MVP P0/P1/P2 구현 계획을 검토함
- When: `PMV-003` 항목을 확인함
- Then: `/api/v2/report/kpi` 구현은 Post-MVP로 표시되고 MVP 완료 조건을 막지 않아야 한다.

Scenario 2: Phase B KPI contract가 추적 가능함
- Given: Phase B KPI 리포트 구현 검토가 시작됨
- When: `PMV-003` backlog를 조회함
- Then: request/response 필드, source data, consent/privacy, 권한 검사, 테스트 변경 항목을 확인할 수 있어야 한다.

Scenario 3: 원문 제공이 기본 응답에 포함되지 않음
- Given: Phase B KPI report contract 초안을 검토함
- When: response field를 확인함
- Then: raw utterance와 AI reply 원문은 기본 응답 필드에 포함되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 범위: Post-MVP 항목은 MVP P0/P1/P2 완료 조건을 차단하지 않는다.
- 개인정보: guardian/institution scope는 동의와 역할 기반 권한 검사를 통과한 요약 데이터만 반환해야 한다.
- 데이터: sleep analysis는 sensor_event schema와 Phase B ADR 확정 전 구현하지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-009, QRY-009, QRY-004
- Blocks: None
