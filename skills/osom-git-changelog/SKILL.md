---
name: osom-git-changelog
description: |
  Git 커밋 히스토리를 분석하여 CHANGELOG를 생성하거나 업데이트합니다.
  Conventional Commits 형식에 따라 변경사항을 분류합니다.
version: 1.1.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["git", "version-control", "vcs", "commit", "pr", "changelog", "branch"]
---

# 변경 로그 생성

Git 커밋 히스토리를 분석하여 CHANGELOG를 생성하거나 업데이트합니다.

## 버전 정보

버전 번호: $ARGUMENTS (제공된 경우 해당 버전으로 생성, 없으면 [Unreleased] 사용)

## 규칙

이 스킬이 적용하는 규칙입니다. 각 규칙의 상세 내용은 `rules/` 디렉토리를 참조하세요.

- [Conventional Commits 분류](rules/conventional-commit-classification.md) — 커밋 타입을 Added/Fixed/Changed 등 CHANGELOG 카테고리로 매핑하는 규칙

## 실행 절차

1. `git tag --sort=-version:refname`으로 태그 목록 확인.
2. 마지막 태그 이후의 커밋 확인: `git log <last-tag>..HEAD --oneline`.
3. 태그가 없으면 전체 히스토리 분석.
4. 위 [Conventional Commits 분류 규칙](rules/conventional-commit-classification.md)에 따라 커밋을 분류.

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

- 버전 번호를 인자로 받으면 해당 버전으로 생성.
- 기존 `CHANGELOG.md`가 있는 경우, 파일의 첫 번째 2단계 헤더(`##`)를 찾아 그 직전에 새로운 내용을 삽입하여 최신 정보가 항상 상단에 위치하도록 합니다.
