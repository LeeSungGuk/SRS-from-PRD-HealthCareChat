# SRS v02 기반 개발 태스크 목록 명세서

문서 ID: TASKS-SRS-001  
대상 SRS: `SRS_v02._gpt.md`  
작성일: 2026-04-21  
작성 관점: Technical Project Management, System Architecture, AI Agent 기반 개발 오케스트레이션

---

## 1. 태스크 추출 원칙

본 태스크 목록은 `SRS_v02._gpt.md`에 명시된 범위만 사용한다. 임의 기능은 추가하지 않는다.

추출 순서는 다음 기준을 따른다.

1. 데이터 모델과 API 통신 계약을 먼저 고정한다.
2. 기능 구현은 상태 변경 여부에 따라 Query와 Command를 분리한다.
3. Acceptance Criteria와 Test Case ID는 별도 자동화 테스트 태스크로 변환한다.
4. UI/UX 설계, 프론트엔드 구현, 백엔드 구현, 인프라/운영 태스크를 분리한다.
5. Phase A는 MVP 우선 구현 범위이며, Phase B는 SRS에 포함된 확장 범위로 태스크화하되 의존성을 명확히 둔다.

---

## 2. Epic 및 Feature 단위 태스크 목록

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|---|---|---|---|---|---|
| DATA-001 | Data Contract | Enum 및 스키마 제약 사전 정의 | 6.2.2 Enum and Schema Constraint Dictionary | None | M |
| DATA-002 | Data Contract | Tenant, ApiCredential, Device, ElderUser 테이블 스키마 및 마이그레이션 정의 | 6.2 Entity & Data Model, 6.2.1 | DATA-001 | H |
| DATA-003 | Data Contract | ConversationTurn, MemoryFact, EmotionAnalysis 테이블 스키마 및 마이그레이션 정의 | 6.2 Entity & Data Model, REQ-FUNC-001~012 | DATA-001, DATA-002 | H |
| DATA-004 | Data Contract | ProactiveScheduleRequest 테이블 스키마 및 마이그레이션 정의 | 6.2 Entity & Data Model, REQ-FUNC-013~015 | DATA-001, DATA-002, DATA-003 | M |
| DATA-005 | Data Contract | SensorReading 테이블 스키마 및 마이그레이션 정의 (Phase B) | 6.2 Entity & Data Model, REQ-FUNC-016~018 | DATA-001, DATA-002 | M |
| DATA-006 | Data Contract | EmergencyAlert, Report, AuditLog 테이블 스키마 및 마이그레이션 정의 | 6.2 Entity & Data Model, REQ-FUNC-019~026, REQ-NF-015 | DATA-001, DATA-002, DATA-003, DATA-005 | H |
| DATA-007 | Data Contract | ERD 기반 PK/FK/Cardinality/Constraint 검증 스크립트 작성 | 6.2.1, 6.2.3 ERD | DATA-002, DATA-003, DATA-004, DATA-005, DATA-006 | M |
| DATA-008 | Data Contract | 테넌트 격리 조회 조건 적용 기준 정의 | CON-005, REQ-FUNC-028, REQ-NF-013, 6.2.2 | DATA-002 | M |
| CONTRACT-001 | API Contract | 공통 API 인증 입력, 테넌트 식별자, JSON 응답 구조 규약 정의 | 3.3 API Overview, 6.1 API Endpoint List, REQ-FUNC-027 | DATA-002, DATA-008 | M |
| CONTRACT-002 | API Contract | `POST /api/v1/chat/reply` Request/Response DTO 정의 | API-001, REQ-FUNC-001~006, 6.1 | CONTRACT-001, DATA-003 | M |
| CONTRACT-003 | API Contract | `POST /api/v1/analyze/emotion` Request/Response DTO 정의 | API-002, REQ-FUNC-009~012, 6.1 | CONTRACT-001, DATA-003 | M |
| CONTRACT-004 | API Contract | `POST /api/v1/schedule/proactive` Request/Response DTO 정의 | API-003, REQ-FUNC-013~015, 6.1 | CONTRACT-001, DATA-004 | M |
| CONTRACT-005 | API Contract | `POST /api/v2/sensor/ingest` Request/Response DTO 정의 (Phase B) | API-004, REQ-FUNC-016~018, 6.1 | CONTRACT-001, DATA-005 | M |
| CONTRACT-006 | API Contract | `/api/v2/alert/emergency` Webhook Payload 및 수신 확인 계약 정의 (Phase B) | API-005, REQ-FUNC-019~022, 6.1 | CONTRACT-001, DATA-006 | M |
| CONTRACT-007 | API Contract | `GET /api/v2/report/kpi` Query/Response DTO 정의 (Phase B) | API-006, REQ-FUNC-023~026, 6.1 | CONTRACT-001, DATA-003, DATA-005, DATA-006 | M |
| CONTRACT-008 | API Contract | OpenAPI/Swagger 개발자 문서 계약 정의 | API-007, REQ-FUNC-007~008, REQ-NF-020 | CONTRACT-002, CONTRACT-003, CONTRACT-004, CONTRACT-005, CONTRACT-006, CONTRACT-007 | M |
| CONTRACT-009 | External Interface | 파트너 STT/TTS 책임 경계 및 텍스트 전용 입력 계약 정의 | EXT-002, CON-001, CON-002, REQ-FUNC-006 | CONTRACT-002 | L |
| CONTRACT-010 | External Interface | 보호자 앱/기관 모니터링 시스템 연동 계약 범위 정의 (Phase B) | EXT-004, EXT-005, CLI-003, CLI-004, ASM-003 | CONTRACT-006, CONTRACT-007 | M |
| MOCK-001 | Mock Data | Tenant, Credential, Device, ElderUser 기본 Mock 데이터 세트 작성 | 6.2 Entity & Data Model, ASM-001 | DATA-002 | L |
| MOCK-002 | Mock Data | 대화, 기억, 감정 분석 Phase A Mock 데이터 세트 작성 | API-001, API-002, REQ-FUNC-001~012 | DATA-003, MOCK-001 | M |
| MOCK-003 | Mock Data | 선제 발화 요청 Mock 데이터 세트 작성 | API-003, REQ-FUNC-013~015 | DATA-004, MOCK-001, MOCK-002 | L |
| MOCK-004 | Mock Data | 센서, 응급 알림, 리포트 Phase B Mock 데이터 세트 작성 | API-004~006, REQ-FUNC-016~026 | DATA-005, DATA-006, MOCK-001, MOCK-002 | M |
| MOCK-005 | Mock API | Phase A API 프론트엔드/파트너 연동용 Mock 응답 서버 구성 | API-001~003, REQ-FUNC-001~015 | CONTRACT-002, CONTRACT-003, CONTRACT-004, MOCK-002, MOCK-003 | M |
| MOCK-006 | Mock API | Phase B API 및 Webhook 연동용 Mock 응답 서버 구성 | API-004~006, REQ-FUNC-016~026 | CONTRACT-005, CONTRACT-006, CONTRACT-007, MOCK-004 | M |
| ARCH-001 | Backend Architecture | API Gateway, Service, Store, Monitoring 경계 구현 골격 정의 | 3.4 Component Diagram, 6.3 Class Diagram | CONTRACT-001, DATA-007 | H |
| ARCH-002 | Backend Architecture | AI 대화/분석 엔진 연동 인터페이스 정의 | EXT-003, 3.4 Component Diagram, 6.3 Class Diagram | CONTRACT-002, CONTRACT-003, CONTRACT-004 | M |
| ARCH-003 | Backend Architecture | PII Masking Pipeline 인터페이스 정의 | REQ-NF-012, 6.3 Class Diagram | DATA-003, CONTRACT-002, CONTRACT-003 | M |
| SEC-001 | Security / Backend | 테넌트 자격 증명 인증 Guard 구현 | REQ-FUNC-027, CON-005, API-001~007 | CONTRACT-001, DATA-002, ARCH-001 | M |
| SEC-002 | Security / Backend | 테넌트 경계 기반 데이터 접근 제어 Guard 구현 | REQ-FUNC-028, REQ-NF-013, DATA-008 | SEC-001, DATA-008 | H |
| SEC-003 | Security / Backend | 개발자 포털 및 운영 인터페이스 RBAC 적용 | REQ-NF-014, API-007 | SEC-001, DATA-002, CONTRACT-008 | M |
| SEC-004 | Security / Backend | 인증 실패 및 권한 거부 감사 로그 Command 구현 | REQ-NF-015, REQ-FUNC-027~028 | SEC-001, SEC-002, DATA-006 | M |
| SEC-005 | Privacy / Backend | 대화 텍스트 PII 탐지 및 마스킹 Command 구현 | REQ-NF-012, 6.3 PiiMaskingPipeline | ARCH-003 | H |
| SEC-006 | Privacy / Backend | 마스킹된 텍스트만 대화/기억/분석 저장소에 전달하는 파이프라인 연결 | REQ-NF-012, REQ-FUNC-001~012 | SEC-005, DATA-003 | M |
| CHAT-001 | Chat Reply / Query | Chat 요청의 tenantId, deviceId, elderUserId, utteranceText 검증 | API-001, REQ-FUNC-001, ASM-001 | CONTRACT-002, SEC-001, SEC-002 | M |
| CHAT-002 | Chat Reply / Query | elderUserId 기준 관련 MemoryFact 조회 로직 구현 | REQ-FUNC-004, 6.3 Detailed Sequence 1 | DATA-003, SEC-002, SEC-006 | M |
| CHAT-003 | Chat Reply / Command | 시니어 맞춤형 응답 텍스트 생성 로직 구현 | REQ-FUNC-002, EXT-003, 6.3 ChatReplyService | ARCH-002, CHAT-001, CHAT-002 | H |
| CHAT-004 | Chat Reply / Command | 의료 진단/처방/치료 지시 차단 가드레일 적용 | REQ-FUNC-005, REQ-NF-016, CON-004, R1 | CHAT-003 | H |
| CHAT-005 | Chat Reply / Command | 마스킹된 대화 턴, 응답, 감정 메타데이터 저장 | REQ-FUNC-003, REQ-FUNC-004, DATA-003 | CHAT-003, CHAT-004, SEC-006 | H |
| CHAT-006 | Chat Reply / API | `POST /api/v1/chat/reply` 엔드포인트 오케스트레이션 구현 | API-001, REQ-FUNC-001~006 | CHAT-001, CHAT-002, CHAT-003, CHAT-004, CHAT-005 | H |
| EMO-001 | Emotion Analysis / Query | 감정 분석 요청의 tenantId, elderUserId, conversationText 검증 | API-002, REQ-FUNC-009 | CONTRACT-003, SEC-001, SEC-002 | M |
| EMO-002 | Emotion Analysis / Command | 외로움, 우울 위험, 통증 호소 등 감정 점수 산출 로직 구현 | REQ-FUNC-010, REQ-NF-007, EXT-003 | ARCH-002, EMO-001, SEC-006 | H |
| EMO-003 | Emotion Analysis / Command | 위험 키워드 추출 로직 구현 | REQ-FUNC-011 | EMO-002 | M |
| EMO-004 | Emotion Analysis / Command | EmotionAnalysis 결과 저장 Command 구현 | REQ-FUNC-012, DATA-003 | EMO-002, EMO-003, SEC-006 | M |
| EMO-005 | Emotion Analysis / Query | 리포트 및 응급 분석을 위한 감정 분석 결과 조회 로직 구현 | REQ-FUNC-012, REQ-FUNC-019, REQ-FUNC-024 | EMO-004, SEC-002 | M |
| EMO-006 | Emotion Analysis / API | `POST /api/v1/analyze/emotion` 엔드포인트 오케스트레이션 구현 | API-002, REQ-FUNC-009~012 | EMO-001, EMO-002, EMO-003, EMO-004 | H |
| PRO-001 | Proactive Message / Query | 선제 발화 요청의 tenantId, deviceId, elderUserId, triggerType 검증 | API-003, REQ-FUNC-013~014 | CONTRACT-004, SEC-001, SEC-002 | M |
| PRO-002 | Proactive Message / Query | 선제 발화 생성을 위한 관련 최근 기억 조회 | REQ-FUNC-015, 6.3 Detailed Sequence 2 | DATA-003, PRO-001, SEC-002 | M |
| PRO-003 | Proactive Message / Command | 기상, 식사, 복약 컨텍스트 기반 선제 메시지 생성 | REQ-FUNC-013~014, EXT-003 | ARCH-002, PRO-001, PRO-002 | H |
| PRO-004 | Proactive Message / Command | 생성된 선제 발화 턴 및 요청 이력 저장 | REQ-FUNC-015, DATA-004 | PRO-003, SEC-006 | M |
| PRO-005 | Proactive Message / API | `POST /api/v1/schedule/proactive` 엔드포인트 오케스트레이션 구현 | API-003, REQ-FUNC-013~015 | PRO-001, PRO-002, PRO-003, PRO-004 | H |
| SENSOR-001 | Sensor Ingest / Query | 센서 수집 요청의 tenantId, deviceId, elderUserId, timestamp 검증 (Phase B) | API-004, REQ-FUNC-016 | CONTRACT-005, SEC-001, SEC-002 | M |
| SENSOR-002 | Sensor Ingest / Query | 지원 센서 유형 검증 로직 구현 (Radar, Sleep, Movement, Wearable, BloodPressure 등) | REQ-FUNC-017, 6.2.2 `sensorType` | DATA-001, SENSOR-001 | M |
| SENSOR-003 | Sensor Ingest / Command | 센서 측정값 저장 Command 구현 (Phase B) | REQ-FUNC-018, DATA-005 | SENSOR-001, SENSOR-002, DATA-005 | M |
| SENSOR-004 | Sensor Ingest / Query | 어르신/기간 기준 센서 측정값 조회 로직 구현 (Phase B) | REQ-FUNC-019, REQ-FUNC-025, REQ-NF-022 | SENSOR-003, SEC-002 | M |
| SENSOR-005 | Sensor Ingest / API | `POST /api/v2/sensor/ingest` 엔드포인트 오케스트레이션 구현 (Phase B) | API-004, REQ-FUNC-016~018 | SENSOR-001, SENSOR-002, SENSOR-003 | H |
| ALERT-001 | Emergency Alert / Query | 최근 대화 위험 신호와 센서 이상 신호 결합 입력 조회 (Phase B) | REQ-FUNC-019, 6.3 Detailed Sequence 3 | EMO-005, SENSOR-004 | H |
| ALERT-002 | Emergency Alert / Command | 응급 위험 판단 결과 생성 로직 구현 (Phase B) | REQ-FUNC-019 | ALERT-001 | H |
| ALERT-003 | Emergency Alert / Command | EmergencyAlert 생성 및 sourceSignals 저장 (Phase B) | REQ-FUNC-021, DATA-006 | ALERT-002 | M |
| ALERT-004 | Emergency Alert / Command | 등록된 보호자/기관 엔드포인트로 Webhook 전송 (Phase B) | API-005, REQ-FUNC-020 | CONTRACT-006, ALERT-003 | H |
| ALERT-005 | Emergency Alert / Command | Webhook 전송 상태 기록 및 감사 로그 연결 (Phase B) | REQ-FUNC-022, REQ-NF-023, DATA-006 | ALERT-004, SEC-004 | M |
| REPORT-001 | Report KPI / Query | 리포트 요청의 tenantId, scope, 기간, RBAC 검증 (Phase B) | API-006, REQ-FUNC-023, REQ-NF-014 | CONTRACT-007, SEC-001, SEC-002, SEC-003 | M |
| REPORT-002 | Report KPI / Query | 주간 정서 요약 데이터 조회 및 집계 (Phase B) | REQ-FUNC-024, DATA-003 | REPORT-001, EMO-005 | M |
| REPORT-003 | Report KPI / Query | 수면 센서 데이터 기반 수면 분석 조회 및 집계 (Phase B) | REQ-FUNC-025, SENSOR-004 | REPORT-001, SENSOR-004 | M |
| REPORT-004 | Report KPI / Query | 기관 범위 KPI 집계 로직 구현 (Phase B) | REQ-FUNC-026, REQ-NF-008~009 | REPORT-001, DATA-006 | H |
| REPORT-005 | Report KPI / API | `GET /api/v2/report/kpi` 엔드포인트 오케스트레이션 구현 (Phase B) | API-006, REQ-FUNC-023~026 | REPORT-001, REPORT-002, REPORT-003, REPORT-004 | H |
| PORTAL-UX-001 | UI/UX Design | 개발자 포털 OpenAPI/샘플 코드 정보구조 설계 | CLI-002, API-007, REQ-FUNC-007~008 | CONTRACT-008 | L |
| PORTAL-FE-001 | Frontend | 개발자 포털에서 OpenAPI/Swagger 명세 표시 | API-007, REQ-FUNC-007, REQ-NF-020 | PORTAL-UX-001, CONTRACT-008, SEC-003 | M |
| PORTAL-FE-002 | Frontend | 개발자 포털 샘플 연동 코드 표시 | REQ-FUNC-008 | PORTAL-UX-001, CONTRACT-008 | M |
| PHASEB-UX-001 | UI/UX Design | 보호자 앱/기관 대시보드 리포트·알림 화면 범위 확인 (Phase B) | CLI-003, CLI-004, ASM-003, API-005~006 | CONTRACT-010 | L |
| OPS-001 | Operations / Query | 어르신별 일평균 대화 API 호출 횟수 집계 | REQ-NF-008, SCP-IN-009 | CHAT-006, DATA-003 | M |
| OPS-002 | Operations / Command | B2B PoC 파트너 연동 기록 유지 | REQ-NF-009, R2, SCP-IN-008 | DATA-002, CONTRACT-008 | M |
| OPS-003 | Operations / Query | 테넌트 및 API 엔드포인트별 성공 요청당 단위 처리 비용 산출 | REQ-NF-017 | CHAT-006, EMO-006, PRO-005, SENSOR-005, REPORT-005 | H |
| OPS-004 | Operations / Query | 요청 수, p95 latency, 5xx 오류율, 활성 기기 수 지표 발행 | REQ-NF-018 | ARCH-001, CHAT-006, EMO-006, PRO-005 | H |
| OPS-005 | Operations / Command | p95/SLA/5xx 기준 위반 시 운영 알림 발생 | REQ-NF-019 | OPS-004, NFR-003 | M |
| OPS-006 | AI Quality / Query | 월간 감정/위험 감지 정확도 평가 데이터셋 실행 | REQ-NF-007, VAL-003 | EMO-006, TEST-EMO-001 | H |
| INFRA-001 | Infrastructure | API 서버, 데이터 저장소, 감사 로그 저장소 배포 환경 구성 | 3.4 Component Diagram, REQ-NF-004~006 | ARCH-001, DATA-007 | H |
| INFRA-002 | Infrastructure | TLS 1.3 기반 HTTPS 외부 API 통신 설정 | REQ-NF-010 | INFRA-001 | M |
| INFRA-003 | Infrastructure | 저장 데이터 암호화 설정 및 검증 절차 구성 | REQ-NF-011 | INFRA-001, DATA-007 | M |
| INFRA-004 | Infrastructure | RPO 15분, RTO 30분 검증을 위한 백업/복구 절차 구성 | REQ-NF-005~006 | INFRA-001, INFRA-003 | H |
| INFRA-005 | Infrastructure | 99.9% 업타임 측정을 위한 가용성 모니터링 구성 | REQ-NF-004 | INFRA-001, OPS-004 | M |
| INFRA-006 | Infrastructure | 수평 확장 가능 API 서비스 운영 구조 검토 | REQ-NF-021 | INFRA-001, NFR-002 | H |
| TEST-DATA-001 | Test Automation | DB 마이그레이션, FK/Cardinality, Enum 제약 자동화 테스트 | 6.2, 6.2.1, 6.2.2 | DATA-007 | M |
| TEST-CONTRACT-001 | Test Automation | API-001~007 OpenAPI 계약 및 DTO 스키마 테스트 | 6.1, API-001~007, REQ-NF-020 | CONTRACT-002, CONTRACT-003, CONTRACT-004, CONTRACT-005, CONTRACT-006, CONTRACT-007, CONTRACT-008 | H |
| TEST-SEC-001 | Test Automation | 유효하지 않은 테넌트 자격 증명 거부 테스트 | REQ-FUNC-027, TC-FUNC-027 | SEC-001, SEC-004 | M |
| TEST-SEC-002 | Test Automation | 교차 테넌트 데이터 접근 거부 테스트 | REQ-FUNC-028, REQ-NF-013, TC-FUNC-028, TC-NF-013 | SEC-002 | H |
| TEST-SEC-003 | Test Automation | RBAC 권한 테스트 | REQ-NF-014, TC-NF-014 | SEC-003 | M |
| TEST-PRIV-001 | Test Automation | PII 마스킹 및 저장 전 비식별화 테스트 | REQ-NF-012, TC-NF-012 | SEC-005, SEC-006 | H |
| TEST-CHAT-001 | Test Automation | `chat/reply` 성공 응답, replyText, 감정 메타데이터 GWT 테스트 | TC-FUNC-001, TC-FUNC-002, TC-FUNC-003 | CHAT-006 | H |
| TEST-CHAT-002 | Test Automation | 음성 파일 없이 텍스트 입력만으로 대화 API 처리 테스트 | TC-FUNC-006, TC-CON-001, TC-CON-002 | CHAT-006, CONTRACT-009 | M |
| TEST-CHAT-003 | Test Automation | 과거 기억 기반 개인화 응답 테스트 | TC-FUNC-004 | CHAT-002, CHAT-006 | M |
| TEST-CHAT-004 | Test Automation | 의료 진단/처방/치료 지시 차단 가드레일 테스트 | TC-FUNC-005, TC-NF-016, TC-CON-004 | CHAT-004, CHAT-006 | H |
| TEST-EMO-001 | Test Automation | 감정 분석 객체 반환, 감정 점수, 위험 키워드, 저장 가능성 테스트 | TC-FUNC-009, TC-FUNC-010, TC-FUNC-011, TC-FUNC-012 | EMO-006 | H |
| TEST-PRO-001 | Test Automation | 선제 발화 생성, triggerType 반영, 기억 활용 테스트 | TC-FUNC-013, TC-FUNC-014, TC-FUNC-015 | PRO-005 | H |
| TEST-SENSOR-001 | Test Automation | 센서 수집 API payload 검증, 센서 유형 검증, 저장 테스트 (Phase B) | TC-FUNC-016, TC-FUNC-017, TC-FUNC-018 | SENSOR-005 | M |
| TEST-ALERT-001 | Test Automation | 응급 위험 판단, Webhook payload, 전송 상태 기록 테스트 (Phase B) | TC-FUNC-019, TC-FUNC-020, TC-FUNC-021, TC-FUNC-022, TC-NF-023 | ALERT-005 | H |
| TEST-REPORT-001 | Test Automation | 리포트 JSON, 주간 정서 요약, 수면 분석, 기관 KPI 테스트 (Phase B) | TC-FUNC-023, TC-FUNC-024, TC-FUNC-025, TC-FUNC-026 | REPORT-005 | H |
| TEST-PORTAL-001 | Test Automation | 개발자 포털 API 명세 및 샘플 코드 표시 테스트 | TC-FUNC-007, TC-FUNC-008, TC-NF-020 | PORTAL-FE-001, PORTAL-FE-002 | M |
| TEST-OPS-001 | Test Automation | 성공 요청당 단위 처리 비용 산출 테스트 | TC-NF-017 | OPS-003 | M |
| TEST-OPS-002 | Test Automation | 요청 수, p95, 5xx, 가용성, 활성 기기, 모델 정확도, 비용 지표 발행 테스트 | TC-NF-018 | OPS-004 | H |
| TEST-OPS-003 | Test Automation | p95, SLA, 5xx 기준 초과 운영 알림 테스트 | TC-NF-019 | OPS-005 | M |
| TEST-NFR-001 | Test Automation | `chat/reply` p95 800ms 및 1초 응답 성능 테스트 | REQ-NF-001, REQ-NF-002, TC-NF-001, TC-NF-002 | CHAT-006, NFR-001 | H |
| TEST-NFR-002 | Test Automation | 10,000대 동시 활성 파트너 기기 조건 5xx 오류율 테스트 | REQ-NF-003, TC-NF-003 | NFR-002, INFRA-006 | H |
| TEST-NFR-003 | Test Automation | TLS 1.3 스캔 및 저장소 암호화 감사 테스트 | REQ-NF-010, REQ-NF-011, TC-NF-010, TC-NF-011 | INFRA-002, INFRA-003 | M |
| TEST-NFR-004 | Test Automation | RTO 30분, RPO 15분 복구 훈련 테스트 | REQ-NF-005, REQ-NF-006, TC-NF-005, TC-NF-006 | INFRA-004 | H |
| TEST-VAL-001 | Validation | 100개 가상 어르신 페르소나 기반 알파 시뮬레이션 테스트 | REQ-FUNC-029, REQ-NF-024, VAL-001 | CHAT-006, EMO-006, PRO-005, TEST-CHAT-004 | H |
| TEST-VAL-002 | Validation | 중소 제조사 1곳 및 50가구 PoC 지표 검증 | VAL-002, REQ-NF-008, REQ-NF-009 | OPS-001, OPS-002 | H |
| TEST-VAL-003 | Validation | 월간 감정/위험 감지 정확도 90% 이상 검증 | VAL-003, REQ-NF-007 | OPS-006 | H |
| TEST-VAL-004 | Validation | Phase B 센서 및 응급 알림 통합 검증 | VAL-004, REQ-FUNC-016~022, REQ-NF-023 | SENSOR-005, ALERT-005, TEST-SENSOR-001, TEST-ALERT-001 | H |
| NFR-001 | Performance / Infra | `chat/reply` p95 및 End-to-End 응답시간 부하 테스트 스크립트 작성 | REQ-NF-001, REQ-NF-002 | CHAT-006 | H |
| NFR-002 | Scalability / Infra | 10,000대 동시 활성 기기 부하 테스트 시나리오 작성 | REQ-NF-003, REQ-NF-021 | CHAT-006, EMO-006, PRO-005, SENSOR-005 | H |
| NFR-003 | Availability / Infra | 월간 99.9% 업타임 측정 및 SLA 리포트 구성 | REQ-NF-004 | INFRA-005 | M |
| NFR-004 | Reliability / Infra | RTO/RPO 검증 리포트 자동화 | REQ-NF-005, REQ-NF-006 | INFRA-004 | H |
| NFR-005 | Security / Infra | TLS 1.3 및 저장 데이터 암호화 정책 검증 | REQ-NF-010, REQ-NF-011 | INFRA-002, INFRA-003 | M |
| NFR-006 | Data Quality / Ops | 센서 및 리포트 원천 데이터 계보 검증 | REQ-NF-022 | SENSOR-003, REPORT-005 | M |
| NFR-007 | Audit / Ops | 인증 실패, 데이터 접근, PII 마스킹, 리포트 접근, Webhook 전송 감사 로그 완전성 검증 | REQ-NF-015 | SEC-004, SEC-006, REPORT-005, ALERT-005 | H |

