---
title: "S3 이벤트 알림을 사용하여 썸네일 생성하기"
description: ""
coverImage: "/assets/img/2024-05-17-UseS3eventnotificationstogeneratethumbnails_0.png"
date: 2024-05-17 18:32
ogImage: 
  url: /assets/img/2024-05-17-UseS3eventnotificationstogeneratethumbnails_0.png
tag: Tech
originalTitle: "Use S3 event notifications to generate thumbnails"
link: "https://medium.com/gitconnected/use-s3-event-notifications-to-generate-thumbnails-7fb2fb11a584"
---


## 이벤트 기반 서버리스 아키텍처

![이미지](/assets/img/2024-05-17-UseS3eventnotificationstogeneratethumbnails_0.png)

안녕하세요!

애플리케이션에서 파일을 클라우드에 저장하는 것은 파일 지속성의 한 방법으로 매우 일반적입니다. 이를 통해 애플리케이션이 어디에서 어떻게 사용될지에 대한 많은 유연성을 제공합니다.

<div class="content-ad"></div>

AWS는 객체를 저장할 수 있는 옵션으로 관리형 서비스 S3 (Simple Storage Service)를 제공합니다. 이 서비스는 높은 가용성, 확장성 및 성능을 갖추고 있습니다. 주로 웹 애플리케이션의 저장 서비스로 사용됩니다.

S3는 버킷 안에서 객체 작업에 대한 알림을 받을 수 있는 기능도 제공합니다. 이는 객체 생성, 업데이트, 이동, 삭제 등의 작업일 수 있습니다. 이를 S3 이벤트 알림이라고 합니다.

이 문서에서는 이미지를 업로드할 때마다 해당 이미지에 대한 섬네일을 생성하는 서버리스 애플리케이션을 살펴보겠습니다.

우리는 이벤트 알림을 수신하고 섬네일을 생성하는 Go로 작성된 람다 함수를 가질 것입니다.

<div class="content-ad"></div>

해보자구요!

# 요구 사항

- AWS 계정
- 좋아하는 코드 편집기 (저는 Visual Studio Code를 사용할 예정입니다)
- GitHub 계정

# 아키텍처

<div class="content-ad"></div>

<img src="/assets/img/2024-05-17-UseS3eventnotificationstogeneratethumbnails_1.png" />

버킷을 구성하여 객체 이벤트를 SNS 토픽으로 보내고 해당 메시지를 생성한 썸네일을 S3 버킷에 업로드할 람다로 전송할 것입니다.

S3 버킷 이벤트에 람다를 직접 대상으로 지정하지 않을 것입니다. 이유는 각 이벤트 알림에 대해 하나의 대상 유형만 지정할 수 있기 때문입니다. SNS 토픽을 대상으로 사용하면 SQS 대기열, 이메일, 전화 알림 및 SNS가 지원하는 기타 여러 대상으로 이벤트를 전파할 수 있습니다.

# Terraform을 사용하여 인프라 구성하기

<div class="content-ad"></div>

## 이미지 S3 버킷

시작하려면 우리의 테라폼 폴더를 설정하기 위해 프로젝트의 루트 레벨에 iac라는 폴더를 만들어 주세요. 그 안에 providers.tf라는 파일을 생성해 AWS 프로바이더를 구성할 수 있도록 다음 코드를 추가해 주세요:

```js
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

// Region은 AWS_REGION 환경 변수에서 설정됩니다
provider "aws" {
}
```

만약 테라폼이 인프라 상태를 추적하도록 하려면, AWS에서 S3 버킷을 생성하고 상태 백엔드로 설정할 수 있습니다. 아래 코드에서처럼 하시면 됩니다:

<div class="content-ad"></div>

```js
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }

  backend "s3" {
    bucket = "terraform-medium-api-notification" // 여기가 상태 버킷입니다
    key    = "thumbnail-generator/state"
  }
}

// AWS_REGION 환경 변수에서 지역 설정됨
provider "aws" {
}
```

이제 이미지를 호스팅하는 S3 버킷을 만들어 봅시다.

iac 폴더에 s3.tf라는 파일을 만들고 다음 코드를 추가해주세요:

