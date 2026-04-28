---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-009: proactive trigger validation과 의료 지시 문구 금지 테스트 작성"
labels: 'feature, test, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-009] proactive trigger validation과 의료 지시 문구 금지 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-020~021` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] proactive trigger request의 필수 필드, 지원 trigger type, tenant/device/elder context validation 테스트를 작성한다.
- [ ] 정상 trigger가 쉬운 존댓말의 짧은 선제 발화 후보를 생성하는지 검증한다.
- [ ] 선제 발화가 의료 지시, 처방, 진단, 응급 확정 표현을 포함하지 않는지 금지어/패턴 fixture를 작성한다.
- [ ] 동의 또는 정책상 proactive message가 불가한 경우 생성하지 않는 negative test를 작성한다.
- [ ] 이 태스크는 실제 push/notification 발송이 아니라 message candidate 생성 검증으로 제한한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 유효한 trigger가 선제 발화 후보를 생성함
- Given: 지원되는 proactive trigger와 유효한 elder/device context가 주어짐
- When: proactive message candidate를 생성함
- Then: 짧고 쉬운 존댓말 발화 후보가 반환되어야 한다.

Scenario 2: 지원하지 않는 trigger는 실패함
- Given: 정의되지 않은 trigger type이 주어짐
- When: proactive validation을 실행함
- Then: `INVALID_REQUEST`에 해당하는 validation 오류가 발생해야 한다.

Scenario 3: 의료 지시 문구가 생성되지 않음
- Given: 건강 관련 proactive trigger가 주어짐
- When: message candidate를 생성함
- Then: 약 복용량, 처방 변경, 진단 확정 등 의료 지시 문구가 포함되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 범위: Phase A에서는 proactive candidate 생성 검증만 포함하고 실제 보호자/기관 앱 알림 UI는 만들지 않는다.
- 안전: 의료/응급 판단을 자동 확정하는 문구는 실패 조건으로 둔다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CMD-009
- Blocks: TEST-018
