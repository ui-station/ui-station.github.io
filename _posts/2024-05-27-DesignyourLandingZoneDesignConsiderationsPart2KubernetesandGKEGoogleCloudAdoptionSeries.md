---
title: "랜딩 존 디자인하기  디자인 고려 사항 파트 2  쿠버네티스와 GKE Google Cloud 채택 시리즈"
description: ""
coverImage: "/assets/img/2024-05-27-DesignyourLandingZoneDesignConsiderationsPart2KubernetesandGKEGoogleCloudAdoptionSeries_0.png"
date: 2024-05-27 17:25
ogImage:
  url: /assets/img/2024-05-27-DesignyourLandingZoneDesignConsiderationsPart2KubernetesandGKEGoogleCloudAdoptionSeries_0.png
tag: Tech
originalTitle: "Design your Landing Zone — Design Considerations Part 2 — Kubernetes and GKE (Google Cloud Adoption Series)"
link: "https://medium.com/google-cloud/design-your-landing-zone-design-considerations-part-2-kubernetes-and-gke-google-cloud-5a500384cb03"
---

마음에 드시는 markdown 형식의 표를 아래에 참조해보세요:

| 구분                      | 설명                                                                         |
| ------------------------- | ---------------------------------------------------------------------------- |
| 착륙 지역이란?            | 착륙 지역의 정의 및 필요성에 대해 다룸                                       |
| 디자인 프로세스 개요      | 착륙 지역 디자인 프로세스 개요 소개                                          |
| 디자인 고려 사항 카테고리 | 착륙 지역 디자인 시 고려해야 할 7가지 카테고리 및 주요 디자인 결정 사항 소개 |

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

# 8. Google Kubernetes Engine (GKE)

앞서 이 시리즈에서 말씀드렸듯이, 컨테이너는 훌륭해요! 가벼우며 빠르고 휴대성이 좋으며 쉽게 확장할 수 있어요. 이것들은 마이크로서비스 아키텍처에 잘 어울려요.

Google Cloud는 컨테이너를 실행하는 몇 가지 다른 방법을 제공하고 있어요. 그러나 클라우드에서 현대적인 컨테이너 기반 작업을 실행한다면 Kubernetes가 필요하겠죠. 그리고 Google Cloud에서 Kubernetes를 실행한다면 Google Kubernetes Engine을 사용해야 해요.

GKE은 Google의 관리형 Kubernetes 플랫폼이에요. 이를 통해 Google Cloud에서 Kubernetes를 실행할 수 있지만 자체 관리형 Kubernetes보다 여러가지 이점을 제공해요:

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

- GKE는 클러스터를 배포하고 쿠버네티스를 설치하며 노드를 등록하는 것을 처리해줍니다. 새로운 클러스터는 몇 분 안에 배포할 수 있어요!
- 쿠버네티스 제어 플레인 노드는 완전 관리되며 소비자로부터 완전히 추상화되어 있어요.
- 호스트는 구글이 투명하게 관리하고 패치하는 견고하고 미리 구성된 컨테이너 용도 OS에서 실행돼요.
- 구글은 릴리스 채널을 통해 쿠버네티스 업그레이드를 투명하게 관리해줘요.
- 클러스터는 워크로드 수요를 충족하기 위해 탄탄하게 자동으로 탄력적으로 확장돼요. (관리되는 인스턴스 그룹 및 오토스케일러와 로드 밸런서를 만들 필요 없이!)
- 비파괴적으로 자동 수리 및 불건전한 클러스터 노드의 교체를 자동으로 수행해줘요.
- 기본 제공 지역 고가용성.
- GKE는 Google Cloud Operations에 네이티브로 통합돼 있어서 모니터링, 로깅 및 메트릭스에 쉽게 액세스할 수 있어요.
- GKE Autopilot으로 클러스터는 기본 제공된 모베스트 프랙티스로 사전구성돼요.
- GKE Autopilot으로 실행 중인 워크로드에 비용을 지급하게 됩니다. (POD별 청구라고도 함) 클러스터를 배포하는 데 비용을 내야 하는 대신입니다. 이것은 게임 체인저에요!
- GKE 노드 풀 및 Autopilot에서 계산 클래스로 작업 부하에 필요한 특정 기계 유형을 할당할 수 있어요. 또한 AI/ML 학습과 같이 필요로 하는 워크로드에 GPU를 할당할 수 있어요.
- 완벽한 Google Anthos 서비스 메시와의 원활한 통합, 스스로 완전 관리 서비스로 제공할 수 있어요.

