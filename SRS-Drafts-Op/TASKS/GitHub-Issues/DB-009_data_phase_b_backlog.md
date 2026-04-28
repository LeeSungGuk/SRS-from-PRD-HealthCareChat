---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] DB-009: `sensor_event` schema와 Phase B index를 migration backlog로 분리 관리"
labels: 'feature, database, priority:low'
assignees: ''
---

## :dart: Summary
- 기능명: [DB-009] `sensor_event` schema와 Phase B index를 migration backlog로 분리 관리
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.2.1~6.2.6, REQ-FUNC-040, REQ-FUNC-046~047` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Phase B backlog 문서 또는 비활성 migration draft로 Prisma model `sensor_event`의 field contract를 정의한다: `sensor_event_id`, `tenant_id`, `device_id`, nullable `elder_id`, `sensor_type`, `observed_at`, `payload`, `ingested_at`.
- [ ] Phase B API `/api/v2/sensor/ingest`의 필수 validation 기준인 `sensor_type`, `observed_at`, `payload`와 tenant/device/elder 연결 조건을 schema checklist에 고정한다.
- [ ] 시계열 조회와 복합 위험 판단을 위해 `(tenant_id, device_id, observed_at)`, `(elder_id, observed_at)`, `sensor_type` index 후보를 문서화한다.
- [ ] 이 태스크는 MVP 활성 migration에 포함하지 않고 Phase B/Post-MVP migration backlog로 분리한다.
- [ ] Phase B command/query/PMV 태스크가 참조할 fixture shape와 cross-tenant 결합 금지 테스트 기준을 문서화한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: sensor_event backlog가 Phase B schema contract를 정의함
- Given: 선행 태스크 `DB-001, DB-003`가 완료됨
- When: `DB-009` backlog artifact를 검토함
- Then: `sensor_event` 필드, PK/FK, tenant/device/elder 관계, Phase B index 후보가 SRS 6.2 기준으로 명시되어야 한다.

Scenario 2: MVP 활성 migration에는 sensor_event가 포함되지 않음
- Given: Phase A MVP migration set을 검토함
- When: active migration path를 확인함
- Then: `sensor_event` 물리 테이블은 활성 MVP migration에 포함되지 않고 Phase B backlog로만 추적되어야 한다.

Scenario 3: Phase B ingest validation 기준이 테스트 가능하게 기록됨
- Given: `/api/v2/sensor/ingest`의 Phase B 구현을 준비함
- When: backlog checklist를 확인함
- Then: `sensor_type`, `observed_at`, `payload` 필수성, 지원 schema 여부, `INVALID_REQUEST` 조건이 명시되어야 한다.

Scenario 4: cross-tenant sensor 결합 금지 기준이 명시됨
- Given: 다른 tenant의 device 또는 elder 식별자가 포함된 sensor ingest 요청이 있음
- When: Phase B 권한 검증 기준을 확인함
- Then: `TENANT_ACCESS_DENIED` 반환 조건과 tenant/device/elder 연결 검증 기준이 명시되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 범위: `sensor_event`는 Post-MVP/Phase B 추적성 유지 대상이며 Phase A MVP 핵심 구현 범위에 포함하지 않는다.
- 격리: Phase B 설계에서도 tenant/device/elder 결합은 tenant scope를 반드시 검증해야 한다.
- 보존: sensor_event 보존기간은 Phase B ADR에서 확정하되, 계약 종료와 elder 삭제 요청을 삭제 트리거로 고려해야 한다.
- 호환성: Phase B 분리 시에도 외부 API 계약과 주요 데이터 모델 ID는 유지해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-001, DB-003
- Blocks: PMV-001, PMV-002, PMV-003
