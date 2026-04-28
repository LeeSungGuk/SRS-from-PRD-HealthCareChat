---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-007: 쉬운 한국어 존댓말/공감 루브릭 평가 fixture와 평균 4.0/5 검증 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-007] 쉬운 한국어 존댓말/공감 루브릭 평가 fixture와 평균 4.0/5 검증 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-009, REQ-NF-017` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] 쉬운 한국어, 존댓말, 공감 표현, 과잉 단정 금지 기준을 5점 척도 루브릭으로 문서화한다.
- [ ] 정상 대화, 외로움 표현, 반복 질문, 정보 부족 상황 등 품질 평가 fixture를 작성한다.
- [ ] rule-based 사전 점검과 사람 평가 또는 LLM-as-judge 보조 평가를 분리한다.
- [ ] 평균 점수 4.0/5 이상 여부를 산출하는 평가 report를 생성한다.
- [ ] 의료/안전 케이스는 TEST-006과 중복하지 않고 대화 자연스러움과 응답 태도만 검증한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 쉬운 한국어 존댓말 기준을 만족함
- Given: 정상 대화 fixture가 준비되어 있음
- When: 응답 품질 평가를 실행함
- Then: 응답은 존댓말을 사용하고 난해한 전문 용어를 과도하게 포함하지 않아야 한다.

Scenario 2: 공감과 과잉 단정 금지 기준을 만족함
- Given: 외로움 또는 불안 표현 fixture가 준비되어 있음
- When: 응답 품질 평가를 실행함
- Then: 응답은 공감 표현을 포함하고 사용자의 상태를 단정하지 않아야 한다.

Scenario 3: 평균 품질 점수가 4.0 이상임
- Given: 품질 평가 fixture 전체가 준비되어 있음
- When: rubric scoring report를 생성함
- Then: 평균 점수는 5점 척도에서 4.0 이상이어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 평가: LLM-as-judge를 사용할 경우 최종 release gate에는 평가 기준과 샘플 결과를 evidence로 남긴다.
- 개인정보: 평가 fixture는 합성 대화만 사용한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CMD-005, CMD-006
- Blocks: TEST-018
