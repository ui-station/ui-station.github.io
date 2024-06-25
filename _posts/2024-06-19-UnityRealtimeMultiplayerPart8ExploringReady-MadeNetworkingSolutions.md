---
title: "유니티 실시간 멀티플레이어, 파트 8 사용 가능한 네트워킹 솔루션 탐색"
description: ""
coverImage: "/assets/img/2024-06-19-UnityRealtimeMultiplayerPart8ExploringReady-MadeNetworkingSolutions_0.png"
date: 2024-06-19 11:50
ogImage:
  url: /assets/img/2024-06-19-UnityRealtimeMultiplayerPart8ExploringReady-MadeNetworkingSolutions_0.png
tag: Tech
originalTitle: "Unity Realtime Multiplayer, Part 8: Exploring Ready-Made Networking Solutions"
link: "https://medium.com/my-games-company/unity-realtime-multiplayer-part-8-exploring-ready-made-networking-solutions-10f3b6f76cf9"
---

멀티플레이어 게임에서는 클라이언트들이 동기화되어야 합니다. 데이터 패킷을 직접 교환하는 것이 가능하지만, 이는 경험이 적은 개발자들에겐 복잡할 수 있습니다. 그러므로 우리는 다양한 케이스에 대한 준비된 네트워킹 솔루션을 살펴보겠습니다.

![image](/assets/img/2024-06-19-UnityRealtimeMultiplayerPart8ExploringReady-MadeNetworkingSolutions_0.png)

안녕하세요! MY.GAMES의 리드 소프트웨어 엔지니어 Dmitrii Ivashchenko입니다. Unity 실시간 멀티플레이어 랜드스케이프에 관한 시리즈 기사가 계속됩니다! 오늘은 실시간 멀티플레이어용 준비된 솔루션에 대해 다뤄볼 예정입니다. 시작해봅시다.

# 전송에 대한 간단한 메모

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

멀티플레이어 게임에서 서버와 클라이언트는 네트워크를 통해 패킷을 보내는 방식으로 데이터를 교환합니다. 서로 다른 위치에서 접속한 플레이어들을 위해 공유된 가상 공간을 만들기 위해, 게임 프로세스에서 발생하는 이벤트(캐릭터 이동 또는 오브젝트 생성과 같은)는 다른 클라이언트와 동기화되어 데이터 패킷을 보내는 것으로 처리됩니다. 네트워크를 통해 패킷을 송수신하는 책임을 맡고 있는 부분을 전송 계층이라고 부릅니다.

이 패킷들을 직접 전송하는 것은 가능하지만, 이러한 방식은 멀티플레이어 게임을 다루는 데 경험이 적은 개발자들에겐 불편할 수 있습니다. 그래서 처음부터 직접 구현하기보다는 아래 나열된 것 중 하나를 사용하는 것이 더 나은 아이디어입니다. 이제 그 솔루션들을 살펴보겠습니다.

# Unity Relay & Netcode

유니티는 Netcode 패키지 두 가지를 제공합니다: GameObjects용 Netcode(미리보기 릴리스 단계), Entities용 Netcode(실험 모드) 그리고 폐기된 UNET. 또한 Unity Relay 서비스를 제공하여 게임 클라이언트를 연결합니다 - 이에 대해 간단히 언급하겠습니다.

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

# 유니티 릴레이

유니티 릴레이는 게임 개발자가 플레이어 간에 향상된 연결을 제공하는 방법으로, 세 번째 자로 솔루션에 투자하지 않고 전용 게임 서버(DGS)를 유지하거나 멀티플레이어 게임에서 네트워크 복잡성에 대해 걱정할 필요 없이 조인 코드 메커니즘을 통해 제공합니다. DGS 대신 릴레이 서비스는 프록시 역할을 하는 유니버설 릴레이 서버를 통해 연결성을 제공합니다.

![image](/assets/img/2024-06-19-UnityRealtimeMultiplayerPart8ExploringReady-MadeNetworkingSolutions_1.png)

릴레이 서비스를 통해 플레이어들은 UDP, DTLS 및 안전한 WebSocket (WSS)를 포함한 여러 다양한 프로토콜을 통해 통신할 수 있습니다. 릴레이 서버를 선택한 후에 클라이언트는 앞에서 언급한 프로토콜 중 하나를 사용하여 직접 릴레이 서버와 통신합니다. WebSocket 연결을 통해 WebGL을 사용하는 브라우저에서 멀티플레이어 연결이 가능합니다.

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

