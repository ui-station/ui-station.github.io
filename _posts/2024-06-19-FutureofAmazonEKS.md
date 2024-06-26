---
title: "아마존 EKS의 미래"
description: ""
coverImage: "/assets/img/2024-06-19-FutureofAmazonEKS_0.png"
date: 2024-06-19 13:10
ogImage:
  url: /assets/img/2024-06-19-FutureofAmazonEKS_0.png
tag: Tech
originalTitle: "Future of Amazon EKS"
link: "https://medium.com/it-newbies-note/future-of-amazon-eks-f32abd083729"
---

AWS re:Invent 2023 세션 중 "Amazon EKS의 미래 (CON203)"를 Nathan Taber, AWS의 Kubernetes 제품 총괄의 프레젠테이션을 시청해야 했어요. 이 기사에서는 논의된 주요 포인트들을 요약하겠으며, 이해를 돕기 위해 전체 내용을 확인해 보라는 주의문을 담을게요.

# Kubernetes의 중요성

Kubernetes는 오케스트레이션을 위한 오픈 소스 기술로, 인기만 끌 뿐만 아니라 엄청난 성공을 거두었어요. Kubernetes의 관리 조직인 Cloud Native Computing Foundation (CNCF)의 설문 조사에 따르면, 지난 해 64%의 기업이 프로덕션에서 Kubernetes를 사용했으며, 추가 25%가 평가 중이거나 시범운영 중이었어요. 이러한 중요한 채용은 Kubernetes가 현대 IT 운영에서 발휘하는 중추적 역할을 반영하고 있어요. 이 기사에서는 이 인기 급증의 이유와 AWS가 Amazon EKS에 투자한 방대한 투자를 탐구해 보겠어요.

## 그렇다면, 왜 사람들은 Kubernetes를 사용할까요?

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

- 혁신 주행: 기관이 Kubernetes로 전환하는 주요 이유는 고객을 대신하여 혁신을 이루는 데 있습니다. 빠르게 움직이고 안전하고 신속하게 변화를주는 능력은 혁신에 대단히 중요합니다. Kubernetes는 개발팀을위한 표준 시스템과 표준 세트를 제공하여 조직이 변화를 수용하고 사용자를위한 더 나은 혁신을 이끌어 내도록합니다.
- 고정 비용 감소: Kubernetes 도입의 또 다른 주요 동기는 고정 비용을 줄이기 위한 욕구입니다. 조직은 정적 자원에서 동적 공유 자원으로 전환하여 컨테이너를 사용하여 복잡한 계약을 분해하고 관리 오버헤드를 줄입니다. 이러한 전환은 비용 절감을 가져오면서 유연성과 확장성을 유지합니다.
- 전체 조직 활성화: Kubernetes는 응용 프로그램을 조정하기 위한 API 표준을 제공하여 다양한 환경에 대한 공통 기반을 제공합니다. AWS 클라우드의 서로 다른 지역, 온프레미스 또는 다른 클라우드 제공 업체에서 실행 중이든 상관없이 Kubernetes를 사용하면 조직이 모든 배포 위치에서 일관되게 최상의 방법, 거버넌스 컨트롤, 보안 조치, 모니터링 및 비용 관리를 수립할 수 있습니다. 이 표준화는 조직이 인수, 합병 또는 멀티 클라우드 설정과 같은 시나리오에서도 복잡성을 관리할 수 있도록합니다.
- 미래를 대비하고 위험 감소: Kubernetes는 즉각적인 운영 요구 사항뿐만 아니라 미래를 대비하고 위험을 감소하는 데도 도움을 줍니다. Kubernetes에서 제공하는 표준은 장기 개발 노력을 지원하고 기술 부채를 완화합니다. 이는 CEO 및 CTO뿐만 아니라 엔지니어 및 개발팀에게도 중요한 문제입니다.

AWS의 Kubernetes 투자: 5주년을 축하하는 Amazon EKS는 AWS의 Kubernetes에 대한 중요한 투자를 대표합니다. AWS는 EKS를 강력한 플랫폼으로 만들기 위해 성실히 노력하고 성능, 규모, 신뢰성 및 가용성에서 뛰어납니다. CNCF 및 AWS 고객의 데이터에 따르면, AWS에서実行되는 Kubernetes 워크로드가 다른 모든 플랫폼보다 많이 있으며, 매주 수십억 개의 EC2 인스턴스 시간이 EKS에서 실행됩니다.

## EKS에서 고객들이 무엇을하고 있을까요?

고객들은 EKS를 다양한 목적으로 활용하고 있습니다. 활동 범위는 레거시 .NET 및 Java 응용 프로그램을 클라우드로 이전하고 데이터 처리 작업을 실행하며 실시간 백엔드를 구축하고 웹 프론트 엔드를 개발하는 등 다양합니다.

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

요즘에는 EKS와 Kubernetes를 활용한 기계 학습과 AI의 중요성이 크게 늘어났습니다. 생성적 AI와 로봇공학과 같은 다양한 분야를 다루며, 특히 저는 자율 주행 차량에 특히 흥미를 가지고 있습니다. EKS에서 중요한 자율 주행 차량 훈련 활동이 진행 중이라는 점을 강조하고 싶습니다. 기계 학습, kube ray, Spark, kube flow와 같은 도구를 활용하여 미래 기술을 개발 중인 기업들을 관찰하는 것은 현재의 환경에서 주목할 만하고 흥미로운 측면입니다.

## Kubernetes 운영 체제

