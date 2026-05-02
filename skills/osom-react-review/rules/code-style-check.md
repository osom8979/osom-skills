# 코드 스타일 검증

변경된 파일이 프로젝트 code-style 규칙을 준수하는지 확인합니다. 일반적으로 검사할 패턴:

- 프로젝트가 정한 경로 alias(`@/...` 등) 사용 여부
- 와일드카드 `import *` 없음
- `React.` 네임스페이스 사용 없음
- `export default function <Name>()` 형태 (페이지/컴포넌트)
- 네이밍 규칙 준수

```bash
# 변경된 ts/tsx 파일 목록
git diff --name-only HEAD~1 | grep -E '\.(tsx?|ts)$'
```

`STRUCTURE.md`의 `Code style doc` 값이 있으면 그 문서가 우선합니다.
