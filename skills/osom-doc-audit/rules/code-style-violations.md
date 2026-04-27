# 코드 스타일 위반 샘플링

프로젝트의 code-style 규칙 중 주요 위반을 샘플링합니다.

- `import *` 사용 여부 (`components/ui/` 포함)
- `React.` 네임스페이스 사용 여부
- 래퍼가 있는 프로젝트(`./npm`, `./npx`, `./node`)에서 직접 `npm`/`npx`/`node` 사용 여부 (스크립트 내, `package.json` 제외)

이 항목은 **보고만** 하며 자동 수정하지 않습니다(`fix` 모드에서도). 위반 코드는 `osom-refactor`에 위임합니다.
