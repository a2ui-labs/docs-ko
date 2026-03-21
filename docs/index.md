---
hide:
  - toc
---

<!-- markdownlint-disable MD041 -->
<!-- markdownlint-disable MD033 -->
<div style="text-align: center; margin: 2rem 0 3rem 0;" markdown>

<img src="assets/A2UI_dark.svg" alt="A2UI Logo" width="120" class="light-mode-only" style="margin-bottom: 1rem;">
<img src="assets/A2UI_light.svg" alt="A2UI Logo" width="120" class="dark-mode-only" style="margin-bottom: 1rem;">

# 에이전트 주도 인터페이스를 위한 프로토콜

<p style="font-size: 1.2rem; max-width: 800px; margin: 0 auto 1rem auto; opacity: 0.9; line-height: 1.6;">
A2UI는 AI 에이전트가 임의의 코드를 실행하지 않고도 웹, 모바일, 데스크톱 전반에서 네이티브하게 렌더링되는 풍부하고 상호작용적인 사용자 인터페이스를 생성할 수 있도록 합니다.
</p>

</div>

## 명세 버전

| 버전 | 상태 | 설명 |
|------|------|------|
| **[v0.8](specification/v0.8-a2ui.md)** | **안정판** | 현재 프로덕션 릴리스입니다. 서피스, 컴포넌트, 데이터 바인딩, 인접 리스트 모델을 포함합니다. |
| **[v0.9](specification/v0.9-a2ui.md)** | **초안** | `createSurface`, 클라이언트 측 함수, 커스텀 카탈로그, 확장 명세를 추가합니다. [진화 가이드 →](specification/v0.9-evolution-guide.md) |

