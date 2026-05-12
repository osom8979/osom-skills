# 로깅

프로젝트 전용 logger를 사용합니다. 모듈마다 `logging.getLogger(__name__)`로 생성하거나, 프로젝트 공통 logger를 import합니다.

## 프로젝트 공통 logger

```python
from your_pkg.logging import logger

logger.info(f"Initialize config: '{logging_path}'")
logger.warning(f"Detect OpenGL config via subprocess ...")
logger.error(f"Failed to send message: {e}")
logger.debug(f"Message sent: {message}")
logger.exception(e)
```

## 모듈별 logger

```python
import logging

logger = logging.getLogger(__name__)


def do_work() -> None:
    logger.info("Starting work")
```

## 로그 레벨 사용 기준

| 레벨 | 용도 | 예시 |
|---|---|---|
| `debug` | 상세한 진단 정보, 개발 시에만 활성화 | "Cache hit for key=X" |
| `info` | 정상 진행 상태, 운영 시 켤 수 있는 수준 | "Started server on port 8080" |
| `warning` | 잠재적 문제, 동작은 계속됨 | "Config value missing, using default" |
| `error` | 작업 실패, 호출 측이 대응해야 함 | "Failed to send message" |
| `critical` | 시스템 동작 불가 | "Database unreachable" |
| `exception` | except 블록에서 stack trace 포함 로그 | `logger.exception(e)` |

## 예외 로깅

```python
try:
    do_something()
except SomeError as e:
    logger.exception(e)  # stack trace 포함
    raise  # 또는 적절히 처리
```

`logger.exception()`은 `logger.error()`와 같은 레벨이지만 stack trace를 자동 포함합니다. except 블록에서만 사용합니다.

## 규칙

- 로그 메시지는 [f-string](string-formatting.md)으로 작성.
- 민감 정보(비밀번호, 토큰, 개인정보)를 로그에 출력하지 마세요.
- 사용자 입력을 그대로 로깅하지 마세요 (log injection 방지: 개행 문자 등 필터링).
- 메시지는 영문이 기본 (운영 환경 i18n 안정성). 사용자 대상 메시지는 별도 처리.
- 로그 메시지 끝에 마침표를 붙이지 않습니다 (관례).
