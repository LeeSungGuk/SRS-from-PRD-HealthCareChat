# Software Requirements Specification (SRS)

**Document ID:** SRS-001
**Revision:** 1.0
**Date:** 2026-04-15
**Standard:** ISO/IEC/IEEE 29148:2018

---

## 1. Introduction

### 1.1 Purpose

본 문서는 **건기식 성분·가격 비교 초자동화 플랫폼**(이하 "본 시스템" 또는 "SuperCalc 플랫폼")의 소프트웨어 요구사항 명세서(SRS)이다.

본 시스템은 국내 건강기능식품 시장(연 6조 원 규모)에서 발생하는 다음의 핵심 문제를 해결하기 위해 설계된다:

| 문제 영역 | 현 상태 (실패 KPI) |
|---|---|
| 채널 간 단가 비교 수동 작업 과부하 | 탐색·계산 소요 시간 >= 60분/건 |
| 성분 정보 해석 불가 — 비교 자체 불가 | 성분 비교 어려움 47.2%, 구매 여정 이탈률 55~75% |
| 광고성 콘텐츠 범람, 독립 신뢰 정보 부재 | 가격-품질 오인율 41.3%, 동일 성분 가격 차이 최대 8.2배 |
| 가격 적정성 판단 기준 부재 | 탐색 후 결론 실패 비율 >= 40% |
| 기존 비교 앱 데이터 오류 | 기존 앱 성분 DB 오류율 추정 >= 10%, 이탈 후 재방문율 < 5% |
| 인지 단계에서 광고 vs 독립 정보 구분 불가 | 오가닉 유입→가입 전환율 <= 15% |

**비전:** *"수동 엑셀 계산과 뒷광고 필터링에 지친 건기식 소비자들을 위한, 리얼-타임 1일 단가 계산 및 의학 팩트체크 플랫폼"*

본 SRS는 이해관계자, 개발팀, QA팀, 법률팀에게 시스템의 기능적·비기능적 요구사항을 명확하고 테스트 가능한 형태로 제공하며, 요구사항의 추적성을 보장한다.

### 1.2 Scope (In-Scope / Out-of-Scope)

#### 1.2.1 In-Scope (MVP)

| # | 범위 항목 |
|---|---|
| S1 | F1. 실시간 1일 단가 정규화 엔진 (iHerb, 쿠팡, 네이버 3채널) |
| S2 | F2. 식약처/논문 등급 배지 + 전문 용어 일상어 번역 |
| S3 | F3. 카카오톡 1-Tap 공유 (웹뷰 기반, 앱 설치 불요) |
| S4 | F4. 원본 라벨 아카이브 + 오류 제보 48h SLA |
| S5 | 상위 300~500개 영양제 제품 DB |
| S6 | 제휴 CPA 수익 모델 (iHerb, 쿠팡) |
| S7 | 모바일 웹 앱 (반응형) |

#### 1.2.2 Out-of-Scope (명시적 배제)

| # | 배제 항목 | 배제 사유 |
|---|---|---|
| OS1 | AI 개인화 맞춤 추천 | 기술 부채, 초기 데이터 편향 위험 |
| OS2 | 커뮤니티/리뷰 게시판 | Anti-BS 포지셔닝 훼손 위험 |
| OS3 | 네이티브 앱 | MVP는 모바일 웹 퍼스트 전략 |
| OS4 | 복잡한 헬스케어 온보딩 (문진/건강 프로필) | MVP 범위 초과 |
| OS5 | 전체 건기식 시장 커버리지 (수만 SKU) | 단계적 확장 전략 |
| OS6 | 브랜드 스폰서 광고/배너 | 독립성 훼손 금지 원칙 |
| OS7 | 데스크톱 전용 대시보드 | 모바일 퍼스트 우선 |

#### 1.2.3 Constraints (제약사항)

| ID | 유형 | 제약 내용 | 근거 |
|---|---|---|---|
| CON-01 | 법적 | 뱃지 텍스트는 식약처 건강기능식품공전 고시 문구만 래핑하며, 질병 예방/치료 표현을 절대 금지한다 | 건강기능식품법, R2 리스크 |
| CON-02 | 기술 | 무단 크롤링을 배제하고, 공식 Affiliate API만 사용한다 | R1 리스크 (Critical, 점수 20) |
| CON-03 | 비용 | MVP 월 인프라 비용을 200만 원 이하로 유지한다 (에셋 라이트 원칙) | NFR 비용 기준 |
| CON-04 | 기술 | 전 구간 TLS 1.2 이상을 적용한다 | 보안 요구사항 |
| CON-05 | 데이터 | B2B 데이터 제공 시 k-anonymity >= 5를 보장한다 | 개인정보 보호 요구사항 |
| CON-06 | 데이터 | 사용자 데이터 수집은 최소 수집 원칙을 따르며, MVP에서는 이메일·비교 이력만 수집한다 | 개인정보 최소 수집 원칙 |

#### 1.2.4 Assumptions (가정)

| ID | 가정 내용 | 검증 방안 | 시한 |
|---|---|---|---|
| ASM-01 | iHerb/쿠팡 파트너스 API가 제품 성분 메타데이터까지 제공한다 | PoC(V3)에서 실제 API 응답 필드 확인 | MVP 착수 전 |
| ASM-02 | 건기식 구매 전 온라인 탐색 비율 72%가 모바일 웹에서도 동일하다 | Google Analytics 채널별 유입 분석 | 출시 후 1개월 |
| ASM-03 | 식약처 공공 데이터 API의 응답 속도가 서비스 요구사항(<= 1초)에 부합한다 | API 부하 테스트 + 캐시 전략 설계 | MVP 착수 전 |
| ASM-04 | Q1-A 세그먼트(C1 한정훈)의 제휴 링크 클릭→실구매 전환율이 6% 이상이다 | MVP 출시 후 3개월간 실측 (V6) | 출시 후 3개월 |
| ASM-05 | 환율 API(한국수출입은행 등)가 안정적으로 운영되며 15분 이내 갱신된다 | SLA 확인 + 폴백 환율 소스 확보 | MVP 착수 전 |
| ASM-06 | 카카오 Link API의 현 정책(외부 딥링크 허용)이 최소 6개월 유지된다 | 카카오 개발자 문서 변경 모니터링 | 지속 |

### 1.3 Definitions, Acronyms, Abbreviations

| 용어 | 정의 |
|---|---|
| **1일 단가** | 특정 건강기능식품 제품의 1일 권장 복용량을 기준으로 산출한 원화(KRW) 단가. 배송비·관세 포함 최종가 기준 |
| **AOS (Adjusted Opportunity Score)** | `Importance × (1 - Satisfaction/5)` 공식으로 산출한 기회 점수. 높을수록 미충족 니즈가 크며 혁신 기회가 높음 |
| **DOS (Discovered Opportunity Score)** | `AOS × Market Relevance`로 산출한 시장 연관 기회 점수 |
| **JTBD (Jobs to be Done)** | 사용자가 특정 상황에서 완수하려는 과업(Job)을 정의하는 프레임워크 |
| **CJM (Customer Journey Map)** | 고객 여정 지도. 소비자가 제품/서비스를 인지→고려→결정→사용하는 전 과정을 시각화한 도구 |
| **MoSCoW** | 우선순위 분류 체계. Must Have / Should Have / Could Have / Won't Have |
| **Anti-BS Dashboard** | 마케팅 콘텐츠(광고성 리뷰, 체험단 블로그 등)를 원천 차단하고, 식약처 인정 원료 DB와 의학 논문에 기반한 팩트체크 결과만 표시하는 대시보드 |
| **Super-Calc Engine** | iHerb/쿠팡/네이버 등 다채널의 건기식 가격을 1일 복용량 기준 원화 단가로 자동 환산·정렬하는 핵심 연산 엔진 |
| **Viral Engine** | 비교 결과를 카카오톡 공유 카드로 1-Tap 생성·전송하는 기능 모듈 |
| **Data Trust System** | 원본 라벨 아카이브, 출처 투명 공개, 오류 제보 및 보상 시스템의 총칭 |
| **뱃지 (Badge)** | 식약처 건강기능식품공전 기반으로 성분/원료의 인정 상태를 시각적으로 표현하는 라벨 (APPROVED / CAUTION / NOT_APPROVED) |
| **OG 이미지 (Open Graph Image)** | 소셜 미디어 공유 시 미리보기에 표시되는 대표 이미지 |
| **CPA (Cost Per Action)** | 제휴 마케팅에서 사용자의 특정 행동(구매 등) 발생 시 지급되는 수수료 방식 |
| **TTC (Time-To-Completion)** | 탐색 시작 후 결제 링크 클릭 또는 SNS 공유 완료까지의 소요 시간. North Star KPI |
| **LCP (Largest Contentful Paint)** | 웹 성능 지표. 페이지의 가장 큰 콘텐츠 요소가 렌더링 완료되는 시점 |
| **K-Factor** | 바이럴 계수. 기존 사용자 1명이 유입시키는 신규 사용자 수 |
| **p95** | 전체 요청 중 95번째 백분위 수의 응답 시간. 상위 5%를 제외한 최대 응답 시간 |
| **RPO (Recovery Point Objective)** | 장애 발생 시 허용 가능한 최대 데이터 손실 시간 |
| **RTO (Recovery Time Objective)** | 장애 발생 후 서비스 복구까지의 최대 허용 시간 |
| **SLA (Service Level Agreement)** | 서비스 수준 협약. 서비스 가용성·응답 시간 등의 보장 기준 |
| **RBAC (Role-Based Access Control)** | 역할 기반 접근 제어 |
| **MFDS** | 식품의약품안전처 (Ministry of Food and Drug Safety) |
| **Persona** | 사용자 유형을 대표하는 가상의 인물 모델 |
| **Validator** | 가설이나 가정을 검증하기 위한 실험 또는 측정 방법 |
| **SKU (Stock Keeping Unit)** | 재고 관리 단위. 개별 제품을 식별하는 고유 코드 |

### 1.4 References

