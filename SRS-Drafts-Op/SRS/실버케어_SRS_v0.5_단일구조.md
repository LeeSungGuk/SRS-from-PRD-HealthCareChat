# Software Requirements Specification (SRS)
Document ID: SRS-001-SINGLE
Revision: 0.5-단일구조
Date: 2026-04-28
Standard: ISO/IEC/IEEE 29148:2018
Architecture Profile: MVP Single Fullstack Structure

-------------------------------------------------

## 1. Introduction

### 1.1 Purpose

본 문서는 실버케어 Phase A MVP 및 Phase B 확장 API에 대한 Software Requirements Specification의 단일구조 구현 변형본이다. 본 SRS는 [REF-01]의 PRD v0.5-단일구조를 유일한 제품 요구 원천으로 사용하여, B2B2C 돌봄 기기 파트너가 텍스트 기반 시니어 대화 API, 정서/위험 신호 태깅 API, PoC 리포트 API, Partner Console, 운영 모니터링 및 향후 센서 기반 통합 돌봄 API를 Next.js App Router 기반 단일 풀스택 구조로 구현·검증할 수 있도록 요구사항을 정의한다.

본 SRS의 목적은 다음과 같다.

| 목적 ID | 목적 |
|---|---|
| OBJ-001 | Phase A MVP의 API 기능, 데이터, 인터페이스, 제약, 비기능 요구사항을 Next.js 단일 풀스택 구현 가능한 수준으로 명세한다. |
| OBJ-002 | PRD의 사용자 스토리와 Acceptance Criteria를 atomic Functional Requirement로 분해하고 Story Source와 Test Case ID를 연결한다. |
| OBJ-003 | KPI, 성능, 보안, 개인정보, AI 안전성, 운영 지표를 measurable Non-Functional Requirement로 정의한다. |
| OBJ-004 | API, 데이터 모델, 시퀀스, 추적성 매트릭스를 제공하여 설계·개발·QA·PoC 운영의 공통 기준선을 제공한다. |

### 1.2 Scope (In-Scope / Out-of-Scope)

#### In-Scope

| Priority | Scope Item | SRS Coverage | Source |
|---|---|---|---|
| Must | B2B API 인증 | 파트너별 API Key, `tenant_id` 기반 데이터 격리, rate limit, 표준 오류 응답 | [REF-01] 1-4, 4 |
| Must | Developer Portal / Sandbox | API Key 발급, Swagger 문서, cURL/TypeScript 샘플, sandbox elder profile, 정상/안전/오류 응답 재현 | [REF-01] 1-4, 3-1, F0 |
| Must | 대화 생성 API | `/api/v1/chat/reply` 텍스트 입력/응답, 쉬운 존댓말, 짧은 공감, 시니어 친화 응답 | [REF-01] 1-4, 3-1, 3-3, F1 |
| Must | 제한적 메모리 | 명시적 동의가 있는 경우에만 최근 대화 요약 또는 중요 메모리 사용 | [REF-01] 1-4, 3-3, 5-3, ADR-004 |
| Must | 정서/위험 신호 태깅 | `/api/v1/analyze/emotion`, `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action` 반환 | [REF-01] 1-4, 3-4, 4 |
| Must | 안전 응답 정책 | 의료, 처방, 응급, 자해 관련 발화에서 진단/처방 금지 및 가족/의료진/긴급 연락 권고 | [REF-01] 1-4, 3-3, 5-4, ADR-003 |
| Must | PoC 집계 리포트 | 파트너 단위 일별 활성 어르신 수, 대화 횟수, 지연시간, 오류율, 위험 신호 수 제공 | [REF-01] 1-4, 3-2, F7 |
| Must | 과금 검증 데이터 | 월 활성 디바이스 수, API 호출량, 기본 제공량 초과 사용량 산출 근거 수집 | [REF-01] 1-5, 3-2 |
| Must | 운영 모니터링 | 요청 로그, `request_id`, 지연시간, 오류율, 안전 정책 발동 여부, PII 마스킹 로그 | [REF-01] 1-4, 3-6, 5-1 |
| Should | 선제적 발화 | `/api/v1/schedule/proactive` 기반 일반 안부, 식사, 수면, 일상 확인성 발화 | [REF-01] 1-4, F3 |
| Should | Phase B 센서 수집 | `/api/v2/sensor/ingest` 기반 센서 데이터 수신 및 시계열 저장. 단일구조 MVP 1차 구현 컷에서는 제외하고 추적성만 유지한다. | [REF-01] 1-2, F4 |
| Should | Phase B 긴급 알림 | 대화 위험 신호와 센서 무활동 결합 시 보호자/기관 Webhook 알림. 단일구조 MVP 1차 구현 컷에서는 제외하고 추적성만 유지한다. | [REF-01] 1-2, F5 |
| Should | Phase B KPI 리포트 | 주간 정서 리포트, 수면 분석 결과, 기관용 성과 데이터 JSON 제공. 단일구조 MVP 1차 구현 컷에서는 제외하고 추적성만 유지한다. | [REF-01] 1-2, F6 |

#### Out-of-Scope

| Out-of-Scope Item | Rationale | Requirement Handling |
|---|---|---|
| 자체 하드웨어 제조 | B2B2C API 플랫폼 전략과 불일치 | SRS에서 구현 요구사항으로 정의하지 않음 |
| STT/TTS 음성 처리 | ADR-001에 따라 B2B 파트너 기기 책임 | API는 텍스트만 수신·반환 |
| 보호자용/기관용 완성 앱 직접 제공 | MVP는 API 검증 목적 | 파트너 또는 후속 Phase UI로 처리 |
| 의료 진단, 처방, 치료 조언 | 의료 책임과 LLM 환각 리스크 | 안전 응답 및 권고 신호로 제한 |
| 응급 구조 보장 | 실제 구조/출동 책임은 실버케어 API 범위 밖 | `risk_level`은 참고 신호로만 제공 |
| 실시간 119/응급기관 자동 연결 | 법적 책임, 오탐/미탐, 기관 연동 리스크 | `suggest_emergency_contact` 권고만 제공 |
| Phase A 센서 데이터 수집/분석 | Phase B 범위 | F4-F6으로 별도 추적 |
| 치매 조기 진단/낙상 판정 | 진단 책임 리스크 | Phase B에서도 진단이 아닌 참고 분석으로 제한 |
| 고도화된 관리자 대시보드 | PoC 리포트는 API/CSV/간단 집계 수준 | 고급 UI 요구사항 제외 |
| 파트너별 커스텀 LLM 파인튜닝 | 초기 PoC는 공통 모델과 정책 기반 커스터마이징 | 모델별 파인튜닝 제외 |
| 정식 가격표 최적화 | MVP는 과금 단위와 유료 전환 의향 검증 목적 | 원화 단가 요구사항 제외 |

#### Constraints and Assumptions

| ID | Type | Statement | Source | Verification |
|---|---|---|---|---|
| CON-001 | Architecture | 실버케어 API는 음성 파일을 받지 않고 STT/TTS를 B2B 파트너 기기에 위임해야 한다. | [REF-01] ADR-001 | API schema에 audio binary field가 없음을 확인 |
| CON-002 | Business Model | 서비스는 B2C 직접 앱이 아니라 B2B2C API 구독형 SaaS 모델로 제공되어야 한다. | [REF-01] ADR-002 | Scope와 API list에 직접 앱 기능이 없음을 확인 |
| CON-003 | Safety | 감정/위험 분석은 의료 진단이 아니라 보호자/기관 확인용 참고 신호로 제한되어야 한다. | [REF-01] ADR-003 | 안전 응답 테스트와 금칙 표현 테스트 통과 |
| CON-004 | Privacy | 기억 메모리는 명시적 동의와 제한된 보존 기간 안에서만 사용되어야 한다. | [REF-01] ADR-004 | 동의 철회 후 `memory_used=false` 확인 |
| CON-005 | Privacy | 대화 원문은 기본적으로 보호자/기관에 제공하지 않고 요약 리포트 중심으로 제공되어야 한다. | [REF-01] ADR-005 | 원문 미노출 API 테스트 통과 |
| CON-006 | Risk | 고위험 알림은 응급 구조 보장이 아니라 확인 권고 이벤트로 표현되어야 한다. | [REF-01] R3, R5 | 알림 payload와 문구 QA |
| CON-007 | Privacy | 외부 LLM/클라우드로 전송되는 데이터는 서비스 제공에 필요한 최소 데이터로 제한되어야 하며 학습 사용을 허용하지 않아야 한다. | [REF-01] R6, 5-3 | 외부 위탁 설정·계약 체크리스트 검토 |
| CON-008 | Validation | 모델, 프롬프트, 안전 정책 변경은 평가셋과 배포 게이트를 통과해야 PoC 환경에 배포될 수 있다. | [REF-01] 5-4 | 배포 파이프라인 gate 결과 확인 |
| ASSUMP-001 | Assumption | PRD v0.5-단일구조는 상세 OpenAPI type, field length, pagination 방식을 확정하지 않는다. SRS는 PRD 필드를 기준으로 baseline schema를 정의하며 최종 OpenAPI는 별도 산출물로 확정한다. | [REF-01] 4, 9 | OpenAPI backlog 생성 |
| ASSUMP-002 | Assumption | PRD v0.5-단일구조는 RPO/RTO 수치를 제공하지 않는다. 본 SRS는 Phase A PoC 운영 기준선을 제안하며 production GA 전 제품·운영 책임자 승인이 필요하다. | [REF-01] 5-3, 9 | 운영 runbook 승인 기록 |
| ASSUMP-003 | Assumption | 보호자/기관 리포트 UI는 파트너 시스템 또는 후속 Phase가 제공하며, 실버케어는 API와 JSON/CSV 데이터 제공에 집중한다. | [REF-01] 1-4 | API-only acceptance test |

### 1.3 Definitions, Acronyms, Abbreviations

| Term | Definition |
|---|---|
| API | Application Programming Interface. B2B 파트너 기기 또는 서버가 실버케어 기능을 호출하는 HTTP 기반 인터페이스. |
| B2B2C | 실버케어가 B2B 파트너에게 API를 제공하고, 파트너 기기/서비스가 최종 어르신·보호자·기관에게 경험을 제공하는 모델. |
| B2B-PM | B2B 파트너사의 제품/사업 담당자. PoC 도입, 정식 탑재, 과금 구조 판단 책임을 가진다. |
| B2B-DEV | B2B 파트너사의 연동 개발자. API Key, Swagger, 샘플 코드로 연동 구현을 수행한다. |
| Elder | 독거 어르신 또는 시니어 최종 사용자. `elder_id`로 가명 식별된다. |
| Guardian | 보호자 자녀 또는 보호자 역할 사용자. 동의된 요약 리포트와 위험 신호 알림을 확인한다. |
| Institution Manager | 요양기관 관리자. 소속 어르신의 요약 리포트와 위험 이벤트 목록을 확인한다. |
| Platform Operator | 실버케어 운영자. 로그, 안전 정책, AI 품질, 개인정보 마스킹 상태를 관리한다. |
| `tenant_id` | B2B 파트너 또는 고객사를 식별하는 테넌트 ID. 데이터 격리와 접근 제어 기준. |
| `device_id` | 파트너 기기 식별자. 월 활성 디바이스 과금과 요청 추적에 사용. |
| `elder_id` | 파트너 시스템 내 어르신 가명/내부 식별자. 직접 식별정보가 아니어야 한다. |
| API Key | 파트너 서버 간 인증에 사용하는 비밀 키 또는 동등한 인증 수단. |
| PoC | Proof of Concept. 1~3개월 무료 또는 저가 고정비로 진행하는 API 검증 단계. |
| Phase A | 현재 MVP 범위. AI 캐릭터챗 API, 정서/위험 태깅, PoC 리포트, 운영 모니터링 중심. |
| Phase B | 확장 범위. 센서 데이터 통합, 긴급 알림, 보호자/기관 KPI 리포트 중심. |
| KPI | Key Performance Indicator. 제품 성공 및 PoC 전환 판단에 사용하는 정량 지표. |
| p95, p99 | 전체 요청 중 95번째/99번째 백분위 응답 시간. |
| SLA | Service Level Agreement. API 가용성 목표. PRD 기준 99.9%. |
| RPO | Recovery Point Objective. 장애 시 허용 가능한 데이터 손실 시간. PRD v0.5-단일구조 미확정, 본 SRS에서 PoC baseline을 둔다. |
| RTO | Recovery Time Objective. 장애 후 서비스 복구 목표 시간. PRD v0.5-단일구조 미확정, 본 SRS에서 PoC baseline을 둔다. |
| RBAC | Role-Based Access Control. 역할 기반 접근 제어. |
| PII | Personally Identifiable Information. 실명, 주민등록번호, 상세 주소 등 개인 식별정보. |
| `risk_level` | 위험 신호 수준 enum. `none`, `low`, `medium`, `high`, `critical`. |
| `emotion_tags` | 정서 및 돌봄 맥락 태그 enum. 예: `lonely`, `pain`, `sleep_issue`, `self_harm_signal`. |
| `recommended_action` | 후속 액션 enum. 예: `check_in`, `notify_guardian`, `suggest_emergency_contact`. |
| AOS | Adjusted Opportunity Score. PRD v0.5-단일구조에서 수치가 제공되지 않았으므로 본 SRS의 요구 우선순위 산정에는 사용하지 않는다. |
| DOS | Discovered Opportunity Score. PRD v0.5-단일구조에서 수치가 제공되지 않았으므로 본 SRS의 요구 우선순위 산정에는 사용하지 않는다. |
| Validator | 요구사항, 수용 기준, 테스트 결과를 검증하는 책임자 또는 검증 기준. 본 SRS에서는 Test Case ID와 Acceptance Criteria로 연결한다. |

### 1.4 References (REF-XX)

| Reference ID | Document | Version / Date | Usage |
|---|---|---|---|
| REF-01 | `실버케어초안_PRD_v0.5_단일구조.md` | v0.5-단일구조 / 2026-04-28 | 본 단일구조 SRS의 유일한 제품 요구 Source of Truth |
| REF-02 | ISO/IEC/IEEE 29148:2018 Systems and software engineering - Life cycle processes - Requirements engineering | 2018 | SRS 작성 구조와 요구사항 품질 기준 |

### 1.5 Assumptions & Constraints

Phase A MVP는 구현 속도, 배포 단순성, PoC 검증 속도를 위해 Next.js 단일 풀스택 구조로 시작한다. 단, Phase B 이후 프론트엔드와 백엔드를 분리할 수 있도록 API 계약, 도메인 서비스, 데이터 접근, LLM provider 경계를 유지해야 한다.

#### 시스템 내부 - 단일 통합 프레임워크

| ID | Area | Constraint | Source | Verification |
|---|---|---|---|---|
| C-TEC-001 | Framework | 모든 서비스는 Next.js App Router 기반의 단일 풀스택 프레임워크로 구현해야 한다. Phase A MVP에서는 프론트엔드와 백엔드를 별도 서비스로 분리하지 않는다. | [REF-01] ADR-007 | 배포 산출물이 단일 Next.js 애플리케이션으로 구성됨을 확인 |
| C-TEC-002 | Server Logic | 서버 측 로직(DB 접근, 외부 API 호출, API Key 검증, 감사 로그 기록 등)은 Next.js의 Server Actions 또는 Route Handlers를 사용하여 별도의 백엔드 서버 없이 구현해야 한다. | [REF-01] ADR-007 | 별도 백엔드 서버 프로세스 또는 별도 backend repository가 Phase A 산출물에 없음을 확인 |
| C-TEC-003 | Database | 데이터베이스는 Prisma와 로컬 SQLite를 사용하여 로컬 개발환경을 구성하고, 배포 시 Supabase PostgreSQL을 사용하여 인프라 설정 복잡도를 최소화해야 한다. | [REF-01] ADR-007 | 로컬 SQLite 실행, Supabase PostgreSQL migration dry-run, 주요 query 테스트 통과 |
| C-TEC-004 | UI Stack | UI 및 스타일링은 Tailwind CSS와 shadcn/ui를 사용하여 일관된 디자인 코드 생성을 강제해야 한다. | [REF-01] ADR-007 | UI component review에서 Tailwind CSS와 shadcn/ui 사용 여부 확인 |

#### 시스템 외부 - 연결 및 AI 통합

| ID | Area | Constraint | Source | Verification |
|---|---|---|---|---|
| C-TEC-005 | LLM Orchestration | LLM 오케스트레이션은 별도의 Python 서버 없이 Vercel AI SDK를 사용하여 Next.js 내부에서 직접 구현해야 한다. | [REF-01] ADR-007 | LLM 호출 경로에서 별도 Python service dependency가 없음을 확인 |
| C-TEC-006 | LLM Provider | LLM 호출은 Google Gemini API를 기본으로 사용하며, 환경 변수 설정만으로 모델 교체가 가능하도록 SDK의 표준 인터페이스를 준수해야 한다. | [REF-01] ADR-007 | 환경 변수 변경 기반 모델 교체 smoke test 통과 |
| C-TEC-007 | Deployment | 배포 및 인프라 관리는 Vercel 플랫폼으로 단일화하며, 별도 CI/CD 설정 없이 Git Push 기반 자동 배포를 사용해야 한다. | [REF-01] ADR-007 | Vercel Git integration 배포 로그와 환경 변수 설정 확인 |

#### 향후 분리 가능성 보장 제약

