# Enabled roles 자동 감지

디렉토리·패키지 존재 여부로 추론합니다.

| 조건                                            | 활성화 후보             |
| ----------------------------------------------- | ----------------------- |
| `components/ui/` 존재 또는 `shadcn.json`        | `shadcn-manager`        |
| `components/` (ui 제외) 존재                    | `web-component-builder` |
| `pages/` 또는 `app/` (Next.js/React Router) 존재 | `web-page-builder`      |
| `src/worker/` 존재 또는 `package.json`에 `hono` | `hono-worker`           |
| `postgres/` 존재 또는 `supabase/` 존재          | `supabase-schema`       |

활성화되지 않은 역할은 드래프트에서 제외합니다. 감지가 모호한 경우(예: `app/`이 Next.js인지 단순 폴더인지) 사용자에게 확인을 요청합니다.
