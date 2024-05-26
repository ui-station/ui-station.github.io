---
title: "REST API를 API Gateway, Lambda, DynamoDB, Cognito를 사용하여 배포하는 단계별 가이드  Terraform"
description: ""
coverImage: "/assets/img/2024-05-23-AStep-by-StepGuideOnDeployingRESTAPIusingAPIGatewayLambdaDynamoDBCognitoTerraform_0.png"
date: 2024-05-23 13:55
ogImage:
  url: /assets/img/2024-05-23-AStep-by-StepGuideOnDeployingRESTAPIusingAPIGatewayLambdaDynamoDBCognitoTerraform_0.png
tag: Tech
originalTitle: "A Step-by-Step Guide On Deploying REST API using API Gateway, Lambda, DynamoDB, Cognito — Terraform"
link: "https://medium.com/aws-tip/a-step-by-step-guide-on-deploying-rest-api-using-api-gateway-lambda-cognito-terraform-f277814d048e"
---


![image](/assets/img/2024-05-23-AStep-by-StepGuideOnDeployingRESTAPIusingAPIGatewayLambdaDynamoDBCognitoTerraform_0.png)

# 소개

우리는 다양한 상황에서 사용할 수 있는 실전 프로젝트를 만들고 싶습니다. 실제 세계에서 매우 일반적인 것으로, 거의 모든 애플리케이션이 모듈식 레고 블록으로 구성된 마이크로서비스에 기반을 두고 있습니다.

특히, 목표는 API 게이트웨이에 호스팅된 API를 만들고, 백엔드는 람다에, 데이터베이스는 DynamoDB에 있는 것입니다. 람다 함수에는 DynamoDB 테이블에서 CRUD 작업 (CREATE, READ, UPDATE, DELETE)을 수행하는 로직이 포함될 것입니다. 그리고 추가로 몇 가지 경로에 대한 공개 액세스를 제한하기 위해 Amazon Cognito를 사용한 인증을 추가할 것입니다. 왜냐하면 데이터베이스에 대한 쓰기 작업은 위험하기 때문입니다.


<div class="content-ad"></div>

시칠리아 섬에서 태어났기 때문에 신기한 섬리아 요리 목록을 관리할 수 있는 간단한 API를 생성할 것입니다.

모든 소스 코드는 여기에서 확인하실 수 있습니다:

# 단계 1: 공급자, AWS 지역, S3 백엔드 설정

첫 번째 단계는 AWS를 제공자로 사용하도록 지정하는 것입니다.

<div class="content-ad"></div>

```js
# provider.tf

provider "aws" {
  region = var.aws_region
}
```

우리는 하나의 변수만 정의하고 있어요.

```js
# variables.tf

variable "aws_region" {
  default   = "eu-west-3"
  type      = string
}
```

그런 다음 AWS 관리 콘솔로 이동해서 “S3” AWS 서비스로 이동하여 나중에 사용할 Terraform 상태 파일을 저장할 S3 버킷을 생성하세요. "key" 속성으로 지정된 경로에 생성할 거에요. 저는 "my-api-gateway-lambda-terraform-state"라고 이름지었어요. 모든 옵션을 기본값으로 남겨두세요.```

<div class="content-ad"></div>

만들었다면, Terraform 구성에서 명시할 것입니다:

```js
# backend.tf

terraform {
  backend "s3" {
    bucket = "my-api-gateway-lambda-terraform-state"
    region = var.aws_region
    key    = "API-Gateway/terraform.tfstate"
  }
  required_version = ">= 0.13.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = ">= 2.7.0"
    }
  }
}
```

## 단계 2: Lambda IAM 역할 생성

Lambda 함수가 DynamoDB 테이블에서 작업을 수행하려면 해당 권한이 있어야 합니다. 따라서 IAM 역할을 생성해야 합니다.

<div class="content-ad"></div>

먼저 IAM 역할의 Assume Role 정책(Trust Relationship이라고도 함)을 명시적으로 지정해야 합니다. 이는 어떤 리소스 또는 서비스가 원하는 역할을 가져갈 수 있는 지를 나타내는데, 이 경우에는 Lambda 함수입니다. AWS 서비스에 권한을 제공할 때 이 단계는 항상 필수적입니다. 이 정책의 형식은 다음과 같습니다:

```js
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Principal": {
            "Service": "lambda.amazonaws.com" # AWS 서비스의 이름
        },
        "Action": "sts:AssumeRole"
    }]
}
```

기본적으로 "내 계정의 모든 Lambda 함수가 이 역할을 가져올 수 있습니다"라고 말하고 있습니다.

또한 Lambda에게 함수 실행과 관련된 로그 작성을 위해 필요한 최소한의 권한을 부여해야 합니다. 이 권한들은 이미 AWS에서 제공하는 AWSLambdaBasicExecutionRole이라는 IAM 서비스 역할에 정의되어 있습니다. 따라서 이 서비스 역할을 새 IAM 역할에 연결하여 Lambda에 할당할 것입니다.

<div class="content-ad"></div>

파일 "iam.tf"를 생성하고 다음 코드를 붝어 주세요:

```js
# iam.tf

# 역할 가정 정책
data "aws_iam_policy_document" "AWSLambdaTrustPolicy" {
  statement {
    actions    = ["sts:AssumeRole"]
    effect     = "Allow"
    principals {
      type        = "Service"
      identifiers = ["lambda.amazonaws.com"]
    }
  }
}