A2UI는 Apache 2.0 라이선스로 제공되며, Google이 CopilotKit과 오픈 소스 커뮤니티의 기여를 받아 만들었고, [GitHub](https://github.com/google/A2UI)에서 활발히 개발 중입니다.

A2UI가 해결하는 문제는 다음과 같습니다: **AI 에이전트가 신뢰 경계를 넘어 풍부한 UI를 안전하게 전송하려면 어떻게 해야 하는가?**

텍스트만 반환하거나 위험한 코드를 실행하는 대신, A2UI는 에이전트가 클라이언트가 자체 네이티브 위젯으로 렌더링할 수 있는 **선언적 컴포넌트 설명**을 전송하게 합니다. 즉, 에이전트가 공통 UI 언어를 사용하는 셈입니다.

이 저장소에서는 다음을 확인할 수 있습니다.
- [A2UI 명세](specification/v0.8-a2ui.md)와 [v0.9 초안](specification/v0.9-a2ui.md)
- 클라이언트 측 [렌더러](reference/renderers.md) 구현
- 에이전트와 클라이언트 사이에서 A2UI 메시지를 전달하는 [전송 계층](concepts/transports.md)

<div class="grid cards" markdown>

- :material-shield-check: **설계부터 보안 중심**

    ---

    실행 가능한 코드가 아닌 선언적 데이터 형식입니다. 에이전트는 카탈로그에서 사전 승인된 컴포넌트만 사용할 수 있으므로 UI 주입 공격을 줄일 수 있습니다.

- :material-rocket-launch: **LLM 친화적**

    ---

    생성이 쉽도록 설계된 평면적이고 스트리밍 가능한 JSON 구조입니다. LLM은 한 번에 완벽한 JSON을 만들지 않아도 UI를 점진적으로 구성할 수 있습니다.

- :material-devices: **프레임워크 독립적**

    ---

    하나의 에이전트 응답이 여러 환경에서 작동합니다. 동일한 UI를 Angular, Flutter, React 또는 네이티브 모바일에서 여러분만의 스타일을 입힌 컴포넌트로 렌더링할 수 있습니다.

- :material-chart-timeline: **점진적 렌더링**

    ---

    UI 업데이트를 생성되는 즉시 스트리밍합니다. 사용자는 전체 응답이 끝날 때까지 기다리지 않고 실시간으로 인터페이스가 완성되는 모습을 볼 수 있습니다.

</div>

## 5분 만에 시작하기

<div class="grid cards" markdown>

- :material-clock-fast:{ .lg .middle } **[퀵스타트 가이드](quickstart.md)**

    ---

    레스토랑 찾기 데모를 실행하고 Gemini 기반 에이전트와 함께 동작하는 A2UI를 확인해 보세요.

    [:octicons-arrow-right-24: 시작하기](quickstart.md)

- :material-book-open-variant:{ .lg .middle } **[핵심 개념](concepts/overview.md)**

    ---

    서피스, 컴포넌트, 데이터 바인딩, 인접 리스트 모델을 이해합니다.

    [:octicons-arrow-right-24: 개념 배우기](concepts/overview.md)

- :material-code-braces:{ .lg .middle } **[개발자 가이드](guides/client-setup.md)**

    ---

    앱에 A2UI 렌더러를 통합하거나 UI를 생성하는 에이전트를 만들어 보세요.

    [:octicons-arrow-right-24: 개발 시작하기](guides/client-setup.md)

- :material-file-document:{ .lg .middle } **프로토콜 사양**

    ---

    전체 기술 사양과 메시지 유형을 확인해 보세요. [v0.8(안정판)](specification/v0.8-a2ui.md) · [v0.9(초안)](specification/v0.9-a2ui.md)

    [:octicons-arrow-right-24: 명세 읽기](specification/v0.8-a2ui.md)

</div>

## 동작 방식

1. **사용자**가 AI 에이전트에 메시지를 보냅니다.
2. **에이전트**가 UI 구조와 데이터를 설명하는 A2UI 메시지를 생성합니다.
3. **메시지**가 클라이언트 애플리케이션으로 스트리밍됩니다.
4. **클라이언트**가 네이티브 컴포넌트(Angular, Flutter, React 등)로 렌더링합니다.
5. **사용자**가 UI와 상호작용하고, 액션을 에이전트로 다시 보냅니다.
6. **에이전트**가 업데이트된 A2UI 메시지로 응답합니다.

![엔드-투-엔드 데이터 흐름](assets/end-to-end-data-flow.png)

## A2UI 실제 사례

### 조경 설계자 데모

<div style="margin: 2rem 0;">
  <div style="border-radius: .8rem; overflow: hidden; box-shadow: var(--md-shadow-z2);">
    <video width="100%" height="auto" controls playsinline style="display: block; aspect-ratio: 16/9; object-fit: cover;">
      <source src="assets/landscape-architect-demo.mp4" type="video/mp4">
      브라우저가 비디오 태그를 지원하지 않습니다.
    </video>
  </div>
  <p style="text-align: center; margin-top: 1rem; opacity: 0.8;">
    에이전트가 조경 설계 애플리케이션의 인터페이스 전체를 생성하는 과정을 확인해 보세요. 사용자가 사진을 업로드하면 에이전트가 Gemini로 이를 해석하고, 조경 요구 사항에 맞는 맞춤형 양식을 생성합니다.
  </p>
</div>

### 커스텀 컴포넌트: 대화형 차트 및 지도

<div style="margin: 2rem 0;">
  <div style="border-radius: .8rem; overflow: hidden; box-shadow: var(--md-shadow-z2);">
    <video width="100%" height="auto" controls playsinline style="display: block; aspect-ratio: 16/9; object-fit: cover;">
      <source src="assets/a2ui-custom-component.mp4" type="video/mp4">
      브라우저가 비디오 태그를 지원하지 않습니다.
    </video>
  </div>
  <p style="text-align: center; margin-top: 1rem; opacity: 0.8;">
    에이전트가 수치 요약 질문에는 차트 컴포넌트를, 위치 질문에는 Google 지도 컴포넌트를 선택해 응답하는 과정을 확인해 보세요. 두 컴포넌트 모두 클라이언트가 제공하는 커스텀 컴포넌트입니다.
  </p>
</div>

### A2UI Composer

CopilotKit의 공개 [A2UI 위젯 빌더](https://go.copilotkit.ai/A2UI-widget-builder)도 체험해 볼 수 있습니다.

[![A2UI Composer](assets/A2UI-widget-builder.png)](https://go.copilotkit.ai/A2UI-widget-builder)
