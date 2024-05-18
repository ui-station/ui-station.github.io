---
title: "라즈베리파이를 와이파이 라우터로 변신해보세요"
description: ""
coverImage: "/assets/img/2024-05-18-TurnyourRaspberryPiintoaWiFiRouter_0.png"
date: 2024-05-18 18:33
ogImage: 
  url: /assets/img/2024-05-18-TurnyourRaspberryPiintoaWiFiRouter_0.png
tag: Tech
originalTitle: "Turn your RaspberryPi into a WiFi Router!"
link: "https://medium.com/@vaibhavji/turn-your-raspberrypi-into-a-wifi-router-5ade510601de"
---


Raspberry Pi를 무선 액세스 포인트로 설정하는 방법

![이미지](/assets/img/2024-05-18-TurnyourRaspberryPiintoaWiFiRouter_0.png)

Raspberry Pi를 무선 액세스 포인트로 설정하는 방법을 보여 드리겠습니다. 기본적으로 다른 장치가 연결할 수 있는 무선 "라우터"로 Raspberry Pi를 변환하는 방법을 안내해 드리겠습니다. 이를 통해 생성된 무선 액세스 포인트를 설정하여 연결된 장치에 인터넷 액세스를 제공(공유)하는 방법도 안내해 드리겠습니다.

![이미지](/assets/img/2024-05-18-TurnyourRaspberryPiintoaWiFiRouter_1.png)

<div class="content-ad"></div>

# 이렇게 하였습니다

라즈베리 파이 설정을 위한 링크에 들어가 보세요. 설정이 완료되면 라즈베리 파이로 Wi-Fi 핫스팟을 만들기 시작해 봅시다.

# 단계 1:

일반적으로 라즈베리 파이를 업데이트하여 최신 버전을 보유하도록 합니다

<div class="content-ad"></div>

```markdown
sudo apt-get update
sudo apt-get upgrade
```

# STEP-2:

라즈베리 파이가 액세스 포인트로 작동하려면 액세스 포인트로 작동하기 위한 소프트웨어 패키지가 필요합니다.

아래 명령어는 라즈베리 파이를 무선 액세스 포인트로 설정할 수 있게 해주는 소프트웨어를 설치하고, AP에 연결된 장치에 네트워크 주소를 할당하는 데 도움을 주는 소프트웨어를 설치합니다.
```

<div class="content-ad"></div>

```js
sudo apt install hostapd
sudo apt install dnsmasq
```

마지막으로, netfilter-persistent와 그 플러그인 iptables-persistent를 설치해주세요. 이 유틸리티는 방화벽 규칙을 저장하고 Raspberry Pi 부팅 시에 복원하는 데 도움이 됩니다.

```js
sudo DEBIAN_FRONTEND=noninteractive apt install -y netfilter-persistent iptables-persistent
```

다음 단계로 이동하기 전에 Raspberry Pi를 다시 부팅해주세요.
```  

<div class="content-ad"></div>

```md
sudo reboot
```

# STEP-3:

Raspberry Pi는 독립 와이어리스 네트워크를 실행하고 관리합니다. 또한 와이어리스 및 이더넷 네트워크 간 경로를 제공하여 와이어리스 클라이언트에게 인터넷 액세스를 제공합니다. Raspberry Pi를 서버로 설정하기 위해서는 와이어리스 포트에 고정 IP 주소를 할당해야 합니다. 이 작업은 dhcpcd 구성 파일을 편집하여 수행할 수 있습니다. dhcpcd.conf 파일을 편집하려면 아래 명령을 실행하세요.

```md
sudo nano /etc/dhcpcd.conf
```

<div class="content-ad"></div>

dhcpcd.conf 파일 끝으로 이동하여 다음 라인들을 추가해주세요.

```js
interface wlan0
    static ip_address=192.168.4.1/24
    nohook wpa_supplicant
```

![이미지](/assets/img/2024-05-18-TurnyourRaspberryPiintoaWiFiRouter_2.png)

# STEP-4:

<div class="content-ad"></div>

라즈베리 파이에서 한 네트워크에서 다른 네트워크로 트래픽이 흐를 수 있도록 하려면 다음 명령을 사용하여 파일을 생성하고 아래 내용을 입력하세요:

```js
sudo nano /etc/sysctl.d/routed-ap.conf
```

파일 내용:

```js
# IPv4 라우팅 활성화
net.ipv4.ip_forward=1
```

<div class="content-ad"></div>

아래는 경로를 추가하여 호스트가 네트워크 192.168.4.0/24로 LAN 및 인터넷으로 이동할 수 있도록하는 설정입니다.

이 프로세스는 Raspberry Pi에 한 개의 방화벽 규칙을 추가하여 구성됩니다.

