---
title: "카프카 클러스터를 쿠버네티스로 이관하는 방법 매끄러운 전환 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-SeamlessTransitionMigratingKafkaClustertoKubernetes_0.png"
date: 2024-06-23 01:01
ogImage:
  url: /assets/img/2024-06-23-SeamlessTransitionMigratingKafkaClustertoKubernetes_0.png
tag: Tech
originalTitle: "Seamless Transition: Migrating Kafka Cluster to Kubernetes"
link: "https://medium.com/zendesk-engineering/seamless-transition-migrating-kafka-cluster-to-kubernetes-c8dc66594d1b"
---

# 배경

저희는 Zendesk에서 자체 Kafka 인프라를 관리합니다. 처음에는 시장에 세련된 관리형 Kafka 서비스가 없었습니다. 그래서 우리는 Chef를 통해 Kafka 인프라를 구축하고 AWS EC2 인스턴스에 배포했습니다.

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

카프카 클러스터는 두 부분으로 구성됩니다: 카프카 브로커 집합과 주키퍼 노드입니다. 브로커는 카프카 클러스터의 서버로 데이터를 저장하고 관리하며 메시지의 발행과 구독을 처리합니다. 주키퍼 클러스터는 카프카 브로커를 조정하고 관리하는 쿼럼으로, 리더 선출, 클러스터 구성원 관리 및 메타데이터 유지와 같은 작업을 처리합니다.

저희는 VM 기반 인프라에서 Kubernetes (K8s) 기반으로 이동 중입니다. Chef 스택의 지원이 줄어들어 카프카 인프라를 마이그레이션해야 했습니다.

## 원활한 마이그레이션 정의

카프카 클러스터의 원활한 마이그레이션은 데이터 무결성과 일관성을 유지하면서 투명하고 다운 타임이 없는 프로세스를 포함하며, 클라이언트의 최소한의 변경과 수동 개입이 필요한 최소한의 변화를 필요로 합니다.

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

이전 클러스터에서 구성 및 데이터를 복제하고, 클라이언트를 새 클러스터로 전환하며, 운영 연속성과 성능 동등성을 보장하는 것을 포함합니다.

원활한 마이그레이션의 복잡성은 클러스터 규모와 클라이언트 수와 직접적인 관련이 있습니다. 사용 빈도가 낮거나 주로 오프라인 워크로드를 처리하는 클러스터를 마이그레이션하는 것은 비교적 간단할 수 있습니다. 그러나 대규모 실시간 워크로드를 처리하고 상당한 데이터 양을 다루는 클러스터를 마이그레이션하는 것은 훨씬 더 어려운 프로젝트입니다.

Zendesk에서는 우리의 Kafka 클러스터가 두 번째 범주에 속합니다. Kafka 클러스터는 상당한 양의 데이터를 관리하며 100개 이상의 서비스에서 사용됩니다.

Kafka 클러스터 상태 자세히:

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

- 12개의 Kafka 클러스터
- 모든 환경을 통틀어 약 100개의 브로커
- 300TB의 데이터 저장 용량
- 하루에 300억 건의 메시지
- 1000개 이상의 토픽
- 연결된 서비스가 100개 이상

## 주요 도전 과제

이러한 대규모이자 중요한 클러스터를 이주하는 것은 신중히 설계된 디자인이 필요합니다. 주의를 요하는 다수의 세부 사항 중에서, 주요 대응해야 할 도전 과제는 다음과 같습니다:

- 복제 단계 중 데이터 무결성과 클러스터 가용성 보장,
  클라이언트들에게 원활한 전환 기회를 제공하기 위한 전환 단계 스핑크 요소입니다.

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

데이터가 완전히 복제된 후 새 클러스터로 고객이 대규모로 전환될 수 있는 구체적인 전환 시점이 없다는 것을 이해하는 것이 중요합니다. 원활한 이주 요구 사항으로 인해 전환 단계는 지속적인 데이터 복제와 클라이언트 전환을 동시에 수용해야 합니다.

# 이주 디자인

## 일방향 클러스터 간 이주

첫 번째 버전 디자인은 단방향 클러스터 간 이주입니다. 이 방법은 MirrorMaker2를 사용하여 이전 클러스터의 데이터 및 구성을 새 클러스터로 복제합니다. MirrorMaker가 클러스터 간의 초기 데이터 로드를 전송한 후에도 데이터를 동기화 상태로 유지하며, 각 클라이언트가 이전 클러스터와 연결을 끊고 새 클러스터에 연결하는 전환 프로세스를 실행합니다.

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

