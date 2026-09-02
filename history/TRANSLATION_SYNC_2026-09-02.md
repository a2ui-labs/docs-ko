# 2026-09-02 번역 동기화 기록

상류 A2UI 저장소(`https://github.com/a2ui-project/a2ui.git`)의 최신 변경 사항(커밋 `18ae5afe` → `a6a2eba2`)을 반영하여 `docs-ko` 문서를 최신화했습니다.

## 1. 주요 변경 및 번역 반영 내역

### 1-1. 빠른 시작 (Quickstart)
- **빠른 시작 (`docs/public/quickstart.md`)**
  - **Flutter 빠른 시작 탭 추가**: 기존 Lit 단일 안내에서 `=== "Lit"` / `=== "Flutter"` 탭 분기 구조로 개편 (Flutter SDK 설치 및 `flutter run -d chrome` 실행 단계 안내 추가)
  - **다이어그램 현대화**: 기존 ASCII 다이어그램을 Mermaid 시퀀스 다이어그램(`sequenceDiagram`)으로 교체
  - **동작 원리 및 소스 코드 탐색 섹션 추가**: "어떻게 동작하나요? (How does it work?)", "소스 코드 살펴보기 (Peek at the source code)" 섹션 신설
  - **콜아웃 서식 표준화**: `> [!WARNING]`, `> [!TIP]`, `> [!INFO]` 등 최신 GFM Admonition 포맷으로 통일
  - **다음 단계 링크 추가**: "나만의 카탈로그 정의하기 (Defining Your Own Catalog)" 가이드 링크 추가

### 1-2. 개발자 가이드 (Guides)
- **A2UI over MCP (`docs/public/guides/a2ui_over_mcp.md`)**
  - **템플릿-데이터 분리 아키텍처 반영**: 정적 프레젠테이션 템플릿(`a2ui://recipe-card`)을 MCP 리소스로 서빙하고, 동적 상태는 도구 호출 시 `updateDataModel`로 반환하는 최신 설계 패턴 반영
  - **도구 UI 메타데이터(`_meta.ui`) 반영**: MCP Tool 선언 및 반환 시 UI 템플릿 리소스 URI(`resourceUri: "a2ui://recipe-card"`)를 명시하도록 예제 코드 전면 업데이트
  - **카탈로그 ID 갱신**: 카탈로그 협상 예시 URI를 `https://a2ui.org/specification/v0_9/basic_catalog.json`으로 최신화

### 1-3. 생태계 (Ecosystem)
- **생태계 렌더러 (`docs/public/ecosystem/renderers.md`)**
  - **Svelte 5 렌더러 추가**: `ChaliceForAuri/a2ui-svelte` (`svelte-a2ui`) 렌더러 항목 표 및 상세 설명 추가
  - **에이전트 사이드 라이브러리 섹션 신설 (`### Agent-Side Libraries`)**: A2UI 생성 서버 측 라이브러리 `Max-Health-Inc/prefab` (`@maxhealth.tech/prefab`) 소개 추가

## 2. 검증 결과

- `docs-ko` 환경에서 `mkdocs build` 정상 완료 (exit code 0).
- 빌드된 모든 문서 페이지 및 링크, Mermaid 다이어그램 정상 렌더링 확인.
