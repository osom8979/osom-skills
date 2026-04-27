# 안티패턴 체크리스트 — 리뷰 시 즉시 지적

- ❌ `BEGIN;` / `ROLLBACK;` 없이 작성된 테스트 → DB 오염
- ❌ `plan(N)` 누락 또는 `finish()` 누락 → TAP 미종결
- ❌ `ok(a = b, msg)` 형태 → `is(a, b, msg)`로 교체 (실패 진단 ↑)
- ❌ `results_eq` + ORDER BY 없음 → flaky
- ❌ 결과셋 함수에 `(ROW(...))` 표현식 전달 → text 변환되어 어긋남
- ❌ UPDATE/DELETE의 RLS 차단을 `throws_ok`로 검증 → 통과해도 무의미 (silent failure)
- ❌ `throws_ok`에서 errmsg만 매칭, SQLSTATE 누락 → i18n/버전 깨짐
- ❌ 익명 `DO $$ ... $$` 블록을 fixture로 반복 → 헬퍼로 추출
- ❌ 같은 `set_auth_user / RESET ROLE` 페어가 5회 이상 반복 → `as_user()` 도입
- ❌ assertion description이 한글이거나 비일관 → 영어·prefix 통일
- ❌ 200줄 넘는 테스트 파일 → 카테고리 분할 또는 헬퍼 추출
- ❌ SECURITY DEFINER 함수에 `SET search_path = ''` 미적용 → 보안 검증 실패
