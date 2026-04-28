---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-010: billable API 호출 usage_event 기록 공통 middleware/service 구현"
labels: 'feature, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-010] billable API 호출 usage_event 기록 공통 middleware/service 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-026, REQ-NF-029~031` 요구를 구현 가능한 작업 단위로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `billable API 호출 usage_event 기록 공통 middleware/service 구현` command 입력 DTO와 validation rule을 구현한다.
- [ ] domain service에서 상태 변경 로직을 구현하고 repository를 통해서만 DB를 변경한다.
- [ ] 성공 시 필요한 event, usage, audit, analysis 또는 alert record를 저장한다.
- [ ] 실패 시 표준 오류 응답과 rollback/side effect 방지 기준을 적용한다.
- [ ] Given/When/Then 기반 단위 테스트와 Route Handler 또는 Server Action 통합 테스트를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 정상 경로 수행
- Given: 선행 태스크 `DB-006, PLAT-001`가 완료되어 있음
- When: `billable API 호출 usage_event 기록 공통 middleware/service 구현` 작업을 수행함
- Then: SRS 관련 섹션 `REQ-FUNC-026, REQ-NF-029~031`의 기대 동작 또는 산출물이 충족되어야 한다.

Scenario 2: 실패 또는 제한 조건 처리
- Given: 필수 입력, 권한, tenant, consent, quota, schema 중 하나가 유효하지 않음
- When: `billable API 호출 usage_event 기록 공통 middleware/service 구현` 작업이 실행됨
- Then: 표준 오류, 접근 제한, 마스킹, audit 또는 backlog 처리 기준에 맞게 실패해야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 보안: tenant 격리, RBAC, audit log, PII masking 기준을 위반하지 않는다.
- 성능: `/api/v1/chat/reply` 관련 경로는 비LLM p95 800ms, LLM 포함 p95 5초 PoC baseline 계측을 방해하지 않는다.
- 비용: LLM 호출은 저비용 모델 기본값, token budget, retry cap, quota guardrail을 따른다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test) 또는 문서 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec 또는 운영 문서가 최신화되었는가?
- [ ] `TASKS/실버케어_SRS_v0.5_단일구조_TASKS.md`의 의존성 기준과 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-006, PLAT-001
- Blocks: 후속 태스크는 TASKS 원본의 Dependencies 기준으로 연결
