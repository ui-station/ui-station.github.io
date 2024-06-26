---
title: "풀루미 대 테라폼 내 IaC 도구를 선택하는 완전 가이드"
description: ""
coverImage: "/assets/img/2024-05-18-PulumiVSTerraformTheDefinitiveGuidetoChoosingYourIaCTool_0.png"
date: 2024-05-18 16:55
ogImage:
  url: /assets/img/2024-05-18-PulumiVSTerraformTheDefinitiveGuidetoChoosingYourIaCTool_0.png
tag: Tech
originalTitle: "Pulumi V.S. Terraform: The Definitive Guide to Choosing Your IaC Tool"
link: "https://medium.com/4th-coffee/pulumi-v-s-terraform-the-definitive-guide-to-choosing-your-iac-tool-5a602f754439"
---

![Pulumi vs Terraform](/assets/img/2024-05-18-PulumiVSTerraformTheDefinitiveGuidetoChoosingYourIaCTool_0.png)

클라우드 네이티브 시대에는 인프라스트럭처 코드 (IaC)가 클라우드 인프라 관리의 표준이 되었습니다. Terraform은 거의 10년 동안 존재해왔고 경쟁사들이 출현하기 전까지 몇 년 동안 유일한 클라우드-비지니스 옵션이었습니다. 하지만 현재는 AWS CDK, CDK for Terraform과 상대적으로 새로운 Pulumi와 같이 다양한 선택지가 있습니다.

하지만 더 많은 선택지가 있다는 것은 우리의 삶을 쉽게 만들어주지는 않습니다. 의사 결정 프로세스는 복잡할 수 있으며 모든 옵션을 조사하는 데 몇 일 또는 몇 주가 소요될 수 있습니다. 그러나 빠르게 변화하는 클라우드 네이티브/데브옵스 시대에는 그것만 하는 여유가 없는 사람들도 있습니다.

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

걱정하지 마세요: 이 블로그에서는 Pulumi 대 Terraform을 깊이 파헤치고 (AWS CDK/CDK for Terraform의 메커니즘에도 약간 언급할 것입니다) 비교 차트, 의사 결정 트리, 팁 및 FAQ가 포함될 것입니다 (요약: 마지막 두 섹션으로 건너뛰세요) 작업에 적합한 올바른 도구를 선택하는 데 도움을 더할 것입니다.

그럼, 더 이상 미루지 말고 지금 시작해 봅시다.

# 1 Terraform

IaC 도구에 대해 이야기하고 있으므로 Terraform을 무시할 수 없습니다. 가장 오래된 도구 중 하나입니다. 네, AWS CloudFormation과 같이 더 오랜 역사를 가진 클라우드별 솔루션이 있지만 클라우드에 독립적이 아닙니다.

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

그래서, Pulumi에 대해 언급하기 전에 먼저 Terraform을 살펴보겠습니다.

## 1.1 Terraform의 간단한 역사

2014년 HashiCorp에 의해 처음으로 출시된 Terraform은 2016년과 2017년에 많은 관심을 얻기 시작했는데, 이 기간 동안 거의 모든 데브옵스 엔지니어들이 열정적으로 사용하거나 적어도 그에 대해 이야기했습니다.

비록 2021년에 v1.0이라는 공식 버전이 출시되었지만, 특히 2017년부터 2019년 사이의 v0.11 및 v0.12와 같은 이전 버전들은 이미 다양한 비즈니스 분야의 기업들에 의해 크게 받아들여지고 개발 환경 뿐만 아니라 프로덕션 환경에서도 널리 사용되고 있습니다.

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

이 모든 역사는 Terraform을 신뢰할 수 있다는 것을 의미합니다. 오랜 기간 실제 제품 환경에서 테스트되었으며, 그와 같이 입증된 성적표가 있는 Terraform으로는 다른 대안들을 시도할 시간이 없거나 귀찮아도 잘못 갈 수 없습니다.

## 1.2 Terraform의 내부 작업 원리

Terraform을 더 잘 이해하기 위해 (그리고 사실은 모든 IaC 도구에 대해), 다음으로는 Terraform이 어떻게 작동하는지 살펴봅시다: 코어-플러그인 아키텍처.

간단히 말해, 코어는 상태 머신입니다. 현재 인프라 상태를 선언적 코드로 정의된 원하는 상태와 비교하여, 인프라를 선언 적으로 정의된 상태로 가져 오기 위해 해당 변경/작업을 수행 할 수 있도록 계획하게 됩니다.

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

