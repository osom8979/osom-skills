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

---

## 1. 모든 테스트의 정규 골격

pgTAP 공식이 제시하는 트랜잭션 격리 패턴 — 위반 금지:

```sql
BEGIN;

SELECT plan(N);                 -- 또는 SELECT * FROM no_plan();

-- 픽스처
-- assertion ...

SELECT * FROM finish();          -- 결과 집계
ROLLBACK;                        -- 모든 변경 폐기 (테스트 간 격리 보장)
```

**원칙**:

- `BEGIN;` … `ROLLBACK;` 외부에 어떤 SQL도 두지 않는다. side effect가 DB에 남는다.
- `plan(N)`을 선언했으면 정확히 N개의 assertion이 실행되어야 한다. 카운트 관리가 부담이면 **신뢰성보다 유지보수성을 택해 `no_plan()`** 으로 전환하되, 보안에 민감한 모듈(RLS, SECURITY DEFINER 검증)은 `plan(N)` 유지.
- `finish()`는 반드시 호출. 누락 시 TAP 출력이 종결되지 않는다.

## 2. assertion 함수 선택 — 권위 가이드

David Wheeler의 명시적 권고: **`is()` / `isnt()`를 `ok()`보다 우선 사용**. 실패 시 expected/actual 양쪽을 진단으로 출력하기 때문.

### 비교 함수 우선순위

| 상황         | 1순위                          | 비고                                    |
| ------------ | ------------------------------ | --------------------------------------- |
| 두 값 동일성 | `is(have, want, msg)`          | `IS NOT DISTINCT FROM` 사용 — NULL-safe |
| 두 값 다름   | `isnt(have, want, msg)`        | NULL-safe                               |
| 단순 boolean | `ok(condition, msg)`           | 위 둘이 안 될 때만                      |
| 정규식       | `matches(have, regex, msg)`    |                                         |
| LIKE 패턴    | `alike(have, pattern, msg)`    |                                         |
| 임의 연산자  | `cmp_ok(have, '>', want, msg)` | `>`, `<=`, `@>` 등                      |
| 타입 검증    | `isa_ok(value, regtype, name)` | `pg_typeof()` 직접 비교보다 깔끔        |

### 예외 검증

```sql
-- errcode + errmsg 둘 다 검증 (가장 엄격)
SELECT throws_ok(
    $$INSERT INTO orgs (name) VALUES (NULL)$$,
    '23502',                     -- not_null_violation
    'null value in column "name"',
    'NULL name should be rejected'
);

-- 정규식 매칭 (errcode를 모를 때)
SELECT throws_matching($$...$$, 'permission denied for .+', 'msg');

-- 발생 안 함 검증
SELECT lives_ok($$UPDATE orgs SET name = 'X' WHERE id = ...$$, 'should succeed');
```

**원칙**: `throws_ok`는 **5자리 SQLSTATE를 알면 반드시 명시**. 메시지만 매칭하면 i18n·버전 변경에 깨진다.

### 결과셋 비교 — 가장 흔한 실수 영역

| 함수                   | 의미                                       | 사용 시점                    |
| ---------------------- | ------------------------------------------ | ---------------------------- |
| `results_eq(a, b)`     | **순서·중복까지 동일** (cursor row-by-row) | 정렬이 의미 있는 경우만      |
| `set_eq(a, b)`         | 같은 행들의 집합 (순서·중복 무시)          | "동일한 데이터가 들어있는가" |
| `bag_eq(a, b)`         | 순서 무시, **중복 횟수는 일치**            | 집계 결과 검증               |
| `is_empty(sql, msg)`   | 0행 반환                                   | RLS 차단·삭제 검증           |
| `isnt_empty(sql, msg)` | 1행 이상                                   |                              |
| `set_has`, `bag_has`   | 부분 집합                                  |                              |
| `row_eq(sql, record)`  | 단일 행이 record 일치                      |                              |

**Wheeler의 경고**: `results_eq`는 **항상 ORDER BY로 완전 정렬**해야 한다 — 정렬 없이 쓰면 PostgreSQL의 row 순서가 바뀌어 flaky해진다. 정렬이 무의미하면 `set_eq`/`bag_eq` 사용.