| ID | Area | Constraint | Source | Verification |
|---|---|---|---|---|
| C-TEC-008 | Module Boundary | 코드 구조는 UI, API Route, domain service, repository/data access, LLM provider adapter, audit/logging 모듈을 분리해야 한다. 각 모듈은 공개 함수 또는 typed interface를 통해서만 호출되어야 한다. | [REF-01] ADR-007 | module dependency review에서 UI가 repository/LLM adapter를 직접 import하지 않음을 확인 |
| C-TEC-009 | Data Access | Prisma Client와 database query는 repository/data access 계층에 격리해야 한다. API Route와 Server Action은 repository 구현체가 아니라 service interface를 통해 데이터 작업을 요청해야 한다. | [REF-01] ADR-007 | repository 외부 파일에서 Prisma Client 직접 import가 없음을 확인 |
| C-TEC-010 | API Contract | 외부 파트너 API와 Web API Playground의 request/response schema는 OpenAPI 호환 계약으로 관리해야 한다. 추후 백엔드 분리 시 동일한 API version에서 breaking change를 발생시켜서는 안 된다. | [REF-01] ADR-007 | OpenAPI schema diff에서 breaking change가 없음을 확인 |
| C-TEC-011 | Client Boundary | Client Component는 DB, LLM provider, API secret, API Key 검증 로직을 직접 참조해서는 안 된다. 모든 민감 로직은 server-only module에서만 실행되어야 한다. | [REF-01] ADR-007 | 정적 코드 검사에서 client bundle 내 DB/LLM/API secret import가 없음을 확인 |
| C-TEC-012 | Stateless Runtime | Route Handler와 Server Action은 stateless 원칙을 유지하고 영속 상태는 DB 또는 승인된 managed storage에만 저장해야 한다. | [REF-01] ADR-007 | 배포 환경 점검에서 local filesystem/session memory 의존성이 없음을 확인 |
| C-TEC-013 | Future Separation Trigger | API 트래픽, background job, webhook retry, LLM orchestration, compliance boundary, 팀 소유권 중 하나가 단일 Next.js 운영 한계를 초과하면 backend service 분리를 별도 Phase로 검토해야 한다. 분리 시 외부 API 계약과 주요 데이터 모델 ID는 유지해야 한다. | [REF-01] ADR-007 | Phase gate review에서 분리 필요성, API 호환성, migration plan 승인 |

## 2. Stakeholders

| Role | Responsibility | Interest |
|---|---|---|
| B2B-PM. 제품/사업 담당자 | PoC 도입 판단, 정식 탑재 검토, 과금 구조 수용성 평가 | API 도입 효과, 활성 사용량, 유료 전환 근거, 비용 예측 가능성 |
| B2B-DEV. 연동 개발자 | API Key 발급, Swagger 확인, 기기/서버 연동, 오류 처리 구현 | 첫 성공 호출 30분 이내, 명확한 schema, 샘플 코드, 안정적 오류 응답 |
| A. 독거 어르신 | 파트너 기기를 통해 대화 기능을 실제 사용 | 쉬운 존댓말, 공감 응답, 이전 맥락 반영, 의료/응급 상황 안전 권고 |
| B. 보호자 자녀 | 동의된 요약 리포트와 위험 신호 알림 확인 | 원문 미노출, 정서 추세, 위험 신호 요약, 권장 확인 액션 |
| C. 요양기관 관리자 | 소속 어르신 다수의 상태와 위험 우선순위 확인 | 어르신별 대화 빈도, 최근 위험 신호, 정서 추세, 마지막 상호작용 시각 |
| D. 플랫폼 운영자 | API 품질, 안전 정책, 개인정보 마스킹, 오류율, 지연시간 모니터링 | 비식별 로그, 안전 정책 발동률, 오탐/미탐 리뷰, 배포 게이트 |
| B2B 파트너 서버/기기 | STT/TTS, API 호출, 알림 수신, 보호자/기관 UI 제공 | 안정적 API, 명확한 책임 경계, tenant 격리, 과금 데이터 |
| 외부 LLM/클라우드 수탁자 | 대화 생성·분석 처리 또는 인프라 제공 | 최소 데이터 처리, 학습 사용 금지, 보관 제한, 삭제 가능성 |
| 실버케어 제품/개발팀 | API, 데이터 모델, 운영·보안·AI 품질 기준 구현 | 요구사항 추적성, 테스트 가능성, PoC 성공 지표 달성 |

## 3. System Context and Interfaces

### 3.1 External Systems

| External System | Direction | Interface | Data Exchanged | Key Constraints |
|---|---|---|---|---|
| B2B 파트너 기기 | Inbound/Outbound | HTTPS JSON API | STT 결과 텍스트, `tenant_id`, `device_id`, `elder_id`, 응답 텍스트, 태그, 위험 수준 | 음성 파일 미수신, API Key 인증, rate limit |
| B2B 파트너 서버 | Inbound/Outbound | HTTPS JSON API, CSV download, Webhook | 리포트 조회, 과금 집계, 알림 이벤트 수신 | tenant 격리, 권한 검증, PII 최소화 |
| 보호자 앱 또는 기관 시스템 | Outbound via Partner | Partner-provided UI/API | 요약 리포트, 위험 신호 알림, 권장 액션 | 대화 원문 기본 미노출, 공유 동의 필수 |
| Google Gemini API | Outbound/Inbound | Vercel AI SDK provider call | 비식별 대화 텍스트, 요약 맥락, 정책 메타데이터, 생성 응답 | 학습 사용 금지, 보관 제한, 최소 데이터, 환경 변수 기반 모델 교체 |
| Vercel Platform | Deployment/Runtime | Git Integration, Serverless/Edge runtime, logs | Next.js App Router, Route Handlers, Server Actions, deployment log, runtime log | 별도 CI/CD 없이 Git Push 자동 배포, server-only secret 관리 |
| 운영 모니터링 시스템 | Outbound | Vercel log/metrics and application audit log | endpoint latency, error rate, token usage, timeout, safety policy rate | 원문/PII 마스킹, 감사 로그 |
| 데이터 저장소 | Internal | Prisma ORM, local SQLite, Supabase PostgreSQL | elder profile, consent, memory summary, analysis result, risk event, usage aggregate | 로컬 개발은 SQLite, 배포/PoC는 Supabase PostgreSQL, AES-256 수준 저장 암호화, retention, cascade deletion |
| Phase B 센서 제공 기기 | Inbound | `/api/v2/sensor/ingest` | sleep, movement, blood pressure or partner sensor payload | Phase B, schema 확정 필요 |
| Partner Webhook Target | Outbound | HTTPS Webhook | `risk_level`, `risk_reason`, `recommended_action`, `request_id`, `detected_at` | retry policy, signature, tenant authorization |

### 3.2 Client Applications

| Client Application | Owner | User | Scope |
|---|---|---|---|
| SilverCare Partner Console | 실버케어 | B2B-DEV, B2B-PM, 플랫폼 운영자 | API 연동, 문서, Playground, sandbox, PoC report, 운영 로그, 사용자/동의 상태 관리를 제공하는 웹 콘솔 |
| Developer Portal | 실버케어 | B2B-DEV | API Key 발급/폐기, Swagger 문서, enum, 샘플 요청/응답, 오류 코드 표 제공 |
| Web API Playground | 실버케어 | B2B-DEV, B2B-PM | sandbox mode에서 주요 API request를 구성·실행하고 JSON 응답, status code, latency, `request_id`를 확인 |
| Sandbox Console | 실버케어 | B2B-DEV | sample elder profile과 샘플 발화로 정상/안전/오류 응답 재현 |
| PoC Report Console | 실버케어 | B2B-PM | 기간 필터, 활성 디바이스, 대화 수, p95 latency, 오류율, 위험 이벤트 수, CSV/JSON export 제공 |
| Ops Monitoring Console | 실버케어 | 플랫폼 운영자 | 비식별 로그, 지연시간, 오류 코드, 안전 정책 발동 여부, 운영 알림, 위험 이벤트 리뷰 큐 확인 |
| User & Consent Admin View | 실버케어 | 플랫폼 운영자, 권한 있는 B2B 운영 담당자 | `elder_id`, `device_id`, 동의 상태, memory enabled, 삭제 요청 job 상태를 제한된 권한으로 조회 |
| B2B Partner Device | B2B 파트너 | 어르신 | STT/TTS, `/api/v1/chat/reply` 호출, `reply_text` 음성 발화 |
| B2B Partner Backend | B2B 파트너 | B2B-PM, 보호자, 기관 관리자 | report API 호출, Webhook 수신, 파트너 UI 제공 |

### 3.3 API Overview

| API / Feature | Method | Endpoint | Phase | Priority | Summary |
|---|---|---|---|---|---|
| SilverCare Partner Console | Web UI | N/A | Phase A | Must | API Key 관리, API Docs, Web API Playground, Sandbox Console, PoC Report Console, Ops Monitoring, User/Consent Admin 제공 |
| Developer Portal / Sandbox | Web UI | N/A | Phase A | Must | API Key 발급/폐기, Swagger, 샘플 코드, sandbox elder profile, 표준 오류 예제 제공 |
| Chat Reply | POST | `/api/v1/chat/reply` | Phase A | Must | 어르신 발화 텍스트를 받아 응답 텍스트, 정서/위험 태그, 권장 액션 반환 |
| Emotion Analysis | POST | `/api/v1/analyze/emotion` | Phase A | Must | 대화 텍스트 또는 요약을 분석하여 `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action` 반환 |
| Proactive Schedule | POST | `/api/v1/schedule/proactive` | Phase A | Should | 기상, 식사, 수면, 안부 확인 등 선제적 일반 발화 생성 |
| PoC Report | GET | `/api/v1/report/poc` | Phase A | Must | 파트너 단위 PoC 집계 리포트 JSON/CSV 제공 |
| Sensor Ingest | POST | `/api/v2/sensor/ingest` | Phase B | Should | 레이더, 웨어러블, 혈압계 등 센서 데이터를 수신해 시계열 저장 |
| Emergency Alert | POST | `/api/v2/alert/emergency` and outbound Webhook | Phase B | Should | 대화 위험 신호와 센서 무활동 결합 시 보호자/기관 대상 알림 이벤트 발송 |
| KPI Report | GET | `/api/v2/report/kpi` | Phase B | Should | 주간 정서 리포트, 수면 분석 결과, 기관용 성과 데이터 JSON 제공 |

모든 API는 다음 공통 계약을 따라야 한다.

| Contract Item | Requirement |
|---|---|
| Implementation Boundary | Phase A MVP의 API endpoint는 Next.js Route Handlers로 구현하고, 내부 Web UI mutation은 Server Actions로 구현해야 한다. |
| Authentication | 모든 API 요청은 API Key 또는 동등한 서버 간 인증을 사용해야 한다. |
| Tenant Isolation | 모든 요청은 `tenant_id` 기준으로 데이터 접근 범위를 제한해야 한다. |
| Schema Version | 주요 API 응답은 `schema_version`을 포함해야 한다. |
| Request Tracing | 모든 성공/오류 응답은 `request_id`를 포함해야 한다. |
| Error Response | 오류 응답은 `error_code`, `message`, `request_id`, `retryable`을 포함해야 한다. |
| Privacy Default | 응답에는 기본적으로 대화 원문과 직접 식별정보를 포함하지 않아야 한다. |
| Safety Default | 의료, 처방, 응급, 자해 관련 발화는 진단/처방 금지와 안전 권고를 적용해야 한다. |

### 3.4 Interaction Sequences

#### Core Phase A Chat and Risk Tagging Sequence

```mermaid
sequenceDiagram
    participant Elder as 어르신
    participant Device as B2B 파트너 기기
    participant API as 실버케어 API 서버
    participant Consent as 동의/권한 서비스
    participant Memory as 메모리 저장소
    participant AI as AI 대화/분석 엔진
    participant Report as 리포트/알림 서비스
    participant Ops as 운영 모니터링

    Elder->>Device: 음성 발화
    Device->>Device: STT 처리
    Device->>API: POST /api/v1/chat/reply
    API->>API: API Key, tenant_id, rate limit 검증
    API->>Consent: elder_id별 메모리/민감정보 동의 확인
    alt 메모리 사용 동의 있음
        API->>Memory: 최근 요약 메모리 조회
        Memory-->>API: memory_summary
    else 동의 없음 또는 기록 없음
        API-->>API: memory_used=false로 처리
    end
    API->>AI: 비식별 맥락, 발화, 안전 정책 전달
    AI-->>API: reply_text, emotion_tags, risk_level, recommended_action
    API->>API: 의료/응급/자해 안전 정책 후처리
    API-->>Device: JSON response with request_id
    Device->>Device: TTS 처리
    Device-->>Elder: 응답 음성 발화
    API->>Report: risk_event와 요약 이벤트 저장
    API->>Ops: 마스킹 로그, latency, error, safety_policy_applied 전송
```

#### Core PoC Reporting Sequence

```mermaid
sequenceDiagram
    participant PM as B2B-PM
    participant Partner as 파트너 서버
    participant Report as 실버케어 Report API
    participant Usage as 사용량/성능 집계 저장소
    participant Privacy as 개인정보 필터

    PM->>Partner: PoC 성과 조회
    Partner->>Report: GET /api/v1/report/poc?tenant_id&from&to
    Report->>Report: API Key, tenant_id 권한 검증
    Report->>Usage: 활성 어르신, 활성 디바이스, 호출 수, latency, 오류율, 위험 이벤트 조회
    Usage-->>Report: aggregate metrics
    Report->>Privacy: PII/원문 제거 검증
    Privacy-->>Report: sanitized aggregate
    Report-->>Partner: JSON 또는 CSV 집계 리포트
    Partner-->>PM: PoC KPI와 과금 검증 지표 표시
```

## 4. Specific Requirements

### 4.1 Functional Requirements

