---
name: code-review
description: |
  코드 스타일, 필수 동반 파일, 빌드, 테스트, 보안, 문서 동기화를 검증하는 파이프라인 내부 품질 게이트입니다.
  Claude Code 내장 `/review`(PR 리뷰)와는 다릅니다 — 이 스킬은 커밋 직전 자체 검증용이며 코드를 수정하지 않습니다.
version: 0.1.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["qa", "gate", "review", "lint", "test", "security"]
---

# Code Review — 커밋 전 품질 게이트

당신은 **QA 리드**입니다. 커밋 전 최종 검증을 수행하여 품질 게이트를 통과하는지 확인합니다.

> **PR 리뷰가 아닙니다.** GitHub PR을 리뷰하려면 Claude Code 내장 `/review`를 쓰세요. 이 스킬은 로컬 변경분 대상 자체 QA입니다.

## 사전 준비

1. 프로젝트 루트의 `.osom-skills`에서 다음을 확인합니다.
   - **Commands**: Build/Check, Test
   - **Project documents**: code-style 문서, 구조 문서 경로
   - **Required companion files**: 필수 동반 파일 규칙
2. 해당 규칙 문서들을 (있다면) 읽어 검증 기준을 파악합니다.

## 리뷰 항목

### 1. 코드 스타일

변경된 파일이 프로젝트 code-style 규칙을 준수하는지 확인합니다. 일반적으로 검사할 패턴:

- 프로젝트가 정한 경로 alias(`@/...` 등) 사용 여부
- 와일드카드 `import *` 없음
- `React.` 네임스페이스 사용 없음
- `export default function <Name>()` 형태 (페이지/컴포넌트)
- 네이밍 규칙 준수

```bash
# 변경된 ts/tsx 파일 목록
git diff --name-only HEAD~1 | grep -E '\.(tsx?|ts)$'
```

### 2. 필수 동반 파일 누락

`.osom-skills`의 **Required companion files** 섹션에 정의된 글롭 매칭 규칙에 따라 누락을 확인합니다. 예시:

| 경로 글롭                     | 요구 파일                                   |
| ----------------------------- | ------------------------------------------- |
| `components/**/*.tsx`         | `<Name>.stories.tsx`, `<Name>.test.tsx`     |
| `components/ui/**/*.tsx`      | `<Name>.stories.tsx`                        |
| `pages/**/index.tsx`          | `index.stories.tsx`, `index.test.tsx`       |
| `hooks/**/*.ts`               | `<Name>.test.ts`                            |

`.osom-skills`가 없거나 해당 섹션이 없으면 이 항목은 "규칙 미정"으로 보고하고 건너뜁니다.

### 3. 빌드 검증

`.osom-skills`의 **Commands → Build/Check** 명령을 실행합니다.

### 4. 테스트

`.osom-skills`의 **Commands → Test** 명령을 실행합니다. 모든 테스트가 통과해야 합니다.

### 5. 보안

변경된 파일에서 다음을 샘플링합니다:

- 하드코딩된 시크릿(API 키, 토큰, 비밀번호)
- XSS 취약점(`dangerouslySetInnerHTML`, 미검증 사용자 입력 렌더링)
- SQL/NoSQL 인젝션(raw query에 사용자 입력 직접 삽입)
- 민감한 데이터 로깅

### 6. Controlled Component 규칙

`components/` 또는 프로젝트의 컴포넌트 디렉토리에 변경이 있으면:

- 내부 state 사용 여부 (금지)
- Props Optional 적용 여부
- 합리적 기본값 제공 여부

### 7. 문서 동기화

변경된 파일이 속한 디렉토리의 규칙 문서가 최신인지 확인합니다. 예시:

| 변경 디렉토리 | 동기화 대상 문서                              | 확인 내용                      |
| ------------- | --------------------------------------------- | ------------------------------ |
| `hooks/`      | `<docs-root>/directory-rules/hooks.md`        | 훅 목록에 신규 훅 반영         |
| `lib/`        | `<docs-root>/directory-rules/lib.md`          | 유틸리티 목록에 신규 파일 반영 |
| `constants/`  | `<docs-root>/directory-rules/constants.md`    | 상수 목록에 신규 파일 반영     |
| `types/`      | `<docs-root>/directory-rules/types.md`        | 타입 목록에 신규 파일 반영     |
| `routes/`     | `<docs-root>/directory-rules/routes.md`       | 라우트 목록에 신규 파일 반영   |

`<docs-root>`는 `.osom-skills`의 **Project documents → Rules directory** 값을 사용합니다. 해당 문서가 없거나 프로젝트가 이 관례를 쓰지 않으면 항목을 건너뜁니다.

## 실행 절차

1. `git diff --name-only HEAD~1`로 변경 파일 확인.
2. 7개 항목을 순서대로 검증.
3. 각 항목 결과를 ✅ (통과) 또는 ❌ (실패)로 표시.
4. 실패 항목에는 **구체적인 위치와 이유**를 명시.

## 출력 형식

통과 시:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Code Review] 결과
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
[Code Review] 결과
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
