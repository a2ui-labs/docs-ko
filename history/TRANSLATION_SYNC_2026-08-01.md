# 2026-08-01 번역 동기화 기록

상류 A2UI 저장소(`https://github.com/a2ui-project/a2ui.git`)의 최신 변경 사항(커밋 `d4723f29` → `2276f8cc`, 총 37개 커밋) 중 문서(`docs/public/**`) 관련 변경분을 동기화하고, `docs-ko` 문서를 최신화했습니다. 해당 구간에서 실제로 diff가 발생한 파일은 `docs/public/` 하위 7개 파일뿐이었습니다(`README.md`, `mkdocs.yaml`은 변경 없음, 확인 완료).

## 1. 문서 업데이트 및 번역 내역 (`docs-ko`)

- **개념 - 카탈로그 (`docs/public/concepts/catalogs.md`)**:
  - "렌더러 구현" 절이 상류에서 전면 개편되어(`api.ts`(Zod 스키마) → `hello_world_banner.ts`(`CatalogComponent` 확장) → `AngularCatalog` 등록으로 이어지는 3단계 흐름, v0.9 신규 패턴) 해당 절을 새 코드 예제와 함께 번역했습니다.
  - Orchestrator 데모(v0.8 API)와 Angular explorer의 `DemoCatalog`(v0.9 예시)를 구분해 안내하는 신규 NOTE 박스, 그리고 클라이언트 측 함수의 `clientOnly` 실행 경계를 카탈로그 정의에서 런타임에 읽어온다는 안내 문단을 추가로 번역했습니다.

- **생태계 렌더러 (`docs/public/ecosystem/renderers.md`)**:
  - 커뮤니티 렌더러 표에 신규 렌더러 `yessGlory17/generative-mui`(`@yessglory/generative-mui-react`, React + Material UI, v0.9.1) 행을 추가했습니다.
  - 해당 렌더러에 대한 하이라이트 문단(Basic Catalog 18개 컴포넌트를 MUI에 매핑, 호스트 `<ThemeProvider>` 상속, 코어/어댑터 분리 구조, Extended Catalog, 스트리밍 복원력, 보안 가드 등)을 새로 번역해 추가했습니다.

- **가이드 - 커스텀 컴포넌트 작성하기 (`docs/public/guides/authoring-components.md`)**:
  - "2. 컴포넌트 구현(클라이언트)" 절을 구식 `DynamicComponent` + `inputBinding` 패턴에서 신규 `api.ts`(Zod 스키마) + `CatalogComponent`(`props()` signal 접근) 패턴으로 갱신했습니다.
  - "3. 렌더러에 등록(클라이언트)" 절을 `DEFAULT_CATALOG` 스프레드 + lazy `import()` 방식에서 `AngularCatalog` 생성자를 통한 즉시(eager) 등록 방식으로 갱신했습니다.

- **가이드 - 클라이언트 설정 (`docs/public/guides/client-setup.md`)**:
  - Angular "설정 예시 (v0.9)"를 `A2UI_RENDERER_CONFIG` 토큰 + `A2uiRendererService` provider 방식에서 `provideA2Ui()` 함수 방식으로 갱신했습니다.
  - 신규 "액션 핸들러에서의 의존성 주입(Dependency Injection)" 하위 절을 번역해 추가했습니다(`provideA2Ui`에 팩토리 함수를 전달해 Angular `inject()`를 사용하는 패턴).

- **소개 - A2UI란 무엇인가요 (`docs/public/introduction/what-is-a2ui.md`)**:
  - diff 문구("v0.9에서는 `createSurface`가 `beginRendering`을 대체..." → "A2UI 메시지는 `createSurface`로 서피스를 초기화...")를 반영하는 과정에서, 기존 KO "예시" 절이 v0.8 스타일 단일 예시만 담고 있고 상류에 이미 도입된 v0.8(레거시)/v0.9(안정판) 2단 탭 비교 구조가 전혀 반영되어 있지 않음을 확인했습니다. diff 대상인 예시 절 자체의 드리프트이므로, `quickstart.md`에서 이미 쓰이는 탭 스타일(`=== "v0.8 (레거시)"` / `=== "v0.9 (안정판)"`)을 그대로 적용해 두 버전의 예시를 모두 번역해 추가했습니다.

