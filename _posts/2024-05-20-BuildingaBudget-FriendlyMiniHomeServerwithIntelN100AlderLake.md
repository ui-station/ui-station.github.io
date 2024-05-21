---
title: "인텔 N100 알더 레이크를 활용한 예산 친화적 미니 홈 서버 구축하기"
description: ""
coverImage: "/assets/img/2024-05-20-BuildingaBudget-FriendlyMiniHomeServerwithIntelN100AlderLake_0.png"
date: 2024-05-20 19:49
ogImage: 
  url: /assets/img/2024-05-20-BuildingaBudget-FriendlyMiniHomeServerwithIntelN100AlderLake_0.png
tag: Tech
originalTitle: "Building a Budget-Friendly Mini Home Server with Intel N100 Alder Lake"
link: "https://medium.com/@vcoder/building-a-budget-friendly-mini-home-server-with-intel-n100-alder-lake-392d4bb440f3"
---


하이퍼링크를 Markdown 형식으로 변경하십시오.

[Build a Budget-Friendly Mini Home Server with Intel N100 Alder Lake](/assets/img/2024-05-20-BuildingaBudget-FriendlyMiniHomeServerwithIntelN100AlderLake_0.png)

<div class="content-ad"></div>


![Building a Budget-Friendly Mini Home Server with Intel N100 Alder Lake](/assets/img/2024-05-20-BuildingaBudget-FriendlyMiniHomeServerwithIntelN100AlderLake_1.png)

그러나 리눅스 배포판을 탐색하고 Windows에서 특정 응용 프로그램을 실행하는 과정에서 라즈베리 파이의 제한 사항을 만나게 되었습니다. 또한 해당 장치는 Plex를 재색인하거나 NextCloud에 저장된 수천 개의 파일을 찾아보는 등 무거운 작업을 동시에 처리하는 데 필요한 전원이 부족합니다.

최근 라즈베리 파이 5가 출시된 후, 라즈베리 파이를 기반으로 한 새로운 서버를 소유하고 운영할 열정이 사라지고 있습니다. 새로운 장치가 기술적 한계에 직면하면서도 더 높은 가격표를 갖게 되었습니다. 다양한 하드웨어 옵션을 탐색한 후, 최근 출시된 Intel Alder Lake N100이 매력적인 선택이라는 것을 발견했습니다:

- 전체 미니 하드웨어 세트가 약 150달러 정도로 저렴하게 제공됩니다. 이는 전체 라즈베리 파이 5 세트의 가격과 비슷합니다.
- N100 CPU는 상대적으로 낮은 6W의 TDP로 운영하면서 라즈베리 파이 5보다 훨씬 더 많은 성능을 제공합니다. 또한 Intel x86-64 아키텍처는 iGPU 통과 기능을 지원하는 VM을 지원합니다.
- N100 칩은 최대 32GB의 DDR5 RAM을 지원하며, 라즈베리 파이 5의 8GB DDR4 제한을 크게 넘어섭니다.
- 일부 N100 세트는 2.5G로 클럭된 듀얼 LAN 포트를 제공하여 빠른 파일 전송과 연결 중복성을 제공합니다.
- N100은 주로 NVMe Gen 3+ 스토리지를 완벽하게 지원하는 마더보드와 번들로 제공되며, 라즈베리 파이 5의 SD 카드, USB 3 또는 PCIe 솔루션에 비해 훨씬 높은 속도의 읽기/쓰기 속도를 제공합니다.


<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-BuildingaBudget-FriendlyMiniHomeServerwithIntelN100AlderLake_2.png" />

## 하드웨어 옵션

Intel N100 설정에 대한 여러 예산 친화적인 옵션이 있습니다. 아마존에서 이 미니 컴퓨터와 같은 제품이 제공됩니다.

또는 일부 사용자는 미니 ITX 마더보드에 N100 설정을 구축하고 RAID 구성을 위한 충분한 저장소를 추가할 수도 있습니다.

