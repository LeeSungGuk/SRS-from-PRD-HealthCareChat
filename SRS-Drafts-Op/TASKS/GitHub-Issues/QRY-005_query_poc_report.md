---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] QRY-005: `/api/v1/report/poc` CSV export 조회 구현: 원문/PII 제외, export audit log 기록"
labels: 'feature, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [QRY-005] `/api/v1/report/poc` CSV export 조회 구현: 원문/PII 제외, export audit log 기록
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `API-004, REQ-FUNC-023, REQ-NF-041` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `/api/v1/report/poc` CSV export input으로 `tenant_id`, `from`, `to`, `format=csv`를 정의한다.
- [ ] CSV column은 QRY-004 집계 필드 중심으로 구성하고 raw utterance, AI reply 원문, 직접 PII column을 금지한다.
- [ ] export 실행 시 `actor_id`, `tenant_id`, 기간, file format, row count, `request_id`를 audit log에 기록한다.
- [ ] CSV 생성 전 SEC-005 PII masking/scan을 적용하고 export 결과에 PII가 없음을 검증한다.
- [ ] empty report export, invalid period, cross-tenant export, audit log 저장 실패 시나리오를 테스트한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: CSV export가 집계값만 포함함
- Given: PoC report 데이터가 존재함
- When: `/api/v1/report/poc?format=csv`를 호출함
- Then: CSV에는 집계 필드만 포함되고 raw transcript, 직접 PII, 개별 발화 원문은 포함되지 않아야 한다.

Scenario 2: export audit log가 생성됨
- Given: 권한 있는 사용자가 CSV export를 실행함
- When: export가 완료됨
- Then: 실행자, tenant, 기간, 파일 형식, request_id가 감사 로그에 기록되어야 한다.

Scenario 3: PII scan 실패 시 export가 차단됨
- Given: CSV 후보 데이터에 직접 PII가 포함되어 있음
- When: export 전 PII scan이 실행됨
- Then: export는 실패하고 감사 로그에 실패 사유가 기록되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 보안/개인정보: export는 NFR-041에 따라 PII 0건과 100% audit log 생성을 만족해야 한다.
- 운영: export 실패도 감사 가능한 이벤트로 남겨야 하며 partial file을 사용자에게 제공하지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: QRY-004, PLAT-003, SEC-005
- Blocks: FE-008, TEST-010
