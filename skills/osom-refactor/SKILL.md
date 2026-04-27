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

1. 프로젝트 루트의 `.osom-skills`에서 **Project documents**에 명시된 code-style / component / page 규칙 문서를 (있다면) 읽습니다.
2. **Commands → Build/Check**를 확인해 빌드 검증 명령을 확인합니다.

## 대상 파악

**변경된 파일만** 대상으로 합니다. 관련 없는 코드는 건드리지 마세요.

```bash
git diff --name-only HEAD~1
```

## 리팩토링 체크리스트

### 1. 중복 제거

- 동일 로직이 여러 파일에 반복되는지
- 공통 로직을 `lib/`이나 `hooks/`로 추출할 수 있는지
- **3회 미만 반복은 추출하지 않음** (조기 추상화 금지)

### 2. 불필요한 복잡성

- 과도한 추상화
- 불필요한 상태 관리
- 간단하게 표현할 수 있는 코드가 복잡하게 작성된 경우

### 3. 네이밍

- 변수·함수·컴포넌트 이름이 의도를 명확히 전달하는지
- 프로젝트의 네이밍 규칙을 따르는지

### 4. 패턴 일관성

- 기존 코드와 동일한 패턴을 사용하는지
- Controlled Component 규칙(props 기반, 내부 state 금지)을 지키는지
- 페이지 네이밍(`<Name>Page`) 규칙을 지키는지
- Props Optional 규칙을 지키는지

### 5. import 정리

- 사용하지 않는 import 제거
- 프로젝트가 쓰는 alias(`@/...`) 사용
- 와일드카드 `import *` 제거

## 리팩토링 하지 않는 것

- 동작하는 코드의 스타일만 바꾸는 것
- 변경되지 않은 파일의 "개선"
- 테스트가 없는 코드의 구조 변경
- 단순히 "더 깔끔해 보이는" 변경

## 실행 절차

1. 변경된 파일 목록 확인.
2. 각 파일을 읽고 체크리스트 적용.
3. 리팩토링이 필요하면 수정.
4. 리팩토링이 불필요하면 "변경 없음" 보고.
5. `.osom-skills`의 **Commands → Build/Check**를 실행해 빌드 검증.

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
- **확신이 없으면 하지 마세요** — 불필요한 리팩토링은 리스크만 증가합니다.
