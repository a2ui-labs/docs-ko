# 클라이언트 설정 가이드

여러분의 플랫폼에 맞는 렌더러를 사용하여 애플리케이션에 A2UI를 통합하세요.

## 렌더러

| 렌더러                                                                                    | 플랫폼             | v0.8 | v0.9 | 상태               |
| ------------------------------------------------------------------------------------------ | ------------------ | ---- | ---- | ------------------ |
| **[React](https://github.com/a2ui-project/a2ui/tree/main/renderers/react)**              | 웹                 | ✅   | ✅   | ✅ 안정(Stable)    |
| **[Lit (Web Components)](https://github.com/a2ui-project/a2ui/tree/main/renderers/lit)** | 웹                 | ✅   | ✅   | ✅ 안정(Stable)    |
| **[Angular](https://github.com/a2ui-project/a2ui/tree/main/renderers/angular)**          | 웹                 | ✅   | ✅   | ✅ 안정(Stable)    |
| **[Flutter (GenUI SDK)](https://docs.flutter.dev/ai/genui)**                              | 모바일/데스크톱/웹 | ✅   | ✅   | ✅ 안정(Stable)    |
| **Jetpack Compose**                                                                        | 안드로이드         | —    | —    | 🚧 2026년 2분기 예정 |

더 많은 정보는 [A2UI 렌더러](../reference/renderers.md)와 [커뮤니티 A2UI 렌더러](../ecosystem/renderers.md)를 참고하세요.

## 컴포넌트 카탈로그

컴포넌트 카탈로그는 컴포넌트들의 임의의 집합입니다. A2UI는 "Basic Catalog"를 제공하지만, 여러분이 직접 컴포넌트를 추가하거나 공유 라이브러리를 사용하거나 basic 컴포넌트를 여러분만의 컴포넌트로 완전히 대체할 것으로 예상합니다.

**중요한 것은 여러분의 디자인 시스템입니다.** 어떤 컴포넌트와 함수의 집합이든 등록할 수 있으며, A2UI는 그것들과 함께 동작합니다. 카탈로그는 단지 에이전트와 렌더러 사이의 계약일 뿐입니다.

여러분의 디자인 시스템에 맞는 카탈로그를 정의하는 방법은 [나만의 카탈로그 정의하기](defining-your-own-catalog.md)를 참고하세요.

## 공유 Web Library

모든 웹 렌더러(Lit, Angular, React)는 **`@a2ui/web_core`**라는 공통 기반을 공유합니다. 이 라이브러리는 모든 웹 렌더러에 필요한 메시지 프로세서, 상태 관리, 데이터 바인딩 로직을 제공합니다. 각 프레임워크별 렌더러는 이 위에 구축되며, 해당 프레임워크를 위한 렌더링 계층만 추가합니다.

즉, 핵심 프로토콜 처리는 웹 플랫폼 전반에서 일관되게 유지되며, 컴포넌트 렌더링 방식만 다릅니다.

공유되는 `web_core` 라이브러리가 제공하는 기능:

- **메시지 프로세서(Message Processor)**: A2UI 상태를 관리하고 수신 메시지를 처리합니다.

## Web Components (Lit)

```bash
npm install @a2ui/lit @a2ui/web_core
```

설치가 끝나면 앱에서 렌더러를 사용할 수 있습니다. Lit 렌더러는 다음을 사용합니다.

- **메시지 프로세서(Message Processor)**: A2UI 메시지 프로세서를 감쌉니다.
- **`<a2ui-surface>` 컴포넌트**: 앱에서 Surface를 렌더링합니다.
- **Lit Signals**: 자동 UI 업데이트를 위한 반응형 상태 관리를 제공합니다.

**실행 가능한 예시:** [Lit 쉘 샘플](https://github.com/a2ui-project/a2ui/tree/main/samples/client/lit/shell) — 자세한 실행 방법은 해당 README를 확인하세요.

## Angular

```bash
npm install @a2ui/angular @a2ui/web_core
```

설치가 끝나면 앱에서 렌더러를 사용할 수 있습니다. Angular 렌더러가 제공하는 기능:

- **`A2uiRendererService`**: A2UI 메시지 프로세서와 반응형 모델을 관리하는 서비스입니다.
- **`a2ui-v09-component-host` 컴포넌트**: Surface로부터 A2UI 컴포넌트를 렌더링하는 동적 컴포넌트 호스트입니다.
- **`A2UI_RENDERER_CONFIG` 토큰**: 카탈로그와 액션 핸들러로 렌더러를 구성하는 데 사용됩니다.

### 설정 예시 (v0.9)

A2UI는 프로토콜별 구현을 위해 버전별 import를 사용합니다. v0.9의 경우 `provideA2Ui`를 사용해 애플리케이션 provider를 다음과 같이 구성하세요.

```typescript
import {ApplicationConfig} from '@angular/core';
import {provideA2Ui, BasicCatalog} from '@a2ui/angular/v0_9';

export const appConfig: ApplicationConfig = {
  providers: [
    provideA2Ui({
      catalogs: [new BasicCatalog()],
      actionHandler: action => {
        console.log('Action dispatched:', action);
      },
    }),
  ],
};
```

#### 액션 핸들러에서의 의존성 주입(Dependency Injection)

`actionHandler`가 의존성을 주입해야 하는 경우(예: 액션이 발생했을 때 서비스를 호출해야 하는 경우), `provideA2Ui`에 팩토리 함수를 전달할 수 있습니다. 이 팩토리 함수 안에서는 Angular의 `inject()` 함수를 사용할 수 있습니다.

```typescript
import {ApplicationConfig, inject} from '@angular/core';
import {provideA2Ui, BasicCatalog} from '@a2ui/angular/v0_9';
import {MyActionDispatcherService} from './my-action-dispatcher.service';

export const appConfig: ApplicationConfig = {
  providers: [
    provideA2Ui(() => {
      const dispatcher = inject(MyActionDispatcherService);
      return {
        catalogs: [new BasicCatalog()],
        actionHandler: action => dispatcher.dispatch(action),
      };
    }),
  ],
};
```

**실행 가능한 예시:** [Angular 샘플](https://github.com/a2ui-project/a2ui/tree/main/samples/client/angular)

### 스트리밍

Angular 클라이언트는 기본적으로 스트리밍 API를 사용합니다. 스트리밍을 비활성화하려면 앱을 시작하기 전에 `ENABLE_STREAMING` 환경 변수를 `false`로 설정하세요.

```bash
export ENABLE_STREAMING=false
yarn start restaurant
```

> [!NOTE]
> **패키지 매니저 사용:** 위의 `yarn start` 명령은 A2UI 모노레포 저장소 안에서 샘플 애플리케이션을 실행할 때 적용되는 방식입니다. 이 저장소 밖에서 여러분만의 일반적인 용도나 독립 프로젝트에 사용할 때는 원하는 패키지 매니저(예: npm, pnpm)를 사용하세요.

## React

```bash
npm install @a2ui/react @a2ui/web_core
```

React 렌더러가 제공하는 기능:

- **`MessageProcessor` 클래스**: A2UI 메시지 프로세서와 반응형 모델을 관리하는 클래스입니다.
- **`<A2UISurface>` 컴포넌트**: React 앱에서 A2UI Surface를 렌더링합니다.
- **`useA2UI()` 훅**: 어떤 컴포넌트에서든 메시지 프로세서에 접근할 수 있습니다.

**실행 가능한 예시:** [React 쉘](https://github.com/a2ui-project/a2ui/tree/main/samples/client/react/shell)

## Flutter (GenUI SDK)

```bash
flutter pub add flutter_genui
```

Flutter는 네이티브 A2UI 렌더링을 제공하는 GenUI SDK를 사용합니다.

**문서:** [GenUI SDK](https://docs.flutter.dev/ai/genui) | [GitHub](https://github.com/flutter/genui) | [GenUI Flutter 패키지 README](https://github.com/flutter/genui/blob/main/packages/genui/README.md#getting-started-with-genui)

## 에이전트 연결하기

클라이언트 애플리케이션은 다음을 수행해야 합니다.

1. 에이전트로부터 **A2UI 메시지 수신** (전송 계층을 통해)
2. 메시지 프로세서를 사용하여 **메시지 처리**
3. 에이전트에게 **사용자 액션 전송**

일반적인 전송 옵션:

- **Server-Sent Events (SSE)**: 서버에서 클라이언트로의 단방향 스트리밍
- **WebSockets**: 양방향 실시간 통신
- **A2A 프로토콜**: A2UI를 지원하는 표준화된 에이전트 간 통신

A2A 프로토콜 클라이언트 사용 예시는 [samples/client/lit/shell/client.ts](https://github.com/a2ui-project/a2ui/tree/main/samples/client/lit/shell/client.ts)를 참고하세요.

**참고:** [전송 계층 가이드](../concepts/transports.md)

## 사용자 액션 처리하기

사용자가 A2UI 컴포넌트와 상호작용(버튼 클릭, 양식 제출 등)할 때 클라이언트는 다음을 수행합니다.

1. 컴포넌트로부터 액션 이벤트 캡처
2. 액션에 필요한 데이터 컨텍스트 확인(Resolve)
3. 에이전트에게 액션 전송
4. 에이전트의 응답 메시지 처리

버튼 클릭과 양식 제출을 처리하는 예시는 [samples/client/lit/shell/app.ts](https://github.com/a2ui-project/a2ui/tree/main/samples/client/lit/shell/app.ts)의 `#maybeRenderData` 안에 있는 `@a2uiaction` 이벤트 핸들러를 참고하세요.

## 오류 처리

처리해야 할 일반적인 오류들:

- **잘못된 Surface ID**: `beginRendering`(v0.8) 또는 `createSurface`(v0.9)를 받기 전에 Surface가 참조된 경우
- **잘못된 컴포넌트 ID**: 컴포넌트 ID는 Surface 내에서 고유해야 함
- **잘못된 데이터 경로**: 데이터 모델 구조 및 JSON Pointer 구문 확인
- **스키마 검증 실패**: 메시지 형식이 A2UI 명세와 일치하는지 확인

통신 오류를 처리하는 예시는 [samples/client/lit/shell/app.ts](https://github.com/a2ui-project/a2ui/tree/main/samples/client/lit/shell/app.ts)의 `#sendMessage` 안에 있는 `try...catch` 블록을 참고하세요.

## 다음 단계

- **[퀵스타트](../quickstart.md)**: 데모 애플리케이션 시도해 보기
- **[테마 및 스타일링](theming.md)**: 룩앤필 커스터마이징
- **[나만의 카탈로그 정의하기](defining-your-own-catalog.md)**: 컴포넌트 카탈로그 확장
- **[에이전트 개발](agent-development.md)**: A2UI를 생성하는 에이전트 구축
- **[참조 문서](../reference/messages.md)**: 프로토콜 심층 분석
