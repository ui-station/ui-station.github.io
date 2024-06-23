---
title: "쿠버네티스 이스티오 앰비언트 메쉬 투어  1부 설정 및 Z터널 사용 방법"
description: ""
coverImage: "/assets/img/2024-06-23-TouringtheKubernetesIstioAmbientMeshPart1SetupZTunnel_0.png"
date: 2024-06-23 23:14
ogImage: 
  url: /assets/img/2024-06-23-TouringtheKubernetesIstioAmbientMeshPart1SetupZTunnel_0.png
tag: Tech
originalTitle: "Touring the Kubernetes Istio Ambient Mesh — Part 1: Setup, ZTunnel"
link: "https://medium.com/@SabujJanaCodes/touring-the-kubernetes-istio-ambient-mesh-part-1-setup-ztunnel-c80336fcfb2d"
---


피츠오는 앰비언트 모드로 전환하고 있어요 — 사이드카 없는 모델로 말이죠. 마침내 우리는 CPU와 메모리 소비가 많은 사이드카를 버릴 수 있게 되었어요!

다가오는 장마시즌을 맞아, 저는 피츠오 앰비언트 메시에 간접적으로 참여해보기로 했어요.

이 블로그를 사용하여 여행을 문서화하고, 비슷한 생각을 가진 피츠오 사용자들이 함께 따라올 수 있게 도와볼 거예요 :P.

![이미지](/assets/img/2024-06-23-TouringtheKubernetesIstioAmbientMeshPart1SetupZTunnel_0.png)

<div class="content-ad"></div>

제가 나름대로 번역해보겠습니다:

안녕하세요! Part 1에서는 주변 메시 실험을 위해 로컬 클러스터 설정을 완벽하게 진행할 예정입니다. 그런 다음 메시의 ztunnel 구성 요소를 탐험할 것입니다.

Part 2에서는 L4 인증 정책에 대해 이야기하고 waypoint 프록시를 시작하는 방법을 살펴볼 것입니다.

# Ambient란 무엇인가요?

Istio Ambient Mesh는 사이드카 없이 Istio의 새로운 데이터플레인 모드입니다.

<div class="content-ad"></div>

일반적인 Istio 모드에서는 모든 응용 프로그램 Pod이 envoy 프록시로 주입되었다는 것을 기억하십시오. 그러나 이 새로운 모드에서는 응용 프로그램 Pod이 건드리지 않을 거에요 :) 그리고 그들은 자신의 응용 프로그램 컨테이너만 가지게 될 거에요.

![이미지](/assets/img/2024-06-23-TouringtheKubernetesIstioAmbientMeshPart1SetupZTunnel_1.png)

가장 먼저 떠오르는 큰 장점 중 하나는 인프라 비용이 크게 절감된다는 것입니다. 컴퓨팅 코어 및 메모리 관점에서 얼마나 많은 돈을 절약하고 있는지 상상해보세요 !!

![이미지](/assets/img/2024-06-23-TouringtheKubernetesIstioAmbientMeshPart1SetupZTunnel_2.png)

<div class="content-ad"></div>

# Ambient Architecture

과거와 현재의 Ambient Architecture를 상상해 보는 시간입니다. 데이터 평면의 트래픽 흐름 경로에서 그들이 어떻게 다른지 살펴봅시다.

## Sidecar 모드

![이미지](/assets/img/2024-06-23-TouringtheKubernetesIstioAmbientMeshPart1SetupZTunnel_3.png)

<div class="content-ad"></div>

이것은 전통적인 사이드카 모델이며 각 서비스 팟에는 애플리케이션 컨테이너와 Envoy 사이드카가 결합되어 있습니다. 애플리케이션으로부터 오고 가는 모든 트래픽은 사이드카에 의해 가로채집니다.

## Ambient 모드, ztunnel 사용

![이미지](/assets/img/2024-06-23-TouringtheKubernetesIstioAmbientMeshPart1SetupZTunnel_4.png)

이 새로운 ambient 모드에서 애플리케이션 팟은 사이드카가 없는 독립적인 팟입니다. 그러나 이 경우에는 클러스터의 각 Kubernetes 노드마다 데몬셋 팟이 실행될 것입니다 - 강력한 ztunnel (제로 트러스트 터널). 노드 내 팟간의 모든 트래픽은 ztunnel에 의해 가로채집됩니다. Ztunnel은 각 노드당 L4 프록시입니다.

<div class="content-ad"></div>

## Ambient mode, with ztunnel + waypoint

![Image](/assets/img/2024-06-23-TouringtheKubernetesIstioAmbientMeshPart1SetupZTunnel_5.png)

Ztunnel은 L4 프록시가 필요한 워크로드 간 네트워킹에 충분합니다. http 헤더 기반 라우팅, L7 권한 부여와 같은 L7 요구 사항을 충족시키기 위해 waypoint 프록시라는 워크로드를 배포합니다. 이는 애플리케이션당 envoy 팟으로, 동일한 노드 또는 다른 노드에서 실행될 수 있습니다.

