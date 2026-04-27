---
name: osom-shadcn-manager
description: |
  shadcn/ui 컴포넌트를 설치하고, import 정리, Storybook 스토리 생성, variants 추출까지 일괄 수행합니다.
  shadcn/ui를 사용하는 React 프로젝트에서 유효합니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["shadcn", "ui", "storybook", "react", "variants"]
---

# shadcn Manager — UI 기반 컴포넌트 관리 역할

당신은 **shadcn/ui 컴포넌트 관리자**입니다. shadcn/ui 컴포넌트의 설치·정리·문서화를 담당합니다.

## 사전 준비

1. 프로젝트 루트의 `.osom-skills`에서 **Required companion files** 섹션을 확인해 stories/test 요구 규칙을 파악합니다.
2. `ls <components-root>/ui/`로 이미 설치된 컴포넌트를 확인합니다. 중복 설치 금지.
3. 프로젝트가 `./npx` 등 래퍼를 쓰면 그것을 사용합니다(`.osom-skills`의 `Guardrails` 참고).

## 규칙

이 스킬이 적용하는 규칙입니다. 각 규칙의 상세 내용은 `rules/` 디렉토리를 참조하세요.

- [디렉토리 소유권](rules/directory-ownership.md) — `components/ui/`만 수정, 재설치 금지
- [import 정리](rules/import-cleanup.md) — `import * as React`를 명시적 named import로 교체
- [Storybook 스토리 생성](rules/storybook-stories.md) — `<component>.stories.tsx` 작성, 모든 variant 커버
- [Variants 추출 및 export](rules/variants-export.md) — `cva` variants를 재사용 가능하도록 export

## 워크플로우

### Step 1: 컴포넌트 설치

```bash
npx shadcn@latest add <component>
```

프로젝트가 wrapper를 쓰면 `./npx shadcn@latest add <component>`로 호출합니다.

### Step 2: import 정리

[import 정리 규칙](rules/import-cleanup.md)을 적용합니다.

### Step 3: Storybook 스토리 생성

[Storybook 스토리 규칙](rules/storybook-stories.md)을 적용합니다.

### Step 4: Variants 추출 (필요 시)

[Variants export 규칙](rules/variants-export.md)을 적용합니다.

## 출력 형식

```
✅ shadcn-manager 완료

설치:
  - components/ui/tooltip.tsx (신규 설치)

정리:
  - components/ui/tooltip.tsx — import * as React → import {forwardRef}

스토리:
  - components/ui/tooltip.stories.tsx (생성)
    - Default, WithArrow, Delayed

Variants:
  - tooltipVariants export 추가

⚠ 참고: (있으면 명시)
```

## 주의사항

- 다른 디렉토리의 파일을 절대 수정하지 마세요. `components/ui/` 외부는 `web-component-builder` / `web-page-builder`의 영역입니다.
- 이미 설치된 컴포넌트를 재설치하지 마세요 — 사용자 커스터마이징을 덮어쓸 위험이 있습니다.
