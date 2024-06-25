---
title: "도커 핵심 개념 완벽 정리"
description: ""
coverImage: "/assets/img/2024-06-23-DockerFundamentals_0.png"
date: 2024-06-23 23:08
ogImage:
  url: /assets/img/2024-06-23-DockerFundamentals_0.png
tag: Tech
originalTitle: "Docker Fundamentals"
link: "https://medium.com/@ibrahims/docker-fundamentals-5e035186f274"
---

도커는 개발자가 컨테이너에서 애플리케이션을 빌드, 배포 및 실행할 수 있도록 하는 플랫폼입니다. 컨테이너는 가벼우며 이식 가능하고 효율적이어서 현대적인 애플리케이션 개발과 배포에 인기가 많습니다.

buymeacoffee ☕ 👈 해당 링크를 클릭해 주세요

## 컨테이너 & 가상 머신(VM)

컨테이너와 가상 머신(VM)은 모두 애플리케이션에 대한 격리된 환경을 제공하지만, 그들은 크게 다릅니다:

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

- VMs: 전체 하드웨어 및 운영 체제를 가상화합니다. 무겁고 더 많은 자원을 사용합니다.
- Containers: 운영 체제를 가상화하고 호스트 머신과 커널을 공유하지만 애플리케이션 프로세스를 격리합니다. 가벼우며 적은 자원을 사용합니다.

## 전통적인 배포와의 도전 과제

전통적인 배포 방법은 종종 다음과 같은 도전 과제에 직면합니다:

- 의존성 충돌: 다른 애플리케이션이 서로 다른 라이브러리나 의존성 버전을 필요로 하여 충돌을 일으킬 수 있습니다.
- 환경 일관성: 구성의 차이로 인해 응용 프로그램이 다양한 환경에서 다르게 동작할 수 있습니다.
- 확장성: 여러 서버에 걸쳐 응용 프로그램을 확장하는 것은 복잡하고 자원을 많이 사용할 수 있습니다.

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

컨테이너화는 애플리케이션과 그 종속성을 컨테이너에 캡슐화하여 일관성과 이식성을 환경 전반에 걸쳐 보장함으로써 이러한 도전 과제에 대응합니다.

도커 아키텍처 이해하기

도커는 클라이언트-서버 아키텍처를 따르며, 주요 구성 요소는 도커 클라이언트, 도커 데몬 및 도커 레지스트리입니다.

![이미지](/assets/img/2024-06-23-DockerFundamentals_0.png)

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

도커는 여러 주요 구성 요소를 사용하는 클라이언트-서버 아키텍처를 활용합니다:

- 도커 클라이언트: 도커와 상호 작용하는 데 사용하는 명령줄 인터페이스(CLI) 도구입니다. 컨테이너를 빌드, 실행 및 관리할 수 있습니다. 이것은 도커를 원격으로 제어하는 것으로 생각할 수 있습니다.
- 도커 엔진 (데몬): 이 소프트웨어는 시스템에서 실행되며 컨테이너의 빌드, 실행 및 배포를 관리합니다. 도커 클라이언트로부터 명령을 수신하고 그에 대해 행동에 옮깁니다.
- 도커 호스트: 도커가 설치된 물리적인 머신(또는 가상 머신)입니다. 도커 컨테이너를 실행하는 데 필요한 리소스 및 환경을 제공합니다.

도커 오브젝트 (이미지 및 컨테이너):

- 도커 이미지: 도커 컨테이너를 만드는 데 필요한 지침이 포함된 청사진입니다. 어플리케이션을 실행하는 데 필요한 환경(운영 체제, 라이브러리, 응용 프로그램 코드)을 정의합니다. 이것은 어플리케이션 환경을 만드는 레시피로 상상할 수 있습니다.
- 도커 컨테이너: 도커 이미지의 실행 중인 인스턴스입니다. 가벼우며 다른 컨테이너로부터 격리됩니다. 기본 호스트 시스템의 커널을 공유합니다.

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

도커 플로우:

일반적으로 도커 작업 흐름은 다음 단계로 진행됩니다:

![도커 이미지 생성](/assets/img/2024-06-23-DockerFundamentals_1.png)

- 빌드: 도커 파일로부터 도커 이미지를 생성합니다.
- 푸시: 이미지를 중간 레지스트리나 도커 허브에 업로드합니다.
- 풀: 레지스트리에서 이미지를 다운로드합니다.
- 실행: 이미지로부터 컨테이너를 배포합니다.
- 공유 (선택 사항): 이미지를 레지스트리(예: 도커 허브)에 푸시하여 다른 사람들과 공유할 수 있습니다.

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

도커 명령어:

도커 명령어를 사용하면 도커 컨테이너를 쉽게 생성, 실행, 중지, 제거하고 관리할 수 있습니다. 이러한 명령어는 응용 프로그램을 컨테이너 환경에서 배포하고 관리하는 프로세스를 자동화하고 간소화하는 데 도움이 될 수 있습니다.

![도커 기초 사항 이미지](/assets/img/2024-06-23-DockerFundamentals_2.png)

가장 일반적으로 사용되는 도커 명령어를 살펴보면 도커 컨테이너를 효과적으로 관리하는 데 도움이 될 것입니다.

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

