# 2026-08-16 번역 동기화 기록

상류 A2UI 저장소(`https://github.com/a2ui-project/a2ui.git`)의 최신 변경 사항(커밋 `43a7bdd8` → `44a420b6`, 총 21개 커밋) 중 문서(`docs/public/**`) 관련 변경분을 동기화하고, `docs-ko` 문서를 최신화했습니다.

이번 구간의 상류 커밋 대부분은 코드·스펙 영역(v1.0 스펙 스키마 확장, `a2ui_core`/`a2ui_agent` 이관, conformance 테스트 재배치, CSP·iframe 보안 강화, 스크립트 정비 등)이며, `docs/public/**`에서 실제 diff가 발생한 파일은 마크다운 3개와 스타일시트 1개, 그리고 루트 `mkdocs.yaml` 1개로 총 5개입니다. 신규·삭제된 문서나 asset은 없습니다.

## 1. 문서 업데이트 및 번역 내역 (`docs-ko`)

- **개념 - 카탈로그 (`docs/public/concepts/catalogs.md`)** — 상류 PR #2184 (issue #2152, `CatalogId`/`Id` 일관성 정리)
  - 카탈로그 예시 JSON에 `catalogId` 필드를 추가했습니다. 상류는 3개 예시 블록 모두에 추가했으나, `docs-ko`에는 "예시: 최소 카탈로그"의 `hello_world` 블록만 존재하므로 해당 블록 1개에 반영했습니다(나머지 2개 블록은 아래 6항 참고).
  - 상류에서 `### CatalogId Naming Convention`에 **JSON Schema 호환성(`$id`와 `catalogId`)** 항목이 추가되었습니다. 그런데 `docs-ko`에는 이 항목이 속할 `## Catalog Naming & Versioning` 절 자체가 존재하지 않아, 번역할 위치가 없는 상태였습니다.
    - 이번 diff를 반영할 수 있도록 **`## 카탈로그 네이밍 및 버전 관리` 절과 그 하위 `### CatalogId 네이밍 규칙` 소절을 신규 번역해 추가**했습니다(도입 문단 + 형식/목적/런타임 페치 없음/JSON Schema 호환성 4개 항목).
    - 배치 위치는 상류 순서를 따라 `### 렌더러 구현` 뒤, `## 다음 단계` 앞으로 잡았습니다.
    - 절 제목의 `CatalogId`는 기존 관례(프로토콜 고유 식별자는 영문 유지)에 맞춰 영문 그대로 두었습니다.

- **가이드 - A2UI에서의 MCP Apps (`docs/public/guides/mcp-apps-in-a2ui.md`)** — 상류 PR #2266, #2267
  - inner iframe 보안 항목에 "하이퍼링크를 통한 데이터 유출 방어"를 신규 번역해 추가했습니다(`allow-popups`를 제외하고 링크 내비게이션을 가로채면, 새로 열린 윈도로 유도하는 클릭재킹을 통한 데이터 유출을 막을 수 있다는 내용).

- **가이드 - 렌더러 개발 (`docs/public/guides/renderer-development.md`)** — 상류 PR #2210 (v1.0 양방향 함수 호출 도입)
  - `v1.0 (후보)` 탭의 프로토콜 요구 사항을 상류 개정에 맞춰 교체했습니다.
    - `- **액션 응답(RPC)**` → `- **방향별 함수 호출(RPC)**`: `actionResponse` 처리 서술을, 에이전트로부터 오는 `callRendererFunction`을 처리하고 `rendererFunctionResponse`(또는 `error`)를 반환한다는 서술로 교체.
    - `**클라이언트-서버 통신**`: `actionId` 생성·포함, `wantResponse: true` 지원 2개 항목을 삭제하고, 원격 함수 실행을 위해 에이전트로 `callAgentFunction` 메시지를 개시하는 동작을 지원한다는 항목으로 교체.
  - `a2uiClientCapabilities` 항목과 `**Capabilities**` 절은 상류와 동일하게 유지했습니다.

## 2. 루트 파일 및 스타일시트

상류 PR #2227(저작권 고지 표준화)에 맞춰 라이선스 헤더를 상류와 동일하게 정규화했습니다. 문서 본문에는 영향이 없습니다.

