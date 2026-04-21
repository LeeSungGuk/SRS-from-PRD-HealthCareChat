# SRS v02 FastAPI MVP Conversion Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** `SRS_v02._gpt.md`를 MVP 구현 관점에서 Vite React 프론트엔드, Python FastAPI 백엔드, MySQL 8.x, LangChain 기반 LLM 오케스트레이션, 사내 LLM Gateway, REST/OpenAPI 3.x 구조에 맞게 수정한 `SRS_v03_fastapi_mvp.md`를 작성한다.

**Architecture:** B2B 파트너 기기와 개발자 포털은 HTTPS REST API를 통해 단일 FastAPI 백엔드 애플리케이션과 통신한다. FastAPI 내부 모듈은 인증, 대화 응답, 감정 분석, 선제 발화, 메모리, 개인정보 마스킹, 감사 로그, 리포트 기능을 담당한다. 데이터는 MySQL에 저장하고, LLM 호출은 LangChain 어댑터를 통해 사내 LLM Gateway로 위임하며 기본 Provider는 Google Gemini로 둔다. Vite React는 B2B 개발자 포털 및 운영 확인 UI를 제공한다.

**Tech Stack:** Vite React.js, Python 3.11+, FastAPI, Pydantic, SQLAlchemy 또는 SQLModel, Alembic, MySQL 8.x InnoDB utf8mb4, LangChain, 사내 LLM Gateway, Google Gemini, REST/OpenAPI 3.x, TLS 1.3.

---

## 1. 작업 범위

### 1.1 수정 대상

| 구분 | 경로 | 처리 방식 |
|---|---|---|
| 원본 SRS | `/Users/jin3137/practice/SRS-from-PRD/SRS-Drafts-Op/SRS_v02._gpt.md` | 직접 덮어쓰지 않고 기준 문서로 사용 |
| 신규 SRS 산출물 | `/Users/jin3137/practice/SRS-from-PRD/SRS-Drafts-Op/SRS_v03_fastapi_mvp.md` | 본 계획에 따라 새 파일로 작성 |
| 본 계획서 | `/Users/jin3137/practice/SRS-from-PRD/SRS-Drafts-Op/plans/SRS_v02_fastapi_mvp_conversion_plan.md` | 실행 기준 문서 |

### 1.2 반영할 기술 제약

| ID | 제약사항 |
|---|---|
| C-TEC-001 | 프론트엔드 서비스는 Vite 기반 React.js로 구현한다. |
| C-TEC-002 | 백엔드 코어 서비스는 Python 3.11+ 및 FastAPI로 구현한다. |
| C-TEC-003 | 데이터베이스는 MySQL 8.x 이상, InnoDB, utf8mb4 문자셋을 사용한다. |
| C-TEC-004 | 시니어 대화 생성, 감정 분석, 선제 발화, LLM 오케스트레이션은 FastAPI 백엔드 내부 모듈과 LangChain으로 구현한다. |
| C-TEC-005 | LLM 호출은 사내 LLM Gateway를 통해 수행하며 기본 Provider는 Google Gemini로 한다. Provider 교체는 어댑터 설정으로 가능해야 한다. |
| C-TEC-006 | 외부 및 내부 MVP 통신은 REST(OpenAPI 3.x)를 기본으로 한다. gRPC와 MSA 분리는 Post-MVP 확장으로 둔다. |
| C-TEC-007 | MVP에서는 단일 FastAPI 백엔드 애플리케이션을 기본 배포 단위로 삼고, 서비스 분리는 트래픽 및 조직 규모 검증 이후로 미룬다. |

### 1.3 변경하지 않을 비즈니스 원칙

| 원칙 | 유지 기준 |
|---|---|
| PRD Source of Truth | `실버케어초안_PRD_v0.3.md`의 Story, AC, KPI, F1-F6 기능 출처는 유지한다. |
| MVP 핵심 가치 | B2B 파트너가 자체 AI 모델 없이 시니어 대화 API를 연동할 수 있어야 한다. |
| Phase A 우선순위 | 대화 응답, 감정 분석, 선제 발화, 개발자 문서, 인증, 개인정보 보호, 감사 로그를 Must로 유지한다. |
| Phase B 확장 | 센서 수집, 응급 Webhook, 보호자/기관 리포트는 Should 또는 Post-MVP 확장으로 유지하되 요구사항 추적성은 삭제하지 않는다. |
| ISO 29148 구조 | Introduction, Stakeholders, System Context, Specific Requirements, Traceability Matrix, Appendix 구조를 유지한다. |

