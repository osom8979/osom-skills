# 디렉토리별 파일 규칙 검증

`.osom-skills`의 **Project documents → Rules directory**에 속하는 문서(예: `structure.md`, `directory-rules/*.md`)의 목록이 실제 디렉토리 내용과 일치하는지 확인합니다.

## 확인 대상 (프로젝트에 존재하는 것만)

- `hooks/` — 문서에 나열된 훅 vs 실제 파일
- `contexts/` — 문서에 나열된 컨텍스트 vs 실제 파일
- `types/` — 문서에 나열된 타입 파일 vs 실제 파일
- `lib/` — 문서에 나열된 유틸리티 vs 실제 파일
- `constants/` — 문서에 나열된 상수 파일 vs 실제 파일
- `routes/` — 문서에 나열된 라우트 파일 vs 실제 파일
- `components/` — 카테고리 목록 일치 여부
- `components/ui/` — 설치된 shadcn 컴포넌트 목록

## 필수 동반 파일 누락

`.osom-skills`의 **Required companion files** 글롭 매칭 규칙으로 누락 파일을 검출합니다.

존재하지 않는 디렉토리는 검증에서 자동 제외합니다.
