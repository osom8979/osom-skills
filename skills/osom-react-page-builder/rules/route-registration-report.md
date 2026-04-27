# 라우트 등록 보고

새 페이지를 만들었으면 **라우트 등록을 직접 수행하지 마세요**. 결과 보고에 필요한 항목을 명시하면 orchestrator의 `integrate` 스킬이 일괄 처리합니다.

```
⚠ 라우트 등록 필요:
  - paths: newFeature: '/new-feature'
  - lazy import: NewFeaturePage
  - Route 엘리먼트 추가 위치: (app) 스코프
```

이렇게 분리하면 라우트/타입/i18n 같은 크로스커팅 파일을 한 번에 통합할 수 있어 머지 충돌과 누락을 줄입니다.
