# A2UI 카탈로그

## 개요

이 가이드는 A2UI 카탈로그 아키텍처를 설명하고, 구현 로드맵을 제공합니다. 카탈로그 스키마의 구조를 설명하고, 미리 준비된 "기본 카탈로그(Basic Catalog)"를 사용할지 아니면 애플리케이션 전용 카탈로그를 정의할지에 대한 전략을 다루며, 카탈로그 협상, 버전 관리, 런타임 검증에 대한 기술적 흐름을 정리합니다.

## 카탈로그 정의

카탈로그는 에이전트가 서버 주도 UI로 A2UI 서피스를 정의할 때 사용할 수 있는 컴포넌트, 함수, 테마를 설명하는 JSON Schema 파일입니다. 에이전트가 전송하는 모든 A2UI JSON은 선택된 카탈로그를 기준으로 검증됩니다.

카탈로그는 보통 다음 세 가지를 포함합니다.

- 컴포넌트 정의
- 함수 정의
- 테마 정의

## 카탈로그 전략

모든 A2UI 서피스는 카탈로그를 기반으로 동작합니다. 카탈로그는 에이전트가 어떤 컴포넌트, 함수, 테마를 사용할 수 있는지를 알려주는 JSON Schema 파일입니다.

간단한 프로토타입이든 복잡한 프로덕션 애플리케이션이든 요구 사항은 같습니다. 에이전트가 UI를 표현하는 데 사용할 카탈로그 정의를 제공해야 합니다.

### 기본 카탈로그

빠르게 시작할 수 있도록 A2UI 팀은 [기본 카탈로그](../specification/v0.9-a2ui.md)를 제공합니다.

이 카탈로그는 버튼, 입력, 카드 같은 일반적인 컴포넌트와 함수 집합을 담은 미리 정의된 파일입니다. 특별한 카탈로그 "종류"가 아니라, 이미 작성되어 있고 오픈 소스 렌더러가 있는 카탈로그 버전이라고 보면 됩니다.

기본 카탈로그는 자신만의 스키마를 처음부터 작성하지 않고도 애플리케이션을 부트스트랩하거나 A2UI 개념을 검증하는 데 유용합니다. 서로 다른 렌더러가 쉽게 구현할 수 있도록 의도적으로 단순하게 유지됩니다.

### 나만의 카탈로그 정의하기

기본 카탈로그는 시작할 때 유용하지만, 대부분의 프로덕션 애플리케이션은 자신의 디자인 시스템을 반영하는 별도 카탈로그를 정의합니다.

자체 카탈로그를 정의하면 에이전트가 기본 입력이나 버튼 같은 일반 컴포넌트가 아니라, 애플리케이션에 실제로 존재하는 컴포넌트와 시각 언어만 사용하도록 제한할 수 있습니다.

저희는 기본 카탈로그를 어댑터로 매핑하려고 하기보다, 클라이언트의 디자인 시스템을 직접 반영하는 카탈로그를 만드는 방식을 권장합니다.

### 권장 사항

| 사용 사례 | 권장 방식 | 작업량 |
|-----------|-----------|--------|
| 성숙한 프런트엔드에 A2UI를 추가 | 기존 디자인 시스템과 동일한 카탈로그 정의 | 중간 |
| 새 앱/그린필드 앱에 A2UI를 추가 | 기본 카탈로그로 시작한 뒤 앱이 성장하면서 자체 카탈로그로 발전 | 낮음 |

## 카탈로그 만들기

카탈로그는 에이전트가 서피스를 만들 때 사용할 컴포넌트, 테마, 함수를 정의하는 JSON Schema 파일입니다.

### 예시: 최소 카탈로그

아래는 단일 컴포넌트를 정의하는 간단한 카탈로그입니다.

```json
{
  "$id": "https://github.com/.../hello_world/v1/catalog.json",
  "components": {
    "HelloWorldBanner": {
      "type": "object",
      "description": "간단한 인사 배너입니다.",
      "properties": {
        "message": {
          "type": "string",
          "description": "배너 텍스트"
        },
        "backgroundColor": {
          "type": "string",
          "default": "#f0f0f0"
        }
      },
      "required": ["message"]
    }
  }
}
```

이 카탈로그를 사용하는 에이전트는 해당 구조를 엄격히 따르는 페이로드를 생성합니다.

### 카탈로그 링킹

