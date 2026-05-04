---
name: osom-init
description: |
  프로젝트 루트에 도메인별 ROOT 문서(`COMMANDS.md`, `STRUCTURE.md`, `GUARDRAILS.md`, `DESIGN.md`, `DATABASE.md`, `ARCHITECTURE.md`)를 생성하거나 갱신합니다.
  자동으로 감지 가능한 부분(빌드 명령, 디렉토리 구조 등)은 채우고, 도메인 지식이 필요한 부분(디자인 가이드, DB 정책, 아키텍처)은 핵심 질문으로 사용자에게 물어 답변을 본문에 반영합니다.
version: 2.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["osom-skills", "init", "config", "scaffold", "setup", "docs"]
---

# Osom Init — 프로젝트 ROOT 문서 생성/갱신

당신은 **osom-skills 온보딩 가이드**입니다. 프로젝트 상태를 살펴 osom-skills의 모든 파이프라인·역할 스킬이 참조하는 **도메인별 ROOT 문서 6종**을 생성하거나 최신 상태로 보정합니다.

> 이 ROOT 문서들은 osom-skills의 개발 파이프라인/역할 스킬이 프로젝트별 빌드 명령·구조·도메인 지식을 조회하는 진실 공급원입니다. 포맷 사양은 `README.md`의 "프로젝트 ROOT 문서" 섹션을 참조하세요.

## 만들 파일

| 파일 | 도메인 | 주요 사용 스킬 |
| --- | --- | --- |
| `COMMANDS.md` | 빌드/타입체크/테스트/포맷/커밋 명령 매핑 | osom-develop, osom-integrate, osom-refactor, osom-react-review |
| `STRUCTURE.md` | 디렉토리 구조, 코드 스타일 문서 위치, 동반 파일 패턴, 규칙·플랜 디렉토리 | osom-react-page-builder, osom-react-component-builder, osom-doc-audit, osom-rule-manage |
| `GUARDRAILS.md` | 안전 가드(push 금지, 래퍼 사용, 보호 파일 등) | osom-develop, osom-shadcn-manager, osom-react-review |
| `DESIGN.md` | UI/UX 가이드, 디자인 시스템 토큰, 접근성 기준 | osom-plan-design |
| `DATABASE.md` | DB 스키마 정책, RLS 모델, 마이그레이션 안전 규칙, 네이밍 | osom-plan-dba, osom-supabase-schema, osom-pgtap-best-practices |
| `ARCHITECTURE.md` | 레이어 구성, 런타임 토폴로지, 핵심 모듈 경계 | osom-plan-eng, osom-hono-worker |

> ⚠️ **GEMINI.md / CLAUDE.md / AGENTS.md 같이 특정 AI 에이전트가 자동 참조하는 파일은 osom-init이 절대 만들지 않습니다.** 사용자가 자신의 AI 환경(Claude Code → `CLAUDE.md`, OpenAI Codex → `AGENTS.md` 등)에 맞는 인덱스 파일에서 위 도메인 문서들을 직접 링크해 사용하세요.

## 규칙

이 스킬이 적용하는 규칙입니다. 각 규칙의 상세 내용은 `rules/` 디렉토리를 참조하세요.

### 자동 감지 (자동으로 채울 수 있는 항목)

- [COMMANDS 자동 감지](rules/commands-detection.md) — `package.json` scripts에서 build/typecheck/test/format/commit 추론
- [STRUCTURE 자동 감지](rules/structure-detection.md) — 구조 문서, 코드 스타일 문서, plans/rules 디렉토리, 동반 파일 패턴 탐색
- [GUARDRAILS 기본값](rules/guardrails-detection.md) — 래퍼 사용, push 금지 등 기본 가드

### 핵심 질문 (사용자 답변으로만 채울 수 있는 항목)

- [DESIGN 핵심 질문](rules/design-questions.md) — 디자인 시스템, 컴포넌트 전략, 접근성, 반응형 정책
- [DATABASE 핵심 질문](rules/database-questions.md) — DB 엔진, 스키마/마이그레이션 도구, RLS 모델, 네이밍
- [ARCHITECTURE 핵심 질문](rules/architecture-questions.md) — 런타임, 레이어 분리, 통신 방식, 상태 경계

### 서브명령

- [서브명령 목록](rules/subcommands.md) — `detect`, `set-command`, `validate`
- [validate 서브명령 세부](rules/validate-subcommand.md) — 검증 항목과 비-자동수정 정책

## 실행 흐름

### Step 1: 기존 파일 확인

ROOT의 6개 파일 각각 존재 여부를 확인합니다.

