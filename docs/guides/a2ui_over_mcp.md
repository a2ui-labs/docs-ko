# Model Context Protocol(MCP) 위의 A2UI

이 가이드는 **Model Context Protocol(MCP)** 위에서 **A2UI** 선언형 문법을 사용해 풍부하고 상호작용적인 인터페이스를 구성하는 방법을 설명합니다.

## 카탈로그 협상

서버가 클라이언트에 A2UI를 보내기 전에, 양쪽은 프로토콜 지원 여부와 사용 가능한 카탈로그를 합의해야 합니다. 시스템 구조에 따라 이 협상은 초기 연결 핸드셰이크 때 한 번만 하거나, 각 메시지마다 수행할 수 있습니다.

### 옵션 A: MCP 초기화 시 카탈로그 핸드셰이크

MCP는 상태를 유지하는 세션 프로토콜이므로, 가장 효율적인 방식은 연결을 맺을 때 한 번만 기능을 선언하는 것입니다. 클라이언트는 표준 initialize 요청의 capabilities 객체 아래에 A2UI 지원 정보를 넣습니다. 서버는 이 상태를 세션 동안 기억합니다.

### 옵션 B: 각 MCP 메시지마다 카탈로그 핸드셰이크

MCP 서버가 완전히 상태 비저장이어야 한다면, 클라이언트는 각 도구 호출 요청의 `_meta` 필드에 A2UI 버전과 카탈로그 지원 정보를 전달할 수 있습니다. 서버는 이 메타데이터를 즉시 읽어 응답 UI에 사용할 카탈로그를 결정합니다.

## 임베디드 리소스로 A2UI 반환하기

임베디드 리소스(Embedded Resource)를 사용하면, 도구가 서버 측 저장이나 추적 없이도 해당 응답에 직접 연결된 UI 레이아웃을 반환할 수 있습니다.

- **URI**: `a2ui://` 접두사와 설명이 있는 식별자를 사용해야 합니다.
- **MIME Type**: `application/json+a2ui`를 사용해야 합니다. 이렇게 하면 MCP 클라이언트가 원시 JSON 대신 A2UI 렌더러로 페이로드를 보냅니다.

### Python 구현 예시

```python
import mcp.types as types

@self.tool()
def get_hello_world_ui():
    a2ui_payload = [
        {
            "version": "v0.10",
            "createSurface": {
                "surfaceId": "default",
                "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json"
            }
        },
        {
            "version": "v0.10",
            "updateComponents": {
                "surfaceId": "default",
                "components": [
                    {
                        "id": "root",
                        "component": "Text",
                        "text": "Hello World!"
                    }
                ]
            }
        }
    ]

    a2ui_resource = types.EmbeddedResource(
        type="resource",
        resource=types.TextResourceContents(
            uri="a2ui://training-plan-page",
            mimeType="application/json+a2ui",
            text=json.dumps(a2ui_payload),
        )
    )

    text_content = types.TextContent(
        type="text",
        text="Here is your generated training plan summary..."
    )

    return types.CallToolResult(content=[text_content, a2ui_resource])
```

## 사용자 액션 처리

`Button` 같은 대화형 컴포넌트는 `actions`를 서버로 보낼 수 있습니다.

### 1. 액션이 포함된 A2UI JSON

```json
{
  "id": "confirm-button",
  "component": {
    "Button": {
      "child": "confirm-button-text",
      "action": {
        "event": {
          "name": "confirm_booking",
          "context": {
            "start": "/dates/start",
            "end": "/dates/end"
          }
        }
      }
    }
  }
}
```

### 2. A2UI 액션 MCP 페이로드

버튼이 클릭되면 클라이언트는 표면 바인딩 상태를 기준으로 `/dates/start`나 `/dates/end` 같은 경로를 해석하고, 이를 MCP 도구 호출 인자로 변환합니다.

### 3. 액션 처리기 MCP 서버 도구

MCP 서버는 도구 호출을 받아 해당 핸들러를 실행합니다.

## 오류 처리

사용자 상호작용과 마찬가지로, MCP 서버는 클라이언트로부터 오류를 받을 수도 있습니다.

### 1. A2UI 오류 MCP 페이로드

클라이언트가 A2UI 페이로드에서 오류를 만나면, 오류 MCP 페이로드를 서버로 보낼 수 있습니다.

### 2. 오류 처리기 MCP 서버 도구

MCP 서버는 도구 호출을 받아 해당 핸들러를 실행합니다.

## 말하기와 가시성 제어

MCP **리소스 주석(Resource Annotations)** 을 사용하면, 이후의 assistant 턴이 백엔드 페이로드를 읽거나 해석할 수 있을지 제어할 수 있습니다.

## 다음 단계

MCP 위에서 A2UI를 활용하는 샘플과 구현은 저장소의 MCP 샘플을 참고하세요.
