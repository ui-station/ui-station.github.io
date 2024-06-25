---
title: "AWS에 LLM 앱을 배포하는 오픈소스 셀프 서비스 방법"
description: ""
coverImage: "/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_0.png"
date: 2024-05-18 17:04
ogImage:
  url: /assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_0.png
tag: Tech
originalTitle: "Deploying LLM Apps to AWS, the Open-Source Self-Service Way"
link: "https://medium.com/towards-data-science/deploying-llm-apps-to-aws-the-open-source-self-service-way-c54b8667d829"
---

![LLM](/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_0.png)

LLM 어플리케이션은 OpenAI와 같은 타사 호스팅 LLM을 사용하도록 개발되었을 때 MLOps 오버헤드가 필요하지 않습니다. 이러한 컨테이너화된 LLM 파워드 앱 또는 마이크로서비스는 DevOps 관행을 따르며 배포할 수 있습니다. 본 문서에서는 우리의 LLM 앱을 AWS와 같은 클라우드 제공업체에 자동으로 배포하는 방법을 탐색해보겠습니다. LlamaIndex는 커뮤니티를 위한 준비된 RAGs 챗봇을 보유하고 있습니다. 우리는 샘플 앱으로 RAGs를 사용하겠습니다.

# IaC Self-Service

IaC는 Infrastructure as Code의 줄임말로 인프라 프로비저닝을 자동화하여 구성이 일관성 있고 반복 가능하게 보장합니다. IaC를 실행할 수 있는 여러 도구들이 있습니다. 본 문서에서는 Terraform을 중점으로 다룰 것인데, Terraform은 클라우드에 중립적인 특성을 가지고 있기 때문입니다.

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

IaC self-service의 주요 목적은 개발자들이 더 많은 액세스, 제어 및 소유권을 통해 파이프라인에 대한 생산성을 향상시키는 데 도움을 주는 것입니다.

관심 있으신 분들을 위해, 약 1년 전에 DevOps self-service 모델에 관한 5부작 시리즈를 작성했습니다. DevOps self-service 모델에 관련된 모든 측면에 대해 자세히 다루었습니다.

# 고수준 배포 다이어그램

AWS에 컨테이너화된 응용 프로그램을 배포하는 다양한 옵션이 있습니다. ECS Fargate는 몇 가지 좋은 이유로 눈에 띕니다:

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

- 컨테이너용 서버리스 컴퓨팅, 서버 관리 없이 가능
- 확장성과 확장성 증가
- 배포 간소화

먼저 RAGs 앱을 위한 고수준 배포 다이어그램을 작성합니다.

![image](/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_1.png)

AWS에 RAGs를 배포하려면 파이프라인이 필요합니다.

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

# 파이프라인 개요

먼저, 내가 만든 3-2-1 규칙을 기반으로 한 자가 서비스 파이프라인 아키텍처를 탐색해보겠습니다:

- 3가지 유형의 소스 코드: Terraform 코드, 앱 소스 코드 및 GitHub Actions 워크플로 코드.
- 2가지 유형의 파이프라인: 인프라 파이프라인 및 애플리케이션 파이프라인.
- 1개의 파이프라인 통합 글루: GitHub Secrets 생성 자동화.

![image](/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_2.png)

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

자, 한번 자세히 살펴봅시다.

# 인프라 파이프라인

저희는 인프라 파이프라인에서 terraform init, terraform plan, terraform apply 등 테라폼의 핵심 기능을 사용합니다. 아래 다이어그램을 참조해주세요. 2023년 8월 테라폼의 라이센스 변경에도 불구하고, 테라폼의 핵심 기능은 여전히 오픈 소스로 유지됩니다.

terraform init 이전에 몇 가지 단계를 추가합니다:

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

- 워크플로우 보안을 위한 Harden Runner
- 클라우드 비용 관리를 위한 Infracost
- 린트를 위한 TFLint
- 정적 IaC 코드 분석을 위한 Checkov

이러한 도구에 대한 자세한 내용은 파이프라인 보안 및 가드레일에 관한 제 논문을 확인해주세요.

