# SRS v0.5 Single Fullstack MVP Alignment Plan

> 실행 기준: 본 계획서는 `SRS/실버케어_SRS_v0.5.md`를 직접 덮어쓰지 않고, `SRS/실버케어_SRS_v0.5_단일구조.md`를 단일 풀스택 MVP 구현 기준 문서로 정렬하기 위한 작업 계획이다.

## 1. Goal

`실버케어_SRS_v0.5_단일구조.md`를 MVP 구현 관점에서 아래 기술 스택에 완전히 부합하도록 정렬한다.

| 구분 | 결정 |
|---|---|
| Frontend / Backend Framework | Next.js App Router 기반 단일 풀스택 애플리케이션 |
| Server Logic | Server Actions, Route Handlers |
| Database | Prisma, local SQLite, Supabase PostgreSQL |
| UI | Tailwind CSS, shadcn/ui |
| LLM Orchestration | Vercel AI SDK |
| Default LLM Provider | Google Gemini API |
| Deployment | Vercel Git Integration 기반 자동 배포 |

본 계획의 핵심 원칙은 다음과 같다.

| 원칙 | 설명 |
|---|---|
| 기준선 보존 | `SRS/실버케어_SRS_v0.5.md`는 PRD v0.5 기준 통합 SRS로 보존한다. |
| 변형본 분리 | 단일구조 구현 결정은 `SRS/실버케어_SRS_v0.5_단일구조.md`에만 명시한다. |
| 기능 요구 유지 | `REQ-FUNC-001`~`REQ-FUNC-060`의 기능 커버리지를 삭제하지 않는다. |
| 비기능 요구 유지 | `REQ-NF-001`~`REQ-NF-041`의 보안, 성능, 개인정보, 운영 기준을 유지하되 배포/구현 표현만 단일구조에 맞춘다. |
| 향후 분리 가능성 | MVP는 통합 구조로 구현하되 API contract, domain service, repository, LLM provider adapter 경계를 유지한다. |

## 2. Target Files

| 구분 | 경로 | 처리 방식 |
|---|---|---|
| 기준 SRS | `/Users/jin3137/practice/SRS-from-PRD/SRS-Drafts-Op/SRS/실버케어_SRS_v0.5.md` | 수정하지 않음 |
| 단일구조 SRS | `/Users/jin3137/practice/SRS-from-PRD/SRS-Drafts-Op/SRS/실버케어_SRS_v0.5_단일구조.md` | 본 계획의 주 수정 대상 |
| 변경 로그 | `/Users/jin3137/practice/SRS-from-PRD/SRS-Drafts-Op/log/2026-04-28_SRS-PRD-v0.5-변경사항.md` | 작업 완료 후 요약 기록 |
| 본 계획서 | `/Users/jin3137/practice/SRS-from-PRD/SRS-Drafts-Op/plans/SRS_v0.5_single_fullstack_mvp_alignment_plan.md` | 실행 기준 문서 |

## 3. Assumptions & Constraints to Apply

### 3.1 시스템 내부 - 단일 통합 프레임워크

| ID | 적용할 제약 |
|---|---|
| C-TEC-001 | 모든 서비스는 Next.js App Router 기반의 단일 풀스택 프레임워크로 구현한다. Phase A MVP에서는 프론트엔드와 백엔드를 별도 분리하지 않는다. |
| C-TEC-002 | 서버 측 로직(DB 접근, API 호출, API Key 검증, 감사 로그 등)은 Next.js의 Server Actions 또는 Route Handlers를 사용하여 별도 백엔드 서버 없이 구현한다. |
| C-TEC-003 | 데이터베이스는 Prisma + local SQLite로 로컬 개발환경을 구성하고, 배포 시 Supabase PostgreSQL을 사용한다. |
| C-TEC-004 | UI 및 스타일링은 Tailwind CSS와 shadcn/ui를 사용한다. |

### 3.2 시스템 외부 - 연결 및 AI 통합

| ID | 적용할 제약 |
|---|---|
| C-TEC-005 | LLM 오케스트레이션은 별도 Python 서버 없이 Vercel AI SDK를 사용하여 Next.js 내부에서 직접 구현한다. |
| C-TEC-006 | LLM 호출은 Google Gemini API를 기본으로 사용하며, 환경 변수 설정만으로 모델 교체가 가능하도록 SDK 표준 인터페이스를 준수한다. |
| C-TEC-007 | 배포 및 인프라 관리는 Vercel 플랫폼으로 단일화하며, 별도 CI/CD 설정 없이 Git Push 기반 자동 배포를 사용한다. |

