# 메시지 유형

이 레퍼런스는 모든 A2UI 메시지 유형에 대한 상세 문서를 제공합니다.

## 메시지 형식

모든 A2UI 메시지는 JSON Lines (JSONL)로 전송되는 JSON 객체입니다. 각 라인은 정확히 하나의 메시지를 포함합니다.

=== "v0.8 메시지 유형"

    - `beginRendering` — 클라이언트에게 서피스를 렌더링하도록 신호를 보냅니다
    - `surfaceUpdate` — 컴포넌트를 추가하거나 업데이트합니다
    - `dataModelUpdate` — 애플리케이션 상태를 업데이트합니다
    - `deleteSurface` — 서피스를 제거합니다

=== "v0.9 메시지 유형"

    - `createSurface` — 서피스를 생성하고 카탈로그를 지정합니다
    - `updateComponents` — 컴포넌트를 추가하거나 업데이트합니다
    - `updateDataModel` — 애플리케이션 상태를 업데이트합니다
    - `deleteSurface` — 서피스를 제거합니다

    모든 v0.9 메시지는 `"version": "v0.9"` 필드를 포함합니다.

---

## beginRendering (v0.8) / createSurface (v0.9)

클라이언트가 서피스를 초기화하고 렌더링하도록 신호를 보냅니다.

=== "v0.8 — `beginRendering`"

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

    | 속성        | 타입   | 필수 여부 | 설명                                                                                     |
    | ----------- | ------ | -------- | ----------------------------------------------------------------------------------------- |
    | `surfaceId` | string | ✅        | 이 서피스의 고유 식별자입니다.                                                            |
    | `root`      | string | ✅        | 이 서피스의 UI 트리 루트가 될 컴포넌트의 `id`입니다.                                       |
    | `catalogId` | string | ❌        | 컴포넌트 카탈로그 식별자입니다. 생략 시 v0.8 기본 카탈로그가 기본값입니다.                 |
    | `styles`    | object | ❌        | 카탈로그에 정의된 UI 스타일링 정보입니다.                                                 |

    ### 예시

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

    컴포넌트 정의를 전송한 후에 보내야 합니다. 클라이언트는 `beginRendering`을 받을 때까지 `surfaceUpdate` 및 `dataModelUpdate` 메시지를 버퍼링합니다.

=== "v0.9 — `createSurface`"

    ### 스키마

    ```typescript
    {
      version: "v0.9";
      createSurface: {
        surfaceId: string;      // 필수: 고유 서피스 식별자
        catalogId: string;      // 필수: 컴포넌트 카탈로그 URL
        theme?: object;         // 선택: 테마 설정
        sendDataModel?: boolean; // 선택: 클라이언트가 데이터 모델 업데이트를 보내도록 요청
      }
    }
    ```

    ### 속성 (Properties)

    | 속성            | 타입    | 필수 여부 | 설명                                                          |
    | --------------- | ------- | -------- | ---------------------------------------------------------------- |
    | `surfaceId`     | string  | ✅        | 이 서피스의 고유 식별자입니다.                                    |
    | `catalogId`     | string  | ✅        | 컴포넌트 카탈로그 식별자입니다.                                   |
    | `theme`         | object  | ❌        | 테마 설정입니다(예: `primaryColor`).                              |
    | `sendDataModel` | boolean | ❌        | true이면 클라이언트가 데이터 모델 변경 사항을 서버로 다시 보냅니다. |

    ### 예시

    ```json
    {
      "version": "v0.9",
      "createSurface": {
        "surfaceId": "main",
        "catalogId": "https://a2ui.org/specification/v0_9/catalogs/basic/catalog.json"
      }
    }
    ```

    루트 컴포넌트는 관례에 따라 결정됩니다. `updateComponents`의 컴포넌트 중 하나가 `"id": "root"`를 가져야 합니다. `catalogId`는 필수입니다.

---

## surfaceUpdate (v0.8) / updateComponents (v0.9)

서피스 내의 컴포넌트를 추가하거나 업데이트합니다.

=== "v0.8 — `surfaceUpdate`"

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

    | 속성         | 타입   | 필수 여부 | 설명                            |
    | ------------ | ------ | -------- | -------------------------------- |
    | `surfaceId`  | string | ✅        | 업데이트할 서피스의 ID           |
    | `components` | array  | ✅        | 컴포넌트 정의 배열               |

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

    다음 사항을 기억하세요.

    - 트리 루트 역할을 수행할 하나의 컴포넌트가 `beginRendering` 메시지에서 `root`로 지정되어야 합니다.
    - 컴포넌트들은 인접 리스트(ID 참조를 가진 평면 구조)를 형성합니다.
    - 기존에 존재하는 ID로 컴포넌트를 보내면 해당 컴포넌트가 업데이트됩니다.
    - 자식 요소들은 ID를 통해 참조됩니다.
    - 컴포넌트들은 증분식(스트리밍)으로 추가될 수 있습니다.

