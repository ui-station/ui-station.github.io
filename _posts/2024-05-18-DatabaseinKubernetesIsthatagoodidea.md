---
title: "쿠버네티스 내에서의 데이터베이스 좋은 아이디어인가요"
description: ""
coverImage: "/assets/img/2024-05-18-DatabaseinKubernetesIsthatagoodidea_0.png"
date: 2024-05-18 16:37
ogImage:
  url: /assets/img/2024-05-18-DatabaseinKubernetesIsthatagoodidea_0.png
tag: Tech
originalTitle: "Database in Kubernetes: Is that a good idea?"
link: "https://medium.com/@fengruohang/database-in-kubernetes-is-that-a-good-idea-daf5775b5c1f"
---

Kubernetes/Docker에 데이터베이스를 저장해야 하는지 여전히 논란이 많습니다. Kubernetes(k8s)는 상태가 없는 응용 프로그램을 관리하는 데 뛰어나지만, 특히 PostgreSQL과 MySQL과 같은 데이터베이스와 같은 상태를 유지하는 서비스에서 기본적인 단점을 가지고 있습니다.

이전 글 "Docker에서 데이터베이스: 좋은 방법인가, 나쁜 방법인가"에서는 데이터베이스를 컨테이너화하는 장단점에 대해 논의했습니다. 오늘은 K8S에서 데이터베이스를 조율하는 데 어떤 희생을 해야 하는지 탐구하고, 이것이 현명한 결정이 아닌 이유에 대해 살펴보겠습니다.

# 개요

Kubernetes(k8s)는 복잡한 상태가 없는 응용 프로그램의 다양한 집합을 더 잘 관리하기 위해 고안된 우수한 컨테이너 조율 도구입니다. StatefulSet, PV, PVC 및 LocalhostPV와 같은 제공 기능에도 불구하고, 이러한 기능들은 여전히 높은 신뢰성을 요구하는 프로덕션 수준의 데이터베이스를 실행하기에는 충분하지 않습니다.

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

데이터베이스는 "소 동물"보다는 "가축"과 같이 주의 깊게 양육이 필요한 존재입니다. K8S에서 데이터베이스를 "가축"으로 취급한다면 외부 디스크/파일 시스템/스토리지 서비스를 새로운 "데이터베이스 애완동물"로 변신시킬 것입니다. 데이터베이스를 EBS/네트워크 스토리지에서 실행하면 신뢰성과 성능에서 상당한 단점이 발생합니다. 그러나 고성능 지역 NVMe 디스크를 사용하면 데이터베이스가 노드에 바인드되어 스케줄할 수 없어져서, K8S에 넣는 주요 목적을 무효화시킬 것입니다.

데이터베이스를 K8S에 배치하는 것은 "lose-lose(양쪽이 손해)" 상황으로 이어집니다 - K8S는 비상태성에서의 간단함을 손실하며, 순수한 비상태적 사용처럼 빠르게 재배치, 스케줄링, 파괴, 재구축을 할 유연성이 부족합니다. 반면, 데이터베이스는 신뢰성, 보안, 성능 및 복잡성 비용에 맞교환으로 제한된 "탄력성"과 활용도를 얻을 수 있지만 - 가상 머신들도 이를 달성할 수 있습니다. 공중 클라우드 업체 외의 사용자들에게는 단점이 혜택을 제대로 상쇄합니다.

K8S를 통해 나타나는 "클라우드 네이티브 열기"는 단지 K8S를 위해 K8S를 채택하는 왜곡된 현상이 되어버렸습니다. 엔지니어들은 대체할 수 없음을 증가시키기 위해 추가 복잡성을 더하고, 한편으로 경영자들은 산업에서 뒤처지는 것을 두려워하며 배포 경쟁에 휩쓸려갑니다. 자전거로 할 수 있는 작업에 탱크를 사용하여 자신을 증명하거나 경험을 쌓고자 하지만, 문제가 그러한 "용사 퇴치" 기술이 필요한지 고려하지 않으면, 이러한 종류의 구조적 호흡법은 결국 부정적인 결과로 이어질 것입니다.

