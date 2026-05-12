# 함수 시그니처 패턴

## Keyword-only 인자 (`*` 이후)

플래그 인자나 옵션 인자는 keyword-only로 만들어 호출 측 가독성을 높입니다.

```python
def update(self, value: _T, *, no_emit: bool = False) -> bool:
    ...

# 호출
obj.update(new_value)
obj.update(new_value, no_emit=True)

# 컴파일 에러: positional로 전달 불가
obj.update(new_value, True)  # TypeError
```

기준:
- 부울 플래그는 항상 keyword-only.
- 옵션 인자가 2개 이상이면 keyword-only.
- 의미가 명확하지 않은 positional 인자가 3개를 넘기면 일부를 keyword-only로 분리.

## @classmethod 팩토리

대안 생성자를 제공할 때 사용합니다. 네이밍은 `from_xxx` 또는 `create_xxx`.

```python
@classmethod
def from_single_value(
    cls,
    value: _T,
    on_change: Optional[Callable[[_T, _T], None]] = None,
):
    return cls(value, value, on_change)


@classmethod
def from_dict(cls, data: Dict[str, Any]) -> "Config":
    return cls(**data)
```

## @staticmethod

클래스 컨텍스트가 필요 없는 유틸리티 메서드.

```python
@staticmethod
def gen_cache_key(service: str, key: str) -> ServiceKey:
    return ServiceKey(service, key)
```

`self`나 `cls`를 사용하지 않으면 staticmethod로 만들거나 모듈 레벨 함수로 분리합니다.

## Positional-only 인자 (`/` 이전, Python 3.8+)

API 안정성을 위해 인자 이름을 외부에 노출하지 않을 때 사용합니다.

```python
def render(canvas, /, *, scale: float = 1.0) -> None:
    ...
```

대부분의 경우 필요하지 않습니다. 라이브러리 API 설계 시 고려.

## 가변 인자

```python
def merge_dicts(*dicts: Dict[str, Any]) -> Dict[str, Any]:
    result = {}
    for d in dicts:
        result.update(d)
    return result


def format_log(template: str, **fields: Any) -> str:
    return template.format(**fields)
```

`*args`, `**kwargs`는 의미가 분명할 때만 사용합니다. 가능하면 명시적 인자를 선호합니다.

## 기본값 안티 패턴

가변 객체를 기본값으로 사용하지 않습니다.

```python
# 나쁜 예: 모든 호출이 같은 리스트를 공유
def append(item, items=[]):
    items.append(item)
    return items

# 좋은 예
def append(item, items: Optional[List] = None) -> List:
    if items is None:
        items = []
    items.append(item)
    return items
```

## 규칙

- 모든 public 함수에 [타입 어노테이션](type-annotations.md) 필수.
- 부울 플래그는 keyword-only.
- classmethod 팩토리 네이밍: `from_xxx`.
- staticmethod는 클래스 컨텍스트가 정말 필요 없을 때만.
- 가변 기본값(`[]`, `{}`) 금지 — `None` 사용 후 함수 내부에서 초기화.
- 인자가 5개를 넘으면 dataclass나 NamedTuple로 묶는 것을 고려.
