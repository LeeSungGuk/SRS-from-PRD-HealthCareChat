---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] MOCK-002: sandbox elder profile, consent state, sample utterance, 정상/안전/오류 시나리오 seed 작성"
labels: 'feature, mock, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [MOCK-002] sandbox elder profile, consent state, sample utterance, 정상/안전/오류 시나리오 seed 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `6.4 Sandbox Scenario Picker, REQ-FUNC-003, REQ-FUNC-053` 요구를 구현 가능한 작업 단위로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `sandbox elder profile, consent state, sample utterance, 정상/안전/오류 시나리오 seed 작성`에 필요한 synthetic seed/fixture 데이터 범위를 정의한다.
- [ ] 실제 개인정보를 포함하지 않는 sample tenant, device, elder, consent, request/response payload를 작성한다.
- [ ] 정상, 실패, 안전 정책, 권한 오류 시나리오를 fixture로 분리한다.
- [ ] Frontend와 테스트가 동일 fixture를 참조할 수 있도록 파일 구조와 naming을 정한다.
- [ ] fixture에 raw PII 또는 실제 API Key가 포함되지 않는지 검토한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 정상 경로 수행
- Given: 선행 태스크 `DB-003, CT-004`가 완료되어 있음
- When: `sandbox elder profile, consent state, sample utterance, 정상/안전/오류 시나리오 seed 작성` 작업을 수행함
- Then: SRS 관련 섹션 `6.4 Sandbox Scenario Picker, REQ-FUNC-003, REQ-FUNC-053`의 기대 동작 또는 산출물이 충족되어야 한다.

Scenario 2: 실패 또는 제한 조건 처리
- Given: 필수 입력, 권한, tenant, consent, quota, schema 중 하나가 유효하지 않음
- When: `sandbox elder profile, consent state, sample utterance, 정상/안전/오류 시나리오 seed 작성` 작업이 실행됨
- Then: 표준 오류, 접근 제한, 마스킹, audit 또는 backlog 처리 기준에 맞게 실패해야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test) 또는 문서 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec 또는 운영 문서가 최신화되었는가?
- [ ] `TASKS/실버케어_SRS_v0.5_단일구조_TASKS.md`의 의존성 기준과 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-003, CT-004
- Blocks: 후속 태스크는 TASKS 원본의 Dependencies 기준으로 연결
