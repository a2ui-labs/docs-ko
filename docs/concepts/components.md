# 컴포넌트 및 구조

A2UI는 컴포넌트 계층 구조를 위해 **인접 리스트(adjacency list) 모델**을 사용합니다. 중첩된 JSON 트리 대신, 컴포넌트들은 ID 참조를 가진 평면적인 리스트로 구성됩니다.

## 왜 평면 리스트를 사용하나요?

**기존의 중첩 방식:**
- LLM이 한 번에 완벽한 중첩 구조를 생성해야 함
- 깊게 중첩된 컴포넌트를 업데이트하기 어려움
- 증분 스트리밍이 어려움

**A2UI 인접 리스트:**
- ✅ 평면적인 구조로 LLM이 생성하기 쉬움
- ✅ 컴포넌트를 증분 방식으로 전송 가능
- ✅ ID를 통해 어떤 컴포넌트든 업데이트 가능
- ✅ 구조와 데이터의 명확한 분리

## 인접 리스트 모델

```json
{
  "surfaceUpdate": {
    "components": [
      {"id": "root", "component": {"Column": {"children": {"explicitList": ["greeting", "buttons"]}}}},
      {"id": "greeting", "component": {"Text": {"text": {"literalString": "안녕하세요"}}}},
      {"id": "buttons", "component": {"Row": {"children": {"explicitList": ["cancel-btn", "ok-btn"]}}}},
      {"id": "cancel-btn", "component": {"Button": {"child": "cancel-text", "action": {"name": "cancel"}}}},
      {"id": "cancel-text", "component": {"Text": {"text": {"literalString": "취소"}}}},
      {"id": "ok-btn", "component": {"Button": {"child": "ok-text", "action": {"name": "ok"}}}},
      {"id": "ok-text", "component": {"Text": {"text": {"literalString": "확인"}}}}
    ]
  }
}
```

컴포넌트는 중첩이 아닌 ID를 통해 자식을 참조합니다.

## 컴포넌트 기본 사항

모든 컴포넌트는 다음을 가집니다:

1. **ID**: 고유 식별자 (`"welcome-message"`)
2. **Type**: 컴포넌트 유형 (`Text`, `Button`, `Card`)
3. **Properties**: 해당 유형에 특화된 구성 설정

```json
{"id": "welcome", "component": {"Text": {"text": {"literalString": "안녕하세요"}, "usageHint": "h1"}}}
```

## 표준 카탈로그

A2UI는 용도별로 정리된 표준 컴포넌트 카탈로그를 정의합니다:

- **레이아웃 (Layout)**: Row, Column, List - 다른 컴포넌트들을 배치
- **표시 (Display)**: Text, Image, Icon, Video, Divider - 정보 표시
- **상호작용 (Interactive)**: Button, TextField, CheckBox, DateTimeInput, Slider - 사용자 입력 수신
- **컨테이너 (Container)**: Card, Tabs, Modal - 콘텐츠 그룹화 및 조직화

예시가 포함된 전체 컴포넌트 갤러리는 [컴포넌트 레퍼런스](../reference/components.md)를 참조하세요.

## 정적 자식 vs 동적 자식

**정적 (`explicitList`)** - 고정된 자식 ID 리스트:
```json
{"children": {"explicitList": ["back-btn", "title", "menu-btn"]}}
```

**동적 (`template`)** - 데이터 배열로부터 자식 생성:
```json
{"children": {"template": {"dataBinding": "/items", "componentId": "item-template"}}}
```

`/items`의 각 항목에 대해 `item-template`을 렌더링합니다. 자세한 내용은 [데이터 바인딩](data-binding.md)을 참조하세요.

## 값 채우기 (Hydrating)

컴포넌트는 두 가지 방식으로 값을 가져옵니다:

**리터럴 (Literal)** - 고정 값: `{"text": {"literalString": "환영합니다"}}`
**데이터 바인딩 (Data-bound)** - 데이터 모델에서 가져옴: `{"text": {"path": "/user/name"}}`

LLM은 리터럴 값을 가진 컴포넌트를 생성하거나, 동적 콘텐츠를 위해 데이터 경로에 바인딩할 수 있습니다.

## 서피스(Surface) 구성

컴포넌트들은 **서피스**(위젯)로 구성됩니다:

1. LLM이 `surfaceUpdate`를 통해 컴포넌트 정의를 생성합니다.
2. LLM이 `dataModelUpdate`를 통해 데이터를 채웁니다.
3. LLM이 `beginRendering`을 통해 렌더링 신호를 보냅니다.
4. 클라이언트가 모든 컴포넌트를 네이티브 위젯으로 렌더링합니다.

서피스는 하나의 완전하고 결집력 있는 UI(양식, 대시보드, 채팅 등)를 의미합니다.

## 증분 업데이트 (Incremental Updates)

**추가** - 새로운 컴포넌트 ID를 포함한 새 `surfaceUpdate` 전송
**업데이트** - 기존 ID와 새 속성을 포함한 `surfaceUpdate` 전송
**삭제** - 삭제할 ID를 제외하도록 부모의 `children` 리스트 업데이트

평면적인 구조 덕분에 모든 업데이트가 단순한 ID 기반 작업으로 처리됩니다.

## 커스텀 컴포넌트

표준 카탈로그 외에도, 클라이언트는 도메인 특정 요구 항에 맞는 커스텀 컴포넌트를 정의할 수 있습니다:

- **방법**: 여러분의 렌더러에 커스텀 컴포넌트 유형 등록
- **대상**: 차트, 지도, 커스텀 시각화, 특수 위젯 등
- **보안**: 커스텀 컴포넌트도 클라이언트의 신뢰할 수 있는 카탈로그의 일부로 관리됨

커스텀 컴포넌트는 클라이언트 렌더러에서 LLM으로 제공(advertised)됩니다. 그러면 LLM은 표준 카탈로그와 함께 이를 사용할 수 있습니다.

구현 세부 사항은 [커스텀 컴포넌트 가이드](../guides/custom-components.md)를 참조하세요.

## 베스트 프랙티스

1. **설명적인 ID 사용**: `"c1"` 대신 `"user-profile-card"`와 같이 사용하세요.
2. **얕은 계층 구조**: 과도하게 깊은 중첩을 피하세요.
3. **구조와 콘텐츠 분리**: 리터럴 대신 데이터 바인딩을 활용하세요.
4. **템플릿 재사용**: 하나의 템플릿으로 동적 자식을 통해 여러 인스턴스를 만드세요.
