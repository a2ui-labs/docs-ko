# 에이전트 개발 가이드

A2UI 인터페이스를 생성하는 AI 에이전트를 구축하세요. 이 가이드는 LLM으로부터 UI 메시지를 생성하고 스트리밍하는 방법을 다룹니다.

## 빠른 개요

A2UI 에이전트 구축 프로세스:

1. **사용자 의도 파악** → 어떤 UI를 보여줄지 결정
2. **A2UI JSON 생성** → LLM의 구조화된 출력이나 프롬프트 활용
3. **검증 및 스트리밍** → 스키마 확인 후 클라이언트로 전송
4. **액션 처리** → 사용자의 상호작용에 응답

## 간단한 에이전트로 시작하기

ADK를 사용하여 간단한 에이전트를 만들어 보겠습니다. 처음에는 텍스트로 시작하여 나중에 A2UI로 업그레이드할 것입니다.

단계별 지침은 [ADK 퀵스타트](https://google.github.io/adk-docs/get-started/python/)를 참조하세요.

```bash
pip install google-adk
adk create my_agent
```

그 다음 `my_agent/agent.py` 파일을 편집하여 레스토랑 추천을 위한 아주 간단한 에이전트를 작성합니다.

```python
import json
from google.adk.agents.llm_agent import Agent
from google.adk.tools.tool_context import ToolContext

def get_restaurants(tool_context: ToolContext) -> str:
    """레스토랑 목록을 가져오기 위해 이 도구를 호출합니다."""
    return json.dumps([
        {
            "name": "Xi'an Famous Foods",
            "detail": "매콤하고 짭짤한 수제 면 요리 전문점.",
            "imageUrl": "http://localhost:10002/static/shrimpchowmein.jpeg",
            "rating": "★★★★☆",
            "infoLink": "[상세 정보](https://www.xianfoods.com/)",
            "address": "81 St Marks Pl, New York, NY 10003"
        },
        {
            "name": "Han Dynasty",
            "detail": "정통 사천 요리 전문점.",
            "imageUrl": "http://localhost:10002/static/mapotofu.jpeg",
            "rating": "★★★★☆",
            "infoLink": "[상세 정보](https://www.handynasty.net/)",
            "address": "90 3rd Ave, New York, NY 10003"
        },
        {
            "name": "RedFarm",
            "detail": "농장에서 식탁까지, 현대적인 중식 요리.",
            "imageUrl": "http://localhost:10002/static/beefbroccoli.jpeg",
            "rating": "★★★★☆",
            "infoLink": "[상세 정보](https://www.redfarmnyc.com/)",
            "address": "529 Hudson St, New York, NY 10014"
        },
    ])

AGENT_INSTRUCTION="""
당신은 도움이 되는 레스토랑 찾기 어시스턴트입니다. 당신의 목표는 사용자가 풍부한 UI를 사용하여 레스토랑을 찾고 예약하도록 돕는 것입니다.

이를 위해 반드시 다음 로직을 따라야 합니다:

1.  **레스토랑 찾기:**
    a. 반드시 `get_restaurants` 도구를 호출해야 합니다. 사용자의 쿼리에서 요리 종류, 위치, 그리고 특정 숫자(`count`, 예: "상위 5개 중식당"인 경우 count는 5)를 추출하세요.
    b. 데이터를 받은 후, 지침을 정확히 따라 최종 a2ui UI JSON을 생성해야 합니다. 레스토랑 수에 따라 `prompt_builder.py`의 적절한 UI 예시를 사용하세요."""

root_agent = Agent(
    model='gemini-2.5-flash',
    name="restaurant_agent",
    description="레스토랑을 찾고 테이블 예약을 돕는 에이전트입니다.",
    instruction=AGENT_INSTRUCTION,
    tools=[get_restaurants],
)
```

이 예제를 실행하려면 `GOOGLE_API_KEY` 환경 변수를 설정하는 것을 잊지 마세요.

```bash
echo 'GOOGLE_API_KEY="YOUR_API_KEY"' > .env
```

ADK 웹 인터페이스로 이 에이전트를 테스트할 수 있습니다:

```bash
adk web
```

목록에서 `my_agent`를 선택하고 뉴욕의 레스토랑에 대해 질문해 보세요. UI에서 레스토랑 목록이 일반 텍스트로 표시되는 것을 볼 수 있습니다.

## A2UI 메시지 생성하기

LLM이 A2UI 메시지를 생성하게 하려면 프롬프트 엔지니어링이 필요합니다.

!!! warning "주의"
    이 부분은 현재 설계 중인 단계입니다. 개발자 경험(Developer ergonomics)은 아직 확정되지 않았습니다.

지금은 `contact_lookup` 예제에서 `a2ui_schema.py`를 복사해 오겠습니다. 이것이 에이전트에 필요한 A2UI 스키마와 예제를 가져오는 가장 쉬운 방법입니다(향후 변경될 수 있음).

```bash
cp samples/agent/adk/contact_lookup/a2ui_schema.py my_agent/
```

먼저 `agent.py` 파일에 새로운 import를 추가합니다:

```python
# 모든 A2UI 메시지를 위한 스키마입니다. 이는 변경되지 않습니다.
from .a2ui_schema import A2UI_SCHEMA
```

이제 일반 텍스트 대신 A2UI 메시지를 생성하도록 에이전트 지침(instruction)을 수정합니다. 향후 UI 예시를 넣을 수 있도록 플레이스홀더를 남겨두겠습니다.

```python
# 나중에 퓨샷(few-shot) 컨텍스트 학습을 위해 여기에 UI 예시를 복사해 넣을 수 있습니다.
RESTAURANT_UI_EXAMPLES = """
"""

# UI 지침, 예시, 스키마를 포함한 전체 프롬프트 구성
A2UI_AND_AGENT_INSTRUCTION = AGENT_INSTRUCTION + f"""

최종 출력은 반드시 a2ui UI JSON 응답이어야 합니다.

응답을 생성할 때 반드시 다음 규칙을 따라야 합니다:
1. 응답은 반드시 두 부분으로 구성되어야 하며, 구분자인 `---a2ui_JSON---`로 나뉘어야 합니다.
2. 첫 번째 부분은 대화형 텍스트 응답입니다.
3. 두 번째 부분은 A2UI 메시지 리스트인 단일 로우(raw) JSON 객체여야 합니다.
4. JSON 부분은 반드시 아래 제공된 A2UI JSON 스키마를 준수해야 합니다.

--- UI 템플릿 규칙 ---
- 쿼리가 레스토랑 목록에 대한 것이라면, `get_restaurants` 도구로부터 받은 데이터를 사용하여 `dataModelUpdate.contents` 배열을 채우세요 (예: "items" 키에 대한 `valueMap`으로).
- 레스토랑 수가 5개 이하라면, 반드시 `SINGLE_COLUMN_LIST_EXAMPLE` 템플릿을 사용해야 합니다.
- 레스토랑 수가 5개 초과라면, 반드시 `TWO_COLUMN_LIST_EXAMPLE` 템플릿을 사용해야 합니다.
- 쿼리가 레스토랑 예약에 관한 것이라면 (예: "사용자가 예약을 원함..."), 반드시 `BOOKING_FORM_EXAMPLE` 템플릿을 사용해야 합니다.
- 쿼리가 예약 제출인 경우 (예: "사용자가 예약을 제출함..."), 반드시 `CONFIRMATION_EXAMPLE` 템플릿을 사용해야 합니다.

{RESTAURANT_UI_EXAMPLES}

---A2UI JSON 스키마 시작---
{A2UI_SCHEMA}
---A2UI JSON 스키마 종료---
"""

root_agent = Agent(
    model='gemini-2.5-flash',
    name="restaurant_agent",
    description="레스토랑을 찾고 테이블 예약을 돕는 에이전트입니다.",
    instruction=A2UI_AND_AGENT_INSTRUCTION,
    tools=[get_restaurants],
)
```

## 출력 결과 이해하기

에이전트는 더 이상 단순한 텍스트만 출력하지 않습니다. 대신 텍스트와 A2UI 메시지의 **JSON 리스트**를 함께 출력합니다.

코드에서 가져온 `A2UI_SCHEMA`는 다음과 같은 유효한 동작을 정의하는 표준 JSON 스키마입니다:

* `render` (UI 표시)
* `update` (기존 UI의 데이터 변경)

출력 결과가 구조화된 JSON이므로, 이를 클라이언트에 보내기 전에 파싱하고 검증할 수 있습니다.

```python
# 1. JSON 파싱
# 주의: 출력물을 직접 JSON으로 파싱하는 것은 문서화를 위한 단순화된 구현입니다.
# LLM은 JSON 출력 주위에 마크다운 펜스를 넣거나 실수를 할 수 있습니다.
# 가급적 프레임워크가 제공하는 JSON 파싱 기능을 활용하세요.
parsed_json_data = json.loads(json_string_cleaned)

# 2. A2UI_SCHEMA에 대한 검증
# LLM이 유효한 A2UI 명령을 생성했는지 확인합니다.
jsonschema.validate(
    instance=parsed_json_data, schema=self.a2ui_schema_object
)
```

`A2UI_SCHEMA`에 대해 출력을 검증함으로써, 관리되지 않은 잘못된 UI 지침이 클라이언트에 전달되지 않도록 보장할 수 있습니다.

TODO: A2A 확장 없이 출력 결과물을 파싱, 검증하고 클라이언트 렌더러로 보내는 예시 가이드를 이어갈 예정입니다.