| ID | 제목 | 유형 | 경로/출처 |
|---|---|---|---|
| REF-01 | VPS Merged V2 (fin) — Value Proposition Sheet | 내부 문서 | `../04_VPS-final/06_value-proposition-sheet_260410(fin).md` |
| REF-02 | Porter's 5 Forces 시장 환경 분석 | 내부 문서 | `01_Biz-Analysis/1_porters-foreces.md` |
| REF-03 | 3개 도메인 경쟁사 분석 | 내부 문서 | `01_Biz-Analysis/2_competents-analysis.md` |
| REF-04 | Value Chain + KSF 분석 | 내부 문서 | `01_Biz-Analysis/3_value-chain.md`, `4_ksf-report.md` |
| REF-05 | TAM-SAM-SOM + 시장 세분화 | 내부 문서 | `01_Biz-Analysis/6_TAM-SAM-SOM+MarketSegment.md` |
| REF-06 | Persona Spectrum Map | 내부 문서 | `01_Biz-Analysis/7_persona-spectrum-map.md` |
| REF-07 | Customer Journey Map | 내부 문서 | `01_Biz-Analysis/8_customer-journey-map.md` |
| REF-08 | AOS-DOS Combined Analysis | 내부 문서 | `01_Biz-Analysis/9_aos-dos-analysis.md` |
| REF-09 | JTBD Interview Report | 내부 문서 | `01_Biz-Analysis/10_jtbd-interview-report.md` |
| REF-10 | 한국소비자원 시장 조사 (2022) | 외부 공개 보고서 | 한국소비자원 공식 발간 |
| REF-11 | 식약처 소비자 인식조사 (2021) | 외부 공개 보고서 | 식품의약품안전처 공식 발간 |
| REF-12 | 공정거래위원회 온라인 광고 실태조사 (2023) | 외부 공개 보고서 | 공정거래위원회 공식 발간 |
| REF-13 | 한국건강기능식품협회 소비자 실태 보고서 (2023) | 외부 공개 보고서 | 한국건강기능식품협회 공식 발간 |
| REF-14 | Nielsen Korea 건기식 구매 행태 분석 (2022) | 외부 공개 보고서 | Nielsen Korea 발간 |
| REF-15 | ISO/IEC/IEEE 29148:2018 | 국제 표준 | ISO 표준 문서 |
| REF-16 | PRD v0.1 — 건기식 성분·가격 비교 초자동화 플랫폼 | 내부 문서 | `PRD_v0.1.md` |

---

## 2. Stakeholders

| 역할 (Role) | 대표 페르소나 | 책임 (Responsibility) | 관심사 (Interest) |
|---|---|---|---|
| **가성비 최적화 직구족** | C1 한정훈 (36세, 개발자) | 다채널 가격 비교의 주 사용자, 수익 엔진의 55%를 차지하는 핵심 전환 사용자 | 5초 내 실시간 최저가 확인, 1일 단가 기준 정렬, 배송비·관세 포함 최종가 정확도 |
| **건강 계기 진입자** | C2 박소연 (43세, 인사팀 과장) | 건강 검진 후 첫 건기식 구매를 시도하는 입문자, 성장 엔진 역할 | 광고 배제 환경에서 30분 내 확신 있는 결정, 전문 용어 일상어 번역 |
| **트렌드 추종 탐색자** | A2 정수빈 (27세, 뷰티 마케터) | 트렌드 성분의 과학적 근거를 확인·공유하는 SEO 트래픽 유입 사용자 | 5초 팩트체크 + 1탭 카카오톡 공유, 비주얼 공유 카드 |
| **극단 — 신뢰 실패자** | E2 김도현 (29세, 데이터 분석가) | 기존 비교 앱 데이터 오류 경험자, 데이터 정확도 SLA 기준을 설정하는 역할 | 출처 2클릭 내 추적, 오류 제보 시 48h 내 수정 보장, 보상 체계 |
| **Product & Engineering팀** | (내부) | 시스템 설계, 개발, 배포, 운영 | 기술적 구현 가능성, 인프라 비용 200만 원/월 이하, 성능 SLA 충족 |
| **QA팀** | (내부) | 요구사항 검증, 테스트 수행, 금지 표현 검수 | 테스트 가능한 인수 기준, 뱃지 정확도, 데이터 정합성 |
| **법률/컴플라이언스** | (내부) | 건강기능식품법 준수 확인, 금지 표현 목록 관리 | 법적 리스크 최소화, 식약처 공전 원문 1:1 매칭 |
| **사업팀** | (내부) | 제휴 수수료 구조 관리, 수익 모델 운영 | CPA 월 수익 >= 500만 원 (Y1 말), 제휴 채널 다변화 |

> **배제 타겟 (Non-Stakeholder):**
> - **E1 나경아** — 디지털 소외 계층, 카카오톡 간접 접근만 허용
> - **N1 조미라 (58세, 경리)** — 브랜드 맹신 성향, DOS -0.60, 전환 유인 0%. MVP 마케팅/기획 자원 투입 **전면 금지**

---

## 3. System Context and Interfaces

### 3.1 External Systems

| # | 외부 시스템 | 유형 | 연동 목적 | 주요 제약 |
|---|---|---|---|---|
| EXT-01 | iHerb Affiliate API | REST API | 제품 가격(USD), 재고, 성분표, 딥링크 URL 조회 | Rate Limit: V3 PoC에서 확인. 수수료: 신규 10% / 기존 5% |
| EXT-02 | 쿠팡 파트너스 API | REST API | 가격(KRW), 딥링크 URL, 제품 메타데이터 조회 | Rate Limit: 일 10,000건 (추정). 수수료: 3% |
| EXT-03 | 식약처 건강기능식품 공공 데이터 API | 공공 API | 기능성 인정 내용, 일일 섭취량, 주의사항 조회 | 갱신 주기: 월 1회 |
| EXT-04 | 환율 API (한국수출입은행 등) | REST API | 매매기준율 조회 (USD/KRW 등) | 갱신 주기: 15분 |
| EXT-05 | 카카오 Link API | JS SDK | 카카오톡 공유 카드 생성 및 전송 | 일 발송 제한 확인 필요 |

### 3.2 Client Applications

| # | 클라이언트 | 플랫폼 | 비고 |
|---|---|---|---|
| CL-01 | 모바일 웹 앱 | iOS/Android 모바일 브라우저 | MVP 1차 타겟. 반응형 웹 |
| CL-02 | 카카오 내장 브라우저 웹뷰 | 카카오톡 인앱 | 공유 카드 수신자 랜딩 환경 |

### 3.3 API Overview

#### 3.3.1 내부 API

| API 명 | 유형 | 엔드포인트 패턴 | 입력 | 출력 | 성능 기준 |
|---|---|---|---|---|---|
| Super-Calc API | REST | `POST /api/v1/calc/daily-cost` | 성분명, 채널 리스트, 환율 | PRICE_SNAPSHOT 배열 (1일 단가 정렬) | p95 <= 2,000ms |
| Badge API | REST | `GET /api/v1/badges/{ingredient_id}` | ingredient_id | BADGE 배열 | 캐시 TTL: 24h |
| Error Report API | REST | `POST /api/v1/reports` | 제품ID, 필드명, 신고값, 정정값 | 접수 확인 + 예상 처리 시간 | 접수 응답 <= 3초 |
| Label Archive API | REST | `GET /api/v1/labels/{product_id}` | product_id | 라벨 이미지 URL 배열 | 아코디언 렌더 <= 500ms |
| Share Card API | REST | `POST /api/v1/share/kakao` | 비교 결과 데이터 | OG 이미지 포함 공유 카드 | 생성 시간 <= 1,500ms |
| Product Search API | REST | `GET /api/v1/products/search` | 키워드, 성분명 | 제품 목록 | p95 <= 1,000ms |

#### 3.3.2 외부 API

| API 명 | 유형 | 입력 | 출력 | 제약 사항 |
|---|---|---|---|---|
| iHerb Affiliate API | 외부 REST | 제품 ID, 키워드 | 가격(USD), 재고, 성분표, 딥링크 URL | Rate Limit TBD (V3 PoC에서 확인), 수수료: 신규 10% / 기존 5% |
| 쿠팡 파트너스 API | 외부 REST | 키워드, 카테고리 | 가격(KRW), 딥링크 URL, 제품 메타 | Rate Limit: 일 10,000건 (추정), 수수료: 3% |
| 식약처 건강기능식품 공공 데이터 | 외부 공공 API | 원료명, 인증번호 | 기능성 인정 내용, 일일 섭취량, 주의사항 | 갱신 주기: 월 1회 |
| 환율 API | 외부 REST | 통화 코드 | 매매기준율 | 갱신 주기: 15분 |
| 카카오 Link API | 외부 JS SDK | 컨텐츠(OG image, title, desc), 딥링크 | 카카오톡 공유 메시지 | 일 발송 제한 확인 필요 |

### 3.4 Interaction Sequences (핵심 시퀀스 다이어그램)

#### 3.4.1 핵심 흐름 1: 실시간 1일 단가 비교 (F1)

```mermaid
sequenceDiagram
    actor User as 사용자 (C1)
    participant Web as 모바일 웹 앱
    participant Calc as Super-Calc API
    participant iHerb as iHerb API
    participant Coupang as 쿠팡 API
    participant FX as 환율 API

    User->>Web: 성분명 검색 (예: 비타민D 1000IU)
    Web->>Calc: POST /api/v1/calc/daily-cost
    
    par 3채널 병렬 조회
        Calc->>iHerb: 제품 가격 조회 (USD)
        Calc->>Coupang: 제품 가격 조회 (KRW)
        Calc->>FX: 환율 조회 (USD/KRW)
    end

    iHerb-->>Calc: 가격, 성분표, 딥링크
    Coupang-->>Calc: 가격, 딥링크
    FX-->>Calc: 매매기준율

    Calc->>Calc: 1일 복용량 기준 원화 단가 산출
    Calc->>Calc: 배송비 + 관세 포함 최종가 계산
    Calc->>Calc: 단가 정렬 (최저가 순)

    Calc-->>Web: PRICE_SNAPSHOT 배열 (정렬 완료)
    Web-->>User: 1일 단가 랭킹 + 환율 기준 시각 표시

    Note over User,Web: 응답 시간 p95 <= 2초
```

