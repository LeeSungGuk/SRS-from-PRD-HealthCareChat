# 실버케어 SRS v0.5 단일구조 기반 개발 태스크 명세서

- Source SRS: `SRS/실버케어_SRS_v0.5_단일구조.md`
- 작성일: 2026-04-28
- 기준: SRS의 API/Data 계약을 먼저 고정한 뒤, 상태 변경(Command), 조회(Query), UI/UX, 테스트, NFR/Infra를 분리한다.
- 범위: Phase A MVP P0/P1/P2는 구현 태스크로 분해하고, Phase B/Post-MVP 항목은 추적 가능한 백로그로 분리한다.
- 원칙: SRS에 없는 보호자용 완성 앱, 기관용 완성 앱, 어르신용 앱 UI는 태스크에 포함하지 않는다.

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|---|---|---|---|---|---|
| CT-001 | Contract / Architecture | Next.js App Router 단일 풀스택 모듈 경계 정의: Web UI, Route Handler, Server Action, domain service, repository, LLM provider adapter, audit/logging | 1.5 C-TEC-001~010, 6.3 Component Diagram, 6.7 P0 | None | M |
| CT-002 | Contract / Protocol | 공통 API envelope, `schema_version`, `request_id`, 성공/오류 응답 구조 정의 | 3.3 API Overview, 6.1 Standard Error Codes, REQ-FUNC-014~015, REQ-FUNC-043 | CT-001 | M |
| CT-003 | Contract / Protocol | 공통 enum 계약 정의: `risk_level`, `emotion_tags`, `recommended_action`, `trigger_type`, `error_code` | 6.2.7, REQ-FUNC-018~019, REQ-FUNC-043 | CT-002 | L |
| CT-004 | Contract / API Spec | Phase A v1 API Request/Response DTO 정의: chat, analyze, proactive, PoC report | 6.1 API-001~004, REQ-FUNC-007~026 | CT-002, CT-003 | H |
| CT-005 | Contract / API Spec | Developer Portal/API Docs용 OpenAPI 또는 동등한 API 계약 산출물 생성 구조 정의 | 3.3, 6.1 API-000, REQ-FUNC-002, REQ-FUNC-050 | CT-004 | M |
| CT-006 | Contract / Security | Partner Console session, role, tenant context, protected route 계약 정의 | 3.2 Client Applications, 6.4, REQ-FUNC-048, REQ-FUNC-039, REQ-NF-036 | CT-001 | M |
| CT-007 | Contract / AI | Gemini provider adapter 입력/출력, 모델 선택, prompt/safety policy version, token budget metadata 계약 정의 | 1.5 C-TEC-005~006, REQ-NF-032, REQ-NF-042~043, 6.7 COST-001~003 | CT-002, CT-003 | H |
| CT-008 | Contract / Privacy | PII masking, raw transcript reference, retention category, deletion cascade 대상 계약 정의 | 6.2.4, REQ-FUNC-038, REQ-NF-020, REQ-NF-024~027 | CT-002 | H |
| DB-001 | Data / Persistence | Prisma, local SQLite, Supabase PostgreSQL datasource와 migration 실행 기준 구성 | 1.5 C-TEC-003, 6.7 P0 | CT-001 | M |
| DB-002 | Data / Tenant Auth | `tenant`, `api_key`, Partner user/session/role 최소 schema와 index 작성 | 6.2.1~6.2.5, 6.4, 6.7 P0, REQ-FUNC-001, REQ-FUNC-004~006, REQ-NF-036 | DB-001, CT-006 | H |
| DB-003 | Data / Identity | `device`, `elder_profile`, `consent_state` schema와 tenant 격리 index 작성 | 6.2.1~6.2.5, REQ-FUNC-036~037, REQ-NF-023 | DB-001, DB-002 | M |
| DB-004 | Data / Conversation | `conversation`, `message`, `memory_summary` schema와 retention/index 작성 | 6.2.1~6.2.5, REQ-FUNC-010~011, REQ-FUNC-037, REQ-NF-024~025 | DB-003, CT-008 | H |
| DB-005 | Data / Analysis | `analysis_result`, `risk_event`, `evaluation_sample` schema와 review index 작성 | 6.2.1~6.2.5, REQ-FUNC-017~019, REQ-FUNC-035, REQ-NF-011~013, REQ-NF-035 | DB-003, CT-003 | H |
| DB-006 | Data / Usage Reporting | `usage_event`, `poc_report_aggregate`, `audit_log` schema와 집계/query index 작성 | 6.2.1~6.2.5, REQ-FUNC-014, REQ-FUNC-022~026, REQ-NF-029~031 | DB-002, DB-003 | H |
| DB-007 | Data / Alert Review | `alert_event` schema와 delivery/review status index 작성 | 6.2.1~6.2.5, REQ-FUNC-028, REQ-NF-035 | DB-005 | M |
| DB-008 | Data / Operations | API Key `last_used_at`, deletion job status, quota/budget config, AI run metadata 보강 schema 작성 | REQ-FUNC-049, REQ-FUNC-059, REQ-NF-032, REQ-NF-044, 6.7 COST-004~007 | DB-002, DB-006, CT-007, CT-008 | H |
| DB-009 | Data / Phase B Backlog | `sensor_event` schema와 Phase B index를 migration backlog로 분리 관리 | 6.2.1~6.2.6, REQ-FUNC-040, REQ-FUNC-046~047 | DB-001, DB-003 | L |
| MOCK-001 | Mock / Seed | Phase A 개발용 tenant, partner user, role, API key seed 데이터 작성 | 6.7 P0, REQ-FUNC-001, REQ-FUNC-048~049 | DB-002, CT-006 | M |
| MOCK-002 | Mock / Seed | sandbox elder profile, consent state, sample utterance, 정상/안전/오류 시나리오 seed 작성 | 6.4 Sandbox Scenario Picker, REQ-FUNC-003, REQ-FUNC-053 | DB-003, CT-004 | M |
| MOCK-003 | Mock / AI | Gemini adapter mock response fixture 작성: 정상 응답, 안전 응답, timeout, upstream error, prompt injection | 6.1 API-001~002, REQ-FUNC-012, REQ-FUNC-045, REQ-NF-014~015 | CT-007, MOCK-002 | H |
| MOCK-004 | Mock / Reporting | PoC report, ops log, review queue UI 개발용 usage/risk/audit/evaluation seed 작성 | REQ-FUNC-022~026, REQ-FUNC-033~035, REQ-FUNC-056~058 | DB-005, DB-006 | M |
| PLAT-001 | Platform / Foundation | Route Handler와 Server Action에서 공유할 request context, tenant context, request_id 생성 유틸 구현 | 1.5 C-TEC-002, REQ-FUNC-014, REQ-FUNC-043 | CT-002, DB-002 | M |
| PLAT-002 | Platform / Foundation | 공통 repository/data access 계층 구현 및 UI에서 repository 직접 import 금지 규칙 적용 | 1.5 C-TEC-008~009, 6.3 Component Diagram | CT-001, DB-001 | M |
| PLAT-003 | Platform / Audit | 감사 로그 생성 공통 서비스 구현 | REQ-FUNC-001, REQ-FUNC-014, REQ-FUNC-039, REQ-NF-022, REQ-NF-038 | DB-006, PLAT-001 | M |
| SEC-001 | Security / API Common | API Key hash 검증, key status 확인, `last_used_at` 갱신 command 구현 | REQ-FUNC-004, REQ-FUNC-049, 6.2 api_key | DB-002, DB-008, PLAT-001 | H |
| SEC-002 | Security / API Common | tenant ownership guard와 cross-tenant `TENANT_ACCESS_DENIED` 처리 구현 | REQ-FUNC-005, REQ-FUNC-039, REQ-NF-021 | DB-002, DB-003, SEC-001 | H |
| SEC-003 | Security / API Common | tenant/partner 단위 rate limit과 `RATE_LIMIT_EXCEEDED` 응답 구현 | REQ-FUNC-006, REQ-NF-044 | DB-008, SEC-001 | M |
| SEC-004 | Security / API Common | 표준 오류 응답 mapper 구현: INVALID_REQUEST, UNAUTHORIZED, FORBIDDEN, LLM_TIMEOUT, UPSTREAM_ERROR, SAFETY_FILTERED, INTERNAL_ERROR | 6.1 Standard Error Codes, REQ-FUNC-015, REQ-FUNC-045 | CT-002, CT-003 | M |
| SEC-005 | Security / Privacy | PII masking service 구현 및 message/log/report 저장 전 적용 | REQ-NF-016, REQ-NF-020, REQ-NF-037, 6.2.4 | CT-008, DB-004, DB-006 | H |
| CMD-001 | Command / API Key | API Key 발급 Server Action 구현: key hash 저장, 원문 key 1회 표시, audit log 기록 | REQ-FUNC-001, REQ-FUNC-049, REQ-NF-038 | SEC-001, PLAT-003 | M |
| CMD-002 | Command / API Key | API Key 폐기 Server Action 구현: status 변경, revoked_at 저장, audit log 기록 | REQ-FUNC-049, REQ-NF-038 | CMD-001 | M |
| CMD-003 | Command / Chat | `/api/v1/chat/reply` 요청 validation과 conversation/message 생성 command 구현 | API-001, REQ-FUNC-007, REQ-FUNC-014 | CT-004, DB-004, SEC-002, SEC-004 | H |
| CMD-004 | Command / Chat | memory consent 판단과 memory_summary 조회 차단/사용 로직 구현 | REQ-FUNC-010~011, REQ-FUNC-037 | CMD-003, DB-004 | H |
| CMD-005 | Command / AI | Gemini provider adapter 호출, 저비용 모델 기본 선택, token cap, retry cap, timeout/error mapping 구현 | C-TEC-005~006, REQ-NF-001, REQ-NF-042~044 | CT-007, MOCK-003, SEC-004 | H |
| CMD-006 | Command / Chat Safety | 의료/응급/자해 안전 응답 후처리와 `SAFETY_FILTERED` 거부 케이스 분리 구현 | REQ-FUNC-012, REQ-FUNC-044~045, REQ-NF-014~015 | CMD-005, CT-003 | H |
| CMD-007 | Command / Chat Analysis | chat 응답의 emotion/risk/action 분석 결과 저장, medium 이상 risk_event 승격, usage_event 저장 구현 | REQ-FUNC-008, REQ-FUNC-013~014, REQ-FUNC-026, REQ-NF-029 | CMD-003, CMD-006, DB-005, DB-006 | H |
| CMD-008 | Command / Analyze | `/api/v1/analyze/emotion` validation, 분석 실행, PII 제거된 detected_keywords, analysis_result 저장 구현 | API-002, REQ-FUNC-016~019, REQ-NF-016 | CT-004, DB-005, SEC-002, SEC-005 | H |
| CMD-009 | Command / Proactive | `/api/v1/schedule/proactive` validation과 일반 안부/확인성 발화 생성 command 구현 | API-003, REQ-FUNC-020~021 | CT-004, CMD-004, CMD-005, CMD-006 | M |
| CMD-010 | Command / Usage | billable API 호출 usage_event 기록 공통 middleware/service 구현 | REQ-FUNC-026, REQ-NF-029~031 | DB-006, PLAT-001 | M |
| CMD-011 | Command / Consent Privacy | 동의 철회/삭제 요청 cascade deletion job 생성 command 구현 | REQ-FUNC-038, REQ-FUNC-059, REQ-NF-024~027 | DB-008, CT-008, PLAT-003 | H |
| CMD-012 | Command / Review Queue | 위험/오류/timeout/신고 대상 이벤트를 review queue/evaluation_sample 후보로 생성하는 command 구현 | REQ-FUNC-035, REQ-NF-011~013, REQ-NF-035 | DB-005, CMD-007, CMD-008 | H |
| CMD-013 | Command / Alert | high/critical 또는 반복 risk_event 기준 alert_event 생성 command 구현 | REQ-FUNC-028, REQ-NF-035 | DB-007, CMD-007 | M |
| QRY-001 | Query / Developer Portal | API Key 목록 조회: 상태, 생성/폐기 시각, 마지막 사용 시각, 원문 key 재표시 금지 | REQ-FUNC-049, 6.4 API Key Manager | DB-002, DB-008, CT-006 | M |
| QRY-002 | Query / API Docs | API Docs, enum, 표준 오류 코드, cURL/TypeScript 샘플 조회 데이터 구성 | REQ-FUNC-002, REQ-FUNC-050, REQ-FUNC-055 | CT-005 | M |
| QRY-003 | Query / Sandbox | sandbox profile, sample utterance, scenario preset 조회 | REQ-FUNC-003, REQ-FUNC-053 | MOCK-002 | L |
| QRY-004 | Query / PoC Report | `/api/v1/report/poc` JSON 집계 조회 구현 | API-004, REQ-FUNC-022, REQ-FUNC-024~025 | DB-006, CMD-010, SEC-002 | H |
| QRY-005 | Query / PoC Report | `/api/v1/report/poc` CSV export 조회 구현: 원문/PII 제외, export audit log 기록 | API-004, REQ-FUNC-023, REQ-NF-041 | QRY-004, PLAT-003, SEC-005 | M |
| QRY-006 | Query / Ops Monitoring | request log, endpoint, tenant, latency, error code, safety status 필터 조회 구현 | REQ-FUNC-033, REQ-FUNC-057, REQ-NF-037 | DB-006, SEC-005, CT-006 | H |
| QRY-007 | Query / Review Queue | high/critical, SAFETY_FILTERED, LLM timeout, 신고 케이스 리뷰 큐 조회 구현 | REQ-FUNC-035, REQ-FUNC-058, REQ-NF-035 | DB-005, DB-007, CMD-012 | H |
| QRY-008 | Query / Consent Admin | elder/device 동의 상태, memory enabled, deletion job status 조회 구현 | REQ-FUNC-036, REQ-FUNC-059, REQ-NF-023 | DB-003, DB-008, CT-006 | M |
| QRY-009 | Query / Guardian Institution Data | 보호자/기관용 요약 리포트 데이터 조회 계약 구현: 원문 미노출, 공유 동의/RBAC 적용 | REQ-FUNC-027, REQ-FUNC-029~032, REQ-FUNC-039 | DB-005, SEC-002, SEC-005, QRY-004 | H |
| UX-001 | UI/UX Design | Partner Console IA와 navigation flow 상세화: Home, Key, Docs, Playground, Report, Ops, Consent | 6.4, 6.5, REQ-FUNC-048~060 | CT-006 | M |
| UX-002 | UI/UX Design | Web API Playground request builder/response viewer/snippet UX 상태 정의: loading, validation error, success, standard error | 6.4, REQ-FUNC-051~055 | CT-004, UX-001 | M |
| UX-003 | UI/UX Design | 운영/개인정보 UI 표시 정책 정의: masked evidence, prohibited display, role별 module visibility | 6.4, REQ-NF-036~038, REQ-FUNC-057~060 | CT-006, CT-008, UX-001 | M |
| FE-001 | Frontend / Console | App Router console shell, authenticated layout, tenant context, role module navigation 구현 | REQ-FUNC-048, REQ-NF-036, 6.5 | CT-006, UX-001, MOCK-001 | M |
| FE-002 | Frontend / Developer Portal | API Key Manager UI 구현: 발급, 폐기, 상태, 마지막 사용 시각, 원문 key 1회 copy | REQ-FUNC-049, 6.4 | FE-001, CMD-001, CMD-002, QRY-001 | M |
| FE-003 | Frontend / Developer Portal | API Docs UI 구현: Swagger 또는 API schema, enum, error code, cURL/TypeScript sample | REQ-FUNC-002, REQ-FUNC-050, 6.4 | FE-001, QRY-002 | M |
| FE-004 | Frontend / Playground | Web API Playground endpoint selector와 request builder 구현 | REQ-FUNC-051~052, REQ-NF-039 | FE-001, QRY-002, UX-002 | H |
| FE-005 | Frontend / Sandbox | Sandbox Scenario Picker UI 구현: 합성 profile, sample utterance, scenario preset | REQ-FUNC-003, REQ-FUNC-053 | FE-004, QRY-003 | M |
| FE-006 | Frontend / Playground | Response Viewer UI 구현: JSON, HTTP status, latency, request_id, risk/safety/error fields | REQ-FUNC-054, REQ-NF-040 | FE-004, CMD-003, CMD-008, CMD-009 | H |
| FE-007 | Frontend / Playground | 현재 request payload 기반 cURL/TypeScript snippet 생성 UI 구현 | REQ-FUNC-055 | FE-004, QRY-002 | M |
| FE-008 | Frontend / PoC Report | PoC Report Console 구현: 기간 필터, 지표 표시, CSV/JSON export | REQ-FUNC-056, REQ-NF-040~041 | FE-001, QRY-004, QRY-005 | H |
| FE-009 | Frontend / Ops Monitoring | Ops Monitoring Console 구현: 로그 필터, analysis view, alert status, review queue 이동 | REQ-FUNC-057~058, REQ-NF-037, REQ-NF-040 | FE-001, QRY-006, QRY-007, UX-003 | H |
| FE-010 | Frontend / Consent Admin | User & Consent Admin View 구현: elder/device, consent state, memory enabled, deletion job status | REQ-FUNC-059, REQ-NF-038 | FE-001, QRY-008, CMD-011, UX-003 | M |
| FE-011 | Frontend / Scope Guard | Guardian app, institution dashboard, elder app 목적 route/navigation 미생성 검증용 UI route inventory 작성 | REQ-FUNC-060, 1.2 Out-of-Scope, 6.6 | FE-001 | L |
| TEST-001 | Test / Contract | DTO와 enum schema validation 단위 테스트 작성 | CT-002~004, REQ-FUNC-018~019, REQ-FUNC-043 | CT-004 | M |
| TEST-002 | Test / API Common | 인증 실패 401, tenant 불일치 403, rate limit 429, 표준 오류 schema 테스트 작성 | REQ-FUNC-004~006, REQ-FUNC-015 | SEC-001, SEC-002, SEC-003, SEC-004 | H |
| TEST-003 | Test / API Key | API Key 발급/폐기/원문 key 1회 표시/audit log 테스트 작성 | REQ-FUNC-001, REQ-FUNC-049, REQ-NF-038 | CMD-001, CMD-002, FE-002 | H |
| TEST-004 | Test / Chat | `/api/v1/chat/reply` 필수 필드 누락 400, 성공 schema, request_id 추적 테스트 작성 | REQ-FUNC-007~008, REQ-FUNC-014 | CMD-003, CMD-007 | H |
| TEST-005 | Test / Chat Memory | memory consent true/false, memory 미사용 시 fabricated past 금지 테스트 작성 | REQ-FUNC-010~011, REQ-FUNC-037 | CMD-004 | H |
| TEST-006 | Test / Chat Safety | 의료/응급/자해 발화 200 안전 응답, prompt injection 422 SAFETY_FILTERED 테스트 작성 | REQ-FUNC-012, REQ-FUNC-044~045, REQ-NF-014~015 | CMD-006 | H |
| TEST-007 | Test / AI Quality | 쉬운 한국어 존댓말/공감 루브릭 평가 fixture와 평균 4.0/5 검증 테스트 작성 | REQ-FUNC-009, REQ-NF-017 | CMD-005, CMD-006 | H |
| TEST-008 | Test / Analyze | `/api/v1/analyze/emotion` 필수 필드, enum, PII 없는 detected_keywords 테스트 작성 | REQ-FUNC-016~019, REQ-NF-016 | CMD-008 | H |
| TEST-009 | Test / Proactive | proactive trigger validation과 의료 지시 문구 금지 테스트 작성 | REQ-FUNC-020~021 | CMD-009 | M |
| TEST-010 | Test / Report | PoC report JSON/CSV 필수 지표, active device distinct, 원문/PII 미포함 테스트 작성 | REQ-FUNC-022~026, REQ-NF-030~031, REQ-NF-041 | QRY-004, QRY-005 | H |
| TEST-011 | Test / Consent Privacy | 동의 6개 항목 저장, deletion cascade job 생성, retention target 테스트 작성 | REQ-FUNC-036, REQ-FUNC-038, REQ-NF-023~027 | CMD-011, QRY-008 | H |
| TEST-012 | Test / RBAC Privacy | 역할별 접근 제한, 보호자/기관 요약 원문 미노출, cross-tenant no data 테스트 작성 | REQ-FUNC-027, REQ-FUNC-029~032, REQ-FUNC-039 | QRY-009, SEC-002, SEC-005 | H |
| TEST-013 | Test / Ops | 운영 로그 마스킹, threshold alert payload, review queue 수집 테스트 작성 | REQ-FUNC-033~035, REQ-NF-006, REQ-NF-035 | QRY-006, QRY-007, CMD-012, CMD-013 | H |
| TEST-014 | Test / Web UI | Partner Console protected route, module visibility, API Docs, Key Manager, Playground 핵심 E2E 테스트 작성 | REQ-FUNC-048~055, REQ-NF-036, REQ-NF-039 | FE-001~FE-007 | H |
| TEST-015 | Test / Web UI | PoC Report, Ops Monitoring, User & Consent Admin UI privacy/performance E2E 테스트 작성 | REQ-FUNC-056~060, REQ-NF-037~041 | FE-008~FE-011 | H |
| TEST-016 | Test / NFR Performance | chat non-LLM p95 800ms, LLM 포함 p95 5초, UI initial load p95 2초 측정 스크립트 작성 | REQ-NF-001, REQ-NF-040 | CMD-007, FE-006, FE-008, FE-009 | H |
| TEST-017 | Test / Cost FinOps | usage event reconciliation, monthly active device replay, quota/playground limit/budget alert 테스트 작성 | REQ-NF-029~031, REQ-NF-044 | CMD-010, NFR-008 | H |
| TEST-018 | Test / Release Gate | Vercel preview promotion gate 테스트 묶음 구성: safety, privacy, performance, regression | REQ-NF-034 | TEST-001~017, NFR-009 | H |
| NFR-001 | NFR / Observability | endpoint별 latency, error rate, token usage, LLM timeout, safety activation metrics 수집 구현 | REQ-NF-001, REQ-NF-005 | PLAT-001, CMD-005, CMD-010 | H |
| NFR-002 | NFR / Operations | tenant별 오류율/지연시간 threshold와 5분 이내 운영자 알림 생성 구현 | REQ-FUNC-034, REQ-NF-006 | NFR-001, CMD-013 | H |
| NFR-003 | NFR / Availability | Vercel/Supabase 기반 health check와 PoC availability 99.0% 측정 대시보드 구성 | REQ-NF-002 | CT-001, DB-001 | M |
| NFR-004 | NFR / DR | RPO 24h/RTO 4h baseline runbook, backup/restore drill 절차 작성 | REQ-NF-003, 6.2.4 backup | DB-001, DB-006 | M |
| NFR-005 | NFR / Security | HTTPS/TLS, storage encryption, environment secret 관리 점검 태스크 수행 | REQ-NF-018~019, C-TEC-007 | DB-001, CT-001 | M |
| NFR-006 | NFR / Privacy | raw transcript 30일, memory 180일, analysis 180일, risk/report/log 1년 retention job 구현 | REQ-NF-024~027, 6.2.4 | DB-004, DB-005, DB-006, CMD-011 | H |
| NFR-007 | NFR / Vendor AI | 외부 LLM 원문 학습 비허용 설정, 국외 이전/보관/삭제 고지 체크리스트 작성 | REQ-NF-028, C-TEC-006 | CT-007 | M |
| NFR-008 | NFR / Cost Control | tenant quota, Playground quota, LLM retry cap, 월 예산 50/75/90 알림 구현 | REQ-NF-042~044, 6.7 COST-001~007 | DB-008, CMD-005, CMD-010 | H |
| NFR-009 | NFR / Deployment | Vercel Git Integration preview deployment와 production/PoC promote gate 구성 | C-TEC-007, REQ-NF-034 | TEST-001, TEST-002 | M |
| NFR-010 | NFR / Maintainability | model/prompt/safety policy/evaluation version 기록과 release checklist 구현 | REQ-NF-032, REQ-NF-033 | CT-007, DB-008, CMD-012 | M |
| NFR-011 | NFR / Developer Experience | 첫 API 성공 30분 이내 onboarding 측정 스크립트와 시나리오 작성 | REQ-NF-007, REQ-FUNC-002, REQ-FUNC-050~055 | FE-002~FE-007, TEST-014 | M |
| NFR-012 | NFR / Scalability | MVP PoC 50~200명 기준 load scenario와 Post-MVP 10,000 devices 확장 테스트 계획 분리 작성 | REQ-NF-004 | TEST-016 | M |
| PMV-001 | Phase B Backlog / Sensor | `/api/v2/sensor/ingest` DTO, validation, tenant/device/elder 연결 구현 백로그 등록 | API-005, REQ-FUNC-040, REQ-FUNC-046~047 | DB-009, CT-004, SEC-002 | M |
| PMV-002 | Phase B Backlog / Emergency Alert | `/api/v2/alert/emergency` 및 outbound webhook signature/delivery 구현 백로그 등록 | API-006~007, REQ-FUNC-041, REQ-FUNC-028 | DB-007, DB-009, CMD-013 | H |
| PMV-003 | Phase B Backlog / KPI Report | `/api/v2/report/kpi` 주간 정서, 수면 분석, 기관 성과 JSON 구현 백로그 등록 | API-008, REQ-FUNC-027, REQ-FUNC-030~032, REQ-FUNC-042 | DB-009, QRY-009, QRY-004 | H |
| PMV-004 | Phase B Backlog / Governance | Phase B 보호자/기관 직접 UI가 필요할 경우 PRD, SRS, UX/UI Spec, OpenAPI, Privacy, Test Plan 변경 태스크 등록 | 1.2 Out-of-Scope, 6.6, REQ-FUNC-060 | None | M |

