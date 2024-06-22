---
title: "컨테이너 안에서 RStudio 실행하기"
description: ""
coverImage: "/assets/img/2024-05-18-RunningRStudioInsideaContainer_0.png"
date: 2024-05-18 16:35
ogImage: 
  url: /assets/img/2024-05-18-RunningRStudioInsideaContainer_0.png
tag: Tech
originalTitle: "Running RStudio Inside a Container"
link: "https://medium.com/towards-data-science/running-rstudio-inside-a-container-e9db5e809ff8"
---


## 로컬 RStudio 설정을 사용하여 컨테이너 내에 RStudio 서버를 설정하는 단계별 가이드

안녕하세요! 이 문서는 로컬 RStudio 설정을 사용하여 컨테이너 내에 RStudio 서버를 설정하는 단계별 가이드입니다. Rocker RStudio 이미지를 사용하여 도커 실행 명령어와 인수를 사용하여 커스터마이징하는 방법을 안내합니다.

이 튜토리얼을 완료하면 다음을 수행할 수 있을 것입니다:

- 컨테이너 내에 RStudio 서버 시작하기
- 로컬 폴더 마운트하기
- 로컬 RStudio 설정 복제하기 (색 테마, 코드 스니펫 등)
- 로컬 Renviron 설정 불러오기

도움이 되길 바라며, 시작해보세요!

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-RunningRStudioInsideaContainer_0.png" />

# 소개

RStudio는 R 프로그래밍 언어를 위한 기본 IDE입니다. VScode와 같은 일반적인 목적의 IDE와 달리, RStudio는 R 사용자 및 그들의 요구에 특별히 설계 및 구축되었습니다. 이것이 RStudio가 R 사용자들 사이에서 인기를 얻는 이유 중 하나입니다. 기본적으로 RStudio는 Docker를 지원하지 않습니다. 컨테이너 내에서 RStudio를 설정하고 실행하는 주요 방법은 RStudio 서버 버전을 사용하는 것입니다. 이것은 일부 사용자에게 진입 장벽이 될 수 있는 컨테이너 내에서 서버를 설치하고 설정해야 한다는 것을 의미합니다. 다행히 R 이미지의 주요 소스 인 Rocker 프로젝트는 RStudio 서버가 내장되어 있고 사용 준비가 된 이미지를 제공합니다.

이 튜토리얼에서는 Docker Hub에서 사용 가능한 Rocker RStudio 이미지를 사용할 것입니다.

<div class="content-ad"></div>

# 준비 사항

이 튜토리얼에 참여하고 아래 코드를 실행하려면 다음이 필요합니다:

- Docker Desktop (또는 대체품)
- Docker Hub 계정
- docker run 명령어의 기본적인 이해

# Rocker로 시작하기

<div class="content-ad"></div>

The Rocker Project은 내장된 R 이미지의 주요 허브입니다. base-r, tidyverse, ML-verse, shiny, 지리적 공간 등과 같이 서로 다른 R 환경 설정이 제공됩니다. 물론 RStudio 서버 이미지도 포함되어 있습니다. 사용 가능한 모든 R 이미지 목록은 Rocker의 Docker Hub 페이지에서 확인할 수 있습니다.

![image](/assets/img/2024-05-18-RunningRStudioInsideaContainer_1.png)

이번에는 rocker/rstudio 이미지를 사용할 것인데, 이미지 이름 그대로 RStudio 서버가 설치되어 사용할 준비가 되어 있습니다. docker run 명령을 사용하여 이 컨테이너를 대화형 모드로 실행하고 브라우저를 통해 RStudio 서버에 액세스할 수 있습니다.

이미지를 docker pull 명령으로 가져와 시작해보겠습니다:

<div class="content-ad"></div>

```js
>docker pull rocker/rstudio                                                                                                                                            확인
기본 태그 사용: latest
latest: rocker/rstudio에서 가져오는 중
a4a2c7a57ed8: 가져오기 완료
d0f9831967fe: 가져오기 완료
e78811385d51: 가져오기 완료
c61633a20287: 가져오기 완료
832cef14f2fb: 가져오기 완료
8395fbba6231: 가져오기 완료
fb53abdcfb34: 가져오기 완료
c942edef0d7f: 가져오기 완료
Digest: sha256:8e25784e1d29420effefae1f31e543c792d215d89ce717b0cc64fb18a77668f3
상태: rocker/rstudio:latest에 대한 새로운 이미지 다운로드 완료
docker.io/rocker/rstudio:latest
```

이미지가 성공적으로 다운로드되었는지 확인하려면 docker images 명령어를 사용할 수 있습니다:

```js
>docker images                                                                                                                                                    확인  36초
저장소          태그         이미지 ID      작성일         크기
rocker/rstudio  최신        7039fb162243   2일 전       1.94GB
```

이제 Rocker Project에서 제안한 명령어를 사용하여 docker run 명령어로 컨테이너 내에서 RStudio를 시작해 봅시다.

<div class="content-ad"></div>

```js
도커를 실행하고 RStudio 서버를 브라우저에서 열기 전에 위에서 사용한 실행 인수를 검토해보겠습니다:

- rm — 컨테이너 종료 시 자동으로 삭제합니다 (터미널에서 control + c를 누름)
- ti — 대화형 모드로 컨테이너를 실행합니다
- e — 환경 변수를 설정합니다. 이 경우 서버 로그인 암호를 'yourpassword'로 정의합니다
- p — 포트 매핑을 정의합니다. 이 경우 컨테이너의 8787포트를 로컬 머신의 8787포트와 매핑합니다

위 명령을 실행한 후, 로컬 호스트 8787번에서 RStudio 서버에 액세스할 수 있습니다 (예: http://localhost:8787). 이때 로그인 페이지가 나타나며, 여기서는 다음을 사용해야 합니다:
```

<div class="content-ad"></div>

- 사용자 이름: rstudio
- 비밀번호: yourpassword (run 명령에서 설정한 대로)

다음 출력을 기대해야 합니다:

<img src="/assets/img/2024-05-18-RunningRStudioInsideaContainer_2.png" />

# 이런! 일시적이네요!

<div class="content-ad"></div>

기본적으로 Docker 컨테이너는 일회성 모드에서 실행됩니다. 컨테이너에 생성하고 저장한 코드나 입력값은 컨테이너 실행 시간이 종료되면 손실됩니다. Docker를 개발 환경으로 사용하고 싶다면 이는 실용적이거나 유용하지 않습니다. 이 문제를 해결하기 위해 우리는 볼륨(v) 인수를 사용할 것이며, 이를 통해 로컬 폴더를 컨테이너 파일 시스템에 마운트할 수 있습니다.

아래 코드는 볼륨 인수를 사용하여 실행 명령을 실행하는 폴더(e.g., .)를 RStudio 서버 홈 폴더에 마운트하는 방법을 보여줍니다:

```js
docker run --rm -ti \
-v .:/home/rstudio \
-e PASSWORD=yourpassword \
-p 8787:8787 rocker/rstudio
```

이제 브라우저로 돌아가서 지역 호스트 주소인 http://localhost:8787을 사용하여 RStudio 서버를 다시 엽니다. RStudio 파일 섹션에는 마운트된 로컬 폴더에 있는 폴더나 파일을 볼 수 있을 것입니다. 제 경우, 튜토리얼 폴더를 마운트하겠습니다. 이 폴더에는 다음과 같은 폴더가 있습니다:

<div class="content-ad"></div>

```js
.
├── Introduction-to-Docker
├── awesome-ds-setting
├── forecast-poc
├── forecasting-at-scale
├── lang2sql
├── postgres-docker
├── python
├── rstudio-docker
├── sdsu-docker-workshop
├── shinylive-r
├── statistical-rethinking-2024
├── vscode-python
├── vscode-python-template
├── vscode-r
└── vscode-r-template
```

아래 스크린샷에서 볼 수 있듯이 RStudio 서버에서 로컬 폴더에 접근 가능합니다 (보라색 사각형으로 표시됨):

<img src="/assets/img/2024-05-18-RunningRStudioInsideaContainer_3.png" />

