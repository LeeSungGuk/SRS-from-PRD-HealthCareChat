---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-012: 역할별 접근 제한, 보호자/기관 요약 원문 미노출, cross-tenant no data 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-012] 역할별 접근 제한, 보호자/기관 요약 원문 미노출, cross-tenant no data 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-027, REQ-FUNC-029~032, REQ-FUNC-039` 요구를 구현 가능한 작업 단위로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `역할별 접근 제한, 보호자/기관 요약 원문 미노출, cross-tenant no data 테스트 작성`의 Given/When/Then 시나리오를 테스트 파일로 분리한다.
- [ ] 성공 경로와 실패 경로를 모두 포함하는 fixture를 작성한다.
- [ ] 테스트가 먼저 실패하는지 확인한 뒤 대상 구현 태스크와 연결한다.
- [ ] privacy, tenant isolation, standard error schema 등 회귀 위험을 assertion으로 고정한다.
- [ ] CI 또는 release gate에서 실행할 명령과 pass/fail 기준을 문서화한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 실패하는 테스트가 먼저 작성됨
- Given: `역할별 접근 제한, 보호자/기관 요약 원문 미노출, cross-tenant no data 테스트 작성` 요구사항이 구현되지 않은 상태임
- When: 신규 테스트를 실행함
- Then: 대상 기능 부재 또는 미준수로 테스트가 실패해야 한다.

Scenario 2: 구현 후 테스트가 통과함
- Given: 대상 기능 태스크가 구현됨
- When: 동일 테스트를 다시 실행함
- Then: SRS 기준 성공/실패 시나리오가 모두 통과해야 한다.

## :gear: Technical & Non-Functional Constraints
- 보안: tenant 격리, RBAC, audit log, PII masking 기준을 위반하지 않는다.
- 성능: `/api/v1/chat/reply` 관련 경로는 비LLM p95 800ms, LLM 포함 p95 5초 PoC baseline 계측을 방해하지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test) 또는 문서 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec 또는 운영 문서가 최신화되었는가?
- [ ] `TASKS/실버케어_SRS_v0.5_단일구조_TASKS.md`의 의존성 기준과 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: QRY-009, SEC-002, SEC-005
- Blocks: 후속 태스크는 TASKS 원본의 Dependencies 기준으로 연결
