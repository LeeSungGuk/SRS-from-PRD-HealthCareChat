---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-016: chat non-LLM p95 800ms, LLM 포함 p95 5초, UI initial load p95 2초 측정 스크립트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-016] chat non-LLM p95 800ms, LLM 포함 p95 5초, UI initial load p95 2초 측정 스크립트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-NF-001, REQ-NF-040` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `/api/v1/chat/reply` 비LLM 처리 구간과 LLM 포함 전체 구간을 분리 측정하는 부하 테스트 스크립트를 작성한다.
- [ ] Phase A PoC 기준 비LLM p95 `<= 800ms`, LLM 포함 전체 완료 p95 `<= 5s`를 report에 표시한다.
- [ ] Partner Console API Docs, Playground response viewer, PoC Report, Ops log query 초기 조회 p95 `<= 2s` 측정 스크립트를 작성한다.
- [ ] 실제 LLM 비용 폭증을 막기 위해 performance test용 mock provider 또는 제한된 sample set을 구성한다.
- [ ] 성능 기준 미달 시 NFR-009 release gate가 실패할 수 있도록 machine-readable report를 남긴다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: chat non-LLM p95가 기준 이내임
- Given: PoC baseline 부하 시나리오가 준비됨
- When: performance test를 실행함
- Then: `/api/v1/chat/reply` 비LLM 처리 p95가 800ms 이하로 report되어야 한다.

Scenario 2: LLM 포함 p95가 기준 이내임
- Given: 제한된 LLM 또는 mock provider 시나리오가 준비됨
- When: chat performance test를 실행함
- Then: LLM 포함 전체 완료 p95가 5초 이하로 report되어야 한다.

Scenario 3: 주요 UI 초기 조회가 2초 이내임
- Given: baseline dataset과 인증 session이 준비됨
- When: Web Console performance test를 실행함
- Then: 대상 화면 초기 조회 p95가 2초 이하로 report되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 비용: LLM performance 측정은 비용 상한과 quota guardrail을 우회하지 않는다.
- 운영: 결과 report는 release gate와 추세 비교에 사용할 수 있어야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CMD-007, FE-006, FE-008, FE-009
- Blocks: TEST-018, NFR-012
