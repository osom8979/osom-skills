# 역할 분배 가이드

기본 매핑 예시(실제 분배는 `ROLES.md`의 `Enabled roles` 기준으로 제한):

| 작업 유형                                      | 역할 스킬                       | Phase 순서                |
| ---------------------------------------------- | ------------------------------- | ------------------------- |
| shadcn/ui 설치, import 정리, stories, variants | `osom-shadcn-manager`           | 먼저 (종속성 없음)        |
| Controlled Component + stories + test          | `osom-react-component-builder`  | shadcn 의존 시 후순위     |
| 라우트 페이지 + 하위 컴포넌트                  | `osom-react-page-builder`       | 컴포넌트 후               |
| Hono API 엔드포인트, Durable Object            | `osom-hono-worker`              | DB 스키마 후              |
| DB 스키마, RLS, 마이그레이션, Edge Func        | `osom-supabase-schema`          | 먼저 (종속성 없음)        |

새 역할이 추가되면 `ROLES.md`의 `Enabled roles`와 `Phase dependency hints`에 함께 기록하세요.
