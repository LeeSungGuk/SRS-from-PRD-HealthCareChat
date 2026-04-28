---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] PLAT-003: 감사 로그 생성 공통 서비스 구현"
labels: 'feature, platform, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [PLAT-003] 감사 로그 생성 공통 서비스 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-001, REQ-FUNC-014, REQ-FUNC-039, REQ-NF-022, REQ-NF-038` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#15-assumptions--constraints`](../../SRS/실버케어_SRS_v0.5_단일구조.md#15-assumptions--constraints)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `writeAuditLog` 또는 동등 공통 서비스의 typed interface를 정의하고 `request_id`, `tenant_id`, `actor_id`, `actor_role`, `action`, `resource_type`, `resource_id`, `result`, `created_at`을 필수화한다.
- [ ] API Key 발급/폐기, 민감 데이터 조회, Playground 실행, 로그 조회, 동의 상태 조회, 삭제 job 조회/실행에 audit log를 연결한다.
- [ ] audit metadata에는 원문 API Key, 원문 대화, 직접 PII를 저장하지 않도록 sanitizer를 적용한다.
- [ ] 감사 로그 저장 실패 시 비즈니스 트랜잭션 차단 여부를 action sensitivity 기준으로 정의한다.
- [ ] REQ-NF-022와 REQ-NF-038의 "100% sensitive accesses audited" 검증 쿼리를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 민감 UI action이 감사 로그를 생성함
- Given: 권한 있는 사용자가 API Key 폐기 또는 대화 로그 조회를 수행함
- When: 해당 Server Action이 완료됨
- Then: audit log에 request_id, tenant_id, actor, action, resource, result가 기록되어야 한다.

Scenario 2: 감사 로그에 원문 key와 PII가 저장되지 않음
- Given: API Key 발급 직후 원문 key가 1회 표시됨
- When: audit log를 조회함
- Then: audit metadata에는 원문 key, raw transcript, 직접 PII가 포함되지 않아야 한다.

Scenario 3: 감사 coverage audit이 통과함
- Given: 민감 action 목록이 정의되어 있음
- When: audit coverage query를 실행함
- Then: 모든 민감 action이 audit log 생성 지점과 연결되어 있어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 보안/개인정보: 감사 로그는 추적성을 제공하되 비밀값과 원문 개인정보를 보관하는 우회 저장소가 되어서는 안 된다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-006, PLAT-001
- Blocks: CMD-001, CMD-011, QRY-005
