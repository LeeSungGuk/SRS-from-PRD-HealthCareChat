# MVP 개발목표 적절성 종합 검토 보고서

문서명: MVP-개발목표-적절성-종합-검토(난이도-가능성-효율성)-보고서  
검토일: 2026-04-28  
검토 대상: `SRS/실버케어_SRS_v0.5_단일구조.md`  
검토 관점: 개발 난이도, 구현 가능성, 개발 속도, 외부 연동 적합성, 기술 스택 리스크, 운영 비용 효율성  
기준 기술 스택: Next.js App Router, Server Actions, Route Handlers, Prisma, local SQLite, Supabase PostgreSQL, Tailwind CSS, shadcn/ui, Vercel AI SDK, Google Gemini API, Vercel

> 파일명 처리 주의: 요청 파일명에 포함된 `/` 문자는 macOS/Unix 파일명에 사용할 수 없으므로 `난이도-가능성-효율성` 형태로 치환하였다.

---

## 1. Executive Summary

`실버케어_SRS_v0.5_단일구조.md`는 MVP의 기술 방향을 Next.js 단일 풀스택 구조로 정렬한 점에서 개발 속도와 초기 운영 비용 측면에 적합하다. B2B API, Partner Console, Web API Playground, PoC Report, 운영 로그, 동의 관리까지 한 프로젝트 안에서 구현할 수 있으므로 완전 분리형 프론트엔드/백엔드 구조보다 MVP 착수 난이도가 낮다.

다만 현재 문서는 ISO 29148 SRS로서 완성도가 높은 대신, 바이브코딩 기반 MVP 실행 기준으로는 일부 요구가 무겁다. 특히 10,000대 동시성, 99.9% SLA, LLM 포함 p95 800ms, 위험 탐지 recall 90% 이상, 완전한 삭제 전파, 운영 등급 RBAC는 MVP 첫 구현 완료 조건으로 두면 개발 속도와 비용 효율성을 떨어뜨린다.

종합 판단은 다음과 같다.

| 평가 항목 | 현재 단일구조 SRS | 조정 후 MVP 기준 | 판단 |
|---|---:|---:|---|
| 개발 속도 | 7/10 | 8.5/10 | 단일구조 선택은 적절하나 Phase B와 production-grade NFR 분리가 필요 |
| 구현 가능성 | 7/10 | 8/10 | 중급 개발자 + AI 보조 구현 가능. 단, 작업 단위 분해 필요 |
| 외부 연동 적합성 | 8/10 | 8.5/10 | REST JSON API, API Key, Playground 방향은 적절 |
| 기술 스택 단순성 | 8/10 | 8.5/10 | Vercel/Supabase/Gemini 조합은 MVP에 적합 |
| 운영 비용 효율성 | 7.5/10 | 8.5/10 | 작은 PoC는 월 수십 달러 수준 가능. LLM 호출량 통제가 핵심 |
| 완전 바이브코딩 적합성 | 6/10 | 7.5/10 | SRS 그대로보다 별도 TASKS 문서 기반 실행이 적절 |

결론적으로 추가 조정은 필요하다. 조정 방향은 기능 삭제가 아니라 MVP 완료 조건과 후속 검증 조건을 분리하는 것이다.

| 조정 필요 항목 | 현재 상태 | 권장 조정 |
|---|---|---|
| Phase B 기능 | SRS 본문과 Appendix에 유지됨 | 구현 대상이 아니라 추적성 보존 항목으로 명확히 표시 |
| p95 800ms | `/api/v1/chat/reply` 전체 응답 기준으로 읽힐 수 있음 | 비LLM 처리, LLM 첫 토큰, LLM 전체 완료 시간을 분리 |
| 10,000대 동시성 | MVP 구현 수용 기준처럼 보임 | Post-MVP 부하 목표로 이동하고 MVP는 50~200명 PoC 기준 |
| AI 평가 지표 | recall 90%, critical recall 95%, 오탐률 10% | 초기 MVP는 평가셋 구조와 리뷰 큐 구현을 완료 조건으로 둠 |
| 개인정보 삭제 전파 | 모든 캐시/인덱스/백업까지 전파 요구 | MVP는 primary DB와 audit 가능한 deletion job부터 구현 |
| 운영/모니터링 | 운영 등급 경보와 SLA 기준 포함 | Vercel/Supabase 기본 로그 + DB audit log 중심으로 시작 |
| 비용 통제 | usage event는 있으나 모델/토큰 예산 구체성이 부족 | Gemini Flash-Lite 기본, 토큰 상한, tenant quota, spend alert 추가 |

