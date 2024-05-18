---
title: "라즈베리 파이에 Tailscale VPN을 통해 SSH하기"
description: ""
coverImage: "/assets/img/2024-05-18-SSHIntoRaspberryPiwithTailscaleVPN_0.png"
date: 2024-05-18 19:06
ogImage: 
  url: /assets/img/2024-05-18-SSHIntoRaspberryPiwithTailscaleVPN_0.png
tag: Tech
originalTitle: "SSH Into Raspberry Pi with Tailscale VPN"
link: "https://medium.com/@shilleh/ssh-into-raspberry-pi-with-tailscale-vpn-eb2d645ae395"
---


<img src="/assets/img/2024-05-18-SSHIntoRaspberryPiwithTailscaleVPN_0.png" />

요즘 연결된 세상에서 원격으로 기기에 액세스하는 것은 많은 기술 애호가와 전문가들에게 필수적인 요소가 되었습니다. 집에서 프로젝트를 작업하는 취미 요원이든, 여러 기기를 관리하는 IT 전문가이든, 안전한 원격 액세스가 중요합니다. 이러한 목적에 가장 적합한 도구 중 하나가 Tailscale입니다.

Tailscale은 기기 간의 안전한 네트워크를 생성하는 과정을 간소화하는 망 VPN 서비스입니다. Tailscale을 사용하면 전 세계 어디에서나 Raspberry Pi에 쉽게 액세스할 수 있으며, 마치 동일한 로컬 네트워크에 있는 것처럼 사용할 수 있습니다. 이 튜토리얼에서는 Raspberry Pi에 Tailscale을 설정하여 외부 네트워크에서 SSH로 액세스하는 방법을 안내하겠습니다. Raspberry Pi와 Mac에서의 설치 과정을 다루고, 기기 인증 방법을 보여드리며, SSH 연결을 설정하는 방법을 시연하겠습니다. 참고로 Windows 사용자들의 단계는 비슷합니다.

나머지 부분을 읽기 전에 아직 채널을 구독하지 않았다면 반드시 구독하고 지원해주세요!

<div class="content-ad"></div>

# 구독하기:

YouTube

# 후원하기:

[Buy Me a Coffee](https://www.buymeacoffee.com/mmshilleh)

<div class="content-ad"></div>

UpWork에서 저를 고용하여 IoT 프로젝트를 구축하세요:

[https://www.upwork.com/freelancers/~017060e77e9d8a1157](https://www.upwork.com/freelancers/~017060e77e9d8a1157)

# 단계 1: 라즈베리 파이에 Tailscale 설정하기

라즈베리 파이 업데이트: 모든 패키지가 최신 상태인지 확인하려면 Raspberry Pi를 업데이트하세요. 터미널을 열고 다음을 실행하세요:

<div class="content-ad"></div>

```js
sudo apt update
sudo apt upgrade -y
```

Tailscale 설치: 먼저 Tailscale을 설치하세요. 라즈베리 파이에 Tailscale 레포지토리를 추가해야 합니다:

```js
curl -fsSL https://pkgs.tailscale.com/stable/raspbian/buster.gpg | sudo apt-key add -
curl -fsSL https://pkgs.tailscale.com/stable/raspbian/buster.list | sudo tee /etc/apt/sources.list.d/tailscale.list
```

패키지 목록을 업데이트하고 Tailscale을 설치하세요:

<div class="content-ad"></div>

```js
sudo apt update
sudo apt install tailscale
```

Tailscale를 시작하세요: 설치가 완료되면 Tailscale 서비스를 시작합니다.

```js
sudo tailscale up
```

Raspberry Pi를 Tailscale 계정으로 인증하기 위해 안내에 따라 따라하세요. 이를 위해 웹 브라우저에서 Tailscale 계정으로 로그인하고 장비를 인가해야 합니다.

<div class="content-ad"></div>

# 단계 2: 맥에서 Tailscale 설정하기

- Tailscale 다운로드: Tailscale 웹사이트를 방문하여 Mac 버전의 Tailscale을 다운로드합니다.
- Tailscale 설치: 다운로드한 파일을 열고 설치 지침에 따릅니다.
- 맥 인증: 응용 프로그램 폴더에서 Tailscale을 열고 Tailscale 계정으로 로그인합니다. 이렇게 하면 맥이 Tailscale 네트워크에 추가됩니다.

# 단계 3: SSH를 통해 라즈베리 파이에 연결하기

라즈베리 파이의 Tailscale IP 찾기: 라즈베리 파이에서 다음을 실행하세요:

<div class="content-ad"></div>

```js
tailscale ip -4
```

이 명령을 실행하면 Raspberry Pi의 Tailscale IP 주소(예: 100.x.x.x)가 표시됩니다.

라즈베리 파이로 SSH 연결하기: 맥에서 터미널을 열고 다음 명령을 사용하여 라즈베리 파이로 SSH 연결할 수 있습니다.

```js
ssh pi@<tailscale-ip>
```

<div class="content-ad"></div>

이전 단계에서 얻은 IP 주소로 `tailscale-ip`를 대체하세요. 예를 들어:

```js
ssh pi@100.64.0.1
```

처음 연결하는 경우 호스트를 알려진 호스트 목록에 추가하라는 메시지가 나타날 수 있습니다. yes를 입력하고 Enter 키를 누르세요.

Windows 사용자를 위한 참고: Windows 기기에 Tailscale을 설정하는 단계는 Mac과 유사합니다. Tailscale 웹사이트에서 Tailscale 설치 파일을 다운로드하고 설치한 다음 Tailscale 계정으로 로그인하세요. 그런 다음 PuTTY나 Windows 터미널과 같은 SSH 클라이언트를 사용하여 Raspberry Pi에 Tailscale IP를 사용하여 SSH로 연결하세요.

<div class="content-ad"></div>

# 결론

Tailscale을 사용하면 라즈베리 파이에 안전한 원격 액세스를 쉽고 효율적으로 설정할 수 있습니다. 이 튜토리얼에서 안내된 단계를 따라하면 어디에서나 라즈베리 파이를 쉽게 관리할 수 있습니다. Mac이든 Windows를 사용하든 Tailscale의 원활한 통합 덕분에 원격 SSH 액세스가 간편해집니다. 해킹을 즐기세요!