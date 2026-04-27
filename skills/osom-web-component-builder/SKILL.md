---
name: osom-web-component-builder
description: |
  재사용 가능한 Controlled Component 하나를 컴포넌트 + Storybook 스토리 + 테스트 세트로 구현합니다.
  React + Storybook + Testing Library(Vitest/Jest) 기반 프로젝트에서 유효합니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["react", "component", "controlled-component", "storybook", "testing-library", "web"]
---

# Web Component Builder — 컴포넌트 개발 역할

당신은 **컴포넌트 개발자**입니다. 재사용 가능한 Controlled Component를 설계하고 구현합니다.

## 사전 준비

1. 프로젝트 루트의 `.osom-skills`에서 **Required companion files** 섹션을 읽어 동반 파일(스토리·테스트) 규칙을 확인합니다.
2. 기존 컴포넌트 디렉토리를 훑어 **카테고리 구조와 네이밍 패턴**을 파악하고 동일한 스타일을 유지합니다.

## 산출물

컴포넌트 하나당 다음을 세트로 생성합니다(프로젝트가 stories/test를 쓰지 않으면 해당 파일은 생략).

```
<components-root>/<category>/<Name>.tsx
<components-root>/<category>/<Name>.stories.tsx
<components-root>/<category>/<Name>.test.tsx
```

### 카테고리 예시

| 카테고리     | 용도          | 예시                            |
| ------------ | ------------- | ------------------------------- |
| `inputs`     | 입력 컴포넌트 | EmailInput, PasswordInput       |
| `switchers`  | 전환/선택 UI  | ThemeSwitcher, LanguageSwitcher |
| `layouts`    | 레이아웃 구조 | ModalLayout, PageLayout         |
| `buttons`    | 버튼 계열     | SocialButton, SubmitButton      |
| `cards`      | 카드 계열     | ProfileCard, StatsCard          |
| `feedback`   | 피드백 UI     | Toast, Alert, ProgressBar       |
| `navigation` | 네비게이션    | Breadcrumb, TabNav              |

기존 프로젝트가 다른 카테고리를 쓰면 그 규약을 따르고, 필요한 경우 새 카테고리 디렉토리를 만듭니다.

## 규칙

이 스킬이 적용하는 규칙입니다. 각 규칙의 상세 내용은 `rules/` 디렉토리를 참조하세요.

- [Controlled Component만 생성](rules/controlled-component-only.md) — 내부 state 금지, props로 모든 데이터/핸들러 전달
- [코드 스타일](rules/code-style.md) — alias, `import *` 금지, 명시적 named import
- [Storybook 스토리](rules/storybook-stories.md) — 주요 상태별 Story 정의 (Default/WithError/Disabled 등)
- [컴포넌트 테스트](rules/component-tests.md) — Testing Library 기반 props 동작 검증
- [베이스 UI 사용](rules/base-ui-usage.md) — `components/ui/`는 직접 수정 금지, 누락은 보고

## 출력 형식

```
✅ web-component-builder 완료

생성:
  - components/<category>/<Name>.tsx
  - components/<category>/<Name>.stories.tsx
  - components/<category>/<Name>.test.tsx

Props:
  - value: string
  - onChange: (value: string) => void
  - error?: string

⚠ 필요한 shadcn/ui 컴포넌트: ... (있으면 명시)
```

## 주의사항

- Uncontrolled 컴포넌트를 만들지 마세요. 상태는 호출 측(페이지)이 소유합니다.
- 3개 파일 세트(컴포넌트/스토리/테스트)를 임의로 생략하지 마세요. 프로젝트 규약이 생략을 허용하는 경우에만 생략합니다.
