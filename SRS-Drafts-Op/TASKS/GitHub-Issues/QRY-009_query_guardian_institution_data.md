---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] QRY-009: 보호자/기관용 요약 리포트 데이터 조회 계약 구현: 원문 미노출, 공유 동의/RBAC 적용"
labels: 'feature, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [QRY-009] 보호자/기관용 요약 리포트 데이터 조회 계약 구현: 원문 미노출, 공유 동의/RBAC 적용
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-FUNC-027, REQ-FUNC-029~032, REQ-FUNC-039` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#41-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] 보호자용 summary query와 기관용 report list/detail query의 input contract를 분리 정의한다.
- [ ] 보호자 공유 동의 또는 기관 공유 동의가 없으면 `CONSENT_REQUIRED` 또는 no data payload를 반환한다.
- [ ] 보호자 summary 응답은 주요 정서 추세, 반복 주제, 위험 신호 요약, 권장 확인 액션만 포함하고 원문을 제외한다.
- [ ] 기관 report 응답은 소속 어르신별 대화 빈도, 최근 위험 신호, 정서 추세, 마지막 상호작용 시각을 포함한다.
- [ ] 기관 상세 조회는 소속/권한 검사 실패 시 403과 no data payload를 반환하고 위험 이벤트 정렬은 `risk_level`, 최근 발생 시각, 반복 횟수 기준을 지원한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 보호자 summary는 원문 없이 반환됨
- Given: guardian sharing consent가 있고 보호자 권한이 유효함
- When: 보호자 요약 리포트 데이터를 조회함
- Then: 정서 추세, 반복 주제, 위험 신호 요약, 권장 확인 액션이 반환되고 raw utterance와 AI reply 원문은 포함되지 않아야 한다.

Scenario 2: 기관 목록은 우선순위 정렬을 지원함
- Given: 기관 소속 어르신별 위험 이벤트가 다수 존재함
- When: 기관 report를 위험 우선순위 기준으로 조회함
- Then: risk_level, 최근 발생 시각, 반복 횟수 기준으로 정렬된 요약 목록이 반환되어야 한다.

Scenario 3: 소속/권한 실패 시 데이터가 반환되지 않음
- Given: 기관 관리자가 소속되지 않은 elder 상세를 요청함
- When: institution detail query가 실행됨
- Then: HTTP 403 또는 표준 forbidden 응답과 no data payload가 반환되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 데이터 접근: Prisma Client는 repository/data access 계층에 격리한다.
- 보안/개인정보: guardian/institution data contract는 원문 미노출, 공유 동의, RBAC를 모두 통과해야 한다.
- 범위: 보호자/기관 완성 UI는 Out-of-Scope이며 본 태스크는 데이터 조회 계약만 정의한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-005, SEC-002, SEC-005, QRY-004
- Blocks: TEST-012, PMV-003
