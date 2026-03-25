## Step 2: 재사용 가능한 워크플로우 사용하기!

잘하셨습니다! 재사용 가능한 워크플로우를 만들었고 이제 사용할 준비가 되었습니다 🚀

이 단계에서는 Pull Request에서 실행되는 호출자 워크플로우를 만들고, 품질 검사를 해당 재사용 가능한 워크플로우에 위임합니다.

### 📖 이론: 재사용 가능한 워크플로우를 어떻게 사용하나요?

재사용 가능한 워크플로우는 같은 저장소에서 호출할 수 있고(이 실습에서 할 것처럼), 다른 저장소에서도 호출하여 팀 간 표준을 공유할 수 있습니다.

| 재사용 가능한 워크플로우 위치 | 참조 형식 | 일반적인 사용 사례 |
| -------------------------- | ---------------- | ---------------- |
| 같은 저장소            | `./.github/workflows/reusable-node-quality.yml` | 같은 저장소의 여러 워크플로우에서 로직 재사용 |
| 다른 저장소       | `owner/repo/.github/workflows/workflow.yml@v1` | 조직의 많은 저장소에서 표준 공유 |


> [!IMPORTANT]
> 다른 저장소의 재사용 가능한 워크플로우를 사용할 때는 예측 가능한 동작과 보안을 위해 안정적인 태그나 커밋 SHA에 고정하세요.

### ⌨️ 활동: CI 워크플로우를 만들고 재사용 가능한 워크플로우 호출하기

Pull Request에서 실행되고 이전 단계에서 만든 재사용 가능한 워크플로우를 호출하는 CI 워크플로우를 만들어 봅시다.

1. Codespace에서 `.github/workflows` 디렉토리에 새 워크플로우 파일을 만드세요:

   ```text
   ci.yml
   ```

1. 해당 파일에 다음 워크플로우 내용을 추가하세요:

   ```yaml
   name: CI

   on:
     pull_request:
       branches:
         - main

   permissions:
     contents: read

   jobs:
     quality:
       uses: ./.github/workflows/reusable-node-quality.yml
       with:
         node-version: 24
   ```

   이 워크플로우는 `main`을 대상으로 하는 모든 Pull Request 변경 시 실행되며, 이전 단계에서 만든 재사용 가능한 워크플로우를 호출합니다.

   > ✨ 훨씬 깔끔하지 않나요? Node 품질 검사가 여러 저장소에서 표준화되어 있다면, 이 재사용 가능한 워크플로우를 모든 저장소에서 호출하고 한 곳에서 관리할 수 있습니다!

1. `ci.yml` 변경사항을 `reusable-workflows` 브랜치에 커밋하고 푸시하세요.

### ⌨️ 활동: 재사용 가능한 워크플로우가 동작하는 것을 확인하기

Pull Request를 열어서 워크플로우가 실행되는 것을 확인해 봅시다!

1. 다른 브라우저 탭에서 저장소의 [Pull requests](https://github.com/{{ full_repo_name}}/pulls) 섹션으로 이동하세요.

1. `reusable-workflows` 브랜치에서 `main`으로 새 Pull Request를 열어주세요.
   > ⚠️ **주의:** 이 Pull Request를 아직 머지하지 마세요. 다음 단계에서 같은 Pull Request에서 계속 작업할 것입니다.

1. 아래로 스크롤하면 **Checks** 섹션을 펼쳐야 할 수 있습니다. 그러면 CI 워크플로우가 세 개의 별도 작업을 실행하는 것을 볼 수 있습니다.

    <img width="600" alt="PR 체크가 실행되는 모습" src="https://raw.githubusercontent.com/skills-kr/reusable-workflows/main/.github/images/ci-checks-running.png" />

   > 💡 `Step ...`과 같은 이름의 추가 체크도 보일 것입니다. 이 체크들은 이 실습을 진행하는 데 사용되므로 걱정하지 않아도 됩니다. 해당 체크 중 하나가 실패하면 이 이슈로 돌아와서 Mona의 피드백을 확인하세요.

1. Pull Request가 열리면 Mona가 다음 단계를 준비하거나 피드백을 제공합니다!
