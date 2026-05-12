# Enum 패턴

`@unique` 데코레이터를 필수로 사용하고, 값은 `auto()`로 자동 할당합니다.

## StrEnum

문자열 값이 의미를 갖는 경우 사용합니다.

```python
from enum import StrEnum, auto, unique


@unique
class Action(StrEnum):
    exec = auto()
    """Execution pins are used to connect nodes together"""

    data = auto()
    """Data pins are used for taking data into a node"""
```

## IntEnum

정수 값이 의미를 갖거나 외부 라이브러리 값과 매핑되는 경우 사용합니다.

```python
from enum import IntEnum, auto, unique
from inspect import Parameter


@unique
class PinKind(IntEnum):
    positional_only = Parameter.POSITIONAL_ONLY.value
    positional_or_keyword = Parameter.POSITIONAL_OR_KEYWORD.value
    return_only = auto()
    unknown = auto()
```

## 일반 Enum

값이 의미가 없거나 객체로 다룰 때 사용합니다.

```python
from enum import Enum, auto, unique


@unique
class Color(Enum):
    red = auto()
    green = auto()
    blue = auto()
```

## match 문 (Python 3.10+)

Enum 분기에는 `match`를 사용합니다.

```python
def create_action(value: AnyAction) -> Action:
    if isinstance(value, Action):
        return value
    elif isinstance(value, str):
        match value.lower():
            case "exec":
                return Action.exec
            case "data":
                return Action.data
            case _:
                raise ValueError(f"Unsupported action value: {value}")
    else:
        raise TypeError(f"Unsupported action type: {type(value).__name__}")
```

## 규칙

- **반드시** `@unique` 데코레이터 사용 — 중복 값을 컴파일 타임에 잡아냅니다.
- 멤버 이름은 `snake_case` (또는 프로젝트 일관성에 따라).
- 멤버에는 인라인 docstring 작성 (의미 명시).
- 값 자체에 의미가 없으면 `auto()` 사용.
- 외부 라이브러리/프로토콜과 매핑되는 값은 명시적으로 작성.