```sql
-- BAD — 순서가 보장되지 않음
SELECT results_eq(
    $$SELECT id, name FROM users WHERE active$$,
    $$VALUES (1, 'a'), (2, 'b')$$
);

-- GOOD — 명시적 정렬
SELECT results_eq(
    $$SELECT id, name FROM users WHERE active ORDER BY id$$,
    $$VALUES (1, 'a'::text), (2, 'b'::text)$$
);

-- 또는 순서 무관시
SELECT set_eq(
    $$SELECT id, name FROM users WHERE active$$,
    $$VALUES (1, 'a'::text), (2, 'b'::text)$$
);
```

또한 결과셋 함수의 인자는 **statement (SELECT/VALUES/PREPARE 이름)** 여야 한다. 표현식 `(ROW ...)` 은 단일 컬럼 텍스트로 변환되어 비교가 어긋난다.

```sql
-- 권장: PREPARE 로 의도 명확화
PREPARE expected AS VALUES (1, 'alice'::text), (2, 'bob'::text);
SELECT set_eq($$SELECT id, name FROM users$$, 'expected');
```

---

## 3. RLS 테스트 — silent failure가 핵심 함정

**가장 권위 있는 단일 인사이트** (Basejump):

> "RLS 정책은 조용히 실패한다. 에러를 던지지 않고 행을 필터링하거나 0개 영향만 미친다."

| 작업   | RLS 차단 시 거동                                             | 검증 방법                                           |
| ------ | ------------------------------------------------------------ | --------------------------------------------------- |
| INSERT | **에러 발생** (`new row violates row-level security policy`) | `throws_ok`                                         |
| UPDATE | **조용히 0행 영향**                                          | `is_empty(... RETURNING ...)` 또는 select-back 비교 |
| DELETE | **조용히 0행 영향**                                          | `is_empty(... RETURNING 1)`                         |
| SELECT | **조용히 0행 반환**                                          | `COUNT(*)` 또는 `is_empty`                          |

### 4가지 정규 패턴

```sql
-- (1) SELECT 차단 검증
SET LOCAL ROLE authenticated;
SET LOCAL request.jwt.claims = '{"sub": "outsider-uuid"}';
SELECT is(
    (SELECT COUNT(*) FROM public.posts WHERE author_id = $owner)::INTEGER, 0,
    'outsider cannot SELECT posts'
);

-- (2) INSERT 차단 검증 — 에러를 던짐
SELECT throws_ok(
    $$INSERT INTO public.posts (author_id, body) VALUES ('...', 'x')$$,
    'new row violates row-level security policy for table "posts"',
    'outsider cannot INSERT'
);

-- (3) UPDATE silent failure 검증 — RETURNING + is_empty 가 가장 강력
SELECT is_empty(
    $$UPDATE public.posts SET body = 'hacked'
      WHERE id = '...' RETURNING 1$$,
    'outsider UPDATE returns no rows (silently blocked)'
);

-- (4) UPDATE 보조 검증 — 작업 후 select-back 으로 변경 무효 확인
UPDATE public.posts SET body = 'hacked' WHERE id = '...';
RESET ROLE;
SELECT is(
    (SELECT body FROM public.posts WHERE id = '...'),
    'original',
    'body unchanged after blocked UPDATE'
);
```

### RLS 활성화 자체 검증

테이블에 RLS를 켜는 것을 잊는 사고를 막는 1차 방어선:

```sql
-- 직접 pg_class 조회
SELECT ok(
    (SELECT relrowsecurity FROM pg_class
     WHERE oid = 'public.posts'::regclass),
    'posts has RLS enabled'
);

-- Supabase/Basejump 환경이면 한 줄로 스키마 전수 검사
-- SELECT tests.rls_enabled('public');
```

### anon 차단

`SET LOCAL ROLE anon;` 후 모든 테이블 접근이 차단되는지 확인. `REVOKE ALL FROM anon` 정책이라면 `permission denied for table` 에러로 끝난다 (RLS 평가 전에 거부됨).

```sql
SET LOCAL ROLE anon;
SELECT throws_ok(
    $$SELECT * FROM public.posts$$,
    '42501', 'permission denied for table posts',
    'anon cannot access posts'
);
RESET ROLE;
```