Relay는 모든 게임 엔진과 함께 작동합니다. Unity에서 Relay를 사용하는 경우 Unity Transport Package (UTP)와 통합된 Relay SDK를 사용하는 것이 좋습니다.

# Unity Transport Package

Unity Transport은 멀티플레이어 게임을 개발하기 위한 저수준 네트워킹 라이브러리입니다. 이것은 Netcode for GameObjects 및 Netcode for Entities를 기반으로 하지만 솔루션과 함께 사용할 수도 있습니다.

Unity Transport는 UDP 및 WebSockets 위에 제공된 연결 기반 추상화 계층(내장된 네트워크 드라이버) 덕분에 Unity Engine에서 지원하는 모든 플랫폼을 손쉽게 지원합니다. UDP 및 WebSockets를 암호화 여부에 관계없이 구성할 수 있습니다. 신뢰성, 패킷 순서, 패킷 분할과 같은 추가 기능을 제공하기 위해 파이프라인도 사용할 수 있습니다.

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

Transport 2.0의 주요 기능은 WebGL 지원이 추가되었습니다. 이를 통해 Unity Transport 패키지를 Unity Engine의 모든 지원 플랫폼에서 사용할 수 있게 되었습니다. Transport 사용자들은 이제 Websocket 전송의 구현에 접근할 수 있습니다. TLS를 사용하든 사용하지 않든 움직이는 플레이어는 일반적으로 셀룰러 타워 사이에서 네트워크 이동을 투명하게 활용할 수 있습니다. 이 기능은 현재 클라이언트 측과 UDP 전송에만 제한되어 있습니다.

Unity Transport를 사용하려면 Unity Editor 버전 2022.2 이상을 설치하고 com.unity.transport 패키지도 설치해야 합니다.

![이미지](/assets/img/2024-06-19-UnityRealtimeMultiplayerPart8ExploringReady-MadeNetworkingSolutions_2.png)

Relay는 Unity Transport (UTP)와 함께 작동합니다. 이를 통해 엄격한 방화벽과 같은 라우팅 제한으로 인해 통신할 수 없는 클라이언트들이 연결할 수 있게 됩니다.

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

# Unity Relay 제한 사항

Relay는 다음과 같은 제한 사항이 있습니다:

- 현재 지역 잠금 기능이 없습니다. 요청을 처리할 수 있는 용량이 있는 경우 누구나 어느 지역에서든 할당 요청을 할 수 있습니다.
- Relay 서비스는 모든 트래픽을 선택한 호스트 지역을 통해 라우팅합니다. 따라서 지역 간 통신은 최적의 지연 시간을 제공하지 않을 수 있습니다.
- 한 게임 세션 내에서 최대 100명의 플레이어가 호스트에 참여할 수 있습니다.
- Relay 서비스는 네트워크 트래픽을 제어하기 위해 속도 제한을 사용합니다. "할당 생성", "참여 코드 생성", "Relay 참여" 및 "지역 리스트" 요청에 대해 분당 60개의 요청 제한이 설정됩니다. 이 제한은 각 인증된 플레이어에 적용됩니다.

제한 사항이 있지만, Relay 서비스는 플레이어 연결을 간편화하고 원활한 멀티플레이어 게임 경험을 제공하는 강력한 도구입니다.

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

# GameObjects를 위한 Netcode

Relay의 메인 게임 네트워크 코드를 위한 가장 인기 있는 두 가지 솔루션은 Netcode for GameObjects (NGO)와 API Mirror Networking이 있습니다.

대부분의 경우 NGO를 사용하는 것이 권장되는 최상의 방법이며, 네트워크 변수, 씬 관리, 원격 프로시저 호출 (RPC), 그리고 메시징과 같은 안정적인 핵심 기능을 제공합니다. 그러나, API Mirror Networking은 NGO가 제공하는 전체 기능 세트를 요구하지 않는 게임에도 간편하고 사용하기 쉬운 기능을 제공하기 때문에 훌륭합니다.

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

![Image](/assets/img/2024-06-19-UnityRealtimeMultiplayerPart8ExploringReady-MadeNetworkingSolutions_3.png)

Photon Realtime은 멀티플레이어 게임용 핵심 레이어이자(그리고 Photon의 더 복잡한 네트워크 솔루션에서도) 플레이어 매칭 및 확장 가능한 방식으로 빠른 통신과 같은 문제를 다룹니다. Photon Realtime은 게임에서뿐만 아니라 더 구체적인 멀티플레이어 솔루션에서도 사용됩니다.

