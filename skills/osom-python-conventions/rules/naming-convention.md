# 네이밍 컨벤션

| 대상 | 스타일 | 예시 |
|---|---|---|
| 모듈 | `lowercase` 또는 `snake_case` | `config.py`, `delta_value.py` |
| 패키지(디렉토리) | `lowercase` | `network`, `maths` |
| 클래스 | `PascalCase` | `DragButton`, `ContextPropagator` |
| 함수/메서드 | `snake_case` | `get_default_arguments` |
| 상수 | `UPPER_SNAKE_CASE` | `TIMEOUT_INFINITE` |
| Private 속성 | `__double_underscore` | `self.__name` (name mangling) |
| Protected 속성 | `_single_underscore` | `self._callback` |
| 모듈 private 함수 | `_single_underscore` | `def _internal_helper():` |
| TypeVar | `_T` 또는 `XxxT` | `_T`, `ErrorT`, `ValueT` |
| NewType | `PascalCase` | `TypePath`, `PinName`, `MediaKey` |
| TypeAlias | `PascalCase` | `Point`, `Size`, `RGBA` |
| Enum 멤버 | `snake_case` 또는 `UPPER_SNAKE_CASE` | `Action.exec`, `Color.RED` |
| 테스트 클래스 | `XxxTestCase` | `DeltaTestCase` |
| 테스트 메서드 | `test_xxx` | `test_update_triggers_callback` |

## 약어 처리

약어는 한 단어로 취급합니다. PascalCase에서는 첫 글자만 대문자입니다.

```python
# 좋은 예
HttpClient, XmlParser, JsonEncoder, UrlBuilder

# 나쁜 예
HTTPClient, XMLParser, JSONEncoder, URLBuilder
```

## 부울 변수

부울 변수는 의문문 또는 상태 형용사 형태로 명명합니다.

```python
is_connected: bool
has_changed: bool
can_retry: bool
should_skip: bool
opened: bool
```

## 함수 동사 접두사

| 접두사 | 의미 | 예시 |
|---|---|---|
| `get_` | 값 조회 | `get_user(id)` |
| `set_` | 값 설정 | `set_value(v)` |
| `is_` / `has_` | 부울 반환 | `is_valid()` |
| `to_` | 변환 | `to_json()` |
| `from_` | 팩토리 (classmethod) | `User.from_dict(d)` |
| `create_` | 새 객체 생성 | `create_session()` |
| `parse_` | 파싱 | `parse_url(s)` |
| `build_` | 빌더 패턴 | `build_query()` |

## 단수/복수

- 단일 객체 → 단수: `user`, `node`
- 컬렉션 → 복수: `users`, `nodes`
- 정수 카운트 → `_count`: `user_count`, `node_count`

## 규칙

- 한 글자 이름은 루프 카운터(`i`, `j`)와 람다 인자(`x`, `_`)에서만 허용.
- 무시할 값은 `_` 사용: `name, _ = pair`.
- 미사용 함수 인자도 `_` 또는 `_name` 사용 (linter 경고 방지).
- 약어 사용 금지 — `db_conn`은 `database_connection`, `req`는 `request`.
- 단, 광범위하게 통용되는 약어(`url`, `id`, `db`, `api`, `http`)는 허용.