```js
resource "aws_s3_bucket" "my-app-images" {
  bucket = "my-super-app-images" // 버킷에 고유한 이름을 사용해주세요
}

resource "aws_s3_object" "images_folder" {
  bucket = aws_s3_bucket.my-app-images.bucket
  key    = "images/"
}

resource "aws_s3_object" "thumbnails_folder" {
  bucket = aws_s3_bucket.my-app-images.bucket
  key    = "thumbnails/"
}
```

<div class="content-ad"></div>

버킷 속성에서는 버킷에 고유한 이름을 사용해야 합니다. 왜냐하면 S3 버킷 이름은 모든 AWS 전역에서 고유하기 때문입니다. 이름을 제공하지 않으려면 비워두고 AWS가 고유한 버킷 이름을 할당해 줄 것입니다.

이 코드는 이미지/ 및 썸네일/ 두 개의 폴더가 있는 S3 버킷을 생성합니다. 이 폴더들은 파일을 저장하는 데 사용할 것입니다.

## SNS를 사용한 메시징

이제 알림 주제와 메시지 대기열을 설정해 봅시다.

<div class="content-ad"></div>

```yaml
# messaging.tf

resource "aws_sns_topic" "topic" {
  name   = "image-events"
}
```

```yaml
# variables.tf

variable "region" {
  description = "Default region of your resources"
  type        = string
  default     = "eu-central-1" # Set as your default region here
}

variable "account_id" {
  description = "The ID of the default AWS account"
  type        = string
}
```

<div class="content-ad"></div>

그리고 variables.tfvars라는 또 다른 파일을 만들어 변수를 설정하세요:

```js
region = "eu-central-1" // 원하는 지역을 설정하세요
```

나중에 테라폼 명령어에 전달할 account_id는 나중에 인수로 전달할 것입니다.

## S3 이벤트 알림

<div class="content-ad"></div>

이제 S3에 대한 이벤트 알림을 설정해 봅시다.

s3.tf 파일에 다음 코드를 추가하여 버킷 알림을 설정하세요:

```js
resource "aws_s3_bucket_notification" "images_put_notification" {
  bucket = aws_s3_bucket.my-app-images.id
  topic {
    topic_arn = aws_sns_topic.topic.arn
    filter_prefix = "images/"
    events = ["s3:ObjectCreated:*"]
  }
}
```

이를 활성화하려면 S3 버킷이 해당 topic으로 알림을 발행할 수 있도록 SNS topic에 정책을 추가해야 합니다. messaging.tf 파일로 이동하여 다음 정책을 추가하세요:

<div class="content-ad"></div>

```js
리소스 "aws_sns_topic" "topic" {
  name   = "image-events"
  policy = data.aws_iam_policy_document.sns-topic-policy.json
}

데이터 "aws_iam_policy_document" "sns-topic-policy" {
  policy_id = "arn:aws:sns:${var.region}:${var.account_id}:image-events/SNSS3NotificationPolicy"
  statement {
    sid    = "s3-allow-send-messages"
    effect = "Allow"
    principals {
      type        = "Service"
      identifiers = ["s3.amazonaws.com"]
    }
    actions = [
      "SNS:Publish",
    ]
    resources = [
      "arn:aws:sns:${var.region}:${var.account_id}:image-events",
    ]
    condition {
      test     = "ArnEquals"
      variable = "aws:SourceArn"
      values = [
        aws_s3_bucket.my-app-images.arn
      ]
    }
  }
}
```

여기서는 sns-topic-policy 리소스를 생성하고 해당 리소스를 policy 속성에 전달하는 topic 리소스를 생성합니다.

## 기본 람다 추가하기

이제 람다의 기반 인프라를 추가하기만 남았습니다. 이후에 코드를 추가할 기본 람다를 설정할 것입니다. Go 언어로 코드를 작성할 예정입니다.
 

<div class="content-ad"></div>

먼저, 람다 함수를 초기화할 기본 코드가 필요합니다. 그래서 iac 폴더에 lambda_init_code라는 폴더를 만들어주세요. 여기서 소스 코드를 가져와서 메인 컴파일된 파일을 직접 사용하거나 README.md 파일의 지시에 따라 새로운 실행 가능 파일을 컴파일할 수 있습니다.

이제 새 파일 lambdas.tf를 생성하고 다음 코드를 추가하여 람다 인프라를 추가할 수 있습니다:

```js
resource "aws_iam_role" "iam_for_lambda" {
  name               = "thumbnail-generator-lambda-role"
  assume_role_policy = data.aws_iam_policy_document.assume_role.json
  inline_policy {
    name   = "DefaultPolicy"
    policy = data.aws_iam_policy_document.lambda_role_policies.json
  }
}
resource "aws_lambda_function" "lambda" {
  filename      = data.archive_file.lambda.output_path
  function_name = "thumbnail-generator"
  role          = aws_iam_role.iam_for_lambda.arn
  handler       = "main"
  runtime       = "go1.x"
  timeout       = 15
}

data "archive_file" "lambda" {
  type        = "zip"
  source_file = "./lambda_init_code/main"
  output_path = "thumbnail_generator_lambda_function_payload.zip"
}

data "aws_iam_policy_document" "assume_role" {
  statement {
    effect = "Allow"
    principals {
      type        = "Service"
      identifiers = ["lambda.amazonaws.com"]
    }
    actions = ["sts:AssumeRole"]
  }
}

data "aws_iam_policy_document" "lambda_role_policies" {
  statement {
    effect = "Allow"
    actions = [
      "logs:CreateLogGroup",
      "logs:CreateLogStream",
      "logs:PutLogEvents",
    ]
    resources = ["arn:aws:logs:*:*:*"]
  }
}
```

이렇게 하면 런타임으로 Go를 사용하는 람다 함수를 생성하고 역할을 만들며, 람다 함수가 이 역할을 가정하고 클라우드워치에 로깅할 수 있도록 권한을 부여합니다.

<div class="content-ad"></div>

다음으로, 람다가 트리거될 수 있도록 SNS 주제에 대한 구독을 만들어야 합니다. lambdas.tf 파일에 다음 코드를 추가할 수 있습니다:

```js
resource "aws_sns_topic_subscription" "topic_subscription" {
  topic_arn = aws_sns_topic.topic.arn
  protocol  = "lambda"
  endpoint  = aws_lambda_function.lambda.arn
}

resource "aws_lambda_permission" "apigw_lambda" {
  statement_id  = "AllowExecutionFromSNS"
  action        = "lambda:InvokeFunction"
  function_name = aws_lambda_function.lambda.arn
  principal     = "sns.amazonaws.com"
  source_arn    = aws_sns_topic.topic.arn
}
```

최종 lambdas.tf 파일이 다음과 같이 되도록 만들어보세요:

```js
resource "aws_iam_role" "iam_for_lambda" {
  name               = "thumbnail-generator-lambda-role"
  assume_role_policy = data.aws_iam_policy_document.assume_role.json
  inline_policy {
    name   = "DefaultPolicy"
    policy = data.aws_iam_policy_document.lambda_role_policies.json
  }
}

resource "aws_lambda_function" "lambda" {
  filename      = data.archive_file.lambda.output_path
  function_name = "thumbnail-generator"
  role          = aws_iam_role.iam_for_lambda.arn
  handler       = "main"
  runtime       = "go1.x"
  timeout       = 15
}

resource "aws_sns_topic_subscription" "topic_subscription" {
  topic_arn = aws_sns_topic.topic.arn
  protocol  = "lambda"
  endpoint  = aws_lambda_function.lambda.arn
}

resource "aws_lambda_permission" "apigw_lambda" {
  statement_id  = "AllowExecutionFromSNS"
  action        = "lambda:InvokeFunction"
  function_name = aws_lambda_function.lambda.arn
  principal     = "sns.amazonaws.com"
  source_arn    = aws_sns_topic.topic.arn
}

data "archive_file" "lambda" {
  type        = "zip"
  source_file = "./lambda_init_code/main"
  output_path = "thumbnail_generator_lambda_function_payload.zip"
}

data "aws_iam_policy_document" "assume_role" {
  statement {
    effect = "Allow"
    principals {
      type        = "Service"
      identifiers = ["lambda.amazonaws.com"]
    }
    actions = ["sts:AssumeRole"]
  }
}

data "aws_iam_policy_document" "lambda_role_policies" {
  statement {
    effect = "Allow"
    actions = [
      "logs:CreateLogGroup",
      "logs:CreateLogStream",
      "logs:PutLogEvents"
    ]
    resources = ["arn:aws:logs:*:*:*"]
  }

  statement {
    effect = "Allow"
    actions = [
      "s3:GetObject",
    ]
    resources = [
      format("%s/%s*", aws_s3_bucket.my-app-images.arn, aws_s3_object.images_folder.key)
    ]
  }

  statement {
    effect = "Allow"
    actions = [
      "s3:PutObject",
    ]
    resources = [
      format("%s/%s*", aws_s3_bucket.my-app-images.arn, aws_s3_object.thumbnails_folder.key)
    ]
  }
}
```

