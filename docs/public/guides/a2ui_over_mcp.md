# Model Context Protocol(MCP) 위의 A2UI

이 가이드는 **Tools**와 **Embedded Resources**를 사용해 **MCP 서버**에서 **풍부하고 상호작용적인 A2UI 인터페이스**를 제공하는 방법을 보여 줍니다. 끝까지 따라 하면 어떤 MCP 호환 클라이언트에도 A2UI 컴포넌트를 반환하는 동작하는 MCP 서버를 갖게 됩니다.

<video width="100%" height="auto" controls playsinline style="display: block; aspect-ratio: 16/9; object-fit: cover; border-radius: 8px; margin-bottom: 24px;">
  <source src="../assets/guides-a2ui-over-mcp-tour.mp4" type="video/mp4">
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
# 저장소 복제 (아직 복제하지 않은 경우)
git clone https://github.com/a2ui-project/a2ui.git
cd a2ui/samples/community/mcp/a2ui-over-mcp-recipe

# MCP 서버 시작 (포트 8000에서 SSE 전송)
uv run .
```

### 옵션 A: MCP Inspector를 통한 상호작용

별도 터미널에서 [MCP Inspector](https://github.com/modelcontextprotocol/inspector)를 실행해 서버와 상호작용합니다.

```bash
npx @modelcontextprotocol/inspector@latest --web --transport sse --server-url http://localhost:8000/sse
```

`http://localhost:6274`를 엽니다:

1. **List Resources** 클릭 → `a2ui://recipe-form` 및 `a2ui://recipe-card` 확인
2. 리소스 읽기 → 데이터 바인딩이 포함된 정적 A2UI 프레젠테이션 템플릿(`createSurface` 및 `updateComponents`) 확인
3. **List Tools** 클릭 → 각각의 프레젠테이션 템플릿 리소스에 대한 `_meta.ui` 링크를 가진 `get_recipe_form_a2ui` 및 `get_recipe_a2ui` 확인
4. `get_recipe_form_a2ui` 실행 → 도구가 `updateDataModel` 메시지로 래핑된 초기 폼 선택 상태 반환
5. 커스텀 파라미터로 `get_recipe_a2ui` 실행 → 도구가 `updateDataModel` 메시지로 래핑된 동적 레시피 세부정보 반환

> [!NOTE]
> 이 샘플은 A2UI Agent SDK에 대한 로컬 경로 참조를 사용합니다. 여러분의 프로젝트에서는 PyPI에서 설치하세요.
>
> ```bash
> pip install a2ui-agent-sdk
> ```

### 옵션 B: Recipe Client 웹 앱 실행

인터랙티브 웹 클라이언트를 실행하려면:

> [!NOTE]
> A2UI 저장소 내장 샘플 애플리케이션을 실행하려면 Yarn 워크스페이스(`yarn install` / `yarn dev`)를 사용합니다. 이 저장소 외부에서는 선호하는 패키지 관리자(npm, pnpm, yarn)를 사용할 수 있습니다.

1. 새 터미널 창에서 client 디렉터리로 이동합니다.
    ```bash
    cd client
    ```
2. 의존성을 설치합니다.
    ```bash
    yarn install
    ```
3. Vite 개발 서버를 시작합니다.
    ```bash
    yarn dev
    ```
4. 브라우저에서 `http://localhost:5173`을 엽니다.

애플리케이션이 로드되면 클라이언트는 SSE를 통해 MCP 서버에 연결하고 `get_recipe_form_a2ui`를 실행합니다. `_meta.ui`를 읽어 `a2ui://recipe-form` 프레젠테이션 템플릿을 가져와 캐시한 다음, 반환된 `updateDataModel`을 적용하여 기본 선택값(`Grilled`, `Chicken`)을 채웁니다. 옵션을 선택하고 **"Get Recipe"**를 클릭하면 `get_recipe_a2ui`가 실행되어 `a2ui://recipe-card`를 가져오고 오른쪽 열에 레시피 세부정보를 동적으로 렌더링합니다.

