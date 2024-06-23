---
title: "Terraform으로 AWS 자원을 올바르게 태깅하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-TaggingAWSresourcestherightwayusingTerraform_0.png"
date: 2024-06-23 22:28
ogImage: 
  url: /assets/img/2024-06-23-TaggingAWSresourcestherightwayusingTerraform_0.png
tag: Tech
originalTitle: "Tagging AWS resources the right way using Terraform"
link: "https://medium.com/@seifeddinerajhi/tagging-aws-resources-the-right-way-using-terraform-dd2cce77be7b"
---



![Tagging AWS resources the right way using Terraform](/assets/img/2024-06-23-TaggingAWSresourcestherightwayusingTerraform_0.png)

# 🔖 소개:

AWS 리소스를 조직화하고 비용을 추적하는 것은 특히 인프라가 확장됨에 따라 어려울 수 있습니다.

리소스에 태그를 붙이는 것은 간단하지만 효과적으로 수행하려면 최선의 방법을 따라야 합니다.


<div class="content-ad"></div>

이 블로그 포스트에서는 인프라스트럭처 코드(IaC) 도구 Terraform을 사용하여 AWS 리소스에 태그를 달는 방법을 안내해 드리겠습니다.

# AWS 리소스에 태그 붙이기 소개:

AWS 리소스에 태그를 붙이는 것은 조직화되고 비용 효율적인 클라우드 인프라를 유지하는 데 중요합니다.

태그는 환경, 애플리케이션, 팀 등과 같은 기준에 따라 리소스를 분류하고 관리하는 키-값 쌍입니다.

<div class="content-ad"></div>

일관된 태깅은 리소스 조직, 비용 할당, 자동화, 보안 및 라이프사이클 관리와 같은 혜택을 제공합니다.

Terraform에서는 프로비저닝하는 동안 리소스에 태그를 달 수 있습니다. 예를 들어, 환경 및 팀 태그와 함께 S3 버킷에 태그를 달기 위해 다음과 같이 할 수 있습니다:

```js
resource "aws_s3_bucket" "example" {
  bucket = "my-bucket"
  tags = {
    Environment = "Production"
    Team = "DevOps"
  }
}
```

또한 모든 리소스에 적용되는 공급자 수준의 기본 태그를 정의할 수도 있습니다.

<div class="content-ad"></div>

```js
공급자 "aws" {
  default_tags {
    tags = {
      Environment = "Production"
      ManagedBy = "Terraform"
    }
  }
}
```

# 리소스 레벨에서 기본 태그 재정의하기:

default_tags 블록을 사용하여 공급자 수준에서 기본 태그를 정의하면 모든 리소스에 자동으로 일반적인 태그가 적용되어 일관성을 유지하고 수동 작업을 줄일 수 있습니다.

기본 태그의 장점


<div class="content-ad"></div>

- 일관성: 모든 리소스에 일관된 태그가 적용되도록 보장합니다.
- 수동 작업 최소화: 리소스 간 반복되는 태그 정의를 피합니다.

예를 들어 AWS 프로바이더에서 기본 태그를 설정하는 방법:

```js
provider "aws" {
  default_tags {
    tags = {
      Environment = "Production"
      ManagedBy   = "Terraform"
    }
  }
}
```

기본 태그를 재정의하거나 추가 태그를 추가하는 방법은 리소스 레벨에서 태그 인수를 지정하여 수행할 수 있습니다.

<div class="content-ad"></div>

이 인수는 제공자 수준에서 정의된 기본 태그보다 우선합니다.

```js
resource "aws_s3_bucket" "example" {
  bucket = "my-bucket"
  
  # 기본 환경 태그를 재정의하고 목적 태그 추가하기
  tags = {
    Environment = "Staging"
    Purpose     = "Data Processing"
  }
}
```

이 예제에서는 환경 태그가 Staging 값으로 재정의되고, 목적 태그가 특히 해당 S3 버킷 리소스에 추가됩니다.

