---
title: "Ubuntu 2204에 Docker Desktop 설치하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-HowToInstallDockerDesktoponUbuntu2204_0.png"
date: 2024-06-23 22:53
ogImage: 
  url: /assets/img/2024-06-23-HowToInstallDockerDesktoponUbuntu2204_0.png
tag: Tech
originalTitle: "How To Install Docker Desktop on Ubuntu 22.04"
link: "https://medium.com/@selvamraju007/how-to-install-docker-desktop-on-ubuntu-22-04-1ebe4b2f8a14"
---


<img src="/assets/img/2024-06-23-HowToInstallDockerDesktoponUbuntu2204_0.png" />

이 블로그에서는 Ubuntu 22.04에 Docker Desktop을 설정하는 방법을 살펴보겠습니다.

Docker Desktop:

Docker Desktop은 macOS, Linux 및 Windows 컴퓨터용 응용 프로그램으로, 컨테이너화된 응용 프로그램 및 마이크로서비스를 빠르고 안전하게 구축하고 공유할 수 있습니다.

<div class="content-ad"></div>

Docker Desktop에는 응용 프로그램 개발을 위한 내장 Kubernetes 설정이 포함되어 있으며, 인증된 이미지, 템플릿, 그리고 원하는 언어와 도구를 사용할 수 있습니다. 개발 워크플로우는 Docker Hub를 활용하여 개발 환경을 안전한 저장소로 확장하여 빠른 자동 빌드, 지속적 통합 및 안전한 협업을 지원합니다.

## 준비 사항

PC가 다음 기본 요구 사항을 충족하는지 확인해주세요.

- 가상화 지원이 활성화된 64비트 CPU
- 적어도 4GB RAM
- GUI 데스크톱 환경 (가능하면 GNOME, MATE 또는 KDE)
- 관리자 권한이 있는 Sudo 사용자

<div class="content-ad"></div>

# KVM 가상화 지원

호스트가 가상화 지원을 하는 경우 kvm 모듈은 자동으로 로드됩니다. 모듈을 수동으로 로드하려면 다음을 실행하세요:

```js
modprobe kvm
```

호스트 머신의 프로세서에 따라 해당 모듈을 로드해야 합니다.

<div class="content-ad"></div>

```js
modprobe kvm_intel  # 인텔 프로세서
modprobe kvm_amd    # AMD 프로세서
```

단계 1: Gnome 데스크톱이 없는 상황에서는 Gnome 터미널을 설치해야 합니다:

```js
sudo apt install gnome-terminal
```

단계 2: Linux용 Docker Desktop의 기술 미리보기 또는 베타 버전을 제거하세요. 실행하세요:

<div class="content-ad"></div>

```js
sudo apt remove docker-desktop
```

# 우분투 22.04에 Docker 설치하기:

이제 Docker를 설치해봅시다. 하지만 그 전에 패키지 목록을 업데이트하고 필수 종속성을 설치해야 합니다. 다음과 같이 입력해주세요.

```js
$ sudo apt update
$ sudo apt install software-properties-common curl apt-transport-https ca-certificates -y
```

<div class="content-ad"></div>

설치가 완료되면 Docker의 GPG 서명 키를 추가해주세요.

```js
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/docker-archive-keyring.gpg
```

다음으로, 아래와 같이 시스템에 공식 Docker 저장소를 추가해주세요.

```js
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

<div class="content-ad"></div>

레포지토리가 준비되었으면 다음과 같이 Docker 및 기타 도커 도구를 설치합니다.

```js
$ sudo apt install docker-ce docker-ce-cli containerd.io uidmap -y
```

도커가 실행 중인지 확인하려면 다음 명령어를 실행하세요:

```js
sudo systemctl status docker
```

<div class="content-ad"></div>

아래는 Docker 버전을 확인하는 방법입니다.

```js
docker version
```

<div class="content-ad"></div>

우분투 22.04에 Docker Desktop 설치 방법:

아래 wget 명령어를 사용하여 Docker Desktop을 설치하세요. Docker Desktop의 최신 버전은 Docker Desktop 버전 4.19.0입니다.

```js
$ wget https://desktop.docker.com/linux/main/amd64/docker-desktop-4.19.0-amd64.deb
```

또한 이 링크에서 DEB 패키지를 다운로드할 수 있고 아래 명령어를 사용하여 설치할 수 있습니다.

<div class="content-ad"></div>

```js
$ sudo apt install ./docker-desktop-*-amd64.deb
```

![Docker Desktop Installation](/assets/img/2024-06-23-HowToInstallDockerDesktoponUbuntu2204_3.png)

도커 데스크톱을 실행하세요:

이제 애플리케이션 메뉴에서 도커 데스크톱을 실행하고 라이센스 약관을 수락하세요.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-23-HowToInstallDockerDesktoponUbuntu2204_4.png" />

이제 CLI에서 명령어 대신 도커 데스크톱에서 컨테이너를 만들 수 있어요.

즐거운 학습 되세요!