네트워크 스토리지의 신뢰성과 성능이 지역 스토리지를 능가할 때까지, 데이터베이스를 K8S에 배치하는 것은 현명한 선택이 아닙니다. 데이터베이스 관리 복잡성을 해결할 수 있는 다른 방법들이 있습니다, RDS와 Pigsty와 같은 오픈 소스 RDS 솔루션을 통한 방법들이 있으며, 이들은 베어 메탈이나 베어 OS에 기반을 두고 있습니다. 사용자들은 자신의 상황과 요구에 근거하여 현명한 결정을 내려야 하며, 이를 위해 장단점을 신중하게 고려해야 합니다.

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

# 현상 유지

K8S는 상태가 없는 애플리케이션 서비스를 관리하는 데 뛰어나지만, 초기에는 상태가 있는 서비스에 제한이 있었습니다. 그러나 K8S와 도커가 의도한 용도가 아니었음에도 불구하고 커뮤니티의 확장 열망은 멈출 줄 모릅니다. 전도자들은 K8S를 차세대 클라우드 운영 체제로 묘사하며, 데이터베이스가 반드시 Kubernetes 내의 일반적인 응용 프로그램이 될 것이라 주장합니다. 상태가 있는 서비스를 지원하기 위해 다양한 추상화가 등장했습니다: StatefulSet, PV, PVC, LocalhostPV 등이 있습니다.

수많은 클라우드 네이티브 열정가들이 기존 데이터베이스를 K8S로 이전하려 시도했으며, 결과적으로 데이터베이스에 대한 CRD와 Operator가 증가하였습니다. PostgreSQL을 예로 들면 이미 PGO, StackGres, CloudNativePG, PostgresOperator, PerconaOperator, CYBERTEC-pg-operator, TemboOperator, Kubegres, KubeDB, KubeBlocks 등 다양한 K8S 배포 솔루션이 있습니다. CNCF 생태계가 빠르게 확장되어 복잡성의 놀이터가 되어가고 있습니다.

그러나 복잡성은 비용을 의미합니다. "비용 절감"이 주류가 되면서, 반성의 목소리가 나타나고 있습니다. 공중 클라우드에서 K8S를 깊이 활용한 DHH와 같은 Could-Exit 선구자들은 자체 호스팅 오픈소스 솔루션으로 전환 중에 과도한 복잡성으로 인해 K8S를 버리고 Docker와 Kamal이라는 Ruby 도구만을 대안으로 채택했습니다. 많은 사람들이 데이터베이스와 같은 상태가 있는 서비스가 Kubernetes에 적합한지 의심하기 시작했습니다.

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

K8S 자체는 상태 지향 애플리케이션을 지원하기 위해 노력하면서, 원래의 컨테이너 오케스트레이션 플랫폼으로부터 벗어나 zun. 쿠버네티스 공동 창립자 팀 호킨은 올해의 KubeCon에서 드문 우려를 표명했습니다. "K8s is Cannibalizing Itself!"라고 말하며 "쿠버네티스는 너무 복잡해졌으며, 자제력을 배워야 합니다. 그렇지 않으면 혁신을 멈추고 기초를 잃을 것입니다."

# 승리의 상실

클라우드 네이티브 분야에서 “애완동물”과 “가축”의 비유는 상태 지향 서비스를 설명하는 데 자주 사용됩니다. "애완동물"인 데이터베이스는 주의 깊고 개별적인 관리가 필요하지만, "가축"은 일회용이며 상태를 가지지 않는 애플리케이션을 나타냅니다(일회용성).

![이미지](/assets/img/2024-05-18-DatabaseinKubernetesIsthatagoodidea_0.png)

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

