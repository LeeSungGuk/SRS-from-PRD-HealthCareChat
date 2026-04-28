---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] FE-010: User & Consent Admin View 구현: elder/device, consent state, memory enabled, deletion job status"
labels: 'feature, frontend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [FE-010] User & Consent Admin View 구현: elder/device, consent state, memory enabled, deletion job status
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-059, REQ-NF-038` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] User & Consent Admin View에 `elder_id`, `device_id`, consent_state 6개 dimension, memory_enabled, deletion_job_status를 표시한다.
- [ ] `QRY-008` 조회와 `CMD-011` deletion/withdrawal action을 연결하고 action confirmation state를 구현한다.
- [ ] 동의 상태 조회, 삭제 요청, 삭제 job 조회는 audit log 대상 action으로 처리되도록 action result를 연결한다.
- [ ] least privilege role만 view/delete action을 사용할 수 있고 다른 tenant elder/device 접근을 차단한다.
- [ ] 실명, 주민등록번호, 상세 주소, raw transcript는 표시하지 않는 privacy assertion을 추가한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 권한 있는 사용자는 동의와 삭제 job 상태를 조회함
- Given: 선행 태스크 `FE-001, QRY-008, CMD-011, UX-003`가 완료되고 인증된 role/tenant context가 있음
- When: 사용자가 elder 또는 device를 조회함
- Then: 가명 `elder_id`, `device_id`, 동의 상태, memory enabled, deletion job status가 표시된다.

Scenario 2: 삭제 요청은 confirmation 후 실행됨
- Given: 권한 있는 사용자가 삭제 요청 action을 선택함
- When: 사용자가 confirmation을 완료함
- Then: `CMD-011`이 실행되고 deletion job status와 request_id가 화면에 반영된다.

Scenario 3: 직접 식별정보는 표시하지 않음
- Given: user/consent admin data를 렌더링함
- When: 화면과 network payload를 검토함
- Then: 실명, 주민등록번호, 상세 주소, raw transcript가 포함되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 개인정보: `REQ-FUNC-059`에 따라 직접 식별정보 없이 가명 ID와 동의/삭제 상태만 표시한다.
- 감사: `REQ-NF-038`에 따라 동의 상태 조회와 삭제 job 조회/요청은 audit log 대상이다.
- 접근 제어: least privilege role과 tenant scope를 모두 만족해야 조회/삭제 action을 사용할 수 있다.
- UI 범위: Partner Console만 포함하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: FE-001, QRY-008, CMD-011, UX-003
- Blocks: TEST-015
