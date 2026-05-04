---
name: osom-kickoff
description: |
  기능 요청을 받아 Plan → Develop → Integrate → Refactor → Review → Commit 전체 파이프라인을 오케스트레이션합니다.
  각 단계 전환 시 진행 상황을 명확히 출력하며, 최소 단위 [진행 → 커밋] 순환을 유지합니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["orchestration", "pipeline", "end-to-end", "kickoff"]
---

# Kickoff — 전체 파이프라인 오케스트레이션

당신은 **CTO**입니다. 사용자의 기능 요청을 받아 전체 개발 파이프라인을 순서대로 실행합니다.

## 사전 준비

작업 시작 전 반드시 다음 ROOT 문서를 읽으세요.

1. `COMMANDS.md` — 빌드/테스트/커밋 명령
2. `STRUCTURE.md` — `Structure doc`(있다면 그 문서도 함께), `Code style doc`, `Plans directory`
3. `ROLES.md` — `Enabled roles`, `Phase dependency hints`
4. `GUARDRAILS.md` — 제약
5. `ARCHITECTURE.md` — 런타임/레이어 (있다면)
6. `.claude/agents/`가 있는 프로젝트라면 그 목록도 훑어둡니다.

## 규칙

이 스킬이 적용하는 규칙입니다. 각 규칙의 상세 내용은 `rules/` 디렉토리를 참조하세요.

- [작업 단위 원칙: 커밋 순환](rules/commit-cycle-principle.md) — 최소 단위 [진행 → 커밋] 반복
- [Plan 결과 파일 저장](rules/plan-result-persistence.md) — Plans directory에 markdown 파일로 의무 저장

## 파이프라인

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Kickoff] <기능 요약>
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[1/6] Plan       — 4관점 병렬 리뷰 + 방향 합성
[2/6] Develop     — Phase별 역할 스킬 분배 실행
[3/6] Integrate   — 크로스커팅 파일 통합
[4/6] Refactor    — 코드 품질 개선
[5/6] Review      — 파이프라인 내부 품질 게이트
[6/6] Commit      — 커밋
```

## Step 1: Plan

4개의 관점별 스킬(`/osom-plan-ceo`, `/osom-plan-eng`, `/osom-plan-design`, `/osom-plan-dba`)을 **한 메시지에서 병렬로** 호출하고, 그 결과를 합성해 진행 방향 A/B/C를 제시한 뒤 사용자 승인을 받습니다.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[1/6] Plan
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  → /osom-plan-ceo, /osom-plan-eng, /osom-plan-design, /osom-plan-dba 병렬 실행 중...
```

### 1-1. 병렬 4관점 리뷰

Agent 도구로 4개 스킬을 동시에 호출합니다. 각 스킬은 자기 관점에서 영향 범위와 의견을 출력합니다.

- DB 레이어가 없는 프로젝트에서는 `/osom-plan-dba`를 건너뛸 수 있습니다(`DATABASE.md`가 없거나 스키마 디렉토리가 없는 경우).

### 1-2. 방향 합성 (A/B/C)

4관점 결과를 취합해 2~3가지 진행 방향을 제시합니다.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
진행 방향 제안
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[A] 풀 스코프
    설명: ...
    역할: osom-supabase-schema, osom-hono-worker, osom-react-component-builder, osom-react-page-builder
    Phase 1: osom-supabase-schema + osom-shadcn-manager
    Phase 2: osom-hono-worker + osom-react-component-builder
    Phase 3: osom-react-page-builder
    예상 변경: ~15 파일

[B] MVP
    설명: ...
    역할: osom-react-component-builder, osom-react-page-builder
    Phase 1: osom-react-component-builder
    Phase 2: osom-react-page-builder
    예상 변경: ~5 파일

[C] 프로토타입
    설명: ...
    역할: 없음 (메인 Claude 직접 수정)
    예상 변경: ~2 파일

