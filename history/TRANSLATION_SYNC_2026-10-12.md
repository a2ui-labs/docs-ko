# 2026-10-12 번역 동기화 기록

상류 A2UI 저장소(`https://github.com/a2ui-project/a2ui.git`)의 최신 변경 사항(커밋 `04e6f07f` → `ce03d005`)을 반영하여 `docs-ko` 문서를 최신화했습니다.

## 1. 주요 변경 및 번역 반영 내역

### 1-1. 개념 (Concepts)
- **액션 (`docs/public/concepts/actions.md`)**
  - Python SDK 디렉터리 구조 변경에 따라 `A2uiSubagentMap` 경로를 `agent_sdks/python/...`에서 `python/a2ui_agent/...`로 수정
- **용어집 (`docs/public/concepts/glossary.md`)**
  - 상류 용어집 개편 반영: 에이전트-렌더러-모델 상호작용 시퀀스 다이어그램, A2UI SDK 안내 및 3대 분류 체계(프로토콜 용어, 에이전트 용어, 생성형 UI 용어) 도입
  - **Function / Function call / Action** 섹션 전면 개편: 렌더러 함수·에이전트 함수 구분, LLM 도구 호출과의 비교표, 함수 호출 맥락 4가지(동적 값 바인딩, 검증 규칙, 컴포넌트 상호작용 트리거, RPC) 및 액션과의 차이점 상세화
  - **Inference format (추론 포맷)** 섹션 신설 및 응답 파싱 단계(태그 언래핑, 컴파일) 보강
  - **권장 코드 구성 (Recommended code organization)**: Agent SDK가 포함된 최신 아키텍처 다이어그램 및 `typescript/web_core` 경로 반영

### 1-2. 생태계 및 레퍼런스 (Ecosystem & Reference)
- **생태계 렌더러 (`docs/public/ecosystem/renderers.md`)**
  - 커뮤니티 렌더러 표에 `v1.0` 지원 여부 열 추가
  - 신규 커뮤니티 렌더러 2종 추가 및 하이라이트 번역:
    - `Language-Lit/material3-expressive-a2ui` (`@language-lit/material3-expressive-a2ui`)
    - `uny/a2ui-compose` (`dev.ynagai.a2ui:a2ui-material3`)
  - 상류에서 제거된 `TanXudong-Vivo/A2UI-Android-Renderer` 항목 제거 및 `BBC6BAE9/a2ui-swift` 플랫폼 표기 최신화
  - 커뮤니티 렌더러 등재 기준의 사양 버전 안내에 `v1.0` 추가
- **실제 환경에서의 A2UI (`docs/public/ecosystem/a2ui-in-the-world.md`)**
  - Google ADK 섹션의 Python 에이전트 SDK 경로(`python/a2ui_agent`) 최신화 및 AG2 파트너 통합 섹션 등 상류 전체 내용 반영
- **렌더러 레퍼런스 (`docs/public/reference/renderers.md`)**
  - 유지 관리되는 렌더러 표를 웹/모바일로 구분하고 **Jetpack Compose**의 `v0.9.1` 상태를 `✅ 알파`로 업데이트(공식 문서 및 코드 링크 추가)

### 1-3. 가이드, 홈 및 빠른 시작 (Guides, Home & Quickstart)
- **에이전트 개발 가이드 (`docs/public/guides/agent-development.md`)**, **MCP를 통한 A2UI (`docs/public/guides/a2ui_over_mcp.md`)**, **컴포넌트 작성 (`docs/public/guides/authoring-components.md`)**
  - Python Agent SDK 리팩토링 반영: `A2uiSchemaManager` 대신 `DirectJsonFormat`, `BasicCatalog`, `resolve_catalogs`, `validate_payload`를 사용하는 최신 API 코드 예제 및 `python/a2ui_agent` 경로로 업데이트
- **A2UI 내 MCP 앱 (`docs/public/guides/mcp-apps-in-a2ui.md`)**
  - `Catalog` 생성자 호출 예시에 `'v0.9'` 버전 인자 추가
- **렌더러 개발 (`docs/public/guides/renderer-development.md`)**, **테마 설정 (`docs/public/guides/theming.md`)**, **빠른 시작 (`docs/public/quickstart.md`)**
  - `@a2ui/web_core` 패키지 경로를 `renderers/web_core`에서 `typescript/web_core`로 업데이트
- **홈 (`docs/public/index.md`)**
  - `A2UI Composer` 링크(`https://a2ui-project.github.io/composer/`) 및 에이전트 프레임워크 가이드 카드 반영

### 1-4. 사양 및 카탈로그 (Specification & Catalogs)
- **기본 카탈로그 구현 가이드 (`docs/public/specification/v1.0-basic-catalog-implementation-guide.md`)**
  - 상류에서 v1.0 기본 카탈로그가 최상위 `catalogs/basic/v1/` 디렉터리로 이동함에 따라 include 경로를 `catalogs/basic/v1/basic_catalog_implementation_guide.md`로 업데이트
- `specification/` 및 `catalogs/` 디렉터리를 상류 최신 상태로 동기화

## 2. 검증 결과

- `docs-ko` 환경에서 `mkdocs build` 정상 완료 (exit code 0).
- 마크다운 include, 빌드된 모든 문서 페이지 및 링크 렌더링 정상 동작 확인.