#### 3.4.2 핵심 흐름 2: 팩트체크 뱃지 조회 (F2)

```mermaid
sequenceDiagram
    actor User as 사용자 (C2/A2)
    participant Web as 모바일 웹 앱
    participant Badge as Badge API
    participant MFDS as 식약처 공공 API
    participant Cache as 캐시 (TTL 24h)

    User->>Web: 제품 상세 페이지 진입
    Web->>Badge: GET /api/v1/badges/{ingredient_id}
    
    Badge->>Cache: 캐시 확인
    
    alt 캐시 존재 (TTL 24h 이내)
        Cache-->>Badge: 캐시된 뱃지 데이터
    else 캐시 미스
        Badge->>MFDS: 기능성 인정 원료 조회
        MFDS-->>Badge: 인정 내용, 일일 섭취량
        Badge->>Badge: APPROVED/CAUTION/NOT_APPROVED 판정
        Badge->>Cache: 결과 캐싱 (TTL 24h)
    end

    Badge-->>Web: BADGE 배열 + 일상어 번역
    Web-->>User: 뱃지 표시 + 전문 용어 괄호 번역
    
    Note over Web: 마케팅 콘텐츠 0건 보장
    Note over User,Web: 뱃지 로드 시간 p95 <= 1초
```

#### 3.4.3 핵심 흐름 3: 1-Tap 카카오톡 공유 (F3)

```mermaid
sequenceDiagram
    actor User as 공유자 (A2)
    participant Web as 모바일 웹 앱
    participant Share as Share Card API
    participant Kakao as 카카오 Link API
    actor Receiver as 수신자

    User->>Web: "카톡 공유" 버튼 탭
    Web->>Share: POST /api/v1/share/kakao
    Share->>Share: OG 이미지 동적 생성 (단가, 뱃지, 성분 비교)
    Share-->>Web: 공유 카드 데이터 (OG image URL, title, desc)
    
    Web->>Kakao: 카카오 Link API 호출
    
    alt 카카오 API 정상 응답
        Kakao-->>Web: 공유 완료
        Web-->>User: 전송 완료 알림
    else 카카오 API 장애
        Web-->>User: "URL 복사" 폴백 UI 표시
        Web-->>User: 네이버 밴드/문자 대체 공유 버튼 노출
    end

    Note over User,Web: 카드 생성 p95 <= 1.5초

    Receiver->>Web: 카카오 내장 브라우저에서 카드 탭
    Web-->>Receiver: 비교 결과 웹뷰 로드 (앱 설치/가입 불요)
    Receiver->>Web: "최저가 구매하기" 탭
    Web-->>Receiver: 제휴 커머스 딥링크 이동

    Note over Receiver,Web: 랜딩 성공률 >= 98%, 로드 시간 <= 2초
```

#### 3.4.4 핵심 흐름 4: 오류 제보 및 출처 확인 (F4)

```mermaid
sequenceDiagram
    actor User as 사용자 (E2)
    participant Web as 모바일 웹 앱
    participant Label as Label Archive API
    participant Report as Error Report API
    participant Admin as 운영팀

    User->>Web: 제품 성분 데이터 확인
    User->>Web: "[출처 확인]" 버튼 탭
    Web->>Label: GET /api/v1/labels/{product_id}
    Label-->>Web: 원본 라벨 이미지 + 출처 정보
    Web-->>User: 아코디언 메뉴 펼침 (식약처 DB 링크, 라벨 이미지, 논문 DOI)
    
    Note over User,Web: 아코디언 렌더 <= 500ms, 출처 도달 <= 2클릭

    User->>Web: "[오류 신고]" 버튼 탭 + 내용 제출
    
    alt 유효 제보
        Web->>Report: POST /api/v1/reports
        Report->>Report: 스팸/중복 필터링 통과
        Report-->>Web: 접수 확인 + 예상 처리 시간
        Web-->>User: 접수 완료 알림
        Report->>Admin: 제보 접수 알림
        Admin->>Report: 검증 + 수정 완료 (48h SLA)
        Report-->>User: 푸시 알림 + 리워드 지급
    else 스팸/중복 제보
        Report-->>Web: 차단 사유
        Web-->>User: "중복 또는 불완전한 제보입니다" 안내
    end

    Note over Admin,Report: 오류 수정 완료 <= 48시간
```

---

## 4. Specific Requirements

### 4.1 Functional Requirements

#### 4.1.1 F1: 실시간 1일 단가 정규화 엔진 (Super-Calc Engine)

| ID | 요구사항 | Source | Priority | Acceptance Criteria |
|---|---|---|---|---|
| **REQ-FUNC-001** | 시스템은 사용자가 특정 영양소(예: 비타민D 1000IU)를 검색하면, iHerb/쿠팡/네이버 3개 채널에서 해당 제품의 가격 데이터를 병렬로 조회한다. | Story 1, F1 | Must | **Given** 사용자가 특정 영양소를 검색한 상태, **When** "1일 단가 비교" 버튼을 탭하면, **Then** 등록된 3개 채널에 대해 병렬 API 조회가 실행된다. |
| **REQ-FUNC-002** | 시스템은 각 채널의 제품 가격을 1일 권장 복용량 기준 원화(KRW) 단가로 자동 환산한다. | Story 1 AC1, F1 | Must | **Given** 3개 채널의 원시 가격 데이터가 수집된 상태, **When** 단가 환산 처리가 실행되면, **Then** 모든 제품의 가격이 "1일 복용량 기준 원화 단가"로 통일 환산되어 PRICE_SNAPSHOT으로 저장된다. |
| **REQ-FUNC-003** | 시스템은 환산된 1일 단가를 최저가 순으로 정렬하여 사용자에게 표시한다. | Story 1 AC1, F1 | Must | **Given** 1일 단가 환산이 완료된 상태, **When** 결과 화면이 로드되면, **Then** 모든 등록 채널의 1일 복용 기준 원화 단가가 최저가 순으로 정렬되어 표시된다. 응답 시간 p95 <= 2초, 실패율 < 1.0%. |
| **REQ-FUNC-004** | 시스템은 iHerb(USD) 등 해외 채널 제품의 가격에 실시간 환율을 적용하고, 적용된 환율과 "환율 기준 시각"을 명시적으로 표시한다. | Story 1 AC2, F1 | Must | **Given** 사용자가 iHerb(USD) 제품과 쿠팡(KRW) 제품을 동시 비교할 때, **When** 결과 화면이 로드되면, **Then** 각 가격에 적용된 환율과 "환율 기준 시각"이 표시된다. 환율 갱신 주기 <= 15분, 표시 정확도 +/- 0.5% 이내. |
| **REQ-FUNC-005** | 시스템은 배송비·관세·할인코드를 포함한 "실지불가(최종가)"를 별도 컬럼으로 제공한다. | Story 1 AC3, F1 | Must | **Given** 배송비/관세/할인코드가 적용 가능한 제품이 있을 때, **When** 단가 랭킹이 표시되면, **Then** "실지불가(배송비+관세 포함 최종가)"가 별도 컬럼으로 제공된다. 최종가 오차율 <= 3%. |
| **REQ-FUNC-006** | 시스템은 오류가 신고된 최종가 데이터를 신고 접수 후 48시간 이내에 수정 완료한다. | Story 1 AC3, F1 | Must | **Given** 사용자가 최종가 오류를 신고한 상태, **When** 신고 접수 후 48시간이 경과하면, **Then** 해당 오류가 수정 완료되어 있다. |
| **REQ-FUNC-007** | 등록된 채널 중 1개 이상이 장애(타임아웃, 5xx) 상태일 때, 시스템은 정상 채널의 결과만 표시하고, 장애 채널에 대해 인라인 안내 메시지를 표시한다. | Story 1 AC4 (Sad Path), F1 | Must | **Given** 등록된 3개 채널 중 1개가 타임아웃 또는 5xx 오류를 반환하는 상태, **When** 사용자가 "1일 단가 비교"를 요청하면, **Then** 정상 응답 채널의 결과만 표시되고, 장애 채널에는 "현재 [채널명] 가격을 가져올 수 없습니다. 마지막 수집: HH:MM" 안내가 인라인으로 표시된다. 부분 결과 제공 성공률 >= 99%, 장애 안내 표시까지 <= 500ms. |
| **REQ-FUNC-008** | 사용자가 DB에 미등록된 성분을 검색한 경우, 시스템은 "해당 성분은 현재 데이터베이스에 미등록 상태입니다" 안내와 "[제품 등록 요청하기]" CTA 버튼을 표시한다. | Story 1 AC5 (Sad Path), F1 | Must | **Given** 사용자가 DB에 미등록된 성분(예: "NMN")을 검색한 상태, **When** 검색 결과 화면이 로드되면, **Then** 안내 메시지와 "[제품 등록 요청하기]" 버튼이 표시된다. CTA 노출까지 <= 300ms, 등록 요청 제출 성공률 >= 99%. |

#### 4.1.2 F2: 식약처/논문 등급 배지 시스템 (Anti-BS Dashboard)

