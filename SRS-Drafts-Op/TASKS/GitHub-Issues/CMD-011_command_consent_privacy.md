---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-011: 동의 철회/삭제 요청 cascade deletion job 생성 command 구현"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-011] 동의 철회/삭제 요청 cascade deletion job 생성 command 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-038, REQ-FUNC-059, REQ-NF-024~027` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] 동의 철회/삭제 요청 command의 입력 DTO를 정의하고 actor 권한, tenant scope, `elder_id` 또는 `device_id` 접근 권한을 검증한다.
- [ ] 동의 철회 시 `consent_state`의 대상 consent dimension을 갱신하고 `memory_storage=false`인 경우 즉시 메모리 삭제 job을 생성한다.
- [ ] 삭제 요청 시 raw messages, memory summary, analysis result, report aggregate/export, evaluation candidates, cache, search index, backup replay 대상을 포함한 cascade deletion job을 생성한다.
- [ ] deletion job의 대상별 상태를 추적할 수 있도록 `request_id`, data category, target id 또는 selector, status, scheduled_at을 기록한다.
- [ ] 처리 요청, 상태 변경, 예외는 `audit_log`에 남기되 원문 발화, 직접 식별정보, 원문 API Key를 기록하지 않는다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 메모리 동의 철회 시 메모리 삭제 job이 즉시 생성됨
- Given: `DB-008, CT-008, PLAT-003`가 완료되고 `memory_storage=true`인 elder가 있음
- When: 권한 있는 사용자가 memory consent 철회를 요청함
- Then: `consent_state.memory_storage=false`로 갱신되고 `memory_summary` 및 personalization cache 삭제 job이 동일 `request_id`로 생성된다.

Scenario 2: 삭제 요청은 모든 cascade target을 추적함
- Given: elder의 대화, 메모리, 분석 결과, 리포트, 평가셋 후보가 존재함
- When: 삭제 요청 command가 실행됨
- Then: raw messages, memory, analysis, report, evaluation candidates, cache, search index, backup replay 대상별 deletion job 상태가 생성된다.

Scenario 3: 권한 없는 actor는 삭제 job을 생성할 수 없음
- Given: actor가 대상 tenant 또는 elder/device에 대한 권한을 갖지 않음
- When: 동의 철회 또는 삭제 요청 command를 실행함
- Then: 표준 권한 오류가 반환되고 `consent_state`와 deletion job 상태는 변경되지 않는다.

Scenario 4: 감사 로그에는 원문과 직접 식별정보가 남지 않음
- Given: 동의 철회 또는 삭제 요청이 성공하거나 실패함
- When: audit log를 조회함
- Then: action, actor, tenant, resource, request_id, status만 확인 가능하고 raw transcript와 직접 식별정보는 포함되지 않는다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 개인정보: `REQ-NF-024~027`의 데이터 유형별 보존기간과 삭제 트리거를 command 정책에 반영한다.
- 데이터 접근: 모든 deletion target 조회는 `tenant_id`와 대상 `elder_id` 또는 `device_id` scope를 포함해야 한다.
- 감사: 동의 상태 조회/삭제 job 조회 및 변경은 `REQ-NF-038` 기준의 audit log 대상이다.
- 안정성: cascade deletion은 비동기 job으로 분리하되 요청 시점에 대상별 추적 상태가 반드시 생성되어야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-008, CT-008, PLAT-003
- Blocks: FE-010, TEST-011, NFR-006
