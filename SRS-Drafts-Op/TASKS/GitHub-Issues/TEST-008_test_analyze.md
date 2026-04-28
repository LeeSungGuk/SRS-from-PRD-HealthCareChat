---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-008: `/api/v1/analyze/emotion` 필수 필드, enum, PII 없는 detected_keywords 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-008] `/api/v1/analyze/emotion` 필수 필드, enum, PII 없는 detected_keywords 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-016~019, REQ-NF-016` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `/api/v1/analyze/emotion` 필수 필드와 타입 validation 테스트를 작성한다.
- [ ] 정상 분석 response의 `emotion_tags`, `risk_level`, `risk_reason`, `detected_keywords`, `request_id`, `schema_version`을 검증한다.
- [ ] `emotion_tags`와 `risk_level` enum 허용값 및 미지원 값 실패 테스트를 작성한다.
- [ ] `detected_keywords`와 저장 분석 결과에 직접 PII가 포함되지 않는지 scan assertion을 추가한다.
- [ ] 고위험 분석 케이스는 review queue와 risk_event 생성 여부를 CMD-012/TEST-013과 연결한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 필수 필드 누락은 400을 반환함
- Given: 분석 대상 텍스트가 누락된 request가 주어짐
- When: `/api/v1/analyze/emotion`을 호출함
- Then: HTTP 400과 `INVALID_REQUEST`가 반환되어야 한다.

Scenario 2: 분석 응답 enum이 contract를 따른다
- Given: 유효한 분석 요청이 주어짐
- When: emotion analysis를 실행함
- Then: `emotion_tags`와 `risk_level`은 정의된 enum 값만 포함해야 한다.

Scenario 3: detected_keywords에 PII가 포함되지 않음
- Given: 입력 텍스트에 이름, 전화번호, 주소가 포함됨
- When: analysis response를 생성함
- Then: `detected_keywords`에는 직접 PII가 포함되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안/개인정보: PII 노출 사고는 0건이어야 하며 fixture는 합성 데이터만 사용한다.
- 테스트 안정성: 외부 LLM 호출은 mock 또는 deterministic provider adapter를 사용한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: CMD-008
- Blocks: TEST-018
