---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] NFR-004: RPO 24h/RTO 4h baseline runbook, backup/restore drill 절차 작성"
labels: 'feature, nfr, priority:medium'
assignees: ''
---

## :dart: Summary
- 기능명: [NFR-004] RPO 24h/RTO 4h baseline runbook, backup/restore drill 절차 작성
- 목적: SRS `실버케어_SRS_v0.5_단일구조.md`의 `REQ-NF-003, 6.2.4 backup` 요구를 후속 구현·테스트가 바로 참조할 수 있는 개발 산출물로 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements`](../../SRS/실버케어_SRS_v0.5_단일구조.md#42-non-functional-requirements)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences`](../../SRS/실버케어_SRS_v0.5_단일구조.md#34-interaction-sequences)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model`](../../SRS/실버케어_SRS_v0.5_단일구조.md#62-entity--data-model)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Prisma/SQLite local과 Supabase PostgreSQL 배포 환경별 backup 대상 테이블과 제외 대상을 정의한다.
- [ ] PoC baseline RPO `<= 24h`, RTO `<= 4h`를 기준으로 backup 주기, 보관 위치, 복구 책임자를 runbook에 명시한다.
- [ ] 최소 1회 restore drill 절차를 작성하고 시작/종료 시각, 복구 데이터 범위, 누락 여부를 evidence로 기록한다.
- [ ] 개인정보 삭제 job과 retention job이 restore 후 재실행 또는 보정 가능한지 확인 절차를 포함한다.
- [ ] production GA 전 RPO/RTO 재승인 필요 항목을 risk 또는 open decision으로 남긴다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: backup age가 RPO를 만족함
- Given: 마지막 성공 backup 기록이 존재함
- When: DR checklist를 실행함
- Then: 마지막 backup 시각이 24시간 이내인지 판정하고 초과 시 실패 evidence를 남겨야 한다.

Scenario 2: restore drill이 RTO를 만족함
- Given: 테스트용 backup snapshot과 복구 대상 환경이 준비되어 있음
- When: restore drill을 실행함
- Then: 4시간 이내에 핵심 운영 데이터가 복구되고 drill report에 소요 시간과 검증 결과가 기록되어야 한다.

Scenario 3: retention과 삭제 이력이 restore 후 보정됨
- Given: restore된 데이터에 retention 만료 또는 삭제 요청 대상이 포함됨
- When: restore 후 lifecycle reconciliation을 실행함
- Then: 만료 데이터와 삭제 요청 대상이 정책에 따라 재삭제 또는 별도 격리되어야 한다.

## :gear: Technical & Non-Functional Constraints
- 아키텍처: Next.js App Router 단일 풀스택 구조를 유지하고 별도 백엔드 서버를 만들지 않는다.
- 보안/개인정보: backup 파일과 restore evidence는 운영 권한자만 접근 가능해야 하며 원문 대화 노출을 최소화한다.
- 운영: RPO/RTO는 Phase A PoC baseline이며 production GA 전 재검토 대상으로 표시한다.
- 비용: backup 보관 기간은 NFR-006 retention 정책과 충돌하지 않도록 제한한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 단위 테스트(Unit Test), 통합 테스트(Integration Test), E2E 테스트 또는 문서 검증 중 해당 작업에 맞는 검증이 추가되었고 통과하는가?
- [ ] Linter, type check, schema validation 또는 review checklist에서 신규 경고가 없는가?
- [ ] 관련 API 명세서, 데이터 모델, UI spec, 운영 문서 또는 README index가 최신화되었는가?
- [ ] 대상 작업의 Dependencies/Blocks 관계와 충돌하지 않는가?

## :construction: Dependencies & Blockers
- Depends on: DB-001, DB-006
- Blocks: None