![이미지](/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_3.png)

우리는 Terraform에서 IaC 코드를 처음부터 작성해야 할까요? 아닙니다. 잘 알려진 오픈 소스 Terraform 재사용 가능한 모듈인 terraform-aws-modules를 활용합니다.

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

## Terraform AWS 모듈

terraform-aws-modules은 AWS에서 리소스를 관리하기 위해 명시적으로 설계된 사전 제작, 재사용 가능한 오픈 소스 Terraform 모듈의 다양한 모음입니다. Anton Babenko가 이끄는 terraform-aws-modules는 지금까지 57개의 모듈을 보유하고 있습니다! 이러한 모듈은 AWS에서 인프라 프로비저닝을 간소화하고 자동화하며, 모범 사례를 표준화하고, 인프라 코드를 작성하는 데 덜 신경 쓰고 빠른 배포를 달성할 수 있도록 돕습니다.

GCP의 경우 terraform-google-modules, Azure의 경우 Azure-Verified-Modules가 있습니다.

자신만의 재사용 가능한 모듈을 작성할 수도 있지만, 이 오픈 소스 재사용 가능한 모듈은 커뮤니티에서 지원하고 테스트됩니다. 인프라 파이프라인 개발을 빠르게 시작하는 데 유용합니다.

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

우리 RAGs 앱을 위해, 새 AWS 계정에 배포할 예정이며, terraform-aws-modules에서 다음 모듈들을 최소한으로 선택할 것입니다. "최소한" 이라고 말한 이유는 프로젝트 요구에 따라 추가로 리소스를 이 스택에 추가할 수 있습니다. 예를 들어 인증/인가 등을 위한 리소스 등이 있습니다. 그러나 이 POC 데모 앱에서는 자가 서비스 모델을 보여주고 오픈소스 IaC 재사용 가능 모듈을 소개하기 위해 최소 요구 사항을 준수할 것입니다. 두 가지 재료를 숙지하면 프로젝트 요구에 따라 추가 리소스를 프로비저닝하기 위해 재사용 가능 모듈을 선택하실 수 있습니다.

![링크](/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_4.png)

- terraform-aws-vpc: 새 VPC, 공용/개인 서브넷, 인터넷 게이트웨이, NAT 게이트웨이, 경로 테이블 등을 프로비저닝하는 네트워킹 모듈입니다.
- terraform-aws-s3-bucket: ALB 로그용 S3 버킷.
- terraform-aws-alb: ECS 클러스터용 응용 프로그램 로드 밸런서 (ALB).
- terraform-aws-ecs: RAGs를 배포할 ECS 클러스터에 대한 Elastic Container Service (ECS) Fargate 인스턴스.
- terraform-aws-ecr: 앱용 도커 이미지를 저장하는 Elastic Container Registry (ECR).

## 구현 전 요구 사항

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

- AWS에서 OpenID Connect (OIDC) 구성: 우리는 GitHub Actions 워크플로를 사용하여 인프라 프로비저닝을 위한 Terraform 모듈을 시작할 것입니다. OIDC는 AWS 자격 증명을 GitHub 측에 저장하지 않고 GitHub Actions 워크플로가 AWS에 액세스할 수 있도록 합니다. GitHub에는 AWS에서 OIDC를 구성하는 방법에 대한 자세한 지침이 있습니다. 이 단계는 한 번만 AWS 계정당 수행하면 됩니다.
- Terraform 원격 상태 관리: 인프라 상태는 Terraform 작업의 중요한 부분이며, 실제 세계 리소스를 구성에 매핑하고 메타데이터를 추적하여 대규모 인프라에서 성능을 향상시킵니다. Terraform 원격 상태를 사용하면 사용자가 인프라 상태를 원격 데이터 저장소에 저장하여 중앙 집중화, 보안, 일관성 및 기타 이점을 얻을 수 있습니다. 이 단계도 한 번만 AWS 계정당 수행하면 됩니다. 나는 원격 상태 관리를 위해 S3 버킷과 상태 잠금을 위한 DynamoDB를 통해 처리하는 Terraform 재사용 가능한 모듈을 개발했습니다. 소스 코드는 내 GitHub 리포지토리에 있습니다. 시작하려면 내 샘플 워크플로와 유사한 GitHub Actions 워크플로를 사용할 수 있습니다. GitHub Actions를 익숙하지 않은 경우 "Application Pipelines" 섹션을 참조하세요.

