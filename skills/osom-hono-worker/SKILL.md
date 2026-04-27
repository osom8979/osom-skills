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

1. 프로젝트 루트의 `.osom-skills`에서 **Project documents** 섹션을 확인해 Worker 구조 문서 경로(있다면)를 읽습니다.
2. 현재 Worker 진입점 파일(예: `<worker-root>/index.ts`)을 읽어 라우트 등록 구조를 파악합니다.
3. `wrangler.json`/`wrangler.toml`의 바인딩을 확인합니다.

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

## 핵심 규칙

### 1. API 엔드포인트 구조

각 엔드포인트를 `api/` 디렉토리에 개별 파일로 분리합니다.

```typescript
// <worker-root>/api/<resource>.ts
import {Hono} from 'hono';

const resource = new Hono<{Bindings: Env}>();

resource.get('/', async c => {
  return c.json({data: []});
});

resource.post('/', async c => {
  const body = await c.req.json();
  return c.json({created: true});
});

export {resource};
```

`index.ts`에 라우트 등록:

```typescript
import {resource} from './api/resource';
app.route('/api/resource', resource);
```

### 2. Supabase 등 외부 서비스 클라이언트

프로젝트가 Supabase를 쓰면 service role 클라이언트는 Worker 전용 헬퍼를 통해 생성합니다.

```typescript
import {createServiceClient} from '../lib/supabase';
const supabase = createServiceClient(c.env);
```

Service Role Key는 RLS를 우회하므로 **민감한 서버 사이드 작업에만** 사용합니다. 클라이언트에서 직접 접근 가능한 작업은 프론트엔드에서 anon 키로 처리합니다.

### 3. 환경 변수

새 환경 변수가 필요하면:

- `env.d.ts`에 타입 추가
- `wrangler.json`/`wrangler.toml` 바인딩 추가 필요 시 결과 보고에 명시 (`integrate` 스킬이 통합)

```typescript
declare namespace Cloudflare {
  interface Env {
    NEW_SECRET: string;
  }
}
```

### 4. Durable Object

기존 DO 파일(`agent-socket.ts` 등)의 패턴을 따릅니다:

- Hibernation API 사용 (`ctx.acceptWebSocket`)
- 태그로 엔티티 식별
- `alarm()`으로 만료 처리

## 코드 스타일

- **Import**: 상대 경로 사용 (`../lib/supabase`). Worker 코드에는 프론트엔드용 `@/` alias를 쓰지 않습니다.
- **Export**: named export (`export {resource}`).
- 프로젝트의 `code-style` 문서가 있다면 그 규칙이 우선합니다.

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
