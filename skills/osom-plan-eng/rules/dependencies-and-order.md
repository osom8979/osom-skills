# 종속성 / 실행 순서

`.osom-skills`의 **Phase dependency hints**를 참고해 역할 간 실행 순서를 제안합니다.

```
Phase 1 (병렬): 역할 A, 역할 B
Phase 2: 역할 C (Phase 1 결과 필요)
Phase 3: 역할 D
```

같은 Phase에 묶인 역할은 병렬 실행 가능해야 합니다 — 서로의 출력물을 입력으로 쓰지 않아야 합니다. 종속성이 있으면 다음 Phase로 분리합니다.