- **퀵스타트 (`docs/public/quickstart.md`)**:
  - "A2UI 메시지 구조" 절의 v0.9 참고 문구를 상류 변경에 맞춰 축약했습니다("v0.9에서는 `createSurface`가 `beginRendering`을 대체하고..." → "컴포넌트는 평평한(flat) 형식을 사용하고...").
  - "컴포넌트 갤러리" 절에 새로 clone한 저장소에서 필요한 사전 빌드 단계(`cd renderers/lit/a2ui_explorer && yarn build`)를 추가하고, 실행 명령을 `yarn start gallery` → `yarn dev`로 갱신했습니다.
  - 직전 두 차례의 동기화 기록(`2026-07-19`, `2026-07-24`)에서 후속 조치가 필요하다고 표시했던 "연락처 조회 데모(`npm run demo:contact`)" 절 — 상류 저장소에 실존한 적이 없는 docs-ko 전용 콘텐츠 — 을 제거하고, 같은 위치의 상류 원문에 실제로 존재하는 "다른 언어와 프레임워크" 절(Angular/React 샘플 안내)을 새로 번역해 대체했습니다.

- **레퍼런스 - 메시지 유형 (`docs/public/reference/messages.md`)**:
  - diff에 포함된 두 문구(각각 `createSurface`/`updateComponents` v0.9 절의 안내문)를 번역하려는 과정에서, 기존 KO 문서 전체가 v0.8 전용 구조(`beginRendering`/`surfaceUpdate`/`dataModelUpdate`)로만 작성되어 있고, 상류에서 이미 모든 메시지 타입에 도입된 v0.8/v0.9 2단 탭 비교 구조가 전혀 반영되어 있지 않음을 확인했습니다(즉, 문서 전체가 제거·개편된 v0.8 API만 설명하는 상태). 이는 diff 대상 파일 자체의 구조적 드리프트이므로, 상류 최신 구조에 맞춰 문서 전체를 재작성했습니다: 메시지 형식, `beginRendering`/`createSurface`, `surfaceUpdate`/`updateComponents`, `dataModelUpdate`/`updateDataModel`, `deleteSurface`, 메시지 순서, 검증 섹션 모두에 v0.8/v0.9 탭을 추가했습니다. 기존에 번역되어 있던 v0.8 콘텐츠는 최대한 그대로 재사용하고 v0.9 콘텐츠를 새로 번역했으며, 링크 경로도 상류의 최신 `specification/v0_8`, `specification/v0_9` 경로 규칙에 맞춰 갱신했습니다.

## 2. 루트 파일 확인

- `README.md`, `mkdocs.yaml`: 상류 diff(`d4723f29` → `2276f8cc`) 구간에 두 파일 모두 변경 이력이 없음을 `git log`/`git diff`로 확인했습니다. 기존 `docs-ko` 번역과 대조해도 이번 동기화 구간에서 새로 반영해야 할 불일치는 없었습니다.

## 3. 범위 밖(작업하지 않음)

- `docs/contributing/**`, `eval/`, 최상위 `specification/`(원문 스펙 JSON 등) — 지시에 따라 완전히 제외했습니다.
- 이번 diff 대상 7개 파일 외의 나머지 `docs/public/**` 파일들은 손대지 않았습니다.

## 4. 알려진 잔여 이슈 (후속 정리 권장)

- `concepts/catalogs.md`는 이번에 갱신한 "렌더러 구현" 절을 제외하면 전체적으로 상류 대비 상당히 축약·재구성된 상태입니다(카탈로그 스키마 상세, Catalog Negotiation, Naming & Versioning, Graceful Degradation, Schema Validation & Fallback, Inline Catalogs 등 여러 절이 KO 버전에 없습니다). 이번 diff 범위에는 포함되지 않아 손대지 않았지만, 향후 파일 단위의 전체 재번역 검토가 필요합니다.
- `reference/messages.md`를 v0.8/v0.9 탭 구조로 전면 재작성하면서, 유사하게 오래된 v0.8-only 구조가 다른 미확인 파일에도 남아 있을 가능성을 발견했습니다. 이번 동기화에서는 diff에 포함된 파일만 확인했으므로, 다음 전체 점검 때 `guides/`, `reference/`, `concepts/`의 나머지 파일들도 상류의 최신 버전 탭 구조를 반영하고 있는지 확인할 것을 권장합니다.
