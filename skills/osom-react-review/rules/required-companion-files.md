# 필수 동반 파일 누락 검증

`.osom-skills`의 **Required companion files** 섹션에 정의된 글롭 매칭 규칙에 따라 누락을 확인합니다. 예시:

| 경로 글롭                | 요구 파일                               |
| ------------------------ | --------------------------------------- |
| `components/**/*.tsx`    | `<Name>.stories.tsx`, `<Name>.test.tsx` |
| `components/ui/**/*.tsx` | `<Name>.stories.tsx`                    |
| `pages/**/index.tsx`     | `index.stories.tsx`, `index.test.tsx`   |
| `hooks/**/*.ts`          | `<Name>.test.ts`                        |

`.osom-skills`가 없거나 해당 섹션이 없으면 이 항목은 "규칙 미정"으로 보고하고 건너뜁니다.
