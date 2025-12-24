# A2UI 렌더러 구현 가이드

이 문서는 0.8 버전 명세를 기반으로 A2UI 프로토콜의 새로운 렌더러 구현에 필요한 요구 사항을 설명합니다. React, Flutter, iOS 등 새로운 렌더러를 구축하려는 개발자를 대상으로 합니다.

## I. 핵심 프로토콜 구현 체크리스트

이 섹션에서는 A2UI 프로토콜의 기본 메커니즘을 다룹니다. 규격을 준수하는 렌더러는 서버 스트림을 파싱하고, 상태를 관리하며, 사용자 상호작용을 처리하기 위해 반드시 이러한 시스템을 구현해야 합니다.

### 메시지 처리 및 상태 관리

- **JSONL 스트림 파싱**: 스트리밍 응답을 한 줄씩 읽고, 각 줄을 별개의 JSON 객체로 디코딩할 수 있는 파서를 구현합니다.
- **메시지 디스패처 (Dispatcher)**: 메시지 유형(`beginRendering`, `surfaceUpdate`, `dataModelUpdate`, `deleteSurface`)을 식별하고 올바른 핸들러로 라우팅하는 디스패처를 만듭니다.
- **서피스(Surface) 관리**:
  - 각 `surfaceId`로 구분되는 여러 UI 서피스를 관리하는 데이터 구조를 구현합니다.
  - `surfaceUpdate` 처리: 지정된 서피스의 컴포넌트 버퍼에 컴포넌트를 추가하거나 업데이트합니다.
  - `deleteSurface` 처리: 지정된 서피스와 그에 할당된 모든 데이터 및 컴포넌트를 제거합니다.
- **컴포넌트 버퍼링 (인접 리스트)**:
  - 각 서피스별로 모든 컴포넌트 정의를 `id` 기반으로 저장하는 컴포넌트 버퍼(예: `Map<String, Component>`)를 유지합니다.
  - 컨테이너 컴포넌트(`children.explicitList`, `child`, `contentChild` 등)에 있는 `id` 참조를 해석하여 렌더링 시점에 UI 트리를 재구성할 수 있어야 합니다.
- **데이터 모델 저장소 (Data Model Store)**:
  - 각 서피스별로 별도의 데이터 모델 저장소(예: JSON 객체 또는 `Map<String, any>`)를 유지합니다.
  - `dataModelUpdate` 처리: 지정된 `path`의 데이터 모델을 업데이트합니다. `contents`는 인접 리스트 형식(예: `[{ "key": "name", "valueString": "Bob" }]`)으로 전달됩니다.

### 렌더링 로직

- **점진적 렌더링 제어**:
  - 들어오는 모든 `surfaceUpdate` 및 `dataModelUpdate` 메시지를 즉시 렌더링하지 않고 버퍼링합니다.
  - `beginRendering` 처리: 이 메시지는 서피스의 초기 렌더링을 수행하고 루트 컴포넌트 ID를 설정하라는 명시적인 신호로 작동합니다.
    - 지정된 `root` 컴포넌트 ID로부터 렌더링을 시작합니다.
    - `catalogId`가 제공된 경우 해당 컴포넌트 카탈로그가 사용되는지 확인합니다 (생략된 경우 표준 카탈로그가 기본값).
    - 이 메시지에 포함된 전역 `styles` (예: `font`, `primaryColor`)를 적용합니다.
- **데이터 바인딩 해결 (Resolution)**:
  - 컴포넌트 속성에서 발견되는 `BoundValue` 객체에 대한 해결기(resolver)를 구현합니다.
  - `literal*` 값(`literalString`, `literalNumber` 등)만 있는 경우 이를 직접 사용합니다.
  - `path`만 있는 경우 서피스의 데이터 모델에 대해 이를 해석합니다.
  - `path`와 `literal*`이 모두 있는 경우, 먼저 `path` 위치의 데이터 모델을 리터럴 값으로 업데이트한 다음, 컴포넌트 속성을 해당 `path`에 바인딩합니다.
- **동적 리스트 렌더링**:
  - `children.template`이 있는 컨테이너의 경우, `template.dataBinding` 경로(데이터 모델 내의 리스트로 해석됨)에서 찾은 데이터 리스트를 순회합니다.
  - 리스트의 각 항목에 대해 `template.componentId`로 지정된 컴포넌트를 렌더링하며, 항목의 데이터를 템플릿 내의 상대적 데이터 바인딩에 사용할 수 있도록 제공합니다.

### 클라이언트-서버 통신

- **이벤트 핸들링**:
  - 사용자가 `action`이 정의된 컴포넌트와 상호작용할 때 `userAction` 페이로드를 구성합니다.
  - `action.context` 내의 모든 데이터 바인딩을 데이터 모델에 대해 해석하여 값을 확정합니다.
  - 완성된 `userAction` 객체를 서버의 이벤트 처리 엔드포인트로 보냅니다.
