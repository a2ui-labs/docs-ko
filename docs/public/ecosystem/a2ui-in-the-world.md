# 실제 환경에서의 A2UI (A2UI in the World)

A2UI는 Google 내부 팀과 파트너 조직에서 차세대 에이전트 주도 애플리케이션을 만들기 위해 채택되고 있습니다. 아래는 A2UI가 실제로 영향력을 발휘하고 있는 사례입니다.

## 프로덕션 배포

### Google Opal: 모두를 위한 AI 미니 앱

[Opal](http://opal.google)은 수십만 명의 사용자가 코딩 없이 자연어로 AI 미니 앱을 만들고, 수정하고, 공유할 수 있게 합니다.

**Opal의 A2UI 활용 방식:**

Google의 Opal 팀은 초기부터 **A2UI의 핵심 기여자**였습니다. 이들은 Opal의 AI 미니 앱을 가능하게 하는 동적 생성형 UI 시스템을 구동하기 위해 A2UI를 사용합니다.

- **빠른 프로토타이핑**: 새로운 UI 패턴을 빠르게 빌드하고 테스트합니다.
- **사용자 생성 앱**: 누구나 커스텀 UI가 포함된 앱을 만들 수 있습니다.
- **동적 인터페이스**: 각 사용 사례에 맞게 UI가 자동으로 조정됩니다.

> "A2UI는 우리 작업의 기반입니다. 고정된 프런트엔드에 얽매이지 않고 AI가 새로운 방식으로 사용자 경험을 주도할 수 있는 유연성을 제공합니다. 선언적 특성과 보안 중심 설계 덕분에 빠르고 안전하게 실험할 수 있습니다."
>
> **— Dimitri Glazkov**, 수석 엔지니어(Principal Engineer), Opal 팀

**자세히 알아보기:** [opal.google](http://opal.google)

---

### Gemini Enterprise: 비즈니스를 위한 커스텀 에이전트

Gemini Enterprise는 기업이 특정 워크플로와 데이터에 맞춘 강력한 커스텀 AI 에이전트를 만들 수 있게 해 줍니다.

**Gemini Enterprise의 A2UI 활용 방식:**

단순한 텍스트 응답을 넘어 복잡한 워크플로를 통해 직원을 안내할 수 있도록, 기업용 에이전트가 비즈니스 애플리케이션 내에서 **풍부하고 상호작용적인 UI**를 렌더링할 수 있게 A2UI가 통합되고 있습니다.

- **데이터 입력 폼**: 구조화된 데이터 수집을 위한 AI 생성 폼
- **승인 대시보드**: 검토 및 승인 프로세스를 위한 동적 UI
- **워크플로 자동화**: 복잡한 작업을 위한 단계별 인터페이스
- **커스텀 엔터프라이즈 UI**: 산업별 요구사항에 맞춘 맞춤형 인터페이스

> "고객들은 에이전트가 단순히 질문에 답하는 것을 넘어 복잡한 워크플로를 통해 직원을 안내하기를 원합니다. A2UI를 통해 Gemini Enterprise에서 개발하는 개발자들은 데이터 입력 폼부터 승인 대시보드까지 어떤 작업에든 필요한 동적 커스텀 UI를 에이전트가 생성하도록 할 수 있으며, 워크플로 자동화를 획기적으로 가속화할 수 있습니다."
>
> **— Fred Jabbour**, 프로덕트 매니저, Gemini Enterprise

> **참고:** Gemini Enterprise에서 A2UI 렌더링을 사용하려면 현재 Agent-to-Agent(A2A) 경로(`a2aAgentDefinition`)로 등록된 셀프서브 에이전트가 필요합니다. Vertex AI Agent Engine에 배포되고 `adkAgentDefinition` 경로로 등록된 관리형(managed) 에이전트는 현재 A2UI 렌더링을 지원하지 않습니다.

**자세히 알아보기:** [Gemini Enterprise](https://cloud.google.com/gemini)

---

### Flutter GenUI SDK: 모바일을 위한 생성형 UI

[Flutter GenUI SDK](https://docs.flutter.dev/ai/genui)는 모바일, 데스크톱, 웹 전반의 Flutter 애플리케이션에 동적인 AI 생성 UI를 제공합니다.

**GenUI의 A2UI 활용 방식:**

GenUI SDK는 서버 측 에이전트와 Flutter 애플리케이션 간 통신을 위한 **기반 프로토콜로 A2UI**를 사용합니다. GenUI를 사용하면 내부적으로 A2UI를 사용하는 것입니다.

- **크로스플랫폼 지원**: 동일한 에이전트가 iOS, Android, 웹, 데스크톱에서 동작합니다.
- **네이티브 성능**: 각 플랫폼에서 Flutter 위젯이 네이티브로 렌더링됩니다.
- **브랜드 일관성**: 앱의 디자인 시스템과 어울리는 UI를 제공합니다.
- **서버 주도 UI**: 앱 업데이트 없이 에이전트가 표시 내용을 제어합니다.

> "개발자들이 Flutter를 선택하는 이유는 모든 플랫폼에서 훌륭하게 느껴지는 표현력 풍부하고 브랜드가 살아있는 커스텀 디자인 시스템을 빠르게 만들 수 있기 때문입니다. A2UI는 모든 플랫폼의 모든 사용자에게 고품질 네이티브 감각의 경험을 보장하기 때문에 Flutter의 GenUI SDK에 완벽하게 들어맞았습니다."
>
> **— Vijay Menon**, 엔지니어링 디렉터, Dart & Flutter

**체험해 보기:**

- [GenUI 문서](https://docs.flutter.dev/ai/genui)
- [시작하기 영상](https://www.youtube.com/watch?v=nWr6eZKM6no)
- [Verdure 예제](https://github.com/flutter/genui/tree/main/examples/verdure) (클라이언트-서버 A2UI 샘플)

---

### Google ADK: Agent Development Kit

[Agent Development Kit](https://google.github.io/adk-docs/)(ADK)는 AI 에이전트를 만들고 배포하기 위한 Google의 오픈 소스 프레임워크입니다. 내장 개발자 UI인 [ADK Web](https://github.com/google/adk-web)은 네이티브 A2UI 렌더링을 포함합니다.

**ADK의 A2UI 활용 방식:**

ADK는 A2UI v0.8 기본 카탈로그를 통합하여 사양을 준수하는 에이전트 파트를 채팅 내에서 직접 네이티브 UI 컴포넌트로 자동 렌더링합니다. 또한 ADK는 A2UI↔A2A 메시지 변환을 처리하므로, ADK로 구축한 에이전트는 모든 A2UI 지원 클라이언트에 풍부한 UI를 전송할 수 있습니다.

- **내장 렌더링**: ADK Web이 개발 UI에서 A2UI 컴포넌트를 네이티브로 렌더링합니다.
- **A2A 통합**: A2UI 메시지가 A2A DataPart 메타데이터와 ADK 이벤트 사이에서 변환됩니다.
- **Agent SDK**: [A2UI Python 에이전트 SDK](https://github.com/a2ui-project/a2ui/tree/main/python/a2ui_agent)는 에이전트에서 A2UI를 생성하기 위한 ADK 확장을 제공합니다.

**체험해 보기:**

- [ADK 문서](https://google.github.io/adk-docs/)
- [ADK Web](https://github.com/google/adk-web) (A2UI를 지원하는 개발자 UI)
- [에이전트 개발 가이드](../guides/agent-development.md) (ADK로 A2UI 에이전트 구축하기)

---

## 파트너 통합

### AG-UI / CopilotKit: 풀스택 에이전틱 프레임워크

[AG-UI](https://ag-ui.com/)는 프로토콜을 제공하고, [CopilotKit](https://www.copilotkit.ai/)은 **출시 첫날부터 A2UI와 호환되는** 에이전틱 애플리케이션 구축용 주요 풀스택 프레임워크를 제공합니다.

**함께 동작하는 방식:**

AG-UI는 커스텀 프런트엔드와 전용 에이전트 사이에 고대역폭 연결을 만드는 데 뛰어납니다. 여기에 A2UI 지원을 추가함으로써 개발자는 두 가지 장점을 모두 얻을 수 있습니다.

- **상태 동기화**: AG-UI가 앱 상태와 채팅 기록을 처리합니다.
- **A2UI 렌더링**: 서드파티 에이전트의 동적 UI를 렌더링합니다.
- **멀티 에이전트 지원**: 여러 에이전트의 UI를 조율합니다.
- **프레임워크 통합**: CopilotKit을 통해 React, Vue, Angular 및 기타 앱 서피스를 통합합니다.

> "AG-UI는 커스텀 프런트엔드와 전용 에이전트 사이에 고대역폭 연결을 만드는 데 탁월합니다. A2UI 지원을 추가함으로써 우리는 개발자들에게 두 세계의 장점을 모두 제공하고 있습니다. 이제 서드파티 에이전트의 동적 UI도 A2UI를 통해 렌더링할 수 있는 풍부한 상태 동기화 애플리케이션을 구축할 수 있습니다. 멀티 에이전트 시대에 완벽한 조합입니다."
>
> **— Atai Barkai**, CopilotKit 및 AG-UI 창립자

**자세히 알아보기:**

- [AG-UI](https://ag-ui.com/)
- [CopilotKit](https://www.copilotkit.ai/)

---

### AG2: 네이티브 A2UI를 지원하는 멀티 에이전트 프레임워크

[AG2](https://ag2.ai/)는 고급 에이전트 오케스트레이션을 제공하는 인기 있는 멀티 에이전트 프레임워크입니다. AG2의 [A2UIAgent](https://docs.ag2.ai/latest/docs/user-guide/reference-agents/a2uiagent)는 네이티브 A2UI 지원을 갖춘 레퍼런스 에이전트로, AG2 에이전트가 A2A와 AG-UI 모두를 통해 제공되는 풍부하고 상호작용적인 UI를 생성할 수 있게 해 줍니다.

**AG2의 A2UI 활용 방식:**

A2UIAgent는 내장 A2UI 기능(프롬프트 엔지니어링, 재시도가 포함된 스키마 검증, 액션 처리)으로 AG2의 ConversableAgent를 확장하므로, 개발자는 별도의 커스텀 렌더링 코드 없이도 에이전트에 생성형 UI를 추가할 수 있습니다.

- **검증된 출력**: 내장 스키마 검증 및 재시도를 통해 신뢰할 수 있는 A2UI 생성을 보장합니다.
- **이중 전송 계층**: 동일한 UI를 A2A(JSON-RPC)와 AG-UI(SSE) 모두로 제공합니다.
- **크로스플랫폼**: 하나의 에이전트가 웹, 데스크톱, 모바일 클라이언트를 모두 지원합니다.
- **커스텀 카탈로그**: 도메인 특화 컴포넌트로 컴포넌트 카탈로그를 확장할 수 있습니다.

> "A2UIAgent는 A2UI 프로토콜을 AG2에 도입하여 동적이고 풍부하며 상호작용적인 UI로 에이전트를 표현할 수 있게 합니다. 신뢰할 수 있고 클라이언트에 구애받지 않는 렌더링 덕분에 개발자들은 통합에 드는 시간을 줄이고 훌륭한 경험을 만드는 데 더 많은 시간을 쏟을 수 있습니다."
>
> **— Mark Sze**, 창립 엔지니어(Founding Engineer), AG2

**자세히 알아보기:**

- [A2UIAgent 문서](https://docs.ag2.ai/latest/docs/user-guide/reference-agents/a2uiagent)
- [기술 심층 분석](https://docs.ag2.ai/latest/docs/blog/2026/03/20/AG2-A2UI/) — AG2로 A2UI 에이전트 구축하기
- [A2UIAgent + Flutter 예제](https://github.com/ag2ai/build-with-ag2/tree/main/a2ui/flutter) — A2A를 통해 Flutter GenUI 클라이언트로 제공되는 A2UIAgent
- [AG2](https://ag2.ai/)

---

### Google의 AI 기반 제품

Google이 전사적으로 AI를 도입함에 따라, A2UI는 AI 에이전트가 단순한 텍스트를 넘어 **사용자 인터페이스를 교환할 수 있는 표준화된 방법**을 제공합니다.

**사내 에이전트 도입:**

- **멀티 에이전트 워크플로**: 여러 에이전트가 동일한 서피스에 기여합니다.
- **원격 에이전트 지원**: 서로 다른 서비스에서 실행되는 에이전트가 UI를 제공할 수 있습니다.
- **표준화**: 팀 간 공통 프로토콜을 통해 통합 오버헤드를 줄입니다.
- **외부 노출**: 사내 에이전트를 외부(예: Gemini Enterprise)로 쉽게 노출할 수 있습니다.

> "A2A가 플랫폼에 관계없이 어떤 에이전트든 다른 에이전트와 대화할 수 있게 해 주는 것처럼, A2UI는 사용자 인터페이스 계층을 표준화하고 오케스트레이터를 통해 원격 에이전트 사용 사례를 지원합니다. 이는 사내 팀에게 엄청난 힘을 실어주었으며, 풍부한 사용자 인터페이스가 예외가 아닌 기본이 되는 에이전트를 빠르게 개발할 수 있게 해 주었습니다. Google이 생성형 UI로 더 나아감에 따라 A2UI는 어떤 클라이언트에서도 렌더링되는 서버 주도 UI를 위한 완벽한 플랫폼을 제공합니다."
>
> **— James Wren**, 시니어 스태프 엔지니어(Senior Staff Engineer), AI Powered Google

---

## 커뮤니티 프로젝트

A2UI 커뮤니티는 흥미로운 프로젝트들을 만들어 가고 있습니다:

### 오픈 소스 예제

- **Restaurant Finder** ([samples/agent/adk/restaurant_finder](https://github.com/a2ui-project/a2ui/tree/main/samples/agent/adk/restaurant_finder))
    - 동적 폼을 포함한 테이블 예약
    - Gemini 기반 에이전트
    - 전체 소스 코드 제공

- **Component Gallery** ([samples/client/angular - gallery mode](https://github.com/a2ui-project/a2ui/tree/main/samples/client/angular))
    - 모든 컴포넌트의 인터랙티브 쇼케이스
    - 코드가 포함된 실시간 예제
    - 학습용으로 적합

### 서드파티 통합

- **[json-render](https://json-render.dev/docs/a2ui)** — Zod 스키마를 통해 A2UI 컴포넌트 카탈로그를 렌더링하는 Vercel의 React 라이브러리입니다. [json-render vs. A2UI 비교](https://dipjyotimetia.medium.com/vercels-json-render-vs-google-s-a2ui-the-head-to-head-6f213cf1a23b)를 참고하세요.
- **[OpenClaw Canvas](https://docs.openclaw.ai/platforms/mac/canvas)** — OpenClaw는 캔버스 시스템을 통해 연결된 기기에서 에이전트가 생성한 UI를 렌더링하는 데 A2UI를 사용합니다. [아키텍처 개요](https://ppaolo.substack.com/p/openclaw-system-architecture-overview)를 참고하세요.

### 커뮤니티 기여

A2UI로 무언가를 만드셨나요? [커뮤니티와 공유해 주세요!](community.md)

---

## 다음 단계

자세한 내용은 다음 리소스를 참고하세요:

- [빠른 시작 가이드](../quickstart.md) - 데모를 실행해 보세요.
- [에이전트 개발](../guides/agent-development.md) - 에이전트를 만들어 보세요.
- [클라이언트 설정](../guides/client-setup.md) - 렌더러를 통합해 보세요.
- [커뮤니티](community.md) - 커뮤니티에 참여하세요.

---

**프로덕션에서 A2UI를 사용하고 계신가요?** [GitHub Discussions](https://github.com/a2ui-project/a2ui/discussions)에서 여러분의 이야기를 들려주세요.
