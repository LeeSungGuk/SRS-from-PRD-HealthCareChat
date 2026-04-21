# SRS v02 추가 추출 태스크 5개 GitHub Issue 명세

대상 문서: `SRS_v02._gpt.md`  
기준 태스크 목록: `TASKS/SRS_v02_gpt_TASKS.md`  
작성일: 2026-04-21

## 기존 태스크 목록 확인 결과

기존 `TASKS/SRS_v02_gpt_TASKS.md`에는 데이터 모델, API 계약, Mock, Phase A/B 기능, 보안, 테스트, NFR, 인프라 태스크가 총 120개 표 행으로 추출되어 있다. 다만 구현 착수 시 에이전트 간 산출물 일관성을 높이기 위해 아래 5개 작업 단위를 추가로 분리하는 것이 적절하다.

| 신규 Task ID | 추가가 필요한 이유 | 관련 기존 태스크 |
|---|---|---|
| CONTRACT-011 | 인증 실패, 권한 거부, 검증 실패, AI 처리 실패 등 공통 오류 응답 계약이 독립 태스크로 분리되어 있지 않다. | CONTRACT-001~008, TEST-CONTRACT-001 |
| DEVREL-001 | 샘플 코드 표시 태스크는 있으나, 샘플 코드 콘텐츠 자체를 작성·검증하는 태스크가 분리되어 있지 않다. | PORTAL-FE-002, TEST-PORTAL-001 |
| MEM-001 | MemoryFact 조회는 있으나, 대화 턴에서 기억 후보를 추출하고 저장하는 Command 태스크가 분리되어 있지 않다. | CHAT-005, CHAT-006, PRO-002 |
| AUDIT-001 | 인증 실패 외 데이터 접근, PII 마스킹, 리포트 접근, Webhook 전송 이벤트를 append-only로 기록하는 통합 파이프라인이 독립 태스크로 약하다. | SEC-004, NFR-007, TEST-OPS-002 |
| VALDATA-001 | 100개 가상 어르신 페르소나 시뮬레이션 테스트는 있으나, 테스트 입력 데이터셋 생성 태스크가 분리되어 있지 않다. | TEST-VAL-001, REQ-FUNC-029 |