---

## 2. 검토 전제

### 2.1 현재 SRS의 기술 제약

`SRS/실버케어_SRS_v0.5_단일구조.md`는 다음 결정을 포함한다.

| ID | 내용 |
|---|---|
| C-TEC-001 | 모든 서비스는 Next.js App Router 기반 단일 풀스택 프레임워크로 구현 |
| C-TEC-002 | 서버 측 로직은 Server Actions 또는 Route Handlers로 구현 |
| C-TEC-003 | 로컬은 Prisma + SQLite, 배포는 Supabase PostgreSQL 사용 |
| C-TEC-004 | UI는 Tailwind CSS와 shadcn/ui 사용 |
| C-TEC-005 | LLM 오케스트레이션은 Vercel AI SDK로 Next.js 내부 구현 |
| C-TEC-006 | 기본 LLM Provider는 Google Gemini API |
| C-TEC-007 | 배포는 Vercel Git Integration 기반 자동 배포 |
| C-TEC-008~013 | 추후 분리를 위해 모듈 경계, 데이터 접근 경계, API 계약, server-only 경계 유지 |

이 방향은 MVP 개발 속도와 초기 비용 효율성 측면에서 타당하다. 특히 프론트엔드, API, 내부 운영 콘솔, Playground를 한 코드베이스에 둘 수 있어 바이브코딩으로 작업을 나누기 쉽다.

### 2.2 현재 가격 정보 기준

아래 비용 검토는 2026-04-28 기준 공식 공개 가격을 참고한 의사결정용 추정이다. 실제 비용은 리전, 환율, 세금, 무료 티어 적용, 사용량, 로그 보존, 데이터 전송량, 모델 버전, 약정 여부에 따라 달라질 수 있다.

| 항목 | 확인한 가격 기준 | 출처 |
|---|---|---|
| Vercel Pro | 월 $20 + 추가 사용량, $20 usage credit 포함 | Vercel Pricing, Vercel Pro Plan |
| Vercel Edge Requests | Pro 기준 월 10M included, 이후 $2 / 1M부터 | Vercel Pricing |
| Vercel Fast Data Transfer | Pro 기준 월 1TB included, 이후 $0.15 / GB부터 | Vercel Pricing |
| Vercel Functions | invocations, active CPU, memory 기준 과금 구조 | Vercel Pricing |
| Supabase Pro | Pro Plan $25 예시, Micro compute와 compute credit 적용 시 단일 프로젝트 총 $25 예시 | Supabase Billing, Supabase Compute |
| Supabase DB | Pro/Team 기준 8GB disk included, 초과 $0.125 / GB | Supabase Billing |
| Supabase Egress | Pro/Team 기준 250GB included, 초과 $0.09 / GB | Supabase Billing |
| Gemini 2.5 Flash-Lite | 입력 $0.10 / 1M tokens, 출력 $0.40 / 1M tokens | Google Gemini API Pricing |
| Gemini 2.5 Flash | 입력 $0.30 / 1M tokens, 출력 $2.50 / 1M tokens | Google Gemini API Pricing |

---

## 3. 개발 속도 관점 검토

### 3.1 현재 방향의 장점

| 항목 | 개발 속도에 유리한 이유 |
|---|---|
| Next.js 단일 앱 | Web UI, API Route, Server Action, 인증, 배포를 한 프로젝트에서 처리 가능 |
| Route Handlers | B2B API를 별도 백엔드 없이 구현 가능 |
| Server Actions | Partner Console 내부 mutation을 빠르게 구현 가능 |
| Prisma | schema, migration, type-safe query를 빠르게 구성 가능 |
| local SQLite | 로컬 개발환경 준비 시간이 짧음 |
| Supabase PostgreSQL | DB 운영, 백업, 인증 확장 가능성을 managed service로 확보 |
| shadcn/ui | Partner Console, API Playground, 표/폼/탭/모달 구현 속도 향상 |
| Vercel 배포 | Git Push 기반 preview/production 배포로 인프라 설정 시간 감소 |
| Vercel AI SDK | Gemini 호출, streaming, structured generation 패턴을 TypeScript 안에서 처리 가능 |

