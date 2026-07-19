# 2026-07-19 번역 동기화 기록

상류 A2UI 저장소(`google/A2UI` → `a2ui-project/a2ui`)가 커밋 `305336f1` → `3708c069`(223개 커밋)로 fast-forward된 것에 맞춰 `docs-ko`를 최신화했습니다.

## 1. 구조 변경: `docs/` → `docs/public/`

상류에서 `mkdocs.yaml`의 `docs_dir`이 `docs`에서 `docs/public`으로 바뀌었습니다. `docs-ko`도 동일하게 맞췄습니다.

- `git mv`로 `docs/*`의 모든 파일(단, `docs/scripts/**` 제외)을 `docs/public/*`로 이동해 히스토리를 보존했습니다.
- `docs/glossary.md`는 `docs/public/concepts/glossary.md`로 이동했습니다(상류와 동일하게, `concepts/` 하위로 편입).
- `docs/scripts/`는 그대로 유지했습니다(`convert_docs.py`, `test_convert_docs.py`는 번역 대상이 아닌 개발 도구 스크립트로, 상류 버전으로 그대로 교체했습니다. `docs/scripts/README.md`는 상류에서도 변경이 없어 그대로 두었습니다).
- 이동 후 최종 트리를 상류의 `git ls-tree`와 비교해 완전히 일치함을 확인했습니다(단, 상류에만 있는 `docs/public/CNAME`은 안내에 따라 추가하지 않았습니다).

새로 추가된 바이너리 자산(`assets/favicon.svg` 등)을 상류에서 그대로 복사했습니다. 반대로 `assets/agent-and-renderer.png`는 상류에서 삭제되고 `glossary.md`의 정적 이미지가 mermaid 시퀀스 다이어그램으로 대체되었으므로, 더 이상 참조되지 않는 이 파일을 함께 제거했습니다.

## 2. `mkdocs.yaml`

- `docs_dir: docs/public` 추가.
- 개념(Concepts) 내비게이션에 `용어집(Glossary): concepts/glossary.md` 추가.
- 가이드(Guides) 내비게이션: "모든 에이전트 프레임워크에서 A2UI 사용 (AG-UI)" → "모든 에이전트 프레임워크 및 하니스에서 A2UI 사용"으로 라벨 변경. MCP 관련 가이드 3개(`a2ui_over_mcp.md`, `mcp-apps-in-a2ui.md`, `a2ui-in-mcp-apps.md`)를 "A2UI + MCP" 하위 그룹으로 재편.
- 사양(Specifications) 내비게이션을 4단계로 확장: v1.0(후보), v0.9.1(현재), v0.9(이전 안정판), v0.8(레거시). 각 버전에 A2UI 프로토콜/A2A 확장/발전 가이드/기본 카탈로그 가이드 항목 추가(v0.8·v0.9는 상류와 동일하게 일부 항목만 존재).
- `exclude_docs`에 `specification/v*/**/*.md` 패턴 추가.
- `repo_name`/`repo_url`/`edit_uri`를 `google/A2UI` → `a2ui-project/a2ui`(및 `raw/main/docs/public/`)로 변경.
- `favicon`을 `assets/A2UI_dark.svg` → `assets/favicon.svg`로 변경.
- 주석 처리되어 있던 `llmstxt` 플러그인 블록 제거(상류와 동일하게 정리).

## 3. 콘텐츠 번역

아래 파일들을 상류 변경분에 맞춰 갱신했습니다. 기존 번역 중 내용이 바뀌지 않은 문단은 그대로 유지하고, 새로 추가되거나 바뀐 내용만 새로 번역했습니다.

- **루트**: `README.md`(v0.9.1 현재 릴리스 안내, Corepack/Yarn 설치 흐름, `a2ui-project/a2ui` 저장소 URL, "AG-UI CLI" 흐름, `npx create-ag-ui-app@latest` 반영).
- **최상위 문서**: `index.md`, `quickstart.md`, `roadmap.md`.
- **개념(concepts/)**: `actions.md`, `catalogs.md`, `components.md`, `data-binding.md`, `data-flow.md`(AG-UI 표기만 수정), `overview.md`(용어집 링크 수정 및 메시지 타입 탭 v0.9/v1.0/v0.8 3단 구성으로 재편), `transports.md`, `glossary.md`(신규 위치, mermaid 다이어그램으로 교체, 상대 링크 깊이 수정).
- **생태계(ecosystem/)**: `a2ui-in-the-world.md`, `community.md`. `renderers.md`는 상류에서 구조와 내용이 전면 개편되어 기존 번역을 참고 삼아 새로 번역했습니다.
- **가이드(guides/)**: `a2ui-in-mcp-apps.md`, `a2ui_over_mcp.md`, `agent-development.md`, `authoring-components.md`, `client-setup.md`, `defining-your-own-catalog.md`, `mcp-apps-in-a2ui.md`, `theming.md`. 이 중 여러 파일은 이번 동기화 기준선(`305336f1`)보다도 더 오래된 번역 상태였음이 확인되어, 해당 파일들은 상류 최신본 구조에 맞춰 전체적으로 갱신했습니다(`client-setup.md`, `theming.md`, `a2ui_over_mcp.md` 등).
  - `renderer-development.md`, `a2ui-with-any-agent-framework.md`는 상류에서 전면 재작성(각각 +186줄, +511줄)되어 기존 번역을 용어/문체 참고용으로만 사용하고 새로 번역했습니다.