(참고로, 여기에서 구글 컴퓨트 엔진에서 자체 관리 쿠버네티스 환경을 실행하려는 것이 왜 좋지 않은 아이디어인지 다양한 이유에 대해 다뤘었어요.)

조금 고려해야 할 설계 결정과 최상의 모베스트 프랙티스가 있어요. 이 주제에 대한 시리즈를 진행할 수도 있겠지만 간략하게 여기서 고려 사항을 요약해드릴게요.

## GKE Autopilot 또는 GKE 표준?

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

![AutoPilot](/assets/img/2024-05-27-DesignyourLandingZoneDesignConsiderationsPart2KubernetesandGKEGoogleCloudAdoptionSeries_0.png)

안녕하세요! Autopilot은 기본 클러스터 배포 모드로 이미 장기간 사용되어 왔어요. 이 기능은 다음과 같은 몇 가지 모범 사례를 기본으로 제공합니다:

- 릴리스 채널로의 의무적 등록. 이것은 클러스터가 자동으로 패치되고 유지되며, 오래된 보안 취약한 쿠버네티스 버전을 실행할 수 없다는 것을 의미합니다.
- 클러스터는 regional로, 클러스터의 고가용성을 보장합니다.
- 클러스터 자동 스케일링 - GKE가 기본으로 노드 수를 자동으로 조정해 줍니다.
- 노드 자동 복구가 기본으로 활성화되어 있습니다.
- 보안 부팅이 있는 shielded 인스턴스로 노드가 구축됩니다.
- Workload identity가 사전 구성되어 있습니다. 이를 통해 쿠버네티스 서비스 계정이 구글 클라우드 IAM 서비스 계정처럼 작동할 수 있습니다. 따라서, GKE 서비스에서 구글 클라우드 API로의 세분화된 액세스 컨트롤을 제공할 수 있습니다.

그리고 이전에 언급한 것처럼, 실제로 배포되고 실행 중인 파드에 대해 비용을 지불하므로, GKE 클러스터를 비효율적으로 사용하는 국면에서 지출을 낭비하는 일을 피할 수 있습니다. (GKE Standard를 실행할 때 흔히 발생하는 문제입니다.)

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

대부분의 경우 Autopilot을 권장합니다. 일부 특정한 경우에는 Standard를 사용하고 싶을 수도 있습니다. 예를 들어:

- TPU와 함께 노드를 배포하고 싶을 때.
- 특정 버전의 Kubernetes를 실행하고 자동 업그레이드를 사용하지 않으려는 경우. (일반적으로 이는 좋지 않은 아이디어이며, 관리형 서비스를 사용하는 의도를 상쇄시키는 것과도 같습니다.)
- 구글 Autopilot 허용 목록에 없는 특권있는 팟 (즉, 상승된 권한이 필요한 작업 부하)을 실행하고 싶을 때.
- 실험적인 작업 부하를 단일 존 클러스터에 배포하고 가용성을 보장할 필요가 없는 경우.

## Multitenant 대 Single Tenant 클러스터?

이것은 이전보다는 덜 검정색과 흰색의 문제입니다. Autopilot 이전에는 답이 명확했습니다: 가능한 한 많은 Multitenant 클러스터를 사용하십시오. 아이디어는 매우 적은 클러스터를 가지고 각 클러스터가 조직 내 다중 테넌트로부터 작업 부하를 호스팅한다는 것입니다. 여기서 테넌트는 일반적으로 조직 내에서 다른 팀들을 의미합니다. 그리고 각 테넌트는 하나 이상의 네임스페이스를 소유하게 됩니다.

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

멀티테넌트 GKE 클러스터를 사용하면:

- 관리해야 할 클러스터가 하나뿐이기 때문에 클러스터 관리 부담이 비교적 적습니다.
- 개별 테넌트/애플리케이션은 클러스터를 프로비저닝할 필요가 없습니다. 기존 클러스터에 작업량을 배포하기만 하면 됩니다.
- 클러스터 자체의 관리 책임을 클라우드 플랫폼 팀에 위임할 수 있어, 애플리케이션 팀은 GKE 관리 책임을 신경 쓸 필요가 없습니다.
- 테넌트 간의 격리는 네임스페이스 사용을 통해 달성합니다. 테넌트는 자신의 네임스페이스에 배포합니다.

한편, 많은 수의 소규모 단일 테넌트 GKE(표준) 클러스터가 있는 경우 — 즉, 각 클러스터가 하나의 애플리케이션 서비스를 호스팅하는 경우 — 이는 비용이 많이 소요됩니다:

- 각 애플리케이션이 자체 클러스터를 관리해야 합니다. 이로 인해 각 팀에 상당한 클러스터 관리 부담이 발생합니다.
- 더불어 높은 가용성을 보장하기 위해 각 클러스터는 지역 전체에 최소 수의 노드를 배포해야 합니다. 단일 테넌트 애플리케이션의 경우, 이는 자주 응용프로그램의 요구 사항보다 훨씬 큰 최소 클러스터 크기에 이르게 됩니다. 결과적으로, 대규모로 과잉 프로비저닝되고 비효율적으로 사용되는 클러스터가 많이 생기게 됩니다. GKE 표준 제품을 사용하면 운영 중인 팟이 아닌 배포한 클러스터에 대한 비용을 지불하므로 많은 현금을 낭비하게 됩니다!

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

<img src="https://miro.medium.com/v2/resize:fit:440/0*sXXDeW5_LtHqMzi4.gif" />

Autopilot을 사용하면 단일 테넌트 클러스터가 많은 영향을 미치지 않습니다:

- 실행 중인 팟만 지불하게 되므로 사용하지 않는 클러스터를 대거 배포하여 자금을 낭비하지 않습니다.
- 박스에서 사전 구성된 많은 모베스트 프랙티스로 인해 테넌트 당 클러스터 관리 부담이 비교적 적습니다.

마무리로 말씀드리면, 가능한 경우에는 멀티테넌트 GKE Autopilot 클러스터를 사용하는 것이 좋습니다. 특별한 경우에만 단일 테넌트 클러스터를 사용하십시오.

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

조직에서 편안한 수준으로 배포된 몇 개의 다중 테넌트 클러스터를 설정할 수 있습니다. 예를 들어, 각 사업 라인에 맞춰 다중 테넌트 클러스터를 설정할 수 있습니다.

이전 부분에서 논의한 대로 공유 VPC 디자인을 중심으로 랜딩 존을 구축했다면, 일반적으로 다중 테넌트 GKE 클러스터를 공유 VPC 내에 배포하고 싶을 것입니다. 이는 다음과 같을 수 있습니다:

- "허브" VPC에 피어링된 다중 테넌트 GKE가 호스팅되는 공유 VPC
- 다른 공유 서비스가 호스팅되는 같은 공유 VPC

어쨌든, 플랫폼 팀이 공유 VPC가 있는 호스트 프로젝트와 다중 테넌트 GKE 클러스터가 있는 호스트 프로젝트를 소유하게 될 것입니다. 테넌트는 서비스 프로젝트를 소유하게 됩니다. 그들은 다중 테넌트 클러스터(네임스페이스 내)에 직접 워크로드를 배포할 수 있으며, 비-GKE 리소스를 서비스 프로젝트로 배포할 수 있습니다.

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

Google Cloud 문서는 이 방법을 다음과 같이 설명합니다:

![Google Cloud](/assets/img/2024-05-27-DesignyourLandingZoneDesignConsiderationsPart2KubernetesandGKEGoogleCloudAdoptionSeries_1.png)

## VPC-Native 또는 라우트 기반 클러스터?

라우트 기반 클러스터는 pod 간 트래픽에 대한 VPC 사용자 정의 라우트에 의존하는 클러스터입니다.

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

VPC-native 클러스터는 포드 주소 지정을 위해 별칭 IP 주소 범위를 사용하는 클러스터입니다. 이는 포드가 클러스터의 VPC 네트워크 및 연결된 VPC 네트워크 내에서 네이티브로 라우팅될 수 있음을 의미합니다. 포드 주소를 위해 사용자 정의 정적 경로의 구성이 필요하지 않습니다. 네트워크 관리 부담은 비교적 낮습니다.

