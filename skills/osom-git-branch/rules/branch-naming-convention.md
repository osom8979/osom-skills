# 브랜치 이름 규칙

새 브랜치를 생성할 때는 다음 prefix 컨벤션을 따릅니다.

| Prefix                  | 용도                       |
| ----------------------- | -------------------------- |
| `feature/<description>` | 새 기능                    |
| `fix/<description>`     | 버그 수정                  |
| `hotfix/<description>`  | 긴급 수정 (운영 환경 대응) |
| `release/<version>`     | 릴리즈 준비                |
| `chore/<description>`   | 기타 작업 (의존성 등)      |

`<description>`은 kebab-case 영문으로 작성하고, 이슈 번호가 있으면 `feature/123-add-login` 형태로 포함합니다.