개발 속도 관점에서 현재 스택은 MVP에 적절하다. FastAPI/MySQL 분리 구조보다 초기 구현 속도는 빠르고, Cursor/Codex/v0 같은 AI 도구와도 궁합이 좋다.

### 3.2 개발 속도를 저해할 수 있는 요소

| 저해 요소 | 문제 | 조정 권고 |
|---|---|---|
| 요구사항 60개 + NFR 41개 | AI에게 한 번에 맡기면 범위가 커져 실패 가능성 증가 | `MVP Cut 1` 작업 목록을 별도로 작성 |
| Phase B 항목 유지 | 센서, Webhook, KPI 리포트가 구현 압력으로 읽힐 수 있음 | `Phase B - Not Implemented in MVP` 라벨 부여 |
| 운영 등급 NFR | 99.9% SLA, 10,000대 동시성, recall 90%가 과도함 | MVP baseline과 production target 분리 |
| 동의/삭제 전파 범위 | 캐시, 검색 인덱스, 백업까지 모두 처리하려면 시간이 큼 | primary DB deletion job + audit trail부터 구현 |
| OpenAPI 계약 | Next.js는 FastAPI처럼 자동 OpenAPI가 기본 제공되지 않음 | Zod schema를 source of truth로 두고 OpenAPI 생성 도구 도입 |
| RBAC 범위 | B2B-DEV, B2B-PM, 운영자, 권한 있는 B2B 운영 담당자 등 역할이 많음 | MVP는 `developer`, `pm`, `operator` 3개 role로 제한 |

### 3.3 개발 속도 기준 권장 MVP 컷

MVP 1차 구현은 아래 범위로 자르는 것이 적절하다.

| 우선순위 | 구현 항목 | 포함 요구사항 |
|---|---|---|
| P0 | Next.js 프로젝트, Tailwind, shadcn/ui, Prisma, SQLite, Supabase 연결 | C-TEC-001~004 |
| P0 | tenant, user, api_key, audit_log, usage_event 기본 모델 | REQ-FUNC-001, 004~006, 014, 026 |
| P0 | API Key 발급/폐기 UI | REQ-FUNC-001, 049 |
| P0 | `/api/v1/chat/reply` Route Handler | REQ-FUNC-007~014 |
| P0 | Gemini Flash-Lite 연동과 provider adapter | C-TEC-005~006 |
| P0 | 안전 응답 최소 정책 | REQ-FUNC-012, REQ-NF-014~015 |
| P0 | Web API Playground | REQ-FUNC-051~055 |
| P1 | `/api/v1/analyze/emotion` | REQ-FUNC-016~019 |
| P1 | PoC Report API/Console | REQ-FUNC-022~026, 056 |
| P1 | Conversation Log / Ops Monitoring 최소 화면 | REQ-FUNC-033~035, 057~058 |
| P1 | User & Consent Admin View 최소 화면 | REQ-FUNC-036~039, 059 |
| P2 | `/api/v1/schedule/proactive` | REQ-FUNC-020~021 |
| Post-MVP | Phase B sensor, webhook, KPI report | REQ-FUNC-040~047 |

### 3.4 개발 속도 결론

현재 단일구조 스택은 개발 속도에 적합하다. 다만 SRS 전체를 그대로 구현 대상으로 삼으면 과하다. 실제 구현은 P0/P1/P2/Post-MVP로 자르고, 첫 MVP는 `API Key + Chat Reply + Playground + 기본 로그 + Gemini 비용 통제`에 집중해야 한다.

---

## 4. 외부 연동 목표와 기술 스택 검토

### 4.1 외부 연동 목표 적합성

