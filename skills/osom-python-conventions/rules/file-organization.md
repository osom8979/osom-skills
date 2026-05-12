# 파일/모듈 조직

## 한 파일 = 한 클래스 (원칙)

각 public 클래스는 자체 모듈을 가집니다. 보조 타입(NamedTuple, 내부 Enum)은 같은 파일에 둘 수 있습니다. 자세한 디렉토리 배치는 [디렉토리 구조](directory-structure.md) 참조.

## `__init__.py`는 비워둡니다

```python
# -*- coding: utf-8 -*-
```

- 빈 파일 또는 인코딩 선언만.
- re-export 하지 않습니다 (`from .module import X` 같은 코드 금지).
- 예외: 패키지 루트(`your_pkg/__init__.py`)의 `__version__` 등 메타 정보.

```python
# your_pkg/__init__.py
# -*- coding: utf-8 -*-

__version__ = "1.0.0"
```

## Import 경로

전체 경로를 사용합니다.

```python
# 좋은 예
from your_pkg.flow.node import FlowNode

# 나쁜 예 (__init__.py에서 re-export 의존)
from your_pkg.flow import FlowNode
```

## 라인 폭

- **88자** (black 기본값).
- 주석/docstring도 88자 이내.
- 라인이 길어지면 줄바꿈 우선순위:
  1. 함수 인자: 괄호 내부에서 줄바꿈
  2. 긴 표현식: 변수에 할당해 분리
  3. 긴 문자열: 인접 리터럴 연결

```python
# 좋은 예
result = some_function(
    arg1=value1,
    arg2=value2,
    arg3=value3,
)
```

## 파일 길이

- 파일이 500줄을 넘으면 분리 고려.
- 클래스가 매우 크면 mixin이나 helper 모듈로 분리.

## 모듈 레벨 순서

```python
# 1. 인코딩 선언
# -*- coding: utf-8 -*-

# 2. 모듈 docstring (필요 시)
"""Module description."""

# 3. __future__ imports
from __future__ import annotations

# 4. 일반 imports (stdlib → 3rd → local)

# 5. 모듈 레벨 상수 / TypeVar / TypeAlias

# 6. 모듈 private 함수/클래스 (`_` 접두사)

# 7. public 함수/클래스

# 8. if __name__ == "__main__": 블록 (있는 경우)
```

## 규칙

- `__init__.py`에서 re-export 금지 — 항상 전체 경로 import.
- 모듈명은 짧고 의미 있게: `delta_value.py`, `pin.py`.
- 한 모듈에 여러 public 클래스를 두지 않습니다.
- 88자 라인 폭 (black/flake8 설정과 일치).
