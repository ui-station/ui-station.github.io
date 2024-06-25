---
title: "Challenge 14 바운스 방법 이해 및 구현"
description: ""
coverImage: "/assets/img/2024-06-23-Challenge14bounce_0.png"
date: 2024-06-23 00:59
ogImage:
  url: /assets/img/2024-06-23-Challenge14bounce_0.png
tag: Tech
originalTitle: "Challenge 14: bounce"
link: "https://medium.com/@danielepolencic/challenge-14-bounce-2ba683214e7b"
---

쿠버네티스 서비스: 생각했던 대로 항상 작동하지 않을 수 있어요.

쿠버네티스에서는 서비스가 존재하지 않아요: IP 주소나 포트를 수신하는 프로세스가 없어요. 대신, 쿠버네티스는 고급 네트워크 규칙을 사용하여 로드 밸런서를 모방해요.

기본적으로, 이러한 네트워크 규칙은 Kube-proxy가 관리하는 iptables 규칙을 사용해요.

하지만, Cilium으로 작성된 eBPF를 사용하는 대안이 있어요.

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

"왜 "표준" 로드 밸런서를 사용하지 않는 거죠?

이것은 주로 단일 고장 지점의 문제를 극복하기 위해 설계되었습니다. 각 노드에 규칙이 기입되어 있기 때문에, 단일 구성 요소의 고장이 전체 클러스터에 영향을 미칠 가능성이 적습니다.

그러나 이것은 흥미로운(그리고 예상치 못한) 결과를 초래할 수 있습니다.

세 개의 노드와 두 개의 팟이 있는 다음 클러스터를 고려해보세요."

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

<img src="/assets/img/2024-06-23-Challenge14bounce_0.png" />

외부 트래픽에 파드를 노출하는 NodePort 서비스가 있습니다.

NodePort로 요청을 보내면, 해당 트래픽이 동일 노드의 파드 2에 도착할 확률은 얼마인가요?

- 트래픽은 항상 파드 2에 도착합니다
- 트래픽이 파드 2에 도착할 확률은 50% 입니다
- 트래픽이 파드 2에 도착할 확률은 33% 입니다
- 위의 어느 것도 아닙니다.

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

# 해결책

왜 그런지 이해하려면 Kube-proxy가 노드에 전달 규칙을 설정하는 방식을 살펴봐야 합니다 (예: iptables, ipvs, eBPF 등).

NodePort를 생성하면 클러스터의 각 노드가 30000~32767 범위의 포트를 노출합니다. 30000번 포트로 가정해 보겠습니다.

`노드 IP`:30000으로 curl 요청을 발행하면 무슨 일이 일어날까요?

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

IP 헤더의 대상 IP 주소는 Kubernetes에 서비스가 존재하지 않기 때문에 다시 작성됩니다(그리고 그들은 트래픽을 리디렉션하고 전달하는 규칙의 모음일 뿐입니다).

두 개의 파드 중 하나가 대상으로 선택되고, 트래픽이 해당 팟에 도달할 수 있습니다.

그래서 각 팟은 트래픽을 받을 확률이 50%입니다.

이는 클러스터에 있는 어느 노드에서 클러스터를 받았는지에 관계없이 발생합니다.

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

하지만, 토론할 가치가 있는 두 가지 흥미로운 경계 사례가 있습니다:

- 팟이 없는 노드를 만나면 여전히 트래픽이 팟 1 또는 팟 2로 리디렉션됩니다.
- 팟 2를 포함한 두 번째 노드를 만나면 트래픽이 여전히 다른 팟으로 이동할 수 있습니다.

이 두 경우 모두 현재 노드는 트래픽을 처리하지 않으며, 추가적인 홉이 발생합니다.

이것이 낭비라고 생각할 수 있지만, 이는 타협점입니다.

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

특정 테이블 태그를 마크다운 형식으로 변경하시면 추가 hop이 발생하여 포드 간에 우수한 인트라 클러스터 로드 밸런싱을 할 수 있거나 항상 서비스를 구성하여 현재 노드의 포드로 트래픽을 전달하도록 설정할 수 있습니다.

대기시간이 향상되지만 요청을 부분적으로 로드 밸런싱할 수 있기 때문에 다른 포드보다 연결 수가 더 많은 포드를 얻을 수도 있습니다.

이 내용이 마음에 드셨다면 아래 내용도 좋아하실 수 있습니다:

- Learnk8s에서 운영하는 Kubernetes 강좌.
- 매주 발행하는 Learn Kubernetes Weekly 뉴스레터.
- 20주 동안 게시한 20가지 Kubernetes 개념 시리즈.

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

# 링크

리소스 할당에 대해 더 자세히 알고 싶다면, 다음 자료를 꼭 확인해보세요:

- [kubernetes service에 핑을 보낼 수 없는 이유](https://medium.com/@danielepolencic/learn-why-you-cant-ping-a-kubernetes-service-dec88b55e1a3)
- [kubernetes 네트워크 패킷](https://learnk8s.io/kubernetes-network-packets)
