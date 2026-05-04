# osom-skills

이 저장소는 AI 에이전트 CLI 환경에서 생산성을 높여주는 유용한 에이전트 스킬(Agent Skills) 모음집입니다.

## 🚀 시작하기

`npx skills` 도구를 사용하여 이 저장소의 스킬을 간편하게 관리할 수 있습니다.

### 설치 및 추가

저장소 전체 또는 특정 스킬만 선택하여 설치할 수 있습니다.

```bash
# 모든 스킬 설치 (프로젝트 레벨)
npx skills add osom8979/osom-skills --all

# 전역(Global)으로 모든 스킬 설치 (프로젝트 레벨)
npx skills add osom8979/osom-skills --all -g

# 특정 스킬만 설치 (예: osom-git-commit)
npx skills add osom8979/osom-skills --skill osom-git-commit

# 전역(Global)으로 설치하고 싶은 경우 -g 옵션 추가
npx skills add osom8979/osom-skills --skill osom-git-commit -g
```

### 업데이트

설치된 스킬을 최신 버전으로 업데이트합니다.

```bash
# 프로젝트 스킬 업데이트
npx skills update

# 전역 스킬 업데이트
npx skills update -g
```

### 목록 확인 및 복원

```bash
# 설치된 스킬 목록 확인
npx skills list

# `skills-lock.json` 에서 스킬 복원 (프로젝트 단위)
npx skills experimental_install
```

### 삭제

```bash
# 스킬 삭제 (인터랙티브 모드)
npx skills remove
```

## ⚙️ 프로젝트 ROOT 문서

개발 파이프라인 스킬(`osom-plan-*`, `osom-develop`, `osom-kickoff`, `osom-react-review` 등)과 역할 스킬(`osom-react-page-builder`, `osom-react-component-builder` 등)은 프로젝트 루트의 **도메인별 마크다운 문서 6종**을 읽어 프로젝트 고유 정보(빌드 명령, 디렉토리 구조, 디자인/DB/아키텍처 정책 등)를 파악합니다.

처음 설정할 때는 `/osom-init`을 호출하세요. 자동으로 감지 가능한 부분(빌드 명령, 디렉토리 구조 등)은 채우고, 도메인 지식이 필요한 부분(디자인 가이드, DB 정책, 아키텍처)은 핵심 질문으로 사용자에게 물어 답변을 본문에 반영합니다.

### 만들어지는 파일

| 파일 | 도메인 | 주요 사용 스킬 |
| --- | --- | --- |
| `COMMANDS.md`     | 빌드/타입체크/테스트/포맷/커밋 명령 매핑 | osom-develop, osom-integrate, osom-refactor, osom-react-review |
| `STRUCTURE.md`    | 디렉토리 구조, 코드 스타일 문서 위치, 동반 파일 패턴, 규칙·플랜 디렉토리 | osom-react-page-builder, osom-react-component-builder, osom-doc-audit, osom-rule-manage |
| `GUARDRAILS.md`   | 안전 가드(push 금지, 래퍼 사용, 보호 파일 등) | osom-develop, osom-shadcn-manager, osom-react-review |
| `DESIGN.md`       | UI/UX 가이드, 디자인 시스템 토큰, 접근성 기준 | osom-plan-design |
| `DATABASE.md`     | DB 스키마 정책, RLS 모델, 마이그레이션 안전 규칙, 네이밍 | osom-plan-dba, osom-supabase-schema, osom-pgtap-best-practices |
| `ARCHITECTURE.md` | 레이어 구성, 런타임 토폴로지, 핵심 모듈 경계 | osom-plan-eng, osom-hono-worker |

