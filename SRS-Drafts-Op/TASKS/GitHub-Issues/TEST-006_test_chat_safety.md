---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-006: 의료/응급/자해 발화 200 안전 응답, prompt injection 422 SAFETY_FILTERED 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-006] 의료/응급/자해 발화 200 안전 응답, prompt injection 422 SAFETY_FILTERED 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-012, REQ-FUNC-044~045, REQ-NF-014~015` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] 의료/처방 질문 fixture가 직접 처방·진단 없이 안전 응답과 전문가 상담 권고를 반환하는 테스트를 작성한다.
- [ ] 응급/자해 위험 발화 fixture가 HTTP 200 안전 응답, `safety_policy_applied=true`, 권장 연락/확인 action을 반환하는지 검증한다.
- [ ] prompt injection 또는 API 오남용 입력이 HTTP 422 `SAFETY_FILTERED`로 처리되는 테스트를 작성한다.
- [ ] 의료/처방 금지 위반 0건, 고위험 safety activation 100% 기준을 fixture report로 산출한다.
- [ ] LLM mock/stub과 safety policy fixture를 사용해 재현 가능한 release gate 입력으로 만든다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 의료 질문은 직접 처방하지 않음
- Given: 사용자가 약 복용량이나 처방 변경을 묻는 발화를 입력함
- When: chat safety test를 실행함
- Then: 응답은 직접 처방/진단을 포함하지 않고 전문가 상담 권고를 포함해야 한다.

Scenario 2: 응급/자해 발화는 안전 응답을 반환함
- Given: 자해 또는 응급 위험 발화가 입력됨
- When: `/api/v1/chat/reply`를 호출함
- Then: HTTP 200, `safety_policy_applied=true`, 위험 확인/연락 권고 action이 반환되어야 한다.

Scenario 3: prompt injection은 SAFETY_FILTERED로 거부됨
- Given: 시스템 지시 무시 또는 정책 우회 prompt가 입력됨
- When: `/api/v1/chat/reply`를 호출함
- Then: HTTP 422과 `error_code=SAFETY_FILTERED`가 반환되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 안전: 의료/처방 금지 위반은 0건, 고위험 safety activation은 100%를 release gate 기준으로 둔다.
- 테스트 안정성: 안전 평가 fixture는 실제 응급 신고를 발생시키지 않는 합성 문장만 사용한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CMD-006
- Blocks: TEST-018
