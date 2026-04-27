# 역할 호출 규칙

각 역할 스킬을 호출할 때 반드시 다음을 지킵니다.

1. **충분한 컨텍스트 전달** — 역할은 현재 대화를 모릅니다. 무엇을 만들어야 하는지, 파일 위치, 관련 타입, 기존 코드 패턴 등을 프롬프트에 명시.
2. **문서 읽기 지시** — 역할에게 관련 규칙 문서(`.osom-skills`의 Project documents 값)를 먼저 읽으라고 지시.
3. **병렬 호출** — 같은 Phase 내 작업은 **하나의 메시지에서 동시에** 도구를 호출. 순차 호출은 Phase 분리 시에만 사용.
4. **결과 검증** — 각 Phase 완료 후 `.osom-skills`의 Build/Check로 빌드 확인.
5. **Task 추적** — 각 역할 작업을 `TaskCreate`로 생성하고, 완료 시 `TaskUpdate`로 상태 갱신.

구현 옵션:

- 해당 역할 스킬이 `.claude/agents/`에 서브에이전트로도 설치되어 있다면 Agent 도구의 `subagent_type`을 사용.
- 서브에이전트가 없으면 Agent 도구로 `general-purpose`를 띄우고 프롬프트에 `"역할 스킬 <role-name>의 지침을 그대로 따르세요. SKILL.md 경로: <path>"`를 명시.
