# GitHub Issue 상세 태스크 목록

이 폴더는 `TASKS/실버케어_SRS_v0.5_단일구조_TASKS.md`에서 추출한 태스크를 GitHub Issue 단위로 분리한 상세 명세입니다.

## 사용 기준

- 각 파일은 하나의 GitHub Issue로 복사 또는 import할 수 있도록 YAML frontmatter를 한 번만 포함한다.
- 자동 추출 기준은 `파일 1개 = GitHub Issue 1개`이다.
- 품질 검수 및 보강 기록은 `QUALITY_REVIEW.md`를 참조한다.

## 전체 Issue Index

| Issue File | Source Task | Epic | Summary | Blocks |
|---|---|---|---|---|
| `CT-001_nextjs_module_boundary.md` | CT-001 | Contract / Architecture | Next.js App Router 단일 풀스택 모듈 경계 정의: Web UI, Route Handler, Server Action, domain service, repository, LLM provider adapter, audit/logging | CT-002, CT-006, DB-001, PLAT-002, NFR-003, NFR-005 |
| `CT-002_api_envelope_error_contract.md` | CT-002 | Contract / Protocol | 공통 API envelope, `schema_version`, `request_id`, 성공/오류 응답 구조 정의 | CT-003, CT-004, CT-007, CT-008, PLAT-001, SEC-004 |
| `CT-003_common_enum_contract.md` | CT-003 | Contract / Protocol | 공통 enum 계약 정의: `risk_level`, `emotion_tags`, `recommended_action`, `trigger_type`, `error_code` | CT-004, CT-007, DB-005, SEC-004, CMD-006 |
| `CT-004_phase_a_v1_api_dto_contract.md` | CT-004 | Contract / API Spec | Phase A v1 API Request/Response DTO 정의: chat, analyze, proactive, PoC report | CT-005, MOCK-002, CMD-003, CMD-008, CMD-009, UX-002, TEST-001, PMV-001 |
| `CT-005_developer_portal_api_docs_contract.md` | CT-005 | Contract / API Spec | Developer Portal/API Docs용 OpenAPI 또는 동등한 API 계약 산출물 생성 구조 정의 | QRY-002 |
| `CT-006_partner_console_security_contract.md` | CT-006 | Contract / Security | Partner Console session, role, tenant context, protected route 계약 정의 | DB-002, MOCK-001, QRY-001, QRY-006, QRY-008, UX-001, UX-003, FE-001 |
| `CT-007_gemini_provider_adapter_contract.md` | CT-007 | Contract / AI | Gemini provider adapter 입력/출력, 모델 선택, prompt/safety policy version, token budget metadata 계약 정의 | DB-008, MOCK-003, CMD-005, NFR-007, NFR-010 |
| `CT-008_privacy_retention_deletion_contract.md` | CT-008 | Contract / Privacy | PII masking, raw transcript reference, retention category, deletion cascade 대상 계약 정의 | DB-004, DB-008, SEC-005, CMD-011, UX-003 |
| `DB-001_prisma_sqlite_supabase_persistence.md` | DB-001 | Data / Persistence | Prisma, local SQLite, Supabase PostgreSQL datasource와 migration 실행 기준 구성 | DB-002, DB-003, DB-009, PLAT-002, NFR-003, NFR-004, NFR-005 |
| `DB-002_tenant_api_key_partner_user_schema.md` | DB-002 | Data / Tenant Auth | `tenant`, `api_key`, Partner user/session/role 최소 schema와 index 작성 | DB-003, DB-006, DB-008, MOCK-001, PLAT-001, SEC-001, SEC-002, QRY-001 |
| `DB-003_data_identity.md` | DB-003 | Data / Identity | `device`, `elder_profile`, `consent_state` schema와 tenant 격리 index 작성 | DB-004, DB-005, DB-006, DB-009, MOCK-002, SEC-002, QRY-008 |
| `DB-004_data_conversation.md` | DB-004 | Data / Conversation | `conversation`, `message`, `memory_summary` schema와 retention/index 작성 | SEC-005, CMD-003, CMD-004, NFR-006 |
| `DB-005_data_analysis.md` | DB-005 | Data / Analysis | `analysis_result`, `risk_event`, `evaluation_sample` schema와 review index 작성 | DB-007, MOCK-004, CMD-007, CMD-008, CMD-012, QRY-007, QRY-009, NFR-006 |
| `DB-006_data_usage_reporting.md` | DB-006 | Data / Usage Reporting | `usage_event`, `poc_report_aggregate`, `audit_log` schema와 집계/query index 작성 | DB-008, MOCK-004, PLAT-003, SEC-005, CMD-007, CMD-010, QRY-004, QRY-006, NFR-004, NFR-006 |
| `DB-007_data_alert_review.md` | DB-007 | Data / Alert Review | `alert_event` schema와 delivery/review status index 작성 | CMD-013, QRY-007, PMV-002 |
| `DB-008_data_operations.md` | DB-008 | Data / Operations | API Key `last_used_at`, deletion job status, quota/budget config, AI run metadata 보강 schema 작성 | SEC-001, SEC-003, CMD-011, QRY-001, QRY-008, NFR-008, NFR-010 |
| `DB-009_data_phase_b_backlog.md` | DB-009 | Data / Phase B Backlog | `sensor_event` schema와 Phase B index를 migration backlog로 분리 관리 | PMV-001, PMV-002, PMV-003 |
| `MOCK-001_mock_seed.md` | MOCK-001 | Mock / Seed | Phase A 개발용 tenant, partner user, role, API key seed 데이터 작성 | FE-001 |
| `MOCK-002_mock_seed.md` | MOCK-002 | Mock / Seed | sandbox elder profile, consent state, sample utterance, 정상/안전/오류 시나리오 seed 작성 | MOCK-003, QRY-003 |
| `MOCK-003_mock_ai.md` | MOCK-003 | Mock / AI | Gemini adapter mock response fixture 작성: 정상 응답, 안전 응답, timeout, upstream error, prompt injection | CMD-005 |
| `MOCK-004_mock_reporting.md` | MOCK-004 | Mock / Reporting | PoC report, ops log, review queue UI 개발용 usage/risk/audit/evaluation seed 작성 | None |
| `PLAT-001_platform_foundation.md` | PLAT-001 | Platform / Foundation | Route Handler와 Server Action에서 공유할 request context, tenant context, request_id 생성 유틸 구현 | PLAT-003, SEC-001, CMD-010, NFR-001 |
| `PLAT-002_platform_foundation.md` | PLAT-002 | Platform / Foundation | 공통 repository/data access 계층 구현 및 UI에서 repository 직접 import 금지 규칙 적용 | None |
| `PLAT-003_platform_audit.md` | PLAT-003 | Platform / Audit | 감사 로그 생성 공통 서비스 구현 | CMD-001, CMD-011, QRY-005 |
| `SEC-001_security_api_common.md` | SEC-001 | Security / API Common | API Key hash 검증, key status 확인, `last_used_at` 갱신 command 구현 | SEC-002, SEC-003, CMD-001, TEST-002 |
| `SEC-002_security_api_common.md` | SEC-002 | Security / API Common | tenant ownership guard와 cross-tenant `TENANT_ACCESS_DENIED` 처리 구현 | CMD-003, CMD-008, QRY-004, QRY-009, TEST-002, TEST-012, PMV-001 |
| `SEC-003_security_api_common.md` | SEC-003 | Security / API Common | tenant/partner 단위 rate limit과 `RATE_LIMIT_EXCEEDED` 응답 구현 | TEST-002 |
| `SEC-004_security_api_common.md` | SEC-004 | Security / API Common | 표준 오류 응답 mapper 구현: INVALID_REQUEST, UNAUTHORIZED, FORBIDDEN, LLM_TIMEOUT, UPSTREAM_ERROR, SAFETY_FILTERED, INTERNAL_ERROR | CMD-003, CMD-005, TEST-002 |
| `SEC-005_security_privacy.md` | SEC-005 | Security / Privacy | PII masking service 구현 및 message/log/report 저장 전 적용 | CMD-008, QRY-005, QRY-006, QRY-009, TEST-012 |
| `CMD-001_command_api_key.md` | CMD-001 | Command / API Key | API Key 발급 Server Action 구현: key hash 저장, 원문 key 1회 표시, audit log 기록 | CMD-002, FE-002, TEST-003 |
| `CMD-002_command_api_key.md` | CMD-002 | Command / API Key | API Key 폐기 Server Action 구현: status 변경, revoked_at 저장, audit log 기록 | FE-002, TEST-003 |
| `CMD-003_command_chat.md` | CMD-003 | Command / Chat | `/api/v1/chat/reply` 요청 validation과 conversation/message 생성 command 구현 | CMD-004, CMD-007, FE-006, TEST-004 |
| `CMD-004_command_chat.md` | CMD-004 | Command / Chat | memory consent 판단과 memory_summary 조회 차단/사용 로직 구현 | CMD-009, TEST-005 |
| `CMD-005_command_ai.md` | CMD-005 | Command / AI | Gemini provider adapter 호출, 저비용 모델 기본 선택, token cap, retry cap, timeout/error mapping 구현 | CMD-006, CMD-009, TEST-007, NFR-001, NFR-008 |
| `CMD-006_command_chat_safety.md` | CMD-006 | Command / Chat Safety | 의료/응급/자해 안전 응답 후처리와 `SAFETY_FILTERED` 거부 케이스 분리 구현 | CMD-007, CMD-009, TEST-006, TEST-007 |
| `CMD-007_command_chat_analysis.md` | CMD-007 | Command / Chat Analysis | chat 응답의 emotion/risk/action 분석 결과 저장, medium 이상 risk_event 승격, usage_event 저장 구현 | CMD-012, CMD-013, TEST-004, TEST-016 |
| `CMD-008_command_analyze.md` | CMD-008 | Command / Analyze | `/api/v1/analyze/emotion` validation, 분석 실행, PII 제거된 detected_keywords, analysis_result 저장 구현 | CMD-012, FE-006, TEST-008 |
| `CMD-009_command_proactive.md` | CMD-009 | Command / Proactive | `/api/v1/schedule/proactive` validation과 일반 안부/확인성 발화 생성 command 구현 | FE-006, TEST-009 |
| `CMD-010_command_usage.md` | CMD-010 | Command / Usage | billable API 호출 usage_event 기록 공통 middleware/service 구현 | QRY-004, TEST-017, NFR-001, NFR-008 |
| `CMD-011_command_consent_privacy.md` | CMD-011 | Command / Consent Privacy | 동의 철회/삭제 요청 cascade deletion job 생성 command 구현 | FE-010, TEST-011, NFR-006 |
| `CMD-012_command_review_queue.md` | CMD-012 | Command / Review Queue | 위험/오류/timeout/신고 대상 이벤트를 review queue/evaluation_sample 후보로 생성하는 command 구현 | QRY-007, TEST-013, NFR-010 |
| `CMD-013_command_alert.md` | CMD-013 | Command / Alert | high/critical 또는 반복 risk_event 기준 alert_event 생성 command 구현 | TEST-013, NFR-002, PMV-002 |
| `QRY-001_query_developer_portal.md` | QRY-001 | Query / Developer Portal | API Key 목록 조회: 상태, 생성/폐기 시각, 마지막 사용 시각, 원문 key 재표시 금지 | FE-002 |
| `QRY-002_query_api_docs.md` | QRY-002 | Query / API Docs | API Docs, enum, 표준 오류 코드, cURL/TypeScript 샘플 조회 데이터 구성 | FE-003, FE-004, FE-007 |
| `QRY-003_query_sandbox.md` | QRY-003 | Query / Sandbox | sandbox profile, sample utterance, scenario preset 조회 | FE-005 |
| `QRY-004_query_poc_report.md` | QRY-004 | Query / PoC Report | `/api/v1/report/poc` JSON 집계 조회 구현 | QRY-005, QRY-009, FE-008, TEST-010, PMV-003 |
| `QRY-005_query_poc_report.md` | QRY-005 | Query / PoC Report | `/api/v1/report/poc` CSV export 조회 구현: 원문/PII 제외, export audit log 기록 | FE-008, TEST-010 |
| `QRY-006_query_ops_monitoring.md` | QRY-006 | Query / Ops Monitoring | request log, endpoint, tenant, latency, error code, safety status 필터 조회 구현 | FE-009, TEST-013 |
| `QRY-007_query_review_queue.md` | QRY-007 | Query / Review Queue | high/critical, SAFETY_FILTERED, LLM timeout, 신고 케이스 리뷰 큐 조회 구현 | FE-009, TEST-013 |
| `QRY-008_query_consent_admin.md` | QRY-008 | Query / Consent Admin | elder/device 동의 상태, memory enabled, deletion job status 조회 구현 | FE-010, TEST-011 |
| `QRY-009_query_guardian_institution_data.md` | QRY-009 | Query / Guardian Institution Data | 보호자/기관용 요약 리포트 데이터 조회 계약 구현: 원문 미노출, 공유 동의/RBAC 적용 | TEST-012, PMV-003 |
| `UX-001_ui_ux_design.md` | UX-001 | UI/UX Design | Partner Console IA와 navigation flow 상세화: Home, Key, Docs, Playground, Report, Ops, Consent | UX-002, UX-003, FE-001 |
| `UX-002_ui_ux_design.md` | UX-002 | UI/UX Design | Web API Playground request builder/response viewer/snippet UX 상태 정의: loading, validation error, success, standard error | FE-004 |
| `UX-003_ui_ux_design.md` | UX-003 | UI/UX Design | 운영/개인정보 UI 표시 정책 정의: masked evidence, prohibited display, role별 module visibility | FE-009, FE-010 |
| `FE-001_frontend_console.md` | FE-001 | Frontend / Console | App Router console shell, authenticated layout, tenant context, role module navigation 구현 | FE-002, FE-003, FE-004, FE-008, FE-009, FE-010, FE-011, TEST-014 |
| `FE-002_frontend_developer_portal.md` | FE-002 | Frontend / Developer Portal | API Key Manager UI 구현: 발급, 폐기, 상태, 마지막 사용 시각, 원문 key 1회 copy | TEST-003, TEST-014, NFR-011 |
| `FE-003_frontend_developer_portal.md` | FE-003 | Frontend / Developer Portal | API Docs UI 구현: Swagger 또는 API schema, enum, error code, cURL/TypeScript sample | TEST-014, NFR-011 |
| `FE-004_frontend_playground.md` | FE-004 | Frontend / Playground | Web API Playground endpoint selector와 request builder 구현 | FE-005, FE-006, FE-007, TEST-014, NFR-011 |
| `FE-005_frontend_sandbox.md` | FE-005 | Frontend / Sandbox | Sandbox Scenario Picker UI 구현: 합성 profile, sample utterance, scenario preset | TEST-014, NFR-011 |
| `FE-006_frontend_playground.md` | FE-006 | Frontend / Playground | Response Viewer UI 구현: JSON, HTTP status, latency, request_id, risk/safety/error fields | TEST-014, TEST-016, NFR-011 |
| `FE-007_frontend_playground.md` | FE-007 | Frontend / Playground | 현재 request payload 기반 cURL/TypeScript snippet 생성 UI 구현 | TEST-014, NFR-011 |
| `FE-008_frontend_poc_report.md` | FE-008 | Frontend / PoC Report | PoC Report Console 구현: 기간 필터, 지표 표시, CSV/JSON export | TEST-015, TEST-016 |
| `FE-009_frontend_ops_monitoring.md` | FE-009 | Frontend / Ops Monitoring | Ops Monitoring Console 구현: 로그 필터, analysis view, alert status, review queue 이동 | TEST-015, TEST-016 |
| `FE-010_frontend_consent_admin.md` | FE-010 | Frontend / Consent Admin | User & Consent Admin View 구현: elder/device, consent state, memory enabled, deletion job status | TEST-015 |
| `FE-011_frontend_scope_guard.md` | FE-011 | Frontend / Scope Guard | Guardian app, institution dashboard, elder app 목적 route/navigation 미생성 검증용 UI route inventory 작성 | TEST-015 |
| `TEST-001_test_contract.md` | TEST-001 | Test / Contract | DTO와 enum schema validation 단위 테스트 작성 | TEST-018, NFR-009 |
| `TEST-002_test_api_common.md` | TEST-002 | Test / API Common | 인증 실패 401, tenant 불일치 403, rate limit 429, 표준 오류 schema 테스트 작성 | TEST-018, NFR-009 |
| `TEST-003_test_api_key.md` | TEST-003 | Test / API Key | API Key 발급/폐기/원문 key 1회 표시/audit log 테스트 작성 | TEST-018 |
| `TEST-004_test_chat.md` | TEST-004 | Test / Chat | `/api/v1/chat/reply` 필수 필드 누락 400, 성공 schema, request_id 추적 테스트 작성 | TEST-018 |
| `TEST-005_test_chat_memory.md` | TEST-005 | Test / Chat Memory | memory consent true/false, memory 미사용 시 fabricated past 금지 테스트 작성 | TEST-018 |
| `TEST-006_test_chat_safety.md` | TEST-006 | Test / Chat Safety | 의료/응급/자해 발화 200 안전 응답, prompt injection 422 SAFETY_FILTERED 테스트 작성 | TEST-018 |
| `TEST-007_test_ai_quality.md` | TEST-007 | Test / AI Quality | 쉬운 한국어 존댓말/공감 루브릭 평가 fixture와 평균 4.0/5 검증 테스트 작성 | TEST-018 |
| `TEST-008_test_analyze.md` | TEST-008 | Test / Analyze | `/api/v1/analyze/emotion` 필수 필드, enum, PII 없는 detected_keywords 테스트 작성 | TEST-018 |
| `TEST-009_test_proactive.md` | TEST-009 | Test / Proactive | proactive trigger validation과 의료 지시 문구 금지 테스트 작성 | TEST-018 |
| `TEST-010_test_report.md` | TEST-010 | Test / Report | PoC report JSON/CSV 필수 지표, active device distinct, 원문/PII 미포함 테스트 작성 | TEST-018 |
| `TEST-011_test_consent_privacy.md` | TEST-011 | Test / Consent Privacy | 동의 6개 항목 저장, deletion cascade job 생성, retention target 테스트 작성 | TEST-018 |
| `TEST-012_test_rbac_privacy.md` | TEST-012 | Test / RBAC Privacy | 역할별 접근 제한, 보호자/기관 요약 원문 미노출, cross-tenant no data 테스트 작성 | TEST-018 |
| `TEST-013_test_ops.md` | TEST-013 | Test / Ops | 운영 로그 마스킹, threshold alert payload, review queue 수집 테스트 작성 | TEST-018 |
| `TEST-014_test_web_ui.md` | TEST-014 | Test / Web UI | Partner Console protected route, module visibility, API Docs, Key Manager, Playground 핵심 E2E 테스트 작성 | TEST-018, NFR-011 |
| `TEST-015_test_web_ui.md` | TEST-015 | Test / Web UI | PoC Report, Ops Monitoring, User & Consent Admin UI privacy/performance E2E 테스트 작성 | TEST-018 |
| `TEST-016_test_nfr_performance.md` | TEST-016 | Test / NFR Performance | chat non-LLM p95 800ms, LLM 포함 p95 5초, UI initial load p95 2초 측정 스크립트 작성 | TEST-018, NFR-012 |
| `TEST-017_test_cost_finops.md` | TEST-017 | Test / Cost FinOps | usage event reconciliation, monthly active device replay, quota/playground limit/budget alert 테스트 작성 | TEST-018 |
| `TEST-018_test_release_gate.md` | TEST-018 | Test / Release Gate | Vercel preview promotion gate 테스트 묶음 구성: safety, privacy, performance, regression | None |
| `NFR-001_nfr_observability.md` | NFR-001 | NFR / Observability | endpoint별 latency, error rate, token usage, LLM timeout, safety activation metrics 수집 구현 | NFR-002 |
| `NFR-002_nfr_operations.md` | NFR-002 | NFR / Operations | tenant별 오류율/지연시간 threshold와 5분 이내 운영자 알림 생성 구현 | None |
| `NFR-003_nfr_availability.md` | NFR-003 | NFR / Availability | Vercel/Supabase 기반 health check와 PoC availability 99.0% 측정 대시보드 구성 | None |
| `NFR-004_nfr_dr.md` | NFR-004 | NFR / DR | RPO 24h/RTO 4h baseline runbook, backup/restore drill 절차 작성 | None |
| `NFR-005_nfr_security.md` | NFR-005 | NFR / Security | HTTPS/TLS, storage encryption, environment secret 관리 점검 태스크 수행 | None |
| `NFR-006_nfr_privacy.md` | NFR-006 | NFR / Privacy | raw transcript 30일, memory 180일, analysis 180일, risk/report/log 1년 retention job 구현 | None |
| `NFR-007_nfr_vendor_ai.md` | NFR-007 | NFR / Vendor AI | 외부 LLM 원문 학습 비허용 설정, 국외 이전/보관/삭제 고지 체크리스트 작성 | None |
| `NFR-008_nfr_cost_control.md` | NFR-008 | NFR / Cost Control | tenant quota, Playground quota, LLM retry cap, 월 예산 50/75/90 알림 구현 | TEST-017 |
| `NFR-009_nfr_deployment.md` | NFR-009 | NFR / Deployment | Vercel Git Integration preview deployment와 production/PoC promote gate 구성 | TEST-018 |
| `NFR-010_nfr_maintainability.md` | NFR-010 | NFR / Maintainability | model/prompt/safety policy/evaluation version 기록과 release checklist 구현 | None |
| `NFR-011_nfr_developer_experience.md` | NFR-011 | NFR / Developer Experience | 첫 API 성공 30분 이내 onboarding 측정 스크립트와 시나리오 작성 | None |
| `NFR-012_nfr_scalability.md` | NFR-012 | NFR / Scalability | MVP PoC 50~200명 기준 load scenario와 Post-MVP 10,000 devices 확장 테스트 계획 분리 작성 | None |
| `PMV-001_phase_b_backlog_sensor.md` | PMV-001 | Phase B Backlog / Sensor | `/api/v2/sensor/ingest` DTO, validation, tenant/device/elder 연결 구현 백로그 등록 | None |
| `PMV-002_phase_b_backlog_emergency_alert.md` | PMV-002 | Phase B Backlog / Emergency Alert | `/api/v2/alert/emergency` 및 outbound webhook signature/delivery 구현 백로그 등록 | None |
| `PMV-003_phase_b_backlog_kpi_report.md` | PMV-003 | Phase B Backlog / KPI Report | `/api/v2/report/kpi` 주간 정서, 수면 분석, 기관 성과 JSON 구현 백로그 등록 | None |
| `PMV-004_phase_b_backlog_governance.md` | PMV-004 | Phase B Backlog / Governance | Phase B 보호자/기관 직접 UI가 필요할 경우 PRD, SRS, UX/UI Spec, OpenAPI, Privacy, Test Plan 변경 태스크 등록 | None |
