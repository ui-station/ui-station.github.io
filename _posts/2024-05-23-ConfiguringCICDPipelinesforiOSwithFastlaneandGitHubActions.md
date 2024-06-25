---
title: "iOS용 Fastlane과 GitHub Actions를 사용하여 CICD 파이프라인 구성하기"
description: ""
coverImage: "/assets/img/2024-05-23-ConfiguringCICDPipelinesforiOSwithFastlaneandGitHubActions_0.png"
date: 2024-05-23 14:50
ogImage:
  url: /assets/img/2024-05-23-ConfiguringCICDPipelinesforiOSwithFastlaneandGitHubActions_0.png
tag: Tech
originalTitle: "Configuring CI CD Pipelines for iOS with Fastlane and GitHub Actions"
link: "https://medium.com/@hafizegungor/configuring-ci-cd-pipelines-for-ios-with-fastlane-and-github-actions-07efd3a837af"
---

현재 소프트웨어 개발에서 필수적인 실천 방법인 지속적 통합 및 지속적 배포 (CI/CD)는 애플리케이션의 테스트, 빌드, 그리고 배포 과정을 자동화합니다. iOS 개발자들에게는 GitHub Actions와 Fastlane을 활용하여 이러한 방식을 효율적으로 구현할 수 있습니다.

CI/CD 파이프라인은 코드 품질을 유지하고 신속하고 신뢰할 수 있는 소프트웨어 제공을 보장하는 데 중요합니다. CI는 코드 변경 사항을 자동으로 테스트하여 문제를 조기에 감지하는 반면, CD는 코드가 빠르고 안전하게 프로덕션 환경에 배포될 수 있도록 합니다. CI/CD를 도입하면 개발자들이 수동 테스트 및 배포에 소비하는 시간을 크게 줄일 수 있어 코드 작성에 집중할 수 있습니다.

iOS 개발에서 GitHub Actions와 Fastlane은 서로 완벽하게 보완적입니다:

- GitHub Actions: GitHub에 직접 통합되어 있는 유연하고 확장 가능한 CI/CD 서비스로, 워크플로우를 자동화할 수 있습니다.
- Fastlane: iOS 및 Android 앱을 빌드, 테스트 및 릴리스 자동화하는 오픈 소스 플랫폼으로, 복잡한 작업의 설정 및 관리를 간편화합니다.

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

일련의 테이블 태그를 Markdown 형식으로 변경해보겠습니다.

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

- 개발 프로세스는 새로운 기능을 작업하거나 버그를 수정하는 개발자로부터 시작됩니다. 이 작업은 주요 개발 브랜치에서 파생된 별도 브랜치에서 수행됩니다.

2. 개발자가 풀 리퀘스트(Pull Request)를 생성합니다:

- 개발자가 변경 사항을 완료하면, 그들은 자신의 브랜치를 주요 개발 브랜치에 병합하기 위해 풀 리퀘스트(PR)를 만듭니다.

3. CI가 단위 테스트를 실행합니다:

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

- 풀 리퀘스트를 생성하면 CI (Continuous Integration) 시스템이 자동으로 유닛 테스트를 실행하게 됩니다. 이 단계는 새 코드가 회귀를 발생시키지 않거나 기존 기능을 파괴하지 않았는지를 확인합니다.

팀원들은 코드 변경 사항을 검토합니다:

- CI 시스템이 테스트를 실행하는 동안, 팀원들은 코드 변경 사항을 검토합니다. 코드 품질, 코딩 표준 준수 여부, 그리고 구현의 정확성을 확인합니다.

만약 코드가 팀에서 좋게 보이고 유닛 테스트가 통과하면, 개발자가 풀 리퀘스트를 병합합니다:

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

- 유닛 테스트가 통과하고 코드 리뷰가 만족스러운 경우, 개발자가 풀 리퀘스트를 메인 개발 브랜치에 병합합니다.

