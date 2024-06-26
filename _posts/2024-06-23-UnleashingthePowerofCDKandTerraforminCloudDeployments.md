---
title: "클라우드 배포에서 CDK와 Terraform의 강력한 기능 활용하기"
description: ""
coverImage: "/assets/img/2024-06-23-UnleashingthePowerofCDKandTerraforminCloudDeployments_0.png"
date: 2024-06-23 00:13
ogImage:
  url: /assets/img/2024-06-23-UnleashingthePowerofCDKandTerraforminCloudDeployments_0.png
tag: Tech
originalTitle: "Unleashing the Power of CDK and Terraform in Cloud Deployments"
link: "https://medium.com/@sidathasiri/unleashing-the-power-of-cdk-and-terraform-in-cloud-deployments-b7871c7e340d"
---

![2024-06-23-UnleashingthePowerofCDKandTerraforminCloudDeployments](/assets/img/2024-06-23-UnleashingthePowerofCDKandTerraforminCloudDeployments_0.png)

# 소개

클라우드로 애플리케이션을 배포하는 것은 현대 소프트웨어 개발의 중요한 부분이 되었습니다. AWS는 클라우드 배포를 용이하게 하는 서비스로 CloudFormation을 제공하며 AWS Cloud Development Kit(CDK) 같은 도구를 제공합니다. 동시에 Terraform은 다중 클라우드 제공 업체로 더 빠른 배포를 가능케 하는 인프라스트럭처의 코드(IaC)에 강력한 솔루션이 되었습니다. 이 글에서는 AWS CDK와 Terraform을 함께 사용하는 이점을 살펴보고 TypeScript에서 CDK를 사용하여 REST API를 생성하는 실용적인 예제를 살펴보겠습니다.

# Terraform과 CDK란 무엇인가요?

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

테라폼과 CDK는 인프라를 코드로 정의하는 데 도움을 주는 주요 도구들입니다. 각 솔루션에는 각각의 장단점이 있습니다. 좀 더 자세히 알아보도록 하죠.

## 테라폼

테라폼은 HashiCorp에서 만든 도구로, HCL (HashiCorp Configuration Language)이라는 고수준 구성 언어를 사용하여 인프라를 정의할 수 있게 해줍니다. 테라폼은 클라우드에 독립적이며 AWS, Azure, Google Cloud Platform을 포함한 다양한 클라우드 제공 업체 간의 인프라를 관리할 수 있습니다. 또한 AWS의 경우 CloudFormation과 비교했을 때 빠른 배포를 가능하게 합니다.

## AWS CDK

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

AWS Cloud Development Kit (CDK)은 클라우드 인프라를 코드로 정의하고 AWS CloudFormation을 통해 프로비저닝하는 오픈 소스 소프트웨어 개발 프레임워크입니다. CDK는 TypeScript를 포함한 익숙한 프로그래밍 언어를 사용하여 애플리케이션을 모델링합니다. CDK는 코드를 사용하여 인프라를 생성하기 위해 일반적인 CloudFormation 템플릿을 생성합니다. 이 추상화로 인해 CDK를 사용하여 몇 줄의 코드로 매우 긴 CloudFormation 템플릿을 생성할 수 있습니다. 이는 개발자가 즐겨 사용하는 프로그래밍 언어로 편리하게 인프라 코드를 구현하고 유지할 수 있도록 도와줍니다.

# Terraform과 CDK를 함께 사용하는 이점

두 도구를 함께 사용하면 양쪽의 이점을 누릴 수 있습니다. Terraform은 HCL을 사용하지만 개발자에게는 불편할 수 있습니다. CDK는 몇 줄의 코드로 인프라를 구현하기 위한 고수준 재사용 가능한 CDK 구조를 제공함으로써 이를 해결합니다. 또한 매우 익숙한 프로그래밍 언어를 사용하기 때문에 개발자에게 친숙합니다.

반면, CDK는 CloudFormation을 내부적으로 사용하며 이는 일반적으로 Terraform보다 느릴 수 있습니다. 그러나 CDK와 Terraform을 함께 사용할 때 Terraform을 사용하여 클라우드 배포를 수행하기 때문에 훨씬 빠른 클라우드 배포를 할 수 있습니다.

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

# 프로젝트 설정

CDK를 사용하여 Typescript를 언어로 사용하여 Terraform 프로젝트를 설정해 봅시다. CDK를 Terraform에 사용하기 위한 몇 가지 선행 조건을 설정해야 합니다.

- Terraform CLI
- NodeJS
- TypeScript
- CDKTF CLI

설정이 완료되면 프로젝트를 시작할 수 있습니다. 먼저, 초기 코드를 설정할 폴더를 만들어 보겠습니다.

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

아래와 같이 CLI 명령어를 사용하여 프로젝트를 초기화할 수 있어요. 이 프로젝트에서는 TypeScript를 사용할 거에요.

프로젝트를 초기화하고 나면, main.ts 파일을 업데이트하여 필요한 인프라를 정의할 수 있어요. main.ts 파일 안에 CDK 앱과 스택이 생성되어 있어요. 스택 내의 리소스를 필요에 맞게 업데이트하여 배포할 수 있어요. API Gateway 및 Lambda 함수를 사용하여 간단한 hello world REST API를 구축해보죠.

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