### 3.3 향후 분리 가능성 보장

| ID | 적용할 제약 |
|---|---|
| C-TEC-008 | UI, API Route, domain service, repository/data access, LLM provider adapter, audit/logging 모듈 경계를 유지한다. |
| C-TEC-009 | Prisma Client와 DB query는 repository/data access 계층에 격리한다. |
| C-TEC-010 | 외부 파트너 API와 Web API Playground request/response schema는 OpenAPI 호환 계약으로 관리한다. |
| C-TEC-011 | Client Component는 DB, LLM provider, API secret, API Key 검증 로직을 직접 참조하지 않는다. |
| C-TEC-012 | Route Handler와 Server Action은 stateless 원칙을 유지한다. |
| C-TEC-013 | API 트래픽, background job, webhook retry, LLM orchestration, compliance boundary, 팀 소유권이 커지면 별도 backend service 분리를 검토한다. |

## 4. Work Plan

### 4.1 문서 생성 및 헤더 정렬

- [ ] `SRS/실버케어_SRS_v0.5.md`를 기준으로 `SRS/실버케어_SRS_v0.5_단일구조.md`를 생성한다.
- [ ] `Document ID`를 `SRS-001-SINGLE`로 설정한다.
- [ ] `Revision`을 `0.5-단일구조`로 설정한다.
- [ ] `Architecture Profile: MVP Single Fullstack Structure`를 헤더에 추가한다.
- [ ] `1.1 Purpose`에 본 문서가 Next.js App Router 기반 단일 풀스택 구현 변형본임을 명시한다.

### 4.2 Scope 정렬

- [ ] `1.2 Scope`의 Developer Portal / Sandbox 설명을 `cURL/TypeScript` 샘플 중심으로 변경한다.
- [ ] 보호자용 완성 앱, 기관용 완성 앱, 어르신용 앱 UI가 Out-of-Scope임을 유지한다.
- [ ] Phase B 센서 수집, 긴급 알림, KPI 리포트는 추적성은 유지하되 MVP 필수 구현 범위와 구분한다.

### 4.3 Technical Assumptions & Constraints 정렬

- [ ] `1.5 Assumptions & Constraints`에 `C-TEC-001`~`C-TEC-007`을 단일구조 기술 스택 기준으로 유지한다.
- [ ] `C-TEC-008`~`C-TEC-013`으로 향후 분리 가능성 보장 제약을 유지한다.
- [ ] 검증 기준에는 단순 선언이 아니라 확인 가능한 기준을 둔다.
  - 단일 Next.js 애플리케이션 배포 산출물
  - 별도 backend repository 또는 backend server process 없음
  - client bundle 내 DB/LLM/API secret import 없음
  - PostgreSQL migration dry-run 통과
  - Vercel Git Integration 배포 로그 확인

### 4.4 System Context and Interfaces 정렬

- [ ] `3.1 External Systems`에서 추상적인 외부 LLM 표현을 `Google Gemini API`와 `Vercel AI SDK provider call`로 구체화한다.
- [ ] `Vercel Platform`을 Deployment/Runtime 외부 시스템으로 추가한다.
- [ ] 데이터 저장소는 `Prisma ORM`, `local SQLite`, `Supabase PostgreSQL` 기준으로 설명한다.
- [ ] 운영 모니터링은 Vercel logs/metrics와 application audit log를 함께 사용하는 것으로 정리한다.
- [ ] API 공통 계약에 “API endpoint는 Route Handlers, Web UI mutation은 Server Actions로 구현”을 추가한다.

### 4.5 Functional Requirements 정렬

- [ ] `REQ-FUNC-001`~`REQ-FUNC-060`을 삭제하지 않는다.
- [ ] Developer Portal 샘플 요구사항의 `cURL/Python` 표현을 `cURL/TypeScript`로 변경한다.
- [ ] Web API Playground snippet 생성 요구사항도 `cURL/TypeScript` 기준으로 변경한다.
- [ ] Partner Console, Web API Playground, PoC Report Console, Ops Monitoring, User & Consent Admin View 요구사항을 유지한다.
- [ ] `REQ-FUNC-060`을 유지하여 보호자/기관/어르신용 완성 앱이 MVP 범위에 들어오지 않도록 한다.

### 4.6 Non-Functional Requirements 정렬

