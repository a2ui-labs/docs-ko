# 모든 에이전트 프레임워크에서 A2UI 사용하기(AG-UI 사용)

A2UI는 선언적 UI 형식입니다. [AG-UI](https://ag-ui.com/)는 에이전트와 브라우저 사이에서 A2UI 메시지를 운반하는 전송 계층입니다. CopilotKit의 AG-UI 구현은 현재 사용자 앞에 A2UI를 배치하는 가장 빠른 경로입니다. CopilotKit이 지원하는 모든 에이전트 프레임워크(ADK, LangGraph, CrewAI, Mastra, 커스텀 Python/TS 서비스 등)는 별도의 전송 glue 없이 A2UI를 내보내고 React 앱에서 렌더링할 수 있습니다.

!!! info "원본 기준"

    이 가이드는 CopilotKit의 [ADK + A2UI 문서](https://docs.copilotkit.ai/adk/generative-ui/a2ui)의 핵심 단계를 반영합니다. 최신 API 표면은 CopilotKit 문서를 참고하세요.

## 1. CopilotKit 설정

선택한 프레임워크(ADK, LangGraph, CrewAI, Mastra 등)와 함께 React/Next.js 앱에 CopilotKit을 설치합니다.

```bash
npx copilotkit@latest init
```

또는 [CopilotKit quickstart](https://docs.copilotkit.ai/quickstart)를 따라 기존 프로젝트에 연결합니다. 이것은 표준 CopilotKit 설정이며, A2UI 전용 scaffold는 아닙니다.

## 2. A2UI 활성화

### Backend

`CopilotRuntime`에서 A2UI를 켜고 `render_a2ui` 도구를 주입하여 에이전트가 A2UI surface를 생성할 수 있게 합니다.

```ts title="app/api/copilotkit/route.ts"
import {CopilotRuntime} from '@copilotkit/runtime';

const runtime = new CopilotRuntime({
  agents: {default: myAgent},
  a2ui: {injectA2UITool: true},
});
```

특정 에이전트로 범위를 제한하려면 `a2ui: { injectA2UITool: true, agents: ["my-agent"] }`를 사용합니다.

### Frontend

A2UI 렌더러는 자동으로 활성화됩니다. 선택적으로 테마를 전달할 수 있습니다.

{% raw %}

```tsx
import {CopilotKitProvider} from '@copilotkit/react-core/v2';
import '@copilotkit/react-core/v2/styles.css';
import {myCustomTheme} from '@copilotkit/a2ui-renderer';

<CopilotKitProvider runtimeUrl="/api/copilotkit" a2ui={{theme: myCustomTheme}}>
  {children}
</CopilotKitProvider>;
```

{% endraw %}

### 커스텀 컴포넌트(BYOC)

A2UI는 즉시 동작하는 surface를 만들 수 있도록 내장 카탈로그(Text, Image, Card 등)를 제공합니다. 진짜 힘은 여기에 _여러분의_ React 컴포넌트를 확장하는 데 있습니다. 즉, 여러분의 디자인 시스템과 데이터 형태를 사용해, 이미 신뢰하는 primitive로 에이전트가 인터페이스를 구성하게 할 수 있습니다. 카탈로그는 세 부분으로 구성됩니다.

1. **정의** — Zod 스키마와 자연어 설명입니다. 에이전트가 시스템 프롬프트에서 보게 되는 내용입니다.
2. **렌더러** — 정의마다 하나씩 있는 타입 지정 React 컴포넌트입니다. 사용자가 보게 되는 내용입니다.
3. **등록** — provider를 통해 카탈로그를 전달하여 A2UI 렌더러가 컴포넌트를 그리는 방법을 알게 합니다.

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

각 정의를 React 컴포넌트에 매핑합니다. `createCatalog`는 정의 타입에 대해 generic이므로, 렌더러가 받는 props는 Zod 스키마에 맞춰 타입 검사됩니다. `props.text`에 오타가 있으면 컴파일 오류가 납니다.

{% raw %}

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

{% endraw %}

`catalogId`는 에이전트가 이 카탈로그를 대상으로 삼을 때 사용하는 안정적인 핸들입니다. `includeBasicCatalog: true`는 자체 컴포넌트와 함께 내장 컴포넌트도 계속 사용할 수 있게 합니다. 이 값을 생략하면 _여러분의 컴포넌트만_ 렌더링합니다.

#### 3. CopilotKit에 카탈로그 전달

{% raw %}

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

{% endraw %}

이제 에이전트는 내장 컴포넌트와 함께 여러분의 커스텀 컴포넌트를 볼 수 있으며, 자신이 내보내는 모든 A2UI surface에서 이를 사용할 수 있습니다.

여러 카탈로그, 테마 hook, 고급 패턴을 포함한 전체 BYOC 레퍼런스는 CopilotKit의 [Custom Components (BYOC) 섹션](https://docs.copilotkit.ai/adk/generative-ui/a2ui#custom-components-byoc)을 참고하세요.

## 3. 고급 사용법

커스텀 카탈로그, 세밀한 제어, 고급 패턴 등 전체 A2UI 통합 표면은 CopilotKit의 [A2UI 문서](https://docs.copilotkit.ai/generative-ui/a2ui)를 참고하세요.

## 다음 단계

- **[A2UI Composer](https://a2ui-composer.ag-ui.com/)** — 위젯을 시각적으로 만듭니다.
- **[개념 › 전송 계층](../concepts/transports.md)** — A2UI가 AG-UI에 매핑되는 방식입니다.
- **[v0.9 사양](../specification/v0.9-a2ui.md)** — 기반 프로토콜입니다.
