# GUARDRAILS.md 기본값

`GUARDRAILS.md`는 다른 스킬이 강제로 준수해야 하는 제약 모음입니다. 안전 관련 항목은 기본 포함을 권장합니다.

## 자동 포함 항목

- **래퍼 존재 시**: 프로젝트 루트에 `./npm`, `./npx`, `./node` 실행 파일이 있으면 `Use ./npm, ./npx, ./node wrappers` 항목 포함.
- **기본 가드**: `Never push; commits only` 포함 (사용자가 명시적으로 빼달라고 하지 않는 한).

## 사용자 확인이 필요한 항목

다음에 대해 사용자에게 추가 가드가 있는지 묻습니다.

- production DB 변경 금지 여부
- 특정 파일/디렉토리 수정 금지 (예: `migrations/`, `vendor/`)
- 시크릿 파일(`.env*`, `*.pem`) 보호
- 의존성 변경 금지 (`package.json` lock 정책)

## 권장 파일 구조

```markdown
# GUARDRAILS

이 프로젝트에서 osom-skills 스킬이 반드시 지켜야 하는 제약입니다.

## Hard rules
- Never push; commits only.
- Use `./npm`, `./npx`, `./node` wrappers (never bare `npm` / `npx` / `node`).
- Do not modify files in `migrations/` directly — use the migration tool.
- Do not commit any file matching `.env*` or `*.pem`.

## Soft rules
- Avoid touching `vendor/` unless the user requests it.
```
