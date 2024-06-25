---
title: "클라우드 제공 로드밸런서 없이 Kong과 Gateway를 사용하여 Kubernetes 서비스에 접근하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-UsingKongtoaccessKubernetesservicesusingaGatewayresourcewithnocloudprovidedLoadBalancer_0.png"
date: 2024-06-23 23:10
ogImage:
  url: /assets/img/2024-06-23-UsingKongtoaccessKubernetesservicesusingaGatewayresourcewithnocloudprovidedLoadBalancer_0.png
tag: Tech
originalTitle: "Using Kong to access Kubernetes services, using a Gateway resource with no cloud provided LoadBalancer"
link: "https://medium.com/@martin.hodges/using-kong-to-access-kubernetes-services-using-a-gateway-resource-with-no-cloud-provided-8a1bcd396be9"
---

## 이 기사에서는 쿠버네티스 클러스터 내에서 Kong을 API 게이트웨이로 배포하여 서비스에 관리된 액세스를 제공하는 방법을 살펴봅니다. 이를 클라우드 서비스에서 수행합니다. 이 클라우드 서비스는 쿠버네티스 호환 외부 로드 밸런서 서비스를 제공하지 않습니다. 또한 Ingress 리소스 대신 최신 Kubernetes Gateway를 사용합니다.

저와 이전 기사 중 한 가지 이상을 따르신 분들은 저의 이전 기사들 중 하나를 따라오셨을 것입니다. 저는 신뢰할 수 있고 비용 효율적이지만 제한된 범위의 서비스를 제공하는 호주 클라우드 제공자인 Binary Lane을 사용합니다.

제한된 범위의 서비스만 제공하는 것은 모든 쿠버네티스 작업을 직접해야 하므로, 배우고 솔루션의 작동 방식을 제어할 수 있는 기회를 제공합니다. 또한 어떤 클라우드 공급 업체에도 얽매이지 않을 수 있습니다. 또한 비용 효율적입니다.

이 기사에서는 Kubernetes 클러스터에 Kong API 게이트웨이를 추가하여 서비스에 액세스하는 방법을 살펴봅니다. 할 일이 꽤 많기 때문에 이 기사는 좀 길지만, API 게이트웨이의 역할에 대한 이론 부분을 별도의 기사로 분리했습니다. API 게이트웨이의 역할을 이해하지 못하신다면 먼저 해당 기사를 읽는 것을 권장합니다.

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

Kong은 신뢰할 만한 엔터프라이즈급 API 게이트웨이이지만 설정하기가 매우 까다로울 수 있습니다. 이 기사의 끝에서 문제를 디버그하는 방법에 대한 일부 힌트를 제공하겠습니다. 이 기사에서 설계를 조정하는 경우, 이름과 포트를 올바르게 구성했는지 확인하세요.

# 네트워크 디자인

Kubernetes 네트워킹은 복잡한 주제이며 여기서 다루기 어렵지만, 고수준에서 네트워크 디자인에 대해 생각해야 합니다.

![네트워크 디자인](/assets/img/2024-06-23-UsingKongtoaccessKubernetesservicesusingaGatewayresourcewithnocloudprovidedLoadBalancer_0.png)

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

이전 기사를 따라오셨다면, 이제 이진 레인(또는 다른 클라우드 제공업체) 서버에 Kubernetes 클러스터가 설치되어 있어야 합니다. 인터넷에서 접근이 불가능한 가상 사설 클라우드 (VPC) 개인 서브넷에 세 개의 노드가 설치되어 있을 것입니다. 이러한 노드들은 인터넷을 통해 접속이 가능한 전용 VPN을 통해서만 연결됩니다(위에는 표시되지 않음). 인터넷 및 VPC 인터페이스를 모두 가지고 있는 게이트웨이 서버가 있어서, 인터넷에서 클러스터로 들어오는 접속(inress)과 클러스터에서 인터넷으로 나가는 접속(egress)을 제공합니다.

