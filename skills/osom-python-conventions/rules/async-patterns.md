# 비동기 패턴

## sync + async 컨텍스트 매니저 (같은 클래스)

같은 리소스에 대해 동기/비동기 컨텍스트가 모두 필요하면 한 클래스에 둘 다 정의합니다.

```python
class ContextPropagator:
    def __enter__(self):
        self._stack.push(self._context)
        return self._context

    def __exit__(self, exc_type, exc_val, exc_tb):
        self._stack.pop()

    async def __aenter__(self):
        self._stack.push(self._context)
        return self._context

    async def __aexit__(self, exc_type, exc_val, exc_tb):
        self._stack.pop()
```

## 비동기 예외 보호

`BaseException`을 잡아 로깅하되 다시 던지지 않을 때 사용합니다 (백그라운드 태스크가 silent fail 하지 않도록).

```python
from logging import Logger


async def shield_any(coro, logger: Logger):
    try:
        return await coro
    except BaseException as e:
        logger.exception(e)
```

## 비동기 함수 시그니처

```python
async def fetch_data(url: str, *, timeout: float = 30.0) -> bytes:
    async with httpx.AsyncClient(timeout=timeout) as client:
        response = await client.get(url)
        return response.content
```

- 리턴 타입은 `Awaitable[...]`이 아닌 실제 값의 타입을 적습니다 (Python이 자동으로 코루틴으로 감쌉니다).
- 키워드 전용 인자를 적극 사용해 호출 측 가독성을 높입니다.

## asyncio.run() 진입점

라이브러리 코드는 이벤트 루프를 생성하지 않습니다. 진입점에서만 `asyncio.run()`을 호출합니다.

```python
import asyncio


async def main() -> None:
    ...


if __name__ == "__main__":
    asyncio.run(main())
```

## 규칙

- 비동기 코드는 비동기 컨텍스트에서만 호출 — `asyncio.run()`을 라이브러리 함수에서 호출하지 않습니다.
- `BaseException` 캐치는 백그라운드 태스크나 예외 보호 헬퍼에서만 — 일반 코드에서는 `Exception`을 잡습니다.
- 동기 코드에서 `asyncio.run()`을 반복 호출하지 마세요. 새 이벤트 루프 생성 비용이 큽니다.
- `asyncio.gather()`로 동시 실행, `asyncio.wait()`로 부분 완료 처리.
- 취소 처리를 위해 `try / except asyncio.CancelledError` 패턴을 적절히 사용합니다.
