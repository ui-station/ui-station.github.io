---
title: "Kubernetes 시작하기"
description: ""
coverImage: "/assets/img/2024-05-20-StartingyourjourneyonKubernetes_0.png"
date: 2024-05-20 17:13
ogImage:
  url: /assets/img/2024-05-20-StartingyourjourneyonKubernetes_0.png
tag: Tech
originalTitle: "Starting your journey on Kubernetes"
link: "https://medium.com/@martin.hodges/starting-your-journey-on-kubernetes-e9c3a57b59cd"
---

## 최근에 저는 Kubernetes와 관련된 다양한 기술의 사용에 대한 여러 기사를 쓰고 있습니다. Kubernetes 클러스터를 자동으로 구축하는 공개 프로젝트에 대한 기사를 소개하기 전에 Kubernetes 클러스터가 무엇이고 왜 필요한지 설명해야겠다고 생각했습니다.

![StartingyourjourneyonKubernetes_0.png](/assets/img/2024-05-20-StartingyourjourneyonKubernetes_0.png)

# Microservices

Kubernetes에 대해 알아보기 전에 이가 해결하는 문제를 이해하는 것이 중요합니다.

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

현대 소프트웨어 개발은 마이크로서비스로 이동했습니다. 이 아키텍처는 솔루션을 작은 독립적인 애플리케이션으로 분해하여 API를 통해 연결합니다. 이러한 각 애플리케이션을 마이크로서비스라고합니다.

![Starting Your Journey on Kubernetes](/assets/img/2024-05-20-StartingyourjourneyonKubernetes_1.png)

이 아키텍처는 매우 성공적이며, 솔루션의 개발과 진화를 훨씬 빠르게 할 수 있습니다.

그러나 이 아키텍처의 단점은 이제 여러 마이크로서비스를 모든 서버에 배포하고 그들 사이의 네트워크 연결을 설정해야 한다는 것입니다.

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

이제 이를 10, 20, 50, 100개의 마이크로 서비스로 확장해 보세요.

머리가 아프지 않나요? 아직 아프지 않다면, 곧 아플 겁니다.

이러한 모든 마이크로 서비스를 관리하는 것은 어렵습니다. 아키텍처가 인기를 얻고 나면 이를 관리하는 해결책이 등장했습니다.

## 컨테이너화

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

저희가 해결한 첫 번째 문제는 각각의 마이크로서비스가 서로 다른 종속성 집합을 가질 수 있다는 문제였습니다. 이 종속성에는 패키지, 도구 및 구성 요소가 포함될 수 있는데, 이들이 서로 호환되지 않을 수 있습니다.

컨테이너화를 통해 이 문제를 해결했습니다. 컨테이너화는 각 마이크로서비스가 자체적이고 가벼운 가상 환경인 컨테이너에서 실행될 수 있게 했습니다. 이 컨테이너들은 독립적으로 마이크로서비스를 운영할 수 있었으며 기본 호스트 환경에서 제공되는 네트워크를 통해 서로 연결할 수 있었습니다. 이러한 방식으로 어떠한 호환성 문제도 제거되었습니다.

이로써 부분적인 어려움은 해결되었지만, 이제 이러한 컨테이너를 배포하고 연결해야 합니다. 이때 필요한 것이 쿠버네티스입니다.

# 그렇다면 쿠버네티스란 무엇인가요?

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

클라우드에 마이크로서비스 애플리케이션을 배포하기로 결정했다고 가정해봅시다. 빠른 검색 결과로 구글에서 개발한 플랫폼인 Kubernetes가 필요하다는 것을 알게 됐습니다. 이 플랫폼은 구글의 비즈니스를 지원하기 위해 개발되었으며 이후에는 클라우드 네이티브 컴퓨팅 재단（CNCF）에 오픈 소스 솔루션으로 기부되었습니다.

귀하의 검색 결과에 따르면, 위키피디아와 많은 다른 사이트들이 Kubernetes를 다음과 같이 설명합니다:

당신은 무슨 생각인지 모르겠지만, 제게는 실제로 무엇인지 이해하는 데 도움이 되지 않았어요.

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

## 클라우드용 운영 체제

나는 쿠버네티스를 '클라우드'를 위한 운영 체제로 생각합니다.

저는 클라우드를 실제 또는 가상 서버 집합이 실제로 무엇인가의 추상화라고 생각합니다. 이 서버들은 AWS 또는 Azure와 같은 공급 업체에서 제공될 수도 있고 귀하의 데이터 센터에서 제공될 수도 있습니다.

그러나 이 클러스터의 운영 체제로 쿠버네티스를 생각해 봅시다. 먼저 컴퓨터의 운영 체제에 대해 생각해보십시오. 이 운영 체제는 컴퓨터의 리소스를 관리합니다.

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

