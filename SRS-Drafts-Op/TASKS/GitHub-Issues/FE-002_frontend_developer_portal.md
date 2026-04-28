---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] FE-002: API Key Manager UI 구현: 발급, 폐기, 상태, 마지막 사용 시각, 원문 key 1회 copy"
labels: 'feature, frontend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [FE-002] API Key Manager UI 구현: 발급, 폐기, 상태, 마지막 사용 시각, 원문 key 1회 copy
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-049, 6.4` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] API Key Manager 화면에 `api_key_id`, status, created_at, revoked_at, last_used_at 목록을 표시한다.
- [ ] `CMD-001` 발급 Server Action과 연결해 발급 성공 시 원문 API Key를 일회성 reveal/copy 영역에만 표시한다.
- [ ] `CMD-002` 폐기 Server Action과 연결해 active key 폐기, revoked 상태 표시, revoked_at 갱신 결과를 반영한다.
- [ ] 발급 이후 reload, 목록 조회, 상세 조회에서는 원문 API Key와 `key_hash`를 표시하지 않는다.
- [ ] tenant admin 또는 developer role만 create/revoke/copy action을 사용할 수 있도록 UI action guard와 E2E 테스트를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 권한 있는 사용자는 API Key를 발급하고 1회 copy함
- Given: 선행 태스크 `FE-001, CMD-001, CMD-002, QRY-001`가 완료되고 인증된 role/tenant context가 있음
- When: 사용자가 API Key 발급을 실행함
- Then: 원문 API Key가 발급 직후 1회 copy 가능한 상태로 표시되고 발급 이후 목록에는 `api_key_id`, status, created_at만 표시된다.

Scenario 2: 발급 후 원문 API Key는 재표시되지 않음
- Given: API Key 발급이 완료되고 사용자가 화면을 새로고침함
- When: API Key Manager 목록을 조회함
- Then: 원문 API Key와 `key_hash`는 표시되지 않고 status, created_at, revoked_at, last_used_at만 표시된다.

Scenario 3: API Key 폐기는 상태와 감사 대상 action을 갱신함
- Given: active API Key가 목록에 표시됨
- When: 사용자가 revoke action을 실행함
- Then: UI는 `status=revoked`, `revoked_at`을 표시하고 revoke action은 더 이상 활성화되지 않는다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안: API Key 원문은 발급 성공 응답 이후 client state, storage, route cache에 남기지 않는다.
- 감사: API Key 발급/폐기 action은 `REQ-NF-038` audit log 생성 대상임을 유지한다.
- 접근 제어: tenant admin 또는 developer role만 API Key create/revoke/copy action을 볼 수 있다.
- UI 범위: Partner Console만 포함하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: FE-001, CMD-001, CMD-002, QRY-001
- Blocks: TEST-003, TEST-014, NFR-011
