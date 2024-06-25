---
title: "Kubernetes에서 서비스 및 네트워킹 이해하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_0.png"
date: 2024-06-23 23:06
ogImage:
  url: /assets/img/2024-06-23-ServicesandNetworkinginKubernetes_0.png
tag: Tech
originalTitle: "Services and Networking in Kubernetes"
link: "https://medium.com/@teva15/services-and-networking-in-kubernetes-ed23509ee4b4"
---

## 쿠버네티스 기본 개념 — 파트 (3.a)

# 소개

쿠버네티스는 조직이 컨테이너화된 애플리케이션을 배포, 확장 및 관리하는 방식을 혁신적으로 변화시켰습니다. 이 오케스트레이션 플랫폼의 핵심에는 컨테이너 간의 원활한 통신과 연결을 보장하는 복잡한 서비스 및 네트워킹 구성 요소들이 있습니다. 이 문서에서는 쿠버네티스 서비스와 네트워킹의 세계에 대해 깊이 있게 다루며 핵심 개념, 서비스 유형 및 이들이 컨테이너화된 애플리케이션의 전반적인 효율성에 어떻게 기여하는지 살펴보겠습니다.

## 튜토리얼 흐름:

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

<img src="/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_0.png" />

서비스에 대해 이야기한 후 인그레스 컨트롤러를 자세히 살펴볼 예정입니다.

# K8s의 서비스

## 쿠버네티스 서비스란?

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

Kubernetes에서 서비스는 포드의 논리적 집합과 해당 포드에 액세스하는 정책을 정의하는 추상화입니다. 서비스를 통해 응용 프로그램의 서로 다른 부분 간의 통신 및 발견이 가능해지며, 확장 가능하고 견고한 아키텍처를 제공할 수 있습니다.

## Kubernetes에서 주요 서비스 유형은 무엇인가요?

- NodePort 서비스: 각 노드의 IP에 정적 포트에서 서비스를 노출합니다. 이 포트를 통해 해당 포트의 어느 노드에서든 서비스에 외부 액세스할 수 있습니다.
- ClusterIP 서비스: 기본 서비스 유형입니다. 클러스터 내부 IP에서 서비스를 노출합니다. 클러스터 내의 포드는 이 IP를 사용하여 서비스에 도달할 수 있습니다.
- LoadBalancer 서비스: 클라우드 제공 업체의 로드 밸런서를 사용하여 서비스를 외부에 노출합니다. 로드 밸런싱이 필요한 외부 액세스가 필요한 응용 프로그램에 적합합니다.

## NodePort 서비스란 무엇이며 예시를 들어 설명해주세요.

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

NodePort은 클러스터의 각 노드에서 특정 포트로 서비스를 노출하는 서비스 유형입니다.

이를 통해 클러스터 외부에서 해당 서비스에 외부 액세스가 가능해집니다.

NodePort는 클러스터 내부에서 실행 중인 웹 응용 프로그램에 액세스하는 것과 같이 서비스를 외부 세계에 노출하는 데 일반적으로 사용됩니다.

NodePort 서비스에 대한 주요 포인트:

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

- 외부 액세스: NodePort는 각 노드의 포트를 서비스의 포트에 매핑하여 서비스를 외부에서 액세스할 수 있게 합니다.
- 클러스터 IP: NodePort 서비스에는 클러스터 내에서 ClusterIP를 통해 내부적으로 액세스할 수 있는 기능도 있습니다.
- 포트 매핑: NodePort 서비스를 생성하면 Kubernetes가 각 노드에 미리 정의된 범위(기본값은 30000~32767)에서 포트를 자동으로 할당합니다. 이 포트가 NodePort입니다.
- 로드 밸런싱: NodePort는 정교한 로드 밸런싱을 제공하지는 않지만, 클러스터 내의 모든 노드에 도달하여 서비스에 외부 액세스할 수 있게 합니다.

![서비스 및 네트워킹 in Kubernetes](/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_1.png)

![서비스 및 네트워킹 in Kubernetes](/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_2.png)

이 예제의 구성 요소를 자세히 살펴보겠습니다.

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

- metadata: 서비스의 이름을 지정합니다.
- spec.selector: 서비스가 대상이 될 파드를 결정하는 셀렉터를 정의합니다. 이 경우에는 app: my-app 라벨이 있는 파드를 대상으로 합니다.
- spec.ports: 포트 구성을 지정합니다. 이 예에서는 서비스가 포트 80에서 수신하고 포트 8080으로 트래픽을 전달합니다.
- spec.type: NodePort: 이것이 NodePort 서비스임을 나타냅니다.
- kubectl apply -f demoNodeport.yaml 명령을 사용하여 이 YAML 구성을 적용하면, Kubernetes가 teva-nodeport-service 라는 이름의 NodePort 서비스를 생성합니다.

하나의 노드에 여러 개의 파드가 있는 경우

![서비스 및 쿠버네티스 네트워킹](/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_3.png)

여러 노드에 파드가 있는 경우

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

![이미지](/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_4.png)

curl http://192.169.1.4:30008 또는 curl http://192.169.1.6:30008 또는 curl http://192.169.1.9:30008

## ClusterIP 서비스란 무엇이며 예시를 들어 설명해주세요?

ClusterIP 서비스는 내부 IP 주소를 노출하고 클러스터 내에서만 접근할 수 있도록 만드는 종류의 서비스입니다.

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

이는 클러스터 외부에서 해당 서비스에 접근할 수 없음을 의미합니다.