실제 변경 또는 작업은 플러그인(또는 프로바이더라고도 불리며, 이 문맥에서는 같은 것을 가리키는 다른 용어일 뿐입니다)에 의해 수행됩니다. 코어는 특정 상태에서 무엇을 해야 하는지 플러그인과 소통합니다.

요약하면, 코어는 상태를 관리하고 플러그인은 작업을 수행합니다: 이것이 모든 IaC 도구가 동작하는 기본적인 방식입니다. 상태를 관리해야 하며, 클라우드 인프라를 운영하는 데 필요한 작업을 수행해야 합니다.

테라폼에 대해 언급할 가치가 있는 몇 가지 추가 정보는 대부분의 플러그인이 Golang으로 구현된다는 점입니다 (물론 테라폼의 코어-플러그인 프레임워크를 통해 다른 프로그래밍 언어로 작성된 플러그인을 사용할 수 있게 됩니다). 따라서 실제 CRUD 작업을 수행하기 위해 플러그인은 클라우드 Go SDK가 필요합니다.

이 세부 정보가 약간 복잡해 보인다면 걱정하지 마세요. 우리는 테라폼 사용자이니까 (개발자/기여자가 아니라), 플러그인 구현에 대해 고민할 필요가 없습니다. 또한 우리는 Go 코드를 작성하는 게 아니라 HCL만 작성합니다. 우리는 HCL로 인프라를 정의하는데, 실제로 테라폼은 이를 구현하기 위해 Go 플러그인을 호출하도록 어떤 변환을 수행하고, 이 플러그인은 다시 클라우드 Go SDK를 사용합니다.

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

## 1.3 Terraform HCL

테라폼에서 인프라를 정의하는 것은 간단합니다. 구성 언어로 HCL (HashiCorp Configuration Language)을 사용하여 인프라를 정의합니다.

AWS에서 S3 버킷을 생성하는 HCL 예제를 살펴보겠습니다:

```js
resource "aws_s3_bucket" "example" {
  bucket = "my-tf-test-bucket"

  tags = {
    Name        = "My bucket"
    Environment = "Dev"
  }
}
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

위의 구문을 아시는 분이라면 위 문법은 너무 낯설지 않을 것입니다. HCL은 블록과 속성이라는 두 가지 개념을 중심으로 구축된 것이 분명하며, 위 예시에서:

- 전체 리소스 ...은 리소스를 정의하는 블록으로, 첫 번째 키워드가 나타냅니다.
- aws_s3_bucket은 리소스의 유형입니다. AWS 공급자 문서를 참조하여 모든 지원되는 AWS 리소스 목록을 얻을 수 있습니다.
- 예시 부분은 리소스의 이름입니다.
- 블록 내에서 키-값 쌍은 해당 리소스의 속성 또는 인수입니다. 다시 한번, 공급자의 문서를 참조하여 지원되는 인수가 무엇인지, 필수적인지 등을 알아내야 합니다.

HCL에 대한 학습 곡선이 존재하지만, 다른 프로그래밍 언어를 배우는 것만큼 가파르지는 않습니다. 왜냐하면 HCL은 실제 프로그래밍 언어가 아니라 구성 목적으로 사용되기 때문입니다. 이것은 당신의 철학적 성향에 따라 장단점이 될 수 있습니다:

- 한편으로, HCL이 실제 프로그래밍 언어가 아니라 단순한 구성 언어이기 때문에 많은 이점이 있습니다: 기본적으로 키-값 쌍으로, 매우 사람이 읽기 편하고 쉽습니다.
- 다른 한편으로, 단순성 때문에 반복문이나 분기와 같이 복잡한 작업을 쉽게 수행할 수 없습니다(특별한 구문을 사용하여 이를 구현할 수 있지만, 이는 파이썬과 같은 실제 프로그래밍 언어에서 `for ...` 또는 `if/else`를 작성하는 것만큼 간단하지는 않습니다).

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

그래서 IaC 도구로서, Terraform은 다소 중간 정도의 학습 곡선과 제한 사항을 갖고 있습니다 (HCL 때문에 두 가지로 인해: 이를 배워야 하며, 이를 사용하여 인프라를 정의해야 합니다). 이러한 상황을 개선하기 위해 많은 다른 IaC 도구들이 등장했습니다. 계속해서 읽어보세요.

# 2 Pulumi

이전 섹션에서 언급했듯이, Terraform은 완벽하지 않습니다. 이 문제를 해결하기 위해 많은 도구들이 등장했는데, Pulumi는 그 중 가장 최근의 시도 중 하나입니다.

Pulumi가 무엇인가요? 간단히 말해, 이것은 Terraform과 마찬가지로 IaC 도구입니다. 그러나 Terraform이 특정 구문 (HCL)을 사용하는 반면, Pulumi는 거의 모든 프로그래밍 언어를 사용하여 인프라를 정의할 수 있게 합니다.

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

그렇다고 하면 너무나 부정확하고 크게 과장된 발언일 것입니다만, 단 하나의 문장으로 Pulumi가 무엇인지 알고 싶은 초보자를 위해 말하자면: Pulumi는 Python/Go/Java/Node.js 등에서 Terraform과 같은 기능을 하는 것이라고 말할 수 있습니다.

## 2.1 Pulumi의 간단한 역사

Pulumi는 2018년에 처음 오픈소스로 공개되었으며, 이는 그리 새로운 것으로 보이지는 않지만, 현재 버전인 v3는 이전 버전과 비교했을 때 몇 가지 중요한 변경 사항을 갖고 있습니다. 2021년에 릴리스되었으며, 그 이후로 이전에 비해 많은 사람들이 Pulumi를 주목하기 시작했습니다. (믿지 못한다면 Google Trend에 물어보세요.)

오늘날에는 Terraform과 비교했을 때, Google 검색에서 Pulumi에 대한 관심은 훨씬 적습니다. Pulumi의 블로그에 따르면, 2023년에는 고객 수가 2,000명 미만이었는데, 이는 Terraform보다 수십 배 적은 수치입니다. (인터넷의 여러 데이터 소스로부터 도출된 정보에 따르면)

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

그래서, 이것은 새로운 기술이고 널리 수용되지 않았습니다. 그럼에도 불구하고, Pulumi를 선택하는 이유는 무엇일까요? 그 이유는 바로 Pulumi의 가장 강력한 기능인 다국어 지원 때문입니다.

## 2.2 Pulumi: 다국어 지원

Terraform을 선택하면 HCL을 작성해야 합니다. 많은 사람들에게 이것은 부담일 수 있습니다.

예를 들어, Go로 주로 프로그램을 작성하고 가끔 클라우드 인프라를 관리하는 백엔드 엔지니어들은 왜 Go로 인프라를 정의할 수 있는데 HCL을 배워야 할까요?

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

프론트엔드/풀스택 엔지니어들이 JavaScript/TypeScript로 주로 쓰는 경우도 동일합니다. 이미 사용 중인 기술에 HCL을 더하는 것은 부담이 될 뿐만 아니라 기술 스택의 복잡성을 증가시킵니다. 기술 스택에 관해서는 대개 더 작을수록 더 나은 경우가 많습니다.

Pulumi에서는 다음 언어로 인프라를 정의할 수 있습니다:

- TypeScript (Node.js)
- Python
- C#, VB, F# (.NET)
- Go
- Java
- YAML

예를 들어, 이전 섹션에서 언급된 것과 정확히 동일한 AWS S3 버킷을 Pulumi와 Python을 사용하여 생성하려면 간단히 다음과 같이 작성하면 됩니다:

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
import pulumi
import pulumi_aws as aws

bucket = aws.s3.Bucket("bucket",
    acl="private",
    tags={
        "Environment": "Dev",
        "Name": "My bucket",
    })
```

아니면 파이썬이 어울리지 않는다면 TypeScript로 작성해 보고 싶을 수도 있어요:

```js
import * as pulumi from "@pulumi/pulumi";
import * as aws from "@pulumi/aws";

const bucket = new aws.s3.Bucket("bucket", {
  acl: "private",
  tags: {
    Environment: "Dev",
    Name: "My bucket",
  },
});
```

재미있게 사용해보세요!

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

## 2.3 Pulumi의 내부 구조

요약하자면, Pulumi는 Terraform과 마찬가지로 상기된 코어 플러그인 아키텍처를 가지고 작동합니다.

Terraform과 마찬가지로 Pulumi도 내부적으로 클라우드 SDK 및 라이브러리를 사용합니다. Pulumi는 플러그인 자체가 여러 언어로 구현되어 있기 때문에 여러 언어를 지원합니다. 예를 들어, 여기서 Pulumi의 AWS 프로바이더를 살펴보면 다양한 언어로의 여러 구현체를 볼 수 있습니다. 이로 인해 파이썬으로 인프라를 정의할 때는 pulumi_aws를 aws로 가져오고, TypeScript로 한다면 Node.js용으로 완전히 다른 패키지인 “@pulumi/aws”를 사용합니다.