# IAM 역할 정의 및 가정 역할 정책 첨부
resource "aws_iam_role" "terraform_function_role" {
  name               = "terraform_function_role"
  assume_role_policy = data.aws_iam_policy_document.AWSLambdaTrustPolicy.json
}

# 방금 정의한 IAM 역할에 IAM 서비스 역할 첨부
# AWSLambdaBasicExecutionRole은 람다 함수에 최소한의 권한을 부여합니다
# (실행에 대한 로그 작성, 오류, 디버깅 등)
resource "aws_iam_role_policy_attachment" "terraform_lambda_policy" {
  role       = aws_iam_role.terraform_function_role.name
  policy_arn = "arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole"
}
```

또한, 이외에도 말씀드린대로 Lambda 함수가 생성할 DynamoDB 테이블에 액세스해야 합니다. 이를 위해 DynamoDB 테이블에서 수행할 모든 작업이 명시적으로 허용된 JSON 정책 문서를 작성합니다:

```js
// lambda_dynamodb_policy.json

{
  "Version": "2012-10-17",
  "Statement": [
      {
          "Sid": "ListAndDescribe",
          "Effect": "Allow",
          "Action": [
              "dynamodb:List*",
              "dynamodb:DescribeReservedCapacity*",
              "dynamodb:DescribeLimits",
              "dynamodb:DescribeTimeToLive"
          ],
          "Resource": "*"
      },
      {
          "Sid": "SpecificTable",
          "Effect": "Allow",
          "Action": [
              "dynamodb:BatchGet*",
              "dynamodb:DescribeStream",
              "dynamodb:DescribeTable",
              "dynamodb:Get*",
              "dynamodb:Query",
              "dynamodb:Scan",
              "dynamodb:BatchWrite*",
              "dynamodb:CreateTable",
              "dynamodb:Delete*",
              "dynamodb:Update*",
              "dynamodb:PutItem"
          ],
          "Resource": "arn:aws:dynamodb:*:*:table/dishes" // DynamoDB 테이블
      }
  ]
}
```

<div class="content-ad"></div>

Lambda 함수가 DynamoDB 테이블에서 CRUD 작업(Get, Put, Update, Delete, Scan, Query 및 기타 작업)을 수행할 수 있는 정책을 정의하고, "dishes"라는 이름의 테이블을 사용할 것입니다.

"iam.tf" 파일에서는 JSON 문서에 정의된 정책을 IAM 역할에 부착하도록 다음과 같이 작성합니다:

```js
# iam.tf

# DynamoDB에 액세스하기 위한 IAM 역할에 사용자 지정 정책 부착
resource "aws_iam_role_policy" "lambda_dynamodb_policy" {
  name   = "lambda_dynamodb_policy"
  role   = aws_iam_role.lambda-iam-role.name
  policy = file("${path.module}/lambda_dynamodb_policy.json")
}
```

# 단계 3: Lambda 코드 설정하기

<div class="content-ad"></div>

Lambda 함수를 설정하는 것이 다음 단계입니다. 이 함수는 DynamoDB 테이블과 상호 작용할 것입니다. 이 함수는 Python으로 작성될 것입니다.

Lambda 함수 안에서는 각각의 작업에 대한 메서드를 정의하고 API Gateway로 응답을 반환할 수 있습니다. 우리는 함수의 코드를 Python으로 작성할 것입니다.

우선적으로 Lambda를 테스트할 때 무슨 일이 벌어지는지 볼 수 있도록 로거를 설정합니다. 그런 다음 lambda_handler() 안에서는 AWS 서비스와 상호 작용하기 위해 사용되는 boto3 라이브러리를 활용하여 DynamoDB 클라이언트를 선언합니다. Lambda를 트리거하는 이벤트는 API Gateway에서 오는 HTTP 요청입니다. 우리는 이를 통해 HTTP 메소드를 읽고 Lambda가 DynamoDB 테이블에서 수행해야 하는 작업을 구별할 수 있습니다.

REST API는 트리 구조로 구성되어 있으며, 우리는 이를 다음과 같이 구조화하고 싶습니다:

<div class="content-ad"></div>


![Image](/assets/img/2024-05-23-AStep-by-StepGuideOnDeployingRESTAPIusingAPIGatewayLambdaDynamoDBCognitoTerraform_1.png)

우리는 다음을 할 수 있기를 원합니다:

- GET dishes/ : 모든 요리 항목 검색
- GET dish/'dishId' : ID로 특정 요리 항목 검색
- POST dish/ : 새로운 요리 항목 저장
- PATCH dish/ : 특정 요리 항목의 속성 업데이트
- DELETE dish/ : 테이블에서 요리 항목 삭제

테이블의 모든 항목을 가져 오기 위해 재귀 함수 recursive_scan을 활용하며, 이 함수는 DynamoDB 테이블에서 레코드를 효율적으로 스캔하는 데 사용됩니다.


<div class="content-ad"></div>

표준 DynamoDB의 scan 작업은 테이블의 모든 항목을 읽는 표준 접근 방식입니다. 그러나 DynamoDB의 분산 특성과 확장성 때문에, 단일 scan 작업으로는 특히 테이블이 큰 경우 모든 항목을 한 번에 검색하지 못할 수 있습니다. DynamoDB는 결과를 페이지별로 반환하며, 다음 결과 페이지가 시작되는 위치를 나타내는 토큰(LastEvaluatedKey)과 함께 항목의 하위 집합을 반환합니다.

recursive_scan 메서드는 모든 페이지의 결과를 재귀적으로 가져와서 더 이상 페이지가 남아있지 않을 때까지(응답에 LastEvaluatedKey가 없을 때) 검색 프로세스를 최적화합니다. 이를 통해 페이지 수에 관계없이 DynamoDB 테이블의 모든 항목을 효율적으로 검색할 수 있습니다.

```js
def recursive_scan(scan_params, items):
    response = table.scan(**scan_params)
    items += response['Items']
    if 'LastEvaluatedKey' in response:
        scan_params['ExclusiveStartKey'] = response['LastEvaluatedKey']
        recursive_scan(scan_params, items)
    return items