ClusterIP 서비스는 주로 클러스터 내의 다른 구성 요소 또는 마이크로서비스 간 통신에 사용됩니다.

ClusterIP 서비스에 대한 주요 포인트:

- 내부 IP 주소: ClusterIP 서비스는 쿠버네티스 클러스터 내에서만 접근할 수 있는 내부 IP 주소가 할당됩니다.
- Pod 선택기: 선별기를 기반으로 한 일련의 파드와 연관되어 있습니다. 서비스는 지정된 레이블 선택기와 일치하는 파드로 트래픽을 전달합니다.
- 로드 밸런싱: ClusterIP 서비스는 해당 서비스와 관련된 파드 간의 기본적인 로드 밸런싱을 제공합니다. 들어오는 트래픽은 선택된 파드 사이에 분산됩니다.
- 클러스터 내 통신: 이러한 서비스는 같은 쿠버네티스 클러스터 내에서 실행되는 응용 프로그램 또는 마이크로서비스의 서로 다른 부분 간 통신에 적합합니다.

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

아래는 Markdown 형식으로 변경하영 표시한 것입니다.

![ClusterIP implementation](/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_5.png)

Example Sample ClusterIP implementation.

![ClusterIP YAML example](/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_6.png)

This YAML defines a ClusterIP service named “backend-service” that selects pods labeled with “app: backend.”

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

서비스는 포트 8080에서 수신하고 해당 포트 8080으로 팟으로 트래픽을 전달합니다.

![Image 1](/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_7.png)

![Image 2](/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_8.png)

이 예시의 구성 요소를 살펴보겠습니다:

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

- metadata: 서비스의 이름을 지정합니다.
- spec.selector: 서비스가 대상이 되는 팟을 결정하는 셀렉터를 정의합니다. 이 경우, app: frontend 라벨을 가진 팟을 대상으로 합니다.
- spec.ports: 포트 구성을 지정합니다. 이 예에서는 서비스가 포트 80에서 수신하고 포트 8080으로 트래픽을 전달합니다.
- spec.type: ClusterIP: 이것이 ClusterIP 서비스임을 나타냅니다.
- kubectl apply -f `filename`.yaml을 사용하여이 YAML 구성을 적용하면 Kubernetes가 example-clusterip-service라는 ClusterIP 서비스를 생성합니다.
- 이 서비스는 클러스터 내에서 ClusterIP 주소를 사용하여 접근할 수 있습니다.
- 동일한 Kubernetes 클러스터 내의 다른 팟들은 이 서비스와 통신하기 위해 ClusterIP 및 포트를 사용할 수 있습니다 (예: example-frontend-service: 80).

## 로드 밸런서 서비스란 무엇이며 예시를 들어 설명하세요?

Kubernetes(K8s)에서 로드 밸런서는 서비스를 외부 세계에 노출시키고 외부 로드 밸런서를 자동으로 프로비저닝하는 유형의 서비스입니다.

이 로드 밸런서는 들어오는 네트워크 트래픽을 여러 노드에 분산하여 응용 프로그램의 고가용성과 신뢰성을 보장합니다.

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

로드밸런서 서비스에 대한 주요 포인트:

- 외부 액세스: 로드밸런서 서비스는 클라우드 제공 업체의 로드 밸런서를 프로비저닝하여 서비스를 외부에서 접근 가능하게 만듭니다. 외부 로드 밸런서에는 일반적으로 공개 IP 주소가 있으며 귀하의 응용 프로그램을 실행하는 노드로 트래픽을 분배할 수 있습니다.
- 자동 프로비저닝: 로드밸런서 서비스를 만들면 Kubernetes가 클라우드 제공 업체와 통신하여 자동으로 로드 밸런서를 프로비저닝합니다. 이 프로세스의 구체적인 내용은 클라우드 제공 업체에 따라 다릅니다.
- NodePort 및 ClusterIP: 로드밸런서 서비스에는 NodePort 및 ClusterIP도 있습니다. NodePort는 클러스터 외부에서 서비스에 액세스할 수 있게 하며, ClusterIP는 클러스터 내에서 내부 액세스를 허용합니다.
- 자동 스케일링: 외부 로드 밸런서는 여러 노드에 트래픽을 분산하여 확장성과 내결함성을 제공하기 위해 수평으로 확장할 수 있습니다.

![로드밸런서 서비스 이미지](/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_9.png)

이 예에서는 세 개의 백엔드 팟 복제본을 만드는 간단한 배포(backend-deployment)를 정의합니다. 이 팟은 선택기 일치를 위해 app: backend로 레이블이 지정됩니다.

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

![이미지](/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_10.png)

백엔드 서비스는 백엔드 팟과 연관된 로드 밸런서 서비스입니다. 포트 80을 노출하고 트래픽을 포트 8080에서 팟으로 전달합니다.

이제 배포 및 서비스를 생성하기 위해 YAML 파일 두 개를 적용하세요:

![이미지](/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_11.png)

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

서비스에 액세스하는 방법: 로드 밸런서 서비스가 프로비저닝되면 외부 IP 주소를 사용하여 서비스에 외부에서 액세스할 수 있습니다.

클라우드 제공업체에 따라 IP 주소가 할당되는 데 시간이 걸릴 수 있습니다.

![이미지](/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_12.png)

"EXTERNAL-IP" 필드를 찾아 할당된 후에 서비스에 액세스할 수 있습니다.

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

![이미지](/assets/img/2024-06-23-ServicesandNetworkinginKubernetes_13.png)

# 인그레스 네트워킹