## 단계 1: GitHub 환경 생성

GitHub 환경은 시크릿/변수를 세 가지 레벨에서 저장할 수 있어서 파이프라인에서 인프라 프로비저닝 또는 애플리케이션 CI/CD 중에 시크릿/변수를 전달하여 파이프라인 작업을 돕는 중요한 역할을 합니다.

우리의 RAGs 앱을 위해 dev라는 GitHub 환경을 만들고 애플리케이션 파이프라인을 위한 ROLE_TO_ASSUME과 인프라 파이프라인을 위한 TERRAFORM_ROLE_TO_ASSUME 두 환경 변수를 생성해보겠습니다. 이 때 값은 이미 상위 섹션의 사전 준비 지침을 따라 IAM 역할을 만들었다고 가정하고 각각의 IAM 역할 ARN으로 지정해주세요. 여기서 두 가지 다른 역할을 사용하는 이유는 서로 다른 권한을 할당할 수 있기 때문입니다. 참고로, "Settings" 탭을 보려면 레포지토리에서 관리자 권한이 필요합니다.

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

<img src="/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_5.png" />

동일한 "설정" 탭 아래에서 우리는 저장소 수준에서 몇 가지 비밀을 생성합니다. 이는 동일한 앱에 대해 다른 환경에 적용할 수 있음을 의미합니다.

<img src="/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_6.png" />

- NPM_TOKEN: 이 토큰이 필요한 이유는 애플리케이션이 Terraform 재사용 모듈을 호출할 때 이러한 자격 증명을 전달하지 않기 때문입니다. Terraform 재사용 모듈을 호출하는 앱이 Terraform 재사용 모듈이 있는 저장소에 연결하려면 저장소 범위의 토큰이 필요합니다. 특히 저장소가 개인 저장소인 경우 이것은 매우 중요합니다.
- PIPELINE_TOKEN: 이 토큰은 Terraform이 GitHub 제공자를 호출하여 ECS_CLUSTER, ECS_SERVICE 등과 같은 GitHub 시크릿/변수를 자동으로 생성하게 하는 데 필요합니다. Terraform이 프비저단할 리소스를 기반으GitHub 시크릿/변수 생성의 자동화는 인프라 파이프라인을 애플리케이션 파이프라인과 통합하여 인프라 제공과 애플리케이션의 CI/CD 사이를 완벽하게 이어줍니다. 이 토큰은 저장소 및 read:public_key 스코프를 가져야 합니다.
- OPENAI_KEY: 여기에는 OpenAI API 키를 저장합니다. 여기에 비밀로 저장되면 소스 코드에 노출되지 않습니다. CI 파이프라인에 이 비밀을 검색하여 전달하는 방법은 "애플리케이션 파이프라인" 섹션에서 자세히 살펴볼 것입니다.
- INFRACOST_API_KEY: Infracost의 API 키입니다. 이는 클라우드 비용 관리를 자동화하는 인프라 비용 관리 도구로 사용될 수 있습니다.

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

## 단계 2: 인프라 파이프라인 코드 추가

마지막으로, 우리의 인프라 파이프라인 코드를 리포지토리에 추가해 봅시다. 아래 파일/폴더와 관련된 인프라 파이프라인을 확인해보세요. 샘플 코드는 내 리포지토리에서 확인할 수 있습니다. 이렇게 구성된 우리의 Terraform 코드가 왜 이런 구조를 갖는지에 대해 자세히 알아보려면 Terraform 프로젝트 구조에 대한 내 기사를 참고하세요.

![이미지](/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_7.png)

