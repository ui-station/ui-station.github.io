---
title: "Kubernetes 클러스터에서 노드 회전 자동화 머신 이미지 업데이트 최적화하기"
description: ""
coverImage: "/assets/img/2024-06-19-StreamliningMachineImageUpdatesAutomatingNodeRotationinKubernetesClusters_0.png"
date: 2024-06-19 13:13
ogImage:
  url: /assets/img/2024-06-19-StreamliningMachineImageUpdatesAutomatingNodeRotationinKubernetesClusters_0.png
tag: Tech
originalTitle: "Streamlining Machine Image Updates: Automating Node Rotation in Kubernetes Clusters"
link: "https://medium.com/sage-ai/ami-update-solution-a0c4bfcf41a0"
---

<img src="/assets/img/2024-06-19-StreamliningMachineImageUpdatesAutomatingNodeRotationinKubernetesClusters_0.png" />

# 문제

Sage AI 인프라 팀은 모든 환경에서 우리의 서비스 및 시스템의 유지 보수와 안정성을 담당합니다. 때로는 현재 머신 이미지에 보안 취약점이 발견된 경우와 같이 상대적으로 짧은 시간 내에 Kubernetes 클러스터의 노드를 새로운 머신 이미지로 업데이트해야 할 때도 있습니다. 우리는 Amazon 클라우드를 사용하기 때문에 작업하는 것은 Amazon Machine Images (AMIs)이지만, 문제와 해결책은 어떤 클라우드 환경의 Kubernetes 클러스터에도 적용 가능합니다.

우리의 경우, 새로운 AMI ID를 적용할 것이지만, 그런 다음 노드가 새 AMI를 적용할 수 있도록 시간 내에 회전되도록 보장해야 합니다.

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

과거에는 오래된 AMI를 감지하여 해당 노드를 드레인하고 있는 사용자 정의 쉘 스크립트를 사용했습니다(해당 노드에서 실행 중인 파드는 제어된 방식으로 종료되어야 하며, 해당 서비스에 중단이 발생하지 않도록해야 합니다). 이 방법을 사용하면 몇 가지 단점이 있었습니다. 첫째, 누군가가 수동으로 스크립트를 호출하고 모니터링해야 했습니다. 둘째, 클러스터에 액세스하기 위해 인증 프록시를 사용하고 있으며, 해당 프록시가 있는 노드를 드레인하면 스크립트가 서버 연결을 잃고 오류가 발생할 수 있습니다. 때로는 이러한 일이 연이어 발생할 수도 있었습니다. 당연히 노드를 회전시키는 작업은 해당 작업을 담당하는 엔지니어에게 상당한 시간, 주의 및 수동 노력이 필요했습니다.

# 솔루션 설계

우리는 클러스터에서 AMI를 업데이트하는 엔지니어들에게 주는 부담을 크게 줄일 수 있는 솔루션을 찾기로 결정했습니다. 오래된 AMI가 있는 노드를 자동으로 회전시킬 수 있는 도구가 필요했다는 것을 알았지만, 이 요구 사항은 넓은 범위를 요구합니다. 조금 더 세부적으로 이를 분석해보면 프로젝트에 대한 몇 가지 요구 사항이 있었습니다;

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

- 우리는 쉽게 운영할 수 있는 도구를 원했습니다. 가장 이상적인 경우에는 감독이 필요하지 않은 것이 좋습니다. 엔지니어의 시간은 귀중하며 딱딱한 도구를 감시하는 데 그 시간을 낭비하는 것은 별 의미가 없습니다.
- 필요한 경우에는 간편하게 유지 및 업데이트할 수 있는 도구를 원했습니다. 우리는 많은 서드파티 도구를 사용하고 주기적으로 모두를 업그레이드해야 할 때가 있습니다. 그 중 일부는 다른 것들보다 업데이트하기가 훨씬 어려웠습니다.
- 우리는 쿠버네티스 클러스터에서 실행 중인 워크로드에 대한 설정한 모니터링 및 가시성 프로세스를 사용할 수 있는 솔루션을 원했습니다.

## 우리의 솔루션

우리는 시스템을 두 단계로 작동하도록 설계하기로 결정했습니다. 교체해야 할 노드를 확인하고 해당 노드의 워크로드를 소진하는 것입니다.

간단히 말해서, 노드를 소진한다는 것은 쿠버네티스가 해당 노드에 대해 어떠한 새로운 워크로드도 시도하지 않도록 태그를 달아놓고, 해당 노드의 프로세스와 상태가 해체되고, 새로운 노드가 대신 생성될 때까지 대기하는 것을 의미합니다.

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

이 작업을 하는 데 몇 가지 이유가 있었습니다.

- 이를 통해 두 가지 구성 요소를 독립적으로 발전 및 유지할 수 있습니다.
- 구성 요소에는 각각 다른 수명 주기 기대치가 있으며, 노드 선택 구성 요소는 선택 기준 또는 기본 인프라 변경에 따라 변경될 필요가 있습니다. 반면, 노드를 비우는 구성 요소는 비교적 안정적인 상태로 유지됩니다.
- 노드 선택 구성 요소는 주로 AWS API와 상호 작용하고, 노드 비우기는 Kubernetes 컨트롤러입니다.

두 구성 요소가 별도로 개발되는 또 다른 이유는 필요에 따라 서로 다른 언어로 작성되어 있으며, 선택 노드를 선택하는 구성 요소는 Bash 및 AWS CLI와 같은 성숙한 도구 및 jq와 같은 JSON 조작 도구 사용의 용이성과 간결성이 더 높을 것입니다. 반면에 우리는 솔루션을 Go 생태계에서 구현하기로 선택했습니다. 왜냐하면 Kubernetes 컨트롤러를 작성하는 데 라이브러리 및 문서 지원이 많기 때문에 노드를 비우는 구성 요소를 Go로 작성하는 것이 유리했기 때문입니다.

