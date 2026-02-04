---
description: Git 브랜치를 관리합니다
---

# 브랜치 관리

브랜치 생성, 전환, 삭제 등 브랜치 관련 작업을 수행합니다.

## 지시사항

사용자 요청에 따라 다음 작업 수행:

### 브랜치 목록
```bash
git branch -a
```

### 새 브랜치 생성
```bash
git checkout -b <branch-name>
```

브랜치 이름 규칙:
- `feature/<description>`: 새 기능
- `fix/<description>`: 버그 수정
- `hotfix/<description>`: 긴급 수정
- `release/<version>`: 릴리즈 준비
- `chore/<description>`: 기타 작업

### 브랜치 전환
```bash
git checkout <branch-name>
```

### 브랜치 삭제
로컬: `git branch -d <branch-name>`
원격: `git push origin --delete <branch-name>`

### 브랜치 정리
병합된 브랜치 확인: `git branch --merged`

## 주의사항

- 삭제 전 반드시 확인
- 강제 삭제(-D)는 사용자 확인 후에만 실행
