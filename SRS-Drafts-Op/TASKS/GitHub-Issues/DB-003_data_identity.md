---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] DB-003: `device`, `elder_profile`, `consent_state` schema와 tenant 격리 index 작성"
labels: 'feature, database, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [DB-003] `device`, `elder_profile`, `consent_state` schema와 tenant 격리 index 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.2.1~6.2.5, REQ-FUNC-036~037, REQ-NF-023` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Prisma model `device`를 `device_id`, `tenant_id`, `device_type`, `activated_at`, `last_seen_at` 필드와 tenant FK/index 기준으로 작성한다.
- [ ] Prisma model `elder_profile`을 `elder_id`, `tenant_id`, `locale`, `consent_state_id`, `memory_enabled`, `created_at` 필드와 tenant FK/index 기준으로 작성한다.
- [ ] Prisma model `consent_state`를 `basic_processing`, `sensitive_processing`, `memory_storage`, `guardian_sharing`, `institution_sharing`, `quality_improvement`, `updated_at` 6개 동의 dimension 기준으로 작성한다.
- [ ] `elder_profile`과 `consent_state`의 1:1 관계, `tenant`와 `device`/`elder_profile`의 1:N 관계를 migration과 Prisma relation으로 고정한다.
- [ ] 직접 식별정보 필드는 추가하지 않고, 가명 `elder_id`, `device_id`, tenant scope 조회 fixture를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: identity migration이 필수 모델과 필드를 생성함
- Given: 선행 태스크 `DB-001, DB-002`가 완료됨
- When: `DB-003` migration을 local SQLite와 Supabase PostgreSQL dry-run 기준으로 적용함
- Then: `device`, `elder_profile`, `consent_state` 모델과 SRS 6.2.2의 필수 필드, PK/FK, tenant index가 생성되어야 한다.

Scenario 2: 동의 6개 dimension이 독립 필드로 저장됨
- Given: elder consent profile을 생성함
- When: `consent_state`를 조회함
- Then: 기본 대화 처리, 민감정보 처리, 메모리 저장, 보호자 공유, 기관 공유, 품질 개선/평가셋 사용 동의를 각각 독립 boolean으로 확인할 수 있어야 한다.

Scenario 3: tenant 격리 조회가 가능함
- Given: 서로 다른 tenant에 같은 형식의 device와 elder profile이 존재함
- When: repository가 tenant scope로 identity 데이터를 조회함
- Then: 요청 tenant의 `device`, `elder_profile`, `consent_state`만 반환되어야 한다.

Scenario 4: 직접 식별정보 필드가 schema에 포함되지 않음
- Given: migration 결과 schema를 검토함
- When: `elder_profile`과 `consent_state` 필드 목록을 확인함
- Then: 실명, 주민등록번호, 상세 주소 등 직접 식별정보 필드가 없어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 개인정보: `REQ-NF-023`에 따라 6개 동의 dimension을 분리 저장해야 한다.
- 격리: tenant-owned identity entity는 `tenant_id` index를 가져야 하며 cross-tenant join을 허용하지 않는다.
- 호환성: local SQLite와 Supabase PostgreSQL 모두에서 migration 적용 또는 dry-run이 가능해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-001, DB-002
- Blocks: DB-004, DB-005, DB-006, DB-009, MOCK-002, SEC-002, QRY-008
