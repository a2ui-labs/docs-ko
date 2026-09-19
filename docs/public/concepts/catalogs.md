---
render_macros: false
---

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
  "catalogId": "https://github.com/.../hello_world/v1/catalog.json",
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

!!! note ""
    Orchestrator 데모는 현재 v0.8 API를 사용합니다. 카탈로그 등록에 대한 v0.9 예시는 Angular explorer의 [DemoCatalog](../../../renderers/angular/a2ui_explorer/src/app/demo-catalog.ts)를 참고하세요.

    또한 클라이언트 측 함수의 경우, 클라이언트는 런타임에 활성 카탈로그 정의에서 설정을 읽어 해당 함수의 실행 경계(예: `clientOnly` 여부)를 결정합니다.

## 카탈로그 네이밍 및 버전 관리

A2UI 컴포넌트 카탈로그에는 버전 관리가 필요합니다. 카탈로그 정의는 대개 컴파일 시점에 빌드에 포함되므로, 에이전트가 생성하는 내용과 클라이언트가 렌더링할 수 있는 내용이 어긋나면 UI에 영향을 줄 수 있기 때문입니다.

### CatalogId 네이밍 규칙

`catalogId`는 클라이언트와 에이전트 간 협상에 사용되는 고유한 텍스트 식별자입니다.

- **형식:** `catalogId`는 기술적으로는 문자열이지만, A2UI의 관례는 **URI**를 사용하는 것입니다(예: `https://example.com/catalogs/mysurface/v1/catalog.json`).
- **목적:** URI를 사용하면 ID가 전역적으로 고유해지고, 개발자가 브라우저에서 직접 확인하기 쉬워집니다.
- **런타임 페치 없음:** 이 URI는 에이전트나 클라이언트가 런타임에 카탈로그를 내려받는다는 뜻이 아닙니다. **카탈로그 정의는 에이전트와 클라이언트가 사전에(컴파일/배포 시점에) 알고 있어야 합니다.** URI는 안정적인 식별자 역할만 합니다.
- **JSON Schema 호환성(`$id`와 `catalogId`):** A2UI 카탈로그는 현재 JSON Schema 문서로 표현되므로, 카탈로그 정의에는 `$id`(JSON Schema 도구용)와 `catalogId`(A2UI SDK 및 카탈로그 협상용)를 모두 포함하고 두 필드를 동일한 URI로 설정해야 합니다.

### 버전 관리 가이드라인 (Versioning Guidelines)

카탈로그는 에이전트와 렌더러 간의 버전 불일치(version skew)가 발생할 수 있는 API처럼 다루어져야 합니다. 에이전트, 렌더러, 템플릿이 활성 세션이나 저장된 대화 기록(transcript)을 깨뜨리지 않고 발전할 수 있도록 카탈로그는 여러 버전에 걸쳐 하위 및 상위 호환성을 유지해야 합니다.

#### 카탈로그 스키마 발전 규칙 (Catalog Schema Evolution Rules)

하위 및 상위 호환성을 유지하기 위해 다음 규칙이 적용됩니다:

