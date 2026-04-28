---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] NFR-010: model/prompt/safety policy/evaluation version 기록과 release checklist 구현"
labels: 'feature, nfr, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [NFR-010] model/prompt/safety policy/evaluation version 기록과 release checklist 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-NF-032, REQ-NF-033` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] AI release record에 model version, prompt version, safety policy version, evaluation dataset version, evaluation result를 함께 저장하는 구조를 정의한다.
- [ ] prompt 또는 safety policy 변경 시 evaluation report 없이는 release checklist가 실패하도록 규칙을 작성한다.
- [ ] enum 값 추가/변경 시 하위 호환성 검토와 partner notice 필요 여부를 기록하는 contract compatibility checklist를 작성한다.
- [ ] release별 변경 이력을 조회할 수 있는 운영 문서 또는 admin query를 제공한다.
- [ ] `CT-007`, `DB-008`, `CMD-012` 산출물과 중복되지 않도록 version 기록과 release gate 책임만 분리한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: AI release record가 완성됨
- Given: model, prompt, safety policy 중 하나가 변경됨
- When: release checklist를 실행함
- Then: model version, prompt version, safety policy version, evaluation result가 모두 기록되어야 통과한다.

Scenario 2: evaluation 결과 누락 시 release가 실패함
- Given: prompt version은 변경되었으나 evaluation result가 없음
- When: release checklist를 실행함
- Then: checklist는 실패하고 누락 항목과 필요한 action이 표시되어야 한다.

Scenario 3: enum 변경은 하위 호환성 검토를 요구함
- Given: API response enum 값이 추가 또는 변경됨
- When: contract compatibility checklist를 실행함
- Then: breaking removal 여부, migration notice, partner notice 필요 여부가 기록되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 유지보수성: AI release record는 추후 provider/model 교체가 가능하도록 provider-specific 필드와 공통 필드를 분리한다.
- 호환성: enum 삭제 또는 의미 변경은 migration notice 없이 허용하지 않는다.
- 보안/개인정보: evaluation result와 release record에는 원문 대화 또는 직접 PII를 저장하지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-007, DB-008, CMD-012
- Blocks: None
