---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-002: API Key 폐기 Server Action 구현: status 변경, revoked_at 저장, audit log 기록"
labels: 'feature, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-002] API Key 폐기 Server Action 구현: status 변경, revoked_at 저장, audit log 기록
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-049, REQ-NF-038` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] API Key 폐기 Server Action의 입력 DTO(`api_key_id`, tenant context)를 정의하고 actor 권한을 검증한다.
- [ ] 대상 API Key를 tenant scope로 조회하고 `active` 상태인 경우 `status=revoked`, `revoked_at=now()`로 갱신한다.
- [ ] 폐기된 Key의 `key_hash`는 인증 거부 및 감사 추적을 위해 보존하되 원문 key는 어떤 응답에도 포함하지 않는다.
- [ ] 폐기 성공 및 실패 이벤트를 `audit_log`에 redacted metadata로 기록한다.
- [ ] 이미 폐기된 Key는 `status=revoked`로 유지하고 `revoked_at`을 덮어쓰지 않도록 테스트로 고정한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 권한 있는 사용자가 active API Key를 폐기함
- Given: `CMD-001`로 생성된 `active` API Key와 권한 있는 tenant developer 또는 tenant admin 사용자가 있음
- When: 사용자가 API Key 폐기 Server Action을 실행함
- Then: 해당 Key의 `status`는 `revoked`로 변경되고 `revoked_at`이 저장되며 폐기 audit log가 생성된다.

Scenario 2: 폐기된 API Key는 이후 인증에 사용할 수 없음
- Given: API Key가 `status=revoked`로 변경됨
- When: 해당 API Key로 보호된 API 인증을 시도함
- Then: 인증 결과는 `UNAUTHORIZED`로 판정되고 응답 또는 로그에 원문 key가 포함되지 않는다.

Scenario 3: 다른 tenant의 API Key는 폐기할 수 없음
- Given: 사용자 tenant와 다른 tenant에 속한 `api_key_id`가 주어짐
- When: API Key 폐기 Server Action을 실행함
- Then: 표준 권한 오류가 반환되고 대상 Key의 `status`, `revoked_at`은 변경되지 않는다.

Scenario 4: 이미 폐기된 API Key는 폐기 시각을 덮어쓰지 않음
- Given: `status=revoked`이고 `revoked_at`이 이미 저장된 API Key가 있음
- When: 동일 API Key 폐기 Server Action을 다시 실행함
- Then: 응답은 revoked 상태를 반환하고 기존 `revoked_at` 값은 변경되지 않는다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 실행 경계: API Key 폐기는 server-only module 또는 Server Action에서만 수행한다.
- 보안: 폐기 처리 중 원문 API Key를 요구하거나 재표시하지 않는다.
- 감사: API Key 폐기는 `REQ-NF-038`에 따라 100% audit log를 남긴다.
- 데이터 접근: Prisma update 조건에는 `api_key_id`와 `tenant_id`를 함께 사용해 cross-tenant 변경을 방지한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CMD-001
- Blocks: FE-002, TEST-003
