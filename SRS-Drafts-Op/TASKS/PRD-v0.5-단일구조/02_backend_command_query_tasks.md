# PRD v0.5 단일구조 - Backend Command / Query 태스크

- Source: `실버케어초안_PRD_v0.5_단일구조.md`
- 추출 원칙: 상태를 변경하는 Command와 단순 조회 Query를 분리한다. Next.js 단일구조에서는 Route Handlers와 Server Actions를 사용하되 domain service와 repository 경계를 유지한다.

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|---|---|---|---|---|---|
| PRD-PLAT-001 | Platform / Request Context | Route Handler/Server Action 공통 request context, tenant context, request_id 생성 구현 | PRD 4 API 공통 계약, 1-6 C-TEC-002 | PRD-CT-002, PRD-DB-002 | M |
| PRD-PLAT-002 | Platform / Repository Boundary | Prisma Client를 repository/data access 계층에 격리하고 domain service 호출 경계 구현 | PRD 1-6 C-TEC-008~009, ADR-007 | PRD-CT-001, PRD-DB-001 | M |
| PRD-PLAT-003 | Platform / Audit | 감사 로그 생성 공통 service 구현 | PRD 4 Partner Console 감사 로그, 5-2, AC-18~AC-21 | PRD-PLAT-001, PRD-DB-007 | M |
| PRD-SEC-001 | Security / API Auth | API Key hash 검증, key status 확인, last_used_at 갱신 구현 | PRD 4 API 공통 계약, AC-1, AC-3 | PRD-DB-002, PRD-PLAT-001 | H |
| PRD-SEC-002 | Security / Tenant Isolation | `tenant_id` ownership guard와 `TENANT_ACCESS_DENIED` 처리 구현 | PRD 4 API 공통 계약, 5-2 멀티테넌시, AC-16 | PRD-SEC-001, PRD-DB-003 | H |
| PRD-SEC-003 | Security / Rate Limit | tenant/partner rate limit과 `RATE_LIMIT_EXCEEDED` 응답 구현 | PRD 4 API 공통 계약, AC-3, PRD 1-5 quota | PRD-SEC-001, PRD-DB-011 | M |
| PRD-SEC-004 | Security / Error Mapper | 표준 오류 응답 mapper 구현: INVALID_REQUEST, UNAUTHORIZED, FORBIDDEN, TENANT_ACCESS_DENIED, CONSENT_REQUIRED, RATE_LIMIT_EXCEEDED, LLM_TIMEOUT, UPSTREAM_ERROR, SAFETY_FILTERED, INTERNAL_ERROR | PRD 4 표준 오류 코드, AC-3 | PRD-CT-003 | M |
| PRD-SEC-005 | Security / PII Masking | PII masking service 구현 및 저장/로그/리포트 전 적용 | PRD 5-2 데이터 비식별화, 5-3 금지 원칙, AC-18 | PRD-CT-012, PRD-DB-004, PRD-DB-007 | H |
| PRD-CMD-001 | Command / API Key | API Key 발급 Server Action 구현: 원문 1회 표시, hash 저장, audit log | PRD 3 AC-1, PRD 4 F0, Partner Console 공통 계약 | PRD-SEC-001, PRD-PLAT-003 | M |
| PRD-CMD-002 | Command / API Key | API Key 폐기 Server Action 구현: status 변경, revoked_at 저장, audit log | PRD 3 AC-1, PRD 4 F0 | PRD-CMD-001 | M |
| PRD-CMD-003 | Command / Chat | `/api/v1/chat/reply` request validation과 conversation/message 생성 구현 | PRD 4 F1, `/api/v1/chat/reply`, AC-2 | PRD-CT-005, PRD-DB-004, PRD-SEC-002, PRD-SEC-004 | H |
| PRD-CMD-004 | Command / Memory | `use_memory` 요청에 대해 서버 consent 최종 판단, memory_summary 조회/차단 구현 | PRD 3 AC-8~AC-9, PRD 4 스키마 결정 원칙, ADR-004 | PRD-CMD-003, PRD-DB-004, PRD-CT-013 | H |
| PRD-CMD-005 | Command / LLM Adapter | Vercel AI SDK + Gemini provider 호출, 저비용 모델 기본값, 상위 모델 승격 조건 구현 | PRD 1-5 LLM 비용 효율성, 1-6 C-TEC-005~006, ADR-008 | PRD-CT-011, PRD-MOCK-003 | H |
| PRD-CMD-006 | Command / Token Retry | token budget, truncation/summary, retry cap, LLM_TIMEOUT/UPSTREAM_ERROR mapping 구현 | PRD 1-5 토큰 예산/retry 제한, 5-1 LLM 비용 관측성 | PRD-CMD-005, PRD-SEC-004, PRD-DB-010 | H |
| PRD-CMD-007 | Command / Safety Policy | 의료/처방/응급/자해 발화 안전 응답 후처리 구현 | PRD 3 AC-10, PRD 4 안전 정책, 5-4 안전 게이트, ADR-003 | PRD-CMD-005, PRD-CT-004 | H |
| PRD-CMD-008 | Command / Chat Result | chat response analysis fields, `risk_event`, `usage_event`, LLM call log 저장 구현 | PRD 3 AC-2, AC-10, PRD 1-5 과금 단위, 5-1 관측성 | PRD-CMD-003, PRD-CMD-007, PRD-DB-005, PRD-DB-006, PRD-DB-010 | H |
| PRD-CMD-009 | Command / Analyze | `/api/v1/analyze/emotion` validation, analysis_result 저장, PII 제거 detected keywords 구현 | PRD 4 F2, `/api/v1/analyze/emotion`, AC-12~AC-14 | PRD-CT-006, PRD-DB-005, PRD-SEC-002, PRD-SEC-005 | H |
| PRD-CMD-010 | Command / Proactive | `/api/v1/schedule/proactive` 일반 안부/확인성 발화 생성 구현 | PRD 4 F3, `/api/v1/schedule/proactive`, AC-8~AC-9 | PRD-CT-008, PRD-CMD-004, PRD-CMD-007 | M |
| PRD-CMD-011 | Command / Usage | billable API 호출 usage_event 기록 공통 middleware/service 구현 | PRD 1-5 과금 단위, PRD 5-1 관측성, AC-7 | PRD-DB-006, PRD-PLAT-001 | M |
| PRD-CMD-012 | Command / Quota | tenant quota, Playground quota, quota exceeded 상태 기록 구현 | PRD 1-5 tenant quota/Playground 제한, AC-4 | PRD-DB-011, PRD-CMD-011 | M |
| PRD-CMD-013 | Command / Consent Deletion | 동의 철회/삭제 요청 cascade deletion job 생성 구현 | PRD 5-3 보존/삭제, AC-21 | PRD-DB-012, PRD-CT-014, PRD-PLAT-003 | H |
| PRD-CMD-014 | Command / Review Queue | high/critical, SAFETY_FILTERED, LLM timeout, 신고 케이스 review queue/evaluation sample 후보 생성 구현 | PRD 5-4 운영 리뷰 루프, AC-20 | PRD-DB-008, PRD-CMD-008, PRD-CMD-009 | H |
| PRD-CMD-015 | Command / Alert Event | 고위험 또는 반복 위험 신호 alert_event 생성 구현 | PRD 3 AC-13, PRD 5-3 자동화된 판단, PRD 4 F5 | PRD-DB-009, PRD-CMD-008 | M |
| PRD-CMD-016 | Command / Budget Alert | Gemini/Vercel/Supabase 예산 50/75/90 도달 운영 확인 이벤트 생성 구현 | PRD 1-5 예산 알림, ADR-008 | PRD-DB-011, PRD-PLAT-003 | M |
| PRD-QRY-001 | Query / API Key | API Key 목록 조회: status, created_at, revoked_at, last_used_at, 원문 key 재표시 금지 | PRD 3 AC-1, PRD 4 Partner Console API Key 보안 | PRD-DB-002, PRD-CT-009 | M |
| PRD-QRY-002 | Query / API Docs | API Docs, enum, error code, sample request/response, cURL/TypeScript snippet source 조회 구현 | PRD 3 AC-1, AC-4, PRD 4 F0 | PRD-CT-010 | M |
| PRD-QRY-003 | Query / Sandbox | sandbox elder profile, sample utterance, scenario preset 조회 구현 | PRD 3 AC-4, PRD 4 F0 | PRD-MOCK-002 | L |
| PRD-QRY-004 | Query / PoC Report | `/api/v1/report/poc` JSON 집계 조회 구현: active_devices, active_elders, conversation_count, p95_latency_ms, error_rate, risk_event_count | PRD 3 AC-5~AC-7, PRD 4 F7 | PRD-DB-006, PRD-CMD-011, PRD-SEC-002 | H |
| PRD-QRY-005 | Query / PoC Export | PoC Report CSV/JSON export 조회 구현: 원문/PII 제외, export audit log | PRD 3 AC-6, PRD 4 Partner Console Export 보안 | PRD-QRY-004, PRD-PLAT-003, PRD-SEC-005 | M |
| PRD-QRY-006 | Query / Ops Logs | request log, tenant, endpoint, latency, error code, safety status 마스킹 조회 구현 | PRD 3 AC-18, PRD 4 F8, PRD 5-2 웹 로그 마스킹 | PRD-DB-007, PRD-SEC-005, PRD-CT-009 | H |
| PRD-QRY-007 | Query / Risk Analysis | 감정/위험 분석 결과, risk event, alert status 필터 조회 구현 | PRD 4 F8, AC-18~AC-20 | PRD-DB-005, PRD-DB-009, PRD-SEC-005 | H |
| PRD-QRY-008 | Query / Review Queue | 오탐/미탐 리뷰 큐와 evaluation sample 후보 조회 구현 | PRD 3 AC-20, PRD 5-4 운영 리뷰 루프 | PRD-DB-008, PRD-CMD-014 | H |
| PRD-QRY-009 | Query / Consent Admin | elder/device, consent_state, memory_enabled, deletion job status 조회 구현 | PRD 3 AC-21, PRD 4 F8, PRD 5-3 동의 범위 | PRD-DB-003, PRD-DB-012, PRD-CT-009 | M |
| PRD-QRY-010 | Query / Guardian Summary Data | 보호자 공유 동의 기반 주요 정서 추세, 반복 주제, 위험 신호 요약, 권장 확인 액션 조회 계약 구현 | PRD 3 AC-12~AC-14, ADR-005 | PRD-DB-005, PRD-SEC-002, PRD-SEC-005 | H |
| PRD-QRY-011 | Query / Institution Summary Data | 기관 공유 동의/RBAC 기반 어르신별 대화 빈도, 위험 신호, 정서 추세, 마지막 상호작용, 우선순위 정렬 조회 구현 | PRD 3 AC-15~AC-17 | PRD-DB-005, PRD-SEC-002, PRD-QRY-010 | H |
| PRD-QRY-012 | Query / Billing Metrics | 월 활성 디바이스, API 호출량, 초과 사용 가능성, estimated cost 조회 구현 | PRD 1-5 과금 원칙, AC-7 | PRD-DB-006, PRD-DB-010, PRD-CT-016 | H |