| ID | 요구사항 | Source | Priority | Acceptance Criteria |
|---|---|---|---|---|
| **REQ-FUNC-009** | 시스템은 제품 상세 페이지에서 제휴 광고 배너, 유저 리뷰, 별점, 체험단 블로그 링크를 0건 표시한다 (마케팅 콘텐츠 원천 차단). | Story 2 AC1, F2 | Must | **Given** 사용자가 특정 제품 상세 페이지에 진입한 상태, **When** 페이지가 로드되면, **Then** 제휴 광고 배너, 유저 리뷰, 별점, 체험단 블로그 링크가 0건 표시된다. 마케팅 콘텐츠 노출 = 0건/페이지. |
| **REQ-FUNC-010** | 시스템은 식약처 건강기능식품공전에 등재된 기능성 인정 원료에 대해 APPROVED/CAUTION/NOT_APPROVED 뱃지를 식약처 공전 원문과 1:1 매칭하여 표시한다. | Story 2 AC2, F2 | Must | **Given** 식약처 건강기능식품공전에 등재된 기능성 인정 원료가 포함된 제품, **When** 뱃지 영역이 렌더링되면, **Then** APPROVED/CAUTION/NOT_APPROVED 뱃지가 식약처 공전 원문과 1:1 매칭되어 표시된다. 뱃지-원문 불일치율 < 0.5%, 뱃지 로드 시간 <= 1초. |
| **REQ-FUNC-011** | 시스템은 뱃지 텍스트에 질병 예방/치료를 암시하는 표현을 포함하지 않으며, 식약처 공전 고시 문구만 래핑한다. | F2, CON-01 | Must | **Given** 뱃지가 생성·표시되는 모든 경우, **When** 뱃지 텍스트를 금지 표현 목록과 대조하면, **Then** 금지 표현 매칭 건수가 0건이다. |
| **REQ-FUNC-012** | 시스템은 성분표에서 전문 용어(예: "콜레칼시페롤") 옆에 일상어 번역(예: "몸에 잘 흡수되는 비타민 D3")을 괄호 형태로 표시한다. | Story 2 AC3, F2 | Must | **Given** "콜레칼시페롤", "피리독신 염산염" 등 전문 용어가 포함된 제품, **When** 성분표가 표시되면, **Then** 전문 용어 옆에 일상어 번역이 괄호 형태로 표시된다. 번역 커버리지 >= 95% (식약처 등록 기능성 원료 기준), 번역 정확도 >= 98%. |
| **REQ-FUNC-013** | 시스템은 식약처 건강기능식품공전에 미등재된 원료에 대해 "식약처 미등재 원료 — 기능성 인정 정보 없음"을 별도 색상(회색)으로 표시하고, 뱃지 미부여 사유를 툴팁으로 제공한다. | Story 2 AC5 (Sad Path), F2 | Must | **Given** 제품에 포함된 성분이 식약처 공전에 미등재된 상태, **When** 뱃지 영역이 렌더링되면, **Then** 해당 성분에는 "식약처 미등재 원료 — 기능성 인정 정보 없음"이 회색으로 표시되며, 뱃지 미부여 사유가 툴팁으로 제공된다. 미등재 원료 식별 정확도 >= 99%, 미매칭 시 뱃지 오발급률 = 0%. |

#### 4.1.3 F3: 1-Tap 팩트 요약 카톡 공유 (Viral Engine)

| ID | 요구사항 | Source | Priority | Acceptance Criteria |
|---|---|---|---|---|
| **REQ-FUNC-014** | 시스템은 사용자가 "카톡 공유" 버튼을 탭하면, 비교 결과의 핵심 지표(1일 단가, 뱃지, 성분 비교)가 담긴 OG 이미지 포함 카카오톡 공유 카드를 자동 생성한다. | Story 3 AC1, F3 | Must | **Given** 사용자가 제품 비교 결과 화면에 있을 때, **When** "카톡 공유" 버튼을 탭하면, **Then** OG 이미지가 포함된 카카오톡 공유 카드가 생성되어 전송 준비 상태에 들어간다. 카드 생성 시간 <= 1.5초, 실패율 < 1%. |
| **REQ-FUNC-015** | 시스템은 카카오톡 공유 카드 수신자가 카드를 탭했을 때, 카카오 내장 브라우저에서 앱 설치/회원가입 요구 없이 비교 결과 웹뷰를 바로 로드한다. | Story 3 AC2, F3 | Must | **Given** 수신자가 카카오톡에서 공유 카드를 탭한 상태, **When** 링크가 열리면, **Then** 카카오 내장 브라우저에서 앱 설치/회원가입 요구 없이 비교 결과 웹뷰가 바로 로드된다. 랜딩 성공률 >= 98%, 페이지 로드 시간 <= 2초. |
| **REQ-FUNC-016** | 시스템은 공유 카드 랜딩 페이지에서 "최저가 구매하기" 버튼 탭 시 해당 제품의 제휴 커머스 결제 페이지로 딥링크 이동시킨다. | Story 3 AC3, F3 | Must | **Given** 수신자가 공유 카드 랜딩 페이지에 도착한 상태, **When** "최저가 구매하기" 버튼을 탭하면, **Then** 해당 제품의 제휴 커머스 결제 페이지로 딥링크 이동한다. |
| **REQ-FUNC-017** | 카카오 Link API가 장애 상태일 때, 시스템은 자동으로 "URL 복사" 폴백 UI를 표시하고, 복사 완료 시 토스트 알림과 대체 공유 버튼(네이버 밴드, 문자)을 노출한다. | Story 3 AC4 (Sad Path), F3 | Must | **Given** 카카오 Link API가 타임아웃 또는 오류를 반환하는 상태, **When** 사용자가 "카톡 공유" 버튼을 탭하면, **Then** "URL 복사" 폴백 UI가 자동 표시되고, 복사 완료 시 토스트 알림("링크가 복사되었습니다")이 뜨며, 네이버 밴드/문자 대체 공유 버튼이 노출된다. 폴백 전환 시간 <= 1초, 폴백 경로 공유 성공률 >= 95%. |

#### 4.1.4 F4: 라벨 원본 아카이브 + 오류 제보 (Data Trust System)

| ID | 요구사항 | Source | Priority | Acceptance Criteria |
|---|---|---|---|---|
| **REQ-FUNC-018** | 시스템은 사용자가 "[출처 확인]" 버튼을 탭하면, 해당 데이터의 원천(식약처 DB 링크, 제조사 라벨 이미지, 논문 DOI)을 아코디언 메뉴로 펼쳐 표시한다. | Story 4 AC1, F4 | Must | **Given** 사용자가 특정 제품의 성분 데이터를 확인하는 상태, **When** "[출처 확인]" 버튼을 탭하면, **Then** 해당 데이터의 원천이 아코디언 메뉴로 펼쳐진다. 출처 도달 <= 2클릭, 아코디언 렌더 <= 500ms. |
| **REQ-FUNC-019** | 시스템은 사용자가 "[오류 신고]" 버튼을 탭하고 내용을 제출하면, 제보 접수 확인 알림과 예상 처리 시간을 즉시 표시한다. | Story 4 AC2, F4 | Must | **Given** 사용자가 데이터 불일치를 발견한 상태, **When** "[오류 신고]" 버튼을 탭하고 내용을 제출하면, **Then** 제보 접수 확인 알림과 예상 처리 시간이 즉시 표시된다. 제보 접수 응답 <= 3초. |
| **REQ-FUNC-020** | 시스템은 접수된 오류 제보를 검증하고 48시간 이내에 수정 완료한다. | Story 4 AC2, F4 | Must | **Given** 유효한 오류 제보가 접수된 상태, **When** 48시간이 경과하면, **Then** 해당 오류가 검증 및 수정 완료되어 있다. 오류 수정 완료 <= 48시간. |
| **REQ-FUNC-021** | 시스템은 제보한 오류가 검증·수정 완료되면, 제보자에게 푸시 알림("귀하의 제보로 수정되었습니다")과 리워드(포인트/배지)를 지급한다. | Story 4 AC3, F4 | Must | **Given** 제보한 오류가 검증/수정 완료된 상태, **When** 수정이 반영되면, **Then** 제보자에게 푸시 알림과 리워드가 지급된다. 수정 후 알림 발송 <= 1시간, 제보자 만족도 >= 4.0/5점. |
| **REQ-FUNC-022** | 시스템은 동일 제품에 대해 24시간 내 5건 이상 반복 제보하거나 빈 문자열 제보를 차단하고, 기존 유효 제보에는 영향을 주지 않는다. | Story 4 AC4 (Sad Path), F4 | Must | **Given** 사용자가 동일 제품에 대해 24시간 내 5건 이상 반복 제보하거나 빈 문자열 제보를 시도하는 상태, **When** "[오류 신고]" 제출을 시도하면, **Then** "중복 또는 불완전한 제보입니다. 내용을 확인해 주세요." 안내와 함께 제출이 차단된다. 스팸 제보 차단 정확도 >= 95%, 유효 제보 오차단률(false positive) <= 2%. |

#### 4.1.5 F7~F12: Should Have / Could Have / Won't Have 기능

| ID | 요구사항 | Source | Priority | 비고 |
|---|---|---|---|---|
| **REQ-FUNC-023** | 시스템은 사용자의 비교 이력을 저장하고, 재방문 시 이전 비교 결과로의 연속성을 제공한다. | F7 | Should | 출시 후 3개월 내 구현 |
| **REQ-FUNC-024** | 시스템은 트렌드 성분에 대한 팩트체크 콘텐츠를 SEO에 최적화된 형태로 제공한다. | F8 | Should | 출시 후 3개월 내 구현 |
| **REQ-FUNC-025** | 시스템은 사용자가 관심 등록한 제품의 가격이 하락하면 Push 알림을 발송한다. | F9 | Should | 출시 후 3개월 내 구현 |
| **REQ-FUNC-026** | 시스템은 3탭 이내에 비교 결론에 도달할 수 있는 UX를 제공한다. | F10 | Could | Phase 2 |
| **REQ-FUNC-027** | 시스템은 B2B 마켓 인텔리전스 대시보드를 제공한다 (가격 동향, 소비자 WTP 분석 등). | F11 | Could | Phase 2 |
| **REQ-FUNC-028** | 시스템은 B2C 프리미엄 알림 구독 기능을 제공한다. | F12 | Could | Phase 2 |
| **REQ-FUNC-029** | 시스템은 유저 자율 리뷰/별점 게시판을 제공하지 않는다. | F5 | Won't | 광고 침투 위험으로 명시적 배제 |
| **REQ-FUNC-030** | 시스템은 AI 문진 기반 개인화 추천 기능을 제공하지 않는다. | F6 | Won't | 초기 데이터 편향 위험으로 명시적 배제 |

### 4.2 Non-Functional Requirements

#### 4.2.1 성능 (Performance)

