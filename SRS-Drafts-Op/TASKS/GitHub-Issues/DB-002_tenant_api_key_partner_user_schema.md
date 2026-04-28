---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] DB-002: Tenant·API Key·Partner User 세션/역할 스키마 작성"
labels: 'feature, database, security, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [DB-002] `tenant`, `api_key`, Partner user/session/role 최소 schema와 index 작성
- 목적: API Key 인증, tenant 격리, Partner Console role-based access, API Key 발급/폐기, audit log 연계를 위한 최소 인증/테넌트 데이터 모델을 만든다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#621-entity-summary`](../../SRS/실버케어_SRS_v0.5_단일구조.md#621-entity-summary)
- 시퀀스 다이어그램: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#developer-onboarding-and-sandbox`](../../SRS/실버케어_SRS_v0.5_단일구조.md#developer-onboarding-and-sandbox)
- 데이터 모델 (ERD): [`../../SRS/실버케어_SRS_v0.5_단일구조.md#622-field-level-schema`](../../SRS/실버케어_SRS_v0.5_단일구조.md#622-field-level-schema)
- API 명세: [`../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list`](../../SRS/실버케어_SRS_v0.5_단일구조.md#61-api-endpoint-list)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `tenant` model을 작성한다: `tenant_id`, `name`, `status`, `created_at`.
- [ ] `api_key` model을 작성한다: `api_key_id`, `tenant_id`, `key_hash`, `status`, `created_at`, `revoked_at`.
- [ ] API Key lookup을 위한 `key_hash` unique index와 `tenant_id`, `status` index를 정의한다.
- [ ] Partner Console용 user/session/role 최소 model을 작성한다: `actor_id`, `tenant_id`, `actor_role`, `status`, `created_at`.
- [ ] role enum 또는 role constant를 CT-006의 role/module matrix와 연결한다.
- [ ] tenant-owned entity가 향후 공통 `tenant_id` index를 유지할 수 있도록 migration naming 규칙을 정의한다.
- [ ] API Key 원문 저장 금지와 hash-only 저장 constraint를 명시한다.

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: API Key hash만 저장됨
- Given: API Key 발급 command가 원문 key를 생성함
- When: `api_key` record를 저장함
- Then: DB에는 원문 key가 저장되지 않고 `key_hash`만 저장되어야 한다.

Scenario 2: API 인증 조회 index가 존재함
- Given: API 요청에 key가 포함됨
- When: 인증 로직이 key hash로 `api_key`를 조회함
- Then: `key_hash` unique index와 `status` 조건으로 active key를 조회할 수 있어야 한다.

Scenario 3: Partner Console role과 tenant context가 연결됨
- Given: Partner Console 사용자가 로그인함
- When: session payload를 구성함
- Then: `actor_id`, `tenant_id`, `actor_role`을 DB에서 조회할 수 있어야 한다.

## :gear: Technical & Non-Functional Constraints
- 보안: API Key 원문은 최초 발급 시 1회만 표시하고 DB에 저장하지 않는다.
- 멀티테넌시: tenant-owned 데이터는 `tenant_id` 기준으로 격리되어야 한다.
- 성능: API 인증 조회는 `key_hash` unique index를 사용해야 한다.
- 감사성: API Key 발급/폐기는 audit log와 연결될 수 있어야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] `tenant`, `api_key`, Partner user/session/role 최소 schema가 작성되었는가?
- [ ] API Key hash-only 저장 규칙이 schema와 구현 가이드에 반영되었는가?
- [ ] 인증 조회와 tenant 격리를 위한 index가 정의되었는가?
- [ ] SEC-001, CT-006, MOCK-001, CMD-001이 참조할 수 있는가?

## :construction: Dependencies & Blockers
- Depends on: DB-001, CT-006
- Blocks: DB-003, DB-006, DB-008, MOCK-001, PLAT-001, SEC-001, SEC-002, QRY-001

