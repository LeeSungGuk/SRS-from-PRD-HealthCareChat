---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-003: API Key 발급/폐기/원문 key 1회 표시/audit log 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-003] API Key 발급/폐기/원문 key 1회 표시/audit log 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-001, REQ-FUNC-049, REQ-NF-038` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] API Key 발급 command가 원문 key를 최초 1회만 반환하는 통합 테스트를 작성한다.
- [ ] API Key 목록 query가 원문 key 없이 status, created_at, revoked_at, last_used_at만 반환하는 테스트를 작성한다.
- [ ] API Key 폐기 command 이후 동일 key 인증이 `UNAUTHORIZED`로 실패하는 테스트를 작성한다.
- [ ] 발급/폐기/조회/Playground 실행이 audit log를 생성하는지 검증한다.
- [ ] UI Key Manager E2E에서 원문 key 재표시 버튼 또는 route가 없는지 확인한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: API Key 원문은 최초 발급 시 1회만 표시됨
- Given: 권한 있는 개발자가 API Key를 발급함
- When: 발급 응답과 이후 목록 조회 응답을 확인함
- Then: 발급 응답에는 원문 key가 있고 목록 조회에는 원문 key가 없어야 한다.

Scenario 2: 폐기된 key는 인증에 실패함
- Given: active key를 폐기함
- When: 폐기된 key로 보호된 API를 호출함
- Then: HTTP 401 `UNAUTHORIZED`가 반환되어야 한다.

Scenario 3: 민감 UI action은 감사 로그를 생성함
- Given: API Key 발급 또는 폐기가 수행됨
- When: audit log를 조회함
- Then: actor, tenant, action, result, request_id가 기록되어야 하며 원문 key는 없어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안/개인정보: 테스트 로그와 snapshot에 원문 API Key가 남지 않도록 fixture key는 즉시 마스킹한다.
- UI 범위: Key Manager는 Partner Console 내부 화면만 검증한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CMD-001, CMD-002, FE-002
- Blocks: TEST-018