=== "v0.9 — `updateComponents`"

    ### 스키마

    ```typescript
    {
      version: "v0.9";
      updateComponents: {
        surfaceId: string;        // 필수: 대상 서피스
        components: Array<{       // 필수: 컴포넌트 리스트
          id: string;             // 필수: 컴포넌트 ID
          component: string;      // 필수: 컴포넌트 유형 이름
          ...properties           // 컴포넌트별 속성(평평한 구조)
        }>
      }
    }
    ```

    ### 속성 (Properties)

    | 속성         | 타입   | 필수 여부 | 설명                            |
    | ------------ | ------ | -------- | -------------------------------- |
    | `surfaceId`  | string | ✅        | 업데이트할 서피스의 ID           |
    | `components` | array  | ✅        | 컴포넌트 정의 배열               |

    ### 컴포넌트 객체

    컴포넌트 구조는 평평합니다(flat).

    - `id` (string, 필수): 서피스 내에서 고유한 식별자
    - `component` (string, 필수): 컴포넌트 유형 이름(예: `"Text"`, `"Button"`)
    - 그 외 모든 속성은 컴포넌트 객체의 최상위 레벨에 위치합니다.

    ### 예시

    **단일 컴포넌트:**

    ```json
    {
      "version": "v0.9",
      "updateComponents": {
        "surfaceId": "main",
        "components": [
          {
            "id": "greeting",
            "component": "Text",
            "text": "안녕하세요!",
            "variant": "h1"
          }
        ]
      }
    }
    ```

    **복수 컴포넌트:**

    ```json
    {
      "version": "v0.9",
      "updateComponents": {
        "surfaceId": "main",
        "components": [
          {
            "id": "root",
            "component": "Column",
            "children": ["header", "body"]
          },
          {
            "id": "header",
            "component": "Text",
            "text": "환영합니다"
          },
          {
            "id": "body",
            "component": "Card",
            "child": "content"
          },
          {
            "id": "content",
            "component": "Text",
            "text": {"path": "/message"}
          }
        ]
      }
    }
    ```

    **기존 컴포넌트 업데이트:**

    ```json
    {
      "version": "v0.9",
      "updateComponents": {
        "surfaceId": "main",
        "components": [
          {
            "id": "greeting",
            "component": "Text",
            "text": "안녕하세요, Alice님!",
            "variant": "h1"
          }
        ]
      }
    }
    ```

    ### 사용 참고 사항

    다음 사항을 기억하세요.

    - 트리 루트 역할을 수행할 컴포넌트는 `"id": "root"`를 가져야 합니다(별도 메시지 필드가 아닌 관례입니다).
    - 컴포넌트 유형은 래퍼 객체 대신 문자열(`"component": "Text"`)로 표현됩니다.
    - 속성은 컴포넌트 객체에 평평하게(flat) 위치합니다(유형 키 아래에 중첩되지 않음).
    - 데이터 바인딩은 `{"path": "/pointer"}`(JSON Pointer)를 사용합니다 — v0.8과 키 이름은 같지만 표준 JSON Pointer 경로를 사용합니다.
    - 컴포넌트들은 증분식(스트리밍)으로 추가될 수 있습니다.

### 오류 코드

| 오류                    | 원인                                    | 해결 방법                                                                                                                                                                                       |
| ---------------------- | -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 서피스가 이미 존재함     | `surfaceId`가 이미 사용 중임             | 렌더러가 존재하는 동안 `surfaceId`가 전역적으로 고유하도록 하세요. 서브에이전트를 둔 오케스트레이터를 사용하는 경우, 오케스트레이터가 충돌을 피하기 위해 필요에 따라 서피스 ID를 관리할 수 있습니다. |
| 서피스를 찾을 수 없음   | `surfaceId`가 존재하지 않음              | `surfaceId`가 생성된 서피스와 일치하는지 확인하세요. v0.8에서는 서피스가 암시적으로 생성되지만, v0.9 이상에서는 `createSurface`가 필요합니다.                                                    |
| 잘못된 컴포넌트 유형     | 알 수 없는 컴포넌트 유형                 | 협의된 카탈로그 내에 해당 컴포넌트 유형이 존재하는지 확인하세요.                                                                                                                                 |
| 잘못된 속성             | 해당 유형에 존재하지 않는 속성           | 카탈로그 스키마를 확인하세요.                                                                                                                                                                    |
| 순환 참조               | 컴포넌트가 자기 자신을 자식으로 참조함    | 컴포넌트 계층 구조를 수정하세요.                                                                                                                                                                 |