<div class="content-ad"></div>

우리의 SNS 토픽이 람다를 이벤트와 함께 호출할 수 있도록 권한을 부여할 것입니다.

람다는 15초의 제한 시간을 갖고 있습니다. 이는 기본 제한 시간이 3초이기 때문에 S3로 파일을 다운로드하고 업로드하는 작업은 이미지 크기에 따라 3초보다 더 오래 걸릴 수 있기 때문입니다. 이는 S3 작업이 인터넷을 통과하기 때문에 발생하는 것입니다. 성능을 향상시키고 싶다면 VPC를 생성하고 람다와 VPC 엔드포인트를 만들어 S3 서비스에 대한 연결이 인터넷이 아닌 AWS 네트워크를 통해 이루어지도록 할 수 있습니다.

## 인프라 배포하기

이제 우리가 코드로 정의한 인프라를 배포하기 위해 GitHub 액션을 사용해보겠습니다.

<div class="content-ad"></div>

코드에 .github/workflows 폴더를 만들고 deploy-infra.yml 파일을 추가하여 GitHub 액션 워크플로우를 정의하세요:

```yml
name: Deploy Infrastructure
on:
  push:
    branches:
      - main
    paths:
      - iac/**/*
      - .github/workflows/deploy-infra.yml

defaults:
  run:
    working-directory: iac/

jobs:
  terraform:
    name: 'Terraform'
    runs-on: ubuntu-latest
    steps:
      # GitHub Actions 러너에 리포지토리를 체크아웃
      - name: Checkout
        uses: actions/checkout@v3

      - name: Configure AWS Credentials Action For GitHub Actions
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: eu-central-1

      # 최신 버전의 Terraform CLI 설치 및 Terraform Cloud 사용자 API 토큰을 사용하여 Terraform CLI 구성 파일 설정
      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3

      # Terraform 워킹 디렉터리를 초기화하고 초기 파일을 생성하거나 기존 파일을 로드하고 모듈 다운로드 등을 수행
      - name: Terraform Init
        run: terraform init

      # 모든 Terraform 구성 파일이 규범적 형식을 준수하는지 확인
      - name: Terraform Format
        run: terraform fmt -check

      # Terraform 실행 계획 생성
      - name: Terraform Plan
        run: |
          terraform plan -out=plan -input=false -var-file="variables.tfvars" -var account_id=${{ secrets.AWS_ACCOUNT_ID }}

      # "main"으로 푸시되면 Terraform 구성 파일에 따라 인프라를 구축 또는 변경함
      # 참고: "Terraform Cloud"에 대해 "strict" 상태 검사를 설정하는 것이 권장됩니다. 자세한 정보는 아래 문서를 참조하세요: https://help.github.com/en/github/administering-a-repository/types-of-required-status-checks
      - name: Terraform Apply
        run: terraform apply -auto-approve -input=false plan
```

- AWS_ACCESS_KEY — 자원을 생성할 권한이 있는 AWS의 액세스 키입니다
- AWS_SECRET_ACCESS_KEY — 액세스 키와 연결된 AWS 시크릿
- AWS_ACCOUNT_ID — AWS 대시보드 오른쪽 상단에 있는 계정 ID입니다
- YOUR_REGION — 인프라를 배포할 기본 지역

이제 코드를 GitHub에 푸시하고 워크플로우가 완료되면 인프라가 생성되는 것을 확인해보세요.

<div class="content-ad"></div>

테스트하려면 이미지/ 폴더에 파일을 업로드하고 CloudWatch에서 람다 로그를 확인할 수 있습니다.

S3는 두 개의 폴더로 생성되어야 합니다:

![image](/assets/img/2024-05-17-UseS3eventnotificationstogeneratethumbnails_2.png)

SNS는 구독이 있는 상태로 생성되어야 합니다:

<div class="content-ad"></div>

<img src="/assets/img/2024-05-17-UseS3eventnotificationstogeneratethumbnails_3.png" /> 

Lambda를 SNS 트리거와 함께 생성해야합니다:

<img src="/assets/img/2024-05-17-UseS3eventnotificationstogeneratethumbnails_4.png" />

# Lambda 구현

<div class="content-ad"></div>

인프라가 설치되었으므로 썸네일 생성기 코드를 구현해야 합니다.

루트 레벨에 src이라는 새 폴더를 생성하고 다음 코드를 실행하여 Go 모듈을 초기화해보세요:

```js
go mod init example.com/thumbnail-generator
go get github.com/aws/aws-lambda-go
go get github.com/aws/aws-sdk-go-v2
go get github.com/aws/aws-sdk-go-v2/service/s3
go get github.com/aws/aws-sdk-go-v2/config
go get github.com/disintegration/imaging
```

원하는 경우 example.com/thumbnail-generator를 선호하는 모듈 이름으로 교체할 수 있습니다.

<div class="content-ad"></div>

이제 main.go 파일을 만들고 다음 코드를 추가하세요:

```go
package main

import (
	"bytes"
	"context"
	"encoding/json"
	"fmt"
	"image"
	"image/png"
	"io"
	"log"
	"strings"

	"github.com/aws/aws-lambda-go/events"
	"github.com/aws/aws-lambda-go/lambda"
	"github.com/aws/aws-sdk-go-v2/aws"
	"github.com/aws/aws-sdk-go-v2/config"
	"github.com/aws/aws-sdk-go-v2/service/s3"
	"github.com/disintegration/imaging"
)

type awsClient struct {
	s3  s3.Client
	ctx *context.Context
}

func handleRequest(ctx context.Context, event events.SNSEvent) error {
	awsConfig, err := config.LoadDefaultConfig(ctx)

	if err != nil {
		log.Fatalf("AWS 기본 구성을 불러 올 수 없습니다")
		return err
	}

	awsClient := awsClient{s3: *s3.NewFromConfig(awsConfig), ctx: &ctx}

	for _, record := range event.Records {
		var imageEvent events.S3Event

		err := json.Unmarshal([]byte(record.SNS.Message), &imageEvent)

		if err != nil {
			log.Fatalf("SNS 메시지 %s을 S3 이벤트 레코드로 언마샬하는 동안 오류가 발생했습니다: %v", record.SNS.Message, err)
			return err
		}

		for _, imageRecord := range imageEvent.Records {
			bucketName := imageRecord.S3.Bucket.Name
			objectKey := imageRecord.S3.Object.Key

			file, err := awsClient.downloadFile(bucketName, objectKey)

			log.Printf("이미지 다운로드 성공")

			if err != nil {
				log.Fatalf("버킷 %s에서 파일 %s을 로드하는 중 오류가 발생했습니다", bucketName, objectKey)
				return err
			}

			thumbnail, err := createThumbnail(file)

			if err != nil {
				log.Fatalf("버킷 %s의 파일 %s에 대한 섬네일 생성 중 오류가 발생했습니다. 오류: %v", bucketName, objectKey, err)
				return err
			}

			log.Printf("섬네일 생성 성공")

			err = awsClient.uploadFile(bucketName, objectKey, thumbnail)

			log.Printf("섬네일 업로드 성공")

			if err != nil {
				log.Fatalf("버킷 %s에 파일 %s을 thumbnails/에 업로드하는 중 오류가 발생했습니다", bucketName, objectKey)
				return err
			}
		}
	}

	return nil
}

func createThumbnail(reader io.Reader) (*bytes.Buffer, error) {
	srcImage, _, err := image.Decode(reader)

	if err != nil {
		log.Fatalf("오류로 인해 파일을 디코딩할 수 없습니다: %v", err)
		return nil, err
	}

	// 80x80 크기의 섬네일 생성
	thumbnail := imaging.Thumbnail(srcImage, 80, 80, imaging.Lanczos)

	var bufferBytes []byte
	buffer := bytes.NewBuffer(bufferBytes)

	err = png.Encode(buffer, thumbnail)

	return buffer, err
}

func (client *awsClient) downloadFile(bucketName string, objectKey string) (*bytes.Reader, error) {
	result, err := client.s3.GetObject(*client.ctx, &s3.GetObjectInput{
		Bucket: aws.String(bucketName),
		Key:    aws.String(objectKey),
	})

	if err != nil {
		log.Fatalf("객체를 가져올 수 없음 %v:%v. 원인: %v", bucketName, objectKey, err)
		return nil, err
	}

	defer result.Body.Close()

	body, err := io.ReadAll(result.Body)

	if err != nil {
		log.Fatalf("파일을 읽는 중 오류 발생. 오류: %s", err)
		return nil, err
	}

	file := bytes.NewReader(body)

	return file, err
}

func (client *awsClient) uploadFile(bucketName string, originalObjectKey string, thumbnail io.Reader) error {
	objectKeyParts := strings.Split(originalObjectKey, "/")
	fileNameWithoutExtensions := strings.Split(objectKeyParts[len(objectKeyParts)-1], ".")[0]
	objectKey := fmt.Sprintf("thumbnails/%s_thumbnail.png", fileNameWithoutExtensions)

	_, err := client.s3.PutObject(*client.ctx, &s3.PutObjectInput{
		Bucket: aws.String(bucketName),
		Key:    aws.String(objectKey),
		Body:   thumbnail,
	})

	if err != nil {
		log.Fatalf("%v을(를) %v의 %v에 업로드할 수 없음. 원인: %v\n",
			originalObjectKey, bucketName, objectKey, err)
	}

	return err
}

func main() {
	lambda.Start(handleRequest)
}
```

