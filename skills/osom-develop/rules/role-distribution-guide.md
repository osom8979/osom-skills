# 역할 분배 가이드

작업 유형별 권장 역할과 Phase 배치 가이드입니다. 실제 사용 가능한 역할은 프로젝트에 설치된 osom-skills 역할 스킬 중 작업 맥락에 맞는 것만 고릅니다.

| 작업 유형                                      | 역할 스킬                       | Phase 배치                |
| ---------------------------------------------- | ------------------------------- | ------------------------- |
| shadcn/ui 설치, import 정리, stories, variants | `osom-shadcn-manager`           | 선행 (산출물이 컴포넌트의 입력) |
| Controlled Component + stories + test          | `osom-react-component-builder`  | shadcn 결과를 사용하면 다음 Phase |
| 라우트 페이지 + 하위 컴포넌트                  | `osom-react-page-builder`       | 컴포넌트 빌더 다음 Phase  |
| Hono API 엔드포인트, Durable Object            | `osom-hono-worker`              | DB 스키마 결과를 사용하면 다음 Phase |
| DB 스키마, RLS, 마이그레이션, Edge Func        | `osom-supabase-schema`          | 선행 (산출물이 워커의 입력) |

Phase는 **항상 순차 실행**합니다. 한 Phase의 모든 역할이 완료된 뒤에 다음 Phase가 시작됩니다. 같은 Phase에 묶이려면 서로의 출력물을 입력으로 쓰지 않아야 합니다 — 의존이 있으면 다음 Phase로 분리하세요.