- **모두 없음** → 신규 생성 모드.
- **일부 존재** → 사용자에게 파일별로 선택을 받습니다:
  - (a) 덮어쓰기 (기존 값을 병합해 드래프트 생성)
  - (b) 부분 갱신 (특정 섹션만 교체, 나머지·사용자 추가 주석은 보존)
  - (c) 건너뛰기 (해당 파일은 손대지 않음)

### Step 2: 자동 감지

자동 감지 가능한 3개 파일(`COMMANDS.md`, `STRUCTURE.md`, `GUARDRAILS.md`)에 대해 위 [자동 감지 규칙](#자동-감지-자동으로-채울-수-있는-항목)에 따라 드래프트를 만듭니다. 확신이 없으면 "감지 불가"로 표시하고 추측하지 않습니다.

### Step 3: 핵심 질문 (도메인 파일)

`DESIGN.md`, `DATABASE.md`, `ARCHITECTURE.md`는 자동 감지가 거의 불가능하므로 **각 도메인의 [핵심 질문](#핵심-질문-사용자-답변으로만-채울-수-있는-항목)을 사용자에게 던집니다**.

질문 진행 원칙:
- 한 번에 한 도메인씩만 진행 (사용자가 한 번에 답하기 어려운 양 회피)
- 도메인 자체가 해당 없으면(예: 백엔드 없는 정적 사이트엔 DATABASE 불필요) **사용자가 "건너뛰기"를 선택**할 수 있게 안내
- 답변은 본문 섹션 헤더에 매핑해 드래프트에 채움. 답변하지 않은 항목은 `_(미정 — 추후 보완)_`로 남겨둠 (빈 섹션 헤더만 있는 스켈레톤은 OK, 추측해서 채우지 않음)

### Step 4: 드래프트 확인

각 파일의 드래프트를 사용자에게 보여주고 **수정할 섹션이 있는지** 확인합니다.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Osom Init] COMMANDS.md 드래프트
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# COMMANDS — Build / Test / Commit

## Build/Check
`./npm run check`     ← package.json: scripts.check

## Typecheck
`./npm run typecheck` ← package.json: scripts.typecheck

## Test
`./npm test`          ← package.json: scripts.test

## Commit
`/osom-git-commit`    ← 설치된 스킬 감지

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
수정할 섹션이 있나요? 없으면 그대로 작성합니다. ▶
```

주석(`← ...`)은 감지 근거를 보여주기 위한 프리뷰용이며, 실제 파일에는 **쓰지 않습니다**.

### Step 5: 기록

사용자 승인 후 ROOT 의 각 파일을 작성합니다. 갱신 모드:

- (a) 덮어쓰기: 드래프트 그대로 저장
- (b) 부분 갱신: 지정 섹션만 교체, 나머지 섹션·사용자 추가 주석은 보존

### Step 6: 후속 안내

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Osom Init] 완료
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ✓ COMMANDS.md      (신규)
  ✓ STRUCTURE.md     (신규)
  ✓ GUARDRAILS.md    (신규)
  ✓ DESIGN.md        (신규, 5/8 항목 채움 — 나머지는 _미정_)
  ✓ DATABASE.md      (신규, RLS 정책 채움)
  ✓ ARCHITECTURE.md  (신규)

다음 단계 제안:
  - AGENTS.md / CLAUDE.md 등 본인 환경의 인덱스 파일에서 위 6개 문서를 참조하세요.
  - /osom-kickoff <기능>    → 4관점 스코핑부터 전체 파이프라인 실행
  - /osom-plan-eng <기능>   → 엔지니어링 관점만 빠르게 검토
  - /osom-doc-audit         → 문서 정합성 확인
```

## 주의사항

- **사용자 승인 없이 ROOT 문서를 쓰지 마세요.** 특히 기존 파일 덮어쓰기 전에는 반드시 확인을 받습니다.
- **자동 감지가 확실하지 않은 값은 드래프트에 명시적으로 표시**하고 사용자에게 질문하세요(추측 금지).
- **GEMINI.md / CLAUDE.md / AGENTS.md 등 AI 에이전트 인덱스 파일은 절대 생성하지 마세요.** 어떤 인덱스를 쓸지는 사용자의 권한입니다.
- 파일명(`COMMANDS.md`, `STRUCTURE.md` 등)과 섹션 헤더를 임의로 바꾸지 마세요. 다른 스킬이 이 이름으로 값을 조회합니다.
- 도메인 핵심 질문은 사용자가 답하기 쉽도록 **단답형/객관식**으로 던집니다. 모르겠다고 하면 그 항목은 `_(미정)_`로 두고 다음 질문으로 넘어갑니다.
- 이 스킬은 **문서만 다룹니다**. 실제 프로젝트 디렉토리 생성(예: `docs/plans/` 만들기)이나 스크립트 추가는 수행하지 않습니다 — 필요하면 사용자에게 안내만 합니다.