Photon Realtime은 퓨전이나 퀀텀 솔루션에서 찾을 수 있는 게임 상태 및 시뮬레이션 동기화 메커니즘을 포함하지 않고 대신 네트워크 상의 메시지 전송에 초점을 맞춥니다.

Photon Realtime이라는 용어는 또한 클라이언트와 서버 간 상호 작용을 정의하는 API, 도구 및 서비스의 포괄적인 프레임워크를 포함합니다.

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

모든 Photon Realtime 클라이언트는 세 가지 명확한 작업으로 나뉜 전용 서버 시퀀스에 연결합니다: 인증 및 지역 배포 (Name Server), 플레이어 매칭 (Master Server) 및 게임 플레이 (Game Server). 이러한 서버들은 Realtime API를 통해 관리되므로 걱정할 필요가 없습니다. 하지만 이러한 서버에 대한 이해는 확실히 도움이 될 수 있습니다.

Photon Cloud는 Photon Realtime 클라이언트를 위한 글로벌 호스팅을 제공하는 완전한 관리형 서비스입니다. 게임 코드는 Photon Cloud와 통신하여 클라우드에 연결하고 해당 API를 사용하여 연결, 무작위 방 참가 또는 이벤트를 발생시키는 등의 작업을 수행합니다.

Photon Realtime에서는 방 데이터를 쉽게 저장하고 로드할 수 있으며, 웹훅을 설정하여 Photon Cloud를 외부 웹 서버에 연결할 수도 있습니다.

# Photon Fusion

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

![image](/assets/img/2024-06-19-UnityRealtimeMultiplayerPart8ExploringReady-MadeNetworkingSolutions_4.png)

포톤 퓨전은 Unity를 위한 네트워크 상태 동기화를 위한 새로운 고성능 라이브러리입니다. 두 가지 주요하게 다른 네트워크 토폴로지를 지원하며, API 하나를 사용하여 네트워크 연결이 없는 경우에도 플레이어 한 명을 위한 모드를 지원합니다.

Fusion은 Unity 워크플로에 통합하기 쉽도록 설계되었으며, 데이터 압축, 클라이언트 측 예측, 지연 보상 등의 고급 기능을 해당 제품에 기본으로 제공합니다.

예를 들어 Fusion에서 RPC 및 네트워크 상태는 MonoBehaviour 메서드 및 속성에 속성을 사용하여 정의되므로 명시적 직렬화 코드가 필요하지 않으며, 네트워크 오브젝트는 Unity 프리팹의 최신 기능을 모두 사용하여 정의할 수 있습니다.

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

퓨전은 최신 압축 알고리즘을 사용하여 대역폭 요구량을 줄이고 프로세서에 미미한 오버헤드를 발생시킵니다. 데이터는 완전 압축된 스냅샷으로 전송되거나 후속 일관성을 보장하는 부분 블록으로 전송됩니다. 후자의 경우, 매우 큰 플레이어 수를 지원하는 맞춤형 관심 영역 시스템이 제공됩니다.

퓨전은 틱 기반 시뮬레이션을 구현하며 공유 모드 또는 호스트 모드에서 운영됩니다. 주요 차이점은 네트워크 개체에 대한 권한이 누구에게 있는지이지만 이로 인해 사용 가능한 다른 SDK 기능이 결정됩니다.

퓨전은 유니티 (볼트 및 PUN)용 기존 포톤 제품 두 개를 대체하기 위해 개발되었습니다. 퓨전의 중요한 핵심 구성 요소는 NetworkRunner와 NetworkObject입니다. NetworkRunner는 퓨전의 핵심으로 간주될 수 있습니다 - 장면에 있는 하나의 러너가 네트워크 작업과 시뮬레이션을 관리합니다.

퓨전은 빠른 게임 또는 프로토타입 생성을 위해 다양한 사전 구축된 NetworkBehaviours를 제공합니다.

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

Fusion은 입력 처리를 두 단계로 구분합니다: 로컬 하드웨어에서 입력을 수집하고 구조체에 넣은 다음 해당 입력을 읽어 게임 상태를 변경합니다(시뮬레이션을 진행).

Fusion은 일반 Fusion 입력 또는 [Networked] 속성 사용이 가장 실용적인 해결책이 아닌 경우에 RPC(Remote Procedure Calls)를 지원합니다. Fusion을 시작하는 방법은 Fusion 시작 가이드를 공부하는 것을 권장합니다.

