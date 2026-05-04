# 서브명령

사용자가 아래 형태로 부분 작업을 요청할 수 있습니다.

| 서브명령                                  | 동작                                                                               |
| ----------------------------------------- | ---------------------------------------------------------------------------------- |
| `/osom-init`                              | 전체 초기화 (6개 ROOT 파일을 모두 검사·생성·갱신, 기존 파일은 모드 선택)           |
| `/osom-init detect`                       | 감지 결과만 출력, 파일은 쓰지 않음                                                 |
| `/osom-init <FILE>`                       | 특정 파일만 처리 (예: `/osom-init DESIGN.md`, `/osom-init COMMANDS.md`)             |
| `/osom-init set-command <key> <command>`  | `COMMANDS.md`의 특정 섹션 값 변경 (예: `set-command Build/Check "npm run verify"`) |
| `/osom-init validate`                     | 6개 ROOT 파일이 실제 프로젝트 상태와 일치하는지 검사                               |

서브명령에서 다루지 않는 도메인 파일(`DESIGN.md`, `DATABASE.md`, `ARCHITECTURE.md`)의 본문 갱신은 `/osom-init <FILE>` 형태로 호출해 핵심 질문 흐름을 다시 진행합니다.
