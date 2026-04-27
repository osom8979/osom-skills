# Required companion files 자동 감지

프로젝트에 **실제로 존재하는 패턴만** 기본값으로 넣습니다 — 컨벤션을 강제하지 않습니다.

- `components/**/*.stories.*`가 하나라도 있으면 `components/**/*.tsx: .stories.tsx`
- `components/**/*.test.*`가 있으면 `.test.tsx` 추가
- `pages/**/index.stories.*`가 있으면 페이지 패턴 추가
- `hooks/**/*.test.*`가 있으면 hooks 패턴 추가

"있어야 한다"가 아니라 "이미 그렇게 쓰고 있는지"가 기준입니다. 사용자가 새 컨벤션을 도입하려면 명시적으로 추가합니다.
