# diag · subtest · skip · todo

## `diag(text)`

TAP 출력에 `# ...` 코멘트 라인을 끼워 카테고리 시각 구분:

```sql
SELECT diag('--- RLS: SELECT ---');
SELECT is(...);
SELECT is(...);
SELECT diag('--- RLS: INSERT ---');
SELECT throws_ok(...);
```

## `subtest(sql, name)` (pgTAP 1.0+)

그룹 안에서 별도 plan을 가지며 외부에선 1라인 결과로 묶임:

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

## `skip(reason, count)`

환경 미충족 시 건너뜀.

## `todo(reason, count)` / `todo_start()` / `todo_end()`

알려진 결함을 표기 — 실패해도 플래그만.
