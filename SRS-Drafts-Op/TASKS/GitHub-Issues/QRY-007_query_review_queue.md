---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] QRY-007: high/critical, SAFETY_FILTERED, LLM timeout, 신고 케이스 리뷰 큐 조회 구현"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [QRY-007] high/critical, SAFETY_FILTERED, LLM timeout, 신고 케이스 리뷰 큐 조회 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-035, REQ-FUNC-058, REQ-NF-035` 요구를 구현 가능한 작업 단위로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `high/critical, SAFETY_FILTERED, LLM timeout, 신고 케이스 리뷰 큐 조회 구현` 조회 DTO, filter, sort, pagination 또는 export 옵션을 정의한다.
- [ ] tenant/role/consent guard를 먼저 적용한 뒤 repository query를 수행한다.
- [ ] 응답 payload에서 raw transcript와 직접 식별정보를 제외하거나 마스킹한다.
- [ ] empty, forbidden, cross-tenant, invalid filter 시나리오를 테스트한다.
- [ ] PoC Report, Console UI, API Docs 또는 Review Queue 소비자가 사용할 response shape를 고정한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 정상 경로 수행
- Given: 선행 태스크 `DB-005, DB-007, CMD-012`가 완료되어 있음
- When: `high/critical, SAFETY_FILTERED, LLM timeout, 신고 케이스 리뷰 큐 조회 구현` 작업을 수행함
- Then: SRS 관련 섹션 `REQ-FUNC-035, REQ-FUNC-058, REQ-NF-035`의 기대 동작 또는 산출물이 충족되어야 한다.

Scenario 2: 실패 또는 제한 조건 처리
- Given: 필수 입력, 권한, tenant, consent, quota, schema 중 하나가 유효하지 않음
- When: `high/critical, SAFETY_FILTERED, LLM timeout, 신고 케이스 리뷰 큐 조회 구현` 작업이 실행됨
- Then: 표준 오류, 접근 제한, 마스킹, audit 또는 backlog 처리 기준에 맞게 실패해야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 보안: tenant 격리, RBAC, audit log, PII masking 기준을 위반하지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test) 또는 문서 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec 또는 운영 문서가 최신화되었는가?
- [ ] `TASKS/실버케어_SRS_v0.5_단일구조_TASKS.md`의 의존성 기준과 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-005, DB-007, CMD-012
- Blocks: 후속 태스크는 TASKS 원본의 Dependencies 기준으로 연결