- **`docs/public/stylesheets/custom.css`**: 주석 여는 기호 `/**` → `/*`, `Copyright 2025` → `Copyright 2024`, 라이선스 URL `http://` → `https://`
- **`mkdocs.yaml`**: `# Copyright 2025 Google LLC` → `# Copyright 2024 Google LLC`

`mkdocs.yaml`의 `nav` 구조에는 이번 구간에서 변경 사항이 없습니다.

## 3. 부가 수정: 매크로 오류로 깨져 있던 페이지 3개 복구

이번 구간부터 실제 `mkdocs build`로 검증하면서, **본문 대신 오류 페이지로 빌드되고 있던 문서 3개**를 발견해 함께 고쳤습니다. 모두 이번 diff와 무관한 기존 문제입니다.

공통 조치는 해당 문서 상단에 `render_macros: false` YAML front matter를 추가하는 것입니다. 세 문서 모두 실제 매크로를 사용하지 않으므로 부작용이 없습니다.

### 3-1. `Macro Rendering Error` — 상류에서 유래한 버그

- **대상**: `concepts/catalogs.md`, `guides/authoring-components.md`
- **원인**: 두 문서의 Angular 템플릿 코드 블록에 있는 `{{ message() }}` / `{{ title() }}`를 `mkdocs-macros-plugin`(Jinja2)이 매크로 변수로 해석해 `UndefinedError`가 발생합니다. 코드 블록 안이라도 매크로 플러그인은 마크다운 파싱 이전 단계에서 전체 텍스트를 렌더링하므로 보호되지 않습니다.
- **범위**: 상류 A2UI 저장소를 그대로 빌드해도 동일하게 재현되는 **상류 자체의 버그**이며, `docs-ja`에서도 동일하게 발생합니다. 상류에는 없는 로컬 수정이므로, 상류가 자체적으로 고치면 그 방식에 맞춰 정리해야 합니다.
- 이 수정 전까지는 이번에 번역한 `catalogs.md` 변경분이 빌드 결과물에 전혀 노출되지 않는 상태였습니다.

### 3-2. `Macro Syntax Error` — 직전 동기화(`2026-08-12`)에서 유입된 회귀

- **대상**: `composer/index.md`
- **원인**: 직전 동기화에서 한국어 제목의 앵커를 맞추려고 추가한 `### Gemini API 키 {#gemini-api}`의 `{#...}`를 Jinja2가 **주석 시작 태그(`{# ... #}`)로 해석**해 `Missing end of comment tag` 오류가 발생했습니다. 그 결과 Composer 문서 전체가 오류 페이지로 대체되어 있었습니다.
- **범위**: 상류에는 이 앵커가 없으므로 상류에서는 재현되지 않는, `docs-ko` / `docs-ja` 고유의 회귀입니다.
- **결과**: 수정 후 의도했던 `id="gemini-api"` 앵커와 본문 내부 링크가 모두 정상 동작함을 빌드 산출물에서 확인했습니다.
- **후속 참고**: 앞으로 `attr_list` 앵커(`{#...}`)를 쓰는 문서에는 `render_macros: false`를 함께 넣어야 합니다.

## 4. 검증

`mkdocs-material` 등 문서 의존성을 임시 venv에 설치해 이번 구간부터는 실제 빌드로 검증했습니다.

- `mkdocs build` 성공(exit 0). 변경 전 상태(`git stash`)와 변경 후 상태의 경고 목록을 비교해 **이번 작업으로 새로 생긴 경고가 없음**을 확인했습니다.
- 위 3항 수정으로 매크로 오류 경고 3건이 모두 제거되었습니다(경고 7건 → 4건).
- 빌드 산출 HTML에서 다음을 직접 확인했습니다.
  - `concepts/catalogs`: 제목이 `A2UI 카탈로그`로 정상 렌더링, `CatalogId 네이밍 규칙` / `JSON Schema 호환성` 신규 절 노출, 코드 블록의 `catalogId` 라인 렌더링
  - `guides/authoring-components`, `composer/index`: 정상 렌더링 복구(`composer/index`는 `id="gemini-api"` 앵커와 내부 링크까지 확인)
  - `guides/mcp-apps-in-a2ui`: 신규 "하이퍼링크를 통한 데이터 유출 방어" 항목 노출
  - `guides/renderer-development`: `callRendererFunction` / `callAgentFunction` / `방향별 함수 호출` 노출, `=== "v1.0 (후보)"` 탭 들여쓰기 유지
