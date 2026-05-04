---
name: osom-doc-audit
description: |
  프로젝트 문서(CLAUDE.md, docs/, 스킬·에이전트 정의)와 실제 코드 상태의 정합성을 검증하고 불일치를 보고합니다.
  `fix` 서브명령으로 자동 수정도 지원합니다(코드 스타일 위반은 보고만).
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["audit", "docs", "consistency", "sync"]
---

# Doc Audit — 문서 정합성 검증

프로젝트 문서와 실제 코드 상태를 대조하여 불일치를 찾아 보고합니다.

## 사전 준비

1. 프로젝트 루트의 ROOT 문서를 읽습니다.
   - `STRUCTURE.md` — `Structure doc`, `Code style doc`, `Rules directory`, `Plans directory`, `Required companion files`
   - `COMMANDS.md`, `GUARDRAILS.md`, `DESIGN.md`, `DATABASE.md`, `ARCHITECTURE.md` — 존재 여부와 미정 항목 비율
2. 사용자가 사용하는 인덱스 파일(`CLAUDE.md` / `AGENTS.md` 등)이 있으면 ROOT 문서들을 올바르게 링크하는지 점검 대상에 포함합니다.

## 규칙

각 검증 항목의 상세 내용은 `rules/` 디렉토리를 참조하세요. 아래 순서대로 검증합니다.

1. [CLAUDE.md 인덱스 링크](rules/claude-md-index-check.md) — 인덱스 링크 유효성
2. [디렉토리별 파일 규칙](rules/directory-rules-check.md) — `directory-rules/*.md` vs 실제 디렉토리 일치 여부
3. [DB 스키마 문서](rules/db-schema-docs-check.md) — `postgres/` 디렉토리와 SQL 규칙 문서 일치 여부
4. [에이전트/스킬 정의](rules/agent-skill-definitions-check.md) — `.claude/agents/`, `.claude/skills/` 정의 유효성
5. [코드 스타일 위반 샘플링](rules/code-style-violations.md) — `import *`, `React.`, 래퍼 우회 등

추가:

- [수정(fix) 모드](rules/fix-mode.md) — `/doc-audit fix` 호출 시 자동 수정 범위와 예외

## 실행 절차

1. 위 5개 항목을 순서대로 검증.
2. 각 항목 결과를 ✅ (일치) 또는 ❌ (불일치)로 표시.
3. 불일치 항목에 구체적인 차이점과 수정 제안을 포함.

## 출력 형식

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Doc Audit] 문서 정합성 검증 결과
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. CLAUDE.md 인덱스
   ✅ 모든 링크 유효 (6/6)

2. 디렉토리별 파일 규칙
   ✅ hooks/ — 8/8 일치
   ❌ lib/ — 문서 8개, 실제 9개
      + new-util.ts (문서 미반영)
   ❌ components/inputs/ — 필수 파일 누락
      - SearchInput.stories.tsx 없음
      - SearchInput.test.tsx 없음

3. DB 스키마 문서
   ✅ 모든 문서 유효

4. 에이전트/스킬 정의
   ✅ 모든 정의 유효

5. 코드 스타일
   ❌ import * as React — 2개 파일
      - components/ui/tooltip.tsx:1
      - components/ui/popover.tsx:1

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
요약: 3개 불일치, 2개 정상
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## 주의사항

- 프로젝트에 존재하지 않는 디렉토리/문서는 검증에서 자동으로 제외합니다. "없으면 실패"가 아닙니다.
- 수정 모드에서도 파괴적 변경(문서 대량 삭제 등)은 사용자 확인을 받습니다.
