# SECURITY DEFINER · 권한 검증

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

## 권한

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
