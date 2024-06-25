---
title: "의존성 관리자 Dependabot GitHub 및 Terraform 버전 관리"
description: ""
coverImage: "/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_0.png"
date: 2024-06-19 13:30
ogImage:
  url: /assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_0.png
tag: Tech
originalTitle: "Dependabot: GitHub, and Terraform versions management"
link: "https://medium.com/itnext/dependabot-github-and-terraform-versions-management-29b1bde6baa9"
---

![image](/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_0.png)

프로젝트가 성장함에 따라 언젠가는 패키지, 모듈 및 차트 버전을 업그레이드해야 할 필요성이 생길 것입니다.

물론 수동으로 할 수도 있지만, 어느 정도까지만 가능합니다. 결국에는 물리적으로 모든 업데이트 사항을 추적하고 업데이트하는 것이 불가능해질 수 있습니다.

이와 같은 프로세스를 자동화하는 다양한 솔루션이 있지만, 가장 일반적으로 사용되는 것은 Renovate와 Dependabot입니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

우크라옵스 슬랙 투표 결과에 따르면, Renovate가 훨씬 많은 투표를 받았으며 실제로 Dependabot보다 더 많은 작업을 수행할 수 있습니다.

반면, Dependabot는 이미 GitHub 저장소에서 사용 가능하며 모든 가격 요금제에서 이용 가능합니다. 그러니까, GitHub를 사용하는 경우, Dependabot을 설정하려면 구성 파일을 추가하기만 하면 됩니다. 앞으로 봤을 때, Renovate를 설정하는 것이 더 쉬우나, 다음 게시물에서 더 자세히 다루겠습니다 — Renovate: GitHub 및 Helm 차트 버전 관리.

실제로, Dependabot를 거의 모든 플랫폼에서 사용할 수 있습니다 — GitHub, Github 엔터프라이즈, Azure DevOps, GitLab, BitBucket 및 AWS CodeCommit 등. Dependabot를 실행하는 방법을 확인하려면 How to run Dependabot을 참조하세요.

그러나 — 이게 제게는 큰 놀램이었습니다 — Dependabot은 Helm 차트와 함께 작동하지 않습니다. 그러나 Terraform에서 작동하며 이미 일부 파이썬 코드 저장소에서 사용 가능합니다. 그러니 먼저 그것에 대해 살펴보겠습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

다시 미래를 내다보면, 저는 Renovate를 훨씬 더 좋아했고, 이후에는 Renovate를 사용할 것입니다.

# Dependabot가 작동하는 방식

다음은 그 방식입니다:

- 저장소에 Dependabot 구성 파일을 만듭니다.
- 파일 내에서 무엇을 정확히 확인해야 하는지 설명합니다 — pip 라이브러리, Terraform 모듈 등을
- 특히 관심 있는 것을 설명합니다 — 보안 업데이트 또는 버전 업데이트
- 업데이트를 발견하면 — Dependabot은 Pull Request를 만들어 해당 업데이트에 대한 상세 정보를 추가합니다
- …
- 수익 창출!

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

오늘은 무엇을 할까요?

- 우리는 모니터링을 위한 GitHub 저장소를 갖고 있어요
- 거기에 Terraform 코드가 있고
- Dependabot을 사용하여 버전 확인 및 PR 생성을 구성할 거예요

문서 — Dependabot Quick Start Guide, dependabot.yml 파일 구성 옵션.

지원되는 저장소 및 생태계 — Dependabot이 지원하는 시스템을 확인하세요.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# Dependabot과 Terraform

Terraform의 맥락에서 Dependabot으로 모니터링할 수 있는 것은 프로바이더 및 모듈의 버전입니다.

예를 들어, 제공자 버전이 설정된 versions.tf와 여러 모듈을 사용하는 lambda.tf와 같은 두 파일이 있습니다. 모듈에는 terraform-aws-modules/security-group/aws, terraform-aws-modules/lambda/aws 등이 있습니다:

![이미지](/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_1.png)

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

이제 Dependabot가 이들에서 버전을 모니터링하도록 시작하려면 디렉토리 .github를 생성하고 그 안에 dependabot.yml 파일을 만듭니다:

![이미지](/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_2.png)

파일에 다음과 같이 매개변수를 설정합니다:

```js
version: 2
updates:
  - package-ecosystem: "terraform"
    directory: "/terraform"
    schedule:
      interval: "daily"
      time: "09:00"
      timezone: "Europe/Kyiv"
    assignees:
      - arseny-zinchenko
    reviewers:
      - arseny-zinchenko
    open-pull-requests-limit: 10
```

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

보편적으로 매개변수 이름에서 모든 것이 명확해요:

- package-ecosystem: 이 설정이 Terraform을 위한 것이므로, 우리는 이것을 지정합니다
- directory: Terraform 파일은 리포지토리 루트의 terraform 디렉토리에 있습니다
- schedule: 체크 일정 - 먼저 dependabot.yml 파일을 추가하면 체크가 즉시 시작되며, 나중에 수동으로 실행할 수 있습니다
- assignees and reviewers: 나를 위해 즉시 PR을 만들어주세요
- open-pull-requests-limit: 기본적으로 Dependabot은 최대 5개의 PR을 엽니다. 이 매개변수로 증가시킬 수 있습니다

리포지토리에 푸쉬하고 상태를 확인해보세요.

리포지토리에서 Insights `Dependency graph` Dependabot로 이동하여 체크가 시작되었음을 확인하세요:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

![image](/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_3.png)

In a minute, we’ll have open Pull Requests:

![image](/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_4.png)

![image](/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_5.png)

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

동시에, Dependabot은 업데이트에 대한 세부 정보를 주석으로 추가합니다 — 릴리스 노트, 변경 내역 등:

![이미지](/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_6.png)

그러나 이 모든 것이 어디서는 안 될 수도 있습니다.

예를 들어, Lambda 모듈의 업데이트는 세부 정보 없이 생성되었습니다:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

![image](/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_7.png)

But Renovate does it much better.

# Dependabot, and GitHub Secrets

Another nuance is the GitHub Secrets that are available to Dependabot.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

프로젝트의 terraform 디렉토리에 변경 내용이 있는 PR이 올라오면, 우리는 GitHub Actions Workflow를 실행합니다. 해당 Workflow에서는 Terraform의 체크를 수행합니다 (GitHub Actions: Terraform deployments with a review of planned changes 참조).

이 Workflow는 전용 저장소에 위치해 있으며, 접근하기 위해 GitHub 배포용 키가 GitHub Actions Secrets를 통해 호출하는 Workflow로 전달됩니다.

그러나 Dependabot에서 시작된 GitHub Actions 작업에서 다음 단계가 실패했습니다:

![Dependabot Error](/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_8.png)

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

워크플로 자체는 모든 비밀을 secrets: inherit:를 통해 전달합니다.

```js
...
jobs:
  terraform-test:
    # Reusable Workflow 파일 호출
    uses: ORG_NAME/atlas-github-actions/.github/workflows/call-terraform-check-and-plan.yml@master
    with:
      aws-iam-role: ${ vars.AWS_IAM_ROLE }
      aws-env: ${ vars.AWS_ENV }
      pr-num: ${ github.event.pull_request.number }
      environment: ops
      slack-channel: '#cicd-devops'
    secrets:
      inherit
```

그러나 Dependabot에서는 이러한 비밀 정보를 Actions의 secrets 및 변수가 아닌 Actions의 secrets와 변수인 Dependabot에 별도로 설정해야 합니다:

<img src="/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_9.png" />

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

새로운 비밀을 추가했고, 이제 확인이 작동합니다:

![Dependabot, and private registries/repositories](/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_10.png)

# Dependabot 및 개인 레지스트리/저장소

다른 것들 중에, 우리는 개인 저장소에 저장된 자체 Terraform 모듈을 가지고 있어요.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

의존성 보트에 액세스할 때 "의존성 보트가 ORG_NAME/atlas-tf-modules에 액세스할 수 없습니다"라는 오류가 발생합니다:

![image](/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_11.png)

첫 번째 옵션은 dependabot.yml 파일에서이 저장소 또는 다른 레지스트리를 명시적으로 추가하는 것입니다. - 개인 레지스트리 구성 항목 참조.

두 번째 옵션은 단순히 '액세스 권한 부여'를 클릭하는 것입니다. 이렇게 하면 조직의 모든 저장소에 대해 해당 저장소의 액세스가 열립니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

해당 테이블 태그를 마크다운 형식으로 변경하거나 수동으로 변경하세요. 조직 설정으로 이동한 후 `보안 코드` 및 `전역 설정`으로 이동하여 `사적 저장소에 Dependabot 액세스 권한 부여` 섹션에서 원하는 저장소에 대한 액세스를 추가하세요:

![Dependabot GitHub and Terraform versions management](/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_12.png)

# Dependabot 및 수동 실행

접근 권한을 추가했다면, 다시 저장소로 돌아가 Insights -> Dependency graph -> Dependabot으로 이동한 후 업데이트 확인을 클릭하세요:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

아래와 같이 작동 중입니다:

![Image 1](/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_13.png)

![Image 2](/assets/img/2024-06-19-DependabotGitHubandTerraformversionsmanagement_14.png)

일반적으로 그게 전부에요. 이제 우리는 모든 저장소를 직접 관리하지 않아도 Terraform 업데이트를 받게 될 거예요.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

한번 더 말해도, Renovate가 정말로 더 좋아. Renovate: GitHub을 보고, Helm Charts 버전 관리를 확인해봐.

원문은 RTFM: Linux, DevOps, 그리고 시스템 관리에서 공개되었습니다.
