## Step 1: 재사용 가능한 워크플로우 만들기

여러분의 회사 Octogames는 계속해서 새로운 웹 게임을 출시하고 있습니다! DevOps 엔지니어인 여러분은 항상 CI/CD를 위한 GitHub Actions 워크플로우를 만드는 업무를 맡아왔습니다.

같은 워크플로우 로직을 여러 저장소에 복사-붙여넣기하고 있다는 것을 발견했습니다. 이 실습에서는 재사용 가능한 워크플로우를 만들어 **CI 로직을 중앙화**하고, **자동화를 표준화**하며, **여러 저장소에서 재사용**할 수 있도록 합니다.

<img width="900" alt="octomatch 게임을 보여주는 이미지" src="https://raw.githubusercontent.com/skills-kr/reusable-workflows/main/.github/images/octomatch.png" />

### 📖 이론: 워크플로우를 재사용 가능하게 만드는 것은 무엇인가?

GitHub Actions의 재사용 가능한 워크플로우는 다른 워크플로우에서 호출할 수 있는 워크플로우입니다. 이를 통해 반복되는 CI 로직을 한 곳에 유지하고 여러 저장소에 복사할 필요가 없습니다.

워크플로우는 `workflow_call` 트리거를 포함하면 재사용 가능해집니다. 재사용 가능한 워크플로우는 입력(inputs)을 받고 출력(outputs)을 반환할 수 있어, 같은 워크플로우를 다른 프로젝트와 컨텍스트에 맞게 조정할 수 있습니다.

이 단계에서는 Node.js 품질 검사를 위한 재사용 가능한 워크플로우를 만들고, 다음 단계에서 호출자 워크플로우에서 사용합니다.

### ⌨️ 활동: 개발 환경 설정하기

**GitHub Codespaces**를 사용하여 클라우드 기반 개발 환경을 설정하고 나머지 실습을 진행합시다!

1. 아래 버튼을 오른쪽 클릭하여 **Create Codespace** 페이지를 새 탭에서 열어주세요. 기본 설정을 사용하세요.

   [![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/{{full_repo_name}}?quickstart=1)

1. 여러분의 실습 복사본(`{{full_repo_name}}`)에 Codespace를 만들고 있는지 확인하세요.

1. Visual Studio Code가 브라우저에서 완전히 로드될 때까지 잠시 기다려주세요.

   > ⏳ **대기:** 최대 몇 분이 소요될 수 있습니다.

1. (선택) Codespace가 완전히 로드되면 다음 명령어를 실행하여 **Octomatch** 애플리케이션이 실행되는 것을 볼 수 있습니다:

   ```bash
   npm run dev
   ```

   > 🪧 **참고:** 이 실습이 끝나면 여러분이 만든 워크플로우에 의해 GitHub Pages에 배포된 이 게임을 다시 플레이할 수 있습니다!

### ⌨️ 활동: 재사용 가능한 워크플로우 만들기

회사의 Node.js 프로젝트를 위한 재사용 가능한 워크플로우를 만들어 봅시다. 이 워크플로우는 린팅, 커버리지 보고가 포함된 테스트, 그리고 [Playwright](https://playwright.dev/)를 사용한 E2E(엔드투엔드) 테스트를 실행합니다.

1. Codespace에서 정확히 `reusable-workflows`라는 이름으로 새 브랜치를 만들고 전환하세요.

   ```bash
   git checkout -b reusable-workflows
   ```

1. `.github/workflows` 디렉토리에 새 워크플로우 파일을 만드세요:

   ```text
   reusable-node-quality.yml
   ```

1. 해당 파일에서 워크플로우 이름, 이벤트 트리거, 권한을 정의합니다:

   ```yaml
   name: Reusable Node Quality
   run-name: Node {% raw %}${{ inputs.node-version }}{% endraw %} Quality Checks

   on:
     workflow_call:
       inputs:
         node-version:
           description: "Node.js version"
           required: false
           default: "20"
           type: string

   permissions:
     contents: read
   ```

   이 워크플로우는 같은 저장소 또는 다른 저장소의 워크플로우에서 호출할 수 있으며, 저장소 콘텐츠에 대한 읽기 전용 접근 권한만 필요합니다.

1. 이제 워크플로우에 세 개의 별도 작업을 추가합니다: `lint`, `tests`, `e2e`.

   이것들은 여러 저장소에서 가장 많이 복사-붙여넣기했던 작업이므로, 재사용 가능한 워크플로우로 중앙화하는 것이 합리적입니다.

   다음 내용을 이해하고 `reusable-node-quality.yml` 워크플로우 파일 하단에 추가하세요:

   ```yaml
   jobs:
     lint:
       name: Lint
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v6
         - uses: actions/setup-node@v6
           with:
             node-version: {% raw %}${{ inputs.node-version }}{% endraw %}
             cache: npm
         - run: npm ci
         - run: npm run lint

     tests:
       name: Tests with Coverage
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v6
         - uses: actions/setup-node@v6
           with:
             node-version: {% raw %}${{ inputs.node-version }}{% endraw %}
             cache: npm
         - run: npm ci
         - run: npm run test:coverage

     e2e:
       name: E2E Tests with Playwright
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v6
         - uses: actions/setup-node@v6
           with:
             node-version: {% raw %}${{ inputs.node-version }}{% endraw %}
             cache: npm
         - run: npm ci
         - run: npx playwright install --with-deps chromium
         - run: npm run test:e2e
   ```

   각 작업은 저장소 코드를 체크아웃하고, 워크플로우 입력에 지정된 버전으로 Node.js를 설정하고, 의존성을 설치하고, 적절한 npm 스크립트(`lint`, `test:coverage`, `test:e2e`)를 실행합니다.


1. `.github/workflows/reusable-node-quality.yml` 파일 변경사항을 `reusable-workflows` 브랜치에 커밋하고 푸시하세요.

   ```bash
   git add .github/workflows/reusable-node-quality.yml
   git commit -m "feat: create reusable node quality workflow"
   git push origin reusable-workflows
   ```

1. 변경사항을 푸시하면 Mona가 여러분의 작업을 확인하고 다음 단계를 준비합니다!

<details>
<summary>문제가 있나요? 🤷</summary><br/>

- 파일 경로가 정확히 `.github/workflows/reusable-node-quality.yml`인지 확인하세요.
- YAML 들여쓰기를 확인하세요. 터미널에서 다음 명령어를 실행하여 워크플로우 파일의 구문을 검증할 수 있습니다:

    ```bash
    actionlint .github/workflows/reusable-node-quality.yml
    ```

</details>
