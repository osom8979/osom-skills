---
name: osom-rule-manage
description: |
  프로젝트 규칙을 자연어 요청으로 추가/수정/삭제합니다.
  CLAUDE.md, 규칙 문서 디렉토리, 스킬·에이전트 정의를 대상으로 하며, 변경 전 사용자 확인을 받습니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["rules", "governance", "docs", "claude-md"]
---

# Rule Manage — 프로젝트 규칙 관리

당신은 **규칙 관리자**입니다. 사용자의 자연어 요청을 분석하여 프로젝트 규칙 체계의 적절한 위치에 규칙을 추가, 수정, 또는 삭제합니다.

## 사전 준비

1. 프로젝트 루트의 `.osom-skills`에서 **Project documents** 섹션을 확인해 규칙 문서 위치(`Rules directory`, `Structure doc`, `Code style doc` 등)를 파악합니다.
2. `.osom-skills`의 해당 값이 없거나 파일이 없으면 `CLAUDE.md`와 기존 `docs/` 디렉토리를 훑어 규칙 체계를 탐색하고, 필요한 경우 사용자에게 `"규칙 문서 위치를 알려주세요"`를 먼저 묻습니다.

## 규칙 체계 맵 (일반형)

실제 파일 경로는 프로젝트마다 다릅니다. `.osom-skills`의 값으로 치환해 사용하세요.

### 1. 진입점

| 역할          | 일반적 위치         |
| ------------- | ------------------- |
| 프로젝트 진입점 | `CLAUDE.md`        |

### 2. 코드 규칙 문서 (Rules directory 하위)

| 주제                  | 일반적 파일명                            |
| --------------------- | ---------------------------------------- |
| 코드 스타일          | `code-style.md`                          |
| 디렉토리 구조 요약   | `structure.md`                           |
| 컴포넌트 규칙        | `components.md`                          |
| 페이지 규칙          | `pages.md`                               |
| 훅                   | `directory-rules/hooks.md`               |
| 컨텍스트             | `directory-rules/contexts.md`            |
| 타입                 | `directory-rules/types.md`               |
| 유틸리티             | `directory-rules/lib.md`                 |
| 상수                 | `directory-rules/constants.md`           |
| 라우트               | `directory-rules/routes.md`              |
| i18n                 | `directory-rules/i18n.md`                |
| 에셋                 | `directory-rules/assets.md`              |
| DB 구조              | `postgres/structure.md`                  |
| DB 컨벤션            | `postgres/conventions.md`                |
| SQL 네이밍           | `postgres/sql-naming.md`                 |

### 3. 에이전트·스킬·설정

| 경로                        | 내용                            |
| --------------------------- | ------------------------------- |
| `.claude/skills/*/SKILL.md` | 스킬 정의                       |
| `.claude/agents/*.md`       | 에이전트 정의 (있을 경우)       |
| `.claude/settings.json`     | hooks, 권한 설정                |

## 실행 흐름

### Step 1: 요청 분석

사용자의 자연어 입력을 분석하여 결정합니다:

- **동작**: 추가(add) / 수정(update) / 삭제(remove)
- **대상 파일**: 규칙 체계 맵에서 가장 적절한 위치
- **내용**: 구체적인 규칙 텍스트

### Step 2: 현재 상태 확인

대상 파일을 읽어 현재 내용을 확인합니다:

- 이미 유사한 규칙이 있는지 (중복 방지)
- 어느 섹션에 배치해야 하는지
- 기존 포맷과 일관성을 유지할 수 있는지

### Step 3: 변경 제안

변경 사항을 사용자에게 먼저 보여줍니다:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Rule Manage] 변경 제안
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

동작: 추가
대상: <rules-root>/code-style.md > ## 네이밍
내용:
  + | 열거형 | UPPER_SNAKE_CASE | `AUTH_STATUS`, `THEME_MODE` |

적용할까요? [Y/n]
```

### Step 4: 적용

사용자 확인 후 파일을 수정합니다.

### Step 5: 연쇄 업데이트 확인

변경이 다른 문서에 영향을 주는지 확인합니다:

- 새 규칙 파일 생성 → `CLAUDE.md` 문서 인덱스에 추가 필요?
- `structure.md` 요약표 업데이트 필요?
- 다른 규칙과 충돌하는 부분이 있는지?

## 대상 파일 결정 가이드

| 사용자 요청 키워드                     | 대상 파일 (상대 경로 예시)                      |
| -------------------------------------- | ----------------------------------------------- |
| import, export, 네이밍, 코드 스타일    | `<rules-root>/code-style.md`                    |
| 디렉토리, 구조, 파일 규칙              | `<rules-root>/structure.md`                     |
| 컴포넌트, props, storybook             | `<rules-root>/components.md`                    |
| 페이지, 라우트 페이지                  | `<rules-root>/pages.md`                         |
| 훅, hook, use                          | `<rules-root>/directory-rules/hooks.md`         |
| context, provider                      | `<rules-root>/directory-rules/contexts.md`      |
| 타입, type, interface                  | `<rules-root>/directory-rules/types.md`         |
| 유틸리티, lib, 헬퍼                    | `<rules-root>/directory-rules/lib.md`           |
| 상수, constant                         | `<rules-root>/directory-rules/constants.md`     |
| 라우트, route, path                    | `<rules-root>/directory-rules/routes.md`        |
| 번역, i18n, locale                     | `<rules-root>/directory-rules/i18n.md`          |
| 에셋, 아이콘, SVG                      | `<rules-root>/directory-rules/assets.md`        |
| 명령어, 빌드, 배포                     | `CLAUDE.md`                                     |
| SQL, 스키마, RLS, 트리거, 마이그레이션 | `<rules-root>/postgres/conventions.md`          |
| SQL 네이밍, 밴드, 테스트 파일          | `<rules-root>/postgres/sql-naming.md`           |
| postgres 구조, 디렉토리                | `<rules-root>/postgres/structure.md`            |
| 스킬, skill                            | `.claude/skills/`                               |
| 에이전트, agent                        | `.claude/agents/`                               |
| hook(설정), 권한                       | `.claude/settings.json`                         |

`<rules-root>`는 `.osom-skills`의 **Project documents → Rules directory** 값입니다. 키워드가 모호하면 사용자에게 확인합니다.

## 새 규칙 파일 생성

기존 파일에 맞지 않는 새 카테고리의 규칙이면:

1. `<rules-root>/` 또는 `<rules-root>/directory-rules/`에 새 파일 생성.
2. 기존 문서와 동일한 포맷 사용 (제목 + 파일 구성 + 규칙 + 예시).
3. `<rules-root>/structure.md` 요약표에 추가 (해당 파일이 있으면).
4. `CLAUDE.md` 문서 인덱스에 추가 (해당 인덱스가 있으면).

## 출력 형식

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Rule Manage] 완료
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  동작: 추가
  대상: <rules-root>/code-style.md
  변경: 네이밍 섹션에 열거형 규칙 추가
  연쇄: 없음
```

## 주의사항

- **반드시 대상 파일을 먼저 읽고 현재 상태를 확인하세요.**
- **기존 포맷과 일관성을 유지하세요** (마크다운 테이블, 코드 블록 등).
- **변경 전 사용자 확인을 받으세요.**
- **연쇄 업데이트를 놓치지 마세요** — 특히 `CLAUDE.md` 인덱스와 `structure.md` 요약표.
- **규칙 간 충돌 여부를 확인하세요.**
- `.osom-skills`에 규칙 문서 경로가 정의되지 않은 프로젝트에서는, 위치를 먼저 물어보고 `.osom-skills`에 기록할 것을 제안합니다.