1. **추가만 허용 (Additive Only)**: 새 컴포넌트, 함수 또는 선택적 속성(optional property)을 추가하는 것은 안전합니다.
2. **삭제 대신 지원 중단 (Deprecate Rather than Delete)**: 기존 컴포넌트, 함수 또는 속성을 절대 삭제하지 마세요. 대신 `deprecated: true`와 함께 `x-deprecated-reason`을 표시하세요. 렌더러는 과거 대화 기록, 캐시된 상태 및 템플릿에 대한 하위 호환성을 유지하기 위해 지원 중단된 컴포넌트와 속성에 대한 처리 로직을 유지해야 합니다.
3. **타입 불변성 (Type Invariance)**: 렌더러는 기존 필드의 타입을 변경하는 필드를 무시할 수 있습니다. 카탈로그는 기존 필드의 데이터 타입을 변경하지 않아야 합니다. 문자열에 enum을 추가하는 것은 타입을 변경하지 않으며 enum은 열려 있는(open) 것으로 간주되므로 아무것도 깨뜨리지 않습니다.
4. **개방형 열거형 (Open Enums)**: 상류(upstream)에 새로운 enum 변형이 도입되더라도 구버전 렌더러가 실패하지 않도록 enum 정의를 개방형(open)으로 취급하세요. 카탈로그는 일반적으로 enum을 선언하되, 렌더러 구현체에서 알 수 없는 enum 변형을 안전하게 처리해야 합니다.
5. **우아한 성능 저하 (Graceful Degradation)**: 렌더러는 알 수 없는 컴포넌트를 만났을 때 전체 뷰 계층이나 서피스를 실패시키는 대신 정상적인 폴백(fallback)을 제공해야 합니다.
6. **알 수 없는 컴포넌트/필드의 왕복 보존 (Round-Trip Unknown Component/Field Preservation)**: 오케스트레이터와 같은 중간 서비스는 메시지를 직렬화/역직렬화할 때 알 수 없는 속성을 그대로 보존해야 합니다.
7. **메이저 버전 판올림 (Major Version Bumps)**: 카탈로그의 메이저 버전 판올림은 전면적인 정리(예: 오래 지원 중단된 필드 제거) 또는 근본적으로 호환성을 깨뜨리는 구조적 변경에만 엄격히 제한하여 사용하세요.

#### 카탈로그 속성 지원 중단 (Deprecating Catalog Properties)

기존 컴포넌트, 함수 또는 속성을 지원 중단할 때:

- **`deprecated`** (boolean, 선택 사항): 컴포넌트, 함수 또는 속성이 지원 중단되었음을 나타내는 표준 JSON Schema 어노테이션입니다.
- **`x-deprecated-reason`** (string, 선택 사항): 해당 항목이 지원 중단된 이유와 대신 무엇을 사용해야 하는지 사람이 읽을 수 있는 설명입니다.

```json
{
  "TextBox": {
    "type": "object",
    "properties": {
      "color": {
        "type": "string",
        "description": "아이콘 색상 사양입니다.",
        "deprecated": true,
        "x-deprecated-reason": "CSS를 사용하여 색상을 설정하려면 style 속성을 사용하세요."
      },
      "style": {
        "type": "string",
        "description": "색상과 같은 CSS 속성입니다."
      }
    }
  }
}
```

### CatalogId를 사용한 메이저 버전 판올림 (Major Version Bumps with CatalogId)

메이저 버전 판올림이 불가피한 근본적인 주요 변경 사항을 도입할 때는 `catalogId` URI에 버전을 포함하세요. 이를 통해 마이그레이션 중에도 A2UI 카탈로그 협상을 사용하여 여러 버전을 동시에 지원할 수 있어 무중단 운영이 가능합니다.

**권장 패턴:**

| 변경 유형 | URI 예시 | 설명 |
| :--- | :--- | :--- |
| **현재 (Current)** | `.../rizzcharts/v1/catalog.json` | 버전 1.x. 1.x 브랜치의 모든 추가적(additive) 업데이트를 지원합니다. |
| **파괴적 변경 (Breaking)** | `.../rizzcharts/v2/catalog.json` | 호환성을 깨뜨리는 구조적 변경 사항을 도입하는 새로운 스키마입니다. |

### 마이그레이션 처리 (Handling Migrations)

동작 중인 에이전트에 영향을 주지 않고 메이저 버전 간 카탈로그를 업그레이드하려면 A2UI 카탈로그 협상을 사용하세요:

1. **클라이언트 업데이트:** 클라이언트는 `supportedCatalogIds` 목록을 업데이트하여 구버전과 신버전을 모두 포함합니다(예: `[".../v2/...", ".../v1/..."]`).
2. **에이전트 업데이트:** 에이전트가 v2 스키마로 다시 빌드됩니다. 클라이언트가 v2를 지원하는 것을 확인하면 에이전트는 이를 우선적으로 선택합니다.
3. **레거시 지원:** 아직 재빌드되지 않은 이전 에이전트는 클라이언트 목록의 v1과 계속 일치하므로 정상적으로 계속 작동합니다.

## A2UI 스키마 검증 및 폴백 (Schema Validation & Fallback)

