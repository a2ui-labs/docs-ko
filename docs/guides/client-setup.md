# 클라이언트 설정 가이드

여러분의 플랫폼에 맞는 렌더러를 사용하여 애플리케이션에 A2UI를 통합하세요.

## 렌더러 목록

| 렌더러 | 플랫폼 | 상태 |
| ------------------------ | ------------------ | ----------------- |
| **Lit (Web Components)** | 웹 | ✅ 안정(Stable) |
| **Angular** | 웹 | ✅ 안정(Stable) |
| **Flutter (GenUI SDK)** | 모바일/데스크톱/웹 | ✅ 안정(Stable) |
| **React** | 웹 | 🚧 2026년 1분기 예정 |
| **SwiftUI** | iOS/macOS | 🚧 2026년 2분기 예정 |
| **Jetpack Compose** | 안드로이드 | 🚧 2026년 2분기 예정 |

## Web Components (Lit)

!!! warning "주의"
    Lit 클라이언트 라이브러리는 아직 NPM에 게시되지 않았습니다. 며칠 내로 다시 확인해 주세요.

```bash
npm install @a2ui/web-lib lit @lit-labs/signals
```

Lit 렌더러의 구성 요소:

- **메시지 프로세서(Message Processor)**: A2UI 상태를 관리하고 수신 메시지를 처리합니다.
- **`<a2ui-surface>` 컴포넌트**: 앱에서 서피스(surface)를 렌더링합니다.
- **Lit Signals**: 자동 UI 업데이트를 위한 반응형 상태 관리를 제공합니다.

TODO: 검증된 설정 예제 추가 예정

**실행 가능한 예시:** [Lit 쉘 샘플](https://github.com/google/a2ui/tree/main/samples/client/lit/shell)

## Angular

!!! warning "주의"
    Angular 클라이언트 라이브러리는 아직 NPM에 게시되지 않았습니다. 며칠 내로 다시 확인해 주세요.

```bash
npm install @a2ui/angular @a2ui/web-lib
```

Angular 렌더러의 구성 요소:

- **`provideA2UI()` 함수**: 앱 설정에서 A2UI를 구성합니다.
- **`Surface` 컴포넌트**: A2UI 서피스를 렌더링합니다.
- **`MessageProcessor` 서비스**: 수신되는 A2UI 메시지를 처리합니다.

TODO: 검증된 설정 예제 추가 예정

**실행 가능한 예시:** [Angular 레스토랑 샘플](https://github.com/google/a2ui/tree/main/samples/client/angular/projects/restaurant)

## Flutter (GenUI SDK)

```bash
flutter pub add flutter_genui
```

Flutter는 네이티브 A2UI 렌더링을 제공하는 GenUI SDK를 사용합니다.

**문서:** [GenUI SDK](https://docs.flutter.dev/ai/genui) | [GitHub](https://github.com/flutter/genui) | [GenUI Flutter 패키지 README](https://github.com/flutter/genui/blob/main/packages/genui/README.md#getting-started-with-genui)

## 에이전트 연결하기

클라이언트 애플리케이션은 다음을 수행해야 합니다:
1. 에이전트로부터 **A2UI 메시지 수신** (전송 계층을 통해)
2. 메시지 프로세서를 사용하여 **메시지 처리**
3. 에이전트에게 **사용자 액션 전송**

일반적인 전송 옵션:
- **Server-Sent Events (SSE)**: 서버에서 클라이언트로의 단방향 스트리밍
- **WebSockets**: 양방향 실시간 통신
- **A2A 프로토콜**: A2UI를 지원하는 표준화된 에이전트 간 통신

**참고:** [전송 계층 가이드](../transports.md)

## 사용자 액션 처리하기

사용자가 A2UI 컴포넌트와 상호작용(버튼 클릭, 양식 제출 등)할 때 클라이언트는 다음을 수행합니다:
1. 컴포넌트로부터 액션 이벤트 캡처
2. 액션에 필요한 데이터 컨텍스트 확인(Resolve)
3. 에이전트에게 액션 전송
4. 에이전트의 응답 메시지 처리

TODO: 액션 처리 예제 추가 예정

## 오류 처리

처리해야 할 일반적인 오류들:
- **잘못된 서피스 ID**: `beginRendering`을 받기 전에 서피스가 참조된 경우
- **잘못된 컴포넌트 ID**: 컴포넌트 ID는 서피스 내에서 고유해야 함
- **잘못된 데이터 경로**: 데이터 모델 구조 및 JSON Pointer 구문 확인
- **스키마 검증 실패**: 메시지 형식이 A2UI 명세와 일치하는지 확인

TODO: 오류 처리 예제 추가 예정

## 다음 단계

- **[퀵스타트](../quickstart.md)**: 데모 애플리케이션 시도해 보기
- **[테마 및 스타일링](theming.md)**: 룩앤필 커스터마이징
- **[커스텀 컴포넌트](custom-components.md)**: 컴포넌트 카탈로그 확장
- **[에이전트 개발](agent-development.md)**: A2UI를 생성하는 에이전트 구축
- **[참조 문서](../reference/messages.md)**: 프로토콜 심층 분석
