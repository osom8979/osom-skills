# validate 서브명령 세부

`/osom-init validate`는 다음을 확인합니다.

- `Commands`의 스크립트가 `package.json`에 실제로 있는지
- `Project documents`의 경로가 실제로 존재하는지
- `Enabled roles`의 각 역할이 osom-skills 저장소에 실제로 존재하고 프로젝트와 맞는지
- `Required companion files` 글롭이 실제 프로젝트에서 의미 있는지 (해당 패턴의 파일이 하나도 없으면 경고)

결과는 각 항목별 ✅/⚠로 표시하고, 수정 제안을 함께 출력합니다. **자동 수정은 하지 않습니다** — 사용자가 필요하면 `/osom-init` 또는 해당 서브명령을 다시 호출합니다.