| 외부 연동 대상 | 현재 SRS 적합성 | 판단 |
|---|---:|---|
| B2B 파트너 기기 | 높음 | STT/TTS를 파트너가 담당하고 실버케어는 텍스트 JSON API만 제공하는 구조가 적절 |
| B2B 파트너 서버 | 높음 | API Key, tenant_id, report API, webhook 확장 방향이 적절 |
| B2B 개발자 | 높음 | Developer Portal, API Docs, Playground, TypeScript snippet은 연동 속도에 유리 |
| 보호자/기관 시스템 | 중간 | MVP에서는 직접 앱 제공이 아니라 파트너 시스템 연동으로 제한되어 적절 |
| Gemini API | 높음 | Vercel AI SDK와 TypeScript 기반 구현에 적합 |
| Supabase | 높음 | MVP DB와 관리형 Postgres로 적절. 다만 Prisma 연결 방식 주의 필요 |
| Vercel | 높음 | Next.js 배포와 Preview Deployment에 최적화. 다만 장기 vendor lock-in 존재 |

외부 연동 목표와 기술 스택은 전체적으로 잘 맞는다. 특히 B2B 파트너에게 제공할 API는 REST/JSON과 API Key 인증이면 충분하다. gRPC, Kafka, MSA, 별도 API Gateway는 MVP에 불필요하다.

### 4.2 기술 스택별 적절성과 리스크

| 기술 | 적절성 | 장점 | 리스크 | 대응 |
|---|---:|---|---|---|
| Next.js App Router | 높음 | UI/API/서버 로직 통합, AI 도구와 궁합 좋음 | client/server boundary 실수 | `server-only` 규칙과 import 검사 |
| Server Actions | 중간-높음 | 콘솔 mutation 빠르게 구현 | 외부 API 계약과 섞이면 추후 분리 어려움 | 내부 UI mutation으로만 제한 |
| Route Handlers | 높음 | B2B API endpoint 구현에 적합 | OpenAPI 자동 생성 약함 | Zod 기반 schema + OpenAPI 생성 |
| Prisma | 높음 | 타입 안정성, migration, 관계 모델링 | serverless connection 관리 | Supabase pooler 또는 Prisma Accelerate 검토 |
| local SQLite | 높음 | 로컬 개발 속도 빠름 | PostgreSQL과 enum/json/index 차이 | PoC 전 Supabase migration 테스트 필수 |
| Supabase PostgreSQL | 높음 | managed DB, backup, dashboard | 과도한 기능 사용 시 lock-in | DB는 PostgreSQL 표준 위주로 사용 |
| Tailwind + shadcn/ui | 높음 | 빠른 UI 구현, 일관된 컴포넌트 | 화면 수가 늘면 유지보수 부담 | Partner Console 범위 제한 |
| Vercel AI SDK | 높음 | TypeScript 기반 LLM integration에 적합 | provider API 변경 가능성 | provider adapter로 캡슐화 |
| Google Gemini API | 높음 | Flash-Lite 비용 효율이 좋음 | rate limit, 모델 품질 변동, API key 비용 사고 | quota, spend alert, key rotation |
| Vercel | 높음 | Next.js 배포 최적화 | Pro/Enterprise 기능 의존 시 비용 증가 | MVP는 Pro 이하 usage cap 유지 |

### 4.3 오픈소스 및 외부 의존성 리스크

| 영역 | 오픈소스/관리형 성격 | 리스크 | MVP 대응 |
|---|---|---|---|
| Next.js | 오픈소스 + Vercel 최적화 | Vercel에 최적화된 구조가 다른 플랫폼 이식성을 낮출 수 있음 | Route Handler와 domain service 경계 유지 |
| shadcn/ui | 오픈소스 컴포넌트 복사 방식 | 컴포넌트 수정 책임이 프로젝트에 있음 | 디자인 시스템을 과도하게 커스터마이징하지 않음 |
| Prisma | ORM/도구 의존 | serverless 연결, migration drift | pooled connection과 migration check 필수 |
| Supabase | 오픈소스 기반 managed service | Auth/Storage/Edge Functions까지 쓰면 lock-in 증가 | MVP는 PostgreSQL 중심으로 사용 |
| Vercel AI SDK | 오픈소스 SDK + provider 연동 | SDK 추상화 변경 가능성 | provider adapter를 얇게 유지 |
| Gemini API | 외부 상용 API | 비용 폭주, 모델 변경, 장애 | per-tenant quota, global monthly cap, fallback response |

### 4.4 외부 연동 및 기술 스택 결론

