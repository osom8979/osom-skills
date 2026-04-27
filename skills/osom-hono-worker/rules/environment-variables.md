# 환경 변수

새 환경 변수가 필요하면 다음을 수행합니다.

- `env.d.ts`에 타입 추가
- `wrangler.json` / `wrangler.toml` 바인딩 추가가 필요하면 결과 보고에 명시 (`integrate` 스킬이 통합 처리)

```typescript
declare namespace Cloudflare {
  interface Env {
    NEW_SECRET: string;
  }
}
```

타입과 바인딩이 어긋나면 런타임에 `undefined`로 빠지므로 두 곳을 함께 갱신해야 합니다.
