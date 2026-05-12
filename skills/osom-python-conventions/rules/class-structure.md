# 클래스 구조

## 원칙: 한 파일에 하나의 클래스

각 클래스는 자체 모듈을 가집니다. 모듈명은 클래스명을 `snake_case`로 변환한 것입니다 (예: `DeltaValue` → `delta_value.py`).

예외: NamedTuple, 작은 dataclass, 내부 helper enum 등 다른 클래스에 종속적인 보조 타입은 같은 파일에 둘 수 있습니다.

## 기본 골격

```python
# -*- coding: utf-8 -*-

from typing import Callable, Generic, Optional, TypeVar

_T = TypeVar("_T")


class DeltaValue(Generic[_T]):
    """
    A class designed for interoperability with `imgui`.

    Provides a value container that tracks changes and
    optionally triggers a callback when updated.
    """

    def __init__(
        self,
        value: _T,
        prev: _T,
        callback: Optional[Callable[[_T, _T], None]] = None,
    ):
        self.prev = prev
        self.value = value
        self._callback = callback

    @classmethod
    def from_single_value(
        cls,
        value: _T,
        on_change: Optional[Callable[[_T, _T], None]] = None,
    ):
        return cls(value, value, on_change)

    @property
    def changed(self) -> bool:
        return self.prev != self.value

    def update(self, value: _T, *, no_emit=False) -> bool:
        self.prev = self.value
        self.value = value
        changed = self.prev != self.value
        if not no_emit and changed and self._callback is not None:
            self._callback(self.value, self.prev)
        return changed
```

## 메서드 순서

다음 순서로 정의합니다.

1. `__init__`
2. 매직 메서드 (`__repr__`, `__eq__`, `__hash__` 등)
3. `@classmethod` (팩토리)
4. `@staticmethod`
5. `@property` (getter/setter)
6. public 메서드
7. `_protected` 메서드
8. `__private` 메서드

## 속성 접근 제어

```python
class Pin:
    def __init__(self, name: PinName, dtype: Union[None, type, Dtype]):
        self.__dtype = dtype if isinstance(dtype, Dtype) else Dtype(dtype)  # private (mangling)
        self.__name = name                                                  # private (mangling)
        self._hidden = False                                                # protected

    @property
    def name(self) -> PinName:
        return self.__name

    @property
    def dtype(self) -> Dtype:
        return self.__dtype
```

| 가시성 | 접두사 | 의미 |
|---|---|---|
| Public | `self.value` | 외부에서 자유롭게 접근 가능 |
| Protected | `self._callback` | 내부/서브클래스에서만 사용 (관습) |
| Private | `self.__name` | name mangling, `@property`로 노출 |

## 규칙

- 불변 속성은 `@property`로 노출하고 setter를 정의하지 않습니다.
- 외부에서 변경해서는 안 되는 내부 상태는 `__double_underscore`로 보호합니다.
- 단순 데이터 컨테이너는 [dataclass](type-annotations.md) 또는 NamedTuple을 사용합니다.
