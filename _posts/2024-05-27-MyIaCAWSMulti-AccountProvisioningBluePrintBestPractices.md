---
title: "내 IaC AWS 다중 계정 프로비저닝 블루프린트, 최고의 관행"
description: ""
coverImage: "/assets/img/2024-05-27-MyIaCAWSMulti-AccountProvisioningBluePrintBestPractices_0.png"
date: 2024-05-27 17:37
ogImage:
  url: /assets/img/2024-05-27-MyIaCAWSMulti-AccountProvisioningBluePrintBestPractices_0.png
tag: Tech
originalTitle: "My IaC AWS Multi-Account Provisioning BluePrint , Best Practices…"
link: "https://medium.com/@hector-reyesaleman/my-iac-aws-multi-account-provisioning-blueprint-best-practices-4d18e280d403"
---

## SSO 사용자와 함께 Terraform 실행 역할을 가정하는 방법.

이 기사에서는 IaC (Terraform) 프로젝트의 구조를 보여드리겠습니다. 이 프로젝트는 보통 여기저기 흩어져 있는 값 파일이 필요하지 않도록 구성됩니다. 또한 하나의 구성 파일만으로 모든 환경을 제어하는 방법과 플랫폼과 응용프로그램 인프라 코드를 어떻게 분리하는지도 보여드리겠습니다.

## 이 기사에서 다루는 주제들:

- 여러 계정 배포 도구의 우승자인 Terragrunt
- Terraform 및 Terragrunt의 협력
- 응용프로그램 인프라 Terragrunt Main 구성 파일 (Locals/Backend S3/AWS Provider)
- Terraform 실행을 위한 AWS Identity Center (SSO) 권한 집합 (AWS 키와 비밀번호가 영구적이지 않는 역할으로 가정)
- 플랫폼 vs 응용프로그램 인프라
- 플랫폼 인프라 Terragrunt Main 구성 파일 (Locals/Backend S3/AWS Provider)
- Terraform 실행 역할 생성 (응용프로그램 인프라 배포를 위해) IAM 액세스 Identity (SSO)로 프로비저닝된 역할의 신뢰 정책
- 안전하게 SSO AWS 임시 자격 증명 구성
- AWS-VAULT

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

# 테라그런트

내 의견으로 테라그런트는 인프라를 위한 다중 계정 배포 구조화에 사용 가능한 (내가 아는 한) 최고 도구입니다.

테라그런트 문서에 따르면 코드를 다음과 같이 구조화하는 것을 제안합니다:

![테라그런트 구조](/assets/img/2024-05-27-MyIaCAWSMulti-AccountProvisioningBluePrintBestPractices_0.png)

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

그러나 주의해야 할 점은 terragrunt를 자원을 조정하는 데 과도하게 사용하지 않아야 합니다.

Terraform은 여러 모듈을 하나로 번들링하여 응용 프로그램이나 플랫폼 서비스를 사용하는 것을 목적으로 합니다.

Terragrunt는 이미 통합된 Terraform 모듈을 서로 다른 환경에 배포하는 데만 사용해야 합니다.

# 응용 프로그램 모듈을 생성하는 데 Terraform을 사용하고, 다른 환경에 배포하는 데 Terragrunt를 사용하세요.

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

애플리케이션 모듈(왼쪽)을 만들 때 Terragrunt를 사용하지 마세요. 그렇지 않으면 복잡해질 수 있고 테라폼 상태가 앱 단위가 아닌 구성 요소 단위로 생성될 수 있습니다. Terragrunt를 사용하는 경우 배포(오른쪽)에만 사용하세요.

과거에 저희가 한 실수를 피하기 위해 이렇게 말씀 드리는데요. 저는 Terragrunt 내부의 컴포넌트에 너무 많이 나눠 애플리케이션 인프라를 분리하면서 개별 리소스를 더 잘 관리하려고 했지만 더 많은 문제를 발생시켰습니다.

# 왜 이런 문제가 발생할 수 있나요?

저의 몇몇 프로젝트 중 하나에서 저는 Terragrunt에서 Terraform 워크스페이스로 다중 계정 배포 방법을 변경하는 작업을 맡게 되었습니다. 이것은 서로 다른 AWS 환경을 관리하기 위해 조직의 표준 절차에 맞추기 위한 것입니다.

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

이 프로세스는 Terragrunt가 단순히 테라폼 래퍼일 뿐이기 때문에 매우 간단할 수 있었을 것입니다.

하지만 앱을 구성 요소로 분할하고 그 구성 요소가 모듈 내에 존재하면 상태 파일을 병합해야 하는 악몽이 될 수 있습니다.

그래서 Terragrunt 풋프린트를 최소화하려면...

T
erraform은 애플리케이션이나 플랫폼 서비스를 위해 서로 다른 모듈을 번들로 묶을 것으로 의도되어야 합니다.

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

