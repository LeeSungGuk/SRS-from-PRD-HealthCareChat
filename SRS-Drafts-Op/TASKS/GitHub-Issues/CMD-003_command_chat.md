---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-003: `/api/v1/chat/reply` 요청 validation과 conversation/message 생성 command 구현"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-003] `/api/v1/chat/reply` 요청 validation과 conversation/message 생성 command 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `API-001, REQ-FUNC-007, REQ-FUNC-014` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `/api/v1/chat/reply` 요청 DTO에서 `tenant_id`, `device_id`, `elder_id`, `utterance` 필수값과 optional `conversation_id`, `use_memory`, `request_context`, `locale`을 검증한다.
- [ ] API Key 인증 결과의 tenant와 body의 `tenant_id`가 일치하는지 확인하고 mismatch 시 상태 변경 없이 표준 오류를 반환한다.
- [ ] 요청 시작 시 `request_id`를 생성하고 신규 또는 기존 `conversation`을 tenant/device/elder scope로 연결한다.
- [ ] 어르신 발화 `message`를 `speaker=elder`, 동일 `request_id`, PII masking 또는 raw reference 정책에 맞게 저장한다.
- [ ] 이 태스크에서는 LLM provider 구현을 포함하지 않고, 후속 응답 생성/분석 command가 사용할 수 있는 command result와 repository 경계를 고정한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 필수 필드 누락 시 validation 오류를 반환함
- Given: `tenant_id`, `device_id`, `elder_id`, `utterance` 중 하나가 누락된 요청이 있음
- When: `/api/v1/chat/reply` command가 실행됨
- Then: HTTP 400 수준의 `INVALID_REQUEST` 오류와 `request_id`가 반환되고 `conversation`, `message` 레코드는 생성되지 않는다.

Scenario 2: 유효한 요청은 대화와 발화를 같은 request_id로 저장함
- Given: `CT-004, DB-004, SEC-002, SEC-004`가 완료되고 API Key tenant와 요청 `tenant_id`가 일치함
- When: 유효한 `/api/v1/chat/reply` 요청이 들어옴
- Then: `conversation`과 어르신 `message`가 생성되며 모든 성공·오류·후속 분석에서 사용할 동일 `request_id`가 반환된다.

Scenario 3: 인증 tenant와 body tenant가 다르면 상태 변경이 없음
- Given: API Key가 속한 tenant와 요청 body의 `tenant_id`가 다름
- When: `/api/v1/chat/reply` command가 실행됨
- Then: 권한 오류가 반환되고 cross-tenant `conversation` 또는 `message`가 생성되지 않는다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리하고 `tenant_id` 조건을 모든 조회/생성 경로에 포함한다.
- 개인정보: `message.text_masked` 저장을 기본으로 하고 원문은 SRS retention 정책에 맞는 `text_raw_ref`로만 참조한다.
- 추적성: `REQ-FUNC-014`에 따라 command 결과, 로그, 후속 분석, 오류 응답이 동일 `request_id`를 사용해야 한다.
- 성능: 이 command의 비LLM 처리 구간은 `REQ-NF-001`의 Phase A p95 800ms baseline 계측 대상이다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-004, DB-004, SEC-002, SEC-004
- Blocks: CMD-004, CMD-007, FE-006, TEST-004
