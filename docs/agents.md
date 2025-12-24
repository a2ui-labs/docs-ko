# 에이전트 (서버 측)

에이전트는 사용자의 요청에 응답하여 A2UI 메시지를 생성하는 서버 측 프로그램입니다.

메시지가 클라이언트로 [전송(transported)](transports.md)된 후, 실제 컴포넌트 렌더링은 [렌더러(renderer)](renderers.md)에서 수행됩니다.
에이전트는 오직 A2UI 메시지를 생성하는 역할만 담당합니다.

## 에이전트 동작 방식

```
사용자 입력 → 에이전트 로직 → LLM → A2UI JSON → 클라이언트로 전송
```

1. 사용자 메시지 **수신**
2. LLM(Gemini, GPT, Claude 등)을 통한 **처리**
3. 구조화된 출력으로 A2UI JSON 메시지 **생성**
4. 전송 계층을 통해 클라이언트로 **전송**

클라이언트에서의 사용자 상호작용은 새로운 사용자 입력으로 처리될 수 있습니다.

## 에이전트 샘플

A2UI 저장소에는 참고할 수 있는 샘플 에이전트들이 포함되어 있습니다:

- [Restaurant Finder](https://github.com/google/A2UI/tree/main/samples/agent/adk/restaurant_finder) 
    - 양식을 통한 테이블 예약
    - ADK를 사용하여 작성됨
- [Contact Lookup](https://github.com/google/A2UI/tree/main/samples/agent/adk/contact_lookup) 
    - 결과 목록 검색
    - ADK를 사용하여 작성됨
- [Rizzcharts](https://github.com/google/A2UI/tree/main/samples/agent/adk/rizzcharts) 
    - A2UI 커스텀 컴포넌트 데모
    - ADK를 사용하여 작성됨

## A2A와 함께 사용되는 에이전트 유형

### 1. 사용자 대면 에이전트 (독립형)

사용자 대면 에이전트는 사용자가 직접 상호작용하는 에이전트입니다.

### 2. 원격 에이전트의 호스트 역할을 하는 사용자 대면 에이전트

사용자 대면 에이전트가 하나 이상의 원격 에이전트에 대한 호스트 역할을 하는 패턴입니다. 사용자 대면 에이전트가 원격 에이전트를 호출하면, 원격 에이전트가 A2UI 메시지를 생성합니다. 이는 클라이언트 에이전트가 서버 에이전트를 호출하는 [A2A](https://a2a-protocol.org) 프로토콜에서 흔히 볼 수 있는 패턴입니다.

- 사용자 대면 에이전트는 A2UI 메시지를 수정 없이 "그대로 전달(passthrough)"할 수 있습니다.
- 사용자 대면 에이전트는 메시지를 클라이언트에 보내기 전에 A2UI 메시지를 수정할 수 있습니다.

### 3. 원격 에이전트

원격 에이전트는 사용자 대면 UI의 직접적인 일부가 아닙니다. 대신 원격 에이전트로 등록되어 사용자 대면 에이전트에 의해 호출될 수 있습니다. 이는 클라이언트 에이전트가 서버 에이전트를 호출하는 [A2A](https://a2a-protocol.org) 프로토콜에서 흔히 볼 수 있는 패턴입니다.