다음으로, 부분 간 통신 방식을 결정해야 했습니다. 일반적으로 노드에 작업을 표시하는 방법은 레이블을 지정하는 것입니다. 그러나 이 경우에는 taint를 사용하기로 결정했습니다. 장점은 taint를 한 번 설정하면 해당 노드에는 pod를 실행할 수 없으며, 추방된 pod가 해당 노드에 실행되는 것을 방지합니다. 따라서 노드 선택 작업은 노드에 taint를 설정하고, 비우기 작업은 노드 선택 작업에 의해 tainted된 노드를 식별하고 해당 pod를 비웁니다. 그런 다음 비워진 노드의 후속 종료는 클러스터 자동 확장기에 의해 처리됩니다.

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

# 거부된 방식

AWS Lambda를 기반으로 한 노드 드레이너 구현체가 여러 가지 있습니다(예: https://github.com/aws-samples/amazon-k8s-node-drainer) 하지만, 우리는 클러스터 내에서 실행되도록 설계된 도구가 필요했습니다. 이는 이미 우리가 갖고 있는 모니터링 기능을 사용하기 위함입니다.

AWS Auto Scaling 그룹 노드 새로 고침 프로세스를 기반으로 한 노드 드레이닝을 구현한 프로젝트들이 이미 존재하며, https://github.com/rebuy-de/node-drainer가 대표적인 예입니다. 이 프로젝트를 고려해 보았지만, 해당 프로젝트는 2023년 3월 이후에 어떠한 업데이트도 릴리스하지 않았기 때문에 채택하는 것이 리스크가 될 수 있습니다. 만약 이 프로젝트나 관련 종속성에 심각한 취약점이 발견된다면, 업데이트된 이미지를 얻을 수 없게 될 수 있으며, 우리 자신의 포크를 유지보수해야 할 수도 있습니다.

# 구현

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

해결 방법의 두 번째 부분은 nodes를 감시하고 지정된 draining taint가 설정되면 node의 포드를 제거하기 시작하는 Kubernetes 컨트롤러로 구현되었습니다. 포드가 떠날 때까지 노드를 제거하는 작업은 타임 아웃 될 때까지 계속 됩니다 (가끔 포드가 떠나고 싶어하지 않을 수도 있어요) 또는 타임아웃 시간이 경과할 때까지입니다. 일정 시간이 지나면 그 노드를 다시 시도합니다. 프로그램은 항상 노드 제거를 직렬화하고, 노드를 처리한 후 클러스터에 보류 중인 포드가 없을 때까지 기다린 후 계속합니다. 이는 클러스터의 용량 문제를 최소화하기 위해 수행됩니다 - 모든 포드가 제거를 위해 tainted되어 있으면 새로운 노드가 생성될 때까지 새로운 포드를 시작할 수 없습니다.

![이미지](/assets/img/2024-06-19-StreamliningMachineImageUpdatesAutomatingNodeRotationinKubernetesClusters_1.png)

# 첫 번째 오픈 소스 기여물로 발표 결정

Sage AI 팀은 우리가 내부적으로 개발한 프로젝트들을 확인하여 일반 커뮤니티에 혜택을 줄 수 있다고 판단한 프로젝트들을 식별하기로 결정했습니다. 우리 인프라 팀은 MLOps 분야에서 많은 훌륭한 작업을 수행했고, 위에서 설명한 워크플로우의 자동화가 오픈 소스 커뮤니티에 공개되었다는 것을 자랑스럽게 발표합니다!

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

우리의 방식은 내부에서 이미 1년 넘게 사용되어 왔고, 이제 코드는 여기에서 찾을 수 있습니다; https://github.com/sageailabs/ektopistis .

관심이 있으시면 자유롭게 살펴보고, 유용하다고 판단되면 귀하의 환경에서 사용하거나 피드백이나 코드로 기여해 주시기 바랍니다!

프로젝트 README의 설치 섹션의 지침을 따라 Helm을 사용하여 클러스터에 설치할 수 있습니다. 노드 선택 구성 요소의 의미론은 임의적이며, 원하는 선택 기준과 태깅 의미론에 기반하여 구성할 수 있습니다. 예를 들어, AWS 오토 스케일링 그룹에서 시작된 모든 클러스터 노드를 표시하고 설정이 ASG의 론칭 템플릿과 일치하지 않는 스크립트가 있습니다.

우리는 우리의 도구를 공유함으로써 더 나은 해결책으로 이어질 것이라고 믿습니다. 귀하의 피드백과 기여를 기대하며, 즐거운 협업을 기대하고 있습니다.

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

# 계획된 미래 작업에 대한 참고사항

저희 노드 드레이너 구현은 중요한 작업 부하의 업데이트를 방해하지 않기 위해 클러스터에 보류 중인 파드가 있을 때 작업을 중지합니다. 그러나 이에는 중요한 하다고 할 수 있는 단점이 있습니다. 클러스터가 클수록 활동이 많을수록 파드의 순환율이 높아집니다. 이로 인해 보류 중인 파드가 계속 발생하여 노드 드레이너의 현재 버전이 멈춰있는 장기간이 발생할 수 있습니다. 저희는 이 대기 정책을 개선하여 프로세스를 가속화하는 계획이 있습니다.