| Requirement ID | Requirement | Source | Priority | Acceptance Criteria (Given / When / Then) | Test Case ID |
|---|---|---|---|---|---|
| REQ-FUNC-001 | 시스템은 유효한 파트너 계정에 대해 Developer Portal에서 API Key 발급 기능을 제공해야 한다. | Story B2B-DEV AC-1; F0 | Must | Given 유효한 파트너 계정, When 개발자가 Developer Portal에 접속해 Key 발급을 요청, Then tenant에 연결된 API Key가 발급되고 발급 이벤트가 감사 로그에 기록된다. | TC-FUNC-001 |
| REQ-FUNC-002 | 시스템은 Developer Portal에서 Swagger 문서, cURL/TypeScript 샘플 요청/응답, 표준 오류 코드 표를 제공해야 한다. | Story B2B-DEV AC-1; F0 | Must | Given 유효한 파트너 계정, When 개발자가 문서 페이지를 열람, Then `/api/v1/chat/reply`, `/api/v1/analyze/emotion`, `/api/v1/report/poc`의 샘플과 오류 코드 표를 확인할 수 있다. | TC-FUNC-002 |
| REQ-FUNC-003 | 시스템은 sandbox elder profile과 샘플 발화를 사용하여 정상 응답, 안전 응답, 오류 응답을 실제 개인정보 없이 재현해야 한다. | Story B2B-DEV AC-4; F0 | Must | Given sandbox 환경, When 개발자가 제공된 샘플 발화를 실행, Then 정상 200 응답, 안전 정책 적용 200 응답, 표준 오류 응답이 각각 재현된다. | TC-FUNC-003 |
| REQ-FUNC-004 | 모든 API는 API Key 또는 동등한 서버 간 인증을 요구하고 인증 실패 시 `UNAUTHORIZED` 오류를 반환해야 한다. | Story B2B-DEV AC-3; API 공통 계약 | Must | Given API Key 누락 또는 invalid key, When API를 호출, Then HTTP 401과 `error_code=UNAUTHORIZED`, `request_id`, `retryable=false`가 반환된다. | TC-FUNC-004 |
| REQ-FUNC-005 | 모든 API는 `tenant_id` 기준으로 데이터 접근 범위를 제한하고 tenant 불일치 시 `TENANT_ACCESS_DENIED` 오류를 반환해야 한다. | Story C AC-16; API 공통 계약 | Must | Given 인증된 파트너가 다른 tenant의 리소스를 요청, When API가 권한을 검사, Then HTTP 403과 `TENANT_ACCESS_DENIED`를 반환하고 데이터 payload를 반환하지 않는다. | TC-FUNC-005 |
| REQ-FUNC-006 | 시스템은 파트너/테넌트 단위 rate limit을 적용하고 초과 시 `RATE_LIMIT_EXCEEDED` 오류를 반환해야 한다. | Story B2B-DEV AC-3; API 공통 계약 | Must | Given tenant가 설정된 rate limit을 초과, When API를 추가 호출, Then HTTP 429, `retryable=true`, `retry_after_seconds`가 포함된다. | TC-FUNC-006 |
| REQ-FUNC-007 | `/api/v1/chat/reply`는 `tenant_id`, `device_id`, `elder_id`, `utterance`를 필수 요청 필드로 검증해야 한다. | Story B2B-DEV AC-2; F1 | Must | Given 필수 필드 중 하나가 누락, When `/api/v1/chat/reply` 호출, Then HTTP 400과 `INVALID_REQUEST`를 반환한다. | TC-FUNC-007 |
| REQ-FUNC-008 | `/api/v1/chat/reply`는 성공 시 `schema_version`, `reply_text`, `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action`, `memory_used`, `request_id`, `safety_policy_applied`를 포함한 JSON을 반환해야 한다. | Story B2B-DEV AC-2; F1 | Must | Given 유효한 요청, When `/api/v1/chat/reply` 호출, Then 응답 JSON에 모든 필수 필드가 존재하고 schema validation을 통과한다. | TC-FUNC-008 |
| REQ-FUNC-009 | `/api/v1/chat/reply`는 어르신 발화에 대해 쉬운 한국어 존댓말과 공감 중심의 응답을 생성해야 한다. | Story A AC-11; F1 | Must | Given 사투리, 불완전 문장, 반복 발화, When API가 응답 생성, Then 의미를 단정하지 않고 쉬운 존댓말로 되묻거나 공감하며 평가 루브릭 평균 4.0/5 이상을 충족한다. | TC-FUNC-009 |
| REQ-FUNC-010 | 시스템은 메모리 저장 동의가 있는 경우에만 최근 대화 요약 또는 중요 메모리를 개인화 응답에 사용해야 한다. | Story A AC-8; F1; ADR-004 | Must | Given `memory_storage=true`와 기존 요약 메모리, When 개인화 응답 생성, Then 이전 맥락을 반영하고 `memory_used=true`를 반환한다. | TC-FUNC-010 |
| REQ-FUNC-011 | 시스템은 이전 대화 기록이 없거나 메모리 저장 동의가 없는 경우 존재하지 않는 과거 대화를 생성하지 않아야 한다. | Story A AC-9; F1; ADR-004 | Must | Given 메모리 기록 없음 또는 `memory_storage=false`, When 개인화 응답 생성, Then 일반 안부 대화로 응답하고 `memory_used=false`를 반환한다. | TC-FUNC-011 |
| REQ-FUNC-012 | `/api/v1/chat/reply`는 통증, 약, 병원, 자해, 응급 관련 발화에 대해 의료 판단·처방을 금지하고 가족/의료진/긴급 연락을 권하는 안전 응답을 반환해야 한다. | Story A AC-10; F1; ADR-003 | Must | Given 의료/응급/자해 발화, When chat reply 호출, Then 진단/처방 문구 없이 안전 응답, `risk_level`, `recommended_action`, `safety_policy_applied=true`를 반환한다. | TC-FUNC-012 |
| REQ-FUNC-013 | `/api/v1/chat/reply`는 응답 생성 결과에 `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action`을 함께 반환해야 한다. | Story B2B-DEV AC-2; Story A AC-10; F1/F2 | Must | Given 유효한 발화, When chat reply 호출, Then 분석 필드가 enum 정의에 맞게 채워진다. | TC-FUNC-013 |
| REQ-FUNC-014 | 모든 성공·오류·알림·로그·리포트 항목은 동일한 `request_id`로 추적 가능해야 한다. | Story B2B-DEV AC-2/AC-3; Story D AC-18 | Must | Given 단일 API 요청, When 응답, 로그, 리포트 이벤트를 조회, Then 동일 `request_id`로 상호 연결된다. | TC-FUNC-014 |
| REQ-FUNC-015 | 오류 응답은 `error_code`, `message`, `request_id`, `retryable`을 필수 필드로 포함해야 한다. | Story B2B-DEV AC-3; API 공통 계약 | Must | Given 인증 실패, rate limit, 필드 누락, LLM timeout 중 하나 발생, When 오류 응답 반환, Then 표준 오류 schema를 충족한다. | TC-FUNC-015 |
| REQ-FUNC-016 | `/api/v1/analyze/emotion`은 `tenant_id`, `elder_id`, `text`를 필수 요청 필드로 검증해야 한다. | Story B AC-12; F2 | Must | Given 필수 필드 누락, When analyze API 호출, Then HTTP 400과 `INVALID_REQUEST`를 반환한다. | TC-FUNC-016 |
| REQ-FUNC-017 | `/api/v1/analyze/emotion`은 `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action`, 선택적 `detected_keywords`를 반환해야 한다. | Story B AC-12/AC-13; F2 | Must | Given 분석 대상 텍스트, When analyze API 호출, Then 결과 필드가 enum 정의를 따르고 `detected_keywords`에는 직접 식별정보가 포함되지 않는다. | TC-FUNC-017 |
| REQ-FUNC-018 | 시스템은 `risk_level` enum을 `none`, `low`, `medium`, `high`, `critical`로 표준화해야 한다. | Story A AC-10; Story B AC-13; F2 | Must | Given 위험 분석 결과, When API가 `risk_level`을 반환, Then 정의된 enum 외 값이 반환되지 않는다. | TC-FUNC-018 |
| REQ-FUNC-019 | 시스템은 `recommended_action` enum을 `none`, `empathetic_response`, `check_in`, `notify_guardian`, `suggest_medical_contact`, `suggest_emergency_contact`, `human_review`로 표준화해야 한다. | Story B AC-13; F2 | Must | Given 분석 결과, When API가 권장 액션을 반환, Then 정의된 enum 외 값이 반환되지 않는다. | TC-FUNC-019 |
| REQ-FUNC-020 | `/api/v1/schedule/proactive`는 `tenant_id`, `device_id`, `elder_id`, `trigger_type`을 필수 요청 필드로 검증해야 한다. | Story A AC-8; F3 | Should | Given `trigger_type` 누락 또는 지원하지 않는 값, When proactive API 호출, Then `INVALID_REQUEST`를 반환한다. | TC-FUNC-020 |
| REQ-FUNC-021 | `/api/v1/schedule/proactive`는 `morning`, `meal`, `sleep`, `check_in`, `custom` 트리거에 대해 일반 안부/확인성 발화를 생성하고 복약 지시 또는 처방을 생성하지 않아야 한다. | Story A AC-8; F3 | Should | Given 식사 또는 수면 트리거, When proactive API 호출, Then 일반 안부/확인성 발화를 반환하고 의료 지시 문구가 없다. | TC-FUNC-021 |
| REQ-FUNC-022 | `/api/v1/report/poc`는 지정 기간의 `active_devices`, `active_elders`, `conversation_count`, `avg_conversation_per_elder`, `p95_latency_ms`, `error_rate`, `risk_event_count`를 반환해야 한다. | Story B2B-PM AC-5; F7 | Must | Given PoC 기간 데이터, When report API 호출, Then 필수 집계 필드가 JSON으로 반환된다. | TC-FUNC-022 |
| REQ-FUNC-023 | `/api/v1/report/poc`는 개인 식별정보를 제외한 집계 리포트를 JSON 또는 CSV 형태로 제공해야 한다. | Story B2B-PM AC-6; F7 | Must | Given PoC 종료 후 성과 데이터 요청, When JSON 또는 CSV를 요청, Then 원문/PII 없이 집계값만 반환된다. | TC-FUNC-023 |
| REQ-FUNC-024 | 시스템은 인당 일평균 대화 횟수, p95 latency, 위험 징후 탐지 결과, 월 활성 디바이스 수, 예상 과금 기준을 tenant 단위로 비교 가능하게 산출해야 한다. | Story B2B-PM AC-7; F7 | Must | Given PoC metrics, When B2B-PM이 지표를 조회, Then 목표값 대비 결과와 활성 디바이스/과금 검증 지표가 tenant 단위로 제공된다. | TC-FUNC-024 |
| REQ-FUNC-025 | 시스템은 해당 월 1회 이상 유효한 대화 API 호출을 발생시킨 `device_id`를 월 활성 디바이스로 집계해야 한다. | Story B2B-PM AC-7; 과금 단위 정의 | Must | Given 동일 월 내 device calls, When 월간 집계 실행, Then 중복 없는 활성 `device_id` 수가 산출된다. | TC-FUNC-025 |
| REQ-FUNC-026 | 시스템은 기본 제공량 초과 사용량 산출을 위해 대화 생성, 감정 분석, 리포트 API 호출량을 tenant·endpoint·device 기준으로 기록해야 한다. | Story B2B-PM AC-7; 과금 단위 정의 | Must | Given billable API 호출, When usage event 저장, Then tenant, endpoint, device, timestamp가 누락 없이 기록된다. | TC-FUNC-026 |
| REQ-FUNC-027 | 보호자 리포트 API 또는 리포트 데이터는 정보 공유 동의가 있을 때 대화 원문이 아닌 주요 정서 추세, 반복 주제, 위험 신호 요약, 권장 확인 액션을 반환해야 한다. | Story B AC-12; F6/ADR-005 | Should | Given guardian sharing consent, When 보호자 리포트 조회, Then 원문 없이 요약·추세·위험 신호·권장 액션이 반환된다. | TC-FUNC-027 |
| REQ-FUNC-028 | 고위험 발화 또는 반복 위험 신호가 감지되고 알림 조건이 충족되면 시스템은 보호자 또는 파트너 서버로 알림 이벤트를 전송해야 한다. | Story B AC-13; F5 | Should | Given `risk_level=high` 또는 `critical` 또는 반복 medium, When 알림 조건 충족, Then `risk_level`, `risk_reason`, `recommended_action`, `request_id`, `detected_at`을 포함한 이벤트가 전송된다. | TC-FUNC-028 |
| REQ-FUNC-029 | 보호자가 원문 열람 권한이 없는 경우 리포트 API는 대화 원문과 민감 개인정보를 반환하지 않아야 한다. | Story B AC-14; ADR-005 | Must | Given guardian lacks raw transcript permission, When report API 호출, Then raw utterance, AI reply 원문, 민감 PII 필드가 응답에 없다. | TC-FUNC-029 |
| REQ-FUNC-030 | 기관 리포트 API 또는 데이터는 소속 어르신별 대화 빈도, 최근 위험 신호, 정서 추세, 마지막 상호작용 시각을 목록으로 반환해야 한다. | Story C AC-15; F6 | Should | Given institution membership exists, When institution report 조회, Then elder별 요약 목록이 반환된다. | TC-FUNC-030 |
| REQ-FUNC-031 | 기관 관리자의 특정 어르신 상세 요청에서 소속/권한 검사가 실패하면 시스템은 대화 요약과 위험 신호 데이터를 반환하지 않아야 한다. | Story C AC-16; RBAC | Must | Given unauthorized institution user, When elder detail requested, Then HTTP 403과 no data payload를 반환한다. | TC-FUNC-031 |
| REQ-FUNC-032 | 기관 리포트는 `risk_level`, 최근 발생 시각, 반복 횟수 기준으로 위험 이벤트 우선순위 정렬을 지원해야 한다. | Story C AC-17; F6 | Should | Given 다수 위험 이벤트, When sort option을 지정, Then 정의된 기준으로 정렬된 목록이 반환된다. | TC-FUNC-032 |
| REQ-FUNC-033 | 운영 로그 조회 화면 또는 로그 API는 원문 PII를 마스킹하고 `tenant_id`, `request_id`, latency, error code, safety policy status를 표시해야 한다. | Story D AC-18; 운영 모니터링 | Must | Given 운영자가 로그 조회, When 로그 항목 표시, Then 원문 PII가 마스킹되고 필수 운영 필드가 확인된다. | TC-FUNC-033 |
| REQ-FUNC-034 | 특정 tenant의 오류율 또는 지연시간이 기준치를 초과하면 시스템은 운영자에게 tenant, endpoint, 시간 범위, 오류 유형을 포함한 알림을 보내야 한다. | Story D AC-19; 운영 모니터링 | Must | Given threshold breach, When monitoring detects breach, Then 운영자 알림 payload에 영향 범위와 오류 유형이 포함된다. | TC-FUNC-034 |
| REQ-FUNC-035 | 위험 징후 오탐/미탐 리뷰 데이터 추출 시 시스템은 개인정보를 제거한 평가용 데이터셋과 라벨링 결과를 분리 저장해야 한다. | Story D AC-20; AI 품질 검증 | Must | Given 운영자가 리뷰 데이터 추출, When dataset 생성, Then PII가 제거되고 sample data와 labeling result가 별도 저장된다. | TC-FUNC-035 |
| REQ-FUNC-036 | 시스템은 기본 대화 처리, 민감정보 처리, 메모리 저장, 보호자 공유, 기관 공유, 품질 개선/평가셋 사용 동의 상태를 별도 필드로 관리해야 한다. | Story A AC-8/AC-9; Story B AC-12; 개인정보 동의 | Must | Given elder consent profile, When 동의 상태 조회, Then 6개 동의 항목이 독립적으로 확인된다. | TC-FUNC-036 |
| REQ-FUNC-037 | 파트너가 `use_memory=true`를 요청해도 서버는 동의 상태를 최종 판단하고 동의가 없으면 메모리를 사용하지 않아야 한다. | Story A AC-9; 스키마 결정 원칙 | Must | Given `use_memory=true` and no memory consent, When chat reply 호출, Then `memory_used=false`를 반환하고 memory store를 조회하지 않는다. | TC-FUNC-037 |
| REQ-FUNC-038 | 삭제 요청 또는 동의 철회 시 시스템은 대화 원문, 요약 메모리, 감정/위험 태그, 리포트, 평가셋 후보, 캐시, 검색 인덱스, 백업 삭제 대상에 삭제 전파 작업을 생성해야 한다. | Story D AC-20; 개인정보 보존/삭제 | Must | Given deletion request, When cascade deletion job starts, Then 각 대상 데이터 유형별 삭제 또는 분리 보존 상태가 추적된다. | TC-FUNC-038 |
| REQ-FUNC-039 | 시스템은 역할별 접근 권한을 적용하여 어르신, 보호자, 기관 관리자, B2B 파트너, 실버케어 운영자, 외부 수탁자의 접근 가능 정보를 제한해야 한다. | Story B AC-14; Story C AC-16; 개인정보 역할별 접근 | Must | Given role-specific credentials, When restricted data requested, Then 허용된 데이터만 반환되고 접근 로그가 남는다. | TC-FUNC-039 |
| REQ-FUNC-040 | `/api/v2/sensor/ingest`는 Phase B에서 tenant, device, elder 또는 동등 식별자와 센서 payload를 받아 시계열 저장소에 저장해야 한다. | Story C AC-15; F4 | Should | Given valid sensor payload, When sensor ingest 호출, Then tenant 격리된 시계열 레코드가 생성된다. | TC-FUNC-040 |
| REQ-FUNC-041 | `/api/v2/alert/emergency`는 Phase B에서 대화 위험 신호와 센서 무활동 데이터를 결합해 고위험으로 판단된 경우 지정된 보호자/기관 서버로 Webhook 알림을 발송해야 한다. | Story B AC-13; Story C AC-17; F5 | Should | Given high conversation risk and inactivity sensor signal, When alert evaluation runs, Then webhook alert is sent with risk fields and request/event IDs. | TC-FUNC-041 |
| REQ-FUNC-042 | `/api/v2/report/kpi`는 Phase B에서 주간 정서 리포트, 수면 분석 결과, 기관용 성과 데이터를 JSON 형태로 제공해야 한다. | Story B AC-12; Story C AC-15; Story B2B-PM AC-7; F6 | Should | Given Phase B data exists, When KPI report requested, Then weekly sentiment, sleep analysis, institution performance fields are returned as JSON. | TC-FUNC-042 |
| REQ-FUNC-043 | 주요 API 응답은 `schema_version`을 포함하고 enum 값 추가·변경 시 하위 호환성을 유지해야 한다. | Story B2B-DEV AC-2; API 공통 계약 | Must | Given API response, When schema validation runs, Then `schema_version` exists; enum extension does not break existing known values. | TC-FUNC-043 |
| REQ-FUNC-044 | 위험 발화 자체는 기본적으로 `SAFETY_FILTERED` 오류가 아니라 안전 응답 정책이 적용된 200 응답으로 처리해야 한다. | Story A AC-10; 오류 코드 표준 | Must | Given elder emergency utterance, When chat reply 호출, Then HTTP 200, safety response, `risk_level=critical`이 반환된다. | TC-FUNC-044 |
| REQ-FUNC-045 | `SAFETY_FILTERED`는 API 오남용, 정책 위반 입력, 프롬프트 주입 시도 등 처리 거부 상황에만 사용해야 한다. | Story D AC-20; 오류 코드 표준 | Must | Given prompt injection input, When API detects policy violation, Then HTTP 422 and `SAFETY_FILTERED` are returned. | TC-FUNC-045 |
| REQ-FUNC-046 | `/api/v2/sensor/ingest`는 `sensor_type`, `observed_at`, `payload`의 필수성 및 지원 schema 여부를 검증해야 한다. | Story C AC-15; F4 | Should | Given 누락되었거나 지원하지 않는 sensor payload, When sensor ingest 호출, Then `INVALID_REQUEST`를 반환하고 시계열 레코드를 생성하지 않는다. | TC-FUNC-046 |
| REQ-FUNC-047 | `/api/v2/sensor/ingest`는 수신된 센서 데이터를 `tenant_id`, `device_id`, `elder_id` 또는 동등한 파트너 식별자와 연결하고 cross-tenant 결합을 금지해야 한다. | Story C AC-15; F4 | Should | Given 다른 tenant의 device 또는 elder 식별자가 포함된 요청, When sensor ingest 권한 검증, Then `TENANT_ACCESS_DENIED`를 반환한다. | TC-FUNC-047 |

#### 4.1.1 Web Console Functional Requirements

