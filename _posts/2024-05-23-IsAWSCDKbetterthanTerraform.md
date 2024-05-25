---
title: "AWS CDK이 Terraform보다 뛰어난가요"
description: ""
coverImage: "/assets/img/2024-05-23-IsAWSCDKbetterthanTerraform_0.png"
date: 2024-05-23 14:35
ogImage: 
  url: /assets/img/2024-05-23-IsAWSCDKbetterthanTerraform_0.png
tag: Tech
originalTitle: "Is AWS CDK better than Terraform?"
link: "https://medium.com/@kvs-vishnu23/is-aws-cdk-better-than-terraform-85194e7a42cd"
---


이 기사에서는 클라우드 인프라를 유지하는 데 AWS CDK를 사용하는 장점과 이해를 돕기 위한 코드 스니펫에 대해 설명하겠습니다.

![AWS CDK vs Terraform](/assets/img/2024-05-23-IsAWSCDKbetterthanTerraform_0.png)

저는 AWS 클라우드 엔지니어로 일하고 있으며, AWS 환경 내에서 내 프로젝트의 클라우드 인프라를 관리하는 역할을 맡고 있습니다. 이 역할에서 저는 Terraform과 AWS CDK를 모두 활용해 왔습니다. 제 경험과 인사이트를 바탕으로, 각각의 우세한 점에 대해 제 생각을 공유하겠습니다.

그러나 그에 앞서, IaC를 이해해 보겠습니다.

<div class="content-ad"></div>

# IaC(Infrastructure as Code)이란 무엇인가요?

IaC 또는 Infrastructure as Code는 클라우드 인프라가 코드를 통해 프로비저닝되고 관리되는 소프트웨어 엔지니어링 방법론입니다. 이는 AWS 콘솔과 같은 수동 프로세스나 대화형 구성 도구를 통해 관리하는 것이 아니라 코드를 통해 인프라를 구축하는 방식을 의미합니다.

간단히 말해, 콘솔을 통해 수동으로 생성하는 대신 인프라를 배포할 코드를 작성하는 것입니다. 이는 산업의 표준적인 실천 방법입니다. 현재 클라우드 인프라에 IaC를 적용하지 않는 조직은 없다고 생각해요.

IaC를 적용하는 주요 이유는 재사용성, 일관성, 자동화 및 확장성입니다.

<div class="content-ad"></div>

이제 Terraform과 AWS CDK를 이해해 봅시다.

## Terraform

Terraform은 HashiCorp가 개발한 IaC 도구입니다. Terraform을 사용하면, 인프라 구성은 도메인 특화 언어인 HashiCorp 구성 언어 (HCL)를 사용하여 코드로 정의되어 버전 관리, 협업 및 자동화가 가능해집니다. Terraform은 그런 다양한 자원들의 전체 라이프사이클을 관리하며, 프로비저닝부터 업데이트, 파괴까지 정의된 설정 파일을 기반으로 합니다.

S3 버킷을 생성하는 Terraform 스크립트의 간단한 예제입니다.

<div class="content-ad"></div>

```tf
# main.tf

provider "aws" {
  region = "us-east-1"  # 원하는 AWS 지역을 설정하세요
}

resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-unique-bucket-name"  # 원하는 버킷 이름을 설정하세요 (전역적으로 고유해야 함)
}
```

# AWS CDK

AWS CDK(Cloud Development Kit)는 AWS가 개발한 오픈소스 프로젝트로, 클라우드 리소스를 프로비저닝하기 위한 더 높은 수준의 추상화를 제공합니다.

AWS CDK를 사용하면 Typescript, Python, Java, Go 등과 같은 익숙한 프로그래밍 언어를 사용하여 인프라 코드를 작성할 수 있습니다.

<div class="content-ad"></div>

AWS CDK를 사용하여 Typescript로 S3 버킷을 만드는 간단한 예제입니다.

