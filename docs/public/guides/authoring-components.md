---
render_macros: false
---

# 커스텀 컴포넌트 작성하기

`rizzcharts` 샘플을 예로 사용해 A2UI에서 커스텀 컴포넌트를 정의하고, 구현하고, 등록하는 방법을 설명합니다. 이 가이드는 Angular 코드 주변에 컴포넌트를 작성하는 데 초점을 둡니다.

## 개요

새 컴포넌트를 작성하는 과정은 네 가지 주요 단계로 구성됩니다.

1.  **카탈로그 스키마 정의**: JSON Schema에 컴포넌트의 속성과 타입을 지정합니다.
2.  **컴포넌트 정의(클라이언트)**: 사용하는 프레임워크(예: Angular)로 UI를 구현합니다.
3.  **렌더러에 등록(클라이언트)**: 컴포넌트를 클라이언트 측 카탈로그에 추가합니다.
4.  **에이전트에서 호출**: 에이전트가 `send_a2ui_json_to_client`를 통해 컴포넌트를 사용하도록 지시합니다.

---

## 1. 카탈로그 스키마 정의

카탈로그 스키마는 카탈로그의 API를 정의합니다. 사용 가능한 컴포넌트와 그 속성을 나열하며, 에이전트는 이를 사용해 UI payload를 구성합니다.