기본 네트워크 토폴로지가 이제 갖추어 졌습니다. 이제 우리는 서비스가 외부 세계에 제공하는 API를 관리할 수 있기를 원합니다. 이 작업은 Kong Gateway API를 통해 수행됩니다.

# 인터넷에서 연결 설정하기 (인그레스)

AWS, 구글 클라우드 또는 Azure와 같은 풀 서비스 제공업체를 사용하면, LoadBalancer 유형의 Kubernetes 서비스를 사용하여 인터넷 연결이 자동으로 생성되는 방식으로 Kubernetes를 설정할 수 있습니다.

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

바이너리 레인에는 로드 밸런서 서비스가 있지만 쿠버네티스를 통해 관리할 수는 없으므로 로드 밸런서를 직접 생성하고 구성하거나 고유한 인그레스 포인트를 만들어야 합니다. 특정 클라우드 제공 업체의 기능에 구속되지 않기 위해, 저는 개인적으로 내 gw 서버에서 NGINX 역방향 프록시를 실행하여 고유한 인그레스 포인트를 만드는 것을 선호합니다.

이 아키텍처에서 gw 서버에서 실행되는 NGINX는 두 가지 기능을 수행합니다:

- 유효한 요청을 모두 쿠버네티스 클러스터로 라우팅하여 Kong이 처리
- 쿠버네티스 노드 간 요청을 로드 밸런싱

Kong이 NodePort 서비스로 노출될 것이므로 클러스터의 모든 노드에서 액세스할 수 있습니다. 이를 통해 NGINX가 노드 간 요청을 로드 밸런싱할 수 있습니다.

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

서비스 자체가 사용 가능한 파드 전체에 무작위로 로드 밸런싱을 수행하므로 NGINX에 의한 로드 밸런싱이 노드 장애나 과부하 상황을 견딜 목적으로만 사용된다는 것을 유의한 점입니다. 서비스 로드 밸런싱에 대해 더 읽어보실 수 있습니다.

본 솔루션에서 NGINX를 수동으로 구성된, 대체될 수 있는 외부 로드 밸런서로 간주하실 수 있습니다.

# 쿠버네티스 서비스

쿠버네티스 서비스는 하나 이상의 파드가 제공하는 서비스에 대한 액세스를 허용합니다. 이를 통해 파드가 종료되고 재예약되더라도 특정 노드에서 요청이 발생하더라도 서비스가 계속하여 요구에 따른 대로 요청을 라우팅하는 단일 접점으로 유지됩니다.

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

![image](/assets/img/2024-06-23-UsingKongtoaccessKubernetesservicesusingaGatewayresourcewithnocloudprovidedLoadBalancer_1.png)

서비스를 사용함으로써 요청을 보낼 수 있는 단일하고 안정적인 IP 주소가 제공됩니다. 서비스는 사용 가능한 Pod들 사이에서 부하 분산 기능을 제공합니다. Kubernetes는 또한 클러스터의 DNS에 서비스에 대한 참조를 추가함으로써 서비스가 이름으로 액세스될 수 있게 합니다. 여러 가지 다른 변형이 등록됩니다:

```js
<서비스 이름>.<네임스페이스>.svc.local
<서비스 이름>.<네임스페이스>.svc
<서비스 이름>.<네임스페이스>
<서비스 이름>
```

# API 게이트웨이

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

NGINX 게이트웨이와 쿠버네티스 서비스는 서비스에 대한 외부 인터페이스를 제공하는 데 도움이 되지만 기능이 제한적이며 수동으로 설정해야 합니다.

API 게이트웨이는 이 문제를 해결하는 클러스터 구성 요소입니다. 이 게이트웨이는 솔루션의 일부로 구성되며 서비스 앞에 위치하여 여기서 설명하는 추가 기능을 제공합니다.

![image](/assets/img/2024-06-23-UsingKongtoaccessKubernetesservicesusingaGatewayresourcewithnocloudprovidedLoadBalancer_2.png)

API 게이트웨이는 클러스터의 일부로 있기 때문에 클러스터 내 리소스의 변경에 따라 자동으로 구성될 수 있습니다.

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

# Kong API Gateway