- **레퍼런스(reference/)**: `agents.md`, `components.md`, `messages.md`, `renderers.md`(v1.0 열 추가, 렌더러별 상태 갱신).
- **소개(introduction/)**: `agent-ui-ecosystem.md`(비교 표·ChatKit 섹션 등 신규 구조 반영), `how-to-use.md`(AG-UI 표기 수정), `what-is-a2ui.md`, `who-is-it-for.md`.
- **사양(specification/)**:
  - 기존 파일 소폭 수정: `v0.8-a2ui.md`, `v0.8-a2a-extension.md`, `v0.9-a2ui.md`, `v0.9-evolution-guide.md` (버전 상태 라벨과 상호 참조 링크를 v1.0/v0.9.1 신설에 맞춰 갱신).
  - 신규 파일 8개 번역: `v0.9.1-a2ui.md`, `v0.9.1-a2ui-extension-specification.md`, `v0.9.1-basic-catalog-implementation-guide.md`, `v0.9.1-evolution-guide.md`, `v1.0-a2ui.md`, `v1.0-a2ui-extension-specification.md`, `v1.0-basic-catalog-implementation-guide.md`, `v1.0-evolution-guide.md`. 기존 `v0.9-a2ui.md` 등에서 확립된 docs-ko 자체 템플릿(관리자용 안내 admonition + `--8<--` 스니펫 include, 점(dot) 표기 버전 경로)을 그대로 따랐습니다.

전 파일에서 "AG UI"(공백) 표기를 "AG-UI"(하이픈)로, `google/A2UI` 저장소 링크를 `a2ui-project/a2ui`로 일괄 정정했습니다.

## 4. 상호 참조 점검

`glossary.md`가 최상위 `docs/`에서 `concepts/`로 이동하면서, 이를 참조하던 `concepts/overview.md`의 링크를 `../glossary.md` → `glossary.md`(같은 폴더 내 형제 링크)로 수정했습니다. 전체 트리를 검색한 결과 `glossary.md`를 참조하는 곳은 이 한 곳뿐이었고, 상류와 정확히 동일한 링크 텍스트로 맞춰져 있음을 확인했습니다.

## 5. 범위 밖(작업하지 않음)

- 최상위 `specification/`(원문 스펙 JSON 등)과 `eval/` 폴더 — 지시에 따라 완전히 제외했습니다.
- `docs/scripts/convert_docs.py`, `docs/scripts/test_convert_docs.py` — 번역하지 않고 상류 버전을 그대로 복사했습니다.
- `docs/scripts/README.md` — 상류에서도 변경이 없어 그대로 두었습니다.
- `docs/public/CNAME` — 지시에 따라 추가하지 않았습니다.
- `composer.md` — 상류에서 100% 동일(rename만 발생)하여 번역 작업이 필요하지 않았습니다.

## 6. 알려진 잔여 이슈 (후속 정리 권장)

- `docs/public/quickstart.md`의 "연락처 조회 데모"(`npm run demo:contact`) 섹션은 docs-ko에만 있던 콘텐츠로, 상류 저장소 어디에도 `demo:contact` 스크립트가 존재한 적이 없습니다. 이번 동기화 대상 diff에는 포함되지 않는 사전 존재 이슈라 손대지 않았지만, 다음 정리 때 실제 샘플과 맞는지 확인이 필요합니다.
- 일부 `guides/*`, `introduction/*` 파일은 `305336f1` 기준선보다도 오래된 번역이었음이 이번 작업 중 드러났습니다(예: `client-setup.md`, `theming.md`, `agent-ui-ecosystem.md`). 이번 동기화에서 최신 상류 구조에 맞춰 갱신했으나, 향후 상류 변경 시 이런 "숨은 지연"이 다시 쌓이지 않는지 주기적으로 전체 diff 점검을 권장합니다.
