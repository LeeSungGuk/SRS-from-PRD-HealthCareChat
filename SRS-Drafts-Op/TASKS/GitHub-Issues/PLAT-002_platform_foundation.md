---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] PLAT-002: 공통 repository/data access 계층 구현 및 UI에서 repository 직접 import 금지 규칙 적용"
labels: 'feature, platform, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [PLAT-002] 공통 repository/data access 계층 구현 및 UI에서 repository 직접 import 금지 규칙 적용
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `1.5 C-TEC-008~009, 6.3 Component Diagram` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#15-assumptions--constraints`](../../SRS/실버케어_SRS_v0.5_단일구조.md#15-assumptions--constraints)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `tenant`, `identity`, `conversation`, `analysis`, `usage`, `audit`, `report` 등 핵심 repository interface를 정의한다.
- [ ] Prisma Client import를 repository/data access 계층으로 제한하고 API Route/Server Action은 domain service interface만 호출하도록 구조를 분리한다.
- [ ] UI/Client Component가 repository, Prisma Client, LLM provider adapter를 직접 import하지 못하도록 lint rule 또는 dependency review script를 작성한다.
- [ ] local SQLite와 Supabase PostgreSQL 간 차이를 repository 내부에 숨기고 service contract는 동일하게 유지한다.
- [ ] repository query의 기본 조건으로 `tenant_id` scope를 요구하는 테스트 fixture를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: Prisma Client가 repository 계층에만 존재함
- Given: 전체 source tree가 준비되어 있음
- When: import boundary 검사를 실행함
- Then: Prisma Client 직접 import는 repository/data access 계층에서만 발견되어야 한다.

Scenario 2: UI가 repository를 직접 import하지 않음
- Given: Partner Console UI 컴포넌트가 구현되어 있음
- When: dependency review script를 실행함
- Then: UI/Client Component에서 repository/data access 또는 LLM provider adapter 직접 import가 없어야 한다.

Scenario 3: repository query는 tenant scope를 요구함
- Given: tenant context 없이 repository query가 호출됨
- When: data access test를 실행함
- Then: query가 실패하거나 명시적 system-level 예외 승인 없이는 실행되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client와 raw database query는 repository/data access 계층에 격리한다.
- 분리 가능성: 단일구조 MVP라도 domain service와 repository interface는 향후 백엔드 분리 시 재사용 가능한 경계로 유지한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-001, DB-001
- Blocks: None