VPC-native 클러스터는 RFC 1918 IP 범위 밖의 IP 주소를 사용할 수도 있습니다. 이는 많은 포드 IP 주소가 필요할 것으로 예상될 때 유용할 수 있습니다. 예를 들어, GKE는 240.0.0.0/4 CIDR 범위를 노드, 포드 및 서비스에 사용할 수 있어 추가로 2억 6800만 개의 IP 주소를 제공합니다!

Google은 VPC-native 클러스터를 권장합니다. 이것은 GKE Autopilot의 기본 설정이며, 2021년에 출시된 버전 1.21.0-gke.1500부터 GKE 표준의 기본 설정이었습니다.

요약하자면: VPC-native 클러스터를 사용하세요!

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

## 개인 클러스터?

기본적으로 GKE 클러스터는 공개적입니다. 이는 제어 평면과 워커 노드가 공개 IP 주소를 가지고 있음을 의미합니다. 그러나 조직이 개인 클러스터를 생성하는 것이 모법 사례입니다. 이렇게 하면 클러스터를 인터넷에서 격리할 수 있습니다.

개인 클러스터에서는:

- 워커 및 제어 평면 노드는 비공개 IP 주소만 가지고 있으며 인터넷에 노출되지 않습니다. 예를 들어 NodePort 유형의 서비스는 노드가 인터넷 라우팅 가능한 공개 IP 주소를 가지고 있지 않기 때문에 인터넷에서 클라이언트에게 접근할 수 없습니다.
- 노드는 Google API 및 서비스와 통신하기 위해 Private Google Access를 사용합니다.
- 인터넷으로의 외부 액세스는 Cloud NAT 또는 Anthos Service Mesh 이그레스 게이트웨이와 같이 구현한 특정 제어를 통해서만 가능합니다.
- 외부 클라이언트로부터의 들어오는 연결은 외부 서비스를 통해서만 허용됩니다. 일반적으로 다음을 사용합니다: 외부 Ingress를 가진 LoadBalancer 서비스 또는 Anthos Service Mesh 공개 인그레스 게이트웨이.
- 워커 노드와 GKE 제어 평면 사이의 통신은 제어 평면의 비공개 엔드포인트를 통해 이루어지며, 제어 평면에 액세스해야 하는 추가 네트워크는 승인되어야 합니다.

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

<img src="/assets/img/2024-05-27-DesignyourLandingZoneDesignConsiderationsPart2KubernetesandGKEGoogleCloudAdoptionSeries_2.png" />

## 클러스터간 IP 주소 범위 공유

VPC 네이티브 클러스터를 생성할 때, 노드, 파드 및 서비스에 대한 IP 주소를 할당해야 합니다.

- 노드 IP 주소: 클러스터의 노드인 호스트 머신에 사용되는 주소입니다. 클러스터는 노드에 IP 주소를 할당하기 위해 서브넷의 기본 IPv4 범위를 사용합니다. 노드의 크기를 예상하는데 있어, 가장 큰 크기를 기준으로 서브넷의 크기를 설정해야 합니다.
- 파드 IP 주소: 클러스터는 파드에 IP 주소를 할당할 때 보조 IPv4 주소 범위를 사용합니다. GKE에서 가장 큰 IP 주소 요구사항이며, 각 노드는 많은 수의 파드를 호스트할 수 있습니다. 기본적으로 GKE Autopilot은 노드 당 최대 파드 수를 32개로 설정하고, 각 노드 당 64개의 IP 주소를 허용하여 파드 교체를 허용합니다. Kubernetes는 각 노드에 보조 IP 주소 범위를 할당하여 각 파드에 고유한 IP 주소를 할당합니다. 기본적으로 GKE Autopilot은 /17 보조 서브넷 범위를 할당하므로 32766개의 사용 가능한 파드가 허용됩니다. (이는 약 1000개가 넘는 노드를 가진 클러스터와 동등합니다.)
- 서비스(ClusterIP) IP 주소: 클러스터는 내부 서비스 주소를 위해 별도의 보조 IP 주소 범위를 사용합니다. 서비스 IP는 ClusterIP 서비스에 할당됩니다; 이는 클러스터 내에서만 접근 가능한 가상 IP 주소입니다. GKE Autopilot에서는 버전 1.27부터 디폴트로 Google 관리 네트워크에서 IP 주소를 할당하며 범위는 34.118.224.0/20입니다. 동일한 범위가 각 클러스터에 할당됩니다. 따라서 각 클러스터마다 4천 개 이상의 서비스 주소가 제공되며, 기관은 GKE 내의 서비스를 위해 IP 주소를 할당하거나 예약할 필요가 없습니다.

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

