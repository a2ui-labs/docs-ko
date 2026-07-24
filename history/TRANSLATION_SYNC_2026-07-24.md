# 2026-07-24 번역 동기화 및 Upstream 반영 기록

상류 A2UI 저장소 (`https://github.com/a2ui-project/a2ui.git`) 의 최신 변경 사항 (커밋 `3708c069` → `d4723f29`, 총 22개 커밋) 을 동기화하고, `docs-ko` 문서를 최신화했습니다.

## 1. Upstream 푸시 완료
- `A2UI` 저장소 local `main` 브랜치를 `upstream/main`과 성공적으로 병합(merge) 후 `origin/main`으로 푸시 완료.

## 2. 문서 업데이트 및 번역 내역 (`docs-ko`)
- **생태계 렌더러 (`docs/public/ecosystem/renderers.md`)**:
  - `AGenUI` (iOS, Android, HarmonyOS 지원 크로스플랫폼 네이티브 렌더러) 항목 및 BoteAI 렌더러 정보 갱신.
  - `BBC6BAE9/a2ui-swift` 렌더러 묘사 문구를 최신 upstream 스펙 충실도 및 Apple 네이티브 플랫폼 동작 관련 문구로 개편 번역.
- **레퍼런스 (`docs/public/reference/renderers.md`)**:
  - 커뮤니티 렌더러 목록에 `AGenUI` 추가 반영.
- **Specification (v1.0)**:
  - `specification/v1_0/` 상류 스키마 및 용어 변경 사항(`agent`, `renderer` 표준화 및 파일명 변경) 자동 연동 점검.
