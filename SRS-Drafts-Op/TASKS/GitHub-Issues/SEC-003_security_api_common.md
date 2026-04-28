---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] SEC-003: tenant/partner 단위 rate limit과 `RATE_LIMIT_EXCEEDED` 응답 구현"
labels: 'feature, security, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [SEC-003] tenant/partner 단위 rate limit과 `RATE_LIMIT_EXCEEDED` 응답 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-006, REQ-NF-044` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] tenant/partner 단위 rate limit key, window, quota, remaining count, reset time 계산 규칙을 정의한다.
- [ ] 초과 시 HTTP 429, `RATE_LIMIT_EXCEEDED`, `retryable=true`, `retry_after_seconds`를 포함한 표준 오류를 반환한다.
- [ ] rate limit 평가 결과를 usage/quota event와 연결해 NFR-008 spend guardrail과 재사용 가능하게 한다.
- [ ] sandbox Playground quota와 production tenant quota가 별도 정책을 사용할 수 있도록 scope를 분리한다.
- [ ] 경계값 테스트를 작성한다: quota 직전 허용, quota 도달 후 차단, window reset 후 재허용.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: quota 이내 요청은 허용됨
- Given: tenant의 현재 호출량이 rate limit보다 작음
- When: API 요청이 들어옴
- Then: 요청은 처리되고 remaining quota가 감소해야 한다.

Scenario 2: rate limit 초과 요청은 429를 반환함
- Given: tenant가 설정된 rate limit을 초과함
- When: API를 추가 호출함
- Then: HTTP 429, `error_code=RATE_LIMIT_EXCEEDED`, `retryable=true`, `retry_after_seconds`가 반환되어야 한다.

Scenario 3: window reset 후 요청이 다시 허용됨
- Given: rate limit window가 만료됨
- When: 같은 tenant가 다시 API를 호출함
- Then: quota가 reset되어 요청이 허용되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 비용: rate limit은 LLM 호출 전 단계에서 적용해 불필요한 provider 비용을 방지한다.
- 운영: rate limit metric은 NFR-001/NFR-008에서 조회 가능해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-008, SEC-001
- Blocks: TEST-002
