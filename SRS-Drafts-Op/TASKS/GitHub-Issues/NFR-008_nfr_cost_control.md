---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] NFR-008: tenant quota, Playground quota, LLM retry cap, 월 예산 50/75/90 알림 구현"
labels: 'feature, nfr, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [NFR-008] tenant quota, Playground quota, LLM retry cap, 월 예산 50/75/90 알림 구현
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-NF-042~044, 6.7 COST-001~007` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] LLM 기본 모델을 `gemini-2.5-flash-lite` 또는 동등 저비용 모델로 설정하고 상위 모델 routing reason을 기록한다.
- [ ] token budget 목표를 입력 p95 `<= 1,200 tokens`, 출력 p95 `<= 350 tokens`로 측정하고 override 사유를 저장한다.
- [ ] tenant별 일/월 호출량 quota와 초과 상태를 usage event에서 재계산 가능하게 구현한다.
- [ ] Web API Playground는 사용자별 일일 실행 제한과 sandbox 기본 실행을 적용한다.
- [ ] LLM timeout/provider 오류 retry cap을 설정하고 Gemini/Vercel/Supabase 월 예산 50%, 75%, 90% 도달 알림 이벤트를 생성한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: LLM 호출마다 모델과 routing reason이 기록됨
- Given: chat reply가 LLM provider를 호출함
- When: 요청이 완료됨
- Then: 선택 모델, routing reason, input/output token usage가 usage event에 기록되어야 한다.

Scenario 2: quota 초과 요청이 제한됨
- Given: 특정 tenant 또는 Playground 사용자가 일일 quota를 초과함
- When: 추가 API 또는 Playground 실행을 요청함
- Then: 요청은 제한되고 초과 상태와 남은 quota 정보가 PII 없이 반환되어야 한다.

Scenario 3: 월 예산 단계 알림이 생성됨
- Given: Gemini, Vercel, Supabase 중 하나의 월 사용량이 예산의 50%, 75%, 90% threshold에 도달함
- When: cost monitor가 실행됨
- Then: 해당 threshold별 운영 확인 이벤트가 한 번씩 생성되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 비용: 정확한 원화 단가는 PRD에서 확정하지 않으므로 활성 디바이스, 호출량, 초과 가능성, provider 사용량 중심으로 산출한다.
- 보안/개인정보: quota와 budget event에는 원문 대화나 직접 PII를 포함하지 않는다.
- 성능: retry cap은 provider 장애 시 retry storm을 방지해야 하며 사용자 요청을 무한 대기시키지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-008, CMD-005, CMD-010
- Blocks: TEST-017