main.tf 파일은 Terraform 재사용 가능 모듈을 위한 주요 래퍼입니다. 귀하의 스택에 따라 이 파일에서 한 개 이상의 재사용 가능 모듈을 호출할 수 있습니다. 우리의 RAGs 앱의 경우, 우리는 인프라를 프로비저닝하기 위해 terraform-aws-modules 섹션에서 언급된 다섯 개의 재사용 가능 모듈을 호출합니다.

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

terraform-aws-modules의 각 재사용 가능한 모듈에 대해 해당 모듈의 사용 패턴에 대한 예제 코드를 참조하십시오. 사용 사례에 따라 단순 또는 완전한 예제 중 하나를 선택하여 해당 재사용 가능한 모듈을 main.tf의 기반이로 사용할 수 있습니다.

그런 다음, 샘플 예제 코드를 매개변수화하여 특정 환경의 terraform.tfvars 파일 아래 .env 폴더로 변수를 외부화합니다. 예를 들어, 프로덕션 환경의 CPU/메모리 값은 개발 환경에서 사용되는 값과 다를 가능성이 높습니다. 따라서 CPU/메모리는 매개변수화하기에 좋은 후보입니다.

아래와 같이 ECS 프로비저닝을 위한 샘플 Terraform 코드의 주요 요소 몇 가지를 살펴보겠습니다.

- Line 174는 우리가 재사용 가능한 모듈 terraform-aws-ecs를 호출하는 곳입니다.
- Line 177 이후는 클러스터 이름과 같은 변수를 재사용 가능한 모듈로 전달하는 곳입니다.

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

<img src="/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_8.png" />

귀하의 사용 사례에 따라, 비슷한 AWS 스택을 공유하는 많은 애플리케이션이 있다면 main.tf의 대부분 로직을 중앙 집중형 재사용 가능한 모듈 리포지토리로 이동하여 원래 terraform-aws-modules 위에 또 다른 추상화 레이어를 구현할 수 있습니다. 이 접근 방식은 IaC 코드의 추가 재사용성을 허용하여 호출자 리포지토리가 매개변수화를 위한 최소한의 IaC 코드를 갖도록 합니다. 제가 작성한 Terraform 프로젝트 구조에 대한 기사에서는 이와 같은 중앙 리포지토리가 조직 내에서 재사용 가능한 모듈을 보유하는 구현 방법을 자세히 설명하고 있습니다. 확인해보세요.

## 단계 3: 인프라 파이프라인을 위한 GitHub Actions 워크플로우 추가

테라폼 프로비저닝을 위한 재사용 가능한 GitHub Actions 워크플로우를 만들었습니다. 워크플로우 보안, 클라우드 비용 관리, IaC 린팅, 스캔 및 마지막으로 terraform init, plan, apply와 같은 단계를 포함하고 있습니다. 이는 샘플 워크플로우로, 귀하의 요구에 맞게 수정할 수 있습니다.

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

우리의 RAGs 리포지토리에서 .github/workflows 디렉토리 아래에 terraform-aws.yml을 추가했습니다. 이 워크플로우의 주요 로직은 아래 빨간색으로 강조된 부분입니다:

![이미지](/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_9.png)

- permissions: GitHub Actions 워크플로우가 OIDC를 사용하여 AWS에 인증할 수 있도록 id-token: write를 명시하는 것이 중요합니다.
- uses: 이 줄은 재사용 가능한 워크플로우를 호출하며, 동일한 로직을 다른 워크플로우 또는 다른 리포지토리로부터 중복으로 작성하는 것을 방지합니다.
- secrets: inherit: 이 줄은 환경/리포지토리/조직 수준에서 구성된 secrets/변수를 다른 리포지토리의 재사용 가능한 워크플로우로 전달할 수 있게 합니다.

## 단계 4: 인프라스트럭처 파이프라인 시작

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

지금, 모든 일이 순조롭게 진행 중이에요. RAGs 저장소에서 "Terraform AWS 프로비저닝" 워크플로를 트리거하여 인프라 파이프라인을 시작해 봅시다.

![이미지](/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_10.png)

이 워크플로는 저희 개발 환경에서 AWS 자원을 프로비저닝할 거에요. 완료되면 Terraform Apply 단계에서 나오는 결과를 주의 깊게 확인해 주세요. 아래 스크린샷을 참고해 주세요. 나중에 RAGs 앱을 실행할 때 alb_dns 값을 사용할 거에요. 이 값을 메모해 두세요.

![이미지](/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_11.png)

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

AWS에 로그인하여 VPC 리소스 맵을 확인해 보세요. 아래 스크린샷을 참조하세요. 네트워킹 (VPC, 서브넷, 라우팅 테이블 등)가 성공적으로 프로비저닝되었습니다. 또한 새로운 ECS 클러스터, ECR, ALB가 준비되었는지 확인하세요.

![AWS 스크린샷](/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_12.png)

# 애플리케이션 파이프라인 (CI/CD)

이제 AWS에서 앱을 배포할 수 있는 인프라가 준비되었습니다. 새로 구성된 ECS 클러스터로 앱을 빌드하고 배포하는 것으로 넘어갑시다.

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

아래 다이어그램은 CI (Continuous Integration) 파이프라인의 주요 단계를 나열한 것입니다.

![CI Pipeline](/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_13.png)

그리고 우리의 샘플 CD (Continuous Deployment) 파이프라인은 다음과 같습니다:

![CD Pipeline](/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_14.png)

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

## 단계 1: 앱을 컨테이너화하세요 (컨테이너화되어 있지 않은 경우)

먼저 RAGs 리포지토리에 Dockerfile을 추가하여 코드를 Docker 이미지로 빌드하고 AWS의 새로 프로비저닝된 ECR에 푸시해야 합니다. 아래 예시 Dockerfile 스니펫을 참조해 주세요.

```js
FROM python:3.9-slim

WORKDIR /app
COPY requirements.txt requirements.txt
COPY . .

RUN pip install -r requirements.txt

EXPOSE 8501
CMD ["streamlit", "run", "1_🏠_Home.py"]
```

## 단계 2: CI/CD를 위한 GitHub Actions 워크플로우 추가

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

개발 목적으로 CI/CD를 하나의 단일 워크플로에 통합할 수 있습니다. 그러나 실제로는 특히 고급 환경에서는 이미지 불변성을 보장하기 위해 CI/CD를 두 개의 다른 워크플로로 분리하는 것이 좋습니다. GitHub Actions 워크플로 오케스트레이션에 대해 더 자세히 확인하려면 제 GitHub Actions 워크플로 조정에 대한 글을 참조하세요.

CI 및 CD를 처리하는 두 가지 재사용 가능한 GitHub Actions 워크플로를 만들었습니다:

- python-build-image.yml: Python 앱을 위한 도커 이미지를 빌드합니다.
- deploy-to-ecs.yml: 도커 이미지를 AWS ECS Fargate에 배포합니다.

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

아래는 이미지 빌드 워크플로우에서의 단계로, 워크플로우는 인프라 파이프라인의 단계 1에서 정의된 GitHub 리포지토리 시크릿을 통해 OpenAI API 키를 검색한 후, 해당 키를 .streamlit 디렉토리 아래의 secrets.toml 파일에 작성합니다. 시크릿 처리를 CI 파이프라인에 위임함으로써 API 키와 같은 시크릿을 관리할 때 걱정 없이 사용할 수 있습니다. API 키와 같은 시크릿을 소스 코드에 절대로 푸쉬하면 안 됩니다.

<img src="/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_15.png" />

## 단계 3: CI/CD 파이프라인 시작

코드 푸시 또는 PR 생성/병합에 의해 CI/CD 파이프라인이 자동으로 트리거되는 것이 좋습니다. GitHub Actions의 주요 장점 중 하나는 GitHub 레포지토리와 원활하게 통합되어 있기 때문에 이와 같이 작동합니다. 워크플로우에서 샘플 트리거는 다음과 같이 정의할 수 있습니다:

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