A2UI 카탈로그는 LLM 추론과 의존성 관리를 단순하게 하기 위해 독립형이어야 합니다. 로컬 개발에서는 외부 문서를 참조하는 모듈식 JSON Schema로 작성할 수 있지만, 최종 카탈로그는 독립형(freestanding)이어야 합니다. 이러한 외부 참조를 번들링하고 등록하는 과정을 "링킹(Linking)"이라고 하며, 여러 플랫폼을 지원하는 단일 Node.js 스크립트(`register-catalogs.js`)로 통합되어 있습니다. 이 링킹 스크립트는 애플리케이션 빌드 과정에서 정적·동적 스키마를 매끄럽게 컴파일, 집계, 연결할 수 있도록 iOS/macOS 클라이언트 빌드용 **Xcode Build Phases**와 Android 클라이언트 빌드용 **Gradle 작업**에 자연스럽게 내장되어 있습니다.

### 합성과 import

모든 것을 처음부터 정의할 필요는 없습니다. 기본 카탈로그나 다른 카탈로그의 기존 컴포넌트를 재사용하면서, 자신만의 렌더링 로직을 얹을 수 있습니다.

### 렌더러 구현

클라이언트 렌더러는 스키마 정의를 실제 코드로 매핑해 카탈로그를 구현합니다.

먼저 카탈로그 스키마에 맞춰 TypeScript로 컴포넌트 API를 정의합니다.

```typescript
// api.ts
import {ComponentApi} from '@a2ui/web_core/v0_9';
import {z} from 'zod';

export const HelloWorldBannerApi = {
  name: 'HelloWorldBanner',
  schema: z.object({
    message: z.string(),
    backgroundColor: z.string().default('#f0f0f0'),
  }).strict(),
} satisfies ComponentApi;
```

다음으로 `CatalogComponent`를 확장해 컴포넌트를 구현합니다.

```typescript
// hello_world_banner.ts
import {CatalogComponent} from '@a2ui/angular/v0_9';
import {Component, computed} from '@angular/core';
import {HelloWorldBannerApi} from './api';

@Component({
  selector: 'hello-world-banner',
  template: `
    <div [style.background-color]="backgroundColor()">
      <h2>Hello World Banner</h2>
      <p>{{ message() }}</p>
    </div>
  `,
})
export class HelloWorldBanner extends CatalogComponent<typeof HelloWorldBannerApi> {
  protected readonly message = computed(() => this.props()['message']?.value() || '');
  protected readonly backgroundColor = computed(() => this.props()['backgroundColor']?.value() || '#f0f0f0');
}
```

마지막으로 커스텀 컴포넌트를 `AngularCatalog`에 등록합니다.

```typescript
// catalog.ts
import {AngularCatalog, BASIC_COMPONENTS, BASIC_FUNCTIONS} from '@a2ui/angular/v0_9';
import {HelloWorldBanner} from './hello_world_banner';
import {HelloWorldBannerApi} from './api';

const customBannerComponent = {
  ...HelloWorldBannerApi,
  component: HelloWorldBanner
};

export const MY_CATALOG = new AngularCatalog(
  'https://github.com/.../hello_world/v1/catalog.json',
  [...BASIC_COMPONENTS, customBannerComponent],
  BASIC_FUNCTIONS
);
```

[Orchestrator 데모](../../../samples/community/client/angular/projects/orchestrator/src/a2ui-catalog/catalog.ts)에서 클라이언트 렌더러의 동작 예시를 확인할 수 있습니다.

> [!NOTE]
> Orchestrator 데모는 현재 v0.8 API를 사용합니다. 카탈로그 등록에 대한 v0.9 예시는 Angular explorer의 [DemoCatalog](../../../renderers/angular/a2ui_explorer/src/app/demo-catalog.ts)를 참고하세요.
>
> 또한 클라이언트 측 함수의 경우, 클라이언트는 런타임에 활성 카탈로그 정의에서 설정을 읽어 해당 함수의 실행 경계(예: `clientOnly` 여부)를 결정합니다.

## 다음 단계

- [메시지 레퍼런스](../reference/messages.md): 카탈로그와 함께 사용되는 메시지 형식
- [렌더러 레퍼런스](../reference/renderers.md): 어떤 클라이언트가 어떤 카탈로그를 렌더링하는지
- [클라이언트 설정 가이드](../guides/client-setup.md): 렌더러를 앱에 연결하는 방법
