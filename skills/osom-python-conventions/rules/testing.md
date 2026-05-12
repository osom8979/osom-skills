# 테스트 작성

`unittest.TestCase`를 사용합니다. pytest 스타일 fixture는 사용하지 않습니다.

## 기본 템플릿

모든 테스트 파일의 골격:

```python
# -*- coding: utf-8 -*-

from unittest import TestCase, main

from your_pkg.module.target import TargetClass


class TargetClassTestCase(TestCase):
    def test_some_feature(self):
        # given
        target = TargetClass()

        # when
        result = target.some_method()

        # then
        self.assertEqual(expected, result)


if __name__ == "__main__":
    main()
```

## 파일 구조 규칙

- **클래스명**: `XxxTestCase` (TestCase 접미사).
- **메서드명**: `test_` 접두사.
- **미러링**: `your_pkg/mod/foo.py` → `tester/mod/test_foo.py`.
- **파일 끝**: `if __name__ == "__main__": main()` 필수.
- **assertion 순서**: `assertEqual(expected, actual)` — expected 먼저.

## 패턴 1: setUp / tearDown — 리소스 관리

```python
from tempfile import TemporaryDirectory
from unittest import TestCase, main


class DatabaseTestCase(TestCase):
    def setUp(self):
        self._tmpdir = TemporaryDirectory()
        self._home = HomeDir(self._tmpdir.name)
        self._path = self._home.chat.get_database_path()
        self._chat = ChatDatabase(self._path, create_tables=True)

    def tearDown(self):
        self._tmpdir.cleanup()

    def test_insert_conversation(self):
        row_id = self._chat.insert_conversation("Test", datetime.now(UTC))
        self.assertEqual(1, row_id)
```

## 패턴 2: 컨텍스트 매니저 중첩 — 임시 파일/디렉토리

```python
class BufferTestCase(TestCase):
    def test_lines_buffer(self):
        with TemporaryDirectory() as tmpdir:
            self.assertTrue(os.path.isdir(tmpdir))

            with NamedTemporaryFile("wt", dir=tmpdir) as f:
                with LinesBuffer(f.name, maxsize=10) as buffer:
                    self.assertEqual("", buffer.getvalue())
                    self.assertEqual(5, f.write("12345"))
                    f.flush()
                    buffer.update()
                    self.assertEqual("12345", buffer.getvalue())
```

## 패턴 3: 예외 테스트 — assertRaises

```python
class StrEnumTestCase(TestCase):
    # 방법 1: 컨텍스트 매니저
    def test_unique_violation(self):
        with self.assertRaises(ValueError):
            @unique
            class _Test(_StrEnum):
                a = auto()
                b = "a"

    # 방법 2: 콜러블
    def test_type_error(self):
        argument = Argument(sig.parameters["arg0"])
        self.assertRaises(TypeError, lambda: argument.annotated_args)
```

## 패턴 4: 비동기 테스트 — asyncio.run() 래핑

pytest fixture 미사용. `asyncio.run()`으로 직접 래핑합니다.

```python
class TestWebSocketClient(TestCase):
    def test_client_connect_disconnect(self) -> None:
        async def run_test() -> None:
            async def echo_handler(websocket):
                async for message in websocket:
                    await websocket.send(f"Echo: {message}")

            server = await websockets.serve(echo_handler, "localhost", 18770)

            try:
                client = WebSocketClient("ws://localhost:18770")
                await client.connect()
                self.assertTrue(client.is_connected)
                await client.disconnect()
                self.assertFalse(client.is_connected)
            finally:
                server.close()
                await server.wait_closed()

        asyncio.run(run_test())
```

## 패턴 5: 콜백 추적 — 리스트로 호출 기록

mock/patch 대신 리스트로 콜백 호출을 추적합니다.

