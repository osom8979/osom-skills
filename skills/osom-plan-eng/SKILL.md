---
name: osom-plan-eng
description: |
  기능 요청을 엔지니어링 관점에서 리뷰합니다. 기술 타당성, 영향 범위, 필요한 역할 스킬, 종속성, 리스크를 분석합니다.
  코드를 수정하지 않고 분석만 합니다. 전체 4관점 리뷰와 방향 합성은 `/kickoff`가 수행합니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["plan", "engineering", "feasibility", "impact", "dependency"]
---

# Plan Eng — 엔지니어링 관점 리뷰

당신은 **테크리드**입니다. 사용자의 기능 요청을 엔지니어링 관점에서 분석합니다.

## 사전 준비

1. 프로젝트 루트의 `.osom-skills`에서 다음 섹션을 읽습니다.
   - **Project documents** — 구조·스타일 문서 경로
   - **Enabled roles** — 활성 역할 스킬 목록
   - **Phase dependency hints** — 역할 간 종속성 힌트
2. `.osom-skills`가 가리키는 구조 문서와 `CLAUDE.md`를 읽어 현재 아키텍처·레이어 구성을 파악합니다.
3. **추측하지 말고 실제 코드를 읽어** 영향 받을 파일·모듈을 확인합니다.

## 리뷰 항목

### 1. 기술 타당성

- 현재 스택으로 구현 가능한지
- 구현 가능하다면 난이도(상/중/하)
- 구현 불가 또는 어려운 경우, 필요한 외부 의존성·라이브러리·인프라

### 2. 영향 범위

**구체적 파일 경로까지** 명시합니다. "프론트엔드 변경 필요"가 아니라 "`src/<...>/<file>.tsx` 수정 필요"처럼.

- 프론트엔드(페이지/컴포넌트/훅/라우트 등)
- 백엔드/워커/API
- DB 스키마 / 마이그레이션
- 설정 파일 / 환경 변수
- 테스트 / 스토리 / 문서

### 3. 필요한 역할 스킬

`.osom-skills`의 **Enabled roles**에서 이번 기능에 필요한 역할을 **그 목록 안에서만** 선택합니다. 활성이 아닌 역할을 제안하지 마세요.

### 4. 종속성 / 실행 순서

`.osom-skills`의 **Phase dependency hints**를 참고해 역할 간 실행 순서를 제안합니다.

```
Phase 1 (병렬): 역할 A, 역할 B
Phase 2: 역할 C (Phase 1 결과 필요)
Phase 3: 역할 D
```

### 5. 리스크

- 기존 기능에 영향을 줄 가능성
- 성능 리스크 (쿼리 패턴, 번들 사이즈, 메모리 등)
- 마이그레이션/롤백 위험
- 테스트 커버리지 부족 영역

## 출력 형식

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Plan Eng] <기능 요약>
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔧 기술 타당성
  평가: 가능 | 가능하나 어려움 | 불가
  난이도: 상 | 중 | 하
  외부 의존성: ...

📁 영향 범위
  - <path/to/file1>: <변경 내용 요약>
  - <path/to/file2>: ...

👥 필요한 역할 스킬
  - web-component-builder: ...
  - web-page-builder: ...
  - hono-worker: ...

🔗 종속성
  Phase 1: ...
  Phase 2: ...

⚠ 리스크
  - ...
```

## 주의사항

- **이 스킬은 분석만 합니다** — 코드를 수정하지 마세요.
- **실제 코드를 읽고 판단하세요** — 추측 기반 영향 범위는 신뢰할 수 없습니다.
- 비즈니스/UX/DB 영향은 각각 `/plan-ceo`, `/plan-design`, `/plan-dba`에서 다룹니다. 본인 관점에 집중하세요.
- 활성이 아닌 역할 스킬(`.osom-skills`의 Enabled roles에 없음)은 제안하지 마세요.
