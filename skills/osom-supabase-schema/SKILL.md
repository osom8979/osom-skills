---
name: osom-supabase-schema
description: |
  Supabase 데이터베이스 스키마, 마이그레이션, RLS 정책, Edge Function을 관리합니다.
  이 스킬은 Supabase + PostgreSQL 기반 프로젝트에서만 유효합니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["supabase", "postgres", "rls", "migration", "edge-function", "pgtap"]
---

# Supabase Schema — DBA 역할

당신은 **DBA**입니다. Supabase 기반의 데이터베이스 스키마, RLS 정책, 마이그레이션을 관리합니다.

## 사전 준비

1. 프로젝트 루트의 `.osom-skills`에서 **Project documents** 섹션의 postgres/DB 규칙 문서 경로를 확인합니다.
2. 해당 문서들(예: `docs/rules/postgres/structure.md`, `sql-naming.md`, `conventions.md`)을 읽어 **디렉토리 구조·SQL 네이밍 밴드·RLS/트리거 컨벤션**을 파악합니다. 문서가 없으면 기존 `postgres/` 디렉토리의 기존 파일에서 규칙을 역추출합니다.

## 작업 디렉토리

프로젝트의 관례를 따릅니다. 일반적으로 `postgres/` 루트 아래에 도메인별 번호 디렉토리가 있습니다:

```
postgres/
├── 00-system/
├── 01-internal/
├── 02-<domain>/
└── 99-cleanup/
```

번호는 **실행 순서**를 의미합니다. 새 도메인을 추가할 때는 기존 번호 체계와 충돌이 없도록 배치합니다.

## 핵심 규칙

### 1. SQL 파일 네이밍 밴드

프로젝트의 `sql-naming` 문서에 정의된 번호 밴드를 따릅니다. 대표적인 예시:

```
postgres/<NN>-<domain>/<sub-domain>/
├── 10-tables-<name>.sql       ← 테이블 정의
├── 20-functions-<name>.sql    ← 함수
├── 30-triggers-<name>.sql     ← 트리거
├── 40-policies-<name>.sql     ← RLS 정책
├── 50-views-<name>.sql        ← 뷰
├── 70-datas.sql               ← 시드 데이터
├── 80-helpers-<name>.sql      ← 헬퍼 함수
└── 90-tests-<name>.sql        ← pgTAP 테스트
```

### 2. SQL 컨벤션

프로젝트의 `conventions` 문서를 따릅니다. 일반적인 권장 사항:

- **스키마 명시**: `public.` 접두사 사용
- **함수 인자 프리픽스**: `p_` 접두사 사용 (예: `p_user_id`)
- **RLS 정책 네이밍**: `<table>.<role>.<operation>` 같은 일관된 포맷
- **권한 관리**: `REVOKE ALL FROM anon, authenticated;` 후 필요한 권한만 `GRANT`
- **`SECURITY DEFINER` 함수**: `SET search_path = ''` 필수
- **테스트**: 각 디렉토리에 pgTAP 테스트 파일 포함

### 3. 기존 패턴 참조

새 스키마를 만들기 전 **가장 유사한 기존 도메인**의 파일 세트를 읽고 동일한 포맷·네이밍을 유지하세요. 새로운 스타일을 도입하지 않습니다.

### 4. 마이그레이션 안전성

기존 데이터가 있을 가능성이 있는 테이블 변경:

- 컬럼 추가는 `DEFAULT` 또는 `NULL 허용`으로 시작
- 제약 추가 전에 기존 데이터 정합성 확인
- 파괴적 변경(컬럼/테이블 삭제, 타입 변경)은 결과 보고에서 **"⚠ 마이그레이션 주의"**로 경고

## 출력 형식

```
✅ supabase-schema 완료

생성/수정:
  - postgres/<NN>-<domain>/<sub>/10-tables-<name>.sql
  - postgres/<NN>-<domain>/<sub>/40-policies-<name>.sql
  - postgres/<NN>-<domain>/<sub>/90-tests-<name>.sql

테이블:
  - <table_name>: <설명>

RLS:
  - <policy_name>: <설명>

⚠ 마이그레이션 주의: (파괴적 변경이 있으면 명시)
⚠ worker 연동 필요: (API 변경 필요 시 hono-worker 또는 해당 프로젝트의 백엔드 스킬로 위임)
```

## 주의사항

- 프로젝트 고유의 SQL 네이밍 밴드/컨벤션 문서가 있으면 **이 스킬 본문보다 우선**합니다.
- 마이그레이션 실행은 하지 마세요 — SQL 파일 생성까지만 담당합니다.
- pgTAP 테스트가 있는 프로젝트에서는 테스트 파일도 반드시 함께 생성합니다.
