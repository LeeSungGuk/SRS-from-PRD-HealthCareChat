---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-010: billable API 호출 usage_event 기록 공통 middleware/service 구현"
labels: 'feature, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-010] billable API 호출 usage_event 기록 공통 middleware/service 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-026, REQ-NF-029~031` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] billable API endpoint 목록과 사용량 기록 대상 조건을 정의한다: chat reply, emotion analyze, PoC report API를 기본 대상으로 한다.
- [ ] Route Handler 또는 service wrapper에서 tenant, endpoint, device, request_id, usage_unit, latency_ms, status_code, created_at을 수집한다.
- [ ] `usage_event.request_id` unique 제약을 기준으로 중복 집계를 방지한다.
- [ ] 성공 응답뿐 아니라 표준 오류로 종료된 billable 호출도 상태 코드와 latency를 기록한다.
- [ ] 월 활성 디바이스, 호출량, 초과 사용 가능성 지표를 raw `usage_event`에서 재계산할 수 있도록 replay 테스트를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: billable API 호출은 usage_event를 1건 생성함
- Given: `DB-006, PLAT-001`가 완료되고 `/api/v1/chat/reply` 또는 `/api/v1/analyze/emotion` 호출이 발생함
- When: usage recording service가 실행됨
- Then: tenant, endpoint, device, request_id, usage_unit, latency_ms, status_code, created_at을 포함한 `usage_event`가 1건 저장된다.

Scenario 2: 동일 request_id는 중복 집계되지 않음
- Given: 동일 `request_id`로 usage recording service가 두 번 호출됨
- When: 두 번째 기록 처리가 실행됨
- Then: `usage_event.request_id` unique 정책에 따라 중복 레코드가 생성되지 않는다.

Scenario 3: 표준 오류 응답도 사용량과 장애 분석용으로 기록됨
- Given: billable API 호출이 `INVALID_REQUEST`, `RATE_LIMIT_EXCEEDED`, `LLM_TIMEOUT` 중 하나로 종료됨
- When: usage recording service가 실행됨
- Then: 실패 status_code와 latency_ms가 포함된 `usage_event`가 저장되고 원문 요청 본문은 저장되지 않는다.

Scenario 4: 월별 지표는 가격표 없이 재계산 가능함
- Given: 한 달치 `usage_event` 데이터가 존재함
- When: billing replay test가 실행됨
- Then: 월 활성 디바이스, endpoint별 호출량, 초과 사용 가능성 지표를 원화 가격표 없이 산출할 수 있다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 비용/FinOps: `REQ-NF-029`에 따라 모든 billable API 호출의 usage event 누락률은 0%여야 한다.
- 비용/FinOps: `REQ-NF-030`에 따라 월별 집계는 raw usage event에서 재현 가능해야 한다.
- 비용/FinOps: `REQ-NF-031`에 따라 PoC 단계에서는 원화 가격표 없이 활성 디바이스와 호출량 지표를 산출한다.
- 개인정보: `usage_event`에는 원문 발화, 분석 근거, API Key 원문을 저장하지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-006, PLAT-001
- Blocks: QRY-004, TEST-017, NFR-001, NFR-008
