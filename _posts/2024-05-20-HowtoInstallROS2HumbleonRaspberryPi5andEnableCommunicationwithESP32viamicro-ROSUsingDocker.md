---
title: "Raspberry Pi 5에 ROS2 Humble을 설치하고 Docker를 사용하여 micro-ROS를 통해 ESP32와 통신하는 방법"
description: ""
coverImage: "/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_0.png"
date: 2024-05-20 19:55
ogImage: 
  url: /assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_0.png
tag: Tech
originalTitle: "How to Install ROS2 Humble on Raspberry Pi 5 and Enable Communication with ESP32 via micro-ROS Using Docker"
link: "https://medium.com/@antonioconsiglio/how-to-install-ros2-humble-on-raspberry-pi-5-and-enable-communication-with-esp32-via-micro-ros-2d30dfcf2111"
---


<img src="/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_0.png" />

어려움을 겪은 끝에, Raspberry Pi 5를 ESP32 마이크로컨트롤러에 연결하는 데 성공했습니다. 목표는 Raspberry Pi 5와 ESP32-WROOM-32 마이크로컨트롤러 간에 마이크로-ROS를 통한 통신을 활성화하는 것이었습니다.

다른 사람들이 시간을 절약할 수 있도록, 이 상세한 튜토리얼을 작성하기로 결정했습니다. 아래 단계에 따라 따라할 수 있습니다:

- Raspberry Pi 5에 Ubuntu 23.10 Server 설치
- Raspberry Pi 5에 Docker 설치
- ROS2 Humble 설치를 위한 Dockerfile 생성
- ROS2 Humble 설치(Dockerfile 내에서)
- 마이크로-ROS 설치(Dockerfile 내에서)
- Docker 이미지 빌드
- ROS2 Humble 도커 컨테이너 실행
- 마이크로-ROS 예제 빌드 및 플래시
- 마이크로-ROS 에이전트 생성 및 실행

<div class="content-ad"></div>

## 라즈베리 파이5에 우분투 23.10 서버 설치하기

우선, Raspberry Pi Imager를 사용하여 라즈베리 파이 5에 우분투 23.10 서버 운영 체제를 설치했습니다. 이를 통해 화면을 사용하지 않고 SSH를 통해 장치에 연결할 수 있었습니다.

## Docker 설치하기

