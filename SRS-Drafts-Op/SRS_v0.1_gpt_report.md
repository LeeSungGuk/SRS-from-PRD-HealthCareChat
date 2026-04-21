# SRS_v0.1.md 검토 보고서

검토 대상: `SRS_v0.1.md`  
검토 기준: 사용자 지정 8개 조건  
검토일: 2026-04-18  
검토자: GPT

## 1. 종합 결론

`SRS_v0.1.md`는 PRD의 핵심 Story, AC, API, KPI, NFR, 추적성, ISO 29148 기반 문서 구조를 상당 부분 반영하고 있다. 다만 다이어그램 요구사항 관점에서는 현재 Sequence Diagram 중심으로 작성되어 있으며, UseCase Diagram, ERD, Class Diagram, Component Diagram이 누락되어 있다.

| 구분 | 판정 | 핵심 사유 |
|---|---|---|
| Story·AC → REQ-FUNC 반영 | 충족 | AC-1, AC-2, AC-3이 REQ-FUNC에 연결됨 |
| KPI·성능 목표 → REQ-NF 반영 | 충족 | 3개 PoC, 일 5회 호출, 800ms, 90%, 99.9%, 10,000대, TLS/AES 등 반영 |
| API 목록의 인터페이스 반영 | 충족 | 3.3 API Overview와 6.1 API Endpoint List에 API-001~API-007 반영 |
| 엔터티·스키마 Appendix 완성도 | 부분 충족 | 엔터티 표는 있으나 PK/FK, 관계, 카디널리티, enum 상세가 부족함 |
| Traceability Matrix 누락 여부 | 부분 충족 | Story/AC/F1~F6/KPI는 연결됨. R2, ADR-002, 일부 Scope/Constraint는 직접 추적 행이 없음 |
| UseCase, ERD, Class, Component Diagram | 미충족 | 현재 SRS에는 sequenceDiagram만 존재함 |
| Sequence Diagram 3~5개 포함 | 부분 충족 | 7개 Sequence Diagram이 있어 포함은 되었으나, 3~5개 조건을 엄격 적용하면 초과 |
| ISO 29148 구조 준수 | 충족 | Introduction, Stakeholders, Interfaces, Requirements, Traceability, Appendix 구조를 갖춤 |

종합 판정: 부분 적합

## 2. 조건별 상세 검토

### 2.1 PRD의 모든 Story·AC가 SRS의 REQ-FUNC에 반영됨

판정: 충족

| PRD Story/AC | SRS 반영 요구사항 | 검토 결과 |
|---|---|---|
| Story B2B AC-1: 1초 이내 시니어 대답 텍스트와 감정 코드 반환 | REQ-FUNC-001, REQ-FUNC-002, REQ-FUNC-003, REQ-NF-002 | 기능 요구사항과 성능 요구사항으로 분리되어 반영됨 |
| Story B2B AC-2: 개발자 포털에서 API 명세와 샘플 코드 확인 | REQ-FUNC-007, REQ-FUNC-008 | 문서와 샘플 코드 요구사항으로 분해됨 |
| Story A AC-3: 과거 허리 통증 문맥 기반 개인화 대화 | REQ-FUNC-004, REQ-FUNC-013, REQ-FUNC-015 | 기억 메모리와 선제 발화 요구사항으로 반영됨 |

의견: Story와 AC는 REQ-FUNC의 Source 컬럼 및 Traceability Matrix에 연결되어 있어 요구사항 추적성이 확보되어 있다.

### 2.2 모든 KPI·성능 목표가 REQ-NF에 반영됨

판정: 충족

| PRD KPI/NFR | SRS 반영 요구사항 | 검토 결과 |
|---|---|---|
| API 연동 B2B 파트너 최소 3개사 확보 | REQ-NF-009 | 반기 파트너 연동 기록 유지 요구사항으로 반영됨 |
| 어르신 1인당 일평균 대화 API 호출 5회 이상 | REQ-NF-008 | 일간 제품 분석 지표 수집 요구사항으로 반영됨 |
| AI API 응답 지연 시간 800ms 이하 | REQ-NF-001, REQ-NF-019 | p95 지연 시간 및 초과 알림 요구사항으로 반영됨 |
| AC-1의 1초 이내 응답 | REQ-NF-002 | End-to-End 응답 시간 요구사항으로 반영됨 |
| 대화 기반 우울/위험 징후 감지 정확도 90% 이상 | REQ-NF-007 | 월간 검증 데이터셋 기준 정확도 요구사항으로 반영됨 |
| API 서버 업타임 99.9% | REQ-NF-004, REQ-NF-019 | SLA 및 알림 요구사항으로 반영됨 |
| 10,000대 이상 동시 호출 | REQ-NF-003, REQ-NF-021 | 동시성 및 수평 확장 요구사항으로 반영됨 |
| TLS 1.3, AES-256 | REQ-NF-010, REQ-NF-011 | 전송 및 저장 암호화 요구사항으로 반영됨 |
| PII 비식별화 | REQ-NF-012 | 저장 전 PII 마스킹 요구사항으로 반영됨 |
| 멀티테넌시 격리 | REQ-NF-013 | API, 저장소, 로그, 리포트, 알림 전반의 격리로 반영됨 |

