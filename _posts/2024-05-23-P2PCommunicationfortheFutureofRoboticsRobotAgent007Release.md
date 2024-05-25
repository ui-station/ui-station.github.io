---
title: "로봇 공학의 미래를 위한 P2P 통신 로봇 에이전트 007 릴리스"
description: ""
coverImage: "/assets/img/2024-05-23-P2PCommunicationfortheFutureofRoboticsRobotAgent007Release_0.png"
date: 2024-05-23 17:10
ogImage: 
  url: /assets/img/2024-05-23-P2PCommunicationfortheFutureofRoboticsRobotAgent007Release_0.png
tag: Tech
originalTitle: "P2P Communication for the Future of Robotics: Robot Agent 0.0.7 Release"
link: "https://medium.com/merklebot/p2p-communication-for-the-future-of-robotics-robot-agent-0-0-7-release-c3b3790bf7cb"
---


<img src="/assets/img/2024-05-23-P2PCommunicationfortheFutureofRoboticsRobotAgent007Release_0.png" />

Merklebot에서는 로봇 플릿 운영자를 위한 데이터 관리 및 개발 도구를 만들고 있습니다. 우리는 다음 15년 안에 100억 대 이상의 로봇이 나올 가능성이 꽤 높다고 믿고 있으며, 이 비전을 실현하기 위해 견고하고 확장 가능하며 안전한 인프라 및 통신 스택을 구축하고 있습니다.

## 로봇 에이전트란?

몇 달 전에 Digital Black Box를 출시했습니다. 이 서비스는 로봇에서 로그, 카메라 피드 및 포인트 클라우드와 같은 데이터를 안전하고 저렴한 분산 저장 네트워크인 Filecoin에 백업하는 기능을 제공합니다. Digital Black Box는 다음으로 구성됩니다:

<div class="content-ad"></div>

- 가장자리에서 실행되는 로봇 에이전트

2. 클라우드 연결을 제공하는 Merklebot 플랫폼

첫 번째 버전의 로봇 에이전트는 가장자리 장치에서 데이터를 수집하고 백업하는데 사용되었습니다. 나중에는 Merklebot 플랫폼을 구축하여 Docker 컨테이너를 가장자리 장치에 배포하고 그 위에서 실행 중인 에이전트에 기능을 추가했습니다.

## 로봇 에이전트 0.0.7

<div class="content-ad"></div>

- 👀 네트워크에서 다른 로봇의 mDNS 자동 탐색
- 🤖↔🤖 로봇 간의 Libp2p 메시징

안녕하세요! 오늘은 로봇 에이전트 0.0.7의 새로운 릴리스를 Libp2p 통신 모듈과 함께 발표합니다. 최신 버전의 로봇 에이전트는 이제 Libp2p 피어 탐색, 주소 설정 및 메시지 전송을 갖추고 있습니다. 네트워크를 유연한 토폴로지로 설정할 수 있으며, 위치 변경이나 다른 매개변수를 바꿀 때마다 통신 스택을 구성할 필요가 없습니다. 이제 저희의 Github에서 공개되어 있습니다.

로봇 에이전트 0.0.7를 설치하면 기기가 다른 에이전트를 자동으로 찾아 정보(상태, 이벤트)를 서로 전달할 수 있습니다. 중앙 서버에 접근하지 않고도 기기 간 통신을 수행할 수 있습니다. 이 도구는 MIT 라이선스로 완전히 오픈 소스이며, 여러분의 필요에 맞게 이 도구를 자유롭게 사용할 수 있습니다.

## Merklebot 플랫폼

<div class="content-ad"></div>

Merklebot 플랫폼은 Robot Agents를 여러 기기 필릿에 중앙 집중식으로 롤아웃할 수 있도록 제공하여 로봇, 센서, 장비 및 기타 기계와 같은 다양한 기기를 안전하고 쉽게 관리할 수 있습니다. 전체 Robot Agents 필릿에 코드 및 Docker 컨테이너를 배포하고 데이터를 관리할 수 있습니다.

Merklebot 없이:

- 로봇에 연결
- 로봇을 vpn 네트워크에 추가하거나 NAT 트래버셜을 우회하는 다른 방법
- 🧑‍💻 코드를 작성합니다
- ssh를 통해 로봇에 연결
- ⏳ git pull
- ⏳ docker build 및 run my-best-code
- ⏳ 로봇 로그 보기
- ⏳ 데이터(카메라 비디오 등)를 가져오기 위해 scp 실행
- ⏳ 취합할 위치에 저장

Merklebot을 사용하면:

<div class="content-ad"></div>

- 로봇에 연결하세요
- install.sh를 실행하세요
- 🧑‍💻코드를 작성하세요
- 🚀플랫폼에서 한 번 클릭하거나 API를 호출하세요
- 😎로그, 비디오 및 기타 데이터를 플랫폼에서 확인하세요

## 다음은 무엇인가요

Merklebot 팀은 오픈 소스 Agent의 기능을 계속 향상시키고 Merklebot 플랫폼에 더 많은 유틸리티를 추가할 새로운 기능 세트에 현재 작업 중입니다.

- 에이전트를 위한 향상된 CLI 도구 (일반 명령 인터페이스). SSH 우회, 구성 업데이트 전달 및 기기에서 편집 없이 에이전트를 관리하세요. 현재 Merklebot 플랫폼에서 활성화되어 있지만, 이를 오픈 소스로 만들 계획을 하고 있어요!
- 오픈 소스 데이터 관리 및 시각화 도구와의 통합. 로그 수집 및 저장, 데이터 작업 실행 및 시각화 생성을 할 수 있어요.
- Jenkins와 같은 CI/CD (지속적 통합 지속적 배포) 도구와의 통합. 자동으로 장치에 새 소프트웨어 릴리스 설치하세요.


<div class="content-ad"></div>

우리 새로운 에이전트를 확인해보세요! 피드백을 기다리고 있어요.

그리고 기기 편대를 관리해야 한다면, Merklebot Platform으로 시작해보세요.