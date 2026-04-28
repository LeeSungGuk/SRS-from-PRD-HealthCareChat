---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] DB-005: `analysis_result`, `risk_event`, `evaluation_sample` schema와 review index 작성"
labels: 'feature, database, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [DB-005] `analysis_result`, `risk_event`, `evaluation_sample` schema와 review index 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.2.1~6.2.5, REQ-FUNC-017~019, REQ-FUNC-035, REQ-NF-011~013, REQ-NF-035` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Prisma model `analysis_result`를 `analysis_id`, `request_id`, `tenant_id`, `elder_id`, `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action`, `created_at` 필드로 작성한다.
- [ ] Prisma model `risk_event`를 `risk_event_id`, `request_id`, `tenant_id`, `elder_id`, `risk_level`, `risk_reason`, `recommended_action`, `detected_at`, `review_status` 필드로 작성한다.
- [ ] Prisma model `evaluation_sample`을 `sample_id`, `source_request_id`, `utterance_masked`, expected label fields, `labeling_rationale`, `review_status`, `expires_at` 필드로 작성한다.
- [ ] `risk_level`, `recommended_action`, `review_status` enum은 SRS 6.2.6 값과 일치하도록 공통 enum 계약을 재사용한다.
- [ ] review queue, 위험 우선순위, 평가셋 후보 만료를 위해 `(tenant_id, risk_level, detected_at)`, `(elder_id, detected_at)`, `(expires_at, review_status, source_request_id)` index를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: analysis migration이 필수 모델과 필드를 생성함
- Given: 선행 태스크 `DB-003, CT-003`가 완료됨
- When: `DB-005` migration을 local SQLite와 Supabase PostgreSQL dry-run 기준으로 적용함
- Then: `analysis_result`, `risk_event`, `evaluation_sample` 모델과 SRS 6.2.2의 필수 필드, PK/FK, tenant/review/retention index가 생성되어야 한다.

Scenario 2: enum 값은 SRS 계약 외 값을 허용하지 않음
- Given: 분석 결과 또는 위험 이벤트를 저장함
- When: `risk_level`, `recommended_action`, `review_status` 값을 검증함
- Then: SRS에 정의된 enum 외 값은 schema validation 또는 repository validation에서 거부되어야 한다.

Scenario 3: medium 이상 위험 이벤트와 리뷰 큐 조회를 지원함
- Given: `risk_level=medium`, `high`, `critical`인 risk_event가 존재함
- When: 운영 리뷰 대상 조회 query를 실행함
- Then: tenant, risk level, detected_at 기준으로 정렬·필터링할 수 있어야 한다.

Scenario 4: evaluation sample 후보는 PII 제거와 30일 만료를 지원함
- Given: evaluation sample 후보를 생성함
- When: 저장된 레코드를 확인함
- Then: `utterance_masked`만 저장되고 `expires_at`은 생성 시점 기준 최대 30일 이내여야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- AI 품질: `REQ-NF-011~013`에 필요한 recall/false-positive 평가 로그 산출이 가능해야 한다.
- 개인정보: `analysis_result`와 `risk_event`에는 진단 표현과 원문 대화 저장을 금지한다.
- 보존: `analysis_result`는 최대 180일, `risk_event`는 최대 1년, `evaluation_sample` 후보는 최대 30일 정책을 지원해야 한다.
- 격리: 모든 analysis/review entity는 tenant scope 조회와 cross-tenant 접근 차단을 전제로 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-003, CT-003
- Blocks: DB-007, MOCK-004, CMD-007, CMD-008, CMD-012, QRY-007, QRY-009, NFR-006
