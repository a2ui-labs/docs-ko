# 메시지 유형

이 레퍼런스는 모든 A2UI 메시지 유형에 대한 상세 문서를 제공합니다.

## 메시지 형식

모든 A2UI 메시지는 JSON Lines (JSONL)로 전송되는 JSON 객체입니다. 각 라인은 정확히 하나의 메시지를 포함하며, 각 메시지는 다음 네 가지 키 중 하나를 반드시 포함해야 합니다:

- `beginRendering`
- `surfaceUpdate`
- `dataModelUpdate`
- `deleteSurface`

## beginRendering

클라이언트가 서피스의 초기 렌더링을 수행하기에 충분한 정보를 가지고 있음을 알립니다.

### 스키마

```typescript
{
  beginRendering: {
    surfaceId: string;      // 필수: 고유 서피스 식별자
    root: string;           // 필수: 렌더링할 루트 컴포넌트의 ID
    catalogId?: string;     // 선택: 컴포넌트 카탈로그 URL
    styles?: object;        // 선택: 스타일링 정보
  }
}
```

### 속성 (Properties)

| 속성 | 타입 | 필수 여부 | 설명 |
| ----------- | ------ | -------- | --------------------------------------------------------------------------------------- |
| `surfaceId` | string | ✅ | 이 서피스의 고유 식별자입니다. |
| `root` | string | ✅ | 이 서피스의 UI 트리 루트가 될 컴포넌트의 `id`입니다. |
| `catalogId` | string | ❌ | 컴포넌트 카탈로그 식별자입니다. 생략 시 v0.8 표준 카탈로그가 기본값입니다. |
| `styles` | object | ❌ | 카탈로그에 정의된 UI 및 테마 관련 스타일링 정보입니다. |

### 예시

**기본 렌더링 신호:**

```json
{
  "beginRendering": {
    "surfaceId": "main",
    "root": "root-component"
  }
}
```

**커스텀 카탈로그 사용:**

```json
{
  "beginRendering": {
    "surfaceId": "custom-ui",
    "root": "root-custom",
    "catalogId": "https://my-company.com/a2ui/v0.8/my_custom_catalog.json"
  }
}
```

### 사용 참고 사항

- 클라이언트가 루트 컴포넌트와 그 초기 자식들에 대한 컴포넌트 정의를 수신한 후에 전송되어야 합니다.
- 클라이언트는 `surfaceUpdate` 및 `dataModelUpdate` 메시지를 버퍼링해야 하며, 해당 서피스의 `beginRendering` 메시지를 받은 후에만 UI를 렌더링해야 합니다.

---

## surfaceUpdate

서피스 내의 컴포넌트를 추가하거나 업데이트합니다.

### 스키마

```typescript
{
  surfaceUpdate: {
    surfaceId: string;        // 필수: 대상 서피스
    components: Array<{       // 필수: 컴포넌트 리스트
      id: string;             // 필수: 컴포넌트 ID
      component: {            // 필수: 컴포넌트 데이터 래퍼
        [ComponentType]: {    // 필수: 정확히 하나의 컴포넌트 유형
          ...properties       // 컴포넌트별 속성
        }
      }
    }>
  }
}
```

### 속성 (Properties)

| 속성 | 타입 | 필수 여부 | 설명 |
| ------------ | ------ | -------- | ------------------------------ |
| `surfaceId` | string | ✅ | 업데이트할 서피스의 ID |
| `components` | array | ✅ | 컴포넌트 정의 배열 |

### 컴포넌트 객체

`components` 배열의 각 객체는 다음을 포함해야 합니다:

- `id` (string, 필수): 서피스 내에서 고유한 식별자
- `component` (object, 필수): 컴포넌트 유형(예: `Text`, `Button`)을 유일한 키로 가지는 래퍼 객체

### 예시

**단일 컴포넌트:**

```json
{
  "surfaceUpdate": {
    "surfaceId": "main",
    "components": [
      {
        "id": "greeting",
        "component": {
          "Text": {
            "text": {"literalString": "안녕하세요!"},
            "usageHint": "h1"
          }
        }
      }
    ]
  }
}
```

**복수 컴포넌트 (인접 리스트 구조):**

