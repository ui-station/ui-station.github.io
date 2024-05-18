---
title: "NVIDIA Jetson Nano에서 Intel RealSense Depth Camera를 사용하여 ROS2 Humble를 활용하기"
description: ""
coverImage: "/assets/img/2024-05-18-NVIDIAJetsonNanowithIntelRealSenseDepthCameraUsingROS2Humble_0.png"
date: 2024-05-18 19:15
ogImage: 
  url: /assets/img/2024-05-18-NVIDIAJetsonNanowithIntelRealSenseDepthCameraUsingROS2Humble_0.png
tag: Tech
originalTitle: "NVIDIA Jetson Nano with Intel RealSense Depth Camera Using ROS2 Humble"
link: "https://medium.com/@kabilankb2003/nvidia-jetson-nano-with-intel-realsense-depth-camera-using-ros2-humble-c5926566a4d8"
---


<img src="/assets/img/2024-05-18-NVIDIAJetsonNanowithIntelRealSenseDepthCameraUsingROS2Humble_0.png" />

이 튜토리얼에서는 NVIDIA Jetson Nano와 Intel RealSense Depth Camera를 ROS2 Humble을 사용하여 어떻게 연결하는지 살펴볼 것입니다. 이 설정은 실시간 인식 및 처리 기능이 필요한 로봇 응용 프로그램에 강력합니다. Jetson Nano는 RealSense 카메라에서 데이터를 처리하는 데 필요한 계산 성능을 제공하며, ROS2 Humble은 로봇 소프트웨어 개발과 관리를 위한 견고한 프레임워크를 제공합니다.

<img src="/assets/img/2024-05-18-NVIDIAJetsonNanowithIntelRealSenseDepthCameraUsingROS2Humble_1.png" />

# Prerequisites

<div class="content-ad"></div>

시작하기 전에 다음 구성 요소가 있는지 확인하십시오:

- NVIDIA Jetson Nano 운영 체제 이미지가 설치된 Ubuntu 20.04
- Intel RealSense Depth Camera (예: D435i)
- Jetson Nano에 설치된 ROS2 Humble
- RealSense 카메라를 Jetson Nano에 연결하기 위한 USB 3.0 케이블
- 필요한 패키지를 다운로드하기 위한 인터넷 연결

ROS2 RealSense 패키지 설치

```js
sudo apt install ros-humble-realsense2-camera
```

<div class="content-ad"></div>

RealSense 노드를 시작해주세요:

RealSense 노드를 시작하는 런치 파일을 만들어 보세요. 새로운 파일 realsense_launch.py를 만들어 주세요:

```python
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        Node(
            package='realsense2_camera',
            executable='realsense2_camera_node',
            name='realsense2_camera',
            output='screen',
            parameters=[{
                'enable_depth': True,
                'enable_infra1': True,
                'enable_infra2': True,
                'enable_color': True,
            }],
        ),
    ])
```

란치 파일을 실행하세요:

<div class="content-ad"></div>

<table>
    <tr>
        <td>![이미지](/assets/img/2024-05-18-NVIDIAJetsonNanowithIntelRealSenseDepthCameraUsingROS2Humble_2.png)</td>
    </tr>
</table>

```js
ros2 launch your_package_name realsense_launch.py
```

rqt에서 데이터 시각화:

![이미지](/assets/img/2024-05-18-NVIDIAJetsonNanowithIntelRealSenseDepthCameraUsingROS2Humble_3.png)

<div class="content-ad"></div>

rqt에 RealSense 데이터 추가하기:

![이미지](/assets/img/2024-05-18-NVIDIAJetsonNanowithIntelRealSenseDepthCameraUsingROS2Humble_4.png)

- 새로운 DepthCloud 표시 추가 및 토픽을 /camera/depth/color/points로 설정합니다.
- 이미지 표시 추가 및 토픽을 /camera/color/image_raw로 설정합니다.

![이미지](/assets/img/2024-05-18-NVIDIAJetsonNanowithIntelRealSenseDepthCameraUsingROS2Humble_5.png)

<div class="content-ad"></div>

깊이 이미지

![깊이 이미지](https://miro.medium.com/v2/resize:fit:1400/1*lqW7eh_9bQwt7jdi9FNFDg.gif)

# 깊이 이미지란?

깊이 이미지는 각 픽셀이 카메라에서 씬 내 객체까지의 거리를 나타내는 회색조 이미지입니다. 색상 정보를 포함하는 일반적인 RGB 이미지와 달리, 깊이 이미지는 깊이 정보를 인코딩하여 환경의 3D 구조를 인식할 수 있게 합니다.

<div class="content-ad"></div>

# 주요 주제 및 메시지

ROS 2에서 RealSense 카메라의 깊이 이미지는 특정 토픽, 보통은 /camera/depth/image_raw에 발행됩니다. 깊이 이미지의 메시지 유형은 sensor_msgs/Image입니다. 주요 요소를 살펴보겠습니다:

- 토픽: /camera/depth/image_raw
- 메시지 유형: sensor_msgs/Image

## sensor_msgs/Image 메시지

<div class="content-ad"></div>

sensor_msgs/Image 메시지에는 여러 필드가 포함되어 있지만, 깊이 이미지에 가장 관련있는 필드는 다음과 같습니다:

- header: 타임스탬프와 좌표 프레임 정보를 포함하는 표준 ROS 메시지 헤더입니다.
- height: 이미지의 높이(픽셀 단위).
- width: 이미지의 너비(픽셀 단위).
- encoding: 이미지 데이터의 인코딩 유형, 예를 들어 16비트 무부호 단일 채널 이미지의 경우 16UC1입니다.
- is_bigendian: 이미지 데이터가 빅엔디안 바이트 순서로 저장되어 있는지 여부.
- step: 바이트 단위의 전체 행 길이.
- data: 바이트 배열로 저장된 실제 픽셀 데이터.

## 깊이 이미지 처리

깊이 이미지는 애플리케이션에 따라 다양한 방식으로 처리할 수 있습니다. 일반적인 작업은 다음과 같습니다:

<div class="content-ad"></div>

- 객체 감지 및 인식: 3D 모양에 기반하여 객체를 식별하고 분류합니다.
- 장애물 피하기: 로봇의 경로에서 장애물을 감지하고 피하기 위해 깊이 정보를 활용합니다.
- 3D 매핑: 내비게이션 목적으로 환경의 3D 지도를 작성합니다.

# 결론

위 단계를 따라서 NVIDIA Jetson Nano가 ROS2 Humble을 사용하여 Intel RealSense Depth Camera와 연결되어 있어야 합니다. 이 설정은 내비게이션, 객체 감지 등을 위해 실시간 깊이 및 색상 데이터를 활용하여 다양한 로봇 응용 프로그램에 매우 유용합니다. 로봇 시스템의 능력을 확장하기 위해 다양한 ROS2 노드 및 패키지를 실험해보세요.

문제가 발생하거나 추가 질문이 있으시면 아래에 댓글을 남겨 주세요!