ManagedBy 기본 태그는 여전히 적용됩니다.

<div class="content-ad"></div>

자원 수준 태그 사용 사례

- 환경별 태그 (dev, staging, prod 등)
- 애플리케이션/프로젝트별 태그
- 자원별 메타데이터 태그 (예: 목적, 소유자, 만료일)
- 데이터 민감성에 따른 규정 준수 또는 규제 태그
- 특정 자원에 대한 비용 할당 태그

자원 수준에서 태그 재정의를 허용함으로써 기본 태그의 이점을 유지하면서 각 자원의 특정 요구 사항에 따라 태그를 유연하게 사용할 수 있습니다.

# 유연한 태깅을 위한 변수와 함수 사용:

<div class="content-ad"></div>

테라폼을 사용하면 태그를 변수로 정의하고 merge()와 같은 함수를 사용하여 다른 태그와 결합하여 재사용성과 유연성을 높일 수 있습니다.

변수로 태그를 정의하기:

```js
variable "default_tags" {
  default = {
    Environment = "Production"
    ManagedBy   = "Terraform"
  }
}
```

merge() 함수를 사용하여 태그를 결합하는 방법:

<div class="content-ad"></div>

```kotlin
resource "aws_instance" "example" {
  ami           = "ami-0c94855ba95c71c99"
  instance_type = "t2.micro"

  tags = merge(
    var.default_tags,
    {
      Name    = "ExampleInstance"
      Project = "MyApp"
    }
  )
}
```

merge() 함수는 default_tags 변수와 추가적인 리소스별 태그를 결합하여 모든 네 가지 태그가 EC2 인스턴스에 적용되도록 합니다.

# 특별한 경우 처리:

일부 AWS 리소스는 특정한 태깅 구성이 필요하거나 태그 적용 방법에 제한이 있는 경우가 있습니다.


<div class="content-ad"></div>

태깅 자동 스케일링 그룹:

자동 스케일링 그룹 ASG 및 시작 템플릿 LT를 올바르게 태그 지정하는 것이 까다로울 수 있습니다.

올바른 구성이 없으면 ASG 및 LT에 의해 시작된 EC2 인스턴스 및 연결된 저장소 볼륨에는 기본 태그가 첨부되지 않을 수 있습니다.

ASG는 propagate_at_launch 태그 구성이 필요합니다.

<div class="content-ad"></div>

```js
리소스 "aws_autoscaling_group" "example" {
  # ...
  tag {
    key                 = "Environment"
    value               = "Production"
    propagate_at_launch = true
  }
}
```

런치 템플릿 태깅:

런치 템플릿은 tag_specifications 구성을 필요로 합니다:

```js
리소스 "aws_launch_template" "example" {
  # ...
  
 tag_specifications {
    resource_type = "instance"
    tags = {
      Environment = "Production"
      ManagedBy   = "Terraform"
    }
  }
  tag_specifications {
    resource_type = "volume"
    tags = {
      Persistence = "Permanent"
    }
  }
}
```

<div class="content-ad"></div>

EBS 볼륨 태그 설정하기:

테라폼을 사용하여 Elastic Compute (EC2) 인스턴스를 생성할 때, EC2에 연결된 Elastic Block Store (EBS) 볼륨은 자동으로 태그가 되지 않습니다. 태그되지 않은 EBS 볼륨은 관리가 어렵습니다.

연결된 EBS 스토리지 볼륨에 EC2 기본 태그를 할당하려면 aws_instance의 volume_tags를 사용하세요.

```js
resource "aws_instance" "example" {
  # ...
 
 volume_tags = {
    Name        = "DataVolume"
    Persistence = "Permanent"
  }
}
```

<div class="content-ad"></div>

기타 특수 사례:

일부 리소스는 AMI, NAT 게이트웨이 또는 VPC 엔드포인트와 같이 특정 태그 지정 요구 사항이나 제한 사항이 있을 수 있습니다.