![이미지](/assets/img/2024-06-23-SeamlessTransitionMigratingKafkaClustertoKubernetes_2.png)

문제: 마이그레이션 복잡성이 클라이언트로 이동됩니다.

이 접근 방식은 처음에는 간단하고 견고해 보이지만, Kafka 클라이언트를 관리하는 각 팀에게 마이그레이션의 복잡성을 이동시킵니다. 이는 모든 클라이언트가 자체 커트오버 전략을 개발하고 실행해야 하며, 구현에 따라 복수의 배포가 필요할 수 있습니다.

예를 들어, 단일팀이 한 주제의 프로듀서와 컨슈머를 모두 제어하는 가장 간단한 시나리오에서, 클라이언트는 코드 변경과 다수의 배포를 통해 다음 단계를 거쳐 마이그레이션될 수 있습니다.

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

- 모든 소비자를 새 클러스터로 이동합니다.
- 이전 클러스터로의 생성을 중지합니다.
- 데이터가 새 클러스터에 완전히 동기화될 때까지 기다립니다.
- 새 클러스터를 가리키는 생산자를 시작합니다.

주제의 여러 팀이 서로 다른 소비자 그룹을 제어하는 더 복잡한 시나리오에서는 모든 팀이 참여하는 조정된 마이그레이션이 필요합니다. 게다가 소비자가 또 다른 주제의 생성자 역할도 한다면 복잡성이 증가하여 복잡하고 다단계의 마이그레이션 과정으로 이어질 수 있습니다.

부수적인 오버헤드

특정 마이그레이션 디자인에는 몇 가지 부수적인 오버헤드가 있습니다:

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

- 단일 장애 지점: MirrorMaker2 연결 클러스터가 단일 장애 지점입니다. 모든 주제의 소비자가 새 클러스터로 성공적으로 마이그레이션되어 있지만 MirrorMaker2의 실패로 인해 이전 클러스터에서 남은 실시간 데이터 동기화에 실패하는 시나리오를 상상해보세요.
- 장애 감지 및 장애 조치 작업의 간접성: MirrorMaker2의 실패는 각 팀의 통제 범위를 벗어나지만 클라이언트 장애 조치 프로세스는 완전히 그들에게 의존적이며 이전 버전의 코드 변경이나 재배포를 필요로 합니다.
- 긴 꼬리 문제: 마이그레이션의 완전한 완료는 가장 느린 클라이언트의 속도에 따라 달려있습니다. 앞서 언급된 복잡성으로 인해 일부 클라이언트는 마이그레이션을 완료하는 데 상당히 오랜 기간이 걸릴 수 있으며 이는 전체 프로젝트 전달 속도를 늦춥니다.
- 비용: 이 단계에서 브로커에 대한 배로된 컴퓨팅 및 저장소 비용뿐만 아니라 세 배로 증가한 데이터 전송 비용에 직면합니다. 즉, 이전 브로커에서 연결 클러스터로 데이터를 이동하고 새로운 브로커로 다시 옮기는 비용입니다.

## 클러스터 간 마이그레이션은 가능하지만, 더 나은 결과를 추구합니다

이 접근 방식은 클라이언트 관점에서의 원활한 마이그레이션이 아니며 운영 및 비용 대비로 이 규모에서 실용적인 구현이 아닙니다.

## 통합된 Kafka 클러스터 내 브로커 수준의 마이그레이션

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

클러스터 간 이동의 주요 도전 과제는 어느 시점에서든 클라이언트가 다른 클러스터를 가리켜야 한다는 것입니다. 데이터 무결성을 보장하기 위해 커트오버의 타이밍은 각각의 소비자와 생산자에 따라 다릅니다.

이 문제를 해결하기 위해 더 강렬한 질문을 해보는 것은 유용할 수 있습니다: 클라이언트가 클러스터 전환에 대해 무관심할 수 있을까요?

이 기사에서 언급한 대로, 클러스터 이전은 이전 클러스터에서 설정 및 데이터를 새 클러스터로 복제하고 클라이언트를 전환하는 것을 포함합니다.

하지만, 만약 우리가 방정식에서 클라이언트 전환이 필요 없게 한다면 어떻게 될까요? 새로운 클러스터를 구축하는 대신, K8s에서 새로운 브로커 그룹을 구성하고 기존 클러스터에 등록할 수 있습니다. 이렇게 하면 클라이언트가 클러스터를 변경할 필요가 없어집니다. 유일하게 남은 작업은 구체화된 Kafka 클러스터 내에서 이전 브로커에서 새 브로커로 데이터를 전송하는 것뿐입니다.

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

