---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-004: memory consent 판단과 memory_summary 조회 차단/사용 로직 구현"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-004] memory consent 판단과 memory_summary 조회 차단/사용 로직 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-010~011, REQ-FUNC-037` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] tenant/elder scope로 `elder_profile`과 `consent_state`를 조회하는 memory consent policy service를 구현한다.
- [ ] `use_memory=true` 요청이 들어와도 `memory_storage=false` 또는 `memory_enabled=false`이면 `memory_summary` 조회를 수행하지 않는다.
- [ ] 동의가 유효한 경우에만 만료되지 않은 `memory_summary`를 조회해 응답 생성 context로 전달하고 `memory_used=true` 판단값을 반환한다.
- [ ] 메모리 기록이 없거나 만료된 경우 존재하지 않는 과거 대화를 만들지 않도록 `memory_used=false`와 일반 안부 context를 반환한다.
- [ ] 동의 있음/없음/철회/만료 케이스를 repository mock 또는 query spy로 검증해 memory store 접근 여부를 테스트한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 메모리 동의가 있으면 요약 메모리를 사용할 수 있음
- Given: `memory_storage=true`, `memory_enabled=true`, 만료되지 않은 `memory_summary`가 존재함
- When: `use_memory=true`인 chat reply command가 실행됨
- Then: 서버는 tenant/elder scope의 memory summary만 context에 포함하고 `memory_used=true` 판단값을 반환한다.

Scenario 2: 파트너 요청보다 서버 동의 상태가 우선함
- Given: 요청에 `use_memory=true`가 포함되었지만 `memory_storage=false` 또는 `memory_enabled=false`임
- When: memory consent policy가 실행됨
- Then: `memory_summary` 저장소를 조회하지 않고 `memory_used=false`를 반환한다.

Scenario 3: 메모리 없음 또는 만료 시 과거 대화를 생성하지 않음
- Given: 동의는 있으나 유효한 memory summary가 없거나 `expires_at`이 지남
- When: 개인화 context를 생성함
- Then: 일반 안부 context와 `memory_used=false`를 반환하고 존재하지 않는 과거 대화 내용을 만들지 않는다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 개인정보: 메모리 사용은 명시적 동의와 `memory_summary.expires_at` 기준을 모두 만족할 때만 허용한다.
- 보존: `memory_summary`는 SRS 기준 최대 180일 이내 만료 정책을 따라야 한다.
- 데이터 접근: memory 조회는 항상 `tenant_id`, `elder_id`, `expires_at > now()` 조건을 포함한다.
- 안전성: 동의가 없을 때 memory store 접근이 발생하지 않음을 자동화 테스트로 검증한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CMD-003, DB-004
- Blocks: CMD-009, TEST-005
