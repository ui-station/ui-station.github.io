---
title: "인프라스트럭처를 코드로 씁니다 새로운 사고방식이 필요합니다"
description: ""
coverImage: "/assets/img/2024-05-20-InfrastructureasCodeNeedsaRethink_0.png"
date: 2024-05-20 17:29
ogImage:
  url: /assets/img/2024-05-20-InfrastructureasCodeNeedsaRethink_0.png
tag: Tech
originalTitle: "Infrastructure as Code Needs a Rethink"
link: "https://medium.com/@hello_9187/infrastructure-as-code-needs-a-rethink-201c6875522c"
---

## 개발자에게 구름의 복잡성을 되돌리면, 제공 속도가 느리고 비용이 많이 들며 보안이 취약해집니다.

# IaC의 진화

코드를 사용하여 클라우드 컴퓨팅 인프라를 정의하는 Infrastructure as Code (IaC)는 클라우드 환경을 배포하고 관리하는 기본 메커니즘이 되었습니다. 이것은 물론, 그런 이유 때문에 그렇습니다.

IaC가 나오기 전에는 조직이 클라우드 제공업체의 웹 응용 프로그램에서 수동으로 클라우드 자원을 생성했습니다. 이제 클라우드 인프라를 선언적으로 정의하고 버전 관리 시스템에 저장함으로써, 개발자는 애플리케이션 코드가 관리되는 방식과 같이 버전이 지정되고 반복 가능한 인프라 릴리스를 만들 수 있습니다. 클라우드 인프라의 제공 속도, 안정성 및 감사 가능성 측면에서 얻는 가치는 과소평가해서는 안 됩니다.

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

우리가 지금까지 있어 온 곳이죠. 이제 클라우드 인프라 관리가 어디로 나아가야 할지 이야기해 보겠습니다.

# 클라우드 복잡성의 직접적 반영

주요 클라우드 공급업체 중립 IaC 도구로는 Terraform과 Pulumi가 있습니다. 생산에서 널리 사용되는 공급업체별 도구로는 CloudFormation과 Azure Arm 템플릿이 있습니다. 참고: Terraform이 압도적으로 가장 널리 사용되고 있기 때문에, 이 기사의 나머지 부분에서는 Terraform과 IaC를 서로 바꿔서 언급할 것입니다.

이 도구들 간의 구문과 구현 세부 사항은 각각 다르지만, 철학적으로 핵심 측면에서는 비슷합니다. 지원되는 클라우드의 인프라 관리를 가능한 한 유연하고 맞춤화하여 제공합니다.

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

표면적으로는 정말 멋져보이죠. 문제는 클라우드가 굉장히 복잡하다는 것이고, 이러한 도구들은 이 복잡성을 개발자에게 직접 전달합니다. 예를 들어, Terraform을 사용하여 AWS에서 상태를 가지는 표준 컨테이너화된 애플리케이션을 실행하고 싶다고 가정해봅시다. 이를 위해서는 다음과 같은 작업을 해야 합니다:

- 어떤 서비스를 사용할지 결정해야 합니다 (AWS ECS, AWS Fargate, Containerized Lambdas, EKS, EC2 인스턴스 등).
- 우리의 컨테이너를 AWS의 ECR 서비스에 저장하고 선택한 컴퓨팅 방법이 액세스할 수 있도록 해야 합니다.
- 데이터베이스 인스턴스를 선택해야 합니다 (RDS, Aurora, DocumentDB, DynamoDB, Redshift 등).
- 네트워킹 리소스를 구성하여 컴퓨팅 인스턴스가 안전하게 데이터베이스에 연결할 수 있도록 해야 합니다. 이 과정에는 VPC, 서브넷, 보안 그룹 등이 포함됩니다.
- 민감한 환경 변수를 AWS Secrets Manager 또는 Parameter Store에 비밀로 저장하고 컴퓨팅 인스턴스가 이에 액세스할 수 있도록 해야 합니다.
- 위 모든 인프라를 Terraform을 사용하여 정의하고 배포해야 합니다. 우리의 비교적 간단한 앱을 위해 위 요구 사항을 충족하는데는 Terraform의 수백 줄이 필요할 수 있습니다.

와우! 이제 우리 애플리케이션이 배포되었습니다. 하지만 우리가 확인했는지 확인해 보았는지요:

- 우리의 컴퓨팅, 데이터베이스 및 네트워킹 구성에서 보안 취약점이 존재하지 않았나요?
- 각 리소스 사이에 제한된 엑세스만 부여되었나요?
- 인프라를 구성하기 전 애플리케이션 비용을 확신하고 추정했나요?
- 각 클라우드 리소스에 적절한 태그가 지정되었나요?

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

마지막으로 고려해야 할 복잡성의 다른 요소 두 가지가 있습니다:

- Terraform 상태 파일을 원격으로 저장하고 있는지 확인해야 합니다. 그렇다면 s3 버킷과 DynamoDB 인스턴스를 생성해야 합니다.
- 만약 동일한 애플리케이션을 새로운 클라우드 공급업체에 배포하려면, 처음부터 시작하여 해당 새로운 클라우드 공급자의 독특한 특성을 고려해야 합니다.

# Best Practices are Applied Reactively

대부분의 조직이라면 우선 빠르게 애플리케이션을 클라우드로 옮기고, 그런 다음 제3자 도구(오픈 소스 및 상용)를 사용하여 클라우드 인프라를 반응적으로 정리하는 것입니다.

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

- 보안 스캐닝: tfsec, checkov, Wiz, Snyk, Prisma Cloud 등
- 클라우드 비용 최적화: infracost, 다양한 유료 오퍼링 등

넓은 권한이 이미 부여된 상황에서 최소 권한 액세스를 강제하는 것은 특히 식별하고 해결하기 어렵습니다.

## 가장 세련된 팀들은 내부 모듈을 제공합니다

매우 성숙한 Terraform 관리를 제공하는 조직은 개별 개발자가 자체 인프라 요구 사항에 활용할 수 있는 미리 구성된 Terraform 모듈을 제공합니다. 이 접근 방식은 주로 보안 및 비용 문제를 해결하지만 다음 측면에서는 약점이 있습니다:

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

- 개발자들이 올바른 모듈을 선택하는 동안 계속해서 인지 부하가 발생합니다.
- 중요한 추가 투자 없이 여러 클라우드 제공 업체에 확장되지 않습니다.
- 구축 및 유지가 매우 비싸며 결과적으로 이 전략은 주로 규제 검토 수준이 높은 기관(예: 금융 기관)에서 사용됩니다.
- 자동으로 최신 릴리스와 일치하는 모듈을 유지하도록 강제하는 것은 크게 해결하지 못한 문제입니다 (내 지식으로는).

조직 외부에는 공개 모듈이 있지만 가장 잘 만들어진 모듈조차도 상당한 복잡성을 나타내며 적절하게 구성하기는 쉽지 않습니다.

## 근본적으로 반응적인

Terraform의 배포 전에 문제를 예방하기 위해 위의 도구와 전략을 적극적으로 도입했다고 하더라도, 운영 모델은 여전히 근본적으로 반응적입니다. Terraform이 거의 모든 리소스를 거의 모든 방법으로 배포할 수 있게 되는 반응으로, 개발 운영/플랫폼/SRE 엔지니어 팀이 복잡한 내부 서비스를 유지보수해야 합니다.

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

## 미래가 어떻게 보일지

인프라스트럭처를 코드로 다루는 새로운 방식은 기존 방식을 깨끗하게 벗어나야 합니다. 모든 클라우드 자원을 모든 사용 사례에 노출시키는 대신 개발자에게 의견이 강한 클라우드 빌딩 블록 세트를 제공하는 방식으로 클라우드 제공 업체의 세부 정보를 추상화할 수 있는 구성 블록을 제공하는 것이 좋겣습니다.

- 클라우드 제공 업체의 세부 정보를 추상화하는 구성 블록: "네트워크에 컴퓨팅 및 데이터베이스가 필요하다면 AWS에서 실행될 수 있도록 구성해주세요. 배포합니다."
- 고유 방식의 접근 권한 및 보안 모범 사례를 기본적으로 조정 - 이러한 모범 사례를 벗어나 배포할 수있는 옵션이 없어야 합니다.
- 새로운 클라우드 자원을 배포하기 전 통합 비용 추정.
- 조금 더 구현 세부사항이지만, 이러한 도구가 "서버" 모드에서 실행되어 자동으로 drift를 조정하거나 상태 파일 없이 작동할 수 있다면 Terraform을 대규모로 사용할 때 발생하는 일부 문제를 해결할 수 있을 것입니다.

# 다음에는

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

새로운 솔루션이 등장하여 현재 IaC 도구로는 해결하기 어려웠던 이러한 큰 문제들을 해결할 수 있을지는 알 수 없습니다. Terraform는 IaC 영역에서 너무도 우세해져서 새롭고 흥미로운 접근 방식이라도 Terraform을 기반으로 사용하고 있습니다 (예: Wing).

그렇다고 해서 시간이 흐르면서 새로운 IaC 도구들이 나타나서 유연성 대신 의견을, 선택의 역설 대신 간단함을, 반응형 모범 사례 대신 선제적인 것들을 제시하는 방향으로 나아갈 것인지는 시간이 풀어줄 것입니다.

—

궁금한 점이나 생각이 있으시면 댓글로 알려주세요!