그렇습니다! 새로운 중개업체가 기존 클러스터에 참여할 예정이기 때문에 구성 이관이 필요하지 않습니다. 필요한 구성은 이미 클러스터 내에 준비되어 있어 프로세스가 간소화됩니다.

![이미지](/assets/img/2024-06-23-SeamlessTransitionMigratingKafkaClustertoKubernetes_3.png)

## 중개업체 간 데이터 전송이 클라이언트 스위치오버를 어떻게 달성하나요?

이 프로세스는 카프카 클라이언트 상호작용 메커니즘에 의해 이뤄집니다. 카프카 주제는 일련의 파티션으로 구성되며, 각 파티션에는 여러 개의 레플리카가 있을 수 있습니다. 대부분의 경우, 클라이언트는 리더 레플리카와 상호작용합니다.

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

한 브로커에서 다른 브로커로 토픽 데이터를 이동할 때는 기본적으로 한 브로커에서 다른 브로커로 복제본을 전송하는 과정을 포함합니다. 한번 복제본이 완전히 이전되고 리더 복제본으로 선출되면, 클라이언트는 이 변경 사항을 감지하고 새 리더 복제본과 상호 작용하기 시작합니다. 결과적으로, 새로운 브로커와 상호 작용을 시작합니다.

아래 그래프에 설명된 프로세스를 따라, 모든 복제본이 새 브로커에 저장되면 모든 클라이언트가 새 브로커와 상호 작용하며, 이전 브로커는 더 이상 중요한 작업을 수행하지 않는다는 것을 의미합니다.

이 접근 방식이 어떻게 작동하는지 구체적으로 설명해 봅니다.

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

- 단계 1: 동일한 수의 브로커를 K8s에 배포하고 ZooKeeper 하에 동일한 Kafka 클러스터에 등록합니다. 이 단계에서 EC2와 K8s의 브로커는 동일한 클러스터의 멤버입니다. 그러나 EC2 브로커는 여전히 모든 데이터를 보유하고 모든 클라이언트 트래픽을 처리합니다. K8s 브로커는 ZooKeeper와의 하트비트를 유지하는 것 외에는 비활성화 상태입니다.
- 단계 2: 토픽 파티션 레플리카가 EC2의 브로커에서 K8s로 마이그레이션을 시작합니다. 한 레플리카가 완전히 이동되면 K8s 브로커는 리더 레플리카를 보유하고 있는지에 따라 실제 클라이언트 토픽을 제공하기 시작할 수 있습니다.
- 단계 3: 모든 토픽 파티션 레플리카가 EC2의 브로커에서 K8s의 브로커로 이전되었습니다. EC2의 브로커는 더 이상 데이터를 보유하지 않고 클라이언트 요청을 처리하지 않습니다.
- 단계 4: EC2의 브로커를 축소하여 클러스터를 완전히 K8s 브로커로 구성합니다. 이제 마이그레이션이 완료되었습니다.

![이미지](/assets/img/2024-06-23-SeamlessTransitionMigratingKafkaClustertoKubernetes_5.png)

위에 언급된 마이그레이션 단계를 따르면 클라이언트 전환이 필요 없이 성공적으로 진행됩니다. 클라이언트는 마이그레이션 전체 과정 동안 일관되게 한 클러스터와 상호 작용하므로, 클라이언트 관점에서 마이그레이션이 완전히 원활해집니다.

# 심층 분석

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

이제 전반적인 개념을 알게 되었으니 구체적인 설정에 대해 다루어 봅시다:

- 네트워크 설정: 중요한 점은 두 가지 다른 브로커 세트를 그룹화하고, 이 하이브리드 카프카 클러스터에서 브로커 검색을 용이하게 하는 것입니다.
- 이주 구현 및 조정: 새 브로커로 레플리카를 이동하는 구현이 중요하지만, 조정은 더욱 중요합니다. 효율적인 조정은 롤백과 같은 재해 복구 시나리오를 고려해야 합니다.
- 메트릭과 모니터링: 하이브리드 클러스터의 모니터링을 효과적으로 수행하는 것은 EC2 및 K8s에 있는 브로커가 트래픽을 처리할 때 문제 감지, 디버깅 및 성능 세밀 조정에 중요합니다.
- 브로커 중단 관리: 고가용성을 위해 서로 다른 가용 영역(AZs)에 있는 여러 브로커가 중단되지 않도록 롤링 브로커를 설정하거나 이주 중에도 방지해야 합니다.

