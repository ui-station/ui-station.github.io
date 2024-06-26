---
title: "테라폼 안전하고 고가용성이 높으며 고장 허용성이 있는 클라우드 인프라 배포하기"
description: ""
coverImage: "/assets/img/2024-06-19-TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures_0.png"
date: 2024-06-19 13:35
ogImage:
  url: /assets/img/2024-06-19-TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures_0.png
tag: Tech
originalTitle: "Terraform: Deploying Secure, Highly Available, and Fault-Tolerant Cloud Infrastructures"
link: "https://medium.com/towards-aws/terraform-deploying-secure-highly-available-and-fault-tolerant-cloud-infrastructures-be98126ca35e"
---

## "구름의 공포"

![Image](/assets/img/2024-06-19-TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures_0.png)

# 소개

강력하고 신뢰할 수 있는 클라우드 인프라는 기업이 고객에게 원활한 서비스를 제공하는 데 중요합니다. 고가용성과 내결함성은 이러한 인프라의 중요한 구성 요소입니다. 이를 달성하기 위해 많은 조직이 Terraform과 같은 인프라 자동화 도구를 활용하여 클라우드 자원을 배포하고 관리합니다.

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

이 글에서는 Terraform을 사용하여 AWS에 높은 가용성과 장애 허용성을 갖춘 클라우드 환경을 배포하는 방법을 살펴보겠습니다. 사용자 정의 VPC의 프라이빗 서브넷에 걸쳐 두 가용 영역을 포함하는 Auto Scaling 그룹 (ASG)을 포함한 환경을 만들어봅니다. 또한 ASG를 퍼블릭 서브넷에 놓인 애플리케이션 로드 밸런서 (ALB)로 프론트 엔드로 사용하고, 적절한 게이트웨이와 라우트 테이블 구성에 대해 살펴봅니다.

결과적으로, Terraform을 활용하여 안정적이고 확장 가능한 클라우드 인프라를 구축하는 방법에 대한 깊은 이해를 갖게 될 것입니다. 이를 통해 고트래픽을 처리하고 각 구성요소의 개별 실패에도 가동 시간을 유지할 수 있는 클라우드 인프라를 구축할 수 있게 됩니다.

자, 함께 알아보겠습니다!

# 배경

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

## 테라폼

이전에 작성한 기사 "밤을 통해 테라포밍"에서는 테라폼에 대해 잘 설명하고, 그 이점 및 테라폼의 가장 기본적인 개념 중 일부를 소개했어요.

오늘의 데모를 위해 신속하게 준비하려면 해당 기사로 이동하여 소개 및 배경 그리고 기본 테라폼 명령어 섹션을 읽어보는 걸 권장합니다.

## 아마존 EC2 오토 스케일링 그룹 (ASG)

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

AWS에서 제공하는 Amazon EC2 Auto Scaling 그룹은 수요에 따라 그룹 내 EC2 인스턴스의 수를 자동으로 조정해주는 서비스입니다.

이를 통해 워크로드를 처리할 수 있는 원하는 인스턴스 수를 유지하고, 필요에 따라 인스턴스를 자동으로 추가하거나 제거할 수 있습니다. 이를 통해 애플리케이션은 수요에 따라 신축성 있게 확장 또는 축소할 수 있으며, 필요한 인스턴스만 실행하여 비용을 최적화할 수 있습니다.

EC2 Auto Scaling 그룹은 다양한 스케일링 정책, 런치 구성, 인스턴스 유형을 구성하여 효율적이고 안정적인 애플리케이션 성능을 보장할 수 있습니다.

## 애플리케이션 부하 분산기 (ALB)

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

AWS의 Application Load Balancer(ALB)는 애플리케이션 수준 콘텐츠를 기반으로 EC2 인스턴스와 같은 여러 대상에 대한 들어오는 트래픽을 분산시킬 수 있습니다.

