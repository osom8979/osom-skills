---
description: 커밋 히스토리를 기반으로 변경 로그를 생성합니다
---

# 변경 로그 생성

Git 커밋 히스토리를 분석하여 CHANGELOG를 생성하거나 업데이트합니다.

## 지시사항

1. `git tag --sort=-version:refname`으로 태그 목록 확인
2. 마지막 태그 이후의 커밋 확인: `git log <last-tag>..HEAD --oneline`
3. 태그가 없으면 전체 히스토리 분석
4. Conventional Commits 형식에 따라 분류:
   - **Added**: feat 커밋
   - **Fixed**: fix 커밋
   - **Changed**: refactor, style 커밋
   - **Deprecated**: deprecated 관련
   - **Removed**: 삭제된 기능
   - **Security**: 보안 관련

## 출력 형식

```markdown
## [Unreleased]

### Added
- 새로운 기능 설명

### Fixed
- 버그 수정 설명

### Changed
- 변경 사항 설명
```

## 옵션

- 버전 번호를 인자로 받으면 해당 버전으로 생성
- 기존 CHANGELOG.md가 있으면 상단에 추가
