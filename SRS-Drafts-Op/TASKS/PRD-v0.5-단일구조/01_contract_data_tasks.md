# PRD v0.5 단일구조 - Contract / Data / Mock 태스크

- Source: `실버케어초안_PRD_v0.5_단일구조.md`
- 추출 원칙: 화면과 비즈니스 로직보다 데이터 모델, API DTO, enum, error code, mock data를 먼저 고정한다.

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|---|---|---|---|---|---|
| PRD-CT-001 | Contract / Architecture | Next.js App Router 단일 풀스택 모듈 경계 정의: Web UI, Route Handler, Server Action, domain service, repository, LLM provider adapter, audit/logging | PRD 1-6, 6, 7 ADR-007 | None | M |
| PRD-CT-002 | Contract / API Common | 공통 API envelope 정의: `schema_version`, `request_id`, success/error response shape | PRD 4 API 공통 계약, API Request/Response Schema 초안, AC-2, AC-3 | PRD-CT-001 | M |
| PRD-CT-003 | Contract / Error Protocol | 표준 오류 코드와 HTTP status, `retryable`, `retry_after_seconds` 계약 정의 | PRD 4 표준 오류 코드, AC-3 | PRD-CT-002 | M |
| PRD-CT-004 | Contract / Enum | `risk_level`, `emotion_tags`, `recommended_action`, `trigger_type` enum SSOT 정의 | PRD 4 분석 결과 및 오류 코드 표준 초안 | PRD-CT-002 | M |
| PRD-CT-005 | Contract / API DTO | `/api/v1/chat/reply` request/response DTO와 validation rule 정의 | PRD 4 F1, `/api/v1/chat/reply`, AC-2, AC-8~AC-11 | PRD-CT-002, PRD-CT-004 | H |
| PRD-CT-006 | Contract / API DTO | `/api/v1/analyze/emotion` request/response DTO와 PII 제거 규칙 정의 | PRD 4 F2, `/api/v1/analyze/emotion`, AC-12~AC-14 | PRD-CT-002, PRD-CT-004 | H |
| PRD-CT-007 | Contract / API DTO | `/api/v1/report/poc` query/JSON/CSV DTO와 집계 필드 정의 | PRD 4 F7, `/api/v1/report/poc`, AC-5~AC-7 | PRD-CT-002 | M |
| PRD-CT-008 | Contract / API DTO | `/api/v1/schedule/proactive` request/response DTO와 P2 optional scope 정의 | PRD 4 F3, `/api/v1/schedule/proactive`, AC-8~AC-9 | PRD-CT-002, PRD-CT-004 | M |
| PRD-CT-009 | Contract / Partner Console | Partner Console session, role, tenant context, sandbox/production 전환 정책 정의 | PRD 4 Partner Console 공통 계약, PRD 9 P0 질문 | PRD-CT-001 | H |
| PRD-CT-010 | Contract / API Docs | OpenAPI 호환 계약, Swagger/API Docs, cURL/TypeScript snippet 생성 기준 정의 | PRD 1-4 개발자 경험, PRD 4 F0, C-TEC-010, AC-1, AC-4 | PRD-CT-005, PRD-CT-006, PRD-CT-007, PRD-CT-008 | H |
| PRD-CT-011 | Contract / LLM Provider | Gemini/Vercel AI SDK provider adapter 계약, model routing reason, token/cost metadata 정의 | PRD 1-5 LLM 비용 효율성, 1-6 C-TEC-005~006, ADR-008 | PRD-CT-001, PRD-CT-004 | H |
| PRD-CT-012 | Contract / Privacy | PII masking, raw transcript reference, minimum collection, 원문 미노출 정책 계약 정의 | PRD 4 개인정보, 5-2, 5-3, ADR-005 | PRD-CT-002 | H |
| PRD-CT-013 | Contract / Consent | `consent_state` 6개 동의 항목과 서버 최종 판단 규칙 정의 | PRD 5-3 동의 범위, API Schema 스키마 결정 원칙, AC-8~AC-14, AC-21 | PRD-CT-012 | M |
| PRD-CT-014 | Contract / Retention | raw transcript 30일, memory 180일, analysis 180일, risk/report/log 1년, evaluation 30일 보존/삭제 계약 정의 | PRD 5-3 보존/삭제 원칙, PRD 9 P0 질문 | PRD-CT-012, PRD-CT-013 | H |
| PRD-CT-015 | Contract / Evaluation | AI 평가셋 sample schema: utterance, expected risk/emotion/action, prohibited response, labeling rationale | PRD 5-4 평가셋 구성, 운영 리뷰 루프 | PRD-CT-004, PRD-CT-012 | H |
| PRD-CT-016 | Contract / Billing | 월 활성 디바이스, 기본 제공량, 초과 사용량, usage event 산출 계약 정의 | PRD 1-5 과금 단위 정의, AC-7 | PRD-CT-002 | M |
| PRD-CT-017 | Contract / Out-of-Scope Guard | STT/TTS, 보호자/기관/어르신 완성 앱, 의료 진단, 실시간 119 연결 제외 범위 검증 규칙 정의 | PRD 1-4 Out of Scope, ADR-001, ADR-006, 10 | None | L |
| PRD-DB-001 | Data / Persistence | Prisma, local SQLite, Supabase PostgreSQL datasource와 migration 기준 구성 | PRD 1-6 C-TEC-003, MVP 구현 컷라인 P0 | PRD-CT-001 | M |
| PRD-DB-002 | Data / Tenant Auth | `tenant`, partner user/session/role, `api_key`, API key hash/status/last_used_at schema 작성 | PRD 1-4 P0 기본 모델, PRD 4 Partner Console 공통 계약, AC-1, AC-3 | PRD-DB-001, PRD-CT-009 | H |
| PRD-DB-003 | Data / Identity Consent | `device`, `elder_profile`, `consent_state` schema와 tenant index 작성 | PRD 4 핵심 데이터 객체, PRD 5-3 동의 범위, AC-8~AC-14, AC-21 | PRD-DB-002, PRD-CT-013 | H |
| PRD-DB-004 | Data / Conversation Memory | `conversation`, `message`, `memory_summary`, raw transcript ref, memory expiry schema 작성 | PRD 4 `/api/v1/chat/reply`, 핵심 데이터 객체, PRD 5-3 보존/삭제 | PRD-DB-003, PRD-CT-014 | H |
| PRD-DB-005 | Data / Analysis Risk | `analysis_result`, `risk_event`, 위험 반복 탐지용 index schema 작성 | PRD 4 F2, 핵심 데이터 객체, AC-10, AC-13, AC-17 | PRD-DB-003, PRD-CT-004 | H |
| PRD-DB-006 | Data / Reporting Usage | `usage_event`, `poc_report_aggregate`, active device/elder 집계 index schema 작성 | PRD 1-5 과금 단위, PRD 4 F7, AC-5~AC-7 | PRD-DB-002, PRD-DB-003, PRD-CT-016 | H |
| PRD-DB-007 | Data / Audit Ops | `audit_log`, sensitive action audit fields, request_id index schema 작성 | PRD 4 Partner Console 감사 로그, PRD 5-2, AC-18~AC-21 | PRD-DB-002 | M |
| PRD-DB-008 | Data / Review Evaluation | `evaluation_sample`, labeling result, review_status, expires_at schema 작성 | PRD 5-4 평가셋 구성, 운영 리뷰 루프, AC-20 | PRD-DB-005, PRD-CT-015 | H |
| PRD-DB-009 | Data / Alert Event | `alert_event`와 notification payload, delivery status, detected_at schema 작성 | PRD 3 AC-13, PRD 4 F5, 자동화된 판단과 사람의 검토 | PRD-DB-005 | M |
| PRD-DB-010 | Data / LLM Cost | LLM call log 또는 usage_event 확장: selected model, routing reason, input/output tokens, retry count, estimated cost | PRD 1-5 LLM 비용 효율성, 5-1 LLM 비용 관측성 | PRD-DB-006, PRD-CT-011 | H |
| PRD-DB-011 | Data / Quota Budget | tenant quota, Playground quota, budget alert threshold/status schema 작성 | PRD 1-5 LLM 비용 효율성, AC-4 | PRD-DB-002, PRD-DB-006 | M |
| PRD-DB-012 | Data / Deletion Job | deletion job status, cascade target, backup replay marker schema 작성 | PRD 5-3 보존/삭제 원칙, AC-21 | PRD-DB-004, PRD-DB-005, PRD-DB-006, PRD-DB-008 | H |
| PRD-DB-013 | Data / Phase B | `sensor_event` 최소 schema와 Phase B 시계열 index 백로그 정의 | PRD 4 F4, 8-2, 9 P1 질문 | PRD-DB-001, PRD-DB-003 | L |
| PRD-MOCK-001 | Mock / Seed | Phase A 개발용 tenant, partner user, role, API key seed 작성 | PRD 1-4 P0 기본 모델, AC-1 | PRD-DB-002 | M |
| PRD-MOCK-002 | Mock / Sandbox | sandbox elder profile, consent state, sample utterance, 정상/안전/오류 scenario seed 작성 | PRD 1-4 Web API Playground, PRD 4 F0, AC-4 | PRD-DB-003, PRD-CT-005, PRD-CT-006 | M |
| PRD-MOCK-003 | Mock / AI | Gemini mock fixture 작성: normal, safety, hallucination guard, timeout, upstream error, prompt injection | PRD 3 AC-2~AC-4, PRD 5-4 평가셋 구성 | PRD-CT-011, PRD-MOCK-002 | H |
| PRD-MOCK-004 | Mock / Reporting Ops | PoC report, usage_event, risk_event, audit_log, review queue UI seed 작성 | PRD 3 AC-5~AC-7, AC-18~AC-20 | PRD-DB-005, PRD-DB-006, PRD-DB-007, PRD-DB-008 | M |
| PRD-MOCK-005 | Mock / Alpha Simulation | 내부 알파 테스트용 가상 어르신 페르소나 100개와 대화 시뮬레이션 seed 작성 | PRD 8-1 내부 알파 테스트, PRD 5-4 평가셋 구성 | PRD-MOCK-002, PRD-CT-015 | H |

