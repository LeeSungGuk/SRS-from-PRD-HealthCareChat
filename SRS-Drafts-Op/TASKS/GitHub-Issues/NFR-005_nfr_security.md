---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] NFR-005: HTTPS/TLS, storage encryption, environment secret 관리 점검 태스크 수행"
labels: 'feature, nfr, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [NFR-005] HTTPS/TLS, storage encryption, environment secret 관리 점검 태스크 수행
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-NF-018~019, C-TEC-007` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Vercel 배포 환경에서 모든 API와 Partner Console 경로가 HTTPS로만 접근되는지 점검한다.
- [ ] TLS 1.3 적용 여부 또는 플랫폼 제한으로 인한 동등 보안 설정 evidence를 수집한다.
- [ ] Supabase PostgreSQL 및 운영 저장소의 at-rest encryption이 AES-256 또는 동등 수준인지 점검 체크리스트를 작성한다.
- [ ] `.env`, Vercel Environment Variables, Gemini API key, Supabase key가 Git repository와 client bundle에 노출되지 않는지 secret scan 절차를 작성한다.
- [ ] 보안 점검 실패 시 production/PoC promote를 차단하는 NFR-009 gate 입력값을 정의한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: HTTPS가 강제됨
- Given: production 또는 PoC 배포 URL이 준비되어 있음
- When: HTTP와 HTTPS 접근을 각각 시도함
- Then: HTTP 요청은 HTTPS로 redirect되거나 차단되고 API 통신은 HTTPS에서만 성공해야 한다.

Scenario 2: 저장 데이터 암호화 evidence가 확인됨
- Given: Supabase 프로젝트와 운영 저장소 설정이 준비되어 있음
- When: security checklist를 실행함
- Then: at-rest encryption 적용 여부와 확인 근거가 checklist에 기록되어야 한다.

Scenario 3: secret이 저장소와 client bundle에 노출되지 않음
- Given: Gemini API key, Supabase service key, database URL이 환경 변수로 설정되어 있음
- When: secret scan과 build artifact scan을 실행함
- Then: 민감 key가 Git tracked file 또는 client bundle에서 발견되지 않아야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안/개인정보: service role key는 Server Actions 또는 Route Handlers 내부에서만 사용하고 browser-exposed 환경 변수로 배포하지 않는다.
- 배포: C-TEC-007에 따라 Vercel Git Integration과 Environment Variables 기준으로 점검한다.
- 운영: 보안 점검 결과는 release evidence로 보존하고 실패 시 promote를 차단한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-001, CT-001
- Blocks: None