## 예시: 최소한의 애플리케이션 인프라 리소스:

```js
data "aws_caller_identity" "current" {}

locals {
  aws_account_id = data.aws_caller_identity.current.account_id
}

module "app_logs" {
  source = "git::https://github.com/terraform-aws-modules/terraform-aws-dynamodb-table.git//"
  name         = "${var.app_name}-logs"
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "organization"
  range_key    = "job_id"

  server_side_encryption_enabled = true
  deletion_protection_enabled    = false

  attributes = [
    {
      name = "organization"
      type = "S"
    },
    {
      name = "job_id"
      type = "S"
    }
  ]
}

module "app_users" {
  source = "git::https://github.com/terraform-aws-modules/terraform-aws-dynamodb-table.git//"
  # 필요 시 다른 DynamoDB 테이블을 여기에 추가
}

module "lambda_execution_role" {
  source = "../lambda-execution-role"
  aws_account_id = local.aws_account_id
  app_name       = var.app_name
  env            = var.env
}

module "s3_bucket_files" {
  source = "git::https://github.com/terraform-aws-modules/terraform-aws-s3-bucket.git//?ref=v3.6.1"
  bucket                  = var.env == "production" ? "my-company-app-${var.app_name}-files" : "my-company-app-${var.app_name}-files-${var.env}"
  block_public_acls       = "true"
  block_public_policy     = "true"
  ignore_public_acls      = "true"
  restrict_public_buckets = "true"
}

module "another_s3_bucket_files" {
  source = "git::https://github.com/terraform-aws-modules/terraform-aws-s3-bucket.git//?ref=v3.6.1"
  # 필요 시 다른 S3 버켓을 여기에 추가
}

# SSM Params는 서버리스나 다른 도구에 값들을 전달하는 좋은 방법입니다.
module "ssm_params" {
  source = "../ssm-parameters-store"
  parameters = {
    1 = {
      name  = "/app/${var.app_name}/s3_bucket_files"
      value = module.s3_bucket_files.s3_bucket_id
    }
    2 = {
      name  = "/app/${var.app_name}/lambda_role"
      value = module.lambda_execution_role.role_arn
    }
    3 = {
      name  = "/app/${var.app_name}/event_bridge_name"
      value = "${var.app_name}-event-bus"
    }
    4 = {
      name  = "/app/${var.app_name}/logs"
      value = module.app_logs.dynamodb_table_id
    }
  }
}
```

이 모듈에서는 다른 모듈을 호출하여 다음과 같은 종류의 리소스를 생성합니다:

- DynamoDB 테이블
- S3 버켓
- IAM Lambda 실행 역할
- 이전에 생성된 리소스의 ARN 또는 ID를 보관하는 SSM 매개변수

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

여기서 빠진 것은 다른 기사에서 보여준 것처럼 백엔드 S3 및 AWS 제공자 구성입니다... 여기서 Terragrunt가 등장합니다...

# 플랫폼 대 응용 프로그램

이러한 염려 사항의 분리는 서로 다른 GIT 저장소를 가지거나 단순히 동일한 Repo 내에서 다른 프로젝트로 취급함을 의미합니다... 여기서 중요한 것은 응용 프로그램 인프라 코드가 플랫폼 인프라 코드에 직접적인 종속성을 포함해서는 안된다는 것입니다... 따라서 이것은 응용 프로그램 소스 코드(BE/FE)와 함께 쉽게 추출될 수 있습니다...

하지만 우리는 어떻게 알 수 있을까요? 무엇을 플랫폼 인프라에 위치시키고 어떤 것을 애플리케이션 인프라에 위치시켜야 할지요?

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

## 플랫폼 인프라스트럭처:

플랫폼 인프라스트럭처는 보다 포괄적이며 조직 전체에서 여러 응용 프로그램을 지원하는 기본 구성 요소와 서비스를 지칭합니다. 일반적으로 다음을 포함합니다:

- CI/CD용 IAM 역할
- Route53 호스팅 영역
- 네트워킹: VPC, 방화벽 및 NAT와 같은 구성 요소
- 모니터링 및 로깅: Prometheus, Grafana 또는 CloudWatch와 같은 요소

## 응용 프로그램 인프라스트럭처:

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

응용 프로그램 인프라는 특정 응용 프로그램 또는 응용 프로그램 세트를 실행하는 데 필요한 구성 요소와 리소스로 구성됩니다. 이는 종종 다음을 포함합니다:

- EC2 인스턴스, 람다 함수
- 트랜짓 게이트웨이, 로드 밸런서
- 앱 데이터베이스: MySQL, PostgreSQL 또는 MongoDB와 같은 데이터베이스
- 메시지 대기열: SQS 또는 Kafka와 같은 메시지 대기열

## 주요 포인트는 개별 앱의 요구 사항에 맞추는 것입니다.

