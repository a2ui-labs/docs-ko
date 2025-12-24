# 퀵스타트: 5분 만에 A2UI 실행하기

레스토랑 찾기 데모를 통해 A2UI를 직접 체험해 보세요. 이 가이드를 따라가면 5분 이내에 에이전트가 생성한 UI를 경험할 수 있습니다.

## 구축할 내용

이 퀵스타트를 마칠 때쯤이면 다음을 확보하게 됩니다:

- ✅ A2UI Lit 렌더러가 작동하는 웹 앱
- ✅ 동적 UI를 생성하는 Gemini 기반 에이전트
- ✅ 양식 생성, 시간 선택 및 예약 확인 흐름이 포함된 대화형 레스토랑 찾기 기능
- ✅ 에이전트에서 UI로 A2UI 메시지가 전달되는 방식에 대한 이해

## 사전 요구 사항

시작하기 전에 다음 사항이 준비되어 있는지 확인하세요:

- **Node.js** (v18 이상) - [여기에서 다운로드](https://nodejs.org/)
- **Gemini API 키** - [Google AI Studio에서 무료로 받기](https://aistudio.google.com/apikey)

!!! warning "보안 알림"
    이 데모는 Gemini를 사용하여 A2UI 응답을 생성하는 A2A 에이전트를 실행합니다. 에이전트는 여러분의 API 키에 접근하며 Google의 Gemini API에 요청을 보냅니다. 프로덕션 환경에서 실행하기 전에 항상 에이전트 코드를 검토하세요.

## 1단계: 저장소 복제

```bash
git clone https://github.com/google/a2ui.git
cd a2ui
```

## 2단계: API 키 설정

Gemini API 키를 환경 변수로 내보냅니다:

```bash
export GEMINI_API_KEY="your_gemini_api_key_here"
```

## 3단계: Lit 클라이언트로 이동

```bash
cd samples/client/lit
```

## 4단계: 설치 및 실행

단일 명령어로 데모 런처를 실행합니다:

```bash
npm install
npm run demo:all
```

이 명령어는 다음 작업을 수행합니다:

1. 모든 의존성 설치
2. A2UI 렌더러 빌드
3. A2A 레스토랑 찾기 에이전트 시작 (Python 백엔드)
4. 개발 서버 시작
5. 브라우저에서 `http://localhost:5173` 열기

!!! success "데모 실행 중"
    모든 것이 정상적으로 작동한다면 브라우저에 웹 앱이 표시될 것입니다. 이제 에이전트가 UI를 생성할 준비가 되었습니다!

## 5단계: 체험해 보기

웹 앱에서 다음 프롬프트를 시도해 보세요:

1. **"2명 예약해 줘"** - 에이전트가 예약 양식을 생성하는 것을 확인하세요.
2. **"근처의 이탈리안 레스토랑 찾아줘"** - 동적 검색 결과를 확인하세요.
3. **"영업시간이 어떻게 돼?"** - 의도에 따라 달라지는 UI 레이아웃을 경험해 보세요.

### 내부 동작 방식

```
┌─────────────┐         ┌──────────────┐         ┌────────────────┐
│   메시지    │────────>│   A2A 에이전트 │────────>│  Gemini API    │
│    입력     │         │   (Python)   │         │    (LLM)       │
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

1. **사용자가** 웹 UI를 통해 메시지를 보냅니다.
2. **A2A 에이전트가** 이를 수신하고 대화 내용을 Gemini로 보냅니다.
3. **Gemini가** UI를 설명하는 A2UI JSON 메시지를 생성합니다.
4. **A2A 에이전트가** 이 메시지들을 웹 앱으로 스트리밍합니다.
5. **A2UI 렌더러가** 이를 네이티브 웹 컴포넌트로 변환합니다.
6. **사용자가** 브라우저에서 렌더링된 UI를 확인합니다.

## A2UI 메시지 구조 분석

에이전트가 무엇을 보내는지 살펴보겠습니다. 다음은 단순화된 JSON 메시지 예시입니다:

### UI 정의

```json
{
  "surfaceUpdate": {
    "surfaceId": "main",
    "components": [
      {
        "id": "header",
        "component": {
          "Text": {
            "text": {"literalString": "테이블 예약하기"},
            "usageHint": "h1"
          }
        }
      },
      {
        "id": "date-picker",
        "component": {
          "DateTimeInput": {
            "label": {"literalString": "날짜 선택"},
            "value": {"path": "/reservation/date"},
            "enableDate": true
          }
        }
      },
      {
        "id": "submit-btn",
        "component": {
          "Button": {
            "child": "submit-text",
            "action": {"name": "confirm_booking"}
          }
        }
      },
      {
        "id": "submit-text",
        "component": {
          "Text": {"text": {"literalString": "예약 확정"}}
        }
      }
    ]
  }
}
```

이 메시지는 서피스(surface)의 UI 컴포넌트들(텍스트 헤더, 날짜 선택기, 버튼)을 정의합니다.

### 데이터 채우기

```json
{
  "dataModelUpdate": {
    "surfaceId": "main",
    "contents": [
      {
        "key": "reservation",
        "valueMap": [
          {"key": "date", "valueString": "2025-12-15"},
          {"key": "time", "valueString": "19:00"},
          {"key": "guests", "valueInt": 2}
        ]
      }
    ]
  }
}
```

이 메시지는 컴포넌트가 바인딩할 수 있는 데이터 모델을 채웁니다.

### 렌더링 시작 신호

```json
{"beginRendering": {"surfaceId": "main", "root": "header"}}
```

이 메시지는 클라이언트에 UI를 렌더링하기에 충분한 정보를 갖추었음을 알립니다.

!!! tip "단순한 JSON 구조"
    이 구조가 얼마나 읽기 쉽고 체계적인지 보이시나요? LLM은 이를 쉽게 생성할 수 있으며, 코드 실행 없이도 안전하게 전송 및 렌더링할 수 있습니다.

## 다른 데모 살펴보기

저장소에는 여러 다른 데모들이 포함되어 있습니다:

### 컴포넌트 갤러리 (에이전트 불필요)

사용 가능한 모든 A2UI 컴포넌트를 확인해 보세요:

```bash
npm start -- gallery
```

이 클라이언트 전용 데모는 모든 표준 컴포넌트(Card, Button, TextField, Timeline 등)를 라이브 예시 및 코드 샘플과 함께 보여줍니다.

### 연락처 조회(Contact Lookup) 데모

다른 에이전트 활용 사례를 시도해 보세요:

```bash
npm run demo:contact
```

이 데모는 검색 양식과 결과 목록을 생성하는 연락처 조회 에이전트를 보여줍니다.

## 다음 단계

A2UI의 작동 모습을 확인했으니, 이제 다음을 할 준비가 되었습니다:

- **[핵심 개념 배우기](concepts/overview.md)**: 서피스, 컴포넌트, 데이터 바인딩 이해하기
- **[나만의 클라이언트 설정하기](guides/client-setup.md)**: 자신의 앱에 A2UI 통합하기
- **[에이전트 빌드하기](guides/agent-development.md)**: A2UI 응답을 생성하는 에이전트 만들기
- **[프로토콜 탐구하기](reference/messages.md)**: 기술 명세 자세히 살펴보기

## 문제 해결

### 포트가 이미 사용 중인 경우

5173 포트가 이미 사용 중이라면 개발 서버가 자동으로 다음 가용 포트를 시도합니다. 터미널 출력에서 실제 URL을 확인하세요.

### API 키 문제

API 키 누락 관련 오류가 표시되는 경우:

1. 키가 제대로 내보내졌는지 확인: `echo $GEMINI_API_KEY`
2. [Google AI Studio](https://aistudio.google.com/apikey)에서 받은 유효한 Gemini API 키인지 확인
3. 다시 내보내기 시도: `export GEMINI_API_KEY="your_key"`

### Python 의존성

데모는 A2A 에이전트를 위해 Python을 사용합니다. Python 오류가 발생하는 경우:

```bash
# Python 3.10 이상이 설치되어 있는지 확인
python3 --version

# 데모는 npm 스크립트를 통해 의존성을 자동 설치해야 합니다.
# 그렇지 않은 경우 수동으로 설치하세요:
cd ../../agent/adk/restaurant_finder
pip install -r requirements.txt
```

### 여전히 문제가 해결되지 않나요?

- [GitHub Issues](https://github.com/google/a2ui/issues)를 확인하세요.
- [samples/client/lit/README.md](https://github.com/google/a2ui/tree/main/samples/client/lit)를 검토하세요.
- 커뮤니티 토론에 참여하세요.

## 데모 코드 이해하기

어떻게 작동하는지 궁금하신가요? 다음 항목들을 확인해 보세요:

- **에이전트 코드**: `samples/agent/adk/restaurant_finder/` - Python A2A 에이전트
- **클라이언트 코드**: `samples/client/lit/` - A2UI 렌더러가 포함된 Lit 웹 클라이언트
- **A2UI 렌더러**: `web-lib/` - 웹 렌더러 구현체

각 디렉토리에는 상세 문서가 포함된 README가 있습니다.

---

**축하합니다!** 첫 번째 A2UI 애플리케이션을 성공적으로 실행했습니다. AI 에이전트가 안전하고 선언적인 JSON 메시지를 통해 웹 애플리케이션에서 네이티브하게 렌더링되는 풍부하고 대화형인 UI를 생성하는 방법을 확인했습니다.