---

## 3. 오케스트레이션 기준 의존성 체인

| 체인 | 순서 |
|---|---|
| 데이터 SSOT | DATA-001 → DATA-002 → DATA-003 → DATA-004/005/006 → DATA-007 |
| API 계약 SSOT | CONTRACT-001 → CONTRACT-002/003/004/005/006/007 → CONTRACT-008 |
| Phase A 대화 MVP | DATA-003 → CONTRACT-002 → SEC-001/002 → SEC-005/006 → CHAT-001~006 → TEST-CHAT-001~004 |
| Phase A 감정 분석 | DATA-003 → CONTRACT-003 → EMO-001~006 → TEST-EMO-001 |
| Phase A 선제 발화 | DATA-004 → CONTRACT-004 → PRO-001~005 → TEST-PRO-001 |
| 개발자 포털 | CONTRACT-008 → PORTAL-UX-001 → PORTAL-FE-001/002 → TEST-PORTAL-001 |
| Phase B 센서/응급 | DATA-005/006 → CONTRACT-005/006 → SENSOR-001~005 → ALERT-001~005 → TEST-VAL-004 |
| Phase B 리포트 | DATA-003/005/006 → CONTRACT-007 → REPORT-001~005 → TEST-REPORT-001 |
| 운영/NFR | INFRA-001~006 → OPS-001~006 → NFR-001~007 → TEST-NFR-001~004 |

