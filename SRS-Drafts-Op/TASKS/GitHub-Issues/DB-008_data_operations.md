---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] DB-008: API Key `last_used_at`, deletion job status, quota/budget config, AI run metadata 보강 schema 작성"
labels: 'feature, database, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [DB-008] API Key `last_used_at`, deletion job status, quota/budget config, AI run metadata 보강 schema 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-049, REQ-FUNC-059, REQ-NF-032, REQ-NF-044, 6.7 COST-004~007` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `api_key` model에 `last_used_at` nullable datetime 필드와 조회 index 보강 migration을 작성한다.
- [ ] deletion job status 추적 model을 작성해 `job_id`, `tenant_id`, `elder_id` 또는 `device_id`, `request_id`, `target_type`, `target_selector`, `status`, `scheduled_at`, `completed_at`, `error_code`를 저장한다.
- [ ] quota/budget config model을 작성해 tenant별 일/월 호출량 quota, playground quota, budget threshold 50/75/90%, enabled 상태를 저장한다.
- [ ] AI run metadata model을 작성해 `request_id`, tenant, model, routing_reason, prompt/safety policy version, token usage, latency, evaluation result reference를 기록한다.
- [ ] deletion status, quota lookup, AI release history 조회를 위해 `tenant_id`, `request_id`, status, created/scheduled time 기준 index를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: operations migration이 API Key와 운영 보강 모델을 생성함
- Given: 선행 태스크 `DB-002, DB-006, CT-007, CT-008`가 완료됨
- When: `DB-008` migration을 local SQLite와 Supabase PostgreSQL dry-run 기준으로 적용함
- Then: `api_key.last_used_at`, deletion job status, quota/budget config, AI run metadata에 필요한 model, field, tenant/request/status index가 생성되어야 한다.

Scenario 2: API Key 마지막 사용 시각을 관리할 수 있음
- Given: 활성 API Key로 인증된 API 호출이 발생함
- When: 인증 후 API Key 상태를 갱신함
- Then: `last_used_at`이 저장되어 Developer Portal에서 마지막 사용 시각을 조회할 수 있어야 한다.

Scenario 3: deletion job 상태를 User & Consent Admin View에서 조회할 수 있음
- Given: 동의 철회 또는 삭제 요청으로 deletion job이 생성됨
- When: 권한 있는 사용자가 elder 또는 device 상태를 조회함
- Then: 직접 식별정보 없이 deletion job status와 request_id를 확인할 수 있어야 한다.

Scenario 4: quota와 budget guardrail 설정을 조회할 수 있음
- Given: tenant별 quota/budget config가 설정됨
- When: Playground 또는 billable API 실행 전 guardrail을 조회함
- Then: tenant quota, playground daily quota, budget threshold 기준을 코드 변경 없이 읽을 수 있어야 한다.

Scenario 5: AI run metadata는 모델/정책 변경 이력을 추적함
- Given: LLM 호출 또는 AI release validation이 실행됨
- When: AI run metadata를 저장함
- Then: model, routing reason, prompt version, safety policy version, token usage, latency, evaluation result reference가 request_id 기준으로 기록된다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 운영성: `REQ-NF-032`에 따라 모델/프롬프트/안전 정책/평가 결과 변경 이력을 추적해야 한다.
- 비용 통제: `REQ-NF-044`와 COST-004~007에 따라 quota, retry cap, budget alert 기준을 조회 가능하게 저장해야 한다.
- 개인정보: deletion job과 AI run metadata에는 raw transcript와 직접 식별정보를 저장하지 않는다.
- 격리: 운영 보강 모델은 모두 `tenant_id` scope와 접근 권한 검사를 전제로 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-002, DB-006, CT-007, CT-008
- Blocks: SEC-001, SEC-003, CMD-011, QRY-001, QRY-008, NFR-008, NFR-010
