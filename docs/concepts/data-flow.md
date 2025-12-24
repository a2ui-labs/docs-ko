# 데이터 흐름 (Data Flow)

에이전트에서 UI까지 메시지가 흐르는 방식에 대해 설명합니다.

## 아키텍처

```
에이전트 (LLM) → A2UI 생성기 → 전송 계층 (SSE/WS/A2A)
                                       ↓
클라이언트 (스트림 리더) → 메시지 파서 → 렌더러 → 네이티브 UI
```

![엔드-투-엔드 데이터 흐름](../assets/end-to-end-data-flow.png)

## 메시지 형식

A2UI는 UI를 설명하는 일련의 JSON 메시지 시퀀스를 정의합니다. 스트리밍 시 이러한 메시지는 종종 **JSON Lines (JSONL)** 형식으로 구성되며, 각 라인은 하나의 완전한 JSON 객체입니다.

```jsonl
{"surfaceUpdate":{"surfaceId":"main","components":[...]}}
{"dataModelUpdate":{"surfaceId":"main","contents":[{"key":"user","valueMap":[{"key":"name","valueString":"Alice"}]}]}}
{"beginRendering":{"surfaceId":"main","root":"root-component"}}
```

**왜 이 형식을 사용하나요?** 독립적인 JSON 객체 시퀀스는 스트리밍에 적합하고, LLM이 증분식으로 생성하기 쉬우며, 오류 발생 시에도 견고합니다.

## 생명주기 예시: 레스토랑 예약

**사용자:** "내일 오후 7시에 2명 예약해 줘"

**1. 에이전트가 UI 구조 정의:**

```json
{"surfaceUpdate": {"surfaceId": "booking", "components": [
  {"id": "root", "component": {"Column": {"children": {"explicitList": ["header", "guests-field", "submit-btn"]}}}},
  {"id": "header", "component": {"Text": {"text": {"literalString": "예약 확정"}, "usageHint": "h1"}}},
  {"id": "guests-field", "component": {"TextField": {"label": {"literalString": "인원 수"}, "text": {"path": "/reservation/guests"}}}},
  {"id": "submit-btn", "component": {"Button": {"child": "submit-text", "action": {"name": "confirm", "context": [{"key": "details", "value": {"path": "/reservation"}}]}}}}
]}}
```

**2. 에이전트가 데이터 채우기:**

```json
{"dataModelUpdate": {"surfaceId": "booking", "path": "/reservation", "contents": [
  {"key": "datetime", "valueString": "2025-12-16T19:00:00Z"},
  {"key": "guests", "valueString": "2"}
]}}
```

**3. 에이전트가 렌더링 신호 전송:**

```json
{"beginRendering": {"surfaceId": "booking", "root": "root"}}
```

**4. 사용자가 인원 수를 "3"으로 편집** → 클라이언트가 자동으로 `/reservation/guests`를 업데이트합니다 (아직 에이전트로 메시지를 보내지 않음).

**5. 사용자가 "확인" 클릭** → 클라이언트가 업데이트된 데이터와 함께 액션을 전송합니다:

```json
{"userAction": {"name": "confirm", "surfaceId": "booking", "context": {"details": {"datetime": "2025-12-16T19:00:00Z", "guests": "3"}}}}
```

**6. 에이전트 응답** → UI를 업데이트하거나, 정리를 위해 `{"deleteSurface": {"surfaceId": "booking"}}`를 보냅니다.

## 전송 옵션

- **A2A 프로토콜**: 멀티 에이전트 시스템 또는 에이전트와 UI 간 통신에 사용 가능
- **AG UI**: 양방향 실시간 통신
- ... 그 외

자세한 내용은 [전송 계층](../transports.md)을 참조하세요.

## 점진적 렌더링 (Progressive Rendering)

사용자에게 무엇인가를 보여주기 위해 전체 응답이 생성될 때까지 기다리는 대신, 응답의 일부가 생성되는 즉시 클라이언트로 스트리밍되어 점진적으로 렌더링될 수 있습니다.

사용자는 스피너를 보며 기다리는 대신 실시간으로 UI가 구축되는 과정을 보게 됩니다.

## 오류 처리

**잘못된 형식의 메시지:** 무시하고 다음 메시지를 처리하거나, 수정을 위해 에이전트에 오류를 보냅니다.
**네트워크 중단:** 오류 상태를 표시하고 재연결을 시도하며, 에이전트가 메시지를 재전송하거나 작업을 재개합니다.

## 성능

**배칭 (Batching):** 16ms 동안 업데이트를 버퍼링하여 한꺼번에 렌더링합니다.
**디핑 (Diffing):** 이전 컴포넌트와 새 컴포넌트를 비교하여 변경된 속성만 업데이트합니다.
**세밀한 업데이트:** 전체 모델이 아닌 `/user/name`과 같이 필요한 경로만 업데이트합니다.
