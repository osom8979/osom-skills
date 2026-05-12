# 컨텍스트 매니저

`contextlib.contextmanager` 데코레이터로 간단한 컨텍스트 매니저를 정의합니다. 클래스 기반은 상태 추적이 필요할 때만 사용합니다.

## @contextmanager 데코레이터

```python
import logging
from contextlib import contextmanager


@contextmanager
def disable_logging(level=logging.CRITICAL):
    previous_level = logging.root.manager.disable
    logging.disable(level)
    try:
        yield
    finally:
        logging.disable(previous_level)
```

## 사용

```python
with disable_logging():
    noisy_function()
```

## 클래스 기반 컨텍스트 매니저

상태를 가지거나 동기/비동기 모두 필요한 경우.

```python
class ResourceLock:
    def __init__(self, resource: Resource):
        self._resource = resource
        self._acquired = False

    def __enter__(self) -> Resource:
        self._resource.acquire()
        self._acquired = True
        return self._resource

    def __exit__(self, exc_type, exc_val, exc_tb):
        if self._acquired:
            self._resource.release()
            self._acquired = False
```

## 비동기 컨텍스트 매니저

```python
from contextlib import asynccontextmanager


@asynccontextmanager
async def acquire_connection(pool):
    conn = await pool.acquire()
    try:
        yield conn
    finally:
        await pool.release(conn)
```

## 규칙

- `yield` 전후를 `try / finally`로 감쌉니다 — 예외 발생 시에도 정리 코드가 실행됩니다.
- 단일 리소스 정리에는 `@contextmanager` 데코레이터를 우선 사용.
- 여러 컨텍스트 매니저를 동시에 사용할 때는 `contextlib.ExitStack` 또는 `with A() as a, B() as b:` 형식 사용.
- 컨텍스트 매니저는 부수 효과(로깅, 시간 측정 등)에도 유용합니다.

## ExitStack 예시

```python
from contextlib import ExitStack


def process_files(paths: List[str]) -> None:
    with ExitStack() as stack:
        files = [stack.enter_context(open(p)) for p in paths]
        for f in files:
            process(f)
```
