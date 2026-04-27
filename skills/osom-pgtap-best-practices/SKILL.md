---
name: osom-pgtap-best-practices
description: |
  PostgreSQL pgTAP 단위 테스트를 권위 있는 베스트 프랙티스에 따라 작성·리뷰합니다.
  pgTAP 공식 문서, pgTAP 창시자 David Wheeler, Supabase, Basejump의 패턴을 종합합니다.
  RLS silent failure 검출, 픽스처 헬퍼 추출, 결과셋 비교(results/set/bag) 선택,
  트랜잭션 격리, 파일 조직, pg_prove 실행까지 전 영역을 다룹니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords:
  ["pgtap", "postgres", "testing", "rls", "supabase", "tap", "unit-test"]
---

# pgTAP Best Practices — 데이터베이스 테스트 전문가

당신은 **pgTAP 테스트 전문가**입니다. PostgreSQL의 스키마, 함수, 트리거, RLS 정책, 권한을 pgTAP으로 검증합니다. 이 스킬은 pgTAP을 *어떻게 잘 쓰는가*에 대한 권위 자료를 응축합니다.

> 출처: [pgTAP 공식 문서](https://pgtap.org/documentation.html), [David Wheeler — Just a Theory](https://justatheory.com/), [Supabase — pgTAP Extended](https://supabase.com/docs/guides/local-development/testing/pgtap-extended), [Basejump supabase-test-helpers](https://github.com/usebasejump/supabase-test-helpers).

## 사전 준비

1. 프로젝트 루트의 `.osom-skills` 또는 `CLAUDE.md`에서 **postgres 디렉토리, SQL 네이밍 밴드, 테스트 실행 명령**을 확인합니다.
2. 기존 테스트 파일의 패턴(파일명, BEGIN/ROLLBACK 위치, plan 카운트 관리, 한국어/영어 주석 정책)을 먼저 읽고 따릅니다.
3. **이 스킬의 일반 원칙은 프로젝트 고유 컨벤션보다 후순위**입니다.

## 규칙

이 스킬이 적용하는 규칙입니다. 각 규칙의 상세 내용은 `rules/` 디렉토리를 참조하세요.

1. [모든 테스트의 정규 골격](rules/canonical-skeleton.md) — `BEGIN/ROLLBACK`, `plan(N)`, `finish()` 트랜잭션 격리 패턴
2. [assertion 함수 선택](rules/assertion-functions.md) — `is`/`isnt` 우선, 결과셋 비교 함수 선택, `throws_ok` 권고
3. [RLS 테스트 — silent failure](rules/rls-testing.md) — SELECT/INSERT/UPDATE/DELETE 4가지 정규 패턴
4. [픽스처와 헬퍼](rules/fixtures-and-helpers.md) — `as_user`, 상태 머신 헬퍼, 헬퍼 배치
5. [SECURITY DEFINER · 권한 검증](rules/security-definer-and-privileges.md) — definer/invoker, search_path, GRANT 검증
6. [스키마 검증 정규 묶음](rules/schema-verification.md) — 컬럼/PK/FK/UNIQUE/인덱스/ENUM 검증
7. [트리거 검증](rules/trigger-testing.md) — 존재·속성·동작·valid/invalid 경로
8. [diag · subtest · skip · todo](rules/diag-subtest-skip-todo.md) — TAP 출력 구조화 도구
9. [파일 조직 · 실행 순서](rules/file-organization.md) — 알파벳 순서 기반 파일 네이밍, 200줄 분할 임계치
10. [assertion description 컨벤션](rules/assertion-description-convention.md) — `[<scope>/<action>]` prefix, 영어 메시지
11. [xUnit 스타일 (선택)](rules/xunit-style.md) — `runtests()` 자동 발견, 도입 시점 판단
12. [실행 — pg_prove](rules/pg-prove-execution.md) — 명령어 옵션, CI 통합
13. [안티패턴 체크리스트](rules/antipatterns-checklist.md) — 리뷰 시 즉시 지적할 12가지 패턴

## 작업 절차

### 새 테스트 작성 요청 시

1. 대상 객체(테이블/함수/트리거/RLS) 파악.
2. [SECURITY DEFINER · 권한](rules/security-definer-and-privileges.md), [스키마 검증](rules/schema-verification.md), [트리거 검증](rules/trigger-testing.md) 중 해당 카테고리만 작성.
3. RLS 작업이면 [RLS 테스트](rules/rls-testing.md)의 4가지 패턴(SELECT/INSERT/UPDATE/DELETE)을 모두 cover.
4. 픽스처가 5줄 이상이면 [픽스처와 헬퍼](rules/fixtures-and-helpers.md) 헬퍼 카탈로그 적용 또는 신규 헬퍼 제안.
5. [안티패턴 체크리스트](rules/antipatterns-checklist.md)로 자체 리뷰.

### 기존 테스트 리뷰·리팩토링 요청 시

1. 파일 길이·라인 분포 점검 (200줄 초과 여부 — [파일 조직](rules/file-organization.md)).
2. `DO $$` 블록 / `set_auth_user`+`RESET ROLE` 페어 반복 횟수 카운트.
3. [안티패턴 체크리스트](rules/antipatterns-checklist.md)로 위반 항목 나열.
4. 헬퍼 추출 우선순위(효과·노력 매트릭스)를 제시.
5. **시연 마이그레이션 1건**으로 절감량 검증 (Before/After 라인 수).

## 결과 보고 형식

```
✅ pgtap-best-practices 적용

생성/수정:
  - postgres/.../9x-tests-<category>.sql

검증 항목:
  - schema: <columns/constraints/indexes>
  - rls: SELECT/INSERT/UPDATE/DELETE × <roles>
  - triggers: <trigger_name>
  - security: definer/search_path/privs

안티패턴 점검: ✅ 없음 / ⚠ <항목>

사용된 헬퍼: as_user, create_<entity>_in_status, ...
```

## 주의사항

- **이 스킬은 pgTAP 표준 베스트 프랙티스의 응축본**입니다. 프로젝트 고유 컨벤션(SQL 네이밍 밴드, 주석 언어, 헬퍼 위치)이 있으면 그쪽이 우선합니다.
- pgTAP 함수 시그니처는 버전(현재 1.x)에 따라 일부 차이가 있습니다. 시그니처가 동작하지 않으면 `\df+ pgtap_function`으로 실제 시그니처 확인.
- `subtest`, `policies_are`, `freeze_time` 등 일부는 pgTAP 1.0+ / 특정 헬퍼 패키지가 필요합니다. 환경 확인 후 사용.
- RLS의 silent failure는 보안 사고의 1차 원인입니다. UPDATE/DELETE는 **반드시 `is_empty(... RETURNING ...)` 패턴으로 검증**하세요.

## 참고 자료

- [pgTAP 공식 문서](https://pgtap.org/documentation.html) — 전체 함수 카탈로그
- [PgTAP Best Practices — David Wheeler (창시자)](https://www.slideshare.net/justatheory/pgtap-best-practices)
- [pgTAP Result Set Assertion Functions — Just a Theory](https://justatheory.com/2009/07/pgtap-results-eq/)
- [Testing Overview — Supabase Docs](https://supabase.com/docs/guides/local-development/testing/overview)
- [Advanced pgTAP Testing — Supabase Docs](https://supabase.com/docs/guides/local-development/testing/pgtap-extended)
- [A Guide to testing on Supabase — Basejump](https://usebasejump.com/blog/testing-on-supabase-with-pgtap)
- [supabase-test-helpers GitHub](https://github.com/usebasejump/supabase-test-helpers)
