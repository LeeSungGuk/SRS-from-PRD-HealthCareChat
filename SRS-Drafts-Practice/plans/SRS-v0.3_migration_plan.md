# SRS v0.3 마이그레이션 계획서

**문서 목적:** SRS v0.2 → v0.3 기술 스택 전환 시, MVP 핵심 사용자 경험(가치 전달)이 훼손되지 않는지 검토하고, 구체적인 문서 변경 계획을 수립한다.

**Base Document:** `SRS-v0.2_opus.md`  
**Target Stack:** C-TEC-001 ~ C-TEC-007  
**작성일:** 2026-04-16

---

## Part 1. 핵심 사용자 경험 훼손 여부 검토

> **검토 원칙:** "기술 스택은 바뀌어도, 사용자가 느끼는 가치(속도, 신뢰, 편리함)는 절대 줄어들면 안 된다."
>
> 아래에서 4대 핵심 기능(F1~F4)별로, PRD가 약속한 **사용자 가치**가 새 스택에서도 **동일하게 전달 가능한지** 하나씩 따져봅니다.

### 1.1 F1: Super-Calc Engine (1일 단가 비교)

#### 📌 약속한 사용자 가치
- C1 한정훈: **"5초 내 실시간 최저가 확인, 1일 단가 기준 정렬"**
- 핵심 SLA: 응답 p95 ≤ 2초, 실패율 < 1%, 부분 결과 성공률 ≥ 99%

#### 🔍 스택 전환 영향 분석

| 항목 | 기존 (분리형 백엔드) | Next.js 풀스택 (C-TEC) | 사용자 경험 영향 |
|---|---|---|---|
| **3채널 병렬 조회** | 전용 서버에서 `Promise.allSettled` | Next.js Route Handler에서 동일하게 `Promise.allSettled` | ✅ **동일** — JS 런타임이므로 병렬 조회 로직 그대로 사용 가능 |
| **응답 시간 p95 ≤ 2초** | 전용 서버 상시 구동 (Cold Start 없음) | Vercel Serverless는 **Cold Start 500ms~1.5s** 발생 가능 | ⚠️ **위험** — 첫 요청 시 SLA 초과 가능성 |
| **환율 캐싱 (TTL 15분)** | Redis 전용 캐시 | Next.js `unstable_cache` + `revalidate: 900` 또는 Vercel KV | ✅ **동일** — 캐싱 메커니즘은 달라도 사용자 체감은 동일 |
| **채널 장애 시 부분 결과** | 서비스 내부 에러 핸들링 | Route Handler 내부 에러 핸들링 (동일 로직) | ✅ **동일** |
| **배송비·관세 포함 최종가** | 서버 로직 | Route Handler 로직 (동일) | ✅ **동일** |

#### ⚠️ 위험 요소 및 완화 방안

| 위험 | 심각도 | 완화 방안 |
|---|---|---|
| Vercel Serverless Cold Start로 p95 ≤ 2초 위반 | 🟡 중간 | **방안 1:** 단가 비교 Route를 **Edge Runtime**으로 설정하여 Cold Start를 50ms 이내로 단축. **방안 2:** Vercel Pro의 **Fluid Compute**(함수 재사용)로 Warm 상태 유지. **방안 3:** Vercel Cron으로 5분마다 자체 Health Check 호출하여 Warm 유지 |

#### ✅ 결론: **사용자 가치 훼손 없음** (Cold Start 완화 조치 전제)

---

### 1.2 F2: Anti-BS Dashboard (팩트체크 뱃지 + 용어 번역)

#### 📌 약속한 사용자 가치
- C2 박소연: **"광고 배제 환경에서 30분 내 확신 있는 결정, 전문 용어 일상어 번역"**
- A2 정수빈: **"5초 팩트체크"**
- 핵심 SLA: 뱃지 렌더 p95 ≤ 1초, 마케팅 콘텐츠 = 0건, 번역 커버리지 ≥ 95%

#### 🔍 스택 전환 영향 분석