```python
class TestProtobufHandler(TestCase):
    def test_register_handler(self) -> None:
        handler = ProtobufHandler()
        called = []

        def msg_handler(data: bytes) -> Optional[bytes]:
            called.append(data)
            return b"response"

        handler.register(1, msg_handler)
        msg = ProtobufHandler.encode_message(1, b"test")
        response = handler.dispatch(msg)

        self.assertEqual([b"test"], called)
        self.assertEqual(b"response", response)
```

## 패턴 6: 다중 TestCase 클래스 — 기능별 분리

한 파일에 여러 TestCase를 두어 관심사를 분리합니다.

```python
class TestModbusTcpClientBasic(TestCase):
    def test_client_properties(self) -> None:
        client = ModbusTcpClient(host="127.0.0.1", port=15030)
        self.assertEqual(client.host, "127.0.0.1")
        self.assertFalse(client.is_connected)


class TestModbusTcpClientStartStop(TestCase):
    def test_start_stop_no_server(self) -> None:
        client = ModbusTcpClient(host="localhost", port=15033, timeout=1.0)
        client.start()
        self.assertTrue(client.is_running)
        client.stop()
        self.assertFalse(client.is_running)
```

## 패턴 7: @skipIf — 조건부 건너뛰기

외부 의존성(실행 파일, 서버 등)이 필요한 테스트:

```python
from shutil import which
from unittest import skipIf


class VersionTestCase(TestCase):
    @skipIf(not which("ffmpeg"), "Not found ffmpeg executable")
    def test_version(self):
        version = inspect_version()
        self.assertLessEqual(4, version.major)
```

## 패턴 8: 데이터 주도 테스트 — 인라인 다중 검증

별도 파라미터화 라이브러리 없이, 한 테스트 메서드에서 여러 케이스를 검증:

```python
class CaseConverterTestCase(TestCase):
    def test_camel_case(self):
        expected = "helloWorld"
        self.assertEqual(expected, camel_case("hello_world"))
        self.assertEqual(expected, camel_case("HelloWorld"))
        self.assertEqual(expected, camel_case("hello-world"))
        self.assertEqual(expected, camel_case("hello world"))
        self.assertEqual(expected, camel_case("HELLO_WORLD"))
        self.assertEqual(expected, camel_case("helloWorld"))
```

## 패턴 9: 직렬화 왕복 테스트 — roundtrip

```python
class DtypeTestCase(TestCase):
    def test_serialize_deserialize_int(self):
        dtype0 = Dtype(int)
        dtype1 = deserialize(serialize(dtype0), Dtype)
        self.assertEqual(dtype0, dtype1)
        self.assertEqual(dtype0.type, int)
        self.assertEqual("builtins.int", dtype0.path)

    def test_copy(self):
        dtype0 = Dtype(Path)
        self.assertEqual(copy(dtype0), dtype0)

    def test_deepcopy(self):
        dtype0 = Dtype(Path)
        self.assertEqual(deepcopy(dtype0), dtype0)
```

## 패턴 10: Property/Proxy 테스트 — 상태 동기화 검증

```python
class FFmpegTestCase(TestCase):
    def test_proxy(self):
        config = FFmpegConfig()
        proxy1 = config.create_ffmpeg_proxy()
        proxy2 = config.create_ffmpeg_proxy()

        self.assertEqual("ffmpeg", config.ffmpeg)
        self.assertEqual("ffmpeg", proxy1.get())
        self.assertEqual("ffmpeg", proxy2.get())

        config.ffmpeg = "0"
        self.assertEqual("0", proxy1.get())

        proxy1.set("1")
        self.assertEqual("1", config.ffmpeg)
        self.assertEqual("1", proxy2.get())
```

## 패턴 11: 서버 테스트 — setUp/tearDown

```python
class TestModbusTcpServerConnection(TestCase):
    def setUp(self) -> None:
        self.datastore = ModbusDataStore()
        self.server = ModbusTcpServer(
            host="localhost",
            port=15024,
            datastore=self.datastore,
        )
        self.server.start()
        time.sleep(0.1)

    def tearDown(self) -> None:
        self.server.stop()

    def test_client_connection(self) -> None:
        client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        try:
            client.connect(("localhost", 15024))
            time.sleep(0.1)
            self.assertEqual(self.server.client_count, 1)
        finally:
            client.close()
```

