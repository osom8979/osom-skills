# 에이전트/스킬 정의 검증

`.claude/agents/*.md`, `.claude/skills/*/SKILL.md`의 정의가 실제 프로젝트 상태와 일치하는지 확인합니다.

확인 대상:

- 참조하는 디렉토리/파일이 실제로 존재하는지
- Working Area 정의가 실제 디렉토리와 일치하는지
- `allowed-tools`에 나열된 도구가 유효한지
- frontmatter 필드(name, description, version) 누락이나 형식 오류가 없는지

깨진 참조는 ❌로 표시하고 수정 제안을 함께 보고합니다.
