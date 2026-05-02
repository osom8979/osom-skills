# COMMANDS.md 자동 감지

`COMMANDS.md`의 각 섹션 값을 `package.json`의 `scripts`에서 추론합니다.

| 섹션 헤더     | 후보 스크립트명                                                    |
| ------------- | ------------------------------------------------------------------ |
| `## Build/Check` | `check`, `build`, `validate`, `verify`                          |
| `## Typecheck`   | `typecheck`, `type-check`, `tsc`, `tsc --noEmit`                |
| `## Test`        | `test`, `test:run`, `test:ci`                                   |
| `## Format`      | `format`, `format:write`, `prettier --write`                    |
| `## Commit`      | 설치된 스킬에 `osom-git-commit`이 있으면 `/osom-git-commit`, 없으면 감지 불가 |

**래퍼 감지**: 프로젝트 루트에 실행 가능한 `./npm`, `./npx`, `./node` 파일이 있으면 명령 앞에 `./`를 붙입니다 (예: `./npm run check`).

확신이 없으면 해당 섹션 본문을 `_(감지 불가 — 사용자 입력 필요)_`로 표시하고 사용자에게 묻습니다.

## 권장 파일 구조

```markdown
# COMMANDS

이 프로젝트의 빌드/테스트/커밋 명령 매핑입니다. osom-skills 파이프라인이 이 값을 그대로 호출합니다.

## Build/Check
`./npm run check`

## Typecheck
`./npm run typecheck`

## Test
`./npm test`

## Format
`./npm run format`

## Commit
`/osom-git-commit`
```
