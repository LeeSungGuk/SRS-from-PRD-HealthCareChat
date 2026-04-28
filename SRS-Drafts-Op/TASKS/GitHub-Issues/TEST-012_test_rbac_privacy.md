---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] TEST-012: 역할별 접근 제한, 보호자/기관 요약 원문 미노출, cross-tenant no data 테스트 작성"
labels: 'feature, test, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [TEST-012] 역할별 접근 제한, 보호자/기관 요약 원문 미노출, cross-tenant no data 테스트 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-027, REQ-FUNC-029~032, REQ-FUNC-039` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#5-traceability-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] guardian, institution manager, B2B partner, SilverCare operator 역할별 접근 허용/차단 matrix 테스트를 작성한다.
- [ ] 보호자 summary report가 공유 동의 없이는 `CONSENT_REQUIRED` 또는 no data를 반환하는 테스트를 작성한다.
- [ ] 보호자/기관용 요약 데이터에 raw utterance, AI reply 원문, 직접 PII가 없는지 검증한다.
- [ ] 기관 관리자가 소속되지 않은 elder detail을 조회할 때 403/no data를 반환하는 테스트를 작성한다.
- [ ] cross-tenant 요청은 `TENANT_ACCESS_DENIED`로 차단되는지 검증한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 보호자 summary는 원문 없이 반환됨
- Given: guardian sharing consent가 있고 보호자 권한이 유효함
- When: 보호자 summary 데이터를 조회함
- Then: 요약·추세·위험 신호·권장 액션만 반환되고 원문과 직접 PII는 없어야 한다.

Scenario 2: 기관 상세 권한 실패는 no data를 반환함
- Given: 기관 관리자가 소속되지 않은 elder detail을 요청함
- When: 기관 상세 query를 실행함
- Then: HTTP 403 또는 forbidden 오류와 no data payload가 반환되어야 한다.

Scenario 3: cross-tenant 접근은 차단됨
- Given: tenant A actor가 tenant B report 또는 elder data를 요청함
- When: RBAC/privacy test를 실행함
- Then: `TENANT_ACCESS_DENIED` 또는 no data가 반환되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안/개인정보: 역할별 접근 실패 응답은 대상 리소스 존재 여부를 과도하게 노출하지 않는다.
- 범위: 보호자/기관 완성 UI는 만들지 않고 데이터 계약과 접근 통제만 검증한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: QRY-009, SEC-002, SEC-005
- Blocks: TEST-018