| 항목 | 기존 | Next.js 풀스택 (C-TEC) | 사용자 경험 영향 |
|---|---|---|---|
| **마케팅 콘텐츠 0건** | 서버 필터링 | 동일 — 데이터 소스 자체가 식약처 API이므로 광고 혼입 경로 없음 | ✅ **동일** |
| **뱃지 판정 로직** | Badge Service | Server Action 또는 Route Handler 내부 함수 | ✅ **동일** — 판정 로직은 순수 함수이므로 실행 위치 무관 |
| **뱃지 캐싱 (TTL 24h)** | Redis | `unstable_cache({ revalidate: 86400 })` 또는 Supabase 테이블 캐싱 | ✅ **동일** |
| **용어 번역** | 별도 TransService | **Vercel AI SDK + Gemini API** (C-TEC-005/006) | ✅ **개선** — LLM 기반으로 번역 커버리지와 자연스러움이 **향상**될 수 있음 |
| **금지 표현 필터** | 서버 로직 | Route Handler 내 Zod 스키마 검증 + 금지어 목록 대조 | ✅ **동일** |

#### ✅ 위험 요소: 없음

> **보너스:** Gemini LLM을 활용한 용어 번역은 기존 정적 사전 방식보다 **더 자연스럽고 커버리지가 넓은** 번역을 제공할 수 있어, 오히려 사용자 가치가 **향상**될 여지가 있습니다.

#### ✅ 결론: **사용자 가치 훼손 없음** (오히려 LLM 번역으로 개선 가능)

---

### 1.3 F3: Viral Engine (카카오톡 1-Tap 공유)

#### 📌 약속한 사용자 가치
- A2 정수빈: **"1탭 카카오톡 공유, 비주얼 공유 카드"**
- 수신자: **"앱 설치 없이 바로 비교 결과 확인"**
- 핵심 SLA: 카드 생성 p95 ≤ 1.5초, 랜딩 성공률 ≥ 98%, 로드 시간 ≤ 2초

#### 🔍 스택 전환 영향 분석

| 항목 | 기존 | Next.js 풀스택 (C-TEC) | 사용자 경험 영향 |
|---|---|---|---|
| **OG 이미지 동적 생성** | 별도 OG Image Generator 서비스 | **`@vercel/og` (Satori)** — Edge Runtime에서 동적 OG 이미지 생성 (Next.js 공식 지원) | ✅ **개선** — Edge에서 실행되므로 전세계 어디서든 빠르고, 별도 서비스 불요 |
| **카카오 Link API 호출** | 프론트엔드 JS SDK | **동일** — Next.js 클라이언트 컴포넌트(`'use client'`)에서 카카오 JS SDK 호출 | ✅ **동일** |
| **폴백 (URL 복사/밴드/문자)** | 프론트엔드 로직 | 동일 — 클라이언트 컴포넌트 내 분기 처리 | ✅ **동일** |
| **공유 카드 랜딩 페이지** | 별도 웹뷰 페이지 | Next.js 동적 라우트 (`/share/[id]`) + `generateMetadata`로 OG 태그 자동 생성 | ✅ **개선** — Next.js의 SSR/ISR로 SEO와 OG 미리보기가 더 완벽 |
| **수신자 랜딩 로드 시간** | SPA 전체 번들 로드 | Next.js SSR/Streaming → **첫 바이트가 더 빠름** | ✅ **개선** |

#### ✅ 위험 요소: 없음

#### ✅ 결론: **사용자 가치 훼손 없음** (OG 이미지, 랜딩 속도 모두 개선)

---

### 1.4 F4: Data Trust System (출처 확인 + 오류 제보)

#### 📌 약속한 사용자 가치
- E2 김도현: **"출처 2클릭 내 추적, 오류 제보 시 48h 내 수정 보장, 보상 체계"**
- 핵심 SLA: 아코디언 렌더 ≤ 500ms, 접수 응답 ≤ 3초, 48h SLA

#### 🔍 스택 전환 영향 분석

| 항목 | 기존 | Next.js 풀스택 (C-TEC) | 사용자 경험 영향 |
|---|---|---|---|
| **출처 아코디언 UI** | SPA 컴포넌트 | shadcn/ui `<Accordion>` 컴포넌트 (C-TEC-004) | ✅ **동일** — UI 라이브러리가 달라도 사용자 체감 동일 |
| **라벨 이미지 저장/조회** | S3 | **Supabase Storage** 또는 **Vercel Blob** | ✅ **동일** — 이미지 URL 기반 조회이므로 스토리지 종류 무관 |
| **오류 제보 접수** | Error Report Service → DB | **Server Action** → Prisma → Supabase DB | ✅ **동일** — DB 쓰기 로직의 실행 위치만 다름 |
| **스팸/중복 필터** | 별도 필터 서비스 | Server Action 내부 Prisma 쿼리 (`where: { productId, reporterId, reportedAt > 24h ago }`, `count >= 5`) | ✅ **동일** |
| **48h SLA 추적** | Jira SLA 보드 | 동일 — 운영 프로세스이므로 기술 스택과 무관 | ✅ **동일** |
| **리워드 지급 + 알림** | 알림 서비스 | Server Action 내 DB 업데이트 + 이메일 API (Resend 등) | ✅ **동일** |