```typescript
import * as cdk from 'aws-cdk-lib';
import { Stack, StackProps } from 'aws-cdk-lib';
import * as s3 from 'aws-cdk-lib/aws-s3';

export class MyS3BucketStack extends Stack {
  constructor(scope: cdk.Construct, id: string, props?: StackProps) {
    super(scope, id, props);

    // S3 버킷 생성
    new s3.Bucket(this, 'MyBucket', {
      bucketName: 'my-unique-bucket-name', // 원하는 버킷 이름으로 변경하세요
    });
  }
}

// 애플리케이션 생성
const app = new cdk.App();
new MyS3BucketStack(app, 'MyS3BucketStack');
```

이제 Terraform 대 AWS CDK 논쟁에 대해 깊게 알아보겠습니다.

# Terraform vs AWS CDK

<div class="content-ad"></div>

그것말고는 실제로 표 스타일이 아니라지만, 저희가 그 내용을 잘 이해할 수 있도록 글의 형식으로 바꿔 드릴게요.

한 가지씩 살펴보며 이 도구들의 차이를 이해해 보겠습니다. 몇 가지 예시를 들어 몇 가지 포인트를 검증하겠습니다. 비교를 더 잘 이해하기 위해 마지막 부분만 주목해주세요.

## 지원

Terraform은 클라우드에 중립적입니다. 즉, AWS, Azure, GCP, Alibaba 등 여러 클라우드 제공업체를 지원합니다.

AWS CDK는 AWS 팀이 특히 AWS 클라우드용으로 만들었습니다.

<div class="content-ad"></div>

만약 다양한 클라우드 공급업체에서 작업 중이라면, Terraform이 최상의 선택일 것입니다. 몇몇 클라우드 공급업체는 자체 IaC 도구인 Azure의 Bicep과 같은 것들이 있지만, 아직 채택 초기 단계에 있습니다.

## 언어

이전에 언급했듯이, Terraform은 Hashicorp에서 특별히 만든 HCL(HashiCorp Configuration Language)을 사용합니다. HCL은 JSON과 유사한 구성 언어로, 배우기 쉽습니다.

AWS CDK는 Typescript, Python, Java 등과 같은 일반 목적 프로그래밍 언어를 지원합니다. 따라서 새로운 언어를 배울 필요가 없습니다. 익숙한 프로그래밍 언어를 선택하고 IaC 작성을 시작할 수 있습니다.

<div class="content-ad"></div>

프로그래밍 언어는 견고하고 유연합니다. IaC에서 OOPS, 함수형 프로그래밍과 같은 다양한 프로그래밍 패러다임을 사용할 수 있어요. 이는 DSL(Domain Specific language)보다 많은 장점을 가지고 있어요.

## 상태 관리

테라폼은 terraform.tfstate 파일을 사용해 배포된 리소스의 상태를 저장해요. 이 파일은 인프라의 현재 상태와 배포된 리소스 및 구성을 추적합니다. 일반적으로 이 상태 파일은 중앙에 저장되며, 종종 S3 버킷에 저장되어 다양한 팀 간의 협업을 용이하게 합니다.

제 경험 상으로, 테라폼 상태를 관리하는 것이 꽤 복잡할 수 있다는 것을 알았어요. 프로젝트 내에서 상태의 드리프트를 마주치는 것이 일반적이며, 이를 해결하는 데 시간이 많이 소요될 수 있어요.

<div class="content-ad"></div>

AWS CDK는 상태가 없는 방식으로 작동합니다. 상태를 관리하기 위해 Git과 같은 버전 관리 시스템을 신뢰합니다. 배포할 때마다 최신 변경 사항을 가져와서 발생하는 모든 충돌을 해결하고 배포를 진행합니다. 이 방식은 깔끔하며 일관성을 유지합니다.

## 버전 관리

우리는 AWS가 계속해서 서비스를 확장하고 있다는 것에 익숙합니다. Terraform은 실행 파일로서 매번 최신 버전을 다운로드하고 업데이트해야 하므로 인프라 코드 업데이트가 어려운 작업이 됩니다. 우리 조직의 많은 프로젝트들은 여전히 오래된 버전에 의존하여 유지보수에 어려움을 겪고 있습니다.