```

여기에 Lambda 함수의 전체 코드가 있습니다.

<div class="content-ad"></div>

```js
# lambda_code.py

import json
import logging
import boto3
from decimal import Decimal
from botocore.exceptions import ClientError

logger = logging.getLogger()
logger.setLevel(logging.INFO)

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('dishes')

dish_path = '/dish'
dishes_path = '/dishes'

def lambda_handler(event, context):

    logger.info('API event: {}'.format(event))

    response = None

    try:
        http_method = event.get('httpMethod')
        path = event.get('path')

        if http_method == 'GET' and path == dishes_path:
            response = get_all_dishes()

        elif http_method == 'GET' and path == dish_path:
            dish_id = event['queryStringParameters']['dish_id']
            response = get_dish(dish_id)

        elif http_method == 'POST' and path == dish_path:
            body = json.loads(event['body'])
            response = save_dish(body)

        elif http_method == 'PATCH' and path == dish_path:
            body = json.loads(event['body'])
            response = update_dish(body['dish_id'], body['update_key'], body['update_value'])

        elif http_method == 'DELETE':
            body = json.loads(event['body'])
            response = delete_dish(body['dish_id'])

        else:
            response = generate_response(404, '리소스를 찾을 수 없습니다.')

    except ClientError as e:
        logger.error('오류: {}'.format(e))
        response = generate_response(404, e.response['Error']['Message'])

    return response

def get_dish(dish_id):
    try:
        response = table.get_item(Key={'dish_id': dish_id})
        item = response['Item']
        logger.info('항목 조회: {}'.format(item))
        return generate_response(200, item)
    except ClientError as e:
        logger.error('오류: {}'.format(e))
        return generate_response(404, e.response['Error']['Message'])

def get_all_dishes():
    try:
        scan_params = {
            'TableName': table.name
        }
        items = recursive_scan(scan_params, [])
        logger.info('모든 항목 조회: {}'.format(items))
        return generate_response(200, items)
    except ClientError as e:
        logger.error('오류: {}'.format(e))
        return generate_response(404, e.response['Error']['Message'])

def recursive_scan(scan_params, items):
    response = table.scan(**scan_params)
    items += response['Items']
    if 'LastEvaluatedKey' in response:
        scan_params['ExclusiveStartKey'] = response['LastEvaluatedKey']
        recursive_scan(scan_params, items)
    return items

def save_dish(item):
    try:
        table.put_item(Item=item)
        logger.info('항목 저장: {}'.format(item))
        body = {
            '작업': '저장',
            '메시지': '성공',
            '항목': item
        }
        return generate_response(200, body)
    except ClientError as e:
        logger.error('오류: {}'.format(e))
        return generate_response(404, e.response['Error']['Message'])

def update_dish(dish_id, update_key, update_value):
    try:
        response = table.update_item(
            Key={'dish_id': dish_id},
            UpdateExpression=f'SET {update_key} = :value',
            ExpressionAttributeValues={':value': update_value},
            ReturnValues='UPDATED_NEW'
        )
        logger.info('항목 업데이트: {}'.format(response))
        body = {
            '작업': '업데이트',
            '메시지': '성공',
            '항목': response
        }
        return generate_response(200, response)
    except ClientError as e:
        logger.error('오류: {}'.format(e))
        return generate_response(404, e.response['Error']['Message'])

def delete_dish(dish_id):
    try:
        response = table.delete_item(
            Key={'dish_id': dish_id},
            ReturnValues='ALL_OLD'
        )
        logger.info('항목 삭제: {}'.format(response))
        body = {
            '작업': '삭제',
            '메시지': '성공',
            '항목': response
        }
        return generate_response(200, body)
    except ClientError as e:
        logger.error('오류: {}'.format(e))
        return generate_response(404, e.response['Error']['Message'])

class DecimalEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, Decimal):
            # 정수 또는 소수인지 확인합니다
            if obj % 1 == 0:
                return int(obj)
            else:
                return float(obj)
        # 기본 클래스의 default 메서드가 TypeError를 발생시키도록 합니다
        return super(DecimalEncoder, self).default(obj)

def generate_response(status_code, body):
    return {
        'statusCode': status_code,
        'headers': {
            'Content-Type': 'application/json',
        },
        'body': json.dumps(body, cls=DecimalEncoder)
    }
