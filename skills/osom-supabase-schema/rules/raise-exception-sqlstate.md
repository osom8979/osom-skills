# RAISE EXCEPTION SQLSTATE 컨벤션

RPC 가 던지는 예외는 **카테고리별로 표준 SQLSTATE 를 명시**해 호출자(프론트/Worker)가
메시지 파싱 없이 분기할 수 있게 한다.

`USING ERRCODE` 값은 항상 PostgreSQL 표준 **condition name (문자열)** 형식을 사용한다.
5자리 SQLSTATE 코드(`'42501'`)는 가독성이 떨어지므로 쓰지 않는다.

```sql
-- O — condition name
RAISE EXCEPTION 'Only project annotators can update annotations'
    USING ERRCODE = 'insufficient_privilege';

-- X — 5자리 코드
RAISE EXCEPTION 'Only project annotators can update annotations'
    USING ERRCODE = '42501';
```

## 프로젝트 표준 카테고리

| 카테고리       | SQLSTATE | 표준 condition name            | 메시지 예시                                          |
| -------------- | -------- | ------------------------------ | ---------------------------------------------------- |
| 권한/role 거부 | `42501`  | `insufficient_privilege`       | `Only project annotators can ...`, `Only admins ...` |
| 입력 검증 실패 | `22023`  | `invalid_parameter_value`      | `Invalid email format: ...`, `Invalid JSON shape`    |
| OCC 충돌       | `P0004`  | `assert_failure`               | `annotations.conflict`                               |
| 쿼터 초과      | `53400`  | `configuration_limit_exceeded` | `org_quota_exceeded`                                 |

기타(not-found, 상태 전이 거부, sole-owner 보호 등)는 명시하지 않으면 기본 `P0001`
(`raise_exception`) 으로 매핑된다.

## 자주 사용하는 SQLSTATE 참고표

PostgreSQL 표준 condition name 중 PL/pgSQL `RAISE EXCEPTION` 에서 자주 쓰이는 것들.
프로젝트 표준 카테고리에 해당하지 않는 케이스에서 의도를 정확히 표현해야 할 때 참고한다.

### Class 22 — Data Exception (입력/데이터 형식 오류)

| SQLSTATE | condition name                  | 용도                              |
| -------- | ------------------------------- | --------------------------------- |
| `22023`  | `invalid_parameter_value`       | **프로젝트 표준 — 입력 검증 실패** |
| `22P02`  | `invalid_text_representation`   | 문자열을 enum/UUID 등으로 캐스팅 실패 |
| `22001`  | `string_data_right_truncation`  | 문자열 길이 초과                   |
| `22003`  | `numeric_value_out_of_range`    | 숫자 범위 초과                     |
| `22012`  | `division_by_zero`              | 0으로 나누기                       |
| `2201B`  | `invalid_regular_expression`    | 정규식 형식 오류                   |

### Class 23 — Integrity Constraint Violation (제약 위반)

| SQLSTATE | condition name                   | 용도                          |
| -------- | -------------------------------- | ----------------------------- |
| `23505`  | `unique_violation`               | UNIQUE 제약 위반              |
| `23503`  | `foreign_key_violation`          | FK 제약 위반                  |
| `23514`  | `check_violation`                | CHECK 제약 위반               |
| `23502`  | `not_null_violation`             | NOT NULL 위반                 |
| `23P01`  | `exclusion_violation`            | EXCLUSION 제약 위반           |

> 일반적으로 RPC 본문에서 명시적으로 던지기보다는 **DB 엔진이 자동으로 던지는 코드**.
> `EXCEPTION WHEN unique_violation THEN ...` 형태로 catch 할 때 주로 사용한다.

### Class 25 — Invalid Transaction State

| SQLSTATE | condition name                  | 용도                          |
| -------- | ------------------------------- | ----------------------------- |
| `25006`  | `read_only_sql_transaction`     | read-only 트랜잭션에서 mutate 시도 |
| `25P02`  | `in_failed_sql_transaction`     | 실패한 트랜잭션에서 추가 명령  |

### Class 40 — Transaction Rollback (동시성 충돌)

| SQLSTATE | condition name              | 용도                                  |
| -------- | --------------------------- | ------------------------------------- |
| `40001`  | `serialization_failure`     | 직렬화 충돌 — 재시도 권장             |
| `40P01`  | `deadlock_detected`         | 데드락 감지                           |

### Class 42 — Syntax Error or Access Rule Violation

| SQLSTATE | condition name              | 용도                                  |
| -------- | --------------------------- | ------------------------------------- |
| `42501`  | `insufficient_privilege`    | **프로젝트 표준 — 권한/role 거부**     |

### Class 53 — Insufficient Resources

| SQLSTATE | condition name                  | 용도                                      |
| -------- | ------------------------------- | ----------------------------------------- |
| `53400`  | `configuration_limit_exceeded`  | **프로젝트 표준 — 쿼터/한도 초과**         |
| `53300`  | `too_many_connections`          | 커넥션 한도 초과                          |

### Class 55 — Object Not In Prerequisite State

| SQLSTATE | condition name                       | 용도                                  |
| -------- | ------------------------------------ | ------------------------------------- |
| `55006`  | `object_in_use`                      | 다른 세션이 점유 중                   |
| `55P03`  | `lock_not_available`                 | `NOWAIT` 잠금 획득 실패               |

### Class P0 — PL/pgSQL Error

| SQLSTATE | condition name        | 용도                                              |
| -------- | --------------------- | ------------------------------------------------- |
| `P0001`  | `raise_exception`     | 기본값 — `USING ERRCODE` 미지정 시 자동 매핑      |
| `P0002`  | `no_data_found`       | `SELECT ... INTO STRICT` 결과 없음                |
| `P0003`  | `too_many_rows`       | `SELECT ... INTO STRICT` 결과 2건 이상            |
| `P0004`  | `assert_failure`      | **프로젝트 표준 — OCC 충돌**, `ASSERT` 실패       |

## 호출자 측 분기 예시

```ts
// 클라이언트에서 SQLSTATE 로 분기
const { data, error } = await supabase.rpc('update_annotation', { ... });
if (error?.code === '42501') {
  // 권한 거부 — 권한 안내 UI
} else if (error?.code === 'P0004') {
  // OCC 충돌 — 최신 데이터 다시 fetch 후 재시도
} else if (error?.code === '22023') {
  // 입력 검증 실패 — 폼 에러 표시
}
```

> Supabase 클라이언트는 `error.code` 에 5자리 SQLSTATE 를 담는다.
> PL/pgSQL 측에서 condition name 으로 던져도 자동으로 SQLSTATE 로 변환되므로
> 클라이언트는 5자리 코드로 비교한다.
