---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] DB-006: `usage_event`, `poc_report_aggregate`, `audit_log` schema와 집계/query index 작성"
labels: 'feature, database, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [DB-006] `usage_event`, `poc_report_aggregate`, `audit_log` schema와 집계/query index 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.2.1~6.2.5, REQ-FUNC-014, REQ-FUNC-022~026, REQ-NF-029~031` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Prisma model `usage_event`를 `usage_event_id`, `tenant_id`, nullable `device_id`, `endpoint`, `request_id`, `usage_unit`, `latency_ms`, `status_code`, `created_at` 필드로 작성한다.
- [ ] Prisma model `poc_report_aggregate`를 `aggregate_id`, `tenant_id`, `period_start`, `period_end`, `active_devices`, `active_elders`, `conversation_count`, `p95_latency_ms`, `error_rate`, `risk_event_count` 필드로 작성한다.
- [ ] Prisma model `audit_log`를 `audit_log_id`, `tenant_id`, `actor_role`, `actor_id`, `action`, `resource_type`, `resource_id`, `request_id`, `created_at` 필드로 작성한다.
- [ ] `usage_event.request_id` unique 제약과 `(tenant_id, device_id, created_at)`, `(tenant_id, endpoint, created_at)`, `(tenant_id, created_at)` 집계 index를 작성한다.
- [ ] PoC report 기간 조회를 위해 `(tenant_id, period_start, period_end)` index를 작성하고 audit 조회를 위해 `request_id`, `(tenant_id, created_at)`, `actor_role`, `resource_type` index를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: usage/reporting migration이 필수 모델과 필드를 생성함
- Given: 선행 태스크 `DB-002, DB-003`가 완료됨
- When: `DB-006` migration을 local SQLite와 Supabase PostgreSQL dry-run 기준으로 적용함
- Then: `usage_event`, `poc_report_aggregate`, `audit_log` 모델과 SRS 6.2.2의 필수 필드, unique/index, tenant FK가 생성되어야 한다.

Scenario 2: usage_event는 request_id 중복 집계를 방지함
- Given: 동일 `request_id`의 billable API 호출 기록이 두 번 저장됨
- When: 두 번째 insert 또는 upsert가 실행됨
- Then: `usage_event.request_id` unique 제약에 의해 중복 usage row가 생성되지 않는다.

Scenario 3: PoC report 필수 지표를 집계할 수 있음
- Given: 기간 내 usage_event와 risk_event 데이터가 존재함
- When: PoC report aggregate query가 실행됨
- Then: active_devices, active_elders, conversation_count, p95_latency_ms, error_rate, risk_event_count를 tenant/period 기준으로 산출할 수 있어야 한다.

Scenario 4: audit_log는 민감 UI/API 작업 추적에 사용할 수 있음
- Given: API Key 발급/폐기, 로그 조회, 동의 상태 조회, 삭제 job 조회 중 하나가 발생함
- When: audit_log를 저장함
- Then: actor, action, resource, request_id, tenant, created_at이 저장되고 원문/PII/API Key 원문은 포함되지 않는다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 비용/FinOps: `REQ-NF-029~031`에 따라 가격표 없이도 사용량과 활성 디바이스 지표를 재계산할 수 있어야 한다.
- 추적성: `REQ-FUNC-014`에 따라 usage/report/audit entity는 가능한 경우 동일 `request_id`로 연결되어야 한다.
- 개인정보: usage_event와 audit_log에는 원문 대화, 직접 식별정보, 원문 API Key를 저장하지 않는다.
- 보존: usage_event, poc_report_aggregate, audit_log는 SRS 6.2.4 보존 정책을 준수할 수 있어야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-002, DB-003
- Blocks: DB-008, MOCK-004, PLAT-003, SEC-005, CMD-007, CMD-010, QRY-004, QRY-006, NFR-004, NFR-006