AWS Cloud Development Kit (AWS CDK) 및 Terraform용 CDK (CDKTF)와 같이 많은 언어로 인프라 코드를 정의할 수 있는 다른 옵션이 있음을 언급할 가치가 있습니다. 그러나 기본적으로 AWS CDK와 CDKTF는 다른 방법으로 다중 언어 지원을 구현합니다: AWS CDK와 CDKTF는 JavaScript 클래스와 자연스럽게 상호 작용할 수 있도록 어떤 언어의 코드든 JavaScript 클래스와 상호 작용할 수 있게 하는 라이브러리 jsii에 의존합니다. 따라서 AWS CDK (CDKTF도 마찬가지)는 TypeScript 코드를 다양한 언어로 변환하여 다중 언어를 지원하는 데 jsii를 사용하지만, Pulumi는 그저 여러 언어로 작성된 프로바이더만을 가지고 있습니다.

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

# 3 Pulumi vs. Terraform: 주요 유사점과 차이점

## 3.1 Pulumi와 Terraform의 주요 유사점

가장 큰 유사점은 작동 방식인 코어 플러그인 아키텍처입니다.

먼저, 코어입니다. 혹은 다른 말로 표현하면, 실제로는 상태입니다. 둘 다 현재 인프라 및 코드로 정의된 내용을 기반으로 상태를 유지하기 위해 코어를 사용하여 계산할 수 있도록 하여 인프라를 정의된 상태로 가져오기 위한 작업 계획을 생성할 수 있습니다. 그리고 둘 다 상태를 로컬, S3 버킷, 또는 클라우드/SaaS 솔루션에 저장할 수 있습니다.

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

두 번째로는 플러그인입니다. 이전에도 언급했듯이, 두 툴 모두 상태를 관리하고 변경을 수행하기 위해 핵심 플러그인 아키텍처를 사용합니다.

## 3.2 Pulumi와 Terraform의 주요 차이점

가장 큰 차이는 물론, 멀티 언어 지원입니다.

Terraform은 HCL을 사용하는데, 이것은 완전한 프로그래밍 언어가 아니라 구성 언어에 불과합니다. 그러므로 본질적으로 다른 프로그래밍 언어만큼 많은 작업을 수행할 수 없습니다. 하지만 이전에 언급했듯이, 이것은 당신에게 좋은 점일 수 있는데, 읽기 쉽고 단순한 게 더 나을 수도 있습니다.

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

Pulumi는 다양한 언어를 지원하며 이겪엔 가장 중요한 차이점 중 하나입니다. 어떤 이유로든 Python/Go/Java 또는 기타 주요 프로그래밍 언어로 인프라를 정의해야 하는 경우, Terraform와 Pulumi 사이에는 경쟁이 없습니다.

언급할 가치가 있는 또 다른 차이점은 Pulumi를 사용하여 인프라 코드의 테스트 가능성입니다. Pulumi를 사용하면 프로그래밍 언어와 함께 제공되는 단위 테스트 및 기능 테스트와 도구의 이점을 누릴 수 있습니다. Terraform에서는 주로 통합 테스트로 수행할 수 있는 테스트 방법이 제한됩니다.

물론 Terraform과 Pulumi 사이에는 다수의 세부적인 기능 차이가 있습니다 (놀랍게도 Pulumi 공식 문서에서는 한 둘보다 훨씬 많은 차이점을 찾을 수 있습니다). 그러나 어느 것도 실질적인 결정 요인이 되지는 않습니다. 예를 들어, 오픈 소스 라이선스나 상태의 기본 구성이 어디에 있는지는 아마도 귀하의 목록 상단에 있는 것이 아닐 것입니다.

# Pulumi에 관한 4가지 오해

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

만약 여러분이 이 블로그를 읽고 계시다면, 이겢이 Pulumi 대 Terraform에 관한 첫 번째 기사가 아닐 것입니다; 아마도 여러분은 상당한 조사를 하고 각각의 장단점을 많이 읽은 것 같습니다.

하지만, 다른 많은 기사들에서 Pulumi에 대해 Terraform과 비교했을 때 가장 흔히 언급되는 몇 가지 단점은 사실 오해이며 더는 사실이 아닙니다. 저는 그것들을 다루어서 Pulumi에 대한 공정하고 정확한 견해를 얻을 수 있도록 하고자 합니다. 그것은 중요하기 때문에 별도의 장으로 다루어져야 한다고 생각합니다.

