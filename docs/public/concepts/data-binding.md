# 데이터 바인딩

데이터 바인딩은 JSON Pointer 경로([RFC 6901](https://tools.ietf.org/html/rfc6901))를 사용해 UI 컴포넌트를 애플리케이션 상태에 연결합니다. 이를 통해 A2UI는 큰 데이터 배열의 레이아웃을 효율적으로 정의하거나, 전체를 다시 만들지 않고도 업데이트된 콘텐츠를 표시할 수 있습니다.

## 구조 vs 상태

A2UI는 다음을 분리합니다.

1. **UI 구조**(컴포넌트): 인터페이스가 어떻게 보이는지
2. **애플리케이션 상태**(데이터 모델): 어떤 데이터를 표시하는지

이 분리는 반응형 업데이트, 데이터 주도 UI, 재사용 가능한 템플릿, 양방향 바인딩을 가능하게 합니다.

## 데이터 모델

각 서피스는 상태를 담는 JSON 객체를 가집니다.

```json
{
  "user": {"name": "Alice", "email": "alice@example.com"},
  "cart": {
    "items": [{"name": "Widget", "price": 9.99, "quantity": 2}],
    "total": 19.98
  }
}
```

## JSON Pointer 경로

**구문**

- `/user/name` - 객체 속성
- `/cart/items/0` - 배열 인덱스(0부터 시작)
- `/cart/items/0/price` - 중첩 경로

**예시**

```json
{"user": {"name": "Alice"}, "items": ["Apple", "Banana"]}
```

- `/user/name` → `"Alice"`
- `/items/0` → `"Apple"`

## 리터럴 vs 경로 값

=== "v0.8"

    **리터럴(고정 값)**

    ```json
    {"id": "title", "component": {"Text": {"text": {"literalString": "환영합니다"}}}}
    ```

    **데이터 바인딩(반응형 값)**

    ```json
    {"id": "username", "component": {"Text": {"text": {"path": "/user/name"}}}}
    ```

=== "v0.9 and later"

    **리터럴(고정 값)**

    ```json
    {"id": "title", "component": "Text", "text": "환영합니다"}
    ```

    **데이터 바인딩(반응형 값)**

    ```json
    {"id": "username", "component": "Text", "text": {"path": "/user/name"}}
    ```

`/user/name`이 `"Alice"`에서 `"Bob"`으로 바뀌면 텍스트는 자동으로 `"Bob"`으로 업데이트됩니다.

## 반응형 업데이트

데이터 경로에 바인딩된 컴포넌트는 데이터가 변경될 때 자동으로 업데이트됩니다.

=== "v0.8"

    ```json
    {"id": "status", "component": {"Text": {"text": {"path": "/order/status"}}}}
    ```

=== "v0.9 and later"

    ```json
    {"id": "status", "component": "Text", "text": {"path": "/order/status"}}
    ```

- **초기 상태**: `/order/status` = `"처리 중..."` → `"처리 중..."` 표시
- **업데이트**: `status: "배송됨"`이 포함된 `dataModelUpdate`를 전송 → `"배송됨"` 표시

컴포넌트를 다시 정의할 필요 없이 데이터만 업데이트하면 됩니다.

## 동적 리스트

템플릿으로 배열을 렌더링합니다.

=== "v0.8"

    ```json
    {
      "id": "product-list",
      "component": {
        "Column": {
          "children": {"template": {"dataBinding": "/products", "componentId": "product-card"}}
        }
      }
    }
    ```

=== "v0.9 and later"

    ```json
    {
      "id": "product-list",
      "component": "Column",
      "children": {"path": "/products", "componentId": "product-card"}
    }
    ```

**데이터**

```json
{"products": [{"name": "메뉴 A", "price": 9000}, {"name": "메뉴 B", "price": 19000}]}
```

**결과**: 각 상품당 하나씩, 총 두 개의 카드가 렌더링됩니다.

### 스코프 경로

템플릿 안의 경로는 배열 아이템 기준으로 스코프가 잡힙니다.

=== "v0.8"

    ```json
    {"id": "product-name", "component": {"Text": {"text": {"path": "/name"}}}}
    ```

    - `/products/0`에서 `/name`은 `/products/0/name`으로 해석되어 → `"메뉴 A"`
    - `/products/1`에서 `/name`은 `/products/1/name`으로 해석되어 → `"메뉴 B"`

=== "v0.9 and later"

    ```json
    {"id": "product-name", "component": "Text", "text": {"path": "name"}}
    ```

    - `/products/0`에서 `name`은 `/products/0/name`으로 해석되어 → `"메뉴 A"`
    - `/products/1`에서 `name`은 `/products/1/name`으로 해석되어 → `"메뉴 B"`

아이템을 추가하거나 제거하면 렌더링된 컴포넌트가 자동으로 갱신됩니다.

## 입력 바인딩

대화형 컴포넌트는 데이터 모델을 양방향으로 업데이트합니다.

=== "v0.8"

    | 컴포넌트 | 예시 | 사용자 동작 | 데이터 업데이트 |
    |-----------|------|-------------|----------------|
    | **TextField** | `{"text": {"path": "/form/name"}}` | `"Alice"` 입력 | `/form/name` = `"Alice"` |
    | **CheckBox** | `{"value": {"path": "/form/agreed"}}` | 체크박스 선택 | `/form/agreed` = `true` |
    | **MultipleChoice** | `{"selections": {"path": "/form/country"}}` | `"한국"` 선택 | `/form/country` = `["kr"]` |

=== "v0.9 and later"

    | 컴포넌트 | 예시 | 사용자 동작 | 데이터 업데이트 |
    |-----------|------|-------------|----------------|
    | **TextField** | `{"value": {"path": "/form/name"}}` | `"Alice"` 입력 | `/form/name` = `"Alice"` |
    | **CheckBox** | `{"value": {"path": "/form/agreed"}}` | 체크박스 선택 | `/form/agreed` = `true` |
    | **ChoicePicker** | `{"value": {"path": "/form/country"}}` | `"한국"` 선택 | `/form/country` = `["kr"]` |

## 베스트 프랙티스

**1. 세밀한 업데이트 사용** - 변경된 경로만 업데이트하세요.

=== "v0.8"

    ```json
    {"dataModelUpdate": {"path": "/user", "contents": [{"key": "name", "valueString": "Alice"}]}}
    ```

=== "v0.9 and later"

    ```json
    {"version": "v0.9.1", "updateDataModel": {"surfaceId": "user_profile", "path": "/user/name", "value": "Alice"}}
    ```

**2. 도메인별로 정리** - 관련 데이터를 묶으세요.

```json
{"user": {...}, "cart": {...}, "ui": {...}}
```

**3. 표시 값을 미리 계산** - 에이전트가 통화, 날짜 같은 값을 전송 전에 포맷하세요.

```json
{"price": "₩19,000"}  // 비권장: {"price": 19000}
```