예를 들어 Serverless Framework나 AWS SAM이 있습니다.

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

서버리스 애플리케이션에 대해서는 테라폼과 서버리스를 사용하는 멋진 가이드가 있어요.

## 테라폼을 사용하지 않고 다른 도구를 사용할 때, 서버리스 프레임워크나 SAM같은:

# 애플리케이션 IaC를 위한 Terragrunt

‘live‘ 디렉토리 내에서 배포할 환경을 지정하세요.

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
├── README.md
├── live
│   ├── _env
│   │   └── app.hcl
│   ├── dev
│   │   └── app
│   │       └── terragrunt.hcl <--- this is not a config file, but an environment resource file
│   ├── production
│   │   └── app
│   │       └── terragrunt.hcl
│   ├── terragrunt.hcl         <------ Terragrunt config file (Unique)
│   └── test
│       └── app
│           └── terragrunt.hcl
└── modules
    ├── lambda-execution-role
    │   ├── data.tf
    │   ├── main.tf
    │   ├── output.tf
    │   └── variables.tf
    ├── application_1
    │   ├── main.tf
    │   └── variables.tf
    └── ssm-parameters-store
        ├── main.tf
        └── variables.tf
```

# 유일무이한 terragrunt.hcl 설정 파일

여기에는 하나의 terragrunt.hcl 구성 파일을 사용하여 멀티 계정 배포를 구성하는 방법이 나와 있어요.

테라폼 워크스페이스 구성과 Terragrunt 구성을 비교해보겠습니다.

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

로컬 변수 정의는 "거의" 같은 상태로 유지되고 있으며, Terragrunt Live 디렉터리를 다른 환경의 이름으로 구조화하고 있습니다.

AWS 환경 이름과 디렉터리 이름이 일치하므로 간단한 정규 표현식으로 실행 시간에 환경 값을 추론할 수 있습니다.

## infrastructure/live/terragrunt.hcl

여기에 완전한 terragrunt.hcl 파일이 있습니다.

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

backend.tf와 provider.tf 생성기에서 "profile" 매개변수를 주목해주세요. 다음 섹션에서 이들을 제거할 예정입니다.

```js
locals {
  # 환경
  env_regex = "infrastructure/live/([a-zA-Z0-9-]+)/"
  aws_env   = try(regex(local.env_regex, get_original_terragrunt_dir())[0])
  # 어플리케이션
  app_name = "my-app"
  component = "api"

  # AWS 조직 계정
  account_mapping = {
    dev             = 22222222222
    test            = 111111111111
    production      = 000000000000
    shared-services = 555555555555
  }
  # 가정할 IAM 역할
  account_role_name     = "apps-terraform-execution-role" # <--- 가정할 역할
  # 지역 및 가용 영역
  region = "us-east-1"
}
remote_state {
  backend = "s3"
  generate = {
    path      = "backend.tf"
    if_exists = "overwrite_terragrunt"
  }
  config = {
    bucket         = "terraform-state-shared-services"
    key            = "${local.app_name}/${get_path_from_repo_root()}/terraform.tfstate"
    region         = local.region
    profile        = "shared-services" #<----- AWS 프로필
    encrypt        = true
    dynamodb_table = "shared-services-lock-table"
  }
}
generate "provider" {
  path      = "provider.tf"
  if_exists = "overwrite_terragrunt"
  contents  = <<-EOF
    provider "aws" {
      region = "${local.region}"
      profile = "shared-services" #<----- AWS 프로필
      allowed_account_ids = [
        "${local.account_mapping[local.aws_env]}"
      ]
      assume_role {
        role_arn = "arn:aws:iam::${local.account_mapping[local.aws_env]}:role/${local.account_role_name}"
      }
      default_tags {
        tags = {
          Environment = "${local.aws_env}"
          ManagedBy   = "terraform"
          DeployedBy  = "terragrunt"
          Creator     = "${get_env("USER", "NOT_SET")}"
          Application = "${local.app_name}"
          Component   = "${local.component}"
        }
      }
    }
EOF
}
```

Terragrunt가 생성기에 보간을 허용하므로 모든 대상 AWS 계정에 대해 매개변수화할 수 있습니다.

## infrastructure/live/\_env/app.hcl

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

테이블 태그를 마크다운 형식으로 변경해주세요.

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

## Terraform 프로비저닝 인프라에 필요한 역할 설정하기

```js
account_role_name     = "apps-terraform-execution-role" # <--- 전환할 역할
```

또한 이전 게시물에서 AWS 공유 서비스 계정에 terraform-multiaccount-role 및 각 작업 부하 계정(개발, 테스트, 프로덕션)에 terraform-role을 설정하는 방법을 설명했습니다.

그러나 이 설정은 하나의 엔터티(terraform-multiaccount-role)가 모든 계정에서 terraform-role을 가정(Assume)할 수 있도록 허용합니다. 이는 개발팀이 자체 인프라를 개발 환경에서 관리할 수 있도록 하려는 경우에는 편리하지 않습니다. 따라서 개발 팀이 Dev 계정에서만 역할을 전환할 수 있는 엔터티와 Test 및 Production에 대한 다른 엔터티가 필요합니다.

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

## AWS Identity Center 위임된 관리자 계정 및 권한 집합

AWS 계정에 권한 집합을 만드는 방법 및 권한 집합을 AWS 계정에 할당하는 방법에 대한 문서를 따르세요.

다음과 같은 권한 집합을 만들어 보세요: terraform-dev, terraform-test, terraform-production.

각 권한 집합에 대한 대상 계정에 다음과 같은 내부 정책을 만드세요. 허용하는 권한 집합(Identity 계정에 설정된)이 공유 서비스 계정에 배포되어 다음 계정에서 역할을 가정할 수 있도록합니다:

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

이렇게 보이는 3가지 다른 권한 세트가 있습니다:

```js
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "arn:aws:iam::000000000000:role/apps-terraform-execution-role"
        },
        {
            "Sid": "Statement1",
            "Effect": "Allow",
            "Action": [
              "s3:GetObject",
              "s3:PutObject",
              "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::terraform-state-shared-services*",
                "arn:aws:s3:::terraform-state-shared-services/*"
            ]
        },
        {
            "Sid": "Statement2",
            "Effect": "Allow",
            "Action": [
              "dynamodb:GetItem",
              "dynamodb:PutItem",
              "dynamodb:DeleteItem"
            ],
            "Resource": [
                "arn:aws:dynamodb:us-east-1:555555555555:table/shared-services-lock-table"
            ]
        }
    ]
}
```

각 워크로드 계정에 apps-terraform-execution-role을 만들고 SSO 사용자가 역할을 할 수 있도록 허용합니다.

참고: "terraform-production"에게도 Test에서 역할을 수행할 수 있도록 정책을 약간 조정할 수도 있습니다.

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

# 플랫폼 인프라 (IaC)

최소한의 애플리케이션을 위해 DNS, 네트워킹 및 식별 관리 역할 및 정책과 같은 플랫폼 리소스를 제공해야 합니다.

```js
➜  live git:(main) ✗ tree
.
├── _env
│   ├── route53-zones.hcl
│   └── iam-roles.hcl
├── dev
│   ├── iam-roles
│   │   └── terragrunt.hcl
│   ├── route53-zones
│   │   └── terragrunt.hcl
│   └── platform
│       └── terragrunt.hcl
├── network
│   ├── dmz
│   │   └── terragrunt.hcl
│   ├── network-egress
│   │   └── terragrunt.hcl
│   ├── transitgateway-dmz-vpc-attachment
│   │   └── terragrunt.hcl
│   └── transitgateway-egress-vpc-attachment
│       ├── mystate.tfstate
│       └── terragrunt.hcl
├── production
│   ├── iam-roles
│   │   └── terragrunt.hcl
│   └── platform
│       └── terragrunt.hcl
├── terragrunt.hcl
└── test
    ├── iam-roles
    │   └── terragrunt.hcl
    ├── route53-zones
    │   └── terragrunt.hcl
    └── platform
        └── terragrunt.hcl