on:
push:
branches: - main
pull_request:

이제 애플리케이션 파이프라인을 시작할 시간입니다. 도커 이미지를 빌드하고 ECS에 배포하기 위해 수동으로 실행하거나 코드를 푸시/PR합니다. 아래에 성공적인 CI/CD 워크플로우 실행을 확인하세요.

![CI/CD Workflow](/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_16.png)

## 단계 4: RAGs 앱 시작하기

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

우리의 RAGs 앱을 시작할 시간이에요. 이제 AWS ECS Fargate에 성공적으로 배포되었어요. 이전에 Terraform Apply 단계에서 인프라 파이프라인의 출력을 기억하시나요? 그 URL을 입력하여 RAGs 앱을 시작해보세요. 잘 작동할 거예요!

![이미지](/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_17.png)

# 제거 및 정리

마지막으로, AWS 리소스를 제거하고 정리해야 해요. 우리의 Terraform GitHub Actions 워크플로 파일에는 제거를 위한 대체 플로우가 구축되어 있어요. 제거 워크플로우는 앱의 제거 브랜치를 생성하고 해당 브랜치를 선택하여 Terraform 워크플로우를 트리거함으로써만 실행될 수 있어요. 이와 같은 활동을 위해 별도의 제거 브랜치를 만드는 이유는 사용자가 제거 작업을 실수로 트리거하지 않도록 보호하는 추가적인 안전 장치를 제공하기 위함이에요. 아래 스크린샷을 보고 Terraform AWS 프로비저닝 워크플로우를 트리거하여 AWS 리소스를 제거해보세요.

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

<img src="/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_18.png" />

# 주요 end-to-end 구현 포인트

저희의 파이프라인의 주요 end-to-end 구현 포인트를 GitHub 저장소, GitHub Actions 워크플로우 및 Terraform 코드에서 살펴봅시다. 다음 다이어그램은 코드부터 구성, 통합 지점까지의 end-to-end 흐름을 강조합니다.

<img src="/assets/img/2024-05-18-DeployingLLMAppstoAWStheOpen-SourceSelf-ServiceWay_19.png" />

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

# 요약

본 기사에서는 DevOps 셀프 서비스 모델을 사용하여 LlamaIndex의 RAGs 챗봇을 AWS ECS fargate에 배포하는 방법을 탐색했습니다. MLOps를 필요로 하지 않는 LLM 기반 앱의 경우, DevOps 셀프 서비스 모델을 사용하여 AWS와 같은 클라우드 제공업체에 배포하는 데 탁월하게 작동합니다.

이 셀프 서비스 모델이 제공하는 파이프라인 프레임워크와 terraform-aws-modules와 같은 다양한 오픈 소스 IaC 재사용 모듈을 결합하면 프로젝트 요구 사항에 따라 재사용 가능한 모듈을 혼합하여 파이프라인에 대한 더 많은 액세스, 제어 및 소유권을 확보하여 생산성을 높일 수 있습니다.

본 기사가 도움이 되었기를 바랍니다. 이 프레임워크의 완전한 소스 코드는 제 GitHub 저장소에 있습니다:

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

- https://github.com/wenqiglantz/reusable-workflows-modules
- https://github.com/wenqiglantz/rags

코딩을 즐기세요!

## 참고 자료:

- terraform-aws-modules GitHub 저장소
- 아마존 웹 서비스에서 OpenID Connect 구성
- 개발 운영자동화(Self-Service)로 향하는 길: 5부작 시리즈
- 개발 운영자동화(Self-Service) 중심의 파이프라인 통합
- 개발 운영자동화(Self-Service) 중심의 파이프라인 보안 및 가드레일
- 개발 운영자동화(Self-Service) 중심의 GitHub Actions 워크플로 오케스트레이션
- 개발 운영자동화(Self-Service) 중심의 Terraform 프로젝트 구조
- 개발 운영자동화(Self-Service) 파이프라인 아키텍처와 3-2-1 규칙
- Infracost + Terraform + GitHub Actions = 클라우드 비용 관리 자동화
