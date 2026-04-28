---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] DB-001: Prisma·SQLite·Supabase PostgreSQL 영속성 기반 구성"
labels: 'feature, database, infrastructure, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [DB-001] Prisma, local SQLite, Supabase PostgreSQL datasource와 migration 실행 기준 구성
- 목적: Phase A MVP의 데이터 모델을 local SQLite에서 빠르게 개발하고, 배포 시 Supabase PostgreSQL로 migration dry-run이 가능한 단일 영속성 기반을 만든다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#15-assumptions--constraints`](../../SRS/실버케어_SRS_v0.5_단일구조.md#15-assumptions--constraints)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#63-detailed-interaction-models`](../../SRS/실버케어_SRS_v0.5_단일구조.md#63-detailed-interaction-models)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Prisma schema baseline과 datasource 환경 변수 규칙을 정의한다.
- [ ] local development datasource를 SQLite로 구성한다.
- [ ] deployment datasource를 Supabase PostgreSQL로 구성한다.
- [ ] migration 생성, local apply, Supabase dry-run 실행 절차를 정의한다.
- [ ] Prisma Client가 repository/data access 계층에서만 사용되도록 연결 기준을 정의한다.
- [ ] CI 또는 release gate에서 migration drift를 감지할 수 있는 검증 명령을 정의한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 로컬 SQLite 개발환경이 동작함
- Given: 개발자가 로컬 환경 변수를 설정함
- When: Prisma migration을 local SQLite에 적용함
- Then: Prisma schema 기준으로 local DB가 생성되고 Prisma Client가 연결되어야 한다.

Scenario 2: Supabase PostgreSQL migration dry-run 가능
- Given: Supabase PostgreSQL 연결 문자열이 설정됨
- When: migration dry-run 또는 diff 검증을 수행함
- Then: 배포 DB에 적용 가능한 migration 계획을 확인할 수 있어야 한다.

Scenario 3: DB 접근 경계 유지
- Given: application code에서 DB query가 필요함
- When: Prisma Client 사용 위치를 검토함
- Then: Prisma Client는 repository/data access 계층에 격리되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 기술 스택: Prisma + local SQLite + Supabase PostgreSQL을 사용한다.
- 아키텍처: 별도 backend server 없이 Next.js App Router 단일 애플리케이션 내부에서 사용한다.
- 유지보수성: DB query는 UI 또는 Client Component에서 직접 수행하지 않는다.
- 배포: Supabase migration dry-run이 통과해야 P0 persistence 기반 완료로 본다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] local SQLite datasource와 Supabase PostgreSQL datasource 기준이 정의되었는가?
- [ ] migration 생성/apply/dry-run 절차가 문서화되었는가?
- [ ] repository/data access 계층 격리 기준이 명시되었는가?
- [ ] DB-002 이후 schema 태스크가 같은 기준으로 migration을 작성할 수 있는가?

## :construction: Dependencies & Blockers
- Depends on: CT-001
- Blocks: DB-002, DB-003, DB-009, PLAT-002, NFR-003, NFR-004, NFR-005

