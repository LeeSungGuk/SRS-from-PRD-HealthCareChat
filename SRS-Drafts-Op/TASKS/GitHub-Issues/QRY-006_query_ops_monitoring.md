---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] QRY-006: request log, endpoint, tenant, latency, error code, safety status 필터 조회 구현"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [QRY-006] request log, endpoint, tenant, latency, error code, safety status 필터 조회 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-033, REQ-FUNC-057, REQ-NF-037` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Ops log query input으로 `tenant_id`, `request_id`, endpoint, latency range, error_code, safety_policy_status, alert_status, time range, pagination을 정의한다.
- [ ] 응답 DTO에 `request_id`, endpoint, tenant, latency, error_code, safety policy status, alert status, timestamp를 포함한다.
- [ ] raw transcript와 직접 PII는 기본 응답에서 제외하고 필요 시 마스킹된 근거 데이터만 반환한다.
- [ ] platform operator와 tenant operator의 조회 범위를 RBAC와 tenant guard로 분리한다.
- [ ] invalid filter, empty result, forbidden role, cross-tenant filter, PII masking 테스트를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 운영 로그가 필수 운영 필드를 반환함
- Given: 운영자가 endpoint와 error_code 조건으로 로그를 조회함
- When: Ops log query가 실행됨
- Then: request_id, endpoint, tenant, latency, error_code, safety policy status, alert status가 반환되어야 한다.

Scenario 2: 로그 조회 결과가 기본적으로 마스킹됨
- Given: 로그 후보에 raw transcript 또는 직접 PII가 포함되어 있음
- When: Ops log response payload를 생성함
- Then: 원문과 직접 PII는 제외 또는 마스킹되어야 한다.

Scenario 3: tenant operator는 자기 tenant 로그만 조회함
- Given: tenant A 운영자가 tenant B 조건으로 로그를 조회함
- When: query guard가 실행됨
- Then: `TENANT_ACCESS_DENIED` 또는 empty result가 반환되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 보안/개인정보: Web Console 로그 조회 기본 화면의 PII/raw transcript exposure count는 0이어야 한다.
- 성능: baseline 데이터셋에서 초기 조회 결과는 NFR-040의 p95 2초 이내 목표를 방해하지 않아야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-006, SEC-005, CT-006
- Blocks: FE-009, TEST-013
