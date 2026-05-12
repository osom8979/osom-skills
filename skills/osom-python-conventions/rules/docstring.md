# Docstring

## 클래스에 작성

모든 public 클래스에 docstring을 작성합니다. 메서드는 복잡하거나 비자명한 동작이 있을 때만 작성합니다.

```python
class IntervalUpdater(IntervalTimer, ABC):
    """
    A class designed for interoperability with `imgui`.

    This class is a generic abstract base class that provides automatic or manual
    data refresh functionality based on a time interval.
    """
```

## Enum 멤버 인라인 docstring

각 멤버 바로 아래에 한 줄로 작성합니다.

```python
@unique
class Action(StrEnum):
    exec = auto()
    """Execution pins are used to connect nodes together"""

    data = auto()
    """Data pins are used for taking data into a node"""
```

## 함수 docstring (선택)

복잡한 알고리즘, 부수 효과, 예외, 비자명한 인자가 있을 때 작성합니다.

```python
def parse_version(text: str) -> Tuple[int, int, int]:
    """
    Parse a semantic version string.

    Args:
        text: Version string in "major.minor.patch" form.

    Returns:
        Tuple of (major, minor, patch).

    Raises:
        ValueError: If the input does not match semantic version format.
    """
    ...
```

## 형식

- 삼중 따옴표 (`"""`) 사용.
- 첫 줄에는 한 줄 요약을 둡니다.
- 빈 줄 후 상세 설명.
- Args/Returns/Raises 섹션은 Google 스타일 또는 NumPy 스타일을 선택해 일관되게 적용.

## 작성 금지 대상

- 자명한 getter/setter — `def get_name(self) -> str:`에 "Returns the name" 불필요.
- 한 줄짜리 헬퍼 함수.
- private(`_`) 함수의 단순 구현.

## 규칙

- 모든 public 클래스에 docstring 작성.
- Enum 멤버에 인라인 docstring (한 줄).
- 추상 메서드의 docstring은 부모에서 제공 — 자식의 `@override`는 docstring 생략 가능.
- 한국어 docstring 허용 (프로젝트 정책에 따라). 영문 우선 권장.
- 88자 라인 폭 유지.

## 안티 패턴

```python
# 나쁜 예: 자명한 docstring
def get_name(self) -> str:
    """Get the name."""
    return self._name

# 나쁜 예: WHAT만 설명
def update(self, value):
    """Updates the value."""  # 함수명이 이미 말함
    ...

# 좋은 예: WHY 설명
def update(self, value, *, no_emit=False):
    """
    Update internal value and trigger callback unless ``no_emit`` is set.

    The callback runs only when the value actually changed to avoid
    redundant UI redraws.
    """
    ...
```
