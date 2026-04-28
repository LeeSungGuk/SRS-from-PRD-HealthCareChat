---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] PMV-002: `/api/v2/alert/emergency` 및 outbound webhook signature/delivery 구현 백로그 등록"
labels: 'feature, post-mvp, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [PMV-002] `/api/v2/alert/emergency` 및 outbound webhook signature/delivery 구현 백로그 등록
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `API-006~007, REQ-FUNC-041, REQ-FUNC-028` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails`](../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `/api/v2/alert/emergency` inbound API와 partner outbound webhook을 Phase B/Post-MVP backlog로 등록한다.
- [ ] inbound contract 초안에 `tenant_id`, `elder_id`, `risk_event_id`, optional `sensor_event_ids`, `schema_version`, `request_id`, `alert_status`, `webhook_delivery_id`를 명시한다.
- [ ] outbound webhook payload 초안에 `risk_level`, `risk_reason`, `recommended_action`, `request_id`, `detected_at`, partner-safe elder alias를 포함한다.
- [ ] webhook signature, retry, delivery status, deduplication, partner endpoint failure 처리 항목을 Phase B 기술 검토로 남긴다.
- [ ] 실시간 119/응급기관 자동 연결은 구현하지 않고 보호자/기관/파트너 서버 알림으로 제한한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: MVP 범위를 차단하지 않는 backlog로 유지됨
- Given: Phase A MVP P0/P1/P2 구현 계획을 검토함
- When: `PMV-002` 항목을 확인함
- Then: emergency API와 outbound webhook 구현은 Post-MVP로 표시되고 MVP 완료 조건을 막지 않아야 한다.

Scenario 2: webhook 구현 전 검토 항목이 추적 가능함
- Given: Phase B 긴급 알림 구현 검토가 시작됨
- When: `PMV-002` backlog를 조회함
- Then: inbound/outbound contract, signature, retry, delivery status, privacy/test 변경 항목을 확인할 수 있어야 한다.

Scenario 3: Phase A에서 자동 응급기관 연결이 제외됨
- Given: MVP 범위 검토를 수행함
- When: emergency alert 관련 route와 UI를 확인함
- Then: 실시간 119/응급기관 자동 연결 기능은 존재하지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 범위: Post-MVP 항목은 MVP P0/P1/P2 완료 조건을 차단하지 않는다.
- 안전: 알림은 위험 신호 전달과 권장 확인 액션으로 제한하며 의료/응급 판단을 자동 확정하지 않는다.
- 개인정보: webhook payload는 partner-safe alias를 우선 사용하고 원문 대화와 직접 PII를 기본 포함하지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-007, DB-009, CMD-013
- Blocks: None