<img src="/assets/img/2024-05-27-DesignyourLandingZoneDesignConsiderationsPart2KubernetesandGKEGoogleCloudAdoptionSeries_3.png" />

동일한 공유 VPC에서 몇 개의 클러스터를 실행해야 할 수도 있습니다. 예를 들어, 업무별로 클러스터를 정의하고 싶을 수도 있습니다. (이미 언급했듯이, 많은 작은 단독 사용자 클러스터를 갖는 것은 좋지 않은 아이디어입니다.) 이 경우, 같은 서브넷에 호스팅된 클러스터 간에 기본 및 보조 IP 주소 범위를 공유할 수 있습니다. 이것은 하는 것이 좋은 일입니다:

- 클러스터당 서브넷을 할당할 필요가 없습니다.
- 클러스터당 서브넷 크기를 고려할 필요가 없습니다.
- 네트워크 관리 오버헤드를 줄일 수 있습니다.
- IP 주소를 효율적으로 절약하고 IP 주소 고갈 위험을 줄입니다.

VPC에서 클러스터 간에 범위를 공유하기로 결정한다면, 이 점을 주의해야 합니다:

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

- 미리 명명된 서브넷을 정의하세요.
- 100.64.0.0/10(약 4.2 백만 개의 파드 사용 가능) 및 240.0.0.0/4(약 2억 6천 8백만 개의 파드 사용 가능)와 같은 RFC 1918 프라이빗 CIDR 범위를 사용하는 것을 고려해보세요.

## 릴리스 채널

여기에서는 GKE 클러스터가 업그레이드되고 유지보수되며 패치되는 전략을 결정합니다.

쿠버네티스 버전은 x.y.z 형식으로 표시되며, 여기서 x는 주요 버전, y는 부 버전, z는 패치 버전을 나타냅니다. 일반적으로 매년 세 번에서 네 번의 중요 (주요 또는 부) 쿠버네티스 릴리스가 있으며, 패치 릴리스는 일주일에 한 번씩 발생합니다. 주요 및 부 릴리스는 새로운 기능과 보안 패치를 모두 포함합니다. 특정 부 릴리스는 대략 1년간 지원됩니다. 따라서 언제든지 일반적으로 지원되는 부 릴리스가 약 세 개 있을 것입니다.

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

![image](/assets/img/2024-05-27-DesignyourLandingZoneDesignConsiderationsPart2KubernetesandGKEGoogleCloudAdoptionSeries_4.png)

내 추천 (그리고 구글의 추천)은 항상 클러스터를 릴리스 채널에 등록하는 것입니다. 릴리스 채널을 사용하면 구글이 클러스터 업그레이드를 관리해줍니다. 워커 노드는 롤링 서지 업그레이드 전략을 사용하여 자동으로 업그레이드되어 워크로드에 미치는 영향을 최소화합니다. 따라서 클러스터 관리자는 클러스터 업그레이드에 시간이나 노력을 들일 필요가 없습니다.

GKE Standard를 사용하면 릴리스 채널을 사용하지 않을 수 있습니다. 이렇게 하려면 클러스터를 특정 버전의 Kubernetes로 유지하고 싶을 때 사용할 수 있습니다. 그러나 제 경험상, 릴리스 채널에 선택적으로 가입하지 않는 기관은 곧 자신들의 취약한 클러스터가 가득한 시스템을 운영하게 됩니다. 그러한 기관들은 큰 패치 문제를 안게 될 것입니다!

![image](/assets/img/2024-05-27-DesignyourLandingZoneDesignConsiderationsPart2KubernetesandGKEGoogleCloudAdoptionSeries_5.png)

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