```

그런 다음, “lambda.tf” Terraform 파일에는 Lambda 코드를 압축하는 데이터 블록을 정의하고 해당 Lambda 자체에 대한 리소스 블록이 있습니다:

```js
data "archive_file" "lambda_code" {
  type        = "zip"
  source_file = "${path.module}/lambda_code.py"
  output_path = "${path.module}/lambda_code.zip"
}

resource "aws_lambda_function" "my-lambda-function" {
  filename      = "${path.module}/lambda_code.zip"
  function_name = "api-gateway-lambda"
  role          = aws_iam_role.lambda-iam-role.arn
  handler       = "lambda_code.lambda_handler"
  runtime       = "python3.12"

  source_code_hash = data.archive_file.lambda_code.output_base64sha256
}
```

# 단계 3: DynamoDB 설정


<div class="content-ad"></div>

시칠리아 요리 테이블을 만드는 시간이 왔습니다!

![Sicilian Dishes](https://miro.medium.com/v2/resize:fit:480/1*WMK7Qze__kL4gO7lXQ5LKg.gif)

`database.tf` 파일을 생성하고 다음 코드를 붙여넣으세요:

```js
# 다이나모DB 테이블 정의
resource "aws_dynamodb_table" "my_dynamodb_table" {
  name         = "dishes"
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "dish_id"

  attribute {
    name = "dish_id"
    type = "S"
  }

  tags = {
    Name = "dishes-table"
  }
}

locals {
  json_data = file("${path.module}/dishes.json")
  dishes    = jsondecode(local.json_data)
}

# 각 요리별로 다이나모DB 테이블에 새 항목 생성
resource "aws_dynamodb_table_item" "dishes" {
  for_each   = local.dishes
  table_name = aws_dynamodb_table.my_dynamodb_table.name
  hash_key   = aws_dynamodb_table.my_dynamodb_table.hash_key
  item       = jsonencode(each.value)
}
```

<div class="content-ad"></div>

JSON 문서에서 데이터를 가져 오므로 "dishes.json" 파일을 만드십시오. 각 속성에 대해 유형을 지정합니다 (S = 문자열, N = 숫자, L = 목록).

```js
// dishes.json

{
    "Item1": {
        "dish_id": {
            "S": "1"
        },
        "name": {
            "S": "아란치니"
        },
        "description": {
            "S": "치즈, 완두 및 고기가 들어있는 튀긴 쌀 공예볼"
        },
        "price": {
            "N": "8.99"
        },
        "ingredients": {
            "L": [
                {"S": "쌀"},
                {"S": "치즈"},
                {"S": "완두"},
                {"S": "고기"},
                {"S": "빵 가루"}
            ]
        }
    },
    "Item2": {
        "dish_id": {
            "S": "2"
        },
        "name": {
            "S": "카놀리"
        },
        "description": {
            "S": "튜브 모양의 튀겨진 페이스트리 도우로 구운 쉘에 달콤하고 부드러운 필링을 채운 시칠리아 디저트"
        },
        "price": {
            "N": "5.99"
        },
        "ingredients": {
            "L": [
                {"S": "밀가루"},
                {"S": "리코타 치즈"},
                {"S": "설탕"},
                {"S": "초콜릿 칩"}
            ]
        }
    },
    "Item3": {
        "dish_id": {
            "S": "3"
        },
        "name": {
            "S": "파스타 알라 노르마"
        },
        "description": {
            "S": "토마토 소스, 튀긴 가지, 갈은 리코타 샐라타 치즈 및 바질이 들어간 파스타"
        },
        "price": {
            "N": "12.99"
        },
        "ingredients": {
            "L": [
                {"S": "파스타"},
                {"S": "토마토 소스"},
                {"S": "가지"},
                {"S": "리코타 치즈"},
                {"S": "바질"}
            ]
        }
    },
    "Item4": {
        "dish_id": {
            "S": "4"
        },
        "name": {
            "S": "카사타"
        },
        "description": {
            "S": "과일 주스 또는 리큐르로 적시한 둥근 스펀지 케이크로 리코타 치즈, 설탕이 묻혔고 카놀리 크림과 유사한 초콜릿 또는 바닐라 필링이 층층이 쌓인 시칠리아 케이크"
        },
        "price": {
            "N": "15.99"
        },
        "ingredients": {
            "L": [
                {"S": "스펀지 케이크"},
                {"S": "과일 주스"},
                {"S": "리큐르"},
                {"S": "리코타 치즈"},
                {"S": "설탕"},
                {"S": "초콜릿"},
                {"S": "바닐라"}
            ]
        }
    }
}
```

# 단계 4: API Gateway 설정

이제 API 게이트웨이를 설정 할 시간입니다. API 게이트웨이는 프록시 역할을합니다. 클라이언트에서 Lambda 함수로 오는 HTTP 요청을 전달하며이 "트릭"을 사용하여 원래의 HTTP 요청이 전송됩니다 (GET, POST 등)

<div class="content-ad"></div>

먼저 API 게이트웨이 REST API를 설정하고 두 가지 API 리소스를 만듭니다. 각각의 경로(/dishes 및 /dish)를 위한 한 가지씩:

```js
# api_gateway.tf

# API 게이트웨이
resource "aws_api_gateway_rest_api" "API-gw" {
  name        = "lambda_rest_api"
  description = "시칠리아 요리를 위한 REST API입니다."
  endpoint_configuration {
    types = ["REGIONAL"]
  }
}