- **클라이언트 기능 보고 (Capabilities Reporting)**:
  - 서버로 보내는 **모든** A2A 메시지(메타데이터의 일부)에 `a2uiClientCapabilities` 객체를 포함합니다.
  - 이 객체는 클라이언트가 지원하는 컴포넌트 카탈로그를 `supportedCatalogIds`(예: 표준 0.8 카탈로그 URI 포함)를 통해 선언해야 합니다.
  - 서버가 지원하는 경우, 선택적으로 실시간 커스텀 컴포넌트 정의를 위한 `inlineCatalogs`를 제공할 수 있습니다.
- **오류 보고**: 클라이언트 측 오류(예: 데이터 바인딩 실패, 알 수 없는 컴포넌트 유형)를 서버에 보고하기 위해 `error` 메시지를 보내는 메커니즘을 구현합니다.

## II. 표준 컴포넌트 카탈로그 체크리스트

플랫폼 간 일관된 사용자 경험을 위해 A2UI는 표준 컴포넌트 셋을 정의합니다. 여러분의 클라이언트는 이러한 추상적인 정의를 해당 플랫폼의 네이티브 UI 위젯으로 매핑해야 합니다.

### 기본 콘텐츠

- **Text**: 텍스트 콘텐츠를 렌더링합니다. `text`에 대한 데이터 바인딩과 스타일링을 위한 `usageHint`(h1-h5, body, caption)를 지원해야 합니다.
- **Image**: URL로부터 이미지를 렌더링합니다. `fit`(cover, contain 등) 및 `usageHint`(avatar, hero 등) 속성을 지원해야 합니다.
- **Icon**: 카탈로그에 지정된 표준 셋으로부터 미리 정의된 아이콘을 렌더링합니다.
- **Video**: 지정된 URL에 대한 비디오 플레이어를 렌더링합니다.
- **AudioPlayer**: 지정된 URL에 대한 오디오 플레이어를 렌더링하며, 선택적으로 설명을 포함할 수 있습니다.
- **Divider**: 시각적 구분선을 렌더링하며, `horizontal` 및 `vertical` 축을 모두 지원합니다.

### 레이아웃 및 컨테이너

- **Row**: 자식들을 가로로 배치합니다. `distribution`(justify-content) 및 `alignment`(align-items)를 지원해야 합니다. 자식들은 flex-grow 동작을 제어하기 위한 `weight` 속성을 가질 수 있습니다.
- **Column**: 자식들을 세로로 배치합니다. `distribution` 및 `alignment`를 지원해야 합니다. 자식들은 `weight` 속성을 가질 수 있습니다.
- **List**: 스크롤 가능한 항목 리스트를 렌더링합니다. `direction`(`horizontal`/`vertical`) 및 `alignment`를 지원해야 합니다.
- **Card**: 테두리, 둥근 모서리, 그림자 등을 통해 자식 콘텐츠를 시각적으로 그룹화하는 컨테이너입니다. 단일 `child`를 가집니다.
- **Tabs**: 탭 세트를 표시하는 컨테이너입니다. 각 항목이 `title`과 `child`를 가진 `tabItems`를 포함합니다.
- **Modal**: 메인 콘텐츠 위에 나타나는 대화상자입니다. `entryPointChild`(예: 버튼)에 의해 트리거되며, 활성화 시 `contentChild`를 표시합니다.

### 상호작용 및 입력 컴포넌트

- **Button**: 클릭 시 `userAction`을 발생시키는 요소입니다. `child` 컴포넌트(일반적으로 Text 또는 Icon)를 포함할 수 있어야 하며, `primary` 불리언 값에 따라 스타일이 달라질 수 있습니다.
- **CheckBox**: 불리언 값을 반영하여 토글할 수 있는 체크박스입니다.
- **TextField**: 텍스트 입력을 위한 필드입니다. `label`, `text`(값), `textFieldType`(`shortText`, `longText`, `number`, `obscured`, `date`), 그리고 `validationRegexp`를 지원해야 합니다.
- **DateTimeInput**: 날짜 및/또는 시간 선택을 위한 전용 입력창입니다. `enableDate` 및 `enableTime`을 지원해야 합니다.
- **MultipleChoice**: 리스트(`options`)에서 하나 이상의 옵션을 선택하기 위한 컴포넌트입니다. `maxAllowedSelections`를 지원해야 하며, `selections`를 리스트 또는 단일 값에 바인딩합니다.
- **Slider**: 정의된 범위(`minValue`, `maxValue`) 내의 숫자 값(`value`)을 선택하기 위한 슬라이더입니다.