- 메모리 및 디스크 공간
- 응용 프로그램 및 패키지 설치
- 사용자
- 보안
- 네트워킹 및 연결성

Kubernetes는 클러스터에서 비슷한 작업을 수행하며 클라우드에서 마이크로서비스를 관리할 수 있게 합니다.

이를 통해 다음을 수행할 수 있습니다:

- 마이크로서비스에 클라우드 리소스(서버 및 네트워크 구성 요소)를 동적으로 할당하여 필요에 따라 확장하고 축소하며 높은 부분의 신뢰성과 가용성을 달성할 수 있습니다.
- 마이크로서비스 및 도구를 쉽게 배포할 수 있습니다.
- 네트워킹 연결성 및 보안을 구성할 수 있습니다.
- 사용자 및 액세스 보안을 관리할 수 있습니다(예: Role Based Access Control — RBAC).

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

간략히 말해, 클라우드나 실제 또는 가상 클러스터에 마이크로서비스를 배포하고 관리할 수 있는 도구를 제공합니다.

## 다른 옵션이 있나요?

다양한 다른 옵션이 있지만 Kubernetes가 지금까지 가장 인기 있는 옵션입니다.

대안으로는:

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

- Amazon Elastic Compute Cloud
- OpenShift
- Google Kubernetes Engine
- Azure Kubernetes Service
- Cloud Foundry

이 모든 솔루션은 여러 서버의 클러스터에서 마이크로서비스를 구동하는 컨테이너를 관리합니다.

## Kubernetes를 사용해야 할까요?

Kubernetes를 사용해야 할 필요성에 대해 스스로 질문해볼 수 있습니다.

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

쿠버네티스 클러스터가 제공하는 가장 중요한 기회 중 하나는 휴대 가능한 환경을 만들어준다는 것입니다. 이는 쿠버네티스를 지원하는 어디에서나 배포 아키텍처를 거의 어디론가 가져갈 수 있게 해줍니다. 또한 다른 공급업체에 있는 클러스터를 활용할 수 있어서 더 높은 신뢰성과 가용성을 제공합니다.

또 다른 기능은 일련의 운영 작업을 자동화할 수 있는 능력입니다. 이를 통해 여러 가지 작업을 자동으로 처리해주어 마음의 안정을 줍니다. 이러한 작업에는 다음이 포함될 수 있습니다:

- 자동 업데이트
- 지속적인 통합/지속적인 배포 (CI/CD)
- 마이크로서비스 및 관리 도구의 자동 확장
- 고장난 마이크로서비스의 자동 재시작
- 리소스 비용 최적화
- 중앙 집중식 모니터링

따라서 만약 고가용성, 높은 신뢰성, 확장성, CI/CD 및/또는 클라우드 서비스 제공업체의 이동성이 필요하다면 쿠버네티스 클러스터를 고려해보시는 것이 좋습니다.

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

그러나 한 대의 서버에 단일 대형 애플리케이션을 배포하고 있고 (네, 이 경우가 존재합니다) 확장, 복원력, 신뢰성 또는 이식성에 대한 중요한 요구사항이 없다면, 마이크로서비스, 쿠버네티스와 같은 컨테이너 기반의 관리 시스템을 유지하는 비용을 피하고 애플리케이션을 해당 서버에 배포하는 것이 훨씬 나을 수 있습니다.

# 시작점

좋아요, 그래서 쿠버네티스를 사용하기로 결정했습니다. 이제 더 많은 것을 알고 싶어합니다.

쿠버네티스 학습 곡선을 시작할 때, 리소스를 이해해야 합니다. 간단히 말해서, 쿠버네티스는 리소스를 관리합니다.

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

리소스는 서버(Node), 애플리케이션(Pod), 서비스(Service) 또는 아래 다이어그램에 표시된 것(그리고 그 외에 더 많은 것들)일 수 있습니다.

![다이어그램](/assets/img/2024-05-20-StartingyourjourneyonKubernetes_2.png)

이 표준 리소스 외에도 Kubernetes는 사용자 정의 리소스를 관리하기 위한 플러그인을 허용합니다.

본질적으로 Kubernetes는 리소스 목록(매니페스트라고 함)을 가져와 이를 클러스터 내 서버(또는 노드)에 구축하고 유지하는 명령어 세트로 변환합니다. 목록을 가져와 Kubernetes가 실제 리소스를 설정하도록 하는 이 개념을 선언적 구성이라고 합니다. 목록을 변경하면 Kubernetes가 클러스터 내에서 필요한 변경 사항을 결정합니다.

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

<img src="/assets/img/2024-05-20-StartingyourjourneyonKubernetes_3.png" />

Kubernetes는 이 개념을 더 발전시킵니다. 이는 클러스터 내의 리소스를 모니터링하고, 만약 리소스가 실패하거나 더 많은(또는 적은) 리소스가 필요한 경우에는 자동으로 실행 중인 리소스를 필요에 맞게 조정할 수 있습니다.

