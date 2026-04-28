---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] NFR-007: 외부 LLM 원문 학습 비허용 설정, 국외 이전/보관/삭제 고지 체크리스트 작성"
labels: 'feature, nfr, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [NFR-007] 외부 LLM 원문 학습 비허용 설정, 국외 이전/보관/삭제 고지 체크리스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-NF-028, C-TEC-006` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Gemini API 및 Vercel AI SDK 사용 시 원문 대화가 외부 제공자의 모델 학습에 사용되지 않도록 설정 또는 계약 근거를 확인한다.
- [ ] LLM provider 설정, 데이터 보관 위치, 보관 기간, 삭제 가능성, 국외 이전 여부를 vendor checklist로 정리한다.
- [ ] partner와 정보주체에게 고지해야 할 항목을 privacy notice checklist로 작성한다.
- [ ] provider 교체 시 동일 checklist를 재검증하는 smoke test 또는 release checklist 항목을 추가한다.
- [ ] 외부 LLM 호출 payload에 불필요한 원문/PII가 포함되지 않도록 NFR-006/NFR-020과 연결한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: vendor learning disabled 근거가 확인됨
- Given: 기본 LLM provider가 Google Gemini API로 설정되어 있음
- When: vendor checklist를 검토함
- Then: 원문 학습 비허용 설정 또는 계약/정책 근거가 checklist에 기록되어야 한다.

Scenario 2: 국외 이전/보관/삭제 고지 항목이 완성됨
- Given: partner onboarding 또는 PoC 운영 전 privacy notice가 준비됨
- When: disclosure checklist를 실행함
- Then: 데이터 보관, 국외 이전 가능성, 삭제 요청 가능성, 외부 LLM 사용 사실이 모두 표시되어야 한다.

Scenario 3: provider 교체 시 재검증이 필요함
- Given: 환경 변수 변경으로 LLM provider 또는 model이 교체됨
- When: release checklist를 실행함
- Then: vendor checklist 재승인 없이는 PoC/production promote가 실패해야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- LLM 통합: C-TEC-006에 따라 Vercel AI SDK 표준 인터페이스를 사용하되 provider별 개인정보 처리 차이를 checklist에 남긴다.
- 보안/개인정보: vendor evidence에는 secret 값이나 원문 대화를 포함하지 않는다.
- 운영: vendor checklist 미완료는 NFR-009 release gate 실패 조건으로 간주한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-007
- Blocks: None