## 2. MVP 기능 커버리지 판단

### 2.1 결론

FastAPI 백엔드로 변경해도 MVP의 핵심 사용자 경험과 가치 전달은 훼손되지 않는다. 단, 기존 SRS의 API Gateway 및 독립 서비스 표현을 물리적 MSA로 해석하지 않도록 FastAPI 내부 모듈 구조로 재작성해야 하며, LLM 응답 지연 시간 목표는 비LLM API와 LLM 생성 API를 분리해 측정해야 한다.

### 2.2 가치 흐름별 검토

| 가치 흐름 | 기존 SRS 가치 | FastAPI 전환 후 영향 | 보존 조건 | 판단 |
|---|---|---|---|---|
| B2B 파트너 연동 | REST API와 OpenAPI 문서로 빠른 연동 제공 | FastAPI는 OpenAPI 자동 생성과 Swagger UI를 기본 제공하므로 개발자 경험이 향상된다. | `/openapi.json`, `/docs`, 샘플 요청/응답, API Key 인증 예제를 SRS에 명시한다. | 보존 |
| 어르신 대화 경험 | `chat/reply`가 개인화 응답과 감정 메타데이터를 반환 | 백엔드 언어 변경은 최종 대화 경험에 직접 영향이 없다. | 메모리 조회, PII 마스킹, 의료 안전 가드레일, LLM Gateway 호출 흐름을 유지한다. | 보존 |
| 선제 발화 경험 | 기상, 식사, 복약 상황에서 먼저 말을 걸 수 있음 | FastAPI 내부 스케줄/트리거 모듈로 구현 가능하다. | `schedule/proactive` API와 `ProactivePromptLog` 저장 모델을 유지한다. | 보존 |
| 보호자/기관 가치 | 감정 분석, 위험 징후, 리포트 기반 상태 확인 | MVP에서 고급 리포트와 센서 기반 응급 판단을 축소하면 Phase B 가치는 지연된다. | Phase A에는 감정 분석 결과 저장과 기본 조회 가능성을 유지하고, Phase B 요구사항은 추적성 있게 남긴다. | 조건부 보존 |
| 운영 가치 | SLA, p95, 오류율, 감사 로그, 비용 지표 측정 | FastAPI에서도 미들웨어와 로그 테이블로 구현 가능하다. | 요청 ID, 테넌트 ID, 엔드포인트별 latency, 5xx, LLM 비용 지표를 요구사항에 반영한다. | 보존 |
| 장기 확장성 | 10,000대 동시 활성 기기 목표 | 단일 FastAPI MVP만으로 즉시 보장하기 어렵다. | MVP 검증 목표와 운영 목표를 분리하고, 수평 확장 가능성 및 큐 도입 기준을 명시한다. | 조건부 보존 |

### 2.3 핵심 리스크

| 리스크 ID | 내용 | 영향 | 계획상 대응 |
|---|---|---|---|
| R-MVP-001 | `800ms p95`를 LLM 전체 응답 완료 시간으로 해석하면 Gemini 호출 지연 때문에 현실성이 낮다. | 대화 API NFR가 구현 불가능해질 수 있다. | NFR을 `비LLM API p95`, `LLM 스트리밍 첫 토큰 또는 첫 응답 시작 p95`, `LLM 전체 완료 p95`로 분리한다. |
| R-MVP-002 | FastAPI 전환 후 기존 `API Gateway`, `Chat Reply Service` 표현이 별도 서비스처럼 남으면 구현 방향이 혼선된다. | MVP 범위가 불필요하게 MSA로 커질 수 있다. | System Context, Component Diagram, Class Diagram을 FastAPI 내부 모듈 기준으로 재작성한다. |
| R-MVP-003 | MySQL만으로 센서 시계열 대량 수집을 모두 처리한다고 명시하면 Phase B 확장성이 약해진다. | 센서/응급 기능의 장기 확장 리스크가 커진다. | MVP는 MySQL 저장으로 제한하고, Post-MVP에서 TimescaleDB, 파티셔닝, 큐 도입 조건을 명시한다. |
| R-MVP-004 | 사내 LLM Gateway가 제공되지 않는 경우 MVP 대화 기능이 차단된다. | 핵심 기능 검증이 지연될 수 있다. | Gateway 인터페이스, Provider 추상화, Gemini 직접 호출 대체 설정을 제약 또는 가정에 명시한다. |
| R-MVP-005 | Vite React 포털을 과도하게 제품 UI로 확장하면 MVP가 B2B API 검증에서 벗어난다. | 구현 범위가 커진다. | 개발자 포털은 문서, API Key 확인, 샘플 코드, 간단한 호출 테스트로 제한한다. |