부하가 증가하면 더 많은 리소스를 할당할 수 있습니다. 리소스가 실패하면 오작동한 리소스를 삭제하고 새 리소스를 생성할 수 있습니다. 리소스를 업데이트하려면 서비스 중단이 없는 방식으로 업그레이드를 배포할 수 있습니다.

# 리소스의 종류

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

알겠어요, 자원에 대한 토론은 조금 추상적이죠. 자원의 몇 가지 구체적인 예시를 살펴볼게요.

## 노드

Kubernetes 클러스터를 생성할 때, 먼저 생성하는 자원은 클러스터를 형성할 서버(실제 또는 가상)입니다. 이러한 서버는 Kubernetes에서 노드라고 합니다.

마스터 노드와 워커 노드가 있습니다.

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

![Starting Your Journey on Kubernetes](/assets/img/2024-05-20-StartingyourjourneyonKubernetes_4.png)

마스터 노드(1개 이상)는 제어 평면을 제공합니다. 제어 평면은 자원 목록(하나 이상의 YAML 파일로 정의된 매니페스트로 알려진)을 가져와서 각 워커 노드 내에서 생성된 자원을 필요한 매니페스트와 일치하도록 관리합니다.

동적으로 워커 노드가 실패하면 해당 자원이 손실되고 제어 평면이 자동으로 해당 자원을 워킹 노드에 다시 생성합니다. 제어 평면은 항상 배포된 자원이 매니페스트 파일의 요청된 목록과 일치하도록 합니다.

일반적으로 클러스터에는 마스터 노드의 홀수 개수와 워커 노드의 홀수 개수가 있습니다. 이것은 일부 응용 프로그램 및 도구에서 지도자를 선정하는 데 도움이 됩니다. 대부분의 예는 1개의 마스터와 3개의 워커를 보여줍니다만 클러스터에는 여러 마스터와 수백 개의 노드가 있을 수 있습니다.

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

## 파드

이해해야 하는 다음 기본 리소스는 파드입니다.

파드는 하나 이상의 컨테이너 이미지(예: Docker 이미지)의 그룹으로, 애플리케이션 기능을 제공하는 역할을 합니다.

일반적으로 파드에는 하나의 컨테이너 이미지(애플리케이션 또는 마이크로서비스)만 포함됩니다.

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

Pods는 일시적입니다. 이는 Kubernetes가 언제든지 pod의 인스턴스를 삭제하고 옵션으로 클러스터의 다른 곳에 다시 생성할 수 있다는 것을 의미합니다. 특정 인스턴스가 항상 존재한다고 확신할 수 없지만, 원하는 경우 Kubernetes가 사용할 수 있는 인스턴스가 있도록 보장할 것입니다.

일부 경우에는 마이크로서비스를 외부에서 적용할 추가 로직이 필요할 수 있습니다. 이 로직을 동일한 pod 내의 다른 컨테이너 이미지로 추가할 수 있습니다. 주 응용 프로그램을 보조하는 경우 이를 sidecar로 알려져 있습니다.

한 pod 내에는 여러 개의 sidecar가 존재할 수 있습니다.

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

## 연결성

쿠버네티스는 네트워크의 모든 파드가 다른 파드에 연결할 수 있어야 하며(다른 노드에 있더라도), 네트워크 주소 전환(NAT)이 필요하지 않아야 합니다. 이를 위해 각 노드가 클러스터 파드가 사용할 새로운 서브넷에 가입해야 합니다. 나중에 어떻게 이 작업을 수행하는지 살펴보겠습니다.

마이크로서비스 애플리케이션과 사이드카는 각각의 파드 내에서 로컬호스트 네트워크를 통해 연결되며, 파드 자체는 쿠버네티스 클러스터 서브넷을 통해 통신합니다.

![이미지](/assets/img/2024-05-20-StartingyourjourneyonKubernetes_6.png)

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

## 자원 관리

우리는 두 가지 유형의 리소스만을 살펴보았고, 이에 대한 표면적인 내용만 다뤘습니다.

이제 당신은 마이크로서비스를 배포하기 위해 먼저 하나 이상의 매니페스트 파일을 생성해야 함을 이해했을 것입니다. 이 매니페스트 파일에는 마이크로서비스를 포드로 나열한 후 이를 쿠버네티스에 제공하여 실제로 필요에 따라 클러스터 노드 상의 포드로 마이크로서비스를 배포하도록 남기게 됩니다. 쿠버네티스는 포드 간의 네트워크 연결성을 관리할 것입니다.

매니페스트에는 배포가 가져야 하는 특성들을 제공할 수 있습니다. 이러한 특성들은 다음과 같습니다:

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

우리 인간들이 사용할 이름을 정하고
다른 리소스가 식별하고 선택할 수 있게 해주는 라벨 세트
필요한 인스턴스의 숫자
그리고 훨씬 더...