다양한 API 게이트웨이 기술이 있지만, 이 글에서는 무료 오픈 소스 솔루션인 Kong을 선택했습니다. 유료 엔터프라이즈 설치도 가능합니다. 여기에서 Kong에 대한 포괄적인 공식 문서를 찾을 수 있습니다.

Kong은 쿠버네티스 커뮤니티와 적극적으로 협력하여 클러스터 내에서 게이트웨이에 대한 새로운 표준을 정의하고 있습니다. 이로 인해 게이트웨이 자체와 혼동되어서는 안 되는 새로운 쿠버네티스 리소스 유형인 게이트웨이 API가 만들어졌습니다.

이것이 Kong이 어떻게 작동하는지 대략적으로 설명했습니다.

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

![이미지](/assets/img/2024-06-23-UsingKongtoaccessKubernetesservicesusingaGatewayresourcewithnocloudprovidedLoadBalancer_3.png)

클러스터 외부의 모든 클라이언트로부터 들어오는 트래픽이 Kong에 도달합니다. Kong은 구성 내의 규칙에 따라 요청을 처리하고 클러스터 내외의 적절한 서비스로 요청을 전달합니다.

Kong은 Kubernetes 리소스 매니페스트에서 정적으로 또는 데이터베이스에서 구성을 가져올 수 있습니다 (DB-less 설치). Kong은 이제 DB-less 설치를 새로운 설치에 사용할 것을 권장하며, 이를 따를 것입니다.

Kong은 성숙한 플러그인 기능을 갖추고 있습니다. 이를 통해 제3자가 Kong의 플러그인으로 기능 확장을 개발할 수 있습니다. 플러그인은 트래픽 흐름에 위치하여 속도 제한 및 인증과 같은 작업에 도움을 줄 수 있습니다.

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

마지막으로 Kong을 관리하기 위해 플러그인, 구성 등을 관리할 수 있도록 Management UI를 제공합니다. Management UI는 Admin API를 통해 Kong과 상호 작용합니다.

Kong에 대해 상세한 문서를 살펴보면 여기서 다룰 수 있는 내용보다 더 많음을 알 수 있습니다. 그래서 제가 다루는 내용은 기본 사항에만 초점을 맞추겠습니다.

## DB-less 설치

DB-less 설치가 어떻게 작동하는지 이해하는 것이 중요하다고 생 생각합니다.

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

이 구성에서 Kong은 두 가지 구성요소를 설치합니다:

- Kong Ingress Controller (KIC)
- Kong Gateway

게이트웨이는 프록시를 통해 모든 사용자 트래픽의 경로 지정을 처리합니다. Kong Ingress Controller (KIC)는 Kubernetes 리소스 정의 (예: HTTPRoute)에서 구성을 가져와서 프록시가 이해하는 규칙으로 변환하고 실시간으로 프록시에 규칙을 업로드합니다. 이러한 방식으로 Kubernetes 구성의 변경 사항이 자동으로 프록시에 적용됩니다.

KIC는 내부 Kubernetes API를 사용하여 Kubernetes 클러스터에 대한 정보를 얻습니다. 이 API는 클러스터를 관리하는 데 사용되는 것으로, kubectl을 사용할 때 실제로는 Kubernetes API와 상호 작용합니다. 이 API를 통해 Kong 및 kubectl과 같은 애플리케이션은 클러스터에 대한 정보를 찾거나 변경할 수 있습니다. KIC는 이 API를 통해 백업 데이터베이스가 필요 없이 클러스터 리소스 파일과 Gateway를 동기화할 수 있습니다.

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

# 테스트할 서비스

Kong 배포에 들어가기 전에 Kong을 통해 접근할 수 있는 서비스를 가지고 있어야 합니다. 어차피 API Gateway에 API가 없다면 그리 유용하지 않을 것이니까요!

가장 간단한 방법은 NGINX를 웹 서버로 배포하고 정적 콘텐츠로 구성하는 것입니다. Kubernetes에서 이 작업을 한 적이 없다면 다른 기사 하나에서 그 방법을 읽어볼 수 있습니다.

