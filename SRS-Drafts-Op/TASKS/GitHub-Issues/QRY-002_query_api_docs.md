---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] QRY-002: API Docs, enum, 표준 오류 코드, cURL/TypeScript 샘플 조회 데이터 구성"
labels: 'feature, backend, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [QRY-002] API Docs, enum, 표준 오류 코드, cURL/TypeScript 샘플 조회 데이터 구성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-002, REQ-FUNC-050, REQ-FUNC-055` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] API Docs 조회 데이터 source를 OpenAPI/contract fixture로 정하고 endpoint별 request/response schema를 노출한다.
- [ ] enum 정의(`risk_level`, `emotion_tags`, error code 등)와 하위 호환성 주의 사항을 조회 DTO에 포함한다.
- [ ] 표준 오류 코드별 HTTP status, retry 가능 여부, 사용자 표시 가능 메시지를 구성한다.
- [ ] `/api/v1/chat/reply`와 Playground 실행에 필요한 cURL/TypeScript sample 조회 데이터를 구성한다.
- [ ] docs response가 CT-005 API contract와 불일치하면 실패하는 contract snapshot test를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: endpoint 문서가 schema와 sample을 포함함
- Given: 개발자가 API Docs에서 `/api/v1/chat/reply`를 선택함
- When: docs query가 실행됨
- Then: request schema, response schema, enum, 표준 오류 코드, cURL/TypeScript sample이 반환되어야 한다.

Scenario 2: 표준 오류 코드가 재시도 가능 여부를 포함함
- Given: API Docs 오류 코드 섹션을 조회함
- When: error code list를 반환함
- Then: 각 오류 코드에 HTTP status, retryable 여부, 개발자용 설명이 포함되어야 한다.

Scenario 3: contract fixture와 docs가 일치함
- Given: CT-005 OpenAPI/contract fixture가 변경됨
- When: docs snapshot test를 실행함
- Then: 문서 DTO와 contract fixture가 일치하지 않으면 테스트가 실패해야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: API Docs 데이터는 가능하면 정적 contract fixture에서 생성하고 운영 DB 조회에 의존하지 않는다.
- 보안/개인정보: sample payload는 synthetic 데이터만 사용하고 실제 elder profile 또는 원문 대화를 포함하지 않는다.
- 유지보수성: enum 추가는 하위 호환성을 유지하며 docs와 contract를 함께 갱신해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-005
- Blocks: FE-003, FE-004, FE-007
