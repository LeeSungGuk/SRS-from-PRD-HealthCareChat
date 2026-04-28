---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] UX-003: 운영/개인정보 UI 표시 정책 정의: masked evidence, prohibited display, role별 module visibility"
labels: 'feature, design, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [UX-003] 운영/개인정보 UI 표시 정책 정의: masked evidence, prohibited display, role별 module visibility
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.4, REQ-NF-036~038, REQ-FUNC-057~060` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Conversation Log, Emotion/Risk Analysis, Review Queue, User & Consent Admin 화면별 allowed field와 prohibited display를 표로 정의한다.
- [ ] masked evidence 표시 기준을 정한다: request_id, risk_level, masked utterance excerpt, review_status, model/prompt version은 허용하고 raw transcript와 직접 PII는 기본 금지한다.
- [ ] role별 module visibility와 row-level access를 정의한다: B2B-DEV, B2B-PM, 운영자, reviewer.
- [ ] API Key 발급/폐기, Playground 실행, 로그 조회, 동의 상태 조회, 삭제 job 조회가 audit 대상임을 UI action spec에 표시한다.
- [ ] 원문 접근 예외가 필요한 경우 별도 권한, 사유 입력, audit log가 필요하다는 UX guard를 정의한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 운영 화면의 금지 표시 정보가 정의됨
- Given: UX privacy display policy를 검토함
- When: Conversation Log, Review Queue, Consent Admin 항목을 확인함
- Then: raw transcript, 주민등록번호, 상세 주소, 원문 API Key 등 금지 표시 항목이 명시되어야 한다.

Scenario 2: masked evidence 표시 기준이 구현 가능함
- Given: 위험 리뷰 큐 이벤트가 주어짐
- When: masked evidence UX spec을 확인함
- Then: request_id, risk_level, masked evidence, review_status는 허용되고 원문/직접 PII는 기본 금지되어야 한다.

Scenario 3: 민감 UI action은 audit 대상임
- Given: API Key, Playground, 로그 조회, 동의 조회, 삭제 job 조회 action이 주어짐
- When: UI action spec을 검토함
- Then: 각 action의 audit log 생성 필요 여부가 명시되어야 한다.

## :gear: Technical & Non-Functional Constraints
- UI 범위: Partner Console만 포함하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.
- 보안/개인정보: Web Console 기본 UI의 원문/PII 노출 count는 0이어야 한다.
- 접근 제어: role별 module visibility와 row-level access는 CT-006 RBAC contract를 따른다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-006, CT-008, UX-001
- Blocks: FE-009, FE-010
