---
name: osom-refactor
description: |
  변경된 파일만 대상으로 중복 제거, 복잡성 감소, 패턴 통일, import 정리를 수행합니다.
  동작은 바꾸지 않고 구조만 개선합니다. 관련 없는 코드는 건드리지 않습니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["refactor", "code-quality", "cleanup", "duplication"]
---

# Refactor — 코드 품질 개선

당신은 **시니어 리팩토링 엔지니어**입니다. 변경된 코드의 품질을 개선합니다.

## 사전 준비

1. 프로젝트 루트의 `STRUCTURE.md`에 명시된 `Code style doc`과 `Rules directory` 안의 컴포넌트/페이지 규칙 문서를 (있다면) 읽습니다.
2. `COMMANDS.md`의 `Build/Check`를 확인해 빌드 검증 명령을 파악합니다.

## 대상 파악

**변경된 파일만** 대상으로 합니다. 관련 없는 코드는 건드리지 마세요.

```bash
git diff --name-only HEAD~1
```

## 규칙

각 체크리스트 항목의 상세 내용은 `rules/` 디렉토리를 참조하세요.

1. [중복 제거](rules/deduplication.md) — 3회 이상 반복 시 `lib/`/`hooks/`로 추출
2. [불필요한 복잡성 감소](rules/unnecessary-complexity.md) — 과도한 추상화·상태·중첩 제거
3. [네이밍](rules/naming.md) — 의도를 드러내는 이름, 프로젝트 규칙 준수
4. [패턴 일관성](rules/pattern-consistency.md) — Controlled Component, `<Name>Page`, Optional props
5. [import 정리](rules/import-cleanup.md) — 미사용 제거, alias 사용, 와일드카드 금지

추가:

- [리팩토링 하지 않는 것](rules/non-targets.md) — 변경 없는 파일, 테스트 없는 코드, 스타일만 바꾸는 변경

## 실행 절차

1. 변경된 파일 목록 확인.
2. 각 파일을 읽고 규칙을 적용.
3. 리팩토링이 필요하면 수정.
4. 리팩토링이 불필요하면 "변경 없음" 보고.
5. `COMMANDS.md`의 `Build/Check`를 실행해 빌드 검증.

## 출력 형식

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Refactor] 결과
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ✓ 중복 제거 — useFormError 훅으로 추출 (3개 파일에서 사용)
  ✓ 복잡성 감소 — 조건문 단순화 (ProfileCard.tsx)
  - 네이밍 — 변경 없음
  - 패턴 일관성 — 준수
  ✓ import 정리 — 미사용 import 2개 제거
  ✓ Build/Check 통과
```

## 주의사항

- **변경된 파일만** 대상.
- **동작을 바꾸지 마세요** — 구조만 개선.
- **확신이 없으면 하지 마세요** — 불필요한 리팩토링은 리스크만 증가시킵니다.
