---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] FE-008: PoC Report Console 구현: 기간 필터, 지표 표시, CSV/JSON export"
labels: 'feature, frontend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [FE-008] PoC Report Console 구현: 기간 필터, 지표 표시, CSV/JSON export
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-056, REQ-NF-040~041` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] PoC Report Console에 period filter, tenant context, active_devices, active_elders, conversation_count, p95_latency_ms, error_rate, risk_event_count를 표시한다.
- [ ] `QRY-004` metric query와 `QRY-005` export query를 연결하고 loading/empty/error/success state를 구현한다.
- [ ] CSV/JSON export action은 원문/PII 없이 집계값만 다운로드 가능한 payload로 생성한다.
- [ ] B2B-PM role 또는 허용된 운영 role만 report 조회와 export action을 사용할 수 있게 제한한다.
- [ ] baseline dataset 기준 초기 결과 표시 p95 2초 이내와 모바일/데스크톱 레이아웃 overflow를 E2E로 검증한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: B2B-PM은 기간별 PoC 지표를 조회함
- Given: 선행 태스크 `FE-001, QRY-004, QRY-005`가 완료되고 인증된 role/tenant context가 있음
- When: 사용자가 기간 필터를 선택해 리포트를 조회함
- Then: 활성 디바이스, 활성 어르신, 대화 수, p95 latency, 오류율, 위험 이벤트 수가 tenant 기준으로 표시된다.

Scenario 2: CSV/JSON export는 집계값만 포함함
- Given: PoC 리포트가 조회됨
- When: 사용자가 CSV 또는 JSON export를 실행함
- Then: export payload에는 집계 지표만 포함되고 대화 원문과 직접 식별정보는 포함되지 않는다.

Scenario 3: 권한 없는 사용자는 리포트와 export를 사용할 수 없음
- Given: 사용자가 B2B-PM 또는 허용 운영 role이 아님
- When: PoC Report Console에 접근하거나 export를 실행함
- Then: 접근 또는 action이 차단되고 tenant 지표가 표시되지 않는다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 성능: `REQ-NF-040`에 따라 baseline dataset 초기 조회 결과는 p95 2초 이내 표시되어야 한다.
- 접근성: `REQ-NF-041`에 따라 keyboard navigation과 form label을 제공한다.
- 개인정보: PoC Report Console과 export에는 원문/PII가 포함되지 않아야 한다.
- UI 범위: Partner Console만 포함하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: FE-001, QRY-004, QRY-005
- Blocks: TEST-015, TEST-016
