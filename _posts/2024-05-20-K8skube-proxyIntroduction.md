---
title: "K8s - kube-proxy 소개"
description: ""
coverImage: "/assets/img/2024-05-20-K8skube-proxyIntroduction_0.png"
date: 2024-05-20 17:09
ogImage:
  url: /assets/img/2024-05-20-K8skube-proxyIntroduction_0.png
tag: Tech
originalTitle: "K8s — kube-proxy Introduction"
link: "https://medium.com/@tonylixu/k8s-kube-proxy-introduction-c847915efe57"
---

![kube-proxy](/assets/img/2024-05-20-K8skube-proxyIntroduction_0.png)

kube-proxy는 kubelet과 마찬가지로 Kubernetes 시스템 내 각 노드에서 실행되는 데몬입니다. 이것은 클러스터 내에서 기본로드 밸런싱을 담당합니다. kube-proxy의 작동은 서비스 및 엔드포인트/엔드포인트 슬라이스에 기반합니다:

- 서비스: 이들은 팟 그룹을위한 로드 밸런서로 작동합니다.
- 엔드포인트(및 엔드포인트 슬라이스): 이것들은 서비스에서 동일한 팟 선택기를 사용하여 자동으로 생성된 준비된 팟 IP 시리즈를 열거합니다.

Kubernetes의 대부분의 서비스 유형은 클러스터 내부 IP 주소 인 클러스터 IP 주소를 보유하며, 클러스터 외부에서 액세스할 수 없습니다.

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

kube-proxy는 여러분이 클러스터 IP 주소로 요청을 안내하고 그 요청이 건강한 파드에 도달하도록 하는 역할을 합니다. kube-proxy에는 네 가지 모드가 있으며, 이 모드에 따라 실행 모드와 구체적인 기능 세트가 변경됩니다:

- Userspace 모드 (폐기 예정): 이 모드에서 kube-proxy는 각 서비스에 대해 포트에서 수신 대기합니다. 트래픽을 수신하면 해당 트래픽을 백엔드 파드 중 하나로 프록시합니다. 이 방법은 성능에 영향을 미치므로 일반적으로 사용되지 않습니다.
- iptables 모드: 이 모드에서 kube-proxy는 네트워크 규칙을 구성하여 서비스의 트래픽을 올바른 백엔드 파드로 안내합니다. 이 모드는 userspace 모드보다 빠르고 믿을 만합니다. kube-proxy의 운영에 대한 기본 모드입니다.
- IPVS 모드: IPVS (IP Virtual Server) 모드는 iptables와 유사하지만 해시 테이블을 사용하여 백엔드를 만들어 네트워크 트래픽 측면에서 훨씬 확장 가능하고 효율적입니다.
- Kernelspace 프록시 모드: 이 프록시 모드는 Windows 노드에서만 사용할 수 있습니다. kube-proxy는 Windows Virtual Filtering Platform (VFP)에 있는 패킷 필터링 규칙을 구성합니다. 이것은 Windows vSwitch의 확장입니다.

# Userspace 모드 (폐기 예정)

최초이자 가장 오래된 운영 모드는 userspace 모드입니다. 이 모드에서 kube-proxy는 웹 서버를 운영하고 모든 서비스 IP 주소를 이 서버로 안내하여 iptables를 활용합니다. 이 웹 서버는 연결을 완료하고 프록시 역할을 수행하여 서비스의 엔드포인트에 나열된 파드로 요청을 전달합니다. 그러나 userspace 모드는 지금은 거의 사용되지 않으며, 이용할만한 강력한 이유가 없다면 피하는 것이 좋습니다.

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

예를 들어, Cluster IP가 10.0.0.1인 Service S가 있고 이 서비스에는 3개의 백엔드 Pod(A, B, C)가 있습니다. 이제 클라이언트가 Service S에 연결하려면 10.0.0.1로 연결될 것입니다.

- 각 노드에서 실행되는 유저스페이스 모드의 kube-proxy는 이 연결을 가로챕니다. 이는 10.0.0.1로 오는 트래픽을 해당 노드에서 실행 중인 자체 프록시 서버로 전달하는 iptables 규칙을 유지합니다.
- 프록시 서버는 건강한 백엔드 Pod 목록(A, B, C)을 유지하며, 서비스에 구성된 세션 어피니티 및 로드 밸런싱 알고리즘에 따라 요청을 전달할 Pod를 선택합니다.
- 프록시 서버는 선택된 Pod에 대한 새로운 연결을 설정하고, 클라이언트의 요청을 보내고, Pod로부터 응답을 받은 후 응답을 클라이언트에게 전달합니다.

