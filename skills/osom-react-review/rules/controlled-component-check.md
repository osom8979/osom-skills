# Controlled Component 규칙 검증

`components/` 또는 프로젝트의 컴포넌트 디렉토리에 변경이 있으면 다음을 확인합니다.

- 내부 state 사용 여부 (금지)
- Props가 Optional로 정의되어 있는지
- 합리적 기본값이 제공되어 있는지

데이터와 핸들러는 모두 props로 전달받아야 하며, 상태 소유권은 페이지/호출 측에 있어야 합니다. (페이지 컴포넌트는 예외 — `osom-react-page-builder` 참조)
