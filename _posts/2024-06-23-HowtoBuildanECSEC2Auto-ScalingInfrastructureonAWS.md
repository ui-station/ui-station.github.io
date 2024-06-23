---
title: "AWS에서 ECS  EC2 오토스케일링 인프라 구축하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_0.png"
date: 2024-06-23 22:40
ogImage: 
  url: /assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_0.png
tag: Tech
originalTitle: "How to Build an ECS + EC2 Auto-Scaling Infrastructure on AWS"
link: "https://medium.com/@ayushunleashed/how-to-build-ecs-ec2-auto-scaling-infrastructure-on-aws-ba730aa076a9"
---


# 소개:

대규모로 백엔드 응용 프로그램을 배포하는 것은 일관된 성능 및 가용성을 보장하기 위해 중요합니다.

AWS에서 Docker 컨테이너를 배포하는 여러 방법이 있습니다. 예를 들어, EC2 인스턴스, AWS Lambda, Fargate를 사용한 ECS 및 EC2를 이용한 ECS 등이 있습니다. 이 블로그에서는 ECS와 EC2를 사용하여 자동 확장 인프라를 설정하는 방법을 안내합니다. 우리의 요구에 맞는 최적의 선택인 이유를 알아보고 AWS UI를 사용하여 인프라를 구축하며, 다음 블로그에서는 Terraform을 사용하여 관리할 것입니다. 예시로, 백엔드 응용 프로그램으로 Rick Roll Docker 이미지를 사용할 예정입니다. 😎

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_0.png)

<div class="content-ad"></div>

## 블로그 개요:

- 컨테이너를 배포하는 다양한 방법
- ECS + EC2를 선택하는 이유?
- 다이어그램을 활용한 인프라 구조
- 플로우 설명
- AWS Management Console을 사용한 인프라 구축

# 요구 사항:

- AWS 계정: 활성화된 AWS 계정이 필요합니다.
- AWS 서비스에 대한 기본 지식: EC2, IAM 역할, VPC, 서브넷, 보안 그룹 및 로드 밸런서에 대한 이해가 필요합니다.
- Docker에 대한 익숙함: Docker 및 컨테이너화 개념에 대한 경험이 필요합니다.
- 인프라 구축 경험: AWS 또는 다른 클라우드 제공업체에서 인프라를 구축한 경험이 유용합니다.

<div class="content-ad"></div>

# 컨테이너 배포의 다양한 방법:

도커 컨테이너를 배포하는 방법은 다양한 AWS 서비스를 통해 가능합니다. 각각의 장단점이 있습니다:

- CI/CD 자동화를 활용한 EC2 머신에서 컨테이너 실행: 전체적인 제어가 가능하지만 더 많은 설정과 유지보수가 필요하며 자동으로 확장되지 않습니다. 컨테이너 오케스트레이션 서비스를 사용하는 것이 더 나을 수 있습니다.

2. AWS App Runner:

<div class="content-ad"></div>

- 장점: 최소 구성으로 간편한 배포, 자동 스케일링.
- 단점:
— 최대 4 vCPU, 12 GB RAM.
— 요청 횟수에 기반한 스케일링으로 CPU 또는 RAM이 아닌 요청 횟수에 따라 스케일링되어 부하를 잘못 추정했을 경우 금액 낭비나 성능 문제 발생 가능성.
— 서비스 생성 후 구성 변경 불가; 구성 변경을 위해 삭제 및 재생성 필요.
— 서비스 생성 후 구성 변경 불가; 구성 변경을 위해 삭제 및 재생성 필요.
- 왜 부적합한가: 제한된 자원과 스케일링 메커니즘은 일관된 트래픽을 가진 진행 중인 프로젝트에 적합하지 않습니다.

3. AWS Lambda: 짧은 작업에 적합하지만 콜드 스타트 문제와 메모리 및 실행 시간 제약이 있습니다.

4. ECS: 강력한 컨테이너 조작, 자동화 관리 및 스케일링을 제공하여 CI/CD, AWS App Runner 또는 AWS Lambda와 비교했을 때 EC2보다 유연성과 제어 능력을 제공합니다.