현재 기술 스택은 MVP에 적합하다. 추가로 조정할 것은 새로운 기술을 더 넣는 것이 아니라, 오히려 기술을 덜 쓰는 것이다.

MVP에서는 다음을 피해야 한다.

| 피해야 할 것 | 이유 |
|---|---|
| 별도 API Gateway | Vercel Route Handlers로 충분 |
| 별도 Python LLM 서버 | C-TEC-005와 충돌하고 운영 비용 증가 |
| Supabase Edge Functions | Next.js Route Handlers와 중복 |
| Vercel Workflow/Queues | 초기에는 DB job table로 충분 |
| Realtime 기능 | PoC 리포트/로그 조회에는 polling 또는 manual refresh로 충분 |
| 고급 Observability 유료 add-on | 초기는 DB audit log와 Vercel 기본 로그로 충분 |

---

## 5. 운영 소요 비용 검토

### 5.1 비용 구조 요약

MVP 비용은 크게 3개 축으로 나뉜다.

| 비용 축 | 주요 비용 발생 요인 | MVP 영향 |
|---|---|---|
| Vercel | Pro seat, function invocation, active CPU, data transfer, observability | 작은 PoC에서는 낮음 |
| Supabase | Pro plan, DB compute, DB size, egress, backup/add-on | 작은 PoC에서는 예측 가능 |
| Gemini | 입력/출력 tokens, 모델 티어, grounded search, retry | 사용량이 늘수록 가장 민감 |

첫 PoC에서는 인프라보다 LLM 비용 통제와 API key 보안이 더 중요하다.

### 5.2 PoC 트래픽 가정

| 가정 ID | 값 | 설명 |
|---|---:|---|
| A-COST-001 | 50명 | 첫 B2B 파트너 PoC 어르신 수 |
| A-COST-002 | 5회/일 | 어르신 1명당 일평균 대화 호출 |
| A-COST-003 | 7,500회/월 | `chat/reply` 기본 호출량 |
| A-COST-004 | 10,000~20,000회/월 | 분석, Playground, 재시도 포함 보수적 LLM 호출량 |
| A-COST-005 | 입력 1,000 tokens/call | system prompt, 최근 요약, 사용자 발화 포함 |
| A-COST-006 | 출력 250 tokens/call | 응답 텍스트와 JSON metadata 포함 |
| A-COST-007 | DB 8GB 이하 | 첫 PoC에서 텍스트 로그 중심 |
| A-COST-008 | egress 250GB 이하 | 텍스트 JSON API 기준 |

### 5.3 LLM 비용 추정

계산식:

```text
월 LLM 비용 = 입력 토큰 / 1,000,000 x 입력 단가 + 출력 토큰 / 1,000,000 x 출력 단가
```

PoC 50명 기준 비용:

| 월 LLM 호출 | 모델 | 입력 토큰 | 출력 토큰 | 예상 월 비용 |
|---:|---|---:|---:|---:|
| 10,000 | Gemini 2.5 Flash-Lite | 10M | 2.5M | 약 $2.00 |
| 20,000 | Gemini 2.5 Flash-Lite | 20M | 5M | 약 $4.00 |
| 10,000 | Gemini 2.5 Flash | 10M | 2.5M | 약 $9.25 |
| 20,000 | Gemini 2.5 Flash | 20M | 5M | 약 $18.50 |

성장 시나리오:

| 시나리오 | 월 LLM 호출 | Flash-Lite 예상 | Flash 예상 | 판단 |
|---|---:|---:|---:|---|
| 내부 알파 | 5,000 | 약 $1 | 약 $4.63 | 비용 부담 낮음 |
| 첫 PoC 50명 | 20,000 | 약 $4 | 약 $18.50 | 비용 부담 낮음 |
| 1,000명 | 195,000 | 약 $39 | 약 $180 | 비용 통제 필요 |
| 10,000명 | 1,950,000 | 약 $390 | 약 $1,804 | Post-MVP 비용 최적화 필요 |

비용 효율성 관점에서는 Gemini 2.5 Flash-Lite를 기본값으로 두고, 위험 판단이나 낮은 신뢰도 케이스만 Flash로 승격하는 모델 라우팅이 적절하다.

### 5.4 Vercel + Supabase 비용 추정