그에 반해 AWS CDK는 AWS가 직접 유지하고 있기 때문에 의존성을 간단히 관리하여 신속한 업데이트를 제공합니다. 이를 통해 시간이 지남에 따라 부드러운 전환과 더 적은 호환성 문제를 보장합니다.

<div class="content-ad"></div>

## 연결성

이해하기 위해, 바로 예제로 들어가 봅시다.

예를 들어, S3 버킷에 객체가 생성될 때마다 람다를 트리거해야 하는 시나리오를 생각해 봅시다. 이를 위해 S3 이벤트 알림과 람다를 결합하여 이를 달성할 수 있습니다.

아래 테라폼 코드를 살펴봅시다.

<div class="content-ad"></div>

```markdown
```terraform
provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-bucket"
  acl    = "private"
}

resource "aws_lambda_function" "my_function" {
  filename      = "lambda_function_payload.zip"
  function_name = "my-function"
  role          = aws_iam_role.lambda_exec_role.arn
  handler       = "index.handler"
  runtime       = "nodejs14.x"
}

resource "aws_lambda_permission" "s3_invoke_permission" {
  statement_id  = "AllowExecutionFromS3Bucket"
  action        = "lambda:InvokeFunction"
  function_name = aws_lambda_function.my_function.function_name
  principal     = "s3.amazonaws.com"
  source_arn    = aws_s3_bucket_notification.s3_notification.arn
}

resource "aws_s3_bucket_notification" "s3_notification" {
  bucket = aws_s3_bucket.my_bucket.id

  lambda_function {
    lambda_function_arn = aws_lambda_function.my_function.arn
    events              = ["s3:ObjectCreated:*"]
  }
}
```

Terraform에서 모든 것을 리소스로 취급하는 개념을 따라갑니다. 먼저 Lambda 및 S3 버킷과 같은 리소스를 정의합니다. 그런 다음 Lambda 함수와 S3 객체 간의 연결을 설정하기 위해 권한을 부여해야 합니다. 이는 aws_lambda_permission 리소스를 사용하여 달성됩니다. 권한이 부여된 후 Lambda 함수와 S3 알림을 연결하는 데 또 다른 리소스인 aws_s3_bucket_notification이 필요합니다.

이제 CDK 코드를 살펴봅시다.

```typescript
import { aws_lambda_nodejs as lambda_nodejs } from 'aws-cdk-lib';
import { aws_s3 as s3 } from 'aws-cdk-lib';
import { aws_s3_notifications as s3notifications } from 'aws-cdk-lib';
import { App, Stack, StackProps } from 'aws-cdk-lib';
import { Construct } from 'constructs';

export class MyStack extends Stack {
  constructor(scope: Construct, id: string, props?: StackProps) {
    super(scope, id, props);

    // S3 버킷 생성
    const bucket = new s3.Bucket(this, 'MyBucket');

    // Lambda 함수 생성
    const fn = new lambda_nodejs.NodejsFunction(this, 'MyFunction', {
      runtime: lambda_nodejs.Runtime.NODEJS_14_X,
      handler: 'handler',
      entry: 'lambda/index.ts',
    });

    // S3 버킷에 대한 이벤트 소스 추가하여 Lambda 함수를 트리거합니다
    fn.addEventSource(new s3notifications.S3EventSource(bucket, {
      events: [s3.EventType.OBJECT_CREATED],
      filters: [{ prefix: 'uploads/' }], // 필요에 따라 접두사 조정
    }));
  }
}

const app = new App();
new MyStack(app, 'MyStack');
```
```

<div class="content-ad"></div>

위의 코드에서는 먼저 S3 버킷을 생성한 다음 람다 함수를 생성합니다. 람다 함수 코드 자체 안에서 S3를 이벤트 소스로 생성할 수 있음을 알 수 있습니다. 이것은 멋지며 프로그래밍의 힘을 보여줍니다.

위의 코드는 모든 것을 Terraform의 리소스 블록을 사용하는 것보다 훨씬 이해하기 쉽습니다.

## 보안

AWS 내에서 보안을 논의할 때 IAM(Identity and Access Management)이 중심에 있습니다. IAM 역할과 정책은 사용자 액세스를 관리하고 서비스 간 안전한 통신을 용이하게 하는 데 사용되는 중요한 구성 요소입니다.