## 3. SRS 수정 작업 계획

### 3.1 산출물 생성

- [ ] `SRS_v02._gpt.md`를 기준으로 신규 파일 `SRS_v03_fastapi_mvp.md`를 만든다.
- [ ] 문서 헤더의 `Revision`을 `3.0`으로 변경하고 `Date`는 `2026-04-21`로 유지한다.
- [ ] `References`에 기술 스택 결정 근거를 `REF-03`으로 추가한다.
- [ ] 기존 Story, AC, F1-F6, KPI 출처는 삭제하지 않는다.

### 3.2 Introduction 및 Scope 수정

- [ ] `1.2 Scope`에서 Phase A를 MVP 구현 범위로 명확히 재정의한다.
- [ ] Phase A In-Scope에 다음 항목을 Must로 유지한다.
  - `POST /api/v1/chat/reply`
  - `POST /api/v1/analyze/emotion`
  - `POST /api/v1/schedule/proactive`
  - Vite React 개발자 포털
  - FastAPI OpenAPI 문서
  - API Key 기반 테넌트 인증
  - 대화, 기억, 감정 분석, 선제 발화 로그 저장
  - PII 마스킹
  - 감사 로그
  - 기본 운영 지표
- [ ] Phase B 또는 Post-MVP 항목으로 다음 범위를 명확히 분리한다.
  - 대량 센서 수집
  - 응급 Webhook 재시도 및 보장 전송
  - 보호자/기관 고급 리포트
  - 10,000대 동시 활성 기기 부하 검증
  - gRPC 또는 MSA 분리
- [ ] `Constraints and Assumptions`를 `1.5 Assumptions & Constraints`로 승격하거나, 기존 위치를 유지하되 `C-TEC-001`부터 `C-TEC-007`까지 추가한다.
- [ ] `MVP` 정의를 “Phase A 캐릭터챗 API + 감정 분석 + 선제 발화 + 개발자 포털 + 보안/관측성 최소 범위”로 갱신한다.

### 3.3 System Context and Interfaces 수정

- [ ] `3.1 External Systems`에서 `AI 대화/분석 엔진`을 별도 실버케어 서비스처럼 표현하지 않고 `사내 LLM Gateway`와 `Google Gemini Provider`로 분리한다.
- [ ] `3.1 External Systems`에 MySQL은 외부 시스템이 아니라 내부 데이터 저장소로 표현한다.
- [ ] `3.2 Client Applications`에 Vite React 개발자 포털을 명시한다.
- [ ] `3.3 API Overview`에 FastAPI 제공 엔드포인트를 기준으로 정리한다.
- [ ] `/openapi` 표현을 FastAPI 기본 산출물인 `/openapi.json` 및 `/docs`로 수정한다.
- [ ] `API Gateway` 명칭은 물리 컴포넌트가 아니라 FastAPI 미들웨어 또는 라우팅 계층으로 변경한다.

### 3.4 API 목록 수정

- [ ] Appendix `6.1 API Endpoint List`를 FastAPI 기준으로 갱신한다.
- [ ] MVP API는 다음으로 고정한다.
  - `POST /api/v1/chat/reply`
  - `POST /api/v1/analyze/emotion`
  - `POST /api/v1/schedule/proactive`
  - `GET /openapi.json`
  - `GET /docs`
- [ ] Post-MVP API는 기존 추적성을 유지하되 Phase B로 명확히 표시한다.
  - `POST /api/v2/sensor/ingest`
  - `POST /api/v2/alert/emergency`
  - `GET /api/v2/report/kpi`
- [ ] 모든 API의 Request/Response 스키마는 Pydantic 모델명 기준으로 보강한다.
- [ ] 인증 방식은 `X-API-Key` 또는 `Authorization: Bearer <tenant api key>` 중 하나로 확정해 SRS 전체에 동일하게 반영한다.

### 3.5 Functional Requirements 수정

