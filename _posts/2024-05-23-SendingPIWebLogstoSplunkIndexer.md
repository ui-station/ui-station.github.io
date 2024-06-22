---
title: "Splunk 인덱서로 PI 웹 로그 보내기"
description: ""
coverImage: "/assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_0.png"
date: 2024-05-23 16:58
ogImage:
  url: /assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_0.png
tag: Tech
originalTitle: "Sending PI Web Logs to Splunk Indexer"
link: "https://medium.com/@almmathis/sending-pi-web-logs-to-splunk-indexer-58575b80c52d"
---

<img src="/assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_0.png" />

이것은 메인 시리즈 "Hackable LEGO Train"의 작은 부분이 될 예정입니다.

다음이 메인 시리즈입니다:

[Hackable Lego Train | Part 1 | Stux | by Stux | May, 2024 | Medium](https://medium.com/)

<div class="content-ad"></div>

이 기사의 주요 포인트:

- Kali Purple 다운로드
- Kali Purple 설치
- "웹 서버"에서 앱 로그 파일을 가져오기 위한 Cron 작업 설정
- Kali-Purple에 Splunk Universal Forwarder 설치
- 웹 로그를 주요 Splunk 인덱서로 전송

부가 사항: 이전에 작성한 기사를 따랐다고 가정합니다. 이미 라즈베리 파이와 Python 웹 사이트가 작동하고 로그를 기록 중이어야 합니다. 또한 이는 공개 웹 사이트가 아니므로 모든 것이 내부 네트워크에 있습니다. 대부분의 경우 "IP-링"은 동일한 서브넷에 있으며 네트워킹을 많이 구성할 필요가 없었습니다.

![이미지](/assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_1.png)

<div class="content-ad"></div>

참고: 물리적 PI가 SCP를 통해 로그를 Host B (Virtual Kali-Purple)로 전송하고 있습니다. Host B는 Splunk Universal Forwarder가 실행되고 있어 로그를 Host A (물리적 Windows) Splunk Indexer로 전송하고 있습니다.

시작해봅시다!

## Kali Purple

Kali Purple을 다음 사이트에서 다운로드하세요:

<div class="content-ad"></div>

Kali Linux 2023.1 릴리스 (칼리 보라 & 파이썬 변경 사항) | Kali Linux 블로그

![image](/assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_2.png)

새 운영 체제를 다운로드하고 설치한 후에 일할 수 있게 됩니다.

제가 전체 네트워크를 실행하는 데 VMware Workstation을 사용했습니다. 가능하다면 여러분도 이를 추천합니다. 하지만 더 저렴한 옵션이 많이 있습니다.

<div class="content-ad"></div>

<span>/assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_3.png</span>

그래서, 이 시점에서 우리는 다음을 가지고 있습니다:

- Kali Linux가 실행되는 Raspberry PI
- Python 웹 앱이 실행되는 Raspberry PI
- 로그를 파일에 기록하는 "access" 로그
- Kali Purple가 우리 네트워크의 다른 곳에서 자체 컴퓨터(가상)로 실행 중

# Kali-Purple + PI 로그 파일 전송

<div class="content-ad"></div>

**참고:** Kali Purple로 로그를 보내는 이유는 라즈베리파이에 Splunk UF가 작동하지 않아서 새 Linux 배포판을 재설치한 것입니다(문제 해결). 따라서 나중에 라즈베리파이 OS 기본 설정에서 작동할 수 있습니다.

크론 작업을 실행하여 로그 파일을 가져올 때마다 매번 암호를 입력해야 하는 번거로움이 없도록 하려고 합니다. 또한 코드 어디에도 암호를 하드코딩하고 싶지 않습니다.

- SSH 키 생성: 두 대의 컴퓨터 간에 암호 없는 인증을 위해 SSH 키가 설정되어 있는지 확인합니다.
- 스크립트 생성: SCP 작업을 수행할 스크립트를 작성합니다.
- 크론 작업 설정: 원하는 주기로 스크립트를 실행할 크론 작업을 구성합니다.

다음과 같이 진행할 수 있습니다:

<div class="content-ad"></div>

# 단계 1: SSH 키 생성하기 (아직 설정되지 않은 경우)

Kali-Purple (192.168.184.133)에서:

```js
ssh-keygen -t rsa
ssh-copy-id stux@192.168.12.194
```

# 단계 2: SCP 스크립트 생성하기

<div class="content-ad"></div>

위 코드를 한국어로 친근하게 번역해 드리겠습니다:

스크립트를 만들어주세요 (예: /home/youruser/scp_pull_log.sh):

```bash
#!/bin/bash

# 변수 정의
REMOTE_USER="stux"
REMOTE_IP="192.168.12.194"
REMOTE_FILE="/home/stux/big-caboose/app.log"
LOCAL_DIR="/var/log/"
LOCAL_FILE="app.log"

# SCP 실행
scp ${REMOTE_USER}@${REMOTE_IP}:${REMOTE_FILE} ${LOCAL_DIR}${LOCAL_FILE}
```

스크립트를 실행 가능하게 만들어주세요:

```bash
chmod +x /home/youruser/scp_pull_log.sh
```

<div class="content-ad"></div>

# 단계 3: 크론 작업 설정하기

첫 번째 기계에서 사용자의 크론탭을 편집하십시오:

```js
crontab - e;
```

![이미지](/assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_4.png)

<div class="content-ad"></div>

다음 라인을 추가하여 cron 작업을 예약합니다 (예: 매 시간마다 실행되도록):

```js
0 * * * * /home/youruser/scp_pull_log.sh
```

# Kali-Purple Splunk Universal Forwarder 구성

Splunk Universal Forwarder를 다운로드하세요.

<div class="content-ad"></div>

Splunk Downloads 페이지를 방문하여 Linux 배포판에 맞는 버전을 다운로드하세요. 또한, 링크가 있다면 wget을 사용하여 직접 다운로드할 수도 있습니다.

계정을 만들어야 합니다.

```js
wget -O splunkforwarder-<version>-Linux-x86_64.tgz "https://download.splunk.com/products/universalforwarder/releases/<version>/linux/splunkforwarder-<version>-Linux-x86_64.tgz"
```

![이미지](/assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_5.png)

<div class="content-ad"></div>

스플렁크 유니버설 포워더를 설치하세요:

```js
tar -xvf splunkforwarder-<version>-Linux-x86_64.tgz -C /opt
cd /opt/splunkforwarder/bin
./splunk start --accept-license
```

<img src="/assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_6.png" />

# 단계 2: 스플렁크 유니버설 포워더 설정

<div class="content-ad"></div>

Splunk Indexer를 설정해보세요:

```js
./splunk add forward-server 192.168.12.172:9997 -auth admin:changeme
```

모니터링할 디렉토리를 추가해보세요:

```js
./splunk add monitor /var/log/
```

<div class="content-ad"></div>

부팅할 때 Splunk Forwarder를 시작하도록 설정하십시오:

```sh
./splunk enable boot-start
```

# 입력/출력 파일 구성

# inputs.conf

<div class="content-ad"></div>

이 파일은 포워더가 모니터링하고 전달해야 하는 데이터를 지정하는 데 사용됩니다.

$SPLUNK_HOME/etc/system/local/ 디렉터리에 위치한 inputs.conf 파일을 생성하거나 편집하십시오.

```js
sudo vi /opt/splunkforwarder/etc/system/local/inputs.conf
```

다음 내용을 추가하여 /var/log/ 디렉터리를 모니터링하십시오:

<div class="content-ad"></div>


[monitor:///var/log/]
disabled = false
index = default
sourcetype = log


## outputs.conf

이 파일은 포워더가 데이터를 전송해야 하는 대상을 지정하는 데 사용됩니다.

$SPLUNK_HOME/etc/system/local/에 위치한 outputs.conf 파일을 만들거나 편집하세요.



<div class="content-ad"></div>



sudo vi /opt/splunkforwarder/etc/system/local/outputs.conf



다음 내용을 추가하여 데이터를 192.168.12.172로 전송하도록 설정하세요:



[tcpout]
defaultGroup = default-autolb-group

[tcpout:default-autolb-group]
server = 192.168.12.172:9997

[tcpout-server://192.168.12.172:9997]


Splunk UF를 다시 시작하세요.

<div class="content-ad"></div>

```shell
cd /opt/splunkforwarder/bin
sudo ./splunk restart
```

# 단계 3: Splunk 인덱서에서 입력 구성

Splunk 인덱서(192.168.12.172)에서:

참고: 이 단계에 대해 더 자세히 설명은 아래에 제공됩니다.

<div class="content-ad"></div>

스플렁크 웹 인터페이스에 로그인하세요.

![이미지](/assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_7.png)

설정으로 이동하여 `데이터 입력` > `전달된 데이터` > `새 전달된 데이터 입력`으로 이동하세요.

![이미지](/assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_8.png)

<div class="content-ad"></div>

새 데이터 입력을 로그로부터 전송하는 데이터 포워더를 구성하는 지시에 따르세요.

![이미지1](/assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_9.png)

![이미지2](/assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_10.png)

# 단계 4: 구성 확인

<div class="content-ad"></div>

- Universal Forwarder(192.168.184.133)에서:

```js
./splunk list forward-server
```

색인기(192.168.12.172)가 목록에 나열되어 활성화되어 있는지 확인해야 합니다.

# Windows에서 Splunk Enterprise 설정 - 호스트 A

<div class="content-ad"></div>

- 단계 1: Splunk Enterprise 다운로드 및 설치

Splunk Enterprise 다운로드:

- Splunk 다운로드 페이지에 방문합니다.
- Windows 버전을 선택하고 설치 프로그램을 다운로드합니다.

Splunk Enterprise 설치:

<div class="content-ad"></div>

- 다운로드한 설치 프로그램을 실행하세요.
- 설치 마법사 지시에 따르세요. 사용권 계약에 동의하고 설치 디렉토리를 선택해야 합니다.
- 관리자 사용자 이름과 암호를 설정하세요.

# 단계 2: Splunk Enterprise 시작 및 로그인

Splunk Enterprise 시작:

- 설치 후 Splunk Enterprise가 자동으로 시작됩니다. 그렇지 않은 경우 수동으로 시작할 수 있습니다. Splunk 설치 디렉토리로 이동하여 splunk.exe start를 실행하세요. (예: C:\Program Files\Splunk\bin)

<div class="content-ad"></div>

Splunk 웹 인터페이스에 로그인하세요:

- 웹 브라우저를 열고 http://localhost:8000 으로 이동하세요.
- 설치 중에 설정한 admin 자격 증명으로 로그인하세요.

# 단계 3: Splunk Enterprise 데이터 수신 설정

Splunk Enterprise에서 데이터 수신 활성화합니다 (위에서 언급한 이미지와 함께):

<div class="content-ad"></div>

- 설정으로 이동하여 `전달 및 수신` 수신 구성 ` 새 수신 포트를 구성하세요.
- 포트 9997 (또는 원하는 다른 포트)를 추가하세요.

구성 저장:

- 설정을 저장하여 Splunk Enterprise가 전달자로부터 수신되는 데이터를 포트 9997에서 수신하도록 설정하세요.

- 인덱서(192.168.12.172)에서:
- 인덱서 로그를 확인하거나 Splunk 웹 인터페이스에서 데이터를 검색하여 Universal Forwarder로부터 데이터를 수신 중인지 확인하세요.

<div class="content-ad"></div>

![image 1](/assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_11.png)

Or:

![image 2](/assets/img/2024-05-23-SendingPIWebLogstoSplunkIndexer_12.png)

We now have a PI website logging data, Kali-Purple using SCP at a regular interval pulling the log and finally sending the log via Splunk UF to the Splunk Indexer. We can now monitor all traffic from the website.

<div class="content-ad"></div>

위의 내용을 친근한 톤으로 한국어로 번역하면 다음과 같습니다.

다음 단계는 더 취약한 웹사이트를 만들고(그래서 우리가 해킹할 수 있게) 포트 접속, 더 나은 웹 로그, 보안 이벤트 로그 등 전반적인 PI 로그를 더 상세하게 추가하는 것입니다.

읽어 주셔서 감사합니다. 다른 누군가도 시도 중에 문제가 발생하면 부디 연락해 주세요. 제가 도와드릴 수 있는 대로 도와드리고 가능한 한 많은 조언을 제공할 테니까요! 즐기면서 진행하세요! — 스툭스
