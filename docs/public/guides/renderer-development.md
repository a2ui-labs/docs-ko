# A2UI 렌더러 구현 가이드

이 문서는 A2UI 프로토콜을 새로 구현하는 렌더러에 필요한 기능을 설명합니다. React, Flutter, iOS 등 새로운 렌더러를 만들려는 개발자를 대상으로 합니다.

> NOTE: 버전별 가이드
>
> 이 가이드는 v0.8, v0.9.1(현재 프로덕션 버전), v1.0(후보) 버전에 대한 구현 체크리스트를 제공합니다. 대상으로 하는 버전을 선택하려면 아래 탭을 사용하세요.

## 웹 렌더러: `@a2ui/web_core`(`web_core`) 사용하기

웹용 렌더러(React, Vue, Svelte 등)를 만들고 있다면, 메시지 처리, 상태 관리, 스키마 검증을 처음부터 직접 구현할 필요가 없습니다. **[`@a2ui/web_core`](https://github.com/a2ui-project/a2ui/tree/main/renderers/web_core)** 패키지(`web_core`)는 유지 관리되는 Lit, Angular, React 렌더러가 공유하는, 프레임워크에 종속되지 않는(framework-agnostic) 모든 로직을 제공합니다.

### `web_core`가 제공하는 것

| 모듈                                      | 하는 일                                                                                           |
| ----------------------------------------- | -------------------------------------------------------------------------------------------------- |
| **`MessageProcessor`**                    | A2UI JSONL 스트림을 처리하고, 메시지를 디스패치하며, 서피스 생명주기를 관리합니다                 |
| **`SurfaceModel` / `SurfaceGroupModel`**  | 서피스, 컴포넌트, 데이터 모델에 대한 상태 관리를 담당합니다                                       |
| **`DataModel` / `DataContext`**           | 데이터 바인딩 해석, 경로 기반 조회, 템플릿 리스트 렌더링을 담당합니다                             |
| **`ComponentModel`**                      | 컴포넌트 트리 상태, 인접 리스트 → 트리 변환을 담당합니다                                          |
| **타입 및 스키마**                        | 모든 A2UI 컴포넌트, primitive, 색상, 스타일에 대한 TypeScript 타입과 JSON 스키마 검증을 제공합니다 |
| **표현식 파서**                           | 클라이언트 측 함수 평가(v0.9 이상)를 담당합니다                                                   |

### 유지 관리되는 렌더러들이 이를 사용하는 방법

세 웹 렌더러 모두 동일한 패턴을 따릅니다 — `web_core`가 프로토콜을 처리하고, 렌더러는 UI를 처리합니다.

```typescript
// 타입 — 모든 렌더러가 공유합니다
import type * as Types from '@a2ui/web_core/types/types';
import type * as Primitives from '@a2ui/web_core/types/primitives';

// v0.8: 메시지 처리 및 상태
import {A2uiMessageProcessor} from '@a2ui/web_core/data/model-processor';

// v0.9.1 / v1.0: 메시지 처리, 서피스, 카탈로그
import {MessageProcessor} from '@a2ui/web_core/v0_9';
import {SurfaceModel} from '@a2ui/web_core/v0_9';

// 스타일 및 레이아웃 헬퍼
import * as Styles from '@a2ui/web_core/styles/index';
```

여러분의 렌더러는 다음만 하면 됩니다.

1. **A2UI 컴포넌트 유형을 여러분의 프레임워크 컴포넌트로 매핑**합니다(예: `Text` → `<p>`, `Button` → `<button>`).
2. `web_core`로부터 **상태 변경을 구독**하고 다시 렌더링합니다.
3. `MessageProcessor`를 통해 **사용자 액션을 다시 전달**합니다.

이 패턴이 실제로 동작하는 예시는 [React 렌더러](https://github.com/a2ui-project/a2ui/tree/main/renderers/react), [Lit 렌더러](https://github.com/a2ui-project/a2ui/tree/main/renderers/lit), [Angular 렌더러](https://github.com/a2ui-project/a2ui/tree/main/renderers/angular)를 참고하세요.

### 버전 지원

`web_core`는 버전별로 API 세트를 내보냅니다(export).

- `@a2ui/web_core/v0_8` — 안정된 v0.8 지원
- `@a2ui/web_core/v0_9` — `createSurface`, 커스텀 카탈로그, 클라이언트 측 함수를 포함한 v0.9/v0.9.1 지원
- `@a2ui/web_core/v1_0` — RPC 액션 응답을 포함한 후보 v1.0 지원

> TIP: `web_core`로 시작하세요
>
> `web_core` 없이 웹 렌더러를 만든다는 것은 약 3,000줄에 달하는 메시지 처리, 상태 관리, 스키마 검증 로직을 다시 구현해야 한다는 뜻입니다. 특별히 다르게 구현해야 할 이유가 없다면 `web_core`를 사용하세요.

---

## I. 핵심 프로토콜 구현 체크리스트

이 섹션에서는 A2UI 프로토콜의 기본 메커니즘을 다룹니다. 규격을 준수하는 렌더러는 서버 스트림을 파싱하고, 상태를 관리하며, 사용자 상호작용을 처리하기 위해 반드시 이러한 시스템을 구현해야 합니다.

=== "v0.8"

    - **JSONL 스트림 파싱**: 스트리밍 응답을 한 줄씩 읽고, 각 줄을 별개의 JSON 객체로 디코딩합니다.
    - **메시지 디스패처(Dispatcher)**: 메시지 유형(`beginRendering`, `surfaceUpdate`, `dataModelUpdate`, `deleteSurface`)을 식별하고 올바른 핸들러로 라우팅합니다.
    - **서피스(Surface) 관리**:
        - `surfaceId`로 서피스를 구분해 관리합니다.
        - `surfaceUpdate` 처리: 서피스 버퍼에 컴포넌트를 추가하거나 업데이트합니다.
        - `deleteSurface` 처리: 서피스와 그에 관련된 모든 데이터/컴포넌트를 제거합니다.
    - **컴포넌트 버퍼링**:
        - 각 서피스별로 컴포넌트 버퍼(예: `Map<String, Component>`)를 유지합니다.
        - `id` 참조(`children.explicitList`, `child`, `contentChild` 등)를 해석해 UI 트리를 재구성합니다.
    - **데이터 모델 저장소**:
        - 각 서피스별로 데이터 모델 상태를 유지합니다.
        - `dataModelUpdate` 처리: 인접 리스트 표현(`[{ "key": "name", "valueString": "Bob" }]`)을 사용해 경로의 값을 업데이트합니다.
    - **점진적 렌더링**:
        - `beginRendering`을 받을 때까지 업데이트를 버퍼링합니다.
        - `beginRendering`을 받으면 지정된 `root` ID로부터 렌더링을 시작합니다. 테마 스타일을 적용합니다.
    - **데이터 바인딩 해석**:
        - `literalString` / `literalNumber` / `path`를 사용해 `BoundValue` 객체를 해석합니다.
    - **동적 리스트**:
        - `children.template`의 경우, `template.dataBinding`에 있는 데이터 리스트를 순회하며 `template.componentId`를 사용해 컴포넌트를 렌더링합니다.
    - **클라이언트-서버 통신**:
        - 해석된 경로를 담은 컨텍스트가 포함된 `userAction`을 서버로 전송합니다.
        - 전송 메타데이터에 `a2uiClientCapabilities`를 포함합니다.

=== "v0.9.1 (현재)"

    - **JSONL 스트림 파싱**: 스트리밍 응답을 한 줄씩 읽고, 각 줄을 별개의 JSON 객체로 디코딩합니다.
    - **메시지 디스패처(Dispatcher)**: 메시지 유형(`createSurface`, `updateComponents`, `updateDataModel`, `deleteSurface`)을 식별하고 올바른 핸들러로 라우팅합니다.
    - **MIME 타입 검증**: 표준화된 `application/a2ui+json` MIME 타입을 기준으로 페이로드를 가로챕니다.
    - **서피스(Surface) 관리**:
        - `surfaceId`로 서피스를 구분해 관리합니다.
        - `createSurface` 처리: 서피스를 생성하고 `catalogId`를 바인딩하며, `theme`과 `sendDataModel`을 등록합니다.
        - `updateComponents` 처리: `"component": "Type"` 판별자를 사용하는 플랫(flat) 형식으로 컴포넌트를 추가하거나 업데이트합니다.
        - `deleteSurface` 처리: 서피스와 그에 관련된 모든 데이터/컴포넌트를 제거합니다.
    - **컴포넌트 버퍼링**:
        - 각 서피스별로 컴포넌트 버퍼(예: `Map<String, Component>`)를 유지합니다.
        - 컨테이너 컴포넌트의 `children` 배열 또는 `child` 필드에 있는 ID 참조를 해석해 UI 트리를 재구성합니다.
    - **데이터 모델 저장소**:
        - 각 서피스별로 데이터 모델 상태를 유지합니다.
        - `updateDataModel` 처리: upsert 시맨틱을 가진 표준 JSON 객체를 사용해 경로의 데이터를 업데이트합니다.
    - **점진적 렌더링**:
        - `updateComponents`에서 유효한 루트 컴포넌트(ID `root`)를 파싱하는 즉시 렌더링합니다. 별도의 렌더링 신호를 기다리지 않습니다.
    - **데이터 바인딩 해석**:
        - 단순화된 바인딩 값(리터럴 또는 `{"path": "..."}`)을 해석합니다.
    - **동적 리스트**:
        - 자식 템플릿의 경우, `path`에 있는 데이터 배열을 순회하며 `componentId`로 지정된 템플릿을 렌더링합니다.
    - **클라이언트 측 함수**:
        - 등록된 카탈로그 정의 함수(예: `formatString` 보간)를 평가합니다.
    - **클라이언트-서버 통신**:
        - 해석된 경로를 담은 컨텍스트가 포함된 `action`(`userAction`을 대체)을 전송합니다.
        - `sendDataModel`이 요청된 경우, 클라이언트 측 전체 데이터 모델을 메타데이터에 자동으로 포함합니다.
        - 스키마 검증에 실패하면 구조화된 `ValidationFailed` 오류 메시지를 서버로 전송합니다.

=== "v1.0 (후보)"

    v0.9.1의 모든 요구 사항에 다음이 추가됩니다.
    - **서피스 속성(Surface Properties)**:
        - `surfaceProperties`(`theme`에서 이름 변경)를 사용하는 `createSurface`를 처리합니다. 커스텀 기본 브랜드 색상은 더 이상 서피스 스키마 내부에서 지원되지 않습니다.
    - **액션 응답(RPC)**:
        - 서버로부터의 `actionResponse` 메시지를 처리합니다. 이 메시지는 `actionId`와 반환 `value` 또는 `error`를 포함합니다.
    - **클라이언트-서버 통신**:
        - `action` 페이로드 내부에 `actionId`를 생성해 포함합니다.
        - 클라이언트가 응답을 기대하는 경우 액션에 `wantResponse: true`를 지원합니다.
        - A2A를 사용하는 경우, 서버로 전송하는 모든 A2A `Message`는 해당 A2A `Message`의 `metadata` 필드에 `a2uiClientCapabilities` 객체를 포함해야 합니다.
    - **Capabilities**:
        - capabilities 교환 시 `theme` 대신 `surfaceProperties`를 노출합니다.

---

## II. 기본 컴포넌트 카탈로그 체크리스트

플랫폼 간 일관된 사용자 경험을 위해 A2UI는 기본 컴포넌트 셋을 정의합니다. 여러분의 클라이언트는 이러한 추상적인 정의를 해당 플랫폼의 네이티브 UI 위젯으로 매핑해야 합니다.

=== "v0.8"

    - **Text**: 텍스트를 렌더링합니다. `usageHint`(h1-h5, body, caption)를 지원합니다.
    - **Image**: URL로부터 이미지를 렌더링합니다. `fit`과 `usageHint`(avatar, hero 등)를 지원합니다.
    - **Icon**: 시스템 아이콘을 렌더링합니다.
    - **Video**: 비디오 플레이어를 렌더링합니다.
    - **AudioPlayer**: 설명이 포함된 오디오 플레이어를 렌더링합니다.
    - **Divider**: 가로/세로 구분선을 렌더링합니다.
    - **Row** / **Column**: 자식들을 가로/세로로 배치합니다. `distribution`과 `alignment`를 지원합니다. 자식의 `weight`를 지원합니다.
    - **List**: 스크롤 가능한 리스트를 렌더링합니다.
    - **Card**: 둥근 모서리와 그림자가 있는 박스 레이아웃입니다.
    - **Tabs**: `tabItems`를 사용하는 탭 내비게이션입니다.
    - **Modal**: `entryPointChild`로 트리거되어 `contentChild`를 표시하는 팝업입니다.
    - **Button**: `userAction`을 트리거하는 클릭 가능한 버튼입니다. `primary` variant를 지원합니다.
    - **CheckBox**: 불리언 체크박스입니다.
    - **TextField**: `label`, `textFieldType`(`shortText`, `longText` 등), `validationRegexp`를 지원하는 입력 필드입니다.
    - **MultipleChoice**: `options`, `maxAllowedSelections`, 단일/다중 값 선택을 지원합니다.
    - **Slider**: `minValue`, `maxValue`를 사용한 범위를 지원합니다.

=== "v0.9.1 (현재)"

    - **Text**: 텍스트를 렌더링합니다. `variant`(`usageHint`를 대체)를 지원합니다.
    - **Image**: URL로부터 이미지를 렌더링합니다. `fit`과 `variant`를 지원합니다.
    - **Icon**: 시스템 아이콘을 렌더링합니다.
    - **Video**: 비디오 플레이어를 렌더링합니다.
    - **AudioPlayer**: 설명이 포함된 오디오 플레이어를 렌더링합니다.
    - **Divider**: 가로/세로 구분선을 렌더링합니다.
    - **Row** / **Column**: 자식들을 가로/세로로 배치합니다. `justify`와 `align`을 지원합니다. 자식의 `weight`를 지원합니다.
    - **List**: 스크롤 가능한 리스트를 렌더링합니다.
    - **Card**: 둥근 모서리와 그림자가 있는 박스 레이아웃입니다.
    - **Tabs**: `tabs`를 사용하는 탭 내비게이션입니다.
    - **Modal**: `trigger`로 트리거되어 `content`를 표시하는 팝업입니다.
    - **Button**: `action`을 트리거하는 클릭 가능한 버튼입니다. `variant`(primary, borderless)를 지원합니다.
    - **CheckBox**: 불리언 체크박스입니다.
    - **TextField**: `label`, `value`(`text`를 대체), `variant`(`shortText`, `longText` 등), `checks`(검증 함수)를 지원하는 입력 필드입니다.
    - **ChoicePicker**: (MultipleChoice를 대체) `options`와 `variant`(`mutuallyExclusive`, `multipleSelection`)를 지원합니다.
    - **Slider**: `min`, `max`(`minValue`, `maxValue`를 대체)를 사용한 범위를 지원합니다.

=== "v1.0 (후보)"

    v0.9.1의 모든 컴포넌트에 다음이 추가됩니다.
    - **Video**: 미리보기 이미지를 표시하는 `posterUrl` 속성을 지원합니다.
    - **TextField**: `placeholder` 속성을 지원합니다.
