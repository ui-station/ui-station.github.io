---
title: "GitLab에서 Docker를 사용하여 Playwright 테스트 실행하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-RunningPlaywrighttestswithDockeronGitLab_0.png"
date: 2024-06-23 00:38
ogImage: 
  url: /assets/img/2024-06-23-RunningPlaywrighttestswithDockeronGitLab_0.png
tag: Tech
originalTitle: "Running Playwright tests with Docker on GitLab"
link: "https://medium.com/@OnurDenizhan/running-playwright-tests-with-docker-on-gitlab-585ff45f734b"
---


![이미지](/assets/img/2024-06-23-RunningPlaywrighttestswithDockeronGitLab_0.png)

안녕하세요 여러분, 이 글에서는 도커를 사용하여 Playwright를 설치하고 GitLab에서 실행하는 방법을 설명하겠습니다. Trendyol 프로젝트의 설치 과정 중에 직면한 일부 어려움들도 함께 공유할 것입니다.

시작하기 전에 Playwright에 대한 기본 정보와 프로젝트 설치 단계에 대한 기본 사항은 생략할 예정이니 참고해 주세요.

# 문제점

<div class="content-ad"></div>

시작하기 전에, 왜 우리가 도커 이미지에서 테스트를 실행하는지 설명하는 것이 중요합니다. 저희 프로젝트에서는 브랜치별 테스팅을 진행하고 있는데요, 그것은 스프린트 동안 개발 중인 기능 브랜치에 대해 테스트를 실행한다는 것을 의미합니다. 이 기능 브랜치는 테스트 프로젝트 이미지와 함께 풀되어 도커 컨테이너에서 실행되며, 그 후 해당 실행 애플리케이션에 대해 테스트를 실행합니다. 아래에 프로세스를 설명하는 스크린샷과 플로우차트를 포함하였습니다.

![이미지 링크](/assets/img/2024-06-23-RunningPlaywrighttestswithDockeronGitLab_1.png)

위의 이미지에서 보듯이, 테스트 자동화 단계에서 개발된 브랜치를 풀고, 개발 중인 격리 환경에서 해당 애플리케이션을 실행합니다. 그런 다음 실행 중인 애플리케이션에 대해 테스트를 수행합니다. 이 새로운 개발이 아무 문제를 발생시키지 않았다고 확신을 갖게 되면, 병합 요청을 올리는 과정으로 나아갑니다.

![이미지 링크](/assets/img/2024-06-23-RunningPlaywrighttestswithDockeronGitLab_2.png)

<div class="content-ad"></div>

# 설치

처음에 언급했듯이 설치 과정은 건너뜁니다. 도움이 필요하면 이 문서를 참조해 주세요. Playwright를 설치한 후에는 프로젝트가 다음과 같이 보일 것입니다.

<img src="/assets/img/2024-06-23-RunningPlaywrighttestswithDockeronGitLab_3.png" />

로컬에서 테스트를 실행하려면 컴퓨터에 Docker를 설치해야 합니다. 이 링크에서 설치할 수 있습니다.

<div class="content-ad"></div>

# 프로젝트에 Docker 추가하기

이를 실현하기 위해서는 프로젝트에 Dockerfile을 포함시키고 그 단계들을 구성해야 합니다.

![Docker 이미지](/assets/img/2024-06-23-RunningPlaywrighttestswithDockeronGitLab_4.png)

```js
FROM mcr.microsoft.com/playwright:v1.44.0-jammy

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

CMD ["npx", "playwright", "test"]
```

<div class="content-ad"></div>

이 기사의 대상은 Docker에 대해 처음 접하는 사람부터 Docker 전문가까지 다양합니다. 모두가 명확히 이해할 수 있도록 한 줄씩 설명하겠습니다.

- 먼저, 우리는 테스트를 실행하는 데 필요한 모든 것을 포함하는 공식 Playwright 이미지를 가져옵니다.
- 프로젝트를 설치하기 위해 컨테이너 내부에 디렉토리를 만듭니다.
- 작업 디렉토리에 package.json 및 package-lock.json 파일을 복사하여 프로젝트를 올바르게 초기화합니다.
- 필요한 파일을 복사한 후, "npm ci"를 실행하여 프로젝트를 컨테이너에 설치합니다.
- 프로젝트를 설치한 후에는 테스트 파일, 구성 파일 등 추가 파일을 컨테이너에 복사합니다.
- 마지막 단계는 Playwright 테스트를 실행하는 명령을 실행하는 것입니다.

참고: Docker에서 npm 패키지를 사용할 때 다른 예제에서 발견할 수 있는 것과 달리 "npm install" 대신 "npm ci"를 사용하는 것이 권장됩니다. 일부 불필요한 단계를 건너뛸 수 있기 때문입니다.

# 로컬에서 테스트 실행

<div class="content-ad"></div>

로컬에서 테스트하려면 먼저 Docker를 시작하고 프로젝트 루트에 생성한 도커 파일을 빌드해야 합니다. VS Code 터미널을 사용하여 다음 명령을 사용하여 도커 파일을 빌드합니다. "docker build -t playwright-demo ."

![이미지](/assets/img/2024-06-23-RunningPlaywrighttestswithDockeronGitLab_5.png)

참고: Playwright의 퀵스타트 폴더 "tests-examples"를 제거했습니다. 불필요한 테스트 예제 실행을 피하기 위함입니다.

