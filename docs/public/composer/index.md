---
render_macros: false
---

# A2UI Composer

**A2UI Composer**로 A2UI 위젯을 대화형으로 만들어 보세요.
![A2UI Composer 작업 공간](../assets/composer_workspace.png)

1. [A2UI Composer](https://a2ui-project.github.io/composer/)로 이동합니다.

2. 기본적으로 Angular 기반 렌더러가 제공하는 Basic 카탈로그를 사용하게 됩니다.

3. Gemini 채팅으로 A2UI 인터페이스를 만들기 시작하세요! (**참고**: Gemini API
   키가 필요합니다. [아래](#gemini-api)를 참고하세요.)

## Composer UI 사용하기

Composer 작업 공간은 A2UI 서피스의 개발과 디버깅을 돕기 위해 여러 개의 대화형
패널로 나뉘어 있습니다.

- **Gemini 어시스턴트:** Gemini 기반의 채팅 인터페이스입니다. 자연어 프롬프트로
  새 레이아웃을 생성하거나, 기존 JSON을 다듬거나, 시각적 속성을 수정하도록
  Gemini에 요청할 수 있습니다.
- 클립 아이콘
  ![클립 아이콘](../assets/composer_paperclip.png){style="width:30px;height:30px;display:inline;vertical-align:middle;"}을
  클릭해 **첨부 파일**(예: 목업)을 업로드할 수 있습니다.
- 카메라 아이콘
  ![카메라 아이콘](../assets/composer_camera.png){style="width:30px;height:30px;display:inline;vertical-align:middle;"}을
  클릭해 현재 A2UI 인터페이스의 **스크린샷을 포함**할 수 있습니다.

- **렌더링된 A2UI 미리보기:** 현재 컴포넌트의 실시간 시각적 미리보기를
  표시합니다.

- **A2UI JSON 편집기:** UI 구조와 컴포넌트 계층을 정의하는 원본 JSON 페이로드를
  표시합니다. 이곳에서 직접 편집하면 렌더링된 미리보기가 즉시 갱신됩니다.
- 편집기에는 **[마우스를 올리면 표시되는 툴팁](../assets/composer_editor_tooltip.png)**이
  있어 해당 A2UI 요소의 설명을 보여줍니다.
- 다른 곳에서 가져온 A2UI JSON이 있다면 이 패널에 붙여 넣을 수 있습니다.
  마우스 오른쪽 버튼을 클릭해 JSON을 포매팅할 수도 있습니다.

- **디버그 및 검사 탭(하단):**
    - **Data Model:** UI 컴포넌트에 바인딩된 런타임 상태/데이터 값을 검사하고
      수정합니다. 여기서 변경한 내용은 미리보기에 전파되며, 미리보기에서 입력한
      사용자 입력은 이 모델에 반영됩니다.
    - **Events:** 렌더링된 컴포넌트가 발생시킨 사용자 상호작용 이벤트(클릭,
      입력, 선택 등)를 기록합니다.
    - **Errors:** 렌더링, JSON 파싱, API 실패에서 발생한 오류를 표시합니다.
    - **Raw Messages:** Composer와 렌더러 사이의 통신은 물론 Gemini와의
      상호작용까지 보여줍니다. ([아래](#raw-messages)에서 자세히 확인하세요.)

## 컴포넌트 갤러리

![컴포넌트의 A2UI 사용법과 속성을 보여주는 스크린샷](../assets/composer_components_gallery.png)

컴포넌트 갤러리에서는 현재 A2UI 카탈로그가 제공하는 모든 컴포넌트를 둘러볼 수
있습니다. 각 컴포넌트마다 렌더링된 예시와 그 렌더링을 만들어 내는 A2UI JSON이
함께 제공됩니다. Usage 패널 상단의 복사 아이콘
![복사 아이콘](../assets/composer_copy.png){style="width:30px;height:30px;display:inline;vertical-align:middle;"}을
사용하면 해당 컴포넌트의 전체 A2UI JSON을 복사할 수 있습니다.

페이지 하단에는 컴포넌트의 모든 속성을 설명, 타입, 필수 여부와 함께 나열한 표가
있습니다.

## 설정

### 렌더러 애플리케이션

설정 페이지에서 사용할 렌더러 애플리케이션을 변경할 수 있습니다. 현재 세 가지
렌더러 애플리케이션이 미리 로드되어 있습니다.

- Angular Basic Catalog
- Lit Basic Catalog
- React Basic Catalog

다른 렌더러 앱을 만들고 있다면 드롭다운에서 "Custom"을 선택하고 텍스트 상자에
URL을 입력하세요. (렌더러 앱을 만드는 방법은
[A2UI Composer 연동](./composer_renderer_integration.md)을 참고하세요.)

### Gemini API 키 {#gemini-api}

이 페이지에서 Gemini API 키를 입력하면 Gemini 채팅 기능을 사용할 수 있습니다.

API 키를 발급받으려면 다음과 같이 하세요.

1. [Google AI Studio](https://aistudio.google.com/api-keys)로 이동해 Google
   계정으로 로그인합니다.
2. Create API key를 클릭합니다.
3. 안내에 따라 Google Cloud 프로젝트를 선택하거나 새로 만든 뒤 Create key를
   클릭합니다.
4. 발급받은 키를 안전한 곳에 보관하세요!

A2UI Composer는 이 키를 암호화하여
[Web Crypto API](https://developer.mozilla.org/ko/docs/Web/API/Web_Crypto_API)로
브라우저의 보안 데이터베이스에 로컬 저장합니다. Google을 비롯한 그 누구도 이
키에 접근할 수 없습니다.

## 진행 중인 작업

A2UI 팀은 다음 개선 사항을 적극적으로 진행하고 있습니다.

- **지연 시간 단축:** Gemini 기반 워크플로의 지연 시간을 개선합니다.
- **시각적 이해:** 이미 렌더링된 서피스에 대한 시각적 이해를 높이도록 Gemini
  기반 워크플로를 개선합니다.

## Raw Messages

Composer와 렌더러 사이에 오가는 메시지는 문제를 해결하고 내부에서 무슨 일이
일어나는지 이해하는 데 도움이 됩니다. 이러한 메시지에는 다음이 포함됩니다.

- **RENDERER_READY**: 렌더러가 부트스트랩을 완전히 마친 뒤에 보냅니다.
- **A2UI_CATALOG**: (Composer의 요청에 따라) 렌더러가 보냅니다. 렌더러가
  지원하는 전체 A2UI 카탈로그를 담고 있습니다.
- **COMPONENT_USAGES**: (Composer의 요청에 따라) 렌더러가 보내며, 컴포넌트 갤러리
  페이지를 구성하는 데 필요한 데이터를 담고 있습니다.

A2UI 컴포넌트의 데이터 모델이 변경될 때마다 **DATA_MODEL_CHANGE** 메시지가
기록되어, 에이전트에 다시 전송될 `updateDataModel` 메시지를 보여줍니다.

또한 Gemini 채팅 기능을 사용할 때는 다음 메시지를 확인할 수 있습니다.

- **LLM_REQUEST**: LLM에 전송된 전체 요청(시스템 프롬프트 포함)을 보여줍니다.
- **LLM_RESPONSE**: LLM에서 받은 전체 응답을 보여줍니다.
