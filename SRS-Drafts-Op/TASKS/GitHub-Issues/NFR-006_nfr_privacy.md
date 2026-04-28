---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] NFR-006: raw transcript 30일, memory 180일, analysis 180일, risk/report/log 1년 retention job 구현"
labels: 'feature, nfr, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [NFR-006] raw transcript 30일, memory 180일, analysis 180일, risk/report/log 1년 retention job 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-NF-024~027, 6.2.4` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `conversation_turn.raw_transcript`는 기본 30일 이내 삭제 또는 요약 전환되도록 lifecycle job 기준을 정의한다.
- [ ] summary memory는 동의 유지 중 최대 180일 보존하고 메모리 동의 철회 시 즉시 삭제 job을 생성한다.
- [ ] emotion/risk analysis 결과는 최대 180일, risk event/report/masked ops log는 최대 1년 보존으로 분리한다.
- [ ] evaluation candidate는 리뷰 완료 전 최대 30일 임시 보관하고 별도 동의 또는 가명/익명 처리 불가 시 폐기한다.
- [ ] retention job 실행 결과, 삭제 건수, 실패 건수, 재시도 대상을 audit 가능한 report로 남긴다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: raw transcript 30일 보존 제한이 적용됨
- Given: 생성 후 30일이 지난 raw transcript가 존재함
- When: retention job이 실행됨
- Then: 해당 원문은 삭제 또는 정책상 허용된 요약 데이터로 전환되고 실행 결과가 기록되어야 한다.

Scenario 2: 메모리 동의 철회 시 즉시 삭제 job이 생성됨
- Given: 사용자가 memory consent를 철회함
- When: consent update command가 완료됨
- Then: 관련 summary memory 삭제 job이 즉시 생성되고 완료 또는 실패 상태가 추적되어야 한다.

Scenario 3: 평가셋 후보 30일 제한이 적용됨
- Given: 리뷰 완료 전 30일을 초과한 evaluation candidate가 존재함
- When: dataset retention job이 실행됨
- Then: 별도 동의 또는 가명/익명 처리 근거가 없으면 해당 candidate가 폐기되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안/개인정보: tenant 격리와 RBAC를 우회하는 bulk delete를 금지하고 삭제 evidence에는 원문을 포함하지 않는다.
- 운영: retention job은 실패 항목을 재시도 가능하게 기록해야 하며 수동 재처리 기준을 제공한다.
- 비용: 장기 보관 데이터가 비용을 증가시키지 않도록 raw transcript와 evaluation candidate를 우선 정리한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-004, DB-005, DB-006, CMD-011
- Blocks: None
