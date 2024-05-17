---
title: "라즈베리 파이를 원격으로 접속하는 방법 Tailscale을 활용한 포괄적인 가이드"
description: ""
coverImage: "/assets/img/2024-05-17-AccessingYourRaspberryPiRemotelyAComprehensiveGuideUsingTailscale_0.png"
date: 2024-05-17 19:30
ogImage: 
  url: /assets/img/2024-05-17-AccessingYourRaspberryPiRemotelyAComprehensiveGuideUsingTailscale_0.png
tag: Tech
originalTitle: "Accessing Your Raspberry Pi Remotely: A Comprehensive Guide Using Tailscale"
link: "https://medium.com/@lennart.dde/accessing-your-raspberry-pi-remotely-a-comprehensive-guide-using-tailscale-b1b30cb02e93"
---


<img src="/assets/img/2024-05-17-원격으로RaspberryPi에액세스하는포괄적인가이드Tailscale을사용하여" />

현재 연결된 세상에서 장치에 원격으로 액세스하는 능력은 놀라운 융통성과 유용성을 제공합니다. 라즈베리 파이 서버를 관리하거나 파일에 액세스하거나 어디에서든 IoT 장치를 제어하려는 경우 안전한 네트워크 액세스는 중요합니다. 이 안내서는 와이어가드(WireGuard) 암호화를 활용하여 안전한 네트워크를 만드는 강력하고 안전한 서비스인 Tailscale을 사용하는 데 초점을 맞춥니다. 우리는 Raspberry Pi에 Tailscale을 설정하여 가정 네트워크 외부에서 액세스하는 단계를 안내할 것입니다.

# 원격 액세스 소개

원격 액세스를 통해 인터넷을 통해 다른 위치에서 Raspberry Pi에 연결할 수 있습니다. 이겢은 서버를 관리하거나 업데이트를 실행하거나 집을 벗어난 상태에서 미디어 파일에 액세스하는 데 특히 유용할 수 있습니다. 그러나 포트 전달과 같은 전통적인 방법은 네트워크를 보안 위험에 노출시킬 수 있습니다. Tailscale은 안전한 VPN을 만들어 더 안전하고 간단한 대안을 제공합니다.

<div class="content-ad"></div>

# 필요한 준비물

- 인터넷에 연결된 라즈베리 파이 헤드리스 시스템
- Tailscale 계정: 개인용으로 무료로 제공됩니다.
- SSH 접근: 라즈베리 파이에 소프트웨어를 설치하고 구성하기 위해 필요합니다.

# 단계 1: 라즈베리 파이에 Tailscale 설정하기

라즈베리 파이에 연결하세요:

<div class="content-ad"></div>

- 아래 명령어를 사용하여 Raspberry Pi에 SSH로 연결하세요:

```js
ssh pi@<IP-ADDRESS>
```

`<IP-ADDRESS>` 자리에 Raspberry Pi의 실제 IP 주소를 입력해주세요.

Tailscale 설치:

<div class="content-ad"></div>

라즈베리 파이에 Tailscale 패키지 소스를 추가하세요:

```js
curl -fsSL https://pkgs.tailscale.com/stable/raspbian/buster.gpg | sudo apt-key add -
curl -fsSL https://pkgs.tailscale.com/stable/raspbian/buster.list | sudo tee /etc/apt/sources.list.d/tailscale.list
```

패키지 목록을 업데이트하고 Tailscale을 설치하세요:

```js
sudo apt update
sudo apt install tailscale
```

<div class="content-ad"></div>

시작 및 Tailscale 인증:

- Raspberry Pi에서 Tailscale 서비스를 시작합니다:

```js
sudo tailscale up
```

터미널에서 제공된 인증 지침을 따르세요. 이는 URL을 방문하고 Tailscale 계정에 로그인하여 장치를 인증하는 과정을 포함합니다.

<div class="content-ad"></div>

# 단계 2: 안전한 액세스를 위한 Tailscale 설정

Raspberry Pi의 Tailscale IP 확인:

인증이 완료되면 Raspberry Pi에 Tailscale IP 주소가 할당됩니다:

```js
tailscale ip -4
```

<div class="content-ad"></div>

접근을 위한 Raspberry Pi 원격 액세스입니다.

<div class="content-ad"></div>

클라이언트 장치에 Tailscale 설치하기:

- 원격 액세스에 사용할 장치(예: 노트북, 스마트폰)에 Tailscale을 다운로드하고 설치하세요. Tailscale 앱은 Windows, macOS, Linux, iOS 및 Android용으로 제공됩니다.

클라이언트 장치 인증하기:

- 라즈베리 파이 설정과 유사하게 클라이언트 장치에서 Tailscale을 시작하고 Tailscale 계정을 통해 인증하세요.

<div class="content-ad"></div>

Raspberry Pi에 SSH로 연결하기:

- 두 장치가 Tailscale 네트워크에 연결되면, Raspberry Pi로 SSH를 사용할 수 있습니다. Raspberry Pi의 Tailscale IP를 사용하여 다음과 같이 명령을 입력하세요:

```js
ssh pi@<TAILSCALE-IP>
```

위 명령에서 `TAILSCALE-IP`를 이전에 메모한 Tailscale IP 주소로 대체해주세요.

<div class="content-ad"></div>

# 단계 4: Tailscale를 사용하여 기기 관리하기

- 연결된 기기 모니터링하기:

- Tailscale 관리자 콘솔을 사용하여 모든 연결된 기기를 확인하고 권한을 관리할 수 있습니다.

- 서브넷 설정하기 (옵션):

<div class="content-ad"></div>

- 만약 라즈베리 파이의 모든 트래픽을 Tailscale 네트워크를 통해 라우팅하고 싶다면 서브넷 라우트를 설정할 수 있습니다. 이것은 Tailscale 네트워크의 다른 기기들이 액세스해야 하는 Pi 상의 서비스가 있는 경우 유용합니다.

# 단계 5: 보안 강화

- 정기적인 업데이트:

- 라즈베리 파이와 모든 기기를 최신 Tailscale 및 OS 업데이트로 업데이트하여 보안 패치가 적용되도록 유지하세요.

<div class="content-ad"></div>

- 강력한 인증 사용하기:

- 라즈베리 파이와 Tailscale 계정에 강력하고 고유한 암호를 사용하세요. 추가적인 보안을 위해 이중 인증 (2FA)을 활성화하는 것을 고려해보세요.

# 결론

이제 라즈베리 파이에 Tailscale을 성공적으로 설정하여 전 세계 어디에서나 안전하게 액세스할 수 있습니다. Tailscale의 사용 편의성과 견고한 보안 기능은 가정 기기에 원격 액세스하기 위한 탁월한 선택지로 제공됩니다. 개인 프로젝트 또는 복잡한 IoT 시스템을 관리하든, Tailscale은 연결성과 제어를 유지하기 위한 확장 가능하고 안전한 방법을 제공합니다.

<div class="content-ad"></div>

이 안내서는 원격 액세스를 위해 Tailscale을 설정하고 사용하는 방법에 대한 종합적인 소개를 제공합니다. 그러나 가능성은 여기서 끝나지 않습니다. Tailscale을 사용하면 네트워크를 확장하고 더 많은 기기를 추가하며 설정을 향상시키기 위해 자동화 스크립트를 통합할 수 있습니다. 원격 기기 관리의 세계가 여러분의 손끝에 있고, Tailscale은 이를 접근 가능하고 안전하게 만들어줍니다.

## 리소스: