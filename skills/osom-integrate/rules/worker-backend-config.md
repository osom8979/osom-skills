# Worker / 백엔드 설정

API 변경이 있으면 다음을 확인하고 누락을 직접 보완합니다.

- Worker 진입점에 라우트 등록 (`app.route('/api/...', resource)`)
- `env.d.ts`에 새 환경 변수 타입 추가
- `wrangler.json` / `wrangler.toml`에 새 바인딩 추가

바인딩 변경이 배포 환경에 영향을 주는 경우(시크릿 추가 등) 결과 보고에 명시하여 사용자가 별도로 적용할 수 있도록 합니다.
