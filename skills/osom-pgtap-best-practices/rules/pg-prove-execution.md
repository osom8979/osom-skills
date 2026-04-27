# 실행 — pg_prove

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

## CI 통합 (Supabase 공식 워크플로)

```yaml
- name: Start Supabase
  run: supabase start
- name: Run Tests
  run: supabase test db
```