![선택 폼과 동적 레시피 카드 생성을 보여주는 Dynamic Recipe Studio 데모, 왼쪽은 선택 폼, 오른쪽은 동적으로 생성되는 레시피 카드](../assets/recipe_sample.gif)

모든 샘플은 [`samples/community/mcp/`](../../../samples/community/mcp)에서 확인할 수 있습니다.

## 디커플링된 아키텍처: 프레젠테이션과 데이터 분리

MCP 위의 A2UI는 사용자 인터페이스를 두 개의 레이어로 분리합니다:

1. **MCP Resources를 통한 정적 프레젠테이션 템플릿 (`resources/read`)**:
   데이터 바인딩(`/title`, `/cookTime`, `/image` 등)이 포함된 컴포넌트 트리(`createSurface` 및 `updateComponents`)를 가진 레이아웃은 MIME 타입 `application/a2ui+json`과 함께 커스텀 URI(예: `a2ui://recipe-form`, `a2ui://recipe-card`) 아래의 MCP 리소스로 제공됩니다. 템플릿에는 하드코딩된 데이터 값이 없으므로 클라이언트가 로컬에 가져와 캐시할 수 있습니다.
2. **MCP Tools를 통한 동적 데이터 업데이트 (`tools/call`)**:
   도구가 실행될 때, 서버는 템플릿에 필요한 동적 값만 A2UI `updateDataModel` 메시지로 패키징하여 반환합니다.
3. **도구 UI 메타데이터 (`_meta.ui`)**:
   도구는 도구 정의 및 `CallToolResult`에 `_meta.ui` 객체를 포함하여 프레젠테이션 템플릿에 연결됩니다:
    ```json
    "_meta": {
      "ui": {
        "resourceUri": "a2ui://recipe-card",
        "mimeType": "application/a2ui+json"
      }
    }
    ```
4. **클라이언트 측 해석 및 하이드레이션 (Client-Side Resolution & Hydration)**:
   클라이언트 호스트는 `_meta.ui.resourceUri`를 검사하고, 로컬 템플릿 캐시를 확인(또는 첫 로드 시 서버에서 리소스를 가져옴)하여 서피스 레이아웃을 초기화한 후 도구 응답의 동적 `updateDataModel`을 적용합니다.

> [!IMPORTANT]
> **MIME 타입 일관성**
> 정적 템플릿 리소스와 동적 도구 페이로드 모두 `application/a2ui+json` MIME 타입을 사용합니다. 도구 응답에서 데이터 모델 업데이트는 폴백 `TextContent`와 함께 `EmbeddedResource` 내부에 반환됩니다. 이러한 식별 방식을 통해 클라이언트 애플리케이션은 페이로드를 A2UI 프로세서로 직접 라우팅할 수 있습니다.

### 전달 흐름

```
1. 도구 호출 (Tool Invocation)
클라이언트 → tools/call (예: get_recipe_a2ui) → MCP 서버
                                                  ↓
                                        동적 값 계산
                                                  ↓
클라이언트 ← CallToolResult (updateDataModel) ← MCP 서버
         + _meta.ui: { resourceUri: "a2ui://recipe-card" }

2. 템플릿 해석 (첫 조회 후 캐시됨)
클라이언트 캐시에 "a2ui://recipe-card"가 없는 경우:
  클라이언트 → resources/read ("a2ui://recipe-card") → MCP 서버
  클라이언트 ← 템플릿 (createSurface, updateComponents) ← MCP 서버

3. 서피스 하이드레이션 (Surface Hydration)
클라이언트가 도구 응답의 updateDataModel을 서피스에 적용
A2UI 렌더러가 화면 업데이트
```

### 1. MCP Resources로 프레젠테이션 템플릿 정의

`resources/list` 및 `resources/read`를 통해 정적 레이아웃 템플릿을 노출합니다:

```python
@app.list_resources()
async def list_resources() -> list[types.Resource]:
    return [
        types.Resource(
            uri="a2ui://recipe-form",
            name="Recipe Form",
            mimeType="application/a2ui+json",
            description="Static form allowing users to pick cuisine and protein.",
        ),
        types.Resource(
            uri="a2ui://recipe-card",
            name="Recipe Card",
            mimeType="application/a2ui+json",
            description="Static recipe card layout template.",
        ),
    ]


@app.read_resource()
async def read_resource(uri: str) -> list[ReadResourceContents]:
    if str(uri) == "a2ui://recipe-form":
        return [
            ReadResourceContents(
                content=json.dumps(recipe_form_json),
                mime_type="application/a2ui+json",
            )
        ]
    if str(uri) == "a2ui://recipe-card":
        return [
            ReadResourceContents(
                content=json.dumps(recipe_a2ui_json),
                mime_type="application/a2ui+json",
            )
        ]
    raise ValueError(f"Unknown resource: {uri}")
```

### 2. 도구 UI 메타데이터 선언

도구 정의에 프레젠테이션 리소스 URI를 선언합니다:

```python
types.Tool(
    name="get_recipe_a2ui",
    title="Get Recipe A2UI",
    description="Returns recipe data and links to the recipe-card template.",
    inputSchema={
        "type": "object",
        "properties": {
            "cookingStyle": {
                "type": "array",
                "items": {"type": "string"},
                "description": "Selected cooking styles",
            },
            "protein": {
                "type": "array",
                "items": {"type": "string"},
                "description": "Selected proteins",
            },
        },
        "additionalProperties": True,
    },
    _meta={
        "ui": {
            "resourceUri": "a2ui://recipe-card",
            "mimeType": "application/a2ui+json",
        }
    },
)
```

### 3. 도구 실행을 통한 동적 데이터 반환

도구 호출 핸들러에서 `_meta.ui`와 함께 동적 상태를 `updateDataModel` 메시지로 반환합니다:

```python
@app.call_tool()
async def handle_call_tool(
    name: str, arguments: dict[str, Any]
) -> types.CallToolResult:
    if name == "get_recipe_a2ui":
        # 사용자 인자로부터 선택된 레시피 해석
        style_list = arguments.get("cookingStyle", ["Baked"])
        protein_list = arguments.get("protein", ["Salmon"])
        style = style_list[0] if style_list else "Baked"
        protein = protein_list[0] if protein_list else "Salmon"
        recipe = RECIPES.get((style, protein))

        # 경량화된 updateDataModel 페이로드 생성
        data_model_update = [
            {
                "version": "v0.9",
                "updateDataModel": {
                    "surfaceId": "recipe-card",
                    "path": "/",
                    "value": {
                        "title": recipe["title"],
                        "rating": recipe["rating"],
                        "reviews": recipe["reviews"],
                        "cookTime": recipe["cookTime"],
                        "prepTime": recipe["prepTime"],
                        "servings": recipe["servings"],
                        "image": recipe["image"],
                    },
                },
            }
        ]

        return types.CallToolResult(
            content=[
                types.TextContent(
                    type="text",
                    text=f"Generated recipe: {recipe['title']}",
                ),
                types.EmbeddedResource(
                    type="resource",
                    resource=types.TextResourceContents(
                        uri="a2ui://recipe-card/data",
                        mimeType="application/a2ui+json",
                        text=json.dumps(data_model_update),
                    ),
                ),
            ],
            _meta={
                "ui": {
                    "resourceUri": "a2ui://recipe-card",
                    "mimeType": "application/a2ui+json",
                }
            },
        )
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
              "https://a2ui.org/specification/v0_9/basic_catalog.json"
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
              "https://a2ui.org/specification/v0_9/basic_catalog.json"
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

사용자가 버튼을 클릭하면, 클라이언트는 surface 상태를 기준으로 (`/dates/start` 같은) 데이터 바인딩을 해석하고 필수 액션 필드를 포함한 도구 호출을 전송합니다.

```json
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "id": "id-456",
  "params": {
    "name": "a2ui_action",
    "arguments": {
      "name": "confirm_booking",
      "surfaceId": "booking-surface",
      "sourceComponentId": "confirm-button",
      "timestamp": "2026-03-20T12:00:00Z",
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
@app.tool()
async def a2ui_action(
    name: str,
    surfaceId: str,
    sourceComponentId: str,
    timestamp: str,
    context: dict[str, Any],
) -> types.CallToolResult:
    """A2UI 사용자 액션을 처리합니다."""
    if name == "confirm_booking":
        # 예약을 처리한 다음 확인 UI를 반환합니다.
        return types.CallToolResult(content=[
            types.TextContent(
                type="text",
                text=f"Booking confirmed for {surfaceId}: {context['start']} to {context['end']}"
            )
        ])
    raise ValueError(f"Unknown action: {name}")
```

> [!NOTE]
> 5개의 모든 액션 필드(`name`, `surfaceId`, `sourceComponentId`, `timestamp`, `context`)는 A2UI 명세에서 필수 항목입니다. 도구 매개변수에 모든 필드를 선언해야 MCP SDK가 `surfaceId` 등의 필드를 누락하여 원래 surface 컨텍스트를 잃어버리는 것을 방지할 수 있습니다.

## 오류 처리

클라이언트는 도구 호출을 통해 A2UI 렌더링 및 검증 오류를 서버에 보고할 수 있습니다:

```json
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "id": "id-789",
  "params": {
    "name": "a2ui_error",
    "arguments": {
      "code": "VALIDATION_FAILED",
      "surfaceId": "booking-surface",
      "path": "/components/0/text",
      "message": "Failed to parse A2UI payload."
    }
  }
}
```

서버에서는 다음과 같이 처리합니다:

```python
@app.tool()
async def a2ui_error(
    code: str,
    surfaceId: str,
    message: str,
    path: str | None = None,
) -> types.CallToolResult:
    """A2UI 클라이언트 오류를 처리합니다."""
    # 오류를 기록하거나, 재시도하거나, 폴백 UI를 전송합니다.
    return types.CallToolResult(content=[
        types.TextContent(
            type="text",
            text=f"Acknowledged error {code} on surface {surfaceId}: {message}"
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
    # 사용자에게는 UI를 보여 주되, LLM에게는 원시 JSON을 숨깁니다.
    annotations=types.Annotations(audience=["user"])
)
```

| 대상(Audience)  | 동작                                                   |
| --------------- | ------------------------------------------------------ |
| _(비어 있음)_   | 사용자와 LLM 모두에게 표시됩니다.                      |
| `["user"]`      | 사용자에게 렌더링되며, LLM 컨텍스트에서는 숨겨집니다.  |
| `["assistant"]` | LLM이 후속 추론에 사용할 수 있지만 렌더링되지는 않습니다. |

## A2UI Agent SDK 사용하기

프로덕션 환경에서는 **A2UI Agent SDK**가 스키마 관리, 검증, 프롬프트 생성을 대신 처리해 줍니다.

```bash
pip install a2ui-agent-sdk
```

```python
from a2ui.core.basic_catalog import BasicCatalog
from a2ui.inference_formats.direct_json import DirectJsonFormat
from a2ui.schema import VERSION_0_9
from a2ui.utils import validate_payload

# 기본 카탈로그로 추론 포맷 초기화
inference_format = DirectJsonFormat([BasicCatalog(VERSION_0_9)])

# 전송 전 A2UI 출력 검증. 카탈로그를 보유한 렌더러가 페이로드를 거부할 경우
# A2uiValidationError를 발생시킵니다.
selected_catalog = inference_format.catalogs[0]
validate_payload([selected_catalog], a2ui_payload)
```

스키마 관리, 동적 카탈로그, 스트리밍에 대한 자세한 내용은 [에이전트 개발 가이드](agent-development.md)를 참고하세요.

## 다음 단계

- [A2UI 명세](../specification/v0.9-a2ui.md) — 전체 프로토콜 레퍼런스
- [컴포넌트 갤러리](../reference/components.md) — 사용 가능한 컴포넌트 둘러보기
- [A2UI Surface 안의 MCP Apps](mcp-apps-in-a2ui.md) — HTML 기반 MCP 앱을 A2UI 안에 임베드하기
- [클라이언트 설정](client-setup.md) — A2UI를 표시하는 렌더러 구축하기
