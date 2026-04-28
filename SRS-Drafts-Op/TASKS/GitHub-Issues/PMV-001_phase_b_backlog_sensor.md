---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] PMV-001: `/api/v2/sensor/ingest` DTO, validation, tenant/device/elder 연결 구현 백로그 등록"
labels: 'feature, post-mvp, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [PMV-001] `/api/v2/sensor/ingest` DTO, validation, tenant/device/elder 연결 구현 백로그 등록
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `API-005, REQ-FUNC-040, REQ-FUNC-046~047` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails`](../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `/api/v2/sensor/ingest`를 Phase B/Post-MVP backlog로 등록하고 Phase A MVP route 구현 대상에서 제외한다.
- [ ] Phase B API contract 초안에 `tenant_id`, `device_id`, `elder_id`, `sensor_type`, `observed_at`, `payload`, `schema_version`, `request_id`, `ingest_status`를 명시한다.
- [ ] validation backlog에 필수 필드 누락, 미지원 `sensor_type`, payload schema 불일치, cross-tenant device/elder 결합 금지를 포함한다.
- [ ] `sensor_event` 데이터 모델과 tenant/device/elder 연결, 시계열 index, 보존 정책 확정 필요 항목을 연결한다.
- [ ] Phase B 착수 전 필요한 PRD/SRS/OpenAPI/Privacy/Test Plan 변경 항목을 체크리스트로 남긴다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: MVP 범위를 차단하지 않는 backlog로 유지됨
- Given: Phase A MVP P0/P1/P2 구현 계획을 검토함
- When: `PMV-001` 항목을 확인함
- Then: `/api/v2/sensor/ingest` route 구현은 Post-MVP로 표시되고 MVP 완료 조건을 막지 않아야 한다.

Scenario 2: Phase B sensor contract가 추적 가능함
- Given: Phase B 센서 수집 구현 검토가 시작됨
- When: `PMV-001` backlog를 조회함
- Then: DTO 필드, validation 항목, tenant/device/elder 연결, 필요한 문서 변경 목록을 확인할 수 있어야 한다.

Scenario 3: Phase A에 실제 센서 route가 생성되지 않음
- Given: Phase A MVP source tree를 검토함
- When: `/api/v2/sensor/ingest` 구현 여부를 확인함
- Then: 실제 production route 또는 UI는 존재하지 않아야 하며 mock/backlog 문서만 존재해야 한다.

## :gear: Technical & Non-Functional Constraints
- 범위: Post-MVP 항목은 MVP P0/P1/P2 완료 조건을 차단하지 않는다.
- 안전: 센서 기반 위험 판단은 Phase B에서도 의료 진단이 아니라 참고 신호로 제한해야 한다.
- 개인정보: sensor payload의 민감도와 보존 기간은 Phase B privacy ADR에서 확정해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-009, CT-004, SEC-002
- Blocks: None