<div class="content-ad"></div>

저에게 있어 간단함이 가장 중요했어요. 전 최소한의 설정만 선택하고 블랙 프라이데이 기간에 SSD와 RAM을 별도로 구매해 좋은 혜택을 누렸어요.

![이미지](/assets/img/2024-05-20-BuildingaBudget-FriendlyMiniHomeServerwithIntelN100AlderLake_3.png)

이 장치는 이동하기 매우 쉽고, 발열이 적고, 소음이 없는 선풍기가 있어요. 보통 조건에서 전력 소비가 10W 미만으로 유지돼요.

![이미지](/assets/img/2024-05-20-BuildingaBudget-FriendlyMiniHomeServerwithIntelN100AlderLake_4.png)

<div class="content-ad"></div>

## 운영 체제 옵션

많은 보통 사용자들에게 미니 데스크톱을 사용하는 주요 목적은 외부 4K 모니터에 연결하면서 Windows 10 또는 11을 사용하여 일상 업무를 처리하는 것입니다. 그러나 제 경우에는 제 미니 데스크톱을 가상 머신을 여러 대 실행할 수 있는 헤드리스 홈 서버로 변신하겠다는 선택을 했습니다.

저는 Proxmox, 일반적인 Debian/Ubuntu 설정에 VM 응용 프로그램을 적용하거나 TrueNAS 또는 Unraid와 같은 전용 NAS/홈 서버 운영 체제를 실행하는 여러 시나리오를 탐구했습니다.

각 시나리오는 고유한 학습 곡선을 제시하여 하드웨어 제약 사항을 이해하고 최적의 VM 성능을 위해 패스스루를 설정하는 데 상당한 시간을 요구했습니다. 특히 하드웨어 렌더링 및 디코딩과 같은 그래픽 집약적 작업에 대한 최적의 성능을 위해.

<div class="content-ad"></div>

## Unraid을 선택했어요

많은 실무 경험과 탐험 끝에, 저는 결국 Unraid를 선택했습니다. 이 플랫폼은 훌륭한 생태계와 45% 할인을 제공하여 경제적인 솔루션으로 제공되는 무어스럽고 매끈한 경험을 제공했습니다. 하드웨어 구성이 약 2개의 VM과 약 열 개의 컨테이너만 지원하는 경우, Unraid의 기능은 제 요구에 완벽하게 부합했어요.

다른 사용자들이 무료이면서 오픈 소스 옵션을 찾는다면, TrueNAS도 좋은 선택이 될 거예요.

![이미지](/assets/img/2024-05-20-BuildingaBudget-FriendlyMiniHomeServerwithIntelN100AlderLake_5.png)

<div class="content-ad"></div>

새 서버를 설정하는 것은 소프트웨어 설치, 백업 복원 및 예약된 작업 설정으로 인해 시간이 걸릴 수 있습니다.

## 내 미니 홈 서버 조직하는 방법

새 서버에서는 모든 응용 프로그램을 Docker 컨테이너로 완전히 실행하고 대부분 VM 내에서 실행하기로 결정했습니다. Unraid는 이 기능을 제공하지만 Proxmox만큼 VM에 최적화되어 있지 않습니다.

하드웨어의 성능에도 불구하고, NextCloud 및 Plex/Jellyfin과 같은 무거운 작업을 호스트에서 직접 실행하는 것이 더 효율적인 것으로 판단했습니다. 나머지 응용 프로그램의 경우, 가벼운 Linux VM 상에서 Docker 컨테이너로 실행하며 특히 Headless Debian에서 실행하여 간소화되고 효율적인 설정을 보장합니다.

<div class="content-ad"></div>

Unraid OS에 대해 배울 수 있는 귀중한 자원은 Spaceinvader One YouTube 채널입니다. 기본 설정부터 고급 기능까지 포괄적인 자습서를 제공하여 모든 능력 수준의 사용자에게 훌륭한 자원이 됩니다.

