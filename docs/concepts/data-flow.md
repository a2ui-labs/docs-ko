# 데이터 흐름

에이전트에서 UI까지 메시지가 이동하는 방식을 설명합니다.

## 아키텍처

```text
에이전트(LLM) → A2UI 생성기 → 전송 계층(SSE/WS/A2A)
                                      ↓
클라이언트(스트림 리더) → 메시지 파서 → 렌더러 → 네이티브 UI
```

![엔드-투-엔드 데이터 흐름](../assets/end-to-end-data-flow.png)

## 메시지 형식

A2UI는 UI를 설명하는 JSON 메시지 시퀀스를 정의합니다. 스트리밍할 때는 각 줄이 하나의 JSON 객체인 **JSON Lines(JSONL)** 형식으로 전달되는 경우가 많습니다.

=== "v0.8"

    ```jsonl
    {"surfaceUpdate":{"surfaceId":"main","components":[...]}}
    {"dataModelUpdate":{"surfaceId":"main","contents":[{"key":"user","valueMap":[{"key":"name","valueString":"Alice"}]}]}}
    {"beginRendering":{"surfaceId":"main","root":"root-component"}}
    ```

=== "v0.9"

    ```jsonl
    {"version":"v0.9","createSurface":{"surfaceId":"main","catalogId":"https://a2ui.org/specification/v0_9/basic_catalog.json"}}
    {"version":"v0.9","updateComponents":{"surfaceId":"main","components":[...]}}
    {"version":"v0.9","updateDataModel":{"surfaceId":"main","path":"/user","value":{"name":"Alice"}}}
    ```

**왜 이 형식인가요?**

서로 독립적인 JSON 객체 시퀀스는 스트리밍에 적합하고, LLM이 점진적으로 생성하기 쉽고, 오류에도 강합니다.

## 생명주기 예시: 레스토랑 예약

**사용자:** "내일 오후 7시에 2명 예약해 줘"

=== "v0.8"

    **1. 에이전트가 UI 구조를 정의합니다.**

    ```json
    {
      "surfaceUpdate": {
        "surfaceId": "booking",
        "components": [
          {
            "id": "root",
            "component": {
              "Column": {
                "children": {"explicitList": ["header", "guests-field", "submit-btn"]}
              }
            }
          },
          {
            "id": "header",
            "component": {
              "Text": {
                "text": {"literalString": "예약 확정"},
                "usageHint": "h1"
              }
            }
          },
          {
            "id": "guests-field",
            "component": {
              "TextField": {
                "label": {"literalString": "인원 수"},
                "text": {"path": "/reservation/guests"}
              }
            }
          },
          {
            "id": "submit-btn",
            "component": {
              "Button": {
                "child": "submit-text",
                "action": {
                  "name": "confirm",
                  "context": [
                    { "key": "details", "value": { "path": "/reservation" } }
                  ]
                }
              }
            }
          }
        ]
      }
    }
    ```

    **2. 에이전트가 데이터를 채웁니다.**

    ```json
    {
      "dataModelUpdate": {
        "surfaceId": "booking",
        "path": "/reservation",
        "contents": [
          { "key": "datetime", "valueString": "2025-12-16T19:00:00Z" },
          { "key": "guests", "valueString": "2" }
        ]
      }
    }
    ```

    **3. 에이전트가 렌더링을 시작합니다.**

    ```json
    {
      "beginRendering": {
        "surfaceId": "booking",
        "root": "root"
      }
    }
    ```

    **4. 사용자가 인원 수를 "3"으로 수정합니다** → 클라이언트가 `/reservation/guests`를 자동으로 업데이트합니다.

    **5. 사용자가 "확인"을 누릅니다** → 클라이언트가 액션을 전송합니다.

    ```json
    {
      "userAction": {
        "name": "confirm",
        "surfaceId": "booking",
        "context": {
          "details": {
            "datetime": "2025-12-16T19:00:00Z",
            "guests": "3"
          }
        }
      }
    }
    ```

    **6. 에이전트가 응답합니다.** UI를 업데이트하거나, 정리를 위해 `{"deleteSurface": {"surfaceId": "booking"}}`를 보낼 수 있습니다.

=== "v0.9"

    **1. 에이전트가 서피스를 생성합니다.**

    ```json
    {
      "version": "v0.9",
      "createSurface": {
        "surfaceId": "booking",
        "catalogId": "https://a2ui.org/specification/v0_9/basic_catalog.json"
      }
    }
    ```

    **2. 에이전트가 UI 구조를 정의합니다.**

    ```json
    {
      "version": "v0.9",
      "updateComponents": {
        "surfaceId": "booking",
        "components": [
          {
            "id": "root",
            "component": "Column",
            "children": ["header", "guests-field", "submit-btn"]
          },
          {
            "id": "header",
            "component": "Text",
            "text": "예약 확정",
            "variant": "h1"
          },
          {
            "id": "guests-field",
            "component": "TextField",
            "label": "인원 수",
            "text": { "path": "/reservation/guests" }
          },
          {
            "id": "submit-btn",
            "component": "Button",
            "child": "submit-text",
            "action": { "event": { "name": "confirm" } }
          }
        ]
      }
    }
    ```

    **3. 에이전트가 데이터를 채웁니다.**

    ```json
    {
      "version": "v0.9",
      "updateDataModel": {
        "surfaceId": "booking",
        "path": "/reservation",
        "value": {
          "datetime": "2025-12-16T19:00:00Z",
          "guests": "2"
        }
      }
    }
    ```

    **4. 사용자가 인원 수를 "3"으로 수정합니다.** 클라이언트가 로컬 데이터 모델을 업데이트합니다.

    **5. 사용자가 "확인"을 누릅니다.** 클라이언트가 업데이트된 상태와 함께 액션을 전송합니다.

    ```json
    {
      "version": "v0.9",
      "action": {
        "name": "confirm",
        "surfaceId": "booking",
        "sourceComponentId": "submit-btn",
        "context": {
          "details": {
            "datetime": "2025-12-16T19:00:00Z",
            "guests": "3"
          }
        }
      }
    }
    ```

    **6. 에이전트가 응답합니다.** UI를 갱신하거나 `deleteSurface`를 보낼 수 있습니다.

## 전송 옵션

- **A2A 프로토콜**: 멀티 에이전트 시스템이나 에이전트-UI 통신에 사용
- **AG UI**: 양방향 실시간 통신
- 그 외 JSON을 전송할 수 있는 모든 방식

자세한 내용은 [전송 계층](transports.md)을 참조하세요.

## 점진적 렌더링

전체 응답이 완성될 때까지 기다리는 대신, 응답 일부가 준비되는 즉시 클라이언트로 스트리밍해 점진적으로 렌더링할 수 있습니다. 사용자는 스피너를 보는 대신 실시간으로 UI가 만들어지는 모습을 보게 됩니다.

## 오류 처리

- **잘못된 형식의 메시지**: 무시하고 다음 메시지를 처리하거나 에이전트에 오류를 보냅니다.
- **네트워크 중단**: 오류 상태를 표시하고 재연결을 시도하며, 에이전트가 메시지를 다시 보내거나 작업을 재개할 수 있습니다.

## 성능

- **배칭(Batching)**: 16ms 동안 업데이트를 버퍼링해 한 번에 렌더링합니다.
- **디핑(Diffing)**: 이전 컴포넌트와 새 컴포넌트를 비교해 바뀐 속성만 업데이트합니다.
- **세밀한 업데이트**: 전체 모델이 아니라 `/user/name`처럼 필요한 경로만 업데이트합니다.
