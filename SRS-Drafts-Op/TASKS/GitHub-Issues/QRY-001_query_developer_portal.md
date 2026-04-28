---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] QRY-001: API Key 목록 조회: 상태, 생성/폐기 시각, 마지막 사용 시각, 원문 key 재표시 금지"
labels: 'feature, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [QRY-001] API Key 목록 조회: 상태, 생성/폐기 시각, 마지막 사용 시각, 원문 key 재표시 금지
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-049, 6.4 API Key Manager` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] API Key 목록 조회 input으로 `tenant_id`, optional `status`, pagination/sort 기준을 정의한다.
- [ ] 응답 DTO에 `api_key_id`, `key_name`, `status`, `scope`, `created_at`, `revoked_at`, `last_used_at`, masked key hint만 포함한다.
- [ ] 원문 API Key는 최초 발급 후 재표시되지 않도록 query layer에서 제외한다.
- [ ] tenant admin 또는 developer role만 조회 가능하도록 RBAC와 tenant guard를 적용한다.
- [ ] empty result, revoked key 포함 조회, cross-tenant 접근, 원문 key 노출 방지 테스트를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: API Key 목록이 관리 필드만 반환함
- Given: 권한 있는 개발자가 API Key Manager를 열람함
- When: API Key 목록 query가 실행됨
- Then: 상태, 생성/폐기 시각, 마지막 사용 시각, scope, masked key hint가 반환되고 원문 key는 반환되지 않아야 한다.

Scenario 2: 폐기된 key 상태가 조회됨
- Given: revoked 상태의 API Key가 존재함
- When: status filter 없이 목록을 조회함
- Then: 해당 key의 `status=revoked`, `revoked_at`, `last_used_at`이 표시되어야 한다.

Scenario 3: 다른 tenant key 조회가 차단됨
- Given: tenant A 사용자가 tenant B API Key 목록을 요청함
- When: query guard가 실행됨
- Then: `TENANT_ACCESS_DENIED` 또는 no data payload가 반환되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 보안/개인정보: 원문 API Key는 발급 직후 1회 표시 이후 어떤 query에서도 재표시하지 않는다.
- 감사: API Key 목록 조회와 폐기 상태 확인은 PLAT-003 감사 로그 정책과 연결한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-002, DB-008, CT-006
- Blocks: FE-002
