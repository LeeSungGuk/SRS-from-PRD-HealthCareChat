---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] SEC-002: tenant ownership guard와 cross-tenant `TENANT_ACCESS_DENIED` 처리 구현"
labels: 'feature, security, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [SEC-002] tenant ownership guard와 cross-tenant `TENANT_ACCESS_DENIED` 처리 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-005, REQ-FUNC-039, REQ-NF-021` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] tenant ownership guard의 input으로 request context, requested tenant/resource id, resource type을 정의한다.
- [ ] device, elder, conversation, report, API Key 등 tenant 소유 리소스별 ownership lookup 규칙을 작성한다.
- [ ] tenant 불일치 시 HTTP 403과 `TENANT_ACCESS_DENIED`를 반환하고 데이터 payload를 반환하지 않는다.
- [ ] role별 접근 가능 데이터 범위를 RBAC와 결합해 guardian, institution, B2B partner, operator 권한을 분리한다.
- [ ] cross-tenant access test pass rate 100%를 검증하는 통합 테스트 matrix를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 같은 tenant 리소스 접근이 허용됨
- Given: tenant A API Key와 tenant A 소유 device/elder 리소스가 존재함
- When: tenant ownership guard가 실행됨
- Then: 접근이 허용되고 domain service가 계속 실행되어야 한다.

Scenario 2: 다른 tenant 리소스 접근이 차단됨
- Given: tenant A API Key로 tenant B device 또는 elder 리소스를 요청함
- When: tenant ownership guard가 실행됨
- Then: HTTP 403, `TENANT_ACCESS_DENIED`, no data payload가 반환되어야 한다.

Scenario 3: role별 접근 범위가 적용됨
- Given: guardian, institution manager, B2B partner, operator role이 각각 주어짐
- When: 제한 데이터 조회를 시도함
- Then: 각 role에 허용된 데이터만 반환되고 민감 접근은 감사 로그로 남아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 보안/개인정보: cross-tenant 실패 응답은 대상 리소스 존재 여부를 과도하게 노출하지 않아야 한다.
- 테스트: 모든 주요 API contract는 tenant 불일치 negative test를 가져야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-002, DB-003, SEC-001
- Blocks: CMD-003, CMD-008, QRY-004, QRY-009, TEST-002, TEST-012, PMV-001
