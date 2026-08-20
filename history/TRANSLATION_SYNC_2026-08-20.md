# 2026-08-20 번역 동기화 기록

상류 A2UI 저장소(`https://github.com/a2ui-project/a2ui.git`)의 최신 변경 사항(커밋 `44a420b6` → `ca09fac3`)을 동기화하고, `docs-ko` 문서 최신화 및 스펙(Specification) 문서 누락 문제를 전면 개선했습니다.

## 1. 주요 개선 사항: 스펙 문서 누락 문제 해결

### 1-1. 배경 및 원인 분석
- `docs/public/specification/*.md` 파일들은 상류 저장소의 최상위 `specification/**` 디렉터리에 있는 원본 마크다운 파일들을 `--8<--` 스니펫 매크로로 임베드하는 방식으로 구성되어 있습니다.
- 기존 `docs-ko` 저장소에는 최상위 `specification/` 디렉터리가 포함되어 있지 않았으며, `docs-ko/docs/public/specification/` 내부의 스니펫 경로가 잘못 표기되어 있었습니다(예: `specification/1.0/` vs `specification/v1_0/`).
- 이로 인해 빌드된 사이트에서 스펙 관련 페이지(v0.8, v0.9, v0.9.1, v1.0 프로토콜 명세, 확장 명세, 발전 가이드 등)에 접속했을 때 상단 안내 링크만 노출되고 본문 내용이 비어 있는 문제가 있었습니다.

### 1-2. 조치 내역
- 상류의 최신 `specification/` 디렉터리를 `docs-ko/specification/`으로 완전히 동기화했습니다.
- `docs-ko/docs/public/specification/*.md` (총 12개 파일)의 임베드 경로를 `specification/v0_8/`, `specification/v0_9/`, `specification/v0_9_1/`, `specification/v1_0/` 표준 경로로 수정했습니다.
- 모든 스펙 래퍼 문서의 제목에 표준 버전 배지(`<span class="version-badge ...">`)와 상류 정합 한국어 안내 블록을 적용했습니다.
- 빌드 검증을 통해 12개 스펙 문서가 모두 완전한 본문으로 정상 렌더링됨을 확인했습니다.

## 2. 문서 업데이트 및 번역 내역 (`docs-ko`)

- **퀵스타트 (`docs/public/quickstart.md`)** — 상류 PR #2107
  - `### 다른 언어 및 프레임워크` 섹션에 Flutter 샘플 경로(`- **Flutter**: samples/client/flutter`)를 추가했습니다.
- **가이드 - 모든 에이전트 프레임워크에서 A2UI 사용 (`docs/public/guides/a2ui-with-any-agent-framework.md`)** — 상류 PR #2328
  - `mkdocs.yaml`의 매크로 기본 비활성화 설정에 맞춰 불필요해진 `{% raw %}` 및 `{% endraw %}` 블록을 제거했습니다.
- **`mkdocs.yaml` 플러그인 설정**
  - 상류 PR #2328 반영: Jinja2 매크로 렌더링 기본 비활성화(`render_by_default: false`, `on_error_fail: true`)를 적용했습니다.

## 3. 검증 결과

- 임시 venv 환경에서 `mkdocs build` 실행 완료(exit code 0).
- `site/specification/*` 산출물에서 모든 프로토콜 명세 및 발전 가이드 본문 정상 렌더링 확인.
- `site/quickstart/index.html` 및 `site/guides/a2ui-with-any-agent-framework/index.html` 정상 렌더링 확인.