이제 GitHub workflow를 설정하여 람다 코드를 배포해야 합니다.

.github/workflows 폴더에 deploy-lambda.yml이라는 새 파일을 추가하고 다음 코드를 추가하세요:```

<div class="content-ad"></div>

```bash
name: Deploy Thumbnail Generator Lambda
on:
  push:
    branches:
      - main
    paths:
      - src/**/*
      - .github/workflows/deploy-lambda.yml

defaults:
  run:
    working-directory: src/

jobs:
  terraform:
    name: 'Deploy Thumbnail Generator Lambda'
    runs-on: ubuntu-latest
    steps:
      # Checkout the repository to the GitHub Actions runner
      - name: Checkout
        uses: actions/checkout@v3

      - uses: actions/setup-go@v4.1.0
        with:
          go-version: '1.22.0'

      - name: Configure AWS Credentials Action For GitHub Actions
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${ secrets.AWS_ACCESS_KEY }
          aws-secret-access-key: ${ secrets.AWS_SECRET_ACCESS_KEY }
          aws-region: eu-central-1

      - name: Build Lambda
        run: GOOS=linux GOARCH=amd64 CGO_ENABLED=0 go build -o build/main .

      - name: Zip build
        run: zip -r -j main.zip ./build

      - name: Update Lambda code
        run: aws lambda update-function-code --function-name=thumbnail-generator --zip-file=fileb://main.zip
```

코드를 커밋하고 레포지토리에 푸시하면 빌드가 실행됩니다.

완료되면 Lambda 페이지의 "최종 수정" 속성을 확인하여 배포된 것을 확인할 수 있습니다:

<img src="/assets/img/2024-05-17-UseS3eventnotificationstogeneratethumbnails_5.png" />


<div class="content-ad"></div>

테스트하려면 S3 버킷의 images/ 폴더에 이미지를 업로드해야 합니다. 업로드가 성공하면 잠시 기다린 후 새로 만든 섬네일을 확인할 수 있습니다.

# 결론

이 글에서는 Terraform 인프라스트럭처를 사용하여 S3 버킷, 람다, SNS, SNS 알림 등을 생성하고 연결하는 방법을 배웠습니다.

또한 S3 이벤트를 SNS 토픽에 보내어 이를 다른 소스(다른 SNS 토픽 포함)로 확산할 수 있는 방법도 배웠습니다.

<div class="content-ad"></div>

저희는 Go로 작성된 람다 함수도 만들었어요. 이 함수는 SNS 메시지를 통해 호출되어 S3 버킷에서 파일을 다운로드하고 이미지에서 썸네일을 생성한 다음 이 썸네일을 S3에 업로드합니다.

이 글의 코드는 여기에서 확인할 수 있어요.

즐거운 코딩하세요! 💻