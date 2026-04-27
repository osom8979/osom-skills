# RLS 테스트 — silent failure가 핵심 함정

**가장 권위 있는 단일 인사이트** (Basejump):

> "RLS 정책은 조용히 실패한다. 에러를 던지지 않고 행을 필터링하거나 0개 영향만 미친다."

| 작업   | RLS 차단 시 거동                                             | 검증 방법                                           |
| ------ | ------------------------------------------------------------ | --------------------------------------------------- |
| INSERT | **에러 발생** (`new row violates row-level security policy`) | `throws_ok`                                         |
| UPDATE | **조용히 0행 영향**                                          | `is_empty(... RETURNING ...)` 또는 select-back 비교 |
| DELETE | **조용히 0행 영향**                                          | `is_empty(... RETURNING 1)`                         |
| SELECT | **조용히 0행 반환**                                          | `COUNT(*)` 또는 `is_empty`                          |

## 4가지 정규 패턴

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

## RLS 활성화 자체 검증

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

## anon 차단

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
