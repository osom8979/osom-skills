# Commands 자동 감지

`package.json`의 `scripts`에서 추론합니다.

| 섹션 키       | 후보 스크립트명                                                    |
| ------------- | ------------------------------------------------------------------ |
| `Build/Check` | `check`, `build`, `validate`, `verify`                             |
| `Typecheck`   | `typecheck`, `type-check`, `tsc`, `tsc --noEmit`                   |
| `Test`        | `test`, `test:run`, `test:ci`                                      |
| `Format`      | `format`, `format:write`, `prettier --write`                       |
| `Commit`      | 설치된 스킬에 `git-commit`이 있으면 `/git-commit`, 없으면 감지 불가 |

**래퍼 감지**: 프로젝트 루트에 실행 가능한 `./npm`, `./npx`, `./node` 파일이 있으면 명령 앞에 `./`를 붙입니다 (예: `./npm run check`).

확신이 없으면 "감지 불가"로 표시하고 사용자에게 묻습니다.