#### ✅ 위험 요소: 없음

#### ✅ 결론: **사용자 가치 훼손 없음**

---

### 1.5 종합 판정 매트릭스

| 핵심 기능 | 약속한 사용자 가치 | 스택 전환 후 | 판정 |
|---|---|---|---|
| **F1. Super-Calc** | 5초 내 최저가, p95 ≤ 2s | Edge Runtime + Warm 유지로 **SLA 충족 가능** | ✅ 유지 |
| **F2. Anti-BS** | 광고 0건, 30분 내 결정, 용어 번역 | 로직 동일 + **LLM 번역으로 개선** | ✅ 유지 (↑개선) |
| **F3. Viral** | 1탭 공유, 앱 설치 불요 랜딩 | `@vercel/og` + SSR로 **더 빠른 랜딩** | ✅ 유지 (↑개선) |
| **F4. Data Trust** | 2클릭 출처, 48h SLA | 로직 동일, UI 컴포넌트만 교체 | ✅ 유지 |

### 1.6 사용자 페르소나별 최종 검토

| 페르소나 | 핵심 니즈 | 훼손 여부 | 비고 |
|---|---|---|---|
| **C1 한정훈** (가성비 직구족) | 실시간 최저가 ≤ 2초 | ✅ 충족 | Edge Runtime 적용 시 Cold Start 해소 |
| **C2 박소연** (건강 계기 진입자) | 광고 없는 환경 + 쉬운 용어 | ✅ 충족 | Gemini LLM으로 번역 품질 오히려 향상 |
| **A2 정수빈** (트렌드 탐색자) | 1탭 공유 + 예쁜 카드 | ✅ 충족 | `@vercel/og`로 더 빠른 OG 이미지 생성 |
| **E2 김도현** (신뢰 실패자) | 출처 투명성 + 48h SLA | ✅ 충족 | 운영 SLA는 기술 스택과 무관 |

> **최종 결론: 4대 핵심 기능의 사용자 경험은 100% 유지되며, F2(번역)와 F3(랜딩 속도)은 오히려 개선됩니다.**
> 유일한 조건부 주의사항은 F1의 Cold Start인데, Edge Runtime 또는 Warm 유지 전략으로 **충분히 완화 가능**합니다.

---

## Part 2. SRS 문서 변경 계획

> 아래는 SRS v0.2 → v0.3 수정 시 변경해야 할 모든 영역을 정리한 것입니다.

### 2.1 §1.2.3 Constraints (제약사항) 변경

**현재 (v0.2):**
- CON-03: MVP 월 인프라 비용을 200만 원 이하로 유지

**변경 후 (v0.3):**
- CON-03: MVP 월 인프라 비용을 **무료~최대 10만 원 이하**로 유지 (프리 티어 적극 활용)
- **C-TEC-001 ~ C-TEC-007** 전체를 Constraints 테이블에 **신규 추가**

```markdown
| C-TEC-001 | 기술 | 모든 서비스는 Next.js (App Router) 단일 풀스택으로 구현한다 | 개발 복잡도 최소화 |
| C-TEC-002 | 기술 | 서버 로직은 Server Actions / Route Handlers로 구현하며 별도 백엔드를 두지 않는다 | 인프라 단순화 |
| C-TEC-003 | 기술 | DB는 Prisma + SQLite(로컬) / Supabase PostgreSQL(배포)를 사용한다 | 인프라 비용 최소화 |
| C-TEC-004 | 기술 | UI는 Tailwind CSS + shadcn/ui를 사용한다 | AI 코드 생성 일관성 |
| C-TEC-005 | 기술 | LLM 오케스트레이션은 Vercel AI SDK 기반으로 Next.js 내부에서 구현한다 | Python 서버 배제 |
| C-TEC-006 | 기술 | LLM은 Google Gemini API 기본 사용, 환경변수로 모델 교체 가능하게 한다 | 모델 유연성 |
| C-TEC-007 | 기술 | 배포/인프라는 Vercel 플랫폼 단일화, Git Push 자동 배포한다 | 운영 복잡도 제거 |
```

