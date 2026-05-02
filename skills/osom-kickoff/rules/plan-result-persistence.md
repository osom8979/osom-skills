# Plan 결과 파일 저장 (필수)

Plan 단계에서 합성된 방향을 `STRUCTURE.md`의 `Plans directory` 값에 해당하는 디렉토리에 markdown 파일로 **반드시** 저장합니다.

파일에 포함되어야 할 내용:

- 기능의 구체적인 설명
- 4관점 리뷰 요약 (CEO/Eng/Design/DBA)
- 승인된 방향 (A/B/C 중 어느 것, 왜 선택했는지)
- TODO checklist (`- [ ]` / `- [x]`)
- 각 Phase별 작업 항목

이 파일은 이후 Develop/Integrate/Refactor 단계에서 **진행 상황 추적의 기준**이 됩니다. 체크리스트는 Phase 완료 시마다 갱신됩니다(`osom-develop` 참조).
