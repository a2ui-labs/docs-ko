# 사용자 액션 처리

이 가이드는 A2UI가 사용자 상호작용을 처리하는 방식을 설명합니다. 컴포넌트는 `action` 속성을 사용해 로컬 **Function**(렌더러에서 실행) 또는 **Event**(에이전트로 dispatch)를 트리거합니다. 또한 **Data Model Synchronization**은 에이전트가 항상 전체 UI 상태에 접근할 수 있게 하여 음성 명령 같은 매끄러운 멀티모달 상호작용을 가능하게 합니다. 이 설계는 안전하고 제한된 환경을 유지하면서도 매우 반응성이 높은 인터페이스를 가능하게 합니다.

## Action 아키텍처

Action은 UI 컴포넌트가 `common_types.json`의 [`Action`](../../specification/v0_9/json/common_types.json#L271-L313) 스키마에 정의된 동작을 트리거할 수 있게 합니다. Action은 다음을 트리거할 수 있습니다.

1.  **Event**: 처리를 위해 Agent로 dispatch됩니다(Agent에서 실행, 예: "Submit" 클릭).
2.  **Function**: [`FunctionCall`](../../specification/v0_9/json/common_types.json#L200-L242)을 사용해 렌더러에서 완전히 실행됩니다(Renderer에서 실행, 예: URL 열기).

### 1. Function(로컬)

Function은 네트워크 왕복 없이 렌더러에서 즉시 동작을 실행합니다. 에이전트는 로컬 함수 호출을 알지 못합니다. Function은 `functionCall` 키워드를 사용합니다.

```json
{
  "id": "help-btn",
  "component": "Button",
  "child": "help-text",
  "action": {
    "functionCall": {
      "call": "openUrl",
      "args": {"url": "https://a2ui.org/help"}
    }
  }
}
```

Function의 일반적인 사용 사례는 다음과 같습니다.

- **Navigation**: URL을 열거나 탭을 전환합니다.
- **Validation**: 제출 전에 입력을 확인합니다(아래 Checks 참고).

### 2. Event(에이전트)

Event는 처리를 위해 데이터를 에이전트로 보냅니다. Event는 `event` 키워드를 사용합니다.

`Button` 같은 컴포넌트는 `action` 속성을 노출합니다. Event를 연결하는 방식은 다음과 같습니다.

```json
{
  "id": "submit-btn",
  "component": "Button",
  "child": "btn-text",
  "action": {
    "event": {
      "name": "submit_reservation",
      "context": {
        "time": {"path": "/reservationTime"},
        "size": {"path": "/partySize"}
      }
    }
  }
}
```

- **`name`**: 에이전트가 분기 처리할 수 있는 안정적인 식별자입니다.
- **`context`**: key-value 쌍의 맵입니다. 값은 literal일 수도 있고, 데이터 모델의 현재 상태에서 값을 가져오기 위해 `path`를 사용할 수도 있습니다.

NOTE: **Context와 Data Model**: Data Model은 surface의 전체 상태 트리를 나타내지만, action의 `context`는 사실상 그 상태 중 직접 고른 **"view"** 또는 부분집합입니다. Agent가 잠재적으로 크고 복잡한 데이터 모델을 탐색하지 않아도 특정 event에 필요한 값만 정확히 받을 수 있어 Agent의 작업이 단순해집니다.

### Basic Catalog 함수 검증(Checks)

basic catalog는 렌더러에서 수행할 수 있는 제한된 check 집합을 정의합니다. 인터랙티브 컴포넌트는 `common_types.json`의 [`Checkable`](../../specification/v0_9/json/common_types.json#L258-L270) 스키마를 사용해 `checks` 목록을 정의할 수 있습니다. `Button`의 경우 check가 하나라도 실패하면 렌더러에서 버튼이 **자동으로 비활성화**됩니다.

- **UX 중심**: 검증 check는 잘못된 상호작용이 발생하기 전에 막아 **UI 상태(사용자 경험)** 를 관리하도록 설계되었습니다. 하지만 이는 여전히 에이전트에서 수행해야 하는 **데이터 무결성** check를 대체하지 않습니다.

이를 통해 사용자가 제출을 시도하기 전에도 UI가 필수 필드 같은 요구사항을 강제할 수 있습니다.

```json
{
  "id": "submit-button",
  "component": "Button",
  "child": "submit-text",
  "checks": [
    {
      "condition": {
        "call": "required",
        "args": {"value": {"path": "/partySize"}}
      },
      "message": "Party size is required"
    }
  ],
  "action": {"event": {"name": "submit_booking"}}
}
```

## 로컬 상태 업데이트와 "Write" 계약

Event가 dispatch되기 전에도 렌더러는 이미 UI 상태를 로컬에서 관리하고 있습니다. A2UI는 `TextField`, `CheckBox`, `Slider` 같은 모든 입력 컴포넌트에 대해 **Read/Write Contract**를 정의합니다.

1.  **Read (Model → View)**: 컴포넌트가 렌더링될 때 Data Model의 바인딩된 `path`에서 값을 가져옵니다.
2.  **Write (View → Model)**: 사용자가 상호작용하는 즉시(예: 문자 입력 또는 체크박스 클릭), 렌더러는 새 값을 로컬 Data Model에 **즉시** 씁니다.

즉 로컬 모델은 UI의 현재 상태에 대한 **항상** source of truth입니다. 이 "View-to-Model" 동기화는 순수하게 렌더러에서 발생합니다. 데이터 모델은 버튼 클릭 같은 event가 발생할 때만 에이전트로 전송됩니다.

IMPORTANT: **동기 업데이트**: 로컬 모델 업데이트는 **동기적**입니다. 따라서 Event가 `context` 경로를 해석하거나 `DataModelSync` payload를 패키징하기 전에 Data Model이 완전히 업데이트되어 있음을 보장합니다. 입력과 클릭 사이에 race condition은 없습니다. "Write"가 항상 먼저 commit됩니다.

이 local-first 접근 방식은 상당한 **성능 이점**을 제공합니다. 동기화가 즉시 로컬에서 이루어지기 때문에 개발자는 사용자가 `TextField`에 입력하는 동안 네트워크 debounce를 구현하거나 지연 jitter를 걱정할 필요가 없습니다. 사용자가 공식 Event를 dispatch할 준비가 될 때까지 네트워크는 개별 키 입력 같은 "UI noise"로부터 완전히 보호됩니다.

### 폼 제출 패턴

이 분리는 견고한 폼 제출 패턴을 가능하게 합니다.

- **Binding**: `TextField`가 `/reservationTime`에 바인딩됩니다.
- **Interaction**: 사용자가 "7:00 PM"을 입력합니다. `/reservationTime`의 로컬 모델이 즉시 업데이트됩니다.
- **Submission**: 사용자가 "Book" 버튼을 클릭합니다. 버튼의 Event는 로컬 모델에서 `path: "/reservationTime"`을 해석하고 현재 값을 에이전트로 보냅니다.

## 사용자 상호작용 흐름

사용자가 컴포넌트와 상호작용할 때(예: 버튼 클릭) 다음이 발생합니다.

1.  **Resolve**: 렌더러가 `context` 안의 모든 `path` 참조를 로컬 **Data Model**에 대해 해석합니다.
2.  **Construct**: 렌더러가 [`client_to_server.json`](../../specification/v0_9/json/client_to_server.json)에 맞는 `action` payload를 구성합니다.
3.  **Dispatch**: 선택한 전송 계층(예: A2A, WebSockets)을 통해 payload가 전송됩니다.

### 예시: Action Payload(v0.9)

위 버튼을 사용자가 클릭했고 데이터 모델에 `{"reservationTime": "7:00 PM", "partySize": 4}`가 들어 있다면, 렌더러는 `action` 키를 사용해 다음 메시지를 보냅니다.

```json
{
  "version": "v0.9",
  "action": {
    "name": "submit_reservation",
    "surfaceId": "booking-surface",
    "sourceComponentId": "submit-btn",
    "timestamp": "2026-02-25T10:40:00Z",
    "context": {
      "time": "7:00 PM",
      "size": 4
    }
  }
}
```

IMPORTANT: **버전 관리 참고(v0.8 vs v0.9)**: v0.8에서는 top-level payload 키가 `userAction`이었습니다(예: `{"userAction": {...}}`). v0.9에서는 위에 표시된 더 단순한 `action` 키로 전환되었습니다. 표준 프로토콜 parser는 payload에 선언된 버전에 대응하는 키를 기대합니다.

## 에이전트 처리

Agent(또는 Orchestrator)는 이 event를 받아 처리합니다. 에이전트 시스템에서는 보통 에이전트가 event를 LLM을 위한 숨겨진 사용자 query로 변환합니다.

**에이전트 처리 예시(Python):**

```python
if action_name == "submit_reservation":
    time = context.get("time")
    size = context.get("size")
    # Feed this to the LLM
    query = f"User submitted a reservation for {size} people at {time}."
    response = await llm.generate(query)
```

## 렌더러에서 에이전트로 오류 보고

사용자가 트리거한 Event 외에도 렌더러는 [`client_to_server.json`](../../specification/v0_9/json/client_to_server.json)에 정의된 `error` payload를 사용해 시스템 수준 오류를 에이전트로 보고할 수 있습니다.

### 검증 실패

에이전트가 보낸 A2UI JSON이 카탈로그 스키마 또는 프로토콜 규칙을 위반하면 렌더러는 `VALIDATION_FAILED` 오류를 보냅니다. 이는 에이전트 시스템에 중요한 feedback loop입니다.

```json
{
  "version": "v0.9",
  "error": {
    "code": "VALIDATION_FAILED",
    "surfaceId": "booking-surface",
    "path": "/components/0/children",
    "message": "Expected array of strings, got null."
  }
}
```

에이전트는 이 오류를 받아 사과하거나(또는 내부적으로 self-correct한 뒤) 수정된 UI를 다시 보낼 수 있습니다.

## Data Model Sync(v0.9)

A2UI v0.9는 강력한 "stateless" 동기화 기능을 도입했습니다. 이 기능을 사용하면 렌더러가 에이전트로 보내는 모든 메시지의 metadata에 surface의 **전체 데이터 모델**을 자동으로 포함할 수 있습니다.

### Sync 활성화

동기화는 surface 초기화 중 에이전트가 요청합니다. `createSurface` 메시지에서 `sendDataModel: true`를 설정하면 에이전트는 렌더러에 sync loop를 시작하도록 지시합니다.

```json
{
  "version": "v0.9",
  "createSurface": {
    "surfaceId": "booking-surface",
    "catalogId": "https://a2ui.org/catalogs/v1/basic.json",
    "sendDataModel": true
  }
}
```

### Wire상의 Sync

sync가 활성화되면 렌더러는 데이터 모델을 별도 메시지로 보내지 않습니다. 대신 나가는 transport envelope(예: A2A 메시지)에 **metadata**로 첨부합니다.

A2A(Agent-to-Agent) binding에서는 데이터 모델이 envelope의 `metadata` 필드 안의 `a2uiClientDataModel` 객체에 배치됩니다.

**Sync가 포함된 A2A Envelope 예시:**

```json
{
  "parts": [{"text": "Submit the reservation"}],
  "metadata": {
    "a2uiClientDataModel": {
      "version": "v0.9",
      "surfaces": {
        "booking-surface": {
          "reservationTime": "7:00 PM",
          "partySize": 4,
          "notes": "Window seat preferred"
        }
      }
    }
  }
}
```

### Data Model Sync를 사용하는 이유

- **더 단순한 연결**: 모든 입력 필드를 버튼의 `context` 속성에 수동으로 매핑할 필요가 없습니다. 에이전트는 metadata를 확인해 모든 필드의 현재 상태를 볼 수 있습니다.
- **Stateless Agents**: 에이전트가 각 사용자 세션의 로컬 상태를 유지할 필요가 없습니다. 모든 상호작용마다 전체 현재 context를 받습니다.
- **음성 단축 명령**: 사용자가 특정 버튼을 클릭하지 않아도 음성이나 텍스트(예: "okay submit")로 Event를 트리거할 수 있습니다. 에이전트는 텍스트 메시지와 함께 업데이트된 데이터 모델을 받기 때문에 요청을 즉시 처리할 수 있습니다.

## 렌더러 Metadata와 Capabilities

에이전트가 안전하게 UI를 보내려면 먼저 렌더러가 어떤 컴포넌트 카탈로그를 지원하는지 광고해야 합니다. 이는 `a2uiClientCapabilities` 객체를 통해 처리됩니다.

### Capabilities 광고

렌더러는 에이전트로 보내는 메시지의 **metadata**에 `a2uiClientCapabilities` 객체를 포함합니다(예: A2A envelope의 `metadata` 필드).

```json
{
  "v0.9": {
    "supportedCatalogIds": [
      "https://a2ui.org/specification/v0_9/catalogs/basic/catalog.json",
      "https://my-company.com/catalogs/v1/custom.json"
    ],
    "inlineCatalogs": []
  }
}
```

- **`supportedCatalogIds`**: 렌더러가 렌더링할 수 있는 catalog URI 배열입니다.
- **`inlineCatalogs`**: (선택) 개발 또는 특수 환경에서 전체 catalog schema를 inline으로 보낼 수 있게 합니다.

이 handshake가 없으면 에이전트는 보낸 특정 컴포넌트를 렌더러가 처리할 수 있는지 확신할 수 없습니다.

## Transport와 Encoding

A2UI는 transport-agnostic이지만, 가장 흔히 **A2A(Agent-to-Agent)** 또는 WebSockets 위에서 사용됩니다. 구현을 위해서는 payload가 어떻게 감싸지는지 이해하는 것이 중요합니다.

### A2A Encoding

표준 A2A binding에서 A2UI 메시지는 A2A **DataPart**로 인코딩됩니다. A2UI payload임을 식별하려면 part를 특정 metadata로 감싸야 합니다.

- **mimeType**: `application/json+a2ui`

`DataPart`의 `data` 필드는 A2UI 메시지의 **list**를 포함합니다. 이를 통해 여러 업데이트(예: `createSurface` 다음 `updateComponents`)를 하나의 네트워크 패킷으로 보낼 수 있습니다.

NOTE: **A2A 버전 관리**: `data` 필드에서 **list**를 사용하는 방식은 **A2A v1.0**에서 도입되었습니다. 이전 A2A 프로토콜 버전은 `data` 필드에 단일 JSON 객체가 들어갈 것으로 기대합니다.

```json
{
  "kind": "data",
  "metadata": {
    "mimeType": "application/json+a2ui"
  },
  "data": [
    {
      "version": "v0.9",
      "action": { ... }
    }
  ]
}
```

## 보안 고려사항

A2UI는 안전하고 sandboxed된 통신을 핵심 원칙으로 설계되었습니다. 프로토콜이 사용자 상태와 상호작용 trigger를 네트워크로 전달하는 데 의존하므로, 데이터 가시성과 실행에 엄격한 경계를 적용합니다.

### Sandboxed 실행

A2UI의 핵심 장점은 제한을 통한 보안입니다. 에이전트가 임의 코드 실행(raw JavaScript 주입 등)을 할 수 없도록 금지함으로써, A2UI는 에이전트가 미리 등록된 동작만 트리거할 수 있게 합니다. `functionCall` 메커니즘은 악성 스크립트에 사용자를 노출하지 않으면서 에이전트가 렌더러 환경과 상호작용할 수 있는 안전한 sandboxed 방식으로 동작합니다.

### Data Model 격리와 Orchestrator Routing

`sendDataModel: true`가 활성화되면 렌더러는 surface의 전체 데이터 모델을 나가는 메시지에 포함합니다. 개발자는 이 데이터의 가시성을 이해해야 합니다.

- **지점 간 가시성**: 이 payload는 transport envelope를 받는 백엔드, 즉 surface를 만든 Agent 또는 중간 Orchestrator만 읽을 수 있습니다.
- **Orchestrator의 책임**: multi-agent 아키텍처에서 중앙 Orchestrator는 보통 사용자 의도를 전문 sub-agent로 라우팅합니다. Orchestrator는 **데이터 격리**를 강제해야 합니다. `a2uiClientDataModel`을 파싱하고 `surfaceId`를 식별한 뒤, 해당 surface를 소유한 특정 sub-agent에만 데이터 모델을 전달할 책임이 있습니다. 한 에이전트 surface의 데이터가 다른 에이전트로 누출되어서는 안 됩니다.

## Orchestration과 Routing

multi-agent 시스템에서 중앙 **Orchestrator**는 보통 사용자와 여러 전문 sub-agent 사이의 상호작용을 관리합니다. 핵심 과제는 렌더러에서 온 `action` 메시지가 UI surface를 생성한 특정 sub-agent로 다시 라우팅되도록 보장하는 것입니다.

### Surface Ownership 패턴

이를 처리하려면 orchestrator가 `surfaceId`와 소유 sub-agent의 매핑을 유지해야 합니다. 이 매핑은 일반적으로 **Session State**에 저장됩니다.

#### 1. Ownership 매핑

sub-agent가 `createSurface` 메시지를 내보내면 orchestrator가 이를 가로채 ownership을 기록합니다.

```python
# Simplified Orchestrator Logic: Record Ownership
def on_surface_created(surface_id, agent_name, session):
    # Store the mapping in the orchestrator's session state
    session.state.update({f"owner_of_{surface_id}": agent_name})
```

#### 2. Event 라우팅

렌더러가 orchestrator로 `action`을 보내면 orchestrator는 `surfaceId`를 조회하고 요청을 올바른 sub-agent로 전달합니다.

```python
# Simplified Orchestrator Logic: Route Event
async def handle_incoming_action(payload, session):
    action = payload.get("action")
    surface_id = action.get("surfaceId")

    # Lookup the owning agent
    target_agent = session.state.get(f"owner_of_{surface_id}")

    if target_agent:
        # Programmatically route the request to the sub-agent
        return transfer_to(target_agent)
```

이 패턴은 복잡한 multi-agent 환경에서도 양방향 통신 루프가 각 기능 영역별로 온전하고 stateful하게 유지되도록 합니다.

### Metadata Stripping으로 데이터 누출 방지

multi-agent 환경에서 `a2uiClientDataModel`에는 서로 다른 에이전트가 소유한 여러 surface의 상태가 포함될 수 있습니다. 민감한 데이터 누출을 방지하려면 orchestrator가 **strip** 작업을 수행해 호출 대상 sub-agent가 소유한 surface만 데이터 모델 metadata에 포함되도록 해야 합니다.

이는 outbound metadata interceptor에서 구현하는 것이 가장 좋습니다.

```python
# Simplified Orchestrator Interceptor: Strip Data Model
async def intercept(self, request_payload, target_agent, session):
    message = request_payload["params"]["message"]
    data_model = message.get("metadata", {}).get("a2uiClientDataModel")

    if data_model:
        # Filter surfaces to only those owned by the target_agent
        filtered_surfaces = {
            surface_id: state for surface_id, state in data_model["surfaces"].items()
            if session.state.get(f"owner_of_{surface_id}") == target_agent.name
        }

        # Replace with the stripped data model
        message["metadata"]["a2uiClientDataModel"]["surfaces"] = filtered_surfaces

    return request_payload
```

metadata를 strip함으로써 orchestrator는 sub-agent가 볼 권한이 있는 데이터 모델 부분만 받도록 보장합니다.

CAUTION: **보안 위험: 상태 스크래핑**: Orchestrator가 `a2uiClientDataModel`을 strip하지 않으면, 악성 또는 침해된 sub-agent가 다른 활성 surface의 상태를 "scrape"할 수 있습니다. 예를 들어 orchestrator가 전체 multi-surface 데이터 모델을 누출하면 날씨 sub-agent가 은행 surface의 민감한 데이터를 읽을 수 있습니다. multi-agent 시스템에서 stripping은 필수 보안 요구사항입니다.

---

## 종합 예시

### 1. 버튼 제출(명시적 Context)

이 예시는 전송할 데이터를 명시적으로 수집하는 버튼을 보여 줍니다.

**컴포넌트 정의:**

```json
{
  "id": "submit-button",
  "component": "Button",
  "child": "submit-text",
  "action": {
    "event": {
      "name": "submit_booking",
      "context": {
        "partySize": {"path": "/partySize"},
        "reservationTime": {"path": "/reservationTime"}
      }
    }
  }
}
```

**결과 Action Payload:**
에이전트는 `context` 필드에 `partySize`와 `reservationTime`이 직접 들어 있는 `action` 객체를 받습니다.

### 2. 음성 제출(Data Model Sync)

이 시나리오에서 사용자는 버튼을 클릭하지 않습니다. 대신 "Okay, submit the form."이라고 말합니다.

**초기화:**
에이전트는 `sendDataModel: true`로 surface를 만들었습니다.

```json
{
  "version": "v0.9",
  "createSurface": {
    "surfaceId": "booking-surface",
    "catalogId": "...",
    "sendDataModel": true
  }
}
```

**렌더러 전송:**
렌더러는 사용자의 텍스트와 데이터 모델을 metadata에 포함한 A2A 메시지를 보냅니다.

```json
{
  "parts": [{"text": "Okay, submit the form"}],
  "metadata": {
    "a2uiClientDataModel": {
      "version": "v0.9",
      "surfaces": {
        "booking-surface": {
          "partySize": 4,
          "reservationTime": "7:00 PM"
        }
      }
    }
  }
}
```

**에이전트 처리:**
에이전트는 사용자의 의도("submit")를 보고 `metadata`에서 `partySize`와 `reservationTime`의 현재 값을 찾아, 추가 확인 없이 작업을 완료할 수 있습니다.
