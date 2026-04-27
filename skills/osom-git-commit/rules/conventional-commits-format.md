# Conventional Commits 형식

커밋 메시지는 `type(scope): description` 형식으로 구성합니다.

- **type**: feat, fix, docs, style, refactor, test, chore, ci, build, perf
- **scope**: 선택 사항. 변경 영역을 나타냄 (예: auth, ui, api)
- **description**: 명령형으로 작성된 간단한 요약

예시:

```
feat(auth): Add Google OAuth provider
fix(api): Handle null response from /me endpoint
refactor: Extract shared OAuth callback logic
```
