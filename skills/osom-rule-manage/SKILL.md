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

1. 프로젝트 루트의 `STRUCTURE.md`에서 규칙 문서 위치(`Rules directory`, `Structure doc`, `Code style doc`)를 파악합니다.
2. `STRUCTURE.md`가 없거나 해당 값이 비어 있으면 사용자가 사용하는 인덱스 파일(`CLAUDE.md` / `AGENTS.md` 등)과 기존 `docs/` 디렉토리를 훑어 규칙 체계를 탐색하고, 필요한 경우 사용자에게 `"규칙 문서 위치를 알려주세요"`를 먼저 묻습니다.

## 규칙

이 스킬이 적용하는 규칙입니다. 각 규칙의 상세 내용은 `rules/` 디렉토리를 참조하세요.

- [규칙 체계 맵](rules/rule-system-map.md) — 진입점, 코드 규칙 문서, 에이전트/스킬/설정의 일반형 위치 매핑
- [대상 파일 결정 가이드](rules/target-file-decision-guide.md) — 사용자 요청 키워드 → 대상 파일 매핑 표
- [새 규칙 파일 생성](rules/new-rule-file-creation.md) — 새 카테고리 추가 시 절차와 연쇄 업데이트

## 실행 흐름

### Step 1: 요청 분석

사용자의 자연어 입력을 분석하여 결정합니다.

- **동작**: 추가(add) / 수정(update) / 삭제(remove)
- **대상 파일**: [규칙 체계 맵](rules/rule-system-map.md)과 [대상 파일 결정 가이드](rules/target-file-decision-guide.md)에서 가장 적절한 위치
- **내용**: 구체적인 규칙 텍스트

### Step 2: 현재 상태 확인

대상 파일을 읽어 현재 내용을 확인합니다.

- 이미 유사한 규칙이 있는지 (중복 방지)
- 어느 섹션에 배치해야 하는지
- 기존 포맷과 일관성을 유지할 수 있는지

### Step 3: 변경 제안

변경 사항을 사용자에게 먼저 보여줍니다.

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

변경이 다른 문서에 영향을 주는지 확인합니다.

- 새 규칙 파일 생성 → `CLAUDE.md` 문서 인덱스에 추가 필요?
- `structure.md` 요약표 업데이트 필요?
- 다른 규칙과 충돌하는 부분이 있는지?

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
- **연쇄 업데이트를 놓치지 마세요** — 특히 사용자의 인덱스 파일(`CLAUDE.md`/`AGENTS.md`)과 `structure.md` 요약표.
- **규칙 간 충돌 여부를 확인하세요.**
- `STRUCTURE.md`에 규칙 문서 경로가 정의되지 않은 프로젝트에서는, 위치를 먼저 물어보고 `STRUCTURE.md`에 기록할 것을 제안합니다 (`/osom-init STRUCTURE.md`).
