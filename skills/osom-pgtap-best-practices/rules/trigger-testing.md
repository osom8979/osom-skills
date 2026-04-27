# 트리거 검증

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