의견: KPI와 성능 목표는 Functional Requirement가 아니라 Non-Functional Requirement로 적절히 이동되어 있다.

### 2.3 API 목록이 인터페이스 섹션에 모두 반영됨

판정: 충족

| PRD API | SRS 3.3 API Overview | SRS 6.1 API Endpoint List | 검토 결과 |
|---|---|---|---|
| `/api/v1/chat/reply` | API-001 | API-001 | 반영됨 |
| `/api/v1/analyze/emotion` | API-002 | API-002 | 반영됨 |
| `/api/v1/schedule/proactive` | API-003 | API-003 | 반영됨 |
| `/api/v2/sensor/ingest` | API-004 | API-004 | 반영됨 |
| `/api/v2/alert/emergency` | API-005 | API-005 | 반영됨 |
| `/api/v2/report/kpi` | API-006 | API-006 | 반영됨 |
| 개발자 포털/OpenAPI | API-007 | API-007 | PRD AC-2 기반으로 추가 반영됨 |

의견: 모든 PRD API가 시스템 인터페이스 섹션과 Appendix API 목록에 중복 반영되어 있다.

### 2.4 엔터티·스키마가 Appendix에 완성됨

판정: 부분 충족

Appendix 6.2에 다음 엔터티가 표로 정의되어 있다.

| 엔터티 | 검토 결과 |
|---|---|
| Tenant | 포함됨 |
| ApiCredential | 포함됨 |
| Device | 포함됨 |
| ElderUser | 포함됨 |
| ConversationTurn | 포함됨 |
| MemoryFact | 포함됨 |
| EmotionAnalysis | 포함됨 |
| ProactiveScheduleRequest | 포함됨 |
| SensorReading | 포함됨 |
| EmergencyAlert | 포함됨 |
| Report | 포함됨 |
| AuditLog | 포함됨 |

부족한 점:

| 항목 | 상태 | 개선 필요 내용 |
|---|---|---|
| Primary Key | 부족 | 각 Entity의 PK 명시 필요 |
| Foreign Key | 부족 | `tenantId`, `elderUserId`, `deviceId` 등의 참조 관계 명시 필요 |
| 관계/카디널리티 | 부족 | Tenant 1:N Device, ElderUser 1:N ConversationTurn 등 관계 정의 필요 |
| enum 값 상세 | 부분 부족 | `riskLevel`, `severity`, `status`, `tenantType`의 허용 값 표준화 필요 |
| 필드 제약 | 부분 부족 | 길이, nullable, unique, index, retention 정책 등 보강 필요 |

의견: Appendix에 엔터티 표는 존재하지만, “스키마 완성”이라고 보기에는 관계형 구조와 제약 조건이 부족하다. ERD 추가와 함께 보강하는 것이 적절하다.

### 2.5 Traceability Matrix가 누락 없이 생성됨

판정: 부분 충족

충족된 항목:

| Source | Traceability Matrix 반영 여부 |
|---|---|
| STORY-B2B AC-1 | 반영됨 |
| STORY-B2B AC-2 | 반영됨 |
| STORY-A AC-3 | 반영됨 |
| F1~F6 | 반영됨 |
| KPI Latency | 반영됨 |
| KPI Detection Accuracy | 반영됨 |
| KPI Daily API Calls | 반영됨 |
| KPI B2B Partners | 반영됨 |
| 주요 NFR | 반영됨 |
| R1 LLM 환각 | 반영됨 |
| ADR-001 Text-only API | 반영됨 |

누락 또는 직접 추적이 약한 항목:

| PRD 항목 | 현재 상태 | 개선 권고 |
|---|---|---|
| R2 B2B 파트너 확보 실패 | Scope에는 반영되었으나 Traceability Matrix 직접 행 없음 | R2 → REQ-NF-009 또는 SCP-IN-008 연결 행 추가 |
| ADR-002 B2B2C API 모델 채택 | Scope/Constraint에는 반영되었으나 Traceability Matrix 직접 행 없음 | ADR-002 → CON-003, SCP-OUT-001, SCP-OUT-003 연결 행 추가 |
| Scope In/Out 항목 | 본문에는 반영되었으나 Matrix에는 일부만 반영 | Scope ID와 요구사항/검증 항목 연결 추가 |
| Assumption/Constraint | 본문에는 반영되었으나 Matrix에는 일부만 반영 | CON/ASM ID 기반 추적 행 추가 |

의견: Story와 기능 중심의 Traceability는 충족한다. 다만 “PRD 항목 전체 누락 없음” 기준으로 보면 리스크, ADR, Scope/Constraint까지 Matrix에 연결하는 보강이 필요하다.

### 2.6 UseCase, ERD, Class Diagram, Component Diagram 등 핵심 다이어그램이 모두 작성됨

판정: 미충족

현재 SRS에서 확인된 Mermaid 다이어그램 유형은 `sequenceDiagram`뿐이다.

| 다이어그램 유형 | 현재 포함 여부 | 개선 필요 |
|---|---|---|
| UseCase Diagram | 없음 | 액터: B2B 파트너 개발자, 어르신, 보호자, 기관 관리자, API 운영팀 기준으로 추가 필요 |
| ERD | 없음 | Tenant, Device, ElderUser, ConversationTurn, EmotionAnalysis, SensorReading, EmergencyAlert, Report 관계 추가 필요 |
| Class Diagram | 없음 | API Controller, Service, Repository, Domain Model 수준 클래스 구조 추가 필요 |
| Component Diagram | 없음 | Partner Device, API Gateway, Chat Service, Emotion Service, Sensor Service, Alert Service, Report Service, AI Engine, Data Stores 구성 추가 필요 |
| Sequence Diagram | 있음 | 7개 존재 |

의견: 이 항목은 명확한 미충족이다. SRS의 아키텍처 이해도를 높이려면 Appendix 또는 3장에 핵심 구조 다이어그램을 추가해야 한다.

### 2.7 Sequence Diagram 3~5개가 포함됨

판정: 부분 충족

현재 포함된 Sequence Diagram은 총 7개다.

| 위치 | 다이어그램 |
|---|---|
| 3.4 | Core Sequence 1: Phase A 대화 응답 및 감정 메타데이터 반환 |
| 3.4 | Core Sequence 2: Phase B 센서 결합 분석 및 응급 알림 |
| 6.3 | Detailed Sequence 1: `POST /api/v1/chat/reply` |
| 6.3 | Detailed Sequence 2: `POST /api/v1/analyze/emotion` |
| 6.3 | Detailed Sequence 3: `POST /api/v1/schedule/proactive` |
| 6.3 | Detailed Sequence 4: `POST /api/v2/sensor/ingest` and Emergency Webhook |
| 6.3 | Detailed Sequence 5: `GET /api/v2/report/kpi` |

의견: “3개 이상 포함” 기준이면 충족이다. 그러나 “3~5개”를 엄격히 수량 조건으로 해석하면 7개로 초과한다. Core Sequence 2개와 Detailed Sequence 5개가 중복 성격을 가지므로, 3~5개로 맞추려면 Core Sequence를 개요 다이어그램으로 전환하거나 상세 Sequence 중 3~5개만 유지하는 방식이 적절하다.

권장 구성:

| 권장 Sequence | 이유 |
|---|---|
| Chat Reply Flow | Phase A 핵심 기능 |
| Emotion Analysis Flow | 보호자 리포트/위험 분석 기반 |
| Proactive Message Flow | Story A AC-3 직접 연결 |
| Sensor Ingest + Emergency Alert Flow | Phase B 핵심 확장 기능 |
| Report KPI Flow | 보호자/기관 가치 전달 기능 |

### 2.8 SRS 전체가 ISO 29148 구조를 준수함

판정: 충족

