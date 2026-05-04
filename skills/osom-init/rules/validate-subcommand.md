# validate 서브명령 세부

`/osom-init validate`는 6개 ROOT 파일이 실제 프로젝트 상태와 일치하는지 검사합니다.

## 검사 항목

### `COMMANDS.md`
- 각 섹션의 `npm run <script>` 또는 `./npm run <script>`가 `package.json`에 실제로 존재하는지
- 슬래시 명령(`/osom-git-commit` 등)이 설치된 스킬에 존재하는지

### `STRUCTURE.md`
- `Structure doc`, `Code style doc`, `Plans directory`, `Rules directory` 경로가 실제로 존재하는지
- `Required companion files` 글롭 패턴의 대상 파일이 프로젝트에 하나라도 있는지 (없으면 죽은 규칙 경고)

### `GUARDRAILS.md`
- 가드 항목과 모순되는 흔적이 있는지 (예: "Never push" 가드가 있는데 최근 push가 있었는지는 검사하지 않음 — 단지 가드 항목 형식만 확인)

### `DESIGN.md` / `DATABASE.md` / `ARCHITECTURE.md`
- 도메인 문서는 자동 검증이 어렵습니다. 다음만 확인:
  - 핵심 질문 매핑 섹션 헤더가 모두 존재하는지
  - 본문이 `_(미정)_`인 섹션이 몇 개인지 → 진행률 리포트

## 출력

각 항목별 ✅/⚠로 표시하고, 수정 제안을 함께 출력합니다.

```
[validate] ROOT 문서 검증 결과
  ✅ COMMANDS.md            (4/4 명령 모두 유효)
  ⚠️  STRUCTURE.md           (Plans directory `docs/plans/` 없음 — 디렉토리 만들거나 경로 수정)
  ✅ GUARDRAILS.md          (4개 항목)
  ⚠️  DESIGN.md              (8개 항목 중 3개 미정)
  ✅ DATABASE.md            (9개 항목 모두 채움)
  ⚠️  ARCHITECTURE.md        (7개 항목 중 2개 미정)

수정 제안:
  - mkdir -p docs/plans  또는  /osom-init STRUCTURE.md 로 경로 수정
  - /osom-init DESIGN.md 로 미정 항목 보완
  - /osom-init ARCHITECTURE.md 로 미정 항목 보완
```

**자동 수정은 하지 않습니다** — 사용자가 필요하면 `/osom-init` 또는 해당 서브명령을 다시 호출합니다.
