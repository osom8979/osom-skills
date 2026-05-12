# Import 스타일

## 순서: stdlib → third-party → local

`isort`의 `black` 프로필을 따릅니다. 그룹 간에는 빈 줄 1개로 구분합니다.

```python
# -*- coding: utf-8 -*-

# 1. 표준 라이브러리 (알파벳 순, from 형태)
from abc import ABC, abstractmethod
from dataclasses import dataclass, field
from enum import IntEnum, StrEnum, auto, unique
from os import PathLike
from os.path import expanduser, isfile, join
from re import Pattern
from re import compile as re_compile
from sys import exit as sys_exit
from typing import (
    Any,
    Callable,
    Dict,
    Final,
    Generic,
    List,
    Optional,
    Sequence,
    Tuple,
    Type,
    TypeAlias,
    TypeVar,
    Union,
)

# 2. 서드파티
from yaml import dump, full_load

# 3. 로컬
from your_pkg.config import Config
from your_pkg.logging import logger
```

## 규칙

- `from module import Name` 형태 사용. `import module` 지양.
- star import (`from x import *`) 절대 금지.
- 이름 충돌 시 별칭 사용: `from sys import exit as sys_exit`.
- 멀티라인은 괄호 `()` 사용, 백슬래시 `\` 금지.
- 같은 모듈에서 여러 이름을 가져올 때는 알파벳 순으로 정렬.

## 예외

- `import os` 같은 짧은 stdlib 모듈은 가독성을 위해 허용할 수 있으나, 일관성을 위해 가능하면 `from` 형태를 우선합니다.
- C 확장 모듈 등 `from` 형태가 동작하지 않는 경우는 `import module` 허용.

## 자동화

```bash
isort --profile black .
```

`pyproject.toml` 설정 예시:

```toml
[tool.isort]
profile = "black"
py_version = 39
```