## 실행 순서 권장

1. CT-001~008과 DB-001~008을 먼저 완료해 SSOT를 고정한다.
2. MOCK-001~004로 프론트엔드와 API 개발을 병렬화할 수 있는 샘플 데이터를 만든다.
3. SEC/PLAT 공통 기반을 완료한 뒤 CMD/QRY 태스크를 API 단위로 구현한다.
4. UX 태스크를 먼저 잠그고 FE 태스크를 구현한다. 보호자/기관/어르신용 완성 앱 UI는 만들지 않는다.
5. TEST 태스크는 대응 기능 태스크와 같은 스프린트에 배치한다. AC는 수동 체크리스트가 아니라 자동화 테스트로 검증한다.
6. NFR 태스크는 P0/P1 완료 직후 붙이는 후행 작업이 아니라, API/Console 구현과 병렬로 계측·보안·비용 통제를 심는다.

## MVP 커버리지 검토

| 커버리지 항목 | 반영 태스크 | 결과 |
|---|---|---|
| API/Data 계약 우선 고정 | CT-001~008, DB-001~009 | 구현 에이전트가 임의 DTO와 DB 구조를 상상하지 않도록 기준점을 제공한다. |
| B2B 개발자 첫 API 성공 경험 | CT-005, CMD-001~002, QRY-001~003, FE-002~007, NFR-011 | API Key, Docs, Playground, Sandbox, snippet이 P0 흐름으로 유지된다. |
| 시니어 대화 API 핵심 가치 | CMD-003~007, TEST-004~007, NFR-001, NFR-008 | chat/reply, 안전 응답, 위험 태깅, usage 기록이 P0 핵심으로 유지된다. |
| PoC 성과 판단 | CMD-010, QRY-004~005, FE-008, TEST-010 | 활성 디바이스, 대화 수, p95 latency, 오류율, 위험 이벤트 수가 P1로 산출된다. |
| 운영/품질/개인정보 통제 | SEC-001~005, CMD-011~013, QRY-006~008, FE-009~010, NFR-006~010 | tenant 격리, RBAC, audit, masking, retention, review queue가 P0/P1에 포함된다. |
| Phase B 추적성 | DB-009, PMV-001~004 | sensor/emergency/KPI 확장은 MVP를 막지 않는 Post-MVP 백로그로 유지된다. |