| ID | 요구사항 | 측정 기준 | 측정 방법 | 근거 |
|---|---|---|---|---|
| **REQ-NF-001** | 단가 비교 API(Super-Calc)의 응답 시간은 p95 <= 2,000ms 이내여야 한다. | p95 응답 시간 <= 2,000ms | CloudWatch / Datadog APM 모니터링 | Story 1 AC1, PRD 5-1 |
| **REQ-NF-002** | 뱃지 렌더링 시간은 p95 <= 1,000ms 이내여야 한다. | p95 렌더링 시간 <= 1,000ms | 프론트엔드 성능 모니터링 | Story 2 AC2, PRD 5-1 |
| **REQ-NF-003** | 카카오 공유 카드 생성 시간은 p95 <= 1,500ms 이내여야 한다. | p95 카드 생성 시간 <= 1,500ms | Share Card API 응답 시간 로깅 | Story 3 AC1, PRD 5-1 |
| **REQ-NF-004** | 출처 아코디언 펼침 시간은 p95 <= 500ms 이내여야 한다. | p95 렌더링 시간 <= 500ms | 프론트엔드 성능 모니터링 | Story 4 AC1, PRD 5-1 |
| **REQ-NF-005** | 전체 페이지 LCP는 모바일 웹 기준 <= 2,500ms 이내여야 한다. | LCP <= 2,500ms | Lighthouse / Web Vitals 모니터링 | PRD 5-1 |
| **REQ-NF-006** | 위 모든 성능 임계치는 동시 접속 200명(피크 시 500명) 부하 조건에서 보장되어야 한다. | 동시 접속 200명 기준 p95 충족, 피크 500명 기준 p95 충족 | k6 또는 Locust 부하 테스트 (동시 접속 200/500명 시나리오, 각 15분 실행) | PRD 5-1 |
| **REQ-NF-007** | 부하 테스트는 출시 전 1회 + 월 1회 주기로 실행하고, p95/p99/에러율을 기록한다. | 출시 전 1회 + 월 1회 | k6 또는 Locust. 결과 리포트 보관 | PRD 5-1 |
| **REQ-NF-008** | 탐색 시작 후 결제 링크 클릭 또는 SNS 공유 완료까지의 소요 시간(TTC)은 p50 <= 5분이어야 시해야 한다. | TTC p50 <= 5분, p95 추적 | Mixpanel 이벤트: `search_start` → `affiliate_click` or `share_complete` | North Star KPI |
| **REQ-NF-009** | 사용자의 결정 소요 시간은 p50 <= 10분, p95 <= 30분이어야 한다. | p50 <= 10분, p95 <= 30분 | Amplitude 퍼널 분석 | Story 2 AC4 |

#### 4.2.2 가용성 및 신뢰성 (Availability & Reliability)

| ID | 요구사항 | 측정 기준 | 측정 방법 | 근거 |
|---|---|---|---|---|
| **REQ-NF-010** | 월간 서비스 가용성은 >= 99.5%이어야 한다 (월 다운타임 <= 3.6시간). | 월간 가용성 >= 99.5% | Uptime 모니터링 (예: UptimeRobot, Datadog) | PRD 5-2 |
| **REQ-NF-011** | API 오류율(5xx)은 <= 0.5% 이내여야 한다. | 5xx 비율 <= 0.5% | CloudWatch / Datadog 에러율 대시보드 | PRD 5-2 |
| **REQ-NF-012** | 성분 DB 오류율은 Phase 1에서 <= 5%, Phase 2에서 <= 2%여야 한다. | Phase 1: <= 5%, Phase 2: <= 2% | 월 1회 무작위 50건 샘플 검수 (Jira: `label:db_error`) | PRD 5-2, Desired Outcome |
| **REQ-NF-013** | 오류 제보 처리 SLA는 접수 후 수정 완료까지 <= 48시간이어야 한다. | 수정 완료 <= 48시간 | Jira SLA 보드 | PRD 5-2, Story 4 AC2 |
| **REQ-NF-014** | 데이터 백업 주기는 일 1회이며, RPO <= 24시간이어야 한다. | RPO <= 24h | 백업 스케줄 모니터링, 복구 테스트 (분기 1회) | PRD 5-2 |
| **REQ-NF-015** | 단가 비교 API의 실패율은 < 1.0%여야 한다. | 실패율 < 1.0% | API 응답 코드 로깅 | Story 1 AC1 |
| **REQ-NF-016** | 카카오톡 공유 카드 생성 실패율은 < 1%여야 한다. | 실패율 < 1% | Share Card API 에러 로깅 | Story 3 AC1 |
| **REQ-NF-017** | 채널 장애 시 부분 결과 제공 성공률은 >= 99%여야 한다. | 부분 결과 성공률 >= 99% | API 응답 로깅 및 장애 시뮬레이션 테스트 | Story 1 AC4 |

#### 4.2.3 보안 (Security)

| ID | 요구사항 | 측정 기준 | 측정 방법 | 근거 |
|---|---|---|---|---|
| **REQ-NF-018** | 전 구간 TLS 1.2 이상을 적용한다. | SSL Labs 등급 A 이상 | SSL Labs 스캔 (출시 전 1회 + 분기 1회) | PRD 5-3, CON-04 |
| **REQ-NF-019** | 사용자 데이터 수집은 최소 수집 원칙을 따르며, MVP에서는 이메일·비교 이력만 수집한다. | 수집 항목: 이메일, 비교 이력만 | 개인정보 수집 항목 감사 (출시 전 + 분기 1회) | PRD 5-3, CON-06 |
| **REQ-NF-020** | B2B 데이터 익명화 시 k-anonymity >= 5를 보장한다. | k-anonymity >= 5 | 익명화 파이프라인 단위 테스트 | PRD 5-3, CON-05 |
| **REQ-NF-021** | 뱃지 텍스트는 식약처 건강기능식품공전 고시 문구만 래핑하며, 질병 예방/치료 표현을 절대 포함하지 않는다. | 금지 표현 매칭 = 0건 | QA 체크리스트 "금지 표현 목록" 검수 (릴리스 전 매회) | PRD 5-3, CON-01 |

#### 4.2.4 비용 (Cost)

| ID | 요구사항 | 측정 기준 | 측정 방법 | 근거 |
|---|---|---|---|---|
| **REQ-NF-022** | MVP 월 인프라 비용은 <= 200만 원이어야 한다 (에셋 라이트 원칙). | 월 비용 <= 200만 원 | AWS Cost Explorer 월간 리포트 (태그: `project=supercalc-mvp`). 초과 시 Slack `#infra-cost` 자동 알림. 매월 1일 경영진 공유 | PRD 5-3, CON-03 |

#### 4.2.5 운영/모니터링 (Operations & Monitoring)

| ID | 요구사항 | 측정 기준 | 측정 방법 | 근거 |
|---|---|---|---|---|
| **REQ-NF-023** | API 응답 시간, 에러 코드, 크롤링 성공/실패 로그를 수집하고, p95 > 3초 또는 5xx > 1% 시 Slack 알림을 발송한다. | p95 > 3s 또는 5xx > 1% 시 알림 | CloudWatch / Datadog → Slack 알림 | PRD 5-4 |
| **REQ-NF-024** | 일간 MAU, 퍼널 전환율, CTR, K-Factor, DB 오류율 대시보드를 제공하고, 주간 리뷰한다. | 대시보드 갱신 주기: 일간, 리뷰 주기: 주간 | Mixpanel / Amplitude 대시보드 | PRD 5-4 |
| **REQ-NF-025** | 환율 API 장애, 제휴 API 연결 끊김, SLA 48h 초과 제보 발생 시 즉시 알림(Tier 1 호출)을 발송한다. | 알림 발송 지연 <= 1분 | PagerDuty 즉시 알림 | PRD 5-4 |
| **REQ-NF-026** | 성분 DB 오류 제보가 일 5건 초과 시 긴급 대응 프로세 가동한다. | 일 5건 초과 시 긴급 대응 | Notion/Jira 보드 자동 알림 | PRD 5-4 |

#### 4.2.6 확장성 (Scalability)

| ID | 요구사항 | 측정 기준 | 측정 방법 | 근거 |
|---|---|---|---|---|
| **REQ-NF-027** | 시스템은 MVP MAU 2,200명에서 시작하여, 트래픽 증가에 따라 수평 확장(horizontal scaling)이 가능한 아키텍처를 채택한다. | 동시 접속 200명 → 500명 전환 시 p95 SLA 유지 | 부하 테스트 (피크 500명 시나리오) | PRD 5-1 부하 기준 |
| **REQ-NF-028** | 제품 DB는 초기 300~500개 SKU에서 시작하여, 알고리즘과 인프라 변경 없이 5,000개 SKU까지 확장 가능해야 한다. | 5,000 SKU 조건에서 REQ-NF-001 SLA 유지 | DB 스키마 리뷰 + 5,000개 시드 데이터 부하 테스트 | MVP In-Scope (300~500개) + 확장 계획 |

#### 4.2.7 유지보수성 (Maintainability)

| ID | 요구사항 | 측정 기준 | 측정 방법 | 근거 |
|---|---|---|---|---|
| **REQ-NF-029** | 식약처 공공 데이터 API 갱신(월 1회) 시, 뱃지 DB 동기화가 자동으로 수행되며 수동 개입 없이 24시간 이내에 반영된다. | 동기화 완료 <= 24시간, 수동 개입 0건 | 동기화 스케줄러 로그 모니터링 | EXT-03 갱신 주기: 월 1회 |
| **REQ-NF-030** | 신규 채널(예: 아마존) 추가 시, 기존 Super-Calc Engine 코어 로직 변경 없이 채널 어댑터만 추가하여 통합 가능한 구조여야 한다. | 신규 채널 통합 시 코어 코드 변경 0줄 | 코드 리뷰 + 아키텍처 문서 검증 | F1 3채널 이상 병렬 조회 구조 |

#### 4.2.8 KPI 관련 비기능 요구사항

