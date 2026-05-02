# STRUCTURE.md 자동 감지

`STRUCTURE.md`는 프로젝트 디렉토리 구조, 코드 스타일 문서 위치, 동반 파일 패턴, 규칙·플랜 디렉토리를 한 곳에 모은 문서입니다.

## 1. 문서/디렉토리 경로 탐색

다음 경로 후보를 순서대로 탐색해 첫 번째로 발견되는 것을 사용합니다.

| 섹션 헤더               | 감지 경로 후보                                                     |
| ----------------------- | ------------------------------------------------------------------ |
| `## Structure doc`      | `docs/rules/structure.md`, `docs/structure.md`                     |
| `## Code style doc`     | `docs/rules/code-style.md`, `docs/code-style.md`                   |
| `## Plans directory`    | `docs/plans/`, `plans/`                                            |
| `## Rules directory`    | `docs/rules/`, `docs/`                                             |

파일/디렉토리가 없으면 그 섹션 본문은 `_(없음)_`으로 표기하거나 섹션을 생략합니다 — 추측해서 채우지 않습니다.

> 참고: `STRUCTURE.md`는 자기 자신이 ROOT 의 구조 문서이므로, 별도의 `docs/rules/structure.md`가 이미 있다면 `## Structure doc` 섹션은 그 경로를 가리키는 포인터로만 두고 본문은 그 파일에 위임합니다.

## 2. 동반 파일 패턴 자동 감지

프로젝트에 **실제로 존재하는 패턴만** 기본값으로 넣습니다 — 컨벤션을 강제로 추가하지 않습니다.

- `components/**/*.stories.*`가 하나라도 있으면 `components/**/*.tsx → .stories.tsx`
- `components/**/*.test.*`가 있으면 `.test.tsx` 추가
- `pages/**/index.stories.*`가 있으면 페이지 패턴 추가
- `hooks/**/*.test.*`가 있으면 hooks 패턴 추가

"있어야 한다"가 아니라 "이미 그렇게 쓰고 있는지"가 기준입니다. 새 컨벤션을 도입하려면 사용자가 명시적으로 추가합니다.

## 권장 파일 구조

```markdown
# STRUCTURE

이 프로젝트의 디렉토리 구조와 동반 파일 규칙입니다.

## Structure doc
`docs/rules/structure.md`

## Code style doc
`docs/rules/code-style.md`

## Plans directory
`docs/plans/`

## Rules directory
`docs/rules/`

## Required companion files
| Glob                    | Required companions             |
| ----------------------- | ------------------------------- |
| `components/**/*.tsx`   | `.stories.tsx`, `.test.tsx`     |
| `pages/**/index.tsx`    | `index.stories.tsx`, `index.test.tsx` |
| `hooks/**/*.ts`         | `.test.ts`                      |
```