SSH를 통해 Raspberry Pi 5에 액세스한 후 Docker를 설치했습니다. ROS2의 Humble 배포의 지원하는 운영 체제인 Ubuntu 22.04를 사용하기 위해 Docker를 사용하면 Raspberry Pi 5에서 호환 환경을 만들 수 있습니다. (https://docs.docker.com/engine/install/ubuntu/)

<div class="content-ad"></div>


```js
sudo apt-get update

sudo apt-get install ca-certificates curl

sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo usermod -aG docker $USER
```
## Raspberry Pi5용 ROS2 Humble 설치를 위한 Dockerfile

원하는 ROS2 버전이 이미 포함된 Docker 이미지로 시작할 수 있습니다. 제 경우에는 필요한 Ubuntu 버전인 22.04(Jammy)에서 시작하는 Dockerfile을 만들기로 결정했습니다.

```js
FROM ubuntu:jammy 

RUN locale  # UTF-8 확인
RUN apt update && apt install locales -y
RUN locale-gen it_IT it_IT.UTF-8
RUN update-locale LC_ALL=it_IT.UTF-8 LANG=it_IT.UTF-8
RUN export LANG=it_IT.UTF-8

# 지역 시간대 설정
ENV ROS_VERSION=2
ENV ROS_DISTRO=humble
ENV ROS_PYTHON_VERSION=3
ENV TZ=Europe/Rome
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
```


<div class="content-ad"></div>

도커 파일은 Ubuntu 22.04의 기본 이미지로 시작합니다. 그 다음으로 로캘 및 시간대가 설정됩니다. 저의 경우에는 이탈리아로 설정되어 있으며, 이는 ROS2를 설치하는 데 필요합니다.

이 설정 이후에는 ROS2 패키지를 설치할 때 시간대를 입력하지 않고도 필요한 환경 변수를 몇 개 설정했습니다.

Markdown 형식으로 표를 바꿔주겠습니다:


| 명령어                                     | 설명                                                                           |
|-------------------------------------------|--------------------------------------------------------------------------------|
| RUN apt install software-properties-common -y | 소프트웨어 속성 공통 부분 설치                                             |
| RUN add-apt-repository universe            | Universe 저장소 추가                                                         |
| RUN apt update && apt install curl -y      | 업데이트 후 curl 설치                                                          |
| RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg | ROS 키 다운로드 |
| RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null | ROS2 리스트 추가 |
| RUN apt update && apt upgrade -y              | 업데이트 및 업그레이드                                                               |


이 부분의 도커 파일에서는 ROS2 설치를 위해 시스템을 구성하며, 필요한 저장소를 추가하고 시스템 패키지를 업데이트합니다(설치에 관한 공식 가이드는 여기에서 확인할 수 있습니다: https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html).

<div class="content-ad"></div>


<img src="/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_1.png" />

시스템을 구성한 후에는 ROS2 Humble 및 micro-ROS를 설치할 준비가 되었습니다. 두 개의 스크립트를 사용했어요: install_ros2.sh와 install_microros_esp32.sh입니다. 이 스크립트들은 작업 디렉토리인 /ros2_project에 복사되고 실행 가능하게 만들었습니다.

```js
WORKDIR /ros2_project
COPY scripts/*.sh /ros2_project/

RUN chmod +x ./*.sh
RUN ./install_ros2.sh
RUN ./install_microros_esp32.sh
```

## ROS2 Humble 설치


<div class="content-ad"></div>

```js
# install_ros2.sh
#!/bin/bash

# ROS2 베이스 및 개발 도구 설치
apt install ros-humble-ros-base -y
apt install ros-dev-tools -y

# 셸을 설정하여 ROS2 설정 파일을 자동으로 소스로 지정합니다.
# echo 'source /opt/ros/humble/setup.bash' >> ~/.bashrc (optional)
echo 'source /usr/share/colcon_argcomplete/hook/colcon-argcomplete.bash' >> ~/.bashrc
```

## 마이크로-ROS 설치

micro-ROS를 설치하고 사용하려면 먼저 ESP32 칩에 호환되는 소프트웨어 개발 환경을 설정해야 합니다. 저는 micro-ROS 개발자들에 의해 테스트된 버전 5.2를 선택했습니다.

```js
# install_espressif.sh
#!/bin/bash

# 'esp' 폴더가 있는지 확인합니다.
if [ ! -d ~/esp ]; then

    # 패키지 목록을 업데이트하고 필요한 종속성을 설치합니다.
    apt-get update -y
    apt-get install git wget flex bison gperf python3 python3-pip python3-venv cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0 -y
    
    # ESP 개발을 위한 디렉토리를 생성하고 ESP-IDF 리포지토리를 복제합니다.
    mkdir -p ~/esp
    cd ~/esp
    git clone -b v5.2 --recursive https://github.com/espressif/esp-idf.git

    # ESP-IDF 디렉토리로 이동하여 ESP32 도구체인을 설치합니다.
    cd ~/esp/esp-idf
    ./install.sh esp32

    # IDF_PATH 환경 변수를 설정하고 지속적으로 유지합니다.
    export IDF_PATH=$HOME/esp/esp-idf
    echo export IDF_PATH=$HOME/esp/esp-idf >> /root/.bashrc
    . $IDF_PATH/export.sh
else
    echo "'esp' 폴더가 이미 있습니다."
fi
```

<div class="content-ad"></div>

```js
# install_microros_esp32.sh

#!/bin/bash
# ESP32 설정 스크립트 실행하기
./install_espressif.sh

# 작업 공간 생성 및 micro-ROS 도구 다운로드하기
git clone -b $ROS_DISTRO https://github.com/micro-ROS/micro_ros_espidf_component.git

# 의존성 설치하기
export IDF_PATH=$HOME/esp/esp-idf
source $IDF_PATH/export.sh && pip3 install catkin_pkg lark-parser colcon-common-extensions
```

## 도커 이미지 빌드

이 단계에서 우리의 Dockerfile은 다음과 같이 나타납니다:

```js
FROM ubuntu:jammy 

RUN locale  # UTF-8 확인
RUN apt update && apt install locales -y
RUN locale-gen it_IT it_IT.UTF-8
RUN update-locale LC_ALL=it_IT.UTF-8 LANG=it_IT.UTF-8
RUN export LANG=it_IT.UTF-8

# 시간대 설정
ENV ROS_VERSION=2
ENV ROS_DISTRO=humble
ENV ROS_PYTHON_VERSION=3
ENV TZ=Europe/Rome
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# 필요한 리포지토리 추가 및 시스템 패키지 업데이트
RUN apt install software-properties-common -y
RUN add-apt-repository universe
RUN apt update && apt install curl -y
RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null
RUN apt update && apt upgrade -y

# 작업 디렉토리 지정하고 스크립트 복사
WORKDIR /ros2_project
COPY scripts/*.sh /ros2_project/

# 스크립트를 실행 가능하도록 하고 실행하기
RUN chmod +x ./*.sh
RUN ./install_ros2.sh
RUN ./install_microros_esp32.sh
```

<div class="content-ad"></div>

도커 파일이 있는 디렉토리에서 다음 명령을 실행하세요: docker build . -t `저장소_이름:태그`

출력은 다음과 같이 나타납니다:

![이미지](/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_2.png)

생성된 이미지를 확인하려면 "docker images" 명령을 사용하여 사용 가능한 모든 도커 이미지를 볼 수 있습니다.

<div class="content-ad"></div>


![이미지](/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_3.png)

## ROS2 Humble 도커 컨테이너 실행하기

이미지를 만든 후에는 해당 이미지를 사용하여 컨테이너를 실행할 수 있습니다. 컨테이너는 가벼우며 응용 프로그램과 의존성을 실행하는 격리된 환경으로, 가상 머신과 유사하지만 오버헤드가 적습니다.

컨테이너를 실행하려면:


<div class="content-ad"></div>

```bash
docker run -it \
    --name=ros2_humble \
    --net=host \
    --privileged \
    -v /dev:/dev \
    ros2rover:humble-pi5-esp32-v1 \
    bash
```

여기서:

- -it: 의사-TTY를 할당하고 STDIN을 열어 놓아 컨테이너와 상호 작용할 수 있게 함.
- --name=ros2_humble: 컨테이너의 이름을 ros2_humble로 지정함
- --net=host: 호스트와 네트워크 네임스페이스를 공유하여 컨테이너가 호스트 네트워크 인터페이스를 사용할 수 있게 함.
- --privileged: 컨테이너를 권한 부여 모드로 실행하여 호스트의 모든 장치에 액세스할 수 있게 함.
- -v /dev:/dev: 호스트의 /dev 디렉토리를 컨테이너에 마운트하여 컨테이너가 호스트의 모든 장치 파일에 액세스할 수 있게 함.
- ros2rover:humble-pi5-esp32-v1: 컨테이너 생성 시 사용할 Docker 이미지의 이름을 지정함.
- bash: 컨테이너 내에서 실행할 명령을 지정함. 이 경우에는 Bash 셸을 시작함.

이 명령을 실행하면 컨테이너 내의 작업 디렉토리에서 Bash 셸 프롬프트가 열리며, 컨테이너 환경과 직접 상호 작용할 수 있게 됩니다.


<div class="content-ad"></div>

## 미크로-ROS int32_publisher 예제 빌드 및 플래시

![예제 이미지](/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_4.png)

기능을 확인하려면 복제된 micro-ROS 저장소에 포함된 예제 중 하나를 빌드할 수 있습니다. 다음은 따라야 할 단계입니다:

- 다음 명령어로 예제 폴더로 이동하세요:

cd micro_ros_espidf_component/examples/int32_publisher

- ESP-IDF를 위해 소프트웨어 개발 환경을 활성화하세요:

. $IDF_PATH/export.sh

- 빌드할 타겟을 설정하세요 (이 경우 ESP32): 

idf.py set-target esp32


<div class="content-ad"></div>


![이미지](/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_5.png)

- 와이파이 연결 및 IP 호스트 대상 설정 (Raspberry Pi5 IP):
runidf.py menuconfig을 실행한 후 micro-ROS 설정으로 이동하여 micro-ROS Agent IP를 호스트 IP로 설정하고 (Raspberry Pi5 IP를 알고 싶다면 bash 쉘에서 ifconfig를 실행하세요) Raspberry Pi5와 ESP32가 연결될 네트워크의 SSID 및 비밀번호로 WiFi 구성을 설정하세요.
그런 다음 “S”로 저장하고 “Q”로 나가세요.

![이미지](/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_6.png)

![이미지](/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_7.png)


<div class="content-ad"></div>


![image](/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_8.png)

- 명령어를 사용하여 예제를 빌드하세요: idf.py build

![image](/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_9.png)

- micro-USB 케이블을 사용하여 ESP32 보드를 라즈베리 파이5에 연결하고 읽기 및 쓰기 권한이 있는지 확인하세요.
연결하는 USB 포트에 할당된 장치 이름을 확인하려면 해당 포트에 연결하기 전에 다음 명령어를 실행하세요: journalctl --follow (라즈베리 파이5 셸에서 실행, 컨테이너에서는 실행하지 마세요).
장치 이름을 알면 다음 명령어를 사용하여 라즈베리 파이5가 읽고 쓸 수 있게 설정할 수 있습니다: 내 경우에는 `device name`이 ttyUSB0과 같습니다.
sudo chmod 666 /dev/`device name`


<div class="content-ad"></div>


![이미지](/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_10.png)

- 마지막으로 ESP32 보드에 펌웨어를 플래시하는 방법입니다: idf.py flash
여러 USB 장치가 연결된 경우 포트를 지정할 수 있습니다: idf.py -p /dev/ttyUSB0 flash

## 마이크로-ROS 에이전트를 생성하고 ESP32에서 발행된 메시지를 읽어보세요

마이크로-ROS 에이전트는 ESP32의 마이크로-ROS 노드와 ROS 2 네트워크 간의 다리 역할을 합니다. 통신을 수립하려면 적절한 이미지를 사용하여 Docker를 통해 마이크로-ROS 에이전트를 실행하세요:


<div class="content-ad"></div>


# udp4은 IPv4 네트워크 상에서 통신을 위해 UDP를 사용하는 것을 특히 나타냅니다.
도커 실행 -it --rm --net=host microros/micro-ros-agent:humble udp4 --port 8888 -v6


![이미지](/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_11.png)

ESP32 보드를 전원을 다시 켜고 끈 후에는, micro-ROS Agent 컨테이너 쉘에서 다음 출력을 볼 수 있어야 합니다:

![이미지](/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_12.png)


<div class="content-ad"></div>

ROS2 Humble 컨테이너에서 ROS2 Humble 환경을 소스로 지정한 다음에는 다음을 작성하세요: ros2 topic list.
이제 /freertos_int32_publisher 토픽이 나타날 것이며, 여기에 ESP32가 정수 (int32) 데이터를 발행 중입니다.

![이미지](/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_13.png)

이제 해당 토픽에서 발행된 메시지를 읽을 수 있습니다. ESP32가 /freertos_int32_publisher 토픽에서 발행한 메시지를 구독하고 표시하려면 다음 명령을 사용하여 ROS2 Humble 컨테이너 내에서 메시지를 구독하세요: ros2 topic echo /freertos_int32_publisher

![이미지](/assets/img/2024-05-20-HowtoInstallROS2HumbleonRaspberryPi5andEnableCommunicationwithESP32viamicro-ROSUsingDocker_14.png)

<div class="content-ad"></div>

# 결론

이렇게까지 와주셔서 축하드립니다! 이 포괄적인 안내를 따라가면, Docker를 통해 ROS2 Humble과 micro-ROS를 활용하여 Raspberry Pi 5와 ESP32 마이크로컨트롤러 간의 통신을 성공적으로 활성화할 수 있습니다.

이 설정은 Docker가 제공하는 유연성과 격리 기능을 활용하여 안정적인 환경을 제공하여 마이크로-ROS 애플리케이션을 개발하고 테스트할 수 있습니다.

이 단계를 따라가면, 이제 마이크로-ROS를 활용하여 Raspberry Pi 5와 ESP32 간의 통신을 브릿지하여 다양한 사물 인터넷(IoT) 애플리케이션에 활용할 수 있습니다.

<div class="content-ad"></div>

행운을 빌며 코딩을 즐기세요! ROS2, Raspberry Pi 5 및 ESP32로 가능한 범위를 넓히는 실험을 망설이지 마세요.

저와 연락해요: [https://www.linkedin.com/in/antonioconsiglio/](https://www.linkedin.com/in/antonioconsiglio/)