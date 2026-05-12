# 상수 정의

모든 모듈 레벨 상수는 `Final` 타입 어노테이션과 `UPPER_SNAKE_CASE` 네이밍을 사용합니다.

## 스칼라 상수

```python
from typing import Final

TIMEOUT_INFINITE: Final[float] = -1.0
DEFAULT_STRING_ENCODING: Final[str] = "utf-8"
LOCALHOST: Final[str] = "localhost"
```

## 컴포짓 상수 (튜플)

```python
CANVAS_ANCHOR_COLOR: Final[RGBA] = 1.0, 0.0, 0.0, 0.9
EMPTY_POINT: Final[Point] = 0.0, 0.0
```

## 불변 시퀀스

```python
from typing import Final, Sequence

SEVERITIES: Final[Sequence[str]] = (
    SEVERITY_NAME_CRITICAL,
    SEVERITY_NAME_ERROR,
    SEVERITY_NAME_WARNING,
)
```

## 정규식

```python
from re import Pattern
from re import compile as re_compile
from typing import Final

VERSION_REGEX: Final[Pattern[str]] = re_compile(r"(\d+\.\d+)(.*)")
```

## 불변 매핑

`MappingProxyType`을 사용하여 의도하지 않은 변경을 방지합니다.

```python
import logging
from types import MappingProxyType
from typing import Final

LEVEL_TO_NAME: Final[MappingProxyType[int, str]] = MappingProxyType({
    logging.CRITICAL: "critical",
    logging.ERROR: "error",
})
```

## 규칙

- **반드시** `Final` 어노테이션 사용 — mypy가 의도치 않은 재할당을 감지합니다.
- 네이밍: `UPPER_SNAKE_CASE`.
- 컬렉션 상수는 가변 컬렉션(`list`, `dict`, `set`)을 피하고 불변 타입(`tuple`, `frozenset`, `MappingProxyType`)을 사용합니다.
- 클래스 내부 상수도 동일한 규칙을 적용합니다.

## 안티 패턴

```python
# 나쁜 예: 어노테이션 없음
TIMEOUT = -1.0

# 나쁜 예: 가변 컬렉션
ITEMS = [1, 2, 3]

# 좋은 예
TIMEOUT: Final[float] = -1.0
ITEMS: Final[Tuple[int, ...]] = (1, 2, 3)
```