| Requirement ID | Requirement | Source | Priority | Acceptance Criteria (Given / When / Then) | Test Case ID |
|---|---|---|---|---|---|
| REQ-FUNC-048 | SilverCare Partner Console은 인증된 사용자에게 현재 `tenant_id`, 역할, 접근 가능한 모듈을 표시해야 한다. | Story B2B-DEV AC-1; Story D AC-18 | Must | Given 인증된 사용자가 콘솔에 접속, When 홈 화면이 로드, Then 현재 tenant context, user role, 허용 모듈 목록이 표시된다. | TC-FUNC-048 |
| REQ-FUNC-049 | Developer Portal은 API Key 발급, 폐기, 상태, 마지막 사용 시각을 관리할 수 있어야 하며 원문 API Key는 최초 발급 시 1회만 표시해야 한다. | Story B2B-DEV AC-1; F0 | Must | Given 권한 있는 개발자, When API Key를 발급 또는 폐기, Then 상태 변경과 마지막 사용 시각이 표시되고 원문 key는 재표시되지 않으며 감사 로그가 기록된다. | TC-FUNC-049 |
| REQ-FUNC-050 | Developer Portal은 Swagger 문서, enum 정의, 표준 오류 코드, cURL/TypeScript 샘플 코드를 조회할 수 있어야 한다. | Story B2B-DEV AC-1; F0 | Must | Given 개발자가 API Docs를 열람, When endpoint를 선택, Then request/response schema, enum, error code, cURL/TypeScript sample이 표시된다. | TC-FUNC-050 |
| REQ-FUNC-051 | Web API Playground는 `/api/v1/chat/reply`, `/api/v1/analyze/emotion`, `/api/v1/schedule/proactive`, `/api/v1/report/poc` endpoint를 선택해 sandbox mode로 실행할 수 있어야 한다. | Story B2B-DEV AC-2; Story B2B-DEV AC-4; F0/F1/F2/F3/F7 | Must | Given sandbox 권한이 있는 사용자, When Playground에서 endpoint를 선택하고 실행, Then sandbox tenant 기준으로 API 호출이 수행된다. | TC-FUNC-051 |
| REQ-FUNC-052 | Web API Playground는 endpoint별 request builder를 제공하고 필수 필드 누락, 지원하지 않는 enum, 잘못된 타입을 실행 전 표시해야 한다. | Story B2B-DEV AC-2/AC-3; API 공통 계약 | Must | Given 사용자가 request field를 입력, When 필수 필드가 누락되거나 enum이 잘못됨, Then UI는 실행 전 validation error를 표시하고 호출을 전송하지 않는다. | TC-FUNC-052 |
| REQ-FUNC-053 | Sandbox Console은 합성 sample elder profile, sample utterance, 정상 응답, 안전 응답, 오류 응답 시나리오를 선택할 수 있어야 한다. | Story B2B-DEV AC-4; F0 | Must | Given sandbox 환경, When 사용자가 sample profile과 scenario를 선택, Then 실제 개인정보 없이 해당 시나리오의 request payload가 구성된다. | TC-FUNC-053 |
| REQ-FUNC-054 | Web API Playground는 실행 결과로 JSON response, HTTP status, latency, `request_id`, `risk_level`, `safety_policy_applied`, 오류 응답 필드를 표시해야 한다. | Story B2B-DEV AC-2/AC-3; Story A AC-10 | Must | Given Playground API 실행 완료, When response viewer가 표시, Then JSON payload, status, latency, request_id, safety fields 또는 error fields가 표시된다. | TC-FUNC-054 |
| REQ-FUNC-055 | Web API Playground는 현재 request payload를 기준으로 cURL 및 TypeScript snippet을 생성해야 한다. | Story B2B-DEV AC-1; F0 | Must | Given 유효한 request payload, When 사용자가 snippet 생성을 선택, Then 동일 endpoint와 payload를 사용하는 cURL 및 TypeScript sample이 생성된다. | TC-FUNC-055 |
| REQ-FUNC-056 | PoC Report Console은 기간 필터와 tenant 기준으로 활성 디바이스, 활성 어르신, 대화 수, p95 latency, 오류율, 위험 이벤트 수를 표시하고 CSV/JSON export를 제공해야 한다. | Story B2B-PM AC-5/AC-6/AC-7; F7 | Must | Given 권한 있는 B2B-PM, When 기간을 선택해 리포트를 조회, Then 필수 PoC 지표가 표시되고 원문/PII 없이 CSV 또는 JSON으로 export된다. | TC-FUNC-056 |
| REQ-FUNC-057 | Ops Monitoring Console은 request log, endpoint, tenant, latency, error code, safety policy status, alert status를 필터링해 조회할 수 있어야 한다. | Story D AC-18/AC-19; 운영 모니터링 | Must | Given 플랫폼 운영자, When 로그 조건을 필터링, Then 원문 PII가 마스킹된 로그와 운영 상태 필드가 표시된다. | TC-FUNC-057 |
| REQ-FUNC-058 | Ops Monitoring Console은 `high`, `critical`, `SAFETY_FILTERED`, LLM timeout, 보호자/기관 신고 케이스의 리뷰 큐를 표시해야 한다. | Story D AC-20; AI 품질 검증 | Must | Given 리뷰 대상 이벤트가 존재, When 운영자가 리뷰 큐를 조회, Then request_id, risk_level, review_status, 마스킹된 근거 데이터가 표시된다. | TC-FUNC-058 |
| REQ-FUNC-059 | User & Consent Admin View는 `elder_id`, `device_id`, 동의 상태, memory enabled, 삭제 요청 job 상태를 권한 있는 사용자에게만 표시해야 한다. | Story A AC-8/AC-9; Story D AC-20; 개인정보 동의/삭제 정책 | Must | Given 권한 있는 사용자, When elder 또는 device를 조회, Then 직접 식별정보 없이 가명 ID, 동의 상태, 메모리 상태, 삭제 job 상태가 표시된다. | TC-FUNC-059 |
| REQ-FUNC-060 | SilverCare Partner Console은 보호자용 완성 앱, 기관용 완성 앱, 어르신용 앱 UI를 제공하지 않아야 하며 해당 UI 책임은 B2B 파트너 또는 별도 Phase로 남겨야 한다. | PRD Out-of-Scope; ADR-002 | Must | Given 콘솔 기능 범위 검토, When UI route와 navigation을 확인, Then guardian app, institution dashboard, elder app 목적의 화면이 포함되지 않는다. | TC-FUNC-060 |

### 4.2 Non-Functional Requirements

| Requirement ID | Category | Requirement | Target / Metric | Source | Verification |
|---|---|---|---|---|---|
| REQ-NF-001 | Performance | `/api/v1/chat/reply`는 비LLM 서버 처리 구간과 LLM 생성 구간의 지연시간을 분리 측정해야 한다. Phase A PoC에서는 비LLM 처리 p95 800ms 이하, LLM 포함 전체 완료 p95 5초 이하를 baseline으로 하고, production target은 비LLM 처리 p99 1.5초 이하 및 LLM 첫 응답 시작 p95 1.5초 이하로 둔다. | Non-LLM p95 <= 800ms; LLM total p95 <= 5s in PoC; first response start p95 <= 1.5s production target | PRD KPI, 5-1, AC-2; MVP efficiency review | Load test, LLM latency metrics |
| REQ-NF-002 | Availability | API 서버는 production GA 기준 월간 업타임 SLA 99.9%를 목표로 해야 한다. Phase A PoC에서는 Vercel/Supabase 상태와 application health check를 기준으로 월간 측정 가용성 99.0% 이상을 baseline으로 운영해야 한다. | PoC measured availability >= 99.0%; production target >= 99.9% | PRD 5-1; MVP efficiency review | Uptime monitoring report |
| REQ-NF-003 | Availability / DR | Phase A PoC의 API 및 주요 운영 데이터 저장소는 RPO 24시간 이하, RTO 4시간 이하를 운영 baseline으로 가져야 한다. 해당 수치는 PRD v0.5-단일구조 미확정 항목이므로 production GA 전 승인되어야 한다. | RPO <= 24h, RTO <= 4h | PRD 5-3 backup policy; ASSUMP-002 | Restore drill, runbook approval |
| REQ-NF-004 | Scalability | 단일구조 MVP는 첫 PoC 기준 50~200명 어르신 또는 계약된 PoC 대상 수의 일평균 대화 호출을 처리해야 한다. 10,000대 이상 동시 디바이스 처리는 Post-MVP scalability target으로 추적하고 production 확장 전 별도 부하 검증을 수행해야 한다. | MVP PoC target 50-200 elders; Post-MVP target >= 10,000 devices | PRD 5-1; MVP efficiency review | PoC load test; post-MVP load test plan |
| REQ-NF-005 | Observability | 시스템은 endpoint별 latency, error rate, token usage, LLM timeout, safety policy activation rate를 수집해야 한다. | 100% of API endpoints emit metrics | PRD 5-1 | Metrics coverage audit |
| REQ-NF-006 | Operations | 특정 tenant의 오류율 또는 지연시간이 기준치를 초과하면 운영자 알림이 5분 이내 생성되어야 한다. | Alert within 5 min of threshold breach | Story D AC-19 | Monitoring alert test |
| REQ-NF-007 | Developer Experience | B2B 개발자는 API Key, Swagger, 샘플 코드만으로 첫 성공 API 호출을 30분 이내 완료할 수 있어야 한다. | First successful call <= 30 min in PoC onboarding test | PRD KPI, MVP validation | Timed onboarding test |
| REQ-NF-008 | Usage KPI | 엔드유저 어르신의 일평균 대화 API 호출 횟수는 PoC 목표 기준 인당 5회 이상이어야 한다. | Avg chat calls per elder per day >= 5 | PRD KPI | PoC usage report |
| REQ-NF-009 | Business KPI | Phase A PoC에서 API 연동 B2B 파트너는 반기 기준 최소 3개사를 목표로 추적되어야 한다. | >= 3 PoC partners per half-year | PRD north star KPI | Partner pipeline report |
| REQ-NF-010 | Business KPI | PoC 종료 시 최소 1개 이상 파트너가 유료 전환 의향서 제출 또는 정식 계약 협의에 진입해야 한다. | >= 1 partner enters paid conversion discussion | PRD KPI | PoC closing report |
| REQ-NF-011 | AI Quality | Phase A MVP는 위험 징후 탐지 평가셋, 라벨 기준, 평가 실행 로그를 구축해야 하며, production readiness target으로 위험 징후 탐지 Recall 90% 이상을 추적해야 한다. | MVP evaluation pipeline exists; production target recall >= 90% | PRD KPI, 5-4; MVP efficiency review | Evaluation report |
| REQ-NF-012 | AI Safety | Phase A MVP는 `critical` 케이스 평가셋과 리뷰 큐를 구축해야 하며, production readiness target으로 `critical` 케이스 탐지 Recall 95% 이상을 추적해야 한다. | MVP critical evaluation set exists; production target critical recall >= 95% | PRD 5-4; MVP efficiency review | Evaluation report |
| REQ-NF-013 | AI Quality | Phase A MVP는 고위험 알림 오탐 리뷰 결과를 저장해야 하며, production readiness target으로 운영 리뷰 기준 고위험 알림 오탐률 10% 이하를 추적해야 한다. | MVP false-positive review log exists; production target false positive rate <= 10% | PRD KPI, 5-4; MVP efficiency review | Human review report |
| REQ-NF-014 | AI Safety | 의료/처방 금지 위반은 평가셋 및 PoC 운영 리뷰에서 0건이어야 한다. | 0 violations | PRD 5-4 | Safety test suite |
| REQ-NF-015 | AI Safety | 응급/자해/의료 고위험 케이스에서 안전 응답 정책 발동률은 100%이어야 한다. | Safety policy activation = 100% | PRD 5-4 | Safety test suite |
| REQ-NF-016 | Privacy | 응답, 로그, 리포트에서 불필요한 원문/PII 노출률은 0건이어야 한다. | 0 PII exposure incidents | PRD 5-4 | PII scanning and QA |
| REQ-NF-017 | Conversation Quality | 대화 자연스러움은 사람 평가자가 쉬운 존댓말, 공감, 과잉 단정 여부를 평가한 5점 척도에서 평균 4.0 이상이어야 한다. | Mean score >= 4.0/5 | PRD 5-4 | Human evaluation |
| REQ-NF-018 | Security | 모든 API 통신은 TLS 1.3 기반 HTTPS를 사용해야 한다. | TLS 1.3 enforced | PRD 5-2 | TLS configuration scan |
| REQ-NF-019 | Security | 저장 데이터는 AES-256 수준의 암호화를 적용해야 한다. | AES-256 or equivalent at rest | PRD 5-2 | Storage encryption audit |
| REQ-NF-020 | Security / Privacy | 대화 기록 저장 전 실명, 주민등록번호, 주소 등 민감 PII는 자동 마스킹되어야 한다. | PII masking precision/recall test; production PII leak count 0 | PRD 5-2, 5-3 | PII masking test |
| REQ-NF-021 | Security | 멀티테넌시 데이터는 `tenant_id` 기준으로 격리되어야 한다. | Cross-tenant access test pass rate 100% | PRD 5-2 | Access control tests |
| REQ-NF-022 | Security | RBAC는 역할별 최소 권한 원칙을 적용하고 모든 민감 데이터 접근을 감사 로그로 기록해야 한다. | 100% sensitive accesses audited | PRD 5-3 | RBAC and audit log test |
| REQ-NF-023 | Privacy | 동의는 기본 대화 처리, 민감정보 처리, 메모리 저장, 보호자 공유, 기관 공유, 품질 개선/평가셋 사용으로 분리 관리되어야 한다. | 6 consent dimensions independently stored | PRD 5-3 | Consent data test |
| REQ-NF-024 | Privacy | 대화 원문은 기본 30일 이내 또는 요약/리포트 생성 후 최소 기간만 보존해야 한다. | Raw transcript retention <= 30 days unless legally segregated | PRD 5-3 | Retention job audit |
| REQ-NF-025 | Privacy | 요약 메모리는 동의 유지 중 최대 180일까지만 보존하고 메모리 동의 철회 시 즉시 삭제되어야 한다. | Memory retention <= 180 days; deletion job created immediately on withdrawal | PRD 5-3 | Retention/deletion test |
| REQ-NF-026 | Privacy | 감정/위험 분석 결과는 최대 180일, 위험 이벤트는 최대 1년, PoC 집계 리포트와 마스킹 운영 로그는 최대 1년 보존해야 한다. | Retention per data type <= PRD limits | PRD 5-3 | Data lifecycle audit |
| REQ-NF-027 | Privacy | 평가셋 후보는 리뷰 완료 전 최대 30일 임시 보관하고 별도 동의 또는 가명/익명 처리 불가 시 폐기해야 한다. | Candidate retention <= 30 days | PRD 5-3 | Dataset retention audit |
| REQ-NF-028 | Privacy / Vendor | 외부 LLM/클라우드 제공자에게 원문 학습을 허용하지 않아야 하며 데이터 보관, 국외 이전, 삭제 가능성을 파트너와 정보주체에게 고지해야 한다. | Vendor learning disabled; disclosure checklist complete | PRD 5-3, R6 | Vendor configuration and contract review |
| REQ-NF-029 | Cost / FinOps | 모든 billable API 호출은 tenant, endpoint, device, timestamp, usage unit을 포함한 usage event를 남겨야 한다. | 100% billable calls have usage events | PRD 1-5 | Usage event reconciliation |
| REQ-NF-030 | Cost / FinOps | 월 활성 디바이스와 기본 제공량 초과 사용량은 월 단위로 재계산 가능해야 한다. | Monthly billing aggregate reproducible from raw usage events | PRD 1-5 | Billing replay test |
| REQ-NF-031 | Cost / FinOps | 정확한 원화 단가는 PRD v0.5-단일구조에서 확정하지 않으므로 시스템은 가격표 없이도 활성 디바이스·호출량·초과 사용 가능성 지표를 산출해야 한다. | No KRW price required for PoC report generation | PRD 1-5 | PoC report test |
| REQ-NF-032 | Maintainability | 모델 버전, 프롬프트 버전, 안전 정책 버전, 평가 결과는 변경 이력으로 함께 기록되어야 한다. | 100% AI releases have version/evaluation record | PRD 5-4 | Release checklist |
| REQ-NF-033 | Maintainability | enum 값 추가·변경은 하위 호환성과 파트너 공지를 전제로 배포되어야 한다. | No breaking enum removal without migration notice | PRD API 공통 계약 | Contract compatibility test |
| REQ-NF-034 | Deployment Gate | Vercel Git Integration 기반 preview deployment에서 안전 게이트, 위험 탐지 게이트, 오탐 게이트, 개인정보 게이트, 성능 게이트, 회귀 게이트를 모두 통과하지 못하면 production 또는 PoC 환경으로 promote하지 않아야 한다. | All gates pass before production/PoC promotion | PRD 5-4; C-TEC-007 | Vercel preview validation and release gate report |
| REQ-NF-035 | Review Loop | `high`/`critical`, `SAFETY_FILTERED`, LLM timeout, 보호자/기관 신고 케이스는 `request_id` 기준으로 운영 리뷰 대상에 수집되어야 한다. | 100% qualifying events queued for review | PRD 5-4 | Review queue audit |
| REQ-NF-036 | Web Security | SilverCare Partner Console의 모든 화면은 authenticated session과 role-based authorization을 요구해야 한다. | 100% protected routes require authentication and role check | PRD 5-2, 5-3 | Route authorization test |
| REQ-NF-037 | Web Privacy | Web Console의 대화 로그, 분석 결과, 리포트, 리뷰 큐는 기본적으로 원문과 직접 식별정보를 마스킹해야 한다. | PII/raw transcript exposure count = 0 in default UI | PRD 5-3, 5-4 | UI privacy scan |
| REQ-NF-038 | Web Auditability | API Key 발급/폐기, Playground 실행, 로그 조회, 동의 상태 조회, 삭제 job 조회는 감사 로그를 생성해야 한다. | 100% sensitive UI actions generate audit_log | Story D AC-18; PRD 5-3 | UI audit trail test |
| REQ-NF-039 | Web Sandbox Isolation | Web API Playground의 기본 실행 환경은 sandbox mode이어야 하며 production tenant 호출은 별도 권한을 요구해야 한다. | Sandbox default enforced; production run requires explicit permission | Story B2B-DEV AC-4 | Sandbox isolation test |
| REQ-NF-040 | Web Performance | Partner Console의 API Docs, Playground response viewer, PoC Report Console, Ops log query 화면은 p95 2초 이내에 초기 조회 결과를 표시해야 한다. | UI initial data load p95 <= 2s for baseline datasets | Developer Experience KPI | Browser performance test |
| REQ-NF-041 | Web Export Security | PoC Report CSV/JSON export는 원문/PII를 포함하지 않아야 하며 export 실행자, tenant, 기간, 파일 형식을 감사 로그에 기록해야 한다. | 0 PII in exports; 100% export actions audited | Story B2B-PM AC-6; PRD 5-3 | Export QA and audit test |
| REQ-NF-042 | LLM Cost Control | LLM 호출 기본 모델은 비용 효율 모델인 `gemini-2.5-flash-lite` 또는 동등한 저비용 모델이어야 하며, `gemini-2.5-flash` 등 상위 모델은 위험/불확실 케이스 또는 운영자가 승인한 케이스에만 사용해야 한다. | 100% LLM calls record selected model and routing reason | C-TEC-006; MVP efficiency review | LLM usage log audit |
| REQ-NF-043 | Token Budget | LLM 호출은 기본 입력 1,200 tokens 이하, 출력 350 tokens 이하를 목표로 제한해야 하며 초과가 필요한 경우 요약, truncation, 또는 명시적 policy override를 기록해야 한다. | p95 input <= 1,200 tokens; p95 output <= 350 tokens; 100% overrides logged | Cost efficiency review | Token usage metrics |
| REQ-NF-044 | Spend Guardrail | 시스템은 tenant별 호출량 quota, Web API Playground 일일 실행 제한, LLM retry 상한, Gemini/Vercel/Supabase 월 예산 알림 기준을 관리해야 한다. | Tenant quota exists; playground quota exists; budget alerts at 50%, 75%, 90% | Cost efficiency review | Quota and budget alert test |

