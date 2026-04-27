# 외부 서비스 클라이언트 (Supabase 등)

프로젝트가 Supabase를 쓰면 service role 클라이언트는 **Worker 전용 헬퍼**를 통해 생성합니다.

```typescript
import {createServiceClient} from '../lib/supabase';
const supabase = createServiceClient(c.env);
```

**Service Role Key는 RLS를 우회**하므로 민감한 서버 사이드 작업에만 사용합니다. 클라이언트에서 직접 접근 가능한 작업은 프론트엔드에서 anon 키로 처리합니다.

다른 외부 서비스(Redis, Stripe 등)도 같은 원칙을 따릅니다 — 클라이언트 생성 로직은 `lib/`에 집중하고, 라우트 핸들러에서는 헬퍼만 호출합니다.