K8S의 주요 아키텍처 목표 중 하나는 가축으로 취급할 수 있는 것들을 가축으로 취급하는 것입니다. 데이터베이스에서 "저장소와 연산을 분리"하는 시도는 이 전략을 따릅니다: 상태를 가진 데이터베이스 서비스를 K8S 외부의 상태 저장소로 분리하고 K8S 내부에서 순수한 연산으로 분리하는 것입니다. 상태는 EBS/클라우드 디스크/분산 저장소 서비스에 저장되어 "무상태" 데이터베이스 부분이 K8S에서 자유롭게 생성, 삭제 및 예약될 수 있도록 합니다.

그러나 데이터베이스, 특히 OLTP 데이터베이스는 디스크 하드웨어에 심하게 의존하며, 네트워크 저장소의 신뢰성과 성능은 아직도 로컬 디스크의 성능을 크게 뒤쳐지고 있습니다. 따라서 K8S는 LocalhostPV 옵션을 제공하여 컨테이너가 데이터 볼륨을 호스트 운영 체제에 직접 사용할 수 있도록 하여 고성능/고신뢰성 로컬 NVMe 디스크 저장소를 활용합니다.

그러나 이것은 딜레마를 제시합니다: K8S의 스케줄링 및 오케스트레이션 능력을 위해 저품질 클라우드 디스크를 사용하고 데이터베이스의 신뢰성/성능을 용인해야할까요? 아니면 호스트 노드에 연결된 고성능 로컬 디스크를 사용하여 모든 유연한 스케줄링 능력을 사실상 상실해야할까요? 전자는 K8S의 작은 보트에 닻을 박아 전반적인 속도와 민첩성을 늦추는 것처럼 보이며, 후자는 배를 일정한 지점에 고정시키는 것과 같습니다.

상태가 없는 K8S 클러스터를 실행하는 것은 간단하고 신뢰할 수 있지만, 물리적 머신의 순수한 운영 체제에서 상태가 있는 데이터베이스를 실행하는 것도 마찬가지입니다. 그러나 둘을 섞으면 K8S는 상태가 없는 유연성과 비현실적인 스케줄링 능력을 잃게 되고 데이터베이스는 신뢰성, 보안, 효율성 및 단숨함과 같은 핵심 속성을 희생하게 되며 데이터베이스에 근본적으로 중요하지 않은 탄력성, 자원 이용 및 Day1 전달 속도를 얻게 됩니다.

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

## Pros and Cons

중대한 기술 결정을 내리기 위해서는 가장 중요한 측면이 장단점을 따져보는 것입니다. 여기서 "품질, 보안, 성능, 비용"의 순서로, 데이터베이스를 K8S에 배치하는 기술적 트레이드오프와 클래식한 베어 메탈/가상머신 배포와의 비교에 대해 토의해보겠습니다. 모든 것을 다 다루는 포괄적인 논문을 쓰고 싶지는 않습니다. 대신 특정 질문 몇 가지를 제시하여 고려하고 토론하려고 합니다.

품질

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

K8S는 물리적 배포와 비교할 때 추가적인 장애 지점과 아키텍처 복잡성을 도입하여 대폭 증가시키며, 장애의 평균 복구 시간을 상당히 연장시킬 수 있습니다. "Docker에 데이터베이스를 넣는 것이 좋은 아이디어일까?"라는 글에서, 신뢰성에 관한 논쟁을 제공했었는데, 이는 Kubernetes에도 적용될 수 있습니다. K8S와 Docker는 데이터베이스에 추가적이고 불필요한 의존성과 장애 지점을 도입하며, 커뮤니티 장애 지식 축적과 신뢰성 추적 기록 (MTTR/MTBF)이 부족합니다.

클라우드 공급업체 분류 시스템에서 K8S는 PaaS에 속하고, RDS는 보다 기본적인 IaaS 계층에 속합니다. 데이터베이스 서비스는 K8S보다 높은 신뢰성 요구를 가지고 있습니다. 예를 들어, 많은 기업의 클라우드 관리 플랫폼은 추가 CMDB 데이터베이스에 의존합니다. 이 데이터베이스는 어디에 위치해야 할까요? K8S가 의존하는 것을 관리하게 두면 안 되며, 불필요한 추가적인 의존성을 추가해서도 안 됩니다. 알리바바 클라우드의 세계적인 대형 장애와 디디의 K8S 아키텍처 조정 재해는 우리에게 이 교훈을 전해주었습니다. 게다가, 이미 외부에 있는 데이터베이스 시스템이 있는데, K8S 내에 별도의 데이터베이스 시스템을 유지하는 것은 더욱 정당화하기 어렵습니다.

