# GitHub Issue Task Quality Review

- 검수일: 2026-04-28
- 기준 문서: `TASKS/실버케어_SRS_v0.5_단일구조_TASKS.md`
- 검수 대상: `TASKS/GitHub-Issues/*.md`

## 식별한 품질 문제

| 문제 | 영향 | 조치 |
|---|---|---|
| 자동 생성된 89개 이슈의 Task Breakdown이 prefix 공통 문장 중심이었다. | 에이전트가 실제 구현 범위와 산출물을 좁히기 어렵다. | Task 유형별로 CT/DB/MOCK/PLAT/SEC/CMD/QRY/UX/FE/TEST/NFR/PMV 전용 실행 계획으로 재작성했다. |
| 자동 생성된 89개 이슈의 Acceptance Criteria가 SRS 항목별 구체성이 부족했다. | 테스트 코드 또는 리뷰 기준으로 바로 전환하기 어렵다. | 각 Task prefix별 GWT 시나리오를 태스크 목적, SRS section, dependencies 기준으로 보강했다. |
| 89개 이슈의 `Blocks`가 실제 후속 태스크가 아니라 일반 문장으로 남아 있었다. | GitHub Project나 자동화 도구가 의존성 그래프를 만들 수 없다. | 원본 TASKS 파일의 Dependencies를 역산해 모든 이슈의 실제 `Blocks` 목록을 채웠다. |
| 일부 파일에 feature 문구가 backtick과 함께 반복되어 문장이 어색했다. | 사람이 읽을 때 작업 지시의 신뢰도가 낮아진다. | 문장 템플릿을 재작성해 feature명을 목적과 산출물 중심으로 정리했다. |

## 보강 결과

| 항목 | 결과 |
|---|---:|
| 전체 원본 Task ID | 99 |
| 개별 GitHub Issue 파일 | 99 |
| 이번에 본문 보강한 자동 생성 이슈 | 89 |
| Dependencies 역산 후 Blocks 보강 | 99개 전부 |
| 누락 필수 섹션 | 0 |
| `TBD`/`TODO`/FastAPI/MySQL/Vite/LangChain 잔존 | 0 |

## 2차 수동 보강 로그

