---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] QRY-008: elder/device 동의 상태, memory enabled, deletion job status 조회 구현"
labels: 'feature, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [QRY-008] elder/device 동의 상태, memory enabled, deletion job status 조회 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-036, REQ-FUNC-059, REQ-NF-023` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] User & Consent Admin query input으로 `tenant_id`, optional `elder_id`, `device_id`, deletion job status filter를 정의한다.
- [ ] 응답 DTO에 가명 elder/device id, 6개 동의 항목, memory enabled, deletion job status, last updated time을 포함한다.
- [ ] 기본 대화 처리, 민감정보 처리, 메모리 저장, 보호자 공유, 기관 공유, 품질 개선/평가셋 사용 동의가 독립 필드로 반환되는지 검증한다.
- [ ] 권한 있는 사용자만 조회 가능하도록 RBAC와 tenant guard를 적용하고 조회 감사 로그를 연결한다.
- [ ] 직접 식별정보, raw transcript, 미권한 tenant 데이터가 응답에 포함되지 않는지 테스트한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 6개 동의 상태가 독립적으로 조회됨
- Given: 권한 있는 운영자가 elder consent 상태를 조회함
- When: Consent Admin query가 실행됨
- Then: 기본 대화 처리, 민감정보 처리, 메모리 저장, 보호자 공유, 기관 공유, 품질 개선/평가셋 사용 동의가 각각 별도 필드로 반환되어야 한다.

Scenario 2: 삭제 job 상태가 표시됨
- Given: 특정 elder의 개인정보 삭제 요청 job이 존재함
- When: 해당 elder/device를 조회함
- Then: deletion job status와 갱신 시각이 응답에 포함되어야 한다.

Scenario 3: 직접 식별정보가 표시되지 않음
- Given: elder profile에 직접 식별정보가 저장되어 있음
- When: Consent Admin response payload를 생성함
- Then: 응답에는 가명 ID와 상태 정보만 포함되고 직접 식별정보는 제외되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 보안/개인정보: consent 조회는 민감 작업으로 간주하여 감사 로그를 생성해야 한다.
- 범위: 본 태스크는 조회이며 동의 변경 command는 별도 CMD 태스크에서 처리한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-003, DB-008, CT-006
- Blocks: FE-010, TEST-011