ALB는 고급 라우팅 규칙을 기반으로 다양한 대상으로 트래픽을 지능적으로 라우팅할 수 있습니다. 이러한 라우팅 규칙에는 경로 기반 라우팅, 호스트 기반 라우팅, HTTP 헤더 기반 라우팅이 포함됩니다.

ALB는 SSL/TLS 오프로딩, 연결 드레이닝, 스티키 세션과 같은 다양한 기능을 제공하며, AWS의 자동 확장 및 탄력성 기능과 함께 사용하여 최적의 성능과 확장성을 보장할 수 있습니다.

# 필수 사항

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

- Terraform 개념과 명령어에 대한 기본 지식 및 이해도
- 기본적인 리눅스 명령 줄 지식
- IAM 사용자 및 관리 권한이 있는 AWS 계정
- 사용할 준비가 된 AWS Cloud9 IDE
- AWS CLI가 설치되어 Cloud9 IDE에 구성됨
- Amazon EC2 키페어

# 사용 사례

전자 상거래 회사인 REX TECH Corp는 휴일 시즌 동안 트래픽 증가를 처리해야 합니다. 회사는 웹 사이트가 고객에게 항상 응답 가능하고 이용 가능하도록 하기를 원합니다.

당신의 매니저가 AWS에 클라우드 인프라를 배포하여 가용성과 고장 허용성을 보장할 것을 지시했습니다. 당신은 고객이 항상 웹 사이트에 접속할 수 있도록 하기 위해 트래픽에 따라 EC2 Auto Scaling 그룹(ASG)을 배포하기 위해 Terraform을 활용하기로 결정했습니다. 이 그룹은 개인 서브넷에 배포되며 공개 서브넷의 응용 프로그램 로드 밸런서(ALB)에 의해 선전되며, 트래픽에 따라 자동으로 확장 또는 축소되어 웹 사이트가 항상 고객에게 응답 가능하도록 보장합니다.

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

# 목표

- 두 개의 공용 서브넷, 두 개의 사설 서브넷이 있는 사용자 정의 가상 사설망(VPC)을 생성합니다.
- 공용 서브넷에 NAT 게이트웨이를 활용하고, 인터넷 게이트웨이를 사용하여 외부 인터넷 트래픽을 제공합니다.
- 공용 라우팅 테이블과 사설 라우팅 테이블을 구성합니다.
- 공용 서브넷에 ALB를 시작합니다.
- 사설 서브넷에 Auto Scaling 그룹을 시작합니다.
- ALB의 공용 DNS를 출력하고 해당 URL을 사용하여 웹 서버에 도달 가능한지 확인합니다.

# 단계 0: IDE 환경에서 최신 Terraform 버전 설치

AWS Cloud9은 Terraform이 사전 설치되어 있지만, 최신 버전으로 업그레이드하여 해당 기능과 기능을 완전히 활용하는 것이 중요합니다.

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

클라우드9 IDE에 최신 Terraform 버전을 설치하려면 다음 명령어를 차례대로 실행해주세요 —

```js
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo
sudo yum -y install terraform
```

클라우드9 터미널에서 "terraform version"을 실행하여 최신 버전이 정상적으로 설치되었는지 확인해주세요 —

![이미지](/assets/img/2024-06-19-TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures_1.png)

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

이해를 높이고 데모를 더 효과적으로 따르기 위해 아래 링크를 클릭하여 GitHub에서 프로젝트 저장소를 클론해 주세요. 계속 진행하면서 필요한 대로 파일을 편집해도 괜찮아요.

이제 Step 1로 이동해봅시다. - 커스텀 VPC 생성 및 구성.

# Step 1: 커스텀 AWS VPC 생성 및 구성

우리의 초기 목표는 AWS 환경 내에서 모든 리소스를 배포할 수 있는 커스텀 VPC를 구축하는 것입니다. 이를 위해 논리적으로 격리된 CIDR 블록을 정의하고 적합한 서브넷 및 해당 가용 영역(AZ)을 신중히 선택하여 배포해야 합니다.

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

