---
title: "믹스 네트워크 소개"
description: ""
coverImage: "/assets/img/2024-05-18-IntroductiontoMixNetworks_0.png"
date: 2024-05-18 21:04
ogImage:
  url: /assets/img/2024-05-18-IntroductiontoMixNetworks_0.png
tag: Tech
originalTitle: "Introduction to Mix Networks"
link: "https://medium.com/@0knowledgee/introduction-to-mix-networks-bad2f6f61044"
---

![Mixnet introduction](/assets/img/2024-05-18-IntroductiontoMixNetworks_0.png)

Mixnets are important because they can provide strong anonymity, even stronger than Tor. Tor is widely known to have weak anonymity as described in academic literature. By utilizing advanced cryptographic methods and sophisticated mixing strategies, mixnets offer a more secure option for anonymous communication, effectively shielding users from both passive and active network observers.

## Defining Network Anonymity

A mixnet falls under the category of anonymous communication networks. Anonymous communication essentially refers to resistance against traffic analysis. In simpler terms, network anonymity implies that a client can interact with a network entity, and it becomes challenging for passive or active network observers to identify which client is communicating with which network entity. More clients using the network improve the privacy and anonymity features since anonymity thrives with greater participation.

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

# 익명성을 위한 패킷 혼합

토르와는 달리, 믹스넷은 익명성 속성을 위해 경로의 예측 불가능성에 의존하지 않습니다. 대신, 믹스넷의 각 믹스는 패킷을 혼합하는 유형의 작업을 수행하며, 이 혼합이 익명성과 개인 정보 보호 속성을 제공합니다. 이 과정은 네트워크 경로가 관찰되더라도 들어오고 나가는 패킷 간의 상관 관계가 가려짐을 보증합니다.

# 에니트러스트 모델

에니트러스트 모델은 믹스넷 문헌에서 중요한 개념으로, 적어도 경로에서 하나 이상의 믹스가 손상되지 않고 정직하게 혼합을 수행하는 한 믹스넷 사용자가 개인 정보 보호 속성을 유지하는 위협 모델을 의미합니다. 이 모델은 일부 노드가 손상되었을 때에도 견고한 익명성을 보장하기 때문에 중요합니다. 에니트러스트 모델의 강점은 그 탄력성에 있어서, 이것이 현대적인 믹스넷 설계의 근간이 되게 합니다.

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

# 제한 사항과 감시 자본주의 문제

분명히 믹스네트들은 감시 자본주의 문제를 해결하지는 않습니다; 사람들은 감시 데이터를 수집하여 광고 기관에 판매하는 일부 매우 큰 기업이 제공하는 서비스를 사용하기를 선택합니다. 그런 감시 문제는 문화적 및 사회적 해결책이 필요합니다. 여기서 논의하는 것은 익명 통신 네트워크가 일반적으로 모든 인터넷 프로토콜이 통신 인프라로 메타데이터를 누출하도록 되어 있어 수동 관찰자가 누가 누구에게 얘기하는지 알 수 있는 기술적 문제에 대한 기술적 해결책입니다.

# 메타데이터 누출 이해

수동 네트워크 관찰자는 암호화되어 있더라도 통신 세트에 대해 많은 정보를 얻을 수 있습니다. 특히, 그들은 다음을 알 수 있습니다:

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

- 지리적 위치
- 메시지 순서
- 보낸 및 받은 메시지 크기
- 통신이 발생한 시간대
- 모든 통신 파트너의 신원 및 전체 소셜 그래프

# 익명성 삼단논법

익명성 삼단논문을 고려해 봅시다. 여기에는 다음이 포함됩니다:

- 강력한 익명성
- 낮은 지연 시간
- 높은 대역폭

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

삼중 선택지는 우리에게 강력한 익명성을 선택한다면 낮은 지연 또는 높은 대역폭 중 하나만 선택할 수 있다는 것을 말해줍니다. 따라서 Tor 및 I2p와 같은 높은 대역폭과 낮은 지연 익명 시스템은 약한 익명성을 제공한다고 여겨지며, 이들이 강한 익명성을 갖도록 만드는 것은 지연 또는 대역폭 중 어느 하나를 희생해야만 가능하다는 것입니다.

## 해독 믹스 네트워크 및 비트 단위 연결 불가능성

해독 믹스 네트워크에서 믹스 노드는 암호적으로 패킷을 변환하여 하나의 암호화 층을 제거합니다. 따라서 입력 패킷은 이 변환으로 인해 출력 패킷과 완전히 다르게 보일 것입니다. 이렇게 함으로써 우리에게 비트 단위 연결 불가능성 속성을 제공한다고 말할 수 있으며, 패킷 내의 비트를 보기만으로도 패시브 네트워크 관찰자가 입력 메시지를 출력 메시지와 연결할 수 없게 만듭니다.