# "/dishes" 경로를 위한 API 리소스
resource "aws_api_gateway_resource" "API-resource-dishes" {
  rest_api_id = aws_api_gateway_rest_api.API-gw.id
  parent_id   = aws_api_gateway_rest_api.API-gw.root_resource_id
  path_part   = "dishes"
}

# "/dish" 경로를 위한 API 리소스
resource "aws_api_gateway_resource" "API-resource-dish" {
  rest_api_id = aws_api_gateway_rest_api.API-gw.id
  parent_id   = aws_api_gateway_rest_api.API-gw.root_resource_id
  path_part   = "dishes"
}
```

우리가 원하는 API 엔드포인트는 다음과 같습니다:

- GET /dishes: 모든 시칠리아 요리의 목록을 가져옵니다.
- GET /dishes/'dishId': ID에 따라 특정 요리의 세부 정보를 가져옵니다.
- POST /dishes: 새로운 시칠리아 요리를 데이터베이스에 추가합니다.
- PATCH /dishes/'dishId': 특정 요리의 세부 정보를 업데이트합니다.
- DELETE /dishes/'dishId': 데이터베이스에서 시칠리아 요리를 삭제합니다.

<div class="content-ad"></div>

각 HTTP 메서드에 대해 아래와 같이 몇 가지 블록을 정의합니다:

- Method (HTTP 메서드 지정)
- Integration (Lambda와 통합)
- Method response
- Integration response

![이미지](/assets/img/2024-05-23-AStep-by-StepGuideOnDeployingRESTAPIusingAPIGatewayLambdaDynamoDBCognitoTerraform_2.png)

“REST API”는 만들 API Gateway 객체 모두를 담고 있는 컨테이너입니다.

<div class="content-ad"></div>

API Gateway에 들어오는 모든 요청은 다음과 일치해야 합니다:

- 구성된 리소스 (특정한 /dish 또는 다른 /dishes)
- HTTP 메서드

API 게이트웨이 리소스의 각 메서드는 Lambda 함수로 들어오는 요청이 보내지는 통합을 가지고 있습니다. "AWS_PROXY" 통합 유형은 API 게이트웨이가 AWS Lambda API를 호출하여 Lambda 함수의 "invocation"을 생성하도록합니다. 그런 다음 우리는 메서드 응답(관련된 상태 코드로) 및 통합 응답을 구성합니다.

```js
# . . .

# Lambda 함수를 트리거하는 API 게이트웨이 정의
resource "aws_api_gateway_rest_api" "API-gw" {
  name        = "lambda_rest_api"
  description = "이것은 시칠리아 요리를 위한 REST API입니다."
  endpoint_configuration {
    types = ["REGIONAL"]
  }
}

resource "aws_api_gateway_resource" "API-resource-dish" {
  rest_api_id = aws_api_gateway_rest_api.API-gw.id
  parent_id   = aws_api_gateway_rest_api.API-gw.root_resource_id
  path_part   = "dish"
}

resource "aws_api_gateway_resource" "API-resource-dishes" {
  rest_api_id = aws_api_gateway_rest_api.API-gw.id
  parent_id   = aws_api_gateway_rest_api.API-gw.root_resource_id
  path_part   = "dishes"
}

#####################################################################################################
########################### GET ALL /dishes #########################################################
#####################################################################################################

resource "aws_api_gateway_method" "GET_all_method" {
  rest_api_id   = aws_api_gateway_rest_api.API-gw.id
  resource_id   = aws_api_gateway_resource.API-resource-dishes.id
  http_method   = "GET"
  authorization = "NONE"
}

. . .
```

<div class="content-ad"></div>

다음으로 API 게이트웨이가 람다 함수를 호출할 수 있도록 설정해야 합니다:

```js
# . . .

# API 게이트웨이가 람다에 접근할 수 있도록 허용
resource "aws_lambda_permission" "apigw" {
  statement_id  = "AllowAPIGatewayInvoke"
  action        = "lambda:InvokeFunction"
  function_name = aws_lambda_function.my-lambda-function.function_name
  principal     = "apigateway.amazonaws.com"

  # 여기서 /*/* 부분은 "REST API" 내에서 어떤 리소스의 어떤 메서드에서도 접근할 수 있게 합니다.
  source_arn = "${aws_api_gateway_rest_api.API-gw.execution_arn}/*/*"
}
```

그런 다음 API 배포를 구성하고 "prod"라는 스테이지를 생성하여, API 게이트웨이 URL이 "/prod/dishes"와 같은 형태가 됩니다:

```js
# . . .

# 배포
resource "aws_api_gateway_deployment" "example" {

  depends_on = [
    aws_api_gateway_integration.GET_one_lambda_integration,
    aws_api_gateway_integration.GET_all_lambda_integration,
    aws_api_gateway_integration.PATCH_lambda_integration,
    aws_api_gateway_integration.POST_lambda_integration,
    aws_api_gateway_integration.DELETE_lambda_integration
  ]

  triggers = {
    redeployment = sha1(jsonencode([
      aws_api_gateway_resource.API-resource-dish,
      aws_api_gateway_method.GET_one_method,
      aws_api_gateway_integration.GET_one_lambda_integration,
      aws_api_gateway_method.GET_all_method,
      aws_api_gateway_integration.GET_all_lambda_integration,
      aws_api_gateway_method.POST_method,
      aws_api_gateway_integration.POST_lambda_integration,
      aws_api_gateway_method.PATCH_method,
      aws_api_gateway_integration.PATCH_lambda_integration,
      aws_api_gateway_method.DELETE_method,
      aws_api_gateway_integration.DELETE_lambda_integration
    ]))
  }

  rest_api_id = aws_api_gateway_rest_api.API-gw.id
}

