---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] QRY-003: sandbox profile, sample utterance, scenario preset 조회"
labels: 'feature, backend, priority:low'
assignees: ''
---

## :dart: Summary
- 기능명: [QRY-003] sandbox profile, sample utterance, scenario preset 조회
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-003, REQ-FUNC-053` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] sandbox profile 목록 조회 DTO에 synthetic elder profile id, display label, locale, consent preset, risk baseline을 포함한다.
- [ ] sample utterance 조회 DTO에 정상 대화, 고독/우울 위험, 안전 응답, validation error 유도 문장을 구분해 제공한다.
- [ ] scenario preset 조회 DTO에 expected response mode, expected risk level, expected error code, request payload template을 포함한다.
- [ ] 모든 sandbox 데이터가 실제 개인정보가 아닌 합성 데이터임을 fixture와 UI 표시 필드에 반영한다.
- [ ] scenario id가 유효하지 않거나 production tenant에서 sandbox preset을 잘못 호출한 경우의 오류 처리를 테스트한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: sandbox scenario preset이 request payload를 구성함
- Given: 사용자가 sample elder profile과 안전 응답 scenario를 선택함
- When: sandbox query가 실행됨
- Then: 실제 개인정보 없이 `/api/v1/chat/reply` request payload template이 반환되어야 한다.

Scenario 2: 정상/안전/오류 scenario가 구분됨
- Given: sandbox scenario 목록을 조회함
- When: query response를 확인함
- Then: 정상 응답, 안전 응답, 오류 응답 시나리오가 각각 식별 가능한 id와 expected result를 가져야 한다.

Scenario 3: 실제 개인정보가 포함되지 않음
- Given: sandbox fixture 전체를 스캔함
- When: PII scan을 실행함
- Then: 실명, 주민등록번호, 실제 주소, 실제 전화번호 형식의 값이 없어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: sandbox preset은 MOCK-002 fixture를 기준으로 제공하며 production elder data를 조회하지 않는다.
- 보안/개인정보: sandbox는 기본 mode로 실행되며 production tenant 호출은 별도 권한이 필요하다.
- 비용: sandbox scenario는 Playground quota와 NFR-008 cost guardrail을 우회하지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: MOCK-002
- Blocks: FE-005