- [ ] `REQ-NF-034`의 `CI/CD gate report` 표현을 Vercel Git Integration preview validation 기준으로 변경한다.
- [ ] 별도 CI/CD pipeline을 전제로 읽히는 표현을 제거한다.
- [ ] 배포 gate는 “preview deployment 검증 후 production/PoC promote” 기준으로 표현한다.
- [ ] Web Security, Web Privacy, Web Auditability, Sandbox Isolation, Web Performance, Web Export Security 요구사항은 유지한다.

### 4.7 Appendix API 및 Data Model 정렬

- [ ] Appendix `6.1 API Endpoint List`의 Web UI 및 API 설명이 Next.js 단일 앱과 충돌하지 않는지 확인한다.
- [ ] Appendix `6.2 Entity & Data Model`은 Prisma schema로 변환 가능한 수준의 entity/table 구조를 유지한다.
- [ ] local SQLite와 Supabase PostgreSQL 간 차이가 문제될 수 있는 enum, JSON, index, transaction 표현을 검토한다.
- [ ] Phase B 센서/알림 entity는 유지하되 MVP 필수 구현과 구분한다.

### 4.8 Diagram 정렬

- [ ] Component Diagram을 단일 Next.js App Router 구조로 교체한다.
- [ ] 다이어그램에 다음 컴포넌트를 포함한다.
  - Vercel Platform
  - SilverCare MVP Single Next.js App Router Application
  - App Router Web UI
  - Route Handlers
  - Server Actions
  - server-only Domain Modules
  - Repository / Data Access with Prisma Client
  - LLM Provider Adapter with Vercel AI SDK
  - Local SQLite
  - Supabase PostgreSQL
  - Google Gemini API
- [ ] Sequence Diagram에서 물리적 백엔드 서비스처럼 읽히는 표현이 있으면 Next.js Route Handler 또는 Server Action 흐름으로 정리한다.
- [ ] Use Case, ERD, Class Diagram은 기능 커버리지를 훼손하지 않는 범위에서 유지한다.

### 4.9 Traceability and Verification 정렬

- [ ] Traceability Matrix에서 기존 Story와 Requirement ID 연결을 유지한다.
- [ ] 신규 또는 변경된 기술 제약은 테스트 가능한 verification 기준을 가진다.
- [ ] `REQ-FUNC` 총 개수와 `REQ-NF` 총 개수가 변경되지 않았는지 확인한다.
- [ ] `rg`로 다음 충돌 표현이 남아 있지 않은지 확인한다.
  - `cURL/Python`
  - `Python sample`
  - `Python snippet`
  - `CI/CD gate report`
  - `External/Internal LLM Engine`
  - 독립 백엔드 서버를 전제로 하는 `API Gateway`

### 4.10 변경 로그 작성

- [ ] `log/2026-04-28_SRS-PRD-v0.5-변경사항.md`에 단일구조 변형본 생성 및 주요 변경 사항을 기록한다.
- [ ] 기능 요구사항 수, 비기능 요구사항 수, 핵심 변경 섹션을 요약한다.

## 5. MVP Core User Experience and Value Delivery Review

### 5.1 결론

Next.js 단일 풀스택 구조로 변경해도 MVP의 핵심 사용자 경험과 가치 전달은 훼손되지 않는다. 이유는 이번 변경이 사용자 가치 자체를 축소하는 변경이 아니라 구현 배포 단위를 단순화하는 아키텍처 결정이기 때문이다.

### 5.2 핵심 가치 흐름별 영향 평가

