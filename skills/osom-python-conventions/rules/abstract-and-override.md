# 추상 클래스와 @override

## 추상 메서드

`abc.ABC`를 상속받고 `@abstractmethod` 데코레이터를 사용합니다. 메서드 본문은 `raise NotImplementedError`로 채웁니다 (`pass` 대신).

```python
from abc import ABC, abstractmethod
from typing import Generic, TypeVar

ValueT = TypeVar("ValueT")


class ProxyValue(Generic[ValueT], ABC):
    @abstractmethod
    def get(self) -> ValueT:
        raise NotImplementedError

    @abstractmethod
    def set(self, value: ValueT) -> None:
        raise NotImplementedError
```

## 왜 `pass`가 아닌 `raise NotImplementedError`인가

- `pass`는 메서드 본문이 비어있을 뿐, 호출 시 `None`을 반환합니다. 서브클래스가 구현을 잊으면 silent failure가 발생합니다.
- `raise NotImplementedError`는 호출 시 즉시 명확한 예외를 던집니다 — 디버깅이 쉬워집니다.
- `@abstractmethod`로 인스턴스화는 막히지만, 다이아몬드 상속이나 super() 호출 시 안전망이 됩니다.

## @override 데코레이터

부모 클래스 메서드를 재정의할 때 사용합니다. Python 3.12+에서는 `typing.override`, 그 이전 버전에서는 `typing_extensions.override`를 사용합니다.

```python
from typing import Any
from typing_extensions import override  # Python < 3.12

from your_pkg.types import Serializable


class Dtype(Generic[_T], Serializable):
    @override
    def __serialize__(self) -> Any:
        return str(self._path)

    @override
    def __deserialize__(self, data: Any) -> None:
        if not isinstance(data, str):
            raise TypeError(f"Unexpected data type: {type(data).__name__}")
        self._type, self._path = load_with_path(data)
```

## 규칙

- 추상 메서드는 항상 `raise NotImplementedError`로 본문 작성.
- 부모 메서드 재정의 시 `@override` 데코레이터 필수.
- 추상 클래스명에 `Abstract` 접두사를 강제하지 않음 (Pythonic 관례).
- 추상 프로퍼티는 `@property + @abstractmethod` 조합 사용.

```python
class Container(ABC):
    @property
    @abstractmethod
    def size(self) -> int:
        raise NotImplementedError
```
