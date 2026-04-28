---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-005: memory consent true/false, memory 미사용 시 fabricated past 금지 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-005] memory consent true/false, memory 미사용 시 fabricated past 금지 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-010~011, REQ-FUNC-037` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] memory consent가 true이고 `use_memory=true`인 경우 저장된 summary memory가 prompt/context에 사용되는지 테스트한다.
- [ ] memory consent가 false이면 summary memory를 조회하지 않고 `memory_used=false`를 반환하는 테스트를 작성한다.
- [ ] memory 미사용 시 과거 사실을 지어내지 않도록 fabricated past 금지 fixture를 작성한다.
- [ ] 메모리 동의 철회 이후 기존 memory가 삭제 job 대상으로 연결되는지 CMD-011/TEST-011과 경계를 확인한다.
- [ ] LLM mock 응답에서 memory 사용 여부가 검증 가능하도록 prompt/context spy를 구성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: memory consent가 true이면 memory를 사용할 수 있음
- Given: memory consent가 true이고 summary memory가 존재함
- When: `use_memory=true`로 chat reply를 호출함
- Then: memory context가 사용되고 response의 `memory_used=true`가 반환되어야 한다.

Scenario 2: memory consent가 false이면 memory를 사용하지 않음
- Given: memory consent가 false이고 summary memory가 존재함
- When: `use_memory=true`로 chat reply를 호출함
- Then: memory context는 사용되지 않고 `memory_used=false`가 반환되어야 한다.

Scenario 3: memory 미사용 시 과거 사실을 지어내지 않음
- Given: 저장된 memory가 없고 사용자가 과거 대화를 암시하지 않음
- When: chat reply를 생성함
- Then: 응답은 과거 사실을 단정하거나 fabricated past를 포함하지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 개인정보: memory 사용은 동의 상태와 retention 정책을 우회하지 않아야 한다.
- 테스트 안정성: fabricated past 검증은 deterministic fixture 또는 rule-based assertion을 우선한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CMD-004
- Blocks: TEST-018
