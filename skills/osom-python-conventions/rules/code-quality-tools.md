# 코드 품질 도구 설정

| 도구 | 설정 | 역할 |
|---|---|---|
| `black` | 88자 라인 폭 | 코드 포매팅 |
| `flake8` | `E203`, `W503` 무시, 88자 | 린트 |
| `isort` | `profile = black`, `py_version = 39` | import 정렬 |
| `mypy` | `ignore_missing_imports = True` | 타입 검사 |
| `pytest` | `asyncio_mode = auto` | 테스트 실행 (호환성) |

## pyproject.toml 예시

```toml
[tool.black]
line-length = 88
target-version = ["py39", "py310", "py311", "py312"]

[tool.isort]
profile = "black"
py_version = 39
line_length = 88

[tool.mypy]
ignore_missing_imports = true
strict_optional = true
warn_unused_ignores = true

[tool.pytest.ini_options]
asyncio_mode = "auto"
```

## setup.cfg (flake8)

flake8는 `pyproject.toml`을 직접 지원하지 않으므로 `setup.cfg` 또는 `.flake8`를 사용합니다.

```ini
[flake8]
max-line-length = 88
extend-ignore =
    E203,
    W503
exclude =
    .git,
    __pycache__,
    build,
    dist,
    .venv
```

### 왜 E203, W503 무시?

- **E203 (whitespace before ':')**: black이 슬라이싱 등에서 의도적으로 공백을 넣습니다. flake8 기본 규칙과 충돌.
- **W503 (line break before binary operator)**: PEP 8 최신 권장 사항은 이항 연산자 앞에서 줄바꿈하는 것입니다.

## 명령어

```bash
# 포매팅
black .

# Import 정렬
isort .

# 린트
flake8 .

# 타입 검사
mypy .

# 모두 실행
black . && isort . && flake8 . && mypy .
```

## 사전 커밋 훅 (pre-commit)

`.pre-commit-config.yaml`:

```yaml
repos:
  - repo: https://github.com/psf/black
    rev: 24.3.0
    hooks:
      - id: black

  - repo: https://github.com/pycqa/isort
    rev: 5.13.2
    hooks:
      - id: isort

  - repo: https://github.com/pycqa/flake8
    rev: 7.0.0
    hooks:
      - id: flake8

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.9.0
    hooks:
      - id: mypy
```

## 규칙

- CI에서 `black --check`, `isort --check`, `flake8`, `mypy` 모두 통과 필수.
- 로컬 커밋 전 pre-commit 훅으로 자동 검증.
- 도구 버전을 `pyproject.toml`이나 `requirements-dev.txt`에 고정.
- 프로젝트별 설정 차이가 있다면 그 설정이 우선.
