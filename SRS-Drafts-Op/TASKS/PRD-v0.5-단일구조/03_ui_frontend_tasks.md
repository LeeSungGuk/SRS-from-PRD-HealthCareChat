# PRD v0.5 단일구조 - UI/UX / Frontend 태스크

- Source: `실버케어초안_PRD_v0.5_단일구조.md`
- 추출 원칙: UI/UX 설계 태스크와 Frontend 구현 태스크를 분리한다. Partner Console은 B2B 연동·운영 콘솔이며 보호자/기관/어르신용 완성 앱 UI를 포함하지 않는다.

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|---|---|---|---|---|---|
| PRD-UX-001 | UI/UX Design | Partner Console IA 정의: Home, API Key, API Docs, Playground, Sandbox, PoC Report, Ops, Consent Admin | PRD 1-4 Partner Console, PRD 4 F0/F8, PRD 9 P0 질문 | PRD-CT-009 | M |
| PRD-UX-002 | UI/UX Design | 역할별 navigation visibility와 sandbox/production environment indicator 설계 | PRD 4 Partner Console 인증/권한, Sandbox 기본값 | PRD-UX-001, PRD-CT-009 | M |
| PRD-UX-003 | UI/UX Design | Web API Playground request builder, validation, response viewer, snippet UX 상태 설계 | PRD 3 AC-4, PRD 4 F0, Web API Playground | PRD-CT-005, PRD-CT-006, PRD-CT-007, PRD-CT-008 | H |
| PRD-UX-004 | UI/UX Design | PoC Report Console 지표, 필터, export flow, empty/error/loading state 설계 | PRD 3 AC-5~AC-7, PRD 4 F7/F8 | PRD-CT-007 | M |
| PRD-UX-005 | UI/UX Design | Ops Monitoring, Conversation Log, Emotion/Risk Analysis, Review Queue 화면 흐름 설계 | PRD 3 AC-18~AC-20, PRD 4 F8 | PRD-CT-012, PRD-CT-015 | H |
| PRD-UX-006 | UI/UX Design | User/Consent Admin View 표시 필드와 direct PII prohibited display 설계 | PRD 3 AC-21, PRD 5-3 동의/역할별 접근 | PRD-CT-013, PRD-CT-014 | M |
| PRD-UX-007 | UI/UX Design | 보호자/기관/어르신 완성 앱 UI 미포함 route inventory와 scope guard 설계 | PRD 1-4 Out of Scope, ADR-006, PRD 10 | PRD-CT-017 | L |
| PRD-FE-001 | Frontend / Foundation | Tailwind CSS + shadcn/ui 기반 App Router console shell과 authenticated layout 구현 | PRD 1-6 C-TEC-004, PRD 4 Partner Console 인증/권한 | PRD-UX-001, PRD-CT-009, PRD-MOCK-001 | M |
| PRD-FE-002 | Frontend / Console Home | tenant context, user role, environment, accessible modules 표시 구현 | PRD 1-4 Partner Console, PRD 4 Partner Console 공통 계약 | PRD-FE-001, PRD-QRY-001 | M |
| PRD-FE-003 | Frontend / API Key Manager | API Key 발급/폐기, 상태, 마지막 사용 시각, 원문 key 1회 copy UI 구현 | PRD 3 AC-1, PRD 4 F0, API Key 보안 | PRD-FE-001, PRD-CMD-001, PRD-CMD-002, PRD-QRY-001 | M |
| PRD-FE-004 | Frontend / API Docs | Swagger/API Docs, enum, error code, sample request/response, cURL/TypeScript sample UI 구현 | PRD 3 AC-1, PRD 4 F0 | PRD-FE-001, PRD-QRY-002 | M |
| PRD-FE-005 | Frontend / Playground | endpoint selector와 endpoint별 request builder 구현 | PRD 3 AC-4, PRD 4 Web API Playground | PRD-FE-001, PRD-UX-003, PRD-QRY-002 | H |
| PRD-FE-006 | Frontend / Playground | 실행 전 validation error 표시와 sandbox default enforcement UI 구현 | PRD 3 AC-4, PRD 4 Partner Console Sandbox 기본값 | PRD-FE-005, PRD-CMD-012 | H |
| PRD-FE-007 | Frontend / Sandbox | sample elder profile, sample utterance, normal/safety/error scenario picker 구현 | PRD 3 AC-4, PRD 4 F0 | PRD-FE-005, PRD-QRY-003 | M |
| PRD-FE-008 | Frontend / Playground | JSON response, HTTP status, latency, request_id, risk/safety/error fields viewer 구현 | PRD 3 AC-2~AC-4, PRD 5-1 Partner Console 응답성 | PRD-FE-005, PRD-CMD-003, PRD-CMD-009, PRD-CMD-010 | H |
| PRD-FE-009 | Frontend / Playground | 현재 request payload 기준 cURL/TypeScript snippet 생성 UI 구현 | PRD 3 AC-1, AC-4, PRD 4 TypeScript 샘플 우선 | PRD-FE-005, PRD-QRY-002 | M |
| PRD-FE-010 | Frontend / PoC Report | PoC Report Console 구현: 기간 필터, active metrics, p95/error/risk 지표 표시 | PRD 3 AC-5~AC-7, PRD 4 F7/F8 | PRD-FE-001, PRD-UX-004, PRD-QRY-004 | H |
| PRD-FE-011 | Frontend / PoC Export | PoC Report CSV/JSON export action UI와 export audit trigger 연결 | PRD 3 AC-6, PRD 4 Export 보안 | PRD-FE-010, PRD-QRY-005 | M |
| PRD-FE-012 | Frontend / Ops Monitoring | Ops Monitoring Console 로그 필터 UI 구현: tenant, endpoint, request_id, latency, error code, safety status | PRD 3 AC-18~AC-19, PRD 4 F8 | PRD-FE-001, PRD-UX-005, PRD-QRY-006 | H |
| PRD-FE-013 | Frontend / Analysis View | Emotion/Risk Analysis View 구현: risk_level, emotion_tags, risk_reason, recommended_action, alert status | PRD 4 F8, PRD 5-4 운영 리뷰 루프 | PRD-FE-012, PRD-QRY-007 | H |
| PRD-FE-014 | Frontend / Review Queue | Review Queue 구현: high/critical, SAFETY_FILTERED, LLM timeout, masked evidence, labeling result | PRD 3 AC-20, PRD 5-4 운영 리뷰 루프 | PRD-FE-013, PRD-QRY-008 | H |
| PRD-FE-015 | Frontend / Consent Admin | User/Consent Admin View 구현: elder_id, device_id, consent state, memory enabled, deletion job status | PRD 3 AC-21, PRD 4 F8 | PRD-FE-001, PRD-UX-006, PRD-QRY-009 | M |
| PRD-FE-016 | Frontend / Privacy UI | 모든 로그/리포트/리뷰 UI에 기본 PII masking과 raw transcript non-display 적용 | PRD 4 Partner Console 개인정보 표시, PRD 5-2 웹 로그 마스킹 | PRD-FE-010, PRD-FE-012, PRD-FE-014, PRD-FE-015 | H |
| PRD-FE-017 | Frontend / Scope Guard | 보호자용/기관용/어르신용 완성 앱 목적 route와 navigation이 없음을 검증하는 UI route inventory 작성 | PRD 1-4 Out of Scope, ADR-006, PRD 10 | PRD-FE-001, PRD-UX-007 | L |