이미지가 성공적으로 빌드되면 "docker run -it — rm playwright-demo"으로 실행 준비가 완료됩니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-23-RunningPlaywrighttestswithDockeronGitLab_6.png" />

프로젝트에 테스트를 추가하여 프로세스를 가속화했습니다. 지금 확인해보면 테스트 실행이 완료된 것을 확인할 수 있습니다.

# Gitlab-ci.yml 구성

프로젝트에 gitlab-ci.yml 파일을 추가하고 이를 구성하여 파이프라인에서 테스트를 실행해봅시다.

<div class="content-ad"></div>

```YAML
기본값:
  이미지: docker:24.0.5
  서비스:
    - docker:24.0.5-dind

변수:
  DOCKER_HOST: tcp://docker:2376
  DOCKER_TLS_CERTDIR: "/certs"
  DOCKER_DRIVER: overlay2
  DOCKER_BUILDKIT: '1'
  FF_USE_FASTZIP: "true"
  ARTIFACT_COMPRESSION_LEVEL: "fast"
  CACHE_COMPRESSION_LEVEL: "fast"

단계:
  - 빌드
  - 테스트

빌드:
  stage: 빌드
  before_script:
    - echo "$CI_REGISTRY_PASSWORD" | docker login $CI_REGISTRY -u $CI_REGISTRY_USER --password-stdin
  script:
    - docker pull $CI_REGISTRY_IMAGE:latest || true
    - docker build 
      --cache-from $CI_REGISTRY_IMAGE:latest
      --tag $CI_REGISTRY_IMAGE:latest 
      --build-arg BUILDKIT_INLINE_CACHE=1 
      "."
    - docker push $CI_REGISTRY_IMAGE:latest

테스트:
  stage: 테스트
  before_script:
    - echo "$CI_REGISTRY_PASSWORD" | docker login $CI_REGISTRY -u $CI_REGISTRY_USER --password-stdin
    - mkdir playwright-report
    - mkdir test-results
  script:
    - docker run --ipc=host --name playwright-tests $CI_REGISTRY_IMAGE:latest
  after_script:
    - docker cp playwright-tests:app/playwright-report/. playwright-report
    - docker cp playwright-tests:app/test-results/. test-results
  allow_failure: false
  artifacts:
    when: 항상
    paths:
      - playwright-report
      - test-results
    expire_in: 1 주
``` 

우리는 Docker 이미지를 정의합니다. 이 섹션 끝에서 몇 가지 중요한 사항을 주목해야 합니다.

변수 섹션에서는 주로 Docker 및 그 캐싱 메커니즘과 관련된 변수를 정의하여 이미지를 가져오는 시간을 단축하는 데 도움이 됩니다.

파이프라인의 초기 단계에서는 "before_script" 섹션 내에서 docker에 로그인하여 이미지를 가져옵니다. Gitlab 이미지 레지스트리에 이미지가 있는 경우에는 캐시에서 가져옵니다. 그렇지 않으면 로컬로 빌드한 다음 미래 빌드를 가속화하기 위해 이미지 레지스트리에 푸시합니다.


<div class="content-ad"></div>

테스트 단계에서는 Docker에 로그인하고 우리의 테스트 결과를 Docker 이미지에서 Gitlab 아티팩트로 전송하기 위해 두 개의 폴더를 생성합니다. 스크립트 섹션에서는 우리의 이미지를 실행하고, 마지막으로 아티팩트 섹션에서 테스트 폴더의 경로를 지정합니다.

중요 참고 사항: 프로젝트 설정 중에 Docker 버전과 Node.js가 충돌하는 특이한 문제가 발생했습니다. 이전 버전의 Docker를 사용하면 Playwright를 실행할 때 예기치 않은 오류가 발생할 수 있습니다. 따라서 프로젝트를 초기화할 때 최신 버전의 Docker 및 Playwright 이미지를 사용하는 것이 권장됩니다.

# Gitlab 파이프라인에서 테스트 실행

변경 사항을 GitLab에 푸시하면 파이프라인에서 이와 유사한 내용이 표시됩니다.

<div class="content-ad"></div>


<img src="/assets/img/2024-06-23-RunningPlaywrighttestswithDockeronGitLab_7.png" />

실행된 파이프라인에서 테스트 단계를 클릭하면 결과에서 비슷한 로그를 볼 수 있습니다.

<img src="/assets/img/2024-06-23-RunningPlaywrighttestswithDockeronGitLab_8.png" />

# 보고서에 접근하기


<div class="content-ad"></div>

보고서에 액세스하려면 페이지 우측 하단에 있는 "찾아보기" 버튼을 클릭하여 프로젝트 자료를 확인하세요.

![이미지](/assets/img/2024-06-23-RunningPlaywrighttestswithDockeronGitLab_9.png)

자료에서 "playwright-report" 하위에 있는 HTML 보고서를 찾을 수 있습니다.

![이미지](/assets/img/2024-06-23-RunningPlaywrighttestswithDockeronGitLab_10.png)

<div class="content-ad"></div>

# 결론

필요하다면 여기서 GitLab 저장소를 찾을 수 있어요.

읽어 주셔서 감사합니다! 이 글을 가능한 한 간단하게 유지하기 위해 노력했어요. 유용하게 찾으셨으면 좋겠어요. 자유롭게 댓글을 남겨주세요. 다음 글에서 만나요! 👋