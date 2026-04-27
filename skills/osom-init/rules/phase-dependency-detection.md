# Phase dependency hints 자동 생성

활성 역할에 따라 표준 종속성 블록을 생성합니다.

- `shadcn-manager` → Phase 1 (no deps)
- `supabase-schema` → Phase 1 (no deps)
- `web-component-builder` → depends on `shadcn-manager`
- `web-page-builder` → depends on `web-component-builder`
- `hono-worker` → depends on `supabase-schema`

활성이 아닌 역할은 출력에서 제외합니다. 새 역할을 추가하면 이 매핑에 종속성을 함께 등록해야 다른 스킬이 올바르게 분배합니다.