- [ ] 기존 `REQ-FUNC-001`부터 `REQ-FUNC-029`까지의 Story 및 AC 연결은 유지한다.
- [ ] FastAPI 구현과 직접 연결되는 요구사항을 추가 또는 보강한다.
  - Pydantic 요청 검증 실패 시 `422` 오류를 반환한다.
  - 인증 실패 시 `401`, 테넌트 권한 위반 시 `403` 오류를 반환한다.
  - 모든 DB 조회와 저장은 `tenant_id` 범위를 포함해야 한다.
  - 대화 응답 생성은 LangChain 오케스트레이션 모듈을 통해 LLM Gateway를 호출해야 한다.
  - LLM Gateway 오류 시 표준 오류 코드와 감사 로그를 남겨야 한다.
  - FastAPI는 OpenAPI 3.x 문서를 자동 생성하고 개발자 포털에서 접근 가능해야 한다.
- [ ] 기존 Phase B 요구사항은 삭제하지 않고 Priority를 `Should` 또는 `Could`로 검토한다.
- [ ] 신규 요구사항 ID는 기존 번호 뒤에서 이어서 `REQ-FUNC-030`부터 부여한다.

### 3.6 Non-Functional Requirements 수정

- [ ] `REQ-NF-001`의 800ms 기준을 다음과 같이 분리한다.
  - 인증, 저장, 조회 중심 비LLM API 서버 처리 p95는 800ms 이하
  - LLM 대화 API의 첫 응답 시작 또는 스트리밍 시작 p95는 800ms 이하
  - LLM 전체 응답 완료 p95는 MVP 검증 조건에서 3초 이하
- [ ] FastAPI 및 Uvicorn/Gunicorn 배포 기준의 헬스체크, 타임아웃, 오류율 요구사항을 추가한다.
- [ ] MySQL 요구사항을 추가한다.
  - InnoDB 사용
  - utf8mb4 문자셋
  - `tenant_id`, `elder_user_id`, `created_at` 복합 인덱스
  - Alembic 마이그레이션 적용
- [ ] LLM Gateway 요구사항을 추가한다.
  - Provider 교체 가능성
  - 호출 타임아웃
  - 재시도 정책
  - 프롬프트 및 응답 감사 로그
  - 비용 산출용 토큰 사용량 기록
- [ ] 운영 지표에 FastAPI HTTP latency, 5xx rate, LLM latency, LLM error rate, MySQL query latency, API 비용, Gemini token usage를 포함한다.
- [ ] 신규 요구사항 ID는 기존 번호 뒤에서 이어서 `REQ-NF-025`부터 부여한다.

### 3.7 데이터 모델 수정

- [ ] Appendix `6.2 Entity & Data Model`을 MySQL 테이블 중심으로 갱신한다.
- [ ] Entity 명칭과 DB 테이블명을 함께 표기한다.
- [ ] 최소 MVP 테이블은 다음을 포함한다.
  - `tenant`
  - `api_key`
  - `developer_user`
  - `elder_user`
  - `device`
  - `conversation_turn`
  - `memory_fact`
  - `emotion_analysis`
  - `proactive_prompt_log`
  - `audit_log`
  - `llm_call_log`
- [ ] Phase B 테이블은 다음을 포함하되 Post-MVP로 표시한다.
  - `sensor_reading`
  - `emergency_alert`
  - `webhook_delivery_log`
  - `report`
- [ ] 각 테이블에 Primary Key, Foreign Key, Unique Key, Index, Required, Data Type, Retention Policy를 표로 정리한다.
- [ ] ERD를 MySQL 테이블명 기준으로 다시 작성한다.

### 3.8 다이어그램 수정

- [ ] `3.4 Interaction Sequences`의 핵심 시퀀스 다이어그램을 FastAPI 구조로 재작성한다.
- [ ] Appendix `6.3 Detailed Interaction Models`에 상세 시퀀스 다이어그램 3개에서 5개를 유지한다.
- [ ] UseCase Diagram은 기존 사용 사례를 유지하되 개발자 포털과 FastAPI OpenAPI 문서 흐름을 반영한다.
- [ ] Component Diagram은 다음 구성으로 수정한다.
  - Vite React Developer Portal
  - Partner Device
  - FastAPI Application
  - Auth Dependency
  - Chat Router
  - Emotion Router
  - Proactive Router
  - Service Layer
  - Repository Layer
  - MySQL
  - LangChain Orchestrator
  - LLM Gateway
  - Google Gemini Provider