아래 테라폼 파일 코드를 검토해 봅시다. 사용자 정의 VPC를 생성하고 구성하는 코드입니다.

## 코드 설명

여기서는 지정된 CIDR 블록과 인스턴스 테넌시를 사용하여 AWS VPC를 생성합니다. 그런 다음 네 개의 서브넷을 생성합니다. 두 개는 공개 서브넷이고 두 개는 사설 서브넷입니다. 각 서브넷은 지정된 CIDR 블록을 가지며 앞서 생성된 VPC와 연결되어 지정된 가용 영역에 배포되도록 정의되어 있습니다.

공개 서브넷은 map_public_ip_on_launch 속성이 true로 설정되어 있어 해당 서브넷에 배포된 인스턴스에는 자동으로 공개 IP 주소가 할당됩니다. 사설 서브넷은 해당 속성이 false로 설정되어 있어 해당 서브넷에 배포된 인스턴스는 NAT 게이트웨이를 통해 인터넷에서 접속할 수 없습니다. 각 서브넷에는 태그를 사용하여 이름이 지정되어 있습니다.

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

이제 사용자 정의 VPC를 생성하고 구성했으니, 2단계로 넘어가서 인터넷 게이트웨이와 NAT 게이트웨이를 생성해 봅시다.

# 2단계: 공용 서브넷에 인터넷 게이트웨이 및 NAT 게이트웨이 생성

VPC에서 리소스를 배포할 때, 인터넷에 안전하고 효율적으로 연결되도록하는 것이 중요합니다. 이를 위해 공용 서브넷에 인터넷 게이트웨이와 NAT 게이트웨이를 생성하는 방법 중 하나입니다.

인터넷 게이트웨이는 VPC 구성요소로서 VPC 내의 인스턴스와 인터넷 간의 통신을 허용합니다. 한편, NAT 게이트웨이는 사설 서브넷에 있는 인스턴스가 인터넷이나 다른 AWS 서비스에 연결할 수 있도록하며, 외부 트래픽에 대한 추가 보안도 제공합니다.

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

아래에서 Terraform 파일을 만들고 구성하여 인터넷 게이트웨이와 NAT 게이트웨이를 생성하는 코드를 살펴보겠습니다 —

## 코드 설명

먼저, 인터넷 게이트웨이를 생성하고 사용자 정의 VPC에 연결합니다. 또한 Elastic IP 주소와 NAT 게이트웨이를 생성하고 Elastic IP와 공용 서브넷에 연결합니다. 인터넷 게이트웨이가 NAT 게이트웨이를 생성하기 전에 먼저 생성되도록 합니다.

좋아요! 이제 인터넷 게이트웨이와 NAT 게이트웨이가 생성되었으니, 다음은 — 3단계로 넘어가 공용 및 사설 라우트 테이블을 구성하는 것입니다.

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

# 단계 3: 공용 라우트 테이블 및 사설 라우트 테이블 구성

이 시점에서, 우리는 맞춤형 VPC와 게이트웨이를 성공적으로 생성했습니다. 그러나, 지금 직면한 문제는 VPC에 배포된 서비스 및 다른 구성 요소들 간의 통신을 관리하는 것입니다. 이곳에서 VPC 내에서의 네트워크 트래픽 흐름을 구성하는 것이 중요해집니다. 이를 위해, 서브넷 간의 네트워크 트래픽을 안내하기 위해 라우팅 테이블을 설정하는 것이 효과적입니다.

아래의 코드를 검토하여 각각의 라우트 테이블을 생성하고 구성하는 방법을 확인해보겠습니다 —

## 코드 설명

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

여기에서는 두 개의 라우팅 테이블을 생성 중입니다 — 공개 라우팅 테이블과 사설 라우팅 테이블. 공개 라우팅 테이블은 인터넷 게이트웨이로의 기본 경로를 갖고 있으며, 사설 라우팅 테이블은 NAT 게이트웨이로의 기본 경로를 갖고 있습니다.