쿠버네티스는 이러한 모든 특성을 고려하여 요구 사항을 충족시키기 위해 필요한 모든 것을 자동으로 배포합니다.

정말 멋지죠.

쿠버네티스 이야기에는 더 많은 것이 있지만, 다른 기사에서 다룰 예정입니다.

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

# 네임스페이스

계속하기 전에, 쿠버네티스의 다른 멋진 기능 중 하나인 네임스페이스에 대해 설명해야 합니다.

파드와 같은 리소스는 다른 네임스페이스에 있으면 격리되어 처리됩니다. 네임스페이스는 실제 클러스터 내에서 가상 하위 클러스터로 생각할 수 있습니다. 다른 네임스페이스의 리소스도 여전히 다른 네임스페이스의 리소스에 연결할 수 있습니다.

네임스페이스는 이름 충돌을 방지할 뿐만 아니라 접근 제어를 통해 보안을 제공할 수도 있습니다. 이는 하나의 개발자가 한 네임스페이스에 액세스하고 다른 개발자가 다른 네임스페이스에 액세스할 수 있지만 둘 다 서로의 네임스페이스에 액세스할 수 없다는 것을 의미합니다.

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

네임스페이스는 Kubernetes에서 생성 및 관리할 때 리소스로 취급됩니다.

클러스터 내의 리소스 사이에 격리를 제공하는 동시에 네임스페이스는 리소스 관리에도 도움이 됩니다. 네임스페이스를 삭제하면 해당하는 모든 리소스도 함께 삭제됩니다.

```js
kubectl config set-context --current --namespace=new-default
```

일부 유형의 리소스는 클러스터 수준에서 생성되며 네임스페이스에서 생성할 수 없습니다. 예를 들어 Persistent Volume (PV)는 클러스터 수준에서 생성됩니다. PV를 생성하거나 관리할 때는 네임스페이스를 지정하지 않으며 (기본값 사용되지 않음)

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

실제로 네임스페이스는 클러스터 수준의 리소스입니다. 즉, 네임스페이스는 네임스페이스를 가질 수 없기 때문에 서로 중첩시킬 수 없습니다.

## 내부 동작

새로운 것에 대해 배울 때, 먼저 원리를 이해하고 나서 어떻게 작동하는지 빨리 알고 싶어해요.

관심이 있다면, 한번 속을 esditeyus.

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

## 쿠버네티스 구성 요소

각 노드 유형 내에는 쿠버네티스의 다양한 구성 요소가 있습니다.

![이미지](/assets/img/2024-05-20-StartingyourjourneyonKubernetes_7.png)

## 워커 노드

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

우선 워커 노드를 살펴보면, 각 워커 노드에는 세 가지 구성 요소가 있습니다:

- Kubelet…제어 플레인에 대한 인터페이스를 제공하고 노드의 상태를 업데이트하며, 제어 플레인이 노드에서 실행 중인 컨테이너를 관리하기 위해 제공하는 명령을 수행합니다.
- Kube Proxy…컨테이너 내에서 실행 중인 모든 응용 프로그램이 서로 직접 통신할 수 있도록 노드에서 네트워킹 관리를 제공합니다(아래에서 더 자세히 설명합니다).
- 컨테이너 관리…Kubernetes의 중심에는 애플리케이션이 컨테이너 내에서 이미지로 실행되는 것이 있으므로, 노드는 이러한 컨테이너를 실행할 수 있어야 합니다.

이 세 가지 구성 요소를 통해 클러스터가 응용 프로그램을 실행하고 이를 연결할 수 있습니다.

## 구성 요소 교체

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

궁금하시다면, 이러한 구성 요소는 Kubernetes를 설치할 때 기본적으로 제공됩니다. 그러나 귀하의 특정 요구 사항을 충족하는 구성 요소로 교체할 수도 있습니다.

예를 들어, Service라는 다른 리소스 유형이 있습니다. 이전에 일회성으로 이야기했던 팟은 언제든지 생성 및 소멸할 수 있다는 것을 의미합니다. 따라서 만약 이 팟이 다른 팟에 서비스를 제공한다면, 다른 팟이 그것을 어떻게 찾을까요?

서비스가 등장합니다. 서비스는 해당 서비스로의 요청을 작동 중인 마이크로서비스의 인스턴스로 경로 설정하는 방법을 알고 있는 고정된 지속적인 IP 주소를 제공합니다. 팟이 계속 생성 및 소멸해도 필요할 때 목적지 목록을 유지하고 조정합니다.

이를 위해 Kube Proxy는 노드의 운영 체제에 있는 기본 IP Tables을 구성하여 서비스로의 경로 지정 및 배경 마이크로서비스로의 경로 지정이 항상 작동하도록 보장합니다.

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