<div class="content-ad"></div>

테라폼 코드에서는 aws_lambda_permission 리소스 블록을 사용하여 S3이 람다를 호출할 수 있도록 필요한 권한을 부여했습니다. 그러나 CDK 코드에서는 그런 작업을 하지 않았습니다. CDK가 IAM 권한을 내부적으로 설정하기 때문입니다. CDK는 필요한 권한만 부여하여 더 안전합니다.

다른 예시를 살펴봅시다. 람다 함수가 DynamoDB 테이블에 데이터를 추가하는 경우를 생각해보겠습니다. 인프라 개발자로서 우리는 람다가 어떻게 작동하는지 신경 쓸 필요가 없습니다. 우리는 람다와 DDB 테이블을 생성하고 람다가 DynamoDB에 데이터를 쓸 수 있는 권한을 부여하기만 하면 됩니다.

먼저 CDK 코드를 살펴보겠습니다.

```js
import * as cdk from 'aws-cdk-lib';
import { Construct } from 'constructs';
import * as lambda from 'aws-cdk-lib/aws-lambda';
import * as dynamodb from 'aws-cdk-lib/aws-dynamodb';

export class MyStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // DynamoDB 테이블 생성
    const table = new dynamodb.Table(this, 'MyTable', {
      partitionKey: { name: 'id', type: dynamodb.AttributeType.STRING },
      billingMode: dynamodb.BillingMode.PAY_PER_REQUEST,
    });

    // 람다 함수 생성
    const myLambda = new lambda.Function(this, 'MyLambda', {
      code: lambda.Code.fromAsset('path/to/your/lambda/code'), // 필요한 경우 경로를 조정
      handler: 'index.handler',
      runtime: lambda.Runtime.NODEJS_14_X,
    });

    // 람다 함수에 DynamoDB 테이블 쓰기 권한 부여
    table.grantReadWriteData(myLambda);
  }
}

const app = new cdk.App();
new MyStack(app, 'MyStack');
```

<div class="content-ad"></div>

마음에 드실 정도로 간결하고 깔끔한 코드입니다. 우리는 ddb 테이블과 람다를 생성했습니다. 그 다음으로는 ddb 테이블의 grantReadWriteData 메서드를 사용하여 해당 람다를 전달했습니다. 이것으로 모든 필요한 IAM 권한을 처리합니다.

이제 Terraform에서 같은 코드를 살펴보겠습니다.

```js
provider "aws" {
  region = "us-east-1"
}

resource "aws_dynamodb_table" "my_table" {
  name           = "MyTable"
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "id"

  attribute {
    name = "id"
    type = "S"
  }
}

resource "aws_iam_role" "lambda_execution_role" {
  name = "lambda_execution_role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "lambda.amazonaws.com"
        }
      },
    ]
  })
}

resource "aws_iam_policy" "ddb_access_policy" {
  name        = "ddb_access_policy"
  description = "람다가 DynamoDB 테이블에 액세스할 수 있는 정책"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = [
          "dynamodb:PutItem",
          "dynamodb:UpdateItem",
          "dynamodb:GetItem",
          "dynamodb:Scan",
          "dynamodb:Query",
          "dynamodb:DeleteItem"
        ]
        Effect   = "Allow"
        Resource = aws_dynamodb_table.my_table.arn
      },
    ]
  })
}

resource "aws_iam_role_policy_attachment" "ddb_access_attachment" {
  role       = aws_iam_role.lambda_execution_role.name
  policy_arn = aws_iam_policy.ddb_access_policy.arn
}

resource "aws_lambda_function" "my_lambda" {
  function_name = "MyLambdaFunction"

  filename         = "이동/전달/패키지.zip의/경로"
  source_code_hash = filebase64sha256("이동/전달/패키지.zip의/경로")
  handler          = "index.handler"
  runtime          = "nodejs14.x"
  role             = aws_iam_role.lambda_execution_role.arn
}
```