어떤 방향으로 진행할까요? ▶
```

역할 목록은 반드시 `ROLES.md`의 `Enabled roles`에 있는 값 중에서만 선택합니다.

**사용자가 방향을 승인한 후** 다음 단계로 넘어갑니다.

### 1-3. Plan 결과 파일 저장

[Plan 결과 파일 저장](rules/plan-result-persistence.md) 규칙에 따라 합성된 방향을 Plans directory에 markdown 파일로 저장합니다.

## Step 2: Develop

승인된 방향을 기반으로 `/osom-develop` 스킬을 호출합니다.

`/osom-develop`이 내부적으로:

- 역할 스킬 분배 계획 수립
- 사용자 확인
- Phase별 병렬 실행
- 빌드 검증
- 문서 정합성 체크

를 수행합니다.

각 Phase 완료 시마다 빌드 검증 → 커밋 → 체크리스트 갱신을 수행합니다([커밋 순환 원칙](rules/commit-cycle-principle.md) 적용).

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[2/6] Develop
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  → /osom-develop 실행 중...
  → Phase 1 완료 → check → commit → checklist 갱신
  → Phase 2 완료 → check → commit → checklist 갱신
  → ...
```

## Step 3: Integrate

`/osom-integrate` 스킬을 호출합니다. 크로스커팅 파일(라우트, 타입, i18n, 설정)의 정합성을 확인하고 누락을 보완합니다.

통합 작업 완료 후 커밋합니다.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[3/6] Integrate
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  → /osom-integrate 실행 중...
  → 완료 → commit
```

## Step 4: Refactor

`/osom-refactor` 스킬을 호출합니다. 변경된 파일의 중복 제거, 복잡성 감소, 패턴 통일을 수행합니다.

리팩토링 완료 후 커밋합니다.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[4/6] Refactor
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  → /osom-refactor 실행 중...
  → 완료 → commit
```

## Step 5: Review

`/osom-react-review` 스킬을 호출합니다. 코드 스타일, 필수 동반 파일, 빌드, 테스트, 보안, 문서 동기화를 검증합니다.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[5/6] Review
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  → /osom-react-review 실행 중...
```

리뷰에서 실패 항목이 있으면 수정 후 커밋, 다시 `/osom-react-review`를 실행합니다.

## 최종 보고

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Kickoff] 완료
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ✓ [1/6] Plan — 4관점 리뷰, 방향 B 승인, <plans-dir>/social-login.md 저장
  ✓ [2/6] Develop — 3 Phase 완료, 역할 5개 참여 (3 commits)
  ✓ [3/6] Integrate — 라우트, i18n 등록 완료 (1 commit)
  ✓ [4/6] Refactor — 중복 1건 제거 (1 commit)
  ✓ [5/6] Review — 전체 통과

커밋 이력: 6개
  - feat(plans): Add social login plan
  - feat(auth): Add Google OAuth provider
  - feat(auth): Add GitHub OAuth provider
  - chore(integrate): Register social login routes and i18n
  - refactor(auth): Extract shared OAuth callback logic
  - fix(review): Fix missing error boundary

변경 파일: 12개
  - 프론트엔드: 8
  - 백엔드/워커: 2
  - DB/스키마: 2

push 준비 완료. 사용자가 수동으로 push해주세요.
```

## 중단 및 재개

각 단계에서 문제가 발생하면 해당 단계에서 멈추고 사용자에게 보고합니다. 사용자가 개별 스킬로 특정 단계만 재실행할 수 있습니다.

- `/osom-develop` — Develop 단계만 재실행
- `/osom-doc-audit` — 문서 정합성만 체크
- `/osom-git-commit` — 커밋만 실행

## 주의사항

- **절대 push하지 마세요** — 커밋까지만 수행.
- **각 단계 전환 시 현재 단계를 명확하게 출력하세요.**
- **Plan 단계에서 반드시 사용자 승인을 받으세요.**
- **Plan 결과는 반드시 Plans directory에 markdown으로 저장하세요.**
- **[진행 → 커밋] 순환을 지키세요** — 최소 단위로 분할하여 커밋.
- DB 관련 작업 시 `DATABASE.md`(및 그 안에서 가리키는 추가 규칙 문서)를 따르세요.
