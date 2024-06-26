---
title: "어렵게 배운 교훈 Cilium의 기본 Pod CIDR을 사용하지 마세요"
description: ""
coverImage: "/assets/img/2024-06-19-LearneditthehardwayDontuseCiliumsdefaultPodCIDR_0.png"
date: 2024-06-19 13:03
ogImage:
  url: /assets/img/2024-06-19-LearneditthehardwayDontuseCiliumsdefaultPodCIDR_0.png
tag: Tech
originalTitle: "Learned it the hard way: Don’t use Cilium’s default Pod CIDR"
link: "https://medium.com/@isalapiyarisi/learned-it-the-hard-way-dont-use-cilium-s-default-pod-cidr-89a78d6df098"
---

전 eBPF에 상당히 오랫동안 관여해 왔습니다. 우리 팀장이 Azure CNI에서 Cilium CNI로 모든 클러스터를 라이브 이전하는 것을 제안했을 때, 기회에 바로 뛰어들었어요. 이 일은 내가 지금까지 맡았던 가장 힘든 일 중 하나였지만, 그 시간 동안 즐거웠어요.

하지만, 그 이야기는 다음에 하기로 해요. 이 기사의 목표는 제 개인적인 k8s.af 이야기 중 하나를 해설하여, 머리카락을 몇 일 절약할 수 있는 누군가를 돕는 것입니다.

![이미지](/assets/img/2024-06-19-LearneditthehardwayDontuseCiliumsdefaultPodCIDR_0.png)

## 왜 Cilium을 선택했는가?

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

Cilium을 선택한 우리의 주요 목표는 다음과 같습니다:

- 더 나은 네트워크 격리: 일부 클러스터에서는 고객의 작업 부하를 실행하고 있기 때문에 출발 트래픽을 효과적으로 공유하고 제어해야 했습니다.
- WireGuard를 사용한 투명한 암호화: 공유 클러스터에서는 제로 트러스트 접근 방식을 채택하고자 했습니다.
- 관찰 가능성: Cilium은 다양한 관찰 기능을 갖추고 있어 추가 계측없이 Kubernetes 작업 부하를 모니터링할 수 있습니다.
- 서비스 메시 기능: Cilium은 사이드카를 필요로하지 않고 다시 시도 및 회로 차단과 같은 서비스 메쉬 기능을 제공할 수 있습니다.
- 효율적이고 가벼운 네트워크 스택: 하드웨어 비용을 낮추면서 더 나은 성능을 원하신다면 저희를 선택해 주세요!
- 클러스터 매시 망: 우리는 인프라를 미래에 대비하기 위해 준비하고 싶었습니다.

이주 후 모든 것이 원활했습니다 (대부분...). Cilium을 기반으로 개발한 기능을 출시하기 시작했고 고객들로부터 좋은 피드백을 받았습니다.

그리고 모든게 좋았는데...

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

# 사건

일반적인 월요일 아침이었고, 매일 릴리스 주기의 일환으로 SRE 팀은 코어 서비스의 최신 업데이트를 스테이징 환경으로 프로모션했습니다.

그러나 프로모션 파이프라인이 완료되자마자 우리의 업타임 모니터링 솔루션이 스테이징 환경에 접근할 수 없다는 이벤트를 트리거하면서 발생했습니다. SRE 팀은 즉시 문제의 근본 원인을 조사하기 시작했습니다.

더 깊이 파고들기 전에, 빠른 다이어그램을 사용하여 우리의 네트워크 아키텍처(간소화된 버전)를 설명해 드리겠습니다.

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

<img src="/assets/img/2024-06-19-LearneditthehardwayDontuseCiliumsdefaultPodCIDR_1.png" />

두 개의 클러스터가 있습니다. 하나는 외부에 공개되어 있고, 다른 하나는 비공개입니다. 외부 클러스터는 방화벽을 통해 노출되어 있습니다. 비공개 클러스터의 일부 서비스는 로드 밸런서를 통해 외부 클러스터와 통신합니다.

초기 디버깅 후, SRE 팀은 문제가 방화벽과 클러스터 1의 인그레스 서비스 간의 연결에 있는 것으로 결론 내렸습니다. 클러스터 1 내의 모든 서비스가 실행되고 클러스터 1의 로드 밸런서를 향해 요청을 보내고 있기 때문에 클러스터 2의 pod들이 작동 중이었습니다.

Wireshark와 Hubble을 통해, 방화벽에서 전송된 "SYN" 패킷이 서비스에 도달했지만 서비스로부터 해당하는 "ACK" 패킷이 전송되지 않았음을 확인했습니다.

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

문제를 해결하기 위한 몇 차례의 무산된 시도 끝에 SRE 팀은 프로덕션으로의 릴리스를 승인했습니다. 중요한 수정 사항을 가능한 빨리 릴리즈해야 했기 때문입니다. 이 결정의 근거는 애플리케이션 수준의 변경이 인프라를 손상시킬 수 없으며, 이 일은 격리된 사건이었습니다.

