# git-helper

Git 버전 관리를 위한 스킬 모음입니다.

## 설치

```bash
/plugin install git-helper@osom-plugins
```

## 스킬 목록

| 스킬 | 설명 |
|------|------|
| `/git-helper:commit` | 변경 사항을 분석하여 커밋 메시지 생성 및 커밋 |
| `/git-helper:pr` | Pull Request 생성 |
| `/git-helper:changelog` | 커밋 히스토리 기반 변경 로그 생성 |
| `/git-helper:branch` | 브랜치 관리 (생성, 전환, 삭제) |

> **참고**: 플러그인 스킬은 `플러그인명:스킬명` 형식으로 호출됩니다.

## 사용 예시

### 커밋 생성
```bash
/git-helper:commit
/git-helper:commit 로그인 기능 관련 변경
```
변경 사항을 분석하여 Conventional Commits 형식의 메시지를 생성합니다.

### PR 생성
```bash
/git-helper:pr
```
현재 브랜치의 모든 커밋을 분석하여 PR을 생성합니다.

### 변경 로그 생성
```bash
/git-helper:changelog
/git-helper:changelog 1.2.0
```

### 브랜치 관리
```bash
/git-helper:branch feature/login 브랜치 만들어줘
/git-helper:branch 병합된 브랜치 정리해줘
```

## 라이선스

MIT
