# ARCHITECTURE.md 핵심 질문

`ARCHITECTURE.md`는 `osom-plan-eng`, `osom-hono-worker`가 엔지니어링 관점 리뷰·구현을 할 때 참조하는 도메인 문서입니다. 자동 감지로는 디렉토리 골격만 알 수 있을 뿐 의도된 레이어 분리·런타임 토폴로지는 사용자만 알기에 핵심 질문으로 채웁니다.

## 질문 진행 원칙

- 객관식/단답형 우선, 모르면 `_(미정)_`로 두고 진행.
- 이 문서는 `STRUCTURE.md`(파일/폴더 위치)와 보완 관계입니다 — 여기는 **왜 그렇게 나누는지**를 담습니다.

## 핵심 질문 7개

| # | 질문 | 보기 (예시) | 매핑 섹션 |
| --- | --- | --- | --- |
| 1 | 런타임은? | Node.js · Bun · Deno · Cloudflare Workers · Browser only · 혼합 | `## Runtime` |
| 2 | 배포 토폴로지는? | 모놀리식 · 프론트+백 분리 · Edge + Origin · 서버리스 함수 · 정적 호스팅 | `## Deployment topology` |
| 3 | 프론트엔드 프레임워크는? | Next.js · React Router · Remix · Vite + React · 없음 | `## Frontend framework` |
| 4 | 백엔드 프레임워크는? | Hono · Express · Fastify · NestJS · Edge Function only · 없음 | `## Backend framework` |
| 5 | 클라이언트 ↔ 서버 통신 방식은? | REST · tRPC · GraphQL · RPC · 혼합 | `## Client-server contract` |
| 6 | 상태 관리/캐시 전략은? | TanStack Query · SWR · Redux · Zustand · context only · 미정 | `## State & cache` |
| 7 | 레이어 경계 원칙은? (도메인/인프라 분리) | Hexagonal/Clean · DDD bounded contexts · feature-sliced · 자유 · 미정 | `## Layering principle` |

## 권장 파일 구조

```markdown
# ARCHITECTURE

이 프로젝트의 런타임·레이어·통신 모델을 담은 도메인 문서입니다. osom-plan-eng가 새 기능의 영향 범위와 종속 역할을 판단할 때 이 문서를 기준으로 합니다.

## Runtime
- Browser: Vite + React
- Server: Hono on Cloudflare Workers

## Deployment topology
Edge(Workers) + Origin(Supabase Postgres). 정적 자산은 Cloudflare Pages.

## Frontend framework
Vite + React + React Router (SPA)

## Backend framework
Hono (HTTP), Durable Objects (실시간/세션)

## Client-server contract
REST + JSON. 타입은 `packages/contract`에서 zod 스키마로 공유.

## State & cache
TanStack Query (서버 상태), Zustand (UI 상태). Context는 테마/i18n에만.

## Layering principle
- `src/features/*` = feature-sliced (UI + hook + API client을 한 곳에).
- `src/shared/*` = 인프라(http, db, auth) — feature가 의존, 역방향 금지.
- `src/worker/*` = 백엔드, 프론트는 import 하지 않는다.
```