대부분의 사용 사례에는 잘 작동하지만, 서비스 수가 수백 개로 증가함에 따라 IP Tables의 규칙 길이가 너무 길어져 효율성이 떨어지게 됩니다.

이런 상황에서는 표준 Kube Proxy를 대체하여 IP Tables보다 더 효율적인 라우팅 방법을 사용하는 대안으로 교체할 수 있습니다. 관심이 있다면 'Kube Proxy 대안'을 검색해보세요.

이것은 일반적인 쿠버네티스 구성 요소를 특정 사용 사례에 맞게 대체하는 예시입니다.

## 마스터 노드

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

마스터 노드에서는 Kublet, Kube Proxy 및 컨테이너 관리 기능도 확인할 수 있습니다. 이를 통해 마스터 노드가 워커 노드로도 작동할 수 있습니다 (나중에 이에 대한 장단점에 대해 이야기하겠습니다).

워커 노드 구성 요소 외에도 마스터 노드에는 다음이 있습니다:

- API 서버… 이는 클러스터를 내부적으로나 kubectl과 같은 명령행 도구를 통해 외부적으로 관리하고 모니터링할 수 있는 API입니다.
- etcd… Kubernetes가 구성과 상태를 저장하는 데 사용하는 분산 키-값 저장소
- 스케줄러… 클러스터 전체의 pod를 추적하고 필요에 따라 생성 또는 제거하는 구성 요소입니다 (pod 스케줄링이라고도 함)
- Kube 컨트롤러 매니저… 필요한 리소스를 변경할 때 현재 상태를 필요한 리소스에 맞추기 위해 무엇을 수행해야 하는지 작업하는 구성 요소로, 스케줄러에게 pod 및 다른 리소스의 요구 사항을 제공합니다.
- 클라우드 컨트롤러 매니저… 경우에 따라 클러스터는 클라우드 제공업체가 자체를 구성해야 할 필요가 있는데 (예: IP 주소 제공 및 로드 밸런서 제공), 이 작업은 이 구성 요소를 통해 수행됩니다.

이들 구성 요소가 함께 워커 노드를 관리하는 제어 플레인을 형성합니다.

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

## 마스터 노드를 워커 노드로 사용

마스터 노드를 사용하여 마이크로서비스를 실행해야 하는지 여부에 대해 궁금해 할 수 있습니다.

한편으로는 마이크로서비스 작업을 마스터 노드에서 실행하지 말아야 하는 이유로 클러스터의 제어에 영향을 미치지 않기를 원하지 않으므로 그렇습니다.

이는 매우 타당한 주장이지만, 오퍼레이터라고 불리는 응용 프로그램이 있습니다. 오퍼레이터는 자동 보조 도구 역할을 합니다. 그들은 마이크로서비스를 설치, 관리, 장애 조치, 백업하고 일반적으로 마이크로서비스를 관리합니다. 이러한 오퍼레이터들은 마이크로서비스보다는 제어 평면 구성 요소와 비슷하게 작용합니다.

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

마스터 노드에서 연산자가 실행되는 것이 이해됩니다.

아마도 궁금해 할 수도 있지만, 두 가지를 모두 가질 수 있을 것입니다. 매니페스트 파일에서 리소스를 정의할 때, 파드가 어느 노드에 배치될 수 있는지에 대한 규칙을 정의할 수 있습니다. 이는 마그넷처럼 작용하여 파드를 끌어당기거나 밀어내는 것과 비슷합니다:

- Affinity(우호성) — 파드를 노드로 끌어당김
- Taints(오점) — 파드를 노드로 밀어냄
- Tolerations(용인) — 오점을 상쇄함

이들 간의 전반적인 상호작용은 복잡합니다. 이러한 규칙을 사용하면 워커 노드나 마스터 노드에 파드를 예약할 수 있습니다. 또한 한 노드에 두 개의 파드 인스턴스가 예약되지 않거나, 한 마이크로서비스가 다른 노드에 예약되지 않도록 할 수도 있습니다.

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

# 컨테이너 네트워크 인터페이스

아직 언급하지 않은 두 가지 구성 요소가 있습니다. 둘 모두 네트워크 구성과 관련이 있습니다.

Kubernetes는 모든 팟이 Network Address Translation (NAT)이 필요 없이 서로 통신할 수 있어야 한다고 요구합니다.

이는 Container Network Interface (CNI) 플러그인을 사용하여 달성됩니다. CNI는 Flannel, Calico, Weave, Cilium 등 여러 플러그인이 구현하는 표준이며 실제로 CNI 표준은 기타 컨테이너 관리 시스템에서도 사용됩니다.

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

![Starting your journey on Kubernetes](/assets/img/2024-05-20-StartingyourjourneyonKubernetes_8.png)

