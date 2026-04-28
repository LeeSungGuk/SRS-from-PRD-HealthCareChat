---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] FE-009: Ops Monitoring Console 구현: 로그 필터, analysis view, alert status, review queue 이동"
labels: 'feature, frontend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [FE-009] Ops Monitoring Console 구현: 로그 필터, analysis view, alert status, review queue 이동
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-057~058, REQ-NF-037, REQ-NF-040` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Ops Monitoring Console에 endpoint, tenant, latency, status_code, error_code, safety_policy_applied, alert status filter를 구현한다.
- [ ] Conversation Log View에는 `request_id`, masked utterance, latency, status_code, safety_policy_applied만 기본 표시한다.
- [ ] Emotion/Risk Analysis View에는 emotion_tags, risk_level, risk_reason, recommended_action, review_status를 표시하되 의료 진단 표현과 원문은 표시하지 않는다.
- [ ] Review Queue 이동 링크는 `high`, `critical`, `SAFETY_FILTERED`, LLM timeout, 신고 케이스를 `request_id` 기준으로 연결한다.
- [ ] 플랫폼 운영자 또는 reviewer role만 접근 가능하도록 route/action guard와 privacy scan 테스트를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 운영자는 마스킹된 로그를 필터링해 조회함
- Given: 선행 태스크 `FE-001, QRY-006, QRY-007, UX-003`가 완료되고 인증된 role/tenant context가 있음
- When: 운영자가 endpoint, status, error code, safety policy 조건으로 로그를 필터링함
- Then: `request_id`, tenant, endpoint, latency, status_code, masked evidence가 표시된다.

Scenario 2: 리뷰 큐 대상은 request_id 기준으로 이동 가능함
- Given: `high`, `critical`, `SAFETY_FILTERED`, LLM timeout 중 하나의 이벤트가 존재함
- When: 운영자가 이벤트 행의 review link를 선택함
- Then: 동일 `request_id`의 Review Queue 상세 또는 필터된 목록으로 이동한다.

Scenario 3: 기본 UI는 원문과 직접 식별정보를 노출하지 않음
- Given: 로그 또는 분석 결과에 원문 대화가 연결될 수 있음
- When: Ops Monitoring Console을 렌더링함
- Then: raw transcript, 실명, 주민등록번호, 상세 주소, 원문 API Key가 표시되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 개인정보: `REQ-NF-037`에 따라 대화 로그, 분석 결과, 리뷰 큐는 기본적으로 원문과 직접 식별정보를 마스킹한다.
- 성능: `REQ-NF-040`에 따라 baseline dataset 초기 조회 결과는 p95 2초 이내 표시되어야 한다.
- 접근 제어: operator 또는 reviewer role 외 접근을 차단한다.
- UI 범위: Partner Console만 포함하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: FE-001, QRY-006, QRY-007, UX-003
- Blocks: TEST-015, TEST-016