| 시나리오 | Vercel | Supabase | Gemini | 월 합계 추정 | 판단 |
|---|---:|---:|---:|---:|---|
| 로컬 개발 | $0 | $0~$25 | $0~$5 | $0~$30 | 로컬 SQLite와 Gemini free/low usage로 충분 |
| 내부 알파 | $0~$20 | $0~$25 | $1~$5 | $1~$50 | 비용 매우 낮음 |
| 첫 PoC 50명 | $20 | $25 | $4~$19 | 약 $49~$64 | 적절 |
| 3개 PoC / 1,000명 | $20~$50 | $25~$50 | $39~$180 | 약 $84~$280 | 여전히 관리 가능 |
| 10,000명 규모 | $50+ | $100+ | $390~$1,804 | $540~$2,000+ | MVP 범위 초과, 최적화 필요 |

첫 PoC 기준으로는 Vercel Pro + Supabase Pro + Gemini Flash-Lite 조합이 월 $50 안팎에서 시작 가능하다. 이 정도면 B2B PoC 검증 비용으로 적절하다.

### 5.5 비용 폭주 리스크

| Risk ID | 리스크 | 영향 | 대응 |
|---|---|---|---|
| R-COST-001 | Gemini API Key 유출 | 단기간 큰 비용 발생 가능 | API key server-only 보관, key rotation, GCP budget alert, quota 설정 |
| R-COST-002 | Playground 오남용 | sandbox 호출이 LLM 비용으로 누적 | sandbox quota, sample response mode, per-user daily limit |
| R-COST-003 | 긴 prompt/context | 입력 토큰 증가 | memory summary만 사용, raw history window 제한 |
| R-COST-004 | 긴 응답 | 출력 토큰 증가 | `maxOutputTokens` 제한, 응답 길이 정책 |
| R-COST-005 | retry storm | timeout 재시도로 중복 비용 발생 | exponential backoff, idempotency key, retry cap |
| R-COST-006 | Vercel function active CPU 증가 | LLM 대기와 heavy processing으로 비용 증가 | streaming, timeout, background 최소화 |
| R-COST-007 | Supabase DB/log 증가 | audit/event 로그가 누적 | retention job, summary table, archive policy |

### 5.6 운영 비용 결론

운영 비용 관점에서 현재 단일구조 스택은 적절하다. 단, 비용 효율성은 “Vercel/Supabase가 싸다”보다 “LLM 호출을 얼마나 잘 제한하느냐”에 달려 있다.

MVP 비용 정책은 다음을 SRS 또는 구현 TASKS에 추가하는 것이 적절하다.

| 정책 | 권장값 |
|---|---|
| 기본 모델 | `gemini-2.5-flash-lite` |
| 승격 모델 | `gemini-2.5-flash`, 위험/불확실 케이스만 사용 |
| 입력 토큰 상한 | 1,200 tokens/call 이하 |
| 출력 토큰 상한 | 250~350 tokens/call |
| tenant별 일 호출 제한 | PoC 계약 단위로 설정 |
| Playground 제한 | sandbox user별 일 N회, production 호출 별도 권한 |
| 월 예산 알림 | Gemini, Vercel, Supabase 모두 50%, 75%, 90% 알림 |
| raw log 보존 | 30일 이하 |
| summary/report 보존 | PRD 기준 최대 180일~1년 |

---

## 6. 더 조정할 항목

### 6.1 SRS에서 조정 권장

| 항목 | 현재 표현 | 조정 권고 |
|---|---|---|
| `REQ-NF-001` | `/api/v1/chat/reply` p95 800ms, p99 1.5s | LLM 첫 응답 시작 p95와 전체 완료 p95를 분리 |
| `REQ-NF-002` | 99.9% SLA | MVP PoC baseline과 Production target 분리 |
| `REQ-NF-004` | 10,000대 concurrent simulated | Post-MVP scalability target으로 이동 |
| `REQ-NF-011~013` | recall/오탐률 정량 목표 | MVP에서는 평가셋/리뷰 큐/측정 체계 구축을 완료 조건으로 둠 |
| `REQ-FUNC-040~047` | Phase B 기능 요구 | 문서 추적성은 유지하되 `Not in MVP implementation cut` 명시 |
| `REQ-FUNC-038` | 삭제 전파 범위가 광범위 | MVP는 primary DB 삭제 job + audit status부터 구현 |
| API Docs | Swagger 표현 | Next.js에서는 OpenAPI-compatible docs 또는 generated OpenAPI로 구체화 |
| 운영 모니터링 | 운영자 알림 5분 | MVP는 dashboard/log query 우선, alert는 P1 또는 P2 |