이 프로세스는 기능적이지만 네트워크 경로에 추가적인 홉(유저스페이스 프록시 서버)을 도입하여 성능 부담을 유발하며, 다른 모드만큼 효율적이지 않을 수 있습니다. 이러한 이유로 유저스페이스 모드는 요즘 거의 사용되지 않습니다. kube-proxy의 기본 모드는 iptables이며, 고성능의 커널 수준 로드 밸런싱이 필요한 경우 ipvs 모드가 사용됩니다.

# iptables 모드

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

`iptables` 모드는 작동에 iptables에만 의존합니다. 이게 기본 모드이자 가장 많이 사용되는 모드입니다. IPVS 모드는 최근에 General Availability (GA) 안정성을 달성했지만, iptables는 이미 잘 알려진 Linux 기술이기 때문에 일부로 인해 이 모드가 더 많이 사용될 수 있습니다.

![이미지](/assets/img/2024-05-20-K8skube-proxyIntroduction_1.png)

`iptables` 모드는 실제 로드 밸런싱이 아니라 연결 분배를 용이하게 합니다. 다시 말해, `iptables` 모드가 연결을 백엔드 팟으로 라우팅하면 해당 연결을 통한 후속 요청은 해당 연결이 종료될 때까지 계속해서 동일한 팟으로 전달됩니다. 최적의 시나리오에서는, 이러한 행동이 간단하고 예측 가능하여 같은 연결 내 연속적인 요청은 백엔드 팟의 로컬 캐싱을 이용할 수 있습니다.

하지만 이 방식은 HTTP/2 연결과 같이 오랜 기간 지속되는 연결 같은 예측할 수 없는 행동을 유발할 수 있습니다. 특히 HTTP/2가 gRPC의 전송 프로토콜로 사용되는 점이 주목할 만합니다.

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

한 예로, 서비스가 X와 Y 두 개의 팟으로 제공되는 상황을 고려해 봅시다. 전형적인 롤링 업데이트 중에 X가 Z로 교체됩니다. 이전 팟인 Y는 모든 기존 연결을 유지하면서도 X 팟이 종료되었을 때 다시 설정해야 했던 연결의 절반을 담당합니다. 이로 인해 Y 팟이 제공하는 트래픽이 상당히 증가할 수 있습니다. 이와 같이 트래픽의 불균형을 야기할 수 있는 여러 상황이 있습니다.

예를 들어, 클러스터 IP가 10.0.0.1이고 백엔드 팟 A, B, C를 갖는 서비스 S가 있다고 가정해 봅시다.

iptables 모드에서 클라이언트가 10.0.0.1의 서비스 S에 연결하려고 하면, 연결 요청은 kube-proxy에 의해 가로채집니다.

모든 노드에서 실행되는 kube-proxy는 10.0.0.1로 오는 트래픽을 백엔드 팟(A, B, 또는 C 중 하나)으로 투명하게 전달하는 iptables 규칙을 유지합니다. 이를 위해 패킷의 목적지 IP 주소를 하나의 팟의 IP 주소로 변경하는 NAT 규칙을 생성합니다. kube-proxy는 서비스가 구성한 세션 어피니티와 로드 밸런싱 알고리즘에 기반하여 백엔드 팟을 선택합니다. 이 결정은 연결이 설정될 때 만들어집니다.

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

한 번 iptables 규칙이 적용되어 패킷의 대상이 변경되면, Linux 커널 자체가 해당 패킷을 선택된 Pod로 전달합니다. 여기서 주요한 점은 한 번 연결이 설정되면, 해당 연결에 대한 모든 패킷이 iptables 규칙에 따라 Linux 커널에 의해 자동으로 선택된 Pod로 전달된다는 것입니다. 각 패킷을 처리하기 위해 kube-proxy가 필요하지 않으므로 iptables 모드가 사용자 공간 모드보다 더 효율적입니다.

위 시나리오를 위한 샘플 iptables 규칙:

```js
# 서비스 S로 전송되는 트래픽을 일치시키고 대상을 변경하는 규칙
iptables -t nat -A PREROUTING -p tcp -d 10.0.0.1 --dport 80 -j DNAT --to-destination 172.17.0.2:80
iptables -t nat -A PREROUTING -p tcp -d 10.0.0.1 --dport 80 -j DNAT --to-destination 172.17.0.3:80
iptables -t nat -A PREROUTING -p tcp -d 10.0.0.1 --dport 80 -j DNAT --to-destination 172.17.0.4:80

# Pod에서 나가는 트래픽을 노드 자체에서 생성된 것처럼 변형하는 규칙
iptables -t nat -A POSTROUTING -s 172.17.0.0/16 -j MASQUERADE
```

