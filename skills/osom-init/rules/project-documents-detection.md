# Project documents 자동 감지

다음 경로 후보를 순서대로 탐색해 첫 번째로 발견되는 것을 사용합니다.

| 섹션 키          | 감지 경로                                                          |
| ---------------- | ------------------------------------------------------------------ |
| `Structure doc`  | `docs/rules/structure.md`, `docs/structure.md`, `ARCHITECTURE.md`  |
| `Code style doc` | `docs/rules/code-style.md`, `docs/code-style.md`                   |
| `Plans directory`| `docs/plans/`, `plans/`                                            |
| `Rules directory`| `docs/rules/`, `docs/`                                             |

파일/디렉토리가 없으면 그 줄은 드래프트에서 **주석 처리**하거나 생략합니다 — 추측해서 채우지 않습니다.