이를 통해 컨테이너에서 실행 시 로컬 폴더로 읽고 쓸 수 있게 되었습니다.

<div class="content-ad"></div>

# 로컬 RStudio 설정 복제하기

이전 섹션에서는 볼륨 인자를 사용하여 로컬 폴더를 컨테이너에 장착하는 방법을 보았습니다. 이를 통해 컨테이너 안에서 작업하면서 코드를 로컬로 저장할 수 있게 되었습니다. 이번 섹션에서는 우리가 컨테이너 내에 있는 것을 사용한 로컬 RStudio 설정을 적용하는 방법을 살펴보겠습니다. 여기서 아이디어는 컨테이너를 시작하고 로컬 설정으로 RStudio 서버를 실행하여 컨테이너를 다시 시작할 때마다 설정을 업데이트할 필요 없이 로컬 설정을 유지하는 것입니다. 이는 색 테마 설정, 코드 스니펫, 환경 변수 등과 같은 로컬 설정을 로딩하는 것을 포함합니다.

로컬 RStudio 구성 폴더를 가진 도커 실행을 업데이트하기 전에, 로컬과 컨테이너 내의 config 폴더의 경로를 식별해야 합니다. 예를 들어, 내 시스템의 경로는 ~/.config/rstudio이고 아래 폴더와 파일들을 포함합니다:

```js
.
├── dictionaries
│   └── custom
├── rstudio-prefs.json
└── snippets
    └── r.snippets
```

<div class="content-ad"></div>

컨테이너 내의 .config/rstudio 폴더도 /home/rstudio/ 아래에 있습니다. 따라서 다음 매핑을 사용할 것입니다:

```js
$HOME/.config/rstudio:/home/rstudio/.config/rstudio
```

또한, 로컬 환경 변수를 사용하기 위해 .Renviron 파일을 마운트하려고 합니다. .Renviron 파일은 로컬 머신의 루트 폴더에 있으며, 로컬 파일을 컨테이너 내의 파일로 매핑하기 위해 동일한 접근 방식을 따릅니다:

```js
$HOME/.Renviron:/home/rstudio/.Renviron
```

<div class="content-ad"></div>

이제 모두 함께 추가하고 컨테이너를 다시 시작해봅시다:

```js
docker run --rm -ti \
-v .:/home/rstudio \
-v $HOME/.config/rstudio:/home/rstudio/.config/rstudio \
-v $HOME/.Renviron:/home/rstudio/.Renviron \
-e PASSWORD=yourpassword \
-p 8787:8787 rocker/rstudio
```

로컬 RStudio 구성 폴더를 컨테이너의 폴더와 연결한 후에는 서버 설정이 이제 내 컴퓨터의 로컬 RStudio 설정과 일치되었습니다:

![Running RStudio Inside a Container](/assets/img/2024-05-18-RunningRStudioInsideaContainer_4.png)

<div class="content-ad"></div>

# 요약

이 튜토리얼은 Rocker의 RStudio 이미지를 사용자 정의하는 데 docker run 명령어를 사용하는 방법에 초점을 맞추고 있습니다. 우리는 볼륨 인수를 사용하여 로컬 폴더를 컨테이너의 작업 디렉토리에 마운트했습니다. 이를 통해 컨테이너 환경에서 작업하고 작업물을 로컬로 저장할 수 있게 되었습니다. 또한 우리는 볼륨 인수를 사용하여 로컬 RStudio 설정을 컨테이너에 복제했습니다. 이를 통해 로컬 환경에서 컨테이너로의 전환이 더 원활해졌습니다. 매개변수를 계속 추가하고 사용함에 따라 명령이 길고 복잡해질 수 있습니다. 실행 설정을 완료하면 Docker Compose를 사용하여 YAML 파일로 전환하는 것이 다음 단계입니다. 컨테이너의 시작 프로세스를 단순화하는 것을 넘어서, Docker Compose는 여러 컨테이너를 시작하는 등 더 복잡한 시나리오를 관리할 수 있게 해줍니다.

# 자원

- RStudio — https://posit.co/products/open-source/rstudio/
- The Rocker Project — https://rocker-project.org/
- Docker Hub — https://hub.docker.com/