다양한 리소스 유형에 대한 태그 구성에 대한 최신 지침은 항상 Terraform 제공 업체 문서를 참조하십시오.

# 흔한 실수 피하기:

<div class="content-ad"></div>

일관되지 않은 태그 명명 규칙:

appid, app_role 및 AppPurpose과 같은 일관되지 않은 태그 키를 사용하면 태그를 사용하고 관리하기 어려워집니다.

```js
resource "aws_s3_bucket" "example" {
  bucket = "my-bucket"
  tags = {
    appid     = "myapp"
    app_role  = "data-processing"
    AppPurpose = "logs"
  }
}
```

대신, 태그 키 명명을 명확히 정의하고 그 규칙을 준수하도록 유도하세요.

<div class="content-ad"></div>

모든 AWS 리소스를 태그하지 않는 경우, EBS 볼륨과 같은 보조 또는 보완 리소스를 포함해서 태그하지 않는 것은 완전한 시각성과 비용 추적을 할 수 없게 됩니다.

```js
resource "aws_instance" "example" {
  ami           = "ami-0c94855ba95c71c99"
  instance_type = "t2.micro"
  tags = {
    Environment = "Production"
  }
}
```

동일한 기본 및 리소스 태그 문제:

<div class="content-ad"></div>

default_tags 및 리소스 tags에 동일한 tag 키 및 값이 있는 경우, Terraform에서 오류가 발생하여 태그를 중복으로 지우거나 우회 방법을 사용해야 합니다.

```js
provider "aws" {
  default_tags {
    tags = {
      Name = "Example"
    }
  }
}

resource "aws_vpc" "example" {
  tags = {
    Name = "Example" # 오류: 태그가 동일함
    }
}
```

부분적인 태그 일치를 위한 영구 차이:

default_tags 및 리소스 tags가 일부 일치하고 다른 태그가 있는 경우, Terraform은 일치하는 태그를 업데이트하려고 계획할 때마다 영구적인 차이를 표시하여 우회 방법이 필요합니다.

<div class="content-ad"></div>

```js
provider "aws" {
  default_tags {
    tags = {
      Match1 = "A"
      Match2 = "B" 
      NoMatch = "X"
    }
  }
}

resource "aws_vpc" "example" {
  tags = {
    Match1 = "A" # Perpetual diff trying
    Match2 = "B" # to update these
    NoMatch = "Y" 
  }
}
```

인프라스트럭처 드리프트와 태그 손실:

외부에서 Terraform 이외의 방법을 사용하여 리소스를 수정할 때 인프라스트럭처 드리프트로 인해 태그가 손실됩니다. IaC를 일관되게 사용하면이 문제를 완화할 수 있습니다.

# Best Practices and Tips:

<div class="content-ad"></div>

명확한 태깅 전략과 명명 규칙을 수립해야 합니다:

인프라 전반에 걸쳐 사용할 일관된 태그 키와 명명 규칙을 정의하세요.

```js
variable "tag_names" {
  default = {
    environment = "환경"
    application = "응용 프로그램"
    team        = "팀"
    costcenter  = "비용 센터"
  }
}
```

리소스를 프로비저닝할 때 (나중에가 아니라) 태그를 추가하세요:

<div class="content-ad"></div>

리소스를 프로비저닝하는 과정에서 태그를 적용하세요. 이렇게 하면 시작부터 일관된 태깅이 가능합니다.

```js
resource "aws_s3_bucket" "example" {
  bucket = "my-bucket"
  tags = {
    (var.tag_names.environment) = "Production"
    (var.tag_names.application) = "MyApp"
  }
}
```

태그를 정기적으로 검토하고 감사하세요:

주기적으로 리소스 태그를 검토하고 감사하여 태깅 전략을 준수하며 누락된 태그 또는 잘못된 태그를 식별합니다.