### 6.2 구현 TASKS에서 조정 권장

| 작업 | 구현 방식 |
|---|---|
| API schema | Zod schema를 request/response source of truth로 사용 |
| OpenAPI | `zod-to-openapi` 또는 유사 도구로 생성 |
| DB access | `src/server/repositories/*` 아래 Prisma 격리 |
| LLM 호출 | `src/server/ai/providers/gemini.ts` adapter로 격리 |
| safety policy | rule-based precheck + Gemini structured output 후처리 |
| audit log | 모든 민감 action을 `audit_log` table에 저장 |
| usage event | 모든 LLM/API billable call을 `usage_event` table에 저장 |
| cost cap | tenant quota, max tokens, retry cap 구현 |
| playground | 기본은 sandbox sample response, 실제 Gemini 호출은 명시 실행 |

### 6.3 지금 추가하지 않는 것이 좋은 것

| 제외 항목 | 이유 |
|---|---|
| 별도 백엔드 서버 | MVP 속도와 C-TEC-001에 반함 |
| Supabase Edge Functions | Next.js Route Handlers와 중복 |
| Vercel Workflow/Queues | 초기에는 DB job table과 cron/manual trigger로 충분 |
| Realtime dashboard | 비용/복잡도 증가. 초기에는 manual refresh로 충분 |
| 고급 APM/Observability add-on | 첫 PoC에서는 Vercel 기본 로그 + DB audit log로 충분 |
| 모델 파인튜닝 | 비용과 데이터 준비 부담이 큼 |
| 보호자/기관 완성 앱 | PRD Out-of-Scope 유지 필요 |

---

## 7. 최종 권고

현재 `실버케어_SRS_v0.5_단일구조.md`는 MVP 개발 방향으로 적절하다. 시스템 및 비용 효율성 관점에서 보면 아키텍처 자체를 바꿀 필요는 없다. 오히려 지금의 단일구조 선택은 개발 속도, 외부 연동 단순성, 운영 비용 측면에서 합리적이다.

다만 MVP 구현 성공률을 높이려면 아래 5가지를 반드시 조정해야 한다.

| 우선순위 | 조정 | 이유 |
|---|---|---|
| 1 | Phase B 기능을 구현 범위에서 명확히 제외 | 개발 범위 폭증 방지 |
| 2 | p95/SLA/10,000대 동시성 같은 NFR을 MVP baseline과 production target으로 분리 | 구현 불가능한 완료 조건 방지 |
| 3 | Gemini Flash-Lite 기본 + Flash 승격 전략 명시 | LLM 비용 통제 |
| 4 | 토큰 상한, tenant quota, Playground quota, spend alert 추가 | 비용 폭주 방지 |
| 5 | SRS와 별도로 `TASKS/MVP_단일구조_구현작업.md` 작성 | 바이브코딩 실행 가능성 향상 |

MVP의 핵심 가치는 훼손되지 않는다. 오히려 단일 Next.js 구조는 B2B 개발자가 API Key를 발급하고, Playground에서 호출을 실행하고, Gemini 기반 응답과 분석 결과를 확인하며, PoC 리포트로 유료 전환 가능성을 판단하는 핵심 흐름을 가장 빠르게 구현할 수 있는 선택이다.

---

## 8. References

| ID | Source |
|---|---|
| REF-COST-01 | Vercel Pricing: https://vercel.com/pricing |
| REF-COST-02 | Vercel Pro Plan: https://vercel.com/docs/plans/pro-plan |
| REF-COST-03 | Supabase Billing: https://supabase.com/docs/guides/platform/billing-on-supabase |
| REF-COST-04 | Supabase Compute Usage: https://supabase.com/docs/guides/platform/manage-your-usage/compute |
| REF-COST-05 | Google Gemini API Pricing: https://ai.google.dev/gemini-api/docs/pricing |