| 가치 흐름 | MVP에서 전달해야 하는 가치 | 단일구조 변경 영향 | 보존 조건 | 판단 |
|---|---|---|---|---|
| B2B 개발자 첫 연동 | API Key, 문서, 샘플, Playground로 빠르게 첫 호출 성공 | Next.js 단일 앱 안에서 Developer Portal, API Docs, Playground를 함께 제공하므로 오히려 진입 장벽이 낮아진다. | API Docs, cURL/TypeScript sample, sandbox scenario, 표준 오류 응답 유지 | 훼손 없음 |
| API Playground 경험 | 웹에서 request를 만들고 sandbox API 응답을 확인 | Route Handlers를 직접 호출할 수 있어 구현 경로가 짧아진다. | sandbox 기본값, production 호출 권한 분리, response viewer 유지 | 훼손 없음 |
| 어르신 대화 응답 | 파트너 기기를 통해 자연스러운 존댓말 응답과 안전 응답 제공 | 백엔드 분리 여부는 최종 대화 경험에 직접 영향이 없다. | Chat Reply API, memory consent, safety policy, Gemini 호출 경계 유지 | 훼손 없음 |
| 정서/위험 분석 | 대화 텍스트에서 감정 태그, 위험 수준, 권장 액션 제공 | Vercel AI SDK + Gemini adapter로 구현 가능하다. | risk_level, emotion_tags, recommended_action, review queue 유지 | 훼손 없음 |
| PoC 성과 검증 | 활성 디바이스, 대화 수, p95 latency, 오류율, 위험 이벤트를 확인 | 단일 앱에서도 Prisma + Supabase로 집계 가능하다. | usage event, audit log, PoC Report Console, CSV/JSON export 유지 | 훼손 없음 |
| 운영/품질 관리 | 로그, 오류율, 안전 정책, 리뷰 큐, 삭제 job 상태 확인 | Vercel logs + application audit log 조합으로 MVP 운영에는 충분하다. | Ops Monitoring, Conversation Log View, User & Consent Admin View 유지 | 훼손 없음 |
| 개인정보 보호 | 원문/PII 마스킹, 동의 상태, 삭제 요청 관리 | 단일구조에서는 client/server 경계가 더 중요해진다. | server-only module, Client Component secret 접근 금지, audit log 유지 | 조건부 보존 |
| 향후 확장성 | 트래픽/조직/규제가 커질 때 백엔드 분리 가능 | 단일구조는 초기 속도에 유리하나 경계가 무너지면 분리 비용이 커진다. | repository, service, adapter, OpenAPI contract 경계 유지 | 조건부 보존 |

### 5.3 훼손되지 않는 핵심 기능 목록

| 기능 | 요구사항 범위 | 단일구조 적용 후 상태 |
|---|---|---|
| API Key 발급/폐기 | `REQ-FUNC-001`, `REQ-FUNC-049` | 유지 |
| Developer Portal / API Docs | `REQ-FUNC-002`, `REQ-FUNC-050` | 유지, TypeScript sample 중심으로 변경 |
| Web API Playground | `REQ-FUNC-051`~`REQ-FUNC-055` | 유지 |
| Chat Reply API | `REQ-FUNC-007`~`REQ-FUNC-014` | 유지 |
| Emotion/Risk Analysis API | `REQ-FUNC-016`~`REQ-FUNC-019` | 유지 |
| Proactive Schedule API | `REQ-FUNC-020`, `REQ-FUNC-021` | 유지 |
| PoC Report API/Console | `REQ-FUNC-022`~`REQ-FUNC-026`, `REQ-FUNC-056` | 유지 |
| Ops Monitoring Console | `REQ-FUNC-057`, `REQ-FUNC-058` | 유지 |
| User & Consent Admin View | `REQ-FUNC-059` | 유지 |
| 보호자/기관/어르신 완성 앱 제외 | `REQ-FUNC-060` | 유지 |

### 5.4 남는 리스크

| Risk ID | 리스크 | 영향 | 대응 |
|---|---|---|---|
| R-SINGLE-001 | Client Component에서 DB/LLM/secret을 직접 import하면 개인정보와 secret이 노출될 수 있다. | 보안 사고, tenant data exposure | `server-only` 모듈 규칙, 정적 import 검사, code review gate 적용 |
| R-SINGLE-002 | Server Actions가 복잡해지면 API contract와 UI mutation 경계가 흐려질 수 있다. | 향후 백엔드 분리 비용 증가 | 외부 파트너 API는 Route Handlers에 고정하고, Server Actions는 콘솔 내부 mutation으로 제한 |
| R-SINGLE-003 | SQLite와 PostgreSQL의 동작 차이로 로컬과 배포 환경의 버그가 갈릴 수 있다. | PoC 배포 장애 | Supabase PostgreSQL migration dry-run과 주요 query 테스트를 필수화 |
| R-SINGLE-004 | Vercel serverless runtime timeout이 LLM 응답 시간과 충돌할 수 있다. | 대화 응답 실패 또는 timeout 증가 | LLM timeout, fallback response, request_id 기반 오류 추적, 필요 시 streaming 도입 |
| R-SINGLE-005 | 별도 CI/CD 없이 Git Push 배포를 사용하면 검증 누락 가능성이 있다. | 품질 회귀 | Vercel preview deployment validation checklist와 release gate report 운영 |

### 5.5 최종 판단

