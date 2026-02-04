# osom-plugins

OSOM 의 Claude Code 플러그인 마켓플레이스입니다.

## 설치

```bash
/plugin marketplace add osom8979/claude-code-marketplace
```

## 플러그인 목록

| 이름 | 설명 | 버전 |
|------|------|------|
| [git-helper](./plugins/git-helper) | Git 버전 관리를 위한 도구 모음 | 1.0.0 |

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
