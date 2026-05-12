# 문자열 처리

f-string만 사용합니다. `%` 포매팅과 `.format()`은 금지합니다.

## 기본

```python
raise TypeError(f"This is not a class type: '{path}'")
```

## 포맷 지정자

```python
def __str__(self):
    return f"{self.month:02}/{self.day:02}"


def format_amount(value: float) -> str:
    return f"{value:,.2f}"


def format_hex(value: int) -> str:
    return f"0x{value:08x}"
```

## 멀티라인 (문자열 연결)

긴 메시지는 인접 문자열 리터럴 연결을 사용합니다 (`\n` 백슬래시 대신).

```python
logger.warning(
    f"Please modify the value of '{self._config_section_path}'"
    f" to {self._update_value} of the"
    f" '{str(self._context.home.cvp_yml)}' file and try again."
)
```

## repr 메서드

객체의 디버깅 표현은 일관된 패턴을 사용합니다.

```python
def __repr__(self) -> str:
    return f"<{type(self).__name__} {self._path}>"


def __repr__(self) -> str:
    return f"{type(self).__name__}(name={self._name!r}, value={self._value!r})"
```

`!r`는 `repr()`을 호출합니다. 문자열 값에 따옴표가 자동으로 붙어 디버깅에 유리합니다.

## 규칙

- **반드시** f-string 사용.
- `%` 포매팅 (`"%s" % name`) 금지.
- `.format()` 메서드 금지.
- 로깅에서도 f-string 사용 (성능보다 가독성 우선).
- 표현식이 복잡하면 별도 변수에 할당 후 f-string에 삽입.

## 예외

- **국제화 (i18n)**: 번역 가능한 문자열은 `{name}` 플레이스홀더와 `.format()`이 필요할 수 있습니다.
- **로깅 라이브러리 lazy 평가**: 일부 컨텍스트에서 `logger.info("%s", value)`가 더 적합합니다 (메시지가 빈번하고 비활성화된 로그 레벨일 때). 단, 이 프로젝트는 가독성을 우선합니다.

## 안티 패턴

```python
# 나쁜 예
msg = "Hello, %s!" % name
msg = "Hello, {}!".format(name)
msg = "Hello, " + name + "!"

# 좋은 예
msg = f"Hello, {name}!"
```
