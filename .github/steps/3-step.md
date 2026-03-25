## Step 3: 권한 인식 배포 및 PR 피드백 추가하기

훌륭한 진전입니다! 이미 `ci.yml`에서 재사용 가능한 품질 검사 워크플로우를 호출하고 있습니다.

이 마지막 단계에서는 워크플로우 권한을 이해해야 하는 더 많은 기능으로 CI 워크플로우를 확장합니다.

또한 **Octomatch**를 GitHub Pages에 배포합니다!

### 📖 이론: 재사용 가능한 워크플로우에 권한이 어떻게 전달되나요

워크플로우가 재사용 가능한 워크플로우를 호출하면, 토큰 권한이 상속되며 동일하게 유지되거나 더 제한적으로만 변경될 수 있습니다.

재사용 가능한 워크플로우는 중첩될 수 있으며, 각 레벨은 원래 최상위 워크플로우가 아닌 직접 호출자로부터 상속됩니다.

| 권한이 설정되는 위치      | 제어하는 것                                                                 | 여기서 권한을 확장할 수 있나요? |
| ------------------------------ | -------------------------------------------------------------------------------- | --------------------------------- |
| 호출자 워크플로우 (예: `ci.yml`) | 모든 호출된 워크플로우와 해당 작업에 사용 가능한 권한 상한          | 예 (상한을 설정)        |
| 호출된 재사용 가능한 워크플로우       | 자체 작업과 하위 재사용 가능한 호출에 대한 권한 범위    | 아니오, 동일하거나 더 좁게만         |
| 중첩된 재사용 가능한 워크플로우 호출 | 직접 부모 재사용 가능한 워크플로우에서 전달된 상속된 권한 상한 | 아니오, 동일하거나 더 좁게만         |

### ⌨️ 활동: 저장소의 GitHub Pages 활성화하기

다음 활동에서는 **Octomatch**를 GitHub Pages에 배포하는 `CI` 워크플로우를 확장할 것입니다. 그 전에 이 저장소에서 GitHub Pages를 활성화해야 합니다.

1. 저장소 [설정](https://github.com/{{ full_repo_name }}/settings)으로 이동하세요.
1. 왼쪽 사이드바에서 `Pages`를 클릭하세요.
1. `Source`에서 `GitHub Actions`를 선택하세요.

<img width="900" alt="GitHub Pages 설정을 보여주는 이미지" src="https://raw.githubusercontent.com/skills-kr/reusable-workflows/main/.github/images/pages-settings.png" />

이제 모든 Pull Request에서 앱을 GitHub Pages에 배포하는 배포 작업을 워크플로우에 추가할 준비가 되었습니다!

### ⌨️ 활동: 배포 및 PR 댓글 작업을 워크플로우에 추가하기

다음 목표는 앱을 배포하고 배포된 페이지 URL을 Pull Request에 댓글로 남기는 것입니다.

`deploy-pages.yml`이라는 재사용 가능한 워크플로우가 이미 `.github/workflows` 디렉토리에 있습니다. 이 워크플로우는 앱을 GitHub Pages에 배포하고 페이지 URL을 출력합니다.

이것을 사용해 봅시다!

1. `.github/workflows/ci.yml`을 열어주세요.
1. `deploy-pages.yml` 재사용 가능한 워크플로우를 호출하는 추가 작업을 `ci.yml` 워크플로우에 포함합시다.

   `ci.yml` 파일 끝에 다음 작업을 추가하세요:

   ```yaml
   github-pages:
     name: Deploy to GitHub Pages
     needs: quality
     uses: ./.github/workflows/deploy-pages.yml
     with:
       node-version: "24"
     permissions:
       contents: read
       pages: write
       id-token: write
   ```

   `needs` 키워드는 이 작업이 `quality` 작업이 성공한 후에만 실행되도록 보장합니다.

   `permissions` 블록은 워크플로우 수준에서 설정된 기본 `contents: read` 권한을 넘어 이 작업의 권한을 재정의하고 확장하는 데 사용됩니다.

1. `github-pages` 작업의 `page_url` 출력을 사용하여 Pull Request에 댓글을 남기는 또 다른 작업을 추가하세요.

   `ci.yml`에 다음 작업을 추가하세요:

   ```yaml
   comment:
     name: Comment on PR
     if: always()
     needs: github-pages
     runs-on: ubuntu-latest
     permissions:
       pull-requests: write
     steps:
       - name: Comment with Pages URL
         uses: GrantBirki/comment@v2.1.1
         with:
           issue-number: {% raw %}${{ github.event.pull_request.number }}{% endraw %}
           body: |
             | 항목 | 링크 |
             | --- | --- |
             | 🌐 GitHub Pages URL | {% raw %}${{ needs.github-pages.outputs.page_url }}{% endraw %} |
             | 🧾 워크플로우 로그 | https://github.com/{% raw %}${{ github.repository }}{% endraw %}/actions/runs/{% raw %}${{ github.run_id }}{% endraw %} |
   ```

   `if: always()` 조건은 배포가 실패하더라도 댓글 작업이 실행되도록 보장하여, 해당 경우에도 워크플로우 로그를 확인할 수 있게 합니다.

### ⌨️ 활동: 변경사항 확인, 커밋 및 푸시하기

이 단계에서 많은 작업을 했습니다! YAML 들여쓰기가 까다로울 수 있으므로, 먼저 `actionlint`를 사용하여 워크플로우 파일에 구문 오류가 없는지 확인합시다!

1. 터미널에서 다음 명령어를 실행하여 `ci.yml` 워크플로우 파일의 구문 오류를 확인하세요:

   ```bash
   actionlint .github/workflows/ci.yml
   ```

   또는 모든 워크플로우 파일을 확인하려면:

   ```bash
   actionlint
   ```

   오류가 있으면 진행하기 전에 수정하세요.

1. `ci.yml` 변경사항을 `reusable-workflows` 브랜치에 커밋하고 푸시하세요.
1. Pull Request에서 `CI` 워크플로우가 실행되는 것을 모니터링하고 완전히 완료될 때까지 기다리세요.
1. 완료되면 Pull Request에 GitHub Pages URL이 포함된 새 댓글이 표시되며, 거기서 **Octomatch**를 플레이할 수 있습니다!
1. `CI` 워크플로우가 완료되면 Mona가 진행 상황을 확인하고 리뷰를 제공합니다!