GKE Autopilot을 사용하면 릴리스 채널을 선택해 사용하지 않을 수 없습니다. (정말 좋은 점이에요!)

(참고로: GKE 컨트롤 플레인 노드는 항상 Google에 의해 업그레이드되며, 이 프로세스를 거부할 방법이 없습니다. 이는 릴리스 채널 등록 여부와 상관없이 해당됩니다.)

선택할 수 있는 세 가지 릴리스 채널이 있습니다:

- 빠른(Rapid) — 오픈 소스 릴리스가 일반적으로 사용 가능한 후 몇 주 후에 Kubernetes 클러스터가 업그레이드됩니다. 이 채널은 가장 최신 기능을 제공하지만 가장 안정성이 낮습니다. 또한, 이 릴리스 채널의 클러스터는 GKE SLA에서 지원되지 않을 것입니다.
- 보통(Regular) — Kubernetes가 릴리스 후 2~3개월 후에 Rapid에서 실행되고 있는 릴리스 버전으로 업그레이드됩니다. 새로운 기능과 안정성 사이의 균형을 제공합니다. 기본적으로 GKE Autopilot은 노드를 이 릴리스 채널에 등록합니다.
- 안정(Stable) — Kubernetes가 Regular에서 실행되고 있는 릴리스 버전으로 업그레이드됩니다. 이 릴리스는 커뮤니티에서 약 5~6개월동안 사용 가능한 후 클러스터 노드에 적용됩니다. 이 채널은 가장 검증되었고 가장 안정적일 것이지만 최근 Kubernetes 기능을 제공하지 않을 것입니다.

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

새로운 GKE 버전이 릴리스 채널에서 기본 버전이 되면 해당 클러스터는 일반적으로 10일 이내에 업그레이드됩니다.

다음은 릴리스 채널 채용에 대한 일반적인 권장 사항입니다:

- 생산 워크로드는 클러스터의 중요성 및 위험 수용 능력에 따라 Stable 또는 Regular에 등록해야 합니다. 중요한 워크로드는 Stable에 등록하는 것을 권장합니다.
- 최종 비생산 환경(일반적으로 스테이징, Pre-Prod 또는 UAT 환경)은 생산 환경과 동일한 릴리스 채널에 등록되어야 합니다. 왜냐하면 새로 시도해보지 않은 Kubernetes 버전에서 워크로드가 생산 환경에 배치되는 것을 원치 않기 때문입니다.
- 상위 비생산 환경(예: Dev 또는 QA)은 이웃한 상위 릴리스 채널에 배포되어야 합니다.
- 새로운 기능을 실험하려는 개발 환경에서만 빠른 릴리스 채널을 사용해야 합니다. 새로운 기능은 안정적인 릴리스 채널에 몇 달 내에 나타날 것을 기억해주세요.

예시:

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

![이미지](/assets/img/2024-05-27-DesignyourLandingZoneDesignConsiderationsPart2KubernetesandGKEGoogleCloudAdoptionSeries_6.png)

릴리스 채널의 한 가지 문제는 클러스터가 언제 업그레이드될지 보장할 수 없다는 것입니다. 최종 비 프로드 환경 및 프로드 환경을 동일한 릴리스 채널(구글에서 권장하는 최상의 방법)에 등록하면 비 프로드 환경이 프로드 환경보다 먼저 업그레이드되도록 보호막을 구현하고 싶을 것입니다. GKE fleets 및 scopes를 사용하면 쉽게 이를 수행할 수 있습니다.

다음은 이러한 배포 순서의 예시입니다:

![이미지](/assets/img/2024-05-27-DesignyourLandingZoneDesignConsiderationsPart2KubernetesandGKEGoogleCloudAdoptionSeries_7.png)

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

이 예에서:

- Staging 환경의 GKE 클러스터는 Staging 플리트에 속합니다.
- Production에 있는 클러스터는 Production 플리트에 속합니다.
- Staging 및 Production에 있는 모든 클러스터는 Stable 릴리스 채널에 등록되어 있습니다.
- Production 플리트는 Staging 플리트가 7일간 실행된 후에만 업그레이드됩니다.

## Workload Identity Federation

