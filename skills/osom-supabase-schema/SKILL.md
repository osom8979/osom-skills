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

1. 프로젝트 루트의 `DATABASE.md`에서 **Engine, Migration tool, Schema location, RLS policy, RLS auth subject, SQL naming, Migration safety**를 확인합니다.
2. `DATABASE.md`가 가리키는 추가 규칙 문서(예: `docs/rules/postgres/structure.md`, `sql-naming.md`, `migration-safety.md`)와 `STRUCTURE.md`의 `Rules directory` 안의 관련 문서를 읽어 **디렉토리 구조·SQL 네이밍 밴드·RLS/트리거 컨벤션**을 파악합니다. 문서가 없으면 기존 스키마 디렉토리의 파일에서 규칙을 역추출합니다.

## 작업 디렉토리

프로젝트의 관례를 따릅니다. 일반적으로 `postgres/` 루트 아래에 도메인별 번호 디렉토리가 있습니다.

```
postgres/
├── 00-system/
├── 01-internal/
├── 02-<domain>/
└── 99-cleanup/
```

번호는 **실행 순서**를 의미합니다. 새 도메인을 추가할 때는 기존 번호 체계와 충돌이 없도록 배치합니다.

## 규칙

이 스킬이 적용하는 규칙입니다. 각 규칙의 상세 내용은 `rules/` 디렉토리를 참조하세요.

- [SQL 파일 네이밍 밴드](rules/sql-naming-bands.md) — `10-tables`, `40-policies` 등 번호 밴드 컨벤션
- [SQL 컨벤션](rules/sql-conventions.md) — 스키마 명시, `p_` 접두사, RLS 네이밍, `SECURITY DEFINER`
- [기존 패턴 참조](rules/follow-existing-patterns.md) — 유사 도메인 파일을 먼저 읽고 동일 포맷 유지
- [마이그레이션 안전성](rules/migration-safety.md) — 다단계 분리, 파괴적 변경 경고

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
