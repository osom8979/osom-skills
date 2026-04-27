# 허용·금지 명령어

이 스킬은 **`git add`로 스테이징된 변경 사항만** 커밋합니다. 스테이징되지 않은 파일은 무시합니다.

## 허용된 명령어

- `git diff --cached` — 스테이징된 변경 사항 보기
- `git log --oneline -10` — 최근 커밋 히스토리 보기
- `git commit -m "..."` — 커밋 생성
- `git log -1` — 직전 커밋 메시지 확인 (영문 검증용)
- `git commit --amend` — 영문 외 메시지가 검출된 경우에 한해 수정

## 금지된 명령어

- `git add *`, `git add .`, `git add -A` — 파일 스테이징은 사용자 책임
- `git push` 계열 — push는 절대 자동 수행하지 않음
