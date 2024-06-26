---
title: "Docker 단계별 가이드 Reactjs 프로젝트를 Docker로 윈도우 환경에서 로컬 설정하는 방법"
description: ""
coverImage: "/assets/img/2024-06-30-Step-by-StepGuidetoDockerLocalSetuponWindowswithaDockerizedReactjsProject_0.png"
date: 2024-06-30 19:27
ogImage: 
  url: /assets/img/2024-06-30-Step-by-StepGuidetoDockerLocalSetuponWindowswithaDockerizedReactjsProject_0.png
tag: Tech
originalTitle: "Step-by-Step Guide to Docker: Local Setup on Windows with a Dockerized React.js Project"
link: "https://medium.com/@techkaala7/step-by-step-guide-to-docker-local-setup-on-windows-with-a-dockerized-react-js-project-3d0f49fbfa24"
---


<img src="/assets/img/2024-06-30-Step-by-StepGuidetoDockerLocalSetuponWindowswithaDockerizedReactjsProject_0.png" />

소개:

도커는 응용 프로그램을 격리된 컨테이너에 배포하고 관리하는 데 도움이 되는 강력한 도구입니다. 이 안내서는 Windows에서 도커를 설정하고 React.js 프로젝트를 도커화하는 방법을 안내하여 원할하고 효율적인 개발 워크플로우를 보장합니다.

도커란 무엇인가요?

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

도커는 가벼운 격리 소프트웨어 컨테이너 내에서 애플리케이션을 배포하고 관리하는 자동화를 가능케하는 오픈 소스 플랫폼이에요. 이를 통해 응용 프로그램이 모든 환경에서 일관되게 실행되도록 보증하며, 모든 종속성을 캡슐화해요.

도커 파일을 이해하고 계신가요?

도커 파일(Dockerfile)은 도커 이미지를 빌드하는 방법에 대한 지시사항이 담긴 텍스트 파일이에요. 도커 이미지를 실행 가능한 이미지로 만들기 위해 기본 이미지, 애플리케이션 코드, 종속성 및 기타 설정을 지정해요.

도커 이미지란 무엇인가요?

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

이미지는 응용 프로그램을 실행하는 데 필요한 모든 것을 포함하는 가벼운, 독립적이고 실행 가능한 소프트웨어 패키지입니다. 코드, 런타임, 시스템 도구, 라이브러리 및 설정을 포함하여 응용 프로그램을 실행하는 데 필요한 모든 것이 포함되어 있습니다. Docker 이미지는 기본 이미지에서 생성되며 추가 구성 요소로 사용자 정의할 수 있습니다.

도커 컨테이너란 무엇인가요?

컨테이너는 응용 프로그램과 해당 종속성을 캡슐화하는 격리된 환경입니다. 각 컨테이너는 호스트 머신에서 격리된 프로세스로 실행되며 자체 파일 시스템, 네트워킹 및 리소스를 가지고 있습니다. 하나의 도커 이미지에서 여러 컨테이너를 생성할 수 있습니다.

도커 컴포즈란 무엇인가요?

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

도커 Compose는 여러 개의 컨테이너로 구성된 애플리케이션을 정의하고 관리하는 도구입니다. 이를 사용하면 YAML 파일에서 여러 개의 컨테이너 설정 및 서로 통신하는 방식을 정의할 수 있습니다. 도커 Compose를 사용하면 복잡한 애플리케이션을 여러 개의 상호 연결된 컨테이너로 쉽게 실행할 수 있습니다.

컨테이너 레지스트리란 무엇인가요?

컨테이너 레지스트리는 컨테이너 이미지를 저장하고 공유하는 중앙 저장소입니다. 버전 관리 및 이미지 무결성을 보장하면서 컨테이너화된 애플리케이션을 안전하게 관리하고 배포할 수 있습니다. 예시로는 Docker Hub, Amazon ECR, Google Container Registry, Azure Container Registry 및 GitHub Container Registry 등이 있습니다.

Windows에 Docker 설치 방법:

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

- 도커 공식 문서인 Docker Docs를 방문해주세요.
- 윈도우용 도커인 Docker Desktop을 다운로드하세요.
- 윈도우에서 WSL을 구성하세요:

  - "Windows 기능 켜기 끄기"를 검색하고 선택하세요.
  - "Windows 하이퍼바이저 플랫폼"과 "Windows용 하위 시스템 for Linux"을 활성화하세요.
  - 확인을 클릭하고 시스템을 다시 시작하세요.
  - 문제가 발생하면 이 안내에 따라 WSL을 수동으로 구성하세요.

