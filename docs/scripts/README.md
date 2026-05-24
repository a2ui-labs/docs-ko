# 문서 변환 스크립트

이 디렉터리에는 **MkDocs** 빌드 프로세스를 위해 문서를 준비하는 유틸리티 스크립트가 들어 있습니다.

## 목적

GitHub와 호스팅된 사이트 모두에서 좋은 읽기 경험을 제공하기 위해, 이 프로젝트는 **GitHub-flavored Markdown**을 기본 원본 형식으로 사용합니다. 이 스크립트는 빌드 파이프라인에서 GitHub의 네이티브 문법을 **MkDocs 호환 문법**으로 변환합니다. 특히 `pymdown-extensions`에 맞춘 변환을 수행합니다.

## 지원되는 변환(단방향)

이 스크립트는 **GitHub Markdown → MkDocs Syntax** 단방향 변환을 수행합니다.

### 알림/Admonition 변환

스크립트는 다음 변환을 처리합니다.

- GitHub는 알림에 blockquote 기반 문법을 사용합니다.
- MkDocs는 색상이 있는 callout 박스를 렌더링하기 위해 `!!!` 또는 `???` 문법을 필요로 합니다.

## 변환 실행

변환은 빌드 파이프라인의 일부로 실행됩니다. 추가 작업은 필요하지 않습니다. 수동으로 변환을 실행해야 한다면 저장소 루트에서 `convert_docs.py` 스크립트를 실행할 수 있습니다.

```bash
python docs/scripts/convert_docs.py
```

### 예시

- **원본(GitHub-flavored Markdown):**

    ```markdown
    > ⚠️ **Attention**
    >
    > This is an alert.
    ```

- **대상(MkDocs Syntax):**

    ```markdown
    !!! warning "Attention"
    This is an alert.
    ```