- `docker run` – 새로운 이미지로부터 Docker 컨테이너를 시작하는 데 사용됩니다.
- `docker ps` – 실행 중인 모든 Docker 컨테이너를 나열하는 데 사용됩니다.
- `docker stop` – 실행 중인 컨테이너를 중지하는 데 사용됩니다.
- `docker rm` – Docker 컨테이너를 제거하는 데 사용됩니다.
- `docker images` – 현재 시스템에 있는 모든 Docker 이미지를 나열하는 데 사용됩니다.
- `docker pull` – 레지스트리에서 Docker 이미지를 다운로드하는 데 사용됩니다.
- `docker exec` – 실행 중인 컨테이너에서 명령을 실행하는 데 사용됩니다.
- `docker-compose` – 여러 컨테이너로 구성된 Docker 애플리케이션을 관리하는 데 사용됩니다.

# Docker 설치

Docker를 설치하는 과정은 운영 체제에 따라 약간 다를 수 있습니다. 아래는 일반적인 플랫폼에 Docker를 설치하는 일반적인 단계입니다:

# 1. Windows에 Docker 설치하기:

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

1. Docker 웹사이트에서 Docker Desktop for Windows 설치 프로그램을 다운로드하고 실행해주세요 🐬.

2. 설치를 완료하기 위해 화면 안내에 따라 진행해주세요. Docker Desktop은 Docker Engine을 포함한 필요한 구성 요소를 시스템에 설치합니다.

3. 설치가 완료되면 Docker Desktop은 시스템 트레이에 나타나며 Docker 명령어는 명령 프롬프트(또는) PowerShell에서 실행할 수 있습니다.

# 2. Linux(Ubuntu/Debian)에 Docker 설치하기:

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

터미널을 열고 아래 명령을 하나씩 실행해주세요:

```bash
# 패키지 인덱스 업데이트

# 필요한 종속성 설치

# Docker GPG 키 추가
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

# 도커 저장소 추가

# 패키지 인덱스 다시 업데이트

# 도커 설치

# 도커 서비스 시작

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

# 도커 명령을 sudo 없이 실행하려면 사용자를 'docker' 그룹에 추가하세요.

## macOS에 도커 설치하기:

1. 도커 웹사이트에서 도커 데스크톱 for Mac 설치 프로그램을 다운로드 🐬 받아 더블 클릭하여 설치를 시작합니다.

2. 설치를 완료하기 위해 Docker.app 파일을 Applications 폴더로 드래그 앤 드롭하세요.

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

3. 응용 프로그램 폴더에서 Docker Desktop을 실행하면 배경에서 실행됩니다.

# 4. Docker 설치 확인:

Docker를 설치한 후 터미널이나 명령 프롬프트에서 다음 명령을 실행하여 설치를 확인할 수 있습니다:

![이미지](/assets/img/2024-06-23-DockerFundamentals_3.png)

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

만약 Docker가 정상적으로 설치되었다면, 버전 번호가 표시됩니다.

![Docker version](/assets/img/2024-06-23-DockerFundamentals_4.png)

## 첫 번째 Docker 컨테이너 실행하기

Nginx 이미지 다운로드: 터미널(또는) 명령 프롬프트에서 다음 명령을 사용하여 Docker 허브에서 공식 Nginx 이미지를 다운로드하세요:

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

![Docker Fundamentals](/assets/img/2024-06-23-DockerFundamentals_5.png)

Run Nginx Container
Now, run the Nginx container using the `docker run` command:

- `-d`: Detached mode. The container will run in the background.
- `-p 80:80`: Publishes port 80 from the container to port 80 on the host machine. This allows you to access the web server on your browser.

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

- **name**: `my_web_app` - 컨테이너에 사용자 정의 이름("my_web_app")을 할당하여 쉽게 식별할 수 있도록 합니다.

- **nginx**: 실행할 이미지의 이름(이 경우에는 공식 Nginx 이미지).

웹 서버가 성공적으로 실행 중인 것을 나타내는 기본 Nginx 랜딩 페이지를 볼 수 있어야 합니다.

![Nginx Landing Page](/assets/img/2024-06-23-DockerFundamentals_6.png)

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

로컬 이미지 목록

현재 실행 중인 컨테이너를 나열하려면:

![DockerFundamentals_7](/assets/img/2024-06-23-DockerFundamentals_7.png)

컨테이너를 중지하려면 다음 명령어를 사용할 수 있습니다:

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

그리고 컨테이너를 제거하고 싶다면 다음을 사용할 수 있어요:

Docker와 Nginx를 사용하여 간단한 웹 애플리케이션을 성공적으로 만들었어요. 이미지를 제거한 후에도요.

# 결론

Docker를 사용한 컨테이너화는 응용 프로그램을 패키지화, 배포, 관리하는 강력하고 효율적인 방법을 제공해요. Docker의 기능과 도구를 활용하여 개발자들은 개발 프로세스를 간소화하고 응용 프로그램의 일관성을 보장하며 확장성과 이식성을 향상시킬 수 있어요.

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

우리 블로그를 읽어 주셔서 감사합니다 🙏.