## 브로커 간 및 클라이언트-브로커 통신을 위한 네트워크 설정

브로커 수준의 이주에 대한 중요한 전제 조건은 두 세트의 브로커와 주키퍼 클러스터가 서로에게 비교적 낮은 지연 시간으로 통신할 수 있어야 한다는 것입니다. 게다가 클라이언트는 두 세트의 브로커를 발견하고 낮은 지연 시간으로 통신할 수 있어야 합니다.

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

우리의 네트워크 아키텍처로 첫 번째 문제가 해결되었습니다. EC2 상의 브로커, Zookeeper 클러스터 및 K8s 클러스터는 서로 다른 서브넷에 배치되었지만, 동일한 AWS 계정 내에 있으며 서브넷은 서로 연결되어 있습니다.

클라이언트가 EC2 및 K8s에 있는 브로커를 발견할 수 있도록 하려면 약간의 작업이 필요합니다. 무엇을 해야 하는지 이해하려면 먼저 클라이언트가 브로커를 어떻게 발견하는지를 파헤쳐야 합니다.

클라이언트-브로커 발견 메커니즘

Kafka 클라이언트는 대상 토픽 아래에 있는 파티션의 수와 해당 파티션 복제본이 어떻게 분산되었는지에 따라 올바르게 작동하기 위해 거의 모든 브로커를 알아야 합니다. 전형적인 접근 방식:

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

- 고객은 부트스트랩 브로커 중 하나를 선택하여 어떤 브로커가 어떤 파티션을 담당하는지의 메타데이터를 가져옵니다.
- 고객은 이러한 브로커들과 연결을 설정하도록 요청합니다.

![이미지](/assets/img/2024-06-23-SeamlessTransitionMigratingKafkaClustertoKubernetes_6.png)

따라서 EC2의 브로커에 대해 정의된 리버스 프록시에 추가로, K8s의 브로커에 대한 리버스 프록시도 클라이언트에게 제공되어야 합니다. 모든 브로커가 동일한 클러스터 내에 있으므로, EC2 또는 K8s에 있더라도 어떤 브로커든 클라이언트에게 전체 메타데이터를 제공할 수 있을 것입니다.

![이미지](/assets/img/2024-06-23-SeamlessTransitionMigratingKafkaClustertoKubernetes_7.png)

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

HashiCorp의 Consul 서비스는 서비스 검색에 사용됩니다. 새로운 리버스 프록시 서비스의 IP 주소를 기존의 Kafka Consul 서비스에 등록함으로써, 클라이언트는 두 세트의 브로커를 모두 발견할 수 있습니다. K8s에서 각 브로커의 홍보 리스너와 해당 홍보 리스너의 리버스 프록시는 모두 K8s 서비스가 될 수 있습니다.

## 이주 실행 및 오케스트레이션

지금까지 잘 진행되고 있습니다. EC2 또는 K8s에서 상호 연결된 브로커로 Kafka 클러스터를 구성하는 것이 가능합니다. 클라이언트는 모든 브로커에 연결하여 발견할 수 있습니다. 해결해야 할 다음 질문은 복제본이 어떻게 다른 브로커로 옮겨질 수 있는지, 그 방법은 무엇인지 입니다.

복제본 이동의 실행은 Cruise Control에 의해 가능합니다. 이는 대규모로 Kafka 클러스터를 관리하기 위한 풍부한 API를 제공합니다. 복제본을 이동하기 위해 사용할 수 있는 엔드포인트 중 하나는 remove_broker 입니다. 이 엔드포인트는 모든 해당 브로커의 복제본을 대상 브로커로 이동시켜 브로커를 비활성화합니다.

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

```js
curl -X POST "$CRUISE_CONTROL_SERVICE/remove_broker?brokerid=11&concurrent_partition_movements_per_broker=25&destination_broker_ids=31&replication_throttle=100000000"
```

여기에서 우리가 필요한 것이 바로 이거에요! 위 요청은 Cruise Control에게 모든 레플리카를 브로커-11에서 브로커-31로 이동하도록 요청합니다. 동시에 한 번에 이동 가능한 레플리카 수는 최대 25개이며, 총 복제 쓰로틀 속도는 약 100Mb/s로 설정되어 있습니다.

## Orchestration

