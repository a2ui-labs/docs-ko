# 컴포넌트 갤러리

이 페이지는 모든 표준 A2UI 컴포넌트를 예시 및 사용 패턴과 함께 소개합니다. 전체 기술 명세는 [표준 카탈로그 정의](https://github.com/google/A2UI/blob/main/specification/0.8/json/standard_catalog_definition.json)를 참조하세요.

## 레이아웃 컴포넌트 (Layout Components)

### Row

가로 레이아웃 컨테이너입니다. 자식 요소들이 왼쪽에서 오른쪽으로 배치됩니다.

```json
{
  "id": "toolbar",
  "component": {
    "Row": {
      "children": {"explicitList": ["btn1", "btn2", "btn3"]},
      "alignment": "center"
    }
  }
}
```

**속성 (Properties):**

- `children`: 정적 배열(`explicitList`) 또는 동적 `template`
- `distribution`: 자식 요소들의 가로 방향 분포 (`start`, `center`, `end`, `spaceBetween`, `spaceAround`, `spaceEvenly`)
- `alignment`: 세로 방향 정렬 (`start`, `center`, `end`, `stretch`)

### Column

세로 레이아웃 컨테이너입니다. 자식 요소들이 위에서 아래로 배치됩니다.

```json
{
  "id": "content",
  "component": {
    "Column": {
      "children": {"explicitList": ["header", "body", "footer"]}
    }
  }
}
```

**속성 (Properties):**

- `children`: 정적 배열(`explicitList`) 또는 동적 `template`
- `distribution`: 자식 요소들의 세로 방향 분포 (`start`, `center`, `end`, `spaceBetween`, `spaceAround`, `spaceEvenly`)
- `alignment`: 가로 방향 정렬 (`start`, `center`, `end`, `stretch`)

## 표시 컴포넌트 (Display Components)

### Text

텍스트 콘텐츠를 표시하며 선택적으로 스타일을 적용할 수 있습니다.

```json
{
  "id": "title",
  "component": {
    "Text": {
      "text": {"literalString": "A2UI에 오신 것을 환영합니다"},
      "usageHint": "h1"
    }
  }
}
```

**`usageHint` 값:** `h1`, `h2`, `h3`, `h4`, `h5`, `caption`, `body`

### Image

URL로부터 이미지를 표시합니다.

```json
{
  "id": "logo",
  "component": {
    "Image": {
      "url": {"literalString": "https://example.com/logo.png"}
    }
  }
}
```

### Icon

Material Icons 또는 커스텀 아이콘 세트를 사용하여 아이콘을 표시합니다.

```json
{
  "id": "check-icon",
  "component": {
    "Icon": {
      "name": {"literalString": "check_circle"}
    }
  }
}
```

### Divider

시각적 구분선을 표시합니다.

```json
{
  "id": "separator",
  "component": {
    "Divider": {
      "axis": "horizontal"
    }
  }
}
```

## 상호작용 컴포넌트 (Interactive Components)

### Button

액션을 지원하는 클릭 가능한 버튼입니다.

```json
{
  "id": "submit-btn-text",
  "component": {
    "Text": {
      "text": { "literalString": "제출" }
    }
  }
}
{
  "id": "submit-btn",
  "component": {
    "Button": {
      "child": "submit-btn-text",
      "primary": true,
      "action": {"name": "submit_form"}
    }
  }
}
```

**속성 (Properties):**
- `child`: 버튼 내부에 표시할 컴포넌트의 ID (예: Text 또는 Icon).
- `primary`: 기본 액션 여부를 나타내는 불리언 값.
- `action`: 클릭 시 수행할 액션.

### TextField

텍스트 입력 필드입니다.

```json
{
  "id": "email-input",
  "component": {
    "TextField": {
      "label": {"literalString": "이메일 주소"},
      "text": {"path": "/user/email"},
      "textFieldType": "shortText"
    }
  }
}
```

**`textFieldType` 값:** `date`, `longText`, `number`, `shortText`, `obscured`

### Checkbox

불리언 토글 스위치입니다.

```json
{
  "id": "terms-checkbox",
  "component": {
    "Checkbox": {
      "label": {"literalString": "약관에 동의합니다"},
      "value": {"path": "/form/agreedToTerms"}
    }
  }
}
```

## 컨테이너 컴포넌트 (Container Components)

### Card

입체감(elevation) 또는 테두리와 패딩이 있는 컨테이너입니다.

```json
{
  "id": "info-card",
  "component": {
    "Card": {
      "child": "card-content"
    }
  }
}
```

### Modal

오버레이 대화상자입니다.

```json
{
  "id": "confirmation-modal",
  "component": {
    "Modal": {
      "entryPointChild": "open-modal-btn",
      "contentChild": "modal-content"
    }
  }
}
```

### Tabs

탭 인터페이스입니다.

```json
{
  "id": "settings-tabs",
  "component": {
    "Tabs": {
      "tabItems": [
        {"title": {"literalString": "일반"}, "child": "general-settings"},
        {"title": {"literalString": "개인정보"}, "child": "privacy-settings"},
        {"title": {"literalString": "고급"}, "child": "advanced-settings"}
      ]
    }
  }
}
```

### List

스크롤 가능한 항목 리스트입니다.

```json
{
  "id": "message-list",
  "component": {
    "List": {
      "children": {
        "template": {
          "dataBinding": "/messages",
          "componentId": "message-item"
        }
      }
    }
  }
}
```

## 공통 속성

대부분의 컴포넌트는 다음 공통 속성을 지원합니다:

- `id` (필수): 컴포넌트 인스턴스의 고유 식별자.
- `weight`: 컴포넌트가 Row 또는 Column의 직계 자식일 때 적용되는 flex-grow 값입니다. 이 속성은 `id` 및 `component`와 함께 지정됩니다.

## 라이브 예시

모든 컴포넌트의 실제 동작을 확인하려면 컴포넌트 갤러리 데모를 실행해 보세요:

```bash
cd samples/client/angular
npm start -- gallery
```

이 명령은 모든 컴포넌트, 변형 및 대화형 예시를 포함한 라이브 갤러리를 창으로 띄웁니다.

## 추가 학습 자료

- **[표준 카탈로그 정의](../../specification/0.9/json/standard_catalog_definition.json)**: 전체 기술 명세
- **[커스텀 컴포넌트 가이드](../guides/custom-components.md)**: 나만의 컴포넌트 빌드하기
- **[테마 가이드](../guides/theming.md)**: 브랜드에 맞춰 컴포넌트 스타일링하기
