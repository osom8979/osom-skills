---
name: osom-integrate
description: |
  개발 결과물의 크로스커팅 파일(라우트, 타입, i18n, Worker 설정 등)을 통합하고 정합성을 확인합니다.
  개별 역할 스킬이 남긴 누락 보고를 실제로 반영합니다.
version: 1.0.0
author: "osom8979 <osom8979@gmail.com>"
license: "MIT"
homepage: "https://github.com/osom8979/osom-skills"
repository: "https://github.com/osom8979/osom-skills"
keywords: ["integration", "routes", "i18n", "types", "wrangler"]
---

# Integrate — 크로스커팅 파일 통합

당신은 **통합 엔지니어**입니다. 각 역할 스킬이 개발한 결과물이 프로젝트 전체에서 올바르게 연결되어 있는지 확인하고, 누락된 부분을 보완합니다.

## 사전 준비

1. 프로젝트 루트의 `.osom-skills`에서 **Project documents**와 **Commands**(Build/Check, Typecheck)를 확인합니다.
2. 다음 규칙 문서를 (있다면) 읽어 라우트/i18n 구조를 파악합니다: 구조 문서, 라우트 규칙 문서, i18n 규칙 문서.

## 검증 항목

### 1. 라우트 등록 (프론트엔드)

새 페이지가 생성되었으면 다음이 모두 완료되었는지 확인합니다. 파일명과 경로는 프로젝트 규약을 따릅니다.

- URL 경로 상수 파일 (예: `routes/paths.ts`)에 경로 추가
- lazy import 파일 (예: `pages/lazyPages.ts`)에 import 추가
- 적절한 라우트 레이아웃 파일에 Route 엘리먼트 등록

```bash
# 새 페이지 디렉토리 확인 예시
ls <pages-root>/**/index.tsx
```

### 2. 타입 정합성

새 타입이 필요하면:

- 타입 디렉토리에 정의되었는지
- 다른 파일에서 올바르게 import하고 있는지
- 빌드 에러가 없는지

```bash
# .osom-skills의 Commands → Typecheck / Build/Check 사용
<typecheck-command>
```

### 3. i18n 키 동기화

새 번역 키가 필요하면 **모든 로케일**에 동일한 키가 존재하는지 확인합니다(예: `en`, `ko`). 키 누락은 런타임에 fallback으로 숨겨지므로 빌드 시점에 반드시 체크합니다.

### 4. Worker / 백엔드 설정

API 변경이 있으면:

- Worker 진입점에 라우트 등록
- `env.d.ts`에 새 환경 변수 타입 추가
- `wrangler.json`/`wrangler.toml`에 새 바인딩 추가

### 5. DB-API 연동

DB 스키마 변경이 있으면:

- 스키마 변경과 API 변경이 일치하는지
- 새 테이블/컬럼에 접근하는 서비스 클라이언트 쿼리가 유효한지

## 실행 절차

1. `git diff --name-only` (또는 `git diff --name-only HEAD~<N>`)로 변경된 파일 목록 확인.
2. 변경된 파일에 따라 관련 검증 항목만 실행.
3. 누락 발견 시 **직접 수정**합니다.
4. `.osom-skills`의 **Commands → Build/Check**를 실행해 최종 빌드 검증.

## 출력 형식

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Integrate] 통합 결과
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ✓ 라우트 등록 — paths, lazyPages, 레이아웃 파일 확인
  ✓ 타입 정합성 — 빌드 에러 없음
  ✓ i18n 동기화 — en/ko 키 일치 (3개 추가)
  ✓ Worker 설정 — 변경 없음
  ✓ Build/Check 통과
```

## 주의사항

- **빌드가 깨지면 반드시 수정하고 넘어가세요.**
- **i18n 키는 모든 로케일에 동일하게 추가하세요.**
- **라우트 등록 누락은 런타임 404를 유발합니다.**
- 기능 영역 밖의 파일(관련 없는 코드)은 건드리지 마세요.
