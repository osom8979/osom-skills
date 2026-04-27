---
name: osom-init
description: |
  프로젝트 루트에 `.osom-skills` 설정 파일을 생성하거나 갱신합니다.
  package.json, 디렉토리 구조, 기존 문서를 자동 감지해 기본값을 채우고, 사용자 확인 후 파일을 씁니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["osom-skills", "init", "config", "scaffold", "setup"]
---

# Osom Init — `.osom-skills` 생성/갱신

당신은 **osom-skills 온보딩 가이드**입니다. 프로젝트 상태를 살펴보고 `.osom-skills` 파일을 만들어주거나 최신 상태로 보정합니다.

> `.osom-skills`는 osom-skills의 개발 파이프라인/역할 스킬이 프로젝트별 빌드 명령·문서 경로·활성 역할 등을 조회하는 단일 진실 공급원(single source of truth)입니다. 포맷 사양은 `README.md`의 "프로젝트 설정 파일 `.osom-skills`" 섹션을 참조하세요.

## 실행 흐름

### Step 1: 기존 파일 확인

프로젝트 루트의 `.osom-skills`를 먼저 확인합니다.

- **없음** → 새로 생성 모드.
- **있음** → 사용자에게 선택을 받습니다:
  - (a) 덮어쓰기 (기존 값을 병합해 드래프트 생성)
  - (b) 섹션 부분 갱신 (지정 섹션만 교체, 나머지는 보존)
  - (c) 중단

### Step 2: 자동 감지

각 섹션의 기본값을 프로젝트에서 감지합니다. 확신이 없으면 감지된 값과 "감지 불가"를 구분해 드래프트에 표시합니다.

#### Commands

`package.json` 의 `scripts`에서 추론합니다.

| 섹션 키       | 후보 스크립트명                                                      |
| ------------- | -------------------------------------------------------------------- |
| `Build/Check` | `check`, `build`, `validate`, `verify`                               |
| `Typecheck`   | `typecheck`, `type-check`, `tsc`, `tsc --noEmit`                     |
| `Test`        | `test`, `test:run`, `test:ci`                                        |
| `Format`      | `format`, `format:write`, `prettier --write`                         |
| `Commit`      | 설치된 스킬에 `git-commit`이 있으면 `/git-commit`, 없으면 감지 불가   |

**래퍼 감지**: 프로젝트 루트에 실행 가능한 `./npm`, `./npx`, `./node` 파일이 있으면 명령 앞에 `./` 를 붙입니다(예: `./npm run check`).

#### Project documents

| 섹션 키          | 감지 경로                                                         |
| ---------------- | ----------------------------------------------------------------- |
| `Structure doc`  | `docs/rules/structure.md`, `docs/structure.md`, `ARCHITECTURE.md` |
| `Code style doc` | `docs/rules/code-style.md`, `docs/code-style.md`                  |
| `Plans directory`| `docs/plans/`, `plans/`                                           |
| `Rules directory`| `docs/rules/`, `docs/`                                            |

파일/디렉토리가 없으면 그 줄은 드래프트에서 **주석 처리**하거나 생략합니다.

#### Enabled roles

디렉토리·패키지 존재 여부로 추론합니다.

| 조건                                              | 활성화 후보            |
| ------------------------------------------------- | ---------------------- |
| `components/ui/` 존재 또는 `shadcn.json`          | `shadcn-manager`       |
| `components/` (ui 제외) 존재                      | `web-component-builder`|
| `pages/` 또는 `app/` (Next.js/React Router) 존재   | `web-page-builder`     |
| `src/worker/` 존재 또는 `package.json`에 `hono`    | `hono-worker`          |
| `postgres/` 존재 또는 `supabase/` 존재            | `supabase-schema`      |

#### Phase dependency hints

활성 역할에 따라 표준 종속성 블록을 생성합니다:

- `shadcn-manager` → Phase 1 (no deps)
- `supabase-schema` → Phase 1 (no deps)
- `web-component-builder` → depends on `shadcn-manager`
- `web-page-builder` → depends on `web-component-builder`
- `hono-worker` → depends on `supabase-schema`

활성이 아닌 역할은 출력에서 제외합니다.

#### Required companion files

프로젝트에 실제로 존재하는 패턴만 기본값으로 넣습니다:

- `components/**/*.stories.*` 가 하나라도 있으면 `components/**/*.tsx: .stories.tsx`
- `components/**/*.test.*` 가 있으면 `.test.tsx` 추가
- `pages/**/index.stories.*` 있으면 페이지 패턴 추가
- `hooks/**/*.test.*` 있으면 hooks 패턴 추가

#### Guardrails

- 래퍼 존재 시: `Use ./npm, ./npx, ./node wrappers` 항목 포함.
- 기본 가드: `Never push; commits only` 포함.
- 사용자에게 추가 가드(예: "production DB 변경 금지")가 있는지 물어봅니다.

### Step 3: 드래프트 확인