공개 라우팅 테이블은 두 개의 공개 서브넷과 연결되어 있고, 사설 라우팅 테이블은 두 개의 사설 서브넷과 연결되어 있습니다. 사설 리소스 연결은 사설 라우팅 테이블이 생성된 후에 수립됩니다. 이 라우팅 테이블에는 태그를 사용하여 이름이 지정되어 있습니다.

이제, Step 4로 계속해 봅시다! 공개 서브넷에서 새 ALB를 실행합니다.

# Step 4: 공개 서브넷에 ALB를 실행합니다.

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

사용자의 ALB를 활성화하기 위한 과정을 살펴보겠습니다. 사용자 정의 VPC의 공용 서브넷에서 ALB를 생성하는 것은 여러 단계를 거칩니다. 이 단계에는 보안 그룹 생성, 대상 그룹, 리스너 및 라우팅 규칙 설정이 포함됩니다.

아래의 Terraform 코드를 검토해보세요. 이 코드는 ALB와 연결할 보안 그룹을 생성합니다.

## 코드 설명

이 Terraform 코드에서는 ALB와 함께 사용할 보안 그룹을 생성합니다. 보안 그룹은 이름과 설명과 함께 정의되며, 사용자 정의 VPC와 연관됩니다.

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

Ingress 규칙을 통해 특정 CIDR 블록에서 HTTP 및 SSH 트래픽을 허용하고, 모든 트래픽을 허용하는 egress 규칙도 있습니다. 또한 보안 그룹에 쉽게 식별할 수 있도록 이름을 태그합니다.

이제 ALB를 생성하고 구성하는 코드를 검토할 수 있습니다 —

## 코드 설명

여기서 ALB 및 해당 리소스를 생성합니다. 먼저 ALB를 생성하고, 사용할 이름, 서브넷 및 보안 그룹을 지정합니다.

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

ALB가 트래픽을 전달할 타겟 그룹을 생성하여 이름, 포트, 프로토콜 및 헬스 체크 구성을 지정합니다.

마지막으로 ALB를 위해 리스너를 생성하여 수신할 포트와 프로토콜을 지정하고, 기본 작업을 이전에 생성한 타겟 그룹으로의 트래픽 전달로 설정합니다. 이렇게 하면 리스너 규칙과 타겟 그룹의 구성에 따라 적절한 리소스로 트래픽이 전달됩니다.

이제 다음 단계로 계속해 보죠. Terraform 구성 파일을 검토하여 사용자 정의 VPC를 생성하고 해당 적절한 서브넷을 구성하며, 인터넷 및 NAT 게이트웨이, 그리고 공용 및 비공용 라우팅 테이블을 설정했습니다. ALB의 구성이 이제 완료되어 준비되었으므로, Step 5로 이동할 수 있습니다 — 비공용 서브넷에서 ASG를 시작합니다.

# Step 5: 비공용 서브넷에서 자동 스케일링 그룹 시작하기

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

오토 스케일링 그룹(ASG)은 일반적으로 공용 서브넷에 배포되지만, 민감한 데이터를 다루거나 규제 요구 사항을 준수해야 하는 경우와 같이 사설 서브넷을 선호하는 경우가 많이 있습니다.

아래 코드를 살펴보면, 두 가용 영역(AZ)에 걸쳐 EC2 ASG를 사설 서브넷에서 시작하는 단계를 설명합니다 —

## 코드 설명

우선, ASG 런치 템플릿용 보안 그룹을 생성하고, 이를 이전에 생성한 사용자 정의 VPC와 연결해야 합니다.

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

보안 그룹은 ALB의 보안 그룹에서 엄격히 HTTP 및 SSH 프로토콜에 대한 인바운드 트래픽을 허용합니다. 이렇게 함으로써 ASG에 대한 액세스는 ALB에서만 가능하게 되어 보안이 제공됩니다. 보안 그룹은 상태를 유지하므로 정의된 변수에서 지정된 CIDR 블록으로 어디서든지 아웃바운드 트래픽을 허용합니다.

