---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] NFR-002: tenant별 오류율/지연시간 threshold와 5분 이내 운영자 알림 생성 구현"
labels: 'feature, nfr, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [NFR-002] tenant별 오류율/지연시간 threshold와 5분 이내 운영자 알림 생성 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-034, REQ-NF-006` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] tenant별 `error_rate`, `p95_latency_ms`, `llm_timeout_rate` threshold 설정 구조를 정의한다.
- [ ] threshold breach 감지 시 `tenant_id`, `endpoint`, `time_window`, `metric_name`, `actual_value`, `threshold`, `error_type`을 포함한 운영 알림 payload를 생성한다.
- [ ] 알림 생성 시각이 breach 감지 시각으로부터 5분 이내인지 검증하는 테스트 fixture를 작성한다.
- [ ] 중복 알림을 방지하기 위한 deduplication key와 해제 조건을 정의한다.
- [ ] 알림 조회/확인 이벤트가 audit log 또는 ops event로 남는지 확인한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: threshold 초과 시 운영 알림이 생성됨
- Given: 특정 tenant의 `/api/v1/chat/reply` p95 latency가 설정 threshold를 초과함
- When: monitoring evaluator가 해당 window를 평가함
- Then: 5분 이내에 tenant, endpoint, 시간 범위, 초과 지표, 실제값, threshold가 포함된 운영 알림이 생성되어야 한다.

Scenario 2: 오류율 초과 알림이 오류 유형을 포함함
- Given: 특정 tenant의 5xx error rate가 threshold를 초과함
- When: monitoring evaluator가 알림을 생성함
- Then: 알림 payload에 오류 유형과 영향 endpoint가 포함되어야 한다.

Scenario 3: threshold 미만에서는 알림이 생성되지 않음
- Given: error rate와 latency가 모두 threshold 미만임
- When: monitoring evaluator가 실행됨
- Then: 신규 운영 알림은 생성되지 않아야 하며 평가 로그만 남아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안/개인정보: 알림 payload에는 원문 대화나 직접 PII를 포함하지 않고 tenant 및 request 범위 식별자만 포함한다.
- 성능: evaluator는 NFR-001 metric을 재사용하며 API 요청 경로의 동기 처리 시간을 증가시키지 않는다.
- 운영: 알림 실패 자체도 evidence로 남겨 수동 확인이 가능해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: NFR-001, CMD-013
- Blocks: None
