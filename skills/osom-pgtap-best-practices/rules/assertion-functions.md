# assertion 함수 선택 — 권위 가이드

David Wheeler의 명시적 권고: **`is()` / `isnt()`를 `ok()`보다 우선 사용**. 실패 시 expected/actual 양쪽을 진단으로 출력하기 때문.

## 비교 함수 우선순위

| 상황         | 1순위                          | 비고                                    |
| ------------ | ------------------------------ | --------------------------------------- |
| 두 값 동일성 | `is(have, want, msg)`          | `IS NOT DISTINCT FROM` 사용 — NULL-safe |
| 두 값 다름   | `isnt(have, want, msg)`        | NULL-safe                               |
| 단순 boolean | `ok(condition, msg)`           | 위 둘이 안 될 때만                      |
| 정규식       | `matches(have, regex, msg)`    |                                         |
| LIKE 패턴    | `alike(have, pattern, msg)`    |                                         |
| 임의 연산자  | `cmp_ok(have, '>', want, msg)` | `>`, `<=`, `@>` 등                      |
| 타입 검증    | `isa_ok(value, regtype, name)` | `pg_typeof()` 직접 비교보다 깔끔        |

## 예외 검증

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

## 결과셋 비교 — 가장 흔한 실수 영역

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

또한 결과셋 함수의 인자는 **statement (SELECT/VALUES/PREPARE 이름)** 여야 한다. 표현식 `(ROW ...)`은 단일 컬럼 텍스트로 변환되어 비교가 어긋난다.

```sql
-- 권장: PREPARE 로 의도 명확화
PREPARE expected AS VALUES (1, 'alice'::text), (2, 'bob'::text);
SELECT set_eq($$SELECT id, name FROM users$$, 'expected');
```