단일구조 적용은 MVP의 핵심 사용자 경험을 훼손하지 않는다. 오히려 B2B 개발자 포털, API Playground, Route Handler 기반 API, Server Action 기반 콘솔 mutation을 한 배포 단위에 묶어 초기 구현 속도와 운영 단순성을 높인다.

단, 단일구조가 장기 구조 부채가 되지 않으려면 다음 4가지는 요구사항 수준에서 반드시 유지해야 한다.

| 필수 유지 조건 | 이유 |
|---|---|
| OpenAPI 호환 API contract | 향후 백엔드 분리 시 파트너 연동을 깨지 않기 위함 |
| service/repository boundary | Prisma와 domain logic 결합을 막기 위함 |
| LLM provider adapter | Gemini 또는 다른 모델로 교체할 때 제품 기능을 흔들지 않기 위함 |
| server-only security boundary | Next.js client/server 혼합 구조에서 secret과 PII 노출을 막기 위함 |

## 6. Acceptance Criteria for the Plan Execution

| AC ID | Acceptance Criteria |
|---|---|
| PLAN-AC-001 | `SRS/실버케어_SRS_v0.5.md`는 수정되지 않고 보존되어야 한다. |
| PLAN-AC-002 | `SRS/실버케어_SRS_v0.5_단일구조.md`는 `Document ID: SRS-001-SINGLE`, `Revision: 0.5-단일구조`를 포함해야 한다. |
| PLAN-AC-003 | 단일구조 SRS는 `C-TEC-001`~`C-TEC-013`을 포함해야 한다. |
| PLAN-AC-004 | 단일구조 SRS에는 `Next.js`, `Route Handlers`, `Server Actions`, `Prisma`, `SQLite`, `Supabase PostgreSQL`, `Tailwind CSS`, `shadcn/ui`, `Vercel AI SDK`, `Google Gemini API`, `Vercel Platform`이 명시되어야 한다. |
| PLAN-AC-005 | 단일구조 SRS에 `cURL/Python`, `Python snippet`, `CI/CD gate report`, `External/Internal LLM Engine` 표현이 남아 있지 않아야 한다. |
| PLAN-AC-006 | `REQ-FUNC` 요구사항 수는 60개로 유지되어야 한다. |
| PLAN-AC-007 | `REQ-NF` 요구사항 수는 41개로 유지되어야 한다. |
| PLAN-AC-008 | Component Diagram은 Next.js App Router 단일 앱, Route Handlers, Server Actions, Prisma, Vercel AI SDK, Gemini API, Vercel Platform을 표현해야 한다. |
| PLAN-AC-009 | 변경 로그에 단일구조 변형본 생성 이유와 주요 변경 사항이 기록되어야 한다. |
| PLAN-AC-010 | MVP 가치 전달 검토 결과가 문서에 포함되어야 하며, 기능 삭제 없이 구현 스택만 정렬되었음을 설명해야 한다. |

## 7. Suggested Verification Commands

```bash
rg -n "Document ID|Revision|Architecture Profile" SRS/실버케어_SRS_v0.5_단일구조.md
rg -n "Next\\.js|Route Handlers|Server Actions|Prisma|SQLite|Supabase|Tailwind|shadcn|Vercel AI SDK|Google Gemini|Vercel Platform" SRS/실버케어_SRS_v0.5_단일구조.md
rg -n "cURL/Python|Python snippet|CI/CD gate report|External/Internal LLM Engine" SRS/실버케어_SRS_v0.5_단일구조.md
rg -c "^\\| REQ-FUNC-[0-9]{3}" SRS/실버케어_SRS_v0.5_단일구조.md
rg -c "^\\| REQ-NF-[0-9]{3}" SRS/실버케어_SRS_v0.5_단일구조.md
```

## 8. Implementation Notes

- SRS 원본 기준선과 단일구조 변형본을 혼동하지 않도록 파일명을 유지한다.
- PRD v0.5는 제품 요구 Source of Truth로 유지한다. 기술 스택 구현 변형은 SRS 단일구조 문서에서 관리한다.
- 보호자/기관/어르신용 완성 앱을 추가하려면 본 계획이 아니라 PRD scope 변경 계획을 먼저 작성해야 한다.
- 단일구조 문서가 구현 기준이 되면, 다음 단계 산출물은 `OpenAPI schema`, `Prisma schema`, `Next.js route map`, `Partner Console IA` 순서로 작성하는 것이 적절하다.