크루즈 컨트롤 엔드포인트를 수동으로 호출하는 것은 이상적이지 않습니다. 안전하거나 효율적이지 않기 때문이죠. 그래서 우리는 이 상황을 위해 Kafka-Migration이라는 오케스트레이션 시스템을 구축했습니다. 이 시스템은 다음과 같은 역할을 합니다:

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

- 마이그레이션 조건 확인: 예를 들어, 복제 이동 호출을 실행하기 전에 클러스터가 건강한 상태인지, 대상 및 목적지 브로커가 동일한 가용 영역(AZ)에 있는지 확인합니다.
- 다양한 마이그레이션 모드: 시스템에는 다양한 마이그레이션 모드가 있습니다. '특정 브로커' 모드는 특정 브로커 세트에서 데이터를 다른 브로커로 이동할 수 있습니다. 'AZ' 모드는 기존 브로커에서 목적지 브로커로 데이터를 이동하는 것을 용이하게 합니다. '완전한 마이그레이션' 모드는 새로운 브로커 세트로의 완전한 데이터 이동을 관리하며 올바른 복제 할당을 보장하고 AZ별로 이동을 조정합니다. 이러한 모드와 규칙을 설정하여 복제 진행이 안전하고 관리 가능한 범위 내에 있음을 보장합니다. 결과적으로 영향을 최소화하고 이동 중 발생하는 사건에 신속히 대응할 수 있습니다.
- 세밀한 마이그레이션 제어: 단방향 마이그레이션 뿐만 아니라 중지, 재개 및 되돌리기 작업도 지원합니다. 예를 들어, 트래픽 급증으로 인해 마이그레이션을 일시 중지하여 운영 트래픽에 더 많은 대역폭을 제공한 다음 트래픽이 다시 감소하면 마이그레이션을 재개할 수 있습니다.

![링크 없음](/assets/img/2024-06-23-SeamlessTransitionMigratingKafkaClustertoKubernetes_8.png)

## 메트릭 및 모니터링

메트릭을 수집하고 상단에 모니터를 구축하는 것은 문제 감지와 클러스터가 마이그레이션 중에 어떻게 수행되는지에 대한 통찰을 얻는 데 중요합니다. Kafka에 대해 수집해야 할 세 가지 다른 메트릭 소스가 있습니다: Kafka 브로커 JMX 메트릭, 브로커 시스템 수준 메트릭 및 AWS EBS 메트릭입니다.

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

JMX 및 EBS 메트릭은 대체로 EC2 및 K8s 전반에 걸쳐 크게 일관성이 있어서, 마이그레이션 중에 본질적으로 변경되지 않습니다. EC2와 K8s 브로커의 메트릭에서 유일한 차이점은 태그에 있습니다. 이 태그를 기존 모니터 시스템으로 다시 매핑하는 것은 간단합니다.

브로커 시스템 레벨 메트릭은 조금 흥미로운데, EC2는 가상 머신이지만 K8s 파드는 여러 개의 컨테이너 그룹입니다. VM 기반 메트릭은 때로 컨테이너 기반 메트릭에서 찾을 수 없는 경우가 있습니다. 예를 들어 호스트 CPU의 작업 부하를 시간별로 측정하고 시스템이 얼마나 바쁜지를 나타내는 로드 평균 메트릭은 컨테이너 수준에 직접적인 동등물이 없습니다. 아마도 CPU 쓰로틀링 비율 메트릭을 가장 유사한 대리자로 사용할 수 있지만 완전히 같지는 않습니다.

문제 탐지를 위해 고수준 메트릭에 의존하는 것이 시간별로 저수준 메트릭을 살펴보는 것보다 좋습니다. 사실, 이러한 고수준 Kafka 브로커 JMX 메트릭은 리소스 고갈 문제의 좋은 지표를 제공할 수 있습니다: 프로듀서 지연 시간, 네트워크 풀 용량, 요청 처리기 용량 및 복제 지연 시간.

## 브로커 중단 관리

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

마이그레이션 중에는 브로커 장애를 주의 깊게 관리해야 합니다. 브로커 장애란 브로커가 정상적으로 작동하지 않거나 사용 불가능해지는 문제나 사건을 의미합니다. 이는 강제적인(예: 하드웨어 고장으로 인한) 또는 자발적인(예: 브로커 롤링 재시작으로 인한) 장애일 수 있습니다.

잘못된 장애 관리는 가용성이 낮아지게 됩니다.