---

### 2.2 §1.2.4 Assumptions (가정) 변경

**추가할 가정:**

```markdown
| ASM-07 | Vercel Serverless Function의 Edge Runtime이 외부 API 병렬 호출 시 p95 ≤ 2초를 충족한다 | MVP 착수 전 PoC에서 3채널 병렬 호출 벤치마크 실행 | MVP 착수 전 |
| ASM-08 | Supabase Free Tier (500MB DB, 1GB Storage)가 MVP 초기 300~500개 SKU를 충분히 수용한다 | Supabase Free Tier 용량 대비 예상 데이터 사이즈 산정 | MVP 착수 전 |
| ASM-09 | Vercel AI SDK + Gemini API의 전문 용어 일상어 번역 정확도가 >= 98%이다 | 식약처 기능성 원료 100건 대상 번역 품질 테스트 | MVP 착수 전 |
```

---

### 2.3 §1.3 용어 정의 추가

```markdown
| Next.js App Router | React 기반 풀스택 웹 프레임워크. 서버/클라이언트 컴포넌트를 나누어 렌더링하는 최신 라우팅 시스템 |
| Server Action | Next.js에서 서버에서만 실행되는 함수. 폼 제출, DB 쓰기 등에 사용 |
| Route Handler | Next.js에서 API 엔드포인트를 만드는 방법. 기존 REST API와 유사 |
| Prisma | TypeScript/JavaScript용 ORM(데이터베이스 연결 도구). 스키마 정의로 자동 타입 생성 |
| Supabase | PostgreSQL 기반 오픈소스 BaaS(Backend-as-a-Service). 인증, DB, 스토리지 제공 |
| Vercel AI SDK | Vercel에서 제공하는 AI/LLM 통합 라이브러리. 스트리밍 응답, 멀티 모델 지원 |
| shadcn/ui | Tailwind CSS 기반의 복사-붙여넣기 가능한 UI 컴포넌트 라이브러리 |
| Edge Runtime | Vercel Edge Network에서 실행되는 경량 런타임. Cold Start가 거의 없음 |
| @vercel/og | Edge에서 동적 OG 이미지를 생성하는 Vercel 공식 라이브러리 |
| ISR | Incremental Static Regeneration. 정적 페이지를 주기적으로 재생성하는 Next.js 기능 |
```

---

### 2.4 §1.4 References 변경

**현재:** REF-01 ~ REF-09에 외부 파일 경로 참조  
**변경:** PRD 문서 외 다른 문서의 참조 경로를 제거하고, PRD 문서에 직접 포함된 내용으로 치환 (이전 대화에서 합의한 사항)

---

### 2.5 §2 Stakeholders 변경

| 현재 | 변경 |
|---|---|
| `인프라 비용 200만 원/월 이하` | `인프라 비용 무료~최대 10만 원/월 이하 (Vercel + Supabase 프리 티어 활용)` |

---

### 2.6 §3.1 External Systems 변경

**추가할 외부 시스템:**

```markdown
| EXT-06 | Google Gemini API | REST API (Vercel AI SDK 경유) | 전문 용어 일상어 번역, 성분 설명 생성 | 무료 티어: 60 RPM, 유료 전환 시 환경변수로 모델 교체 |
| EXT-07 | Supabase | BaaS (PostgreSQL + Storage + Auth) | 메인 DB, 라벨 이미지 저장, 사용자 인증 | Free Tier: 500MB DB, 1GB Storage |
```

**삭제/수정 대상:**
- Redis 캐시 언급 → Next.js 내장 캐싱(`unstable_cache`, ISR)으로 대체
- S3 Object Storage → Supabase Storage로 대체
- API Gateway → 삭제 (Next.js 미들웨어가 대체)

---

### 2.7 §3.3 API Overview 변경

**기존 구조:** `POST /api/v1/calc/daily-cost` (별도 백엔드 REST API)  
**새 구조:** Next.js Route Handler (`app/api/calc/daily-cost/route.ts`)

엔드포인트 **URL 패턴은 동일하게 유지** (Next.js Route Handler가 같은 경로를 지원하므로), 단 구현 위치가 달라짐을 명시:

| API 명 | 구현 방식 (v0.3) | 비고 |
|---|---|---|
| Super-Calc API | Route Handler (`app/api/calc/daily-cost/route.ts`) | Edge Runtime 적용 |
| Badge API | Route Handler (`app/api/badges/[ingredientId]/route.ts`) | `unstable_cache` TTL 24h |
| Error Report API | **Server Action** (`app/actions/report.ts`) | 폼 제출이므로 Server Action이 적합 |
| Label Archive API | Route Handler (`app/api/labels/[productId]/route.ts`) | Supabase Storage URL 반환 |
| Share Card API | Route Handler (`app/api/share/kakao/route.ts`) + `@vercel/og` | OG 이미지 동적 생성 |
| Product Search API | Route Handler (`app/api/products/search/route.ts`) | Prisma 풀텍스트 검색 |

---

### 2.8 §3.6 Component Diagram 전면 재설계

**현재:** 6개 계층 (Client → Gateway → Core Services → Data → External → Monitoring)  
**변경 후:** Next.js 단일 앱 기반 3계층 구조

```
변경 후 구조 (Mermaid로 재작성 예정):

┌─────────────────────────────────────────────────┐
│  Vercel Edge Network                            │
│  ┌─────────────────────────────────────────────┐│
│  │  Next.js App (App Router)                   ││
│  │  ┌──────────────────┬──────────────────────┐││
│  │  │  Client 컴포넌트  │  Server 컴포넌트     │││
│  │  │  (Tailwind+shadcn)│  (RSC, SSR, ISR)    │││
│  │  └──────────────────┴──────────────────────┘││
│  │  ┌─────────────────────────────────────────┐││
│  │  │  Route Handlers & Server Actions        │││
│  │  │  - /api/calc/daily-cost (Edge Runtime)  │││
│  │  │  - /api/badges/[id]                     │││
│  │  │  - /api/share/kakao + @vercel/og        │││
│  │  │  - Server Action: report, register      │││
│  │  └─────────────────────────────────────────┘││
│  │  ┌───────────────────────────────────────┐  ││
│  │  │  Prisma ORM                           │  ││
│  │  │  (Next.js <-> DB 연결 도구)            │  ││
│  │  └───────────────────────────────────────┘  ││
│  └─────────────────────────────────────────────┘│
│  ┌──────────┐ ┌──────────┐ ┌──────────────────┐│
│  │ Vercel   │ │ Vercel   │ │ Vercel           ││
│  │ Analytics│ │ Logs     │ │ Cron (Warm 유지) ││
│  └──────────┘ └──────────┘ └──────────────────┘│
└──────────────────────┬──────────────────────────┘
                       │ HTTPS
        ┌──────────────┼──────────────────┐
        v              v                  v
┌──────────────┐ ┌──────────┐  ┌──────────────────┐
│ Supabase     │ │ 외부     │  │ Google           │
│ - PostgreSQL │ │ API들    │  │ Gemini API       │
│ - Storage    │ │ - iHerb  │  │ (Vercel AI SDK)  │
│ - Auth       │ │ - 쿠팡   │  │                  │
│              │ │ - 식약처  │  │                  │
│              │ │ - 환율   │  │                  │
│              │ │ - 카카오  │  │                  │
└──────────────┘ └──────────┘  └──────────────────┘
```

---

### 2.9 §4.2 Non-Functional Requirements 변경

| REQ ID | 변경 사항 |
|---|---|
| **REQ-NF-001** | 측정 방법: `CloudWatch / Datadog` → `Vercel Analytics + Vercel Logs` |
| **REQ-NF-005** | LCP 측정: `Lighthouse` 유지 + `Vercel Speed Insights` 추가 |
| **REQ-NF-010** | Uptime 모니터링: `UptimeRobot` 유지 (무료), `Datadog` 제거 |
| **REQ-NF-011** | 에러율: `CloudWatch` → `Vercel Logs 에러율 대시보드` |
| **REQ-NF-014** | 백업: `Supabase 자동 백업 (Pro: Point-in-time recovery)` |
| **REQ-NF-022** | 비용: `200만 원` → `무료~최대 10만 원`. 측정: `Vercel Dashboard + Supabase Dashboard` |
| **REQ-NF-023** | 알림: `CloudWatch / Datadog → Slack` → `Vercel Logs → Slack Webhook` |
| **REQ-NF-025** | 즉시 알림: `PagerDuty` → `Vercel Logs Alert → Slack #alerts` |
| **REQ-NF-027** | 수평 확장: Vercel Serverless는 자동 스케일링이므로 `별도 설정 불요` 명시 |
| **REQ-NF-029** | 뱃지 동기화: `Vercel Cron Jobs`로 월 1회 자동 실행 |