```

## 로컬 변수

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

여러 개의 계정 값들을 하나의 장소에서 중앙화하는 방법의 예시입니다:

```js
locals {
  # 환경 변수
  env_regex = "infrastructure/live/([a-zA-Z0-9-]+)/"
  aws_env   = try(regex(local.env_regex, get_original_terragrunt_dir())[0])
  # 어플리케이션
  app_name  = "my-application-platform"
  component = "platform"
  platform_apps = [
    "application_1",
    "application_2",
    "application_3"
  ]
  # AWS 조직 계정
  application_account_mapping = {
    dev        = 222222222222
    test       = 111111111111
    production = 000000000000
  }
  platform_account_mapping = {
    shared-services = 555555555555
    network         = "666666666666"
    dns             = 888888888888
  }
  account_mapping = merge(local.application_account_mapping, local.platform_account_mapping)

  # 가정할 IAM 역할
  account_role_name     = "terraform-role"
  multiaccount_role_arn = "arn:aws:iam::555555555555:role/terraform-multiaccount-role" # shared-services

  terraform_execution_role_mapping = {
    dev = "arn:aws:iam::555555555555:role/aws-reserved/sso.amazonaws.com/AWSReservedSSO_terraform-dev_ab1"
    test = "arn:aws:iam::555555555555:role/aws-reserved/sso.amazonaws.com/AWSReservedSSO_terraform-test_ab2"
    production = "arn:aws:iam::555555555555:role/aws-reserved/sso.amazonaws.com/AWSReservedSSO_terraform-production_ab3"
  }

  # 기타 플랫폼 구성 값들
  # ===================================
  # DNS
  main_public_dns_zone_name     = "example.com"
  env_public_dns_zone_subdomain = "${local.aws_env}"
  environment_hosted_zone_name  = "${local.env_public_dns_zone_subdomain}.${local.main_public_dns_zone_name}"
  acm_subject_alternative_names = [
    "${local.environment_hosted_zone_name}",
    "*.${local.environment_hosted_zone_name}",
    "*.api.${local.environment_hosted_zone_name}",
    "*.myapps.${local.environment_hosted_zone_name}",
    "*.apps.${local.environment_hosted_zone_name}"
  ]
  # 지역 및 가용 영역
  region = "us-east-1"
  azs = [
    "us-east-1a",
    "us-east-1b",
    "us-east-1c"
  ]
  platform_accounts = {
    dev = {
      cidr = "10.3.0.0/16"
      private_subnets = [
        "10.3.1.0/24",
        "10.3.2.0/24",
        "10.3.3.0/24"
      ]
    }
    test = {
      cidr = "10.2.0.0/16"
      private_subnets = [
        "10.2.1.0/24",
        "10.2.2.0/24",
        "10.2.3.0/24"
      ]
    }
    production = {
      cidr = "10.0.0.0/16"
      private_subnets = [
        "10.0.1.0/24",
        "10.0.2.0/24",
        "10.0.3.0/24"
      ]
    }
  }
}
```

```js
include "root" {
  path = find_in_parent_folders()
}