---

## 4. 픽스처와 헬퍼 — 반복 패턴은 즉시 추출

가장 큰 가독성 손실은 **테스트 본문보다 길어진 픽스처 셋업**에서 온다. 다음 패턴이 한 번 이상 반복되면 즉시 헬퍼로 빼라.

### 정규 헬퍼 카탈로그

| 헬퍼                                | 시그니처            | 효과                                        |
| ----------------------------------- | ------------------- | ------------------------------------------- |
| `create_auth_user(email)`           | `RETURNS VOID/UUID` | `auth.users` insert 패턴 1줄                |
| `set_auth_user(email)`              | `RETURNS VOID`      | `SET LOCAL ROLE authenticated + JWT claims` |
| `as_user(email, sql)`               | `RETURNS VOID`      | `set_auth_user → EXECUTE → RESET ROLE` 1줄  |
| `create_<entity>(owner, name, ...)` | `RETURNS UUID/TEXT` | 도메인 엔티티 1회 호출 생성                 |
| `create_<entity>_in_status(...)`    | `RETURNS TEXT`      | 상태 머신 가진 엔티티를 특정 상태까지 chain |

### `as_user` 헬퍼 (강력 추천)

set/action/RESET 3줄 노이즈를 1줄로 압축:

```sql
CREATE OR REPLACE FUNCTION test_helpers.as_user(p_email TEXT, p_sql TEXT)
    RETURNS VOID
    LANGUAGE plpgsql
    SET search_path = ''
AS $$
BEGIN
    PERFORM test_helpers.set_auth_user(p_email);
    EXECUTE p_sql;
    EXECUTE 'RESET ROLE';
END;
$$;
```

**주의**: `EXECUTE p_sql`이 throw하면 RESET이 실행 안 된다. `throws_ok` 검증에는 적합하지 않다 — 그 경우 `set_auth_user/RESET` 직접 사용.

### 상태 머신 엔티티 헬퍼 (chain UPDATE)

전이 트리거가 있는 엔티티는 **canonical 최단 경로를 헬퍼 안에서 chain**:

```sql
CREATE FUNCTION test_helpers.set_<entity>_status(p_id UUID, p_target <enum>)
    RETURNS VOID AS $$
DECLARE v_path <enum>[]; v_step <enum>;
BEGIN
    v_path := CASE p_target
        WHEN 'state_a' THEN ARRAY['state_a']::<enum>[]
        WHEN 'state_b' THEN ARRAY['state_a', 'state_b']::<enum>[]
        ...
    END;
    FOREACH v_step IN ARRAY v_path LOOP
        UPDATE public.<table> SET status = v_step WHERE id = p_id;
    END LOOP;
END;
$$ LANGUAGE plpgsql;
```

이 패턴은 익명 `DO $$ ... $$` 블록을 테스트 본문에서 제거한다 — 픽스처 비중을 50%+ 줄인다.

### 헬퍼 배치

- 일반 인증·세션 헬퍼: `<schema_root>/00-test-helpers/` 아래에 모음.
- 도메인 헬퍼: 해당 도메인 디렉토리의 `80-helpers-*.sql` 밴드에 (프로젝트 컨벤션이 있다면 그 밴드를 따른다).
- 헬퍼는 `test_helpers` 등 별도 스키마. `REVOKE ALL FROM PUBLIC, anon, authenticated;` 명시.

---

## 5. SECURITY DEFINER · 권한 검증

도메인 함수의 보안 속성은 항상 명시 검증:

```sql
-- 함수 존재
SELECT has_function('public', 'create_org', ARRAY['text'],
    'create_org(text) should exist');

-- SECURITY DEFINER (DEFINER 의도면)
SELECT is_definer('public', 'create_org', ARRAY['text'],
    'create_org should be SECURITY DEFINER');

-- 또는 SECURITY INVOKER
SELECT isnt_definer('public', 'pure_query', ARRAY['uuid'],
    'pure_query should be SECURITY INVOKER');

-- volatility (immutable/stable/volatile)
SELECT volatility_is('public', 'slug_to_uuid', ARRAY['text'], 'immutable',
    'slug_to_uuid should be IMMUTABLE');

-- search_path 격리 (SECURITY DEFINER 안전성 핵심)
-- 직접 검증 함수는 없으므로 pg_proc 조회로:
SELECT ok(
    (SELECT 'search_path=' = ANY(proconfig)
     FROM pg_proc WHERE oid = 'public.create_org(text)'::regprocedure),
    'create_org pins search_path'
);
```

