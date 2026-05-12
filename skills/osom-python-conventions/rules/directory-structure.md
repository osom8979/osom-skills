# 디렉토리 구조

## 전체 구조: 기능별 그룹화 (Functional Grouping)

```
your_pkg/                     # 메인 패키지
├── __init__.py               # 버전만 포함 (__version__)
├── __main__.py               # CLI 진입점 (최소 코드)
├── entrypoint.py             # main() 함수
├── arguments.py              # CLI 인자 파싱
├── variables.py              # 환경 변수 정의
│
├── apps/                     # ── 애플리케이션 레이어 ──
│   ├── agent/                #   백그라운드 서비스
│   ├── cli/                  #   CLI 인터페이스
│   ├── gui/                  #   GUI 애플리케이션
│   │   └── modes/            #     N개 서브 모드 (각 기능별 디렉토리)
│   └── tester/               #   테스트용 애플리케이션
│
├── core/                     # ── 도메인 코어 ──
│   ├── graph.py
│   ├── node.py
│   └── manager.py
│
├── nodes/                    # ── 노드 시스템 (라이브러리별 그룹) ──
│   ├── node.py               #   베이스 노드
│   └── defaults/             #   기본 노드 구현
│       ├── numpy/            #     NumPy 연산 노드
│       ├── pandas/           #     Pandas 연산 노드
│       └── opencv/           #     OpenCV 연산 노드
│
├── ffmpeg/                   # ── 미디어 처리 ──
│   ├── ffmpeg/               #   FFmpeg 빌더
│   ├── ffprobe/              #   미디어 정보 탐색
│   └── h264/, hevc/, av1/    #   코덱별 모듈
│
├── network/                  # ── 네트워크/통신 ──
│   ├── uri/
│   └── ports/
├── ws/                       #   WebSocket
│   ├── asyncio/
│   └── threading/
│
├── maths/                    # ── 수학 ──
│   ├── geometry/
│   │   ├── bbox/
│   │   ├── system/
│   │   └── transform/
│   └── statistics/
│
├── config/                   # ── 설정 ──
│   └── sections/             #   설정 섹션 (기능별 1:1 매핑)
│
├── types/                    # ── 타입 시스템 ──
│   ├── dataclass/
│   ├── enum/
│   └── override.py
│
├── logging/                  # ── 로깅 ──
│   ├── formatters/
│   ├── handlers/
│   └── records/
│
└── ...                       # (기타 유틸리티 모듈)

tester/                       # 테스트 패키지 (your_pkg/ 미러링)
├── __init__.py
├── test_entrypoint.py        # your_pkg/entrypoint.py 테스트
├── liveness.py               # 공유 테스트 유틸리티
├── core/                     # your_pkg/core/ 테스트
├── maths/                    # your_pkg/maths/ 테스트
│   ├── geometry/
│   └── statistics/
└── ...
```

## 그룹화 원칙

| 원칙 | 설명 | 예시 |
|---|---|---|
| **기능별 그룹** | 관련 기능을 하나의 디렉토리에 집중 | `ffmpeg/`, `maths/`, `ws/` |
| **라이브러리별 분류** | 외부 라이브러리 의존 코드를 분리 | `nodes/defaults/numpy/`, `nodes/defaults/opencv/` |
| **카테고리별 서브모듈** | 동일 도메인 내 기능 분류 | `maths/geometry/bbox/` |
| **인터페이스 믹스인** | `able/` 디렉토리에 인터페이스 집중 | `gui/able/displayable.py` |
| **설정 미러링** | config 섹션이 기능 모듈과 1:1 대응 | `config/sections/font.py` ↔ `fonts/` |
| **테스트 미러링** | 소스와 동일 구조 유지 | `your_pkg/core/node.py` → `tester/core/test_node.py` |

## 최대 깊이: 3단계

```
your_pkg/nodes/defaults/numpy/    # 3단계 ✅
your_pkg/maths/geometry/bbox/     # 3단계 ✅
your_pkg/a/b/c/d/                 # 4단계 ❌
```

3단계를 초과하지 않습니다. 더 깊은 분류가 필요하면 파일명으로 구분합니다.

## `__init__.py` 원칙

자세한 내용은 [파일/모듈 조직](file-organization.md) 참조.

```python
# 대부분 비어 있음
# -*- coding: utf-8 -*-
```

- `__init__.py`에 re-export 하지 않습니다.
- 임포트는 항상 전체 경로: `from your_pkg.core.node import Node`.
- 예외: 패키지 루트 `__init__.py`에 `__version__` 포함.

## 특수 파일 역할

| 파일 | 위치 | 역할 |
|---|---|---|
| `__init__.py` | 모든 패키지 | 비어 있음 (패키지 표시용) |
| `__main__.py` | 패키지 루트 | `python -m your_pkg` 진입점 (최소 코드) |
| `entrypoint.py` | 패키지 루트 | `main()` 함수 정의 |
| `arguments.py` | 패키지 루트 | argparse CLI 인자 파싱 |
| `variables.py` | 패키지 루트 | 환경 변수 및 전역 상수 |
| `liveness.py` | `tester/` | 테스트용 공유 유틸리티 |

## 규칙

- 기능별로 디렉토리 분리, 레이어별 분리는 보조 수단.
- 외부 라이브러리 의존 코드는 라이브러리명을 디렉토리로 분리.
- 테스트는 소스 구조를 그대로 미러링.
- 최대 깊이 3단계 — 그 이상은 파일명으로 분류.