```json
{
  "surfaceUpdate": {
    "surfaceId": "main",
    "components": [
      {
        "id": "root",
        "component": {
          "Column": {
            "children": {"explicitList": ["header", "body"]}
          }
        }
      },
      {
        "id": "header",
        "component": {
          "Text": {
            "text": {"literalString": "환영합니다"}
          }
        }
      },
      {
        "id": "body",
        "component": {
          "Card": {
            "child": "content"
          }
        }
      },
      {
        "id": "content",
        "component": {
          "Text": {
            "text": {"path": "/message"}
          }
        }
      }
    ]
  }
}
```

**기존 컴포넌트 업데이트:**

```json
{
  "surfaceUpdate": {
    "surfaceId": "main",
    "components": [
      {
        "id": "greeting",
        "component": {
          "Text": {
            "text": {"literalString": "안녕하세요, Alice님!"},
            "usageHint": "h1"
          }
        }
      }
    ]
  }
}
```

`id: "greeting"`인 컴포넌트가 중복 생성되지 않고 업데이트됩니다.

### 사용 참고 사항

- 트리 루트 역할을 수행할 하나의 컴포넌트가 `beginRendering` 메시지에서 `root`로 지정되어야 합니다.
- 컴포넌트들은 인접 리스트(ID 참조를 가진 평면 구조)를 형성합니다.
- 기존에 존재하는 ID로 컴포넌트를 보내면 해당 컴포넌트가 업데이트됩니다.
- 자식 요소들은 ID를 통해 참조됩니다.
- 컴포넌트들은 증분식(스트리밍)으로 추가될 수 있습니다.

### 오류 코드

| 오류 | 원인 | 해결 방법 |
| ---------------------- | -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| 서피스를 찾을 수 없음 | `surfaceId`가 존재하지 않음 | 특정 서피스에 대해 일관된 `surfaceId`를 사용하고 있는지 확인하세요. 서피스는 첫 업데이트 시 암시적으로 생성됩니다. |
| 잘못된 컴포넌트 유형 | 알 수 없는 컴포넌트 유형 | 협의된 카탈로그 내에 해당 컴포넌트 유형이 존재하는지 확인하세요. |
| 잘못된 속성 | 해당 유형에 존재하지 않는 속성 | 카탈로그 스키마를 확인하세요. |
| 순환 참조 | 컴포넌트가 자기 자신을 자식으로 참조함 | 컴포넌트 계층 구조를 수정하세요. |

---

## dataModelUpdate

컴포넌트가 바인딩되는 데이터 모델을 업데이트합니다.

### 스키마

```typescript
{
  dataModelUpdate: {
    surfaceId: string;      // 필수: 대상 서피스
    path?: string;          // 선택: 모델 내의 특정 위치 경로
    contents: Array<{       // 필수: 데이터 항목들
      key: string;
      valueString?: string;
      valueNumber?: number;
      valueBoolean?: boolean;
      valueMap?: Array<{...}>;
    }>
  }
}
```

### 속성 (Properties)

| 속성 | 타입 | 필수 여부 | 설명 |
| ----------- | ------ | -------- | ---------------------------------------------------------------------------------------------------- |
| `surfaceId` | string | ✅ | 업데이트할 서피스의 ID입니다. |
| `path` | string | ❌ | 데이터 모델 내의 특정 위치 경로(예: 'user')입니다. 생략 시 루트에 적용됩니다. |
| `contents` | array | ✅ | 인접 리스트 형식의 데이터 항목 배열입니다. 각 항목은 `key`와 타입이 지정된 `value*` 속성을 가집니다. |

### `contents` 인접 리스트

`contents` 배열은 키-값 쌍의 리스트입니다. 배열 내의 각 객체는 `key` 속성과 정확히 하나의 `value*` 속성(`valueString`, `valueNumber`, `valueBoolean`, 또는 `valueMap`)을 가져야 합니다. 이 구조는 LLM이 타입을 생성하기에 용이하며, 일반적인 `value` 필드에서 타입을 추론해야 하는 문제를 방지합니다.

### 예시

**전체 모델 초기화:**

`path`가 생략되면, `contents`는 해당 서피스의 전체 데이터 모델을 교체합니다.

```json
{
  "dataModelUpdate": {
    "surfaceId": "main",
    "contents": [
      {
        "key": "user",
        "valueMap": [
          { "key": "name", "valueString": "Alice" },
          { "key": "email", "valueString": "alice@example.com" }
        ]
      },
      { "key": "items", "valueMap": [] }
    ]
  }
}
```

**중첩된 속성 업데이트:**

`path`가 제공되면, 해당 위치의 데이터만 업데이트됩니다.