![이미지](/assets/img/2024-06-19-FutureofAmazonEKS_0.png)

인프라 조사에서 Kubernetes가 핵심적인 역할을 하는 복잡한 계층을 관찰하며, 다양한 인프라 요소를 조율하여 원활하게 통합하는 모습을 보게 됩니다. 고객들은 Kubernetes 위에 배포, 관측, 거버넌스, 트래픽 제어, 보안 제어를 추가하여 적극 참여하고 있습니다.

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

놀랍게도 CNCF 내에서 599개의 프로젝트가 Kubernetes 내부 또는 옆에서 실행됩니다. 이 중 173개 프로젝트는 CNCF로부터 직접 지도 받는 개방형 지배를 갖고 있습니다. Kubernetes 위에 이러한 방대한 플랫폼 레이어를 관리하는 것은 상당한 일이죠.

게다가 AWS는 자사의 서비스가 Kubernetes로의 기능 제공을 원활하게 하는 통합을 개발하기 위해 적극적으로 노력하고 있습니다. 또한, 통합 개발자 플랫폼(IDP)이 Kubernetes에서 응용 프로그램을 패키징하고 실행하며, 데이터 처리 작업을 조정하고, 기계 학습 워크플로를 관리하는 데 중요해집니다. 이 다양한 레이어를 통해 탐색이 진행됨에 따라 고객 경험 향상과 포장, 컨테이너, 실행 생태계 내에서 프로세스를 정교화하는 데 초점이 맞춰집니다.

## AWS Kubernetes의 목표

- 차별화되지 않은 무거운 작업 제거:

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

- AWS에서 Kubernetes를 관리하는 데 연관된 막거운 무거운 작업의 부담에서 해방된 고객들.
- 운영 복잡성이 처리되어 사용자들이 핵심 역량에 초점을 맞추고 루틴적인 작업 관리에 관여하는 대신에 방향을 전환할 수 있게 합니다.

2. 작업 단순화 및 액세스 부여:

- AWS에서 Kubernetes 경험을 단순화하여 사용자들에게 간소화되고 사용자 친화적인 환경을 제공하는 것을 목표로 합니다.
- 사용자들에게 클라우드의 규모, 안정성, 보안에 대한 완전한 액세스 권한이 부여되며, 운영 환경이 원활하고 효율적으로 관리되도록 보장합니다.

3. 오픈 소스 표준의 계속적인 유지:

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

- 쿠버네티스의 오픈 소스 표준을 계속해서 유지보수합니다.
- 이는 CNCF 내 599개 프로젝트와의 호환성을 보장하며 빠른 혁신을 가능하게 하고 커뮤니티 및 파트너의 새로운 개발에 접근하며 커뮤니티 표준의 계속된 향상을 도와줍니다.

## AWS가 어떻게 Kubernetes를 지원하나요

AWS 생태계 내에서 Kubernetes에 대한 포괄적인 지원에 참여하고 있는 Amazon EKS는 관리 버킷에서 중요한 요소로 두드러집니다. Kubernetes에 대한 AWS의 약속의 중심 역할을 하는 EKS는 고객에 견고하고 효율적인 Kubernetes 관리 솔루션을 제공하는 의지를 보여줍니다. EKS 외에도 AWS는 Kubernetes 배포의 개발, Kubernetes와 AWS 구성 요소 간의 연결을 용이하게 해주는 다양한 방식을 제공하며 상류 개발에 적극적으로 참여함으로써 포용합니다.

AWS 팀은 CNCF의 보안 협의회 및 Kubernetes 프로젝트에 기여함으로써 안전에 대한 약속을 확장하고 견고한 안전 조치를 보장합니다. 더불어 AWS는 공동 비용 절감 및 공유 리소스의 가용성 향상에 기여하는 커뮤니티 프로젝트에 금융적 지원을 제공함으로써 중요한 역할을 합니다.

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

## 어디서나 Kubernetes 실행하기

![Future of Amazon EKS](/assets/img/2024-06-19-FutureofAmazonEKS_1.png)

EKS를 중심으로, AWS에서의 Kubernetes 제공은 다양한 환경으로 확장되었습니다. EKS는 우리의 Distro 및 EKS Anywhere에 걸쳐 퍼져 있으며 온-프레미스 Kubernetes 배포를 위한 툴 체인을 포함합니다. 전통적인 AWS 지역을 넘어, EKS는 Snow, Outposts, 로컬 존 및 파장까지 다양한 환경에 확장되었습니다. 이 방대한 커버리지는 사용자가 AWS 인프라 또는 다양한 온-프레미스 시나리오에서 Kubernetes를 실행하더라도 일관되고 성능 좋은 Kubernetes 경험을 쉽게 얻을 수 있도록 합니다. AWS의 목표는 사용자에게 유연한 옵션을 제공하여 선택한 배포 환경에 관계없이 일관되고 신뢰할 수 있는 Kubernetes 경험을 보장하는 것입니다.

# EKS 5주년

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

지난 5년 동안 이 제품에 투자된 상당한 개발을 되돌아보면, 2018년 EKS 발표 이후 222번 이상의 다양한 런칭이 진행되었다는 사실을 강조해야 합니다. 이러한 런칭은 가격 인하와 규정 준수 조치부터 새로운 프로젝트 시작, 클러스터 생성 시간 가속화, 새로운 인스턴스 및 지역 지원 추가, 중요한 기능 도입까지 다양한 향상을 포함하고 있습니다.

노바 라일라툴 리즈키아