- 첫 번째 규칙 세트는 nat 테이블의 PREROUTING 체인에 있습니다. 이러한 규칙은 서비스 IP 10.0.0.1로 오는 트래픽과 포트 80으로 가는 트래픽과를 일치시키고 패킷의 대상을 백엔드 Pod 중 하나로 변경합니다.
- POSTROUTING 체인의 MASQUERADE 규칙은 Pod에서 나오는 패킷의 소스 IP를 노드의 IP로 변경하여 응답이 올바르게 노드로 라우팅되고 클라이언트로 다시 라우팅될 수 있도록 합니다.

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

# IPVS 모드

Kubernetes의 kube-proxy에서 IPVS 모드는 IP Virtual Server를 나타냅니다. 이 모드는 iptables나 사용자 공간 모드보다 고성능의 커널 수준 로드 밸런싱 및 더 정교한 로드 밸런싱 알고리즘을 제공합니다.

IPVS 모드에서 kube-proxy는 netfilter 후크를 사용하여 패킷을 캡처하고, 그런 다음 해당 패킷을 리눅스 커널의 IPVS 모듈에 전달합니다. IPVS 모듈은 IP 기반 로드 밸런싱을 수행하고 선택된 로드 밸런싱 알고리즘에 따라 패킷을 백엔드 Pod로 전달합니다. IPVS 로드 밸런싱 알고리즘에는 라운드로빈, 최소 연결, 가장 짧은 예상 지연 등이 포함될 수 있습니다.

![이미지](/assets/img/2024-05-20-K8skube-proxyIntroduction_2.png)

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

IPVS 모드는 대규모 서비스 규모에 적합하며 iptables보다 일관된 해싱을 제공합니다. 또한 더 나은 네트워크 처리량, 더 나은 프로그래밀 수 및 IP 주소 및 포트 이외의 요소에 기반한 부하 분산 기능을 제공합니다.

예를 들어 클러스터 IP가 10.0.0.1이고 백엔드 Pod A, B 및 C가 있는 S 서비스가 있다고 가정해 봅시다.

IPVS 모드에서:

- 클라이언트가 10.0.0.1에 연결하려고 할 때 연결 요청이 kube-proxy에 의해 잡힙니다.
- 각 노드에서 실행되는 kube-proxy는 Linux 커널의 IPVS 모듈을 사용하여 10.0.0.1로 오는 트래픽을 백엔드 Pod (A, B 또는 C) 중 하나로 보냅니다.
- IPVS 모듈은 서비스의 구성된 세션 어 피니티 및 부하 분산 알고리즘을 기반으로 백엔드 Pod를 선택합니다.
- IPVS 모듈이 패킷의 대상을 업데이트하면 Linux 커널 자체가 선택된 Pod로 패킷을 전달합니다.
  IPVS 모드는 고급 로드 밸런싱 기능을 제공하며 iptables 또는 사용자 공간 모드보다 더 많은 서비스 및 백엔드를 처리할 수 있습니다.

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

여기는 kube-proxy가 로드 밸런싱을 설정하는 데 사용할 수 있는 IPVS 명령어를 시뮬레이션한 것입니다:

```js
# IPVS 서비스 추가
ipvsadm -A -t 10.0.0.1:80 -s rr

# 백엔드 서버(팟) 추가
ipvsadm -a -t 10.0.0.1:80 -r 172.17.0.2:80 -m
ipvsadm -a -t 10.0.0.1:80 -r 172.17.0.3:80 -m
ipvsadm -a -t 10.0.0.1:80 -r 172.17.0.4:80 -m
```

-s rr 옵션은 스케줄링 방법을 라운드 로빈으로 설정합니다. -m 옵션은 포워딩 방법을 마스커레이팅으로 설정하며, 이는 SNAT과 유사합니다. 이러한 명령어로 규칙을 설정한 후, 다음과 같은 내용을 표시할 때 룰을 확인할 수 있습니다:

```js
# IPVS 규칙 표시
ipvsadm -L -n

IP Virtual Server 버전 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  10.0.0.1:80 rr
  -> 172.17.0.2:80                Masq    1      0          0
  -> 172.17.0.3:80                Masq    1      0          0
  -> 172.17.0.4:80                Masq    1      0          0
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

# KernelSpace Mode

**KernelSpace** 모드는 가장 최근에 추가된 기능이며 Windows 시스템에서만 사용할 수 있습니다. Kubernetes를 Windows에서 사용할 때 유저스페이스 모드에 대한 대체 방법을 제공합니다. 왜냐하면 iptables과 ipvs는 리눅스에 특화된 기능들이기 때문입니다.