---

## dataModelUpdate (v0.8) / updateDataModel (v0.9)

컴포넌트가 바인딩되는 데이터 모델을 업데이트합니다.

=== "v0.8 — `dataModelUpdate`"

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

    | 속성        | 타입   | 필수 여부 | 설명                                                                                                  |
    | ----------- | ------ | -------- | ------------------------------------------------------------------------------------------------------ |
    | `surfaceId` | string | ✅        | 업데이트할 서피스의 ID입니다.                                                                          |
    | `path`      | string | ❌        | 데이터 모델 내의 특정 위치 경로(예: 'user')입니다. 생략 시 루트에 적용됩니다.                          |
    | `contents`  | array  | ✅        | 인접 리스트 형식의 데이터 항목 배열입니다. 각 항목은 `key`와 타입이 지정된 `value*` 속성을 가집니다.   |

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

    다음 사항을 기억하세요.

    - 데이터 모델은 서피스별로 관리됩니다.
    - 컴포넌트는 바인딩된 데이터가 변경되면 자동으로 다시 렌더링됩니다.
    - 전체 모델을 교체하기보다 특정 경로에 대한 세밀한 업데이트를 권장합니다.
    - 타입이 지정된 값 필드(`valueString`, `valueNumber`, `valueBoolean`, `valueMap`)를 사용합니다 — LLM 친화적이며 타입 추론이 필요 없습니다.
    - 모든 데이터 변환(예: 날짜 포맷팅)은 서버에서 `dataModelUpdate` 메시지를 보내기 전에 완료되어야 합니다.

=== "v0.9 — `updateDataModel`"

    ### 스키마

    ```typescript
    {
      version: "v0.9";
      updateDataModel: {
        surfaceId: string;      // 필수: 대상 서피스
        path?: string;          // 선택: JSON Pointer 경로(기본값 "/")
        value?: any;            // 선택: 설정할 값(삭제하려면 생략)
      }
    }
    ```

    ### 속성 (Properties)

    | 속성        | 타입   | 필수 여부 | 설명                                                              |
    | ----------- | ------ | -------- | -------------------------------------------------------------------- |
    | `surfaceId` | string | ✅        | 업데이트할 서피스의 ID입니다.                                        |
    | `path`      | string | ❌        | JSON Pointer 경로입니다(예: `/user/email`). 기본값은 `/`(루트)입니다. |
    | `value`     | any    | ❌        | 설정할 값입니다. 생략하면 `path`의 키가 제거됩니다.                   |

    ### 예시

    **전체 모델 초기화:**

    ```json
    {
      "version": "v0.9",
      "updateDataModel": {
        "surfaceId": "main",
        "path": "/",
        "value": {
          "user": {
            "name": "Alice",
            "email": "alice@example.com"
          },
          "items": []
        }
      }
    }
    ```

    **중첩된 속성 업데이트:**

    ```json
    {
      "version": "v0.9",
      "updateDataModel": {
        "surfaceId": "main",
        "path": "/user/email",
        "value": "alice@newdomain.com"
      }
    }
    ```

    ### 사용 참고 사항

    다음 사항을 기억하세요.

    - v0.9는 표준 JSON Pointer 경로와 일반 JSON 값을 사용합니다 — 타입 래퍼가 없습니다.
    - `path`를 생략하면 기본값은 `"/"`(루트)입니다.
    - `value`는 어떤 JSON 타입이든 가능합니다(string, number, boolean, object, array, null). 삭제하려면 생략하세요.
    - v0.8의 `contents` 인접 리스트보다 단순하며, 표준 JSON Patch 시맨틱에 더 가깝습니다.
    - `{"path": "/user/email"}`을 참조하는 컴포넌트는 해당 경로가 변경되면 자동으로 업데이트됩니다.

---

## deleteSurface

서피스와 그에 포함된 모든 컴포넌트 및 데이터를 제거합니다.

=== "v0.8 — `deleteSurface`"

    ### 스키마

    ```typescript
    {
      deleteSurface: {
        surfaceId: string;        // 필수: 삭제할 서피스
      }
    }
    ```

    ### 예시

    ```json
    {
      "deleteSurface": {
        "surfaceId": "modal"
      }
    }
    ```

