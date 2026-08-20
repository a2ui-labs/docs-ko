# 모든 에이전트 프레임워크 및 하니스에서 A2UI 사용

A2UI는 선언적 UI 형식입니다. [AG-UI](https://ag-ui.com/)는 에이전트와 앱 사이에서 A2UI 메시지를 운반하는 전송 계층입니다. 이 가이드를 사용해 ADK, LangGraph, Mastra, Strands, CrewAI, Google Chat, Slack 등 AG-UI를 지원하는 모든 에이전트 프레임워크나 서비스 기반의 AG-UI 앱 또는 하니스에 A2UI를 추가하세요.

<style>
  .agui-demo-video {
    border-radius: 8px;
    display: block;
    margin: 24px auto;
    max-width: 100%;
    width: 75%;
  }

  @media (max-width: 700px) {
    .agui-demo-video {
      width: 100%;
    }
  }
</style>

<video class="agui-demo-video" controls playsinline preload="metadata">
  <source src="https://cdn.copilotkit.ai/docs/a2ui/ag-ui-a2ui-demo.mp4" type="video/mp4">
  브라우저가 비디오 태그를 지원하지 않습니다.
</video>

아래 예제는 AG-UI 호환 런타임 도구를 사용하므로, 렌더러 활성화, 에이전트에 카탈로그 제공, 사용자에게 UI 업데이트 스트리밍 등 A2UI surface에 집중할 수 있습니다. 프로토콜 수준의 설정과 개념은 [AG-UI 문서](https://docs.ag-ui.com/)를 참고하세요.

## 에이전트 스킬

코딩 에이전트를 사용해 이를 연결한다면, 앱을 수정하기 전에 [AG-UI `ag-ui-a2ui-integration` 스킬](https://github.com/ag-ui-protocol/ag-ui/tree/main/skills/ag-ui-a2ui-integration)을 로드하세요. 이 스킬은 AG-UI 프레임워크 어댑터, 지원되는 `create-ag-ui-app` 플래그, 전송 계층 설정, A2UI 런타임 및 렌더러 연결, AG-UI + A2UI 앱의 엔드투엔드 검증을 다룹니다.

앱이 A2UI 렌더링에 CopilotKit을 사용한다면, CopilotKit v2 런타임, provider, 테마, 카탈로그 규칙을 위해 [CopilotKit `a2ui-renderer` 스킬](https://github.com/CopilotKit/CopilotKit/blob/main/skills/a2ui-renderer/SKILL.md)도 함께 로드하세요.

## 1. AG-UI 설정

이미 사용 중인 에이전트 프레임워크에서 시작한 다음, 에이전트와 앱 사이에 AG-UI 런타임 연결을 추가합니다. 이 런타임은 A2UI 메시지를 포함한 에이전트 이벤트를 클라이언트 surface로 스트리밍합니다.

원하는 클라이언트와 에이전트 프레임워크로 AG-UI 앱을 scaffold하려면 AG-UI CLI를 사용하세요.

```bash
npx create-ag-ui-app@latest
```

지원되는 프레임워크 템플릿에서 바로 시작할 수도 있습니다.

```bash
npx create-ag-ui-app@latest --adk
npx create-ag-ui-app@latest --langgraph-py
npx create-ag-ui-app@latest --langgraph-js
```

Strands는 아직 scaffold 플래그가 없습니다. 기존 Strands 에이전트를 감싸서 사용하세요(아래 Strands 패널 참고).

중요한 부분은 전송 계약(transport contract)입니다. 앱은 AG-UI 이벤트를 수신하고 A2UI payload를 A2UI 렌더러로 라우팅합니다. 일부 scaffold 경로는 내부적으로 Next.js와 함께 [CopilotKit의 A2UI 런타임](https://docs.copilotkit.ai/generative-ui/a2ui)을 사용하지만, 설정 표면은 여전히 AG-UI를 우선합니다.

## 2. 에이전트 또는 하니스 설정

A2UI 단계는 프레임워크에 관계없이 동일합니다. 에이전트를 AG-UI에 연결하고, A2UI payload를 활성화한 다음, 앱에서 해당 payload를 렌더링합니다. 이미 사용 중인 프레임워크나 하니스로 시작하세요. 아래 코드 조각은 각 AG-UI 통합에서 가져온 것이며, AG-UI가 감싸는 프레임워크 고유의 에이전트 형태를 보여줍니다.

=== "ADK"

    에이전트가 이미 Google의 Agent Development Kit에서 실행 중이라면 ADK를 사용하세요. AG-UI ADK 미들웨어는 에이전트를 AG-UI 이벤트 스트림으로 노출합니다.

    ```python
    from fastapi import FastAPI
    from ag_ui_adk import ADKAgent, AGUIToolset, add_adk_fastapi_endpoint
    from google.adk.agents import Agent

    my_agent = Agent(
        name="assistant",
        instruction="You are a helpful assistant.",
        tools=[
            AGUIToolset(),  # Adds tools provided by the AG-UI client.
        ],
    )

    agent = ADKAgent(
        adk_agent=my_agent,
        app_name="my_app",
        user_id="user123",
    )

    app = FastAPI()
    add_adk_fastapi_endpoint(app, agent, path="/chat")
    ```

    [AG-UI ADK 미들웨어](https://github.com/ag-ui-protocol/ag-ui/tree/main/integrations/adk-middleware/python)를 참고하세요.

=== "LangGraph (Python)"

    에이전트 워크플로가 상태를 가진 노드들의 그래프라면 LangGraph를 사용하세요. 평소 사용하던 LangGraph 에이전트에서 시작하면 됩니다 — A2UI는 그래프에 별도의 도구 연결이 필요하지 않습니다.

    ```python
    from copilotkit import CopilotKitMiddleware
    from langchain.agents import create_agent
    from langchain_google_genai import ChatGoogleGenerativeAI

    gemini = ChatGoogleGenerativeAI(
        model="gemini-2.5-pro",
        thinking_budget=1024,
    )

    # 평범한 LangGraph 에이전트입니다 — 그래프에 A2UI 도구 연결이 없습니다. CopilotKit
    # 런타임이 프론트엔드 카탈로그를 전달하고 `generate_a2ui` 도구를 주입하므로,
    # A2UI 기능을 사용하려면 CopilotKitMiddleware를 포함하세요.
    graph = create_agent(
        model=gemini,
        tools=[],
        middleware=[CopilotKitMiddleware()],
        system_prompt="You are a helpful assistant.",
    )
    ```

    LangGraph의 A2UI 도구는 CopilotKit 미들웨어 계층에서 실행되므로, A2UI 기능을 사용하려면 `CopilotKitMiddleware`를 포함해야 합니다. CopilotKit 런타임은 카탈로그를 전달하고 `generate_a2ui`를 자동으로 주입합니다. 이 예제는 LangChain의 Google GenAI 통합을 통해 Gemini를 사용합니다.

    [AG-UI LangGraph 통합](https://github.com/ag-ui-protocol/ag-ui/tree/main/integrations/langgraph/python)과 [ChatGoogleGenerativeAI 통합](https://docs.langchain.com/oss/python/integrations/chat/google_generative_ai)을 참고하세요.

=== "LangGraph (FastAPI)"

    동일한 LangGraph 그래프를 FastAPI 앱 뒤에서 서빙한다면 FastAPI 변형을 사용하세요. 에이전트 형태는 동일합니다 — 같은 `graph`를 export하고 AG-UI LangGraph 엔드포인트를 통해 서빙하면 됩니다.

    ```python
    from copilotkit import CopilotKitMiddleware
    from langchain.agents import create_agent
    from langchain_google_genai import ChatGoogleGenerativeAI

    gemini = ChatGoogleGenerativeAI(
        model="gemini-2.5-pro",
        thinking_budget=1024,
    )

    graph = create_agent(
        model=gemini,
        tools=[],
        middleware=[CopilotKitMiddleware()],
        system_prompt="You are a helpful assistant.",
    )
    ```

    [AG-UI LangGraph 통합](https://github.com/ag-ui-protocol/ag-ui/tree/main/integrations/langgraph/python)을 참고하세요.

=== "LangGraph (TypeScript)"

    LangGraph 에이전트가 TypeScript로 작성되어 있다면 TypeScript 변형을 사용하세요. 형태는 Python 에이전트와 동일합니다 — 평범한 그래프에 CopilotKit 미들웨어를 더한 것입니다.

    ```ts
    import { createAgent } from "langchain";
    import { ChatOpenAI } from "@langchain/openai";
    import { copilotkitMiddleware } from "@copilotkit/sdk-js/langgraph";

    export const graph = createAgent({
      model: new ChatOpenAI({ model: "gpt-4o" }),
      tools: [],
      middleware: [copilotkitMiddleware],
      systemPrompt: "You are a helpful assistant.",
    });
    ```

    [AG-UI LangGraph TypeScript 통합](https://github.com/ag-ui-protocol/ag-ui/tree/main/integrations/langgraph/typescript)을 참고하세요.

=== "Strands (Python)"

    에이전트 오케스트레이션이 AWS Strands 기반이라면 Strands를 사용하세요. 평범한 Strands 에이전트를 AG-UI Strands 어댑터로 감쌉니다.

    ```python
    from strands import Agent
    from ag_ui_strands import StrandsAgent

    strands_agent = Agent(
        system_prompt="You are a helpful assistant.",
    )

    agent = StrandsAgent(
        agent=strands_agent,
        name="my-agent",
        description="A Strands agent exposed via AG-UI",
    )
    ```

    [AG-UI AWS Strands 통합](https://github.com/ag-ui-protocol/ag-ui/tree/main/integrations/aws-strands/python)을 참고하세요.

=== "Strands (TypeScript)"

    Strands 에이전트가 TypeScript로 작성되어 있다면 TypeScript 변형을 사용하세요. AG-UI Strands 어댑터가 AG-UI 클라이언트를 위해 Strands 에이전트를 감쌉니다.

    ```ts
    import { Agent } from "@strands-agents/sdk";
    import { StrandsAgent } from "@ag-ui/aws-strands";
    import { createStrandsApp } from "@ag-ui/aws-strands/server";

    const strandsAgent = new Agent({
      systemPrompt: "You are a helpful assistant.",
      tools: [],
    });

    const aguiAgent = new StrandsAgent({
      agent: strandsAgent,
      name: "MyAgent",
      description: "A Strands agent exposed via AG-UI",
    });

    const app = await createStrandsApp(aguiAgent, { path: "/invocations" });
    app.listen(8000);
    ```

    [AG-UI AWS Strands 통합](https://github.com/ag-ui-protocol/ag-ui/tree/main/integrations/aws-strands/typescript)을 참고하세요.

=== "Slack"

    사용자 경험이 Slack 앱 안에 있다면 Slack을 사용하세요. Slack 스레드를 동일한 AG-UI 에이전트 엔드포인트로 라우팅합니다. 동일한 AG-UI 이벤트 스트림이 Slack 하니스에 데이터를 공급하고, surface의 클라이언트 브리지를 통해 A2UI를 렌더링할 수 있습니다.

    <video class="agui-demo-video" controls playsinline preload="metadata">
      <source src="https://cdn.copilotkit.ai/docs/a2ui/ag-ui-slack-demo.mp4" type="video/mp4">
      브라우저가 비디오 태그를 지원하지 않습니다.
    </video>

    CopilotKit의 Slack 어댑터는 이미 이 패턴을 구현하고 있습니다.

    ```ts
    import { createBot } from "@copilotkit/bot";
    import {
      slack,
      SanitizingHttpAgent,
      defaultSlackTools,
      defaultSlackContext,
    } from "@copilotkit/bot-slack";

    const bot = createBot({
      adapters: [
        slack({
          botToken: process.env.SLACK_BOT_TOKEN!,
          appToken: process.env.SLACK_APP_TOKEN!,
        }),
      ],
      agent: (threadId) => {
        const agent = new SanitizingHttpAgent({
          url: process.env.AGENT_URL!,
        });
        agent.threadId = threadId;
        return agent;
      },
      tools: [...defaultSlackTools],
      context: [...defaultSlackContext],
    });

    bot.onMention(async ({ thread }) => {
      await thread.runAgent();
    });

    await bot.start();
    ```

이 코드 조각들은 AG-UI 서버 연결을 구성합니다. Slack은 자체 하니스와 클라이언트 브리지를 통해 동일한 AG-UI/A2UI 계약을 사용합니다. 다음 섹션에서는 앱 surface 내부에서 A2UI 렌더링, 카탈로그, 컴포넌트 정의를 켜는 방법을 다룹니다.

## 3. A2UI 활성화

원하는 개발자 경험에서 시작하세요. 에이전트가 볼 수 있는 카탈로그 정의를 정의하고, 각 정의를 렌더러에 매핑한 다음, 카탈로그를 생성해 CopilotKit에 전달합니다. 프론트엔드 카탈로그 설정이 목표로 하는 A2UI 활성화 표면입니다.

```tsx
import {CopilotKit, CopilotChat} from '@copilotkit/react-core/v2';
import {
  createCatalog,
  type CatalogDefinitions,
  type CatalogRenderers,
} from '@copilotkit/a2ui-renderer';
import {z} from 'zod';

// 카탈로그 정의 — 에이전트에게 기본 구성 요소 컴포넌트를 설명합니다
export const catalogDefinitions = {
  Card: {
    description: 'A titled card container.',
    props: z.object({title: z.string(), subtitle: z.string().optional()}),
  },
  PrimaryButton: {
    description: 'A styled primary button.',
    props: z.object({label: z.string(), action: z.any().optional()}),
  },
} satisfies CatalogDefinitions;

// 카탈로그 렌더러 — 각 primitive가 DOM에서 어떻게 렌더링되는지 정의합니다(이 예제에서는 React)
export const catalogRenderers = {
  Card: MyCard,
  PrimaryButton: MyPrimaryButton,
} satisfies CatalogRenderers<typeof catalogDefinitions>;

// 정의(definitions)와 렌더러(renderers)를 합쳐 카탈로그 선언을 정의합니다
const catalog = createCatalog(catalogDefinitions, catalogRenderers, {
  catalogId: 'my-catalog',
  includeBasicCatalog: true,
});

<CopilotKit runtimeUrl="/api/copilotkit" a2ui={{catalog}}>
  <CopilotChat />
</CopilotKit>;
```

카탈로그를 provider에 전달하면 A2UI가 자동으로 활성화되고 `generate_a2ui` 도구가 주입되므로, 별도의 런타임 설정 없이도 에이전트가 surface를 생성할 수 있습니다(CopilotKit ≥ 1.61.2). 런타임을 직접 설정하면 이 기능을 끄거나, 카탈로그 없이 수동으로 켤 수도 있습니다.

```ts title="app/api/copilotkit/route.ts"
import {CopilotRuntime} from '@copilotkit/runtime';

const runtime = new CopilotRuntime({
  agents: {default: myAgent},
  a2ui: {injectA2UITool: true},
});
```

`a2ui: { injectA2UITool: true, agents: ["my-agent"] }`를 사용해 특정 에이전트로 범위를 제한할 수 있습니다. 에이전트가 이미 `a2ui_operations`를 반환하는 고정 스키마 플로우라면 `a2ui: true` 또는 `a2ui: {}`만으로 충분합니다.

### 커스텀 컴포넌트(BYOC)

A2UI는 즉시 동작하는 surface를 만들 수 있도록 내장 카탈로그(Text, Image, Card 등)를 제공합니다. 아래 확장된 BYOC 플로우는 실제 앱에서 동일한 카탈로그 패턴을 여러 파일로 나눈 모습을 보여줍니다.

1. **정의(Definitions)** — Zod 스키마와 자연어 설명입니다. 에이전트가 시스템 프롬프트에서 보게 되는 내용입니다. 클라이언트 측 함수의 경우, 클라이언트는 활성 카탈로그 정의에서 설정을 읽어 런타임에 함수의 실행 경계(예: clientOnly 상태)를 결정한다는 점에 유의하세요.
2. **렌더러(Renderers)** — 정의마다 하나씩 있는 타입 지정 React 컴포넌트입니다. 사용자가 보게 되는 내용입니다.
3. **등록(Registration)** — provider를 통해 카탈로그를 전달하여 A2UI 렌더러가 컴포넌트를 그리는 방법을 알게 합니다.

#### 1. 컴포넌트 스키마 정의

Zod로 플랫폼에 종속되지 않는 정의를 만듭니다. `description` 필드는 에이전트 프롬프트에 주입되어 LLM이 각 컴포넌트를 언제 사용할지 알 수 있게 하고, 스키마는 에이전트가 보내는 props를 검증합니다.

```ts title="lib/a2ui/definitions.ts"
import {z} from 'zod';

export const myDefinitions = {
  StatusBadge: {
    description: 'A colored status badge.',
    props: z.object({
      text: z.string(),
      variant: z.enum(['success', 'warning', 'error']).optional(),
    }),
  },
  Metric: {
    description: 'A key metric with label and value.',
    props: z.object({
      label: z.string(),
      value: z.string(),
      trend: z.enum(['up', 'down']).optional(),
    }),
  },
};

export type MyDefinitions = typeof myDefinitions;
```

#### 2. React 렌더러 만들기

각 정의를 React 컴포넌트에 매핑합니다. `createCatalog`는 정의 타입에 대해 generic이므로, 렌더러가 받는 props는 Zod 스키마에 맞춰 타입 검사됩니다. 따라서 `props.text`에 오타가 있으면 컴파일 오류가 납니다.

```tsx title="lib/a2ui/renderers.tsx"
'use client';

import {createCatalog, type CatalogRenderers} from '@copilotkit/a2ui-renderer';
import {myDefinitions, type MyDefinitions} from './definitions';

const myRenderers: CatalogRenderers<MyDefinitions> = {
  StatusBadge: ({props}) => {
    const colors = {
      success: {bg: '#dcfce7', text: '#166534'},
      warning: {bg: '#fef3c7', text: '#92400e'},
      error: {bg: '#fee2e2', text: '#991b1b'},
    };
    const c = colors[props.variant ?? 'success'];
    return (
      <span
        style={{
          padding: '2px 8px',
          borderRadius: 9999,
          fontSize: '0.75rem',
          background: c.bg,
          color: c.text,
        }}
      >
        {props.text}
      </span>
    );
  },

  Metric: ({props}) => (
    <div>
      <div style={{fontSize: '0.75rem', color: '#6b7280'}}>{props.label}</div>
      <div style={{fontSize: '1.5rem', fontWeight: 700}}>
        {props.value} {props.trend === 'up' ? '↑' : props.trend === 'down' ? '↓' : ''}
      </div>
    </div>
  ),
};

export const myCatalog = createCatalog(myDefinitions, myRenderers, {
  catalogId: 'my-app-catalog',
  includeBasicCatalog: true, // 내장 컴포넌트와 병합
});
```

`catalogId`는 에이전트가 이 카탈로그를 대상으로 삼을 때 사용하는 안정적인 핸들입니다. `includeBasicCatalog: true`는 자체 컴포넌트와 함께 내장 컴포넌트도 계속 사용할 수 있게 합니다(생략하면 _여러분의 컴포넌트만_ 렌더링합니다).

#### 3. CopilotKit에 카탈로그 전달

```tsx title="app/layout.tsx"
'use client';

import {CopilotKitProvider} from '@copilotkit/react-core/v2';
import '@copilotkit/react-core/v2/styles.css';
import {myCatalog} from '@/lib/a2ui/renderers';

export default function Layout({children}: {children: React.ReactNode}) {
  return (
    <CopilotKitProvider runtimeUrl="/api/copilotkit" a2ui={{catalog: myCatalog}}>
      {children}
    </CopilotKitProvider>
  );
}
```

이제 에이전트는 내장 컴포넌트와 함께 여러분의 커스텀 컴포넌트를 볼 수 있으며, 자신이 내보내는 모든 A2UI surface에서 이를 사용할 수 있습니다.

여러 카탈로그, 테마 hook, 고급 패턴을 포함한 전체 BYOC 레퍼런스는 CopilotKit의 [Custom Components (BYOC) 섹션](https://docs.copilotkit.ai/generative-ui/a2ui)을 참고하세요.

## 4. 고급 사용법

커스텀 카탈로그, 세밀한 제어, 고급 패턴 등 전체 A2UI 통합 표면은 CopilotKit의 [A2UI 문서](https://docs.copilotkit.ai/generative-ui/a2ui)를 참고하세요.

## 다음 단계

- **[A2UI Composer](https://a2ui-composer.ag-ui.com/)** — 위젯을 시각적으로 만듭니다.
- **[개념 › 전송 계층](../concepts/transports.md)** — A2UI가 AG-UI에 매핑되는 방식입니다.
- **[v0.9 사양](../specification/v0.9-a2ui.md)** — 기반 프로토콜입니다.