## 믹싱 전략 및 지연

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

혼합 노드는 패킷을 암호화하는 것 외에도 자신들의 혼합 전략에 따라 지연을 추가합니다. 먼저 임계값 혼합 전략에 대해 생각해 봅시다. 이 전략은 패킷이 일정 임계값에 도달할 때까지 패킷을 축적하고, 그 후에 패킷을 섞어 다음 홉으로 전송합니다. 이 경우, 임계값을 5로 설정했지만 혼합 대기열에는 4개의 메시지만 있는 경우, 해당 메시지는 영원히 기다리거나 다섯 번째 메시지가 도착할 때까지 기다릴 것입니다. 적대자는 입력 메시지와 출력 메시지 간의 연결을 추측할 확률이 5분의 1입니다. 따라서 실제 혼합 넷에서 우리는 임계값을 1000이나 10000 또는 매우 높은 값으로 설정하여 적대자가 우리의 개인 정보 개념을 도전적으로 파괴하기 어렵게 만들어야 합니다.

# 혼합 노드의 연쇄 및 가용성 해결

혼합 노드의 연쇄 A - B - C - D - E가 있다고 가정해 봅시다.

클라이언트가 메시지를 혼합 A로 보내고, 혼합 B로 전송하고, 그러면 계속 진행합니다. 이 설계는 우리에게 Anytrust 모델을 제공합니다. 라우팅하는 경로에 둘 이상의 혼합이 있으므로 여러 엔티티(보안 도메인으로도 알려짐)가 운영하는 것으로 가정됩니다. 그러나 이 설계는 고가용성을 제공하지 않습니다. 경로의 어떤 혼합 노드라도 실패하면 전체 네트워크가 실패하고 메시지를 라우팅할 수 없게 됩니다.

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

학술 믹스넷 문헌에서는 이 문제를 두 가지 다른 방식으로 해결합니다:

- 다중 카스케이드
- 계층화된 토폴로지

## 적절한 토폴로지 선택 방법

익명 통신 네트워크용으로 많은 다른 토폴로지가 있습니다. 그러나 "자유 경로"라는 단점이 있는데, 이는 모든 믹스 노드가 어떤 믹스 노드와든 통신할 수 있는 것입니다. 이는 믹싱 엔트로피의 양을 줄이게 됩니다. 네트워크 토폴로지의 영향과 저대없액 미확인성에 대한 결과로, 계층화된 토폴로지나 다중 카스케이드를 사용할 때 적대자들은 최대한 불확실할 것입니다.

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

# 계층화된 위상 대비 다중 카스케이드

믹스넷의 사용자에게 여러 개의 믹스 노드 카스케이드를 제공하거나 믹스 노드를 계층화된 위상으로 배열할 수 있습니다. 계층화된 위상은 모든 믹스 노드가 "라우팅 위상 계층"으로 배열된 네트워크로, 이는 믹스 노드의 서로 다른 집합인 순서가 지정된 집합입니다. 각 계층은 고유한 믹스 노드 집합을 갖습니다. 1계층의 믹스 노드는 오직 2계층의 믹스 노드에게만 메시지를 보낼 수 있으며, 마찬가지로 2계층의 믹스 노드는 오직 3계층의 믹스 노드에게만 메시지를 보낼 수 있습니다.

# 결론

믹스 네트워크는 교통 분석에 효과적으로 대응하여 디지털 통신에서 강력한 익명성을 제공합니다. Tor와 달리 믹스넷은 통신 패턴을 숨기기 위해 암호 변환과 믹싱 전략을 사용하여 높은 개인 정보 보호 수준을 보장합니다. Anytrust 모델 및 계층화된 위상 또는 다중 카스케이드를 활용하여, 믹스넷은 사용자 익명성을 보존하기 위한 견고한 솔루션을 제공합니다. 메타데이터 누출 문제를 해결하지만 감시주의적 자본주의 등 보다 넓은 문제 해결은 아직 이뤄지지 않습니다. 연구가 진행됨에 따라 믹스넷은 계속 발전하며 안전하고 익명의 통신을 강화할 것입니다. 0 Knowledge Network(0KN)는 이러한 발전을 선도하며 이러한 고급 기술을 활용하여 디지털 통신의 강력한 익명성과 보안을 제공하면서 탈중앙화된 개인 정보 보호 응용 프로그램의 새로운 세대를 육성하고 있습니다.