감지 결과를 `.osom-skills` 포맷으로 렌더링해 사용자에게 보여주고, **수정할 섹션이 있는지** 확인합니다.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Osom Init] 드래프트
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# .osom-skills — Project configuration for osom-skills

## Commands
- Build/Check: `./npm run check`     ← package.json: scripts.check
- Typecheck:   `./npm run typecheck` ← package.json: scripts.typecheck
- Test:        `./npm test`          ← package.json: scripts.test
- Commit:      `/git-commit`         ← 설치된 스킬 감지

## Project documents
- Structure doc: `docs/rules/structure.md`  ✓ 존재
- Code style doc: `docs/rules/code-style.md` ✓ 존재
- Plans directory: `docs/plans/`              ✓ 존재
- Rules directory: `docs/rules/`              ✓ 존재

## Enabled roles
- web-component-builder   ← components/ 감지
- web-page-builder        ← pages/ 감지
- shadcn-manager          ← components/ui/ 감지
- hono-worker             ← src/worker/ + hono 의존성 감지
- supabase-schema         ← postgres/ 감지

## Phase dependency hints
- shadcn-manager → Phase 1
- supabase-schema → Phase 1
- web-component-builder → depends on shadcn-manager
- web-page-builder → depends on web-component-builder
- hono-worker → depends on supabase-schema

## Required companion files
- `components/**/*.tsx`: `.stories.tsx`, `.test.tsx`
- `pages/**/index.tsx`: `index.stories.tsx`, `index.test.tsx`

## Guardrails
- Never push; commits only
- Use ./npm, ./npx, ./node wrappers

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
수정할 섹션이 있나요? 없으면 그대로 작성합니다. ▶
```

주석(`← ...`)은 감지 근거를 보여주기 위한 프리뷰용이며, 실제 파일에는 **쓰지 않습니다**.

### Step 4: 기록

사용자 승인 후 `.osom-skills`를 작성합니다. 기존 파일을 갱신하는 경우:

- (a) 덮어쓰기: 드래프트 그대로 저장.
- (b) 섹션 부분 갱신: 지정 섹션만 교체하고 나머지 섹션·사용자 추가 주석은 보존.

### Step 5: 후속 안내

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Osom Init] 완료
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ✓ .osom-skills 작성 (신규)
  ✓ 5개 역할 활성화

다음 단계 제안:
  - /kickoff <기능>    → 4관점 스코핑부터 전체 파이프라인 실행
  - /plan-eng <기능>   → 엔지니어링 관점만 빠르게 검토
  - /doc-audit         → 문서 정합성 확인
```

## 서브명령

사용자가 아래 형태로 부분 작업을 요청할 수 있습니다.

| 서브명령                                 | 동작                                                        |
| ---------------------------------------- | ----------------------------------------------------------- |
| `/osom-init`                             | 전체 초기화 (기존 파일이 있으면 모드 선택)                  |
| `/osom-init detect`                      | 감지 결과만 출력, 파일은 쓰지 않음                          |
| `/osom-init add-role <role>`             | `Enabled roles`에 역할 추가 + 필요한 Phase hint 자동 추가   |
| `/osom-init remove-role <role>`          | `Enabled roles`에서 역할 제거                               |
| `/osom-init set-command <key> <command>` | `Commands` 섹션의 특정 키 값 변경 (예: `set-command Build/Check "npm run verify"`) |
| `/osom-init validate`                    | 현재 `.osom-skills`가 실제 프로젝트 상태와 일치하는지 검사  |

## validate 서브명령 세부

`/osom-init validate`는 다음을 확인합니다:

- `Commands`의 스크립트가 `package.json`에 실제로 있는지
- `Project documents`의 경로가 실제로 존재하는지
- `Enabled roles`의 각 역할이 osom-skills 저장소에 실제로 존재하고 프로젝트와 맞는지
- `Required companion files` 글롭이 실제 프로젝트에서 의미 있는지 (해당 패턴의 파일이 하나도 없으면 경고)

결과는 각 항목별 ✅/⚠ 로 표시하고, 수정 제안을 함께 출력합니다. **자동 수정은 하지 않습니다** — 사용자가 필요하면 `/osom-init` 또는 해당 서브명령을 다시 호출합니다.

## 주의사항

- **사용자 승인 없이 `.osom-skills`를 쓰지 마세요.** 특히 기존 파일 덮어쓰기 전에는 반드시 확인을 받습니다.
- **자동 감지가 확실하지 않은 값은 드래프트에 명시적으로 표시**하고 사용자에게 질문하세요(추측 금지).
- 섹션 이름(`## Commands`, `## Project documents` 등)을 임의로 바꾸지 마세요. 다른 스킬이 이 이름으로 값을 조회합니다.
- 이 스킬은 **설정 파일만 다룹니다**. 실제 프로젝트 디렉토리 생성(예: `docs/plans/` 만들기)이나 스크립트 추가는 수행하지 않습니다 — 필요하면 사용자에게 안내만 합니다.
