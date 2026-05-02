# DATABASE.md 핵심 질문

`DATABASE.md`는 `osom-plan-dba`, `osom-supabase-schema`, `osom-pgtap-best-practices`가 DB 관점 작업/검증을 할 때 참조하는 도메인 문서입니다.

## 질문 진행 원칙

- DB 레이어가 없는 프로젝트(순수 프론트엔드, 정적 사이트 등)에서는 사용자가 **이 파일 자체를 건너뛸 수 있게** 안내.
- 객관식/단답형 우선, 모르면 `_(미정)_`로 두고 진행.

## 핵심 질문 9개

| # | 질문 | 보기 (예시) | 매핑 섹션 |
| --- | --- | --- | --- |
| 1 | DB 엔진은? | PostgreSQL · MySQL · SQLite · MongoDB · 기타 | `## Engine` |
| 2 | DB 호스팅 환경은? | Supabase · Neon · RDS · Self-hosted · 로컬만 | `## Hosting` |
| 3 | 마이그레이션 도구는? | Supabase CLI · Prisma · Drizzle · TypeORM · 자체 SQL · 미정 | `## Migration tool` |
| 4 | 스키마 디렉토리/파일 위치는? | `supabase/migrations/` · `prisma/schema.prisma` · `postgres/` · 기타 경로 입력 | `## Schema location` |
| 5 | RLS(Row-Level Security) 사용? | 사용 · 미사용 · 일부 테이블만 | `## RLS policy` |
| 6 | RLS 사용 시 인증 주체는? | `auth.uid()` (Supabase Auth) · 커스텀 JWT claim · 기타 | `## RLS auth subject` |
| 7 | SQL 네이밍 컨벤션은? | snake_case · camelCase · 혼합 (별도 문서 링크) | `## SQL naming` |
| 8 | 테스트 프레임워크는? | pgTAP · Jest + 실제 DB · 통합 테스트만 · 없음 | `## DB testing` |
| 9 | 프로덕션 마이그레이션 안전 규칙은? | 다운타임 0 정책 · 백워드 호환 필수 · 별도 정책 문서 · 미정 | `## Migration safety` |

답변하지 않은 항목은 `_(미정)_`로 둡니다. 9번 같이 정책 문서가 따로 있으면 본문은 그 문서를 가리키는 링크로 대체.

## 권장 파일 구조

```markdown
# DATABASE

이 프로젝트의 DB 도메인 정책입니다. osom-plan-dba, osom-supabase-schema, osom-pgtap-best-practices가 이 문서를 기준으로 동작합니다.

## Engine
PostgreSQL 15

## Hosting
Supabase (production), Docker compose Postgres 15 (local)

## Migration tool
Supabase CLI — `supabase migration new`, `supabase db push`

## Schema location
`supabase/migrations/` (timestamped SQL files)

## RLS policy
모든 사용자 데이터 테이블에 RLS 활성. 시스템 테이블은 service_role 전용.

## RLS auth subject
`auth.uid()` (Supabase Auth)

## SQL naming
snake_case (테이블·컬럼·함수). 자세한 규칙은 `docs/rules/sql-naming.md`.

## DB testing
pgTAP (정책 검증) + Jest 통합 테스트(실제 Postgres 컨테이너 사용)

## Migration safety
- 다운타임 0 원칙: 컬럼 삭제 전 코드에서 미사용 → 다음 릴리스에서 drop.
- 모든 마이그레이션은 `BEGIN/COMMIT`로 감싸고 롤백 시나리오를 주석으로 남긴다.
- 자세한 규칙: `docs/rules/migration-safety.md`
```