## Bash 스크립트

ASG를 생성하기 위해 우리가 준비할 것은 ASG의 런치 템플릿에 통합될 bash 스크립트를 먼저 작성하는 것입니다. 이 스크립트는 EC2 인스턴스의 사용자 데이터 역할을 하여 Apache 웹 서비스의 설치 및 시작, 그리고 맞춤식 웹 페이지의 생성을 자동화합니다.

아래의 bash 스크립트를 살펴봅시다 —

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

## 코드 설명

이 스크립트에서는 먼저 모든 yum 패키지 저장소를 업데이트한 다음 yum을 사용하여 Apache Web Server 패키지를 설치합니다. 그런 다음 Apache 서비스를 시작하고 부팅 시 자동으로 시작되도록 활성화합니다.

또한 이 스크립트는 흔히 사용되는 소프트웨어 패키지를 쉽게 설치할 수 있게 하는 Extra Packages for Enterprise Linux (EPEL) 저장소를 설치합니다.

또한 스크립트는 인스턴스를 고부하로 테스트하는 데 사용할 수 있는 stress 패키지를 설치합니다. 마지막으로 스크립트는 기본 index.html 파일에 REX TECH에 오신 것을 환영합니다! 메시지가 포함된 사용자 정의 웹페이지를 Apache 문서 루트 디렉토리 /var/www/html/에 추가합니다.

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

## ASG 생성 및 구성

우리의 ASG 보안 그룹 및 Bash 스크립트가 생성되었으므로 이제 ASG를 만들고 구성할 차례입니다 —

## 코드 설명

ASG를 생성할 때 최소, 최대 및 원하는 용량을 설정해야 합니다. 또한 ASG는 서브넷 ID로 식별되는 두 개의 사설 서브넷에서 시작됩니다.

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

런치 템플릿(Launch Template)은 Amazon Machine Image (AMI), 인스턴스 유형 및 키파일과 같은 구성과 함께 생성됩니다. 여기에는 이전에 생성한 Bash 스크립트를 base64로 인코딩한 사용자 데이터 파일로 지정합니다. 이는 Apache 웹 서버를 설치하고 시작하고 사용자 정의 웹 페이지를 생성하는 스크립트를 실행합니다.

마지막으로 ASG를 ALB의 대상 그룹에 자동 확장 첨부를 사용하여 연결합니다. 이를 통해 ASG가 들어오는 트래픽에 대응하여 인스턴스 간 부하를 자동으로 균형 잡아 최적의 성능과 가용성을 보장합니다.

이제 6단계로 넘어가서 output.tf 파일을 활용하여 ALB의 퍼블릭 DNS를 얻어봅시다.

# Step 6: output.tf 파일을 활용하여 ALB의 퍼블릭 DNS를 얻기

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

ALB를 만든 후에는 ALB의 공개 DNS에 직접 액세스하는 것이 귀찮을 수 있습니다. 다행히도, 테라폼에서 output.tf 파일을 생성하여 ALB의 공개 DNS를 즉시 얻을 수 있는 쉬운 해결책을 제공할 수 있습니다. 이를 통해 들어오는 트래픽이 올바르게 타깃 그룹 내의 인스턴스로 경로 지정되도록 보장할 수 있습니다.

아래 output.tf 파일을 확인해 봅시다 —

## 코드 설명

Outputs를 사용하면 테라폼 인프라에서 생성된 리소스의 값들을 볼 수 있습니다. 여기서 우리는 테라폼 인프라를 위해 여러가지 output을 생성합니다. 각 output은 다른 리소스를 참조합니다.

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

저희의 출력물에는 VPC의 ID, 퍼블릭 및 프라이빗 서브넷의 ID, 인터넷 게이트웨이와 ALB의 DNS 이름이 포함됩니다. 이러한 출력물은 인프라에 대한 정보를 제공하기 위해 사용될 것입니다.

