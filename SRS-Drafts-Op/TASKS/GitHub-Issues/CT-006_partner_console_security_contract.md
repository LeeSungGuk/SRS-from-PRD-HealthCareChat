---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CT-006: Partner Console 세션·역할·테넌트 보안 계약 정의"
labels: 'feature, security, frontend, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CT-006] Partner Console session, role, tenant context, protected route 계약 정의
- 목적: SilverCare Partner Console의 모든 화면과 Server Action이 인증된 세션, 역할 기반 권한, tenant context를 일관되게 사용하도록 보안 계약을 고정한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#65-web-console-navigation-flow`](../../SRS/실버케어_SRS_v0.5_단일구조.md#65-web-console-navigation-flow)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Partner Console에서 사용할 session payload 필드를 정의한다: `actor_id`, `actor_role`, `tenant_id`, `environment`, `allowed_modules`.
- [ ] 역할별 접근 가능한 모듈을 정의한다: B2B-DEV, B2B-PM, 플랫폼 운영자.
- [ ] Console Home, API Key Manager, API Docs, Playground, PoC Report, Ops Monitoring, User/Consent Admin protected route 규칙을 정의한다.
- [ ] Server Action 호출 시 session과 tenant context를 검증하는 공통 contract를 정의한다.
- [ ] sandbox mode와 production tenant 접근 권한의 차이를 명시한다.
- [ ] 권한 실패 시 `FORBIDDEN` 또는 `TENANT_ACCESS_DENIED`로 매핑하는 기준을 정의한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 인증되지 않은 사용자의 콘솔 접근 차단
- Given: authenticated session이 없는 사용자가 Partner Console route에 접근함
- When: protected route guard가 실행됨
- Then: 화면 데이터와 tenant 정보를 반환하지 않고 로그인 또는 인증 실패 상태로 처리한다.

Scenario 2: 역할별 모듈 접근 제한
- Given: B2B-DEV 역할의 사용자가 PoC Report Console에 접근함
- When: role authorization을 검사함
- Then: B2B-DEV에게 허용되지 않은 모듈이면 데이터를 반환하지 않고 접근 거부 상태를 반환한다.

Scenario 3: tenant context 불일치 차단
- Given: 인증된 사용자가 다른 tenant의 리소스를 요청함
- When: Server Action 또는 Console query가 tenant ownership을 검사함
- Then: `TENANT_ACCESS_DENIED` 기준으로 처리되고 다른 tenant payload는 반환되지 않는다.

## :gear: Technical & Non-Functional Constraints
- 보안: Partner Console의 모든 화면은 authenticated session과 role-based authorization을 요구한다.
- 개인정보: 기본 UI는 원문과 직접 식별정보를 표시하지 않는다.
- 감사성: API Key 발급/폐기, Playground 실행, 로그 조회, 동의 상태 조회, 삭제 job 조회는 audit log 대상이다.
- 구조: Client Component는 DB, LLM provider, API secret, API Key 검증 로직을 직접 참조하지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] session payload와 role/module matrix가 정의되었는가?
- [ ] protected route와 Server Action authorization contract가 정의되었는가?
- [ ] sandbox/production tenant 접근 정책이 명시되었는가?
- [ ] DB-002와 FE-001이 참조할 수 있는 보안 계약인가?

## :construction: Dependencies & Blockers
- Depends on: CT-001
- Blocks: DB-002, MOCK-001, QRY-001, QRY-006, QRY-008, UX-001, UX-003, FE-001

