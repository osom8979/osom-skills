# 종속성 / 실행 순서

Phase는 **항상 순차 실행**합니다. 같은 Phase 내 역할 스킬만 병렬로 실행하고, 출력물이 다음 작업의 입력이 되는 역할은 다음 Phase로 분리합니다.

```
Phase 1 (병렬 가능): 역할 A, 역할 B
Phase 2: 역할 C (Phase 1 결과 필요)
Phase 3: 역할 D
```

같은 Phase에 묶인 역할은 서로의 출력물을 입력으로 쓰지 않아야 합니다. 의존이 있으면 다음 Phase로 분리합니다.

## 일반적인 선후 관계

작업 유형 사이의 전형적인 입력→출력 관계 예시입니다(설치된 역할 중 해당하는 것만 적용).

- DB 스키마 (`osom-supabase-schema`) → API/워커 (`osom-hono-worker`)
- shadcn 설치 (`osom-shadcn-manager`) → 컴포넌트 빌더 (`osom-react-component-builder`)
- 컴포넌트 빌더 → 페이지 빌더 (`osom-react-page-builder`)

종속성이 없으면 같은 Phase에 묶을 수 있습니다.
