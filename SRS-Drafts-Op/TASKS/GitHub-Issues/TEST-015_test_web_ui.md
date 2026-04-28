---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-015: PoC Report, Ops Monitoring, User & Consent Admin UI privacy/performance E2E 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-015] PoC Report, Ops Monitoring, User & Consent Admin UI privacy/performance E2E 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-056~060, REQ-NF-037~041` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] PoC Report Console이 JSON/CSV 집계 지표를 표시하고 export에 원문/PII가 없는지 E2E를 작성한다.
- [ ] Ops Monitoring 화면이 request log 필터와 마스킹 필드를 표시하는지 검증한다.
- [ ] Review Queue 화면이 high/critical, SAFETY_FILTERED, LLM timeout 항목을 표시하는지 검증한다.
- [ ] User & Consent Admin View가 가명 elder/device id, 6개 동의 상태, memory enabled, deletion job status를 권한 있는 사용자에게만 표시하는지 테스트한다.
- [ ] 주요 Web Console 초기 조회 화면이 baseline dataset에서 p95 2초 이내 표시되는지 performance 측정과 연결한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: PoC Report UI가 PII 없이 지표를 표시함
- Given: 권한 있는 B2B-PM session과 PoC 집계 데이터가 있음
- When: PoC Report Console을 열람함
- Then: 필수 지표가 표시되고 원문/직접 PII는 노출되지 않아야 한다.

Scenario 2: Ops와 Review Queue는 마스킹된 근거만 표시함
- Given: 운영자 session과 리뷰 대상 이벤트가 있음
- When: Ops Monitoring과 Review Queue를 열람함
- Then: request_id, risk/review 상태가 표시되고 원문/직접 PII는 마스킹되어야 한다.

Scenario 3: Consent Admin은 권한 있는 사용자에게만 표시됨
- Given: 권한 없는 role이 Consent Admin route에 접근함
- When: 페이지를 열람함
- Then: 접근이 차단되어야 하며 동의 상태와 삭제 job 데이터가 표시되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안/개인정보: Web Console 기본 UI의 PII/raw transcript exposure count는 0이어야 한다.
- 성능: API Docs, Playground response viewer, PoC Report, Ops log query 초기 조회는 p95 2초 이내 목표를 측정한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: FE-008~FE-011
- Blocks: TEST-018
