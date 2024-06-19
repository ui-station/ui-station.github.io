---
title: "다중 환경에서 Terraform을 사용하여 AWS Lambda 함수를 배포하는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-DeploymentofAWSLambdaFunctionsAcrossMultipleEnvironmentsUsingTerraform_0.png"
date: 2024-06-19 13:38
ogImage: 
  url: /assets/img/2024-06-19-DeploymentofAWSLambdaFunctionsAcrossMultipleEnvironmentsUsingTerraform_0.png
tag: Tech
originalTitle: "Deployment of AWS Lambda Functions Across Multiple Environments Using Terraform"
link: "https://medium.com/infostrux-solutions/deployment-of-aws-lambda-functions-across-multiple-environments-using-terraform-66374fb6ad36"
---


## 여러 환경에 걸쳐 AWS Lambda 함수를 배포하는 방법 배우기 — Hashicorp Terraform을 통해 가능하게 하기

![이미지](/assets/img/2024-06-19-DeploymentofAWSLambdaFunctionsAcrossMultipleEnvironmentsUsingTerraform_0.png)

AWS Lambda는 서버를 프로비저닝하거나 관리하지 않고 코드를 실행할 수 있는 서버리스 컴퓨팅 서비스로, 배포 프로세스를 간소화하고 비즈니스가 자동으로 확장할 수 있게 합니다. 그러나 특히 여러 환경에 걸쳐 Lambda 함수를 배포하는 것은 개발 라이프사이클을 복잡하게 만들 수 있는 특정한 도전을 야기합니다.

# Lambda 배포 도전


<div class="content-ad"></div>

AWS 람다 함수를 배포하는 가장 기본적인 방법은 주로 AWS 관리 콘솔을 통해 zip 파일을 수동으로 업로드하거나 AWS CLI 명령을 사용하는 것입니다. 그러나 이 방법은 몇 가지 어려움을 야기할 수 있습니다:

- 일관성 문제: 수동 프로세스는 실수하기 쉽습니다. 개발, 테스트 및 프로덕션 환경을 거치는 반복 배포가 신중하게 관리되지 않으면 일관성 문제를 야기할 수 있습니다.
- 확장성 제한: 애플리케이션이 성장함에 따라 여러 함수를 수동으로 관리하는 것은 다루기 어렵고 실수를 유발할 수 있습니다.
- 구성 관리: 다른 환경(예: 개발, 스테이징, 프로덕션)에 대한 다른 구성을 수동으로 처리하는 것은 복잡하며 구성 드리프트를 야기할 수 있습니다.
- 버전 관리: 수동 배포는 기본적으로 버전 관리를 지원하지 않으며, 변경 사항을 추적하거나 이전 버전의 함수로 롤백하는 것이 어려울 수 있습니다.

# Terraform을 활용하여 다중 환경 도전을 해결하기

개발, 스테이징, 프로덕션과 같은 여러 환경에 걸쳐 람다 함수를 배포하려면 구성 및 인프라를 일관되게 처리하는 전략이 필요합니다. 이것이 Terraform이 이러한 문제들에 대처하는 방법입니다:

<div class="content-ad"></div>

- 인프라스트럭처를 코드로 정의함으로써 Terraform은 배포가 다양한 환경에서 반복 가능하고 일관성 있게 이루어지도록 합니다. 이는 "내 컴퓨터에서는 작동한다" 문제를 제거합니다.
- 환경 분리: Terraform은 워크스페이스나 변수 파일을 활용하여 동일한 코드로 여러 환경을 관리할 수 있습니다. 각 환경은 Terraform의 상태 파일을 통해 구성을 관리함으로써 격리를 보장하고 교차 환경 오염의 위험을 최소화합니다.
- 자동화된 배포: Terraform은 자원 생성, 수정 및 삭제 과정을 자동화하여 인간 에러 가능성을 줄이고 효율성을 높입니다.
- 변경 관리와 버전 관리: Terraform을 사용하면 모든 변경 사항이 버전 관리되고 적용되기 전에 검토되어 수정 사항에 대한 감사 트레일을 제공하고 쉬운 롤백 매커니즘을 제공합니다.

# Terraform을 활용한 실용적인 구현

제공된 Terraform 코드는 Lambda 함수용 필요한 인프라를 설정하는 방법을 보여줍니다. IAM 역할, 정책, 그리고 Lambda 함수 자체를 구성하고 Terraform 변수를 사용하여 환경별 구성을 설정합니다.
AWS Terraform 프로바이더에 익숙하지 않은 경우, Terraform을 AWS에 연결하는 방법을 이해하기 위해 해당 문서를 읽어보세요.

1. IAM 역할 및 정책 설정

<div class="content-ad"></div>

람다 함수는 다른 AWS 리소스와 상호작용할 것입니다. 이를 위해 IAM 역할을 가정해야 합니다. 먼저 해당 역할을 만들어 봅시다.

```js
# 람다 함수가 가정할 IAM 역할을 정의합니다.
resource "aws_iam_role" "lambda_role" {
  name               = "lambda_function_role"
  assume_role_policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": "sts:AssumeRole",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Effect": "Allow",
      "Sid": ""
    }
  ]
}
EOF
}
```

## 설명

