---
title: "쿠버네티스 Pod 간 통신 완벽 가이드 마스터하기 위한 모든 것"
description: ""
coverImage: "/assets/img/2024-06-23-MasteringKubernetesPod-to-PodCommunicationAComprehensiveGuide_0.png"
date: 2024-06-23 22:50
ogImage:
  url: /assets/img/2024-06-23-MasteringKubernetesPod-to-PodCommunicationAComprehensiveGuide_0.png
tag: Tech
originalTitle: "Mastering Kubernetes Pod-to-Pod Communication: A Comprehensive Guide"
link: "https://medium.com/@extio/mastering-kubernetes-pod-to-pod-communication-a-comprehensive-guide-46832b30556b"
---

<img src="/assets/img/2024-06-23-MasteringKubernetesPod-to-PodCommunicationAComprehensiveGuide_0.png" />

# 소개

Kubernetes는 컨테이너 오케스트레이션을 혁신하여 기업이 규모에 맞게 응용 프로그램을 배포하고 관리할 수 있게 하였습니다. Kubernetes의 주요 구성요소 중 하나인 pod는 하나 이상의 강하게 결합된 컨테이너들의 논리적 그룹입니다. pod가 서로 통신하는 방식을 이해하는 것은 Kubernetes 클러스터에서 탄력적이고 확장 가능한 응용 프로그램을 구축하는 데 중요합니다. 이 블로그 게시물에서는 Kubernetes pod 간 통신에 대해 깊이 알아보고 다양한 통신 패턴과 기술을 탐구할 것입니다.

# Pod 네트워킹 이해하기

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

쿠버네티스에서 Pods는 클러스터 내에서 고유한 IP 주소가 할당되어 직접 통신할 수 있습니다. 기본적으로 각 Pod는 격리되어 고유한 IP 주소를 가지며, 안전한 통신을 가능하게 하고 포트 충돌을 피할 수 있습니다. 이러한 IP 주소는 외부 액세스를 위해 특정 구성이 없는 한 쿠버네티스 클러스터 네트워크 내에서만 접근 가능합니다.

# 쿠버네티스 네트워킹 모델

- 동일 노드 내 Pod 간 통신: 동일한 노드에 여러 개의 Pod가 예약되면 localhost 또는 루프백 인터페이스를 사용하여 직접 통신할 수 있습니다. 이 통신은 주로 가상 이더넷(veth) 쌍 형태로 클러스터 내에 할당된 Pod의 IP 주소를 통해 이루어집니다. 이 통신은 네트워크 계층에서 발생하여 동일 노드의 Pod 간 높은 성능과 낮은 지연 시간 상호작용을 가능하게 합니다.
- 노드 간 Pod 간 통신: 클러스터 내 서로 다른 노드 간에 통신이 필요한 경우, 쿠버네티스는 다양한 네트워킹 솔루션인 Container Network Interfaces (CNIs) 및 소프트웨어 정의 네트워킹(SDN) 기술을 활용합니다. 이러한 솔루션은 전체 클러스터를 가로지르는 가상 네트워크 오버레이를 생성하여 노드 간 Pod 간 통신을 가능하게 합니다. Calico, Flannel, Weave, Cilium 등이 인기 있는 CNIs 중 일부이며, 이러한 네트워킹 솔루션은 Pod의 IP 주소가 Reachable하도록 보장하며, 클러스터 내에서 Pod의 위치에 관계없이 투명한 네트워크 연결성을 제공합니다.

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

기본적으로 쿠버네티스 클러스터 내의 파드는 내부 IP 주소를 사용하여 서로 통신할 수 있습니다. 이 통신은 백그라운드의 컨테이너 런타임이나 네트워크 플러그인에서 제공하는 가상 네트워크 오버레이를 통해 이루어집니다. 내부 IP 주소는 쿠버네티스 클러스터 네트워킹 솔루션에 의해 할당되며 클러스터 내에서만 라우터링됩니다.

# DNS 기반 서비스 검색

쿠버네티스는 클러스터 내에서 서비스 검색을 위한 내장 DNS 서비스를 제공합니다. 서비스들은 기본 파드를 추상화한 안정적인 엔드포인트 역할을 합니다. 각 서비스는 DNS 이름이 할당되며, 해당 서비스를 지원하는 파드의 IP 주소로 해석됩니다. 이 DNS 기반 접근 방식을 통해 파드는 개별 파드 IP 주소를 직접 참조하는 대신 서비스 이름을 사용하여 서로 통신할 수 있습니다.

# 서비스 로드 밸런싱

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

여러 개의 팟이 동일한 애플리케이션을 제공할 때, Kubernetes는 이러한 팟들 간의 트래픽을 분산하는 데 사용하는 내장된 로드 밸런싱 기능을 제공합니다. 서비스 객체를 생성하고 이를 일련의 팟과 연결함으로써, Kubernetes는 사용 가능한 팟들 사이에 들어오는 요청을 자동으로 로드 밸런싱합니다. 이 로드 밸런싱 메커니즘은 애플리케이션의 고가용성과 확장성을 보장합니다.

# 네트워크 정책

