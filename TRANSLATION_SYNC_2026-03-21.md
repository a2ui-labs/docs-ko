# 2026-03-21 번역 동기화 기록

## 작업 단위

1. `mkdocs.yaml`
   - 최신 A2UI 문서 구조에 맞게 내비게이션을 재구성했습니다.
   - `concepts`, `guides`, `reference`, `ecosystem` 하위 경로를 추가했습니다.
   - `renderers.md`, `agents.md`, `transports.md`, `community.md`, `introduction/where-is-it-used.md`에 대한 리다이렉트 맵을 추가했습니다.

2. 진입 문서
   - `docs/index.md`를 최신 영어 원문 구조에 맞춰 갱신했습니다.
   - `docs/quickstart.md`를 v0.8/v0.9 예시와 최신 설치 흐름으로 정리했습니다.

3. 핵심 개념
   - `docs/concepts/overview.md`
   - `docs/concepts/data-flow.md`
   - `docs/concepts/data-binding.md`
   - `docs/concepts/catalogs.md`
   - `docs/concepts/client_to_server_actions.md`
   - `docs/concepts/transports.md`

4. 가이드
   - `docs/guides/a2ui_over_mcp.md`를 추가했습니다.

5. 생태계/레퍼런스
   - `docs/ecosystem/a2ui-in-the-world.md`
   - `docs/ecosystem/community.md`
   - `docs/ecosystem/renderers.md`
   - `docs/reference/renderers.md`
   - `docs/reference/agents.md`

## 참고 사항

- 기존 번역 문서는 가능한 한 유지했고, 새 구조에서 필요한 문서만 추가/갱신했습니다.
- 일부 기존 문서(`docs/reference/components.md`, `docs/reference/messages.md`, `docs/guides/*`, `docs/introduction/*`, `docs/roadmap.md`)는 후속 정리가 필요할 수 있습니다.
- 외부 링크는 새 구조와 호환되도록 조정했지만, 저장소에 없는 일부 원문 스키마 파일 링크는 로컬 문서 링크로 대체하거나 요약했습니다.
