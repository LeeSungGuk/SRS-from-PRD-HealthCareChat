---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-001: DTO와 enum schema validation 단위 테스트 작성"
labels: 'feature, test, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-001] DTO와 enum schema validation 단위 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `CT-002~004, REQ-FUNC-018~019, REQ-FUNC-043` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `chat/reply`, `analyze/emotion`, `proactive/message` DTO의 필수 필드와 타입 validation fixture를 작성한다.
- [ ] `risk_level`, `emotion_tags`, `recommended_action`, 표준 오류 코드 enum의 허용/미허용 값 테스트를 작성한다.
- [ ] 주요 API response에 `schema_version`이 포함되는지 검증한다.
- [ ] enum 추가 시 기존 known value가 깨지지 않는 하위 호환성 snapshot 테스트를 작성한다.
- [ ] contract test 명령과 실패 시 갱신해야 할 CT 문서/fixture 경로를 문서화한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 필수 DTO 누락이 validation 실패로 잡힘
- Given: `tenant_id` 또는 `utterance`가 누락된 chat request fixture가 있음
- When: DTO validation 단위 테스트를 실행함
- Then: `INVALID_REQUEST`에 해당하는 validation 오류가 발생해야 한다.

Scenario 2: 지원하지 않는 enum 값이 거부됨
- Given: `risk_level=urgent_unknown`과 같이 정의되지 않은 enum 값이 포함됨
- When: schema validation 테스트를 실행함
- Then: 허용 enum 목록 불일치로 테스트가 실패해야 한다.

Scenario 3: response schema_version이 필수임
- Given: 주요 API response fixture가 있음
- When: contract snapshot test를 실행함
- Then: 모든 response fixture가 `schema_version`을 포함해야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 계약: fixture는 SRS Appendix 6.1 API Endpoint List와 CT-002~004 contract를 기준으로 작성한다.
- 호환성: enum 삭제 또는 의미 변경은 테스트 실패로 처리한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-004
- Blocks: TEST-018, NFR-009