```json
{
  "dataModelUpdate": {
    "surfaceId": "main",
    "path": "user",
    "contents": [
      { "key": "email", "valueString": "alice@newdomain.com" }
    ]
  }
}
```

이 메시지는 `/user/name`에 영향을 주지 않고 `/user/email`만 변경합니다.

### 사용 참고 사항

- 데이터 모델은 서피스별로 관리됩니다.
- 컴포넌트는 바인딩된 데이터가 변경되면 자동으로 다시 렌더링됩니다.
- 전체 모델을 교체하기보다 특정 경로에 대한 세밀한 업데이트를 권장합니다.
- 데이터 모델은 순수 JSON 객체입니다.
- 모든 데이터 변환(예: 날짜 포맷팅)은 서버에서 `dataModelUpdate` 메시지를 보내기 전에 완료되어야 합니다.

---

## deleteSurface

서피스와 그에 포함된 모든 컴포넌트 및 데이터를 제거합니다.

### 스키마

```typescript
{
  deleteSurface: {
    surfaceId: string;        // 필수: 삭제할 서피스
  }
}
```

### 속성 (Properties)

| 속성 | 타입 | 필수 여부 | 설명 |
| ----------- | ------ | -------- | --------------------------- |
| `surfaceId` | string | ✅ | 삭제할 서피스의 ID |

### 예시

**서피스 삭제:**

```json
{
  "deleteSurface": {
    "surfaceId": "modal"
  }
}
```

**복수 서피스 삭제:**

```json
{"deleteSurface": {"surfaceId": "sidebar"}}
{"deleteSurface": {"surfaceId": "content"}}
```

### 사용 참고 사항

- 서피스와 관련된 모든 컴포넌트를 제거합니다.
- 서피스의 데이터 모델을 초기화합니다.
- 클라이언트는 UI에서 해당 서피스를 제거해야 합니다.
- 존재하지 않는 서피스를 삭제하려 해도 안전합니다 (no-op).
- 모달이나 대화상자를 닫을 때, 또는 다른 화면으로 이동할 때 사용하세요.

### 오류

| 오류 | 원인 | 해결 방법 |
| ------------------------------- | ----- | -------- |
| (없음 - 삭제 동작은 멱등성을 가짐) | | |

---

## 메시지 순서 (Message Ordering)

### 요구 사항

1. `beginRendering`은 해당 서피스의 초기 `surfaceUpdate` 메시지들 이후에 와야 합니다.
2. `surfaceUpdate`는 `dataModelUpdate` 전후 어디에나 올 수 있습니다.
3. 서로 다른 서피스에 대한 메시지들은 독립적입니다.
4. 여러 메시지가 하나의 서피스를 점진적으로 업데이트할 수 있습니다.

### 권장 순서

```jsonl
{"surfaceUpdate": {"surfaceId": "main", "components": [...]}}
{"dataModelUpdate": {"surfaceId": "main", "contents": {...}}}
{"beginRendering": {"surfaceId": "main", "root": "root-id"}}
```

### 점진적 구축 예시

```jsonl
{"surfaceUpdate": {"surfaceId": "main", "components": [...]}}  // 헤더 (Header)
{"surfaceUpdate": {"surfaceId": "main", "components": [...]}}  // 본문 (Body)
{"beginRendering": {"surfaceId": "main", "root": "root-id"}} // 초기 렌더링
{"surfaceUpdate": {"surfaceId": "main", "components": [...]}}  // 푸터 (Footer) (초기 렌더링 이후)
{"dataModelUpdate": {"surfaceId": "main", "contents": {...}}}   // 데이터 채우기
```

## 검증 (Validation)

모든 메시지는 다음 스키마에 대해 검증되어야 합니다:

- **[server_to_client.json](https://github.com/google/A2UI/blob/main/specification/0.8/json/server_to_client.json)**: 메시지 엔벨로프(envelope) 스키마
- **[standard_catalog_definition.json](https://github.com/google/A2UI/blob/main/specification/0.8/json/standard_catalog_definition.json)**: 컴포넌트 스키마

## 추가 학습 자료

- **[컴포넌트 갤러리](components.md)**: 사용 가능한 모든 컴포넌트 유형
- **[데이터 바인딩 가이드](../concepts/data-binding.md)**: 데이터 바인딩 동작 원리
- **[에이전트 개발 가이드](../guides/agent-development.md)**: 유효한 메시지 생성 방법
