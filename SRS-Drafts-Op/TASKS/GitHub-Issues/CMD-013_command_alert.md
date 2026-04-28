---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-013: high/critical 또는 반복 risk_event 기준 alert_event 생성 command 구현"
labels: 'feature, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-013] high/critical 또는 반복 risk_event 기준 alert_event 생성 command 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-028, REQ-NF-035` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] alert 조건을 구현한다: `risk_level=high`, `risk_level=critical`, 또는 정책에서 정한 반복 medium risk_event.
- [ ] alert 대상 `risk_event`를 tenant/elder scope로 조회하고 이미 생성된 alert가 있는 경우 중복 생성하지 않는다.
- [ ] `alert_event`를 `delivery_status=pending`으로 생성하고 `risk_level`, `risk_reason`, `recommended_action`, `request_id`, `detected_at`을 포함한 원문 미포함 payload를 저장한다.
- [ ] recipient type은 guardian, institution, partner 중 허용값으로 제한하고 공유 동의 또는 Phase B 정책 조건을 확인한다.
- [ ] 외부 Webhook 전송 자체는 후속 Phase B delivery 태스크로 분리하고, 이 태스크는 alert event 생성과 추적 상태까지로 범위를 고정한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: high/critical risk_event는 alert_event를 생성함
- Given: `DB-007, CMD-007`가 완료되고 `risk_level=high` 또는 `critical`인 risk_event가 있음
- When: alert command가 실행됨
- Then: 동일 tenant/elder/request_id를 참조하는 `alert_event`가 `delivery_status=pending`으로 생성된다.

Scenario 2: alert payload에는 원문 대화가 포함되지 않음
- Given: alert 대상 risk_event와 연결된 message가 있음
- When: alert payload가 생성됨
- Then: payload에는 `risk_level`, `risk_reason`, `recommended_action`, `request_id`, `detected_at`만 포함되고 raw transcript와 직접 식별정보는 포함되지 않는다.

Scenario 3: 동일 risk_event는 중복 alert를 만들지 않음
- Given: 특정 `risk_event_id`로 이미 `alert_event`가 생성되어 있음
- When: alert command가 같은 risk_event로 다시 실행됨
- Then: 신규 alert_event가 중복 생성되지 않고 기존 delivery 상태를 반환한다.

Scenario 4: 공유 동의 또는 Phase B 정책 조건이 없으면 alert를 생성하지 않음
- Given: 보호자/기관 공유 동의 또는 파트너 알림 정책 조건이 충족되지 않음
- When: alert command가 실행됨
- Then: alert_event는 생성되지 않고 정책상 미생성 사유가 기록된다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 범위: Phase A에서는 alert_event 생성과 추적 상태를 구현하고, 외부 Webhook delivery는 Phase B/Post-MVP 태스크에서 확정한다.
- 개인정보: alert payload는 원문 미포함, 권장 액션 중심이어야 한다.
- Review Loop: `high`, `critical`, 반복 위험 신호는 `REQ-NF-035` 리뷰 큐와도 연결 가능해야 한다.
- 데이터 접근: risk_event와 alert_event 조회/생성은 항상 `tenant_id` 조건을 포함한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-007, CMD-007
- Blocks: TEST-013, NFR-002, PMV-002