**이 스키마는 클라이언트와 서버(에이전트) 사이의 계약 역할을 합니다.** 렌더링이 동작하려면 양쪽이 이 스키마에 동의해야 합니다. 클라이언트는 자신이 지원하는 카탈로그를 광고하고, 서버는 호환되는 카탈로그를 선택합니다. 이 handshake의 작동 방식은 [A2UI Catalog Negotiation](../concepts/catalogs.md#a2ui-catalog-negotiation)을 참고하세요.

[`rizzcharts`](../../../samples/community/agent/adk/rizzcharts/python/README.md) 예시에서는 카탈로그 스키마가 [`rizzcharts_catalog_definition.json`](../../../samples/community/agent/adk/rizzcharts/catalog_schemas/0.9/rizzcharts_catalog_definition.json)에 정의되어 있습니다.

다음은 `Chart` 컴포넌트의 스키마입니다.

```json
"Chart": {
  "type": "object",
  "description": "An interactive chart that uses a hierarchical list of objects for its data.",
  "properties": {
    "type": {
      "type": "string",
      "description": "The type of chart to render.",
      "enum": [
        "doughnut",
        "pie"
      ]
    },
    "title": {
      "type": "object",
      "description": "The title of the chart. Can be a literal string or a data model path.",
      "properties": {
        "literalString": {
          "type": "string"
        },
        "path": {
          "type": "string"
        }
      }
    },
    "chartData": {
      "type": "object",
      "description": "The data for the chart, provided as a list of items. Can be a literal array or a data model path.",
      "properties": {
        "literalArray": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "label": {
                "type": "string"
              },
              "value": {
                "type": "number"
              },
              "drillDown": {
                "type": "array",
                "description": "An optional list of items for the next level of data.",
                "items": {
                  "type": "object",
                  "properties": {
                    "label": {
                      "type": "string"
                    },
                    "value": {
                      "type": "number"
                    }
                  },
                  "required": [
                    "label",
                    "value"
                  ]
                }
              }
            },
            "required": [
              "label",
              "value"
            ]
          }
        },
        "path": {
          "type": "string"
        }
      }
    }
  },
  "required": [
    "type",
    "chartData"
  ]
}
```

---

## 2. 컴포넌트 구현(클라이언트)

클라이언트 측 프레임워크를 사용해 컴포넌트를 구현합니다. Angular의 경우 컴포넌트는 `@a2ui/angular/v0_9`가 제공하는 `CatalogComponent`를 확장해야 합니다.

`rizzcharts` 예시에서 `Chart` 컴포넌트는 `chart.ts`에 정의되어 있습니다.

먼저 TypeScript로 컴포넌트 API를 정의합니다. 이는 1단계에서 정의한 JSON Schema와 일치해야 합니다.

```typescript
// api.ts
import {ComponentApi} from '@a2ui/web_core/v0_9';
import {z} from 'zod';

export const ChartApi = {
  name: 'Chart',
  schema: z.object({
    type: z.enum(['doughnut', 'pie']),
    title: z.string().optional(),
    chartData: z.array(
      z.object({
        label: z.string(),
        value: z.number(),
        drillDown: z.array(
          z.object({
            label: z.string(),
            value: z.number(),
          })
        ).optional(),
      })
    ),
  }).strict(),
} satisfies ComponentApi;
```

이제 Angular 컴포넌트를 구현합니다.

```typescript
import {CatalogComponent} from '@a2ui/angular/v0_9';
import {Component, computed} from '@angular/core';
import {BaseChartDirective} from 'ng2-charts';
import {ChartApi} from './api';

@Component({
  selector: 'a2ui-chart',
  imports: [BaseChartDirective],
  template: `
    <div>
      <h2>{{ title() }}</h2>
      <canvas baseChart [data]="chartData()" [type]="chartType()"></canvas>
    </div>
  `,
})
export class Chart extends CatalogComponent<typeof ChartApi> {
  protected readonly chartType = computed(() => this.props()['type']?.value() || 'pie');
  protected readonly title = computed(() => this.props()['title']?.value() || '');
  protected readonly chartData = computed(() => {
    const rawData = this.props()['chartData']?.value() || [];
    return {
      labels: rawData.map(item => item.label),
      datasets: [
        {
          data: rawData.map(item => item.value),
        },
      ],
    };
  });
}
```

컴포넌트를 구현할 때는 다음 핵심 사항을 기억하세요.

- **`CatalogComponent` 확장**: 타입 안전한 `props` signal input에 접근할 수 있습니다.
- **`props()` Signal 사용**: `this.props()['propertyName']?.value()`를 통해 해석된 속성에 반응형으로 접근합니다. 프레임워크가 데이터 바인딩과 표현식 해석을 자동으로 처리합니다.

---

## 3. 렌더러에 등록(클라이언트)

컴포넌트를 구현한 뒤에는 클라이언트 카탈로그에 등록합니다. 이렇게 하면 에이전트가 사용하는 컴포넌트 이름이 구현 클래스에 매핑됩니다.

`AngularCatalog` 클래스를 사용해 카탈로그를 정의합니다.

```typescript
import {AngularCatalog, BASIC_COMPONENTS, BASIC_FUNCTIONS} from '@a2ui/angular/v0_9';
import {Chart} from './chart';
import {ChartApi} from './api';

const customChartComponent = {
  ...ChartApi,
  component: Chart
};

export const RIZZ_CHARTS_CATALOG = new AngularCatalog(
  'https://github.com/.../rizzcharts_catalog_definition.json',
  [...BASIC_COMPONENTS, customChartComponent],
  BASIC_FUNCTIONS
);
```

등록 시 핵심 사항은 다음과 같습니다.

- **즉시(Eager) 등록**: 컴포넌트 클래스는 카탈로그 정의에 직접 등록됩니다.

---

## 4. 에이전트에서 호출

커스텀 컴포넌트를 사용하려면, 여러분의 카탈로그를 이해하는 A2UI SDK의 도구로 에이전트를 초기화합니다. SDK는 카탈로그 해석과 모델에 예시를 제공하는 작업을 처리합니다.

흐름은 다음과 같이 연결됩니다.

### 4.1 세션 준비(Executor)

실행 계층(예: `RizzchartsAgentExecutor`)은 들어오는 메시지를 가로채 A2UI가 활성화되어 있는지, 클라이언트가 어떤 카탈로그를 지원하는지 감지합니다. 그런 다음 카탈로그를 해석하고 세션 상태에 저장합니다.

```python
# In agent_executor.py

use_ui = try_activate_a2ui_extension(context)
if use_ui:
    # Resolve catalog based on client capabilities
    a2ui_catalog = self.schema_manager.get_selected_catalog(
        client_ui_capabilities=capabilities
    )
    examples = self.schema_manager.load_examples(a2ui_catalog, validate=True)

    # Save to session (Event contains state_delta)
    await runner.session_service.append_event(
        session,
        Event(
            actions=EventActions(
                state_delta={
                    _A2UI_ENABLED_KEY: True,
                    _A2UI_CATALOG_KEY: a2ui_catalog,
                    _A2UI_EXAMPLES_KEY: examples,
                }
            ),
        ),
    )
```

### 4.2 에이전트 도구 설정

Agent는 [SendA2uiToClientToolset](../../../agent_sdks/python/a2ui_agent/src/a2ui/adk/send_a2ui_to_client_toolset.py)을 사용해 A2UI를 클라이언트로 보낼 수 있는 도구를 에이전트에 제공합니다.

```python
from a2ui.adk.send_a2ui_to_client_toolset import SendA2uiToClientToolset

a2ui_catalog = self.schema_manager.get_selected_catalog(
    client_ui_capabilities=capabilities
)
agent.tools = [
    SendA2uiToClientToolset(
        a2ui_catalog=a2ui_catalog,
        a2ui_enabled=True,
    )
]
```

### 4.3 도구 실행

LLM이 [SendA2uiToClientToolset](../../../agent_sdks/python/a2ui_agent/src/a2ui/adk/send_a2ui_to_client_toolset.py)의 도구를 호출하면, A2A Agent Executor에서 [A2uiEventConverter](../../../agent_sdks/python/a2ui_agent/src/a2ui/adk/a2a/event_converter.py)를 통해 이를 가로챕니다. 이 변환기는 도구 호출을 A2UI payload가 포함된 A2A Datapart로 자동 변환합니다.

```python
from a2ui.adk.a2a.event_converter import (
    A2uiEventConverter,
)

config = A2aAgentExecutorConfig(event_converter=A2uiEventConverter())
```
