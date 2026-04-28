# PRD v0.5 단일구조 - Test / NFR / Infra 태스크

- Source: `실버케어초안_PRD_v0.5_단일구조.md`
- 추출 원칙: PRD의 AC와 NFR을 수동 체크리스트가 아니라 자동화된 테스트, 계측, 배포 게이트 태스크로 전환한다.

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|---|---|---|---|---|---|
| PRD-TEST-001 | Test / Contract | API DTO, enum, standard error schema validation unit test 작성 | PRD 4 API Request/Response Schema, 표준 오류 코드 | PRD-CT-002, PRD-CT-003, PRD-CT-004, PRD-CT-005, PRD-CT-006, PRD-CT-007, PRD-CT-008 | H |
| PRD-TEST-002 | Test / API Common | API Key 누락/invalid 401, tenant mismatch 403, rate limit 429 테스트 작성 | PRD 3 AC-3, PRD 4 API 공통 계약, PRD 5-2 | PRD-SEC-001, PRD-SEC-002, PRD-SEC-003, PRD-SEC-004 | H |
| PRD-TEST-003 | Test / API Key | API Key 발급/폐기, 원문 key 1회 표시, audit log 생성 테스트 작성 | PRD 3 AC-1, Partner Console API Key 보안 | PRD-CMD-001, PRD-CMD-002, PRD-FE-003 | H |
| PRD-TEST-004 | Test / Chat API | `/api/v1/chat/reply` 필수 필드 누락 400, 성공 schema, request_id 추적 테스트 작성 | PRD 3 AC-2~AC-3, PRD 4 F1 | PRD-CMD-003, PRD-CMD-008 | H |
| PRD-TEST-005 | Test / Memory | memory consent true/false, no memory일 때 fabricated past 금지 테스트 작성 | PRD 3 AC-8~AC-9, ADR-004 | PRD-CMD-004 | H |
| PRD-TEST-006 | Test / Safety | 의료/처방/응급/자해 발화 안전 응답 200, prompt injection SAFETY_FILTERED 테스트 작성 | PRD 3 AC-10, PRD 4 안전 정책, PRD 5-4 안전 게이트 | PRD-CMD-007, PRD-SEC-004 | H |
| PRD-TEST-007 | Test / Conversation Quality | 사투리, 불완전 문장, 반복 발화에 대한 쉬운 존댓말/공감 루브릭 테스트 작성 | PRD 3 AC-11, PRD 5-4 대화 자연스러움 | PRD-CMD-005, PRD-CMD-007 | H |
| PRD-TEST-008 | Test / Analyze API | `/api/v1/analyze/emotion` validation, enum, PII 없는 detected_keywords 테스트 작성 | PRD 3 AC-12~AC-14, PRD 4 F2 | PRD-CMD-009 | H |
| PRD-TEST-009 | Test / Proactive API | `/api/v1/schedule/proactive` trigger validation과 복약/의료 지시 문구 금지 테스트 작성 | PRD 3 AC-8~AC-9, PRD 4 F3 | PRD-CMD-010 | M |
| PRD-TEST-010 | Test / Report API | PoC report 필수 지표, active device/elder 집계, JSON/CSV PII 미포함 테스트 작성 | PRD 3 AC-5~AC-7, PRD 4 F7 | PRD-QRY-004, PRD-QRY-005 | H |
| PRD-TEST-011 | Test / Consent Deletion | 6개 동의 항목 저장, 동의 철회, deletion job 생성, retention target 테스트 작성 | PRD 5-3 동의/보존/삭제, AC-21 | PRD-CMD-013, PRD-QRY-009 | H |
| PRD-TEST-012 | Test / RBAC Privacy | 보호자/기관 요약 데이터 원문 미노출, 권한 실패 시 no data payload 테스트 작성 | PRD 3 AC-12~AC-17, PRD 5-3 역할별 접근 | PRD-QRY-010, PRD-QRY-011, PRD-SEC-002, PRD-SEC-005 | H |
| PRD-TEST-013 | Test / Ops | 운영 로그 마스킹, tenant threshold alert payload, review queue 수집 테스트 작성 | PRD 3 AC-18~AC-20, PRD 5-4 운영 리뷰 루프 | PRD-QRY-006, PRD-QRY-008, PRD-CMD-014, PRD-CMD-016 | H |
| PRD-TEST-014 | Test / Web E2E | Partner Console auth, role module visibility, API Docs, API Key Manager, Playground E2E 테스트 작성 | PRD 3 AC-1, AC-4, PRD 4 Partner Console 공통 계약 | PRD-FE-001~PRD-FE-009 | H |
| PRD-TEST-015 | Test / Web E2E | PoC Report, Ops Monitoring, Review Queue, User/Consent Admin privacy/performance E2E 테스트 작성 | PRD 3 AC-5~AC-7, AC-18~AC-21 | PRD-FE-010~PRD-FE-016 | H |
| PRD-TEST-016 | Test / Scope Guard | STT/TTS, 보호자/기관/어르신 완성 앱 route 미포함, 의료 진단 문구 미포함 테스트 작성 | PRD 1-4 Out of Scope, ADR-001, ADR-006 | PRD-FE-017, PRD-CT-017 | M |
| PRD-TEST-017 | Test / Performance | chat non-LLM p95 800ms, LLM 포함 p95 5초, UI initial load p95 2초 측정 스크립트 작성 | PRD 5-1, AC-2, AC-7 | PRD-CMD-008, PRD-FE-008, PRD-FE-010, PRD-FE-012 | H |
| PRD-TEST-018 | Test / Cost FinOps | usage event reconciliation, active device replay, token budget, quota, budget alert 테스트 작성 | PRD 1-5, PRD 5-1 LLM 비용 관측성 | PRD-CMD-011, PRD-CMD-012, PRD-CMD-016, PRD-NFR-008 | H |
| PRD-TEST-019 | Test / AI Evaluation | synthetic evaluation dataset, risk recall, critical recall, false positive review log 테스트 작성 | PRD 5-4 평가셋/핵심 평가 지표 | PRD-MOCK-005, PRD-CMD-014 | H |
| PRD-TEST-020 | Test / Release Gate | 안전, 위험 탐지, 오탐, 개인정보, 성능, 회귀 배포 게이트 자동화 작성 | PRD 5-4 배포 게이트, 금지되는 배포 | PRD-TEST-001~PRD-TEST-019 | H |
| PRD-NFR-001 | NFR / Observability | endpoint latency, error rate, token usage, LLM timeout, safety activation metric 수집 구현 | PRD 5-1 관측성 | PRD-PLAT-001, PRD-CMD-006, PRD-CMD-011 | H |
| PRD-NFR-002 | NFR / Operations | tenant별 오류율/지연시간 threshold와 운영자 알림 구현 | PRD 3 AC-19, PRD 5-1 관측성 | PRD-NFR-001, PRD-CMD-016 | H |
| PRD-NFR-003 | NFR / Availability | Vercel/Supabase health check와 PoC availability 99.0% 측정 구성 | PRD 5-1 가용성 | PRD-INFRA-001 | M |
| PRD-NFR-004 | NFR / Scalability | MVP 50~200명 PoC load scenario와 Post-MVP 10,000 devices 확장 테스트 계획 작성 | PRD 5-1 동시 접속 | PRD-TEST-017 | M |
| PRD-NFR-005 | NFR / Privacy | raw transcript 30일, memory 180일, analysis 180일, risk/report/log 1년, evaluation 30일 retention job 구현 | PRD 5-3 보존/삭제 원칙 | PRD-CMD-013, PRD-DB-012 | H |
| PRD-NFR-006 | NFR / Vendor AI | 외부 LLM 학습 금지, 보관/국외 이전/삭제 가능성 고지 체크리스트와 config audit 작성 | PRD 5-3 위탁/재위탁, ADR-008 | PRD-CT-011, PRD-CT-012 | M |
| PRD-NFR-007 | NFR / Security | TLS 1.3, AES-256 equivalent at-rest encryption, API secret 관리 점검 작성 | PRD 5-2 암호화, C-TEC-007 | PRD-INFRA-001 | M |
| PRD-NFR-008 | NFR / Cost Control | Gemini model routing, token cap, tenant quota, Playground quota, retry cap, budget alert 구현 | PRD 1-5 LLM 비용 효율성, ADR-008 | PRD-DB-010, PRD-DB-011, PRD-CMD-006, PRD-CMD-012 | H |
| PRD-NFR-009 | NFR / Developer Experience | 첫 API 성공 30분 이내 onboarding measurement script와 UX 시나리오 작성 | PRD 1-3 KPI, PRD 3 AC-1, AC-4 | PRD-FE-003, PRD-FE-004, PRD-FE-005, PRD-FE-009 | M |
| PRD-NFR-010 | NFR / Maintainability | model version, prompt version, safety policy version, evaluation result 변경 이력 구현 | PRD 5-4 운영 리뷰 루프, ADR-007 | PRD-DB-008, PRD-CT-015 | M |
| PRD-INFRA-001 | Infra / Next.js App | Next.js App Router, Tailwind CSS, shadcn/ui, Prisma local SQLite 개발환경 구성 | PRD 1-6 C-TEC-001~004, MVP 구현 컷라인 P0 | PRD-CT-001, PRD-DB-001 | M |
| PRD-INFRA-002 | Infra / Supabase | Supabase PostgreSQL 배포환경과 Prisma migration dry-run 구성 | PRD 1-6 C-TEC-003, MVP 구현 컷라인 P0 | PRD-INFRA-001 | M |
| PRD-INFRA-003 | Infra / Vercel | Vercel Git Integration preview deployment와 env var 관리 구성 | PRD 1-6 C-TEC-007 | PRD-INFRA-001 | M |
| PRD-INFRA-004 | Infra / Deployment Gate | PoC/production promote 전 release gate 실행 워크플로 구성 | PRD 5-4 배포 게이트 | PRD-INFRA-003, PRD-TEST-020 | H |
| PRD-INFRA-005 | Infra / Backup Restore | RPO/RTO baseline, backup restore drill, 삭제 요청 복원 replay runbook 작성 | PRD 5-3 백업 데이터, 5-1 가용성 | PRD-INFRA-002, PRD-NFR-005 | M |

