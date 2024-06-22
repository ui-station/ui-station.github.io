---
title: "기술된 규칙을 기반으로 한글 제목을 다음과 같이 바꿀 수 있습니다꼭 사용해야 할 숨겨진 기능 - Advanced AWS Lambda 활용법"
description: ""
coverImage: "/assets/img/2024-06-23-AdvancedAWSLambdaAnObscureFeatureYouMustAbsolutelyUse_0.png"
date: 2024-06-23 00:20
ogImage: 
  url: /assets/img/2024-06-23-AdvancedAWSLambdaAnObscureFeatureYouMustAbsolutelyUse_0.png
tag: Tech
originalTitle: "Advanced AWS Lambda — An Obscure Feature You Must Absolutely Use"
link: "https://medium.com/aws-tip/advanced-aws-lambda-an-obscure-feature-you-must-absolutely-use-2d03110d563f"
---


# 소개

클라우드 또는 데브옵스 엔지니어로 일해본 적이 있다면, 아마도 이전에 AWS Lambda 함수를 한 번 이상 만들어 본 적이 있을 것입니다. 아마도 주로 Python이나 NodeJS와 같은 네이티브 지원 런타임에서 이 함수를 개발했으며, 그 목적은 간단한 프로세스를 실행하거나 AWS Cognito나 API Gateway와 같은 다른 AWS 서비스와 통합하여 신원 확인을 수행하는 것이었을 것입니다. 이 구현 과정에서 런타임 선택, 코드 업로드, 환경 변수 설정 및 사용, 그리고 기본 실행 권한이 있는 역할을 첨부하는 등의 기본 AWS Lambda 작업에 익숙해졌을 것입니다.

하지만 쉘 또는 Go 스크립트와 같은 네이티브 지원되지 않는 런타임으로 작성된 코드가 있다면 어떨까요? 첫 번째 충동은 그 코드를 Python으로 번역하려고 하는 것일 것입니다. 또는 더 좋은 방법으로는 GenAI에게 번역을 요청하는 것일 것입니다. 이 방법은 코드가 간단하고 짧은 함수일 경우에는 잘 작동할 수 있습니다. 또는 회사의 보안 및 기밀 정책이 제3자인 GenAI에게 코드를 전송하는 것을 허용한다면 더욱 좋을 것입니다. 그러나 대부분의 경우, 엄격한 규정과 요구사항을 가진 대기업에서 일하는 경우에는 이를 할 수 없을 것입니다.