- [ ] Class Diagram은 FastAPI router, Pydantic schema, service, repository, gateway adapter 중심으로 재작성한다.
- [ ] Sequence Diagram은 다음 4개를 권장한다.
  - 개발자 포털에서 OpenAPI 문서 확인 및 API Key 테스트 호출
  - `POST /api/v1/chat/reply` 처리 흐름
  - `POST /api/v1/analyze/emotion` 처리 흐름
  - `POST /api/v1/schedule/proactive` 처리 흐름

### 3.9 Traceability Matrix 수정

- [ ] 기존 Story, AC, Feature, KPI 매핑은 유지한다.
- [ ] 신규 C-TEC 제약사항을 요구사항과 테스트 케이스에 연결한다.
- [ ] 예시 매핑은 다음 기준을 따른다.

| Source | Requirement ID | Test Case ID |
|---|---|---|
| C-TEC-001 Vite React | REQ-FUNC-007, 신규 개발자 포털 요구사항 | TC-TEC-001 |
| C-TEC-002 FastAPI | 신규 API 구현 요구사항, REQ-NF-020 | TC-TEC-002 |
| C-TEC-003 MySQL | 신규 데이터 저장 및 마이그레이션 NFR | TC-TEC-003 |
| C-TEC-004 LangChain | 대화, 감정 분석, 선제 발화 LLM 요구사항 | TC-TEC-004 |
| C-TEC-005 LLM Gateway | LLM Provider 추상화 및 장애 처리 요구사항 | TC-TEC-005 |
| C-TEC-006 REST/OpenAPI | API Overview, OpenAPI NFR | TC-TEC-006 |
| C-TEC-007 단일 FastAPI MVP | Scope, Component Diagram, 배포 제약 | TC-TEC-007 |

### 3.10 Validation Plan 수정

- [ ] Appendix Validation Plan에 FastAPI MVP 검증 항목을 추가한다.
- [ ] 최소 검증 항목은 다음을 포함한다.
  - OpenAPI schema 생성 검증
  - Pydantic 요청 검증 검증
  - API Key 인증 및 테넌트 격리 검증
  - MySQL 마이그레이션 검증
  - LLM Gateway Provider 교체 검증
  - Gemini Provider 호출 실패 시 표준 오류 응답 검증
  - 100개 가상 어르신 페르소나 알파 시뮬레이션 검증

## 4. 구현 시 주의할 판단 기준

### 4.1 MVP에서 반드시 지킬 것

| 기준 | 이유 |
|---|---|
| API 계약 우선 | B2B 파트너의 핵심 가치는 빠른 연동 가능성이다. |
| 대화 품질 우선 | 어르신 최종 경험은 백엔드 스택보다 응답 품질, 기억, 안전 가드레일에 좌우된다. |
| OpenAPI 자동 문서화 | FastAPI 채택의 실질적 장점이며 B2B 개발자 경험을 개선한다. |
| 테넌트 격리 | B2B SaaS에서 초기부터 무너지면 이후 복구 비용이 크다. |
| PII 마스킹 | 시니어 대화 데이터는 민감도가 높으므로 MVP에서도 제외할 수 없다. |
| 감사 로그 | LLM 응답, 개인정보 처리, 인증 실패를 추적할 수 있어야 PoC 신뢰를 얻을 수 있다. |

### 4.2 MVP에서 과감히 줄일 것

| 축소 항목 | 처리 방침 |
|---|---|
| MSA 분리 | FastAPI 내부 모듈로만 분리하고 물리 서비스 분리는 Post-MVP로 둔다. |
| gRPC | REST/OpenAPI만 사용한다. |
| 대량 센서 처리 | Phase B로 유지하고 MVP 데이터 모델에는 확장 가능성만 반영한다. |
| 고급 보호자 리포트 UI | MVP에서는 API 응답과 기본 데이터 저장까지만 요구한다. |
| 응급 Webhook 보장 전송 | Phase B에서 큐와 재시도 정책을 구체화한다. |

## 5. 완료 기준

### 5.1 문서 품질 기준

- [ ] `SRS_v03_fastapi_mvp.md`는 ISO/IEC/IEEE 29148:2018 구조를 유지한다.
- [ ] 모든 Functional Requirement는 `REQ-FUNC-xxx` ID를 가진다.
- [ ] 모든 Non-Functional Requirement는 `REQ-NF-xxx` ID를 가진다.
- [ ] 모든 PRD Story와 AC는 Functional Requirement의 Source로 연결된다.
- [ ] 모든 KPI와 성능 목표는 Non-Functional Requirement에 반영된다.
- [ ] 모든 API는 `3.3 API Overview`와 `6.1 API Endpoint List`에 중복 없이 반영된다.
- [ ] 모든 엔터티와 스키마는 Appendix 표와 ERD에 반영된다.
- [ ] Traceability Matrix에는 Story, AC, Feature, KPI, C-TEC, Test Case ID가 누락 없이 연결된다.
- [ ] UseCase Diagram, ERD, Class Diagram, Component Diagram, 3개에서 5개의 Sequence Diagram이 포함된다.