## 4.1 오해 1: Pulumi 문서 부족

이젠 사실이 아닙니다(시작했을 때는 그랬을지도 모릅니다).

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

Pulumi는 설치 방법부터 시작하는 방법까지 매우 상세하고 단계별 설명서를 제공합니다. 더 깊이 알고 싶다면, Pulumi의 핵심 개념에 대한 훌륭한 섹션이 있습니다. 게다가 Pulumi에는 다수의 클라우드 제공업체에 대한 상세한 설명서와 예제가 있습니다.

플러그인/제공자에 대해 특정 제공자(예: AWS) 또는 PagerDuty Pulumi 제공자와 같이 덜 인기 있는 제공자를 검색하고, Terraform의 문서와 비교해 보면 "Pulumi 문서가 부족하다"는 결론에 도달하지 않을 것입니다. 왜냐하면 사실이 아니기 때문이죠.

## 4.2 오해 2: Pulumi 커뮤니티가 작다.

이제 더 이상 사실이 아닙니다.

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

풀루미의 공식 블로그에 따르면, 이 회사는 2000명의 고객과 15만 명의 최종 사용자를 보유하고 있습니다. GitHub에 따르면, 주요 저장소 pulumi/pulumi만으로도 18.8k개 이상의 스타를 받았으며, 1.9k개의 이슈와 184개의 오픈된 풀 리퀘스트가 있습니다.

어떤 지표를 보더라도, 커뮤니티 규모를 측정하는 기준에 상관없이 풀루미의 커뮤니티는 분명히 거대합니다. 테라폼보다는 작을 수 있지만, 절대적으로 말해 그 커뮤니티는 작다고 할 수 없습니다.

## 4.3 오해 3: Pulumi는 모든 환경에 적합하지 않다

다시 한 번, 이는 사실이 아닙니다.

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

많은 사람들이 Pulumi가 더 최근에 나왔기 때문에 모든 분야에 적용되지 않는다고 결론 지을 것으로 생각할 수 있지만, 실제로는 그렇지 않습니다.

프로그래밍 언어에 대해 이야기할 때, Pulumi는 사실 대부분의 주요 언어를 지원합니다.

플러그인 및 프로바이더에 대해 이야기할 때, 새로운 도구가 존재 기간이 짧다는 이유로 플러그인이 적을 것으로 생각할 수 있지만, 현실은 그렇지 않습니다. Pulumi는 모든 주요 퍼블릭 클라우드 프로바이더를 지원합니다. 게다가, 클라우드 이외의 인프라(예: 특정 소프트웨어에서 팀 및 사용자 관리)를 관리할 때도 Terraform과 비슷한 범위를 갖추고 있습니다.

예를 들어, 대기업에서 GitHub, PagerDuty, DataDog, Sentry 등을 관리하는 여러 개의 데브옵스 도구가 있을 수 있습니다. 아마도 이러한 도구들의 사용자/팀/권한을 IaC로 관리하고 싶을 수도 있습니다. 이 경우, 이 도구들의 플러그인을 검색하면 Pulumi가 모두 갖추고 있다는 놀라운 사실을 알게 될 것입니다. 이러한 플러그인은 널리 사용되지 않는 클라우드 관련 플러그인이더라도 Terraform과 마찬가지로 모두 지원합니다.

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

말씀하신 대로, Pulumi/Terraform을 객관적으로 평가하고, "문서가 부족하다" 또는 "커뮤니티가 작다"는 어딘가에서 읽은 이유만으로 Pulumi를 부정적으로 평가하지 말고, 심지어 "최신 버전은 보편성이 낮다"고 생각하지 말아주셨으면 좋겠습니다.

## 5. Pulumi 대 Terraform: 실제로 살펴보기

이전 절에서는 Terraform과 Pulumi의 구문과 사용법을 설명하기 위해 코드 조각들을 제공했지만, 이들은 실세계에서 의미 있는 것을 나타내기에는 너무 간단하고 "Hello World" 수준이었습니다. 실세계에서는 일반적으로 사물이 기하급수적으로 커지기 때문에 코드를 간단하고 가독성 있게 유지하고 동시에 확장 가능하게 유지하는 것이 어렵습니다.

다음으로, Pulumi 대 Terraform을 살펴보고 이들이 이러한 실제 문제에 대처하는 방식을 비교해보겠습니다.

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