> ⚠️ **AGENT.md / CLAUDE.md / AGENTS.md 같이 특정 AI 에이전트가 자동 참조하는 파일은 osom-skills이 만들지 않습니다.** 사용자가 자신의 AI 환경(Claude Code → `CLAUDE.md`, OpenAI Codex → `AGENTS.md` 등)에 맞는 인덱스 파일에서 위 7개 도메인 문서를 직접 링크해 사용하세요. 인덱스 예시:
>
> ```markdown
> # CLAUDE.md
>
> 이 프로젝트의 ROOT 문서:
> - [COMMANDS](./COMMANDS.md)
> - [STRUCTURE](./STRUCTURE.md)
> - [GUARDRAILS](./GUARDRAILS.md)
> - [DESIGN](./DESIGN.md)
> - [DATABASE](./DATABASE.md)
> - [ARCHITECTURE](./ARCHITECTURE.md)
> ```

### 파일 포맷 (Markdown)

각 파일은 osom-skills 스킬이 섹션 헤더로 값을 찾으므로 헤더 이름을 유지해야 합니다. 자세한 섹션 사양과 예시는 `osom-init` 스킬의 [`rules/`](./skills/osom-init/rules/) 디렉토리를 참조하세요.

`COMMANDS.md` 예시:

```markdown
# COMMANDS

## Build/Check
`./npm run check`

## Typecheck
`./npm run typecheck`

## Test
`./npm test`

## Format
`./npm run format`

## Commit
`/osom-git-commit`
```

도메인 문서(`DESIGN.md`, `DATABASE.md`, `ARCHITECTURE.md`)는 자동 감지가 어려우므로 `/osom-init`이 도메인별 핵심 질문을 던져 답변을 본문에 반영합니다. 답변하지 않은 항목은 `_(미정)_`로 두고 나중에 `/osom-init <FILE>`로 보완합니다.

## 🛠️ 제공하는 스킬 목록

### Git 관련

| 스킬 이름 | 주요 기능 | 주요 키워드 |
| :--- | :--- | :--- |
| [`osom-git-commit`](./skills/osom-git-commit/SKILL.md) | Conventional Commits 명세를 따르는 전문적인 영문 커밋 메시지 자동 생성 및 한국어 번역 제공 | git, commit, conventional commits |
| [`osom-git-branch`](./skills/osom-git-branch/SKILL.md) | 명명 규칙(feature/, fix/ 등)을 준수하는 Git 브랜치 생성, 전환, 삭제 및 정리 | git, branch, checkout, merge |
| [`osom-git-changelog`](./skills/osom-git-changelog/SKILL.md) | 커밋 히스토리를 분석하여 최신 정보가 상단에 위치하는 `CHANGELOG.md` 자동 생성 | git, changelog, version control |

### 콘텐츠 변환

