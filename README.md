# 재사용 가능한 워크플로우 만들기

_재사용 가능한 GitHub Actions 워크플로우를 만들고, 다른 워크플로우에서 호출하는 방법을 배웁니다._

## 환영합니다

- **대상**: GitHub Actions 파이프라인에서 중복을 줄이고 싶은 개발자 및 관리자.
- **배울 내용**: 재사용 가능한 워크플로우를 정의하고 호출하는 방법, 그리고 호출자와 중첩된 재사용 가능한 워크플로우 간 권한이 어떻게 흐르는지.
- **만들 것**: 재사용 가능한 품질 검사를 실행하고, GitHub Pages에 배포하며, Pull Request에 배포 정보를 댓글로 남기는 CI 설정.
- **사전 요구 사항**:
  - GitHub Actions 워크플로우에 대한 기본적인 이해
  - 추천 GitHub Skills 실습:
    - [Hello GitHub Actions](https://github.com/skills-kr/hello-github-actions)
    - [Actions로 테스트하기](https://github.com/skills-kr/test-with-actions/tree/main/src)

- **소요 시간**: 이 실습은 1시간 이내로 완료할 수 있습니다.

이 실습에서는 다음을 수행합니다:

1. 재사용 가능한 워크플로우를 처음부터 만들고 다른 워크플로우에서 사용합니다.
1. 호출자와 재사용 가능한 워크플로우 간 권한이 어떻게 작동하는지 깊이 이해합니다.
1. CI 워크플로우를 확장하여 GitHub Pages에 배포하고 Pull Request에 배포 정보를 댓글로 남깁니다.

### 이 실습을 시작하는 방법

아래 버튼을 클릭하여 실습을 여러분의 계정으로 복사한 다음, 좋아하는 Octocat(Mona)에게 **약 20초** 정도 첫 번째 레슨을 준비할 시간을 주고 **페이지를 새로고침**하세요.

[![](https://img.shields.io/badge/실습%20복사-%E2%86%92-1f883d?style=for-the-badge&logo=github&labelColor=197935)](https://github.com/new?template_owner=skills-kr&template_name=reusable-workflows&owner=%40me&name=skills-reusable-workflows&description=실습:+재사용+가능한+워크플로우+만들기&visibility=public)

<details>
<summary>문제가 있나요? 🤷</summary><br/>

실습을 복사할 때, 다음 설정을 권장합니다:

- 소유자(owner)는 개인 계정 또는 조직(organization)을 선택하세요.

- Public 저장소로 만드는 것을 권장합니다. Private 저장소는 Actions 사용 시간이 차감됩니다.

20초 후에도 실습이 준비되지 않으면 [Actions](../../actions) 탭을 확인하세요.

- 작업이 실행 중인지 확인하세요. 때로는 조금 더 오래 걸릴 수 있습니다.

- 실패한 작업이 표시되면 이슈를 제출해 주세요. 버그를 찾으셨네요! 🐛

</details>

> **참고**: 이 실습은 [skills/reusable-workflows](https://github.com/skills/reusable-workflows)를 기반으로 한글화하고, [🏆 GitHub Skills Workshop Dashboard](https://github-skills.studydev.com)와 연계되어 있습니다.