# 배포 스테이지
resource "aws_api_gateway_stage" "my-prod-stage" {
  deployment_id = aws_api_gateway_deployment.example.id
  rest_api_id   = aws_api_gateway_rest_api.API-gw.id
  stage_name    = "prod"

  depends_on = [aws_cloudwatch_log_group.rest-api-logs]
}
```

<div class="content-ad"></div>

백그라운드에서 요청을 보낼 때 무엇이 일어나는지 기록하기 위해 CloudWatch 로그 그룹을 설정하고 있습니다. CloudWatch LogGroup의 이름은 API-Gateway-Execution-Logs\_'YOUR_API_ID'/'YOUR_STAGE_NAME' 형식이어야 합니다.

그런 다음 API Gateway 스테이지 수준 실행 로깅을 설정하기 위해 "method_settings" 리소스를 사용합니다.

```js
# . . .

# 디버깅 목적의 CloudWatch 로그 그룹
resource "aws_cloudwatch_log_group" "rest-api-logs" {
  name              = "API-Gateway-Execution-Logs_${aws_api_gateway_rest_api.API-gw.id}/prod"
  retention_in_days = 7
}

# 메서드 설정
resource "aws_api_gateway_method_settings" "my_settings" {
  rest_api_id = aws_api_gateway_rest_api.API-gw.id
  stage_name  = aws_api_gateway_stage.my-prod-stage.stage_name
  method_path = "*/*"
  settings {
    logging_level = "INFO"
    data_trace_enabled = true
    metrics_enabled = true
  }
}
```

다음으로 CORS 모듈을 정의합니다. AWS 문서는 CORS 및 통합 및 통합 응답과 관련된 모든 뉘앙스를 잘 설명하고 있으므로 여기에 링크만 첨부하겠습니다.

<div class="content-ad"></div>

```js
# . . .

module "cors" {
  source = "./modules/cors"

  api_id            = aws_api_gateway_rest_api.API-gw.id
  api_resource_id   = aws_api_gateway_resource.API-resource-dish.id
  allow_credentials = true
}
```

![Image](/assets/img/2024-05-23-AStep-by-StepGuideOnDeployingRESTAPIusingAPIGatewayLambdaDynamoDBCognitoTerraform_3.png)

이 구조를 따라가서 각 파일에 다음 코드를 붙여넣으세요.

cors.tf:



<div class="content-ad"></div>

```js
# cors.tf

resource "aws_api_gateway_method" "_" {
  rest_api_id   = var.api_id
  resource_id   = var.api_resource_id
  http_method   = "OPTIONS"
  authorization = "NONE"
}

# aws_api_gateway_integration._
resource "aws_api_gateway_integration" "_" {
  rest_api_id = var.api_id
  resource_id = var.api_resource_id
  http_method = aws_api_gateway_method._.http_method

  type = "MOCK"

  request_templates = {
    "application/json" = "{ \"statusCode\": 200 }"
  }
}

# aws_api_gateway_integration_response._
resource "aws_api_gateway_integration_response" "_" {
  rest_api_id = var.api_id
  resource_id = var.api_resource_id
  http_method = aws_api_gateway_method._.http_method
  status_code = 200

  response_parameters = local.integration_response_parameters

  depends_on = [
    aws_api_gateway_integration._,
    aws_api_gateway_method_response._,
  ]
}

# aws_api_gateway_method_response._
resource "aws_api_gateway_method_response" "_" {
  rest_api_id = var.api_id
  resource_id = var.api_resource_id
  http_method = aws_api_gateway_method._.http_method
  status_code = 200

  response_parameters = local.method_response_parameters

  response_models = {
    "application/json" = "Empty"
  }

  depends_on = [
    aws_api_gateway_method._,
  ]
}
```

headers.tf:

```js
# headers.tf

locals {
  headers = tomap({
     "Access-Control-Allow-Headers"= "'${join(",", var.allow_headers)}'",
    "Access-Control-Allow-Methods"= "'${join(",", var.allow_methods)}'",
    "Access-Control-Allow-Origin"= "'${var.allow_origin}'",
    "Access-Control-Max-Age"= "'${var.allow_max_age}'",
    "Access-Control-Allow-Credentials"= var.allow_credentials ? "'true'" : ""
  })

  # Pick non-empty header values
  header_values = compact(values(local.headers))

  # Pick names that from non-empty header values
  header_names = matchkeys(
    keys(local.headers),
    values(local.headers),
    local.header_values
  )

  # Parameter names for method and integration responses
  parameter_names = formatlist("method.response.header.%s", local.header_names)

  # Map parameter list to "true" values
  true_list = split("|",
    replace(join("|", local.parameter_names), "/[^|]+/", "true")
  )

  # Integration response parameters
  integration_response_parameters = zipmap(
    local.parameter_names,
    local.header_values
  )

  # Method response parameters
  method_response_parameters = zipmap(
    local.parameter_names,
    local.true_list
  )
}
```

variables.tf:

<div class="content-ad"></div>

```json
변수 "api_id" {
  설명 = "API 식별자"
}

