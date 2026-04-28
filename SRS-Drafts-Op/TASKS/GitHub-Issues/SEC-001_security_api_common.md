---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] SEC-001: API Key hash 검증, key status 확인, `last_used_at` 갱신 command 구현"
labels: 'feature, security, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [SEC-001] API Key hash 검증, key status 확인, `last_used_at` 갱신 command 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-004, REQ-FUNC-049, 6.2 api_key` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] API Key 인증 함수의 input/output을 정의하고 raw key, hashed key lookup, tenant context 반환 구조를 분리한다.
- [ ] 저장소에는 API Key 원문을 저장하지 않고 hash와 key prefix/suffix hint만 저장·조회하도록 검증한다.
- [ ] key status가 `active`가 아니거나 만료/폐기된 경우 `UNAUTHORIZED` 표준 오류를 반환한다.
- [ ] 인증 성공 시 `last_used_at`과 usage/auth audit metadata를 갱신한다.
- [ ] 인증 실패 로그에는 원문 API Key 또는 hash 전체값이 남지 않도록 sanitizer를 적용한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: active API Key 인증이 성공함
- Given: hash가 저장된 active API Key 원문이 요청 header로 제공됨
- When: API Key 인증 로직을 실행함
- Then: tenant context가 반환되고 `last_used_at`이 갱신되어야 한다.

Scenario 2: invalid 또는 revoked key는 거부됨
- Given: 존재하지 않거나 revoked 상태의 API Key가 제공됨
- When: API Key 인증 로직을 실행함
- Then: HTTP 401, `error_code=UNAUTHORIZED`, `retryable=false`, `request_id`가 포함된 오류가 반환되어야 한다.

Scenario 3: 원문 key가 로그와 DB에 저장되지 않음
- Given: API Key 인증 성공 또는 실패 이벤트가 발생함
- When: 저장 데이터와 로그를 검사함
- Then: 원문 API Key는 저장소와 로그에 존재하지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 보안: raw API Key는 최초 발급 시 1회 표시 후 재표시하지 않으며 인증은 hash 비교로 수행한다.
- 감사: 인증 성공/실패는 원문 key 없이 request_id와 tenant 후보 정보 중심으로 추적한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-002, DB-008, PLAT-001
- Blocks: SEC-002, SEC-003, CMD-001, TEST-002