그러나 릴리스가 프로덕션으로 승급되자마자 프로덕션 로드 밸런서도 응답을 중단했습니다. SRE 팀은 신속하게 변경 사항을 롤백하여 프로덕션에서 문제를 해결했습니다. 놀랍게도 스테이징 클러스터에서 변경 사항을 롤백해도 문제가 해결되지 않았습니다.

## 재앙이 계속됩니다

스테이징에서의 문제가 여전히 해결되지 않아 전문 네트워크 전문가와 함께 전투실에 호출되었습니다.

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

지금쯤 SRE 팀은 광범위한 실험을 통해 많은 데이터를 수집했습니다. 우리 VPC 내의 다른 서브넷에 VM을 배포하는 실험을 해보았는데, 로컬 노드 풀 서브넷부터 다른 클러스터에 속한 원격 서브넷, 로드 밸런서 서브넷까지 다양한 곳에 시도해봤습니다. 모든 것이 예상대로 작동되었는데, 방화벽을 통해 통과하는 트래픽에서 문제가 발생했습니다.

제가 노력에 합류하면서, 그들이 수집한 모든 데이터를 철저히 검토했고, 여러 종류의 워크로드를 배포하고 Hubble과 Wireshark 로그를 분석하며 더 많은 테스트를 진행했습니다. 루트 원인을 밝힐 수 있는 단서나 누락된 부분을 찾기 위해 노력했습니다.

Azure 네트워크 엔지니어가 합류하면서, SRE 팀은 그들이 지금까지 한 모든 단계에 대해 설명했습니다. 수집한 데이터를 분석한 후, 엔지니어는 방화벽 서브넷 내에 VM을 배포하고 문제가 있는 Kubernetes 클러스터로 TCP 연결을 시도하라는 제안을 했습니다.

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

저희 SRE 팀은 방화벽 서브넷 내에 임시 VM을 빠르게 설정하고 로드 밸런서 IP로 telnet을 시도해 보았어요. 이 테스트 중에 우리가 직면한 동일한 문제를 관찰했는데, telnet 연결이 초기화되지 않았어요. 그래서 그들은 방화벽 서브넷과 로드 밸런서 서브넷 또는 노드 풀 서브넷 간 네트워크 피어링에 문제가 있는지 조사하기로 결정했어요.

호기심에 저는 다른 SRE 멤버에게 서버에 다시 ping을 시도하도록 요청하고 Hubble 로그를 모니터링했어요. 놀랍게도, telnet이 시작된 순간에 SYN 패킷이 수신되었지만 "ACK"은 전달되지 않았어요.

이 행동은 이상하게 보였기 때문에 임시 VM에서 포트를 열도록 요청하고 쿠버네티스 클러스터에서 해당 VM으로 ping을 시도해 보았어요. 하지만 응답이 없었고, 임시 VM에 Wireshark를 설치한 후에도 들어오는 SYN 패킷을 볼 수 없었어요. 흥미로운 점은 방화벽 서브넷을 제외한 다른 목적지로 ping을 시도하면 문제가 없었다는 점이었어요.

의아해하며, 그들에게 노드 풀 서브넷에 생성한 임시 VM을 사용하여 동일한 테스트를 수행해 보라고 요청했더니, 드디어 작동했어요.

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

# 루트 원인

이러한 동작이 이상하다고 생각해, 주요 사고 대응 팀 구성원들의 작업을 방해하고 있는 피어링을 점검 중이었던 멤버들에게 얘기했어요. 문제가 인그레스가 아니라 이그레스에 문제가 있을 수 있다는 것을 알려줬죠. 이로써 AKS-관리 노드 내의 라우팅 테이블 문제일 수도 있다는 느낌을 받았어요.

그래서 저희 SRE 팀은 쿠버네티스 클러스터에서 노드 중 하나로 SSH를 통해 연결하고 `ip route` 명령을 실행했어요. 이 명령을 실행하면 Cilium이 교차 노드 통신을 가능하게하기 위해 추가한 몇 가지 라우팅 규칙이 표시됐어요.

![Image](/assets/img/2024-06-19-LearneditthehardwayDontuseCiliumsdefaultPodCIDR_3.png)

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

## Cilium 노드 라우팅

고수준 개요에서 Cilium을 클러스터 범위 IPAM 모드에서 실행할 때, Cilium에게 가상 IP를 파드에 할당하도록 지시하기 위해 CIDR 범위를 제공해야 합니다. 기본적으로 이 CIDR 범위는 10.0.0.0/8입니다.

새 노드가 클러스터에 가입하면, Cilium은 해당 노드에게 주어진 CIDR 블록에서 고유한 서브넷을 할당합니다. 노드의 모든 파드는 이 할당된 서브넷 범위에서 IP 주소를 받습니다.

예를 들어, CIDR 범위인 10.0.0.0/8을 사용한다면:

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

