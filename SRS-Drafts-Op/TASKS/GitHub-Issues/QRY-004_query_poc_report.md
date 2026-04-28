---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] QRY-004: `/api/v1/report/poc` JSON 집계 조회 구현"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [QRY-004] `/api/v1/report/poc` JSON 집계 조회 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `API-004, REQ-FUNC-022, REQ-FUNC-024~025` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `/api/v1/report/poc` JSON query input으로 `tenant_id`, `from`, `to`, optional `group_by`, `format=json`을 정의한다.
- [ ] 응답 DTO에 `active_devices`, `active_elders`, `conversation_count`, `avg_conversation_per_elder`, `p95_latency_ms`, `error_rate`, `risk_event_count`를 필수 포함한다.
- [ ] 월 활성 디바이스는 해당 월 1회 이상 유효한 대화 API 호출을 발생시킨 중복 없는 `device_id` 기준으로 산출한다.
- [ ] tenant 단위 목표 대비 결과와 예상 과금 검증 지표를 `usage_event`와 `poc_report_aggregate`에서 재계산 가능하게 연결한다.
- [ ] 기간 오류, 빈 데이터, cross-tenant 조회, 권한 없는 role 요청에 대한 표준 오류 또는 빈 집계 응답 테스트를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: PoC JSON 집계 필드가 반환됨
- Given: 특정 tenant의 PoC 기간 내 사용량, 지연시간, 오류, 위험 이벤트 데이터가 존재함
- When: `/api/v1/report/poc?format=json`을 호출함
- Then: active_devices, active_elders, conversation_count, avg_conversation_per_elder, p95_latency_ms, error_rate, risk_event_count가 JSON으로 반환되어야 한다.

Scenario 2: 월 활성 디바이스가 중복 없이 산출됨
- Given: 동일 월에 같은 device가 여러 번 대화 API를 호출함
- When: 월간 PoC report를 생성함
- Then: 해당 device는 active_devices 계산에서 1개로만 집계되어야 한다.

Scenario 3: cross-tenant report 조회가 차단됨
- Given: tenant A API Key로 tenant B report를 요청함
- When: report query guard가 실행됨
- Then: `TENANT_ACCESS_DENIED`가 반환되고 집계 payload는 반환되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 보안/개인정보: JSON report에는 raw transcript, 직접 PII, 개별 발화 원문을 포함하지 않는다.
- 비용: 정확한 원화 단가가 없어도 활성 디바이스와 호출량 기반의 과금 검증 지표는 산출 가능해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-006, CMD-010, SEC-002
- Blocks: QRY-005, QRY-009, FE-008, TEST-010, PMV-003
