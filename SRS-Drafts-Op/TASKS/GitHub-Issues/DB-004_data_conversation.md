---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] DB-004: `conversation`, `message`, `memory_summary` schema와 retention/index 작성"
labels: 'feature, database, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [DB-004] `conversation`, `message`, `memory_summary` schema와 retention/index 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.2.1~6.2.5, REQ-FUNC-010~011, REQ-FUNC-037, REQ-NF-024~025` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Prisma model `conversation`을 `conversation_id`, `tenant_id`, nullable `device_id`, `elder_id`, `started_at`, `ended_at` 필드와 tenant/elder/device index 기준으로 작성한다.
- [ ] Prisma model `message`를 `message_id`, `conversation_id`, `request_id`, `speaker`, `text_masked`, `text_raw_ref`, `created_at` 필드와 request/conversation index 기준으로 작성한다.
- [ ] Prisma model `memory_summary`를 `memory_id`, `tenant_id`, `elder_id`, `summary`, `source_request_id`, `expires_at`, `created_at` 필드와 만료 조회 index 기준으로 작성한다.
- [ ] `conversation` 1:N `message`, `elder_profile` 1:N `conversation`, `elder_profile` 1:N `memory_summary` 관계를 migration과 Prisma relation으로 고정한다.
- [ ] raw transcript는 DB 본문 저장이 아니라 `text_raw_ref`로만 참조하고 `text_masked`를 기본 저장 대상으로 하는 fixture를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: conversation migration이 필수 모델과 필드를 생성함
- Given: 선행 태스크 `DB-003, CT-008`가 완료됨
- When: `DB-004` migration을 local SQLite와 Supabase PostgreSQL dry-run 기준으로 적용함
- Then: `conversation`, `message`, `memory_summary` 모델과 SRS 6.2.2의 필수 필드, PK/FK, tenant/index/retention 필드가 생성되어야 한다.

Scenario 2: message는 request_id로 추적 가능하고 원문 직접 저장을 피함
- Given: chat reply command가 elder 발화를 저장함
- When: `message` 레코드를 생성함
- Then: `request_id`, `speaker`, `created_at`이 필수로 저장되고 원문은 `text_raw_ref` 참조 또는 `text_masked`로만 저장되어야 한다.

Scenario 3: memory_summary는 만료 처리를 위한 index를 가짐
- Given: memory summary가 생성됨
- When: retention job이 만료 대상을 조회함
- Then: `expires_at` 및 `(elder_id, expires_at)` 기준으로 최대 180일 보존 제한을 처리할 수 있어야 한다.

Scenario 4: 메모리 미동의 상태에서 memory_summary를 조회하지 않는 후속 command를 지원함
- Given: `consent_state.memory_storage=false`인 elder가 있음
- When: 후속 `CMD-004`가 memory context를 판단함
- Then: tenant/elder scope와 consent state를 기준으로 memory store 접근을 차단할 수 있는 relation과 query path가 존재해야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 개인정보: `REQ-NF-024`에 따라 raw transcript는 기본 30일 이내 보존 대상으로 분리되어야 한다.
- 개인정보: `REQ-NF-025`에 따라 memory summary는 동의 유지 중 최대 180일까지만 보존한다.
- 격리: conversation과 memory summary 조회는 `tenant_id`, `elder_id`, 필요 시 `device_id` 조건을 포함해야 한다.
- 호환성: local SQLite와 Supabase PostgreSQL 모두에서 migration 적용 또는 dry-run이 가능해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-003, CT-008
- Blocks: SEC-005, CMD-003, CMD-004, NFR-006
