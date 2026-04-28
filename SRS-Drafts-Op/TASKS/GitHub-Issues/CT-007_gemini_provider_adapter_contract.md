---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CT-007: Gemini Provider Adapter 및 LLM 비용 메타데이터 계약 정의"
labels: 'feature, ai, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CT-007] Gemini provider adapter 입력/출력, 모델 선택, prompt/safety policy version, token budget metadata 계약 정의
- 목적: Vercel AI SDK와 Google Gemini API 호출을 Next.js 내부에서 통제하며 모델 선택, 토큰 예산, retry, 비용 추적, 안전 정책 버전을 일관되게 기록할 수 있는 adapter 계약을 정의한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#15-assumptions--constraints`](../../SRS/실버케어_SRS_v0.5_단일구조.md#15-assumptions--constraints)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#core-phase-a-chat-and-risk-tagging-sequence`](../../SRS/실버케어_SRS_v0.5_단일구조.md#core-phase-a-chat-and-risk-tagging-sequence)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] LLM adapter input contract를 정의한다: `tenant_id`, `request_id`, `utterance`, masked context, optional memory summary, safety policy version.
- [ ] LLM adapter output contract를 정의한다: `reply_text`, `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action`, `safety_policy_applied`.
- [ ] 기본 모델을 `gemini-2.5-flash-lite` 또는 동등 저비용 모델로 지정하는 모델 선택 규칙을 정의한다.
- [ ] 상위 모델 승격 조건을 정의한다: 위험/불확실 케이스 또는 운영자 승인 케이스.
- [ ] token budget metadata를 정의한다: input tokens, output tokens, truncation/summary 여부, override reason.
- [ ] timeout, retry cap, upstream error를 표준 오류 코드와 연결하는 기준을 정의한다.
- [ ] model version, prompt version, safety policy version, evaluation result 기록 규칙을 정의한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 기본 저비용 모델 선택
- Given: 일반 대화 요청이 주어짐
- When: LLM adapter가 모델을 선택함
- Then: 기본 모델은 `gemini-2.5-flash-lite` 또는 동등 저비용 모델이어야 하며 selected model과 routing reason이 기록되어야 한다.

Scenario 2: 토큰 예산 초과 처리
- Given: 입력 맥락이 기본 입력 1,200 tokens 목표를 초과함
- When: LLM adapter input contract가 구성됨
- Then: 요약 또는 truncation 여부와 policy override reason이 metadata에 기록되어야 한다.

Scenario 3: LLM timeout 표준 오류 매핑
- Given: Gemini provider 호출이 timeout 됨
- When: adapter가 오류를 반환함
- Then: `LLM_TIMEOUT`, `request_id`, `retryable=true`로 매핑 가능한 결과를 반환해야 한다.

## :gear: Technical & Non-Functional Constraints
- 비용: 기본 입력 1,200 tokens 이하, 출력 350 tokens 이하를 목표로 한다.
- 비용: 모든 LLM 호출은 selected model, routing reason, token usage, retry count를 기록해야 한다.
- 보안: 외부 LLM으로 전송되는 입력은 원문 최소화와 PII masking 원칙을 따른다.
- 유지보수성: provider 교체는 환경 변수와 SDK 표준 interface 변경만으로 가능해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] LLM adapter input/output contract가 정의되었는가?
- [ ] 모델 선택, 상위 모델 승격, token budget, retry cap 정책이 명시되었는가?
- [ ] REQ-NF-032, REQ-NF-042, REQ-NF-043을 구현 태스크가 참조할 수 있는가?
- [ ] MOCK-003과 CMD-005가 재사용할 fixture 기준이 정의되었는가?

## :construction: Dependencies & Blockers
- Depends on: CT-002, CT-003
- Blocks: DB-008, MOCK-003, CMD-005, NFR-007, NFR-010