---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CONTRACT-011: 공통 API 오류 응답 및 상태 전이 계약 정의"
labels: 'feature, backend, api-contract, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [CONTRACT-011] 공통 API 오류 응답 및 상태 전이 계약 정의
- 목적: 모든 공개 API가 인증 실패, 권한 거부, 요청 검증 실패, AI 처리 실패, 저장소 실패 상황에서 일관된 JSON 오류 응답을 반환하도록 공통 계약을 고정한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`/Users/jin3137/practice/SRS-from-PRD/SRS-Drafts-Op/SRS_v02._gpt.md#33-api-overview`](../SRS_v02._gpt.md#33-api-overview)
- API 목록: [`/Users/jin3137/practice/SRS-from-PRD/SRS-Drafts-Op/SRS_v02._gpt.md#61-api-endpoint-list`](../SRS_v02._gpt.md#61-api-endpoint-list)
- 기능 요구사항: [`REQ-FUNC-027`, `REQ-FUNC-028`](../SRS_v02._gpt.md#41-functional-requirements-테이블-필수)
- 비기능 요구사항: [`REQ-NF-013`, `REQ-NF-015`, `REQ-NF-020`](../SRS_v02._gpt.md#42-non-functional-requirements-테이블-필수)
- 기존 태스크: [`CONTRACT-001`, `TEST-CONTRACT-001`](./SRS_v02_gpt_TASKS.md)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] API-001~API-007에 공통 적용할 성공/실패 JSON envelope 필드 정의
- [ ] 인증 실패, 테넌트 권한 위반, 요청 검증 실패, 리소스 없음, AI 처리 실패, 저장소 실패 오류 코드 정의
- [ ] 각 오류 유형별 HTTP status, 내부 errorCode, message, requestId, timestamp, retryable 여부 정의
- [ ] OpenAPI schema에 공통 ErrorResponse 모델 반영
- [ ] CONTRACT-002~CONTRACT-008의 응답 계약이 공통 오류 모델을 참조하도록 연결
- [ ] 계약 테스트에서 모든 보호 API가 공통 오류 응답 구조를 만족하는지 검증

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 인증 실패 오류 계약
- Given: 유효하지 않은 테넌트 자격 증명을 포함한 API 요청이 주어짐
- When: 보호된 API-001~API-006 중 하나를 호출함
- Then: 요청은 거부되고 공통 ErrorResponse JSON에 errorCode, message, requestId, timestamp가 포함된다.

Scenario 2: 교차 테넌트 접근 거부 오류 계약
- Given: Tenant A 자격 증명으로 Tenant B의 데이터에 접근하는 요청이 주어짐
- When: 테넌트 범위 검증이 수행됨
- Then: 요청은 거부되고 오류 응답은 테넌트 데이터 격리 정책 위반을 식별 가능한 내부 errorCode로 반환한다.

Scenario 3: OpenAPI 오류 응답 문서화
- Given: API-001~API-007의 OpenAPI 문서가 생성됨
- When: 각 엔드포인트의 오류 응답 스키마를 확인함
- Then: 모든 보호 API는 공통 ErrorResponse schema를 참조한다.

## :gear: Technical & Non-Functional Constraints
- API 계약은 `REQ-NF-020`에 따라 기계 판독 가능한 OpenAPI 형식으로 기술되어야 한다.
- 오류 응답에는 PII, 원문 발화, 민감 토큰, 내부 stack trace를 포함하지 않는다.
- `REQ-FUNC-027`, `REQ-FUNC-028`, `REQ-NF-013`의 인증 및 테넌트 격리 실패를 표현할 수 있어야 한다.
- 감사 로그 기록 대상 오류는 `REQ-NF-015`와 연결되어야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test) 및 통합 테스트(Integration Test)가 추가되었고 통과하는가?
- [ ] SonarQube / Linter 등의 정적 분석 도구 경고가 없는가?
- [ ] API 명세서(Swagger 등)가 최신화되었는가?

## :construction: Dependencies & Blockers
- Depends on: CONTRACT-001, CONTRACT-002, CONTRACT-003, CONTRACT-004, CONTRACT-005, CONTRACT-006, CONTRACT-007, CONTRACT-008
- Blocks: SEC-001, SEC-002, TEST-CONTRACT-001, TEST-SEC-001, TEST-SEC-002

---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] DEVREL-001: B2B 개발자용 API 샘플 연동 코드 작성 및 검증"
labels: 'feature, frontend, docs, developer-experience, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [DEVREL-001] B2B 개발자용 API 샘플 연동 코드 작성 및 검증
- 목적: B2B 파트너 개발자가 `chat/reply`, `analyze/emotion`, `schedule/proactive` API를 문서와 샘플 코드만으로 검증할 수 있도록 최소 샘플 연동 코드를 제공한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`/Users/jin3137/practice/SRS-from-PRD/SRS-Drafts-Op/SRS_v02._gpt.md#12-scope-in-scope--out-of-scope`](../SRS_v02._gpt.md#12-scope-in-scope--out-of-scope)
- 개발자 포털 요구사항: [`REQ-FUNC-007`, `REQ-FUNC-008`](../SRS_v02._gpt.md#41-functional-requirements-테이블-필수)
- API 명세: [`API-001`, `API-002`, `API-003`, `API-007`](../SRS_v02._gpt.md#61-api-endpoint-list)
- 클라이언트 애플리케이션: [`CLI-002`](../SRS_v02._gpt.md#32-client-applications)
- 기존 태스크: [`CONTRACT-008`, `PORTAL-FE-002`, `TEST-PORTAL-001`](./SRS_v02_gpt_TASKS.md)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `POST /api/v1/chat/reply` 샘플 요청/응답 코드 작성
- [ ] `POST /api/v1/analyze/emotion` 샘플 요청/응답 코드 작성
- [ ] `POST /api/v1/schedule/proactive` 샘플 요청/응답 코드 작성
- [ ] API 인증 헤더와 tenantId, deviceId, elderUserId 입력 예시 작성
- [ ] 샘플 코드가 Mock API 또는 개발 API에 대해 실행 가능한지 검증
- [ ] 개발자 포털에서 샘플 코드가 복사 가능한 형식으로 노출되도록 PORTAL-FE-002와 연결

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 대화 응답 API 샘플 코드 실행
- Given: 유효한 tenantId, deviceId, elderUserId, utteranceText 예제가 제공됨
- When: B2B 개발자가 `chat/reply` 샘플 코드를 실행함
- Then: 구조화된 JSON 응답에 replyText, emotionCode 또는 emotionScores, responseId가 포함된다.

Scenario 2: 감정 분석 API 샘플 코드 실행
- Given: 유효한 elderUserId와 conversationText 예제가 제공됨
- When: B2B 개발자가 `analyze/emotion` 샘플 코드를 실행함
- Then: emotionScores, riskKeywords, riskLevel, analysisId가 포함된 JSON 응답 예시를 확인할 수 있다.

Scenario 3: 개발자 포털 샘플 코드 표시
- Given: B2B 개발자가 개발자 포털에서 API 문서를 열었음
- When: 각 Phase A 엔드포인트의 예제 섹션을 확인함
- Then: 샘플 요청/응답 코드가 표시되고 복사 가능해야 한다.

## :gear: Technical & Non-Functional Constraints
- 샘플 코드는 SRS의 API-001~API-003 Request/Response 필드만 사용해야 한다.
- STT/TTS 처리는 `CON-001`, `CON-002`에 따라 샘플 코드에 포함하지 않는다.
- 샘플 코드에는 실제 API Key나 민감 토큰을 포함하지 않는다.
- OpenAPI 문서와 샘플 코드의 필드명이 불일치하면 작업 완료로 보지 않는다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test) 및 통합 테스트(Integration Test)가 추가되었고 통과하는가?
- [ ] SonarQube / Linter 등의 정적 분석 도구 경고가 없는가?
- [ ] API 명세서(Swagger 등)가 최신화되었는가?

## :construction: Dependencies & Blockers
- Depends on: CONTRACT-002, CONTRACT-003, CONTRACT-004, CONTRACT-008, MOCK-005
- Blocks: PORTAL-FE-002, TEST-PORTAL-001

---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] MEM-001: 대화 턴 기반 MemoryFact 추출 및 저장 Command 구현"
labels: 'feature, backend, ai, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [MEM-001] 대화 턴 기반 MemoryFact 추출 및 저장 Command 구현
- 목적: 어르신의 반복 설명을 줄이고 개인화 응답을 가능하게 하기 위해, 대화 턴에서 장기 기억 후보를 추출하여 `MemoryFact`로 저장한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`/Users/jin3137/practice/SRS-from-PRD/SRS-Drafts-Op/SRS_v02._gpt.md#62-entity--data-model-표-필수`](../SRS_v02._gpt.md#62-entity--data-model-표-필수)
- 기능 요구사항: [`REQ-FUNC-004`, `REQ-FUNC-015`](../SRS_v02._gpt.md#41-functional-requirements-테이블-필수)
- 데이터 모델: [`MemoryFact`](../SRS_v02._gpt.md#62-entity--data-model-표-필수)
- 상세 시퀀스: [`Detailed Sequence 1`, `Detailed Sequence 2`](../SRS_v02._gpt.md#63-detailed-interaction-models)
- 기존 태스크: [`DATA-003`, `CHAT-005`, `CHAT-006`, `PRO-002`, `TEST-CHAT-003`](./SRS_v02_gpt_TASKS.md)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `ConversationTurn`에서 장기 기억 후보를 추출하는 Command 인터페이스 정의
- [ ] PII 마스킹이 완료된 텍스트만 MemoryFact 저장 입력으로 허용
- [ ] `elderUserId`, `memoryTextMasked`, `sourceTurnId`를 포함한 MemoryFact 저장 로직 구현
- [ ] 동일 대화 턴에서 중복 MemoryFact가 반복 저장되지 않도록 기본 중복 방지 규칙 적용
- [ ] `chat/reply` 저장 흐름 이후 MemoryFact 추출 Command를 연결
- [ ] `schedule/proactive`의 기억 조회 로직이 저장된 MemoryFact를 사용할 수 있도록 데이터 접근 경로 검증

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 건강 관련 기억 저장
- Given: 어르신이 "허리가 아프다"는 내용을 포함한 발화를 입력함
- When: `POST /api/v1/chat/reply` 처리가 완료됨
- Then: PII가 마스킹된 MemoryFact가 elderUserId와 sourceTurnId를 포함하여 저장된다.

Scenario 2: 저장된 기억을 대화 응답에서 참조
- Given: 과거 대화에서 허리 통증 MemoryFact가 저장되어 있음
- When: 이후 `chat/reply` 또는 `schedule/proactive` 요청이 처리됨
- Then: 어르신이 반복 설명하지 않아도 관련 과거 기억이 응답 생성 컨텍스트에 포함될 수 있다.

Scenario 3: PII 포함 기억 저장 방지
- Given: 발화에 실명, 주소 등 PII가 포함되어 있음
- When: MemoryFact 저장 Command가 실행됨
- Then: 원문 PII가 아닌 마스킹된 memoryTextMasked만 저장된다.

## :gear: Technical & Non-Functional Constraints
- `REQ-NF-012`에 따라 원문 PII는 AI 파이프라인 저장 전에 마스킹 또는 비식별화되어야 한다.
- `REQ-FUNC-028`, `REQ-NF-013`에 따라 모든 MemoryFact 조회와 저장은 테넌트 경계를 준수해야 한다.
- MemoryFact는 `ConversationTurn`과 추적 가능해야 하며 `sourceTurnId`를 사용할 수 있어야 한다.
- 의료 진단/처방 추론을 MemoryFact로 저장하지 않는다. 저장 대상은 대화 문맥과 선제 발화 개인화에 필요한 사실 수준으로 제한한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test) 및 통합 테스트(Integration Test)가 추가되었고 통과하는가?
- [ ] SonarQube / Linter 등의 정적 분석 도구 경고가 없는가?
- [ ] API 명세서(Swagger 등)가 최신화되었는가?

## :construction: Dependencies & Blockers
- Depends on: DATA-003, SEC-002, SEC-005, SEC-006, CHAT-005
- Blocks: CHAT-002, PRO-002, TEST-CHAT-003, TEST-PRO-001

---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] AUDIT-001: 통합 감사 로그 이벤트 파이프라인 구현"
labels: 'feature, backend, security, observability, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [AUDIT-001] 통합 감사 로그 이벤트 파이프라인 구현
- 목적: 인증 실패, 데이터 접근, PII 마스킹, 리포트 접근, Webhook 전송 시도 등 SRS가 요구하는 감사 이벤트를 append-only 방식으로 기록한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`/Users/jin3137/practice/SRS-from-PRD/SRS-Drafts-Op/SRS_v02._gpt.md#42-non-functional-requirements-테이블-필수`](../SRS_v02._gpt.md#42-non-functional-requirements-테이블-필수)
- 비기능 요구사항: [`REQ-NF-015`, `REQ-NF-018`, `REQ-NF-023`](../SRS_v02._gpt.md#42-non-functional-requirements-테이블-필수)
- 데이터 모델: [`AuditLog`](../SRS_v02._gpt.md#62-entity--data-model-표-필수)
- 상세 시퀀스: [`Detailed Sequence 3`](../SRS_v02._gpt.md#63-detailed-interaction-models)
- 기존 태스크: [`DATA-006`, `SEC-004`, `NFR-007`, `TEST-OPS-002`](./SRS_v02_gpt_TASKS.md)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] AuditLog append-only repository 인터페이스 구현
- [ ] 인증 실패 및 권한 거부 이벤트 기록 연결
- [ ] 데이터 접근 이벤트 기록 연결
- [ ] PII 마스킹 이벤트 기록 연결
- [ ] 리포트 접근 이벤트 기록 연결
- [ ] 응급 Webhook 전송 시도 및 전송 상태 이벤트 기록 연결
- [ ] 감사 로그에 tenantId, actorId, eventType, createdAt을 포함하도록 검증

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 인증 실패 감사 로그 기록
- Given: 유효하지 않은 테넌트 자격 증명으로 보호 API 요청이 들어옴
- When: 인증 Guard가 요청을 거부함
- Then: AuditLog에 authentication 또는 authorization-denied 유형의 이벤트가 append-only로 기록된다.

Scenario 2: PII 마스킹 감사 로그 기록
- Given: 대화 텍스트에 PII가 포함되어 있음
- When: PII 마스킹 파이프라인이 실행됨
- Then: 원문 PII 없이 PII-mask 이벤트가 AuditLog에 기록된다.

Scenario 3: Webhook 전송 감사 로그 기록
- Given: 응급 위험이 감지되어 Webhook 전송이 시도됨
- When: 전송 시도가 성공 또는 실패로 종료됨
- Then: Webhook dispatch 이벤트와 전송 상태가 AuditLog 또는 관련 전송 기록에 남는다.

## :gear: Technical & Non-Functional Constraints
- AuditLog는 append-only로 저장되어야 하며 기존 이벤트를 수정 또는 삭제하지 않는다.
- 감사 로그에는 원문 발화, 주민등록번호, 주소 등 PII를 포함하지 않는다.
- `REQ-NF-013`에 따라 감사 로그 조회도 테넌트 경계를 준수해야 한다.
- `REQ-NF-018`의 운영 지표 발행과 충돌하지 않도록 감사 로그와 metric 이벤트의 책임을 구분한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test) 및 통합 테스트(Integration Test)가 추가되었고 통과하는가?
- [ ] SonarQube / Linter 등의 정적 분석 도구 경고가 없는가?
- [ ] API 명세서(Swagger 등)가 최신화되었는가?

## :construction: Dependencies & Blockers
- Depends on: DATA-006, SEC-001, SEC-002, SEC-004, SEC-005, SEC-006
- Blocks: ALERT-005, REPORT-005, NFR-007, TEST-OPS-002

---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] VALDATA-001: 100개 가상 어르신 페르소나 알파 시뮬레이션 데이터셋 작성"
labels: 'feature, test, validation, ai-quality, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [VALDATA-001] 100개 가상 어르신 페르소나 알파 시뮬레이션 데이터셋 작성
- 목적: Phase A 알파 검증에서 대화 품질, 환각, 지연 시간, 의료 가드레일을 반복 검증할 수 있도록 100개 가상 어르신 페르소나 입력 데이터셋을 준비한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`/Users/jin3137/practice/SRS-from-PRD/SRS-Drafts-Op/SRS_v02._gpt.md#appendix-validation-plan`](../SRS_v02._gpt.md#appendix-validation-plan)
- 기능 요구사항: [`REQ-FUNC-029`](../SRS_v02._gpt.md#41-functional-requirements-테이블-필수)
- 비기능 요구사항: [`REQ-NF-001`, `REQ-NF-016`, `REQ-NF-024`](../SRS_v02._gpt.md#42-non-functional-requirements-테이블-필수)
- 검증 계획: [`VAL-001`](../SRS_v02._gpt.md#appendix-validation-plan)
- 기존 태스크: [`TEST-VAL-001`, `TEST-CHAT-004`, `TEST-NFR-001`](./SRS_v02_gpt_TASKS.md)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] 100개 가상 어르신 페르소나 프로필 schema 정의
- [ ] 각 페르소나에 tenantId, deviceId, elderUserId, 대화 시나리오, 위험/비위험 발화 유형을 매핑
- [ ] 외로움, 우울 위험, 통증 호소, 일반 대화, 의료 질문 차단 케이스를 포함한 스크립트 작성
- [ ] `chat/reply`, `analyze/emotion`, `schedule/proactive` 테스트 입력으로 재사용 가능한 JSON 또는 fixture 파일 작성
- [ ] p95 대화 지연 시간과 의료 조언 차단 검증에 필요한 expected outcome 필드 정의
- [ ] TEST-VAL-001이 데이터셋을 자동 로드할 수 있도록 경로와 포맷 문서화

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 100개 페르소나 데이터셋 로드
- Given: 100개 가상 어르신 페르소나 fixture가 준비되어 있음
- When: 알파 시뮬레이션 테스트가 데이터셋을 로드함
- Then: 모든 페르소나가 elderUserId, 대화 스크립트, 기대 검증 항목을 포함한다.

Scenario 2: 의료 조언 차단 케이스 포함
- Given: 의료 진단, 처방, 치료 방법을 묻는 발화가 포함된 페르소나 시나리오가 있음
- When: TEST-VAL-001 또는 TEST-CHAT-004가 실행됨
- Then: 해당 시나리오는 의료 조언 차단 가드레일 검증 케이스로 사용된다.

Scenario 3: 감정/위험 분석 케이스 포함
- Given: 외로움, 우울 위험, 통증 호소를 나타내는 발화가 포함되어 있음
- When: `analyze/emotion` 검증 테스트가 실행됨
- Then: 기대 emotion/risk category와 실제 분석 결과를 비교할 수 있다.

## :gear: Technical & Non-Functional Constraints
- 데이터셋은 실제 개인정보를 포함하지 않는 가상 데이터여야 한다.
- STT/TTS 원본 음성 파일은 포함하지 않고 텍스트 입력만 포함한다.
- `REQ-NF-024`에 따라 100개 페르소나가 스크립트 대화 흐름을 수행할 수 있어야 한다.
- `REQ-NF-016`의 의료 안전 가드레일 검증 케이스를 반드시 포함한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test) 및 통합 테스트(Integration Test)가 추가되었고 통과하는가?
- [ ] SonarQube / Linter 등의 정적 분석 도구 경고가 없는가?
- [ ] API 명세서(Swagger 등)가 최신화되었는가?

## :construction: Dependencies & Blockers
- Depends on: MOCK-001, MOCK-002, MOCK-003, CONTRACT-002, CONTRACT-003, CONTRACT-004
- Blocks: TEST-VAL-001, TEST-VAL-003, TEST-NFR-001
