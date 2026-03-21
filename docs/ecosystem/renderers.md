# 생태계 렌더러

커뮤니티와 서드파티 A2UI 렌더러 구현을 소개합니다.

> **참고**
>
> 아래 렌더러들은 A2UI 팀이 아니라 각 프로젝트의 유지보수자가 관리합니다. 호환성, 지원 버전, 유지 상태는 각 프로젝트를 확인하세요.

## 커뮤니티 렌더러

| 렌더러 | 플랫폼 | v0.8 | v0.9 | 활동 | 링크 |
|----------|----------|------|------|------|------|
| **@a2ui-sdk/react** | React(Web) | ✅ | ❌ | GitHub stars / 최근 업데이트 배지 | [GitHub](https://github.com/easyops-cn/a2ui-sdk) · [npm](https://www.npmjs.com/package/@a2ui-sdk/react) · [Docs](https://a2ui-sdk.js.org/) |
| **A2UI-Android** | Android(Compose) | ✅ | ❌ | GitHub stars / 최근 업데이트 배지 | [GitHub](https://github.com/lmee/A2UI-Android) |
| **a2ui-react-native** | React Native | ✅ | ❌ | GitHub stars / 최근 업데이트 배지 | [GitHub](https://github.com/sivamrudram-eng/a2ui-react-native) |
| **@zhama/a2ui** | React(Web) | ✅ | ❌ | - | [npm](https://www.npmjs.com/package/@zhama/a2ui) |
| **A2UI-react** | React(Web) | ✅ | ❌ | GitHub stars / 최근 업데이트 배지 | [GitHub](https://github.com/jem-computer/A2UI-react) |

### 주목할 만한 프로젝트

- **[@xpert-ai/a2ui-react](https://www.npmjs.com/package/@xpert-ai/a2ui-react)** - ShadCN UI 컴포넌트를 사용하는 React 렌더러
- **[a2ui-3d-renderer](https://github.com/josh-english-2k18/a2ui-3d-renderer)** - Three.js/WebGL 기반 실험적 3D 렌더러
- **[ai-kit-a2ui](https://github.com/AINative-Studio/ai-kit-a2ui)** - AIKit 프레임워크용 React + ShadCN 렌더러

### 하이라이트

`@a2ui-sdk/react`는 현재 가장 성숙한 커뮤니티 React 렌더러 중 하나로, Radix UI 프리미티브와 Tailwind CSS 스타일링을 사용합니다.

`A2UI-Android`는 Android용 Jetpack Compose 렌더러로서 중요한 공백을 메웁니다.

`a2ui-react-native`는 iOS와 Android에서 하나의 코드베이스로 A2UI를 사용할 수 있게 해 줍니다.

### Python / PyPI

2026년 3월 기준으로 PyPI에서 신뢰할 만한 A2UI 렌더러 패키지는 찾지 못했습니다. A2UI 렌더러는 클라이언트 측 UI 라이브러리이므로, 생태계는 자연스럽게 JavaScript/TypeScript와 네이티브 모바일 프레임워크 중심으로 구성됩니다.

## 렌더러 제출하기

A2UI 렌더러를 만들었다면 이 목록에 추가해 주세요.

1. [google/A2UI](https://github.com/google/A2UI) 저장소를 포크합니다.
2. 이 파일(`docs/ecosystem/renderers.md`)을 수정해 커뮤니티 렌더러 표에 항목을 추가합니다.
3. 짧은 설명과 함께 PR을 올립니다.
4. [GitHub Discussions](https://github.com/google/A2UI/discussions)에 공유해 커뮤니티에 알립니다.

좋은 커뮤니티 렌더러는 다음을 갖추면 좋습니다.

- 공개된 소스 코드
- 지원하는 A2UI 사양 버전
- 핵심 컴포넌트 지원
- 설치 방법과 최소 사용 예제가 있는 README
- 활발한 유지보수 상태

실험적이거나 초기 단계 프로젝트도 환영합니다.
