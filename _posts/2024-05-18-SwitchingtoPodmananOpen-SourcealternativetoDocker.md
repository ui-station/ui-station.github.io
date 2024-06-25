---
title: "파드맨으로 전환하기 도커의 오픈소스 대안"
description: ""
coverImage: "/assets/img/2024-05-18-SwitchingtoPodmananOpen-SourcealternativetoDocker_0.png"
date: 2024-05-18 17:36
ogImage:
  url: /assets/img/2024-05-18-SwitchingtoPodmananOpen-SourcealternativetoDocker_0.png
tag: Tech
originalTitle: "Switching to Podman: an Open-Source alternative to Docker"
link: "https://medium.com/@dsousa_12/switching-to-podman-an-open-source-alternative-to-docker-a5cf06c33480"
---

2021년 8월 기준으로 Docker Desktop은 기업용 유료 구독이 필요하지만 CLI는 무료로 제공됩니다. 이 변경으로 일부 사용자들이 대안 옵션을 찾게 될 수 있습니다. 하지만 macOS 사용자로서 Docker Desktop은 Docker CLI를 실행하는 데 필수적이지만 걱정하지 마세요. 다른 옵션이 있습니다.

Colima와 Podman은 Docker의 가장 인기 있는 대체품 중 두 가지로, 둘 다 시도해본 결과 이 글에서는 컨테이너 관리를 위한 무료 오픈 소스 대안인 Podman에 초점을 맞출 것입니다.

Podman은 제가 발견한 최고의 대안이며, 그 이유를 설명하겠습니다!

- Docker 사용자이지만 CLI를 자주 사용하지 않는 경우, Podman은 데스크톱 애플리케이션을 갖추고 있어 무료이므로 훌륭한 선택입니다!
- Docker 사용자이고 CLI만 사용하는 경우에도 Podman은 작동합니다!

물론, Podman을 설치하고 docker `무언가`를 실행하려고 하면 작동하지 않을 것입니다. 그러나 이 글을 따라가면 Podman을 내 Docker 대체 도구로 사용하는 방법을 알 수 있습니다!

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

# Podman 설치 방법

Podman을 설치하려면 데스크톱 앱을 포함하여 두 가지 명령어만 필요합니다:

- Podman CLI 설치: brew install podman;
- Podman 데스크톱 설치: brew install --cask podman-desktop;

- 데스크톱 애플리케이션을 원하는 사용자를 위한 명령어임을 주의해주세요.

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

# 가상 머신을 시작하는 방법

Podman을 설치한 후 가상 머신을 시작하려면 만들고 시작해야 합니다. 아래 단계를 따라 주세요:

- podman machine init;
- podman machine start;

## 가상 머신이 성공적으로 초기화되었는지 어떻게 알 수 있나요?

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

가상 머신이 성공적으로 시작되었는지 확인하려면 터미널에서 `podman machine list`를 실행하십시오. 아래 스크린샷과 유사한 내용이 표시되어야 합니다. 여기에는 기본 머신이 현재 실행 중인 것이 표시됩니다:

![스크린샷](/assets/img/2024-05-18-SwitchingtoPodmananOpen-SourcealternativetoDocker_0.png)

# Podman과 함께 Docker Compose 사용하는 방법

기본 설정에서 Docker Compose는 Podman을 "Docker" 인스턴스로 인식하지 않을 것입니다. 그러나 "수정"하여 사용할 수 있습니다!

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

어떤 일을 하기 전에 brew install docker-compose 명령을 사용하여 Docker Compose를 설치해야 합니다.

그러나 이 시점에서 docker-compose up을 실행하면 "Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?"과 같은 오류 메시지가 표시될 것입니다.

Docker Compose를 Podman과 함께 작동하도록 하려면 다음 단계를 따르세요:

- sudo /usr/local/Cellar/podman/`podman-version`/bin/podman-mac-helper install;

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

- 참고: `podman-version`을(를) 사용 중인 Podman 버전으로 바꿔주세요.

- podman machine stop && podman machine start;를 실행해주세요;

이제 작동하는지 확인해볼 시간입니다! 기존의 docker-compose.yaml 파일을 사용하거나 새로 만들어서 docker-compose up -d를 실행해보세요.

![이미지](/assets/img/2024-05-18-SwitchingtoPodmananOpen-SourcealternativetoDocker_1.png)

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

## 작동하지 않나요?

이러한 단계를 따라도 Docker Compose가 여전히 동일한 오류("Docker 데몬에 연결할 수 없음...")를 표시한다면, 아래 두 가지 해결 방법 중 하나를 시도해 보세요:

- 다음 명령을 사용하여 DOCKER_HOST를 내보내려 해보세요:
  sh
  export DOCKER_HOST=`unix:///Users/your-user/.local/share/containers/podman/machine/podman-machine-default/podman.sock`
- rootful 권한으로 Podman Machine을 설정하려면 다음 명령을 사용하세요:
  sh
  podman machine stop && podman machine set --rootful && podman machine start

# Podman 명령어

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

Docker와 Podman 사이에는 많은 명령어가 유사하지만, 이미지 및 컨테이너 작업을 할 때 구문 및 동작에 차이가 있습니다. 특히 특정 명령어와 옵션에 대해 더 알아보려면 항상 Podman 설명서를 참고하거나 podman --help를 실행하는 것이 좋습니다.

일부 예시를 살펴보겠습니다:

- docker ps는 podman ps로 변경됩니다;
- docker run은 podman run으로 변경됩니다;
- docker rm `container`는 podman rm `container`로 변경됩니다;
- 기타.

다음은 docker와 함께 사용할 Podman 명령어에 별칭을 생성하는 단계입니다:

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

- 물고기 (Oh My Fish를 사용하는 경우):

- IDE에서 파일 ~/.config/fish/conf.d/omf.fish을 엽니다;
- 초기 설정 라인 (1에서 4까지) 다음에 다음 라인을 추가합니다: alias docker="podman";
- 터미널을 다시 시작하거나 source ~/.config/fish/conf.d/omf.fish을 실행합니다.

- Zsh의 경우:

- IDE에서 파일 ~/.zshrc을 엽니다;
- # Example aliases 주석 다음에 다음 라인을 추가합니다: alias docker="podman";
- 터미널을 다시 시작하거나 source ~/.zshrc을 실행합니다.

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

- Bash에 대한 설명:

  - IDE \*\*\*\*에서 파일 ~/.bashrc을 열어주세요;
  - 다음 줄을 파일 끝에 추가해주세요: alias docker="podman";
  - 터미널을 재시작하거나 source ~/.bashrc을 실행해주세요.

해당 alias를 생성한 후에는 Docker와 동일하게 docker 명령을 사용할 수 있지만 해당 명령은 Podman 명령을 실행합니다.

# Podman 데스크톱

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

![Switching to Podman: an Open-Source alternative to Docker](/assets/img/2024-05-18-SwitchingtoPodmananOpen-SourcealternativetoDocker_2.png)

이미 이전에 언급한 바와 같이, Podman은 CLI를 사용하기를 원하지 않는 경우 필요한 모든 옵션을 갖춘 데스크톱 애플리케이션을 갖고 있습니다. 아래 이미지는 일상적으로 사용할 수 있는 가장 중요한 화면을 보여줍니다.

Docker Desktop과 유사하게, 이 앱은 컨테이너, 이미지 및 볼륨에 대한 메뉴를 갖고 있어 쉽게 작업을 수행할 수 있습니다.

![Switching to Podman: an Open-Source alternative to Docker](/assets/img/2024-05-18-SwitchingtoPodmananOpen-SourcealternativetoDocker_3.png)

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

![Switching to Podman: an Open-Source Alternative to Docker](/assets/img/2024-05-18-SwitchingtoPodmananOpen-SourcealternativetoDocker_4.png)

![Switching to Podman: an Open-Source Alternative to Docker](/assets/img/2024-05-18-SwitchingtoPodmananOpen-SourcealternativetoDocker_5.png)

이미지만 봐도 이해하기 쉬울 거에요. 궁금한 점 있으면 댓글로 남겨주세요.

참고: Podman Desktop을 설치하면 가상 머신이 로그인 시 시작하도록 설정을 변경할 수 있습니다. 이렇게 하면 매번 podman machine start를 실행할 필요가 없어요.

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

개발자로서 제 생각이에요. 아마도 당신에게는 Podman이 모든 필요를 충족시키지 못할 수도 있어요. 그러나 제 일상 사용에는 충분하고, 지금까지 어떤 문제도 만나지 않았어요.

요약하면, Podman은 Docker에 대한 강력한 대안으로, 안전하고 가벼운 실행 환경, Docker와 유사한 명령줄 인터페이스, 데몬이 필요하지 않은 macOS 및 Linux 배포판에서 실행할 수 있는 기능과 같은 많은 이점을 제공해요. Docker 대체품을 찾고 있다면, Podman은 분명히 확인할 가치가 있어요.

이 글이 도움이 되었으면 좋겠어요!
