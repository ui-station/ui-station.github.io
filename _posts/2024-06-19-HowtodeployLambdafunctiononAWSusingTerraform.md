---
title: "AWS에서 Terraform을 사용하여 Lambda 함수를 배포하는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-HowtodeployLambdafunctiononAWSusingTerraform_0.png"
date: 2024-06-19 12:12
ogImage: 
  url: /assets/img/2024-06-19-HowtodeployLambdafunctiononAWSusingTerraform_0.png
tag: Tech
originalTitle: "How to deploy Lambda function on AWS using Terraform"
link: "https://medium.com/@bulutbatuhan/how-to-deploy-lambda-function-on-aws-using-terraform-16f7f1fa930f"
---


서버리스 함수는 인프라 걱정 할 필요 없이 DevOps 및 SysOps에게 필수적입니다.

우리는 Amazon Web Services (AWS) 람다 함수를 살펴보고, 어떻게 테라폼을 사용하여 AWS 람다를 배포할 수 있는지 알아볼 것입니다.

![이미지](/assets/img/2024-06-19-HowtodeployLambdafunctiononAWSusingTerraform_0.png)

모든 자료에 대한 Github 링크: [https://github.com/batuhan-bulut/terraform-aws-lambda](https://github.com/batuhan-bulut/terraform-aws-lambda)

<div class="content-ad"></div>

# AWS Lambda란 무엇인가요?

AWS Lambda를 사용하면 서버에 대해 걱정하지 않고 지원되는 언어로 스크립트를 실행할 수 있습니다. Node.JS, Python, C# 등으로 작성된 코드를 실행할 수 있습니다.

Lambda를 사용하면 다음을 수행할 수 있습니다.
- 지역별 EC2 인스턴스 상태 확인
- AWS CLI를 사용하여 일부 자동화 실행
- AWS SQS로 작업 예약
- 기타

이런 가능성들로 AWS Lambda는 데브옵스 및 시스옵스 팀에 매우 중요한 역할을 합니다.

<div class="content-ad"></div>

AWS 웹 사이트에서 GUI를 사용하여 AWS Lambda를 쉽게 배포할 수 있어요.

여러 계정이 있고 동일한 Lambda를 다른 지역에 배포해야 한다면 어떻게 할 건가요? 하나씩 Lambda를 배포할까요?

이때 IaC와 Terraform이 게임에 합류해요.

# IaC란? (Infrastructure as Code)

<div class="content-ad"></div>

IaC를 사용하면 우리는 쉽게 환경 (개발, 테스트, 스테이징, 프로덕션 등)을 설정할 수 있어요. 인기 있는 공급 업체들은 요구 사항에 맞는 CDK(Cloud Development Kit)를 가지고 있어요.

## 왜 테라폼이 중요한가요?

예를 들어, AWS에 앱이 있고 (EC2, RDS, Lambda, SQS 등을 사용하는) 다양한 서비스를 사용한다고 가정해봅시다. AWS CDK로 이 애플리케이션을 쉽게 배포할 수 있어요.

그런데 만약 경영진이 Azure, GCP 또는 다른 클라우드 공급업체로 전환하기로 결정한다면 어떻게 될까요?

<div class="content-ad"></div>

AWS CDK에 대한 지식 대부분은 중요하지 않아요. 왜냐하면 해당 공급업체가 자체 CDK를 가지고 있거든요.

여기서 Terraform이 우리의 워크플로에 합류하는 곳이에요.

# Terraform이란?

지식과 경험을 통해 Terraform을 사용하면 간단한 명령어로 많은 리소스를 관리할 수 있어요.

<div class="content-ad"></div>

이 문서에서는 Terraform의 AWS 쪽에 중점을 둘 것입니다.

# 불이 켜지면, 카메라 맞춰, 액션!

주의하세요: 이 작업은 AWS 측에 비용을 발생시킬 수 있습니다.

## 우리의 Terraform 코드는 무엇을 할까요?

<div class="content-ad"></div>

- IAM 역할 제공
- IAM 프로필 제공
- 프로필에 IAM 정책 부착
- 업로드용 ZIP 파일 생성
- 람다 함수 생성

이 문서에서는 하드코딩된 값 대신 변수를 사용할 것입니다. 이렇게 하면 다양한 설정으로 동일한 스크립트를 실행할 수 있습니다.

이것은 우리의 terraform/main.tf 파일입니다. 우리 코드의 구조를 담고 있습니다.

```js
provider "aws" {
  region = var.region
}

# AWS Lambda를 위한 IAM 역할
resource "aws_iam_role" "terraform_lambda_iam_role" {
name               = var.terraform_lambda_iam_role.name
assume_role_policy = var.terraform_lambda_iam_role.assume_role_policy
}

# 람다를 위한 새로운 정책 생성
resource "aws_iam_policy" "iam_policy_for_lambda" {
name         = "iam_policy_${var.terraform_lambda_iam_role.name}"
description  = var.iam_policy_for_lambda.description
policy       = var.iam_policy_for_lambda.policy
}

# IAM 정책을 IAM 역할에 부착
resource "aws_iam_role_policy_attachment" "attach_iam_policy_to_iam_role" {
role        = aws_iam_role.terraform_lambda_iam_role.name
policy_arn  = aws_iam_policy.iam_policy_for_lambda.arn
}

# Lambda에 업로드할 ZIP 파일 생성
data "archive_file" "zip_python_lambda_code" {
type        = "zip"
output_path = "${path.module}/python/${var.zip_python_lambda_code.name}.zip"
source_file = "${path.module}/../${var.zip_python_lambda_code.name}.py"
}

# Lambda 함수 생성
resource "aws_lambda_function" "terraform_lambda" {
filename     = "${path.module}/python/${var.zip_python_lambda_code.name}.zip"
function_name  = var.terraform_lambda.name
role        = aws_iam_role.terraform_lambda_iam_role.arn
runtime     = var.terraform_lambda.runtime
handler     = "${var.zip_python_lambda_code.name}.lambda_handler"
}
```

<div class="content-ad"></div>

이 코드 블록에서는 terraform/terraform.tf 파일에서 모든 변수를 읽습니다. 동일한 Terraform 파일을 프로덕션, 스테이징 또는 다른 AWS 지역과 같이 다른 환경에 대해 다양한 구성으로 실행할 수 있습니다.

이 변수들을 읽기 위해서는 변수를 정의해야 합니다. “.tf” 파일을 사용하여 변수를 선언할 수 있습니다.

위 코드에 대한 terraform.tf 파일이 다음과 같이 보입니다.

```js
변수 "region" {
  type = string
  설명 = "AWS 지역"
  기본 = "eu-central-1"
}

변수 "zip_python_lambda_code" {
  type = map(string)
  설명 = "Python 파일의 이름"
  기본 = {
    name = "index"
  }
}

변수 "terraform_lambda_iam_role" {
  type = map(string)
  설명 = "aws_iam_role - terraform_lambda_iam_role 변수"
  기본 = {
    name = "Lambda-from-terraform"
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
}

변수 "iam_policy_for_lambda" {
  type = map(string)
  설명 = "람다를 위한 IAM 정책"
  기본 = {
    description = "Terraform으로 생성된 IAM 정책"
    policy = <<EOF
{
 "Version": "2012-10-17",
 "Statement": [
   {
     "Action": [
       "logs:CreateLogGroup",
       "logs:CreateLogStream",
       "logs:PutLogEvents"
     ],
     "Resource": "arn:aws:logs:*:*:*",
     "Effect": "Allow"
   }
 ]
}
EOF
  }
}

변수 "terraform_lambda" {
  type = map(string)
  설명 = "람다를 위한 변수"
  기본 = {
    name = "Lambda_Terraform"
    runtime = "python3.12"
    handler = "index.lambda_handler"
  }
}
```

<div class="content-ad"></div>

이 파일에서는 변수와 그 기본 값들을 선언합니다. 테라폼에서는 다양한 유형의 변수를 선언할 수 있어요.

더 많은 정보를 원하신다면 테라폼 문서를 확인해보세요.

코드에 기본 값을 사용하고 싶지 않다면 기본 변수를 덮어쓸 terraform/vars.tfvars 파일을 추가할 수도 있어요. 이 파일의 내용은 일반적으로 "key = value" 형식입니다.

여기 .tfvars 파일의 예시가 있어요.

<div class="content-ad"></div>

```js
region = "us-east-1"

terraform_lambda= {
    name = "Override_Name"
    runtime = "python3.9"
    handler = "override.lambda_handler"
}
```

이렇게 하면 Terraform이 region 및 terraform_lambda 변수의 기본값을 재정의할 것입니다.

참고: 이 예와 같이 terraform_lambda와 같은 객체에서 변수를 변경하려면 객체의 모든 변수를 전달해야 합니다. 그렇지 않으면 오류가 발생합니다.

그리고 루트 폴더에 간단한 Python 앱을 index.py라는 이름으로 추가해보세요.

<div class="content-ad"></div>

```js
# 간단한 Hello 함수
import json

def lambda_handler(event, context):
    return {
        'statusCode': 200,
        'body': json.dumps('Lambda에서 안녕하세요!')
    }
```

다음은 폴더가 보이는 예시입니다

<img src="/assets/img/2024-06-19-HowtodeployLambdafunctiononAWSusingTerraform_1.png" />

# 쇼타임!
```

<div class="content-ad"></div>

코드를 실행할 준비가 되었습니다.

먼저 테라폼 구성 파일이 포함된 작업 디렉터리를 초기화하기 위해 terraform init 명령을 실행해야 합니다.

```js
$terraform init

백엔드 초기화 중...

공급자 플러그인 초기화 중...
- 이전 의존성 락 파일에서 hashicorp/aws의 이전 버전 재사용 중
- 이전 의존성 락 파일에서 hashicorp/archive의 이전 버전 재사용 중
- 이전에 설치한 hashicorp/aws v5.54.1 사용 중
- 이전에 설치한 hashicorp/archive v2.4.2 사용 중

테라폼이 성공적으로 초기화되었습니다!

이제 테라폼을 사용할 수 있습니다. 인프라에 필요한 변경 사항을 볼려면 "terraform plan"을 실행해보세요. 이제 모든 테라폼 명령이 작동해야 합니다.

테라폼의 모듈 또는 백엔드 구성을 설정하거나 변경한 경우, 작업 디렉터리를 다시 초기화하려면이 명령을 다시 실행하십시오. 잊어버릴 경우 다른 명령어가 감지하여 필요시 상기시켜줄 것입니다.
```

이후, 인프라 구조의 변경 사항을 확인하기 위해 terraform plan을 실행할 수 있습니다.

<div class="content-ad"></div>

```js
data.archive_file.zip_python_lambda_code: 읽는 중...
data.archive_file.zip_python_lambda_code: 읽기 완료 소요 시간 0초 [id=111]

테라폼은 선택한 공급업체를 사용하여 다음 실행 계획을 생성했습니다. 자원 작업은 다음 기호로 표시됩니다:
  + 생성

테라폼은 다음 작업을 수행할 것입니다:

  # aws_iam_policy.iam_policy_for_lambda가 생성됩니다
  + resource "aws_iam_policy" "iam_policy_for_lambda" {
      + arn              = (적용 후 알려짐)
      + attachment_count = (적용 후 알려짐)
      + description      = "테라폼에 의해 생성된 IAM 정책"
      + id               = (적용 후 알려짐)
      + name             = "iam_policy_Lambda-from-terraform"
      + name_prefix      = (적용 후 알려짐)
      + path             = "/"
      + policy           = jsonencode(
            {
              + Statement = [
                  + {
                      + Action   = [
                          + "logs:CreateLogGroup",
                          + "logs:CreateLogStream",
                          + "logs:PutLogEvents",
                        ]
                      + Effect   = "Allow"
                      + Resource = "arn:aws:logs:*:*:*"
                    },
                ]
              + Version   = "2012-10-17"
            }
        )
      + policy_id        = (적용 후 알려짐)
      + tags_all         = (적용 후 알려짐)
    }

  # aws_iam_role.terraform_lambda_iam_role가 생성됩니다
  + resource "aws_iam_role" "terraform_lambda_iam_role" {
      + arn                   = (적용 후 알려짐)
      + assume_role_policy    = jsonencode(
            {
              + Statement = [
                  + {
                      + Action    = "sts:AssumeRole"
                      + Effect    = "Allow"
                      + Principal = {
                          + Service = "lambda.amazonaws.com"
                        }
                      + Sid       = ""
                    },
                ]
              + Version   = "2012-10-17"
            }
        )
      + create_date           = (적용 후 알려짐)
      + force_detach_policies = false
      + id                    = (적용 후 알려짐)
      + managed_policy_arns   = (적용 후 알려짐)
      + max_session_duration  = 3600
      + name                  = "Lambda-from-terraform"
      + name_prefix           = (적용 후 알려짐)
      + path                  = "/"
      + tags_all              = (적용 후 알려짐)
      + unique_id             = (적용 후 알려짐)
    }

  # aws_iam_role_policy_attachment.attach_iam_policy_to_iam_role 생성됩니다
  + resource "aws_iam_role_policy_attachment" "attach_iam_policy_to_iam_role" {
      + id         = (적용 후 알려짐)
      + policy_arn = (적용 후 알려짐)
      + role       = "Lambda-from-terraform"
    }

  # aws_lambda_function.terraform_lambda 생성됩니다
  + resource "aws_lambda_function" "terraform_lambda" {
      + architectures                  = (적용 후 알려짐)
      + arn                            = (적용 후 알려짐)
      + code_sha256                    = (적용 후 알려짐)
      + filename                       = "./python/index.zip"
      + function_name                  = "Override_Name"
      + handler                        = "index.lambda_handler"
      + id                             = (적용 후 알려짐)
      + invoke_arn                     = (적용 후 알려짐)
      + last_modified                  = (적용 후 알려짐)
      + memory_size                    = 128
      + package_type                   = "Zip"
      + publish                        = false
      + qualified_arn                  = (적용 후 알려짐)
      + qualified_invoke_arn           = (적용 후 알려짐)
      + reserved_concurrent_executions = -1
      + role                           = (적용 후 알려짐)
      + runtime                        = "python3.9"
      + signing_job_arn                = (적용 후 알려짐)
      + signing_profile_version_arn    = (적용 후 알려짐)
      + skip_destroy                   = false
      + source_code_hash               = (적용 후 알려짐)
      + source_code_size               = (적용 후 알려짐)
      + tags_all                       = (적용 후 알려짐)
      + timeout                        = 3
      + version                        = (적용 후 알려짐)
    }

계획: 4개 추가, 0개 변경, 0개 제거.```

앞서 설명한 변경 사항을 적용시키기 위해 terraform apply를 실행할 수 있습니다.

그 후 콘솔에 성공 메시지가 표시될 것입니다. 이는 모든 리소스가 AWS 측에 생성된 것을 의미합니다. AWS GUI에서 람다 함수를 확인하고 실행하거나 CLI에서 람다 함수를 확인할 수 있습니다.

```js
aws lambda list-functions --region us-east-1 | grep Override_Name 

"FunctionName": "Override_Name",
"FunctionArn": "arn:aws:lambda:us-east-1:11111:function:Override_Name",
"LogGroup": "/aws/lambda/Override_Name"
```