보안

멀티 테넌트 환경에서의 데이터베이스는 추가적인 공격 표면을 도입하여 더 높은 위험과 더 복잡한 감사 준수 도전을 가져옵니다. K8S는 데이터베이스를 보다 안전하게 만들까요? K8S 아키텍처 조정의 복잡성이 K8S를 잘 모르는 스크립트 키디들을 막을 수도 있지만, 진짜 공격자들에게는 보다 많은 구성 요소와 의존성이 더 넓은 공격 표면을 의미할 때도 있습니다.

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

"BrokenSesame 알리바바 클라우드 PostgreSQL 취약점 기술 세부 정보”에서 보안 인원이 자체 PostgreSQL 컨테이너를 사용하여 K8S 호스트 노드로 이탈하고 K8S API 및 다른 유저들의 컨테이너와 데이터에 액세스했습니다. 이는 명백히 K8S에만 적용되는 문제입니다 — 리스크는 실재하며 이러한 공격이 발생한 바 있으며 심지어 로컬 클라우드 산업 선두주자인 알리바바 클라우드도 침투당했습니다.

![이미지](/assets/img/2024-05-18-DatabaseinKubernetesIsthatagoodidea_1.png)

성능

“도커에 데이터베이스를 넣으면 좋은 아이디어인가?”에서 설명한 대로, 추가적인 네트워크 부하, 인그레스 병목 현상 또는 성능이 저하된 클라우드 디스크는 모두 데이터베이스 성능에 부정적인 영향을 미칩니다. 예를 들어 “PostgreSQL@K8s 성능 최적화”에서 밝혀진 것처럼, K8S에서 데이터베이스 성능을 베어메탈과 거의 맞출 정도로 만들기 위해서는 상당한 기술 능력이 필요합니다."

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

![Database in Kubernetes](/assets/img/2024-05-18-DatabaseinKubernetesIsthatagoodidea_2.png)

효율성에 대한 또 다른 오해는 자원 활용입니다. 오프라인 분석 업무와 달리 중요한 온라인 OLTP 데이터베이스는 자원 활용을 증가시키려는 것이 아니라 시스템의 신뢰성과 사용자 경험을 향상시키기 위해 의도적으로 낮춰야 합니다. 많은 조각화된 비지니스가 있는 경우 PDB/공유 데이터베이스 클러스터를 통해 자원 활용률을 향상시킬 수 있습니다. K8S가 옹호하는 탄력성 효율성은 유일한 것이 아닙니다. — KVM/EC2도 이 문제를 효과적으로 해결할 수 있습니다.

비용 측면에서 K8S와 다양한 오퍼레이터는 데이터베이스 관리의 일부 복잡성을 캡슐화하여 DBA가 없는 팀들에게 매력적입니다. 그러나 데이터베이스 관리를 담당하는 데 사용될 때 줄어드는 복잡성은 K8S 자체를 사용함으로써 도입되는 복잡성과는 비교되지 않을 정도입니다. 예를 들어, 무작위 IP 주소 변화와 Pod 자동 재시작은 상태를 저장하지 않는 응용프로그램에는 큰 문제가 되지 않을 수 있지만 데이터베이스에게는 용납할 수 없습니다. 많은 기업이 이러한 동작을 피하기 위해 kubelet을 수정하려고 시도했으며, 결과적으로 더 많은 복잡성과 유지 보수 비용을 도입하게 됐습니다.

