---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-013: 운영 로그 마스킹, threshold alert payload, review queue 수집 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-013] 운영 로그 마스킹, threshold alert payload, review queue 수집 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-033~035, REQ-NF-006, REQ-NF-035` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] 운영 로그 조회 결과가 `request_id`, endpoint, tenant, latency, error code, safety status, alert status를 포함하는지 테스트한다.
- [ ] 운영 로그와 리뷰 큐 기본 응답에 raw transcript와 직접 PII가 없는지 scan assertion을 추가한다.
- [ ] tenant별 오류율/지연시간 threshold breach 시 5분 이내 alert payload가 생성되는지 테스트한다.
- [ ] alert payload에 tenant, endpoint, 시간 범위, 오류 유형, 실제값, threshold가 포함되는지 검증한다.
- [ ] high/critical, `SAFETY_FILTERED`, LLM timeout, 신고 케이스가 review queue에 100% 수집되는지 coverage test를 작성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 운영 로그가 마스킹되어 표시됨
- Given: raw transcript와 PII가 포함된 요청 로그가 존재함
- When: Ops Monitoring query를 실행함
- Then: 응답에는 필수 운영 필드가 있고 원문/직접 PII는 마스킹되어야 한다.

Scenario 2: threshold breach는 5분 이내 알림을 생성함
- Given: 특정 tenant의 latency 또는 error rate가 threshold를 초과함
- When: monitoring evaluator가 실행됨
- Then: 5분 이내에 영향 범위와 오류 유형이 포함된 alert가 생성되어야 한다.

Scenario 3: qualifying event는 review queue에 수집됨
- Given: high/critical, SAFETY_FILTERED, LLM timeout 이벤트가 발생함
- When: review queue coverage test를 실행함
- Then: 모든 qualifying event가 `request_id` 기준으로 review queue에 연결되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안/개인정보: 운영 테스트 fixture는 합성 데이터만 사용하고 PII 노출 0건을 실패 기준으로 둔다.
- 운영: alert와 review queue 테스트 결과는 release gate evidence로 재사용 가능해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: QRY-006, QRY-007, CMD-012, CMD-013
- Blocks: TEST-018
