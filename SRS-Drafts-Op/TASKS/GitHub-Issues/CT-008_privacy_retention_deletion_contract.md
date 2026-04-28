---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CT-008: 개인정보 마스킹·보존·전파 삭제 계약 정의"
labels: 'feature, security, privacy, backend, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CT-008] PII masking, raw transcript reference, retention category, deletion cascade 대상 계약 정의
- 목적: 대화 원문, 요약 메모리, 분석 결과, 리포트, 평가셋, 로그, 백업에 대한 저장·마스킹·보존·삭제 기준을 구현 전 계약으로 고정한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#624-data-retention-and-deletion-matrix`](../../SRS/실버케어_SRS_v0.5_단일구조.md#624-data-retention-and-deletion-matrix)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#consent-withdrawal-and-cascade-deletion`](../../SRS/실버케어_SRS_v0.5_단일구조.md#consent-withdrawal-and-cascade-deletion)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] PII masking 대상 범위를 정의한다: 실명, 주민등록번호, 상세 주소, 직접 연락처, 민감 원문.
- [ ] `message.text_masked`와 `message.text_raw_ref` 사용 규칙을 정의한다.
- [ ] raw transcript 보존 기준을 정의한다: 기본 30일 이내 또는 요약/리포트 생성 후 최소 기간.
- [ ] `memory_summary` 보존 기준을 정의한다: 동의 유지 중 최대 180일, 동의 철회 시 삭제.
- [ ] `analysis_result`, `risk_event`, `poc_report_aggregate`, `audit_log`, `evaluation_sample` retention category를 정의한다.
- [ ] 삭제 전파 대상 목록을 정의한다: raw object, cache, search index, report source, evaluation candidate source, backup replay.
- [ ] UI 기본 표시 금지 항목을 정의한다: raw transcript, 주민등록번호, 상세 주소, 직접 식별정보.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 원문 저장 최소화
- Given: elder utterance와 AI reply가 생성됨
- When: message 저장 계약을 검토함
- Then: 기본 저장 필드는 PII 마스킹된 `text_masked`이며 raw text는 직접 저장하지 않고 `text_raw_ref`로 분리되어야 한다.

Scenario 2: 메모리 동의 철회 시 삭제 대상 식별
- Given: elder가 memory storage consent를 철회함
- When: deletion cascade contract가 실행 대상 목록을 생성함
- Then: `memory_summary`, personalization cache, 관련 search index 삭제 대상이 포함되어야 한다.

Scenario 3: 기본 UI에서 원문 미노출
- Given: 운영자가 Conversation Log 또는 Review Queue를 조회함
- When: UI data contract가 적용됨
- Then: 원문과 직접 식별정보는 기본 payload에 포함되지 않고 masked evidence만 제공되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 개인정보: 응답, 로그, 리포트에서 불필요한 원문/PII 노출률은 0건이어야 한다.
- 보존: raw transcript는 30일 이내, memory는 180일 이내, evaluation candidate는 30일 이내로 제한한다.
- 감사성: 삭제 job 조회와 민감 데이터 접근은 audit log를 생성해야 한다.
- 위탁: 외부 LLM/클라우드 제공자에게 원문 학습을 허용하지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] masking 대상과 저장 필드 계약이 정의되었는가?
- [ ] retention category와 deletion cascade 대상이 정의되었는가?
- [ ] DB-004, DB-008, CMD-011, SEC-005가 참조할 수 있는가?
- [ ] Web Console 기본 미노출 정책이 UX/FE 태스크와 연결되는가?

## :construction: Dependencies & Blockers
- Depends on: CT-002
- Blocks: DB-004, DB-008, SEC-005, CMD-011, NFR-006, TEST-011

