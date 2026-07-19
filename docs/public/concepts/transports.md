# 전송 계층 (메시지 전달)

전송 계층은 에이전트가 생성한 A2UI 메시지를 클라이언트로 전달합니다. A2UI는 전송 방식에 종속되지 않으므로 JSON을 보낼 수 있는 어떤 방식도 사용할 수 있습니다.

실제 컴포넌트 렌더링은 [렌더러](../reference/renderers.md)가 담당하고, [에이전트](../reference/agents.md)는 A2UI 메시지를 생성합니다. 에이전트에서 클라이언트로 메시지를 전달하는 역할이 전송 계층입니다.

## 동작 방식

```text
에이전트 → 전송 계층 → 클라이언트 렌더러
```

A2UI는 JSON 메시지 시퀀스를 정의합니다. 전송 계층은 이 시퀀스를 에이전트에서 클라이언트로 전달합니다. 보통은 JSON Lines(JSONL) 같은 스트림 형식이 사용되며, 각 줄이 하나의 A2UI 메시지입니다.

## 사용 가능한 전송 방식

| 전송 방식 | 상태 | 활용 사례 |
|-----------|------|----------|
| **A2A 프로토콜** | ✅ 안정판 | 멀티 에이전트 시스템, 엔터프라이즈 메시 |
| **AG-UI** | ✅ 안정판 | 풀스택 React, Vue, Angular 애플리케이션 (CopilotKit) |
| **REST API** | 📋 계획됨 | 단순 HTTP 엔드포인트 |
| **WebSockets** | 💡 제안됨 | 실시간 양방향 통신 |
| **SSE (Server-Sent Events)** | 💡 제안됨 | 웹 스트리밍 |

## A2A 프로토콜

[Agent2Agent(A2A) 프로토콜](https://a2a-protocol.org)은 안전하고 표준화된 에이전트 간 통신을 제공합니다. A2UI 확장을 통해 A2UI와 쉽게 통합할 수 있습니다.

장점:

- 보안 및 인증 내장
- 다양한 메시지 형식과 인증/전송 프로토콜에 대한 바인딩 지원
- 관심사의 명확한 분리

### AG-UI

[AG-UI](https://ag-ui.com/)는 A2UI 메시지를 AG-UI 이벤트로 변환하고, 전송과 상태 동기화를 자동으로 처리합니다. 풀스택 React, Vue, Angular 애플리케이션에서 널리 사용되며, CopilotKit이 AG-UI를 만들었고 가장 주요한 사용처이기도 합니다.

## 커스텀 전송 방식

JSON을 전송할 수 있는 방식이라면 무엇이든 사용할 수 있습니다.

```javascript
// HTTP/REST 예시를 여기에 추가할 수 있습니다.
```

```javascript
// WebSocket 예시를 여기에 추가할 수 있습니다.
```

```javascript
// SSE 예시를 여기에 추가할 수 있습니다.
```

## 다음 단계

- [A2A 프로토콜 문서](https://a2a-protocol.org)
- [A2A 확장 명세](../specification/v0.8-a2a-extension.md)
