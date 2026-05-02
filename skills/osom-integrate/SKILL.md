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

1. 프로젝트 루트의 `STRUCTURE.md`(Structure doc, Rules directory)와 `COMMANDS.md`(Build/Check, Typecheck)를 확인합니다.
2. `STRUCTURE.md`가 가리키는 구조 문서, 그리고 `Rules directory` 안의 라우트 규칙 / i18n 규칙 문서를 (있다면) 읽어 통합 대상 구조를 파악합니다.

## 규칙

각 검증 항목의 상세 내용은 `rules/` 디렉토리를 참조하세요. 변경된 파일에 따라 관련 항목만 실행합니다.

1. [라우트 등록](rules/route-registration.md) — paths, lazy import, Route 엘리먼트 등록 확인
2. [타입 정합성](rules/type-consistency.md) — 타입 정의 위치와 import 경로 확인
3. [i18n 키 동기화](rules/i18n-key-sync.md) — 모든 로케일에 동일 키 존재 여부
4. [Worker / 백엔드 설정](rules/worker-backend-config.md) — Worker 라우트, env, wrangler 바인딩
5. [DB-API 연동](rules/db-api-integration.md) — 스키마 변경과 API 변경 일치 여부

## 실행 절차

1. `git diff --name-only` (또는 `git diff --name-only HEAD~<N>`)로 변경된 파일 목록 확인.
2. 변경된 파일에 따라 관련 검증 항목만 실행.
3. 누락 발견 시 **직접 수정**합니다.
4. `COMMANDS.md`의 `Build/Check`를 실행해 최종 빌드 검증.

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
