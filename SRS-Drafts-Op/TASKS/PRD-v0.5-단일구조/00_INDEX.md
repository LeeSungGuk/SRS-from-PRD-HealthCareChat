# PRD v0.5 단일구조 기반 개발 태스크 명세서 Index

- Source: `실버케어초안_PRD_v0.5_단일구조.md`
- 작성일: 2026-04-28
- 목적: PRD의 기능, 데이터, API, UI, NFR, 비용/운영 제약을 실행 가능한 Epic/Feature 단위 태스크로 분해한다.
- 주의: 요청 포맷의 `관련 SRS 섹션` 컬럼명은 유지하되, 각 값은 본 산출물의 입력 원천인 PRD 섹션을 참조한다.

## 파일 구성

| 파일 | 범위 | 우선 사용 시점 |
|---|---|---|
| `01_contract_data_tasks.md` | API/Data/Schema/Mock/SSOT | 구현 착수 전 최우선 |
| `02_backend_command_query_tasks.md` | Route Handler, Server Action, Command, Query, domain logic | Contract/Data 확정 직후 |
| `03_ui_frontend_tasks.md` | UX 설계, Partner Console, Playground, Report/Ops/Consent UI | Mock/API contract 준비 후 |
| `04_test_nfr_infra_tasks.md` | AC 기반 테스트, 성능/보안/운영/배포/비용 제약 | 기능 구현과 병렬 |
| `05_phase_b_post_mvp_tasks.md` | Sensor, Emergency Alert, KPI Report, 보호자/기관 앱 범위 변경 | MVP 이후 또는 별도 Phase |

## 핵심 의존 순서

1. `PRD-CT-*`, `PRD-DB-*`, `PRD-MOCK-*`를 먼저 완료한다.
2. `PRD-PLAT-*`, `PRD-SEC-*`로 공통 실행 기반을 만든다.
3. `PRD-CMD-*`와 `PRD-QRY-*`를 CQRS 기준으로 분리 구현한다.
4. `PRD-UX-*`, `PRD-FE-*`는 Mock/API contract를 기준으로 병렬 구현한다.
5. `PRD-TEST-*`, `PRD-NFR-*`, `PRD-INFRA-*`는 AC와 NFR을 자동화된 검증 루프로 전환한다.
6. `PRD-PMB-*`는 Phase B/Post-MVP 추적성만 유지하며 Phase A MVP를 막지 않는다.

## 커버리지 요약

| PRD 영역 | 반영 파일 | 반영 방식 |
|---|---|---|
| 1-4 Phase A MVP Scope | 01, 02, 03, 04 | P0/P1/P2 구현 태스크와 Out-of-Scope guard로 분리 |
| 1-5 과금/비용 가드레일 | 01, 02, 04 | usage event, quota, token budget, budget alert 태스크로 분리 |
| 1-6 단일구조 기술 스택 | 01, 04 | Next.js, Prisma, Supabase, Vercel, Gemini adapter 경계 태스크 |
| 3 Story/AC | 02, 03, 04 | Command/Query/UI 태스크와 테스트 태스크로 변환 |
| 4 기능 요구사항 F0~F8 | 01, 02, 03 | API/Console/Playground/Report/Ops 기능 단위로 분해 |
| 5 NFR/Privacy/AI Safety | 04 | 성능, 보안, 관측성, retention, evaluation gate 태스크 |
| 6 Architecture | 01, 02 | API 연동 sequence와 module boundary 태스크 |
| 7 ADR/Risk | 01, 04 | 책임 경계, 안전 정책, 위탁, 분리 가능성 태스크 |
| 8 Rollout | 04 | alpha simulation, PoC measurement, release gate 태스크 |
| 10 Future App Scope | 05 | 보호자/기관/어르신 앱은 MVP 구현 제외, 범위 변경 governance로 관리 |