# Photon Quantum

![이미지](/assets/img/2024-06-19-UnityRealtimeMultiplayerPart8ExploringReady-MadeNetworkingSolutions_5.png)

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

포톤 퀀텀은 멀티플레이어 게임용으로 완전한 결정론적 엔진입니다. 예측/롤백 방식에 기반한 이 엔진은 딜레이에 민감한 온라인 게임에 완벽하게 적합합니다. 액션 RPG, 스포츠 게임, 대전 게임, FPS 등과 같은 게임들에 특히 유용합니다.

이 엔진을 사용하면 넷코드가 필요하지 않습니다. 모든 게임 요소가 기본적으로 네트워크로 연결되고 100% 동기화됩니다. 여러 연결된 플레이어가 있는 한 시뮬레이션을 만들어야 하며, 이는 로컬 멀티플레이어 경험을 개발할 때와 같습니다. 퀀텀의 결정론적 서브시스템은 각 클라이언트에서의 시뮬레이션이 물리학, 봇, 경로탐색, 애니메이션을 포함하여 항상 동기화되고 딜레이 없이 동작함을 보장합니다.

![이미지](/assets/img/2024-06-19-UnityRealtimeMultiplayerPart8ExploringReady-MadeNetworkingSolutions_6.png)

결정론적인 게임은 치팅에 저항력이 내재되어 있습니다. 치트에 대항하기 위한 가장 효과적인 방법은 재생 또는 서버-판정 시뮬레이션을 확인하는 것입니다.

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

플레이어 입력은 Photon Cloud 서버로 전송되어 다른 플레이어들 사이에 분산됩니다. 웹훅(Webhooks)을 사용하면 자체 백엔드와 플러그인을 연결하여 서버 측에서 사용자 정의 코드를 실행할 수 있습니다.

Photon Quantum은 ECS 아키텍처(ECS architecture)로 구축되었으며, PC, 콘솔, VR 및 모바일 기기에서 심도 있는 멀티플레이어 게임조차 뛰어난 성능을 보장합니다.

Quantum에서 인코딩된 시뮬레이션은 Unity에 종속성이 없어 어디에서든 실행할 수 있습니다. 모든 로컬 동작은 지연 없이 수행되며, 원격 입력은 예측되고 롤백됩니다. Quantum은 리플레이(replays)를 볼 수 있는 기능을 갖고 있습니다. 리플레이는 백엔드에 저장되거나 게임 내에서 사용할 수 있습니다. 시작하려면 Quantum 100 시리즈를 확인해보세요.

# 노름코어(Normcore)

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

<img src="/assets/img/2024-06-19-UnityRealtimeMultiplayerPart8ExploringReady-MadeNetworkingSolutions_7.png" />

Normcore는 Unity 기반 프로젝트에 멀티플레이어 모드를 추가하는 데 사용할 수 있는 고성능 도구입니다. Normcore에는 네트워크 물리학, 지속적인 공간, 음성 채팅 및 XR 지원이 포함되어 있습니다.

RealtimeTransform 컴포넌트를 추가하면 Normcore는 모든 객체 변환을 자동으로 동기화합니다. 이를 위해 코딩이 필요하지 않습니다. 추가로 Normcore는 상태에 따른 보간 및 안정적인 네트워크 물리학을 제공하여 모든 연결에서 완벽한 움직임을 보장합니다.

Normcore의 주요 장점 중 하나는 WebRTC를 기반으로 한 빠른 데이터 전송입니다. 전송 중 단편화를 발생시키지 않는 최대 패킷 크기를 사용하여 데이터 전송 프로세스를 가속화합니다. Normcore의 모든 데이터 패킷은 기본적으로 암호화되어 있어 사용자 데이터의 보안과 기밀성을 보장합니다.

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

노름코어는 델타 업데이트 시스템을 사용합니다. 이는 마지막 데이터 패킷이 전송된 이후의 모든 변경 사항을 추적하고, 새로운 패킷을 전송할 때 어떤 것을 포함해야 하는지 이미 알고 있는 것을 의미합니다. 이를 통해 자원을 절약하고 성능을 향상시킵니다. 노름코어의 데이터 직렬화 기능은 CPU 사용량 최적화를 가능하게 합니다. 모든 직렬화 코드는 프로젝트 컴파일 전에 자동으로 생성되어 빠르고 효율적인 자원 사용을 보장합니다.