이제, 7단계로 넘어갑시다 — 리소스 인자의 동적 값에 대한 변수 생성.

# Step 7: 리소스 인자의 동적 값에 대한 변수 생성

Terraform을 사용하여 인프라스트럭처를 생성할 때 리소스 인자에 동적 값들을 사용하는 것이 일반적입니다. 이러한 동적 값들은 사용자 입력, 환경 변수 또는 다른 리소스의 출력 값에 기반할 수 있습니다.

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

이러한 동적 값들을 관리하기 위해 변수를 사용하는 것이 중요합니다. 변수를 사용하면 배포 사항 전반에 걸쳐 일관성을 유지하고, 코드를 읽고 유지하기 쉽게 만들며, 코드의 재사용성을 가능케 합니다.

아래 코드에서 정의한 많은 변수들을 살펴봅시다 —

## 코드 설명

이곳에서는 이 Terraform 프로젝트에서 동적 값들을 사용할 수 있게 해주는 일련의 변수들을 정의합니다.

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

각 변수에는 이름, 기본값 및 유형이 있습니다. 기본값은 Terraform 코드를 실행할 때 특정 값이 지정되지 않은 경우 변수가 설정되는 값을 나타냅니다. 유형은 변수의 예상 데이터 유형을 지정합니다.

예를 들어, aws_region 변수는 기본값으로 us-east-1을 가지고 있으며 문자열 유형을 갖습니다. 이는 문자열 값을 예상한다는 것을 의미합니다. 마찬가지로 auto-assign-ip 변수는 기본값으로 true를 가지고 있으며 불리언(bool) 유형을 갖습니다. 이는 불리언 값을 예상한다는 것을 의미합니다.

일부 변수는 인프라의 네트워킹 구성 요소와 관련되어 있으며 VPC, 공용 서브넷 및 사설 서브넷의 이름과 같은 값을 지정합니다.

변수를 정의함으로써 주요 구성 파일을 수정하지 않고 값이 쉽게 변경될 수 있습니다.

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

알겠어요! 이제 우리가 모든 Terraform 구성 파일들을 검토했으니, Step 8로 넘어갈게요 — 정의된 인프라를 배포하기 위해 Terraform 워크플로우를 실행해봅시다.

# Step 8: Terraform 워크플로우 실행해서 초기화, 유효성 검사, 계획 및 적용하기

Cloud9 터미널에서 필요한 공급자를 초기화하려면 Cloud9 터미널에서 다음 명령을 실행해주세요 —

```js
terraform init
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

초기화 프로세스가 완료되면 아래와 같이 성공적인 프롬프트가 표시됩니다.

![이미지](/assets/img/2024-06-19-TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures_2.png)

이제 다음 명령을 실행하여 코드에 구문 오류가 없는지 확인해보겠습니다 —

```js
terraform validate
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

아래와 같이 유효한 것으로 확인되는 성공 메시지를 생성하는 명령어여야 합니다.

![image](/assets/img/2024-06-19-TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures_3.png)

이제 다음 명령어를 실행하여 Terraform이 적용할 모든 수정 사항 목록을 생성해 봅시다. —

```js
terraform plan
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

테라폼이 기대하는 인프라 리소스에 적용할 변경 목록을 표시해야 합니다. "+" 기호는 추가될 내용을 나타내고, "-" 기호는 제거될 내용을 나타냅니다.

![변경 목록](/assets/img/2024-06-19-TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures_4.png)

이제 이 인프라를 배포해 봅시다! 다음 명령을 실행하여 변경 사항을 적용하고 리소스를 배포하세요.

참고 - 이 명령을 실행한 후에 변경 사항에 동의하기 위해 "yes"를 입력해야 합니다.

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
terraform apply
```

테라폼은 인프라에 대한 모든 변경 사항을 적용하는 프로세스를 시작할 것입니다. 배포 프로세스가 완료될 때까지 잠시 기다려주시기 바랍니다.

