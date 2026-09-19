# 2026-09-19 번역 동기화 기록

상류 A2UI 저장소(`https://github.com/a2ui-project/a2ui.git`)의 최신 변경 사항(커밋 `a6a2eba2` → `04e6f07f`)을 반영하여 `docs-ko` 문서를 최신화했습니다.

## 1. 주요 변경 및 번역 반영 내역

### 1-1. 개념 (Concepts)
- **카탈로그 (`docs/public/concepts/catalogs.md`)**
  - **버전 관리 가이드라인 (Versioning Guidelines) 전면 개편**: 에이전트와 렌더러 간 버전 불일치(version skew)를 흡수할 수 있도록 하위 및 상위 호환성 유지 원칙 상세화
  - **카탈로그 스키마 발전 규칙 7가지 추가**:
    1. 추가만 허용 (Additive Only)
    2. 삭제 대신 지원 중단 (Deprecate Rather than Delete)
    3. 타입 불변성 (Type Invariance)
    4. 개방형 열거형 (Open Enums)
    5. 우아한 성능 저하 (Graceful Degradation)
    6. 알 수 없는 필드의 왕복 보존 (Round-Trip Unknown Preservation)
    7. 메이저 버전 판올림 원칙
  - **속성 지원 중단 규칙 (Deprecating Catalog Properties)**: `deprecated: true` 및 `x-deprecated-reason` 설명과 예제 JSON 추가
  - **CatalogId 메이저 버전 판올림 및 무중단 마이그레이션 패턴**: URI 기반 네고시에이션 가이드 및 표 제공
  - **2단계 스키마 검증 및 폴백 전략**: 에이전트 사전 검증과 클라이언트 검증, 런타임 폴백, 클라이언트 오류 보고(`VALIDATION_FAILED`) 절차 보완
  - **인라인 카탈로그** 안내 보강

- **용어집 (`docs/public/concepts/glossary.md`)**
  - **Capabilities Object (기능 객체) 섹션 신설**: 지원 카탈로그 집합 광고(advertisement), 전송 메타데이터 교환 방식 및 프로토콜 버전별 명칭 차이 정리

### 1-2. 빠른 시작 (Quickstart)
- **빠른 시작 (`docs/public/quickstart.md`)**
  - **Flutter 렌더러 경로 최신화**: 상류 디렉터리 리팩토링에 따라 `renderers/flutter/`에서 `dart/a2ui_flutter/`로 경로 수정

### 1-3. 개발자 가이드 (Guides)
- **에이전트 프레임워크 연동 (`docs/public/guides/a2ui-with-any-agent-framework.md`)**
  - CopilotKit 스킬 링크 최신화 (`a2ui-renderer` 스킬 → `copilotkit` 스킬)

### 1-4. 사양 (Specification)
- `specification/v1_0/` 최신 변경사항 동기화:
  - 프로토콜 명세(`a2ui_protocol.md`) 및 발전 가이드(`evolution_guide.md`) 최신화
  - 카탈로그 정의(`catalog_definition.json`), 공통 타입(`common_types.json`), 기본 카탈로그(`catalog.json`) 최신화
  - 상류 구조 변경에 따른 `specification/*/eval` 정리

## 2. 검증 결과

- `docs-ko` 환경에서 `mkdocs build` 정상 완료 (exit code 0).
- 마크다운 include, 빌드된 모든 문서 페이지 및 링크 렌더링 정상 동작 확인.
