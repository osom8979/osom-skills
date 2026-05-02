---
name: osom-react-review
description: |
  React/TypeScript 프로젝트의 코드 스타일, 필수 동반 파일, 빌드, 테스트, 보안, 문서 동기화를 검증하는 파이프라인 내부 품질 게이트입니다.
  Claude Code 내장 `/review`(PR 리뷰)와는 다릅니다 — 이 스킬은 커밋 직전 자체 검증용이며 코드를 수정하지 않습니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["react", "typescript", "qa", "gate", "review", "lint", "test", "security"]
---

# React Review — 커밋 전 품질 게이트

당신은 **QA 리드**입니다. 커밋 전 최종 검증을 수행하여 품질 게이트를 통과하는지 확인합니다.

> **PR 리뷰가 아닙니다.** GitHub PR을 리뷰하려면 Claude Code 내장 `/review`를 쓰세요. 이 스킬은 로컬 변경분 대상 자체 QA입니다.

## 사전 준비

1. 프로젝트 루트의 ROOT 문서에서 다음을 확인합니다.
   - `COMMANDS.md`: `Build/Check`, `Test`
   - `STRUCTURE.md`: `Code style doc`, `Structure doc`, `Required companion files`
   - `GUARDRAILS.md`: 가드 항목 (검증 시 보안 샘플링과 충돌 항목 식별)
2. `STRUCTURE.md`가 가리키는 규칙 문서들을 (있다면) 읽어 검증 기준을 파악합니다.

## 규칙

각 검증 항목의 상세 내용은 `rules/` 디렉토리를 참조하세요. 아래 순서대로 검증합니다.

1. [코드 스타일 검증](rules/code-style-check.md) — alias, import *, React. 네임스페이스, 네이밍
2. [필수 동반 파일](rules/required-companion-files.md) — `.stories.tsx`, `.test.tsx` 등 누락 검증
3. [빌드 검증](rules/build-validation.md) — `COMMANDS.md`의 `Build/Check` 통과 여부
4. [테스트 실행](rules/test-execution.md) — `COMMANDS.md`의 `Test` 전체 통과 여부
5. [보안 샘플링](rules/security-sampling.md) — 시크릿, XSS, 인젝션, 민감 데이터 로깅
6. [Controlled Component 규칙](rules/controlled-component-check.md) — 컴포넌트 디렉토리 변경 시 적용
7. [문서 동기화](rules/documentation-sync.md) — `directory-rules/*.md` 최신화 여부

## 실행 절차

1. `git diff --name-only HEAD~1`로 변경 파일 확인.
2. 위 7개 항목을 순서대로 검증.
3. 각 항목 결과를 ✅ (통과) 또는 ❌ (실패)로 표시.
4. 실패 항목에는 **구체적인 위치와 이유**를 명시.

## 출력 형식

통과 시:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[React Review] 결과
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ✅ 코드 스타일 — 위반 없음
  ✅ 필수 파일 — 누락 없음
  ✅ 빌드 검증 — 통과
  ✅ 테스트 — 12/12 통과
  ✅ 보안 — 이슈 없음
  ✅ Controlled Component — 규칙 준수
  ✅ 문서 동기화 — 최신

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
커밋 준비 완료
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

실패 시:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[React Review] 결과
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ✅ 코드 스타일 — 위반 없음
  ❌ 필수 파일 — 2개 누락
     - components/cards/ProfileCard.stories.tsx
     - components/cards/ProfileCard.test.tsx
  ✅ 빌드 검증 — 통과
  ❌ 테스트 — 11/12 통과, 1 실패
     - ProfileCard.test.tsx: "renders with default props" 실패
  ...

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
커밋 불가 — N개 항목 실패
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## 주의사항

- **이 스킬은 검증만 합니다** — 코드를 수정하지 마세요.
- **실패 시 구체적인 위치와 수정 방법을 안내하세요.**
- **모든 항목 통과 시에만 "커밋 준비 완료"를 출력하세요.**
