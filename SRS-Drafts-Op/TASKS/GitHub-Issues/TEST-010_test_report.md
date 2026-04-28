---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-010: PoC report JSON/CSV 필수 지표, active device distinct, 원문/PII 미포함 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-010] PoC report JSON/CSV 필수 지표, active device distinct, 원문/PII 미포함 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-022~026, REQ-NF-030~031, REQ-NF-041` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] PoC report JSON이 active_devices, active_elders, conversation_count, avg_conversation_per_elder, p95_latency_ms, error_rate, risk_event_count를 반환하는 테스트를 작성한다.
- [ ] 월 활성 디바이스가 동일 월 유효 대화 호출을 발생시킨 distinct `device_id` 기준인지 replay 테스트를 작성한다.
- [ ] CSV export가 JSON 집계와 동일한 집계값을 포함하는지 비교 테스트를 작성한다.
- [ ] JSON/CSV export 모두 raw transcript와 직접 PII를 포함하지 않는 scan test를 작성한다.
- [ ] export 실행 시 audit log가 생성되는지 확인한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: JSON report 필수 지표가 반환됨
- Given: PoC 기간의 usage, latency, error, risk event 데이터가 존재함
- When: `/api/v1/report/poc?format=json`을 호출함
- Then: SRS 필수 집계 지표가 모두 반환되어야 한다.

Scenario 2: active device는 distinct 기준으로 계산됨
- Given: 동일 device가 같은 월에 여러 번 유효 대화 API를 호출함
- When: 월간 active_devices를 집계함
- Then: 해당 device는 1개로 계산되어야 한다.

Scenario 3: CSV export는 PII 없이 감사 로그를 남김
- Given: PoC report CSV export를 실행함
- When: export 파일과 audit log를 확인함
- Then: 파일에는 원문/PII가 없고 audit log에는 실행자, tenant, 기간, 파일 형식이 기록되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안/개인정보: report JSON/CSV의 PII 노출은 0건이어야 한다.
- 비용: PoC report는 원화 가격표 없이도 활성 디바이스와 호출량 기반 지표를 산출해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: QRY-004, QRY-005
- Blocks: TEST-018
