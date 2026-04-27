# 문서 동기화 검증

변경된 파일이 속한 디렉토리의 규칙 문서가 최신인지 확인합니다.

| 변경 디렉토리 | 동기화 대상 문서                           | 확인 내용                      |
| ------------- | ------------------------------------------ | ------------------------------ |
| `hooks/`      | `<docs-root>/directory-rules/hooks.md`     | 훅 목록에 신규 훅 반영         |
| `lib/`        | `<docs-root>/directory-rules/lib.md`       | 유틸리티 목록에 신규 파일 반영 |
| `constants/`  | `<docs-root>/directory-rules/constants.md` | 상수 목록에 신규 파일 반영     |
| `types/`      | `<docs-root>/directory-rules/types.md`     | 타입 목록에 신규 파일 반영     |
| `routes/`     | `<docs-root>/directory-rules/routes.md`    | 라우트 목록에 신규 파일 반영   |

`<docs-root>`는 `.osom-skills`의 **Project documents → Rules directory** 값입니다. 해당 문서가 없거나 프로젝트가 이 관례를 쓰지 않으면 항목을 건너뜁니다.