워크로드 ID 페더레이션은 Kubernetes 서비스 계정이 IAM 서비스 계정으로 작동할 수 있게 합니다. 구성된 Kubernetes 서비스 계정을 사용하는 파드는 Google Cloud API에 액세스할 때 자동으로 IAM 서비스 계정으로 인증합니다. 이를 통해 GKE 애플리케이션 워크로드가 인증되고 허가되어 Google Cloud 서비스에 액세스할 수 있도록 보장할 수 있습니다.

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

오토파일럿 클러스터는 GKE에서 기본적으로 워크로드 ID 연합을 지원합니다.

## 오토스케일링 전략

오토스케일링은 클러스터가 실행 중인 응용 프로그램의 수요를 충족하기 위해 조정할 수 있는 능력을 가리킵니다.

GKE에는 네 가지 스케일링 차원이 있습니다:

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

- 워크로드는 수요에 따라 가로 방향으로 확장됩니다. Pod를 추가하거나 제거함으로써 관리됩니다. 이는 가로 방향 팟 오토스케일러(HPA)에 의해 관리되며, 수요에 따라 빠르게 확장됩니다. HPA는 CPU 사용률과 같은 표준 메트릭 또는 초당 요청과 같은 사용자 정의 메트릭에 응답합니다.
- 인프라는 클러스터 노드를 추가하거나 제거함으로써 가로 방향으로 확장됩니다. 이는 예약된 팟을 수용하기 위해 클러스터 자동스케일러(CA)에 의해 예측적으로 관리됩니다. 예를 들어, 새로 생성된 팟을 예약할 노드가 없는 경우, 클러스터 자동스케일러가 새 노드를 만듭니다.
- 워크로드는 팟 크기를 조절함으로써 세로 방향으로 확장됩니다. 이는 세로 방향 팟 오토스케일러(VPA)에 의해 관리됩니다. VPA는 시간이 경과함에 따라 팟의 CPU 및 메모리 사용률을 모니터링하고 이에 따라 팟 크기를 조정합니다. 이로써 보다 최적화되고 비용 효율적인 팟 크기를 얻을 수 있습니다.
- 인프라는 가장 효율적인 팟의 바이너리 패킹을 달성하기 위해 최적화된 노드(VM) 사이즈로 노드 풀을 배포하거나 삭제함으로써 세로 방향으로 확장됩니다. 이를 "노드 자동 프로비저닝"이라고 하며, 다른 종류의 확장에 비해 상대적으로 느립니다.

아래는 몇 가지 팁입니다:

- GKE Autopilot을 사용할 때는 인프라 자동스케일링 (CA 및 NAP)이 Google에 의해 설정됩니다. 당신은 팟 오토스케일링 구성만 고려하면 됩니다.
- HPA와 VPA를 동시에 사용하지 마세요. 같은 자원 메트릭으로 HPA와 VPA를 함께 설정하는 것을 피하세요. 예를 들어, CPU 사용률에 대한 HPA와 VPA를 동시에 설정하는 것을 피하세요.
- 가로와 세로 방향 팟 오토스케일링을 관리하는 다차원 팟 오토스케일러(MPA)를 사용하면 워크로드 확장을 간단히 할 수 있습니다.

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

결론적으로:

- GKE 표준 버전을 사용하면 인프라 스케일링과 워크로드(파드) 스케일링을 모두 관리해야 합니다.
- GKE Autopilot을 사용하면 워크로드 스케일링에만 집중하면 되며, MPA를 사용하여 워크로드 자동 스케일링을 간편하게 할 수 있습니다.

## 인프라 및 워크로드 배포

Google은 클러스터 자체를 배포할 때 클라우드 인프라의 경우와 마찬가지로 인프라를 코드로 관리하는 것을 권장합니다(Terraform과 같은). 따라서 클러스터 및 네임스페이스를 배포할 때 IaC를 사용하세요.

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

귀하의 작업 로드를 배포하려면 — 배포, 서비스, 작업, StatefufSets, 인그레스, 정책 등 — 선언적 yaml 파일을 사용하여 네이티브 Kubernetes API 호출을 해야 합니다. Helm은 쿠버네티스에서 애플리케이션 배포를 관리하는 데 도움을 줄 수 있습니다.

## GKE 디자인 결정 요약

요약하자면: 고려해야 할 주요 Kubernetes 디자인 결정 사항과 각각에 대한 나의 권장 사항입니다.