## 5. Traceability Matrix

| Source Story / KPI | Requirement ID | Test Case ID |
|---|---|---|
| Story B2B-DEV AC-1 | REQ-FUNC-001, REQ-FUNC-002 | TC-FUNC-001, TC-FUNC-002 |
| Story B2B-DEV AC-2 | REQ-FUNC-007, REQ-FUNC-008, REQ-FUNC-013, REQ-FUNC-014, REQ-FUNC-043 | TC-FUNC-007, TC-FUNC-008, TC-FUNC-013, TC-FUNC-014, TC-FUNC-043 |
| Story B2B-DEV AC-3 | REQ-FUNC-004, REQ-FUNC-006, REQ-FUNC-015 | TC-FUNC-004, TC-FUNC-006, TC-FUNC-015 |
| Story B2B-DEV AC-4 | REQ-FUNC-003 | TC-FUNC-003 |
| Story B2B-PM AC-5 | REQ-FUNC-022 | TC-FUNC-022 |
| Story B2B-PM AC-6 | REQ-FUNC-023 | TC-FUNC-023 |
| Story B2B-PM AC-7 | REQ-FUNC-024, REQ-FUNC-025, REQ-FUNC-026, REQ-FUNC-042 | TC-FUNC-024, TC-FUNC-025, TC-FUNC-026, TC-FUNC-042 |
| Story A AC-8 | REQ-FUNC-010, REQ-FUNC-020, REQ-FUNC-021, REQ-FUNC-036 | TC-FUNC-010, TC-FUNC-020, TC-FUNC-021, TC-FUNC-036 |
| Story A AC-9 | REQ-FUNC-011, REQ-FUNC-037 | TC-FUNC-011, TC-FUNC-037 |
| Story A AC-10 | REQ-FUNC-012, REQ-FUNC-018, REQ-FUNC-044 | TC-FUNC-012, TC-FUNC-018, TC-FUNC-044 |
| Story A AC-11 | REQ-FUNC-009 | TC-FUNC-009 |
| Story B AC-12 | REQ-FUNC-016, REQ-FUNC-017, REQ-FUNC-027, REQ-FUNC-042 | TC-FUNC-016, TC-FUNC-017, TC-FUNC-027, TC-FUNC-042 |
| Story B AC-13 | REQ-FUNC-017, REQ-FUNC-019, REQ-FUNC-028, REQ-FUNC-041 | TC-FUNC-017, TC-FUNC-019, TC-FUNC-028, TC-FUNC-041 |
| Story B AC-14 | REQ-FUNC-029, REQ-FUNC-039 | TC-FUNC-029, TC-FUNC-039 |
| Story C AC-15 | REQ-FUNC-030, REQ-FUNC-040, REQ-FUNC-046, REQ-FUNC-047, REQ-FUNC-042 | TC-FUNC-030, TC-FUNC-040, TC-FUNC-046, TC-FUNC-047, TC-FUNC-042 |
| Story C AC-16 | REQ-FUNC-005, REQ-FUNC-031, REQ-FUNC-039 | TC-FUNC-005, TC-FUNC-031, TC-FUNC-039 |
| Story C AC-17 | REQ-FUNC-032, REQ-FUNC-041 | TC-FUNC-032, TC-FUNC-041 |
| Story D AC-18 | REQ-FUNC-014, REQ-FUNC-033 | TC-FUNC-014, TC-FUNC-033 |
| Story D AC-19 | REQ-FUNC-034 | TC-FUNC-034 |
| Story D AC-20 | REQ-FUNC-035, REQ-FUNC-038, REQ-FUNC-045 | TC-FUNC-035, TC-FUNC-038, TC-FUNC-045 |
| Story B2B-DEV AC-1 - Web Console | REQ-FUNC-048, REQ-FUNC-049, REQ-FUNC-050, REQ-FUNC-055 | TC-FUNC-048, TC-FUNC-049, TC-FUNC-050, TC-FUNC-055 |
| Story B2B-DEV AC-2/AC-3 - Web API Playground | REQ-FUNC-051, REQ-FUNC-052, REQ-FUNC-054 | TC-FUNC-051, TC-FUNC-052, TC-FUNC-054 |
| Story B2B-DEV AC-4 - Sandbox Console | REQ-FUNC-051, REQ-FUNC-053 | TC-FUNC-051, TC-FUNC-053 |
| Story B2B-PM AC-5/AC-6/AC-7 - PoC Report Console | REQ-FUNC-056 | TC-FUNC-056 |
| Story D AC-18/AC-19/AC-20 - Ops Console | REQ-FUNC-057, REQ-FUNC-058, REQ-FUNC-059 | TC-FUNC-057, TC-FUNC-058, TC-FUNC-059 |
| PRD Out-of-Scope - Guardian/Institution/Elder Apps | REQ-FUNC-060 | TC-FUNC-060 |
| KPI: partner count | REQ-NF-009 | TC-NF-009 |
| KPI: daily chat usage | REQ-NF-008 | TC-NF-008 |
| KPI: p95/p99 latency | REQ-NF-001 | TC-NF-001 |
| KPI: risk recall | REQ-NF-011, REQ-NF-012 | TC-NF-011, TC-NF-012 |
| KPI: high-risk false positive rate | REQ-NF-013 | TC-NF-013 |
| KPI: first API success time | REQ-NF-007 | TC-NF-007 |
| KPI: paid conversion intent | REQ-NF-010 | TC-NF-010 |
| NFR: availability / SLA / RPO / RTO | REQ-NF-002, REQ-NF-003 | TC-NF-002, TC-NF-003 |
| NFR: security / privacy | REQ-NF-018, REQ-NF-019, REQ-NF-020, REQ-NF-021, REQ-NF-022, REQ-NF-023, REQ-NF-024, REQ-NF-025, REQ-NF-026, REQ-NF-027, REQ-NF-028 | TC-NF-018, TC-NF-019, TC-NF-020, TC-NF-021, TC-NF-022, TC-NF-023, TC-NF-024, TC-NF-025, TC-NF-026, TC-NF-027, TC-NF-028 |
| NFR: cost and usage metering | REQ-NF-029, REQ-NF-030, REQ-NF-031, REQ-NF-042, REQ-NF-043, REQ-NF-044 | TC-NF-029, TC-NF-030, TC-NF-031, TC-NF-042, TC-NF-043, TC-NF-044 |
| NFR: deployment and operations | REQ-NF-005, REQ-NF-006, REQ-NF-032, REQ-NF-033, REQ-NF-034, REQ-NF-035 | TC-NF-005, TC-NF-006, TC-NF-032, TC-NF-033, TC-NF-034, TC-NF-035 |
| NFR: Web Console security/privacy/performance | REQ-NF-036, REQ-NF-037, REQ-NF-038, REQ-NF-039, REQ-NF-040, REQ-NF-041 | TC-NF-036, TC-NF-037, TC-NF-038, TC-NF-039, TC-NF-040, TC-NF-041 |

## 6. Appendix

### 6.1 API Endpoint List

| Endpoint ID | Method | Endpoint / Interface | Phase | Priority | Auth | Request Fields | Response Fields | Related Requirements |
|---|---|---|---|---|---|---|---|---|
| API-000 | UI | Developer Portal / Sandbox | Phase A | Must | Partner login | Partner account, tenant context | API Key, Swagger, samples, error table, sandbox profiles | REQ-FUNC-001, REQ-FUNC-002, REQ-FUNC-003 |
| API-001 | POST | `/api/v1/chat/reply` | Phase A | Must | API Key | `tenant_id`, `device_id`, `elder_id`, `utterance`, optional `conversation_id`, `use_memory`, `request_context`, `locale` | `schema_version`, `request_id`, `reply_text`, `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action`, `memory_used`, `safety_policy_applied` | REQ-FUNC-007 to REQ-FUNC-014, REQ-FUNC-044 |
| API-002 | POST | `/api/v1/analyze/emotion` | Phase A | Must | API Key | `tenant_id`, `elder_id`, `text`, optional `context_window`, `locale` | `schema_version`, `request_id`, `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action`, optional `detected_keywords` | REQ-FUNC-016 to REQ-FUNC-019 |
| API-003 | POST | `/api/v1/schedule/proactive` | Phase A | Should | API Key | `tenant_id`, `device_id`, `elder_id`, `trigger_type`, optional `scheduled_at`, `use_memory` | `schema_version`, `request_id`, `reply_text`, `memory_used`, `safety_policy_applied` | REQ-FUNC-020, REQ-FUNC-021 |
| API-004 | GET | `/api/v1/report/poc` | Phase A | Must | API Key | `tenant_id`, `from`, `to`, optional `group_by`, optional `format=json/csv` | `active_devices`, `active_elders`, `conversation_count`, `avg_conversation_per_elder`, `p95_latency_ms`, `error_rate`, `risk_event_count` | REQ-FUNC-022 to REQ-FUNC-026 |
| API-005 | POST | `/api/v2/sensor/ingest` | Phase B / Post-MVP | Should | API Key | `tenant_id`, `device_id`, `elder_id`, `sensor_type`, `observed_at`, `payload` | `schema_version`, `request_id`, `ingest_status` | REQ-FUNC-040, REQ-FUNC-046, REQ-FUNC-047 |
| API-006 | POST | `/api/v2/alert/emergency` | Phase B / Post-MVP | Should | API Key or service auth | `tenant_id`, `elder_id`, `risk_event_id`, optional `sensor_event_ids` | `schema_version`, `request_id`, `alert_status`, `webhook_delivery_id` | REQ-FUNC-041 |
| API-007 | Outbound POST | Partner-configured Webhook | Phase B / Post-MVP | Should | Webhook signature | `risk_level`, `risk_reason`, `recommended_action`, `request_id`, `detected_at`, `elder_id` or partner-safe alias | Partner-defined delivery response | REQ-FUNC-028, REQ-FUNC-041 |
| API-008 | GET | `/api/v2/report/kpi` | Phase B / Post-MVP | Should | API Key | `tenant_id`, `from`, `to`, optional `scope=guardian/institution/partner` | weekly sentiment report, sleep analysis result, institution performance data | REQ-FUNC-027, REQ-FUNC-030, REQ-FUNC-032, REQ-FUNC-042 |

#### Standard Error Codes

| `error_code` | HTTP | `retryable` | Meaning |
|---|---:|:---:|---|
| `INVALID_REQUEST` | 400 | false | 필수 필드 누락, 잘못된 타입, 지원하지 않는 enum |
| `UNAUTHORIZED` | 401 | false | API Key 누락 또는 인증 실패 |
| `FORBIDDEN` | 403 | false | 인증은 되었지만 해당 리소스 접근 권한 없음 |
| `TENANT_ACCESS_DENIED` | 403 | false | `tenant_id`와 리소스 소유 관계 불일치 |
| `CONSENT_REQUIRED` | 403 | false | 메모리, 보호자 공유, 기관 공유 등 필요한 동의 없음 |
| `RATE_LIMIT_EXCEEDED` | 429 | true | 파트너/테넌트 단위 호출 한도 초과 |
| `LLM_TIMEOUT` | 504 | true | LLM 또는 분석 엔진 응답 시간 초과 |
| `UPSTREAM_ERROR` | 502 | true | 외부 LLM/클라우드 등 의존 서비스 장애 |
| `SAFETY_FILTERED` | 422 | false | API 오남용, 정책 위반 입력, 프롬프트 주입 시도 등으로 처리 거부 |
| `INTERNAL_ERROR` | 500 | true | 내부 서버 오류 |

### 6.2 Entity & Data Model

#### 6.2.1 Entity Summary

| Entity | Key Fields | Required Fields | Purpose | Privacy / Retention |
|---|---|---|---|---|
| `tenant` | `tenant_id`, `name`, `status`, `created_at` | `tenant_id`, `status` | B2B 파트너 식별과 데이터 격리 | tenant별 격리, 계약 종료 시 보존 정책 적용 |
| `api_key` | `api_key_id`, `tenant_id`, `key_hash`, `status`, `created_at`, `revoked_at` | `api_key_id`, `tenant_id`, `key_hash`, `status` | 서버 간 인증 | 원문 key 저장 금지, 감사 로그 필수 |
| `device` | `device_id`, `tenant_id`, `device_type`, `activated_at`, `last_seen_at` | `device_id`, `tenant_id` | 월 활성 디바이스 과금과 요청 추적 | tenant 격리, 월간 사용량 집계 |
| `elder_profile` | `elder_id`, `tenant_id`, `locale`, `consent_state_id`, `memory_enabled`, `created_at` | `elder_id`, `tenant_id`, `locale`, `consent_state_id` | 개인화와 권한 판단을 위한 최소 프로필 | 직접 식별정보 금지, 가명 ID 사용 |
| `consent_state` | `consent_state_id`, `elder_id`, `basic_processing`, `sensitive_processing`, `memory_storage`, `guardian_sharing`, `institution_sharing`, `quality_improvement`, `updated_at` | all consent booleans, `updated_at` | 기능별 동의 상태 | 동의 변경 이력 보존, 철회 시 cascade 처리 |
| `conversation` | `conversation_id`, `tenant_id`, `device_id`, `elder_id`, `started_at`, `ended_at` | `conversation_id`, `tenant_id`, `elder_id` | 대화 세션 추적 | 원문 보존 최소화 |
| `message` | `message_id`, `conversation_id`, `request_id`, `speaker`, `text_masked`, `text_raw_ref`, `created_at` | `message_id`, `conversation_id`, `request_id`, `speaker`, `created_at` | 발화/응답 기록 | 원문 기본 30일 이내, PII 마스킹 저장 |
| `memory_summary` | `memory_id`, `tenant_id`, `elder_id`, `summary`, `source_request_id`, `expires_at`, `created_at` | `memory_id`, `elder_id`, `summary`, `expires_at` | 개인화 메모리 | 메모리 동의 필수, 최대 180일 |
| `analysis_result` | `analysis_id`, `request_id`, `tenant_id`, `elder_id`, `emotion_tags`, `risk_level`, `risk_reason`, `recommended_action`, `created_at` | `analysis_id`, `request_id`, `risk_level`, `recommended_action` | 정서/위험 태깅 결과 | 최대 180일, 진단 표현 금지 |
| `risk_event` | `risk_event_id`, `request_id`, `tenant_id`, `elder_id`, `risk_level`, `risk_reason`, `recommended_action`, `detected_at`, `review_status` | `risk_event_id`, `request_id`, `risk_level`, `detected_at` | 알림과 운영 리뷰 기준 이벤트 | 최대 1년, 식별 가능 데이터 삭제 요청 처리 |
| `usage_event` | `usage_event_id`, `tenant_id`, `device_id`, `endpoint`, `request_id`, `usage_unit`, `latency_ms`, `status_code`, `created_at` | `usage_event_id`, `tenant_id`, `endpoint`, `request_id`, `created_at` | 호출량, 비용, latency, 오류율 산출 | 원문 없음, 정산·장애 목적 |
| `poc_report_aggregate` | `aggregate_id`, `tenant_id`, `period_start`, `period_end`, `active_devices`, `active_elders`, `conversation_count`, `p95_latency_ms`, `error_rate`, `risk_event_count` | all metric fields | PoC 리포트 JSON/CSV | 개인 식별정보 없는 집계, 최대 1년 |
| `alert_event` | `alert_event_id`, `tenant_id`, `elder_id`, `risk_event_id`, `recipient_type`, `payload`, `delivery_status`, `detected_at`, `sent_at` | `alert_event_id`, `risk_event_id`, `recipient_type`, `delivery_status` | 보호자/기관/파트너 알림 | 원문 미포함, 권장 액션 중심 |
| `audit_log` | `audit_log_id`, `tenant_id`, `actor_role`, `actor_id`, `action`, `resource_type`, `resource_id`, `request_id`, `created_at` | all audit identifiers | 민감 데이터 접근과 관리 작업 추적 | 원문/PII 마스킹, 최대 1년 이상은 계약/법무 검토 |
| `evaluation_sample` | `sample_id`, `source_request_id`, `utterance_masked`, `expected_risk_level`, `expected_emotion_tags`, `expected_recommended_action`, `labeling_rationale`, `review_status`, `expires_at` | `sample_id`, expected labels, `review_status` | AI 안전/품질 평가셋 후보 | 별도 동의 또는 가명/익명 처리, 후보 최대 30일 |
| `sensor_event` | `sensor_event_id`, `tenant_id`, `device_id`, `elder_id`, `sensor_type`, `observed_at`, `payload`, `ingested_at` | `sensor_event_id`, `tenant_id`, `device_id`, `observed_at` | Phase B 센서 시계열 데이터 | Phase B 표준 schema 확정 필요 |

