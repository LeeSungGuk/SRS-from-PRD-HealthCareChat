# 2026-04-28 TASKS GitHub Issues 품질 보강 변경사항

- 대상 문서:
  - `TASKS/실버케어_SRS_v0.5_단일구조_TASKS.md`
  - `TASKS/GitHub-Issues/*.md`
  - `TASKS/GitHub-Issues/QUALITY_REVIEW.md`
- 기준 문서:
  - `SRS/실버케어_SRS_v0.5_단일구조.md`
  - `TASKS/실버케어_SRS_v0.5_단일구조_TASKS.md`
- 작업일: 2026-04-28
- 목적: SRS 기반으로 생성된 GitHub Issue 상세 태스크 문서의 자동 템플릿성 문구를 제거하고, 각 태스크를 구현 가능한 수준의 실행 계획, BDD/GWT 인수 기준, 의존성 기준으로 보강

---

## 1. 전체 변경 요약

### 변경 내용

`TASKS/GitHub-Issues` 아래 개별 GitHub Issue 문서를 전수 검수하고, 품질이 부족한 파일을 도메인별로 보강했다.

### 주요 보강 방향

- 공통 템플릿 문구 제거
- 태스크별 실행 계획 구체화
- SRS Requirement ID와 구현 산출물 연결 강화
- BDD/GWT Acceptance Criteria를 테스트 가능한 형태로 재작성
- Privacy, RBAC, tenant isolation, audit log, PII masking 기준 명시
- MVP 단일구조 스택 기준 유지
- Phase B/Post-MVP 범위와 Phase A MVP 구현 범위 분리
- Release gate와 NFR 검증 기준 연결

### 핵심 판단

기존 이슈 파일은 GitHub Issue 형식은 갖추고 있었지만, 다수 파일의 `Task Breakdown`과 `Acceptance Criteria`가 prefix별 공통 문장에 머물러 있었다. 이 상태에서는 AI Agent 또는 개발자가 각 태스크의 실제 구현 경계, 실패 조건, 테스트 기준을 안정적으로 판단하기 어렵다. 따라서 각 파일을 SRS 요구사항, 데이터/API 계약, 개인정보 제약, MVP 범위 기준에 맞춰 다시 구체화했다.

---

## 2. 보강 대상

### 전체 대상

- 개별 GitHub Issue 파일: 99개
- 본문 품질 보강 대상 자동 생성 이슈: 89개
- Dependencies / Blocks 관계 보강 대상: 99개
- 품질 검수 로그: `TASKS/GitHub-Issues/QUALITY_REVIEW.md`

### 보강한 주요 Task Prefix

- `CMD-*`: 상태 변경 command 태스크
- `DB-*`: 데이터 모델 및 schema 태스크
- `FE-*`: Partner Console frontend 태스크
- `MOCK-*`: mock/seed fixture 태스크
- `NFR-*`: 비기능 요구사항 태스크
- `PLAT-*`: 플랫폼 공통 기반 태스크
- `PMV-*`: Post-MVP / Phase B backlog 태스크
- `QRY-*`: 조회/query 태스크
- `SEC-*`: 보안/API 공통 태스크
- `TEST-*`: 자동화 테스트 및 release gate 태스크
- `UX-*`: UI/UX 설계 태스크

---

## 3. 도메인별 변경 내용

### 3.1 Command 태스크 보강

`CMD-001`~`CMD-013`의 실행 계획과 GWT를 구체화했다.

주요 반영 내용:

- API Key 발급/폐기/원문 key 1회 표시
- `/api/v1/chat/reply` request validation, memory consent, Gemini adapter 연동
- 안전 응답과 `SAFETY_FILTERED` 처리 분리
- emotion/risk analysis enum 검증과 PII 제거
- proactive message의 의료 지시 금지
- usage event 생성과 월별 replay 가능성
- consent withdrawal과 deletion job cascade
- review queue 수집과 alert payload 기준

### 3.2 Data / DB 태스크 보강

`DB-003`~`DB-009`를 중심으로 field-level schema와 retention/index 기준을 보강했다.

주요 반영 내용:

- identity / consent / device / elder profile 구조
- conversation, message, summary memory 보존 기준
- analysis_result, risk_event, evaluation candidate schema
- usage_event, poc_report_aggregate, audit_log schema
- alert_event, review_status, 운영 metadata
- Phase B sensor_event는 MVP 구현이 아닌 backlog schema로 분리

### 3.3 Frontend / Mock 태스크 보강

`FE-001`~`FE-011`, `MOCK-001`~`MOCK-004`를 Partner Console MVP 범위에 맞게 보강했다.

주요 반영 내용:

- Console Home, API Key Manager, API Docs, Playground, Sandbox, Report, Ops, Consent Admin 화면별 표시 필드와 action
- API Key 원문 재표시 금지
- Playground sandbox 기본 실행, request validation, response viewer, snippet 생성
- PoC Report와 Ops 화면의 원문/PII 미노출
- 보호자/기관/어르신 완성 앱 route guard
- synthetic tenant/API key/sandbox/Gemini/reporting fixture

### 3.4 NFR 태스크 보강

`NFR-001`~`NFR-012`를 수치 기준과 운영 evidence 중심으로 재작성했다.

