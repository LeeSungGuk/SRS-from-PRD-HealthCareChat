---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-011: 동의 6개 항목 저장, deletion cascade job 생성, retention target 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-011] 동의 6개 항목 저장, deletion cascade job 생성, retention target 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-036, REQ-FUNC-038, REQ-NF-023~027` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] 6개 동의 항목이 독립 필드로 저장/조회되는 unit/integration test를 작성한다.
- [ ] memory consent 철회 시 memory deletion job이 즉시 생성되는지 테스트한다.
- [ ] 삭제 요청이 message, memory, analysis, risk/report/log, evaluation candidate 등 cascade 대상과 연결되는지 검증한다.
- [ ] raw transcript 30일, memory 180일, analysis 180일, risk/report/log 1년, evaluation candidate 30일 retention target 테스트를 작성한다.
- [ ] deletion/retention job evidence에 원문과 직접 PII가 포함되지 않는지 확인한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 6개 동의 항목이 독립 저장됨
- Given: elder consent profile을 생성함
- When: 동의 상태를 저장하고 조회함
- Then: 기본 대화 처리, 민감정보 처리, 메모리 저장, 보호자 공유, 기관 공유, 품질 개선/평가셋 사용 동의가 독립 필드로 확인되어야 한다.

Scenario 2: memory 동의 철회 시 삭제 job이 생성됨
- Given: memory consent가 true이고 summary memory가 존재함
- When: memory consent를 false로 변경함
- Then: 관련 memory deletion job이 즉시 생성되어야 한다.

Scenario 3: retention target 초과 데이터가 정리됨
- Given: retention 기간을 초과한 raw transcript와 evaluation candidate가 존재함
- When: retention job test를 실행함
- Then: 정책에 따라 삭제 또는 폐기 상태로 전환되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안/개인정보: 삭제/retention evidence에는 원문 대화와 직접 PII를 포함하지 않는다.
- 운영: retention 테스트는 실제 운영 데이터가 아닌 synthetic fixture와 clock control을 사용한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CMD-011, QRY-008
- Blocks: TEST-018