| ID | 요구사항 | 측정 기준 | 측정 방법 | 근거 |
|---|---|---|---|---|
| **REQ-NF-031** | SEO 오가닉 월 방문자 수는 출시 후 6개월 이내에 >= 10,000명에 도달해야 한다. | 월 방문자 >= 10,000명 | Google Search Console `clicks` 합산 | 보조 KPI (획득) |
| **REQ-NF-032** | 메인→단가 산출/뱃지 화면 퍼널 전환율은 >= 60%여야 한다. | 전환율 >= 60% | Mixpanel Funnel: `page_view(main)` → `calc_result_view` | 보조 KPI (활성) |
| **REQ-NF-033** | 제휴사 구매 링크 CTR은 >= 15%여야 한다. | CTR >= 15% | Mixpanel: `affiliate_link_click` / `calc_result_view` × 100 | 보조 KPI (전환) |
| **REQ-NF-034** | 카카오톡 공유 카드 발송율(K-Factor)은 >= 10% (K >= 1.1)이어야 한다. | K-Factor >= 1.1 | Mixpanel: `kakao_share_send` / `session_start` × 100 | 보조 KPI (바이럴) |
| **REQ-NF-035** | D30~D60 재방문율은 >= 20%여야 한다. | D30~D60 재방문율 >= 20% | Amplitude Retention: `session_start` 코호트 D30/D60 셀 | 보조 KPI (유지) |
| **REQ-NF-036** | 제휴 CPA 월 수익은 Y1 말 기준 >= 500만 원이어야 한다. | 월 수익 >= 500만 원 | iHerb 파트너스 대시보드 + 쿠팡 파트너스 정산 합산 | 보조 KPI (매출) |

---

## 5. Traceability Matrix

### 5.1 Story ↔ Requirement ID ↔ Test Case ID

| Story | Requirement ID | Test Case ID | Priority |
|---|---|---|---|
| Story 1 (실시간 채널 단가 비교) AC1 | REQ-FUNC-001, REQ-FUNC-002, REQ-FUNC-003 | TC-FUNC-001, TC-FUNC-002, TC-FUNC-003 | Must |
| Story 1 AC2 | REQ-FUNC-004 | TC-FUNC-004 | Must |
| Story 1 AC3 | REQ-FUNC-005, REQ-FUNC-006 | TC-FUNC-005, TC-FUNC-006 | Must |
| Story 1 AC4 (Sad Path) | REQ-FUNC-007 | TC-FUNC-007 | Must |
| Story 1 AC5 (Sad Path) | REQ-FUNC-008 | TC-FUNC-008 | Must |
| Story 2 (팩트체크 대시보드) AC1 | REQ-FUNC-009 | TC-FUNC-009 | Must |
| Story 2 AC2 | REQ-FUNC-010, REQ-FUNC-011 | TC-FUNC-010, TC-FUNC-011 | Must |
| Story 2 AC3 | REQ-FUNC-012 | TC-FUNC-012 | Must |
| Story 2 AC4 | REQ-NF-008, REQ-NF-009 | TC-NF-008, TC-NF-009 | Must |
| Story 2 AC5 (Sad Path) | REQ-FUNC-013 | TC-FUNC-013 | Must |
| Story 3 (1-Tap 카톡 공유) AC1 | REQ-FUNC-014 | TC-FUNC-014 | Must |
| Story 3 AC2 | REQ-FUNC-015 | TC-FUNC-015 | Must |
| Story 3 AC3 | REQ-FUNC-016 | TC-FUNC-016 | Must |
| Story 3 AC4 (Sad Path) | REQ-FUNC-017 | TC-FUNC-017 | Must |
| Story 4 (데이터 신뢰 시스템) AC1 | REQ-FUNC-018 | TC-FUNC-018 | Must |
| Story 4 AC2 | REQ-FUNC-019, REQ-FUNC-020 | TC-FUNC-019, TC-FUNC-020 | Must |
| Story 4 AC3 | REQ-FUNC-021 | TC-FUNC-021 | Must |
| Story 4 AC4 (Sad Path) | REQ-FUNC-022 | TC-FUNC-022 | Must |

### 5.2 Feature ↔ Requirement ID 매핑

| Feature | Requirement IDs |
|---|---|
| F1. Super-Calc Engine | REQ-FUNC-001 ~ REQ-FUNC-008, REQ-NF-001, REQ-NF-006, REQ-NF-015, REQ-NF-017 |
| F2. Anti-BS Dashboard | REQ-FUNC-009 ~ REQ-FUNC-013, REQ-NF-002, REQ-NF-009, REQ-NF-021 |
| F3. Viral Engine | REQ-FUNC-014 ~ REQ-FUNC-017, REQ-NF-003, REQ-NF-016, REQ-NF-034 |
| F4. Data Trust System | REQ-FUNC-018 ~ REQ-FUNC-022, REQ-NF-004, REQ-NF-012, REQ-NF-013, REQ-NF-026 |
| F7. 비교 이력 저장 | REQ-FUNC-023 |
| F8. 트렌드 팩트체크 | REQ-FUNC-024 |
| F9. 가격 하락 알림 | REQ-FUNC-025 |
| F10. 3탭 UX 고도화 | REQ-FUNC-026 |
| F11. B2B 대시보드 | REQ-FUNC-027 |
| F12. B2C 프리미엄 | REQ-FUNC-028 |

### 5.3 Pain ID ↔ Requirement ID 매핑

| Pain ID | 관련 Requirement ID |
|---|---|
| CORE-1 (단가 비교 수동 작업) | REQ-FUNC-001 ~ REQ-FUNC-008, REQ-NF-001, REQ-NF-008 |
| CORE-2 (성분 정보 해석 불가) | REQ-FUNC-010, REQ-FUNC-012, REQ-FUNC-013 |
| CORE-3 (광고성 콘텐츠 범람) | REQ-FUNC-009, REQ-FUNC-010, REQ-NF-021 |
| CORE-4 (가격 적정성 판단 기준 부재) | REQ-FUNC-005, REQ-NF-008, REQ-NF-009 |
| EXT-2 (기존 비교 앱 데이터 오류) | REQ-FUNC-018 ~ REQ-FUNC-022, REQ-NF-012, REQ-NF-013 |
| CJM-1 (광고 vs 독립 정보 구분 불가) | REQ-FUNC-009, REQ-NF-031 |

### 5.4 KPI ↔ Requirement ID 매핑

| KPI | 관련 Requirement ID |
|---|---|
| TTC (North Star) | REQ-NF-008 |
| SEO 오가닉 방문자 | REQ-NF-031 |
| 퍼널 전환율 | REQ-NF-032 |
| 제휴 CTR | REQ-NF-033 |
| K-Factor | REQ-NF-034, REQ-FUNC-014 ~ REQ-FUNC-017 |
| D30~D60 재방문율 | REQ-NF-035 |
| DB 오류율 | REQ-NF-012 |
| 제휴 CPA 월 수익 | REQ-NF-036 |

---

## 6. Appendix

### 6.1 API Endpoint List

| # | Method | Endpoint | 설명 | 인증 | Rate Limit | 응답 시간 SLA |
|---|---|---|---|---|---|---|
| 1 | POST | `/api/v1/calc/daily-cost` | 성분명 기준 다채널 1일 단가 산출·정렬 | API Key | TBD | p95 <= 2,000ms |
| 2 | GET | `/api/v1/badges/{ingredient_id}` | 성분별 뱃지 조회 | 없음 | TBD | 캐시 TTL 24h, p95 <= 1,000ms |
| 3 | POST | `/api/v1/reports` | 오류 제보 접수 | 사용자 인증 | 동일 제품 24h 내 5건 제한 | 접수 응답 <= 3초 |
| 4 | GET | `/api/v1/labels/{product_id}` | 제품 원본 라벨 이미지 조회 | 없음 | TBD | p95 <= 500ms |
| 5 | POST | `/api/v1/share/kakao` | 카카오톡 공유 카드 생성 | 사용자 세션 | TBD | p95 <= 1,500ms |
| 6 | GET | `/api/v1/products/search` | 제품 검색 (키워드/성분명) | 없음 | TBD | p95 <= 1,000ms |
| 7 | GET | `/api/v1/products/{product_id}` | 제품 상세 정보 조회 | 없음 | TBD | p95 <= 1,000ms |
| 8 | POST | `/api/v1/products/register-request` | 미등록 성분 등록 요청 | 사용자 인증 | TBD | 제출 성공률 >= 99% |

### 6.2 Entity & Data Model

#### 6.2.1 PRODUCT (제품)

| 필드명 | 타입 | 제약 | 설명 |
|---|---|---|---|
| product_id | STRING | PK | 제품 고유 식별자 |
| product_name | STRING | NOT NULL | 제품명 |
| brand_name | STRING | NOT NULL | 브랜드명 |
| manufacturer | STRING | | 제조사 |
| category | STRING | NOT NULL | 카테고리 (예: 비타민, 미네랄, 프로바이오틱스) |
| source_channel | STRING | NOT NULL | 수집 채널 (iHerb / 쿠팡 / 네이버) |
| original_url | STRING | NOT NULL | 원본 제품 페이지 URL |

#### 6.2.2 INGREDIENT (성분)

| 필드명 | 타입 | 제약 | 설명 |
|---|---|---|---|
| ingredient_id | STRING | PK | 성분 고유 식별자 |
| standard_name | STRING | NOT NULL | 표준 성분명 (정규화된 이름) |
| common_name | STRING | | 일상어 번역명 (예: "몸에 잘 흡수되는 비타민 D3") |
| amount_per_serving | FLOAT | NOT NULL | 1회 복용량당 함량 |
| unit | STRING | NOT NULL | 단위 (예: mg, IU, mcg) |
| mfds_status | STRING | NOT NULL | 식약처 인정 상태 (APPROVED / CAUTION / NOT_APPROVED / NOT_LISTED) |
| mfds_claim | STRING | | 식약처 기능성 인정 내용 (공전 원문) |

#### 6.2.3 PRICE_SNAPSHOT (가격 스냅샷)

| 필드명 | 타입 | 제약 | 설명 |
|---|---|---|---|
| snapshot_id | STRING | PK | 스냅샷 고유 식별자 |
| product_id | STRING | FK → PRODUCT | 제품 참조 |
| original_price | FLOAT | NOT NULL | 원래 가격 (원화 또는 외화) |
| currency | STRING | NOT NULL | 통화 코드 (KRW, USD 등) |
| exchange_rate | FLOAT | | 적용 환율 (외화인 경우) |
| shipping_fee | FLOAT | DEFAULT 0 | 배송비 |
| customs_fee | FLOAT | DEFAULT 0 | 관세 |
| daily_cost_krw | FLOAT | NOT NULL | 1일 복용량 기준 원화 단가 (최종가) |
| captured_at | DATETIME | NOT NULL | 가격 수집 시점 |