# var.api_resource_id
변수 "api_resource_id" {
  설명 = "API 리소스 식별자"
}

# -----------------------------------------------------------------------------
# Variables: CORS-related
# -----------------------------------------------------------------------------

# var.allow_headers
변수 "allow_headers" {
  설명 = "허용 헤더"
  유형 = list(string)

  기본값 = [
    "Authorization",
    "Content-Type",
    "X-Amz-Date",
    "X-Amz-Security-Token",
    "X-Api-Key",
  ]
}

# var.allow_methods
변수 "allow_methods" {
  설명 = "허용 메소드"
  유형 = list(string)

  기본값 = [
    "OPTIONS",
    "HEAD",
    "GET",
    "POST",
    "PUT",
    "PATCH",
    "DELETE",
  ]
}

# var.allow_origin
변수 "allow_origin" {
  설명 = "허용 출처"
  유형 = string
  기본값 = "*"
}

# var.allow_max_age
변수 "allow_max_age" {
  설명 = "응답 캐싱 시간을 허용"
  유형 = string
  기본값 = "7200"
}

# var.allowed_credentials
변수 "allow_credentials" {
  설명 = "자격 증명 허용"
  기본값 = false
}
```

마지막으로 "outputs.tf" 파일에 아래와 같이 API Gateway를 적용한 후의 호출 URL을 출력하는 블록을 선언하세요:

```json
# 테스트 API Gateway URL
output "api_gateway_url" {
  value = aws_api_gateway_deployment.example.invoke_url
}
```

# 단계 5: 코그니토로 인증 추가하기

`

<div class="content-ad"></div>

다이나모DB 테이블의 작업(POST, PATCH, DELETE)은 위험할 수 있습니다. REST API를 공개적으로 노출하고 싶지 않으므로 일부 HTTP 엔드포인트에 대한 액세스를 제한하고 싶습니다. 따라서 인증을 구현하고자 하는데, 첫 번째 단계는 Cognito 사용자 풀을 생성하는 것입니다.

```js
# authentication.tf

resource "aws_cognito_user_pool" "pool" {
  name = "mypool"
}
```

응용 프로그램이 사용자 풀에 액세스할 수 있도록하려면 사용자 풀 클라이언트를 정의해야 합니다. 우리는 기본 사용자 정보(email, openid, profile)에 대한 허용된 OAuth 플로 및 사용자 스코프를 명시하고 있습니다. 클라이언트 시크릿을 생성하지 않습니다. 또한 관리자 및 사용자 비밀번호 인증이 모두 허용됩니다. Cognito가 식별 제공자입니다. 그런 다음 OAuth 2.0 인증 서버가 사용자를 성공적으로 인증한 후 사용자를 리디렉션해야 할 위치 및 로그아웃 후 리디렉션할 위치가 정의됩니다. 어쨌든 이 프로젝트에 대해서는 이렇게까지 자세히 묘사하는 것은 그리 중요하지 않습니다.

```js
# authentication.tf

resource "aws_cognito_user_pool_client" "client" {
  name = "client"
  allowed_oauth_flows_user_pool_client = true
  generate_secret = false
  allowed_oauth_scopes = ["aws.cognito.signin.user.admin","email", "openid", "profile"]
  allowed_oauth_flows = ["implicit", "code"]
  explicit_auth_flows = ["ADMIN_NO_SRP_AUTH", "USER_PASSWORD_AUTH"]
  supported_identity_providers = ["COGNITO"]

  user_pool_id = aws_cognito_user_pool.pool.id
  callback_urls = ["https://example.com"]
  logout_urls = ["https://example.com"]
}
```

<div class="content-ad"></div>

유저 풀 내에서 API 액세스를 테스트하기 위해 유저도 생성합니다.

```js
# authentication.tf

resource "aws_cognito_user" "example" {
  user_pool_id = aws_cognito_user_pool.pool.id
  username = "mattia"
  password = "Test@123"
}
```

이 구성을 적용하여 모든 리소스가 올바르게 생성되었는지 확인해보세요 (유저 풀, 유저 풀 클라이언트 및 유저). AWS 관리 콘솔에서 “Amazon Cognito”로 이동하여 “User pools”를 선택하고 방금 생성한 풀을 클릭합니다. User pool ID를 메모하세요.

<img src="/assets/img/2024-05-23-AStep-by-StepGuideOnDeployingRESTAPIusingAPIGatewayLambdaDynamoDBCognitoTerraform_4.png" />

<div class="content-ad"></div>

새 사용자도 확인할 수 있습니다:

![이미지](/assets/img/2024-05-23-AStep-by-StepGuideOnDeployingRESTAPIusingAPIGatewayLambdaDynamoDBCognitoTerraform_5.png)

“앱 통합”을 클릭하고 “앱 클라이언트 및 분석”으로 내려가면 우리가 만든 클라이언트도 확인할 수 있습니다. 클라이언트 ID를 메모해 두세요.

![이미지](/assets/img/2024-05-23-AStep-by-StepGuideOnDeployingRESTAPIusingAPIGatewayLambdaDynamoDBCognitoTerraform_6.png)

<div class="content-ad"></div>

