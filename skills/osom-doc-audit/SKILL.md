---
name: osom-doc-audit
description: |
  프로젝트 문서(CLAUDE.md, docs/, 스킬·에이전트 정의)와 실제 코드 상태의 정합성을 검증하고 불일치를 보고합니다.
  `fix` 서브명령으로 자동 수정도 지원합니다(코드 스타일 위반은 보고만).
version: 0.1.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["audit", "docs", "consistency", "sync"]
---

# Doc Audit — 문서 정합성 검증

프로젝트 문서와 실제 코드 상태를 대조하여 불일치를 찾아 보고합니다.

## 사전 준비

1. 프로젝트 루트의 `.osom-skills`에서 **Project documents** 섹션(구조 문서, 규칙 디렉토리)과 **Required companion files** 섹션을 읽습니다.
2. `CLAUDE.md`가 있으면 문서 인덱스 위치를 파악합니다.

## 검증 항목

### 1. CLAUDE.md 인덱스 링크 검증

`CLAUDE.md`의 문서 인덱스에 나열된 모든 링크가 실제로 존재하는지 확인합니다.

### 2. 디렉토리별 파일 규칙 검증

`.osom-skills`의 **Project documents → Rules directory**에 속하는 문서(예: `structure.md`, `directory-rules/*.md`)의 목록이 실제 디렉토리 내용과 일치하는지 확인합니다.

**확인 대상 (프로젝트에 존재하는 것만)**

- `hooks/` — 문서에 나열된 훅 vs 실제 파일
- `contexts/` — 문서에 나열된 컨텍스트 vs 실제 파일
- `types/` — 문서에 나열된 타입 파일 vs 실제 파일
- `lib/` — 문서에 나열된 유틸리티 vs 실제 파일
- `constants/` — 문서에 나열된 상수 파일 vs 실제 파일
- `routes/` — 문서에 나열된 라우트 파일 vs 실제 파일
- `components/` — 카테고리 목록 일치 여부
- `components/ui/` — 설치된 shadcn 컴포넌트 목록

**필수 동반 파일 누락** (`.osom-skills`의 Required companion files 기준)

### 3. DB 스키마 문서 검증 (해당하는 프로젝트만)

프로젝트가 `postgres/` 같은 DB 스키마 디렉토리를 가지면, 관련 규칙 문서(`structure`, `conventions`, `sql-naming` 등)가 실제 디렉토리 구조와 일치하는지 확인합니다.

### 4. 에이전트/스킬 정의 검증

`.claude/agents/*.md`, `.claude/skills/*/SKILL.md`의 정의가:

- 참조하는 디렉토리/파일이 실제로 존재하는지
- Working Area 정의가 실제 디렉토리와 일치하는지
- `allowed-tools`에 나열된 도구가 유효한지

### 5. 코드 스타일 위반 샘플링

프로젝트의 code-style 규칙 중 주요 위반을 샘플링합니다:

- `import *` 사용 여부 (`components/ui/` 포함)
- `React.` 네임스페이스 사용 여부
- 래퍼가 있는 프로젝트에서 `npm`/`npx`/`node` 직접 사용 여부 (스크립트 내, `package.json` 제외)

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

## 수정 모드

사용자가 `/doc-audit fix`로 호출하면, 불일치 항목을 자동으로 수정합니다:

- 문서에 누락된 파일/항목 추가
- 삭제된 파일/항목 제거
- **단, 코드 스타일 위반은 보고만 하고 자동 수정하지 않습니다** (리팩토링 범위이므로 `/refactor`에 위임).

## 주의사항

- 프로젝트에 존재하지 않는 디렉토리/문서는 검증에서 자동으로 제외합니다. "없으면 실패"가 아닙니다.
- 수정 모드에서도 파괴적 변경(문서 대량 삭제 등)은 사용자 확인을 받습니다.