4. 도커 데스크탑 설치하기:

  - 다운로드한 Docker Desktop Installer.exe를 실행하세요.
  - 설치 단계를 따라 진행하고 시스템을 재시작하세요.
  - 자동으로 재시작하면 화면에 "도커 구독 서비스 약정" 대화상자가 나타납니다.
  - 동의 버튼을 클릭하세요.
  - 로그인하거나 로그인 없이 계속하세요.
  - 도커 홈페이지에 오신 것을 환영합니다.

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

5. 설치 검증하기:

- 명령 프롬프트를 열고 도커가 올바르게 설치되었는지 확인하기 위해 docker version 및 images를 실행합니다.

```js
docker version

docker images
```

React.js 프로젝트 도커화하기:

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

- React.js 프로젝트를 생성하세요.
- 개발용 도커 파일을 생성하세요:

- React.js 프로젝트의 루트 디렉토리에 다음 내용을 가진 Dockerfile.dev을 생성하세요

```js
FROM node:alpine
WORKDIR /app
COPY package.json /app
RUN npm install
COPY . /app
CMD ["npm", "start"]
```

3. .dockerignore 파일을 생성하세요:

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

- React.js 프로젝트의 루트 디렉토리에 다음 내용으로 .dockerignore 파일을 만들어주세요:

```js
node_modules
README.md
.gitignore
```

4. Docker 이미지 빌드하기:

```js
docker build -f Dockerfile.dev -t myapp .
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

- docker build: 이 명령은 Docker에게 Dockerfile에 있는 지시사항을 기반으로 새 이미지를 생성하도록 지시합니다.
- -f Dockerfile.dev: -f 플래그는 사용할 Dockerfile의 이름을 지정합니다. 이 경우에는 Dockerfile.dev입니다.
- -t myapp: -t 플래그는 이미지에 이름을 태그합니다. 여기서 이미지는 myapp으로 태그됩니다.
- .: 명령어 끝에 있는 점은 현재 디렉토리인 빌드 컨텍스트를 나타냅니다.

5. Docker 이미지 실행:

```js
docker ps
```

```js
docker run -it --name myProject -p 3000:3000 myapp
docker run -it -d --name myProject -p 3000:3000 myapp
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

- -i (interactive): 상호작용 모드를 유지하여 STDIN을 닫지 않습니다. 이는 대화형 프로세스에 유용합니다.
- -t (tty): 의사-TTY를 할당합니다. 이는 터미널 상호작용에 유용합니다.
- -d: 이 플래그는 컨테이너를 백그라운드에서 실행하고 터미널을 차단하지 않는 분리된 모드로 실행합니다.
- --name myProject: 이 플래그는 컨테이너에 이름을 지정하여 관리를 더 쉽게 합니다. 이 경우 컨테이너의 이름은 myProject입니다.
- -p 3000:3000: 이 플래그는 호스트의 포트 3000을 컨테이너의 포트 3000에 매핑합니다. 형식은 호스트포트:컨테이너포트입니다. 이를 통해 컨테이너 내에서 실행 중인 애플리케이션에 호스트 머신의 포트 3000을 통해 액세스할 수 있습니다.

YouTube 비디오:
도커 + React js 설정 및 구성에 대한 상세한 단계는 아래 비디오 튜토리얼을 시청하세요:

결론:
Docker는 현대 개발자들에게 귀중한 도구로, 격리된 환경에서 애플리케이션을 관리하고 배포하는 효율적인 방법을 제공합니다. 이 안내를 따라오면 다음을 배울 수 있습니다:

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

- Dockerfiles, 이미지, 컨테이너, Docker Compose 및 컨테이너 레지스트리를 포함한 Docker의 기본 개념을 이해합니다.
- WSL과 같은 필수 구성 요소를 올바르게 구성하여 Windows 기기에 Docker를 설치합니다.
- React.js 프로젝트를 Docker화하여 일관된 이식 가능한 개발 환경을 활성화합니다.

더 많은 DevOps 및 기술 관련 자습서와 통찰력을 기대해주세요!

저작권 © 2024 MAC Tech Family, 판권 소유.