## 왜 ECS + EC2를 선택해야 하나요?

<div class="content-ad"></div>

우리는 여러 이유로 ECS + EC2를 선택했습니다:

- 컨테이너 오케스트레이션: ECS는 강력한 컨테이너 오케스트레이션 기능을 제공합니다.
- 확장성: 높은 사용량에 따라 자동으로 스케일링할 수 있는 능력.
- 일관된 트래픽: 안정적인 트래픽을 갖는 B2B 애플리케이션에 적합하며, AWS의 권장을 따르고 있습니다.
- Cold Starts 회피: AWS 람다나 Fargate와 같은 서버리스 옵션과 달리, EC2의 ECS는 Cold Start 지연 문제가 없습니다.
- 미래 지향성: ECS + EC2는 GPU 워크로드 및 높은 RAM 사용을 처리할 수 있으며, 람다, 앱 러너 또는 심지어 ECS + Fargate와 같은 서비스가 지원할 수 없습니다. 이는 애플리케이션 요구사항이 계속 발전하는 경우에도 주요 재설계 없이 대응할 수 있는 미래 지향적인 솔루션이 됩니다.
- 제어 및 유연성: 인스턴스 유형, 스케일링 정책 및 다른 구성에 대한 완전한 제어로, 복잡한 재설계 없이 인프라를 발전시킬 수 있습니다.
- 장기 비용 절감: Fargate는 EC2 인스턴스 관리를 추상화하지만, 안정적인 트래픽을 처리할 때 EC2 인스턴스를 직접 사용하는 것이 장기적으로 더 비용 효율적일 수 있습니다.

<img src="/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_1.png" />

무서워하지 마세요. 깊게 들이마셔… 깊게 내쉬세요. 그래, 그렇습니다. 여전히 여기 있네요? 멋져요. 이해를 돕기 위해 이해해야 할 네 가지 주요 구성 요소가 있습니다: Route 53 부분, ECS 부분, 로드 밸런서 부분 및 자동 스케일링 부분입니다. 이는 이전에 이미 본 것의 상세한 버전일 뿐입니다.

<div class="content-ad"></div>

모든 녹색 항목은 우리 인프라에서 정의해야 하는 자원입니다. 모든 빨간색 항목은 해당 자원의 부산물입니다. 그렇기 때문에 인프라를 코드로 관리한다면, 녹색으로 표시된 모든 것을 Terraform 자원으로 정의해야 합니다.

# 인프라 작동 방식:

인프라스트럭처에서 ECS + EC2 자동 확장 기능이 작동하는 방법

먼저 자원의 기본적인 정의부터 시작해봅시다:

<div class="content-ad"></div>

- Route 53 호스티드 존: 특정 도메인의 DNS 레코드를 관리합니다.
- Route 53 레코드: 귀하의 애플리케이션으로 트래픽을 라우팅하는 DNS 레코드입니다.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_2.png)

- 로드 밸런서: 여러 대상 (EC2 인스턴스) 사이에 들어오는 애플리케이션 트래픽을 분산시킵니다.
- 리스너: 클라이언트에서 로드 밸런서로의 연결을 위한 프로토콜과 포트를 정의합니다.
- 대상 그룹: 트래픽을 라우팅할 대상 (EC2 인스턴스)의 논리적 그룹화입니다.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_3.png)

<div class="content-ad"></div>

- ECS 클러스터: 작업 및 서비스의 논리적 그룹화입니다.
- ECS 서비스: ECS 클러스터 내의 작업을 배포하고 확장하는 서비스입니다.
- 작업 정의: Docker 컨테이너가 어떻게 시작되어야 하는지를 지정합니다.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_4.png)

- ECS Capacity Provider: ECS 클러스터 내에서 작업을 실행하기 위한 인프라를 관리합니다.
- Auto Scaling 그룹: EC2 인스턴스 그룹을 관리하고 수요에 따라 자동으로 조정합니다.
- 시작 템플릿: EC2 인스턴스를 시작하기 위한 구성 세부정보를 제공합니다.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_5.png)

