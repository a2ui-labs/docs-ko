# 생태계 렌더러

커뮤니티와 서드파티 A2UI 렌더러 구현을 소개합니다.

> **참고**
>
> 아래 렌더러들은 A2UI 팀이 아니라 각 프로젝트의 유지보수자가 관리합니다. 호환성, 지원 버전, 유지 상태는 각 프로젝트를 확인하세요.

> **팁**
>
> **공식** A2UI React 렌더러를 찾고 계신가요? A2UI 팀이 관리하는 코어 A2UI React 렌더러인 [`@a2ui/react`](https://www.npmjs.com/package/@a2ui/react)를 확인하세요.

## 커뮤니티 렌더러

| 렌더러                                             | 플랫폼                                              | v0.8 | v0.9 | 활동                                                                                                                                                                                                                                      | 링크                                                                                                                                                                                           |
| -------------------------------------------------- | --------------------------------------------------- | ---- | ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **easyops-cn/a2ui-sdk** (`@a2ui-sdk/react`)        | React(Web)                                          | ✅   | ❌   | ![Stars](https://img.shields.io/github/stars/easyops-cn/a2ui-sdk?style=flat-square&label=⭐) ![Last commit](https://img.shields.io/github/last-commit/easyops-cn/a2ui-sdk?style=flat-square&label=updated)                                    | [GitHub](https://github.com/easyops-cn/a2ui-sdk) · [npm](https://www.npmjs.com/package/@a2ui-sdk/react) · [Docs](https://a2ui-sdk.js.org/)                                                      |
| **lmee/A2UI-Android**                              | Android(Compose)                                    | ✅   | ❌   | ![Stars](https://img.shields.io/github/stars/lmee/A2UI-Android?style=flat-square&label=⭐) ![Last commit](https://img.shields.io/github/last-commit/lmee/A2UI-Android?style=flat-square&label=updated)                                        | [GitHub](https://github.com/lmee/A2UI-Android)                                                                                                                                                  |
| **sivamrudram-eng/a2ui-react-native**              | React Native                                         | ✅   | ❌   | ![Stars](https://img.shields.io/github/stars/sivamrudram-eng/a2ui-react-native?style=flat-square&label=⭐) ![Last commit](https://img.shields.io/github/last-commit/sivamrudram-eng/a2ui-react-native?style=flat-square&label=updated)        | [GitHub](https://github.com/sivamrudram-eng/a2ui-react-native)                                                                                                                                  |
| **zhama/a2ui**                                     | React(Web)                                          | ✅   | ❌   | —                                                                                                                                                                                                                                             | [npm](https://www.npmjs.com/package/@zhama/a2ui)                                                                                                                                                |
| **jem-computer/A2UI-react**                        | React(Web)                                          | ✅   | ❌   | ![Stars](https://img.shields.io/github/stars/jem-computer/A2UI-react?style=flat-square&label=⭐) ![Last commit](https://img.shields.io/github/last-commit/jem-computer/A2UI-react?style=flat-square&label=updated)                            | [GitHub](https://github.com/jem-computer/A2UI-react)                                                                                                                                            |
| **BBC6BAE9/a2ui-swift**                            | Apple(iOS, iPadOS, macOS, tvOS, watchOS, visionOS) | ✅   | ✅   | ![Stars](https://img.shields.io/github/stars/BBC6BAE9/a2ui-swift?style=flat-square&label=⭐) ![Last commit](https://img.shields.io/github/last-commit/BBC6BAE9/a2ui-swift?style=flat-square&label=updated)                                    | [GitHub](https://github.com/BBC6BAE9/a2ui-swift)                                                                                                                                                |
| **TanXudong-Vivo/A2UI-Android-Renderer**           | Android(Jetpack Compose)                            | ❌   | ✅   | ![Stars](https://img.shields.io/github/stars/TanXudong-Vivo/A2UI-Android-Renderer?style=flat-square&label=⭐) ![Last commit](https://img.shields.io/github/last-commit/TanXudong-Vivo/A2UI-Android-Renderer?style=flat-square&label=updated)  | [GitHub](https://github.com/TanXudong-Vivo/A2UI-Android-Renderer)                                                                                                                               |
| **a2ui-vue**                                       | Vue(Web)                                            | ✅   | ✅   | ![Stars](https://img.shields.io/github/stars/shawnwang15/a2ui-vue?style=flat-square&label=⭐) ![Last commit](https://img.shields.io/github/last-commit/shawnwang15/a2ui-vue?style=flat-square&label=updated)                                  | [GitHub](https://github.com/shawnwang15/a2ui-vue) · [npm](https://www.npmjs.com/package/a2ui-vue) · [Docs](https://shawnwang15.github.io/a2ui-vue/en/)                                          |
| **AGenUI/AGenUI**                                  | iOS, Android, HarmonyOS                             | ❌   | ✅   | ![Stars](https://img.shields.io/github/stars/AGenUI/AGenUI?style=flat-square&label=⭐) ![Last commit](https://img.shields.io/github/last-commit/AGenUI/AGenUI?style=flat-square&label=updated)                                                | [GitHub](https://github.com/AGenUI/AGenUI) · [공식 웹사이트](https://genui.amap.com/)                                                                                                        |
| **lynx-family/lynx-stack** (`@lynx-js/genui/a2ui`) | Lynx(모바일, 웹, 데스크톱)                         | ❌   | ✅   | ![Stars](https://img.shields.io/github/stars/lynx-family/lynx-stack?style=flat-square&label=⭐) ![Last commit](https://img.shields.io/github/last-commit/lynx-family/lynx-stack?path=packages%2Fgenui%2Fa2ui&style=flat-square&label=updated) | [GitHub](https://github.com/lynx-family/lynx-stack/tree/main/packages/genui/a2ui) · [npm](https://www.npmjs.com/package/@lynx-js/genui) · [Docs](https://lynxjs.org/next/react/genui/a2ui.html) |
| **BoteAI/a2ui** (`@boteai/a2ui-render`)            | React(Web)                                          | ✅   | ✅   | ![Stars](https://img.shields.io/github/stars/BoteAI/a2ui?style=flat-square&label=⭐) ![Last commit](https://img.shields.io/github/last-commit/BoteAI/a2ui?style=flat-square&label=updated)                                                    | [GitHub](https://github.com/BoteAI/a2ui) · [npm](https://www.npmjs.com/package/@boteai/a2ui-render)                                                                                             |
| **kokoro-ele/a2ui-ink** (`@evanyu/a2ui-ink`)       | 터미널 / CLI(Ink)                                   | ❌   | ✅   | ![Stars](https://img.shields.io/github/stars/kokoro-ele/a2ui-ink?style=flat-square&label=⭐) ![Last commit](https://img.shields.io/github/last-commit/kokoro-ele/a2ui-ink?style=flat-square&label=updated)                                    | [GitHub](https://github.com/kokoro-ele/a2ui-ink) · [npm](https://www.npmjs.com/package/@evanyu/a2ui-ink)                                                                                        |
| **yessGlory17/generative-mui** (`@yessglory/generative-mui-react`) | React + Material UI(Web)                | ❌   | ✅   | ![Stars](https://img.shields.io/github/stars/yessGlory17/generative-mui?style=flat-square&label=⭐) ![Last commit](https://img.shields.io/github/last-commit/yessGlory17/generative-mui?style=flat-square&label=updated)                      | [GitHub](https://github.com/yessGlory17/generative-mui) · [npm](https://www.npmjs.com/package/@yessglory/generative-mui-react)                                                                  |

### 주목할 만한 프로젝트

다음은 초기 단계이거나 실험적인 프로젝트입니다.

- **[xpert-ai/a2ui-react](https://www.npmjs.com/package/@xpert-ai/a2ui-react)** (`@xpert-ai/a2ui-react`) — ShadCN UI 컴포넌트를 사용하는 React 렌더러 (v0.0.1, 2026년 1월 공개).
- **[josh-english-2k18/a2ui-3d-renderer](https://github.com/josh-english-2k18/a2ui-3d-renderer)** — A2UI용 실험적 Three.js/WebGL 3D 렌더러 (~2 stars).
- **[AINative-Studio/ai-kit-a2ui](https://github.com/AINative-Studio/ai-kit-a2ui)** — AIKit 프레임워크용 React + ShadCN 렌더러 (~2 stars).

### 관련 프로젝트

다음 프로젝트들은 A2UI 렌더러는 아니지만 A2UI와 밀접하게 관련되어 있으며 A2UI를 지원합니다.

| 프로젝트                                       | 플랫폼                                   | 설명                                                                                                                                                                                                       | 링크                                                                                                                                              |
| ----------------------------------------------- | ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **vercel-labs/json-render** (`@json-render/*`) | React, Vue, Svelte, Solid, React Native | Vercel의 생성형 UI 프레임워크로, A2UI 프로토콜이 아닌 자체 JSON 스키마와 Zod 기반 컴포넌트 카탈로그를 사용합니다. 스트리밍, 36개의 사전 제작된 shadcn/ui 컴포넌트, 크로스플랫폼 렌더링을 지원합니다. | [GitHub](https://github.com/vercel-labs/json-render) · [npm](https://www.npmjs.com/package/@json-render/core) · [Docs](https://json-render.dev/) |

### 생태계 유틸리티

- **[@a2ui/markdown-it](https://www.npmjs.com/package/@a2ui/markdown-it)** — 모든 렌더러의 Text 위젯에서 마크다운 렌더링을 지원합니다.

### 하이라이트

**easyops-cn/a2ui-sdk** (`@a2ui-sdk/react`)는 가장 완성도 높은 커뮤니티 React 렌더러로, 11개의 게시된 버전과 Radix UI 프리미티브, Tailwind CSS 스타일링, 전용 문서 사이트를 갖추고 있습니다. [A2UI Discussions에서 공지](https://github.com/a2ui-project/a2ui/discussions/489)되었습니다. 공식 A2UI React 렌더러는 [`@a2ui/react`](https://www.npmjs.com/package/@a2ui/react)를 참고하세요.

**lmee/A2UI-Android**는 중요한 공백을 메웁니다 — 현재 유일한 Jetpack Compose 렌더러로, Android 5.0 이상을 지원하며 20개 이상의 컴포넌트, 데이터 바인딩, 접근성 지원을 갖추고 있습니다.

**sivamrudram-eng/a2ui-react-native**는 유일한 React Native 렌더러로, 하나의 코드베이스로 iOS와 Android에서 A2UI를 사용할 수 있게 해 줍니다.

**BBC6BAE9/a2ui-swift**는 A2UI를 위한 Swift 기반의 명세 준수 네이티브 Apple 렌더러입니다. 공유 `A2UISwiftCore` 레이어를 통해 SwiftUI, UIKit, AppKit을 지원하며 iOS, iPadOS, macOS, tvOS, visionOS, watchOS에서 동작합니다. 이 프로젝트는 공식 A2UI 명세 및 Apple 네이티브 플랫폼 동작과의 긴밀한 정합성에 중점을 두고 A2UI v0.8, v0.9, v0.9.1을 지원합니다. 서로 다른 플랫폼 간의 픽셀 단위 동일성보다는 명세 수준의 상호 운용성과 네이티브 플랫폼 표현을 우선시합니다. 기본 렌더링은 Human Interface Guidelines에 맞춘 네이티브 Apple 컨트롤과 플랫폼에 적합한 상호작용 패턴을 사용하며, 앱은 A2UI 카탈로그, 테마 및 커스텀 컴포넌트를 통해 스타일과 동작을 맞춤 설정할 수 있습니다.

**TanXudong-Vivo/A2UI-Android-Renderer**는 Jetpack Compose와 Material 3로 만들어진 모듈형 Android 렌더러이며, A2UI v0.9 프로토콜을 지원하는 최초의 Android 구현체입니다. 13개의 완전히 구현된 컴포넌트(Coil을 통한 Image 로딩 포함), LLM 토큰 스트림으로부터의 스트리밍 렌더링, `path` 표현식과 `formatDate`를 이용한 데이터 바인딩, 추가 컴포넌트 유형을 등록할 수 있는 플러그형 Custom Catalog를 지원합니다. 데모 앱에는 공식 `restaurant_finder` ADK 에이전트와의 실시간 연결이 포함되어 있습니다.

**AGenUI/AGenUI**는 A2UI v0.9를 위한 크로스플랫폼 네이티브 렌더러로, 공유 C++ 코어를 통해 iOS, Android, HarmonyOS를 지원합니다. 높은 성능, 확장성, 크로스플랫폼 일관성을 목표로 설계되었습니다. AGenUI는 A2UI v0.9를 완전히 구현하며, UI 컴포넌트와 함수 호출을 확장하기 위한 런타임 API를 제공합니다. 또한 [커스텀 Catalog](https://github.com/AGenUI/AGenUI/blob/main/agenui_catalog.json)를 도입해 Table, Carousel, Web, RichText와 더 풍부한 외관 및 레이아웃 제어를 위한 Styles 속성으로 Basic Catalog를 확장하면서도, A2UI의 확장 가능한 Catalog 모델과의 정합성을 유지합니다. [컴포넌트 데모](https://genui.amap.com/components)와 관련 오픈소스 [A2UI Generation Skill](https://github.com/AGenUI/AGenUI/tree/main/skills/a2ui-generation)을 확인하고, [GitHub](https://github.com/AGenUI/AGenUI)에서 더 자세히 알아보세요.

**lynx-family/lynx-stack** (`@lynx-js/genui/a2ui`)는 A2UI v0.9를 위한 ReactLynx 렌더러를 제공합니다. `MessageStore`를 통해 검증된 서버-클라이언트 A2UI 메시지를 소비하고, 호출자가 제공한 카탈로그로부터 승인된 ReactLynx 컴포넌트를 렌더링하며, 생성된 UI 액션을 `onAction`을 통해 전달합니다. 게시된 `@lynx-js/genui` 패키지는 `a2ui` 서브패스를 통해 A2UI 렌더러를 노출하며, GenUI CLI는 빌드 타임 카탈로그 아티팩트 및 A2UI 시스템 프롬프트 생성을 지원합니다.

**yessGlory17/generative-mui** (`@yessglory/generative-mui-react`)는 A2UI(v0.9.1)를 위한 **Material UI** 렌더러입니다. Basic Catalog의 18개 컴포넌트 전체를 MUI에 1:1로 매핑하고, **호스트 앱에 이미 있는 `<ThemeProvider>` 내부에서** 렌더링합니다 — 서피스 자체는 별도 테마를 갖지 않고 호스트의 팔레트/타이포그래피를 그대로 상속받으며, 테마가 바뀌면 처음부터 끝까지 다시 스킨됩니다. 프레임워크에 종속되지 않는 코어(`@yessglory/generative-mui-core` — Zod 스키마, JSONL 파서, JSON Pointer, 구독 가능한 결정론적 `SurfaceStore`, 프로바이더에 종속되지 않는 에이전트 도구 스키마)와 React/MUI 어댑터로 분리되어 있으며, `react → core` 단방향 의존성은 lint 단계에서 강제됩니다. Basic Catalog 외에도 옵트인 방식의 Extended Catalog(`@mui/x-charts` 기반 차트, Table 등), 양방향 데이터 바인딩, `checks` 기반 검증, 스트리밍 복원력(점진적 스켈레톤, append-only 문자열 델타, 순환/깊이 및 노드별 오류 경계), 보안 가드(등록되지 않은 타입은 절대 실행되지 않음, 에이전트가 보낸 `sx`/`style`/`className`은 제거됨, 로컬 `regex`에 대한 ReDoS 길이 제한)를 제공합니다.

## 렌더러 제출하기

A2UI 렌더러를 만들었다면 이 목록에 추가해 보세요.

### 제출 방법

렌더러를 제출하려면 다음 단계를 따르세요.

1. [a2ui-project/a2ui](https://github.com/a2ui-project/a2ui) 저장소를 **포크(Fork)**합니다.
2. 이 파일(`docs/ecosystem/renderers.md`)을 **수정**해 커뮤니티 렌더러 표에 렌더러 이름, 플랫폼, npm 패키지(있는 경우), 지원 버전, 소스 링크를 담은 행을 추가합니다.
3. 렌더러에 대한 짧은 설명과 함께 `a2ui-project/a2ui`에 **PR을 엽니다**.
4. **[GitHub Discussions](https://github.com/a2ui-project/a2ui/discussions)에 게시**해 커뮤니티에 무엇을 만들었는지 알려주세요! 짧은 데모 영상이 큰 도움이 됩니다.

영감이 필요하신가요? 저장소의 **[커뮤니티 샘플](https://github.com/a2ui-project/a2ui/tree/main/samples)**을 살펴보세요 — Angular, Lit, ADK 기반 에이전트를 다루고 있어 좋은 출발점이 됩니다.

### 좋은 커뮤니티 렌더러의 조건은 무엇인가요?

다음 기준을 충족하면 목록에 등재되고 사용될 가능성이 높아집니다.

- **공개된 소스 코드**를 보유하고 있습니다 (오픈소스, MIT 또는 Apache 2.0 라이선스 권장).
- 지원하는 **A2UI 사양 버전**(v0.8, v0.9, 또는 둘 다)을 명확히 명시합니다.
- 다른 A2UI 렌더러들의 **기본 컴포넌트**(텍스트, 버튼, 입력, 기본 레이아웃 컴포넌트 등)를 지원합니다.
- 설치 방법과 최소한의 사용 예제가 담긴 **README**를 포함합니다.
- **활발히 유지보수**되고 있습니다 — 더 이상 지원하지 않는다면 archived로 표시해 주세요.

커뮤니티 렌더러가 목록에 오르기 위해 프로덕션 수준일 필요는 없습니다 — 실험적이거나 초기 단계의 프로젝트도 주목할 만한 프로젝트 섹션에서 환영합니다.
