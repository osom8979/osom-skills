# osom-skills

이 저장소는 AI 에이전트 CLI 환경에서 생산성을 높여주는 유용한 에이전트 스킬(Agent Skills) 모음집입니다.

## 🚀 시작하기

`npx skills` 도구를 사용하여 이 저장소의 스킬을 간편하게 관리할 수 있습니다.

### 설치 및 추가

저장소 전체 또는 특정 스킬만 선택하여 설치할 수 있습니다.

```bash
# 모든 스킬 설치 (프로젝트 레벨)
npx skills add osom8979/osom-skills --all

# 특정 스킬만 설치 (예: git-commit)
npx skills add osom8979/osom-skills --skill git-commit

# 전역(Global)으로 설치하고 싶은 경우 -g 옵션 추가
npx skills add osom8979/osom-skills --skill git-commit -g
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

# skills-lock.json에서 스킬 복원 (프로젝트 단위)
npx skills experimental_install
```

### 삭제

```bash
# 스킬 삭제 (인터랙티브 모드)
npx skills remove
```

## 🛠️ 제공하는 스킬 목록

| 스킬 이름 | 주요 기능 | 주요 키워드 |
| :--- | :--- | :--- |
| [`git-commit`](./skills/git-commit/SKILL.md) | Conventional Commits 명세를 따르는 전문적인 영문 커밋 메시지 자동 생성 및 한국어 번역 제공 | git, commit, conventional commits |
| [`git-branch`](./skills/git-branch/SKILL.md) | 명명 규칙(feature/, fix/ 등)을 준수하는 Git 브랜치 생성, 전환, 삭제 및 정리 | git, branch, checkout, merge |
| [`git-changelog`](./skills/git-changelog/SKILL.md) | 커밋 히스토리를 분석하여 최신 정보가 상단에 위치하는 `CHANGELOG.md` 자동 생성 | git, changelog, version control |
| [`geek-to-mediawiki`](./skills/geek-to-mediawiki/SKILL.md) | [GeekNews](https://news.hada.io/) 페이지 내용을 분석하여 MediaWiki 문법으로 즉시 변환 | geeknews, mediawiki, wiki, converter |

## 🧪 스킬 개발 및 테스트

이 저장소에는 스킬을 체계적으로 만들고 검증할 수 있는 개발 도구가 포함되어 있습니다.

- **Skill Creator**: `.agents/skills/skill-creator/`에 위치하며, 새로운 스킬의 드래프트 작성, 테스트 케이스 실행 및 벤치마크 분석을 도와줍니다.
- **테스트 환경**: `skills-evaluation-workspace/`를 통해 실제 환경에서의 동작을 사전에 검증합니다.

## 📄 라이선스

이 프로젝트는 [MIT](./LICENSE) 라이선스 하에 배포됩니다.
