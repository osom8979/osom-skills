# 코드 스타일

- **Import**: 상대 경로 사용 (`../lib/supabase`). Worker 코드에는 프론트엔드용 `@/` alias를 쓰지 않습니다.
- **Export**: named export (`export {resource}`).
- 프로젝트의 `code-style` 문서가 있다면 그 규칙이 우선합니다.

`@/` alias는 프론트엔드 빌드 도구(Vite 등)가 해석하는 alias이므로 Worker 빌드에서는 깨집니다. 두 빌드가 분리되어 있다는 점을 명심하세요.
