# 파일 조직 · 실행 순서

`pg_prove`는 **알파벳 순서**로 파일을 실행한다.

| 패턴                                               | 효과                                                   |
| -------------------------------------------------- | ------------------------------------------------------ |
| `000-setup-tests-hooks.sql`                        | 모든 테스트 전 1회 실행되는 부트스트랩 (Supabase 권장) |
| `9x-tests-{schema,rls,triggers,function-name}.sql` | 한 디렉토리 안에서 관심사별 분리 (가장 보편)           |
| `XX-feature-being-tested.sql`                      | flat 디렉토리(예: `supabase/tests/`)에서 순서 강제     |

**테스트 파일 분할 임계치**: 200줄. 초과 시 (a) fixture를 헬퍼로 추출하거나 (b) 더 좁은 카테고리로 분할.

```
03-orgs/00-orgs/
  90-tests-schema.sql      ← 컬럼/제약/존재 여부
  91-tests-rls.sql         ← Row-level security
  92-tests-triggers.sql    ← 트리거 동작
  93-tests-functions.sql   ← 도메인 함수
```