| Batch | 대상 파일 | 식별한 추가 품질 문제 | 보강 내용 |
|---|---|---|---|
| 01 | CMD-001, CMD-002, CMD-003, CMD-004, CMD-005 | 1차 자동 보강 문구가 command 공통 표현에 머물러 API Key, chat request, memory consent, Gemini adapter의 실제 구현 경계가 흐렸다. | 각 파일의 Task Breakdown, Acceptance Criteria, Technical Constraints를 SRS 요구사항과 데이터/API 계약에 맞춰 도메인별로 재작성했다. |
| 02 | CMD-006, CMD-007, CMD-008, CMD-009, CMD-010 | safety, analysis, proactive, usage metering 태스크가 공통 상태 변경 문구로 작성되어 `SAFETY_FILTERED`, enum 검증, `analysis_result`, `risk_event`, `usage_event`의 테스트 기준이 부족했다. | 안전 응답/처리 거부 분리, 분석 enum 검증, PII 제거, 선제 발화 제한, usage replay 기준을 GWT 시나리오와 제약사항에 반영했다. |
| 03 | CMD-011, CMD-012, CMD-013, DB-003, DB-004 | 개인정보 삭제/동의, 리뷰 큐, 알림, identity/conversation schema 태스크가 공통 command/data 문구로 작성되어 cascade 대상, 평가셋 보존, alert payload, consent dimension, retention/index 기준이 부족했다. | deletion job cascade target, review/evaluation sample 정책, alert_event 범위, identity 6개 동의 필드, conversation/message/memory retention schema를 GWT 시나리오와 제약사항에 반영했다. |
| 04 | DB-005, DB-006, DB-007, DB-008, DB-009 | 분석/사용량/알림/운영/Phase B 데이터 태스크가 공통 migration 문구로 작성되어 필드 단위 schema, 집계 index, 운영 보강 모델, Phase B 비활성 backlog 경계가 부족했다. | analysis/risk/evaluation, usage/report/audit, alert_event, operations metadata, sensor_event backlog를 SRS 필드·인덱스·보존기간과 MVP/Phase B 경계에 맞춰 재작성했다. |
| 05 | FE-001, FE-002, FE-003, FE-004, FE-005 | Partner Console 초기 화면, API Key Manager, API Docs, Playground, Sandbox가 공통 UI 템플릿 문구로 작성되어 화면별 필드, 액션, 금지 표시, sandbox/production 경계가 부족했다. | SRS 6.4/6.5 기준으로 tenant context, API Key 1회 표시, OpenAPI docs, request builder validation, sandbox scenario preset을 구체화했다. |
| 06 | FE-006, FE-007, FE-008, FE-009, FE-010, FE-011, MOCK-001, MOCK-002, MOCK-003, MOCK-004 | Response viewer, snippet, report, ops, consent admin, scope guard 및 mock fixture가 공통 UI/mock 문구로 작성되어 표시 필드, privacy assertion, fixture shape, Phase A synthetic data 기준이 부족했다. | Playground response/snippet, PoC/Ops/Consent UI, out-of-scope route guard, tenant/API key/sandbox/Gemini/reporting fixture를 테스트 가능한 GWT와 제약사항으로 재작성했다. |
| 07 | NFR-001, NFR-002, NFR-003, NFR-004, NFR-005, NFR-006, NFR-007, NFR-008, NFR-009, NFR-010, NFR-011, NFR-012 | Observability, operations, availability, DR, security, privacy, vendor AI, cost, deployment, maintainability, DX, scalability 태스크가 공통 NFR 문구로 작성되어 수치 기준, 실패 조건, evidence, release gate 연결이 부족했다. | SRS REQ-NF 수치 기준을 반영해 metric coverage, 5분 알림, 99.0/99.9 availability, RPO/RTO, TLS/encryption, retention, vendor disclosure, quota/budget, Vercel gate, AI release record, 30분 onboarding, PoC/Post-MVP load 경계를 GWT와 제약사항으로 재작성했다. |
| 08 | PLAT-001, PLAT-002, PLAT-003, PMV-001, PMV-002, PMV-003, PMV-004, QRY-001, QRY-002, QRY-003 | 플랫폼 공통 정책, Post-MVP governance, Developer Portal query가 공통 platform/query/backlog 문구에 머물러 request context, repository boundary, audit coverage, Phase B 제외 범위, API Key/docs/sandbox 조회 필드가 부족했다. | request_id/tenant context, Prisma repository 격리, audit sanitizer, Phase B sensor/emergency/KPI contract backlog, 보호자/기관 UI scope change 절차, API Key 원문 재표시 금지, docs contract snapshot, sandbox synthetic fixture 기준을 구체화했다. |
| 09 | QRY-004, QRY-005, QRY-006, QRY-007, QRY-008, QRY-009, SEC-001, SEC-002, SEC-003, SEC-004, SEC-005 | PoC report, ops log, review queue, consent admin, guardian/institution data, API key auth, tenant guard, rate limit, error mapper, PII masking 태스크가 공통 query/security 문구로 작성되어 응답 필드, 오류 코드, audit, PII 차단 기준이 부족했다. | PoC JSON/CSV 집계 필드, export audit, 운영 로그 필터, 리뷰 큐 coverage, 6개 동의 상태, 보호자/기관 원문 미노출 계약, API Key hash 검증, TENANT_ACCESS_DENIED, RATE_LIMIT_EXCEEDED, 표준 오류 mapper, PII masking hook과 GWT를 구체화했다. |
| 10 | TEST-001, TEST-002, TEST-003, TEST-004, TEST-005, TEST-006, TEST-007, TEST-008, TEST-009, TEST-010, TEST-011, TEST-012, TEST-013, TEST-014, TEST-015, TEST-016, TEST-017, TEST-018 | 테스트 태스크가 공통 "요구사항 위반 탐지/회귀 방지" 문구로 작성되어 실제 fixture, 실패 조건, release gate 연결이 부족했다. | contract, API common, API Key, chat, memory, safety, AI quality, analyze, proactive, report, consent/privacy, RBAC, ops, web UI, performance, cost, release gate별 테스트 fixture와 GWT를 SRS 수치·보안·privacy 기준에 맞춰 재작성했다. |
| 11 | UX-001, UX-002, UX-003 | UX 태스크가 공통 화면 설계 템플릿에 머물러 Partner Console IA, Playground 상태, privacy display policy, role visibility가 부족했다. | Home/Key/Docs/Playground/Report/Ops/Consent navigation, request builder/response viewer/snippet 상태, masked evidence, prohibited display, audit 대상 UI action, role별 module visibility를 SRS 6.4/6.5 기준으로 구체화했다. |

## 보강 대상 Task ID

CMD-001, CMD-002, CMD-003, CMD-004, CMD-005, CMD-006, CMD-007, CMD-008, CMD-009, CMD-010, CMD-011, CMD-012, CMD-013, DB-003, DB-004, DB-005, DB-006, DB-007, DB-008, DB-009, FE-001, FE-002, FE-003, FE-004, FE-005, FE-006, FE-007, FE-008, FE-009, FE-010, FE-011, MOCK-001, MOCK-002, MOCK-003, MOCK-004, NFR-001, NFR-002, NFR-003, NFR-004, NFR-005, NFR-006, NFR-007, NFR-008, NFR-009, NFR-010, NFR-011, NFR-012, PLAT-001, PLAT-002, PLAT-003, PMV-001, PMV-002, PMV-003, PMV-004, QRY-001, QRY-002, QRY-003, QRY-004, QRY-005, QRY-006, QRY-007, QRY-008, QRY-009, SEC-001, SEC-002, SEC-003, SEC-004, SEC-005, TEST-001, TEST-002, TEST-003, TEST-004, TEST-005, TEST-006, TEST-007, TEST-008, TEST-009, TEST-010, TEST-011, TEST-012, TEST-013, TEST-014, TEST-015, TEST-016, TEST-017, TEST-018, UX-001, UX-002, UX-003
