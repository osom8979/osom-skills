# git-helper

Git 버전 관리를 위한 스킬 모음입니다.

## 설치

```bash
/plugin install git-helper@osom-plugins
```

## 스킬 목록

| 스킬 | 설명 |
|------|------|
| `/commit` | 변경 사항을 분석하여 커밋 메시지 생성 및 커밋 |
| `/pr` | Pull Request 생성 |
| `/changelog` | 커밋 히스토리 기반 변경 로그 생성 |
| `/branch` | 브랜치 관리 (생성, 전환, 삭제) |

## 사용 예시

### 커밋 생성
```bash
/commit
```
변경 사항을 분석하여 Conventional Commits 형식의 메시지를 생성합니다.

### PR 생성
```bash
/pr
```
현재 브랜치의 모든 커밋을 분석하여 PR을 생성합니다.

### 변경 로그 생성
```bash
/changelog
/changelog 1.2.0
```

### 브랜치 관리
```bash
/branch 새 기능 브랜치 만들어줘
/branch 병합된 브랜치 정리해줘
```

## 라이선스

MIT