include "env" {
  path = "../../_env/iam-roles.hcl"
}
```

```js
locals {
  env_vars = read_terragrunt_config(find_in_parent_folders("terragrunt.hcl"))
  env      = local.env_vars.locals.aws_env
}

terraform {
  source = "../../../modules//platform/iam-roles"
}

inputs = {
  trusted_role_arns = [
    local.env_vars.locals.terraform_execution_role_mapping[local.env]
  ]
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

## infrastructure/modules/platform/iam-roles

"Iam:_"에 대한 범위를 좁히고 있음을 알려드립니다. 이는 Terraform이 응용 프로그램을 위해 Role 및 정책을 생성, 제거 및 업데이트해야하기 때문에 필요한 권한입니다. 그러나 이 경우 ":role/app/_" 내의 Roles 및 Policies로 범위를 좁힐 수 있습니다.

```json
data "aws_caller_identity" "current" {}
module "app_terraform_execution_role" {
  source                            = "../../iam-role"
  role_name                         = "apps-terraform-execution-role"
  create_role                       = true
  role_requires_mfa                 = var.role_requires_mfa
  path                              = "/"
  description                       = "Terraform Execution role"
  trusted_role_arns                 = var.trusted_role_arns
  number_of_custom_role_policy_arns = 1
  policy = jsonencode(
    {
      "Version" : "2012-10-17",
      "Statement" : [
        {
          "Action" : var.app_allowed_actions,
          "Effect" : "Allow",
          "Resource" : "*",
          "Condition" : {
            "StringEquals" : {
              "aws:RequestedRegion" : "us-east-1"
            }
          }
        },
        {
          "Action" : [
            "iam:*"
          ],
          "Effect" : "Allow",
          "Resource" : "arn:aws:iam::${data.aws_caller_identity.current.account_id}:role/app/*"
        }
      ]
  })
}

variable "app_allowed_actions" {
  default = [
    "cloudwatch:*",
    "iam:List*",
    "iam:Get*",
    "iam:Describe*",
    "logs:*",
    "logs:ListTagsLogGroup",
    "s3:*",
    "secretsmanager:*",
    "ses:*",
    "sns:*",
    "ssm:*",
    "dynamodb:*",
    "sts:*",
    "route53:*"
  ]
}
```

```json
module "iam_policy" {
  source      = "git::https://github.com/terraform-aws-modules/terraform-aws-iam.git//modules/iam-policy"
  path        = var.path
  name        = "${var.role_name}-policy"
  description = var.description
  policy      = var.policy
}

module "iam_assumable_role" {
  source            = "git::https://github.com/terraform-aws-modules/terraform-aws-iam.git//modules/iam-assumable-role"
  create_role       = var.create_role
  role_name         = var.role_name
  role_requires_mfa = var.role_requires_mfa
  trusted_role_arns = var.trusted_role_arns
  custom_role_policy_arns = [
    module.iam_policy.arn
  ]
  number_of_custom_role_policy_arns = var.number_of_custom_role_policy_arns
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

# AWS 인가: SSO를 사용할 예정이므로 장기적으로 유효한 자격 증명이 필요하지 않아요 🥳

# 로컬 구성 프로파일 구성 (~/.aws/config)

## 플랫폼

요기 못생긴 닭과 계란 문제가 있는데... 깔끔한 AWS 계정이 있고, 플랫폼 인프라 프로비저닝에 권한을 부여해야 해요. 이건 IaC를 사용할 수 없어서 할 수 없는 문제에요.

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

AWS 콘솔이나 CLI를 사용하여 Terraform 플랫폼에 대한 IAM 또는 IAM Identity Center 사용자를 수동으로 설정해야 합니다. 설정이 완료되면 IaC를 프로비저닝할 수 있습니다.

애플리케이션 프로비저닝을 위한 모든 역할과 정책(CICD 측면을 고려한)은 플랫폼 IaC에서 생성됩니다. 애플리케이션 실행을 위한 모든 역할과 정책(예: 람다 실행 역할)은 응용 프로그램 코드 내에서 생성됩니다.

```js
[profile terraform-platform]
sso_start_url=https://my-organization.awsapps.com/start
sso_region=us-east-1
sso_account_id=555555555555
sso_role_name=PlatformTerraform
region=us-east-1
output=json
```

## 응용 프로그램 리소스 (개발자가 쓰기 액세스를 얻는 곳)

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

<img src="/assets/img/2024-05-27-MyIaCAWSMulti-AccountProvisioningBluePrintBestPractices_1.png" />

```js
[profile terraform]
sso_start_url=https://my-organization.awsapps.com/start
sso_region=us-east-1
sso_account_id=555555555555
sso_role_name=terraform-dev
region=us-east-1
output=json

[profile terraform-test]
sso_start_url=https://my-organization.awsapps.com/start
sso_region=us-east-1
sso_account_id=555555555555
sso_role_name=terraform-test
region=us-east-1
output=json

[profile terraform-production]
sso_start_url=https://my-organization.awsapps.com/start
sso_region=us-east-1
sso_account_id=555555555555
sso_role_name=terraform-production
region=us-east-1
output=json
```

# AWS Backend & Provider Config

어머머... 휴스턴, 문제 발생했어요.... AWS 계정 별로 다른 권한 세트가 있어요... 이제 더 이상 `profile = shared-services`를 사용할 수 없게 됐네요.

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

혹시 매핑할까요? 아니면 환경과 접두사를 추가할까요?….

```js
remote_state {
  backend = "s3"
 .....
  }
  config = {
   ........

    # 여기가 문제입니다
    profile        = "terraform" # 혹은 "terraform-test" 또는 "terraform-production"
  }
}