![이미지](https://miro.medium.com/v2/resize:fit:480/1*HLkvF3q4nyCWnwjiUiH5qA.gif)

<div class="content-ad"></div>

이 게시물에서는 Docker 및 AWS Lambda 개념에 대한 기본적인 이해만 있으면 함수 실행에 필요한 사용자 정의 런타임을 제공하는 Docker 이미지를 쉽고 간단히 만드는 방법을 설명하겠습니다.

# AWS Lambda의 내부 구조

사용자 지정 런타임을 구성하는 방법에 대해 자세히 알아보기 전에 AWS Lambda가 서버리스 모드에서 함수를 실행하는 마법같은 방법을 이해해 보겠습니다.

![AWS Lambda 내부 구조](/assets/img/2024-06-23-AdvancedAWSLambdaAnObscureFeatureYouMustAbsolutelyUse_0.png)

<div class="content-ad"></div>

AWS Lambda 서버리스 실행은 세 가지 구성 요소 덕분에 가능해집니다:

- Lambda Service: 프로비저닝, 스케일링, 모니터링 및 로깅을 처리합니다. 코드를 배포하고 함수를 호출하는 것을 포함한 함수 실행의 수명주기를 관리합니다.
- Execution Runtime: 코드가 실행되는 환경입니다. AWS Lambda는 Node.js, Python, Java 등 다양한 런타임을 지원하며, 다른 프로그래밍 언어나 특정 런타임 버전을 위한 사용자 정의 런타임 사용도 허용합니다.
- Runtime API: 이 인터페이스는 Lambda 서비스와 실행 런타임 사이에 위치합니다. AWS Lambda 인프라와 함수 실행 환경 간의 통신에 사용됩니다.

![Advanced AWS Lambda](/assets/img/2024-06-23-AdvancedAWSLambdaAnObscureFeatureYouMustAbsolutelyUse_1.png)

주로 런타임과 함수 코드로 구성된 파란색 사각형에 초점을 맞출 것입니다. 우리 Lambda 함수가 작동하도록 수정해야 하는 부분입니다. 일반적으로 지원되는 AWS Lambda 런타임은 런타임 API를 지속적으로 폴링하여 새 이벤트를 수신하고, 함수 핸들러를 로드하고, 이벤트를 수신하면 실행하여 결과를 런타임 API로 다시 보내며, 이를 통해 클라이언트(이 경우 Lambda 서비스)에 결과를 반환합니다.

<div class="content-ad"></div>

AWS는 우리에게 OS전용 런타임이라고 불리는 것을 제공합니다. 이는 언어 지원을 제공하며, 실행 스크립트를 실행하는 데 필요한 환경 변수 및 인증서와 같은 추가 설정을 제공하고, Lambda 서비스와 상호 작용하기 위한 모든 필요한 API를 구성합니다 [1]. 모든 것을 할 일은 작은 스크립트를 추가하고, 코드와 번들로 만든 후, AWS OS 전용 런타임을 기반으로 새로운 도커 이미지를 빌드하고, 이 이미지를 ECR에 업로드한 후, 마지막으로 AWS Lambda의 이미지로 사용하는 것뿐입니다. 쉽고 간단해요!

![이미지](https://miro.medium.com/v2/resize:fit:500/1*Fs-d5kxj5Aa3LL0Jm0MF-Q.gif)

# 사용자 정의 런타임 구현

이 섹션에서는 AWS Lambda 사용자 정의 런타임을 위한 도커 이미지를 빌드하는 데 필요한 스크립트와 코드를 제공하겠습니다. 준비가 되셨나요? 코드 정글로의 여행에 깊이 파고 들어가기 전에 커피를 가져오세요!

<div class="content-ad"></div>


![image](https://miro.medium.com/v2/resize:fit:600/1*-AMtT2H3qw0N26uRiqTVwQ.gif)

```js
#!/bin/sh

set -euo pipefail

# export
# Initialization - load function handler
source $LAMBDA_TASK_ROOT/"$(echo $HANDLER | cut -d. -f1).sh"

# Processing
while true
do
  HEADERS="$(mktemp)"
  # Get an event. The HTTP request will block until one is received
  EVENT_DATA=$(curl -sS -LD "$HEADERS" "http://${AWS_LAMBDA_RUNTIME_API}/2018-06-01/runtime/invocation/next")

  # Extract request ID by scraping response headers received above
  REQUEST_ID=$(grep -Fi Lambda-Runtime-Aws-Request-Id "$HEADERS" | tr -d '[:space:]' | cut -d: -f2)

  # Run the handler function from the script
  RESPONSE=$($(echo "$HANDLER" | cut -d. -f2) "$EVENT_DATA")

  # Send the response
  curl "http://${AWS_LAMBDA_RUNTIME_API}/2018-06-01/runtime/invocation/$REQUEST_ID/response"  -d "$RESPONSE"
done
```

The first line "set -euo pipefail" is to configure the shell to handle errors robustly and to exit immediately if any command within the script fails.

This script starts by loading the function handler through the source command.


<div class="content-ad"></div>

그런 다음, invocation/next API를 호출하여 Runtime API에서 새 이벤트를 조사하기 위한 루프가 시작됩니다.

그런 다음 Lambda 호출을 식별하는 데 도움이 되는 요청 ID를 추출합니다.

그런 다음 Lambda 서비스로부터 수신한 페이로드 데이터로 주요 셸 스크립트를 실행합니다.

마지막으로, invocation/$Request_ID/response를 호출하여 Runtime API로 응답을 보냅니다.

<div class="content-ad"></div>

```js
FROM public.ecr.aws/lambda/provided:al2023

ENV HANDLER="function.handler"

COPY ./bootstrap  ${LAMBDA_TASK_ROOT}/bootstrap
COPY ./function.sh  ${LAMBDA_TASK_ROOT}/function.sh

RUN chmod +x ${LAMBDA_TASK_ROOT}/bootstrap
RUN chmod +x ${LAMBDA_TASK_ROOT}/function.sh

ENTRYPOINT ["/var/task/bootstrap"]
```

이 Dockerfile은 AWS public Lambda 이미지 `lambda/provided:al2023`를 기반으로 하는 Docker 이미지를 생성합니다 [3]. 이 이미지는 언어 지원과 스크립트 실행에 필요한 환경 변수 및 인증서와 같은 추가 설정을 제공합니다.

우리는 환경 변수를 우리 셸 스크립트의 핸들러로 설정했습니다. 이는 `스크립트 이름`.`함수 이름` 형식으로 되어야 합니다.

이후 이전 부트스트랩 스크립트와 우리의 셸 스크립트를 $LAMBDA_TASK_ROOT로 복사합니다 (Lambda 런타임 환경 변수로 정의된, 함수 코드 디렉토리 `/var/task`를 참조하는 Lambda 환경 변수) [4]


<div class="content-ad"></div>

우리는 스크립트를 실행할 수 있도록 만들고, 마지막으로 실행 시점을 기다리는 새 이벤트를 기다리는 부트스트랩 스크립트로 설정합니다.

```js
function handler () {
  EVENT_DATA=$1
  echo "$EVENT_DATA" 1>&2;
  RESPONSE="Echoing request: '$EVENT_DATA'"

  echo $RESPONSE
}
```

이것은 이벤트 데이터를 인수로 받아 출력하는 샘플 셸 스크립트입니다.

```js
runtime-tutorial
├ bootstrap
├ Dockerfile
└ function.sh
```

<div class="content-ad"></div>

# 직접 테스트해보세요

지금까지 따라오셨다면, 축하드립니다. 여러분은 이제 AWS 람다 함수에 대한 가장 복잡한 컨셉 중 하나를 이해하셨습니다. 우리 모두가 알다시피, 지식을 확고히 하는 가장 좋은 방법은 실습하는 것이죠!

![image](https://miro.medium.com/v2/resize:fit:480/1*3JqMenMdO5NIFvKGb0Tylg.gif)

그래서 저는 이전 스크립트를 포함하고 사용자 정의 런타임 람다를 생성하고 자체적으로 그 인보케이션을 테스트할 필요한 AWS 리소스를 생성하는 Terraform 모듈을 만들었습니다.

<div class="content-ad"></div>

이제 Lambda 함수와 해당 사용자 지정 런타임을 배포하고 탐색하려면 작성하고 실행해야 하는 Terraform 스크립트입니다:

```js
data "aws_caller_identity" "current" {}

data "aws_region" "current" {}

locals {
  current_account_number = data.aws_caller_identity.current.account_id
  region = data.aws_region.current.name
}

module "custom-lambda" {
  source = "github.com/Ilyassxx99/lambda-custom-runtime.git"
  custom_bash_function_name = "custom-bash-function"
  custom_bash_ecr_repository_name = "custom-lambda"
  current_account_number = local.current_account_number
  docker_image_custom_bash_uri = "custom-lambda:latest"
  lambda_image_custom_bash_version    = "3.12.2"
  lambda_image_arch              = "amd64"
  region = local.region
  tags = {
    Terraform-module = "custom-bash-runtime"
  }
}
```

# 결론

제 블로그 시리즈 '고급 AWS Lambda'의 첫 번째 글에서는 Lambda 런타임을 사용자 지정하여 작성한 스크립트에 사용한 어떤 프로그래밍 언어든 지원할 수 있는 방법을 살펴보았습니다. 이것은 Go나 Rust와 같은 AOT 컴파일된 언어로 작업할 때 스크립트의 실행 성능이 중요할 때 매우 유용합니다.

<div class="content-ad"></div>

이 블로그가 유용했기를 바라요. 피드백이나 이 블로그 주제에 대한 질문 또는 앞으로 논의하고 싶은 다른 기술 주제에 대해 궁금한 점이 있으면 제 LinkedIn(https://www.linkedin.com/in/ifezouaniilyass/)에서 연락해 주세요.

소중한 시간 내어 주셔서 감사합니다. 다음 블로그에서 만나요. 건배!

![Image](https://miro.medium.com/v2/resize:fit:720/1*WIjcsTL49wdYazQX2VS95Q.gif)

## 노트:

<div class="content-ad"></div>

- AWS는 Docker 이미지 대신 직접 zip을 가져올 수 있는 OS-only provided.al2023 런타임을 지원합니다. 이 문서에서는 코드에 다른 종속성을 포함해야 하는 경우를 다루기 위해 Docker 이미지를 사용하기로 선택했습니다. Docker 이미지 내에서 패키지/모듈을 직접 더 쉽게 설치하고 패키징할 수 있습니다.
- Go 또는 Rust와 같이 AOT 컴파일된 언어의 경우, AWS는 런타임을 구현하는 라이브러리를 제공하며 해당 기능을 사용하여 메인 프로세스를 랩핑할 수 있습니다. 이 문서에서는 셸 스크립트를 배포하려고 한다고 가정할 때, 런타임 API 호출과 프로세스 실행을 수행하는 스크립트를 작성해야 합니다.

## 참고:

[1] https://docs.aws.amazon.com/lambda/latest/dg/runtimes-provided.html

[2] https://docs.aws.amazon.com/lambda/latest/dg/runtimes-api.html

<div class="content-ad"></div>

[3] [Amazon Linux 2023 Runtime for AWS Lambda 소개](https://aws.amazon.com/fr/blogs/compute/introducing-the-amazon-linux-2023-runtime-for-aws-lambda/)

[4] [AWS Lambda 환경 변수 구성](https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars.html#configuration-envvars-runtime)

[5] [Lambda 런타임 API](https://docs.aws.amazon.com/lambda/latest/dg/runtimes-api.html)