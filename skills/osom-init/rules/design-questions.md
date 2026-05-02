# DESIGN.md 핵심 질문

`DESIGN.md`는 `osom-plan-design`이 디자인/UX 관점 리뷰를 할 때 참조하는 도메인 문서입니다. 자동 감지가 거의 불가능하므로 사용자에게 핵심 질문을 던져 답변을 본문에 반영합니다.

## 질문 진행 원칙

- **객관식/단답형 우선**. 자유서술은 답변이 어려우므로 보기 + "기타"를 함께 제시.
- 모르겠다고 하면 해당 섹션은 `_(미정 — 추후 보완)_`로 두고 다음 질문으로 진행.
- 도메인 자체가 해당 없는 프로젝트(예: 순수 백엔드)에서는 사용자가 "이 파일 건너뛰기"를 선택할 수 있게 안내.

## 핵심 질문 8개

| # | 질문 | 보기 (예시) | 매핑 섹션 |
| --- | --- | --- | --- |
| 1 | 디자인 시스템 기반은? | shadcn/ui · MUI · Chakra · Tailwind only · 자체 시스템 · 기타 | `## Design system` |
| 2 | 디자인 토큰(색·간격·타이포)은 어디에 정의되어 있나요? | `tailwind.config.*` · `theme.ts` · CSS 변수 · Figma 동기화 · 없음 | `## Design tokens` |
| 3 | 다크모드 지원? | 지원 · 라이트 전용 · 다크 전용 | `## Theming` |
| 4 | 반응형 브레이크포인트 정책은? | Tailwind 기본 · 커스텀 · 모바일 전용 · 데스크톱 전용 | `## Responsive policy` |
| 5 | 접근성(a11y) 목표 수준은? | WCAG 2.1 AA · AAA · 별도 기준 없음 | `## Accessibility target` |
| 6 | 컴포넌트 작성 원칙은? | controlled only · uncontrolled 허용 · 혼합 · 미정 | `## Component principles` |
| 7 | 폼 검증 라이브러리는? | react-hook-form · formik · 자체 · 미사용 | `## Form library` |
| 8 | 아이콘 세트는? | lucide · heroicons · 자체 SVG · 기타 | `## Icon set` |

답변하지 않은 항목은 빈 섹션 헤더만 두고 본문은 `_(미정)_`로 채웁니다.

## 권장 파일 구조

```markdown
# DESIGN

이 프로젝트의 디자인 시스템·UX 정책 가이드입니다. osom-plan-design이 이 문서를 기준으로 디자인 관점 리뷰를 합니다.

## Design system
shadcn/ui (Radix UI primitives + Tailwind)

## Design tokens
`tailwind.config.ts` + CSS custom properties in `src/styles/tokens.css`

## Theming
Light + Dark (system preference)

## Responsive policy
Tailwind 기본 브레이크포인트(sm/md/lg/xl/2xl) — 모바일 우선

## Accessibility target
WCAG 2.1 AA

## Component principles
Controlled component only. 모든 입력 컴포넌트는 `value` + `onChange` props를 받는다.

## Form library
react-hook-form + zod

## Icon set
lucide-react
```
