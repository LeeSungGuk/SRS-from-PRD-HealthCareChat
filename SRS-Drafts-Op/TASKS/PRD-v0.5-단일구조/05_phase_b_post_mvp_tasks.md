# PRD v0.5 단일구조 - Phase B / Post-MVP Backlog 태스크

- Source: `실버케어초안_PRD_v0.5_단일구조.md`
- 추출 원칙: Phase B는 PRD에 명시되어 있으나 단일구조 MVP 1차 구현 컷에서는 제외한다. 구현 태스크가 아니라 추적 가능한 Post-MVP 백로그로 분리한다.

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|---|---|---|---|---|---|
| PRD-PMB-001 | Phase B / Sensor Contract | `/api/v2/sensor/ingest` 최소 표준 schema, sensor_type, observed_at, payload quality rule 정의 | PRD 4 F4, PRD 8-2, PRD 9 P1 질문 | PRD-DB-013 | M |
| PRD-PMB-002 | Phase B / Sensor Data | sensor_event 시계열 저장, tenant/device/elder 연결, cross-tenant 금지 구현 백로그 | PRD 4 F4, PRD 1-4 Post-MVP | PRD-PMB-001, PRD-SEC-002 | H |
| PRD-PMB-003 | Phase B / Emergency Alert | `/api/v2/alert/emergency` request/response, alert_status, webhook_delivery_id 계약 정의 | PRD 4 F5, PRD 8-2 | PRD-DB-009, PRD-PMB-001 | H |
| PRD-PMB-004 | Phase B / Webhook | Partner-configured webhook signature, retry, delivery status, 책임 경계 구현 백로그 | PRD 4 F5, R5, PRD 8-2 | PRD-PMB-003 | H |
| PRD-PMB-005 | Phase B / KPI Report | `/api/v2/report/kpi` 주간 정서 리포트, 수면 분석 결과, 기관 성과 JSON 계약 정의 | PRD 4 F6, PRD 8-2 | PRD-PMB-002, PRD-QRY-011 | H |
| PRD-PMB-006 | Phase B / Premium Billing | Phase B premium tier, sensor integration, advanced risk analysis 과금 지표 정의 | PRD 1-5 Premium / Phase B | PRD-PMB-002, PRD-PMB-005, PRD-QRY-012 | M |
| PRD-PMB-007 | Governance / Future Apps | 보호자용/기관용/어르신용 완성 앱을 실버케어가 직접 제공할 경우 PRD, SRS, UX/UI Spec, OpenAPI, Privacy/Data Policy, Test Plan 변경 태스크 생성 | PRD 1-4 Out of Scope, ADR-006, PRD 10 | None | M |
| PRD-PMB-008 | Governance / Phase B ADR | 센서 데이터 품질 기준, 응급 알림 책임 경계, 의료/낙상 판정 금지 기준을 Phase B ADR로 확정 | PRD 8-2, R3, R5, ADR-003 | PRD-PMB-001, PRD-PMB-003 | M |

