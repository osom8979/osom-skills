# xUnit 스타일 (선택)

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

`pg_prove -d <db> --runtests`로 외부에서도 실행 가능.

**언제 도입하나**: 픽스처 재사용이 많고 동일 setup을 여러 테스트가 공유할 때. 단순 SQL 스크립트가 명료하면 도입 자제.
