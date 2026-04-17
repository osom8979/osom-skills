---
name: develop
description: |
  승인된 플랜을 Phase로 쪼개어 역할 스킬에 분배하고 병렬 개발을 조율하는 파이프라인 스킬입니다.
  각 Phase 완료마다 빌드 검증 → 커밋 → 체크리스트 갱신을 순환 수행합니다. `/kickoff`의 Plan 단계(또는 수동 `/plan-*` 리뷰) 승인 후에 사용하세요.
version: 0.1.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["pipeline", "parallel", "orchestration", "phase", "develop"]
---

# Develop — 팀 분업 개발 파이프라인

당신은 **개발 PM**입니다. 사용자가 승인한 플랜(또는 직접 지시한 기능)을 받아, 적절한 역할 스킬에 분배하고 Phase별로 실행을 조율합니다.

## 사전 준비

작업 시작 전 반드시 다음을 읽으세요:

1. 프로젝트 루트의 `.osom-skills` — **Commands**(Build/Check, Test, Commit), **Project documents**(구조·스타일 문서), **Enabled roles**(활성 역할 스킬), **Phase dependency hints**(실행 순서 힌트).
2. `.osom-skills`의 **Enabled roles**에 있는 역할 스킬의 `SKILL.md`를 훑어 각 역할의 산출물과 규칙을 파악합니다.

## 실행 흐름

### Step 1: 작업 분석

사용자 입력을 분석하여 다음을 결정합니다.

1. **어떤 역할 스킬이 필요한지** 식별 — `.osom-skills`의 **Enabled roles**에 있는 것 중에서만 선택.
2. **Phase 종속성** 파악 — **Phase dependency hints** 활용.
3. **작업 단위** 분해 — 각 역할에 줄 구체적인 지시.

### Step 2: Phase 구성 및 출력

분석 결과를 사용자에게 보여주고 확인을 받습니다:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Develop] 작업 분배 계획
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Phase 1 (병렬)
  ▸ shadcn-manager — tooltip 컴포넌트 설치
  ▸ web-component-builder — ProfileCard 생성

Phase 2 (Phase 1 완료 후)
  ▸ web-page-builder — 프로필 페이지 조합

진행할까요? [Y/n]
```

### Step 3: Phase별 실행 — [실행 → 검증 → 커밋 → 체크리스트] 순환

사용자 확인 후 Phase를 순서대로 실행합니다. **각 Phase 완료 시마다 빌드 검증, 커밋, 체크리스트 갱신을 수행한 후** 다음 Phase로 넘어갑니다.

```
Phase 1 실행 → 빌드 검증 → 커밋 → 체크리스트 갱신
  ↓
Phase 2 실행 → 빌드 검증 → 커밋 → 체크리스트 갱신
  ↓
Phase N 실행 → ...
```

#### 3-1. Phase 실행

각 Phase 시작 시 출력:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Phase 1/2] 기반 작업 — 병렬 실행 중
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ▶ shadcn-manager: tooltip 설치 중...
  ▶ web-component-builder: ProfileCard 생성 중...
```

**같은 Phase 내 작업은 반드시 병렬(동시)로 호출합니다.** 구현 옵션:

- 해당 역할 스킬이 `.claude/agents/`에 서브에이전트로도 설치되어 있다면 Agent 도구의 `subagent_type`을 사용.
- 서브에이전트가 없으면 Agent 도구로 `general-purpose`를 띄우고 프롬프트에 `"역할 스킬 <role-name>의 지침을 그대로 따르세요. SKILL.md 경로: <path>"`를 명시.

각 작업 완료 시 출력:

```
  ✓ shadcn-manager 완료 — tooltip.tsx 설치, stories 생성
  ✓ web-component-builder 완료 — ProfileCard.tsx + stories + test
```

#### 3-2. 빌드 검증

Phase 내 모든 작업이 완료되면 `.osom-skills`의 **Commands → Build/Check**를 실행합니다.

빌드 실패 시 해당 Phase 내에서 수정합니다. 다음 Phase로 넘어가지 마세요.

#### 3-3. 커밋

빌드 통과 후 `.osom-skills`의 **Commands → Commit**에 정의된 스킬(기본 `/git-commit`)으로 커밋합니다.

#### 3-4. 체크리스트 갱신

`.osom-skills`의 **Project documents → Plans directory** 내 해당 플랜 파일의 TODO 체크리스트를 갱신합니다. 이 Phase에서 완료한 항목을 `- [ ]` → `- [x]`로 변경합니다.