- GKE, 또는 스스로 관리하는 방식? 권장 사항: GKE.
- GKE Autopilot 대 GKE Standard. 권장 사항: Autopilot.
- 멀티 테넌트 클러스터? 어떤 수준의 클러스터? 권장 사항: 멀티 테넌트.
- 싱글 테넌트 클러스터를 만들 수 있는 능력. 권장 사항: 예외적으로만.
- VPC 네이티브 또는 라우트 기반 클러스터? 권장 사항: VPC 네이티브.
- 프라이빗 클러스터? 권장 사항: 프라이빗 클러스터를 사용하세요.
- 클러스터 간 IP 주소 범위 공유? 추천 사항: 공유 VPC 내에 몇 개 또는 많은 클러스터가 있다면 이를 수행해야 합니다.
- 릴리스 채널? 항상 릴리스 채널에 등록하세요. 스테이징 및 프로드 클러스터를 동일한 릴리스 채널에 유지하세요. 클러스터가 올바른 순서로 업그레이드되도록 보장하기 위해 fleets 및 rollout sequences를 사용하세요.
- Workload identity? 네 — 사용하세요.
- 오토스케일링 전략? 워크로드 오토스케일링을 지원하기 위해 클러스터 오토스케일러를 사용하세요. GKE Autopilot을 사용할 때 클러스터 오토스케일링은 자동으로 관리됩니다. 워크로드 오토스케일링을 간소화하기 위해 MPA를 사용하세요.
- 인프라 및 워크로드 배포? 클러스터 배포 및 관리에는 IaC(예: Terraform)를 사용하세요. 애플리케이션 워크로드를 배포하기 위해 선언적 Kubernetes 매니페스트를 사용하세요.

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

# 마무리

GKE를 랜딩 존에서 성공적으로 디자인하는 데 고려해야 할 주요 사항들은 여기까지입니다. 다음 파트에서는 LZ 디자인 고려 사항을 완료하고, 로깅 및 모니터링 전략, 요금 청구, 인프라스트럭처 코드 (IaC)와 같은 주제를 다룰 것입니다.

# 떠나시기 전에

- 관심이 있을 것으로 생각되는 분과 공유해주세요. 그들에게 도움이 될 수도 있고, 저에게 정말로 도움이 됩니다!
- 박수를 부탁드립니다! 여러분은 한 번 이상 박수를 칠 수 있다는 걸 아시나요?
- 자유롭게 댓글을 남겨주세요 💬.
- 내 컨텐츠를 놓치지 않으려면 팔로우하고 구독해주세요. 내 프로필 페이지로 이동하여 이 아이콘들을 클릭해주세요:

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

![Image](/assets/img/2024-05-27-DesignyourLandingZoneDesignConsiderationsPart2KubernetesandGKEGoogleCloudAdoptionSeries_9.png)

# 링크

- Google Cloud의 랜딩 존: 필요성 및 생성 방법
- Google Cloud의 랜딩 존 디자인
- GKE Autopilot
- GKE Autopilot 특권 업무 용 업무
- GKE 클러스터 구성 옵션
- 기업용 GKE 멀티 테넌시에 대한 모베스트 프랙티스
- VPC 네이티브 GKE 클러스터
- GKE의 프라이빗 클러스터
- 외부 애플리케이션 로드 밸런서를 위한 GKE Ingress
- GKE로 이주할 때 IP 주소 계획
- GKE 네트워크 플래닝 2023 (William Denniss)
- GKE 릴리스 채널
- 롤아웃 순서로 GKE 클러스터 업그레이드
- GKE SLA
- GKE 자동 확장 (Kaslin Fields)
- GKE에서 비용 최적화된 애플리케이션에 대한 모베스트 프랙티스
- GKE를 위한 워크로드 ID 연합
- Google Cloud 아키텍처 프레임워크
- 기업용 기초 설계 청사진

# 시리즈 내비게이션

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

- 시리즈 개요 및 구조
- 이전: 랜딩 존 설계하기 — 디자인 고려사항 파트 1
- 다음: 랜딩 존 설계하기 — 디자인 고려사항 파트 3 — 모니터링, 로깅, 빌링 및 라벨링