### 5.2 MVP 가치 보존 기준

- [ ] B2B 개발자는 문서 확인 후 Phase A API 3개를 호출할 수 있어야 한다.
- [ ] 파트너 기기는 어르신 발화 텍스트를 보내고 `replyText`, 감정 메타데이터, 위험 키워드를 받을 수 있어야 한다.
- [ ] 시스템은 어르신별 기억을 저장하고 이후 대화 또는 선제 발화에서 참조할 수 있어야 한다.
- [ ] 시스템은 의료 진단, 처방, 치료 지시를 차단해야 한다.
- [ ] 시스템은 PII 마스킹, 테넌트 격리, 감사 로그를 MVP에서 제공해야 한다.
- [ ] 시스템은 PoC 성공 여부를 판단할 수 있는 대화 호출 수, latency, 오류율, 감정/위험 감지 정확도 지표를 수집해야 한다.

## 6. 검증 명령

실행자는 SRS 수정 완료 후 다음 명령으로 구조적 누락을 확인한다.

```bash
test -f SRS_v03_fastapi_mvp.md
rg -n "C-TEC-001|FastAPI|Vite|MySQL|LangChain|LLM Gateway|Google Gemini|OpenAPI 3" SRS_v03_fastapi_mvp.md
rg -n "POST /api/v1/chat/reply|POST /api/v1/analyze/emotion|POST /api/v1/schedule/proactive|GET /openapi.json|GET /docs" SRS_v03_fastapi_mvp.md
rg -n "REQ-FUNC-030|REQ-NF-025|Traceability Matrix|TC-TEC-001" SRS_v03_fastapi_mvp.md
rg -n "sequenceDiagram|erDiagram|classDiagram|flowchart|Component" SRS_v03_fastapi_mvp.md
rg -n "tenant|api_key|elder_user|conversation_turn|memory_fact|emotion_analysis|proactive_prompt_log|audit_log|llm_call_log" SRS_v03_fastapi_mvp.md
```

다음 명령은 모호 표현과 미완성 표식을 확인하기 위한 방어적 검사다. 출력이 없을 때 통과로 본다.

```bash
rg -n "\\b(TB[D]|TO[D]O)\\b|빠르게|적절히|원활히" SRS_v03_fastapi_mvp.md
```

## 7. 예상 산출물 요약

| 산출물 | 설명 |
|---|---|
| `SRS_v03_fastapi_mvp.md` | FastAPI 기반 MVP 구현 제약이 전면 반영된 SRS |
| 개정된 `1.5 Assumptions & Constraints` | C-TEC-001부터 C-TEC-007까지 포함 |
| 개정된 System Context | Vite React, FastAPI, MySQL, LangChain, LLM Gateway 중심 구조 |
| 개정된 Functional/NFR | PRD 기능 추적성을 유지하면서 FastAPI 구현 요구사항 추가 |
| 개정된 Appendix | API 목록, MySQL 데이터 모델, ERD, Class, Component, Sequence Diagram 포함 |
| 개정된 Traceability Matrix | Story, AC, Feature, KPI, C-TEC, Test Case ID까지 추적 |

## 8. 최종 판단

이 변경은 “기술 스택을 바꾸는 작업”이지 “제품 가치를 바꾸는 작업”이 아니다. MVP의 핵심 가치는 B2B 파트너가 시니어 대화 지능을 API로 빠르게 연동하고, 어르신에게 개인화된 대화와 선제 발화를 제공하며, 보호자/기관 확장으로 이어질 수 있는 데이터를 축적하는 것이다.

FastAPI, MySQL, LangChain, LLM Gateway 조합은 이 핵심 흐름을 구현하기에 충분하다. 다만 기존 SRS의 MSA식 표현을 그대로 두면 구현 범위가 과대해지므로, SRS 개정에서는 “단일 FastAPI 애플리케이션 내부 모듈화”와 “Post-MVP 확장 경계”를 가장 강하게 고정해야 한다.
