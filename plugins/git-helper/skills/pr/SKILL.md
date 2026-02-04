---
description: Pull Request를 생성합니다
---

# Pull Request 생성

현재 브랜치의 변경 사항을 분석하여 PR을 생성합니다.

## 지시사항

1. `git branch --show-current`로 현재 브랜치 확인
2. `git log main..HEAD --oneline` 또는 `git log master..HEAD --oneline`으로 커밋 목록 확인
3. `git diff main...HEAD` 또는 `git diff master...HEAD`로 전체 변경 사항 확인
4. 변경 사항을 분석하여 PR 제목과 본문 작성
5. `gh pr create` 명령으로 PR 생성

## PR 형식

```markdown
## Summary
<1-3 bullet points>

## Changes
- 주요 변경 사항 나열

## Test plan
- [ ] 테스트 항목
```

## 주의사항

- PR 제목은 70자 이내
- 변경 사항의 "왜"를 설명
- 관련 이슈가 있으면 연결