#### 6.2.2 Field-Level Schema

본 필드 명세는 PRD v0.5-단일구조의 API 및 데이터 정책을 구현 가능한 baseline schema로 변환한 것이다. 물리 DB의 문자열 길이, partition strategy, storage engine은 상세 설계에서 확정하되, 아래의 key, nullable, privacy, retention 의미는 변경하지 않아야 한다.

| Entity | Field | Type | Nullable | Key | Description / Constraint |
|---|---|---|:---:|---|---|
| `tenant` | `tenant_id` | string | N | PK | B2B 파트너 또는 고객사 식별자 |
| `tenant` | `name` | string | Y |  | 파트너 표시명 |
| `tenant` | `status` | enum | N | IDX | `active`, `suspended`, `terminated` 중 하나 |
| `tenant` | `created_at` | datetime | N |  | tenant 생성 시각 |
| `api_key` | `api_key_id` | string | N | PK | API Key 레코드 식별자 |
| `api_key` | `tenant_id` | string | N | FK, IDX | `tenant.tenant_id` 참조 |
| `api_key` | `key_hash` | string | N | UNIQUE | 원문 API Key가 아닌 hash 값만 저장 |
| `api_key` | `status` | enum | N | IDX | `active`, `revoked`, `expired` 중 하나 |
| `api_key` | `created_at` | datetime | N |  | 발급 시각 |
| `api_key` | `revoked_at` | datetime | Y |  | 폐기 시각 |
| `device` | `device_id` | string | N | PK | 파트너 기기 식별자 |
| `device` | `tenant_id` | string | N | FK, IDX | `tenant.tenant_id` 참조 |
| `device` | `device_type` | string | Y |  | 로봇, 스피커, 태블릿 등 파트너 정의 유형 |
| `device` | `activated_at` | datetime | Y |  | 최초 유효 호출 또는 등록 시각 |
| `device` | `last_seen_at` | datetime | Y | IDX | 마지막 API 호출 시각 |
| `elder_profile` | `elder_id` | string | N | PK | 파트너 시스템 내 가명/내부 어르신 ID |
| `elder_profile` | `tenant_id` | string | N | FK, IDX | `tenant.tenant_id` 참조 |
| `elder_profile` | `locale` | string | N |  | MVP 기본값 `ko-KR` |
| `elder_profile` | `consent_state_id` | string | N | FK | `consent_state.consent_state_id` 참조 |
| `elder_profile` | `memory_enabled` | boolean | N |  | 현재 메모리 기능 사용 가능 여부 |
| `elder_profile` | `created_at` | datetime | N |  | 프로필 생성 시각 |
| `consent_state` | `consent_state_id` | string | N | PK | 동의 상태 레코드 식별자 |
| `consent_state` | `elder_id` | string | N | FK, UNIQUE | `elder_profile.elder_id` 참조 |
| `consent_state` | `basic_processing` | boolean | N |  | 기본 대화 처리 동의 |
| `consent_state` | `sensitive_processing` | boolean | N |  | 건강·통증·복약 등 민감정보 처리 동의 |
| `consent_state` | `memory_storage` | boolean | N |  | 개인화 메모리 저장 동의 |
| `consent_state` | `guardian_sharing` | boolean | N |  | 보호자 리포트/알림 공유 동의 |
| `consent_state` | `institution_sharing` | boolean | N |  | 기관 리포트 공유 동의 |
| `consent_state` | `quality_improvement` | boolean | N |  | 품질 개선/평가셋 사용 동의 또는 가명 처리 허용 |
| `consent_state` | `updated_at` | datetime | N |  | 마지막 동의 변경 시각 |
| `conversation` | `conversation_id` | string | N | PK | 대화 세션 식별자 |
| `conversation` | `tenant_id` | string | N | FK, IDX | `tenant.tenant_id` 참조 |
| `conversation` | `device_id` | string | Y | FK, IDX | `device.device_id` 참조. 서버 생성 대화는 null 허용 |
| `conversation` | `elder_id` | string | N | FK, IDX | `elder_profile.elder_id` 참조 |
| `conversation` | `started_at` | datetime | N | IDX | 대화 시작 시각 |
| `conversation` | `ended_at` | datetime | Y |  | 대화 종료 시각 |
| `message` | `message_id` | string | N | PK | 메시지 식별자 |
| `message` | `conversation_id` | string | N | FK, IDX | `conversation.conversation_id` 참조 |
| `message` | `request_id` | string | N | IDX | API 요청 추적 ID |
| `message` | `speaker` | enum | N |  | `elder`, `assistant`, `system` 중 하나 |
| `message` | `text_masked` | text | Y |  | PII 마스킹된 발화 또는 응답 텍스트 |
| `message` | `text_raw_ref` | string | Y |  | 원문 보관 위치 참조. 원문 직접 저장 금지 권장 |
| `message` | `created_at` | datetime | N | IDX | 메시지 생성 시각 |
| `memory_summary` | `memory_id` | string | N | PK | 메모리 요약 식별자 |
| `memory_summary` | `tenant_id` | string | N | FK, IDX | `tenant.tenant_id` 참조 |
| `memory_summary` | `elder_id` | string | N | FK, IDX | `elder_profile.elder_id` 참조 |
| `memory_summary` | `summary` | text | N |  | 원문이 아닌 요약 중심 개인화 메모리 |
| `memory_summary` | `source_request_id` | string | N | IDX | 메모리 생성 근거 요청 ID |
| `memory_summary` | `expires_at` | datetime | N | IDX | 최대 180일 이내 만료 시각 |
| `memory_summary` | `created_at` | datetime | N |  | 생성 시각 |
| `analysis_result` | `analysis_id` | string | N | PK | 분석 결과 식별자 |
| `analysis_result` | `request_id` | string | N | IDX | API 요청 추적 ID |
| `analysis_result` | `tenant_id` | string | N | FK, IDX | `tenant.tenant_id` 참조 |
| `analysis_result` | `elder_id` | string | N | FK, IDX | `elder_profile.elder_id` 참조 |
| `analysis_result` | `emotion_tags` | string[] | N |  | `emotion_tags` enum 배열 |
| `analysis_result` | `risk_level` | enum | N | IDX | `risk_level` enum |
| `analysis_result` | `risk_reason` | text | Y |  | 파트너/운영자용 판단 근거. 진단 표현 금지 |
| `analysis_result` | `recommended_action` | enum | N | IDX | `recommended_action` enum |
| `analysis_result` | `created_at` | datetime | N | IDX | 분석 생성 시각 |
| `risk_event` | `risk_event_id` | string | N | PK | 위험 이벤트 식별자 |
| `risk_event` | `request_id` | string | N | IDX | API 요청 추적 ID |
| `risk_event` | `tenant_id` | string | N | FK, IDX | `tenant.tenant_id` 참조 |
| `risk_event` | `elder_id` | string | N | FK, IDX | `elder_profile.elder_id` 참조 |
| `risk_event` | `risk_level` | enum | N | IDX | `medium` 이상 이벤트 저장 대상 |
| `risk_event` | `risk_reason` | text | Y |  | 알림/리뷰용 위험 근거 |
| `risk_event` | `recommended_action` | enum | N |  | 권장 후속 액션 |
| `risk_event` | `detected_at` | datetime | N | IDX | 위험 감지 시각 |
| `risk_event` | `review_status` | enum | N | IDX | `pending`, `reviewed`, `excluded` 중 하나 |
| `usage_event` | `usage_event_id` | string | N | PK | 사용량 이벤트 식별자 |
| `usage_event` | `tenant_id` | string | N | FK, IDX | tenant별 과금/집계 기준 |
| `usage_event` | `device_id` | string | Y | FK, IDX | 월 활성 디바이스 집계 기준 |
| `usage_event` | `endpoint` | string | N | IDX | 호출 endpoint |
| `usage_event` | `request_id` | string | N | UNIQUE | 요청 추적 및 중복 집계 방지 |
| `usage_event` | `usage_unit` | integer | N |  | 과금 또는 사용량 계산 단위 |
| `usage_event` | `latency_ms` | integer | N |  | 응답 지연시간 |
| `usage_event` | `status_code` | integer | N | IDX | HTTP status code |
| `usage_event` | `created_at` | datetime | N | IDX | 호출 발생 시각 |
| `poc_report_aggregate` | `aggregate_id` | string | N | PK | 집계 레코드 식별자 |
| `poc_report_aggregate` | `tenant_id` | string | N | FK, IDX | 집계 대상 tenant |
| `poc_report_aggregate` | `period_start` | date | N | IDX | 집계 시작일 |
| `poc_report_aggregate` | `period_end` | date | N | IDX | 집계 종료일 |
| `poc_report_aggregate` | `active_devices` | integer | N |  | 기간 내 활성 device 수 |
| `poc_report_aggregate` | `active_elders` | integer | N |  | 기간 내 활성 elder 수 |
| `poc_report_aggregate` | `conversation_count` | integer | N |  | 대화 API 호출 수 |
| `poc_report_aggregate` | `p95_latency_ms` | integer | N |  | p95 지연시간 |
| `poc_report_aggregate` | `error_rate` | decimal | N |  | 오류율 |
| `poc_report_aggregate` | `risk_event_count` | integer | N |  | `medium` 이상 위험 이벤트 수 |
| `alert_event` | `alert_event_id` | string | N | PK | 알림 이벤트 식별자 |
| `alert_event` | `tenant_id` | string | N | FK, IDX | 알림 발생 tenant |
| `alert_event` | `elder_id` | string | N | FK, IDX | 알림 대상 elder |
| `alert_event` | `risk_event_id` | string | N | FK, IDX | `risk_event.risk_event_id` 참조 |
| `alert_event` | `recipient_type` | enum | N |  | `guardian`, `institution`, `partner` 중 하나 |
| `alert_event` | `payload` | json | N |  | 원문 미포함 알림 payload |
| `alert_event` | `delivery_status` | enum | N | IDX | `pending`, `sent`, `failed`, `retrying` 중 하나 |
| `alert_event` | `detected_at` | datetime | N | IDX | 위험 감지 시각 |
| `alert_event` | `sent_at` | datetime | Y |  | 발송 완료 시각 |
| `audit_log` | `audit_log_id` | string | N | PK | 감사 로그 식별자 |
| `audit_log` | `tenant_id` | string | N | FK, IDX | 감사 대상 tenant |
| `audit_log` | `actor_role` | enum | N | IDX | 접근 주체 역할 |
| `audit_log` | `actor_id` | string | Y | IDX | 접근 주체 식별자 또는 시스템 ID |
| `audit_log` | `action` | string | N | IDX | 수행 작업 |
| `audit_log` | `resource_type` | string | N | IDX | 접근 대상 유형 |
| `audit_log` | `resource_id` | string | Y | IDX | 접근 대상 ID |
| `audit_log` | `request_id` | string | Y | IDX | 연계 요청 ID |
| `audit_log` | `created_at` | datetime | N | IDX | 감사 이벤트 시각 |
| `evaluation_sample` | `sample_id` | string | N | PK | 평가셋 후보 또는 확정 샘플 ID |
| `evaluation_sample` | `source_request_id` | string | Y | IDX | 원천 요청 ID. 비식별 후 연결 |
| `evaluation_sample` | `utterance_masked` | text | N |  | PII 제거 또는 가명 처리된 발화 |
| `evaluation_sample` | `expected_risk_level` | enum | N |  | 기대 `risk_level` |
| `evaluation_sample` | `expected_emotion_tags` | string[] | N |  | 기대 `emotion_tags` |
| `evaluation_sample` | `expected_recommended_action` | enum | N |  | 기대 `recommended_action` |
| `evaluation_sample` | `labeling_rationale` | text | Y |  | 라벨링 근거 |
| `evaluation_sample` | `review_status` | enum | N | IDX | `candidate`, `approved`, `rejected`, `expired` 중 하나 |
| `evaluation_sample` | `expires_at` | datetime | N | IDX | 후보 데이터 최대 30일 만료 시각 |
| `sensor_event` | `sensor_event_id` | string | N | PK | Phase B 센서 이벤트 식별자 |
| `sensor_event` | `tenant_id` | string | N | FK, IDX | `tenant.tenant_id` 참조 |
| `sensor_event` | `device_id` | string | N | FK, IDX | `device.device_id` 참조 |
| `sensor_event` | `elder_id` | string | Y | FK, IDX | 파트너가 elder 매핑을 제공할 때 사용 |
| `sensor_event` | `sensor_type` | string | N | IDX | 수면, 움직임, 혈압 등 파트너/Phase B schema 값 |
| `sensor_event` | `observed_at` | datetime | N | IDX | 센서 관측 시각 |
| `sensor_event` | `payload` | json | N |  | Phase B 표준 schema에 맞는 센서 payload |
| `sensor_event` | `ingested_at` | datetime | N | IDX | 실버케어 수신 시각 |

#### 6.2.3 Relationship & Cardinality Matrix

| Parent Entity | Child Entity | Cardinality | Relationship Rule |
|---|---|---|---|
| `tenant` | `api_key` | 1:N | 하나의 tenant는 여러 API Key를 가질 수 있다. 폐기된 Key는 `status=revoked`로 보존한다. |
| `tenant` | `device` | 1:N | 모든 device는 정확히 하나의 tenant에 속해야 한다. |
| `tenant` | `elder_profile` | 1:N | 모든 elder profile은 정확히 하나의 tenant에 속해야 한다. |
| `tenant` | `usage_event` | 1:N | 모든 billable API 호출은 tenant 단위로 집계되어야 한다. |
| `tenant` | `poc_report_aggregate` | 1:N | PoC 집계 리포트는 tenant와 기간 단위로 생성된다. |
| `elder_profile` | `consent_state` | 1:1 | 각 elder는 하나의 현재 동의 상태를 가진다. 변경 이력은 감사 로그로 추적한다. |
| `elder_profile` | `conversation` | 1:N | 하나의 elder는 여러 대화 세션을 가질 수 있다. |
| `device` | `conversation` | 0..1:N | device 없이 서버 생성된 대화는 허용하되, 기기 호출 대화는 device와 연결되어야 한다. |
| `conversation` | `message` | 1:N | 하나의 대화 세션은 하나 이상의 message를 포함할 수 있다. |
| `message` | `analysis_result` | 0..1:1 | 분석이 수행된 message 또는 요청은 최대 하나의 최신 analysis result와 연결된다. |
| `elder_profile` | `memory_summary` | 1:N | 메모리 저장 동의가 있는 elder만 memory summary를 가질 수 있다. |
| `analysis_result` | `risk_event` | 0..1:1 | `medium` 이상 또는 운영 리뷰 대상 분석 결과는 risk event로 승격될 수 있다. |
| `risk_event` | `alert_event` | 1:N | 하나의 위험 이벤트는 보호자, 기관, 파트너 등 여러 수신자에게 알림을 만들 수 있다. |
| `risk_event` | `evaluation_sample` | 0..1:N | 오탐/미탐 리뷰 대상 위험 이벤트는 평가셋 후보로 연결될 수 있다. |
| `device` | `sensor_event` | 1:N | Phase B에서 센서 이벤트는 device 기준으로 수신된다. |
| `sensor_event` | `risk_event` | 0..N:0..N | Phase B 복합 위험 판단에서 여러 센서 이벤트와 대화 위험 이벤트가 결합될 수 있다. 물리 구현은 join table 또는 event correlation ID를 사용한다. |

#### 6.2.4 Data Retention and Deletion Matrix