Kublet 컴포넌트가 포드와 그 컨테이너를 시작할 때, CNI를 호출하고 해당 포드에 IP 주소를 할당해 달라고 요청합니다. CNI는 또한 컨테이너 내의 네트워크 요소를 프로비저닝하여 클러스터 서브넷에서 포드 간의 원활한 통신을 가능케 합니다. 이러한 요소들은 포드가 예정된대로 스케줄되면 필요에 따라 삭제 및 업데이트됩니다.

CNI 플러그인은 다양한 솔루션을 사용하여 보편적 네트워크를 구현하는 복잡한 네트워크 구성 요소입니다. 이러한 것들을 설명하는 것은 이 글의 범위를 벗어납니다만, Kubernetes CNI를 검색하면 더 많은 정보를 얻을 수 있습니다.

# DNS

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

네트워크 솔루션의 두 번째 부분은 클러스터용 DNS 서비스입니다. Pod가 서비스에 액세스해야 할 때, 해당 서비스를 도메인 이름을 통해 찾습니다.

이전에 말했듯이, 서비스는 Pod가 일회성 Pod 중 하나 이상에 구현될 수 있음에도 불구하고 해당 서비스를 찾을 수 있는 자원입니다. 이러한 서비스는 클러스터 서브넷 내에서 고정된 영속적인 IP 주소를 부여받습니다.

```js
kubectl cluster-info dump | grep -m 1 service-cluster-ip-range
kubectl cluster-info dump | grep -m 1 cluster-cidr
```

IP 주소를 할당받을 뿐만 아니라, 서비스의 이름이 DNS 항목으로 사용됩니다. 예를 들어, hello라는 이름의 서비스를 world 네임스페이스에 생성한다고 가정해봅시다. 서비스가 생성되고 IP 주소를 부여받는데, 예를 들어 10.96.0.5라고 하겠습니다. 그럼 클러스터 DNS는 엔트리를 업데이트하면서 다음과 같은 항목을 추가합니다:

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
10.96.0.5 hello
```

동시에 파드는 /etc/resolv.conf 파일에 검색 기준을 제공받습니다. 예를 들면:

```js
search world.svc.cluster.local svc.cluster.local cluster.local
nameserver 10.96.0.10
options ndots:5
```

이는 서비스에 다음을 사용하여 액세스할 수 있음을 의미합니다:

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
hello;
hello.world;
hello.world.svc;
hello.world.svc.cluster.local;
```

필요한 경우 다른 네임스페이스에 있는 서비스를 참조할 수 있음을 알 수 있습니다.

DNS는 클러스터의 팟으로 설치된 coreDNS라는 애플리케이션에서 제공됩니다. 다른 솔루션도 사용할 수 있습니다. 다른 리소스와 마찬가지로 coreDNS는 추가적인 사용자 지정 매핑 규칙을 제공하기 위해 매니페스트 파일을 통해 구성할 수 있습니다.

# 실제 리소스 제한하기

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

이전에 언급했던 대로 Kubernetes는 클라우드용 운영 체제와 같습니다. 이런 표현은 너무 넓은 범위일 수도 있어요. 사실은 클러스터용 운영 체제와 비슷합니다.

또한 운영 체제가 리소스를 관리한다고 언급했습니다. 이 경우 리소스는 CPU, 메모리 및 저장 공간과 같은 계산 리소스를 말합니다.

이 비유는 상당히 일치합니다. Kubernetes에게 파드 내 컨테이너가 사용할 계산 리소스의 양을 할당하고 제한하도록 요청할 수 있어요. 이러한 계산 리소스는 노드가 제공하는 리소스에서 할당됩니다.

Kubernetes는 이러한 제한을 Unix 개념인 cgroups를 사용하여 적용합니다. 이 기사에서 cgroups에 대해 다루는 것은 범위를 벗어나지만, 프로세스가 사용하는 리소스를 제한할 수 있다는 것만 언급해도 충분해요. 제한은 다양한 방식으로 적용될 수 있어요. 예를 들어, CPU 제한에 도달하면 응용 프로그램이 간단히 느려지지만, 메모리 제한에 도달하면 오류가 발생하여 파드가 종료되고 재스케줄링될 수 있습니다.

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

리소스를 생성할 때 Kubernetes에게 지정된 계산 리소스 양이 있는 노드에 리소스를 예약하도록 요청할 수도 있습니다. 이것은 요청입니다. 요청은 제한이 설정되어 있는 것을 의미하지 않으며 요청 및 제한 구성은 독립적인 설정입니다.

# 클러스터에 리소스 정의 및 배포

여기까지 오셨다면 이제 Kubernetes가 하나 이상의 매니페스트 파일에 정의된 리소스 목록을 가져다가 클러스터에 배포한다는 것을 이해하게 될 것입니다. 그런 다음 클러스터를 모니터링하고 대상 상태를 유지하므로 리소스가 실패하면 대체할 수 있습니다.