주요 반영 내용:

- endpoint latency, error rate, token usage, LLM timeout, safety activation metric
- tenant별 threshold breach 5분 이내 운영 알림
- PoC availability 99.0%, production target 99.9%
- RPO 24h, RTO 4h baseline runbook
- TLS/HTTPS, at-rest encryption, secret 관리
- raw transcript 30일, memory 180일, analysis 180일, risk/report/log 1년 retention
- vendor AI 원문 학습 비허용 및 disclosure checklist
- tenant quota, Playground quota, retry cap, 월 예산 50/75/90 알림
- Vercel preview promotion gate
- model/prompt/safety/evaluation version release record
- 첫 API 성공 30분 이내 onboarding
- MVP PoC 50~200명 load scenario와 Post-MVP 10,000 devices 계획 분리

### 3.5 Platform / Query / Security 태스크 보강

`PLAT-*`, `QRY-*`, `SEC-*`를 시스템 경계와 보안 계약 중심으로 보강했다.

주요 반영 내용:

- Route Handler / Server Action 공통 request context
- `request_id`, `tenant_id`, actor role propagation
- Prisma repository/data access 계층 격리
- audit log 공통 서비스와 sanitizer
- API Key 목록 조회 원문 key 미노출
- API Docs contract snapshot
- sandbox scenario 조회
- PoC report JSON/CSV 집계와 export audit
- Ops log, Review Queue, Consent Admin 조회 계약
- 보호자/기관용 요약 데이터 원문 미노출
- API Key hash 검증, tenant ownership guard, rate limit, 표준 오류 mapper, PII masking service

### 3.6 Test 태스크 보강

`TEST-001`~`TEST-018`을 실제 자동화 피드백 루프가 되도록 재작성했다.

주요 반영 내용:

- DTO/enum/schema validation test
- 인증 실패 401, tenant 불일치 403, rate limit 429, 표준 오류 schema test
- API Key 발급/폐기/원문 key 1회 표시/audit log test
- Chat success schema, request_id 추적, memory consent, fabricated past 금지 test
- 의료/응급/자해 안전 응답, prompt injection `SAFETY_FILTERED` test
- 쉬운 한국어 존댓말/공감 rubric 평균 4.0/5 평가
- emotion analysis enum과 PII 없는 detected keywords test
- proactive trigger validation과 의료 지시 문구 금지 test
- PoC report JSON/CSV, active device distinct, PII 미포함 test
- 동의 6개 항목, deletion cascade, retention target test
- RBAC/privacy/cross-tenant no data test
- Ops masking, threshold alert, review queue 수집 test
- Partner Console Web UI E2E
- performance, cost/FinOps, release gate test

### 3.7 UX 태스크 보강

`UX-001`~`UX-003`을 SRS 6.4/6.5 기준으로 구체화했다.

주요 반영 내용:

- Partner Console IA와 navigation flow
- Home, Key, Docs, Playground, Report, Ops, Consent module 구성
- 역할별 module visibility
- Web API Playground request builder, response viewer, snippet UX 상태
- loading, validation error, standard error, success, permission denied, quota exceeded 상태
- masked evidence, prohibited display, audit 대상 UI action
- guardian app, institution dashboard, elder app UI는 Out-of-Scope로 유지

---

## 4. 품질 검증 결과

### 검증한 항목

- 필수 GitHub Issue 섹션 존재 여부
- 자동 템플릿성 공통 문구 잔존 여부
- 이전/부적합 기술 스택 참조 잔존 여부
- TASK ID별 Dependencies / Blocks 관계 유지 여부
- SRS 단일구조 MVP 범위와의 정합성

### 검증 결과

- 개별 GitHub Issue 파일 수: 99개
- 필수 섹션 누락: 0건
- 공통 자동 템플릿 문구 잔존: 0건
- `FastAPI`, `MySQL`, `Vite`, `LangChain` 등 부적합 스택 참조: 0건
- `TBD`, `TODO` 잔존: 0건

---

## 5. 산출물

### 신규 작성

- `TASKS/GitHub-Issues/QUALITY_REVIEW.md`
- `log/2026-04-28_TASKS-GitHub-Issues-품질보강-변경사항.md`

### 주요 수정

- `TASKS/GitHub-Issues/*.md`

---

## 6. 핵심 판단

이번 보강은 기능을 새로 추가한 것이 아니라, 이미 SRS와 TASKS 문서에 정의된 요구사항을 AI Agent와 개발자가 실제로 실행 가능한 GitHub Issue 수준으로 정제한 작업이다.

특히 MVP 구현에서는 태스크가 추상적이면 AI Agent가 임의의 데이터 구조, API 응답, UI 상태, 테스트 기준을 만들어낼 위험이 크다. 이를 줄이기 위해 데이터 계약, API 계약, 상태 변경 여부, 개인정보 경계, 자동화 테스트 기준을 각 이슈 안에 명시했다.

따라서 현재 TASK 이슈 묶음은 단순 체크리스트가 아니라, Next.js App Router 단일구조 MVP를 구현하기 위한 실행 가능한 작업 지시서와 테스트 기준으로 사용할 수 있는 수준으로 정리되었다.