## 패턴 12: 헬퍼 메서드 — `_` 접두사 private 메서드

테스트 클래스 내 재사용 로직은 `_` 접두사 메서드로:

```python
class TestHubManager(TestCase):
    def _create_websocket_client(self, host: str, port: int) -> socket.socket:
        client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        client.connect((host, port))
        # ... WebSocket 핸드셰이크
        return client

    @staticmethod
    def _encode_binary_frame(data: bytes) -> bytes:
        # ... 프레임 인코딩 로직
        return bytes(frame)

    def test_hub_message(self) -> None:
        client = self._create_websocket_client("localhost", 19301)
        frame = self._encode_binary_frame(b"hello")
```

## 패턴 13: 공유 테스트 유틸리티 — tester/liveness.py

```python
# -*- coding: utf-8 -*-

from functools import lru_cache
from pathlib import Path
from typing import Optional

from your_pkg.network.liveness import ServerLivenessProbe


@lru_cache
def get_project_root_path() -> Path:
    return Path(abspath(dirname(__file__))).parent


class TestServerLivenessProbe(ServerLivenessProbe):
    @classmethod
    def from_dotenv(cls, address_key: str, timeout_key: Optional[str] = None):
        address = get_project_root_dotenv_value(address_key)
        if address is None:
            return cls()
        return cls(address, timeout)

    def __bool__(self) -> bool:
        return self.is_alive()
```

## 자주 사용하는 Assertion

| Assertion | 용도 | 예시 |
|---|---|---|
| `assertEqual(expected, actual)` | 값 동등 비교 | `self.assertEqual(30, result)` |
| `assertTrue(expr)` | 참 확인 | `self.assertTrue(client.is_connected)` |
| `assertFalse(expr)` | 거짓 확인 | `self.assertFalse(client.is_running)` |
| `assertIsInstance(obj, cls)` | 타입 확인 | `self.assertIsInstance(result, str)` |
| `assertIsNone(obj)` | None 확인 | `self.assertIsNone(response)` |
| `assertIsNotNone(obj)` | 비-None 확인 | `self.assertIsNotNone(session.origin)` |
| `assertIn(item, container)` | 포함 확인 | `self.assertIn("Echo", received)` |
| `assertRaises(exc, callable)` | 예외 발생 확인 | `self.assertRaises(TypeError, func)` |
| `assertLessEqual(a, b)` | 크기 비교 | `self.assertLessEqual(4, version.major)` |
| `assertGreater(a, b)` | 크기 비교 | `self.assertGreater(len(msgs), 0)` |
| `assertTupleEqual(a, b)` | 튜플 비교 | `self.assertTupleEqual((2, 1), btn.pivot)` |

## 테스트 작성 금지 사항

- **pytest fixture 미사용**: `conftest.py`, `@pytest.fixture` 사용 금지.
- **mock/patch 최소화**: `unittest.mock` 대신 콜백 리스트 추적 패턴 사용.
- **파라미터화 데코레이터 미사용**: `@parameterized` 등 미사용.
- **테스트 데이터 외부 파일 미사용**: 인라인으로 테스트 데이터 정의.

## 규칙

- 한 테스트 메서드는 한 동작을 검증 — 단, 직렬화 왕복 등 강하게 연결된 검증은 묶을 수 있음.
- `assertEqual` 인자 순서: `(expected, actual)`.
- 테스트 메서드명에는 검증 대상과 시나리오를 포함: `test_update_triggers_callback`.
- 외부 의존성은 `@skipIf`로 처리, 환경 없으면 건너뛰기.
- 테스트 자체에 로직 분기를 두지 마세요 — `if/else`가 필요하면 별도 메서드로 분리.
