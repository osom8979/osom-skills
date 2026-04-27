# Guardrails 기본값

- **래퍼 존재 시**: `Use ./npm, ./npx, ./node wrappers` 항목 포함.
- **기본 가드**: `Never push; commits only` 포함.
- 사용자에게 **추가 가드**(예: "production DB 변경 금지", "특정 파일 수정 금지")가 있는지 물어봅니다.

Guardrails는 다른 스킬이 강제로 준수해야 하는 제약입니다. 안전 관련 항목(`Never push`, 시크릿 파일 보호 등)은 기본 포함을 권장합니다.
