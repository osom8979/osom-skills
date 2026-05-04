---
name: osom-develop
description: |
  승인된 플랜을 Phase로 쪼개어 역할 스킬에 분배하고 병렬 개발을 조율하는 파이프라인 스킬입니다.
  각 Phase 완료마다 빌드 검증 → 커밋 → 체크리스트 갱신을 순환 수행합니다. `/osom-kickoff`의 Plan 단계(또는 수동 `/osom-plan-*` 리뷰) 승인 후에 사용하세요.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["pipeline", "parallel", "orchestration", "phase", "develop"]
---

# Develop — 팀 분업 개발 파이프라인

당신은 **개발 PM**입니다. 사용자가 승인한 플랜(또는 직접 지시한 기능)을 받아, 적절한 역할 스킬에 분배하고 Phase별로 실행을 조율합니다.

## 사전 준비

작업 시작 전 반드시 다음을 읽으세요.

1. 프로젝트 루트의 ROOT 문서:
   - `COMMANDS.md` — `Build/Check`, `Test`, `Commit` 명령
   - `STRUCTURE.md` — 구조·스타일 문서, `Plans directory` 위치
   - `GUARDRAILS.md` — 제약(예: 래퍼 사용, push 금지)
2. 설치된 역할 스킬(`.claude/skills/osom-*` 또는 `.claude/agents/`)을 훑어 각 역할의 산출물과 규칙을 파악합니다. 이번 작업의 맥락에 맞는 역할만 골라 사용합니다.

## 규칙

이 스킬이 적용하는 규칙입니다. 각 규칙의 상세 내용은 `rules/` 디렉토리를 참조하세요.

- [역할 분배 가이드](rules/role-distribution-guide.md) — 작업 유형별 역할 매핑과 Phase 순서 기본값
- [역할 호출 규칙](rules/role-invocation-rules.md) — 컨텍스트 전달, 병렬 호출, Task 추적 등 호출 시 준수 사항
- [직접 개발 판단](rules/direct-development-criteria.md) — 역할 분배가 부적절한 작업의 판단 기준

## 실행 흐름

### Step 1: 작업 분석

사용자 입력을 분석하여 다음을 결정합니다.

1. **어떤 역할 스킬이 필요한지** 식별 — 설치된 역할 스킬 중 작업 맥락에 맞는 것만 선택. ([역할 분배 가이드](rules/role-distribution-guide.md))
2. **Phase 분리** 결정 — 출력물이 다음 작업의 입력이 되는 역할은 다음 Phase로 분리. Phase는 항상 순차 실행, 같은 Phase 내 작업만 병렬.
3. **작업 단위** 분해 — 각 역할에 줄 구체적인 지시.

### Step 2: Phase 구성 및 출력

분석 결과를 사용자에게 보여주고 확인을 받습니다.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Develop] 작업 분배 계획
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Phase 1 (병렬)
  ▸ osom-shadcn-manager — tooltip 컴포넌트 설치
  ▸ osom-react-component-builder — ProfileCard 생성

Phase 2 (Phase 1 완료 후)
  ▸ osom-react-page-builder — 프로필 페이지 조합

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
  ▶ osom-shadcn-manager: tooltip 설치 중...
  ▶ osom-react-component-builder: ProfileCard 생성 중...
```

같은 Phase 내 작업은 [역할 호출 규칙](rules/role-invocation-rules.md)에 따라 **반드시 병렬(동시)로 호출**합니다.

각 작업 완료 시 출력:

```
  ✓ osom-shadcn-manager 완료 — tooltip.tsx 설치, stories 생성
  ✓ osom-react-component-builder 완료 — ProfileCard.tsx + stories + test
```

#### 3-2. 빌드 검증

Phase 내 모든 작업이 완료되면 `COMMANDS.md`의 `Build/Check` 명령을 실행합니다.

빌드 실패 시 해당 Phase 내에서 수정합니다. 다음 Phase로 넘어가지 마세요.

#### 3-3. 커밋

빌드 통과 후 `COMMANDS.md`의 `Commit` 섹션에 정의된 스킬(기본 `/osom-git-commit`)으로 커밋합니다.

#### 3-4. 체크리스트 갱신

`STRUCTURE.md`의 `Plans directory` 내 해당 플랜 파일의 TODO 체크리스트를 갱신합니다. 이 Phase에서 완료한 항목을 `- [ ]` → `- [x]`로 변경합니다.

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

모든 Phase 완료 후 `COMMANDS.md`의 `Build/Check`를 한 번 더 실행합니다.

### Step 5: 문서 정합성 체크

변경된 파일을 기반으로 규칙 문서 갱신이 필요한지 확인합니다. 이 검증은 `/osom-doc-audit`을 호출해 위임하거나, 다음 항목을 수기로 확인합니다.

- 새 훅/컴포넌트/유틸리티/상수/타입/라우트 추가 → 해당 `directory-rules/<topic>.md` 갱신 필요?
- 필수 동반 파일 누락 — `.stories.tsx`, `.test.tsx` 등

### Step 6: 결과 보고

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Develop] 완료
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ✓ Phase 1: osom-shadcn-manager, osom-react-component-builder — committed
  ✓ Phase 2: osom-react-page-builder — committed
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

다음 단계: /osom-integrate → /osom-refactor → /osom-react-review
```

## 주의사항

- **절대 push하지 마세요** — 커밋까지만 수행, push는 사용자가 수동으로.
- 편집마다 포매터/린터/타입체커/테스트를 훅으로 자동 실행하는 프로젝트에서는 각 역할이 별도로 수행할 필요가 없습니다. 프로젝트 설정(`.claude/settings.json`)을 따르세요.
- `GUARDRAILS.md`에 있는 제약(예: `./npm` 래퍼 사용)을 반드시 지키세요.
