# 대상 파일 결정 가이드

| 사용자 요청 키워드                     | 대상 파일 (상대 경로 예시)                  |
| -------------------------------------- | ------------------------------------------- |
| import, export, 네이밍, 코드 스타일    | `<rules-root>/code-style.md`                |
| 디렉토리, 구조, 파일 규칙              | `<rules-root>/structure.md`                 |
| 컴포넌트, props, storybook             | `<rules-root>/components.md`                |
| 페이지, 라우트 페이지                  | `<rules-root>/pages.md`                     |
| 훅, hook, use                          | `<rules-root>/directory-rules/hooks.md`     |
| context, provider                      | `<rules-root>/directory-rules/contexts.md`  |
| 타입, type, interface                  | `<rules-root>/directory-rules/types.md`     |
| 유틸리티, lib, 헬퍼                    | `<rules-root>/directory-rules/lib.md`       |
| 상수, constant                         | `<rules-root>/directory-rules/constants.md` |
| 라우트, route, path                    | `<rules-root>/directory-rules/routes.md`    |
| 번역, i18n, locale                     | `<rules-root>/directory-rules/i18n.md`      |
| 에셋, 아이콘, SVG                      | `<rules-root>/directory-rules/assets.md`    |
| 명령어, 빌드, 배포                     | `CLAUDE.md`                                 |
| SQL, 스키마, RLS, 트리거, 마이그레이션 | `<rules-root>/postgres/conventions.md`      |
| SQL 네이밍, 밴드, 테스트 파일          | `<rules-root>/postgres/sql-naming.md`       |
| postgres 구조, 디렉토리                | `<rules-root>/postgres/structure.md`        |
| 스킬, skill                            | `.claude/skills/`                           |
| 에이전트, agent                        | `.claude/agents/`                           |
| hook(설정), 권한                       | `.claude/settings.json`                     |

`<rules-root>`는 `.osom-skills`의 **Project documents → Rules directory** 값입니다. 키워드가 모호하면 사용자에게 확인합니다.
