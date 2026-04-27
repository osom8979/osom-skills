# 모든 테스트의 정규 골격

pgTAP 공식이 제시하는 트랜잭션 격리 패턴 — **위반 금지**.

```sql
BEGIN;

SELECT plan(N);                 -- 또는 SELECT * FROM no_plan();

-- 픽스처
-- assertion ...

SELECT * FROM finish();          -- 결과 집계
ROLLBACK;                        -- 모든 변경 폐기 (테스트 간 격리 보장)
```

## 원칙

- `BEGIN;` … `ROLLBACK;` **외부에 어떤 SQL도 두지 않는다** — side effect가 DB에 남는다.
- `plan(N)`을 선언했으면 **정확히 N개의 assertion이 실행되어야 한다**. 카운트 관리가 부담이면 신뢰성보다 유지보수성을 택해 `no_plan()`으로 전환하되, **보안에 민감한 모듈(RLS, SECURITY DEFINER 검증)은 `plan(N)` 유지**.
- `finish()`는 반드시 호출. 누락 시 TAP 출력이 종결되지 않는다.