---

## 4. 구현 우선순위 제안

| Priority | Task 범위 | 이유 |
|---|---|---|
| P0 | DATA-001~004, CONTRACT-001~004, SEC-001~006 | 데이터와 계약, 인증/개인정보가 흔들리면 이후 AI 생성물이 분산된다. |
| P1 | CHAT-001~006, EMO-001~006, PRO-001~005, 관련 테스트 | Phase A MVP 핵심 사용자 가치다. |
| P2 | CONTRACT-008, PORTAL-UX-001, PORTAL-FE-001~002, OPS-001~004 | B2B 개발자 연동과 운영 관측성을 만든다. |
| P3 | DATA-005~006, SENSOR-001~005, ALERT-001~005, REPORT-001~005 | Phase B 확장 기능이다. |
| P4 | 10,000대 동시성, RTO/RPO, 고급 SLA 검증 | 운영 확장 단계의 검증 과제다. |

---

## 5. 산출물 사용 지침

AI 에이전트에게 작업을 위임할 때는 하나의 Task ID 또는 같은 Epic 내 폐쇄된 작은 묶음만 전달한다. 특히 Command 태스크와 Query 태스크를 함께 넓게 주지 않는다.

예시:

```text
CHAT-002만 구현하라. DATA-003의 MemoryFact 스키마와 SEC-002의 tenant boundary를 참조하고, DB 상태를 변경하지 말라.
```

```text
TEST-CHAT-003을 먼저 작성하라. 테스트가 실패하는 상태를 확인한 뒤 CHAT-002/CHAT-006 구현을 보완하라.
```