3개의 가용 영역(AZ)에 걸쳐 있는 브로커로 이루어진 Kafka 클러스터의 경우, 대부분의 토픽이 3개의 복제본과 최소 2개의 인-싱크 복제본으로 구성되어 있습니다. 한 가용 영역에서 일부 브로커를 잃는 것은 관리할 수 있는 일입니다. 그러나 서로 다른 두 가용 영역에서 브로커 두 대를 잃는 것은 문제가 발생할 수 있습니다. 왜냐하면 최소 인-싱크 복제본이 2개로 설정되어 있으면 "오프라인 파티션" 문제가 발생합니다.

이러한 시나리오가 발생하지 않도록 예방하는 시스템을 만들어야 합니다. 강제적인 장애는 피할 수 없는 것이지만 강제적인 장애 발생 시 자발적인 장애를 최소화할 수 있도록 개선할 수 있습니다.

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

커파 모니터링 서비스를 이용한 Pod 중단 예산

K8s에서 Pod 중단 예산(PDB)은 K8s에 배포된 브로커 Pod를 모니터링하기 위해 설정됩니다. PDB는 오프라인 브로커를 감지하면 해당 중단 예산을 깨어 K8s의 다른 브로커가 내쫓히는 것을 방지합니다.

그러나 PDB로는 EC2 인스턴스의 브로커 상태를 감지할 수 있는 방법이 없습니다. 우리는 Kafka 모니터링 서비스를 추가로 설정하여 EC2의 브로커 상태를 모니터링 범위에 포함시켰습니다. 이 서비스는 Under Replicated Partitions (URP)를 확인하여 카프카 클러스터 전반의 상태를 감시합니다. URP가 감지되면 서비스는 준비 상태로 전환됩니다.

PDB는 Kafka 모니터링 서비스와 K8s에 배포된 브로커 모두를 모니터링하도록 구성되어 있습니다. 이로써 브로커 Pod를 직접 감시하여 K8s의 오프라인 브로커뿐만 아니라 Kafka 모니터링 서비스를 통해 EC2의 오프라인 브로커도 감지할 수 있습니다.

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

![Image 1](/assets/img/2024-06-23-SeamlessTransitionMigratingKafkaClustertoKubernetes_9.png)

# Zookeeper Migration

카프카 브로커를 EC2 인스턴스에서 K8s로 성공적으로 이전했습니다. 그러나 Zookeeper 클러스터는 여전히 EC2 인스턴스에서 실행 중입니다.

![Image 2](/assets/img/2024-06-23-SeamlessTransitionMigratingKafkaClustertoKubernetes_10.png)

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

물론, Zookeeper 클러스터를 Kafka 브로커를 마이그레이션하는 방식과 유사하게 마이그레이션할 수 있습니다. 그러나 더 좋은 아이디어가 있습니다! Kafka 버전 3.6에서 Kraft가 드디어 General Availability에 도달했는데, 이는 Kraft로 구동된 컨트롤러 역할을 하는 브로커를 사용하여 Zookeeper를 대체할 수 있음을 의미합니다. 이미 브로커가 K8s에서 실행 중이기 때문에, 컨트롤러 역할을 하는 새로운 브로커 집합을 쉽게 설정할 수 있습니다.

![Kubernetes를 통한 Kafka 클러스터 마이그레이션](/assets/img/2024-06-23-SeamlessTransitionMigratingKafkaClustertoKubernetes_11.png)

# 요약

통합된 Kafka 클러스터 내에서 브로커 수준의 마이그레이션 전략을 적용하여, 우리는 모든 Kafka 클러스터를 K8s로 성공적으로 마이그레이션했습니다. 마이그레이션 프로세스는 클라이언트 관점에서 완전히 원활하며, 마이그레이션으로 인한 사건은 전혀 발생하지 않았습니다.

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

K8s에서 Kafka를 관리하는 것은 EC2보다 훨씬 쉽습니다. K8s가 제공하는 강력하고 확장 가능하며 유연한 플랫폼 덕분에 배포부터 모니터링, 확장, 그리고 자가 치유까지 많은 운영 작업이 자동화됩니다. 잘 정의된 K8s 객체와 Custom Resource Definition(CRD)을 통해 우리는 Kafka 주변에 오퍼레이터를 구축하여 운영 작업을 더욱 자동화할 수 있습니다.

개선된 자동화와 감소된 운영 부담은 우리에게 Kafka 생태계를 더 빠른 속도로 발전시킬 수 있는 기회를 제공할 뿐만 아니라, 팀의 개발 시간을 새로운 개발 작업에 집중할 수 있도록 해줍니다.