안정적인 사용자 경험을 보장하기 위해 A2UI는 2단계 검증(two-phase validation) 전략을 사용합니다. 이러한 "심층 방어(defense in depth)" 접근 방식은 오류를 가능한 한 조기에 포착하는 동시에 예상치 못한 페이로드를 마주했을 때도 클라이언트의 견고성을 유지합니다.

### 2단계 검증 (Two-Phase Validation)

1. **에이전트 측 (전송 전):** UI 페이로드를 전송하기 전에 에이전트 런타임은 생성된 JSON을 카탈로그 정의를 기준으로 검증합니다.
    - 목적: 환각(hallucination)으로 생성된 속성이나 잘못된 구조를 원천에서 감지합니다.
    - 결과: 검증에 실패하면 에이전트는 A2UI JSON을 수정하거나 재생성할 수 있으며, 대화형 앱에서는 텍스트로 폴백하는 등 우아한 성능 저하를 수행할 수 있습니다.
2. **클라이언트 측:** 페이로드를 수신하면 클라이언트 라이브러리는 해당 카탈로그의 로컬 정의를 기준으로 JSON을 검증합니다.
    - 목적: 보안 및 안정성. 사용자 기기에서 실행되는 코드가 예상된 규약을 엄격히 준수하도록 보장하여 버전 불일치나 손상된 에이전트 출력으로부터 보호합니다.
    - 결과: 여기서 발생하는 오류는 "error" 클라이언트 메시지를 통해 에이전트로 다시 보고됩니다.

### 우아한 성능 저하 (Graceful Degradation)

페이로드가 스키마 검증을 통과하더라도 렌더러에서 런타임 문제(예: 누락된 에셋, 아직 로드되지 않은 컴포넌트 구현체 또는 플랫폼별 제한 사항)가 발생할 수 있습니다.

클라이언트는 이러한 오류가 발생해도 비정상 종료(crash)되지 않아야 합니다. 대신 우아한 성능 저하를 적용해야 합니다:

- **알 수 없는 컴포넌트:** 스키마에서는 인식되지만 렌더러에 구현되지 않은 컴포넌트의 경우, "안전한" 폴백(예: 컴포넌트의 디버그 이름이 표시된 일반 카드)을 렌더링하거나 해당 노드의 렌더링을 완전히 건너뜁니다.
- **텍스트 폴백:** 전체 서피스 렌더링에 실패하면 원본 텍스트 설명(사용 가능한 경우) 또는 일반 오류 메시지(_"이 인터페이스를 표시할 수 없습니다."_)를 표시합니다.

### 클라이언트-서버 오류 보고 (Client-to-Server Error Reporting)

클라이언트가 검증 오류나 런타임 실패를 감지하면 이를 에이전트에 다시 보고할 수 있습니다. 이를 통해 에이전트 시스템은 개발자를 위해 실패를 기록하거나 향후 동작을 조정할 수 있습니다.

클라이언트는 표준 A2UI 클라이언트-서버 이벤트 스키마를 사용하여 `VALIDATION_FAILED` 이벤트를 전송합니다.

필수 필드가 누락된 경우 클라이언트가 보고하는 예시:

```json
{
  "version": "v0.9",
  "error": {
    "code": "VALIDATION_FAILED",
    "surfaceId": "flight-status-card-123",
    "path": "/components/FlightCard/flightNumber",
    "message": "Missing required property 'flightNumber' in component 'FlightCard'."
  }
}
```

## 인라인 카탈로그 (Inline Catalogs)

런타임에 클라이언트가 전송하는 인라인 카탈로그는 지원되지만 프로덕션 환경에서는 권장되지 않습니다. 자세한 내용은 [여기](../specification/v0.9-a2ui.md)에서 확인할 수 있습니다.

## 다음 단계

- [메시지 레퍼런스](../reference/messages.md): 카탈로그와 함께 사용되는 메시지 형식
- [렌더러 레퍼런스](../reference/renderers.md): 어떤 클라이언트가 어떤 카탈로그를 렌더링하는지
- [클라이언트 설정 가이드](../guides/client-setup.md): 렌더러를 앱에 연결하는 방법