이 코드가 얼마나 지루한지 보이실 것입니다. 먼저 ddb 테이블을 생성하고, 그런 다음 람다 역할을 만듭니다. 이후 필요한 권한을 지정하는 정책을 만들고 해당 역할에 첨부합니다. 마지막으로 람다를 만들고 이 역할을 사용합니다.

<div class="content-ad"></div>

이 코드는 너무 길고 정책 문서에서 수동으로 권한을 지정하고 있습니다. 이렇게 하면 올바른 권한을 지정하지 않으면 액세스 문제가 발생할 수 있습니다.

## 통합

AWS CDK는 AWS SDK와 강력한 통합을 가지고 있습니다. 더 잘 이해하기 위해 람다 함수를 예로 들어봅시다.

AWS 람다가 리눅스를 지원하고 있으며 Amd 및 Arm 기반의 두 가지 CPU 아키텍처만 지원한다는 점을 알고 있습니다. 또한 AWS는 파이썬, Node.js, Go 등 다양한 런타임을 지원합니다.

<div class="content-ad"></div>

이제, 만약 람다 함수가 ARM 기반과 파이썬 런타임을 사용하고 개발자가 Windows 기계에서 작업 중이라면, 그는 OS에서 번들을 생성할 수 없을 것입니다. Lambda 런타임과 호환되지 않을 것입니다. 이 문제는 ARM 기반 리눅스 이미지를 사용하여 코드를 빌드하는 파이프라인을 사용하면 해결할 수 있습니다.

CDK에서는 코드를 쉽게 Lambda에 배포할 수 있습니다. AWS CDK는 다른 런타임에 대해 다른 함수를 제공합니다.

```js
@aws-cdk/aws-lambda-python-alpha » PythonFunction
aws-cdk-lib » aws_lambda_nodejs » NodejsFunction
@aws-cdk/aws-lambda-go-alpha » GoFunction
```

패키지를 사용하면 람다 함수의 소스 코드를 가리킬 수 있고 코드를 번들로 만들 필요가 없습니다. 번들링 옵션을 직접 선택할 수 있습니다. Node.js의 경우 esbuild, 다른 런타임의 경우 docker가 좋은 선택지입니다.

<div class="content-ad"></div>

```js
private createGetData(): PythonFunction {
    const fn = new PythonFunction(this, "GetData", {
      entry: join(__dirname, "..", "src", "get-data"),
      runtime: lambda.Runtime.PYTHON_3_11,
      bundling: {
        assetExcludes: [".venv"],
        assetHashType: AssetHashType.SOURCE
      },
      timeout: Duration.minutes(2),
      index: "lambda_handler.py",
      handler: "lambda_handler",
      memorySize: 128
    });
    return fn;
  }
```

위의 코드에서는 람다 코드 위치를 entry 매개변수에만 지정했습니다. 런타임이 파이썬이기 때문에 lambda_handler.py 및 requirements.txt 두 파일만 필요합니다.

이 작업은 Terraform에서 수행할 수 없습니다.

참고: AWS에서 풀 스택 개발을 수행 중이거나 AWS SDK 및 동시에 AWS 인프라 생성에 작업 중인 경우에 매우 유용합니다.```

<div class="content-ad"></div>

# 마지막으로

내 의견으로는, AWS CDK는 논의된 요소를 기반으로 Terraform을 능가한다고 생각합니다. 그러나 이것은 프로젝트와 조직의 특정 요구 사항에 따라 달라질 수 있는 주관적인 의견으로 인식합니다. 고유한 요구 사항과 상황에 기반하여 적합한 인프라스트럭처를 코드로 관리하는 도구(IaC)를 평가하고 선택하는 것이 중요합니다.

# 내 프로젝트

저는 Candletower(www.candletower.com)라는 프로젝트를 만들었습니다. 이 웹사이트는 캔들스틱 패턴 분석을 기반으로 한 주식 시장 분석을 제공합니다. 투자를 하거나 주식 시장에 입문하려는 경우, 이 웹사이트를 꼭 확인해보세요. 광고 없음, 로그인 없음, 완전 무료입니다. 여러분의 생각을 알려주세요.

<div class="content-ad"></div>

마지막으로, 읽어 주셔서 감사합니다!