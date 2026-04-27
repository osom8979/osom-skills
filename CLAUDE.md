# CLAUDE.md — osom-skills 저장소 작업 가이드

이 저장소는 [Claude Code](https://claude.com/claude-code)용 에이전트 스킬 모음집입니다. 사용자에게 배포되는 각 스킬은 `skills/<skill-name>/` 디렉토리 하나에 자족적으로(self-contained) 들어 있어야 합니다.

## 디렉토리 구조

```
skills/
└── <skill-name>/
    ├── SKILL.md          ← 스킬 진입점 + 규칙 인덱스
    └── rules/            ← 규칙 1개 = 파일 1개
        ├── <rule-1>.md
        ├── <rule-2>.md
        └── ...
```

## 스킬 작성 규칙

### 1. SKILL.md는 인덱스 역할만

각 스킬의 `SKILL.md`는 **본문 규칙을 직접 서술하지 않고**, `rules/` 디렉토리의 규칙 파일들을 가리키는 인덱스로 유지합니다.

`SKILL.md`에 남겨도 되는 것:

- frontmatter (`name`, `description`, `version`, `keywords` 등)
- 스킬의 역할/페르소나 소개 (한두 문단)
- **사전 준비** 같은 절차 섹션
- **실행 절차 / 실행 흐름** 같은 단계 섹션
- **출력 형식** (결과 보고 템플릿)
- **주의사항** (메타 가드)
- **`## 규칙` 섹션** — `rules/` 디렉토리 파일 링크 목록

### 2. 규칙 파일 = `rules/<kebab-case>.md`

검증/판단 기준이 되는 "진짜 규칙"은 `rules/` 하위에 **규칙 1개 = 파일 1개**로 분리합니다.

- 파일명은 **kebab-case 영문**으로 작성합니다 (예: `controlled-component-only.md`, `migration-safety.md`).
- 절차/형식/메타(예: `사전 준비`, `실행 절차`, `출력 형식`, `주의사항`)는 분리 대상이 아닙니다 — `SKILL.md`에 그대로 둡니다.
- 각 규칙 파일은 **자족적**이어야 합니다. 규칙 본문, 예시 코드, 적용 기준을 한 파일 안에 담습니다.

### 3. SKILL.md의 `## 규칙` 인덱스 포맷

```markdown
## 규칙

이 스킬이 적용하는 규칙입니다. 각 규칙의 상세 내용은 `rules/` 디렉토리를 참조하세요.

- [규칙 제목](rules/file-name.md) — 한 줄 요약
- [다른 규칙](rules/other-rule.md) — 한 줄 요약
```

순서 의존이 있는 규칙(예: 검증 항목 1~7)은 번호 매김 리스트로 표기합니다.

### 4. 새 스킬을 추가할 때

1. `skills/<new-skill-name>/SKILL.md`를 작성하고 frontmatter를 채웁니다.
2. 본문에서 분리할 만한 "진짜 규칙"이 있으면 `rules/` 디렉토리에 파일별로 작성합니다.
3. `SKILL.md`의 `## 규칙` 섹션에 인덱스 링크를 추가합니다.
4. `README.md`의 스킬 목록 표에 새 스킬을 등록합니다.

### 5. 기존 스킬에 규칙을 추가할 때

1. `skills/<skill-name>/rules/<new-rule>.md`를 작성합니다.
2. `SKILL.md`의 `## 규칙` 인덱스에 항목을 추가합니다.
3. 본문 절차에서 그 규칙을 참조해야 하면 인라인 링크로 연결합니다 (`... [<규칙 제목>](rules/<file>.md)에 따라 ...`).

## 명명 규칙

- **스킬 디렉토리**: `osom-<topic>` (예: `osom-git-commit`, `osom-pgtap-best-practices`)
- **규칙 파일**: kebab-case 영문 (예: `english-only.md`, `rls-testing.md`)
- **frontmatter `name`**: 디렉토리명과 동일

## 변경 시 점검 항목

- [ ] `SKILL.md`에 절차/형식 외 본문 규칙이 남아 있지 않은지
- [ ] `rules/`의 모든 파일이 `SKILL.md`의 `## 규칙` 인덱스에 등록되어 있는지
- [ ] 인덱스 링크가 실제 파일을 가리키는지 (`osom-doc-audit` 스킬로 검증 가능)
- [ ] 규칙 파일 1개에 1개 규칙만 들어있는지 (한 파일에 여러 독립 규칙이 섞이지 않았는지)
- [ ] 파일명이 kebab-case 영문인지

## 관련 문서

- [README.md](./README.md) — 스킬 목록, 설치 가이드, `.osom-skills` 포맷
- 각 스킬의 `SKILL.md` — 해당 스킬의 진입점과 규칙 인덱스