2개의 서비스를 생성하는 것을 제안합니다:

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

- 안녕하세요 world 1: 2개의 레플리카
- 안녕하세요 world 2: 1개의 레플리카

이들은 클러스터 내 파드에서 ClusterIP 서비스를 통해 접근할 수 있어야 합니다. 이러한 서비스를 설정하는 내 기사에서는 브라우저에서 서비스를 확인할 수 있도록 NodePort 서비스를 생성합니다. 이를 수행할 경우 내부 클러스터 IP 및 포트를 사용해야 합니다. 서비스 유형에 대한 자세한 내용은 다른 기사에서 확인할 수 있습니다.

두 서비스가 올바르게 실행되고 Hello World HTML을 제공할 수 있는지 확인하세요.

내가 여기서 설명하는 예제에서, 내 두 서비스는 다음과 같이 위치해 있습니다:

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

- http://`node IP 주소`:30082 — 'Hello World 1 !!'라고 응답합니다.
- http://`node IP 주소`:30082 — 'Hello World 2 !!'라고 응답합니다.

어느 경로도 필요하지 않으며 경로를 추가하면 (예: http://`node IP 주소`:30082/world1) 404 오류가 발생합니다. 이 사실을 인식하지 못하면 나중에 문제가 될 수 있으므로 주의해야 합니다.

이제 서비스가 실행 중이므로 Kong을 통해 액세스해 보겠습니다.

# 쿠버네티스 게이트웨이 자원 생성하기

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

DB 미사용 모드에서는 Kong API 게이트웨이를 구성할 때 Kubernetes Ingress 또는 HTTPRoute 리소스를 생성합니다. 이러한 리소스를 클러스터에 적용하면 Kong이 프록시 구성 요소 내에서 라우팅 규칙을 정의하는 데 사용됩니다. 이를 통해 들어오는 트래픽이 서비스로 전달됩니다.

Ingress 리소스는 작동하지만 기능이 제한적입니다. Kubernetes 커뮤니티와 함께 Kong에서 개발한 새 Gateway 리소스를 사용하면 API를 더 정교하게 관리할 수 있습니다.

우리는 Kong과 함께 Gateway 리소스를 사용할 것입니다. 이를 위해 먼저 GatewayClass 및 Gateway 리소스를 지원하는 새로운 Custom Resource Definitions (CRD)를 클러스터에 적용해야 합니다. 클러스터에서 kubectl을 실행하는 위치에서 다음 명령을 실행하여 이 작업을 수행할 수 있습니다. 저의 경우에는 제 k8s-master 서버에서 이를 실행합니다.

```js
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.0.0/standard-install.yaml
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

본 문서에서는 사용하지 않을 실험적 기능 몇 가지를 소개해드리겠습니다만, 참고용으로 여기에 추가해두었습니다.

```js
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.0.0/experimental-install.yaml
```

이제 Kubernetes를 위해 GatewayClass 및 Gateway 두 리소스를 정의할 수 있습니다. 이들이 무엇을 하는 지에 대해 설명했으니, 이를 다시 반복하지는 않겠습니다. 간결함을 위해 해당 내용은 여기서 생략합니다.

## GatewayClass

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

Kong 기술을 클러스터에 소개하는 GatewayClass를 정의할 것입니다. 다음 파일을 생성해주세요:

kong-gw-class.yml

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: kong-class
  annotations:
    konghq.com/gatewayclass-unmanaged: "true"
spec:
  controllerName: konghq.com/kic-gateway-controller
```

이 파일에 대해 몇 가지 주의할 사항이 있습니다:

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

- 이것은 클러스터 수준 리소스이기 때문에 네임스페이스가 없습니다.
- 주석은 솔루션별 옵션을 정의하며 Kong의 경우 konghq로 시작합니다.
- konghq.com/gatewayclass-unmanaged 주석은 'true'(문자열)로 설정되어 있습니다. 왜냐하면 Kong이 오퍼레이터를 통해 자동으로 설정되는 것이 아니라 수동으로 설정되고 있기 때문입니다.(다른 옵션도 있으니 여기를 참조하세요)
- 인그레스 컨트롤러는 Kong 인그레스 컨트롤러(konghq.com/kic-gateway-controller)이며 contollerName 필드에서 구성됩니다.

이제 다음과 같이 클래스를 생성하세요:

```js
kubectl apply -f kong-gw-class.yml
```

이제 이 클래스를 사용하는 게이트웨이를 생성할 수 있습니다. 동일한 GatewayClass를 참조하는 여러 Gateway 인스턴스를 생성할 수 있다는 점을 유의하세요.

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

## 게이트웨이

수동으로 설치된 Kong 게이트웨이의 경우 (우리가 생성중인 것과 같이), 다음 파일을 만들어야 합니다:

kong-gw-gateway.yml

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: kong-gateway
  namespace: kong
spec:
  gatewayClassName: kong-class
  listeners:
    - name: world-selector
      hostname: worlds.com
      port: 80
      protocol: HTTP
      allowedRoutes:
        namespaces:
          from: All
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

파일에서 유의해야 할 몇 가지 사항이 또 있습니다:

- 나중에 참조할 수 있는 이름이 있습니다 (kong-gateway)
- GatewayClass는 위에서 생성한 GatewayClass의 이름을 의미합니다
- 이 Gateway는 이 API Gateway의 진입점인 하나의 리스너만 정의합니다
- 리스너에는 URL 호환성이 있는 고유한 이름이 지정됩니다
- 리스너는 포트 80에 바인드됩니다
- 호스트명은 일치 필드로 사용되며 옵션입니다
- 이 리스너에 연결할 서비스(allowedRoutes)를 제어할 수 있으며 해당 서비스들은 네임스페이스를 통해 연결됩니다 - 동일한 네임스페이스를 기본으로 사용하여 다른 네임스페이스로 연결하기 위해 모든 네임스페이스로 변경됩니다
- Gateway 사양은 게이트웨이가 HTTP를 통해 단일 포트(80)에서 수신하는 것을 예상합니다.

Gateways는 네임스페이스에 특정하며 API Gateway를 설치하기 전에 생성해야 합니다:

```js
kubectl create namespace kong
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

이제 다음 명령을 사용하여 리소스를 만드세요:

```js
kubectl apply -f kong-gw-gateway.yml
```

이제 GatewayClass 및 Gateway 리소스가 정의되었으므로, 애플리케이션 자체를 설치하여 이 두 리소스의 구현을 형성할 수 있습니다.

# Kong 설치

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

지금은 Helm 차트를 사용하여 Kong을 설치할 것입니다. 만약 Helm이 없다면, Helm을 설치하는 방법은 여기에서 찾을 수 있습니다.

## Kong CRDs

Kong을 설치하기 전에 Kong Custom Resource Definitions (CRDs)를 설치해야 합니다. 이 작업은 클러스터에서 kubectl을 실행하는 위치에서 다음 명령을 실행하여 수행할 수 있습니다. 제 경우에는 k8s-master 서버에서 이 작업을 수행하고 있습니다.

```js
kubectl apply -k https://github.com/Kong/kubernetes-ingress-controller/config/crd
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

## Kong Application

Kong을 Kubernetes 클러스터에 설치할 때, 두 가지 구성 요소가 설치됩니다:

- Kong 인그레스 컨트롤러 (KIC) — 쿠버네티스 리소스 정의를 Kong 게이트웨이 구성으로 변환합니다.
- Kong 게이트웨이 — Kong 인그레스 컨트롤러 (KIC)에 의해 삽입된 구성을 기반으로 서비스로의 라우팅을 담당합니다.

먼저, 로컬 헬름에 Kong 저장소를 추가하십시오:

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
helm repo add kong https://charts.konghq.com
helm repo update
```

만약 다음 명령어로 Helm 차트를 검색하면:

```js
helm search repo kong
```

두 개의 항목을 찾을 수 있습니다:

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
이름           차트 버전  앱 버전  설명
kong/kong    2.33.3    3.5       클라우드 네이티브 인그레스 및 API 관리
kong/ingress 0.10.2    3.4       콩 인그레스 컨트롤러 및 콩 게이트웨이 배포
```

DB 레스 구성을 사용할 것이므로 kong/ingress를 사용할 것입니다. 설치하기 전에 몇 가지 값을 재정의해야 합니다. 다음 파일을 만들어주세요:

kong-values.yml

```js
#controller:
#  ingressController:
#    env:
#      LOG_LEVEL: trace
#      dump_config: true

gateway:
  admin:
    http:
      enabled: true
  proxy:
    type: NodePort
    http:
      enabled: true
      nodePort: 32001
    tls:
      enabled: false
#  ingressController:
#    env:
#      LOG_LEVEL: trace
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

KIC 및 Kong Gateway를 부모 Helm 차트를 통해 설치하고 있기 때문에 이 두 애플리케이션의 구성은 각각 컨트롤러 및 게이트웨이 레이블 아래에 있습니다. 컨트롤러는 단순히 알림으로 남겨두었습니다.

또한, 주석 처리된 여러 줄을 볼 수 있습니다. 이것들은 Pod 로그를 통해 무엇이 발생하는지 디버그하고 싶을 때 유용합니다.

Binary Lane은 Kubernetes가 구성할 수 있는 로드 밸런서를 제공하지 않기 때문에 프록시 구성을 재정의하고 있습니다. Kubernetes에게 LoadBalancer 서비스 대신 NodePort 서비스를 설정하도록 지시하고 있습니다. 게이트웨이를 클러스터의 모든 노드에서 사용할 수 있도록 포트 32001에 노출하고 있습니다.

이전에 kong 네임스페이스를 생성했으므로 이제 Kong을 설치할 준비가 되었습니다:

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
helm install kong kong/ingress -f kong-values.yml -n kong
```

이제 설치가 예상대로 작동하는지 확인할 수 있습니다. 준비되는 데 1-2분 정도 걸릴 수 있습니다:

```js
kubectl get all -n kong
```

다음과 같은 결과를 얻어야 합니다:

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

### NAME READY STATUS RESTARTS AGE

- pod/kong-controller-68cddcbcb7-z46lh 1/1 Running 0 45s
- pod/kong-gateway-687c5b78db-5qvgd 1/1 Running 0 45s

### NAME TYPE CLUSTER-IP EXTERNAL-IP PORT(S) AGE

- service/kong-controller-validation-webhook ClusterIP 10.110.172.40 <none> 443/TCP 46s
- service/kong-gateway-admin ClusterIP None <none> 8444/TCP 46s
- service/kong-gateway-manager NodePort 10.100.254.169 <none> 8002:30698/TCP,8445:30393/TCP 46s
- service/kong-gateway-proxy NodePort 10.96.24.196 <none> 80:32001/TCP 46s

### NAME READY UP-TO-DATE AVAILABLE AGE

- deployment.apps/kong-controller 1/1 1 1 45s
- deployment.apps/kong-gateway 1/1 1 1 45s

### NAME DESIRED CURRENT READY AGE

- replicaset.apps/kong-controller-68cddcbcb7 1 1 1 45s
- replicaset.apps/kong-gateway-687c5b78db 1 1 1 45s

Management UI 서비스가 NodePort를 통해 노출됩니다. 이는 관리 API를 볼 수 있는 것을 기대하고 작동하지 않을 것입니다. DB-less 설치를 하고 있기 때문에, 관리 UI의 유일한 사용은 설정을 확인하는 것뿐입니다.

클러스터 내 노드에서 프록시 주소를 curl로 테스트할 수 있습니다:

```js
curl localhost:32001
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

요청한 표는 Markdown 형식으로 변경해야 합니다.

```json
{
  "message": "해당 값으로 일치하는 경로가 없습니다.",
  "request_id": "7fc9db053e3029105581890e81effe12"
}
```

요청 ID는 해당 거래에 고유하며 curl 명령을 다시 실행하면 다른 값을 볼 수 있습니다. 이는 Kong에서 추가되어 시스템을 통해 요청을 추적할 수 있게 합니다. 멋지죠?

이제 새 API 게이트웨이를 구성하여 이전에 생성한 테스트 서비스로 요청을 라우트할 준비가 되었습니다.

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

# 라우트 추가하기

게이트웨이 리소스에 라우트를 추가하려면 HTTPRoute 리소스를 사용합니다. 다른 수준의 라우팅을 위한 다른 리소스 유형도 있습니다. 이제 worlds.com/world1을 hello-world-1-svc에, worlds.com/world2를 hello-world-2-svc에 연결하기 위해 이러한 리소스 중 하나를 생성할 것입니다.

저는 하나의 HTTPRoute 리소스를 설명하겠고, 다른 하나는 여러분에게 만들어 달라고 요청할 것입니다. 리소스 파일을 생성해주세요:

hello-world-1-route.yml

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
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: example-1
  annotations:
    konghq.com/strip-path: "true"
spec:
  parentRefs:
    - name: kong-gateway
      namespace: kong
  hostnames:
    - worlds.com
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /world1
      backendRefs:
        - name: hello-world-1-svc
          port: 80
          kind: Service
```

이 파일에서는 'true'로 설정된 Kong 특정 주석인 konghq.com/strip-path를 추가했습니다. 이는 수신된 일치하는 경로를 요청에서 southbound 서비스로 줄일 것입니다. 다른 줄에는 다음이 포함되어 있습니다:

- 사용할 게이트웨이의 정의(ParentRefs에서)는 이름과 네임스페이스로 참조됩니다.
- 게이트웨이에서 적절한 수신기에 일치시킬 호스트명에 대한 선택적 참조
- 이 경로에 대해 들어오는 요청과 일치시키는 규칙
- 요청을 이 일치에 대해 경로지정할 서비스를 정의하는 backendRefs(서비스의 내부 DNS 이름이름이며 포트는 서비스에 대한 매핑되지 않은 클러스터 IP 포트임을 주의하세요)

이 경로에서 일치는 /world1의 접두사이며, 그 후 서비스로 전달되기 전에 제거됩니다.

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

이제 다음 라우트를 만듭니다:

```js
kubectl apply -f hello-world-1-route.yml
```

NodePort 서비스를 사용하여 게이트웨이를 만들었습니다. 이제 서비스를 테스트할 수 있습니다. NodePort 서비스는 클러스터의 모든 노드에서 사용할 수 있습니다. 보통 저는 k8s-master 노드를 사용하지만 다른 노드도 사용할 수 있습니다. 다음 명령어로 테스트할 수 있습니다:

```js
curl -H "Host: worlds.com" <k8s-master IP 주소>:32001/world2
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

원하는 요청 라우팅을 위해 Host 헤더로 호스트명을 worlds.com으로 설정했습니다. 테스트 서비스 응답이 돌아오는 것을 확인할 수 있어야 합니다.

이제 두 번째 HTTPRoute 리소스를 추가하여 두 번째 서비스의 요청을 관리할 수 있습니다.

이제 클러스터 노드에서 서비스에 액세스할 수 있으므로 최종 단계 진행할 수 있습니다 - gw 서버 구성.

# 인그레스 지점 구성

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

만약 AWS, Azure, 또는 Google Cloud에서 작업 중이었다면, Gateway 서비스를 LoadBalancer로 유지하고 자동으로 인그레스 포인트가 생성되도록 할 수 있었을 텐데 Binary Lane에서 작업 중이므로 직접 만들어야 합니다.

제 글을 따라오셨다면 알겠지만, 저희는 클러스터로부터 인터넷으로의 인그레스 포인트로 작용하는 gw 서버가 있다는 것을 알고 계실 것입니다. 이 서버는 간단하게 NGINX를 사용하여 구성되어 있습니다.

우리는 이것을 모든 요청을 라운드 로빈 로드 밸런서를 사용하여 클러스터 내 모든 노드로 경로를 설정하도록 구성할 것입니다.

gw 서버에 로그인하고 root 사용자로 다음 파일을 업데이트하십시오 (``에 자신의 값으로 필드를 교체하는 것을 잊지 마세요):

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

아래는 worlds.conf 파일의 내용입니다.

```js
upstream k8s_cluster {
  server <k8s-master>:32001;
  server <k8s-node1>:32001;
  server <k8s-node2>:32001;
}

server {
    listen 80;
    listen [::]:80;

    server_name worlds.com;

    location / {
        proxy_pass http://k8s_cluster;
        include proxy_params;
    }
}
```

일반적으로 프록시 매개변수는 별도의 파일에 설정됩니다:

`/etc/nginx/proxy_params`

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
proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
```

이렇게 하면 Host 헤더 및 기타 세부 정보가 전달되어 라우팅이 효율적으로 작동할 수 있습니다.

이제 사이트를 활성화하고 구성을 테스트한 다음 NGINX를 재시작하십시오:

```js
ln -s /etc/nginx/sites-available/worlds.conf /etc/nginx/sites-enabled/
nginx -t
systemctl restart nginx
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

다음과 같이 테스트할 수 있습니다 ( ' '필드를 귀하의 값으로 대체하세요):

```js
curl -H "Host: worlds.com" <gw 서버 공인 IP 주소>/world1
```

서버로부터 응답을 받아야 합니다.

축하합니다! 이제 콩(Kong)을 설치하고 서비스에 연결하도록 구성하는 데 성공했습니다.

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

# Kong 디버깅

만약 Kong에서 문제가 발생하면 디버깅하는 것이 어려울 수 있어요. 제가 Kong 설치 과정에서 발견한 몇 가지 지침을 공유해드릴게요:

- GatewayClass, Gateway, 그리고 controller/gateway 포드에 kubectl describe를 사용해서 결과물을 주의깊게 살펴보세요. 이런 방법을 이용해 해결책을 찾을 때까지 곤란한 상황에 직면한 적이 있어요.
- controller와 gateway 로그를 다음과 같이 확인해보세요:

```js
kubectl logs <pod name> -n kong
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

- kong-values.yml 파일을 사용하여 로깅 레벨을 높이세요 (이전에 보여준 라인의 주석을 제거하세요)
- NodePort 주소를 얻기 위해 kubectl get svc -n kong를 사용하여 관리 UI에 접속하세요 — HTTP 포트를 사용하고 Admin API를 포트 포워딩하세요 (서비스를 외부로 바인딩하기 위해 --address 옵션을 추가하세요):

```js
kubectl port--forward <게이트웨이 파드 이름> 8001:8001 --address <k8s-마스터 IP 주소>
```

- 포트 8001을 포워딩한 이후, Postman와 같은 REST API 도구를 사용하여 Admin UI에 접속하세요
- 접속할 수 있는 디버그 포트가 있습니다:

```js
kubectl port-forward -n kong <컨트롤러 파드 이름>  10256:10256 --address <k8s-마스터 IP 주소>
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

# 요약

이 글은 API Gateway인 Kong을 설치하기 위해 필요한 모든 단계를 함께 수행해야 하기 때문에 길었습니다.

이 글에서 우리는:

- 네트워크 토폴로지를 검토했습니다.
- 서비스가 서비스에 접근하는 데 도움이 되는 방법을 살펐습니다.
- Kong이 Kubernetes와 어떻게 작동하는지 살펐습니다.
- 사용할 테스트 서비스를 생성했습니다.
- GatewayClass 및 Gateway 리소스를 설치하고 구성했습니다.
- Kong을 설치하고 구성했습니다.
- 자체 외부 로드 밸런서를 구성했습니다.
- API Gateway 설치 문제를 해결하는 방법을 고려했습니다.

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

마침내 저희는 Kong API 게이트웨이를 통해 인터넷에서 저희의 테스트 서비스에 접속할 수 있었습니다.

이 기사가 흥미롭게 여겨진다면 박수를 부탁드립니다. 이는 미래에 어떤 기사를 쓸지 판단하는 데 도움이 됩니다. 의견이 있으시면 댓글에 남겨주시기 바랍니다.