권한:

```sql
-- function GRANT 검증
SELECT function_privs_are('public', 'create_org', ARRAY['text'],
    'authenticated', ARRAY['EXECUTE'],
    'authenticated should have EXECUTE on create_org');

-- table GRANT 검증
SELECT table_privs_are('public', 'orgs', 'authenticated',
    ARRAY['SELECT', 'UPDATE'],
    'authenticated should have SELECT, UPDATE on orgs');

-- 컬럼 단위
SELECT column_privs_are('public', 'agent_internals', 'auth_token_hash',
    'authenticated', ARRAY[]::TEXT[],
    'authenticated must NOT have any privilege on auth_token_hash');
```

---

## 6. 스키마 검증 정규 묶음

테이블이 만들어졌는지보다 **컬럼 타입·NULL·DEFAULT·제약·인덱스**까지 단단히 잠그는 게 회귀 방지에 효과적:

```sql
-- 컬럼 존재 + 타입
SELECT has_column('public', 'orgs', 'name', 'orgs.name exists');
SELECT col_type_is('public', 'orgs', 'name', 'text', 'orgs.name is text');
SELECT col_not_null('public', 'orgs', 'name', 'orgs.name is NOT NULL');
SELECT col_has_default('public', 'orgs', 'created_at', 'orgs.created_at has default');
SELECT col_default_is('public', 'orgs', 'created_at', 'now()',
    'default is now()');

-- PK / FK
SELECT col_is_pk('public', 'orgs', 'id', 'orgs.id is PK');
SELECT fk_ok('public', 'org_members', ARRAY['org_id'],
              'public', 'orgs',         ARRAY['id'],
    'org_members.org_id references orgs.id');

-- UNIQUE / CHECK
SELECT col_is_unique('public', 'orgs', 'slug', 'orgs.slug is UNIQUE');
SELECT has_check('public', 'orgs', 'orgs has CHECK constraint');

-- 인덱스
SELECT has_index('public', 'orgs', 'orgs_slug_idx', ARRAY['slug'],
    'orgs has index on slug');
SELECT index_is_unique('public', 'orgs_slug_idx', 'index is UNIQUE');

-- ENUM
SELECT enum_has_labels('public', 'role',
    ARRAY['owner', 'maintainer', 'developer', 'viewer'],
    'role enum has expected labels');
```

전수 검사용 `*_are`는 **드리프트 검출에 유용**하지만 새 컬럼 추가 시마다 깨지므로, 핵심 도메인 객체에만 한정:

```sql
SELECT columns_are('public', 'orgs', ARRAY['id','slug','name','created_at','updated_at'],
    'orgs columns frozen');
```

---

## 7. 트리거 검증

```sql
-- 트리거 존재 + 속성
SELECT has_trigger('public', 'orgs', 'on_orgs_updated', 'updated_at trigger exists');
SELECT trigger_is('public', 'orgs', 'on_orgs_updated',
    'public', 'set_updated_at', 'updated_at trigger fires correct function');

-- 트리거 동작 (실제 INSERT/UPDATE 후 결과 검증)
INSERT INTO public.orgs (name) VALUES ('A') RETURNING created_at;
UPDATE public.orgs SET name = 'B' WHERE name = 'A';

SELECT cmp_ok(
    (SELECT updated_at FROM public.orgs WHERE name = 'B'),
    '>',
    (SELECT created_at FROM public.orgs WHERE name = 'B'),
    'on_orgs_updated bumps updated_at on UPDATE'
);
```

상태 전이 트리거는 **valid 경로 + invalid 경로** 양쪽 모두 검증:

```sql
-- valid: pending -> active
SELECT lives_ok($$UPDATE entities SET status = 'active' WHERE id = $1$$, 'valid transition');

-- invalid: pending -> stopped (skipping active)
SELECT throws_ok(
    $$UPDATE entities SET status = 'stopped' WHERE id = $1$$,
    'check_violation',          -- SQLSTATE
    'Invalid status transition: pending -> stopped',
    'invalid transition rejected'
);
```

---

## 8. diag · subtest · skip · todo

**`diag(text)`**: TAP 출력에 `# ...` 코멘트 라인을 끼워 카테고리 시각 구분:

```sql
SELECT diag('--- RLS: SELECT ---');
SELECT is(...);
SELECT is(...);
SELECT diag('--- RLS: INSERT ---');
SELECT throws_ok(...);
```

**`subtest(sql, name)`** (pgTAP 1.0+): 그룹 안에서 별도 plan을 가지며 외부에선 1라인 결과로 묶임:

```sql
SELECT plan(2);
SELECT subtest($$
    SELECT plan(2);
    SELECT is(...);
    SELECT is(...);
    SELECT finish();
$$, 'RLS — projects table');
SELECT subtest($$ ... $$, 'RLS — project_members table');
```

`pg_prove` 출력에서 `ok 1 - RLS — projects table`처럼 카테고리 자체가 1줄 결과가 된다. 단, 내부 plan을 따로 관리해야 하므로 **케이스가 많은 RLS 파일에 한정**.

**`skip(reason, count)`**: 환경 미충족 시 건너뜀.
**`todo(reason, count)` / `todo_start()` / `todo_end()`**: 알려진 결함을 표기 — 실패해도 플래그만.

---

## 9. 파일 조직 · 실행 순서

`pg_prove`는 **알파벳 순서**로 파일을 실행한다.

| 패턴                                               | 효과                                                   |
| -------------------------------------------------- | ------------------------------------------------------ |
| `000-setup-tests-hooks.sql`                        | 모든 테스트 전 1회 실행되는 부트스트랩 (Supabase 권장) |
| `9x-tests-{schema,rls,triggers,function-name}.sql` | 한 디렉토리 안에서 관심사별 분리 (가장 보편)           |
| `XX-feature-being-tested.sql`                      | flat 디렉토리(예: `supabase/tests/`)에서 순서 강제     |

**테스트 파일 분할 임계치**: 200줄. 초과 시 (a) fixture를 헬퍼로 추출하거나 (b) 더 좁은 카테고리로 분할.

```
03-orgs/00-orgs/
  90-tests-schema.sql      ← 컬럼/제약/존재 여부
  91-tests-rls.sql         ← Row-level security
  92-tests-triggers.sql    ← 트리거 동작
  93-tests-functions.sql   ← 도메인 함수
```

---

## 10. assertion description 컨벤션

David Wheeler: "test description은 검색 가능하게 하라." 권장 prefix:

```
[<scope>/<action>] <expected behavior>
```

예:

```
'[orgs/SELECT] member can view org'
'[orgs/INSERT] outsider blocked'
'[org_role/owner] returns owner role'
'[trigger/sole-owner] cannot remove last owner'
'[create_org/auth] anon cannot call'
```

`grep '\[orgs/'` 로 카테고리 필터링이 가능해지고, 실패 메시지에서 위치 추적이 즉각.

테스트 description 언어:

- **assertion message는 영어** (TAP 출력 도구·로그 호환).
- SQL 주석(`--`)은 프로젝트 정책에 따라 한국어 가능.

---

## 11. xUnit 스타일 (선택)

대규모 프로젝트에서 SQL 스크립트가 너무 많아지면 함수 기반 xUnit 스타일 도입 가능. `setup_*` / `test_*` / `teardown_*` 네이밍을 준수하면 `runtests()`가 자동 발견:

```sql
CREATE FUNCTION setup_users() RETURNS SETOF TEXT AS $$
BEGIN
    INSERT INTO users (nick) VALUES ('alice');
    RETURN NEXT pass('users seeded');
END;
$$ LANGUAGE plpgsql;

CREATE FUNCTION test_user_count() RETURNS SETOF TEXT AS $$
    SELECT is(COUNT(*)::INTEGER, 1, 'one user') FROM users;
$$ LANGUAGE sql;

-- 실행 — 각 함수가 자체 트랜잭션에서 실행되고 롤백됨
SELECT * FROM runtests();
```