6. 풀 리퀘스트가 병합되면 CI가 빌드를 생성하여 TestFlight로 전송합니다:

- 풀 리퀘스트가 병합되면 CI 시스템이 빌드 프로세스를 트리거합니다. 이 빌드는 자동으로 TestFlight로 업로드되며, TestFlight는 iOS 베타 앱을 테스터에게 배포하는 데 사용되는 플랫폼입니다.

이 워크플로우는 새로운 코드 변경사항이 본 브랜치에 통합되기 전에 철저히 테스트되고 리뷰되도록 보장합니다. CI/CD의 사용은 테스트 및 배포 프로세스를 자동화하여 효율성을 향상시키고 프로덕션 환경에 오류를 도입할 가능성을 줄입니다.

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

# 전제 조건

CI/CD 파이프라인을 설정하기 전에 다음 사항을 확인해주세요:

- iOS 프로젝트용 GitHub 저장소
- Apple 개발자 계정
- 로컬 머신에 Xcode 설치

# CI/CD 설정 단계별 안내

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

# 1. 초기 설정

먼저, 귀하의 저장소를 복제하고 로컬로 이동하세요:

```js
git clone https://github.com/yourusername/your-ios-repo.git
cd your-ios-repo
```

# 2. Fastlane 설치

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

Fastlane을 RubyGems를 이용하여 설치해보세요:

```js
sudo gem install fastlane -NV
```

다음으로, 프로젝트 디렉토리로 이동하고 Fastlane을 초기화해보세요:

```js
cd your-ios-repo
fastlane init
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

프로젝트용 Fastlane을 설정하는 방법을 따라하세요. 베타 배포와 테스팅을 자동화하는 옵션을 선택하세요.

# 3. Fastlane 설정하기

프로젝트의 fastlane 디렉토리에 Fastlane에 의해 생성된 Fastfile을 열어보세요. 앱을 빌드하고 테스트하는 레인을 정의하세요. 예를 들면:

```js
default_platform(:ios)

platform :ios do
  desc "모든 테스트 실행"
  lane :test do
    scan
  end

  desc "베타 배포용 앱 빌드"
  lane :beta do
    build_app
    upload_to_testflight
  end
end
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

# 4. GitHub Actions 설정하기

프로젝트 루트에 .github/workflows 디렉토리를 생성해주세요. 만약 없다면, 새로 만들어주세요. 이 디렉토리 안에 ci.yml이라는 파일을 만들어주세요:

```yaml
name: CI

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: macos-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Ruby
        uses: actions/setup-ruby@v1
        with:
          ruby-version: 2.7

      - name: Install Fastlane
        run: gem install fastlane

      - name: Install dependencies
        run: bundle install

      - name: Run tests
        run: fastlane test
```

이 설정은 main 브랜치로의 모든 push 및 pull request에서 workflow를 트리거합니다. macOS 환경을 설정하고, Ruby와 Fastlane을 설치한 후 Fastlane 구성에서 정의된 테스트를 실행합니다.

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

위의 내용을 한국어로 번역하겠습니다.