<div class="content-ad"></div>

- 앱 자동 스케일링 정책: CPU 사용률과 같은 메트릭에 기반한 스케일링 정책을 정의합니다.
- 앱 자동 스케일링 대상: 어떤 ECS 서비스를 스케일할지 지정합니다.

# 플로우 설명:

- Route 53을 이용한 DNS 구성:
도메인을 위한 Route 53 호스트된 존이 있으며, lb-prod.domain.com과 같은 DNS 레코드를 생성합니다. 이 레코드는 사용자 트래픽을 로드 밸런서로 보냅니다.
- 로드 밸런서를 통한 트래픽 관리:

- 로드 밸런서는 사용자로부터 트래픽을 수신합니다. EC2 인스턴스가 실행 중인 작업에 직접 트래픽을 보낼 수 없으므로 트래픽을 대상 그룹으로 전달합니다.
- 로드 밸런서의 리스너는 트래픽 전달에 사용할 프로토콜과 포트를 정의합니다.

<div class="content-ad"></div>

3. 대상 그룹 및 ECS 서비스 통합:

- 대상 그룹은 EC2 인스턴스에서 실행 중인 작업을 대상으로 하는 ECS 서비스에 연결됩니다.
- ECS 서비스는 ECS 클러스터에서 작업을 관리하여 예상대로 실행되도록 합니다.

4. 작업 정의 및 ECS 클러스터:

- 작업 정의는 Docker 컨테이너의 세부 정보를 지정합니다. ECS 서비스는 이 정의를 사용하여 작업을 생성합니다.
- 이러한 작업은 ECS 클러스터 내에서 실행되며, 이는 작업과 서비스의 논리적 그룹화입니다.

<div class="content-ad"></div>

5. EC2 인스턴스에서 작업 실행:

- 작업을 실행하기 위해 EC2 인스턴스에서 제공되는 인프라가 필요합니다.
- 자동 확장 그룹은 런치 템플릿을 사용하여 EC2 인스턴스를 자동으로 시작하고 관리합니다.

6. ECS Capacity Provider:

- ECS 용량 제공자는 작업 실행에 필요한 인프라를 관리합니다. EC2 인스턴스가 작업 실행에 사용할 수 있는지를 보장합니다.

<div class="content-ad"></div>

7. 자동 스케일링 정책:

- 앱의 자동 스케일링 정책은 CPU 사용률 임계값을 50%로 정의합니다. 평균 CPU 사용률이 이 임계값을 초과하면 ECS 용량 공급자를 사용하는 ECS 서비스가 확장되어 실행 중인 작업 수가 증가합니다.
- 자동 스케일링 그룹은 수요에 맞게 EC2 인스턴스 수를 조정합니다.

8. 앱 자동 스케일링 대상:

- 자동 스케일링 대상은 스케일링 정책이 올바른 ECS 서비스에 연결되어 있는지 확인합니다.

<div class="content-ad"></div>

9. ECS 클러스터 용량 공급자:

- 이 구성 요소는 여러 ECS 용량 공급자를 추적하고 ECS 클러스터와 연결합니다. 서비스에 특정 용량 공급자가 연결되지 않은 경우 기본 전략을 적용합니다.

## 요약하면 다음과 같은 흐름이 됩니다

- 사용자 트래픽은 Route 53 하위 도메인 레코드를 통해 로드 밸런서로 이동됩니다.
- 로드 밸런서는 트래픽을 대상 그룹으로 전달하여 ECS 서비스에서 관리하는 작업으로 경로 지정합니다.
- 작업은 ECS 용량 공급자와 오토스케일링 그룹에서 관리하는 EC2 인스턴스에서 실행됩니다.
- 오토스케일링 정책은 수요에 따라 인프라가 확장되어 최적의 성능을 유지하게 합니다.

<div class="content-ad"></div>

# AWS UI를 사용하여 인프라 구축하기

