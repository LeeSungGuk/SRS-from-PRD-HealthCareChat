---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] QRY-007: high/critical, SAFETY_FILTERED, LLM timeout, 신고 케이스 리뷰 큐 조회 구현"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [QRY-007] high/critical, SAFETY_FILTERED, LLM timeout, 신고 케이스 리뷰 큐 조회 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-035, REQ-FUNC-058, REQ-NF-035` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Review Queue query input으로 risk level, event type, review_status, tenant, time range, pagination, sort를 정의한다.
- [ ] high/critical, `SAFETY_FILTERED`, LLM timeout, 보호자/기관 신고 케이스를 `request_id` 기준으로 조회한다.
- [ ] 응답 DTO에 `request_id`, `risk_level`, `event_type`, `review_status`, masked evidence, detected_at, reviewer assignment를 포함한다.
- [ ] 평가용 dataset 추출 시 sample data와 labeling result를 분리하고 PII 제거 상태를 표시한다.
- [ ] queue 누락 audit을 위해 qualifying event 대비 review queue 생성률 100% 검증 쿼리를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 리뷰 대상 이벤트가 queue에 표시됨
- Given: high risk, critical risk, SAFETY_FILTERED, LLM timeout 이벤트가 존재함
- When: Review Queue query를 실행함
- Then: 각 이벤트가 request_id, risk_level, review_status, 마스킹된 근거 데이터와 함께 표시되어야 한다.

Scenario 2: 평가셋 후보는 PII 제거 상태를 포함함
- Given: 운영자가 리뷰 데이터셋 후보를 조회함
- When: dataset candidate query가 실행됨
- Then: sample data와 labeling result가 분리되어 있고 PII 제거 상태가 표시되어야 한다.

Scenario 3: qualifying event coverage가 100%임
- Given: high/critical, SAFETY_FILTERED, LLM timeout 이벤트 목록이 존재함
- When: review queue coverage audit을 실행함
- Then: 모든 qualifying event가 review queue에 연결되어 있어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 보안/개인정보: review queue와 dataset candidate 기본 응답은 원문과 직접 PII를 노출하지 않는다.
- 운영: review_status 변경 command와 조회 query의 책임을 분리하고, 본 태스크는 조회 계약만 다룬다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-005, DB-007, CMD-012
- Blocks: FE-009, TEST-013
