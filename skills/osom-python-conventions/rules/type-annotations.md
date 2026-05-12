# 타입 어노테이션

모든 public 함수·메서드는 파라미터와 리턴 타입을 명시합니다.

## TypeVar와 Generic

```python
from typing import Generic, Type, TypeVar

_T = TypeVar("_T")
ErrorT = TypeVar("ErrorT", bound=BaseException)


class Dtype(Generic[_T], Serializable):
    _type: Type[_T]
    _path: TypePath
```

네이밍:
- 단일 문자 prefix `_T`, `_U`, `_V` — 내부 전용
- `XxxT` 접미사 — 의미를 부여한 TypeVar (`ValueT`, `ErrorT`)

## TypeAlias

읽기 쉬운 이름으로 복잡한 타입을 별칭화합니다.

```python
from typing import Tuple, TypeAlias

_X: TypeAlias = float
_Y: TypeAlias = float
_Width: TypeAlias = float
_Height: TypeAlias = float

Point: TypeAlias = Tuple[_X, _Y]
Size: TypeAlias = Tuple[_Width, _Height]
RGBA: TypeAlias = Tuple[float, float, float, float]
```

## NewType (의미 구분)

동일한 기본 타입이지만 서로 다른 의미를 갖는 값을 구분합니다.

```python
from typing import NewType

TypePath = NewType("TypePath", str)
PinName = NewType("PinName", str)
MediaKey = NewType("MediaKey", str)
```

## Overload (다중 시그니처)

```python
from typing import Optional, overload


@overload
def get(self, service: str, key: str) -> Optional[str]: ...
@overload
def get(self, service: str, key: str, default: str) -> str: ...


def get(
    self,
    service: str,
    key: str,
    default: Optional[str] = None,
) -> Optional[str]:
    cache_key = self.gen_cache_key(service, key)
    if cache_key in self._cache:
        return self._cache[cache_key]
    return default
```

## NamedTuple

불변 데이터 + 메서드가 필요할 때 사용합니다.

```python
from datetime import date
from typing import NamedTuple


class ServiceKey(NamedTuple):
    service: str
    key: str


class MonthDay(NamedTuple):
    month: int  # 1-12
    day: int    # 1-31

    def __str__(self):
        return f"{self.month:02}/{self.day:02}"

    @classmethod
    def from_date(cls, d: date):
        return cls(d.month, d.day)
```

## Dataclass

가변 데이터 컨테이너에 사용합니다. `field(default_factory=...)`로 가변 기본값을 안전하게 처리합니다.

```python
from dataclasses import dataclass, field
from typing import Tuple
from uuid import uuid4


@dataclass
class MediaConfig:
    uuid: str = field(default_factory=lambda: str(uuid4()))
    name: str = field(default_factory=str)
    opened: bool = False
    frame_width: int = 1920
    frame_height: int = 1080

    @property
    def frame_size(self) -> Tuple[int, int]:
        return self.frame_width, self.frame_height

    @frame_size.setter
    def frame_size(self, value: Tuple[int, int]) -> None:
        self.frame_width = value[0]
        self.frame_height = value[1]
```

## 규칙

- public 함수의 모든 파라미터·리턴 타입 어노테이션 필수.
- private(`_`)/내부 헬퍼는 선택적이지만 권장.
- `Optional[X]`를 `X | None` 대신 사용 (Python 3.9 호환 시).
- `Union[A, B]`보다 `A | B` 우선 (Python 3.10+).
- Collection 타입은 `Sequence`/`Mapping` 같은 추상 타입 우선 (불변 입력 표명).