일단 루트 53 부분은 선택 사항이며 인프라 재구성 시 로드 밸런서의 동일한 DNS가 필요한 경우에만 사용될 예정이니 지금은 건너 뛰도록 하겠습니다. 이 부분은 terraform을 통해 관리할 때 다시 설정하는 것으로 할 거에요.

## 우리가 구축할 내용 개요:

- 먼저 ECS 클러스터를 구축합니다
- EC2 인스턴스를 선택할 겁니다
- 이들을 위해 새로운 auto-scaling 그룹을 생성합니다
- 런치 템플릿 정의
— 원하는 용량 정의
— 인스턴스 유형
— AMI
— 스토리지
— 사용할 키페어
— 서브넷
— 보안 그룹
- 태스크 정의를 생성합니다
- EC2를 런치 타입으로 설정
- OS
- CPU, RAM 요구 사항
- 태스크 실행 롤
- 사용할 이미지로 최소한 하나의 컨테이너를 정의합니다
- 볼륨 추가
- 컨테이너 포트 설정
- 이제 ECS 서비스를 생성합니다
- Capacity provider 전략
- 태스크 패밀리
- 원하는 태스크
- 로드 밸런싱 (로드 밸런서 생성)
— 응용 프로그램 로드 밸런서
— 타겟 그룹
— 서비스 자동 확장 정책

<div class="content-ad"></div>

# 단계:

우선 AWS 계정에 로그인하신 후, ECS를 검색하고 열어주세요.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_6.png)

이제 ECS 클러스터 생성부터 시작해봅시다.

<div class="content-ad"></div>

- "Create Cluster"을 클릭하세요. 이름을 지어주세요; 저는 "rick-roll-prod-cluster"로 부를 거에요. 자원의 이름 짓는 규칙으로 kebab-case를 사용할 거예요.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_7.png)

2. "Infrastructure" 섹션으로 스크롤 내려가보세요. EC2 또는 Fargate 중 하나를 선택할 수 있습니다; 이전에 논의했던대로 EC2 런치 타입을 선택할 거예요.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_8.png)

<div class="content-ad"></div>

3. 저희는 EC2 인스턴스의 생성 및 종료를 관리할 자동 스케일링 그룹을 생성할 예정입니다.

- 새로운 EC2 인스턴스를 생성하기 위해서는 ASG가 사용할 런치 템플릿을 지정해야 합니다:
  - AMI: 이를 유지합시다.
  - 인스턴스 유형: t2.micro로 진행합시다.
  - EC2 인스턴스 역할: 새 역할을 생성할 것입니다.
  - 원하는 수용 능력: 최소값을 0으로 설정하고 최대값을 4로 설정해주세요. 이는 ASG가 유지하는 ECS 클러스터의 인스턴스 수에 대한 제한입니다.
  - SSH 키: 선택적으로 이 인스턴스에 로그인하기 위한 SSH 키를 생성하거나 무시해주세요.
  - 볼륨: 기본값인 30으로 설정해주세요.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_9.png)

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_10.png)

<div class="content-ad"></div>

4. 이제 VPC 및 서브넷은 기본 설정으로 남겨두세요.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_11.png)

5. 새 보안 그룹을 만들고 어디에서나 HTTP 액세스를 허용하는 인바운드 규칙을 추가하세요.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_12.png)

<div class="content-ad"></div>

6. 모니터링을 활성화하고 원하는 경우 태그를 추가할 수 있습니다. 이제 클러스터를 생성하세요.

![Cluster Image](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_13.png)

- 시간이 걸릴 수 있지만, ASG, EC2 런치 템플릿 및 보안 그룹이 포함된 ECS 클러스터를 성공적으로 만들었습니다.

태스크 정의 생성:

<div class="content-ad"></div>

- 태스크는 컨테이너 주변의 래퍼와 같습니다. 태스크 정의를 만들고, 그에 따라 우리의 컨테이너가 실행됩니다. 왼쪽에 있는 "태스크 정의"를 클릭하고, "새 태스크 정의 생성"을 클릭하세요.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_14.png)

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_15.png)

2. 이름을 지정하고 EC2에서 실행하려는 것을 명시합니다.

