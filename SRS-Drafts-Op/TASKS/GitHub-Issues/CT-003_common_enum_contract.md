---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CT-003: 공통 Enum 계약 정의"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CT-003] 공통 enum 계약 정의
- 목적: `risk_level`, `emotion_tags`, `recommended_action`, `trigger_type`, `error_code` 값의 단일 진실 공급원을 만들어 API, DB, UI, 테스트가 동일한 값 집합을 사용하도록 한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`/SRS/실버케어_SRS_v0.5_단일구조.md#627-enumerations`](../../SRS/실버케어_SRS_v0.5_단일구조.md#627-enumerations)
- 시퀀스 다이어그램: [`/SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`/SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`/SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `risk_level` enum 값을 `none`, `low`, `medium`, `high`, `critical`로 정의한다.
- [ ] `emotion_tags` enum 값을 SRS 6.2.7 기준으로 정의한다.
- [ ] `recommended_action` enum 값을 SRS 6.2.7 기준으로 정의한다.
- [ ] `trigger_type` enum 값을 `morning`, `meal`, `sleep`, `check_in`, `custom`으로 정의한다.
- [ ] `error_code` enum 값을 standard error code와 동일하게 정의한다.
- [ ] enum 추가·변경 시 하위 호환성을 유지하는 versioning 규칙을 정의한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 정의되지 않은 `risk_level` 반환 방지
- Given: 위험 분석 결과가 생성됨
- When: API response schema validation을 수행함
- Then: `risk_level`은 `none`, `low`, `medium`, `high`, `critical` 중 하나여야 한다.

Scenario 2: 정의되지 않은 `recommended_action` 반환 방지
- Given: 분석 또는 안전 응답 결과가 생성됨
- When: API response schema validation을 수행함
- Then: `recommended_action`은 SRS 6.2.7에 정의된 값 중 하나여야 한다.

Scenario 3: enum 확장 시 하위 호환성 유지
- Given: 새로운 enum 후보가 추가되어야 하는 요구가 발생함
- When: enum 계약 변경 절차를 검토함
- Then: 기존 enum 값을 제거하거나 의미를 변경하지 않고 schema version 또는 partner notice 기준이 명시되어 있어야 한다.

## :gear: Technical & Non-Functional Constraints
- 호환성: 기존 enum 값 제거는 금지하고, 추가 시 하위 호환성을 유지해야 한다.
- 테스트 가능성: 모든 enum은 schema validation과 unit test에서 재사용 가능해야 한다.
- 문서화: API Docs와 Playground request builder가 동일 enum 정의를 참조해야 한다.
- 보안: 오류 코드 enum은 내부 시스템 세부사항을 노출하는 값을 포함하지 않아야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] API, DB, UI, 테스트에서 재사용 가능한 enum 정의가 명시되었는가?
- [ ] SRS 6.2.7과 불일치하는 enum 값이 없는가?
- [ ] enum 추가·변경 versioning 규칙이 문서화되었는가?
- [ ] schema validation 테스트 항목이 정의되었는가?

## :construction: Dependencies & Blockers
- Depends on: CT-002
- Blocks: CT-004, CT-007, DB-005, SEC-004, CMD-006
