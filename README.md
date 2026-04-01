# osom-plugins

OSOM 의 Claude Code 플러그인 마켓플레이스입니다.

## 설치

```bash
claude plugin marketplace add osom8979/claude-code-marketplace
```

마켓플레이스의 모든 플러그인을 한 번에 설치합니다:

```bash
claude plugin install --all --marketplace osom8979/claude-code-marketplace
```

## 제거

### 개별 플러그인 제거

```bash
claude plugin remove translator@osom-plugins
claude plugin remove git-helper@osom-plugins
claude plugin remove geek-to-mediawiki@osom-plugins
```

### 마켓플레이스 제거

마켓플레이스와 해당 플러그인을 모두 제거합니다:

```bash
claude plugin marketplace remove osom-plugins
```

## 업데이트

이미 마켓플레이스 및 플러그인이 설치된 환경에서 최신 버전으로 업데이트하려면:

### 마켓플레이스 업데이트

```bash
claude plugin marketplace update osom-plugins
```

### 개별 플러그인 업데이트

특정 플러그인만 업데이트할 수 있습니다:

```bash
claude plugin update translator@osom-plugins
claude plugin update git-helper@osom-plugins
claude plugin update geek-to-mediawiki@osom-plugins
```

### 전체 플러그인 일괄 업데이트

설치된 모든 플러그인을 한 번에 업데이트합니다:

```bash
claude plugin update --all
```

## 확인

### 마켓플레이스 설치 확인

등록된 마켓플레이스 목록을 확인합니다:

```bash
claude plugin marketplace list
```

### 설치된 플러그인 확인

현재 설치된 플러그인 목록과 버전을 확인합니다:

```bash
claude plugin list
```

### 개별 플러그인 상세 정보

특정 플러그인의 상세 정보를 확인합니다:

```bash
claude plugin info translator
```

## 플러그인 목록

| 이름 | 설명 | 버전 |
|------|------|------|
| [git-helper](./plugins/git-helper) | Git 버전 관리를 위한 도구 모음 | 1.0.0 |
| [translator](./plugins/translator) | 다국어 번역 및 국제화(i18n) 지원 도구 모음 | 1.0.0 |
| [geek-to-mediawiki](./plugins/geek-to-mediawiki) | GeekNews 뉴스를 MediaWiki 페이지 포맷으로 변환 | 1.0.0 |

## 플러그인 구조

각 플러그인은 다음 구조를 따릅니다:

```
plugins/plugin-name/
├── .claude-plugin/
│   └── plugin.json       # 플러그인 매니페스트
├── skills/               # 슬래시 커맨드
│   └── command-name/
│       └── SKILL.md
├── agents/               # 에이전트 정의
├── hooks/                # 이벤트 훅
└── README.md
```

## 라이선스

MIT
