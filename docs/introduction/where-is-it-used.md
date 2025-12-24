# A2UI는 어디에서 사용되나요?

A2UI는 차세대 에이전트 주도 애플리케이션을 구축하기 위해 Google의 여러 팀과 파트너 조직들에 의해 채택되고 있습니다. 다음은 A2UI가 실제로 영향력을 발휘하고 있는 사례들입니다.

## 실제 서비스 배포 사례

### Google Opal: 모두를 위한 AI 미니 앱

[Opal](http://opal.google)은 수십만 명의 사람들이 코딩 없이 자연어를 사용하여 AI 미니 앱을 구축하고, 편집하고, 공유할 수 있게 해줍니다.

**Opal이 A2UI를 사용하는 방식:**

Google의 Opal 팀은 시작 단계부터 **A2UI의 핵심 기여자**로 참여해 왔습니다. 그들은 Opal의 AI 미니 앱을 가능하게 하는 동적인 생성형 UI 시스템을 구동하기 위해 A2UI를 사용합니다.

- **신속한 프로토타이핑**: 새로운 UI 패턴을 빠르게 구축하고 테스트
- **사용자 생성 앱**: 누구나 커스텀 UI가 포함된 앱 제작 가능
- **동적 인터페이스**: 각 활용 사례에 맞춰 UI가 자동으로 적응

> "A2UI는 우리 작업의 기반입니다. 고정된 프론트엔드에 제약받지 않고 AI가 새로운 방식으로 사용자 경험을 주도할 수 있게 하는 유연성을 제공합니다. 선언적 특성과 보안에 대한 집중 덕분에 우리는 빠르고 안전하게 실험할 수 있습니다."
>
> **— Dimitri Glazkov**, Opal 팀 수석 엔지니어

**더 알아보기:** [opal.google](http://opal.google)

---

### Gemini Enterprise: 비즈니스를 위한 커스텀 에이전트

Gemini Enterprise는 기업이 특정 워크플로와 데이터에 맞춘 강력한 커스텀 AI 에이전트를 구축할 수 있게 해줍니다.

**Gemini Enterprise가 A2UI를 사용하는 방식:**

A2UI는 엔터프라이즈 에이전트가 비즈니스 애플리케이션 내에서 **풍부하고 대화형인 UI**를 렌더링할 수 있도록 통합되고 있습니다. 이는 단순한 텍스트 응답을 넘어 직원을 복잡한 워크플로로 안내하는 역할을 합니다.

- **데이터 입력 양식**: 구조화된 데이터 수집을 위한 AI 생성 양식
- **승인 대시보드**: 검토 및 승인 프로세스를 위한 동적 UI
- **워크플로 자동화**: 복잡한 작업을 위한 단계별 인터페이스
- **커스텀 엔터프라이즈 UI**: 산업별 요구 사항에 맞춘 맞춤형 인터페이스

> "우리 고객들은 에이전트가 단순히 질문에 답하는 것 이상의 일을 하기를 원합니다. 그들은 에이전트가 직원을 복잡한 워크플로로 안내하기를 바랍니다. A2UI를 통해 Gemini Enterprise 기반으로 개발하는 사용자들은 데이터 입력 양식부터 승인 대시보드까지 어떤 작업에도 필요한 동적인 커스텀 UI를 에이전트가 생성하도록 할 수 있으며, 이는 워크플로 자동화를 획기적으로 가속화할 것입니다."
>
> **— Fred Jabbour**, Gemini Enterprise 제품 매니저

**더 알아보기:** [Gemini Enterprise](https://cloud.google.com/gemini)

---

### Flutter GenUI SDK: 모바일을 위한 생성형 UI

[Flutter GenUI SDK](https://docs.flutter.dev/ai/genui)는 모바일, 데스크톱, 웹을 아우르는 Flutter 애플리케이션에 동적인 AI 생성 UI를 제공합니다.

**GenUI가 A2UI를 사용하는 방식:**

GenUI SDK는 서버 측 에이전트와 Flutter 애플리케이션 간의 통신을 위한 **기반 프로토콜로 A2UI**를 사용합니다. 여러분이 GenUI를 사용한다면, 내부적으로 A2UI를 사용하고 있는 것입니다.

- **크로스 플랫폼 지원**: iOS, 안드로이드, 웹, 데스크톱에서 동일한 에이전트 작동
- **네이티브 성능**: 각 플랫폼에서 네이티브하게 렌더링되는 Flutter 위젯
- **브랜드 일관성**: 앱의 디자인 시스템과 조화를 이루는 UI
- **서버 주도 UI**: 앱 업데이트 없이 에이전트가 화면 콘텐츠 제어

> "우리 개발자들은 모든 플랫폼에서 만족스러운 경험을 주는 표현력 풍부하고 브랜드 가치가 높은 커스텀 디자인 시스템을 빠르게 만들 수 있기 때문에 Flutter를 선택합니다. A2UI는 Flutter GenUI SDK에 완벽하게 들어맞았습니다. 모든 플랫폼의 모든 사용자가 고품질의 네이티브 느낌을 주는 경험을 할 수 있도록 보장해주기 때문입니다."
>
> **— Vijay Menon**, Dart & Flutter 엔지니어링 디렉터

**시도해 보기:**
- [GenUI 문서](https://docs.flutter.dev/ai/genui)
- [시작하기 비디오](https://www.youtube.com/watch?v=nWr6eZKM6no)
- [Verdure 예제](https://github.com/flutter/genui/tree/main/examples/verdure) (클라이언트-서버 A2UI 샘플)

---

## 파트너 통합 사례

### AG UI / CopilotKit: 풀스택 에이전트 프레임워크

[AG UI](https://ag-ui.com/)와 [CopilotKit](https://www.copilotkit.ai/)은 에이전트 기반 애플리케이션 구축을 위한 포괄적인 프레임워크를 제공하며, **A2UI와의 즉각적인 호환성**을 지원합니다.

**협업 방식:**

AG UI는 커스텀 프론트엔드와 전용 에이전트 간의 고대역폭 연결을 만드는 데 탁월합니다. 여기에 A2UI 지원을 추가함으로써 개발자는 두 가지 장점을 모두 얻을 수 있습니다:

- **상태 동기화**: AG UI가 앱 상태와 채팅 기록 관리
- **A2UI 렌더러**: 서드파티 에이전트로부터 전달된 동적 UI 렌더링
- **멀티 에이전트 지원**: 여러 에이전트의 UI를 조율
- **React 통합**: React 애플리케이션과의 원활한 통합

> "AG UI는 커스텀 프론트엔드와 전용 에이전트 사이의 고대역폭 연결을 만드는 데 강점이 있습니다. A2UI 지원을 추가함으로써 우리는 개발자들에게 최상의 결과를 제공하고 있습니다. 이제 풍부하고 상태가 동기화된 애플리케이션을 빌드하는 동시에, A2UI를 통해 서드파티 에이전트의 동적 UI도 렌더링할 수 있습니다. 이는 멀티 에이전트 세상에 완벽하게 부합합니다."
>
> **— Atai Barkai**, CopilotKit 및 AG UI 창립자

**더 알아보기:**
- [AG UI](https://ag-ui.com/)
- [CopilotKit](https://www.copilotkit.ai/)

---

### Google의 AI 구동 제품들

Google이 전사적으로 AI를 도입함에 따라, A2UI는 AI 에이전트가 텍스트뿐만 아니라 **사용자 인터페이스를 주고받을 수 있는 표준화된 방법**을 제공합니다.

**내부 에이전트 채택 현황:**

- **멀티 에이전트 워크플로**: 여러 에이전트가 동일한 화면(surface)에 기여
- **원격 에이전트 지원**: 다른 서비스에서 실행 중인 에이전트가 UI 제공 가능
- **표준화**: 팀 간 공통 프로토콜 사용으로 통합 비용 절감
- **외부 노출**: 내부 에이전트를 외부(예: Gemini Enterprise)로 쉽게 연동

> "A2A가 플랫폼에 상관없이 에이전트 간 대화를 가능하게 하듯, A2UI는 사용자 인터페이스 계층을 표준화하고 오케스트레이터를 통해 원격 에이전트 활용 사례를 지원합니다. 이는 내부 팀들에게 매우 강력한 도구가 되었으며, 풍부한 UI가 예외가 아닌 일상이 되는 에이전트를 빠르게 개발할 수 있게 해주었습니다. Google이 생성형 UI를 더 밀어붙임에 따라, A2UI는 어떤 클라이언트에서도 렌더링되는 서버 주도 UI를 위한 완벽한 플랫폼을 제공합니다."
>
> **— James Wren**, Senior Staff Engineer, AI Powered Google

---

## 커뮤니티 프로젝트

A2UI 커뮤니티는 흥미로운 프로젝트들을 만들고 있습니다:

### 오픈 소스 예제

- **레스토랑 찾기 (Restaurant Finder)** ([samples/agent/adk/restaurant_finder](https://github.com/google/A2UI/tree/main/samples/agent/adk/restaurant_finder))
  - 동적 양식을 포함한 테이블 예약 기능
  - Gemini 기반 에이전트
  - 전체 소스 코드 공개

- **연락처 조회 (Contact Lookup)** ([samples/agent/adk/contact_lookup](https://github.com/google/A2UI/tree/main/samples/agent/adk/contact_lookup))
  - 결과 목록이 포함된 검색 인터페이스
  - A2A 에이전트 예시
  - 데이터 바인딩 시연

- **컴포넌트 갤러리 (Component Gallery)** ([samples/client/angular - gallery 모드](https://github.com/google/A2UI/tree/main/samples/client/angular))
  - 모든 컴포넌트에 대한 대화형 쇼케이스
  - 코드가 포함된 라이브 예시
  - 학습용으로 최적

### 커뮤니티 기여

A2UI로 무엇인가를 만드셨나요? [커뮤니티와 공유해 주세요!](../community.md)

---

## 다음 단계

- [퀵스타트 가이드](../quickstart.md) - 데모 살펴보기
- [에이전트 개발](../guides/agent-development.md) - 에이전트 빌드하기
- [클라이언트 설정](../guides/client-setup.md) - 렌더러 통합하기
- [커뮤니티](../community.md) - 커뮤니티 참여하기

---

**프로덕션 환경에서 A2UI를 사용 중이신가요?** [GitHub Discussions](https://github.com/google/A2UI/discussions)에서 여러분의 이야기를 들려주세요.