## 5.1 Pulumi 대비 Terraform: 코드 구조, 가독성 및 확장성

Terraform에서는 모듈을 정의하고 재사용하여 최대 코드 재사용성을 달성할 수 있습니다. 전형적인 단일식 Terraform 저장소는 다음과 같이 보일 수 있습니다:

```js
.
├── dev
│   ├── config.tf
│   ├── main.tf
│   ├── output.tf
│   └── variables.tf
├── modules
│   ├── module_a
│   └── module_b
└── prod
    ├── config.tf
    ├── main.tf
    ├── output.tf
    └── variables.tf
```

단일 저장소에는 강점이 있습니다. 매우 사람이 읽기 쉽고 설명 없이 쉽게 이해할 수 있습니다. 그리고 Terraform의 특성 상, 디렉토리 구조가 두 단계 또는 최대 세 단계로 설정될 수 있어 한눈에 쉽게 파악하고 관리하기 쉽습니다. 또한, 또 다른 환경을 만드는 것은 기존 저장소에 "test"라는 폴더를 추가하는 것만큼 복잡하지 않습니다.

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

위의 깔끔한 코드 기반을 기준으로 프로젝트가 커질 때 여러 가지 개선 방법이 있습니다. 인프라의 다른 부분들을 서로 다른 저장소로 분리하거나 모듈들을 다른 저장소 하나 또는 몇 개로 이동하거나 서로 다른 환경을 다른 저장소로 이동할 수 있습니다. 모든 선택 사항은 유연하며 모두 새로운 저장소를 만들어 깔끔하고 이해하기 쉬운 디렉토리 구조를 유지합니다.

Pulumi를 사용하면 상황이 약간 복잡해질 수 있습니다. 전형적인 단일체 Pulumi 프로젝트는 다음과 같이 보일 수 있습니다:

```js
.
├── Pulumi.dev.yml
├── Pulumi.prod.yml
├── Pulumi.yml
├── api-gateway
│   ├── index.ts
│   └── micro-service-01
│       └── index.ts
├── database
│   └── table-01.ts
├── index.ts
├── package-lock.json
├── package.json
├── sns
│   └── topics.ts
└── queues.ts
├── pkg
│   └──application
│     └── app.go
└── .etc
```

Terraform과 마찬가지로 일반적인 내용을 패키지로 만들어 프론트엔드 또는 백엔드 프로젝트에서 작업한 것과 같이 처리할 수 있습니다.

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

프로젝트가 커질수록, 프로젝트를 별도로 관리되는 더 작은 프로젝트로 분할하는 마이크로-스택스 접근 방식을 사용할 수 있습니다. 그리고 각 프로젝트는 위에서 설명한 것과 같을 수 있습니다.

그러나 프로젝트가 커지면 디렉토리 구조가 더 복잡해지고 많은 디렉토리와 수준이 더 많아져 혼란스러울 수 있습니다. Java나 혹은 지금까지 경험했던 실제 프로젝트를 상상해보세요. 그 프로젝트 전체를 빠르게 이해하기 쉬운가요? 아니요, 많은 폴더와 디렉토리 수준이 많아서 무엇이 무엇인지 심지어 어디서 시작해야 하는지도 모를 정도입니다.

Pulumi의 가장 강력한 장점은 다국어 지원이지만, 큰 힘은 큰 책임을 수반합니다. 코드를 이해하고 유지 관리하기 쉽게 구성하는 것이 중요하며, 이는 사용하는 도구에 상관 없이 사실입니다.

## 5.2 Pulumi 대 Terraform: 통합

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

대부분의 경우, IaC가 단순히 IaC를 수행하고 끝나는 것이 아닙니다. 인프라 부분은 CI/CD 파이프라인과 같은 다른 요소들과 통합되어야 합니다. 다행히, Terraform과 Pulumi는 변경 사항을 배포하는 데 하나의 명령만 필요하므로 이들은 통합하기에 이상적입니다. 그러나 차이점도 있습니다.

경우에 따라 IaC 파이프라인이 시작되기 전에 뭔가를 수행하고 싶을 수 있습니다.

예를 들어, 사용자, 팀, 멤버십, 권한을 관리하기 위해 IaC를 사용하고 싶다고 가정해 봅시다. 새로운 사용자를 추가할 때, 코드베이스를 열어서 복사하여 수정하고 커밋하는 것은 번거로울 수 있습니다. 사용자 목록을 어딘가에 저장해두고, 그 파일을 검색하여 일부 템플릿 도구를 사용하여 IaC 코드를 자동으로 생성할 수 있습니다.