- `mkdocs.yaml`의 모든 `nav` 대상 파일 존재 여부, 변경 파일의 코드 펜스 균형, `catalogs.md` 내 JSON 블록 파싱 → 이상 없음
- `--strict` 빌드는 아래 5항의 기존 링크 경고 때문에 여전히 실패합니다(이번 작업과 무관, 변경 전에도 동일).

## 5. 범위 밖(작업하지 않음)

- `docs/contributing/**`, `eval/`, 최상위 `specification/`(원문 스펙 JSON, `specification/v1_0/docs/evolution_guide.md` 등) — 기존 방침대로 완전히 제외했습니다.
  - 이번 구간에 상류 PR #2235로 `specification/v1_0/docs/evolution_guide.md`가 개정되었으나(41줄 추가/27줄 삭제), `docs/public/specification/v1.0-evolution-guide.md`는 해당 파일을 `--8<--`로 포함만 하는 stub이며 `docs-ko`에는 원문 스펙 소스가 없으므로 대상에서 제외했습니다.
- 이번 diff 대상 외의 나머지 `docs/public/**` 파일들은 손대지 않았습니다.

## 6. 알려진 잔여 이슈 (후속 정리 권장)

- **`concepts/catalogs.md`의 상류 대비 축약 상태(지속)**: 상류 475줄 대비 `docs-ko`는 179줄입니다. 이번에 `## 카탈로그 네이밍 및 버전 관리`의 도입부와 `### CatalogId 네이밍 규칙`만 추가했으므로, 여전히 다음 절들이 통째로 누락되어 있습니다.
  - `## A2UI Catalog Negotiation`(3단계 협상 절차 전체)
  - `### Versioning Guidelines`, `### Graceful Degradation`, `### Versioning with CatalogId`, `### Handling Migrations`
  - `## A2UI Schema Validation & Fallback`(2단계 검증, 클라이언트-서버 오류 보고)
  - `## Inline Catalogs`
  - 또한 `### 합성과 import` 하위의 예시 2개(`hello_world_with_all_basic`, `hello_world_with_some_basic`)와 `## 카탈로그 정의` 상단의 Catalog JSON Schema 발췌도 누락되어 있습니다. 이번 상류 diff의 `catalogId` 추가분 3건 중 2건이 이 때문에 반영 대상이 없었습니다(`docs-ja`에는 해당 예시가 있어 3건 모두 반영됨).
- **상대 링크 해석 불가(지속)**: `docs-ko`는 문서 전용 저장소이므로 `../../../samples/...`, `../../../renderers/...`, `../specification/...` 형태의 상류 소스 경로 링크가 해석되지 않습니다. 3항 수정 후 남은 빌드 경고 4건이 모두 이 원인(`concepts/glossary.md`)이며, `--strict` 빌드가 실패하는 이유이기도 합니다. `https://github.com/a2ui-project/a2ui/blob/main/...` 절대 URL로 일괄 치환을 권장합니다.
- **상류 문서 자체의 모순(신규 발견)**: `guides/mcp-apps-in-a2ui.md`에서 권한 항목은 여전히 `sandbox="allow-scripts allow-forms allow-popups allow-modals"`로 `allow-popups`를 포함한다고 적혀 있는데, 이번에 추가된 "하이퍼링크를 통한 데이터 유출 방어" 항목은 `allow-popups`를 제외하라고 설명합니다. 상류 원문을 그대로 따라 번역했으며, 상류에서 정리되면 함께 반영해야 합니다.
- **상류의 구 Composer URL 잔존(지속)**: `docs/public/index.md`, `guides/a2ui-with-any-agent-framework.md`의 `https://a2ui-composer.ag-ui.com/`는 이번 구간에도 정리되지 않았습니다.
- 직전 기록(`2026-08-12`)의 나머지 잔여 이슈(`guides/`·`reference/`·`concepts/` 파일의 v0.8/v0.9 탭 구조 반영 여부)는 이번 diff 범위 밖이라 그대로 남아 있습니다.
