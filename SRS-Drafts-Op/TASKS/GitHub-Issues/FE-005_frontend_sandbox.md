---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] FE-005: Sandbox Scenario Picker UI 구현: 합성 profile, sample utterance, scenario preset"
labels: 'feature, frontend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [FE-005] Sandbox Scenario Picker UI 구현: 합성 profile, sample utterance, scenario preset
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-003, REQ-FUNC-053` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Sandbox Scenario Picker에 합성 elder profile, sample utterance, scenario type(normal, safety, error)을 선택하는 UI를 구현한다.
- [ ] 선택한 scenario가 Playground request builder를 채우도록 endpoint, tenant, device, elder, utterance 또는 error preset payload를 생성한다.
- [ ] 정상 200 응답, 안전 정책 적용 200 응답, 표준 오류 응답을 실제 개인정보 없이 재현할 수 있는 preset을 제공한다.
- [ ] sandbox profile과 sample utterance는 synthetic data로만 구성하고 production elder profile을 참조하지 않는다.
- [ ] scenario 선택, request populate, Playground 실행 이동 흐름을 E2E 테스트로 검증한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 사용자는 합성 profile과 시나리오를 선택함
- Given: 선행 태스크 `FE-004, QRY-003`가 완료되고 인증된 role/tenant context가 있음
- When: 사용자가 sample elder profile과 scenario type을 선택함
- Then: 실제 개인정보 없이 해당 시나리오의 request payload가 구성된다.

Scenario 2: 정상/안전/오류 응답 preset을 재현함
- Given: normal, safety, error scenario preset이 존재함
- When: 각 scenario를 Playground에 적용하고 실행함
- Then: 정상 200 응답, 안전 정책 적용 200 응답, 표준 오류 응답을 각각 재현할 수 있어야 한다.

Scenario 3: sandbox는 실제 개인정보 profile을 사용하지 않음
- Given: scenario list와 generated payload를 검토함
- When: profile, utterance, device, elder 값을 확인함
- Then: 실제 개인정보 profile, 직접 식별정보, production tenant 데이터가 포함되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- Sandbox: 모든 scenario는 synthetic fixture와 sandbox tenant context를 사용해야 한다.
- 개발자 경험: B2B 개발자가 정상/안전/오류 응답 차이를 빠르게 이해할 수 있도록 Playground request와 직접 연결한다.
- 개인정보: 실제 elder profile, raw transcript, 직접 식별정보를 scenario preset에 포함하지 않는다.
- UI 범위: Partner Console만 포함하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: FE-004, QRY-003
- Blocks: TEST-014, NFR-011