# REST API 구축

어떤 AWS 리소스도 추가하기 전에, AWS를 클라우드 제공업체로 사용할 것이므로 Terraform에서 AWS 프로바이더를 구성해야 합니다. 또한,

Terraform 백엔드를 저장하고 배포 상태를 추적하기 위해 S3 버킷을 사용할 수 있습니다.

아래와 같이 필요한 CDK 생성물 (AwsProvider, S3Backend)과 같은 매개변수를 추가하여 간단히 구성할 수 있습니다.

여기서 우리는 배포에 필요한 AWS 계정 ID와 지역을 제공함으로써 AWS 프로바이더를 구성했습니다. 마찬가지로, S3 백엔드를 구성하려면 버킷 이름과 다른 구성을 제공했습니다.

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

이제 람다 함수를 실행할 IAM 역할을 생성하고, 기본 람다 실행 역할의 권한을 포함시킵니다.

이제 람다 함수를 생성할 시간입니다. src 폴더 내 index.ts 파일에 람다 함수 코드를 추가해 보겠습니다. 우리는 간단한 hello-world 애플리케이션을 구축하고 있으므로, 람다 함수는 간단한 hello-world 응답을 반환합니다.

람다 함수 핸들러 구현을 추가한 후, 해당 핸들러를 참조하고 람다 함수 리소스를 생성하기 위해 CDK 구현을 추가할 수 있습니다.

위 정의에 따라 함수 코드를 보관할 S3 버킷을 생성하고, 람다 함수를 만듭니다. 앞서 정의한 역할은 함수의 실행 역할로 제공됩니다.

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

람다 함수가 준비되면 이제 API Gateway REST API를 생성하고 람다 함수와 통합할 수 있습니다.

여기서는 API Gateway를 위한 구성을 정의하고, /hello 경로를 위한 리소스 및 해당 /hello GET 엔드포인트에 대한 GET 메서드를 정의하고 있습니다. 마지막으로, 우리는 앞에서 만든 람다 함수와 프록시 통합으로 통합했습니다.

모든 것이 올바르게 통합되었으므로 API Gateway에 스테이지를 생성하고 아래와 같이 배포를 생성할 수 있습니다.

우리는 설정에서 만들고자 하는 스테이지 이름과 API를 제공했습니다.

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

이제 필요한 모든 리소스를 생성했습니다. 하지만 해야 할 일이 아직 하나 더 있어요. API Gateway 서비스가 제공된 람다 함수를 호출할 수 있도록 보장해야 합니다. 이를 위해 람다 함수 내에서 해당 작업을 허용하는 리소스 기반 정책을 생성하고 첨부해야 해요. LambdaPermission 구성을 사용하여 아래와 같이 쉽게 할 수 있어요.

이 구성 요소는 람다 함수에 필요한 권한을 추가하여 앞서 생성한 API에 의해 호출될 수 있도록 해줘요. 이렇게 하면 구현이 완료됩니다.

이제 모든 것이 배포할 준비가 됐어요. 인프라를 프로비저닝하기 위해 Terraform이 AWS에 액세스할 수 있도록 AWS 자격 증명을 올바르게 구성했는지 확인하세요. 먼저 코드를 빌드하고 아래 명령을 사용하여 배포할 수 있어요.

![image](/assets/img/2024-06-23-UnleashingthePowerofCDKandTerraforminCloudDeployments_3.png)

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

위의 명령을 실행하면 CDK for Terraform이 누락된 패키지가 있는 경우 설치되고 배포가 시작됩니다. 배포가 완료되면 생성된 리소스를 확인하고 API를 사용해 볼 수 있습니다.

![이미지](/assets/img/2024-06-23-UnleashingthePowerofCDKandTerraforminCloudDeployments_4.png)

게다가, 우리는 Terraform이 CloudFormation보다 배포를 훨씬 빠르게 실행한다는 것을 알 수 있습니다. 이는 무척 유리한 점입니다.

만들어 둔 리소스를 삭제하려면 cdktf destroy 명령을 실행할 수 있습니다. 이렇게 하면 프로젝트에서 생성된 모든 리소스가 적절히 정리됩니다.

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

AWS CDK를 Terraform과 함께 사용하면 클라우드 인프라를 효과적으로 관리하는 여러 가지 이점이 있습니다. CDK는 AWS와 깊게 통합되어 있으며 TypeScript와 같은 익숙한 프로그래밍 언어를 지원하여 AWS 리소스를 정의하는 것을 직관적이고 유지보수하기 쉽게 만듭니다. Terraform의 클라우드에 중립적인 기능은 여러 클라우드 제공업체에 걸쳐 원활한 관리를 가능하게 하여 CDK를 보완합니다. 이 조합은 유연성, 사용 편의성 및 모듈성을 제공하여 전체 인프라 관리 워크플로우를 향상시킵니다. 이 두 도구를 활용하면 배포 프로세스를 최적화하고 효율성을 향상시키며 더 견고하고 다양한 인프라 관리 솔루션을 구축할 수 있습니다.