<div class="content-ad"></div>


![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_16.png)

3. 이제 작업 크기를 결정해야 합니다. 작업 크기는 작업(컨테이너)에 필요한 CPU 및 메모리 양을 결정합니다.

- CPU를 1 vCPU로, Memory를 0.5 GB로 설정합니다. 둘 다 EC2 하드웨어(t2.micro) 한도 내에 있습니다.
- OS 및 Network 모드는 기본 설정으로 유지합니다.
- Task 역할을 `ecsTaskExecutionRole`로 설정합니다.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_17.png)


<div class="content-ad"></div>

4. 작업에서 실행할 적어도 하나의 컨테이너를 지정해야 합니다.

- 컨테이너에 이름을 지정하세요.
- 이미지로는 Docker Hub에서 사용 가능한 Rick Roll 이미지를 사용할 것입니다.
- Rick Roll이 실행될 포트인 80으로 설정하세요.

```js
kale5/rickroll:vclatest
```

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_18.png)

<div class="content-ad"></div>

5. 건강 상태 확인: 저희 컨테이너의 건강 상태를 모니터링하기 위해 건강 상태 확인을 설정해야 합니다. ECS 서비스는 서버가 작동 중인지 계속 확인하기 위해 이 엔드포인트를 반복해서 핑합니다. 우리 경우에는 root 엔드포인트로 curl을 수행하는 건강 상태 확인을 생성할 수 있습니다.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_19.png)

6. 원하는 경우 작업 정의에 태그를 추가할 수 있습니다. 이제 "만들기"를 클릭하면 작업이 생성됩니다.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_20.png)

<div class="content-ad"></div>

- 이제 ECS 클러스터와 작업 정의를 갖고 있으니 클러스터에서 작업을 시작할 수 있습니다. 그러나 작업을 자동으로 관리하고 확장하기 위해 ECS 서비스를 생성할 것입니다.

ECS 서비스 생성:

- 이제 ECS 클러스터를 열고 “Service” 섹션을 클릭한 다음 “Create”를 클릭하세요.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_21.png)

<div class="content-ad"></div>

2. 컴퓨팅 옵션의 경우, 기본 설정을 사용하겠습니다. 클러스터 용량 공급자와 ECS 용량 공급자가 이미 생성되어 있습니다.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_22.png)

3. 애플리케이션 유형은 기본값인 Service로 남겨두세요.

4. 작업 정의의 경우, 이전에 정의한 작업 패밀리를 선택하세요. 가장 최신의 수정본이 자동으로 가져와집니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_23.png" />

5. 서비스에 이름을 지정해주세요. "원하는 작업" 섹션에서는 ECS 서비스가 ECS 클러스터에서 실행 중인 작업을 얼마나 유지할지 정의합니다. 2로 설정하면 언제나 최소 2개의 작업이 실행 중임을 보장합니다.

<img src="/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_24.png" />

6. 이제 로드 밸런서를 만들고 해당 로드 밸런서를 ECS 서비스에 연결하여 ECS에 부하를 분산합니다. "로드 밸런싱 - 선택 사항"을 선택하여 시작하세요.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_25.png" />

- 자세히 보기에서 로드 밸런서 유형을 ALB로 선택하세요. 컨테이너에서는 "rick-roll-vc-image 80:80"를 선택하세요. 새로운 로드 밸런서를 만들고 이름을 붙여주세요.

<img src="/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_26.png" />

7. 아래로 스크롤하세요. 이제 새로운 리스너와 타겟 그룹을 만듭니다. 리스너는 로드 밸런서에서 타겟 그룹으로 트래픽을 보내고, 해당 타겟 그룹은 실행 중인 ECS 태스크를 대상으로합니다.

<div class="content-ad"></div>


<img src="/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_27.png" />

8. 이제 모든 로드 밸런서 관련 구성이 완료되었습니다. 더 아래로 스크롤하세요. 부하에 따라 확장 및 축소하려면 서비스에 대한 자동 확장 정책을 정의해야 합니다.

