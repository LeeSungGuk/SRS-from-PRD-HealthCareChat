---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] SEC-005: PII masking service 구현 및 message/log/report 저장 전 적용"
labels: 'feature, security, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [SEC-005] PII masking service 구현 및 message/log/report 저장 전 적용
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-NF-016, REQ-NF-020, REQ-NF-037, 6.2.4` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] PII masking service의 input/output을 정의하고 실명, 주민등록번호, 전화번호, 주소, 계좌/카드 패턴 등 민감 후보를 마스킹한다.
- [ ] message 저장 전 raw transcript 처리, ops log 저장, report 생성, review queue/dataset candidate 생성 전에 masking hook을 연결한다.
- [ ] 마스킹 결과에는 원문 저장 위치와 마스킹된 display text를 구분하고 raw 보존 기간은 NFR-006 retention 정책을 따른다.
- [ ] Web Console 기본 응답에서 raw transcript와 직접 식별정보가 노출되지 않도록 query layer와 연결한다.
- [ ] PII masking precision/recall fixture와 production PII exposure incident 0건을 검증하는 scan test를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 대화 저장 전 PII가 마스킹됨
- Given: 실명, 주민등록번호, 전화번호, 주소가 포함된 utterance가 입력됨
- When: message 저장 전 PII masking service가 실행됨
- Then: display/log/report용 텍스트에는 해당 PII가 마스킹되어야 한다.

Scenario 2: Ops/Report/Review 응답에 원문 PII가 노출되지 않음
- Given: 원문 데이터에 직접 식별정보가 포함되어 있음
- When: 운영 로그, PoC report, review queue response를 생성함
- Then: 기본 응답에는 raw transcript와 직접 PII가 포함되지 않아야 한다.

Scenario 3: PII scan fixture가 기준을 통과함
- Given: PII positive/negative test fixture가 준비되어 있음
- When: masking test suite를 실행함
- Then: fixture 기준의 탐지/마스킹 결과가 기대값과 일치하고 노출 사고 count가 0이어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 보안/개인정보: 마스킹 서비스는 raw 보존 정책을 우회하지 않으며 원문 접근은 별도 권한과 감사 로그가 있어야 한다.
- 안전: 과도한 마스킹으로 안전 판단에 필요한 위험 신호가 사라지지 않도록 원문 저장 정책과 분석 입력 정책을 분리 검토한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-008, DB-004, DB-006
- Blocks: CMD-008, QRY-005, QRY-006, QRY-009, TEST-012