“비용 감소와 효율성 향상으로부터 비용 감소와 복잡성 최소화로”에서 언급한 대로, 지적 능력은 공간적으로 축적하기 어렵습니다. 데이터베이스에 문제가 발생하면 데이터베이스 전문가가 해결해야 하고, Kubernetes에 문제가 발생하면 K8S 전문가가 살펴봐야 합니다. 그러나 데이터베이스를 Kubernetes에 넣으면 복잡성이 결합되어 상태 공간이 급격히 증가하지만 개별 데이터베이스 전문가와 K8S 전문가의 지적 대역폭을 쌓기 어렵습니다. 문제를 해결하기 위해서는 이중 전문가가 필요하며, 이러한 전문가들은 명백히 순수한 데이터베이스 전문가보다 훨씬 더 드물고 비쌉니다. 이러한 건축적인 연출은 대다수의 팀, 상위 퍼블릭 클라우드/대기업을 포함해 대규모 장애가 발생할 경우 주요 진전의 원인이 될 수 있습니다.

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

# 클라우드 네이티브 열풍

흥미로운 질문이 제기됩니다: K8S가 상태 유지 데이터베이스에 적합하지 않다면, 왜 많은 기업들, 대기업 포함, 이에 급급한 걸까요? 그 이유는 기술적인 측면이 아닙니다.

Google이 내부 Borg 우주선을 본따 만든 K8S 전함을 오픈소스로 공개했고, 뒤처지지 않으려는 매니저들이 Google과 동등한 위치에 있을 것이라 생각하여 K8S를 채택하기에 급급했습니다. 그런데 Google 자체는 K8S를 사용하지 않고 있어, AWS를 방해하려고하거나 산업을 오도하기 위한 것이 더 가능성이 높습니다. 그러나 대부분의 기업은 Google과 같은 인력을 운영하기에 훨씬 단순한 방식이 필요할 겁니다. MySQL + PHP, PostgreSQL + Go/Python을 베어 메탈에서 실행하는 것만으로도 이미 많은 기업들이 IPO로 나아가는 길을 걷게 되었습니다.

현대의 하드웨어 환경에서 대부분의 애플리케이션의 복잡성은 전체 수명 주기에 걸쳐 K8S를 사용할 정당성이 부족합니다. 그럼에도 불구하고 K8S를 상징으로 하는 "클라우드 네이티브" 열풍은 왜곡된 현상이 되었습니다: 단지 K8S를 사용하기 위해 K8S를 채택한다는 것. 일부 엔지니어들은 자신의 개인 목표를 달성하기 위해 대기업에서 사용하는 "첨단"이고 "멋진" 기술을 찾아내며, 직장을 옮길 수 있는 기회 혹은 승진을 위해 직장 안정성을 확보하기 위해 복잡성을 더하는 경향이 있습니다. 이 때 그들의 문제를 해결하기 위해 이러한 "용감한 용사" 기술이 꼭 필요한지를 고려하지 않습니다.

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

클라우드 네이티브 랜드스케이프는 화려한 프로젝트로 가득합니다. 매번 새로운 개발 팀은 무언가를 도입하고 싶어합니다: 오늘은 헬름, 내일은 쿠벨라. 밝은 미래와 최고의 효율에 대해 크게 이야기하지만, 실제로는 건축적 복잡성의 산과 "YAML 아이들"의 놀이터를 만들어냅니다. 최신 기술을 만지작거리고, 개념을 발명하며, 사용자들이 복잡성과 유지보수 비용을 감당하는 대가로 경험과 평판을 쌓는 말이지요.

![이미지](/assets/img/2024-05-18-DatabaseinKubernetesIsthatagoodidea_3.png)

클라우드 네이티브 운동의 철학은 매력적입니다 — 모든 사용자를 위해 퍼블릭 클라우드의 탄탄한 예약 기능을 민주화하기 위해서. K8S는 실제로 상태가 없는 애플리케이션에서 뛰어납니다. 그러나 지나친 열정이 K8S를 원래의 의도와 방향에서 벗어나 상태가 있는 애플리케이션을 오케스트레이팅에서 잘못된 결정으로 버거운 상태로 만듭니다.

