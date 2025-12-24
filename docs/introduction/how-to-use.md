# A2UI를 어떻게 사용할 수 있나요?

여러분의 역할과 활용 사례에 맞는 통합 경로를 선택하세요.

## 세 가지 경로

### 경로 1: 호스트 애플리케이션 구축 (프론트엔드)

기존 앱에 A2UI 렌더링 기능을 통합하거나 에이전트 기반의 새로운 프론트엔드를 구축합니다.

**렌더러 선택:**

- **웹:** Lit, Angular
- **모바일/데스크톱:** Flutter GenUI SDK
- **React:** 2026년 1분기 예정

**빠른 설정:**

Angular 앱을 사용하는 경우, Angular 렌더러를 추가할 수 있습니다:

```bash
npm install @a2ui/angular 
```

에이전트 메시지(SSE, WebSockets 또는 A2A)에 연결하고 브랜드에 맞게 스타일을 커스터마이징하세요.

**다음 단계:** [클라이언트 설정 가이드](../guides/client-setup.md) | [테마 설정](../guides/theming.md)

---

### 경로 2: 에이전트 구축 (백엔드)

모든 호환 가능한 클라이언트를 위해 A2UI 응답을 생성하는 에이전트를 만듭니다.

**프레임워크 선택:**

- **Python:** Google ADK, LangChain, 커스텀 구현
- **Node.js:** A2A SDK, Vercel AI SDK, 커스텀 구현

LLM 프롬프트에 A2UI 스키마를 포함하고, JSONL 메시지를 생성하여 SSE, WebSockets 또는 A2A를 통해 클라이언트로 스트리밍합니다.

**다음 단계:** [에이전트 개발 가이드](../guides/agent-development.md)

---

### 경로 3: 기존 프레임워크 활용

이미 A2UI를 지원하는 프레임워크를 통해 사용합니다:

- **[AG UI / CopilotKit](https://ag-ui.com/)** - A2UI 렌더링 기능이 포함된 풀스택 React 프레임워크
- **[Flutter GenUI SDK](https://docs.flutter.dev/ai/genui)** - 크로스 플랫폼 생성형 UI (내부적으로 A2UI 사용)

**다음 단계:** [에이전트 UI 생태계](agent-ui-ecosystem.md) | [A2UI는 어디에서 사용되나요?](where-is-it-used.md)
