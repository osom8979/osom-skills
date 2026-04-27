# 픽스처와 헬퍼 — 반복 패턴은 즉시 추출

가장 큰 가독성 손실은 **테스트 본문보다 길어진 픽스처 셋업**에서 온다. 다음 패턴이 한 번 이상 반복되면 즉시 헬퍼로 빼라.

## 정규 헬퍼 카탈로그

| 헬퍼                                | 시그니처            | 효과                                        |
| ----------------------------------- | ------------------- | ------------------------------------------- |
| `create_auth_user(email)`           | `RETURNS VOID/UUID` | `auth.users` insert 패턴 1줄                |
| `set_auth_user(email)`              | `RETURNS VOID`      | `SET LOCAL ROLE authenticated + JWT claims` |
| `as_user(email, sql)`               | `RETURNS VOID`      | `set_auth_user → EXECUTE → RESET ROLE` 1줄  |
| `create_<entity>(owner, name, ...)` | `RETURNS UUID/TEXT` | 도메인 엔티티 1회 호출 생성                 |
| `create_<entity>_in_status(...)`    | `RETURNS TEXT`      | 상태 머신 가진 엔티티를 특정 상태까지 chain |

## `as_user` 헬퍼 (강력 추천)

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

## 상태 머신 엔티티 헬퍼 (chain UPDATE)

전이 트리거가 있는 엔티티는 **canonical 최단 경로를 헬퍼 안에서 chain**:

```sql
CREATE FUNCTION test_helpers.set_<entity>_status(p_id UUID, p_target <enum>)
    RETURNS VOID AS $$
DECLARE v_path <enum>[]; v_step <enum>;
BEGIN
    v_path := CASE p_target
        WHEN 'state_a' THEN ARRAY['state_a']::<enum>[]
        WHEN 'state_b' THEN ARRAY['state_a', 'state_b']::<enum>[]
        -- ...
    END;
    FOREACH v_step IN ARRAY v_path LOOP
        UPDATE public.<table> SET status = v_step WHERE id = p_id;
    END LOOP;
END;
$$ LANGUAGE plpgsql;
```

이 패턴은 익명 `DO $$ ... $$` 블록을 테스트 본문에서 제거한다 — 픽스처 비중을 50%+ 줄인다.

## 헬퍼 배치

- 일반 인증·세션 헬퍼: `<schema_root>/00-test-helpers/` 아래에 모음.
- 도메인 헬퍼: 해당 도메인 디렉토리의 `80-helpers-*.sql` 밴드에 (프로젝트 컨벤션이 있다면 그 밴드를 따른다).
- 헬퍼는 `test_helpers` 등 별도 스키마. `REVOKE ALL FROM PUBLIC, anon, authenticated;` 명시.