이 IAM 역할을 생성하고 해당 가정 역할 정책을 첨부함으로써, 람다 함수가 이 역할을 가정할 수 있게 합니다. 즉, 람다 함수는 이 역할과 관련된 권한을 사용하여 다른 AWS 리소스와 상호작용할 수 있습니다. 예를 들어, 이 역할에 S3 버킷에 액세스를 허용하는 정책을 첨부하면, 람다 함수는 이 역할을 사용하여 해당 버킷에서 읽거나 쓰는 작업을 수행할 수 있습니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-DeploymentofAWSLambdaFunctionsAcrossMultipleEnvironmentsUsingTerraform_1.png" />

참고: 본 튜토리얼에서는 환경 변수가 어떻게 사용되는지 간단하게 알 수 있도록 실제 버킷과 AWS 정책은 사용하지 않습니다. 하지만 다음 기사에서 사용할 예정입니다.

# 람다 함수 배포

환경에 따라 다른 변수를 출력하는 기본 "Hello World!" 파이썬 함수를 배포할 것입니다. 람다 패키지는 두 개의 파일로 구성됩니다:

<div class="content-ad"></div>

```js
# index.py, 핸들러가 있는 곳입니다
from config import BUCKET_NAME

def lambda_handler(event, context):
   message = f"버킷 사용 중: {BUCKET_NAME}"
   return {
       'message' : message
   }
```

```js
# config.py, 환경 변수를 가져와 다른 구성을 할 수 있습니다
import os

# 지정되지 않은 경우 'mybucket'으로 기본 설정
BUCKET_NAME = os.getenv('BUCKET_NAME', 'mybucket')
```

먼저, Lambda 함수 코드가 있는 "code" 폴더를 압축하여 배포에 사용할 ZIP 파일로 만들기 위해 Terraform 아카이브 파일 데이터 원본을 정의할 것입니다.

```js
# Lambda 배포를 위해 Python 코드를 zip 파일로 묶습니다.
data "archive_file" "zip_the_code" {
  type        = "zip"
  source_dir  = "./code/"
  output_path = "./code/test-terraform.zip"
}
```

<div class="content-ad"></div>

이제 화면 구조를 명확하게하기 위해 스크린샷을 여기에 넣었습니다.

![폴더 구조 이미지](/assets/img/2024-06-19-DeploymentofAWSLambdaFunctionsAcrossMultipleEnvironmentsUsingTerraform_2.png)

이제 우리는 람다 자원을 생성하기 위해 출력을 사용할 수 있습니다. 여기서 source_code_hash 속성이 매우 중요합니다. 소스 코드 해시 덕분에 Terraform이 코드의 변경을 감지할 수 있습니다.

```js
# 환경별 변수로 Lambda 함수를 배포합니다.
resource "aws_lambda_function" "terraform_lambda_func" {
  filename         = data.archive_file.zip_the_code.output_path
  function_name    = "MyLambdaFunction"
  role             = aws_iam_role.lambda_role.arn
  handler          = "index.lambda_handler"
  source_code_hash = data.archive_file.zip_the_code.output_base64sha256
  runtime          = "python3.8"
  environment {
    variables = {
      BUCKET_NAME = var.BUCKET_NAME
    }
  }
}
```

<div class="content-ad"></div>

여기서 주요 요소는 환경 블록입니다. 이 블록을 정의함으로써 AWS Lambda가 실행 중에 읽을 수 있는 환경 변수인 BUCKET_NAME의 값을 지정할 수 있습니다.

# 여러 환경 관리

여러 환경을 관리하는 방법으로는 Terraform 워크스페이스를 사용하거나 서로 다른 변수 파일로 Terraform을 실행하여 각 환경별로 리소스를 분리하고 상태를 별도로 관리하는 데 도움이 됩니다:

```js
# 새 워크스페이스를 생성하고 개발 환경을 위한 리소스를 배포합니다.
terraform workspace new development
terraform apply -var="BUCKET_NAME=dev-bucket"

# 프로덕션 워크스페이스로 전환하고 리소스를 배포합니다.
terraform workspace new production
terraform apply -var="BUCKET_NAME=prod-bucket"
```

<div class="content-ad"></div>

가끔은 많은 환경 변수를 정의해야 할 때가 있습니다. 그럴 땐 환경에 따라 다른 테라폼 변수 파일을 가리킬 수 있습니다.

## 예시

```js
terraform apply -var-file="dev-variables.tfvars"
terraform apply -var-file="prod-variables.tfvars"
```

## 결론

<div class="content-ad"></div>

AWS Lambda 함수를 여러 환경에 배포하기 위해 Terraform을 사용하면 배포를 표준화하고 서버리스 애플리케이션을 간단하게 관리할 수 있어요.

다음에는 GitHub 액션, CSV 파일 및 적절한 폴더 구조를 사용하여 오늘 본 개념과 기술을 어떻게 제품화하는지 보여 드릴게요.

읽어 주셔서 감사합니다. 저는 Infostrux의 데이터 아키텍트인 Mehdi Sidi Boumedine입니다. LinkedIn에서 저와 연락하거나 팔로우하고 Infostrux 블로그의 최신 기고를 확인해 주세요. 다음 글도 기대해 주세요.