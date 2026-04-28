---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CT-001: Next.js 단일 풀스택 모듈 경계 정의"
labels: 'feature, architecture, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CT-001] Next.js App Router 단일 풀스택 모듈 경계 정의
- 목적: Phase A MVP를 단일 Next.js 애플리케이션으로 구현하되, 향후 프론트엔드/백엔드 분리가 가능하도록 UI, API Route, Server Action, domain service, repository, LLM provider adapter, audit/logging 경계를 명확히 고정한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`/SRS/실버케어_SRS_v0.5_단일구조.md#15-assumptions--constraints`](../../SRS/실버케어_SRS_v0.5_단일구조.md#15-assumptions--constraints)
- 시퀀스 다이어그램: [`/SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`/SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`/SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Next.js App Router 기준 권장 디렉터리 구조를 정의한다.
- [ ] `app` UI route, `app/api` Route Handler, Server Action, domain service, repository, provider adapter, audit/logging 모듈의 책임을 문서화한다.
- [ ] UI 계층이 repository 또는 LLM provider adapter를 직접 import하지 못하도록 의존성 규칙을 정의한다.
- [ ] API Route Handler와 Server Action이 domain service를 통해서만 business rule을 호출하도록 호출 방향을 정의한다.
- [ ] 향후 분리 가능성을 위해 domain service와 repository의 공개 함수 또는 typed interface naming 규칙을 정의한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 단일 Next.js 애플리케이션 구조가 정의됨
- Given: SRS의 C-TEC-001~010 제약조건이 주어짐
- When: 개발자가 프로젝트 구조 문서를 확인함
- Then: Web UI, Route Handler, Server Action, domain service, repository, LLM provider adapter, audit/logging 모듈의 책임과 호출 방향을 확인할 수 있다.

Scenario 2: UI 계층의 직접 데이터 접근이 금지됨
- Given: Web UI 컴포넌트 또는 route가 주어짐
- When: 모듈 의존성 규칙을 검토함
- Then: UI가 Prisma repository 또는 LLM provider adapter를 직접 호출하지 않고 Server Action 또는 typed interface를 통해서만 접근해야 한다는 규칙이 명시되어 있다.

Scenario 3: 향후 분리 가능성이 보장됨
- Given: Phase B 이후 프론트엔드/백엔드 분리 가능성 제약이 주어짐
- When: API/domain/repository 경계 문서를 검토함
- Then: domain service와 repository가 typed interface를 통해 호출되어 별도 backend service로 이동 가능한 구조임을 확인할 수 있다.

## :gear: Technical & Non-Functional Constraints
- 기술 스택: Next.js App Router, Server Actions, Route Handlers, Prisma, Tailwind CSS, shadcn/ui 기준을 따른다.
- 아키텍처: Phase A MVP에서는 별도 백엔드 서버를 만들지 않는다.
- 유지보수성: UI, API, domain, repository, provider adapter, logging 책임은 하나의 모듈에 중복 구현하지 않는다.
- 확장성: 향후 분리 가능성을 위해 공개 interface와 DTO는 UI 내부 타입에 종속되지 않아야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 모듈 경계와 호출 방향이 문서화되었는가?
- [ ] CT-002~CT-005가 참조할 수 있는 공통 architecture decision이 명확한가?
- [ ] ESLint 또는 dependency rule로 검증 가능한 규칙 후보가 정리되었는가?
- [ ] SRS의 C-TEC-001~010과 충돌하는 구조가 없는가?

## :construction: Dependencies & Blockers
- Depends on: None
- Blocks: CT-002, CT-006, DB-001, PLAT-002
