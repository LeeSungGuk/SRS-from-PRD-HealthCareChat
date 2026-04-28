---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] MOCK-001: Phase A 개발용 tenant, partner user, role, API key seed 데이터 작성"
labels: 'feature, mock, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [MOCK-001] Phase A 개발용 tenant, partner user, role, API key seed 데이터 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.7 P0, REQ-FUNC-001, REQ-FUNC-048~049` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails`](../../SRS/실버케어_SRS_v0.5_단일구조.md#67-mvp-implementation-cut-and-cost-efficiency-guardrails)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Phase A 개발용 synthetic tenant fixture를 작성한다: active tenant, suspended tenant, 다른 tenant.
- [ ] partner user fixture를 role별로 작성한다: developer, tenant admin, B2B-PM, operator, unauthorized user.
- [ ] API Key fixture는 원문 key가 아니라 `api_key_id`, `tenant_id`, `key_hash` 또는 redacted placeholder, status, created_at, revoked_at, last_used_at만 포함한다.
- [ ] Console Home과 API Key Manager가 사용할 module list와 role permission fixture를 포함한다.
- [ ] fixture path와 import 방식을 문서화해 FE/SEC/TEST 태스크가 같은 seed를 재사용하게 한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: tenant/user/API Key seed가 Partner Console 개발에 사용 가능함
- Given: UI/API/Test 작업자가 백엔드 완성을 기다리지 않고 개발해야 함
- When: `MOCK-001` fixture를 로드함
- Then: tenant context, role별 module list, API Key 상태 목록, 권한 실패 시나리오를 재현할 수 있어야 한다.

Scenario 2: API Key fixture는 원문 key를 포함하지 않음
- Given: API Key seed 데이터를 검토함
- When: fixture 파일을 검색함
- Then: 실제 API Key 원문과 production secret이 포함되지 않아야 한다.

Scenario 3: fixture가 계약과 일치함
- Given: CT/DB 계약이 변경됨
- When: mock seed 또는 fixture를 schema validation에 통과시킴
- Then: API DTO, enum, error code, tenant/consent 필드가 최신 계약과 일치해야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 개인정보/보안: 실제 API Key, production tenant, 직접 식별정보를 fixture에 포함하지 않는다.
- 재사용성: fixture는 frontend, security, test 태스크가 동일 source를 참조할 수 있어야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-002, CT-006
- Blocks: FE-001