| ISO/SRS 구성 요소 | 현재 SRS 반영 |
|---|---|
| Introduction | 1장에 목적, 범위, 정의, 참조 포함 |
| Stakeholders | 2장에 역할, 책임, 관심사 포함 |
| System Context and Interfaces | 3장에 외부 시스템, 클라이언트, API, 핵심 시퀀스 포함 |
| Specific Requirements | 4장에 Functional/NFR 분리 포함 |
| Traceability | 5장에 Matrix 포함 |
| Appendix | 6장에 API, Entity/Data Model, 상세 Interaction Model 포함 |

의견: 사용자 지정 SRS 구조에 맞춰 ISO 29148의 핵심 요구사항 명세 관점을 반영하고 있다. 단, ISO 29148 관점의 품질을 더 높이려면 각 요구사항의 검증 방법, 우선순위, 출처, 테스트 케이스는 현재처럼 유지하되, 데이터 모델 관계성과 다이어그램 세트를 보강하는 것이 필요하다.

## 3. 주요 발견 사항

### Finding 1. 핵심 구조 다이어그램 누락

심각도: High  
영향: 시스템 구조, 데이터 관계, 사용 시나리오를 검토자가 빠르게 이해하기 어렵다.

현재 SRS에는 Mermaid `sequenceDiagram`만 존재한다. 요청 조건에 명시된 UseCase, ERD, Class Diagram, Component Diagram은 작성되어 있지 않다.

권고:

| 추가 위치 | 추가 다이어그램 |
|---|---|
| 3.4 또는 Appendix 6.3 | UseCase Diagram |
| Appendix 6.2 | ERD |
| Appendix 6.3 | Class Diagram |
| 3.1 또는 Appendix 6.3 | Component Diagram |

### Finding 2. Sequence Diagram 수량 조건 초과

심각도: Medium  
영향: 조건을 엄격 적용하는 리뷰에서는 “3~5개 포함” 기준에서 부적합 판정을 받을 수 있다.

현재 Sequence Diagram은 7개다. 기능별 상세성은 충분하지만, 조건이 수량 범위를 요구하므로 3~5개로 정리하는 것이 안전하다.

권고: Core Sequence 2개를 개요 다이어그램 또는 Component Diagram으로 대체하고, 상세 Sequence Diagram 5개를 유지한다.

### Finding 3. 데이터 모델은 있으나 관계형 스키마 완성도가 부족함

심각도: Medium  
영향: 구현자와 QA가 데이터 무결성, 조인 관계, 소유권 경계를 명확히 판단하기 어렵다.

Appendix 6.2의 Entity 표는 좋은 출발점이지만 PK/FK, 관계, 카디널리티, enum 상세, unique/index 제약이 빠져 있다.

권고: ERD를 추가하고, Entity 표에 `Key`, `Constraints`, `Relation` 컬럼을 보강한다.

### Finding 4. Traceability Matrix가 기능 중심으로는 충분하나 PRD 전체 산출물 추적은 약함

심각도: Low  
영향: Story/F/KPI 중심 검토에서는 통과 가능하지만, 리스크·ADR·Scope까지 포함한 전체 PRD 추적성 검토에서는 누락 지적이 가능하다.

권고: R2, ADR-002, CON-001~CON-005, ASM-001~ASM-004, SCP-IN/OUT 항목을 Matrix에 추가한다.

## 4. 개선 우선순위

| 우선순위 | 작업 | 기대 효과 |
|---|---|---|
| P0 | UseCase, ERD, Class Diagram, Component Diagram 추가 | 요청 조건의 명확한 미충족 해소 |
| P1 | Sequence Diagram을 3~5개로 재정리 | 수량 조건 충족 및 중복 감소 |
| P1 | Entity/Data Model에 PK/FK/관계/enum/제약 추가 | 스키마 완성도 향상 |
| P2 | Traceability Matrix에 R2, ADR-002, Scope/Constraint/Assumption 추가 | PRD 전체 추적성 강화 |

## 5. 최종 판정

현재 `SRS_v0.1.md`는 요구사항 명세 문서로서 기본 구조와 핵심 요구사항 반영은 양호하다. 특히 Story/AC, F1~F6, KPI/NFR, API, 기능 요구사항 분해, 테스트 케이스 연결은 실무적으로 의미 있게 작성되어 있다.

다만 사용자가 제시한 검토 조건을 기준으로 하면, 다이어그램 세트 누락이 가장 큰 결함이다. UseCase Diagram, ERD, Class Diagram, Component Diagram을 추가하고 Sequence Diagram 수량을 3~5개로 조정하면 전체 적합 판정에 가까워진다.