## 지금까지 배운 것

Unraid는 주로 NAS (Network Attached Storage) 서버용으로 설계되어 있으며, RAID 구성, 캐시 읽기/쓰기 및 네트워크 파일 공유를 지원합니다. Slackware 기반으로 만들어진 Unraid는 특정 명령줄 인터페이스 (CLI) 기능에 대한 액세스를 제한하여 우연한 시스템 변경의 위험을 최소화함으로써 사용자 경험을 간소화합니다. 그러나 사용자는 플러그인을 설치하여 추가 기능을 해제할 수 있습니다.

Unraid는 앱 스토어에서 사용 가능한 다양한 응용 프로그램을 자랑하며, 이 중 많은 것들이 Docker 컨테이너입니다. 이러한 컨테이너는 운영 체제를 수정할 필요 없이 쉽게 배포하고 실행할 수 있어 Unraid의 서버 관리에 대한 사용자 친화적인 접근 방식을 더욱 강화합니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-BuildingaBudget-FriendlyMiniHomeServerwithIntelN100AlderLake_6.png" />

## 안전하고 빠른 홈 서버에 대한 내 개인 팁

소프트웨어 측면에 대해 심층적으로 다루지는 않겠습니다. 이미 전에 라즈베리 파이에 대한 이야기를 다뤘기 때문입니다. 그러나 새로운 홈 서버에서 배운 중요한 세 가지 교훈을 강조하고 싶습니다.

1. 클라우드플레어 제로 트러스트 워프 앱을 통한 원격 접속: 클라우드플레어 제로 트러스트 워프 앱을 클라우드플레어 터널과 호스트 또는 가상 머신에 설치하여 전 세계 어디에서나 안전하게 홈 서버에 원격 액세스할 수 있습니다. 이 설정은 무료뿐만 아니라 설정도 매우 쉽습니다.

<div class="content-ad"></div>

2. 홈 IP로 VM 활용하기: VPN을 통해 내 집 네트워크에 연결하여, VM에 쉽게 액세스할 수 있습니다. 여행 중에도 귀찮음 없이 집 IP 주소를 사용하여 다양한 작업을 안전하게 수행할 수 있어서 매우 유용합니다. 끊김 없는 액세스를 보장하기 위해 여분의 VM 몇 대를 항상 준비해 둡니다. 놀랍게도, 제 작은 집 서버는 MacOS를 포함한 다양한 운영 체제를 지원합니다.

![이미지](/assets/img/2024-05-20-BuildingaBudget-FriendlyMiniHomeServerwithIntelN100AlderLake_7.png)

3. 최적화된 원격 데스크톱 경험: 더 부드러운 사용자 경험을 위해 Linux VM에는 NoMachine을, Windows VM에는 Microsoft Remote Desktop을 사용하는 것을 추천합니다. 하드웨어 패스스루 기능을 활용하여, Intel iGPU를 성공적으로 설치하여 Windows 10 Tiny 버전에서 가벼운 게임을 즐기고 4K YouTube 동영상을 원활하게 재생할 수 있었습니다.

![이미지](/assets/img/2024-05-20-BuildingaBudget-FriendlyMiniHomeServerwithIntelN100AlderLake_8.png)

<div class="content-ad"></div>

## 마무리

내 새로운 홈 서버를 설정하는 과정은 몇 주 동안의 여정이었지만, VM, 자원 공유 및 여러 리눅스 서버 운영 체제에 대한 이해에서 큰 발전이 이루어졌습니다. 이 과정을 통해 서버 관리에 대한 소중한 통찰과 지식을 얻었습니다.

나의 경험을 공유함으로써 예산을 고려한 강력한 미니 홈 서버를 VM 기능과 함께 구축하려는 다른 사람들에게 도움이 되었으면 좋겠습니다. 본 안내서가 프로그래밍과 셀프 호스팅의 영역으로 나아가려는 사람들에게 발판이 되기를 바랍니다.