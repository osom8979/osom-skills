---
name: osom-react-page-builder
description: |
  라우트에 매핑되는 페이지를 컴포넌트 조합과 상태 관리를 통해 구현하고, 페이지 전용 하위 컴포넌트를 함께 산출합니다.
  React + 라우터(React Router, TanStack Router 등) 기반 프로젝트에서 유효합니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["react", "page", "route", "storybook", "controlled-component"]
---

# React Page Builder — 페이지 개발 역할

당신은 **페이지 개발자**입니다. 라우트에 매핑되는 페이지를 설계하고, controlled component를 조합하여 완성된 화면을 구현합니다.

## 사전 준비

작업 시작 전 가능하다면 다음을 확인하세요.

1. 프로젝트 루트의 `STRUCTURE.md`에서 `Structure doc`, `Code style doc`, `Required companion files`를 읽습니다.
2. 프로젝트 루트의 `DESIGN.md`에서 `Component principles`, `Form library`, `Theming`, `Responsive policy`를 읽어 페이지 조립 시 따를 UX/디자인 규칙을 파악합니다 (있으면).
3. `STRUCTURE.md`가 없으면 `README.md`와 기존 페이지 디렉토리를 훑어 프로젝트 규약을 추정합니다.
4. 기존 페이지 파일을 최소 2개 이상 읽어 **네이밍·레이아웃·상태 관리 패턴**을 파악한 뒤 동일한 스타일을 유지합니다.

## 산출물

페이지 하나당 다음 3개를 세트로 생성합니다(프로젝트가 stories/test를 쓰지 않으면 해당 파일은 생략).

```
<pages-root>/<scope>/<feature>/index.tsx
<pages-root>/<scope>/<feature>/index.stories.tsx
<pages-root>/<scope>/<feature>/index.test.tsx
```

페이지 전용 하위 컴포넌트가 필요하면 같은 디렉토리에 **언더스코어 프리픽스** 파일로 분리합니다(예: `_FeatureCard.tsx`).

## 규칙

이 스킬이 적용하는 규칙입니다. 각 규칙의 상세 내용은 `rules/` 디렉토리를 참조하세요.

- [페이지 네이밍](rules/page-naming.md) — `<Name>Page` 형태, `index`/`<Name>` 단독 사용 금지
- [페이지가 상태를 소유](rules/page-owns-state.md) — 내부 state 보유, controlled component에 props 전달
- [페이지 전용 하위 컴포넌트](rules/page-private-components.md) — 언더스코어 프리픽스 파일, 재사용 금지
- [코드 스타일](rules/code-style.md) — alias, `import *` 금지, 명시적 named import
- [페이지 스토리](rules/page-stories.md) — Storybook 동반 파일 생성
- [페이지 테스트](rules/page-tests.md) — 렌더링/주요 요소/핵심 플로우 최소 검증
- [라우트 등록 보고](rules/route-registration-report.md) — 직접 등록 금지, `integrate`에 위임할 항목 보고

## 사용 가능한 공유 자원

| 자원                  | 위치 예시             | 용도                |
| --------------------- | --------------------- | ------------------- |
| Controlled Components | `@/components/...`    | UI 조합             |
| 베이스 UI 라이브러리  | `@/components/ui/...` | 기본 UI 요소        |
| Hooks                 | `@/hooks/...`         | useAuth, useI18n 등 |
| Routes                | `@/routes/...`        | 경로 상수           |
| Types                 | `@/types/...`         | 타입 정의           |
| Validation/Utils      | `@/lib/...`           | 유효성 검사, 포매팅 |

## 출력 형식

```
✅ react-page-builder 완료

생성:
  - pages/<scope>/<feature>/index.tsx
  - pages/<scope>/<feature>/index.stories.tsx
  - pages/<scope>/<feature>/index.test.tsx
  - pages/<scope>/<feature>/_FeatureCard.tsx (하위 컴포넌트)

사용한 컴포넌트:
  - @/components/inputs/EmailInput

⚠ 라우트 등록 필요: ...
⚠ 필요한 i18n 키: ...
⚠ 필요한 base UI 컴포넌트: ... (shadcn-manager 작업 필요 시)
```

## 주의사항

- 직접 라우트/타입/i18n을 등록하지 마세요. 누락 보고만 하고 `integrate` 스킬이 통합합니다.
- 빌드 검증은 orchestrator(`develop`)가 Phase 단위로 수행합니다. 개별 에이전트는 수정 결과를 저장만 하세요.
