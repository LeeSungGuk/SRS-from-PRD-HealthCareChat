---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] NFR-011: 첫 API 성공 30분 이내 onboarding 측정 스크립트와 시나리오 작성"
labels: 'feature, nfr, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [NFR-011] 첫 API 성공 30분 이내 onboarding 측정 스크립트와 시나리오 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-NF-007, REQ-FUNC-002, REQ-FUNC-050~055` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] API Key 발급, API Docs 확인, sample request 실행, 첫 `/api/v1/chat/reply` 성공까지의 onboarding 시나리오를 작성한다.
- [ ] 측정 시작/종료 기준을 정의하고 PoC onboarding test에서 첫 성공 API 호출 시간이 30분 이내인지 기록한다.
- [ ] cURL 또는 TypeScript 샘플 코드가 sandbox tenant와 Web API Playground에서 동일한 request/response contract를 사용하도록 검증한다.
- [ ] 실패 원인을 API Key 오류, 권한 오류, DTO validation 오류, 문서 이해 오류로 분류하는 observation sheet를 작성한다.
- [ ] onboarding 개선이 필요한 friction point를 backlog로 연결하되 SRS 외 기능은 추가하지 않는다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 신규 B2B 개발자가 30분 이내 첫 API 호출에 성공함
- Given: 개발자에게 API Key, API Docs, sample code만 제공됨
- When: onboarding timer를 시작하고 `/api/v1/chat/reply` sandbox 호출을 수행함
- Then: 첫 2xx 응답까지 걸린 시간이 30분 이내로 기록되어야 한다.

Scenario 2: 샘플 코드와 Playground contract가 일치함
- Given: API Docs의 sample request와 Web API Playground sandbox scenario가 준비되어 있음
- When: 두 경로로 동일한 scenario를 실행함
- Then: request 필드와 response 주요 필드가 SRS API contract와 일치해야 한다.

Scenario 3: 실패 원인이 분류됨
- Given: onboarding test 중 첫 호출이 실패함
- When: observation sheet를 작성함
- Then: 실패 원인이 인증, 권한, validation, 문서 이해, 시스템 오류 중 하나로 분류되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안/개인정보: onboarding용 API Key는 sandbox tenant 범위로 제한하고 production 호출 권한을 부여하지 않는다.
- 비용: onboarding test는 Playground quota와 LLM retry cap을 우회하지 않는다.
- DX: 측정은 실제 신규 사용자 관찰 또는 재현 가능한 scripted walkthrough 중 하나로 evidence를 남긴다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: FE-002~FE-007, TEST-014
- Blocks: None
