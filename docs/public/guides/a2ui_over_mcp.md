# Model Context Protocol(MCP) 위의 A2UI

이 가이드는 **Tools**와 **Embedded Resources**를 사용해 **MCP 서버**에서 **풍부하고 상호작용적인 A2UI 인터페이스**를 제공하는 방법을 보여 줍니다. 끝까지 따라 하면 어떤 MCP 호환 클라이언트에도 A2UI 컴포넌트를 반환하는 동작하는 MCP 서버를 갖게 됩니다.

<video width="100%" height="auto" controls playsinline style="display: block; aspect-ratio: 16/9; object-fit: cover; border-radius: 8px; margin-bottom: 24px;">
  <source src="https://raw.githubusercontent.com/a2ui-project/a2ui/main/docs/public/assets/guides-a2ui-over-mcp-tour.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

## 사전 요구사항

시작하기 전에 다음이 설치되어 있는지 확인하세요.

- **Python** (3.10 버전 이상)
- 빠른 Python 패키지 관리를 위한 **[uv](https://docs.astral.sh/uv/)**
- MCP Inspector를 위한 **Node.js** (18 버전 이상)

## Quick Start: 샘플 실행

프로토콜의 세부 사항을 다루기 전에, 먼저 동작하는 예제를 실행해 보겠습니다. A2UI 저장소에는 바로 사용할 수 있는 MCP 레시피 데모가 포함되어 있습니다.

```bash
# Clone the repo (if you haven't already)
git clone https://github.com/a2ui-project/a2ui.git
cd a2ui/samples/mcp/a2ui-over-mcp-recipe

# Start the MCP server (SSE transport on port 8000)
uv run .
```

### 옵션 A: MCP Inspector를 통한 상호작용

별도 터미널에서 [MCP Inspector](https://github.com/modelcontextprotocol/inspector)를 실행해 서버와 상호작용합니다.

```bash
npx @modelcontextprotocol/inspector
```

Inspector에서:

1. **Transport Type**을 `SSE`로 설정합니다.
2. `http://localhost:8000/sse`에 연결합니다.
3. **List Resources**를 클릭하면 "Recipe Form" 리소스가 보입니다.
4. `a2ui://recipe-form` 리소스를 읽으면, 리소스 내용이 간단한 폼을 렌더링하는 A2UI JSON입니다.
5. **List Tools**를 클릭하면 `get_recipe_a2ui`가 보입니다.
6. 도구를 실행하면 응답에 레시피 카드를 렌더링하는 A2UI JSON이 포함됩니다.

> NOTE: 참고
>
> 이 샘플은 A2UI Agent SDK에 대한 로컬 경로 참조를 사용합니다. 여러분의 프로젝트에서는 PyPI에서 설치하세요.
>
> ```bash
> pip install a2ui-agent-sdk
> ```

### 옵션 B: Recipe Client 웹 앱 실행

A2UI over MCP를 시각적으로 보여 주는 완전히 렌더링된 인터랙티브 경험을 원한다면, 포함된 웹 애플리케이션을 실행하세요.

> [!NOTE]
> **패키지 매니저 사용:** A2UI 저장소 안에 내장된 샘플 애플리케이션을 실행하려면 Corepack workspace로 구성된 Yarn(`yarn install` / `yarn dev`)이 필요합니다. 이 저장소 밖에서 여러분만의 일반적인 용도나 독립 프로젝트에 사용할 때는 원하는 패키지 매니저(예: npm, pnpm)를 사용하세요.

1. 새 터미널 창에서 client 디렉터리로 이동합니다.
    ```bash
    cd client
    ```
2. Node.js 의존성을 설치합니다.
    ```bash
    yarn install
    ```
3. Vite 개발 서버를 시작합니다.
    ```bash
    yarn dev
    ```
4. 터미널에 표시된 URL(보통 `http://localhost:5173`)로 브라우저를 엽니다.

프리미엄하고 반응형인 2단 컬럼 인터페이스가 표시됩니다. 왼쪽 컬럼은 MCP Resource(`a2ui://recipe-form`)로부터 Selection Form을 렌더링합니다. 옵션을 선택하고 **"Get Recipe"**를 클릭하면 MCP Tool(`get_recipe_a2ui`)이 실행되어, 반환된 커스텀 A2UI 레시피 카드가 오른쪽 컬럼에 동적으로 렌더링됩니다.

![선택 폼과 동적 레시피 카드 생성을 보여주는 Dynamic Recipe Studio 데모, 왼쪽은 선택 폼, 오른쪽은 동적으로 생성되는 레시피 카드](../assets/recipe_sample.gif)

모든 샘플은 [`samples/community/mcp/`](https://github.com/a2ui-project/a2ui/tree/main/samples/community/mcp)에서 확인할 수 있습니다.

## 동작 방식

MCP 서버가 클라이언트에 A2UI 콘텐츠를 전달하는 방법은 크게 두 가지입니다.

1. **리소스 읽기(`resources/read`)를 통한 방법**: 클라이언트가 MCP 리소스를 직접 읽습니다(예: `a2ui://recipe-form`). 서버는 A2UI JSON payload를 직접 반환합니다.
2. **도구 호출(`tools/call`)을 통한 방법**: 클라이언트가 MCP 도구를 호출합니다(예: `get_recipe_a2ui`). 서버는 A2UI JSON payload를 도구 응답 안의 **Embedded Resource**로 감싸서 반환합니다.

두 경우 모두 클라이언트는 `application/a2ui+json` MIME 타입을 감지해 payload를 A2UI 렌더러로 전달합니다.

> [!IMPORTANT]
> **MIME 타입 일관성**
> 전달 경로(Resource로 직접 가져오든, Tool의 `CallToolResult` 안에서 반환되든)와 관계없이, A2UI JSON payload는 항상 `application/a2ui+json` MIME 타입으로 식별됩니다. Tool 응답에서는 payload가 이 MIME 타입을 가진 `EmbeddedResource`로 감싸져야 합니다. 이러한 일관된 식별 방식 덕분에 클라이언트 측 미들웨어가 정적 리소스와 동적 도구 응답 모두를 매끄럽게 가로채 A2UI로 라우팅할 수 있습니다.

### 1. 리소스 기반 전달 흐름 (`resources/read`)

```
Client → resources/read → MCP Server
                             ↓
                 Retrieve A2UI JSON
                             ↓
Client ← ResourceContents ← MCP Server
          (application/a2ui+json)
   ↓
A2UI Renderer displays UI
```

### 2. 도구 기반 전달 흐름 (`tools/call`)

```
Client → tools/call → MCP Server
                         ↓
              Generate A2UI JSON
                         ↓
         Wrap as EmbeddedResource
              (application/a2ui+json)
                         ↓
Client ← CallToolResult ← MCP Server
   ↓
A2UI Renderer displays UI
```

## Resources vs. Tools: 용도에 따른 구분

MCP 위에서 A2UI 통합을 설계할 때는 UI payload가 정적인지 동적인지에 따라 **Resources**와 **Tools** 중 하나를 선택해야 합니다.

### 1. MCP Resources를 통한 정적 UI (`resources/read`)

사용자 프롬프트 입력이나 대화 이력에 의존하지 않는 단순하고 정적인 사용자 인터페이스라면, A2UI를 MCP Resource로 직접 제공해야 합니다.

- **개념**: 클라이언트가 표준 리소스 URI(예: `a2ui://recipe-form`)를 사용해 미리 정의된 A2UI 리소스를 읽습니다.
- **사용 사례**: 정적인 설정 폼, 선택 화면, 설정 대시보드, 고정된 레이아웃에 이상적입니다.
- **장점**: 구현이 매우 단순하고 오버헤드가 낮으며, LLM/에이전트가 구조를 가져오기 위해 도구를 호출할 필요가 없습니다.

**Python 서버 예시:**

```python
@app.list_resources()
async def list_resources() -> list[types.Resource]:
    return [
        types.Resource(
            uri="a2ui://recipe-form",
            name="Recipe Form",
            mimeType="application/a2ui+json",
            description="Static form allowing users to pick options.",
        )
    ]

@app.read_resource()
async def read_resource(uri: str) -> list[ReadResourceContents]:
    if uri == "a2ui://recipe-form":
        return [
            ReadResourceContents(
                content=json.dumps(recipe_form_json),
                mime_type="application/a2ui+json",
            )
        ]
    raise ValueError(f"Unknown resource: {uri}")
```

### 2. MCP Tools를 통한 동적 UI (`tools/call`)

대화 맥락, 사용자 매개변수, 실시간 데이터에 따라 동적으로 생성되어야 하는 사용자 인터페이스라면, A2UI를 MCP Tool의 응답 안에 제공해야 합니다.

- **개념**: 클라이언트/에이전트가 특정 인자(예: 선택한 재료, 선호도)로 도구를 호출하면, 서버는 `CallToolResult` 안의 `EmbeddedResource`로 감싼 커스터마이즈된 A2UI JSON을 반환합니다.
- **사용 사례**: 실시간 데이터베이스 조회, 이전 입력값, 대화형 단계별 마법사 상태, 개인화된 추천(예: 커스터마이즈된 레시피 카드)에 의존하는 콘텐츠에 이상적입니다.
- **장점**: 유연성과 컨텍스트 인식을 극대화하며, 매우 동적인 흐름을 지원합니다.
- **베스트 프랙티스 (Fallback 텍스트)**: `CallToolResult` 안에 `EmbeddedResource`와 함께 항상 `TextContent`를 포함하세요. A2UI를 지원하지 않는 클라이언트는 이 텍스트를 사용자에게 대신 표시합니다.

**Python 서버 예시:**

```python
@app.call_tool()
async def handle_call_tool(name: str, arguments: dict[str, Any]) -> types.CallToolResult:
    if name == "get_recipe_a2ui":
        # Resolve dynamic selections from client parameters
        style = arguments.get("cookingStyle", "Baked")
        protein = arguments.get("protein", "Salmon")

        # Retrieve customized recipe database entry
        recipe_data = RECIPES.get((style, protein))

        # Customize base A2UI schema dynamically
        custom_recipe_json = copy.deepcopy(recipe_a2ui_json)
        custom_recipe_json[1]["updateComponents"]["components"][0]["text"] = recipe_data["title"]

        # Return customized recipe card as EmbeddedResource
        return types.CallToolResult(content=[
            types.EmbeddedResource(
                type="resource",
                resource=types.TextResourceContents(
                    uri="a2ui://recipe-card",
                    mimeType="application/a2ui+json",
                    text=json.dumps(custom_recipe_json),
                )
            )
        ])
```

## 카탈로그 협상

서버가 클라이언트에 A2UI를 보내기 전에, 양쪽은 어떤 카탈로그를 사용할 수 있는지 합의해야 합니다. 시스템 구조에 따라 이 협상은 두 가지 방식 중 하나로 이루어질 수 있습니다.

### 옵션 A: MCP 초기화 시점 (권장)

MCP는 상태를 유지하는 세션 프로토콜이므로, 가장 효율적인 방식은 연결을 맺을 때 한 번만 기능을 선언하는 것입니다. 클라이언트는 `capabilities` 아래에 A2UI 지원 정보를 선언합니다.

```json
{
  "jsonrpc": "2.0",
  "method": "initialize",
  "id": "init-123",
  "params": {
    "protocolVersion": "2025-11-25",
    "clientInfo": {
      "name": "a2ui-enabled-client",
      "version": "1.0.0"
    },
    "capabilities": {
      "a2ui": {
        "clientCapabilities": {
          "v0.9": {
            "supportedCatalogIds": [
              "https://a2ui.org/specification/v0_9/catalogs/basic/catalog.json"
            ]
          }
        }
      }
    }
  }
}
```

서버는 세션이 지속되는 동안 이 상태를 저장합니다.

### 옵션 B: 메시지별 메타데이터 (상태 비저장 서버용)

서버가 반드시 상태를 유지하지 않아야 한다면, 클라이언트는 모든 도구 호출의 `_meta` 필드에 A2UI 기능을 전달할 수 있습니다.

```json
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "id": "id-123",
  "params": {
    "name": "generate_report",
    "arguments": {"date": "2026-03-01"},
    "_meta": {
      "a2ui": {
        "clientCapabilities": {
          "v0.9": {
            "supportedCatalogIds": [
              "https://a2ui.org/specification/v0_9/catalogs/basic/catalog.json"
            ],
            "inlineCatalogs": []
          }
        }
      }
    }
  }
}
```

## 사용자 액션 처리

`Button` 같은 인터랙티브 컴포넌트는 MCP 도구 호출로 서버에 다시 전송되는 액션을 트리거할 수 있습니다.

### 1. 액션이 포함된 Button 정의

A2UI JSON에서 컴포넌트에 `action`을 추가합니다.

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

### 2. 클라이언트가 액션을 도구 호출로 전송

사용자가 버튼을 클릭하면, 클라이언트는 surface 상태를 기준으로 (`/dates/start` 같은) 데이터 바인딩을 해석하고 도구 호출을 전송합니다.

```json
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "id": "id-456",
  "params": {
    "name": "a2ui_action",
    "arguments": {
      "name": "confirm_booking",
      "context": {
        "start": "2026-03-20",
        "end": "2026-03-25"
      }
    }
  }
}
```

### 3. 서버에서 액션 처리

```python
@self.tool()
async def a2ui_action(name: str, context: dict) -> types.CallToolResult:
    """Handle A2UI user actions."""
    if name == "confirm_booking":
        # Process the booking, then return confirmation UI
        return types.CallToolResult(content=[
            types.TextContent(
                type="text",
                text=f"Booking confirmed: {context['start']} to {context['end']}"
            )
        ])
    raise ValueError(f"Unknown action: {name}")
```

## 오류 처리

클라이언트는 도구 호출을 통해 A2UI 렌더링 오류를 서버에 보고할 수 있습니다.

```json
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "id": "id-789",
  "params": {
    "name": "a2ui_error",
    "arguments": {
      "code": "INVALID_JSON",
      "message": "Failed to parse A2UI payload.",
      "surfaceId": "default"
    }
  }
}
```

서버에서는 다음과 같이 처리합니다.

```python
@self.tool()
async def a2ui_error(code: str, message: str, surfaceId: str = "") -> types.CallToolResult:
    """Handle A2UI client errors."""
    # Log the error, retry, or send a fallback UI
    return types.CallToolResult(content=[
        types.TextContent(
            type="text",
            text=f"Acknowledged error {code}: {message}"
        )
    ])
```

## 말하기와 가시성 제어

MCP **Resource Annotations**를 사용하면, 이후 턴에서 LLM이 A2UI payload를 "읽을" 수 있는지를 제어할 수 있습니다.

```python
a2ui_resource = types.EmbeddedResource(
    type="resource",
    resource=types.TextResourceContents(
        uri="a2ui://training-plan-page",
        mimeType="application/a2ui+json",
        text=json.dumps(a2ui_payload)
    ),
    # Show the UI to the user, but hide the raw JSON from the LLM
    annotations=types.Annotations(audience=["user"])
)
```

| Audience        | 동작                                               |
| --------------- | ------------------------------------------------------ |
| _(비어 있음)_   | 사용자와 LLM 모두에게 표시됩니다.                        |
| `["user"]`      | 사용자에게 렌더링되며, LLM 컨텍스트에서는 숨겨집니다.       |
| `["assistant"]` | LLM이 후속 추론에 사용할 수 있지만 렌더링되지는 않습니다. |

## A2UI Agent SDK 사용하기

프로덕션 환경에서는 **A2UI Agent SDK**가 스키마 관리, 검증, 프롬프트 생성을 대신 처리해 줍니다.

```bash
pip install a2ui-agent-sdk
```

```python
from a2ui.strategies.schema import A2uiSchemaManager
from a2ui.basic_catalog.provider import BasicCatalog

# Initialize the schema manager with the basic catalog
schema_manager = A2uiSchemaManager(
    catalogs=[BasicCatalog.get_config()],
)

# Validate A2UI output before sending
selected_catalog = schema_manager.get_selected_catalog()
selected_catalog.validator.validate(a2ui_payload)
```

스키마 관리, 동적 카탈로그, 스트리밍에 대한 자세한 내용은 [에이전트 개발 가이드](agent-development.md)를 참고하세요.

## 다음 단계

- [A2UI 명세](../specification/v0.9-a2ui.md) — 전체 프로토콜 레퍼런스
- [컴포넌트 갤러리](../reference/components.md) — 사용 가능한 컴포넌트 둘러보기
- [A2UI Surface 안의 MCP Apps](mcp-apps-in-a2ui.md) — HTML 기반 MCP 앱을 A2UI 안에 임베드하기
- [클라이언트 설정](client-setup.md) — A2UI를 표시하는 렌더러 구축하기
