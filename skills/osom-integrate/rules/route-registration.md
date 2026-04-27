# 라우트 등록 (프론트엔드)

새 페이지가 생성되었으면 다음이 모두 완료되었는지 확인합니다. 파일명과 경로는 프로젝트 규약을 따릅니다.

- URL 경로 상수 파일 (예: `routes/paths.ts`)에 경로 추가
- lazy import 파일 (예: `pages/lazyPages.ts`)에 import 추가
- 적절한 라우트 레이아웃 파일에 Route 엘리먼트 등록

```bash
# 새 페이지 디렉토리 확인 예시
ls <pages-root>/**/index.tsx
```

라우트 등록 누락은 **런타임 404**를 유발하므로 발견 시 직접 수정합니다.
