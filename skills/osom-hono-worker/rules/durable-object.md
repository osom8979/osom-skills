# Durable Object

기존 DO 파일(예: `agent-socket.ts`)의 패턴을 따릅니다.

- **Hibernation API 사용** (`ctx.acceptWebSocket`) — 메모리 비용을 최소화
- **태그로 엔티티 식별** — WebSocket 태깅으로 라우팅
- **`alarm()`으로 만료 처리** — 시간 기반 정리는 `setTimeout`이 아니라 alarm

DO는 단일 인스턴스 직렬화 모델이므로, 동시성 보장이 필요한 상태(채팅 룸, 카운터 등)에만 사용합니다. 단순 KV 저장은 KV namespace나 D1을 우선 고려합니다.