이 경우 ztunnel에서 생성된 트래픽은 waypoint 프록시에 도달하고, waypoint는 그것을 목적지 ztunnel로 전달합니다.

<div class="content-ad"></div>

그러므로, 어플리케이션이 L7 처리를 요구하지 않는 경우 waypoint를 없애고 ztunnel만 사용할 수 있습니다. 이전 방식에서는, 우리가 L4 요구사항만 가지고 있더라도 envoy sidecars를 반드시 사용해야 했습니다.

# 클러스터 설정 깊이에 대한 정보

이상적으로 지원되는 환경에서 사용할 수 있는 유효한 CNI 설정으로 Kind 클러스터를 설정하는 데 많은 조정이 필요했습니다. 현재는 어떤 공개 GKE/AKS/EKS k8s 클러스터에 대한 실험을 할 수 있는 권한이 없습니다.

![이미지](/assets/img/2024-06-23-TouringtheKubernetesIstioAmbientMeshPart1SetupZTunnel_6.png)

<div class="content-ad"></div>

그러므로 로컬 Kind 클러스터를 홈 설정하겠습니다.

## 시스템 사양

- Mac M1 (Apple-Silicon 아키텍처)를 사용하며 기본 구성으로 진행됩니다.

시스템 아키텍처(Linux/Windows/Apple Intel)를 고려하여 비슷하게 진행할 수 있습니다.

<div class="content-ad"></div>

- Rancher Desktop

저희 k8s 클러스터를 위해 도커 런타임이 필요한데, 저는 SUSE의 Rancher Desktop을 사용할 예정입니다 — https://rancherdesktop.io/.

다른 대안으로는 Docker Desktop이 있습니다 — https://www.docker.com/products/docker-desktop/

- Kind

<div class="content-ad"></div>

저는 Multi-Node K8s 클러스터가 필요하다고 생각해요. 그래서 우리가 사용할 최상의 도구는 Kind를 이용해 클러스터를 부트스트랩하는 거죠. 여기 https://kind.sigs.k8s.io/docs/user/quick-start 에서 보다 자세한 정보를 얻을 수 있어요.

이를 통해 우리는 k8s 노드에 ssh로 접속하고 노드 구성을 실험해볼 수도 있을 거에요.

그리고 블로그에 나온대로 istio-ambient 프로필을 설치한 후에는 Kind 노드에서 문제가 발생할 수 있어요. 여기 https://kind.sigs.k8s.io/docs/user/known-issues/#pod-errors-due-to-too-many-open-files 에서 문제를 해결할 수 있어요. 영향을 받는 노드의 sysctl.conf를 수정한 후에는 istio-system pods가 문제없이 생성될 거에요.

<img src="/assets/img/2024-06-23-TouringtheKubernetesIstioAmbientMeshPart1SetupZTunnel_7.png" />

<div class="content-ad"></div>

- CNI(Container Networking Interface)

"Istio Ambient Mode"은 Calico, Cilium 등과 같은 CNI와 함께 지원됩니다. Kind 클러스터의 기본 CNI는 "kindnetd"이며, 저는 직접 테스트해 본 결과 ztunnel 팟이 실행되지 않았습니다. 그래서 다른 CNI가 필요했습니다.