`pg_prove -d <db> --runtests` 로 외부에서도 실행 가능.

**언제 도입하나**: 픽스처 재사용이 많고 동일 setup을 여러 테스트가 공유할 때. 단순 SQL 스크립트가 명료하면 도입 자제.

---

## 12. 실행 — pg_prove

```bash
# 기본
pg_prove -U postgres -d mydb sql/*.sql

# verbose (TAP 라인을 그대로 출력 + diag)
pg_prove -v -U postgres -d mydb sql/*.sql

# 환경변수: PGDATABASE, PGHOST, PGPORT, PGUSER 사용
pg_prove sql/*.sql

# xUnit 함수 자동 실행
pg_prove -d mydb --runtests

# 재귀 + 타이밍
pg_prove -r --timer postgres/
```

CI 통합 (Supabase 공식 워크플로):

```yaml
- name: Start Supabase
  run: supabase start
- name: Run Tests
  run: supabase test db
```

---

## 13. 안티패턴 체크리스트 — 리뷰 시 즉시 지적

- ❌ `BEGIN;` / `ROLLBACK;` 없이 작성된 테스트 → DB 오염
- ❌ `plan(N)` 누락 또는 `finish()` 누락 → TAP 미종결
- ❌ `ok(a = b, msg)` 형태 → `is(a, b, msg)`로 교체 (실패 진단 ↑)
- ❌ `results_eq` + ORDER BY 없음 → flaky
- ❌ 결과셋 함수에 `(ROW(...))` 표현식 전달 → text 변환되어 어긋남
- ❌ UPDATE/DELETE의 RLS 차단을 `throws_ok`로 검증 → 통과해도 무의미 (silent failure)
- ❌ `throws_ok`에서 errmsg만 매칭, SQLSTATE 누락 → i18n/버전 깨짐
- ❌ 익명 `DO $$ ... $$` 블록을 fixture로 반복 → 헬퍼로 추출
- ❌ 같은 `set_auth_user / RESET ROLE` 페어가 5회 이상 반복 → `as_user()` 도입
- ❌ assertion description이 한글이거나 비일관 → 영어·prefix 통일
- ❌ 200줄 넘는 테스트 파일 → 카테고리 분할 또는 헬퍼 추출
- ❌ SECURITY DEFINER 함수에 `SET search_path = ''` 미적용 → 보안 검증 실패

---

## 14. 작업 절차

### 새 테스트 작성 요청 시

1. 대상 객체(테이블/함수/트리거/RLS) 파악.
2. **§5~7** 검증 묶음 중 해당 카테고리만 작성.
3. RLS 작업이면 §3의 4가지 패턴(SELECT/INSERT/UPDATE/DELETE)을 모두 cover.
4. 픽스처가 5줄 이상이면 §4의 헬퍼 카탈로그 적용 또는 신규 헬퍼 제안.
5. 안티패턴 체크리스트(§13)로 자체 리뷰.

### 기존 테스트 리뷰·리팩토링 요청 시

1. 파일 길이·라인 분포 점검 (200줄 초과 여부).
2. `DO $$` 블록 / `set_auth_user`+`RESET ROLE` 페어 반복 횟수 카운트.
3. 안티패턴 체크리스트로 위반 항목 나열.
4. 헬퍼 추출 우선순위(효과·노력 매트릭스)를 제시.
5. **시연 마이그레이션 1건**으로 절감량 검증 (Before/After 라인 수).

### 결과 보고 형식

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

---

## 주의사항

- **이 스킬은 pgTAP 표준 베스트 프랙티스의 응축본**입니다. 프로젝트 고유 컨벤션(SQL 네이밍 밴드, 주석 언어, 헬퍼 위치)이 있으면 그쪽이 우선합니다.
- pgTAP 함수 시그니처는 버전(현재 1.x)에 따라 일부 차이가 있습니다. 시그니처가 동작하지 않으면 `\df+ pgtap_function` 으로 실제 시그니처 확인.
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
