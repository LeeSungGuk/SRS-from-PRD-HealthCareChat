---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] PLAT-001: Route Handler와 Server Action에서 공유할 request context, tenant context, request_id 생성 유틸 구현"
labels: 'feature, platform, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [PLAT-001] Route Handler와 Server Action에서 공유할 request context, tenant context, request_id 생성 유틸 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `1.5 C-TEC-002, REQ-FUNC-014, REQ-FUNC-043` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#15-assumptions--constraints`](../../SRS/실버케어_SRS_v0.5_단일구조.md#15-assumptions--constraints)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Route Handler와 Server Action에서 공통으로 호출할 `createRequestContext` 또는 동등 유틸의 typed interface를 정의한다.
- [ ] context에 `request_id`, `tenant_id`, `actor_id`, `actor_role`, `auth_type`, `schema_version`, `started_at`을 포함한다.
- [ ] API Key 기반 요청과 authenticated session 기반 Web Console 요청을 같은 tenant context 형식으로 정규화한다.
- [ ] 모든 성공/오류 응답, audit log, usage event, report event가 동일 `request_id`를 참조하도록 propagation 규칙을 구현한다.
- [ ] context 생성 실패 시 `UNAUTHORIZED`, `TENANT_ACCESS_DENIED`, `INVALID_REQUEST` 중 표준 오류로 매핑하는 fixture를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: Route Handler와 Server Action이 동일 context를 사용함
- Given: 유효한 API Key 요청과 유효한 Web Console session 요청이 각각 주어짐
- When: 공통 request context 유틸을 호출함
- Then: 두 경로 모두 `request_id`, `tenant_id`, `actor_role`, `schema_version`을 포함한 동일 구조의 context를 반환해야 한다.

Scenario 2: 단일 request_id로 응답과 로그가 연결됨
- Given: `/api/v1/chat/reply` 요청이 처리됨
- When: response, audit log, usage event를 조회함
- Then: 모든 항목이 동일한 `request_id`를 포함해야 한다.

Scenario 3: tenant context 생성 실패가 표준 오류로 매핑됨
- Given: 폐기된 API Key 또는 다른 tenant device 식별자가 포함된 요청이 주어짐
- When: request context 유틸을 호출함
- Then: 표준 오류 코드와 마스킹된 오류 메시지가 반환되고 원문 key는 로그에 남지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 경계: request context 유틸은 domain service에서 재사용 가능해야 하며 Client Component에서 secret 또는 Prisma Client 접근을 유도하지 않아야 한다.
- 추적성: `request_id`는 API response body와 server-side log/audit/usage event에 동일하게 전파되어야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-002, DB-002
- Blocks: PLAT-003, SEC-001, CMD-010, NFR-001