| Data Category / Entity | Default Retention | Deletion Trigger | Cascade Target | Notes |
|---|---|---|---|---|
| `message.text_raw_ref` / raw transcript | 30일 이내 또는 요약/리포트 생성 후 최소 기간 | 삭제 요청, 동의 철회, 보존 기간 만료 | raw object, cache, search index, evaluation candidate source | 보호자/기관 기본 미노출 |
| `message.text_masked` | 서비스 제공과 감사 목적 범위 내 제한 보존 | 삭제 요청 또는 tenant 계약 종료 | message, analysis linkage | PII 마스킹 필수 |
| `memory_summary` | 동의 유지 중 최대 180일 | 메모리 동의 철회, 만료 시각 도달 | memory summary, personalization cache | 철회 후 `memory_used=false` |
| `analysis_result` | 최대 180일 | 공유 동의 철회, 보존 기간 만료 | risk linkage, report source | 진단이 아닌 참고 신호로만 유지 |
| `risk_event` | 최대 1년 | 삭제 요청 또는 운영 리뷰 목적 종료 | alert event, review queue linkage | 비식별 통계는 보존 가능 |
| `poc_report_aggregate` | PoC 종료 후 최대 1년 | 파트너 계약 조건 또는 보존 기간 만료 | exported report files | 개인 식별정보 없는 집계만 보존 |
| `usage_event` | 정산·장애·보안 목적 범위 내 최대 1년 | 보존 기간 만료 또는 계약 종료 | billing replay cache | 원문 없음 |
| `audit_log` | 최소 1년 또는 계약·법무 기준 | 법무 승인된 만료 정책 | audit archive | 원문/PII 저장 금지 |
| `evaluation_sample` | 후보 최대 30일 | 별도 동의 없음, 가명/익명 처리 불가, 만료 | labeling result linkage | 승인 샘플은 비식별 기준 충족 시 평가셋으로 이동 |
| `sensor_event` | Phase B 정책 확정 전 제한 보존 | tenant 계약 종료, elder 삭제 요청 | time-series record, derived risk correlation | Phase B ADR에서 세부 확정 |
| `backup` | 운영 백업 정책에 따른 제한 보존 | 삭제 요청 데이터의 백업 보존 주기 만료 | restored data deletion replay | 복원 시 삭제 요청 재적용 필수 |

#### 6.2.5 Query Keys and Index Recommendations

| Use Case | Entity | Required Query Keys / Indexes | Performance Rationale |
|---|---|---|---|
| API 인증 | `api_key` | `key_hash` unique index, `tenant_id`, `status` | 모든 API 요청의 인증 latency 최소화 |
| tenant 격리 | all tenant-owned entities | `tenant_id` index | cross-tenant access 방지와 권한 필터링 |
| 월 활성 디바이스 산출 | `usage_event`, `device` | `(tenant_id, device_id, created_at)`, endpoint filter | 월별 distinct active device 재계산 |
| PoC 리포트 생성 | `usage_event`, `risk_event`, `poc_report_aggregate` | `(tenant_id, created_at)`, `(tenant_id, detected_at)`, `(tenant_id, period_start, period_end)` | 대화 횟수, p95 latency, 오류율, 위험 이벤트 수 집계 |
| p95/p99 latency 산출 | `usage_event` | `(tenant_id, endpoint, created_at)`, `latency_ms` metric aggregation | endpoint별 성능 NFR 검증 |
| 운영 로그 조회 | `audit_log`, `usage_event` | `request_id`, `(tenant_id, created_at)`, `actor_role`, `resource_type` | 장애, 보안, 개인정보 접근 추적 |
| 위험 이벤트 우선순위 | `risk_event` | `(tenant_id, risk_level, detected_at)`, `(elder_id, detected_at)` | 기관 리포트 정렬과 반복 위험 신호 탐지 |
| 보호자/기관 알림 상태 | `alert_event` | `risk_event_id`, `(tenant_id, delivery_status, detected_at)` | 알림 재시도와 delivery 상태 추적 |
| 메모리 만료 처리 | `memory_summary` | `expires_at`, `(elder_id, expires_at)` | 180일 보존 제한과 철회 처리 |
| 평가셋 후보 만료 | `evaluation_sample` | `expires_at`, `review_status`, `source_request_id` | 후보 30일 제한과 human review queue 관리 |
| Phase B 센서 분석 | `sensor_event` | `(tenant_id, device_id, observed_at)`, `(elder_id, observed_at)`, `sensor_type` | 시계열 조회와 복합 위험 판단 |

#### 6.2.6 API Payload Schema Cross-Reference

| Endpoint | Payload Area | Backing Entities | Notes |
|---|---|---|---|
| `/api/v1/chat/reply` | Request identity fields | `tenant`, `device`, `elder_profile`, `conversation` | `tenant_id`, `device_id`, `elder_id`, optional `conversation_id`와 연결 |
| `/api/v1/chat/reply` | Response analysis fields | `message`, `analysis_result`, optional `risk_event`, `usage_event` | `reply_text`, `emotion_tags`, `risk_level`, `recommended_action`, `request_id` 저장·추적 |
| `/api/v1/analyze/emotion` | Analysis request/response | `analysis_result`, optional `risk_event`, `evaluation_sample` | 원문 전체 전송 최소화, detected keywords는 PII 제거 |
| `/api/v1/schedule/proactive` | Proactive utterance | `elder_profile`, `consent_state`, `memory_summary`, `message` | 메모리 사용은 서버 동의 판단을 우선 |
| `/api/v1/report/poc` | PoC aggregate response | `usage_event`, `risk_event`, `poc_report_aggregate` | 원문/PII 없이 JSON 또는 CSV 제공 |
| `/api/v2/sensor/ingest` | Sensor payload | `device`, `elder_profile`, `sensor_event` | Phase B schema 확정 전에도 tenant/device 격리 필수 |
| `/api/v2/alert/emergency` | Alert request/response | `risk_event`, `sensor_event`, `alert_event` | 응급 구조 보장이 아닌 확인 권고 이벤트 |
| `/api/v2/report/kpi` | KPI report response | `analysis_result`, `risk_event`, `sensor_event`, `poc_report_aggregate` | Phase B 주간 정서·수면·기관 성과 데이터 |

#### 6.2.7 Enumerations

| Enum | Values |
|---|---|
| `risk_level` | `none`, `low`, `medium`, `high`, `critical` |
| `emotion_tags` | `neutral`, `positive`, `lonely`, `sad`, `anxious`, `angry`, `pain`, `sleep_issue`, `appetite_issue`, `medication_mention`, `confusion`, `emergency_signal`, `self_harm_signal` |
| `recommended_action` | `none`, `empathetic_response`, `check_in`, `notify_guardian`, `suggest_medical_contact`, `suggest_emergency_contact`, `human_review` |
| `trigger_type` | `morning`, `meal`, `sleep`, `check_in`, `custom` |
| `error_code` | `INVALID_REQUEST`, `UNAUTHORIZED`, `FORBIDDEN`, `TENANT_ACCESS_DENIED`, `CONSENT_REQUIRED`, `RATE_LIMIT_EXCEEDED`, `LLM_TIMEOUT`, `UPSTREAM_ERROR`, `SAFETY_FILTERED`, `INTERNAL_ERROR` |

### 6.3 Detailed Interaction Models

#### Use Case Diagram

```mermaid
graph LR
    B2BDEV["B2B-DEV<br/>연동 개발자"]
    B2BPM["B2B-PM<br/>제품/사업 담당자"]
    Elder["A. 독거 어르신"]
    Guardian["B. 보호자 자녀"]
    Institution["C. 요양기관 관리자"]
    Operator["D. 플랫폼 운영자"]
    Device["B2B 파트너 기기"]
    PartnerBackend["B2B 파트너 서버"]

    UC01(["API Key 발급"])
    UC02(["Swagger/샘플 확인"])
    UC03(["Sandbox 응답 재현"])
    UC04(["대화 응답 생성"])
    UC05(["정서/위험 신호 분석"])
    UC06(["선제적 안부 발화 생성"])
    UC07(["PoC 성과 리포트 조회"])
    UC08(["월 활성 디바이스/사용량 확인"])
    UC09(["보호자 요약 리포트 확인"])
    UC10(["위험 신호 알림 수신"])
    UC11(["기관 어르신 목록/우선순위 조회"])
    UC12(["마스킹 로그/품질 모니터링"])
    UC13(["오탐/미탐 리뷰 데이터 추출"])
    UC14(["동의 철회/삭제 요청 처리"])
    UC15(["Phase B 센서 데이터 수집"])
    UC16(["Phase B 통합 KPI 리포트 조회"])
    UC17(["Web API Playground 실행"])
    UC18(["대화 로그/분석 결과 조회"])
    UC19(["사용자/동의 상태 관리"])

    B2BDEV --> UC01
    B2BDEV --> UC02
    B2BDEV --> UC03
    B2BDEV --> UC17
    B2BDEV --> UC04
    B2BDEV --> UC05
    B2BDEV --> UC06

    B2BPM --> UC07
    B2BPM --> UC08
    B2BPM --> UC17
    B2BPM --> UC16

    Elder --> Device
    Device --> UC04
    Device --> UC06

    Guardian --> PartnerBackend
    PartnerBackend --> UC09
    PartnerBackend --> UC10

    Institution --> PartnerBackend
    PartnerBackend --> UC11
    PartnerBackend --> UC16

    Operator --> UC12
    Operator --> UC13
    Operator --> UC14
    Operator --> UC18
    Operator --> UC19

    UC17 --> UC04
    UC17 --> UC05
    UC04 --> UC05
    UC05 --> UC10
    UC07 --> UC08
    UC15 --> UC16
```

#### Entity Relationship Diagram

```mermaid
erDiagram
    TENANT ||--o{ API_KEY : issues
    TENANT ||--o{ DEVICE : owns
    TENANT ||--o{ ELDER_PROFILE : contains
    TENANT ||--o{ USAGE_EVENT : records
    TENANT ||--o{ POC_REPORT_AGGREGATE : aggregates
    TENANT ||--o{ AUDIT_LOG : audits

    ELDER_PROFILE ||--|| CONSENT_STATE : has
    ELDER_PROFILE ||--o{ CONVERSATION : participates_in
    ELDER_PROFILE ||--o{ MEMORY_SUMMARY : stores
    ELDER_PROFILE ||--o{ ANALYSIS_RESULT : produces
    ELDER_PROFILE ||--o{ RISK_EVENT : triggers
    ELDER_PROFILE ||--o{ SENSOR_EVENT : observes

    DEVICE ||--o{ CONVERSATION : starts
    DEVICE ||--o{ USAGE_EVENT : generates
    DEVICE ||--o{ SENSOR_EVENT : sends

    CONVERSATION ||--o{ MESSAGE : contains
    MESSAGE ||--o| ANALYSIS_RESULT : analyzed_as
    MESSAGE ||--o| MEMORY_SUMMARY : summarized_into

    ANALYSIS_RESULT ||--o| RISK_EVENT : escalates_to
    RISK_EVENT ||--o{ ALERT_EVENT : notifies
    RISK_EVENT ||--o{ EVALUATION_SAMPLE : reviewed_as
    SENSOR_EVENT ||--o| RISK_EVENT : contributes_to

    TENANT {
        string tenant_id PK
        string name
        string status
        datetime created_at
    }
    API_KEY {
        string api_key_id PK
        string tenant_id FK
        string key_hash
        string status
        datetime revoked_at
    }
    DEVICE {
        string device_id PK
        string tenant_id FK
        string device_type
        datetime last_seen_at
    }
    ELDER_PROFILE {
        string elder_id PK
        string tenant_id FK
        string locale
        boolean memory_enabled
    }
    CONSENT_STATE {
        string consent_state_id PK
        string elder_id FK
        boolean basic_processing
        boolean sensitive_processing
        boolean memory_storage
        boolean guardian_sharing
        boolean institution_sharing
        boolean quality_improvement
    }
    CONVERSATION {
        string conversation_id PK
        string tenant_id FK
        string device_id FK
        string elder_id FK
        datetime started_at
    }
    MESSAGE {
        string message_id PK
        string conversation_id FK
        string request_id
        string speaker
        datetime created_at
    }
    MEMORY_SUMMARY {
        string memory_id PK
        string elder_id FK
        string source_request_id
        datetime expires_at
    }
    ANALYSIS_RESULT {
        string analysis_id PK
        string request_id
        string risk_level
        string recommended_action
    }
    RISK_EVENT {
        string risk_event_id PK
        string request_id
        string risk_level
        datetime detected_at
    }
    USAGE_EVENT {
        string usage_event_id PK
        string tenant_id FK
        string endpoint
        int latency_ms
        int status_code
    }
    ALERT_EVENT {
        string alert_event_id PK
        string risk_event_id FK
        string delivery_status
        datetime sent_at
    }
    SENSOR_EVENT {
        string sensor_event_id PK
        string tenant_id FK
        string device_id FK
        string sensor_type
        datetime observed_at
    }
```

#### Domain Class Diagram

```mermaid
classDiagram
    class ApiGateway {
        +authenticate(apiKey)
        +enforceRateLimit(tenantId)
        +route(request)
    }
    class ChatService {
        +createReply(request)
        +applySeniorTone(reply)
        +returnChatResponse()
    }
    class EmotionAnalysisService {
        +analyzeText(text, context)
        +assignRiskLevel()
        +recommendAction()
    }
    class ConsentService {
        +getConsentState(elderId)
        +canUseMemory(elderId)
        +canShareWithGuardian(elderId)
        +withdrawConsent(elderId, scope)
    }
    class MemoryService {
        +loadSummary(elderId)
        +saveSummary(requestId, summary)
        +expireSummary(memoryId)
    }
    class SafetyPolicyEngine {
        +detectMedicalRisk(text)
        +detectEmergencyRisk(text)
        +detectPromptInjection(text)
        +enforceSafeResponse()
    }
    class ReportService {
        +getPocReport(tenantId, period)
        +getGuardianSummary(elderId)
        +getInstitutionReport(scope)
        +exportCsv()
    }
    class AlertService {
        +evaluateAlert(riskEvent)
        +sendWebhook(payload)
        +recordDeliveryStatus()
    }
    class UsageMeteringService {
        +recordUsageEvent(request)
        +calculateActiveDevices(period)
        +calculateOverage(period)
    }
    class MonitoringService {
        +emitMetrics(endpoint)
        +maskLogs(logEvent)
        +notifyOperator(breach)
    }
    class DeletionOrchestrator {
        +createDeletionJob(elderId)
        +deleteRawMessages()
        +deleteMemory()
        +scheduleBackupDeletion()
    }
    class SensorIngestService {
        +validateSensorPayload(payload)
        +storeSensorEvent()
        +linkToRiskEvaluation()
    }

    ApiGateway --> ChatService
    ApiGateway --> EmotionAnalysisService
    ApiGateway --> ReportService
    ApiGateway --> SensorIngestService
    ChatService --> ConsentService
    ChatService --> MemoryService
    ChatService --> EmotionAnalysisService
    ChatService --> SafetyPolicyEngine
    EmotionAnalysisService --> SafetyPolicyEngine
    EmotionAnalysisService --> AlertService
    ReportService --> UsageMeteringService
    ReportService --> ConsentService
    AlertService --> MonitoringService
    DeletionOrchestrator --> ConsentService
    DeletionOrchestrator --> MemoryService
    SensorIngestService --> AlertService
    ApiGateway --> MonitoringService
```

#### Component Diagram

```mermaid
flowchart TB
    subgraph Partner["B2B Partner Domain"]
        Device["Partner Device<br/>STT/TTS 책임"]
        Backend["Partner Backend<br/>Report/Webhook/UI"]
        DevPortalUser["B2B Developer"]
        PMUser["B2B PM"]
    end

    subgraph Vercel["Vercel Platform<br/>Git Push Auto Deployment"]
        subgraph NextApp["SilverCare MVP Single Next.js App Router Application"]
            subgraph WebUI["App Router Web UI"]
                Console["SilverCare Partner Console"]
                Portal["Developer Portal / API Docs"]
                Playground["Web API Playground / Sandbox Console"]
                ReportUI["PoC Report Console"]
                OpsUI["Ops Monitoring / User Consent Admin"]
            end

            subgraph ServerBoundary["Next.js Server Boundary"]
                RouteHandlers["Route Handlers<br/>/api/v1, /api/v2"]
                ServerActions["Server Actions<br/>Console mutations"]
                AuthGuard["Session, API Key, RBAC, Rate Limit"]
            end

            subgraph Domain["server-only Domain Modules"]
                Chat["Chat Reply Service"]
                Analysis["Emotion/Risk Analysis Service"]
                Consent["Consent/RBAC Service"]
                Memory["Memory Service"]
                Safety["Safety Policy Engine"]
                Report["Report Service"]
                Alert["Alert/Webhook Service"]
                Metering["Usage Metering Service"]
                Sensor["Sensor Ingest Service<br/>Phase B"]
                Ops["Monitoring & Audit Service"]
            end

            subgraph Adapters["Adapters"]
                PrismaRepo["Repository / Data Access<br/>Prisma Client"]
                LLMAdapter["LLM Provider Adapter<br/>Vercel AI SDK"]
                AuditAdapter["Audit / Usage Logger"]
            end
        end
    end

    subgraph Data["Data Stores"]
        LocalDB[(Local SQLite<br/>Development)]
        OperationalDB[(Supabase PostgreSQL<br/>PoC/Deployment)]
        EventStore[(Usage/Risk Event Store)]
        TimeSeries[(Sensor Time-Series DB)]
        Evaluation[(Evaluation Dataset Store)]
        Backup[(Backup Storage)]
    end

    subgraph External["External Providers"]
        Gemini["Google Gemini API"]
        WebhookTarget["Partner Webhook Target"]
    end

    DevPortalUser --> Console
    PMUser --> Console
    PMUser --> Backend
    Device --> RouteHandlers
    Backend --> RouteHandlers
    Console --> Portal
    Console --> Playground
    Console --> ReportUI
    Console --> OpsUI
    Playground --> RouteHandlers
    ReportUI --> ServerActions
    OpsUI --> ServerActions
    Portal --> ServerActions

    RouteHandlers --> AuthGuard
    ServerActions --> AuthGuard
    AuthGuard --> Chat
    AuthGuard --> Analysis
    AuthGuard --> Report
    AuthGuard --> Sensor
    AuthGuard --> Consent

    Chat --> Consent
    Chat --> Memory
    Chat --> Safety
    Chat --> LLMAdapter
    Analysis --> Safety
    Analysis --> LLMAdapter
    Analysis --> Alert
    Report --> Metering
    Report --> Consent
    Sensor --> PrismaRepo
    Sensor --> Alert
    Alert --> WebhookTarget
    WebhookTarget --> Backend

    Consent --> PrismaRepo
    Memory --> PrismaRepo
    Report --> PrismaRepo
    Chat --> AuditAdapter
    Analysis --> AuditAdapter
    Metering --> AuditAdapter
    Ops --> AuditAdapter
    Ops --> Evaluation
    LLMAdapter --> Gemini
    PrismaRepo --> LocalDB
    PrismaRepo --> OperationalDB
    PrismaRepo --> TimeSeries
    AuditAdapter --> EventStore
    OperationalDB --> Backup
    EventStore --> Backup
    TimeSeries --> Backup
```

