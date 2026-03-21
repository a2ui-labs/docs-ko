# 퀵스타트: 5분 만에 A2UI 실행하기

레스토랑 찾기 데모를 실행해 A2UI를 직접 체험해 보세요. 이 가이드를 따라가면 5분 안에 에이전트가 생성한 UI를 볼 수 있습니다.

## 만들게 될 것

이 퀵스타트를 끝내면 다음을 갖게 됩니다.

- A2UI Lit 렌더러가 동작하는 웹 앱
- 동적 UI를 생성하는 Gemini 기반 에이전트
- 폼 생성, 시간 선택, 확인 흐름을 포함한 대화형 레스토랑 찾기
- A2UI 메시지가 에이전트에서 UI까지 흘러가는 방식에 대한 이해

## 사전 준비

시작하기 전에 다음이 준비되어 있어야 합니다.

- **Node.js**(v18 이상) - [다운로드](https://nodejs.org/)
- **uv**(Python 패키지 관리자) - [설치 안내](https://docs.astral.sh/uv/getting-started/installation/)
  Python 에이전트 백엔드를 실행할 때 사용합니다.
- **Gemini API 키** - [Google AI Studio에서 무료로 받기](https://aistudio.google.com/apikey)

> ⚠️ **보안 알림**
>
> 이 데모는 Gemini를 사용해 A2UI 응답을 생성하는 A2A 에이전트를 실행합니다. 에이전트는 여러분의 API 키에 접근하고 Google Gemini API에 요청을 보냅니다. 프로덕션 환경에서 실행하기 전에 항상 에이전트 코드를 검토하세요.

## 1단계: 저장소 복제

```bash
git clone https://github.com/google/A2UI.git
cd A2UI
```

## 2단계: API 키 설정

Gemini API 키를 환경 변수로 설정합니다.

```bash
export GEMINI_API_KEY="your_gemini_api_key_here"
```

## 3단계: Lit 클라이언트로 이동

```bash
cd samples/client/lit
```

## 4단계: 설치 및 실행

데모 실행기를 한 번에 시작합니다.

```bash
npm install
npm run demo:all
```

이 명령은 다음을 수행합니다.

1. 모든 의존성 설치
2. A2UI 렌더러 빌드
3. A2A 레스토랑 찾기 에이전트 시작(Python 백엔드)
4. 개발 서버 시작
5. 브라우저에서 `http://localhost:5173` 열기

> ✅ **데모 실행 중**
>
> 모든 것이 정상이라면 브라우저에 웹 앱이 표시됩니다. 이제 에이전트가 UI를 생성할 준비가 된 것입니다.

## 5단계: 직접 해 보기

웹 앱에서 아래 프롬프트를 시도해 보세요.

1. **"2명 예약해 줘"** - 에이전트가 예약 양식을 생성하는 것을 확인합니다.
2. **"근처의 이탈리안 레스토랑 찾아줘"** - 동적 검색 결과를 확인합니다.
3. **"영업시간이 어떻게 돼?"** - 의도에 따라 달라지는 UI 레이아웃을 살펴봅니다.

### 내부에서 일어나는 일

```text
┌─────────────┐         ┌──────────────┐         ┌────────────────┐
│   메시지 입력 │────────>│   A2A 에이전트 │────────>│  Gemini API    │
│             │         │   (Python)   │         │    (LLM)       │
└─────────────┘         └──────────────┘         └────────────────┘
                               │                         │
                               │ A2UI JSON 생성          │
                               │<────────────────────────┘
                               │
                               │ JSONL 메시지 스트리밍
                               v
                        ┌──────────────┐
                        │    웹 앱     │
                        │ (A2UI Lit    │
                        │   렌더러)    │
                        └──────────────┘
                               │
                               │ 네이티브 컴포넌트 렌더링
                               v
                        ┌──────────────┐
                        │   사용자 UI   │
                        └──────────────┘
```

1. **사용자**가 웹 UI를 통해 메시지를 보냅니다.
2. **A2A 에이전트**가 이를 받아 대화 내용을 Gemini로 전달합니다.
3. **Gemini**가 UI를 설명하는 A2UI JSON 메시지를 생성합니다.
4. **A2A 에이전트**가 이 메시지들을 웹 앱으로 스트리밍합니다.
5. **A2UI 렌더러**가 이를 네이티브 웹 컴포넌트로 변환합니다.
6. **사용자**는 브라우저에서 렌더링된 UI를 봅니다.

## A2UI 메시지 구조

에이전트가 무엇을 보내는지 살펴보겠습니다. 아래는 단순화된 예시입니다.

=== "v0.8 (안정판)"

    **UI 정의**

    ```json
    {"surfaceUpdate": {"surfaceId": "main", "components": [
      {"id": "header", "component": {"Text": {"text": {"literalString": "테이블 예약하기"}, "usageHint": "h1"}}},
      {"id": "date-picker", "component": {"DateTimeInput": {"label": {"literalString": "날짜 선택"}, "value": {"path": "/reservation/date"}, "enableDate": true}}},
      {"id": "submit-text", "component": {"Text": {"text": {"literalString": "예약 확정"}}}},
      {"id": "submit-btn", "component": {"Button": {"child": "submit-text", "action": {"name": "confirm_booking"}}}}
    ]}}
    ```

    **데이터 채우기**

    ```json
    {"dataModelUpdate": {"surfaceId": "main", "contents": [
      {"key": "reservation", "valueMap": [
        {"key": "date", "valueString": "2025-12-15"},
        {"key": "time", "valueString": "19:00"},
        {"key": "guests", "valueInt": 2}
      ]}
    ]}}
    ```

    **렌더링 시작 신호**

    ```json
    {"beginRendering": {"surfaceId": "main", "root": "header"}}
    ```

=== "v0.9 (초안)"

    **서피스 생성**

    ```json
    {"version": "v0.9", "createSurface": {"surfaceId": "main", "catalogId": "https://a2ui.org/specification/v0_9/basic_catalog.json"}}
    ```

    **UI 정의**

    ```json
    {"version": "v0.9", "updateComponents": {"surfaceId": "main", "components": [
      {"id": "header", "component": "Text", "text": "# 테이블 예약하기", "variant": "h1"},
      {"id": "date-picker", "component": "DateTimeInput", "label": "날짜 선택", "value": {"path": "/reservation/date"}, "enableDate": true},
      {"id": "submit-text", "component": "Text", "text": "예약 확정"},
      {"id": "submit-btn", "component": "Button", "child": "submit-text", "variant": "primary", "action": {"event": {"name": "confirm_booking"}}}
    ]}}
    ```

    **데이터 채우기**

    ```json
    {"version": "v0.9", "updateDataModel": {"surfaceId": "main", "path": "/reservation", "value": {"date": "2025-12-15", "time": "19:00", "guests": 2}}}
    ```

    참고: v0.9에서는 `createSurface`가 `beginRendering`을 대체하고, 컴포넌트는 더 평평한 형식을 사용하며, 데이터 모델은 typed adjacency list 대신 일반 JSON 값을 사용합니다.

> 💡 **그냥 JSON입니다**
>
> 이 구조가 얼마나 읽기 쉽고 체계적인지 보이시나요? LLM은 쉽게 생성할 수 있고, 코드 실행 없이 안전하게 전송 및 렌더링할 수 있습니다.

## 다른 데모 살펴보기

저장소에는 여러 다른 데모도 포함되어 있습니다.

### 컴포넌트 갤러리(에이전트 불필요)

사용 가능한 모든 A2UI 컴포넌트를 확인해 보세요.

```bash
npm start -- gallery
```

이 클라이언트 전용 데모는 표준 컴포넌트(Card, Button, TextField, Timeline 등)를 라이브 예시와 코드 샘플과 함께 보여줍니다.

### 연락처 조회 데모

다른 에이전트 사례를 확인해 보세요.

```bash
npm run demo:contact
```

이 데모는 검색 양식과 결과 목록을 생성하는 연락처 조회 에이전트를 보여줍니다.

## 다음 단계

A2UI가 어떻게 동작하는지 확인했다면, 이제 다음으로 이동해 보세요.

- **[핵심 개념 배우기](concepts/overview.md)**: 서피스, 컴포넌트, 데이터 바인딩 이해하기
- **[나만의 클라이언트 설정하기](guides/client-setup.md)**: 자신의 앱에 A2UI 통합하기
- **[에이전트 빌드하기](guides/agent-development.md)**: A2UI 응답을 생성하는 에이전트 만들기
- **[프로토콜 살펴보기](reference/messages.md)**: 기술 사양 자세히 보기

## 문제 해결

### 포트가 이미 사용 중인 경우

포트 5173이 이미 사용 중이면 개발 서버가 자동으로 다음 사용 가능한 포트를 시도합니다. 실제 URL은 터미널 출력을 확인하세요.

### API 키 문제

API 키가 없다는 오류가 보이면 다음을 확인하세요.

1. 키가 내보내졌는지 확인합니다: `echo $GEMINI_API_KEY`
2. [Google AI Studio](https://aistudio.google.com/apikey)에서 받은 유효한 Gemini API 키인지 확인합니다.
3. 다시 설정해 봅니다: `export GEMINI_API_KEY="your_key"`

### 시작 시 연결 오류

브라우저가 열릴 때 `ERR_CONNECTION_REFUSED` 오류가 보이면 걱정하지 마세요. 이는 알려진 레이스 컨디션입니다([#587](https://github.com/google/A2UI/issues/587)). 웹 앱이 Python 에이전트 백엔드보다 더 빨리 시작될 수 있습니다. 몇 초 기다렸다가 새로고침하면 됩니다.

### Python / uv 문제

데모 에이전트는 실행에 [uv](https://docs.astral.sh/uv/)가 필요합니다. `uv: command not found`가 보이면:

```bash
# uv 설치
curl -LsSf https://astral.sh/uv/install.sh | sh

# 확인
uv --version
```

다른 Python 오류가 발생하면:

```bash
# Python 3.10+이 있는지 확인
python3 --version

# 에이전트를 직접 실행해 보기
cd samples/agent/adk/restaurant_finder
uv run .
```

### 여전히 문제가 있나요?

- [GitHub Issues](https://github.com/google/A2UI/issues)를 확인하세요.
- [samples/client/lit/README.md](https://github.com/google/A2UI/tree/main/samples/client/lit)를 검토하세요.
- 커뮤니티 토론에 참여하세요.

## 데모 코드 이해하기

동작 방식을 직접 보고 싶다면 다음을 확인해 보세요.

- **에이전트 코드**: `samples/agent/adk/restaurant_finder/` - Python A2A 에이전트
- **클라이언트 코드**: `samples/client/lit/` - A2UI 렌더러가 포함된 Lit 웹 클라이언트
- **A2UI 렌더러**: `renderers/lit/`(Lit) 및 `renderers/web_core/`(프레임워크 중립 코어)

각 디렉터리에는 자세한 문서가 담긴 자체 README가 있습니다.

---

**축하합니다!** 첫 번째 A2UI 애플리케이션을 실행했습니다. 이제 AI 에이전트가 풍부하고 상호작용적인 UI를 안전한 선언적 JSON 메시지만으로 웹 애플리케이션에 네이티브하게 렌더링할 수 있다는 것을 확인했습니다.
