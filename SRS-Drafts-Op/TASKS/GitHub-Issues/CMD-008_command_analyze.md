---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-008: `/api/v1/analyze/emotion` validation, 분석 실행, PII 제거된 detected_keywords, analysis_result 저장 구현"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-008] `/api/v1/analyze/emotion` validation, 분석 실행, PII 제거된 detected_keywords, analysis_result 저장 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `API-002, REQ-FUNC-016~019, REQ-NF-016` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `/api/v1/analyze/emotion` 요청 DTO에서 `tenant_id`, `elder_id`, `text` 필수값과 optional `context_window`, `locale`을 검증한다.
- [ ] API Key 인증 tenant와 요청 `tenant_id` 일치를 확인하고 tenant mismatch 시 상태 변경 없이 표준 오류를 반환한다.
- [ ] 감정/위험 분석 결과를 `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action`, optional `detected_keywords` schema로 생성한다.
- [ ] `detected_keywords`에는 직접 식별정보와 원문 전체가 포함되지 않도록 PII 제거/마스킹을 적용한다.
- [ ] 분석 결과를 `analysis_result`에 저장하고 `request_id`, tenant, elder 기준으로 조회 가능하게 한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 필수 필드 누락 시 분석을 실행하지 않음
- Given: `tenant_id`, `elder_id`, `text` 중 하나가 누락된 요청이 있음
- When: `/api/v1/analyze/emotion` command가 실행됨
- Then: HTTP 400 수준의 `INVALID_REQUEST`가 반환되고 `analysis_result`는 생성되지 않는다.

Scenario 2: 유효한 분석 요청은 enum 기반 결과를 반환하고 저장함
- Given: `CT-004, DB-005, SEC-002, SEC-005`가 완료되고 유효한 분석 요청이 있음
- When: `/api/v1/analyze/emotion` command가 실행됨
- Then: 응답과 `analysis_result`에는 SRS enum을 따르는 `emotion_tags`, `risk_level`, `recommended_action`과 동일 `request_id`가 포함된다.

Scenario 3: detected_keywords에는 직접 식별정보가 포함되지 않음
- Given: 분석 대상 텍스트에 전화번호, 주소, 실명 등 직접 식별정보가 포함됨
- When: detected keywords가 생성됨
- Then: `detected_keywords`와 저장된 분석 결과에는 직접 식별정보 또는 원문 전체가 포함되지 않는다.

Scenario 4: tenant mismatch 시 cross-tenant 분석 저장이 발생하지 않음
- Given: API Key tenant와 요청 body의 `tenant_id`가 다름
- When: `/api/v1/analyze/emotion` command가 실행됨
- Then: 권한 오류가 반환되고 다른 tenant의 `analysis_result`가 생성되지 않는다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: `analysis_result` 저장은 `tenant_id`, `elder_id`, `request_id`를 필수로 포함한다.
- 개인정보: `REQ-NF-016`에 따라 응답, 로그, 리포트의 불필요한 원문/PII 노출은 0건이어야 한다.
- API 계약: `/api/v1/analyze/emotion` 응답은 API-002의 response field와 enum 계약을 만족해야 한다.
- 범위: 보호자/기관 알림 발송은 이 태스크에 포함하지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CT-004, DB-005, SEC-002, SEC-005
- Blocks: CMD-012, FE-006, TEST-008