Kubernetes는 또한 매니페스트 파일을 감시하며 필요한 리소스에 변경이 있으면 클러스터를 수정하여 요구 사항과 다시 일치하도록 유지합니다. 이는 리소스를 삭제해야 하는 경우에도 해당됩니다.

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

알겠어요, 지금 어떻게 매니페스트 파일을 구성하는지 궁금할 수도 있겠죠. 매니페스트 파일은 YAML 파일입니다. 각 리소스는 YAML 파일 내의 문서로 정의됩니다.

아마도 YAML 파일이 이렇게 구분되어있는 여러 문서를 포함할 수 있다는 것을 아실 겁니다:

```js
---
# 문서 1
---
# 문서 2
---
# 문서 3
---
```

이것은 여러 리소스를 (각 문서당 하나씩) 단일 매니페스트 파일에 정의할 수 있다는 것을 의미합니다. 여러 파일로 배포를 분리할지 또는 모두 단일 파일에 포함시킬지는 여러분의 선택입니다.

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

이 기사에서는 YAML 파일당 하나의 리소스를 가정합니다.

매니페스트 파일의 기본 구조는 아래와 같습니다:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment-1
  namespace: test-env
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
        testService: service-1
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
          volumeMounts:
            - name: nginx-index-config
              mountPath: /usr/share/nginx/html
      volumes:
        - name: nginx-index-config
          configMap:
            name: nginx-config-1
```

이 경우 NGINX 프록시를 배포하여 테스트 서비스를 정의하고 있습니다.

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

한 조각 씩 분석해 봅시다.

```js
apiVersion: apps/v1
...
```

이 부분은 Kubernetes에 어떤 API를 사용할 것인지 알려줍니다. 이 경우 API는 매니페스트 문서의 구조를 의미하며 (Kubernetes에 액세스하는 데 사용되는 API와는 구분됩니다), 모든 리소스 유형에는 연관된 구조와 정의된 API 버전이 있습니다.

사용자 정의 리소스 정의(CRD)를 포함하는 Kubernetes 확장을 로드하여 해당 사용자 정의 API 정의를 가져올 수 있습니다.

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

이 경우 apps/v1은 표준 Kubernetes API입니다.

```js
...
kind: Deployment
...
```

이것은 Kubernetes에게 우리가 생성하려는 리소스 유형을 알려줍니다. 이 경우 Deployment는 하나 이상의 팟에 응용 프로그램을 생성합니다.

```js
...
metadata:
  name: nginx-deployment-1
  namespace: test-env
...
```

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

메타데이터 섹션 또는 스탄자는 Kubernetes에 리소스에 대한 정보를 제공합니다. 이 경우에는 Deployment 리소스의 이름을 알려주어 나중에 참조할 수 있도록 합니다. 또한 해당 리소스가 주어진 test-env 네임스페이스에 생성되도록 지정합니다.

다음 섹션에는 더 많은 옵션이 있습니다. 여기서 사용한 옵션만 강조하겠습니다.

```js
...
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
...
```

이제 우리는 Kubernetes에 spec 스탄자에서 생성해야 할 리소스의 사양 또는 구성에 대해 알려줍니다.

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

이 경우에는 명세서가 다음과 같이 말합니다:

- replicas ... 내 애플리케이션의 인스턴스를 하나 생성합니다.
- selector ... 이 리소스는 이 규칙과 일치하는 레이블을 갖는 모든 리소스를 참조합니다 (app: ngix).

Selector가 항상 약간 혼란스럽습니다. 다음과 같이 생각해보세요. Kubernetes가 우리의 1개 복제본이 존재하는지 확인하러 오면, 복제본을 생성하거나 제거해야 하는지 확인하기 위해 파드 목록을 쿼리해야 합니다. 이를 찾기 위해 이 선택기 규칙을 사용합니다. 즉, app: nginx와 일치하는 레이블을 갖는 모든 파드입니다.

```js
...
spec
  ...
  template:
    metadata:
      labels:
        app: nginx
        testService: service-1
...
```

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

배포를 위해 Kubernetes에게 무엇을 배포해야 하는지 알려주어야 합니다. 이 스펙(spec) 안에는 어플리케이션을 배포하는 방법과 내용을 정의하는 템플릿을 지정합니다.

템플릿에는 메타데이터 섹션도 포함되어 있습니다. 이 섹션에는 이 템플릿을 사용하여 생성된 모든 파드에 할당될 레이블이 포함되어 있습니다. 이제 Kubernetes가 클러스터를 확인할 때, app: nginx 레이블이 할당된 파드의 인스턴스를 찾을 수 있습니다. 이는 셀렉터와 일치하며 Kubernetes는 이 어플리케이션이 포함된 파드를 생성하거나(또는 제거)해야 하는지 결정할 수 있습니다.

```js
...
spec:
  ...
  template:
    ...
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
        volumeMounts:
        - name: nginx-index-config
          mountPath: /usr/share/nginx/html
      volumes:
      - name: nginx-index-config
        configMap:
          name: nginx-config-1
