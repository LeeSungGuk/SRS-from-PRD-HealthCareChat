---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] FE-004: Web API Playground endpoint selector와 request builder 구현"
labels: 'feature, frontend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [FE-004] Web API Playground endpoint selector와 request builder 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-051~052, REQ-NF-039` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification`](../../SRS/실버케어_SRS_v0.5_단일구조.md#64-web-console-surface-specification)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Playground endpoint selector에 `/api/v1/chat/reply`, `/api/v1/analyze/emotion`, `/api/v1/schedule/proactive`, `/api/v1/report/poc`를 제공한다.
- [ ] 선택 endpoint의 request builder를 schema 기반으로 렌더링하고 필수 필드, enum, type validation을 실행 전 표시한다.
- [ ] 기본 실행 환경은 sandbox mode로 고정하고 production tenant 호출은 별도 권한과 명시적 confirmation이 있을 때만 허용한다.
- [ ] 실행 전 validation error가 있으면 API 호출을 전송하지 않고 field-level error를 표시한다.
- [ ] Playground 실행 action은 audit log 대상이며, 실행 결과는 후속 response viewer 태스크가 사용할 status, latency, request_id, JSON payload 형태로 반환한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 사용자는 sandbox endpoint를 선택하고 request를 구성함
- Given: 선행 태스크 `FE-001, QRY-002, UX-002`가 완료되고 인증된 role/tenant context가 있음
- When: 사용자가 endpoint를 선택함
- Then: 선택 endpoint의 request fields와 기본 sandbox tenant context가 표시된다.

Scenario 2: 잘못된 request는 실행 전에 차단됨
- Given: 필수 필드 누락, 지원하지 않는 enum, 잘못된 타입 중 하나가 있음
- When: 사용자가 Run action을 누름
- Then: UI는 field-level validation error를 표시하고 API 호출을 전송하지 않는다.

Scenario 3: sandbox mode가 기본값으로 강제됨
- Given: 사용자가 Playground를 처음 열거나 새 request를 생성함
- When: 실행 환경을 확인함
- Then: 기본값은 sandbox mode이며 production 실행은 별도 권한과 명시적 confirmation 없이는 불가능하다.

Scenario 4: Playground 실행은 감사 대상임
- Given: 유효한 sandbox request가 구성됨
- When: 사용자가 Run action을 실행함
- Then: Playground execution audit event를 생성할 수 있도록 tenant, actor, endpoint, request_id가 action result에 연결된다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- Sandbox: `REQ-NF-039`에 따라 sandbox default를 강제하고 production run은 별도 권한을 요구한다.
- API 계약: request builder는 OpenAPI 호환 schema와 동일한 request/response contract를 사용한다.
- 보안/개인정보: production data를 기본값으로 표시하거나 자동 호출하지 않는다.
- UI 범위: Partner Console만 포함하고 보호자/기관/어르신용 완성 앱 UI를 만들지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: FE-001, QRY-002, UX-002
- Blocks: FE-005, FE-006, FE-007, TEST-014, NFR-011
