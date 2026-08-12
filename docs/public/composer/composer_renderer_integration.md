# A2UI Composer 연동 매뉴얼

## 배경

A2UI Composer는 특정 카탈로그나 렌더러 스택에 대해 아무것도 알지 못하며 이들과
직접 연동되어 있지도 않습니다. A2UI JSON이 렌더링된 결과를 보기 위해 Composer는
"렌더러 애플리케이션"에 의존합니다. A2UI Composer는 렌더러 애플리케이션을
iframe에 호스팅하고 **postMessage**로 통신합니다. 렌더러 애플리케이션을 호스팅하는
iframe에는 `sandbox='allow-scripts allow-same-origin allow-forms'`가 적용되어
있는데, 단순히 A2UI JSON을 렌더링하는 데에는 문제가 되지 않습니다.

렌더러 애플리케이션은 A2UI JSON을 받아 자신의 렌더러로 결과를 표시하는 역할을
담당합니다.

## 브리지(Bridge)

A2UI Composer와의 연동 작업을 단순화하기 위해 **브리지**가 마련되어 있습니다.
이는 렌더러 애플리케이션이 포함시키는 소량의 JavaScript 코드로, A2UI Composer와
렌더러 애플리케이션 사이의 모든 통신을 조율합니다.

연동을 한층 더 간단히 할 수 있도록 프레임워크별 래퍼도 함께 제공됩니다.

## 예제

샘플 렌더러 앱을 확인해 보세요.

- [Angular](https://github.com/a2ui-project/composer/tree/main/samples/ng-basic-catalog)
- [Lit](https://github.com/a2ui-project/composer/tree/main/samples/lit-basic-catalog)
- [React](https://github.com/a2ui-project/composer/tree/main/samples/react-basic-catalog)

이들은 모두 동일한 버전의 Basic 카탈로그를 제공합니다.

호스팅된 [A2UI Composer](https://a2ui-project.github.io/composer/)를 실행한 뒤
설정 페이지로 이동해 Renderer 드롭다운을 클릭하면 각 렌더러 애플리케이션이 실제로
동작하는 모습을 볼 수 있습니다.

### Angular 사용하기

예시로 Angular 기반의 렌더러 애플리케이션을 만드는 과정을 살펴보겠습니다.

### 의존성 추가

먼저 프로젝트의 의존성 목록에 핵심 연동 패키지를 추가합니다.

```
yarn add a2ui-bridge @a2ui/web_core @a2ui/angular
```

물론 다른 패키지 매니저를 사용한다면 그에 맞는 방법을 따르세요.

#### 래퍼 컴포넌트 만들기

다음과 같은 새 컴포넌트를 만듭니다.

```ts
import {Component, inject} from '@angular/core';
import {SurfaceComponent} from '@a2ui/angular/v0_9';
import {A2uiSandboxConnection} from 'a2ui-bridge/angular';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [SurfaceComponent],
  template: `
    <main class="sandbox-shell">
      @if (sandbox.surfaceId()) {
        <a2ui-v09-surface [surfaceId]="sandbox.surfaceId()" />
      } @else {
        <p style="padding: 24px; color: #666; font-family: sans-serif; text-align: center;">
          Waiting for RENDER_A2UI payloads...
        </p>
      }
    </main>
  `,
})
export class AppComponent {
  protected sandbox = inject(A2uiSandboxConnection);
}
```

`@else` 블록의 내용은 원하는 대로 수정해도 되지만, 템플릿의 나머지 부분은 그대로
두어야 합니다.

#### 렌더러 애플리케이션 부트스트랩

표준 Angular 부트스트랩 진입점 파일(`src/main.ts`)을 설정하면서, 샌드박스
provider 매핑에 카탈로그 클래스를 동적으로 전달합니다.

```ts
import {bootstrapApplication} from '@angular/platform-browser';
import {AppComponent} from './app/app.component';
import {provideA2uiSandbox} from 'a2ui-bridge/angular';
import {BasicCatalog} from '@a2ui/angular/v0_9';

bootstrapApplication(AppComponent, {
  providers: [
    provideA2uiSandbox([BasicCatalog]), // Injects and exposes dynamic catalogs
  ],
}).catch((err) => console.error('A2UI Sandbox Bootstrap Failed:', err));
```

`BasicCatalog`는 반드시 여러분의 카탈로그로 바꿔 주세요.

> 변경 감지(change detection) 호환성에 대한 참고: `provideA2uiSandbox` 헬퍼는
> 별도 설정 없이도 표준 Zone 기반 변경 감지(`zone.js` 사용)와
> Zoneless 변경 감지(`provideZonelessChangeDetection()`) 모두와 100% 호환됩니다.
