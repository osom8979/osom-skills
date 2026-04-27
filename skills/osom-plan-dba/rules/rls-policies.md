# RLS 정책

다음을 분석합니다.

- 필요한 행 수준 보안 정책
- 영향받는 역할(`anon`, `authenticated`, `service_role`, 커스텀 역할)
- 기존 정책과의 충돌 여부

RLS는 **silent failure**를 일으킬 수 있으므로(UPDATE/DELETE는 0행만 영향, 에러 없음), 정책 누락 가능성이 큰 영역을 명시적으로 지적합니다. 검증은 `pgtap-best-practices`의 RLS 패턴을 따릅니다.
