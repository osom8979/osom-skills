# 규칙 체계 맵 (일반형)

실제 파일 경로는 프로젝트마다 다릅니다. ROOT 도메인 문서(`STRUCTURE.md` 등)의 값으로 치환해 사용하세요.

## 1. 진입점

| 역할                        | 일반적 위치 |
| --------------------------- | ----------- |
| 사용자 환경 인덱스          | `CLAUDE.md`(Claude Code) / `AGENTS.md`(Codex 등) — 사용자가 직접 관리 |
| osom-skills ROOT 도메인 문서| `COMMANDS.md`, `STRUCTURE.md`, `GUARDRAILS.md`, `DESIGN.md`, `DATABASE.md`, `ARCHITECTURE.md` |

## 2. 코드 규칙 문서 (Rules directory 하위)

| 주제              | 일반적 파일명                     |
| ----------------- | --------------------------------- |
| 코드 스타일       | `code-style.md`                   |
| 디렉토리 구조 요약 | `structure.md`                   |
| 컴포넌트 규칙     | `components.md`                   |
| 페이지 규칙       | `pages.md`                        |
| 훅                | `directory-rules/hooks.md`        |
| 컨텍스트          | `directory-rules/contexts.md`     |
| 타입              | `directory-rules/types.md`        |
| 유틸리티          | `directory-rules/lib.md`          |
| 상수              | `directory-rules/constants.md`    |
| 라우트            | `directory-rules/routes.md`       |
| i18n              | `directory-rules/i18n.md`         |
| 에셋              | `directory-rules/assets.md`       |
| DB 구조           | `postgres/structure.md`           |
| DB 컨벤션         | `postgres/conventions.md`         |
| SQL 네이밍        | `postgres/sql-naming.md`          |

## 3. 에이전트·스킬·설정

| 경로                        | 내용                       |
| --------------------------- | -------------------------- |
| `.claude/skills/*/SKILL.md` | 스킬 정의                  |
| `.claude/agents/*.md`       | 에이전트 정의 (있을 경우)  |
| `.claude/settings.json`     | hooks, 권한 설정           |
