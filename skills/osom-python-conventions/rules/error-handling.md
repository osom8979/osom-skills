# 에러 처리

## Generic 커스텀 예외

타입 정보를 전달해야 하면 `Generic`을 활용합니다.

```python
from typing import Generic, TypeVar

ValueT = TypeVar("ValueT")


class AutoFixerError(Generic[ValueT], Exception):
    def __init__(self, path: str, value: ValueT):
        super().__init__(
            f"Due to AutoFixer, '{path}' was automatically corrected to {value}"
        )
```

## 예외 체이닝

원본 예외를 보존하려면 `raise ... from error`를 사용합니다.

```python
try:
    parse_value(raw)
except ValueError as error:
    raise AutoFixerError[ValueT](
        self._config_section_path, self._update_value
    ) from error
```

`from None`은 의도적으로 원본 예외를 숨길 때 사용합니다 (보통은 권장하지 않음).

## 선택적 의존성 패턴

런타임에 선택적 패키지를 처리할 때:

```python
try:
    from dotenv import load_dotenv
    load_dotenv(args.dotenv_path)
except ModuleNotFoundError:
    pass
```

조건부 import는 모듈 상단이 아닌 함수 내부에서도 가능합니다. 단, 상단에서 한 번만 검사하는 것을 우선합니다.

## 좁은 예외 캐치

```python
# 나쁜 예: 너무 광범위
try:
    process()
except Exception:
    pass

# 좋은 예: 구체적인 예외만 캐치
try:
    process()
except (ValueError, TypeError) as e:
    logger.warning(f"Invalid input: {e}")
    raise
```

## except 블록의 형태

- 잡은 예외를 그대로 무시(`pass`)하지 마세요. 최소한 로깅하거나 변환 후 다시 던집니다.
- 디버깅을 위해 항상 `as e:`로 이름을 부여합니다.
- 빈 `except:`는 절대 사용하지 않습니다 (시스템 종료 신호까지 잡힙니다).

## 규칙

- 커스텀 예외는 도메인 의미를 반영합니다 (`AutoFixerError`, `InvalidPinKind`).
- 표준 예외(`ValueError`, `TypeError`, `KeyError`)도 적극 활용합니다.
- 예외 메시지에는 문제가 발생한 값과 컨텍스트를 포함합니다.
- 예외 체이닝(`from error`)으로 원본 traceback을 보존합니다.
- 백그라운드 태스크에서는 `BaseException`을 잡아 silent fail을 방지합니다 ([비동기 패턴](async-patterns.md) 참조).

## assert 사용

- 프로덕션 코드에서 입력 검증에 `assert`를 쓰지 마세요 (`python -O` 실행 시 무시됨).
- 개발자가 보장한 불변식 검증, 테스트 코드에서만 사용합니다.

```python
# 나쁜 예: 입력 검증
def divide(a, b):
    assert b != 0  # -O 옵션으로 사라짐!
    return a / b

# 좋은 예
def divide(a, b):
    if b == 0:
        raise ValueError("Division by zero")
    return a / b
```