- 노드 A는 서브넷 10.1.0.0/16을 받을 수 있습니다.
- 노드 B는 서브넷 10.4.0.0/16을 받을 수 있습니다.

노드 간 통신을 원활하게 하기 위해 Cilium은 IP 경로를 설정하여 트래픽이 노드 간에 올바르게 전달되도록 합니다. 노드 A에 있는 IP가 10.1.5.13인 팟이 노드 B에 있는 IP가 10.4.63.38인 팟과 통신하려고 할 때, 데이터 패킷은 노드 A의 네트워크 인터페이스로 전송됩니다. 그 후 IP 라우팅 테이블을 기반으로 패킷은 노드 B로 라우팅되며, 이는 노드 B가 10.4.0.0/16 서브넷을 소유하기 때문입니다.

## 잠자는 용

보통은 정상적으로 작동합니다. 그러나 유감스럽게도, 노드에 할당된 서브넷 중 하나가 방화벽의 서브넷 범위와 겹치는 문제가 발생했습니다. 이로 인해 SYN 패킷이 팟에 성공적으로 도달하지만, 팟이 응답을 시도할 때 요청이 노드의 네트워크 인터페이스로 전달되는 상황이 발생했습니다.

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

그 때, 노드의 IP 라우팅 규칙 때문에 패킷이 다른 노드로 라우팅되었습니다. 이는 방화벽의 VM IP 주소가 두 번째 노드의 서브넷 범위 내에 속했기 때문에 발생했습니다. 그러나 두 번째 노드에는 방화벽의 VM과 정확히 일치하는 IP 주소를 가진 pod가 없었기 때문에 패킷이 소멸하여 사라졌습니다.

이 가설을 확인하기 위해 SRE 팀은 충돌하는 노드에 대해 `kubectl delete node`을 실행했고, 그 노드가 제거되자마자 방화벽을 통한 외부 연결이 다시 작동하기 시작했습니다.

하지만 왜 Cilium을 배포한 후 거의 8개월이 지난 후에 이 문제가 발생했을까요? 이 모든 것은 자동 스케일링으로 귀결되었습니다. 관찰한 바에 따르면, 특정 CIDR 범위가 노드에 할당되면 해당 노드가 제거되더라도 재사용되지 않았습니다. 따라서 Cilium 운영자는 우리가 할당한 대규모 CIDR 범위를 하나씩 소비하면서 점진적으로 이동하고 있었고, 마침내 방화벽의 CIDR 범위에 다다랐습니다.

그 판명날인 첫 월요일 아침, SRE 팀이 개발 환경에서 스테이징으로 릴리스를 승격시키자마자 노드 스케일업을 트리거하여 충돌하는 CIDR 범위가 있는 노드가 생성되었고, 이로 인해 전체 통신 경로가 다운되었습니다.

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

# 문제 수정

스테이징에서 발생한 이슈를 해결하기 위해, 우리는 clusterPoolIPv4PodCIDRList를 기존 내부 서브넷과 충돌하지 않는 CIDR 범위로 업데이트해 보았습니다. 헬름 업그레이드를 실행한 후에도 아무 변화가 없었습니다. 그래서 노드 스케일 업을 트리거하고 다행히도 - 새로 생성된 노드가 새로운 CIDR 범위의 서브넷으로 생성되었습니다.

저는 두 개의 DaemonSet을 사용하여 교차 노드 통신을 테스트하기 위해 만든 빠른 워크로드를 실행하여 두 개의 CIDR 범위가 아무 문제없이 작동하는 것을 확인했습니다. 그 후, SRE 팀은 기존 노드를 안전하게 비우고 제거하고 나쁜 CIDR 범위를 가진 모든 노드가 완전히 제거될 때까지 새로운 노드 풀을 확장하는 스크립트를 신속하게 작성했습니다. 그 스크립트를 실행하여 스테이징 클러스터를 완전히 복구했습니다.

추가 테스트를 실행한 후에 우리의 프로덕션 클러스터도 동일한 문제를 겪었기 때문에, Cilium 문서에서 반대하고 있던 것에도 불구하고 clusterPoolIPv4PodCIDRList를 업데이트하여 문제를 영구적으로 해결하기로 결정했습니다. 이에 이해 관계자들로부터 동의를 받고 마이그레이션을 실행했습니다.

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

# 결론

다양한 테스트를 거친 후에도, 거의 2000개의 구성 가능한 값이 있는 Cilium과 같은 복잡한 시스템은 여전히 잘못된 구성을 빠뜨릴 수 있어 예상치 못한 실패로 이어질 수 있습니다.

이 사건을 통해, 네트워크 문제를 체계적으로 해결하고 클라우드 추상화에 의해 제공된 낮은 수준의 네트워킹 인프라와 기술을 이해하는 중요성을 깨달았습니다. 이 문제에 대해 협업한 후, 매우 어려운 경험이었지만 소중한 학습 기회를 제공했다는 것에 대해 모두 동의했습니다.
