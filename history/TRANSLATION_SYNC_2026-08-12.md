# 2026-08-12 번역 동기화 기록

상류 A2UI 저장소(`https://github.com/a2ui-project/a2ui.git`)의 최신 변경 사항(커밋 `2276f8cc` → `43a7bdd8`, 총 27개 커밋) 중 문서(`docs/public/**`) 관련 변경분을 동기화하고, `docs-ko` 문서를 최신화했습니다. 해당 구간에서 실제로 diff가 발생한 텍스트 파일은 `README.md`, `mkdocs.yaml`을 포함해 8개이며, 이미지 asset 6개가 추가되고 1개가 삭제되었습니다.

이번 구간의 문서 변경은 사실상 하나의 큰 흐름으로, **A2UI Composer가 CopilotKit의 외부 Widget Builder에서 A2UI 프로젝트가 직접 운영하는 공식 도구(`https://a2ui-project.github.io/composer/`)로 대체**되고, 그에 맞춰 단일 페이지였던 `composer.md`가 `composer/` 디렉터리 아래 2개 문서로 확장된 것입니다(상류 PR #2201, #2215).

## 1. 문서 업데이트 및 번역 내역 (`docs-ko`)

- **A2UI Composer (`docs/public/composer.md` → `docs/public/composer/index.md`)**:
  - 기존 `composer.md`(CopilotKit Widget Builder를 안내하던 16줄짜리 문서)를 삭제하고, 상류에서 신설된 `composer/index.md`를 전문 번역해 새로 작성했습니다.
  - 번역 범위: Composer 시작 방법 3단계, Composer UI 패널 설명(Gemini 어시스턴트, 렌더링된 A2UI 미리보기, A2UI JSON 편집기, 하단 디버그·검사 탭의 Data Model / Events / Errors / Raw Messages), 컴포넌트 갤러리, 설정(렌더러 애플리케이션 선택, Gemini API 키 발급 절차 및 Web Crypto API 기반 로컬 암호화 저장 안내), 진행 중인 작업, Raw Messages 메시지 목록(`RENDERER_READY`, `A2UI_CATALOG`, `COMPONENT_USAGES`, `DATA_MODEL_CHANGE`, `LLM_REQUEST`, `LLM_RESPONSE`).
  - 상류 원문의 `#gemini-api-key` 앵커는 한국어 제목("Gemini API 키")에서 동일하게 생성되지 않으므로, `attr_list` 확장을 이용해 제목에 `{#gemini-api}` 명시적 앵커를 부여하고 본문 링크도 이에 맞춰 조정했습니다(`mkdocs.yaml`에 `attr_list`가 이미 활성화되어 있음을 확인).

- **A2UI Composer 연동 매뉴얼 (`docs/public/composer/composer_renderer_integration.md`, 신규)**:
  - 상류에서 신설된 문서를 전문 번역했습니다. 배경(Composer는 특정 카탈로그·렌더러를 알지 못하며 "렌더러 애플리케이션"을 iframe에 호스팅하고 postMessage로 통신), 브리지(bridge)와 프레임워크별 래퍼, 샘플 렌더러 앱 3종(Angular/Lit/React) 안내, Angular 기반 렌더러 앱 제작 절차(의존성 추가 → 래퍼 컴포넌트 작성 → `provideA2uiSandbox`로 부트스트랩), Zone/Zoneless 변경 감지 호환성 NOTE를 포함합니다.
  - 저장소 관례에 따라 코드 블록과 코드 주석은 상류 원문 그대로 유지했습니다.

- **개념 - 용어집 (`docs/public/concepts/glossary.md`)**:
  - 상류에 새로 추가된 4개 용어를 번역해 반영했습니다(상류 PR #2021).
    - `Catalog Transformer`(+ 하위 절 "필요한 이유", "예시"): 시스템 프롬프트 생성·검증 스키마 컴파일 전에 카탈로그를 필터링·변형하는 규칙 집합. 컨텍스트 윈도 토큰 최적화, 작업별 기능 가드레일, 모델 시그니처 축소의 3가지 동기와 `ComponentPruningTransformer` / `FunctionPruningTransformer` 예시 포함.
    - `A2UI Tag`, `Tag Unwrapping`, `Compilation`: LLM 응답 파싱 파이프라인 관련 용어.
  - 상류의 배치 순서를 그대로 따라 `Catalog Transformer`는 `Basic Catalog`와 `Surface` 사이에, 나머지 3개는 `Surface`와 `에이전트 아키텍처` 사이에 삽입했습니다.
  - 용어 제목은 기존 문서 관례(`Catalog`, `Basic Catalog`, `Surface`, `Action` 등 프로토콜 고유 용어는 영문 유지)에 맞춰 영문 그대로 두었습니다.

- **홈 (`docs/public/index.md`)**:
  - 하단 "A2UI Composer" 절을 CopilotKit Widget Builder 안내 + 스크린샷 링크에서, 공식 Composer 링크와 신규 Composer 문서(`./composer/index.md`) 링크로 교체했습니다.
  - 상류 원문과 동일하게 `A2UI-widget-builder.png` 이미지 임베드는 제거했습니다.

- **소개 - 에이전트 UI 생태계 (`docs/public/introduction/agent-ui-ecosystem.md`)**:
  - "A2UI vs AG-UI / CopilotKit" 절에서 CopilotKit 팀이 "A2UI Composer에도 기여했다"는 서술과 `../composer.md` 링크를 제거했습니다(Composer가 A2UI 프로젝트 자체 도구로 바뀌었으므로).

- **가이드 - A2UI에서의 MCP Apps (`docs/public/guides/mcp-apps-in-a2ui.md`)**:
  - inner iframe 권한 설명에 `allow-top-navigation`, `allow-top-navigation-by-user-activation`을 금지 목록에 추가했습니다.
  - "최상위 윈도 하이재킹 방어" 항목을 새로 번역해 추가했습니다(frame busting 공격으로 host 윈도가 리디렉션되는 것을 막는다는 설명, 상류 PR #2218).

## 2. 루트 파일 및 네비게이션

- **`README.md`**: "시작 경로" 표의 Composer 행에서 `Widget Builder`(`go.copilotkit.ai`) 링크를 제거하고, Composer URL을 `https://a2ui-composer.ag-ui.com/` → `https://a2ui-project.github.io/composer/`로 갱신했습니다. 표 구분선 정렬도 상류에 맞춰 조정했습니다.
- **`mkdocs.yaml`**: `A2UI Composer ⭐: composer.md` 단일 항목을 상류와 동일하게 2단 구조로 변경했습니다(`composer/index.md` + `Composer 연동: composer/composer_renderer_integration.md`).

## 3. Asset

- 추가(상류에서 복사, 6개): `composer_workspace.png`, `composer_components_gallery.png`, `composer_editor_tooltip.png`, `composer_paperclip.png`, `composer_camera.png`, `composer_copy.png`
- 삭제: `A2UI-widget-builder.png` (참조하던 문서가 모두 제거됨)

## 4. 검증

- `mkdocs build`는 로컬에 `mkdocs-material`, `mkdocs-macros-plugin`, `mkdocs-mermaid2-plugin`이 설치되어 있지 않아 실행하지 못했습니다. 대신 스크립트로 다음을 확인했습니다.
  - `mkdocs.yaml`의 모든 `nav` 대상 파일 존재 여부 → 이상 없음
  - 신규 작성한 composer 문서 2개의 모든 상대 링크·이미지 경로 → 전부 정상 해석
  - 삭제한 `composer.md` / `A2UI-widget-builder.png`를 참조하는 잔여 링크 → 없음

## 5. 범위 밖(작업하지 않음)

- `docs/contributing/**`, `eval/`, 최상위 `specification/`(원문 스펙 JSON 등) — 기존 방침대로 완전히 제외했습니다.
- 이번 diff 대상 외의 나머지 `docs/public/**` 파일들은 손대지 않았습니다.
- 이번 상류 구간의 변경 대부분(총 27개 커밋, 483개 파일)은 Swift/SwiftUI 렌더러 신규 추가, v1.0 스펙 스키마 변경, 클라이언트 보안 강화 등 코드·스펙 영역이며 `docs/public/**`에는 영향을 주지 않았습니다.

## 6. 알려진 잔여 이슈 (후속 정리 권장)

- 상류 `docs/public/index.md`와 `docs/public/guides/a2ui-with-any-agent-framework.md`에는 여전히 구 Composer URL(`https://a2ui-composer.ag-ui.com/`)이 남아 있습니다. 상류 자체의 불일치이므로 이번에는 상류를 그대로 따랐고 KO 번역도 손대지 않았습니다. 상류에서 정리되면 함께 반영해야 합니다.
- 링크 점검 스크립트에서 `docs-ko` 전체 기준으로 상류 저장소 소스 경로를 가리키는 상대 링크(`../../../samples/...`, `../../../renderers/...`, `../specification/...` 등) 다수가 해석 불가로 확인되었습니다. `docs-ko`는 문서만 담은 저장소이므로 구조적으로 발생하는 문제이며 이번 diff와 무관한 기존 이슈입니다(HEAD 시점에도 동일하게 존재함을 확인). 향후 이런 링크를 `https://github.com/a2ui-project/a2ui/blob/main/...` 형태의 절대 URL로 일괄 치환하는 작업을 권장합니다. `guides/client-setup.md`, `guides/mcp-apps-in-a2ui.md` 등 일부 파일은 이미 절대 URL 방식으로 정리되어 있습니다.
- 직전 기록(`2026-08-01`)에서 언급한 잔여 이슈 — `concepts/catalogs.md`의 상류 대비 축약 상태, `guides/`·`reference/`·`concepts/` 나머지 파일의 v0.8/v0.9 탭 구조 반영 여부 — 는 이번 diff 범위 밖이라 그대로 남아 있습니다.
