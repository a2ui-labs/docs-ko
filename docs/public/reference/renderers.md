# 렌더러 (클라이언트 라이브러리)

렌더러는 A2UI JSON 메시지를 다양한 플랫폼의 네이티브 UI 컴포넌트로 변환합니다.

[에이전트](agents.md)는 A2UI 메시지를 생성하고, [전송 계층](../concepts/transports.md)은 메시지를 클라이언트로 전달합니다. 클라이언트 렌더러 라이브러리는 A2UI 메시지를 버퍼링하고 처리하며, A2UI 생명주기를 구현하고 서피스(위젯)를 렌더링해야 합니다.

커스텀 컴포넌트를 렌더러에 가져오거나, 자신만의 UI 컴포넌트 프레임워크를 지원하는 렌더러를 만들 수도 있습니다.

## 유지 관리되는 렌더러

| 렌더러 | 플랫폼 | v0.8 | v0.9.1 | v1.0 | 링크 |
|----------|----------|------|------|------|------|
| **React** | 웹 | ✅ 안정판 | ✅ 안정판 | 🚧 예정 | [코드](https://github.com/a2ui-project/a2ui/tree/main/renderers/react) |
| **Lit(Web Components)** | 웹 | ✅ 안정판 | ✅ 안정판 | 🚧 예정 | [코드](https://github.com/a2ui-project/a2ui/tree/main/renderers/lit) |
| **Angular** | 웹 | ✅ 안정판 | ✅ 안정판 | 🚧 예정 | [코드](https://github.com/a2ui-project/a2ui/tree/main/renderers/angular) |
| **Flutter(GenUI SDK)** | 모바일/데스크톱/웹 | ✅ 안정판 | ✅ 안정판 | 🚧 예정 | [문서](https://docs.flutter.dev/ai/genui) · [코드](https://github.com/flutter/genui) |
| **SwiftUI** | iOS/macOS | - | - | 🚧 예정 | - |
| **Jetpack Compose** | Android | - | - | 🚧 예정 | - |

자세한 내용은 [로드맵](../roadmap.md)을 확인하세요.

## 생태계 렌더러

커뮤니티는 추가 플랫폼용 A2UI 렌더러를 만들고 있습니다.

- **json-render** - Zod 스키마를 통해 A2UI 카탈로그를 렌더링하는 Vercel의 React 라이브러리
- **A2UI-Android** - 커뮤니티 Jetpack Compose 렌더러
- **a2ui-react-native** - iOS/Android용 React Native 렌더러
- **Lynx A2UI** - A2UI용 ReactLynx 렌더러
- **AGenUI** - iOS, Android, HarmonyOS를 지원하는 크로스 플랫폼 네이티브 렌더러(v0.9)

더 많은 커뮤니티 프로젝트와 제출 방법은 [생태계 렌더러 목록](../ecosystem/renderers.md)을 참고하세요.

## 렌더러 동작 방식

```text
A2UI JSON → 렌더러 → 네이티브 컴포넌트 → 여러분의 앱
```

1. 전송 계층으로부터 A2UI 메시지를 받습니다.
2. JSON을 파싱하고 스키마를 검증합니다.
3. 플랫폼 네이티브 컴포넌트로 렌더링합니다.
4. 앱의 테마에 맞게 스타일을 적용합니다.

## 렌더러 사용하기

앱에 A2UI를 통합하려면 선택한 렌더러의 설정 가이드를 따르세요.

- [React](../guides/client-setup.md#react)
- [Lit(Web Components)](../guides/client-setup.md#web-components-lit)
- [Angular](../guides/client-setup.md#angular)
- [Flutter(GenUI SDK)](../guides/client-setup.md#flutter-genui-sdk)

## 렌더러 만들기

자신의 플랫폼용 렌더러를 만들고 싶다면:

- [로드맵](../roadmap.md)에서 계획 중인 프레임워크를 확인하세요.
- 기존 렌더러의 패턴을 살펴보세요.
- [렌더러 개발 가이드](../guides/renderer-development.md)를 참고해 구현 세부 사항을 확인하세요.

### 핵심 요구 사항

- A2UI JSON 메시지 파싱
- A2UI 컴포넌트를 네이티브 위젯으로 매핑
- 데이터 바인딩과 생명주기 이벤트 처리
- 증분 A2UI 메시지 시퀀스를 처리해 UI를 구축하고 갱신
- 서버 주도 업데이트 지원
- 사용자 액션 처리

## 다음 단계

- [클라이언트 설정 가이드](../guides/client-setup.md)
- [퀵스타트](../quickstart.md)
- [컴포넌트 레퍼런스](components.md)