<div class="content-ad"></div>

프로비저닝 중에 가능한 경우 태그 자동 적용을 자동화하세요:

테라폼의 default_tags, 변수, 및 기능과 같은 기능을 활용하여 프로비저닝 중에 태그를 자동으로 적용하여 수동 노력을 줄이고 일관성을 유지하세요.

# AWS 리소스 그룹 및 태그 편집기:

모든 리전에 있는 모든 AWS 리소스에서 team=`platform engineering` 태그가 있는 리소스를 찾아볼 수 있다면 어떨까요?

<div class="content-ad"></div>

AWS 리소스 그룹 및 태그 편집기는 여러 AWS 리소스 및 지역에 걸쳐 태그를 효과적으로 관리할 수 있는 강력한 도구입니다.

리소스 그룹:

리소스 그룹은 공유된 태그를 기반으로 AWS 리소스 모음을 구성하고 관리할 수 있는 중앙화된 방법을 제공합니다. 리소스 그룹을 사용하면 다음과 같은 작업을 수행할 수 있습니다:

- 특정 태그(예: team=`platform engineering`)가 적용된 지역 전체의 리소스를 찾을 수 있습니다.
- 태그가 누락되었거나 잘못된 태그 값이 있는 리소스를 식별할 수 있습니다.
- 리소스 그룹 멤버십에 따라 인스턴스 시작/중지 또는 구성 적용과 같은 작업을 자동화할 수 있습니다.
- 그룹 내에서 리소스 상태, 비용 및 구성에 대한 종합적인 정보를 확인할 수 있습니다.

<div class="content-ad"></div>

태그 편집기:

태그 편집기는 지원되는 AWS 서비스 및 지역 전체에 걸쳐 대량 태깅 작업을 가능하게 하는 리소스 그룹의 구성 요소입니다. 태그 편집기를 사용하면 다음을 수행할 수 있습니다:

- 리소스 유형 및 기존 태그에 기반한 리소스 검색을 통해 "팀=`플랫폼 엔지니어링`인 모든 EC2 인스턴스 찾기"와 같은 쿼리를 실행할 수 있습니다.
- 여러 리소스에 대해 태그를 추가, 수정 또는 제거하여 태깅 노력을 효율화할 수 있습니다.
- 변경 사항을 적용하기 전에 미리 보기를 통해 정확성을 확인하고 의도하지 않은 수정을 피할 수 있습니다.
- 태그 기반의 액세스 제어 정책을 사용하여 태그 값에 기반한 리소스 액세스를 관리할 수 있습니다.

# 🔚 결론:

<div class="content-ad"></div>

마크다운 형식으로 테이블 태그를 써야 해요. 

AWS 설정이 조직적이고 비용 효율적으로 이루어지려면, Terraform의 태깅 도구를 사용하는 방법과 일반적인 실수를 피하는 법을 꼭 숙지해야 해요.

읽어주셔서 감사해요! AWS 리소스에 태깅하는 방법에 대해 무언가 배웠으면 좋겠네요!

다음에 또 만나요 🇵🇸 🎉

<img src="/assets/img/2024-06-23-TaggingAWSresourcestherightwayusingTerraform_1.png" />

<div class="content-ad"></div>

읽어 주셔서 감사합니다!! 🙌🏻😁📃 다음 블로그에서 만나요.🤘🇵🇸

🚀 끝까지 함께해 줘서 감사합니다. 이 블로그에 대한 질문/피드백이 있으시면 언제든지 연락해 주세요:

♻️ 🇵🇸LinkedIn: https://www.linkedin.com/in/rajhi-saif/

♻️🇵🇸 Twitter: https://twitter.com/rajhisaifeddine

<div class="content-ad"></div>

제공해주셔서 감사합니다 ✌🏻

# 🔰 계속해서 학습해요!! 지식을 나눠요 !! 🔰

# 참고 문헌: