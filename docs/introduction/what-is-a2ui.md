# A2UI란 무엇인가요?

**A2UI (Agent to UI)는 에이전트 주도 인터페이스를 위한 선언적 UI 프로토콜입니다.** AI 에이전트는 임의의 코드를 실행하지 않고도 웹, 모바일, 데스크톱 등 다양한 플랫폼에서 네이티브하게 렌더링되는 풍부하고 대화형인 UI를 생성할 수 있습니다.

## 문제점

**텍스트 전용 에이전트 상호작용은 비효율적입니다:**

```
사용자: "내일 오후 7시에 2명 예약해 줘"
에이전트: "네, 어느 날짜로 해드릴까요?"
사용자: "내일이요"
에이전트: "몇 시인가요?"
...
```

**더 나은 방식:** 에이전트가 날짜 선택기, 시간 선택기, 제출 버튼이 포함된 양식을 생성합니다. 사용자는 텍스트가 아닌 UI와 상호작용합니다.

## 과제

멀티 에이전트 시스템에서 에이전트는 종종 원격(다른 서버나 조직)에서 실행됩니다. 에이전트는 여러분의 UI를 직접 조작할 수 없으며 반드시 메시지를 보내야 합니다.

**기존 접근 방식:** iframe에 HTML/JavaScript를 포함시켜 전송

- 무겁고 시각적으로 일관성이 없음
- 보안 복잡성 증대
- 앱의 스타일과 어울리지 않음

**필요한 것:** 데이터처럼 안전하고 코드처럼 표현력이 풍부한 UI 전송 방식.

## 해결책

A2UI는 다음과 같은 기능을 수행하는 UI 설명 JSON 메시지입니다:

- LLM이 구조화된 출력으로 생성 가능
- 모든 전송 계층(A2A, AG UI, SSE, WebSockets)을 통해 전달 가능
- 클라이언트가 자체 네이티브 컴포넌트를 사용하여 렌더링

**결과:** 클라이언트가 보안과 스타일을 제어하며, 에이전트가 생성한 UI가 네이티브 앱처럼 느껴집니다.

### 예시

```json
{"surfaceUpdate": {"surfaceId": "booking", "components": [
  {"id": "title", "component": {"Text": {"text": {"literalString": "테이블 예약하기"}, "usageHint": "h1"}}},
  {"id": "datetime", "component": {"DateTimeInput": {"value": {"path": "/booking/date"}, "enableDate": true}}},
  {"id": "submit-text", "component": {"Text": {"text": {"literalString": "확인"}}}},
  {"id": "submit-btn", "component": {"Button": {"child": "submit-text", "action": {"name": "confirm_booking"}}}}
]}}
```

```json
{"dataModelUpdate": {"surfaceId": "booking", "contents": [
  {"key": "booking", "valueMap": [{"key": "date", "valueString": "2025-12-16T19:00:00Z"}]}
]}}
```

```json
{"beginRendering": {"surfaceId": "booking", "root": "title"}}
```

클라이언트는 이러한 메시지를 네이티브 컴포넌트(Angular, Flutter, React 등)로 렌더링합니다.

## 핵심 가치

**1. 보안:** 코드가 아닌 선언적 데이터입니다. 에이전트는 클라이언트의 신뢰할 수 있는 카탈로그에 있는 컴포넌트만 요청할 수 있습니다. 코드 실행 위험이 없습니다.

**2. 네이티브 느낌:** iframe을 사용하지 않습니다. 클라이언트가 자체 UI 프레임워크로 렌더링합니다. 앱의 스타일, 접근성, 성능을 그대로 상속받습니다.

**3. 이식성:** 하나의 에이전트 응답이 모든 곳에서 작동합니다. 동일한 JSON이 웹(Lit/Angular/React), 모바일(Flutter/SwiftUI/Jetpack Compose), 데스크톱에서 렌더링됩니다.

## 설계 원칙

**1. LLM 친화적:** ID 참조 구조를 가진 평면적인 컴포넌트 리스트입니다. 점진적으로 생성하고, 실수를 수정하고, 스트리밍하기 쉽습니다.

**2. 프레임워크 독립적:** 에이전트는 추상적인 컴포넌트 트리를 보냅니다. 클라이언트는 이를 네이티브 위젯(웹/모바일/데스크톱)에 매핑합니다.

**3. 관심사 분리:** UI 구조, 애플리케이션 상태, 클라이언트 렌더링의 세 계층으로 나뉩니다. 데이터 바인딩, 반응형 업데이트, 깔끔한 아키텍처를 가능하게 합니다.

## A2UI가 아닌 것

- 프레임워크가 아님 (프로토콜임)
- HTML의 대체제가 아님 (정적 사이트가 아닌 에이전트 생성 UI용)
- 강력한 스타일링 시스템이 아님 (클라이언트가 스타일링을 제어하며 서버 측 스타일링 지원은 제한적임)
- 웹에만 국한되지 않음 (모바일 및 데스크톱에서도 작동)

## 핵심 개념

- **서피스 (Surface)**: 컴포넌트가 그려지는 캔버스 (대화상자, 사이드바, 메인 뷰 등)
- **컴포넌트 (Component)**: UI 요소 (Button, TextField, Card 등)
- **데이터 모델 (Data Model)**: 애플리케이션 상태로, 컴포넌트가 이에 바인딩됨
- **카탈로그 (Catalog)**: 사용 가능한 컴포넌트 유형 목록
- **메시지 (Message)**: JSON 객체 (`surfaceUpdate`, `dataModelUpdate`, `beginRendering` 등)

유사한 프로젝트와의 비교는 [에이전트 UI 생태계](agent-ui-ecosystem.md)를 참조하세요.