#### 6.2.4 BADGE (뱃지)

| 필드명 | 타입 | 제약 | 설명 |
|---|---|---|---|
| badge_id | STRING | PK | 뱃지 고유 식별자 |
| ingredient_id | STRING | FK → INGREDIENT | 성분 참조 |
| badge_type | STRING | NOT NULL, ENUM(APPROVED, CAUTION, NOT_APPROVED) | 뱃지 유형 |
| badge_label | STRING | NOT NULL | 뱃지 표시 텍스트 (식약처 공전 원문 래핑) |
| evidence_source | STRING | NOT NULL | 근거 출처명 (예: "식약처 건강기능식품공전") |
| evidence_url | STRING | | 근거 출처 URL (식약처 DB 링크, 논문 DOI 등) |

#### 6.2.5 LABEL_ARCHIVE (라벨 아카이브)

| 필드명 | 타입 | 제약 | 설명 |
|---|---|---|---|
| label_id | STRING | PK | 라벨 고유 식별자 |
| product_id | STRING | FK → PRODUCT | 제품 참조 |
| image_url | STRING | NOT NULL | 원본 라벨 이미지 URL |
| uploaded_at | DATETIME | NOT NULL | 업로드 일시 |

#### 6.2.6 ERROR_REPORT (오류 제보)

| 필드명 | 타입 | 제약 | 설명 |
|---|---|---|---|
| report_id | STRING | PK | 제보 고유 식별자 |
| product_id | STRING | FK → PRODUCT | 대상 제품 참조 |
| reporter_id | STRING | FK → USER | 제보자 참조 |
| field_name | STRING | NOT NULL | 오류 필드명 (예: daily_cost_krw, mfds_status) |
| reported_value | STRING | NOT NULL | 현재 표시 값 (오류 값) |
| correct_value | STRING | NOT NULL | 제보자가 주장하는 정정 값 |
| status | STRING | NOT NULL, ENUM(SUBMITTED, REVIEWING, RESOLVED, REJECTED, SPAM) | 처리 상태 |
| reported_at | DATETIME | NOT NULL | 제보 일시 |
| resolved_at | DATETIME | | 수정 완료 일시 |

#### 6.2.7 USER (사용자)

| 필드명 | 타입 | 제약 | 설명 |
|---|---|---|---|
| user_id | STRING | PK | 사용자 고유 식별자 |
| email | STRING | UNIQUE, NOT NULL | 이메일 주소 |
| persona_type | STRING | | 페르소나 유형 (C1, C2, A2, E2 등. 분석 목적) |
| created_at | DATETIME | NOT NULL | 계정 생성 일시 |

#### 6.2.8 엔터티 관계

| 관계 | 설명 | 다중성 |
|---|---|---|
| PRODUCT ↔ INGREDIENT | 제품은 여러 성분을 포함한다 | 1:N |
| PRODUCT ↔ PRICE_SNAPSHOT | 제품은 여러 가격 스냅샷을 가진다 (시점별) | 1:N |
| PRODUCT ↔ LABEL_ARCHIVE | 제품은 여러 라벨 이미지를 가진다 | 1:N |
| INGREDIENT ↔ BADGE | 성분은 여러 뱃지를 가진다 | 1:N |
| PRODUCT ↔ ERROR_REPORT | 제품은 여러 오류 제보를 받을 수 있다 | 1:N |
| USER ↔ ERROR_REPORT | 사용자는 여러 오류 제보를 제출할 수 있다 | 1:N |

### 6.3 Detailed Interaction Models (상세 시퀀스 다이어그램)

#### 6.3.1 상세 흐름 1: 단가 비교 전체 플로우 (Happy Path + Edge Cases)

```mermaid
sequenceDiagram
    actor User as 사용자
    participant FE as 프론트엔드 (모바일 웹)
    participant GW as API Gateway
    participant Calc as Super-Calc Engine
    participant iHerb as iHerb Affiliate API
    participant Coupang as 쿠팡 파트너스 API
    participant Naver as 네이버 API
    participant FX as 환율 API (한국수출입은행)
    participant Cache as 캐시 (Redis)
    participant DB as 데이터베이스

    User->>FE: 성분명 입력 (예: "비타민D 1000IU")
    FE->>GW: GET /api/v1/products/search?q=비타민D+1000IU
    GW->>DB: 제품 검색
    DB-->>GW: 매칭 제품 목록
    
    alt 검색 결과 있음
        GW-->>FE: 제품 목록 반환
        FE-->>User: 검색 결과 표시
    else 미등록 성분
        GW-->>FE: 빈 결과 + 등록 요청 CTA
        FE-->>User: "미등록 상태입니다" + [제품 등록 요청하기] 버튼
        User->>FE: [제품 등록 요청하기] 탭
        FE->>GW: POST /api/v1/products/register-request
        GW-->>FE: 요청 접수 확인
        FE-->>User: 요청 완료 알림
        Note over User,FE: CTA 노출 <= 300ms, 제출 성공률 >= 99%
    end

    User->>FE: "1일 단가 비교" 버튼 탭
    FE->>GW: POST /api/v1/calc/daily-cost
    GW->>Calc: 단가 산출 요청

    Calc->>Cache: 환율 캐시 확인
    alt 환율 캐시 유효 (15분 이내)
        Cache-->>Calc: 캐시된 환율
    else 환율 캐시 만료
        Calc->>FX: GET /exchange-rate?currency=USD
        FX-->>Calc: 매매기준율 + 기준 시각
        Calc->>Cache: 환율 캐싱 (TTL 15분)
    end

    par 3채널 병렬 조회 (타임아웃: 5초)
        Calc->>iHerb: 제품 가격 조회
        Calc->>Coupang: 제품 가격 조회
        Calc->>Naver: 제품 가격 조회
    end

    alt iHerb 정상 응답
        iHerb-->>Calc: 가격(USD), 성분표, 딥링크, 배송비
    else iHerb 장애 (5xx / 타임아웃)
        Note over Calc,iHerb: 장애 채널 기록
    end

    alt 쿠팡 정상 응답
        Coupang-->>Calc: 가격(KRW), 딥링크
    else 쿠팡 장애
        Note over Calc,Coupang: 장애 채널 기록
    end

    alt 네이버 정상 응답
        Naver-->>Calc: 가격(KRW), 딥링크
    else 네이버 장애
        Note over Calc,Naver: 장애 채널 기록
    end

    Calc->>Calc: 1일 복용량 기준 원화 단가 산출
    Calc->>Calc: 배송비 + 관세 포함 최종가 계산 (오차 <= 3%)
    Calc->>Calc: 최저가 순 정렬
    Calc->>DB: PRICE_SNAPSHOT 저장

    Calc-->>GW: 정상 채널 결과 + 장애 채널 안내
    GW-->>FE: 응답 반환
    FE-->>User: 1일 단가 랭킹 표시
    FE-->>User: 환율 기준 시각 표시
    FE-->>User: 장애 채널 인라인 안내 (해당 시)

    Note over User,FE: 전체 응답 p95 <= 2초
    Note over Calc: 부분 결과 제공 성공률 >= 99%
```

#### 6.3.2 상세 흐름 2: 뱃지 판정 + 일상어 번역 전체 플로우

```mermaid
sequenceDiagram
    actor User as 사용자 (C2/A2)
    participant FE as 프론트엔드
    participant GW as API Gateway
    participant Badge as Badge Service
    participant MFDS as 식약처 공공 API
    participant Trans as 용어 번역 서비스
    participant Cache as 캐시 (Redis, TTL 24h)
    participant DB as 데이터베이스

    User->>FE: 제품 상세 페이지 진입
    FE->>FE: 마케팅 콘텐츠 필터링 (광고, 리뷰, 별점 = 0건)
    FE->>GW: GET /api/v1/products/{product_id}
    GW->>DB: 제품 + 성분 목록 조회
    DB-->>GW: PRODUCT + INGREDIENT 배열
    GW-->>FE: 제품 상세 데이터

    loop 각 성분(ingredient)에 대해
        FE->>GW: GET /api/v1/badges/{ingredient_id}
        GW->>Badge: 뱃지 조회 요청
        Badge->>Cache: 캐시 확인

        alt 캐시 히트 (TTL 24h 이내)
            Cache-->>Badge: 캐시된 BADGE 데이터
        else 캐시 미스
            Badge->>MFDS: 기능성 인정 원료 조회 (원료명/인증번호)
            
            alt 식약처 등재 원료
                MFDS-->>Badge: 기능성 인정 내용, 일일 섭취량, 주의사항
                Badge->>Badge: 뱃지 유형 판정 (APPROVED / CAUTION)
                Badge->>Badge: 공전 원문 1:1 매칭 검증
            else 식약처 미등재 원료
                MFDS-->>Badge: 미등재 응답
                Badge->>Badge: NOT_LISTED 판정
            end

            Badge->>Cache: 결과 캐싱 (TTL 24h)
        end

        Badge->>Trans: 전문 용어 일상어 번역 요청
        Trans-->>Badge: 번역 결과 (커버리지 >= 95%, 정확도 >= 98%)
        Badge-->>GW: BADGE + 번역 데이터
        GW-->>FE: 응답
    end

    FE->>FE: 뱃지 렌더링
    FE-->>User: APPROVED/CAUTION/NOT_APPROVED 뱃지 표시
    FE-->>User: 미등재 원료 → 회색 "기능성 인정 정보 없음" + 툴팁
    FE-->>User: 전문 용어 옆 일상어 번역 괄호 표시

    Note over User,FE: 뱃지 로드 시간 p95 <= 1초
    Note over FE: 마케팅 콘텐츠 = 0건/페이지
    Note over Badge: 뱃지-원문 불일치율 < 0.5%
```

#### 6.3.3 상세 흐름 3: 카카오톡 공유 + 수신자 구매 전환 전체 플로우