![이미지](/assets/img/2024-06-19-TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures_5.png)

# 성공!

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

지금은 추가된, 수정된 및 삭제된 리소스의 총 수를 명시하고 "적용 완료"라는 메시지로 프로세스를 마무리해야 합니다. 몇 가지 리소스 출력과 함께 해야 합니다.

브라우저에서 웹 페이지에 액세스하려면 ALB의 DNS URL을 복사하여 저장하세요.

![ALB DNS URL](/assets/img/2024-06-19-TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures_6.png)

이제 리소스가 생성되었는지 확인하기 위해 관리 콘솔에서 검토해봅시다.

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

# 단계 9: ASG 및 대상 그룹의 건강 상태에서 EC2 인스턴스 생성 확인

AWS 관리 콘솔에서 EC2 대시보드로 이동하여 ASG에서 시작된 두 대 서버가 있는지 확인하세요.

![이미지](/assets/img/2024-06-19-TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures_7.png)

또한 왼쪽 창으로 이동하여 대상 그룹을 선택하고 대상 그룹을 확인하고, 하단으로 스크롤하여 인스턴스의 건강 상태가 정상인지 확인하세요.

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

<img src="/assets/img/2024-06-19-TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures_8.png" />

좋아요! ASG가 예상대로 EC2 인스턴스를 생성했고, 모든 대상 그룹의 상태가 정상적으로 표시되고 있는 것을 성공적으로 확인했습니다. 이제 ALB를 통해 ASG의 웹 서버에 액세스할 수 있는지 확인해 봅시다.

# 단계 10: 브라우저에서 URL을 사용하여 웹 서버에 도달 가능한지 확인하기

설정한 브라우저를 열고 ALB의 DNS URL을 브라우저에 붙여넣어 주세요.

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

**알림** - ALB에 연결하려면 "http://" 프로토콜을 사용해야 합니다. "https://"는 사용하지 마세요.

![사진](/assets/img/2024-06-19-TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures_9.png)

# 축하합니다!

"The Terror In The Clouds"를 성공적으로 완료했어요. Terraform을 사용하여 신뢰할 수 있고 확장 가능한 클라우드 인프라를 구축하는 방법을 배웠습니다. 이 인프라는 높은 트래픽을 다룰 수 있으며 개별 구성 요소의 장애가 발생해도 가동 시간을 유지할 수 있습니다.

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

# 정리하기

## 인프라 파괴

아래 명령어를 실행하여 이전에 Terraform으로 프로비저닝한 모든 리소스를 삭제/제거/해제하세요 —

```js
terraform destroy
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

"작업이 완료될 때까지 기다려주세요. 마침내 '파괴가 완료되었습니다'라는 메시지와 함께 파괴된 리소스의 양이 표시됩니다.

지금까지 읽어 주셔서 감사합니다! 유익했기를 바랍니다.

![TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures](/assets/img/2024-06-19-TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures_10.png)

Ifeanyi Otuonye는 클라우드/데브옵스 엔지니어로 클라우드 기술과 데브옵스 문화에 광신적인 엔지니어입니다. 배우려는 열정으로 움직이며 협업 환경에서 번영합니다. 정보 기술과 프로젝트 관리의 배경을 가지고 있으며 프로 선수의 삶을 균형 있게 유지하고 있습니다. 2021년 말부터 클라우드/데브옵스 엔지니어로 나아가기 위해 자기 학습을 시작했으며, 최근에는 Level Up In Tech 프로그램에 참여하였습니다!"

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

# Master DynamoDB

지금 'TOWARDSAWS' 코드를 사용하여 DynamoDB Book을 35% 할인된 가격으로 구매하세요.

![DynamoDB Book](/assets/img/2024-06-19-TerraformDeployingSecureHighlyAvailableandFault-TolerantCloudInfrastructures_11.png)
