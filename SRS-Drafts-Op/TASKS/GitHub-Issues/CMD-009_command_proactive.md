---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-009: `/api/v1/schedule/proactive` validation과 일반 안부/확인성 발화 생성 command 구현"
labels: 'feature, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-009] `/api/v1/schedule/proactive` validation과 일반 안부/확인성 발화 생성 command 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `API-003, REQ-FUNC-020~021` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `/api/v1/schedule/proactive` 요청 DTO에서 `tenant_id`, `device_id`, `elder_id`, `trigger_type` 필수값과 optional `scheduled_at`, `use_memory`를 검증한다.
- [ ] `trigger_type`은 `morning`, `meal`, `sleep`, `check_in`, `custom`만 허용하고 미지원 값은 `INVALID_REQUEST`로 처리한다.
- [ ] `CMD-004` memory consent 판단을 재사용해 동의가 있을 때만 메모리 context를 사용한다.
- [ ] `CMD-005` provider adapter와 `CMD-006` safety policy를 통해 일반 안부/확인성 발화만 생성한다.
- [ ] 생성 응답에는 `schema_version`, `request_id`, `reply_text`, `memory_used`, `safety_policy_applied`를 포함하고 복약 지시·처방 문구를 금지한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 지원하지 않는 trigger_type은 거부됨
- Given: `trigger_type`이 누락되었거나 SRS 허용 목록에 없는 값임
- When: `/api/v1/schedule/proactive` command가 실행됨
- Then: `INVALID_REQUEST` 오류가 반환되고 proactive 발화가 생성되지 않는다.

Scenario 2: 식사 또는 수면 트리거는 일반 안부/확인성 발화를 반환함
- Given: `CT-004, CMD-004, CMD-005, CMD-006`가 완료되고 `trigger_type=meal` 또는 `sleep`인 유효한 요청이 있음
- When: proactive command가 실행됨
- Then: 응답에는 일반 안부 또는 확인성 `reply_text`, `request_id`, `memory_used`, `safety_policy_applied`가 포함된다.

Scenario 3: 복약 지시 또는 처방 문구를 생성하지 않음
- Given: `trigger_type=meal`, `sleep`, `custom` 중 하나가 주어짐
- When: proactive 발화가 생성됨
- Then: 응답에는 약 복용 지시, 처방, 진단 문구가 포함되지 않아야 한다.

Scenario 4: 메모리 사용은 서버 동의 판단을 우선함
- Given: 요청에 `use_memory=true`가 포함되었지만 memory consent가 없음
- When: proactive command가 실행됨
- Then: memory summary를 사용하지 않고 `memory_used=false`를 반환한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- API 계약: `/api/v1/schedule/proactive`는 Phase A `Should` 범위이며 API-003 request/response field를 만족해야 한다.
- 안전성: 선제 발화는 의료 지시가 아닌 일반 안부/확인성 발화로 제한한다.
- 개인정보: 메모리 context 사용은 `CMD-004`의 consent policy 결과를 따라야 한다.
- 비용: LLM 호출 시 `CMD-005`의 기본 저비용 모델, token cap, retry cap을 준수한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-004, CMD-004, CMD-005, CMD-006
- Blocks: FE-006, TEST-009