Kubernetes는 팟 간의 트래픽 흐름을 제어하기 위한 수단으로 네트워크 정책을 제공합니다. 네트워크 정책은 IP 주소, 포트 및 프로토콜과 같은 다양한 매개변수를 기반으로 어떤 팟이 서로 통신할 수 있는지를 지정하는 규칙을 정의합니다. 네트워크 정책을 시행함으로써, 애플리케이션의 네트워크 트래픽을 세분화하고 추가적인 보안 층을 추가할 수 있습니다.

# 외부 통신

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

Kubernetes 클러스터 바깥의 리소스와 통신해야 하는 팟이 자주 있습니다. 외부 서비스나 데이터베이스와 통신하는 방법을 용이하게 해주는 여러 메커니즘이 Kubernetes에서 제공됩니다. 한 가지 접근법은 서비스 유형이 "LoadBalancer" 또는 "NodePort"인 서비스를 사용하여 팟 또는 팟 세트를 노출하는 것으로, 외부 클라이언트가 팟에 액세스할 수 있게 합니다. 또 다른 옵션은 Ingress 컨트롤러를 사용하는 것인데, 이를 통해 외부 클러스터로부터 들어오는 트래픽을 정의된 규칙에 따라 적절한 팟으로 라우팅할 수 있습니다.

# 서비스 메쉬

고급 네트워킹 시나리오를 위해, 서비스 메쉬를 사용하여 팟 간 통신을 강화할 수 있습니다. Istio나 Linkerd와 같은 서비스 메쉬는 Kubernetes 클러스터 상단에 있는 레이어로써 트래픽 관리, 관찰가능성, 보안과 같은 기능을 제공합니다. 서비스 메쉬를 사용하면 고급 라우팅 규칙, 회로 차단, 분산 추적을 통해 팟 간 통신을 제어하고 모니터링할 수 있습니다.

# 예시

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

Kubernetes에서 pod 간 통신을 구성하는 방법을 보여드리기 위해 예제 사양을 살펴보겠습니다. 이 예제에서는 서비스를 사용하여 두 개의 파드를 생성하고 그들 간의 통신을 수립할 것입니다.

- Pod A 생성: 먼저 간단한 웹 애플리케이션을 실행하는 Pod A를 생성해 보겠습니다. pod-a.yaml이라는 파일을 만들고 아래 내용을 추가하세요:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-a
spec:
  containers:
    - name: web-app
      image: your-web-app-image
      ports:
        - containerPort: 8080
```

"your-web-app-image"를 사용 중인 웹 애플리케이션의 적절한 이미지로 교체해 주세요. 이 사양은 지정된 포트 8080이 노출된 상태로 "pod-a"라는 이름의 파드를 생성합니다.

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

2. Pod B를 생성하세요: 이제 Pod A와 통신하는 클라이언트 Pod인 Pod B를 생성해 봅시다. pod-b.yaml이라는 파일을 만들고 다음 내용을 추가해 주세요:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-b
spec:
  containers:
    - name: client-app
      image: your-client-app-image
      command: ["sleep", "infinity"]
```

your-client-app-image를 사용하고 있는 클라이언트 어플리케이션에 해당하는 이미지로 변경해 주세요. 이 명세는 "pod-b"라는 이름의 pod를 생성하고, 해당 컨테이너를 실행하여 pod를 계속 실행 상태로 유지할 수 있는 무한 sleep 명령을 실행합니다.

3. 서비스 생성하기: Pod A와 Pod B 간의 통신을 가능하게 하기 위해 안전한 엔드포인트로 작동하는 서비스를 생성할 것입니다. service.yaml이라는 파일을 만들고 다음 내용을 추가하세요:

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
apiVersion: v1
kind: Service
metadata:
  name: pod-service
spec:
  selector:
    app: web-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

이 구성은 "pod-service"라는 서비스를 생성하며, 이 서비스는 app: web-app 라벨을 가진 파드를 대상으로합니다 (우리가 Pod A에 추가 할 것입니다). 이 서비스는 포트 80을 노출하고 선택한 파드의 포트 8080으로 트래픽을 전달합니다.

4. 설정 적용: 다음 명령을 사용하여 생성된 구성을 적용하십시오:

```yaml
kubectl apply -f pod-a.yaml
kubectl apply -f pod-b.yaml
kubectl apply -f service.yaml
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

이제 쿠버네티스 클러스터에 Pod A, Pod B 및 서비스가 생성됩니다.

5. 통신 테스트: 통신을 테스트하려면 Pod B에 접속하여 서비스의 DNS 이름을 사용하여 Pod A에 요청을 보낼 수 있습니다. 다음 명령어를 실행하세요:

```sh
kubectl exec -it pod-b -- sh
```

Pod B 쉘에 들어간 후에 curl과 같은 도구를 사용하여 Pod A에 요청을 보낼 수 있습니다. 서비스 명세서에서 다른 이름을 사용했다면 실제 서비스 이름으로 pod-service를 대체하세요.

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
curl pod-service
```

이 명령은 서비스로 요청을 보내어 트래픽을 로드 밸런싱하고 Pod A로 전달합니다.

여기까지입니다! 이제 쿠버네티스에서 서비스를 사용하여 pod 간 통신을 설정했습니다. 다양한 통신 패턴을 탐색하거나 네트워크 정책을 적용하거나 특정 요구 사항을 충족시키기 위해 추가적인 쿠버네티스 기능을 활용하여 이 예시를 확장할 수 있습니다.

# 결론

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

마크다운 형식으로 테이블 태그를 변경하세요.