=== "v0.9 — `deleteSurface`"

    ### 스키마

    ```typescript
    {
      version: "v0.9";
      deleteSurface: {
        surfaceId: string;        // 필수: 삭제할 서피스
      }
    }
    ```

    ### 예시

    ```json
    {
      "version": "v0.9",
      "deleteSurface": {
        "surfaceId": "modal"
      }
    }
    ```

### 속성 (Properties)

| 속성        | 타입   | 필수 여부 | 설명                 |
| ----------- | ------ | -------- | --------------------- |
| `surfaceId` | string | ✅       | 삭제할 서피스의 ID     |

### 사용 참고 사항

다음 사항을 기억하세요.

- 서피스와 관련된 모든 컴포넌트를 제거합니다.
- 서피스의 데이터 모델을 초기화합니다.
- 클라이언트는 UI에서 해당 서피스를 제거해야 합니다.
- 존재하지 않는 서피스를 삭제하려 해도 안전합니다 (no-op).
- 모달이나 대화상자를 닫을 때, 또는 다른 화면으로 이동할 때 사용하세요.
- 두 버전 모두 구조가 동일합니다(v0.9는 `version` 필드만 추가됩니다).

---

## 메시지 순서 (Message Ordering)

### 요구 사항

메시지 순서는 다음 요구 사항을 충족해야 합니다.

1. `beginRendering`은 해당 서피스의 초기 `surfaceUpdate` 메시지들 이후에 와야 합니다.
2. `surfaceUpdate`는 `dataModelUpdate` 전후 어디에나 올 수 있습니다.
3. 서로 다른 서피스에 대한 메시지들은 독립적입니다.
4. 여러 메시지가 하나의 서피스를 점진적으로 업데이트할 수 있습니다.

### 권장 순서

=== "v0.8"

    ```jsonl
    { "surfaceUpdate":    { "surfaceId": "main", "components": [...] } }
    { "dataModelUpdate":  { "surfaceId": "main", "contents": {...} } }
    { "beginRendering":   { "surfaceId": "main", "root": "root-id" } }
    ```

=== "v0.9"

    ```jsonl
    { "version": "v0.9", "createSurface":    { "surfaceId": "main", "catalogId": "..." } }
    { "version": "v0.9", "updateComponents": { "surfaceId": "main", "components": [...] } }
    { "version": "v0.9", "updateDataModel":  { "surfaceId": "main", "path": "/", "value": {...} } }
    ```

### 점진적 구축 예시

=== "v0.8"

    ```jsonl
    { "surfaceUpdate":   { "surfaceId": "main", "components": [...] } }  // 헤더 (Header)
    { "surfaceUpdate":   { "surfaceId": "main", "components": [...] } }  // 본문 (Body)
    { "beginRendering":  { "surfaceId": "main", "root": "root-id" } }   // 렌더링
    { "surfaceUpdate":   { "surfaceId": "main", "components": [...] } }  // 푸터 (Footer) (렌더링 이후)
    { "dataModelUpdate": { "surfaceId": "main", "contents": {...} } }    // 데이터 채우기
    ```

=== "v0.9"

    ```jsonl
    { "version": "v0.9", "createSurface":    { "surfaceId": "main", "catalogId": "..." } }
    { "version": "v0.9", "updateComponents": { "surfaceId": "main", "components": [...] } }  // 헤더
    { "version": "v0.9", "updateComponents": { "surfaceId": "main", "components": [...] } }  // 본문 + 푸터
    { "version": "v0.9", "updateDataModel":  { "surfaceId": "main", "path": "/", "value": {...} } }
    ```

## 검증 (Validation)

=== "v0.8"

    다음 스키마에 대해 검증합니다.

    - **[server_to_client.json](../../../specification/v0_8/json/server_to_client.json)**: 메시지 엔벨로프(envelope) 스키마
    - **[standard_catalog_definition.json](../../../specification/v0_8/json/standard_catalog_definition.json)**: 컴포넌트 스키마

=== "v0.9"

    다음 스키마에 대해 검증합니다.

    - **[server_to_client.json](../../../specification/v0_9/json/server_to_client.json)**: 메시지 엔벨로프(envelope) 스키마
    - **[catalogs/basic/catalog.json](../../../specification/v0_9/catalogs/basic/catalog.json)**: 컴포넌트 스키마

## 추가 학습 자료

- **[컴포넌트 갤러리](components.md)**: 사용 가능한 모든 컴포넌트 유형
- **[데이터 바인딩 가이드](../concepts/data-binding.md)**: 데이터 바인딩 동작 원리
- **[에이전트 개발 가이드](../guides/agent-development.md)**: 유효한 메시지 생성 방법
