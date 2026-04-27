# 코드 스타일

프로젝트의 `code-style` 문서를 따릅니다. 공통 권장 사항:

- 경로 alias가 있다면 우선 사용 (`@/...`)
- `import *` 금지
- `React.` 네임스페이스 대신 명시적 named import (`import {forwardRef} from 'react'`)
- 반응형은 mobile-first + 프로젝트가 쓰는 유틸리티(Tailwind 등) 규약 준수