```mermaid
sequenceDiagram
    actor Sender as 공유자 (A2)
    participant FE as 프론트엔드
    participant GW as API Gateway
    participant Share as Share Card Service
    participant OG as OG Image Generator
    participant Kakao as 카카오 Link API
    actor Receiver as 수신자
    participant Landing as 랜딩 페이지
    participant Affiliate as 제휴 커머스

    Sender->>FE: 비교 결과 화면에서 "카톡 공유" 버튼 탭
    FE->>GW: POST /api/v1/share/kakao
    GW->>Share: 공유 카드 생성 요청

    Share->>OG: OG 이미지 동적 생성 요청
    Note over OG: 단가, 뱃지, 성분 비교 포함
    OG-->>Share: OG 이미지 URL 반환
    Share-->>GW: 공유 카드 데이터 (OG URL, title, description, link)
    GW-->>FE: 응답

    Note over FE: 카드 생성 완료 (p95 <= 1.5초)

    FE->>Kakao: 카카오 Link API 호출 (JS SDK)

    alt 카카오 API 정상 응답
        Kakao-->>FE: 전송 성공
        FE-->>Sender: "공유 완료" 알림
        FE->>GW: 이벤트 로깅 (kakao_share_send)
    else 카카오 API 장애 (타임아웃/오류)
        FE->>FE: 폴백 UI 활성화 (<= 1초)
        FE-->>Sender: "URL 복사" 버튼 표시
        FE-->>Sender: 네이버 밴드 / 문자 대체 공유 버튼 노출
        
        alt URL 복사 선택
            Sender->>FE: "URL 복사" 탭
            FE->>FE: 클립보드 복사
            FE-->>Sender: 토스트 알림 "링크가 복사되었습니다"
        else 대체 채널 선택
            Sender->>FE: 대체 공유 버튼 탭
            FE->>FE: 해당 채널 공유 처리
        end
        
        Note over FE: 폴백 공유 성공률 >= 95%
    end

    Note over Sender,Receiver: --- 수신자 여정 ---

    Receiver->>Landing: 카카오 내장 브라우저에서 카드 탭
    Landing->>Landing: 비교 결과 웹뷰 로드 (앱 설치/가입 불요)
    Landing-->>Receiver: 비교 결과 표시 (1일 단가, 뱃지, 성분)

    Note over Receiver,Landing: 랜딩 성공률 >= 98%, 로드 시간 <= 2초

    Receiver->>Landing: "최저가 구매하기" 버튼 탭
    Landing->>Affiliate: 제휴 커머스 딥링크 이동 (UTM 파라미터 포함)
    Affiliate-->>Receiver: 결제 페이지 표시

    Note over Landing: 공유→구매 링크 클릭 전환율 >= 8%
```

#### 6.3.4 상세 흐름 4: 오류 제보 전체 플로우 (유효 + 스팸 처리)

```mermaid
sequenceDiagram
    actor User as 사용자 (E2)
    participant FE as 프론트엔드
    participant GW as API Gateway
    participant Label as Label Archive Service
    participant Report as Error Report Service
    participant Filter as 스팸/중복 필터
    participant DB as 데이터베이스
    participant Admin as 운영팀
    participant Notify as 알림 서비스

    %% 출처 확인 프로세스
    User->>FE: 제품 성분 데이터 확인 중
    User->>FE: "[출처 확인]" 버튼 탭 (1번째 클릭)
    FE->>GW: GET /api/v1/labels/{product_id}
    GW->>Label: 라벨 아카이브 조회
    Label->>DB: 원본 라벨 + 출처 정보 조회
    DB-->>Label: LABEL_ARCHIVE + 출처 데이터
    Label-->>GW: 라벨 이미지 URL + 출처 정보
    GW-->>FE: 응답
    FE-->>User: 아코디언 메뉴 펼침 (2번째 클릭으로 도달)

    Note over User,FE: 출처 도달 <= 2클릭, 렌더 <= 500ms
    Note over FE: 표시 항목: 식약처 DB 링크, 라벨 이미지, 논문 DOI

    %% 오류 제보 프로세스
    User->>FE: 데이터 불일치 발견
    User->>FE: "[오류 신고]" 버튼 탭
    FE-->>User: 제보 양식 표시 (필드명, 현재값, 정정값)
    User->>FE: 제보 내용 작성 후 제출
    FE->>GW: POST /api/v1/reports

    GW->>Report: 제보 접수 요청
    Report->>Filter: 스팸/중복 검사

    Filter->>DB: 동일 제품·동일 사용자 24h 내 제보 이력 조회
    DB-->>Filter: 이력 데이터

    alt 유효 제보 (중복 < 5건 & 내용 비어있지 않음)
        Filter-->>Report: 유효 판정
        Report->>DB: ERROR_REPORT 저장 (status: SUBMITTED)
        Report-->>GW: 접수 확인 + 예상 처리 시간 (48h)
        GW-->>FE: 응답
        FE-->>User: "접수 완료" 알림 + 예상 처리 시간 표시

        Note over User,FE: 접수 응답 <= 3초

        Report->>Notify: 운영팀 알림 발송
        Notify->>Admin: 신규 제보 알림

        Admin->>DB: 제보 검증 (원본 데이터 대조)
        Admin->>DB: ERROR_REPORT 상태 → REVIEWING
        Admin->>DB: 데이터 수정 반영
        Admin->>DB: ERROR_REPORT 상태 → RESOLVED + resolved_at 기록

        Note over Admin,DB: 수정 완료 <= 48시간 (SLA)

        DB-->>Report: 수정 완료 이벤트
        Report->>Notify: 제보자 알림 요청
        Notify-->>User: 푸시 알림 "귀하의 제보로 수정되었습니다"
        Report->>DB: 리워드 (포인트/배지) 지급

        Note over Report: 알림 발송 <= 수정 후 1시간
    else 스팸/중복 제보 (24h 내 5건 이상 또는 빈 문자열)
        Filter-->>Report: 스팸/중복 판정
        Report-->>GW: 차단 사유
        GW-->>FE: 응답
        FE-->>User: "중복 또는 불완전한 제보입니다. 내용을 확인해 주세요."

        Note over Filter: 차단 정확도 >= 95%, 오차단률 <= 2%
        Note over Report: 기존 유효 제보 영향 없음
    end
```

### 6.4 Validation Plan (검증 계획)

#### 6.4.1 실험 가설 및 성공 기준

| # | 실험 가설 | 실험 설계 | 측정 KPI | 성공 기준 |
|---|---|---|---|---|
| H1 | C1은 Super-Calc Engine이 수동 계산보다 빠르고 정확하다고 느낀다 | 시간 측정 실험 (n=30): 동일 제품 비교 과제를 (A) 수동 엑셀 vs (B) 플랫폼으로 수행 | 과제 완료 시간(초), 최종가 오차율(%), 만족도(5점 척도) | 완료 시간 90% 단축, 오차율 <= 3%, 만족도 >= 4.0/5 |
| H2 | C2/A2는 Anti-BS Dashboard로 기존 탐색 대비 더 높은 신뢰감을 느낀다 | Within-subject 사전/사후 설문 (n=70, power >= 0.80, alpha=0.05, d=0.5) | 결정 소요 시간(분), 결정 확신도(5점 척도), "광고 걱정 없음" 동의율(%) | 결정 시간 <= 30분(p50), 확신도 >= 4.0/5 (사전 대비 >= +1.0점 상승), 동의율 >= 70% |
| H3 | 카카오톡 공유 카드 수신자의 일정 비율이 구매 링크를 클릭한다 | 공유 전환 추적 (n=200 공유 건): (A) OG 이미지 포함 카드 vs (B) 텍스트 전용 링크 | 수신자 랜딩률(%), 구매 링크 클릭률(%), K-Factor | 랜딩률 >= 50%, 클릭률 >= 8%, K-Factor >= 1.1 |
| H4 | 오류 제보 및 48h 수정 SLA가 E2 타겟의 신뢰도를 유의미하게 높인다 | 제보 SLA 만족도 조사 (n=30 제보자) | 48h 내 수정 완료율(%), 제보자 만족도(5점 척도), D30 리텐션 | 수정 완료율 >= 90%, 만족도 >= 4.0/5, D30 리텐션 >= 25% |
| H5 | 구매 여정 이탈률(55~75% 추정)이 플랫폼에서 유의미하게 낮다 | 퍼널 분석 (Public Launch 후 4주, n >= 500 세션) | 단계별 이탈률(%), 전체 퍼널 완주율(%) | 전체 퍼널 완주율 >= 15% |

#### 6.4.2 베타 채널 설계

| 단계 | 대상 | 규모 | 목적 |
|---|---|---|---|
| Alpha (내부) | 팀 구성원 + 지인 | 10명 | 크리티컬 버그, UX 플로우 검증 |
| Closed Beta 1 | C1 타겟 — iHerb 파워유저, 건기식 커뮤니티 모집 | 30명 | F1(Super-Calc) 정확도/속도 검증, TTC 측정 |
| Closed Beta 2 | C2 타겟 — 건강검진 후 첫 구매 경험자 모집 | 20명 | F2(Anti-BS) 신뢰도/만족도, 결정 소요 시간 측정 |
| Public Launch | SEO 오가닉 + 건기식 커뮤니티 시딩 | 목표 MAU 2,200명 | 퍼널 전환율, K-Factor, 제휴 CTR 실측 |

#### 6.4.3 경쟁 대안 대비 벤치마크

| 비교 대상 | 비교 축 | 기대 결과 |
|---|---|---|
| 필라이즈 (AI 초개인화 추천) | 비교 정확도 vs 추천 다양성 | 본 플랫폼: "최저가 정확도" 우위 |
| 에누리 건강플러스 (국내 가격비교) | 채널 커버리지, 1일 단가 환산 여부 | 본 플랫폼: 3채널 커버리지 + 1일 단가 환산 유일 지원 |
| 수동 스프레드시트 (C1 현행 대안) | 소요 시간, 오류율 | 시간 720배 증가, 오류율 70% 감소 |
| 네이버 블로그/약사 유튜버 (C2 현행 대안) | 신뢰도, 광고 비율 | 광고 비율 72.3%+ → 0%, 신뢰도 2배 증가 |

---

*— End of Document —*