- "서비스 자동 확장" 드롭다운 메뉴를 엽니다.
- "서비스 자동 확장 사용" 확인란을 선택합니다.
- 서비스가 실행되기를 원하는 최소 및 최대 작업 수를 정의합니다.

<img src="/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_28.png" />

<div class="content-ad"></div>

- 자동 스케일링 정책의 정책 이름을 정의하세요. 서비스 메트릭에서는 임계값으로 평균 CPU 사용률을 사용할 것입니다. 서비스가 작업을 확장하거나 축소하는 데 사용할 수 있는 다른 메트릭을 선택할 수도 있습니다.
- 타겟 값을 50%로 설정하면, 언제든지 평균 CPU 사용률이 이 숫자를 넘어가면 시스템을 확장하고, 그 밑으로 내려가면 축소할 것입니다.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_29.png)

9. 다른 것들은 그대로 두고 고유한 태그를 부착한 후 “생성”을 클릭하여 서비스를 만드세요.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_30.png)

<div class="content-ad"></div>

- 생성 프로세스가 완료될 때까지 몇 분 정도 소요될 예정이니, 조금만 기다려 주세요.

# 인프라 테스트 중:

- AWS 관리 콘솔에서 "부하 분산기"를 검색하고 클릭합니다.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_31.png)

<div class="content-ad"></div>

2. 이제 새로 생성한 Application Load Balancer (ALB)을 찾아서 열어보세요.

![ALB image](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_32.png)

- DNS 레코드를 복사하고 새 탭에서 열어보세요. 이것이 우리 앱의 URL입니다 (Application Load Balancer의 DNS 레코드). 

![DNS record image](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_33.png)

<div class="content-ad"></div>

- 503 에러가 발생할 수 있습니다. 이는 로드 밸런서가 작동 중이지만 ECS 작업으로 트래픽이 수신되지 않음을 의미합니다. 이는 서비스가 아직 생성 중이거나 작업이 아직 시작되지 않았을 수 있습니다.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_34.png)

3. 리스너, 서비스, 대상 그룹 및 작업을 확인하세요. 일부 리소스가 아직 완전히 생성되지 않은 경우가 있습니다.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_35.png)

<div class="content-ad"></div>

- 일정 시간이 지나면 모든 리소스가 완전히 생성되어 ​​건강한 작업이 실행되는 것을 확인할 수 있습니다.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_36.png)

- 이제 로드 밸런서 DNS가 열린 탭을 새로고침해주세요.

![이미지](/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_37.png)

<div class="content-ad"></div>

- 와! 우리 인프라가 작동 중이야! 🎉

# 결론 및 다음 단계

우리 인프라가 마침내 가동 중임을 확인할 수 있어요. 고량의 트래픽을 보내면 EC2 인스턴스에서 실행 중인 작업을 자동으로 확장하여 수요를 충족시킬 거에요.

이 정도까지 오다니 축하해요!

<div class="content-ad"></div>

<img src="/assets/img/2024-06-23-HowtoBuildanECSEC2Auto-ScalingInfrastructureonAWS_38.png" />

다음 블로그에서는 이 인프라를 테라폼을 사용하여 코드로 변환하는 방법을 보여드리겠습니다. 이를 통해 인프라의 쉬운 사용자 정의, 수정 및 여러 버전을 제공할 수 있습니다. 현재는 여러분의 여정에 튼튼한 출발점이 될 것입니다. 테라폼을 다룬 다음 파트에 관심이 있다면 댓글을 남겨주세요. 계속해서 지켜봐 주시고 즐거운 코딩 되세요!

저와 연결을 원하시나요? 여기가 제 LinkedIn입니다.

# 감사의 글

<div class="content-ad"></div>

Yogendra Manawat ( SDE Intern @AiCaller.io ) : 리뷰 및 플로우를 완벽하게 만드는 데 도움을 주었습니다.

Chirag Panjwani ( SDE @ Genesis Technologies ): 가치 있는 피드백을 제공해 주셨습니다.

Siddharth Singh Patel : 개선을 위한 제안 및 선행 조건 추가와 같은 아이디어에 대해 감사드립니다.