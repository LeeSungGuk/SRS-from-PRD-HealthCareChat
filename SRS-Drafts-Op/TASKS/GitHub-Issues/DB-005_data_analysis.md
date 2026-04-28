---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] DB-005: `analysis_result`, `risk_event`, `evaluation_sample` schema와 review index 작성"
labels: 'feature, database, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [DB-005] `analysis_result`, `risk_event`, `evaluation_sample` schema와 review index 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.2.1~6.2.5, REQ-FUNC-017~019, REQ-FUNC-035, REQ-NF-011~013, REQ-NF-035` 요구를 구현 가능한 작업 단위로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] SRS 데이터 모델과 ``analysis_result`, `risk_event`, `evaluation_sample` schema와 review index 작성` 요구에 맞는 Prisma model 초안을 작성한다.
- [ ] 필수 필드, nullable 여부, primary key, foreign key, unique/index 조건을 정의한다.
- [ ] tenant-owned 데이터인 경우 `tenant_id` 기반 격리 index를 포함한다.
- [ ] local SQLite와 Supabase PostgreSQL에서 migration dry-run 가능한 구조로 작성한다.
- [ ] seed, repository, query/command 태스크가 참조할 데이터 제약을 문서화한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 스키마가 SRS 필드를 반영함
- Given: SRS 관련 섹션 `6.2.1~6.2.5, REQ-FUNC-017~019, REQ-FUNC-035, REQ-NF-011~013, REQ-NF-035`이 주어짐
- When: `DB-005` Prisma schema와 migration을 검토함
- Then: 필수 필드, key, index, relation이 누락 없이 정의되어야 한다.

Scenario 2: 선행 태스크와 연결됨
- Given: 선행 태스크 `DB-003, CT-003`가 완료됨
- When: ``analysis_result`, `risk_event`, `evaluation_sample` schema와 review index 작성` migration을 적용함
- Then: 후속 command/query 태스크가 참조할 수 있는 DB 구조가 생성되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test) 또는 문서 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec 또는 운영 문서가 최신화되었는가?
- [ ] `TASKS/실버케어_SRS_v0.5_단일구조_TASKS.md`의 의존성 기준과 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-003, CT-003
- Blocks: 후속 태스크는 TASKS 원본의 Dependencies 기준으로 연결
