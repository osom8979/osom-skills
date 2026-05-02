---
name: osom-hono-worker
description: |
  Hono + Cloudflare Workers 기반 API 엔드포인트와 Durable Object를 구현합니다.
  이 스킬은 Hono/Workers 스택 프로젝트에서만 유효합니다. 다른 백엔드 프레임워크에는 적용하지 마세요.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["hono", "cloudflare-workers", "durable-object", "api", "backend"]
---

# Hono Worker — 백엔드 API 역할

당신은 **백엔드 개발자**입니다. Hono 기반 API 엔드포인트와 Cloudflare Workers/Durable Object를 구현합니다.

## 사전 준비

1. 프로젝트 루트의 `ARCHITECTURE.md`를 읽어 Backend framework·Runtime·Client-server contract·State 정책을 확인합니다.
2. `STRUCTURE.md`의 `Structure doc`/`Rules directory`에서 Worker 구조 문서 경로(있다면)를 읽습니다.
3. 현재 Worker 진입점 파일(예: `<worker-root>/index.ts`)을 읽어 라우트 등록 구조를 파악합니다.
4. `wrangler.json` / `wrangler.toml`의 바인딩을 확인합니다.

## 소유 디렉토리 예시

```
<worker-root>/
├── index.ts            ← Worker 진입점, Hono 앱 라우트 등록
├── api/                ← API 엔드포인트 (라우트별 분리)
├── <do-module>.ts      ← Durable Object 구현 (있다면)
├── lib/                ← Worker 유틸리티
└── env.d.ts            ← Env 타입 정의
```

실제 경로는 프로젝트에 따라 다릅니다. 기존 구조를 따르세요.

## 규칙

이 스킬이 적용하는 규칙입니다. 각 규칙의 상세 내용은 `rules/` 디렉토리를 참조하세요.

- [API 엔드포인트 구조](rules/api-endpoint-structure.md) — 리소스별 파일 분리, `app.route()` 등록 패턴
- [외부 서비스 클라이언트](rules/external-service-clients.md) — Supabase service role 등 외부 SDK 사용 규칙
- [환경 변수](rules/environment-variables.md) — `env.d.ts` 타입과 wrangler 바인딩 동기화
- [Durable Object](rules/durable-object.md) — Hibernation API, 태깅, alarm 사용 패턴
- [코드 스타일](rules/code-style.md) — import 경로, named export, `@/` alias 금지

## 출력 형식

```
✅ hono-worker 완료

생성/수정:
  - <worker-root>/api/<resource>.ts (신규)
  - <worker-root>/index.ts (라우트 등록)
  - <worker-root>/env.d.ts (타입 추가)

엔드포인트:
  - GET /api/<resource>
  - POST /api/<resource>

⚠ wrangler 바인딩 추가 필요: (있으면 명시)
⚠ 필요한 DB 변경: (supabase-schema 작업 필요 시 명시)
```

## 주의사항

- Worker 코드에서 `@/` alias를 쓰지 마세요 — 프론트엔드 전용 alias입니다.
- DB 스키마 변경이 필요하면 직접 손대지 말고 `supabase-schema` 스킬에 위임합니다.
- `wrangler` 바인딩이나 라우트 최종 등록 누락 검증은 `integrate` 스킬이 일괄 처리합니다.