---

### 2.10 §6.2 Data Model 변경

**엔터티 스키마 자체는 동일** (필드명, 관계 모두 유지)  
**변경점:**
- ORM: `Prisma schema`로 모든 엔터티 정의한다는 점 명시
- DB: 로컬 `SQLite` / 배포 `Supabase PostgreSQL` 이중 설정 명시
- ID 생성: `STRING PK` → `cuid()` 또는 `uuid()` 자동 생성 명시 (Prisma 기본값)

---

### 2.11 §6.2.10 Class Diagram 변경

**현재:** `SuperCalcEngine`, `BadgeService`, `ShareCardService` 등 별도 서비스 클래스  
**변경 후:** 서비스 클래스 제거 → 비즈니스 로직을 **도메인 함수 모듈**로 재구성

```
변경 전: class SuperCalcEngine { ... }
변경 후: // lib/calc/daily-cost.ts
         export async function calcDailyCost(...)
         // lib/badge/judge.ts
         export async function judgeBadge(...)
```

Class Diagram에서 서비스 클래스들을 **모듈/함수 집합**으로 재표현

---

### 2.12 §6.3 Sequence Diagram 변경

**공통 변경:**
- 모든 다이어그램에서 `API Gateway (GW)` 참여자 **제거**
- `프론트엔드 → GW → 서비스` 3단 호출 → `클라이언트 컴포넌트 → Route Handler (Next.js)` 2단 호출로 단순화
- `캐시 (Redis)` → `Next.js Cache (unstable_cache)` 로 명칭 변경
- `DB (데이터베이스)` → `Prisma → Supabase DB` 로 명확화

---

## Part 3. 변경 적용 순서

| 순서 | 영역 | 우선순위 | 난이도 | 사유 |
|---|---|---|---|---|
| 1 | §1.2.3 Constraints (C-TEC 추가 + 비용 변경) | 🔴 필수 | ⭐ 쉬움 | 전체 문서의 기술 전제를 먼저 확정해야 나머지 수정 가능 |
| 2 | §1.2.4 Assumptions (신규 가정 추가) | 🔴 필수 | ⭐ 쉬움 | C-TEC 스택 관련 검증 필요 사항 확정 |
| 3 | §1.3 용어 정의 + §1.4 References | 🟡 권장 | ⭐ 쉬움 | 새 기술 용어 + 참조 경로 정리 |
| 4 | §2 Stakeholders (비용 변경) | 🟡 권장 | ⭐ 쉬움 | 단순 수치 교체 |
| 5 | §3.1 External Systems (Gemini, Supabase 추가) | 🔴 필수 | ⭐⭐ 보통 | 외부 시스템 목록 갱신 |
| 6 | §3.3 API Overview (구현 방식 변경) | 🔴 필수 | ⭐⭐ 보통 | Route Handler / Server Action 매핑 |
| 7 | §3.6 Component Diagram (전면 재설계) | 🔴 필수 | ⭐⭐⭐ 어려움 | Mermaid 전면 재작성 |
| 8 | §4.2 NFR (모니터링/비용/배포 변경) | 🔴 필수 | ⭐⭐ 보통 | 도구명·수치 교체 |
| 9 | §6.2 Data Model (Prisma 명시) | 🟡 권장 | ⭐ 쉬움 | ORM 명시 + ID 생성 정책 |
| 10 | §6.2.10 Class Diagram (서비스 → 모듈) | 🟡 권장 | ⭐⭐⭐ 어려움 | Mermaid 전면 재작성 |
| 11 | §6.3 Sequence Diagrams (GW 제거, 2단 호출) | 🟡 권장 | ⭐⭐⭐ 어려움 | 4개 상세 다이어그램 전면 재작성 |

---

## Part 4. 작업 범위 추정

| 항목 | 예상 작업량 |
|---|---|
| 텍스트/표 수정 (순서 1~6, 8~9) | 중간 — 기존 내용의 단어·수치 교체 위주 |
| Mermaid 다이어그램 재작성 (순서 7, 10, 11) | 많음 — Component 1개, Class 1개, Sequence 4개 = 총 6개 다이어그램 |
| **총 합** | SRS v0.2 전체 1392줄 중 약 **40~50% 영역**에 변경 발생 |

---

*— End of Plan —*
