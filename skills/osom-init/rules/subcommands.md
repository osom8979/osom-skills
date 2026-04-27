# 서브명령

사용자가 아래 형태로 부분 작업을 요청할 수 있습니다.

| 서브명령                                 | 동작                                                                              |
| ---------------------------------------- | --------------------------------------------------------------------------------- |
| `/osom-init`                             | 전체 초기화 (기존 파일이 있으면 모드 선택)                                        |
| `/osom-init detect`                      | 감지 결과만 출력, 파일은 쓰지 않음                                                |
| `/osom-init add-role <role>`             | `Enabled roles`에 역할 추가 + 필요한 Phase hint 자동 추가                         |
| `/osom-init remove-role <role>`          | `Enabled roles`에서 역할 제거                                                     |
| `/osom-init set-command <key> <command>` | `Commands` 섹션의 특정 키 값 변경 (예: `set-command Build/Check "npm run verify"`) |
| `/osom-init validate`                    | 현재 `.osom-skills`가 실제 프로젝트 상태와 일치하는지 검사                         |