#### Developer Onboarding and Sandbox

```mermaid
sequenceDiagram
    participant Dev as B2B-DEV
    participant Portal as Developer Portal
    participant Auth as Partner Auth
    participant Key as API Key Service
    participant Sandbox as Sandbox API
    participant API as SilverCare API

    Dev->>Portal: 로그인
    Portal->>Auth: 파트너 계정 검증
    Auth-->>Portal: tenant_id와 권한 반환
    Dev->>Portal: API Key 발급 요청
    Portal->>Key: create key for tenant
    Key-->>Portal: API Key 1회 표시
    Dev->>Portal: Swagger와 샘플 확인
    Dev->>Sandbox: sample elder profile로 샘플 요청 실행
    Sandbox->>API: 정상/안전/오류 샘플 호출
    API-->>Sandbox: 표준 응답 세트 반환
    Sandbox-->>Dev: 재현 가능한 샘플 결과 표시
```

#### Consent Withdrawal and Cascade Deletion

```mermaid
sequenceDiagram
    participant User as Elder/Authorized Actor
    participant Partner as Partner System
    participant Consent as Consent Service
    participant Delete as Deletion Orchestrator
    participant Data as Data Stores
    participant Backup as Backup System
    participant Audit as Audit Log

    User->>Partner: 동의 철회 또는 삭제 요청
    Partner->>Consent: consent update/delete request
    Consent->>Audit: request_id와 actor 기록
    Consent->>Delete: cascade deletion job 생성
    Delete->>Data: raw messages, memory, analysis, report, evaluation candidates 삭제 또는 비식별화
    Data-->>Delete: deletion status
    Delete->>Backup: 백업 삭제 재적용 대상 등록
    Backup-->>Delete: backup deletion scheduled
    Delete->>Audit: 완료/예외 상태 기록
    Consent-->>Partner: 처리 상태 반환
```

#### Phase B Sensor and Emergency Alert

```mermaid
sequenceDiagram
    participant Sensor as Partner Sensor Device
    participant Ingest as Sensor Ingest API
    participant TSDB as Time-Series Store
    participant Risk as Combined Risk Engine
    participant Alert as Emergency Alert API
    participant Guardian as Guardian/Institution Webhook

    Sensor->>Ingest: POST /api/v2/sensor/ingest
    Ingest->>Ingest: API Key, tenant_id, schema 검증
    Ingest->>TSDB: sensor_event 저장
    TSDB-->>Risk: 최근 무활동/수면/건강 신호 제공
    Risk->>Risk: 대화 risk_event와 센서 신호 결합
    alt high 또는 critical 판단
        Risk->>Alert: /api/v2/alert/emergency 요청
        Alert->>Guardian: Webhook alert 전송
        Guardian-->>Alert: delivery status
    end
```

#### Validation Plan Coverage

| Validation Area | Method | Acceptance |
|---|---|---|
| Internal alpha simulation | 가상의 할머니/할아버지 페르소나 봇 100개로 대화 시뮬레이션 | 환각, 지연시간, 안전 응답, 개인정보 마스킹 테스트 결과가 배포 게이트를 통과 |
| First PoC | 중소 제조사 1곳, 1~3개월, 어르신 50가구 대상 API 검증 | 인당 일 5회 이상 대화, p95 800ms 이하, 안전 응답 정상 발동 |
| AI safety evaluation | 합성 또는 동의/가명 처리된 평가셋 기반 회귀 테스트 | 위험 Recall 90% 이상, critical Recall 95% 이상, 고위험 오탐률 10% 이하 |
| Privacy validation | 응답, 로그, 리포트, 평가셋 후보의 PII 검사 | 불필요한 원문/PII 노출 0건 |
| Paid conversion review | PoC 유료 전환 조건 6개 중 3개 이상 충족 여부 검토 | 충족 시 월 기본료 + 활성 디바이스 + 초과 사용량 과금 구조로 정식 계약 협의 제안 |

### 6.4 Web Console Surface Specification

SilverCare Partner Console은 B2B 개발자, B2B 제품/사업 담당자, 플랫폼 운영자가 API 연동과 PoC 운영을 수행하기 위한 웹 콘솔이다. 본 콘솔은 보호자용 완성 앱, 기관용 완성 앱, 어르신용 앱 UI를 대체하지 않는다.

| Surface | Primary User | Purpose | Required Fields / Data | Required Actions | Access Control | Prohibited Display |
|---|---|---|---|---|---|---|
| Console Home | B2B-DEV, B2B-PM, 운영자 | tenant context와 접근 가능 모듈 확인 | `tenant_id`, user role, module list, environment | module navigation | authenticated session, role check | 다른 tenant 정보 |
| API Key Manager | B2B-DEV | API Key 발급·폐기·상태 확인 | `api_key_id`, status, created_at, revoked_at, last_used_at | create key, revoke key, copy key once | tenant admin or developer role | 원문 API Key 재표시 |
| API Docs | B2B-DEV | API 계약과 샘플 확인 | endpoint, method, request fields, response fields, enum, error code | view docs, copy sample | authenticated developer role | 실제 elder 원문 데이터 |
| Web API Playground | B2B-DEV, B2B-PM | 웹에서 sandbox API 호출과 응답 확인 | endpoint, request payload, status, latency, `request_id`, JSON response | select endpoint, validate request, run sandbox call, generate snippet | sandbox access; production call requires explicit permission | production data by default |
| Sandbox Scenario Picker | B2B-DEV | 정상/안전/오류 응답 재현 | sample elder profile, sample utterance, scenario type | select profile, select scenario, populate request | sandbox access | 실제 개인정보 profile |
| PoC Report Console | B2B-PM | PoC 성과와 과금 검증 지표 확인 | active_devices, active_elders, conversation_count, p95_latency_ms, error_rate, risk_event_count | filter period, view metrics, export CSV/JSON | tenant PM role | 대화 원문, 직접 식별정보 |
| Conversation Log View | 운영자 | 장애/품질/안전 정책 검토 | `request_id`, tenant_id, endpoint, masked utterance, latency, status_code, safety_policy_applied | filter, inspect request, jump to risk event | operator role; raw access exception required for 원문 | default raw transcript, resident registration number, detailed address |
| Emotion/Risk Analysis View | 운영자 | 정서/위험 태그와 권장 액션 확인 | emotion_tags, risk_level, risk_reason, recommended_action, review_status | filter by risk, mark review status | operator role | 의료 진단 표현 |
| User & Consent Admin View | 운영자, 권한 있는 B2B 운영 담당자 | elder/device와 동의 상태 확인 | `elder_id`, `device_id`, consent_state, memory_enabled, deletion_job_status | view consent, request deletion, track job | least privilege, audit log | 실명, 주민등록번호, 상세 주소 |
| Review Queue | 운영자 | 오탐/미탐 및 안전 정책 케이스 리뷰 | request_id, risk_level, masked evidence, labeling result, model/prompt version | assign review, update label, export de-identified sample | operator or reviewer role | 동의/가명 처리 없는 원문 |

### 6.5 Web Console Navigation Flow

```mermaid
flowchart LR
    Login["Login"]
    Home["Console Home<br/>tenant context"]
    Key["API Key Manager"]
    Docs["API Docs"]
    Playground["Web API Playground"]
    Scenario["Sandbox Scenario Picker"]
    Response["Response Viewer<br/>JSON/status/latency/request_id"]
    Snippet["cURL/TypeScript Snippet"]
    Report["PoC Report Console"]
    Ops["Ops Monitoring Console"]
    Logs["Conversation Log View"]
    Analysis["Emotion/Risk Analysis View"]
    Review["Review Queue"]
    Consent["User & Consent Admin View"]
    Export["CSV/JSON Export"]

    Login --> Home
    Home --> Key
    Home --> Docs
    Home --> Playground
    Home --> Report
    Home --> Ops
    Home --> Consent

    Docs --> Playground
    Key --> Playground
    Playground --> Scenario
    Scenario --> Playground
    Playground --> Response
    Response --> Snippet

    Report --> Export
    Ops --> Logs
    Ops --> Analysis
    Analysis --> Review
    Logs --> Review
    Consent --> Review
```

### 6.6 Future Guardian and Institution App Scope Change Guidance

보호자용 웹/앱 또는 기관용 웹/앱을 실버케어가 직접 제공하려면 본 SRS만 수정해서는 안 된다. PRD v0.5-단일구조에서 해당 앱은 Out-of-Scope로 정의되어 있으므로, 제품 범위 변경은 PRD를 먼저 수정한 뒤 SRS와 하위 산출물을 갱신해야 한다.

| Order | Document | Required Change |
|---:|---|---|
| 1 | PRD | `1-4. Phase A MVP 범위`의 보호자/기관 완성 앱 Out-of-Scope 항목을 제거하거나 Phase B/C In-Scope로 이동한다. |
| 2 | PRD | `2. 사용자와 페르소나`에서 보호자와 기관 관리자를 API 수신자가 아닌 직접 앱 사용자로 재정의한다. |
| 3 | PRD | `3. 사용자 스토리와 AC`에 보호자 앱, 기관 웹 대시보드, 알림, 리포트 조회, 권한 관리 Story/AC를 추가한다. |
| 4 | PRD | `5. 비기능 요구사항`과 `5-3. 개인정보 정책`에 앱 보안, 접근성, 알림 지연시간, 원문 열람, 제3자 제공, 삭제/철회 UX 기준을 추가한다. |
| 5 | SRS | `1.2 Scope`, `2. Stakeholders`, `3.2 Client Applications`, `4.1 Functional Requirements`, `4.2 NFR`, `5. Traceability Matrix`, Appendix UI/API/Data sections를 갱신한다. |
| 6 | UX/UI Spec | Guardian App, Institution Web Console의 IA, 화면 흐름, 권한별 화면, empty/error/loading state를 정의한다. |
| 7 | OpenAPI Spec | 앱에서 소비할 auth, report, notification, consent, pagination, error schema를 확정한다. |
| 8 | Privacy / Data Policy | 보호자/기관 제공 항목, 제공받는 자, 목적, 보유기간, 대리 동의, 원문 열람 권한을 확정한다. |
| 9 | Test Plan | 보호자/기관 사용자 시나리오, 권한 분리, 개인정보 노출, 알림 지연시간, 접근성 테스트를 추가한다. |

| Future UI Candidate | Current Status | Required Governance |
|---|---|---|
| Guardian App | Out-of-Scope | PRD scope change and consent policy update required |
| Institution Web Console | Out-of-Scope | PRD scope change, institution RBAC, report data contract required |
| Elder App UI | Out-of-Scope | Partner device responsibility unless PRD strategy changes |
| Emergency Response UI | Out-of-Scope | Legal responsibility and emergency operation ADR required |

### 6.7 MVP Implementation Cut and Cost Efficiency Guardrails

단일구조 MVP는 기능 가치 검증과 개발 속도를 우선한다. 아래 구현 컷라인은 `REQ-FUNC`와 `REQ-NF`를 삭제하지 않고, 실제 MVP 1차 구현 순서를 명확히 하기 위한 기준이다.

#### MVP Implementation Cut

| Cut | Implementation Scope | Included Requirements | Completion Standard |
|---|---|---|---|
| P0 | Next.js App Router, Tailwind CSS, shadcn/ui, Prisma, local SQLite, Supabase PostgreSQL 연결 | C-TEC-001 to C-TEC-004 | 로컬 개발과 Supabase migration dry-run이 통과한다. |
| P0 | tenant, user, API key, audit log, usage event 기본 모델 | REQ-FUNC-001, REQ-FUNC-004 to REQ-FUNC-006, REQ-FUNC-014, REQ-FUNC-026 | tenant 격리, API Key 인증, 감사 로그 저장이 동작한다. |
| P0 | API Key Manager 및 Developer Portal 기본 화면 | REQ-FUNC-001, REQ-FUNC-002, REQ-FUNC-049, REQ-FUNC-050 | API Key 발급/폐기와 cURL/TypeScript 샘플 확인이 가능하다. |
| P0 | `/api/v1/chat/reply` Route Handler와 Gemini provider adapter | REQ-FUNC-007 to REQ-FUNC-014, REQ-NF-042, REQ-NF-043 | 안전 응답, 분석 필드, request_id, usage event가 반환·저장된다. |
| P0 | Web API Playground와 Sandbox Scenario Picker | REQ-FUNC-003, REQ-FUNC-051 to REQ-FUNC-055, REQ-NF-039, REQ-NF-044 | sandbox 기본 실행, validation, JSON response viewer, quota가 동작한다. |
| P1 | `/api/v1/analyze/emotion` | REQ-FUNC-016 to REQ-FUNC-019 | emotion/risk/action enum이 schema에 맞게 반환된다. |
| P1 | PoC Report API와 PoC Report Console | REQ-FUNC-022 to REQ-FUNC-026, REQ-FUNC-056 | 활성 디바이스, 대화 수, 지연시간, 오류율, 위험 이벤트 수가 집계된다. |
| P1 | Ops Monitoring, Conversation Log, Review Queue | REQ-FUNC-033 to REQ-FUNC-035, REQ-FUNC-057, REQ-FUNC-058 | 마스킹 로그, 위험 리뷰 큐, LLM timeout 항목을 조회할 수 있다. |
| P1 | User & Consent Admin View | REQ-FUNC-036 to REQ-FUNC-039, REQ-FUNC-059 | 동의 상태, memory enabled, deletion job status를 권한 기반으로 조회한다. |
| P2 | `/api/v1/schedule/proactive` | REQ-FUNC-020, REQ-FUNC-021 | 일반 안부/확인성 발화가 생성되고 의료 지시 문구가 생성되지 않는다. |
| Post-MVP | Phase B sensor, emergency alert, KPI report | REQ-FUNC-040 to REQ-FUNC-047 | 본 단일구조 MVP 1차 구현 컷에서는 제외하고 추적성만 유지한다. |

#### Cost Efficiency Guardrails

| Guardrail ID | Guardrail | Required Behavior | Related Requirements |
|---|---|---|---|
| COST-001 | Default Model | 기본 LLM 모델은 `gemini-2.5-flash-lite` 또는 동등한 저비용 모델로 설정한다. | REQ-NF-042 |
| COST-002 | Model Escalation | 상위 모델은 위험/불확실 케이스 또는 운영자 승인 케이스에만 사용하고 routing reason을 저장한다. | REQ-NF-042 |
| COST-003 | Token Cap | 기본 입력 1,200 tokens 이하, 출력 350 tokens 이하를 목표로 하고 초과 시 요약 또는 truncation을 적용한다. | REQ-NF-043 |
| COST-004 | Playground Quota | Web API Playground는 sandbox 사용자별 일일 실행 제한을 가져야 하며 production 호출은 별도 권한을 요구한다. | REQ-NF-039, REQ-NF-044 |
| COST-005 | Tenant Quota | tenant별 일/월 호출량 quota와 초과 상태를 usage event에서 재계산할 수 있어야 한다. | REQ-NF-029, REQ-NF-030, REQ-NF-044 |
| COST-006 | Retry Cap | LLM timeout과 provider 오류 재시도는 상한을 가져야 하며 retry storm을 방지해야 한다. | REQ-NF-005, REQ-NF-044 |
| COST-007 | Budget Alert | Gemini, Vercel, Supabase 사용 비용은 월 예산의 50%, 75%, 90% 도달 시 운영자 확인 이벤트를 남겨야 한다. | REQ-NF-044 |
| COST-008 | Retention Control | raw transcript와 운영 로그는 보존 기간 정책에 따라 삭제 또는 요약되어 DB/log 비용 증가를 제한해야 한다. | REQ-NF-024 to REQ-NF-027 |

#### MVP Value Preservation Check

| Core Value | Preservation Result | Evidence |
|---|---|---|
| B2B 개발자가 30분 이내 첫 API 호출을 성공한다. | 보존 | Developer Portal, API Key Manager, Web API Playground, TypeScript snippet을 P0로 유지한다. |
| 파트너 기기에서 시니어 대화 응답을 검증한다. | 보존 | `/api/v1/chat/reply`, 안전 응답, 정서/위험 필드를 P0로 유지한다. |
| PoC에서 유료 전환 판단 지표를 확보한다. | 보존 | usage event와 PoC Report를 P1로 유지한다. |
| 개인정보와 안전 리스크를 MVP에서 최소 통제한다. | 보존 | tenant 격리, RBAC, audit log, PII masking, consent view를 P0/P1에 포함한다. |
| 개발 속도와 비용 효율성을 유지한다. | 보존 | Phase B 기능은 Post-MVP로 분리하고 Gemini model/token/quota guardrail을 NFR로 추가한다. |