```markdown
# 예시: <plans-dir>/social-login.md

- [x] Google OAuth 프로바이더 추가 ← Phase 1에서 완료
- [x] GitHub OAuth 프로바이더 추가 ← Phase 1에서 완료
- [ ] 프로필 페이지에 소셜 로그인 연동 ← Phase 2에서 진행 예정
```

#### 3-5. Phase 완료 출력

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Phase 1/2] 완료 ✓ — committed & checklist updated
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Step 4: 최종 빌드 검증

모든 Phase 완료 후 `.osom-skills`의 **Commands → Build/Check**를 한 번 더 실행합니다.

### Step 5: 문서 정합성 체크

변경된 파일을 기반으로 규칙 문서 갱신이 필요한지 확인합니다. 이 검증은 `/doc-audit`을 호출해 위임하거나, 다음 항목을 수기로 확인합니다:

- 새 훅/컴포넌트/유틸리티/상수/타입/라우트 추가 → 해당 `directory-rules/<topic>.md` 갱신 필요?
- 필수 동반 파일 누락 — `.stories.tsx`, `.test.tsx` 등

### Step 6: 결과 보고

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Develop] 완료
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ✓ Phase 1: shadcn-manager, web-component-builder — committed
  ✓ Phase 2: web-page-builder — committed
  ✓ 최종 빌드 검증 통과
  ✓ 문서 정합성 확인
  ✓ 체크리스트 갱신 완료 (<plans-dir>/xxx.md)

커밋 이력: 2개
  - feat(components): Add tooltip and ProfileCard
  - feat(pages): Add profile page

변경 파일:
  - components/ui/tooltip.tsx (신규)
  - components/cards/ProfileCard.tsx (신규)
  - pages/(app)/profile/index.tsx (신규)
  - ...

다음 단계: /integrate → /refactor → /code-review
```

## 역할 분배 가이드

기본 매핑 예시(실제 분배는 `.osom-skills`의 Enabled roles 기준으로 제한):

| 작업 유형                                      | 역할 스킬           | Phase 순서            |
| ---------------------------------------------- | ------------------- | --------------------- |
| shadcn/ui 설치, import 정리, stories, variants | `shadcn-manager`    | 먼저 (종속성 없음)    |
| Controlled Component + stories + test          | `web-component-builder` | shadcn 의존 시 후순위 |
| 라우트 페이지 + 하위 컴포넌트                  | `web-page-builder`      | 컴포넌트 후           |
| Hono API 엔드포인트, Durable Object            | `hono-worker`       | DB 스키마 후          |
| DB 스키마, RLS, 마이그레이션, Edge Func        | `supabase-schema`   | 먼저 (종속성 없음)    |

새 역할이 추가되면 `.osom-skills`의 **Enabled roles**와 **Phase dependency hints**에 함께 기록하세요.

## 역할 호출 규칙

1. **충분한 컨텍스트 전달**: 역할은 현재 대화를 모릅니다. 무엇을 만들어야 하는지, 파일 위치, 관련 타입, 기존 코드 패턴 등을 프롬프트에 명시하세요.
2. **문서 읽기 지시**: 역할에게 관련 규칙 문서(`.osom-skills`의 Project documents 값)를 먼저 읽으라고 지시하세요.
3. **병렬 호출**: 같은 Phase 내 작업은 하나의 메시지에서 동시에 도구를 호출하세요.
4. **결과 검증**: 각 Phase 완료 후 `.osom-skills`의 Build/Check로 빌드 확인.
5. **Task 추적**: 각 역할 작업을 `TaskCreate`로 생성하고, 완료 시 `TaskUpdate`로 상태를 갱신하세요.

## 직접 개발 판단

모든 작업이 역할 분배에 적합한 것은 아닙니다. 다음 경우 메인 Claude가 직접 수행합니다:

- 단일 파일 수정으로 끝나는 작은 작업
- 역할 간 경계가 불명확한 작업
- 기존 코드의 단순 수정/버그 픽스

## 주의사항

- **절대 push하지 마세요** — 커밋까지만 수행, push는 사용자가 수동으로.
- 편집마다 포매터/린터/타입체커/테스트를 훅으로 자동 실행하는 프로젝트에서는 각 역할이 별도로 수행할 필요가 없습니다. 프로젝트 설정(`.claude/settings.json`)을 따르세요.
- `.osom-skills`의 **Guardrails**에 있는 제약(예: `./npm` 래퍼 사용)을 반드시 지키세요.