```js
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

<div class="content-ad"></div>

이제 현재 IPv4 및 IPv6 방화벽 규칙을 netfilter-persistent 서비스를 통해 부팅 시 로드할 수 있도록 다음 명령을 사용하여 저장하세요.

```js
sudo netfilter-persistent save
```

# 단계-5:

DHCP 및 DNS 서비스는 dnsmasq에서 제공됩니다. 기본 구성 파일은 모든 가능한 구성 옵션의 템플릿 역할을 합니다.

<div class="content-ad"></div>

기본 설정 파일의 이름을 변경하고 새 파일을 편집해보세요

```js
sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig
sudo nano /etc/dnsmasq.conf
```

다음을 파일에 추가하고 저장하세요,

```js
interface=wlan0
dhcp-range=192.168.4.2,192.168.4.20,255.255.255.0,24시간
domain=wlan
address=/gw.wlan/192.168.4.1
```

<div class="content-ad"></div>

아래는 코드 블록 안에 있는 내용을 실행하여 라즈베리파이의 WiFi 라디오가 차단되지 않도록 할 수 있습니다:

```js
sudo rfkill unblock wlan
```

<div class="content-ad"></div>

이 설정은 부팅 시 자동으로 복원됩니다. 액세스 포인트 소프트웨어 구성에서 적절한 국가 코드를 정의할 것입니다.

# 단계-6:

새로운 무선 네트워크를 위한 다양한 매개변수를 추가하기 위해 /etc/hostapd/hostapd.conf에 있는 호스팅 설정 파일을 만들어 주세요.

```js
country_code=IN
interface=wlan0
ssid=네트워크이름
hw_mode=g
channel=7
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=암호
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
```

<div class="content-ad"></div>

참고: `country_code`는 컴퓨터가 올바른 무선 주파수를 사용하도록 구성합니다. 두 글자 ISO 3166-1 국가 코드 목록은 위키피디아에서 확인할 수 있습니다.

- SSID: 여러분의 SSID (제 경우 네트워크 이름)

- PWD: 여러분의 비밀번호 (제 경우 비밀번호)

![이미지](/assets/img/2024-05-18-TurnyourRaspberryPiintoaWiFiRouter_5.png)

<div class="content-ad"></div>

Raspberry Pi에 적용한 변경 사항을 적용하려면 시스템을 다시 시작하세요.

```js
sudo systemctl reboot
```

시스템이 다시 켜지면 Raspberry Pi에서 만든 무선 액세스 포인트에 연결하여 인터넷에 액세스할 수 있게 될 것입니다.

<img src="/assets/img/2024-05-18-TurnyourRaspberryPiintoaWiFiRouter_6.png" />

<div class="content-ad"></div>

읽어 주셔서 감사합니다. 피드백과 코멘트는 언제나 환영합니다.

# GEEKY BAWA

## 새로운 기술을 탐구하고 멋진 경험을 하는 것을 좋아하는 어리석은 Geek예요.

# 다른 블로그도 재밌게 둘러보세요

<div class="content-ad"></div>

# 내 유튜브 채널도 한번 확인해보세요

만약 연락하고 싶으시거나 좋은 농담을 알고 계시다면 트위터나 링크드인을 통해 연락해 주세요.

독자 여러분 감사합니다!😄 🙌

트위터와 링크드인에서 저와 소통해 보세요.

<div class="content-ad"></div>

다른 기사와 참고 자료를 확인하러 시간 내어보세요. 제 글을 알림받으려면 저를 팔로우해주세요.

# Vaibhav Hariramani가 진심을 담아 제작했습니다.

- 이 글을 즐기셨다면 미디엄에서 더 많은 글을 읽어보세요!
- 더 많은 콘텐츠를 원하시면 캐글에서 저를 팔로우하세요!
- 링크드인에서 연락해주세요.
- 협업에 관심이 있으세요?
- 제 웹사이트를 확인해보세요.
- 다른 웹사이트도 함께 확인해보세요.

# 저희를 태그하지 말고는 없습니다.

<div class="content-ad"></div>

만약 이 블로그가 도움이 되었다면 친구들과 공유하고, 저희를 언급하는 것을 잊지 마세요. 그리고 Linkedin, Instagram, Facebook, Twitter, Github에서도 공유하는 것을 잊지 마세요.

# 추가 자료

이 자료들에 대해 더 배우고 싶다면 아래 나의 쓴 글들을 참고하세요:-

- Medium
- geeky Traveller
- Blogs
- Youtube
- 이메일: vaibhav.hariramani01@gmail.com

<div class="content-ad"></div>

# 더 바이브하브 하리라마니 앱 다운로드

# 더 바이브하브 하리라마니 앱 (최신 버전)

더 바이브하브 하리라마니 앱 다운로드는 안드로이드 스튜디오 및 웹 뷰로 개발된 사이트의 자습서, 프로젝트, 블로그 및 브이로그를 포함하고 있습니다. 안드로이드 기기에 설치해보세요.

# 저를 팔로우해주세요

<div class="content-ad"></div>

마크다운 형식으로 테이블 태그를 변경해주세요.

| Linkedin | Instagram | Facebook | Twitter | Github |
|---------|-----------|---------|-------|--------|
| Happy coding ❤️ |