# API 엔드포인트 구조

각 엔드포인트를 `api/` 디렉토리에 **개별 파일**로 분리합니다.

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

리소스 단위로 파일을 분리하여 검색성을 보장합니다. 한 파일에 여러 리소스를 섞지 않습니다.