- Cilium CNI도 Mac M1에서 제대로 로드되지 않았습니다. 즉, cilium 데몬셋 팟이 실행되지 않았습니다.
- 마침내 Calico CNI로 Kind를 설정하는 방법을 찾았습니다. Kind에서 기본 CNI를 비활성화하고 다음과 같이 진행합니다: [https://docs.tigera.io/calico/latest/getting-started/kubernetes/kind](https://docs.tigera.io/calico/latest/getting-started/kubernetes/kind)
- 이 설정에서 마지막 단계를 따를 수도 있습니다: [https://alexbrand.dev/post/creating-a-kind-cluster-with-calico-networking/](https://alexbrand.dev/post/creating-a-kind-cluster-with-calico-networking/)

# Istio 설치

<div class="content-ad"></div>

## 클러스터 개요

이스티오를 시작하기 전에 클러스터 스펙을 살펴봅시다.

다음은 Kind 클러스터 설정 스크립트입니다.

마스터 노드 1개와 워커 노드 2개가 있습니다.

<div class="content-ad"></div>

```js
(⎈|kind-ambient:istio-system)➜  ~ kg 노드
이름                    상태     역할           생성 시간    버전
ambient-control-plane   준비     제어 플레인    117분     v1.30.0
ambient-worker          준비     <없음>         116분     v1.30.0
ambient-worker2         준비     <없음>         116분     v1.30.0
```

또한 Calico CNI 데몬세트가 실행 중인지 확인합니다. 클러스터 CoreDNS도 준비되어 있는지 확인합니다.

```js
(⎈|kind-ambient:kube-system)➜  ~ kgpo
이름                                            준비     상태      재시작      생성 시간
calico-kube-controllers-564985c589-xmsrp        1/1     실행 중  1 (96분 전)  117분
calico-node-796kq                               1/1     실행 중  0             116분
calico-node-l6fsg                               1/1     실행 중  0             116분
calico-node-zkckp                               1/1     실행 중  0             116분
coredns-7db6d8ff4d-h88q6                        1/1     실행 중  0             118분
coredns-7db6d8ff4d-rtncd                        1/1     실행 중  0             118분
```

## Istio Ambient 프로필 설치

<div class="content-ad"></div>

설치는 간단하게 진행됩니다. 여기에서 찾을 수 있어요: [https://istio.io/v1.20/docs/ops/ambient/getting-started/](https://istio.io/v1.20/docs/ops/ambient/getting-started/)

```js
sabuj $ istioctl install --set profile=ambient --set "components.ingressGateways[0].enabled=true" --set "components.ingressGateways[0].name=istio-ingressgateway" --skip-confirmation
✔ Istio core가 설치되었습니다
✔ Ztunnel이 설치되었습니다
✔ Istiod가 설치되었습니다
✔ CNI가 설치되었습니다
✔ 인그레스 게이트웨이가 설치되었습니다
✔ 설치가 완료되었습니다
이 설치를 주입과 유효성 검사를 위한 기본 설정으로 지정합니다.
```

- 내가 사용 중인 Istio 버전은 다음과 같아요.

```js
(⎈|kind-ambient:kube-system)➜  ~ istioctl version
client version: 1.15.0
control plane version: 1.22.1
data plane version: 1.22.1 (4 프록시)
```

<div class="content-ad"></div>

- Ztunnel은 각 노드에서 실행되는 데몬세트입니다 — 이 경우 3개의 노드가 있습니다. istio-system 네임스페이스에서 ztunnel이 제대로 실행 중인지 확인해 봅시다.

```js
(⎈|kind-ambient:istio-system)➜  ~ kgpo -owide
NAME                                    READY   STATUS    RESTARTS   AGE    IP               NODE                    NOMINATED NODE   READINESS GATES
istio-cni-node-7q6z7                    1/1     Running   0          117m   172.18.0.2       ambient-worker2         <none>           <none>
istio-cni-node-fg6n4                    1/1     Running   0          117m   172.18.0.4       ambient-worker          <none>           <none>
istio-cni-node-gwvl8                    1/1     Running   0          117m   172.18.0.3       ambient-control-plane   <none>           <none>
istio-ingressgateway-6f48dfb7db-862sm   1/1     Running   0          117m   192.168.184.70   ambient-worker          <none>           <none>
istiod-6875bc5c58-n4j7d                 1/1     Running   0          118m   192.168.246.2    ambient-worker2         <none>           <none>
ztunnel-62hp8                           1/1     Running   0          117m   192.168.246.3    ambient-worker2         <none>           <none>
ztunnel-fv5f8                           1/1     Running   0          117m   192.168.184.71   ambient-worker          <none>           <none>
ztunnel-gs52c                           1/1     Running   0          117m   192.168.208.1    ambient-control-plane   <none>           <none>

(⎈|kind-ambient:istio-system)➜  ~ kg ds
NAME             DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
istio-cni-node   3         3         3       3            3           kubernetes.io/os=linux   119m
ztunnel          3         3         3       3            3           kubernetes.io/os=linux   118m
```

정말로 3개의 파드가 있네요 !

- 비슷하게 Istio-cni는 또 다른 설치된 데몬세트입니다.
- Istio 제어 평면인 istiod도 실행 중입니다.
- 우리는 공개 트래픽을 위해 기본 istio ingressgateway도 설치했습니다.

<div class="content-ad"></div>

# 작업량 설정

이 프로젝트의 모든 매니페스트 파일은 여기에서 찾을 수 있습니다: https://github.com/JanaSabuj/istio-ambient-mesh-exploration

## 안건

- 우리는 앰비언트 프로필 없이 앱을 설정하고, 일반 Istio crds를 사용할 것입니다. 그 후에 앱을 호출할 예정입니다. i) 인그레스 ii) 디버그 클라이언트 pod
- 그 후에, Istio 데이터 평면 모드를 앰비언트로 전환하고, ztunnel pod를 통해 흐르는 트래픽 경로의 변화를 관찰할 것입니다.

<div class="content-ad"></div>

## 네임스페이스

ambient-demo라는 네임스페이스를 만들어 보겠습니다. 이 네임스페이스에서 앱을 호스팅할 예정입니다.

```js
(⎈|kind-ambient:istio-system)➜  ~ k create ns ambient-demo

(⎈|kind-ambient:istio-system)➜  ~ kg ns
NAME                 STATUS   AGE
default              Active   132m
istio-system         Active   125m
kube-node-lease      Active   132m
kube-public          Active   132m
kube-system          Active   132m
local-path-storage   Active   132m
```

## 애플리케이션

<div class="content-ad"></div>

그럼 배포, 서비스 및 서비스 어카운트 yaml을 사용하여 애플리케이션을 배포합니다.

클러스터에서는 다음과 같이 보입니다.

```bash
(⎈|kind-ambient:ambient-demo)➜  ~ kg all
NAME                          READY   STATUS    RESTARTS   AGE
pod/httpbin-6f4dc97cb-5dpz9   1/1     Running   0          3m21s
pod/httpbin-6f4dc97cb-swdlb   1/1     Running   0          3m31s

NAME              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/httpbin   ClusterIP   10.96.157.221   <none>        80/TCP    143m

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/httpbin   2/2     2            2           143m

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/httpbin-6f4dc97cb    2         2         2       3m31s
```

## Istio 구성

<div class="content-ad"></div>

외부 세계에 노출하기 위해 Istio Gateway 및 Istio VirtualService를 생성하여 Istio Ingress를 통해 액세스할 수 있게 만듭니다.

- 우리는 Istio Ingress 파드에 8081 포트를 통해 게이트웨이를 추가하고 있습니다.
- 그 다음으로, VirtualService를 통해 ambient-demo 네임스페이스에서 실행되는 httpbin 서비스로 라우팅합니다.

## Istio Ingress를 통한 확인

이제 우리의 응용 프로그램에 액세스할 수 있는지 확인할 수 있습니다.

<div class="content-ad"></div>

```js
(⎈|kind-ambient:istio-system)➜  ~ kgs
NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                                      AGE
istio-ingressgateway   LoadBalancer   10.96.77.172   <pending>     15021:30807/TCP,80:30587/TCP,443:31004/TCP   147m
```

Kind가 인그레스 서비스를 위한 외부 IP를 제공하지 않았기 때문에, 원하는 리스너 8081에서 인그레스 파드를 포트포워딩하여 외부 액세스를 시뮬레이션할 것입니다.

```js
(⎈|kind-ambient:istio-system)➜  ~ kpf istio-ingressgateway-6f48dfb7db-862sm 8081
Forwarding from 127.0.0.1:8081 -> 8081
Forwarding from [::1]:8081 -> 8081
```

브라우저에서 127.0.0.1:8081을 입력하여 애플리케이션을 확인할 수 있습니다!


<div class="content-ad"></div>

```js
http://127.0.0.1:8081/

httpbin.org
 0.9.2 
[ Base URL: 127.0.0.1:8081/ ]
간단한 HTTP 요청 및 응답 서비스입니다.

로컬에서 실행: $ docker run -p 80:80 kennethreitz/httpbin

개발자 - 웹사이트
개발자에게 이메일 보내기
```

- 또 다른 확인 방법은 로컬에서 curl을 통해 하는 것입니다. 우리는 요청이 istio-envoy 파드에 의해 처리된 것을 확인합니다.

```js
(⎈|kind-ambient:istio-system)➜  ~ curl 127.0.0.1:8081 -v
*   Trying 127.0.0.1:8081...
* Connected to 127.0.0.1 (127.0.0.1) port 8081
> GET / HTTP/1.1
> Host: 127.0.0.1:8081
> User-Agent: curl/8.6.0
> Accept: */*
>
< HTTP/1.1 200 OK
< server: istio-envoy
< date: Sun, 16 Jun 2024 11:52:13 GMT
< content-type: text/html; charset=utf-8
< content-length: 9593
< access-control-allow-origin: *
< access-control-allow-credentials: true
< x-envoy-upstream-service-time: 271
<
<!DOCTYPE html>
<html lang="en">
...
```

## 디버그 클라이언트 파드를 통한 확인


<div class="content-ad"></div>

우리는 메쉬 내부 호출을 테스트해보려고 합니다. 따라서 각 노드마다 클라이언트 팟이 하나씩 있는 것이 좋습니다. 이를 위해 디버거 데몬세트 클라이언트 워크로드를 설정할 수 있습니다 — https://github.com/digitalocean/doks-debug

사용한 수정된 Manifest: https://gist.github.com/JanaSabuj/a4dd2504752b8c2b30d2d2d05320f7ef

저는 이 데몬세트를 우리 ambient-demo 네임스페이스에 배포했습니다.

```js
(⎈|kind-ambient:ambient-demo)➜  ~ kg ds
NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
doks-debug   3         3         3       3            3           <none>          89m

(⎈|kind-ambient:ambient-demo)➜  ~ kgpo -owide
NAME                      READY   STATUS    RESTARTS   AGE   IP               NODE                    NOMINATED NODE   READINESS GATES
doks-debug-j9mm5          1/1     Running   0          90m   192.168.246.5    ambient-worker2         <none>           <none>
doks-debug-rdhgq          1/1     Running   0          90m   192.168.184.73   ambient-worker          <none>           <none>
doks-debug-v7cld          1/1     Running   0          90m   192.168.208.3    ambient-control-plane   <none>
```

<div class="content-ad"></div>

테스트해 보기 위해 디버그 팟으로 진입하여 k8s fqdn httpbin.ambient-demo.svc.cluster.local을 curl을 시도해 보았습니다.

200 OK를 반환하고 있습니다.

```js
(⎈|kind-ambient:ambient-demo)➜  ~ k exec -it doks-debug-j9mm5 -- /bin/bash

root@doks-debug-j9mm5:~# curl httpbin.ambient-demo.svc.cluster.local -v
*   Trying 10.96.157.221:80...
* Connected to httpbin.ambient-demo.svc.cluster.local (10.96.157.221) port 80 (#0)
> GET / HTTP/1.1
> Host: httpbin.ambient-demo.svc.cluster.local
> User-Agent: curl/7.88.1
> Accept: */*
>
< HTTP/1.1 200 OK
< Server: gunicorn/19.9.0
< Date: Sun, 16 Jun 2024 12:06:11 GMT
< Connection: keep-alive
< Content-Type: text/html; charset=utf-8
< Content-Length: 9593
< Access-Control-Allow-Origin: *
< Access-Control-Allow-Credentials: true
<
```

# Ambient Injection

<div class="content-ad"></div>

지금까지 앰비언트 모드가 활성화되지 않았습니다. Istio 게이트웨이에서 추가 된 리스너로 인해 요청은 Istio 인그레스 파드에 도착한 다음 VirtualService를 통해 앱 파드로 라우팅됩니다.

매쉬 내부 호출의 경우, 클라이언트와 서버 파드 사이에 직접적인 포드 간 통신이 이루어집니다.

![이미지](/assets/img/2024-06-23-TouringtheKubernetesIstioAmbientMeshPart1SetupZTunnel_8.png)

앰비언트를 주입하는 동안 우리는 또한 다음 로그를 계속 추적할 것입니다.

<div class="content-ad"></div>

- istio-cni
- ztunnel

Istio가 네임스페이스 ambient-demo에 대한 Ambient Dataplane 모드를 활성화하도록 내부적으로 라우트, iptables 등을 설정했는지 확인하려면 아래 명령어를 사용해보세요.

```js
(⎈|kind-ambient:ambient-demo)➜ 
~ kubectl label namespace ambient-demo istio.io/dataplane-mode=ambient
```

## 관찰된 로그

<div class="content-ad"></div>

- istio-cni

```js
(⎈|kind-ambient:ambient-demo)➜ ~ stern istio-cni -n istio-system

istio-cni-node-7q6z7 install-cni 2024-06-16T10:13:59.243451Z info ambient Namespace ambient-demo is enabled in ambient mesh
istio-cni-node-7q6z7 install-cni 2024-06-16T10:13:59.264500Z info ambient in pod mode - adding pod ambient-demo/httpbin-5bd875fbdd-84vs8 to ztunnel
istio-cni-node-gwvl8 install-cni 2024-06-16T10:13:59.291505Z info ambient Namespace ambient-demo is enabled in ambient mesh
istio-cni-node-fg6n4 install-cni 2024-06-16T10:13:59.281587Z info ambient Namespace ambient-demo is enabled in ambient mesh
istio-cni-node-fg6n4 install-cni 2024-06-16T10:13:59.313668Z info ambient in pod mode - adding pod ambient-demo/httpbin-5bd875fbdd-dp4ct to ztunnel

istio-cni-node-7q6z7 install-cni 2024-06-16T10:13:59.264500Z info ambient in pod mode - adding pod ambient-demo/httpbin-5bd875fbdd-84vs8 to ztunnel
istio-cni-node-7q6z7 install-cni 2024-06-16T10:13:59.325907Z info iptables Running iptables-restore with the following input:
istio-cni-node-7q6z7 install-cni * nat
istio-cni-node-7q6z7 install-cni -N ISTIO_OUTPUT
istio-cni-node-7q6z7 install-cni -A OUTPUT -j ISTIO_OUTPUT
istio-cni-node-7q6z7 install-cni -A ISTIO_OUTPUT -d 169.254.7.127 -p tcp -m tcp -j ACCEPT
istio-cni-node-7q6z7 install-cni -A ISTIO_OUTPUT -p tcp -m mark --mark 0x111/0xfff -j ACCEPT
istio-cni-node-7q6z7 install-cni -A ISTIO_OUTPUT ! -d 127.0.0.1/32 -o lo -j ACCEPT
istio-cni-node-7q6z7 install-cni -A ISTIO_OUTPUT ! -d 127.0.0.1/32 -p tcp -m mark ! --mark 0x539/0xfff -j REDIRECT --to-ports 15001
istio-cni-node-7q6z7 install-cni COMMIT
istio-cni-node-7q6z7 install-cni * mangle
istio-cni-node-7q6z7 install-cni -N ISTIO_PRERT
istio-cni-node-7q6z7 install-cni -N ISTIO_OUTPUT
istio-cni-node-7q6z7 install-cni -A PREROUTING -j ISTIO_PRERT
istio-cni-node-7q6z7 install-cni -A OUTPUT -j ISTIO_OUTPUT
istio-cni-node-7q6z7 install-cni -A ISTIO_PRERT -m mark --mark 0x539/0xfff -j CONNMARK --set-xmark 0x111/0xfff
istio-cni-node-7q6z7 install-cni -A ISTIO_PRERT -s 169.254.7.127 -p tcp -m tcp -j ACCEPT
istio-cni-node-7q6z7 install-cni -A ISTIO_PRERT ! -d 127.0.0.1/32 -p tcp -i lo -j ACCEPT
istio-cni-node-7q6z7 install-cni -A ISTIO_PRERT -p tcp -m tcp --dport 15008 -m mark ! --mark 0x539/0xfff -j TPROXY --on-port 15008 --tproxy-mark 0x111/0xfff
istio-cni-node-7q6z7 install-cni -A ISTIO_PRERT -p tcp -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
istio-cni-node-7q6z7 install-cni -A ISTIO_PRERT ! -d 127.0.0.1/32 -p tcp -m mark ! --mark 0x539/0xfff -j TPROXY --on-port 15006 --tproxy-mark 0x111/0xfff
istio-cni-node-7q6z7 install-cni -A ISTIO_OUTPUT -m connmark --mark 0x111/0xfff -j CONNMARK --restore-mark --nfmask 0xffffffff --ctmask 0xffffffff
istio-cni-node-7q6z7 install-cni COMMIT
istio-cni-node-7q6z7 install-cni 2024-06-16T10:13:59.335115Z info Running command (with wait lock): iptables-restore --noflush -v --wait=30
istio-cni-node-7q6z7 install-cni 2024-06-16T10:13:59.550687Z info ambient About to send added pod: 3ab72f78-8e2b-4e49-bc47-45fa4f90dbf7 to ztunnel: add:{uid:"3ab72f78-8e2b-4e49-bc47-45fa4f90dbf7" workload_info:{name:"httpbin-5bd875fbdd-84vs8" namespace:"ambient-demo" service_account:"default" trust_domain:"cluster.local"}
```

많은 로그가 생성되었습니다. 우리는 각 httpbin pod를 ambient-demo 네임스페이스에 추가하여 ztunnel을 통해 ambient 경로에 추가하고 있음을 확인할 수 있습니다.

- ztunnel

<div class="content-ad"></div>

```plaintext
(⎈|kind-ambient:ambient-demo)➜ ~ stern ztunnel -n istio-system

ztunnel-62hp8 istio-proxy 2024-06-16T10:13:59.559505Z 정보 inpod::statemanager pod WorkloadUid("3ab72f78-8e2b-4e49-bc47-45fa4f90dbf7")가 netns를 수신하고 프록시를 시작합니다.
ztunnel-fv5f8 istio-proxy 2024-06-16T10:13:59.557545Z 정보 inpod::statemanager pod WorkloadUid("7f8be6a6-64f2-40e9-8926-6c3a618eb7d9")가 netns를 수신하고 프록시를 시작합니다.
ztunnel-fv5f8 istio-proxy 2024-06-16T10:13:59.560458Z 정보 proxy::inbound 리스너가 구성되었으며 주소=[::]:15008 구성요소="inbound" 투명=true
ztunnel-fv5f8 istio-proxy 2024-06-16T10:13:59.561604Z 정보 proxy::inbound_passthrough 리스너가 구성되었으며 주소=[::]:15006 구성요소="inbound plaintext" 투명=true
ztunnel-fv5f8 istio-proxy 2024-06-16T10:13:59.561647Z 정보 proxy::outbound 리스너가 구성되었으며 주소=[::]:15001 구성요소="outbound" 투명=true
ztunnel-62hp8 istio-proxy 2024-06-16T10:13:59.573883Z 정보 proxy::inbound 리스너가 구성되었으며 주소=[::]:15008 구성요소="inbound" 투명=true
ztunnel-62hp8 istio-proxy 2024-06-16T10:13:59.586596Z 정보 proxy::inbound_passthrough 리스너가 구성되었으며 주소=[::]:15006 구성요소="inbound plaintext" 투명=true
ztunnel-62hp8 istio-proxy 2024-06-16T10:13:59.586819Z 정보 proxy::outbound 리스너가 구성되었으며 주소=[::]:15001 구성요소="outbound" 투명=true
ztunnel-fv5f8 istio-proxy 2024-06-16T10:13:59.811460Z 정보 xds::client:xds{id=14}가 응답을 수신했습니다. type_url="type.googleapis.com/istio.workload.Address" 크기=2  삭제=0
ztunnel-62hp8 istio-proxy 2024-06-16T10:13:59.816352Z 정보 xds::client:xds{id=14}가 응답을 수신했습니다. type_url="type.googleapis.com/istio.workload.Address" 크기=2  삭제=0
ztunnel-gs52c istio-proxy 2024-06-16T10:13:59.821310Z 정보 xds::client:xds{id=14}가 응답을 수신했습니다. type_url="type.googleapis.com/istio.workload.Address" 크기=2  삭제=0
```

ztunnel이 자체 내부 및 외부 리스너를 설정 중인 것으로 보입니다.

## 트래픽 흐름 - Istio Ingress를 통해

이전과 마찬가지로, istio-ingress pod로 포트 포워딩을 설정하고 localhost를 통해 액세스합니다.


<div class="content-ad"></div>

두 개의 호출을 각각 2개의 httpbin 팟에 대응하도록 인그레스에서 호출을 추출하려고 합니다. 그리고 동시에 동일한 ztunnel 로그를 캡처하려고 합니다.

```js
(⎈|kind-ambient:istio-system)➜  ~ kpf istio-ingressgateway-6f48dfb7db-862sm 8081
Forwarding from 127.0.0.1:8081 -> 8081

$ curl localhost:8081/
$ curl localhost:8081/
```

```js
(⎈|kind-ambient:istio-system)➜  ~ stern ztunnel -n istio-system

ztunnel-fv5f8 istio-proxy 2024-06-16T12:26:24.154500Z 
info access connection complete src.addr=192.168.184.70:52792 
src.workload=istio-ingressgateway-6f48dfb7db-862sm src.namespace=istio-system 
src.identity="spiffe://cluster.local/ns/istio-system/sa/istio-ingressgateway-service-account" 
dst.addr=192.168.184.74:80 dst.hbone_addr=192.168.184.74:80 
dst.service=httpbin.ambient-demo.svc.cluster.local 
dst.workload=httpbin-6f4dc97cb-swdlb dst.namespace=ambient-demo 
dst.identity="spiffe://cluster.local/ns/ambient-demo/sa/httpbin-sa" 
direction="inbound" bytes_sent=51083 bytes_recv=4180 duration="2166ms"

ztunnel-62hp8 istio-proxy 2024-06-16T12:28:40.267690Z 
info access connection complete src.addr=192.168.184.70:55036 
src.workload=istio-ingressgateway-6f48dfb7db-862sm src.namespace=istio-system 
src.identity="spiffe://cluster.local/ns/istio-system/sa/istio-ingressgateway-service-account" 
dst.addr=192.168.246.6:80 dst.hbone_addr=192.168.246.6:80 
dst.service=httpbin.ambient-demo.svc.cluster.local 
dst.workload=httpbin-6f4dc97cb-5dpz9 dst.namespace=ambient-demo 
dst.identity="spiffe://cluster.local/ns/ambient-demo/sa/httpbin-sa" 
direction="inbound" bytes_sent=41251 bytes_recv=2007 duration="2331ms"
```

인그레스가 주변 데이터 플레인 경로에 떨어지지 않기 때문에 인그레스 팟에서의 호출은 주변 데이터 플레인 경로로 직접 ztunnel 팟으로 이어집니다. 한 번에 하나의 노드로 이동한 후 해당 내부 노드 팟으로 inbound됩니다.

<div class="content-ad"></div>

아래는 로그 캡처에서 확인한 내용입니다

```js
direction="inbound"
```

- 이는 주변 레이블이 붙은 네임스페이스 파드로의 트래픽이 항상 ztunnel을 통해 이동함을 확인합니다.

<div class="content-ad"></div>


dst.hbone_addr=192.168.184.74:80
dst.hbone_addr=192.168.246.6:80

httpbin-6f4dc97cb-5dpz9   1/1     Running   0          56m    192.168.246.6    ambient-worker2         <none>           <none>
httpbin-6f4dc97cb-swdlb   1/1     Running   0          56m    192.168.184.74   ambient-worker          <none>           <none>


- 이것은 실제 httpbin pod ip에 해당하는 dest pod ip를 캡처합니다.

## 트래픽 흐름 — Mesh 내부를 통해

클라이언트 디버그 pod에 exec하여 httpbin 서비스로의 Mesh 내부 호출을 시도하고 동시에 ztunnel 로그를 캡쳐하여 동일한 동작을 확인해보겠습니다.


<div class="content-ad"></div>

```js
(⎈|kind-ambient:ambient-demo)➜  ~ kgpo -owide
NAME                      READY   STATUS    RESTARTS   AGE    IP               NODE                    NOMINATED NODE   READINESS GATES
doks-debug-j9mm5          1/1     Running   0          122m   192.168.246.5    ambient-worker2         <none>           <none>
doks-debug-rdhgq          1/1     Running   0          122m   192.168.184.73   ambient-worker          <none>           <none>
doks-debug-v7cld          1/1     Running   0          122m   192.168.208.3    ambient-control-plane   <none>           <none>
httpbin-6f4dc97cb-5dpz9   1/1     Running   0          56m    192.168.246.6    ambient-worker2         <none>           <none>
httpbin-6f4dc97cb-swdlb   1/1     Running   0          56m    192.168.184.74   ambient-worker          <none>           <none>
```

그냥 같은 노드에 있는 클라이언트와 앱 팟들을 연결하기 위해 —

```js
(⎈|kind-ambient:ambient-demo)➜  ~ kgpo -A -owide | grep "ambient-worker "
ambient-demo         doks-debug-rdhgq                                1/1     Running   0               133m    192.168.184.73   ambient-worker          <none>           <none>
istio-system         ztunnel-fv5f8                                   1/1     Running   0               3h24m   192.168.184.71   ambient-worker          <none>           <none>
ambient-demo         httpbin-6f4dc97cb-swdlb                         1/1     Running   0               68m     192.168.184.74   ambient-worker          <none>           <none>


(⎈|kind-ambient:ambient-demo)➜  ~ kgpo -A -owide | grep "ambient-worker2"
kube-system          doks-debug-9nlh7                                1/1     Running   0               3h6m    192.168.246.4    ambient-worker2         <none>           <none>
istio-system         ztunnel-62hp8                                   1/1     Running   0               3h23m   192.168.246.3    ambient-worker2         <none>           <none>
ambient-demo         httpbin-6f4dc97cb-5dpz9                         1/1     Running   0               67m     192.168.246.6    ambient-worker2         <none>           <none>
```

debug pod인 doks-debug-rdhgq를 실행하기 위해 ambient-worker 노드에 스케줄된 상태로 들어가보겠습니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-23-TouringtheKubernetesIstioAmbientMeshPart1SetupZTunnel_10.png" />

```js 
테이블 1: 동일 노드 내의 클라이언트 및 서버
---------
ztunnel-fv5f8 istio-proxy 2024-06-16T12:40:48.707707Z info access connection complete src.addr=192.168.184.73:56463 src.workload=doks-debug-rdhgq src.namespace=ambient-demo src.identity="spiffe://cluster.local/ns/ambient-demo/sa/default" dst.addr=192.168.184.74:80 dst.hbone_addr=192.168.184.74:80 dst.service=httpbin.ambient-demo.svc.cluster.local dst.workload=httpbin-6f4dc97cb-swdlb dst.namespace=ambient-demo dst.identity="spiffe://cluster.local/ns/ambient-demo/sa/httpbin-sa" direction  ="inbound" bytes_sent=9832 bytes_recv=84 duration="178ms"
ztunnel-fv5f8 istio-proxy 2024-06-16T12:40:48.708070Z info access connection complete src.addr=192.168.184.73:34190 src.workload=doks-debug-rdhgq src.namespace=ambient-demo src.identity="spiffe://cluster.local/ns/ambient-demo/sa/default" dst.addr=192.168.184.74:15008 dst.hbone_addr=192.168.184.74:80 dst.service=httpbin.ambient-demo.svc.cluster.local dst.workload=httpbin-6f4dc97cb-swdlb dst.namespace=ambient-demo dst.identity="spiffe://cluster.local/ns/ambient-demo/sa/httpbin-sa" direction="outbound" bytes_sent=84 bytes_recv=9832 duration="203ms"
```

동일 노드 내의 클라이언트와 서버의 경우, 파드에서 ztunnel로 외부 패킷이 들어오면 동일한 ztunnel을 통해 대상 파드로 들어오는 내부 패킷으로 리디렉션됩니다.

따라서, 동일 노드 내의 클라이언트 및 서버에서는 한 ztunnel이 외부 패킷과 내부 패킷을 받습니다. "bound"는 동일 노드 내의 응용 프로그램 파드로부터/받는 방향을 지정합니다.

<div class="content-ad"></div>

```js
캡처 2: 클라이언트 및 서버가 다른 노드에 있는 경우
---------
ztunnel-62hp8 istio-proxy 2024-06-16T12:48:51.792527Z info access connection complete src.addr=192.168.184.73:53265 src.workload=doks-debug-rdhgq src.namespace=ambient-demo src.identity="spiffe://cluster.local/ns/ambient-demo/sa/default" dst.addr=192.168.246.6:80 dst.hbone_addr=192.168.246.6:80 dst.service=httpbin.ambient-demo.svc.cluster.local dst.workload=httpbin-6f4dc97cb-5dpz9 dst.namespace=ambient-demo dst.identity="spiffe://cluster.local/ns/ambient-demo/sa/httpbin-sa" direction="inbound" bytes_sent=9832 bytes_recv=84 duration="60ms"
ztunnel-fv5f8 istio-proxy 2024-06-16T12:48:51.793112Z info access connection complete src.addr=192.168.184.73:60162 src.workload=doks-debug-rdhgq src.namespace=ambient-demo src.identity="spiffe://cluster.local/ns/ambient-demo/sa/default" dst.addr=192.168.246.6:15008 dst.hbone_addr=192.168.246.6:80 dst.service=httpbin.ambient-demo.svc.cluster.local dst.workload=httpbin-6f4dc97cb-5dpz9 dst.namespace=ambient-demo dst.identity="spiffe://cluster.local/ns/ambient-demo/sa/httpbin-sa" direction="outbound" bytes_sent=84 bytes_recv=9832 duration="61ms"
```

만약 클라이언트와 서버가 다른 노드에 있는 경우, 출발 트래픽은 동일한 노드인 ztunnel에서 외부 트래픽 패킷을 만나고, 그런 다음 목적지 파드의 노드의 ztunnel로 전송되어 들어오는 패킷으로 처리됩니다.

# 결론

이 실험을 통해 Istio Ambient Mesh에서 ztunnel을 통한 패킷 흐름을 시각화할 수 있었습니다.

<div class="content-ad"></div>

![이미지](/assets/img/2024-06-23-TouringtheKubernetesIstioAmbientMeshPart1SetupZTunnel_11.png)

다음 파트에서는 ztunnel을 통해 적용할 수 있는 L4 인증 정책을 탐색할 것입니다.

또한 Ambient에 있는 L7 프록시인 Waypoint Proxy에 대해 탐구할 것입니다.

다른 기술 블로그는 여기에서 확인할 수 있습니다: [링크](https://janasabuj.github.io/posts/)