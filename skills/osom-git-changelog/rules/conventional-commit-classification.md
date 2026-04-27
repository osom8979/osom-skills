# Conventional Commits 분류 규칙

CHANGELOG 항목은 Conventional Commits 형식의 타입을 다음과 같이 분류합니다.

| 분류             | 매핑되는 커밋 타입         |
| ---------------- | -------------------------- |
| **Added**        | `feat`                     |
| **Fixed**        | `fix`                      |
| **Changed**      | `refactor`, `style`        |
| **Deprecated**   | deprecation 관련 변경      |
| **Removed**      | 삭제된 기능                |
| **Security**     | 보안 관련 변경             |

분류되지 않는 커밋 타입(`chore`, `ci`, `build`, `docs`, `test` 등)은 CHANGELOG에 포함하지 않습니다(릴리즈 노트 가독성 우선). 단, 사용자가 명시 요청 시 `Internal` 같은 별도 섹션으로 추가할 수 있습니다.
