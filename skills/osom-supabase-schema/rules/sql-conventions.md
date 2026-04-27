# SQL 컨벤션

프로젝트의 `conventions` 문서를 따릅니다. 일반적 권장 사항:

- **스키마 명시**: `public.` 접두사 사용
- **함수 인자 프리픽스**: `p_` 접두사 사용 (예: `p_user_id`)
- **RLS 정책 네이밍**: `<table>.<role>.<operation>` 같은 일관된 포맷
- **권한 관리**: `REVOKE ALL FROM anon, authenticated;` 후 필요한 권한만 `GRANT`
- **`SECURITY DEFINER` 함수**: `SET search_path = ''` **필수** (search_path 인젝션 방지)
- **테스트**: 각 디렉토리에 pgTAP 테스트 파일 포함

프로젝트 고유 컨벤션이 있으면 그쪽이 우선합니다.
