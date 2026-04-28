---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] DB-007: `alert_event` schema와 delivery/review status index 작성"
labels: 'feature, database, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [DB-007] `alert_event` schema와 delivery/review status index 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.2.1~6.2.5, REQ-FUNC-028, REQ-NF-035` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Prisma model `alert_event`를 `alert_event_id`, `tenant_id`, `elder_id`, `risk_event_id`, `recipient_type`, `payload`, `delivery_status`, `detected_at`, `sent_at` 필드로 작성한다.
- [ ] `recipient_type` enum은 `guardian`, `institution`, `partner`만 허용하고 `delivery_status` enum은 `pending`, `sent`, `failed`, `retrying`만 허용한다.
- [ ] `risk_event` 1:N `alert_event` relation과 tenant/elder FK를 작성한다.
- [ ] delivery 조회와 재시도 대상을 위해 `risk_event_id`, `(tenant_id, delivery_status, detected_at)` index를 작성한다.
- [ ] alert payload fixture는 원문 대화 없이 `risk_level`, `risk_reason`, `recommended_action`, `request_id`, `detected_at` 중심으로 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: alert_event migration이 필수 모델과 필드를 생성함
- Given: 선행 태스크 `DB-005`가 완료됨
- When: `DB-007` migration을 local SQLite와 Supabase PostgreSQL dry-run 기준으로 적용함
- Then: `alert_event` 모델과 SRS 6.2.2의 필수 필드, risk_event relation, tenant/delivery index가 생성되어야 한다.

Scenario 2: delivery status enum은 허용값만 저장함
- Given: alert delivery 상태를 저장함
- When: `delivery_status` 값을 검증함
- Then: `pending`, `sent`, `failed`, `retrying` 외 값은 거부되어야 한다.

Scenario 3: 알림 재시도 대상 조회를 지원함
- Given: `delivery_status=failed` 또는 `retrying`인 alert_event가 존재함
- When: tenant별 delivery queue query를 실행함
- Then: `(tenant_id, delivery_status, detected_at)` 기준으로 조회·정렬할 수 있어야 한다.

Scenario 4: alert payload에는 원문과 직접 식별정보가 포함되지 않음
- Given: alert_event payload를 저장함
- When: payload JSON을 검사함
- Then: raw transcript, 상세 주소, 주민등록번호, 원문 API Key가 포함되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 범위: Phase A에서는 alert_event 추적 schema를 제공하고 외부 Webhook delivery 구현은 Phase B/Post-MVP와 연결한다.
- Review Loop: alert_event는 `REQ-NF-035` 리뷰 큐와 연결 가능한 `risk_event_id`를 유지해야 한다.
- 개인정보: payload는 원문 미포함, 권장 액션 중심이어야 한다.
- 격리: alert_event 조회와 갱신은 항상 `tenant_id` scope를 포함해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-005
- Blocks: CMD-013, QRY-007, PMV-002