이 경우, Terraform의 경우 추가 도구(예: 일부 Python 스크립트)에 의존해야 할 수도 있습니다. 파일을 다운로드하고 구문 분석하고 템플릿을 사용하여 생성된 IaC 파일을 커밋한 다음 IaC 파이프라인을 실행하기 전에 모든 작업을 수행해야 할 수 있습니다. Pulumi의 경우, 모든 것을 한 번에 처리할 수 있어서 훨씬 간단할 수 있습니다. 사용할 수 있는 프로그래밍 언어를 사용하여 파일을 다운로드/구문 분석하고, 동일한 언어를 사용하여 for 루프를 통해 간단히 작업을 생성할 수 있습니다.

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

다른 경우에는 IaC가 끝난 후에 무언가를 수행하고 싶을 수 있습니다. 예를 들어, IaC 부분의 출력에 사용하려는 로드 밸런서 URL이 포함되어 있을 수 있습니다. Terraform의 경우 다시 다른 스크립트를 실행해야 할 수도 있지만, Pulumi를 사용하면 IaC 코드 이후에도 그대로 진행하여 이를 수행할 수 있습니다.

간략히 말하면, Terraform과 스크립트를 통합하는 데 어려움을 겪는 경우에는 Pulumi를 사용해 보는 것이 좋을 것입니다.

## 5.3 Pulumi 대 Terraform: 보안

보안은 항상 코드에 중요한 주제이며, 인프라 코드 역시 코드입니다.

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

코드 보안을 위한 가장 기본적인 원칙은 아마도 코드에 비밀을 평문으로 저장하지 않는 것입니다. 이 부분에서 Terraform과 Pulumi는 모두 잘 작동합니다. Terraform은 다양한 시크릿 매니저와 통합될 수 있으며, Pulumi에서는 시크릿 매니저에서 한 줄의 코드로 읽는 것도 쉽습니다. 예를 들어, Terraform에서 시크릿을 관리하는 블로그가 있습니다.

코드 보안에 대해 더 말씀드릴 수 있습니다: Terraform으로 HCL을 작성하면 구성 코드가 생성, 읽기, 업데이트 및 삭제 리소스에 대한 API 호출로 변환됩니다. 물론, Terraform 코어와 플러그인 자체에도 보안 문제와 CVE가 발생할 수 있지만, 다른 IaC 옵션에 대해서도 동일하게 말할 수 있습니다. Pulumi의 경우, IaC 코드를 여러 언어로 작성하고 훨씬 더 많은 작업을 수행할 수 있기 때문에 공격 표면이 증가할 수 있습니다. 이는 Pulumi의 단점으로 보일 수 있지만, 다행히도 인프라 코드를 스캔하는 데 사용되는 최선의 방법과 도구 (SAST 및 DAST와 같은)가 있어서 보안을 강화할 수 있습니다.

Terraform 및 IaC 보안에 관심이 있다면, Terraform을 사용한 IaC 보안에 대한 블로그와 Terraform의 몇 가지 모베스트 프랙티스에 관한 블로그를 읽어보세요 (걱정하지 마세요, 이전에 읽은 것과는 달라요).

코드 보안을 떠나서, IaC 도구는 인프라를 관리하고 중요한 정보가 실제로 상태에 저장되기 때문에 상태의 보안도 중요합니다. Terraform과 Pulumi는 민감한 정보를 상태에 평문으로 출력되지 않도록 암호화할 수 있습니다. 그리고 두 도구 모두 암호화된 상태를 저장하기 위한 다양한 백엔드를 지원합니다.

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

이 섹션을 마무리하기 위한 몇 가지 권장 사항:

- IaC 툴을 사용하여 민감한 데이터를 관리하는 경우(데이터베이스 비밀번호, 사용자 비밀번호 또는 개인 키와 같은), 상태 자체를 민감한 데이터로 처리해야 합니다. 즉:
- 상태를 원격으로 저장하면 더 나은 보안을 제공할 수 있으며 상태에 로컬 디스크를 사용하지 않도록합니다.
- 쉽히 상태 데이터를 암호화할 수 있는 백엔드를 사용하세요.

# 6 Summary: Choosing Your IaC Tool

## 6.1 Comparison Table

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

이 작업을 쉽게 하기 위해 두 가지 주요 기능을 빠르게 비교할 수 있는 다음 표를 보겠습니다:

![Comparison Table](/assets/img/2024-05-18-PulumiVSTerraformTheDefinitiveGuidetoChoosingYourIaCTool_1.png)

## 6.2 IaC 도구 선택

더 재미있게 하기 위해, 다음 플로 차트를 만들어 여러분이 적합한 도구를 선택하는 데 도움이 되도록 하겠습니다:

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

## 6.3 작업에 적합한 도구 선택하기

재미있는 이야기는 떠나고, 작업에 적합한 도구를 선택하는 몇 가지 팁을 제공해 드리겠습니다:

- 여전히 앱 코드를 작성하는 것이 본업이고 인프라 코드를 관리하는 것이 부분적인 업무라면, Pulumi가 더 나은 선택이 될 수도 있습니다.
- 만약 Terraform에 경험이 있고 불만족스럽다면, 의문없이 Pulumi를 시도해 보세요. Terraform으로는 할 수 있는 중요한 일을 Pulumi로 할 수 없는 것은 없습니다.
- 모든 것을 지배할 도구를 선택할 필요는 없습니다. Pulumi 대 Terraform은 싸움이 아니며, 어떤 것이 더 나은지, 무엇이 틀린지를 결정해야 하는 것도 아닙니다. 사실 둘 다 사용할 수 있습니다. 프로젝트가 확장될수록 단일 인프라 저장소를 관리하는 것은 어려워지며, 마이크로서비스 스타일의 인프라 프로젝트를 가지고 있다면, 각 부분에 맞는 올바른 도구를 사용하여 최선의 선택을 할 수 있습니다. 올바른 도구를 선택하면 특정 작업을 더 쉽게 처리할 수 있습니다.
- 직접 체험해 보세요. 블로그와 기사를 읽어도 괜찮지만, 결과적으로 두 도구를 짧게 체험하면 마음이 원하는 것을 알 수 있습니다.

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

# 자주 묻는 질문

## Q: Terraform은 오래된 기술인가요?

네, 그렇습니다.

네, Terraform은 많은 해동안 사용되어 왔습니다. 그러나 약간의 한계도 갖고 있습니다.

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

그러나, HCL을 사용해야 한다는 점을 제외하고는, Terraform은 거의 모든 것을 다 다루며, 그것들을 잘 처리합니다.

Terraform은 다른 프로그래밍 언어로 인프라를 정의할 수 있는 CDKTF를 지원한다는 점을 언급할 가치가 있습니다.

## 질문: Pulumi가 Terraform보다 나은가요?

예와 아니요.

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

네, 다른 프로그래밍 언어를 선택하여 인프라를 정의할 수도 있습니다.

하지만, 완전한 프로그래밍 언어로 큰 프로젝트를 개발하면 간단한 구성 언어보다 이해하기 어렵고 읽기 어려울 수 있습니다.

각 도구는 강점을 가지고 있으며 누가 더 좋은지는 말할 수 없습니다.

## Q: Pulumi가 Terraform이 할 수 있는 모든 것을 할 수 있을까요?

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

네.

음, 그렇게 말해도 될 것 같아요. 두 도구 모두 각자의 독특한 점과 기능을 갖고 있지만, IaC에 관한 기본적인 기능은 두 도구 모두 갖추고 있어요. 만약 Pulumi를 선택하고 나중에 Terraform이 훌륭히 수행하는 몇 가지 마법 같은 기능을 Pulumi가 수행하지 못한다고 알게 되더라도, 불편한 상황에 처할 일은 없을 거에요.

## 질문: Pulumi의 단점은 무엇인가요?

정말 없어요.

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

잘하는 것은 그것이 하는 일을 아주 잘하고, 사용하는 프로그래밍 언어를 선택하여 할 수 있어요. 게다가 문서와 커뮤니티 모두 좋아요.

Pulumi를 조금 더 세밀하게 살펴보자면, Python, Go 또는 JavaScript로 만든 큰 코드베이스를 관리하기가 HCL 구성 파일 저장소보다 어려울 수 있어요. 하지만 이는 Pulumi가 이 난관을 가져온 것이 아니라 프로그래밍 부분 때문이에요. 게다가 HCL을 사용한다고 해서 코드베이스가 자동으로 더 읽기 쉬워지거나 관리가 쉬워지는 것은 아니에요: 분명히 엉망으로 만들 수 있어요. 결국, 깔끔한 코드를 작성하고 유지하는 것은 프로그래머들의 몫이에요.
