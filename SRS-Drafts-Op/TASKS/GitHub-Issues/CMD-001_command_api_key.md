---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-001: API Key 발급 Server Action 구현: key hash 저장, 원문 key 1회 표시, audit log 기록"
labels: 'feature, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-001] API Key 발급 Server Action 구현: key hash 저장, 원문 key 1회 표시, audit log 기록
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-001, REQ-FUNC-049, REQ-NF-038` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] API Key 발급 Server Action의 입력 DTO를 정의하고 tenant context와 actor role을 검증한다.
- [ ] 충분한 엔트로피를 가진 원문 API Key를 생성하고 `key_hash`만 `api_key` 테이블에 저장한다.
- [ ] 발급 성공 응답에는 원문 API Key를 최초 1회만 포함하고, 이후 조회/목록 응답에서는 원문 key와 `key_hash`를 모두 제외한다.
- [ ] `status=active`, `tenant_id`, `created_at`을 저장하고 발급 이벤트를 `audit_log`에 redacted metadata로 기록한다.
- [ ] 권한 없음, tenant mismatch, validation 실패 시 표준 오류를 반환하고 API Key 레코드가 생성되지 않도록 테스트한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 권한 있는 파트너 개발자가 API Key를 발급함
- Given: `SEC-001, PLAT-003`가 완료되고 tenant developer 또는 tenant admin 권한의 사용자가 있음
- When: 사용자가 Developer Portal에서 API Key 발급 Server Action을 실행함
- Then: `api_key` 레코드가 `status=active`로 생성되고 원문 key가 아닌 `key_hash`만 저장되며 발급 audit log가 생성된다.

Scenario 2: 원문 API Key는 최초 발급 응답에서만 표시됨
- Given: API Key 발급이 성공함
- When: 동일 사용자가 API Key 목록 또는 상세 정보를 다시 조회함
- Then: 응답에는 `api_key_id`, `status`, `created_at` 등 관리 정보만 포함되고 원문 API Key와 `key_hash`는 포함되지 않는다.

Scenario 3: 권한 또는 tenant 검증 실패 시 Key가 생성되지 않음
- Given: 권한 없는 사용자이거나 요청 tenant가 사용자 tenant와 다름
- When: API Key 발급 Server Action을 실행함
- Then: 표준 오류가 반환되고 신규 `api_key` 레코드와 원문 key 로그가 남지 않는다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 실행 경계: API Key 생성과 hash 저장은 server-only module 또는 Server Action에서만 수행한다.
- 보안: 원문 API Key는 DB, audit log, application log, client storage에 저장하지 않는다.
- 감사: API Key 발급은 `REQ-NF-038`에 따라 100% audit log를 남긴다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리하고 tenant 조건을 필수로 포함한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: SEC-001, PLAT-003
- Blocks: CMD-002, FE-002, TEST-003