- on: 이 키는 workflow를 트리거하는 이벤트를 정의합니다.
- push: 지정된 브랜치에 푸시가 발생할 때마다 workflow가 실행되어야 함을 나타냅니다.
- branches: 푸시 이벤트를 트리거하는 브랜치를 나열합니다. 여기에서는 주 브랜치로 설정되어 있습니다.
- pull_request: 지정된 브랜치를 대상으로 하는 pull request에 대해 workflow가 실행되어야 함을 나타냅니다. 다시 말해, 주 브랜치입니다.
- jobs: workflow를 구성하는 작업을 정의합니다. 이 예시에서는 빌드라는 단일 작업이 있습니다.
- build: 이것은 작업의 이름입니다.
- runs-on: 작업을 실행할 기계의 유형을 지정합니다. macos-latest은 GitHub의 호스팅된 러너에서 사용 가능한 macOS의 최신 버전에서 작업이 실행됨을 나타냅니다.
- steps: 작업을 구성하는 개별 단계를 정의합니다. 각 단계는 단일 작업을 실행합니다.
- name: 단계를 설명합니다. 가독성을 위해 단계가 무엇을 하는지 식별하는 데 도움이 됩니다.
- uses: GitHub Actions marketplace에서 사용할 액션을 지정합니다. actions/checkout@v2는 저장소 코드를 확인하여 이후 단계에서 액세스할 수 있게 하는 동작입니다.
- uses: GitHub Actions marketplace의 다른 동작입니다. actions/setup-ruby@v1은 Ruby 환경을 설정합니다.
- with: 동작에 대한 추가 매개변수를 지정합니다.
- ruby-version: 사용할 Ruby 버전을 정의합니다. 여기에서는 2.7입니다.
- run: 셸 명령을 실행합니다. 이 단계에서는 Fastlane이라는 iOS 및 Android 배포를 자동화하는 도구를 설치하기 위해 gem install fastlane 명령을 실행합니다.
- run: 이 단계에서는 bundle install을 실행하여 Gemfile에서 지정된 종속성을 설치합니다. 모든 필요한 gem이 설치됨을 보장합니다.
- run: 이 단계에서는 Fastfile에서 정의된 Fastlane 테스트 라인을 실행합니다. 일반적으로 iOS 프로젝트의 단위 테스트를 실행하는 것을 포함합니다.

이 workflow의 각 단계는 iOS 프로젝트의 CI 프로세스를 자동화하기 위해 설계되었습니다:

- 코드 가져오기: 저장소의 코드를 검색합니다.
- Ruby 설정: Ruby 환경을 준비합니다.
- Fastlane 설치: Fastlane 도구를 설치합니다.
- 종속성 설치: Bundler를 사용하여 필요한 프로젝트 종속성을 설치합니다.
- 테스트 실행: 코드가 올바르게 작동하는지 확인하기 위해 Fastlane에 정의된 테스트를 실행합니다.

# 5. 보안 관리

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

앱을 배포하려면 API 키 및 인증서와 같은 민감한 정보를 안전하게 저장해야 합니다. GitHub 저장소에서 Settings > Secrets 메뉴로 이동하여 필요한 시크릿을 추가하세요:

- APP_STORE_CONNECT_API_KEY
- MATCH_PASSWORD
- FASTLANE_SESSION

자세한 지시 사항은 Fastlane 문서에서 시크릿 및 API 키를 관리하는 방법을 참조하세요.

# 6. 워크플로우 실행하기

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

깃허브에 변경 사항을 푸시하세요. 이 작업은 ci.yml에서 정의한 워크플로를 트리거합니다. 깃허브 저장소의 Actions 탭에서 워크플로 진행 상황을 확인할 수 있습니다.

# 결론

GitHub Actions와 Fastlane을 사용하여 iOS 개발을 위한 CI/CD 파이프라인을 설정하면 자동화된 테스트, 빌드 및 배포를 제공하여 개발 워크플로를 크게 향상시킬 수 있습니다. 위에서 설명한 단계는 이러한 설정이 신속하게 구현될 수 있음을 보여주며, 코드 품질을 유지하고 릴리스 프로세스를 가속화할 수 있도록 도와줍니다. 이러한 도구를 활용하여 견고하고 효율적인 개발 주기를 보장함으로써 팀이 훌륭한 앱을 만들 수 있도록 돕는 것입니다.

지속적 통합 및 배포가 구현되면 문제를 빨리 파악하고 코드 품질을 보장하며 기능을 더 빠르고 신뢰할 수 있게 사용자에게 제공할 수 있습니다. 이는 현대 소프트웨어 개발의 최상의 관행을 구현하는 것입니다.

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

## 축하드려요! 🥳

![이미지](/assets/img/2024-05-23-ConfiguringCICDPipelinesforiOSwithFastlaneandGitHubActions_1.png)
