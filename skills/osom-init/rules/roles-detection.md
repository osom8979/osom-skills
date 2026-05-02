# ROLES.md 자동 감지

`ROLES.md`는 활성 역할 스킬(Enabled roles)과 Phase 의존 순서를 함께 담은 문서입니다.

## 1. 활성 역할 추론

디렉토리·패키지 존재 여부로 추론합니다.

| 조건                                            | 활성화 후보                |
| ----------------------------------------------- | -------------------------- |
| `components/ui/` 존재 또는 `shadcn.json`        | `osom-shadcn-manager`        |
| `components/` (ui 제외) 존재                    | `osom-react-component-builder` |
| `pages/` 또는 `app/` (Next.js/React Router) 존재 | `osom-react-page-builder`    |
| `src/worker/` 존재 또는 `package.json`에 `hono` | `osom-hono-worker`           |
| `postgres/` 존재 또는 `supabase/` 존재          | `osom-supabase-schema`       |

활성화되지 않은 역할은 드래프트에서 제외합니다. 감지가 모호한 경우(예: `app/`이 Next.js인지 단순 폴더인지) 사용자에게 확인을 요청합니다.

## 2. Phase 의존 매핑

활성 역할에 따라 표준 종속성 블록을 자동 생성합니다.

- `osom-shadcn-manager` → Phase 1 (no deps)
- `osom-supabase-schema` → Phase 1 (no deps)
- `osom-react-component-builder` → depends on `osom-shadcn-manager`
- `osom-react-page-builder` → depends on `osom-react-component-builder`
- `osom-hono-worker` → depends on `osom-supabase-schema`

활성이 아닌 역할은 출력에서 제외합니다. 새 역할을 추가하면 이 매핑에 종속성을 함께 등록해야 다른 스킬이 올바르게 분배합니다.

## 권장 파일 구조

```markdown
# ROLES

이 프로젝트에서 활성화된 역할 스킬과 실행 순서입니다. osom-develop, osom-kickoff, osom-plan-eng가 이 목록을 기준으로 작업을 분배합니다.

## Enabled roles
- osom-shadcn-manager
- osom-react-component-builder
- osom-react-page-builder
- osom-supabase-schema
- osom-hono-worker

## Phase dependency hints
- osom-shadcn-manager → Phase 1 (no deps)
- osom-supabase-schema → Phase 1 (no deps)
- osom-react-component-builder → depends on osom-shadcn-manager
- osom-react-page-builder → depends on osom-react-component-builder
- osom-hono-worker → depends on osom-supabase-schema
```