인증을 실제로 구현하고 애플리케이션의 일부 API 엔드포인트에 대한 공개 액세스를 제한하려면 먼저 Cognito 사용자 풀 내에서 Authorizer를 정의해야 합니다. Authorizer가 활성화되면 Lambda가 트리거되기 전에 수신된 요청 토큰이 먼저 이 Cognito 사용자 풀과 대조되어야 합니다. 따라서 "api_gateway.tf"에서 Authorizer를 정의합니다:

```js
# api_gateway.tf

resource "aws_api_gateway_authorizer" "demo" {
  name = "my_apig_authorizer2"
  rest_api_id = aws_api_gateway_rest_api.API-gw.id
  type = "COGNITO_USER_POOLS"
  provider_arns = [aws_cognito_user_pool.pool.arn]
}
```

기억하시나요? HTTP 메서드의 리소스 블록을 정의할 때 "authorization"을 "NONE"으로 설정했던 것을요. 이제 이 값을 변경하여 "COGNITO_USER_POOLS"로 설정하고 Authorizer ID를 지정하려고 합니다. 예를 들어 POST HTTP 메서드의 경우:

```js
# api_gateway.tf

#####################################################################################################
########################### POST /dish #########################################################
#####################################################################################################

resource "aws_api_gateway_method" "POST_method" {
  rest_api_id   = aws_api_gateway_rest_api.API-gw.id
  resource_id   = aws_api_gateway_resource.API-resource-dish.id
  http_method   = "POST"
  # authorization = "NONE" // 주석 처리
  authorization = "COGNITO_USER_POOLS"
  authorizer_id = aws_api_gateway_authorizer.demo.id
}
```

<div class="content-ad"></div>

위의 모든 중요한 엔드포인트(POST, PATCH, DELETE)에 대해 이 작업을 수행하십시오.

더불어 이 구성을 적용하면 Postman으로 새 요청을 보내면 401 Unauthorized가 반환될 것입니다:

![image](/assets/img/2024-05-23-AStep-by-StepGuideOnDeployingRESTAPIusingAPIGatewayLambdaDynamoDBCognitoTerraform_7.png)

이제 HTTP 요청을 제출할 때 인가를 받기 위해 액세스 토큰을 제공해야 합니다. 액세스 토큰을 생성하려면 이전에 기록한 정보를 사용하여 "aws cognito-idp" 명령을 사용할 것입니다:

<div class="content-ad"></div>

```md
aws cognito-idp admin-initiate-auth --user-pool-id <USER_POOL_ID> --client-id <CLIENT_ID> --auth-flow ADMIN_NO_SRP_AUTH --auth-parameters USERNAME=mattia,PASSWORD=Test@123
```

위 명령어에서 User Pool ID, User Pool client ID, 그리고 이전에 정의한 테스트 사용자의 사용자 이름과 암호를 교체해야 합니다.

우리는 Cognito 사용자 풀에 대한 테스트 사용자를 인증하고, 그 결과로 액세스 토큰을 받습니다. 위 명령어의 출력은 아래와 유사합니다:

```md
{
"ChallengeParameters": {},
"AuthenticationResult": {
"AccessToken": <ACCESS_TOKEN>,
"ExpiresIn": 3600,
"TokenType": "Bearer",
"RefreshToken": <REFRESH_TOKEN>,
"IdToken": <ID_TOKEN> # ID 토큰의 값을 복사하세요
}
}
```

<div class="content-ad"></div>

이 토큰들을 테스트하려면 ID 토큰의 값을 복사하고 AWS 관리 콘솔에서 "API Gateway"로 이동한 다음, API를 선택하고 왼쪽에 있는 "Authorizers"를 클릭하세요:

![API Gateway Authorizer Test](/assets/img/2024-05-23-AStep-by-StepGuideOnDeployingRESTAPIusingAPIGatewayLambdaDynamoDBCognitoTerraform_8.png)

이제 Authorizer를 클릭하세요. 그런 다음 Authorizer 테스트 섹션에 이전에 복사한 ID 토큰을 붙여넣고 "Test authorizer" 버튼을 클릭하세요. 파란 상자 안의 그것과 같은 출력이 있어야 합니다:

![Authorizer Test Output](/assets/img/2024-05-23-AStep-by-StepGuideOnDeployingRESTAPIusingAPIGatewayLambdaDynamoDBCognitoTerraform_9.png)

<div class="content-ad"></div>

"토큰 만료 날짜인 'exp' 필드가 있는 것을 확인할 수 있습니다. 제 경우에는 유효합니다.

이제 Postman으로 돌아가서 "Headers" 탭으로 이동하여 새 필드를 만들고 키를 "Authorization"로 선택한 후 값 필드에 다음 형식으로 ID 토큰을 지정하세요:

```js
Bearer <ID_TOKEN>
```

<img src="/assets/img/2024-05-23-AStep-by-StepGuideOnDeployingRESTAPIusingAPIGatewayLambdaDynamoDBCognitoTerraform_10.png" />"

<div class="content-ad"></div>

요청을 보내시면 지금은 200 상태 코드를 받게 될 거에요. 모든 것이 잘 되고 있어요.

# 결론

이 프로젝트를 좋아해 주셨으면 좋겠고, 다음에 또 만나요! 궁금한 점 있으면 언제나 물어봐 주세요!