```

이제 템플릿 내 spec 섹션은 Kubernetes에게 어플리케이션을 생성하고 설정하는 방법을 알려줍니다.

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

컨테이너 ... 이는 파드 배포에 포함해야 할 컨테이너를 정의합니다. 이름은 nginx로 하고 이미지는 nginx를 사용해야 합니다. 이 이미지는 여러분이 만든 이미지일 수도 있고 버전을 포함할 수도 있습니다. 노드에 처음으로 파드를 배포할 때 이미지를 다운로드하지만, 이후에 동일한 파드의 새 인스턴스를 만들어야 할 경우 이미지를 다시 다운로드할 필요가 없도록 캐시합니다.

ports ... 이는 애플리케이션이 노출하는 포트를 정의하고 클러스터에서 사용할 수 있어야 합니다.

volumeMounts ... 이는 외부 볼륨을 컨테이너 내 파일 시스템에 매핑하는 것을 정의합니다.

컨테이너를 정의할 뿐만 아니라, 마운트해야 하는 볼륨도 정의해야 할 수 있습니다. 이는 아무 종류의 스토리지일 수 있지만, 이 경우 ConfigMap이라는 다른 리소스 유형을 사용할 것입니다.

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

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      ...
      volumes:
      - name: nginx-index-config
        configMap:
          name: nginx-config-1
```

볼륨의 이름을 nginx-index-config로 지정하였고, 이는 volumeMount stanza에 매핑하는 데 사용됩니다. 그런 다음 볼륨의 유형과 구성을 정의합니다. 이 경우에는 nginx-config-1 configMap입니다.

이것으로 거의 다 끝났습니다. 이제 다음 명령으로 배포할 수 있습니다:

```yaml
kubectl apply -f <filename> -n <namespace>
```

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

네임스페이스(-n 또는 --namespace)는 선택 사항이며, 이 경우에는 manifest에서 네임스페이스를 정의했기 때문에 필요하지 않습니다. 사용자에게 선택권을 주려면(또는 기본값을 사용하려면), 메타데이터에서 네임스페이스를 생략하면 됩니다.

물론, 이를 배포하려면 configMap 리소스가 필요합니다. 예시를 보겠습니다:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-config-1
  namespace: test-env
data:
  index.html: |
    <html>
    <h2>Hello world 1!!</h2>
    </html>
```

configMap 리소스를 사용하면 어플리케이션이나 리소스에 자유로운 형태의 구성을 제공할 수 있습니다. 여전히 API 버전, 리소스 유형 및 메타데이터(이 경우에는 네임스페이스 포함)를 제공하는 것을 볼 수 있습니다.

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

이제 데이터 스탠자를 정의합니다. 여기서는 표시된 HTML 콘텐츠를 포함하는 index.html 파일이라는 데이터가 정의됩니다.

이를 다른 리소스와 마찬가지로 다음과 같이 배포할 수 있습니다:

```js
kubectl -f <파일이름>
```

매니페스트 파일에서 이름 공간 옵션을 정의했기 때문에 이름 공간 옵션은 필요하지 않습니다.

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

Kubernetes는 선언적이기 때문에 파일을 적용하는 순서가 중요하지 않아야 합니다. 그러나 NGINX pod가 configMap을 찾는 등의 지속적인 다시 시작을 피하기 위해 의존성을 먼저 배포하는 것이 좋습니다. 이것은 두 매니페스트를 단일 파일에 포함할 수 있는 이유 중 하나입니다.

# 요약

이 글을 통해 Kubernetes에 대한 좋은 소개를 받았기를 바라며, Kubernetes의 기능, 작동 방식 및 사용 방법에 대해 알아보았습니다.

이 글에서는 많은 내용을 다루었습니다. 여기에는 다음이 포함되어 있습니다:

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

- Microservices
- 쿠버네티스란 무엇인가요?
- 왜 쿠버네티스와 같은 것을 사용해야 하는지
- 리소스를 기반으로 하는 방법에 대한 설명
- 네임스페이스란 무엇인가요?
- 내부 작동 방식
- CNI가 무엇이며 어떻게 사용되는가요?
- 클러스터에서 DNS가 구성되고 사용되는 방법
- 컴퓨팅 리소스에 대한 제한
- 리소스 정의 및 배포

이것은 쿠버네티스에 대한 간략한 소개일 뿐이며, 더 많은 내용이 있습니다.

본문이 유익하다고 생각되면 박수를 눌러주세요. 그렇게 하시면 사람들이 유용하게 여기는 내용과 앞으로 쓸 기사에 대한 정보를 파악하는 데 도움이 됩니다. 의견이나 제안이 있으시면 댓글에 추가해 주세요.