generate "provider" {
  path      = "provider.tf"
  if_exists = "overwrite_terragrunt"
  contents  = <<-EOF
    provider "aws" {
      ...
      ....
      profile = "terraform" # 혹은 "terraform-test" 또는 "terraform-production"
    }
EOF
}
```

## 이것을 제거하고 환경 변수로 설정합시다

```js
locals {
  # ENV
  env_regex = "infrastructure/live/([a-zA-Z0-9-]+)/"
  aws_env   = try(regex(local.env_regex, get_original_terragrunt_dir())[0])
  # Application
  app_name  = "my-application-platform"
  component = "platform"
  platform_apps = [
    "application_1",
    "application_2",
    "application_3"
  ]
  # AWS Organizations Accounts
  application_account_mapping = {
    dev        = 222222222222
    test       = 111111111111
    production = 000000000000
  }
  platform_account_mapping = {
    shared-services = 555555555555
    network         = "666666666666"
    dns             = 888888888888
  }
  account_mapping = merge(local.application_account_mapping, local.platform_account_mapping)

  # IAM Roles to Assume
  account_role_name     = "terraform-role"
  multiaccount_role_arn = "arn:aws:iam::555555555555:role/terraform-multiaccount-role" # shared-services

  terraform_execution_role_mapping = {
    dev = "arn:aws:iam::555555555555:role/aws-reserved/sso.amazonaws.com/AWSReservedSSO_terraform-dev_ab1"
    test = "arn:aws:iam::555555555555:role/aws-reserved/sso.amazonaws.com/AWSReservedSSO_terraform-test_ab2"
    production = "arn:aws:iam::555555555555:role/aws-reserved/sso.amazonaws.com/AWSReservedSSO_terraform-production_ab3"
  }

  # Other Platform Config Values Below
  # ===================================
  # DNS
  main_public_dns_zone_name     = "example.com"
  env_public_dns_zone_subdomain = "${local.aws_env}"
  environment_hosted_zone_name  = "${local.env_public_dns_zone_subdomain}.${local.main_public_dns_zone_name}"
  acm_subject_alternative_names = [
    "${local.environment_hosted_zone_name}",
    "*.${local.environment_hosted_zone_name}",
    "*.api.${local.environment_hosted_zone_name}",
    "*.myapps.${local.environment_hosted_zone_name}",
    "*.apps.${local.environment_hosted_zone_name}"
  ]
  # Region and Zones
  region = "us-east-1"
  azs = [
    "us-east-1a",
    "us-east-1b",
    "us-east-1c"
  ]
  platform_accounts = {
    dev = {
      cidr = "10.3.0.0/16"
      private_subnets = [
        "10.3.1.0/24",
        "10.3.2.0/24",
        "10.3.3.0/24"
      ]
    }
    test = {
      cidr = "10.2.0.0/16"
      private_subnets = [
        "10.2.1.0/24",
        "10.2.2.0/24",
        "10.2.3.0/24"
      ]
    }
    production = {
      cidr = "10.0.0.0/16"
      private_subnets = [
        "10.0.1.0/24",
        "10.0.2.0/24",
        "10.0.3.0/24"
      ]
    }
  }
}
remote_state {
  backend = "s3"
  generate = {
    path      = "backend.tf"
    if_exists = "overwrite_terragrunt"
  }
  config = {
    bucket         = "terraform-state-shared-services"
    key            = "${local.app_name}/${get_path_from_repo_root()}/terraform.tfstate"
    region         = local.region
    encrypt        = true
    dynamodb_table = "shared-services-lock-table"
  }
}