| 스킬 이름 | 주요 기능 | 주요 키워드 |
| :--- | :--- | :--- |
| [`osom-geek-to-mediawiki`](./skills/osom-geek-to-mediawiki/SKILL.md) | [GeekNews](https://news.hada.io/) 페이지 내용을 분석하여 MediaWiki 문법으로 즉시 변환 | geeknews, mediawiki, wiki |

### 개발 파이프라인

기능 요청 한 건을 "4관점 기획 → 분업 개발 → 통합 → 리팩토링 → 리뷰 → 커밋" 흐름으로 처리합니다. ROOT 문서(`COMMANDS.md`, `STRUCTURE.md`, `GUARDRAILS.md` 등)의 값에 따라 프로젝트별로 동작을 맞춥니다.

| 스킬 이름 | 주요 기능 | 주요 키워드 |
| :--- | :--- | :--- |
| [`osom-plan-ceo`](./skills/osom-plan-ceo/SKILL.md) | 기능 요청을 CEO/비즈니스 관점에서 리뷰 (임팩트·우선순위·MVP 범위) | plan, ceo, business |
| [`osom-plan-eng`](./skills/osom-plan-eng/SKILL.md) | 기능 요청을 엔지니어링 관점에서 리뷰 (타당성·영향 범위·필요 역할·종속성) | plan, engineering, dependency |
| [`osom-plan-design`](./skills/osom-plan-design/SKILL.md) | 기능 요청을 디자인/UX 관점에서 리뷰 (플로우·필요 컴포넌트·재사용·접근성) | plan, design, ux |
| [`osom-plan-dba`](./skills/osom-plan-dba/SKILL.md) | 기능 요청을 DBA 관점에서 리뷰 (스키마·RLS·성능·마이그레이션 안전성) | plan, dba, schema |
| [`osom-develop`](./skills/osom-develop/SKILL.md) | Phase별 역할 스킬 분배·병렬 실행·빌드 검증 | pipeline, parallel, build |
| [`osom-integrate`](./skills/osom-integrate/SKILL.md) | 라우트/타입/i18n/설정 등 크로스커팅 파일 정합성 통합 | integration, routes, i18n |
| [`osom-refactor`](./skills/osom-refactor/SKILL.md) | 변경된 파일의 중복 제거·복잡성 감소·패턴 통일 | refactor, code quality |
| [`osom-react-review`](./skills/osom-react-review/SKILL.md) | React/TS 프로젝트의 코드 스타일·필수 파일·빌드·테스트·보안 검증 (PR 리뷰가 아닌 파이프라인 품질 게이트) | react, qa, gate, review |
| [`osom-kickoff`](./skills/osom-kickoff/SKILL.md) | Plan → Develop → Integrate → Refactor → Review → Commit 전체 흐름 오케스트레이션 | orchestration, pipeline |

### 프로젝트 거버넌스

| 스킬 이름 | 주요 기능 | 주요 키워드 |
| :--- | :--- | :--- |
| [`osom-init`](./skills/osom-init/SKILL.md) | 프로젝트 ROOT 도메인 문서 6종(COMMANDS/STRUCTURE/GUARDRAILS/DESIGN/DATABASE/ARCHITECTURE)을 자동 감지+핵심 질문으로 생성·갱신·검증 | init, config, setup, docs |
| [`osom-doc-audit`](./skills/osom-doc-audit/SKILL.md) | 문서(CLAUDE.md, docs/, 스킬·에이전트 정의)와 실제 코드 상태의 정합성 검증 | audit, docs, consistency |
| [`osom-rule-manage`](./skills/osom-rule-manage/SKILL.md) | 자연어 요청을 받아 프로젝트 규칙 문서의 적절한 위치에 규칙을 추가/수정/삭제 | rules, governance, docs |

### 역할 템플릿 (개발 파이프라인이 분배하는 작업자 패턴)

| 스킬 이름 | 주요 기능 | 주요 키워드 |
| :--- | :--- | :--- |
| [`osom-react-page-builder`](./skills/osom-react-page-builder/SKILL.md) | 라우트 페이지와 페이지 전용 하위 컴포넌트 세트(페이지·스토리·테스트) 생성 | react, page, route |
| [`osom-react-component-builder`](./skills/osom-react-component-builder/SKILL.md) | 재사용 가능한 controlled component + Storybook + 테스트 세트 생성 | react, component, storybook |
| [`osom-hono-worker`](./skills/osom-hono-worker/SKILL.md) | Hono + Cloudflare Workers API 엔드포인트와 Durable Object 구현 (해당 스택 프로젝트 전용) | hono, workers, api |
| [`osom-shadcn-manager`](./skills/osom-shadcn-manager/SKILL.md) | shadcn/ui 컴포넌트 설치·import 정리·Storybook 생성·variants 추출 | shadcn, ui, storybook |
| [`osom-supabase-schema`](./skills/osom-supabase-schema/SKILL.md) | Supabase 테이블·RLS·마이그레이션·Edge Function 스키마 관리 (Supabase 프로젝트 전용) | supabase, postgres, rls |

### 데이터베이스 테스트

| 스킬 이름 | 주요 기능 | 주요 키워드 |
| :--- | :--- | :--- |
| [`osom-pgtap-best-practices`](./skills/osom-pgtap-best-practices/SKILL.md) | PostgreSQL pgTAP 단위 테스트 작성·리뷰 베스트 프랙티스 (RLS 검증·픽스처·결과셋 비교·트랜잭션 격리) | pgtap, postgres, rls, test |

## 📄 라이선스

이 프로젝트는 [MIT](./LICENSE) 라이선스 하에 배포됩니다.
