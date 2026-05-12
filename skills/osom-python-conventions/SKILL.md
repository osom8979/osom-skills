---
name: osom-python-conventions
description: |
  Python 코드를 작성·수정·리뷰할 때 적용하는 코딩 컨벤션 레퍼런스입니다.
  파일 헤더, import 스타일, 타입 어노테이션, 클래스 구조, 테스트 작성, 네이밍 등 19개 규칙을 정의합니다.
  새 `.py` 파일 생성, 클래스/함수 작성, 테스트 작성, 리팩토링 시 자동 참조합니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["python", "convention", "style", "typing", "testing", "black", "isort"]
---

# Python Conventions — Python 코딩 컨벤션 레퍼런스

당신은 **Python 코드 스타일 가드**입니다. 이 스킬은 Python 코드를 작성·수정·리뷰할 때 따라야 하는 코딩 규칙을 제공합니다.

규칙은 실무 프로젝트의 실제 코드에서 추출한 패턴을 기반으로 하며, `black`/`isort`/`flake8`/`mypy` 표준 도구 설정과 호환됩니다.

## 사전 준비

1. 작업 대상 Python 파일의 확장자가 `.py`인지 확인합니다.
2. 프로젝트 루트에 `pyproject.toml` / `setup.cfg` / `.flake8` 등 도구 설정 파일이 있다면 우선 확인합니다.
   - 라인 폭이 88자가 아니거나 isort profile이 다르면 그 값을 우선합니다.
3. 프로젝트 자체 코딩 가이드 문서(예: `CODESTYLE.md`, `CONTRIBUTING.md`)가 있다면 그 문서가 우선합니다.

## 규칙

이 스킬이 적용하는 규칙입니다. 각 규칙의 상세 내용은 `rules/` 디렉토리를 참조하세요.

- [파일 헤더](rules/file-header.md) — 모든 `.py` 파일은 UTF-8 인코딩 선언으로 시작
- [Import 스타일](rules/import-style.md) — stdlib → third-party → local, `from ... import` 사용
- [상수 정의](rules/constants-definition.md) — `Final` 타입 어노테이션 + `UPPER_SNAKE_CASE`
- [클래스 구조](rules/class-structure.md) — 한 파일에 하나의 클래스, 메서드 순서, 접근 제어
- [타입 어노테이션](rules/type-annotations.md) — TypeVar, TypeAlias, NewType, Overload, NamedTuple, Dataclass
- [Enum 패턴](rules/enum-patterns.md) — `@unique` 데코레이터 필수, `auto()` 사용
- [추상 클래스와 @override](rules/abstract-and-override.md) — `@abstractmethod` + `raise NotImplementedError`, 재정의 시 `@override`
- [문자열 처리](rules/string-formatting.md) — f-string만 사용, `%` / `.format()` 금지
- [비동기 패턴](rules/async-patterns.md) — sync/async 컨텍스트 매니저, 예외 보호
- [컨텍스트 매니저](rules/context-managers.md) — `@contextmanager` 데코레이터 활용
- [로깅](rules/logging.md) — 전용 logger 사용, f-string으로 메시지 구성
- [에러 처리](rules/error-handling.md) — Generic 커스텀 예외, 선택적 의존성, 예외 체이닝
- [네이밍 컨벤션](rules/naming-convention.md) — 대상별 스타일 매핑 테이블
- [파일/모듈 조직](rules/file-organization.md) — `__init__.py` 비우기, 88자 라인 폭
- [Docstring](rules/docstring.md) — 클래스에만 작성, Enum 멤버 인라인
- [함수 시그니처](rules/function-signatures.md) — keyword-only 인자, `@classmethod`/`@staticmethod` 패턴
- [코드 품질 도구 설정](rules/code-quality-tools.md) — black/flake8/isort/mypy/pytest 설정
- [디렉토리 구조](rules/directory-structure.md) — 기능별 그룹화, 최대 3단계 깊이
- [테스트 작성](rules/testing.md) — `unittest.TestCase` 기반, 16개 패턴

## 실행 절차

이 스킬은 두 가지 모드로 동작합니다.

### 모드 1: 코드 작성/수정 시 자동 참조

새 `.py` 파일을 만들거나 기존 코드를 수정할 때, 해당 작업과 관련된 규칙을 자동으로 적용합니다.

- 새 파일 생성 → [파일 헤더](rules/file-header.md), [Import 스타일](rules/import-style.md), [파일/모듈 조직](rules/file-organization.md)
- 클래스 정의 → [클래스 구조](rules/class-structure.md), [타입 어노테이션](rules/type-annotations.md), [Docstring](rules/docstring.md)
- 함수 정의 → [함수 시그니처](rules/function-signatures.md), [타입 어노테이션](rules/type-annotations.md)
- 테스트 작성 → [테스트 작성](rules/testing.md)
- 상수 추가 → [상수 정의](rules/constants-definition.md)
- 예외 처리 → [에러 처리](rules/error-handling.md), [로깅](rules/logging.md)

### 모드 2: 코드 리뷰

기존 코드를 리뷰할 때 19개 규칙을 순회하며 위반 항목을 식별합니다.

1. 변경된 `.py` 파일 목록 수집.
2. 각 파일에 대해 19개 규칙을 순서대로 점검.
3. 위반 항목별로 파일·라인 위치와 수정 제안을 출력.

## 출력 형식

### 코드 작성/수정 시

규칙을 적용한 코드를 직접 생성합니다. 별도 보고 형식 없음.

### 코드 리뷰 시

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Python Conventions] 리뷰 결과
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

검사 대상: 3개 파일

❌ src/foo/bar.py
  - L1: 파일 헤더 누락 — `# -*- coding: utf-8 -*-` 추가 필요
  - L23: `import os` → `from os import ...` 형태로 변경 (Import 스타일)
  - L45: 상수 `TIMEOUT`에 `Final` 어노테이션 누락 (상수 정의)

✅ src/foo/baz.py — 위반 없음

❌ tests/test_qux.py
  - L12: `pytest` fixture 사용 — `unittest.TestCase`로 변경 (테스트 작성)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
위반: 4건 / 검사: 3개 파일
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## 주의사항

- **프로젝트 고유 설정이 우선합니다** — `pyproject.toml`, `setup.cfg`, `.flake8` 등 도구 설정 값이 이 스킬의 기본값과 다르면 프로젝트 설정을 따릅니다.
- **점진적 적용**: 기존 코드베이스 전체에 일괄 적용하지 마세요. 변경/추가되는 파일에만 적용합니다.
- **타입 어노테이션 강도**: public API에는 필수, 내부 헬퍼는 선택적입니다.
- **컨벤션은 가이드라인입니다** — 합당한 이유로 벗어나야 한다면 주석으로 이유를 명시합니다.