# 현명한 결정을 내리는 것

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

여러 해 전에 K8S를 처음 만났을 때 나도 열정적이었어요. 그곳은 TanTan이었죠. 2만 개가 넘는 코어와 수백 개의 데이터베이스 클러스터가 있었고, 데이터베이스를 Kubernetes에 넣어보고 모든 가능한 Operator를 테스트해보려고 했어요. 그러나 2-3년의 광범위한 연구와 설계 노력 끝에 제가 진정되었고 그 광기를 버렸어요. 대신에 저희 데이터베이스 서비스를 베어 메탈/운영 체제를 기반으로 설계했죠. 우리에게 K8S가 데이터베이스에 제공한 혜택은 문제와 귀찮음에 비해하다는 것을 깨달았거든요.

데이터베이스를 K8S에 넣어야 할까요? 이것은 상황에 따라 다릅니다. 과도한 자원 판매를 기반으로 하는 공개 클라우드 업체의 경우, 확장성과 이용률이 중요할 것이며, 이는 수익과 이윤과 직접적으로 연결됩니다. 신뢰성과 성능은 후순위일 수 있습니다. 그래도 99.9% 이하의 가용성은 매달 25%의 신용을 보상해야 합니다. 그러나 대부분의 사용자, 우리를 포함하여, 이러한 절충안은 다릅니다. 일회성 Day1 설정, 확장성, 자원 사용률은 주요 관심사가 아닙니다. 신뢰성, 성능, Day2 운영 비용은 이러한 데이터베이스의 핵심 속성 중 가장 중요합니다.

우리는 데이터베이스 서비스 아키텍처를 오픈 소스로 공개했어요. PostgreSQL 배포 및 로컬 기반 RDS 대안인 Pigsty입니다. 우리는 K8S 및 Docker의 "한 번 빌드하고 어디서든 실행" 접근 방식 대신 다른 OS 배포판 및 주요 버전에 적응했고, Ansible을 사용하여 K8S CRD IaC와 유사한 API를 통해 관리 복잡성을 해결했어요. 이것은 수고스러운 작업이었지만 올바른 선택이었어요. PostgreSQL을 K8S에 넣는 서투른 시도는 세상이 필요로 하는 것이 아니에요. 그럼에도, 하드웨어 성능과 신뢰성을 극대화한 프로덕션 데이터베이스 서비스 아키텍처가 필요합니다.

![Database in Kubernetes](/assets/img/2024-05-18-DatabaseinKubernetesIsthatagoodidea_4.png)

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

어쩌면 언젠가는 분산 네트워크 스토리지의 신뢰성과 성능이 로컬 스토리지를 넘어서며 주류 데이터베이스가 스토리지-연산 분리를 기본적으로 지원할 때, 상황이 다시 바뀔 수도 있습니다 — K8S가 데이터베이스에 적합해질 수도 있겠죠. 하지만 현재로서는, 제 생각으로는 심각한 프로덕션 OLTP 데이터베이스를 K8S에 배치하는 것은 미숙하고 부적절하다고 믿습니다. 독자 여러분이 이 문제에 대해 현명한 선택을 하시기를 바랍니다.

# 참고

도커에서의 데이터베이스: 좋은 생각인가요?

"Kubernetes Founder Speaks Out! K8s Facing a Backlash!"

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

"도커의 저주: 최고의 해결책이라 생각했지만 결국 '죄악'인가?"

"디디의 장애로 배우는 것"

"Kubernetes에서 PostgreSQL 성능 최적화 기억"

"Kubernetes에서 데이터베이스 실행하기"

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

컴퓨터 하드웨어의 혜택을 다시 누리세요.

비용을 낮추고 효율을 높이는 방법을 찾아보세요.

컴퓨터 하드웨어의 혜택을 다시 누리세요.

우리는 알리바바 클라우드의 역사적 장애로 무엇을 배울 수 있을까요?

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

클라우드 컴퓨팅을 포기할 시간인가요?

클라우드 SLA는 위약물인가요?