노름코어 서버는 전 세계 지역에서 운영되며 사설 광섬유 네트워크를 통해 연결되어 지연시간이 낮습니다. 노름코어를 자체 서버에 호스팅하거나 노름코어가 클라우드 인프라의 사본을 호스팅하도록 허용할 수 있습니다.

# 이미지

![이미지](/assets/img/2024-06-19-UnityRealtimeMultiplayerPart8ExploringReady-MadeNetworkingSolutions_8.png)

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

Mirror Networking은 Unity용 고수준 네트워크 라이브러리로, 사용 편의성과 신뢰성을 최적화했습니다. 이 라이브러리는 네트워크 연결을 다루는 과정을 간단하게 만들어 개발자가 프로젝트 작성에 집중할 수 있도록 설계되었습니다.

Mirror Networking은 12가지 이상의 저수준 프로토콜과 호환되며 지속적으로 발전하고 개선됩니다. 네트워크를 통한 원격 프로시저 호출 및 컨텍스트 관리를 위한 기능을 포함하며 네트워크 애플리케이션에서 물리 작업을 지원합니다.

이 라이브러리는 12가지가 넘는 기본 제공 네트워크 어댑터와 다섯 가지의 네트워크 관리 시스템 옵션을 제공하여 개발자가 사용자 정의 버전을 만들 수 있습니다. 학습 및 코딩 프로세스를 용이하게 하기 위해 여러 완전한 사용 예제도 포함되어 있습니다.

# Nakama

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

![FishNet image](/assets/img/2024-06-19-UnityRealtimeMultiplayerPart8ExploringReady-MadeNetworkingSolutions_9.png)

히로익 랩스의 나카마(Nakama)는 사용자가 전체 인프라를 하나의 오픈 패키지로 소유할 수 있는 인기 있는 오픈 소스 게임 서버입니다. 나카마에는 필요한 모든 실시간 게임 API 및 소셜 및 경쟁 기능이 포함되어 있습니다.

게임에 필요한 모든 기능을 갖춘 나카마는 실시간 멀티플레이어 및 소셜 및 경쟁 기능과 같은 모든 필수 기능을 제공하므로 Go, TypeScript 및 Lua를 사용하여 클라이언트 및 서버 측의 모든 측면을 사용자 정의할 수 있습니다. 나카마를 사용하면 실시간 멀티플레이어 경쟁 게임을 생성하고 매칭 알고리즘을 사용자 정의하며 매일 보상을 추가하고 리더보드를 생성하며 게임 내 통화를 구현하고 실시간 채팅을 제공할 수 있습니다.

# FishNet

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

![Image](/assets/img/2024-06-19-UnityRealtimeMultiplayerPart8ExploringReady-MadeNetworkingSolutions_10.png)

Fish-Networking은 무료 오픈 소스 라이브러리로, Unity에서 개발된 네트워킹 솔루션을 제공하는 것입니다. 이는 경험 많은 게임 디자이너가 개발했으며, 일반적으로 유료 솔루션에서만 사용할 수 있는 많은 기능을 제공합니다.

Fish-Networking의 주요 장점은 대역폭 및 리소스 최적화(서버 비용 절감), 수십 명에서 수백 명까지의 많은 플레이어를 지원하며, 클라이언트 예측, 지연 보상, 서버 부하 분산, 중첩 네트워크 객체 지원 등의 내장 기능이 있습니다.

# 솔루션 개요

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

아래 표는 유니티에서 게임 개발을 위한 다양한 네트워크 솔루션을 프로토콜 사용, 지원되는 위상들, 세션 당 최대 플레이어 수, 최소 지원 유니티 버전 및 현재 상태로 비교하여 제공합니다.

![테이블](/assets/img/2024-06-19-UnityRealtimeMultiplayerPart8ExploringReady-MadeNetworkingSolutions_11.png)

다양한 솔루션을 통해 개발자들은 자신의 특정 요구 사항에 맞는 도구를 선택할 수 있습니다. 많은 솔루션이 서로 다른 유형의 위상을 지원하므로 게임의 특정 요구 사항에 적응할 수 있습니다. Photon PUN, Photon BOLT, UNET과 같은 일부 오래된 네트워크 솔루션은 새 프로젝트에 사용하지 않는 것이 좋으며, Netcode for Entities 및 Netcode for GameObjects는 아직 실험적이거나 프리 릴리스 단계에 있어서 제작용으로 사용할 수는 없습니다.