generate "provider" {
  path      = "provider.tf"
  if_exists = "overwrite_terragrunt"
  contents  = <<-EOF
    provider "aws" {
      region = "${local.region}"
      allowed_account_ids = [
        "${local.account_mapping[local.aws_env]}"
      ]
      assume_role {
        role_arn = "arn:aws:iam::${local.account_mapping[local.aws_env]}:role/${local.account_role_name}"
      }
      default_tags {
        tags = {
          Environment = "${local.aws_env}"
          ManagedBy   = "terraform"
          DeployedBy  = "terragrunt"
          Creator     = "${get_env("USER", "NOT_SET")}"
          Application = "${local.app_name}"
          Component   = "${local.component}"
        }
      }
    }
EOF
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

# 배포

## 플랫폼

```js
aws sso login --profile terraform-platform
export AWS_PROFILE=terraform-platform

# 개발
cd infrastructure/live/dev/iam-roles
terragrunt init
terragrunt plan
terragrunt apply

# 테스트
cd infrastructure/live/test/iam-roles
terragrunt init
terragrunt plan
terragrunt apply

# 프로덕션
cd infrastructure/live/production/iam-roles
terragrunt init
terragrunt plan
terragrunt apply
```

## 어플리케이션

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

여기서는 방금 만든 새 역할을 사용합니다.

```js
# 개발
cd infrastructure/live/dev/app
aws sso login --profile terraform-dev
export AWS_PROFILE=terraform-dev
terragrunt init
terragrunt plan
terragrunt apply

# 테스트
cd infrastructure/live/test/app
aws sso login --profile terraform-test
export AWS_PROFILE=terraform-test
terragrunt init
terragrunt plan
terragrunt apply

# 프로덕션
cd infrastructure/live/production/app
aws sso login --profile terraform-production
export AWS_PROFILE=terraform-production
terragrunt init
terragrunt plan
terragrunt apply
```

# AWS Vault

AWS Vault은 개발 환경에서 AWS 자격 증명을 안전하게 저장하고 액세스하는 도구입니다.

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

AWS Vault은 IAM 자격 증명을 사용자의 운영 체제 안전한 키 저장소에 저장한 다음 해당 자격 증명을 임시 자격 증명으로 생성하여 쉘 및 응용 프로그램에 노출합니다. 이는 AWS CLI 도구와 함께 보조적으로 사용되도록 설계되었으며 ~/.aws/config에 있는 프로필과 구성을 인식합니다.

```js
brew install --cask aws-vault
```

## 따라서 이는 백엔드 구성 및 공급자에서 "프로필" 매개변수를 제거할 수 있음을 의미합니다.

# AWS-Vault로 배포하기

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

# 플랫폼

```js
# Dev
cd infrastructure/live/dev/iam-roles
aws-vault exec terraform-platform terragrunt init
aws-vault exec terraform-platform terragrunt plan
aws-vault exec terraform-platform terragrunt apply

# 테스트
cd infrastructure/live/test/iam-roles
aws-vault exec terraform-platform terragrunt init
aws-vault exec terraform-platform terragrunt plan
aws-vault exec terraform-platform terragrunt apply

# 프로덕션
cd infrastructure/live/production/iam-roles
aws-vault exec terraform-platform terragrunt init
aws-vault exec terraform-platform terragrunt plan
aws-vault exec terraform-platform terragrunt apply
```

# 애플리케이션

```js
cd infrastructure/live/dev/app
aws-vault exec terraform-dev terragrunt init
aws-vault exec terraform-dev terragrunt plan
aws-vault exec terraform-dev terragrunt apply

cd infrastructure/live/test/app
aws-vault exec terraform-test terragrunt init
aws-vault exec terraform-test terragrunt plan
aws-vault exec terraform-test terragrunt apply

cd infrastructure/live/production/app
aws-vault exec terraform-production terragrunt init
aws-vault exec terraform-production terragrunt plan
aws-vault exec terraform-production terragrunt apply
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

<img src="/assets/img/2024-05-27-MyIaCAWSMulti-AccountProvisioningBluePrintBestPractices_2.png" />

## 결과

```js
 $ aws-vault exec terraform-production terragrunt apply



백엔드 초기화 중...

백엔드 "s3"이 성공적으로 구성되었습니다! 백엔드 구성이 변경되지 않는 한 Terraform은
자동으로이 백엔드를 사용할 것입니다.
모듈 초기화 중...
jobs_logs(을)를 위해 git::https://github.com/terraform-aws-modules/terraform-aws-dynamodb-table.git을(를) 다운로드 중...
- .terraform/modules/logs에 jobs_logs
- ../lambda-execution-role에 lambda_execution_role
s3_bucket_files(을)를 위해 git::https://github.com/terraform-aws-modules/terraform-aws-s3-bucket.git?ref=v3.6.1을(를) 다운로드 중...
- .terraform/modules/s3_bucket_files에 s3_bucket_files
- ../ssm-parameters-store에 ssm_params

제공자 플러그인 초기화 중...
- 종속성 잠금 파일에서 이전 버전의 hashicorp/aws 재사용
- hashicorp/aws v5.23.1 설치 중...
- hashicorp/aws v5.23.1 설치됨 (HashiCorp에서 서명됨)

Terraform이 성공적으로 초기화되었습니다!

이제 Terraform을 사용하여 작업을 시작할 수 있습니다. "terraform plan"을 실행하여
인프라에 필요한 모든 변경 사항을 확인해 보세요. 이제 모든 Terraform 명령이
작동해야 합니다.

Terraform을 위해 모듈이나 백엔드 구성을 설정 또는 변경한 경우,
작업 디렉터리를 다시 초기화하려면이 명령을 다시 실행하세요. 잊어 버린 경우
다른 명령이 필요한 경우이를 감지하고 다시 실행하라는 메시지가 표시됩니다.
상태 잠금 획득 중. 이 작업은 몇 분 정도 걸릴 수 있습니다...
module.lambda_execution_role.data.aws_iam_policy_document.lambda_assume_role_policy: 읽는 중...
module.s3_bucket_files.data.aws_caller_identity.current: 읽는 중...
module.s3_bucket_files.data.aws_canonical_user_id.this: 읽는 중...
data.aws_caller_identity.current: 읽는 중...
module.s3_bucket_files.aws_s3_bucket.this[0]: 상태 새로 고침 중... [id=my-company-myapp-files-dev]
module.jobs_logs.aws_dynamodb_table.this[0]: 상태 새로 고침 중... [id=my-company-myapp-logs]
module.lambda_execution_role.data.aws_iam_policy_document.lambda_assume_role_policy: 0초 후에 읽기 완료 [id=000000000]
data.aws_caller_identity.current: 0초 후에 읽기 완료 [id=000000000]
module.lambda_execution_role.data.aws_iam_policy_document.inline_policy: 읽는 중...
module.s3_bucket_files.data.aws_caller_identity.current: 0초 후에 읽기 완료 [id=000000000]
module.lambda_execution_role.data.aws_iam_policy_document.inline_policy: 0초 후에 읽기 완료 [id=000000000]
module.lambda_execution_role.aws_iam_role.lambda_execution_role: 상태 새로 고침 중... [id=my-company-myapp-lambda-execution-role]
module.s3_bucket_files.data.aws_canonical_user_id.this: 1초 후에 읽기 완료 [id=51528157cefbacf17d3cc90bf31e07f8f463cadfbf31739f6b264afac67fb2ce]
module.s3_bucket_files.aws_s3_bucket_public_access_block.this[0]: 상태 새로 고침 중... [id=my-company-myapp-files-dev]
module.ssm_params.aws_ssm_parameter.this["2"]: 상태 새로 고침 중... [id=/app/myapp/lambda_role]
module.ssm_params.aws_ssm_parameter.this["3"]: 상태 새로 고침 중... [id=/app/myapp/event_bridge_name]
module.ssm_params.aws_ssm_parameter.this["4"]: 상태 새로 고침 중... [id=/app/myapp/logs]
module.ssm_params.aws_ssm_parameter.this["1"]: 상태 새로 고침 중... [id=/app/myapp/s3_bucket_files]

변경 사항이 없습니다. 인프라가 구성과 일치합니다.

Terraform은 실제 인프라를 구성과 비교하고 차이점을 찾았으며 변경 사항이 필요하지 않습니다.
상태 잠금 해제 중. 이 작업은 몇 분 정도 걸릴 수 있습니다...
```

읽어 주셔서 감사합니다!
