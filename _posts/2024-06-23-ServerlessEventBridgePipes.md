---
title: "서버리스 EventBridge Pipes로 이벤트 처리하는 방법 최신 2024"
description: ""
coverImage: "/assets/img/2024-06-23-ServerlessEventBridgePipes_0.png"
date: 2024-06-23 22:26
ogImage:
  url: /assets/img/2024-06-23-ServerlessEventBridgePipes_0.png
tag: Tech
originalTitle: "Serverless EventBridge Pipes"
link: "https://medium.com/@leejamesgilmore/serverless-eventbridge-pipes-309bdf209ecd"
---

<img src="/assets/img/2024-06-23-ServerlessEventBridgePipes_0.png" />

## 안내

✔️ Amazon EventBridge Pipes 및 그들이 무엇인지에 대해 논의합니다.
✔️ Python 및 AWS CDK를 사용한 예제를 통해 안내합니다.

# 소개 👋🏽

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

이 기사에서는 AWS CDK와 Python을 사용하여 구축된 허구의 치과 앱을 만들어가면서 Amazon EventBridge Pipes에 대해 다뤄볼 것입니다.

![이미지](/assets/img/2024-06-23-ServerlessEventBridgePipes_1.png)

'LJ 치과 의사' 앱은 사람들이 가장 가까운 치과 의사와 예약을 잡을 수 있는 기능을 제공합니다.

![이미지](/assets/img/2024-06-23-ServerlessEventBridgePipes_2.png)

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

그들이 이미 방문한 적이 있는 경우에는 이메일, SMS 또는 통신 없음을 보내기 전에 선호하는 연락 방법을 확인합니다. 이 모두는 Amazon EventBridge Pipes를 사용하여 처리됩니다.

![이미지](/assets/img/2024-06-23-ServerlessEventBridgePipes_3.png)

이 기사의 전체 코드 예제는 여기에서 확인할 수 있습니다:

# 우리가 무엇을 만들고 있나요? 🛠️

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

환자들이 온라인으로 진료 예약을 할 수 있도록 다음 아키텍처를 사용할 예정입니다:

![아키텍처](/assets/img/2024-06-23-ServerlessEventBridgePipes_4.png)

환자들이 다음을 사용하여 진료를 예약할 수 있습니다:

- Amazon API Gateway REST API를 사용하여 온라인으로 진료 예약을 합니다.
- Lambda 함수가 진료 상세 내용을 DynamoDB 테이블에 기록합니다.
- 우리는 DynamoDB 스트림을 사용하여 테이블의 변경 사항을 감지하고 레코드가 생성되었을 경우에만 필터링하여 Amazon EventBridge Pipes를 통해 해당 스트림을 소스로 사용합니다.
- 일치하는 레코드가 연락처 선호도 테이블에 있는지 확인하는 Lambda 함수를 호출하여 스트림 레코드에 선호도를 추가합니다.
- 마지막으로, Amazon EventBridge Pipes의 대상은 SQS로 설정되어 진료를 처리하기 위해 준비된 예약을 저장합니다.

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

👇 더 진전하기 전에 — 향후 블로그 게시물과 서버리스 뉴스를 만나보기 위해 LinkedIn에 연락해 주세요 https://www.linkedin.com/in/lee-james-gilmore/

![ServerlessEventBridgePipes_5](/assets/img/2024-06-23-ServerlessEventBridgePipes_5.png)

# Amazon EventBridge Pipes란 무엇인가요?

Amazon EventBridge Pipes는 옵션 변환, 필터 및 풍부한 단계를 통해 이벤트 생성자와 소비자 간의 포인트 투 포인트 통합을 생성하는 데 도움을 줍니다. EventBridge Pipes를 사용하면 이벤트 중심 애플리케이션을 구축할 때 필요한 통합 코드 양을 줄이고 유지할 수 있습니다. 아래 다이어그램은 우리 예시를 위해 이를 보여줍니다:

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

위 다이어그램에서 알 수 있듯이 다음이 있습니다:

- 소스로 DynamoDB 스트림을 사용합니다.
- 새로 삽입된 레코드만 받도록 추가 필터링을 수행합니다(예: 삭제 또는 업데이트 제외).
- 별도의 데이터베이스에서 연락처 정보를 읽는 enrichment Lambda 함수를 통해 스트림에서 약속 데이터를 추가 정보로 향상시킵니다.
- 최종적으로 약속 레코드를 더 처리하기 위해 SQS 대기열로 설정된 대상이 있습니다.

참고로 아래에 표시된 다양한 서비스와 구성을 사용할 수 있습니다:

![이미지](/assets/img/2024-06-23-ServerlessEventBridgePipes_6.png)

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

# 주요 코드 설명하기 👨‍💻

이제 몇 가지 주요 코드에 대해 이야기해봅시다.

아래에서 볼 수 있는대로 CDK 애플리케이션을 상태(Stateful) 및 상태 없음(Stateless) 리소스(스택)로 분리하기 시작합니다:

```js
#!/usr/bin/env python3
import os

import aws_cdk as cdk
from stateful.stateful import DentistsStatefulStack
from stateless.stateless import DentistsStatelessStack

app = cdk.App()

# 앱을 상태(Stateful) 및 상태 없음(Stateless) 리소스로 분리합니다
DentistsStatefulStack(app, "DentistsStatefulStack")
DentistsStatelessStack(app, "DentistsStatelessStack")

app.synth()
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

Stateful 스택을 먼저 살펴 보면, 두 개의 DynamoDB 테이블을 설정했음을 알 수 있습니다. 하나는 약속을, 다른 하나는 선호하는 연락처 세부 정보를 저장하는 테이블입니다:

```js
from aws_cdk import CfnOutput, RemovalPolicy, Stack
from aws_cdk import aws_dynamodb as dynamodb
from constructs import Construct

class DentistsStatefulStack(Stack):

    def __init__(self, scope: Construct, construct_id: str, **kwargs) -> None:
        super().__init__(scope, construct_id, **kwargs)

        # 약속을 저장하는 DynamoDB 테이블 추가 및 스트림 활성화
        table = dynamodb.Table(
            self, 'DentistTable',
            billing_mode=dynamodb.BillingMode.PAY_PER_REQUEST,
            table_name='DentistTable',
            stream=dynamodb.StreamViewType.NEW_IMAGE,
            removal_policy=RemovalPolicy.DESTROY,
            partition_key=dynamodb.Attribute(
                name='id',
                type=dynamodb.AttributeType.STRING
            )
        )

        # 이메일 주소를 기반으로 하는 연락 선호도 DynamoDB 테이블 추가
        contact_table = dynamodb.Table(
            self, 'DentistContactsTable',
            billing_mode=dynamodb.BillingMode.PAY_PER_REQUEST,
            table_name='DentistContactsTable',
            removal_policy=RemovalPolicy.DESTROY,
            partition_key=dynamodb.Attribute(
                name='id',
                type=dynamodb.AttributeType.STRING
            )
        )

        # 테이블 이름을 위한 스택 출력 추가
        CfnOutput(
            self, 'DentistDynamoDBTableName',
            value=table.table_name,
            description='DynamoDB 테이블 이름',
            export_name='DentistDynamoDBTableName'
        )

        # 연락처 테이블 이름을 위한 스택 출력 추가
        CfnOutput(
            self, 'DentistContactDynamoDBTableName',
            value=contact_table.table_name,
            description='연락 선호도 DynamoDB 테이블 이름',
            export_name='DentistContactDynamoDBTableName'
        )

        # 약속 테이블의 테이블 스트림 ARN을 위한 스택 출력 추가
        CfnOutput(
            self, 'DentistDynamoDBTableStreamArn',
            value=table.table_stream_arn,
            description='DynamoDB 테이블 스트림 ARN',
            export_name='DentistDynamoDBTableStreamArn'
        )
```

이제 Stateful 리소스가 설정됐으므로, Stateless 스택에서 먼저 약속을 생성하는 람다 함수를 생성합니다:

```js
# 약속을 생성하는 람다 함수 생성
create_appointment_lambda = aws_lambda.Function(
    self, 'CreateAppointment',
    runtime=aws_lambda.Runtime.PYTHON_3_12,
    handler='create_appointment.handler',
    code=aws_lambda.Code.from_asset(os.path.join(DIRNAME, 'src')),
    function_name='CreateAppointment',
    environment={
        'dynamodb_table': dynamodb_table_name,
    },
)
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

Lambda 함수에 추가적으로 두 번째 Lambda 함수를 추가합니다. 이 함수는 DynamoDB 스트림이 제공하는 데이터를 풍부하게 하는 데 사용될 것입니다:

```js
# 연락처 세부 정보를 검색하는 람다 함수 생성
get_contact_details = aws_lambda.Function(
    self, 'GetContactDetails',
    runtime=aws_lambda.Runtime.PYTHON_3_12,
    handler='get_contact_details.handler',
    code=aws_lambda.Code.from_asset(os.path.join(DIRNAME, 'src')),
    function_name='GetContactDetails',
    environment={
        'contacts_dynamodb_table': contacts_dynamodb_table_name,
    },
)
```

다음으로, 우리의 파이프 대상이 될 Amazon SQS 대기열을 추가합니다:

```js
# AppointmentsQueue로 메시지를 보낼 SQS 대기열 생성
sqs_queue = sqs.Queue(
    self, 'AppointmentsQueue',
    queue_name='AppointmentsQueue',
    removal_policy=RemovalPolicy.DESTROY
)
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

그럼 Amazon EventBridge 파이프 자체에 대한 IAM 정책을 만들어서 원본, 보강 및 대상 자원과 연결할 수 있도록합니다.

```js
# 파이프 원본(다이나모DB 스트림)용 파이프 정책과 역할 생성
pipe_source_policy = iam.PolicyStatement(
    actions=[
        'dynamodb:DescribeStream',
        'dynamodb:GetRecords',
        'dynamodb:GetShardIterator',
        'dynamodb:ListStreams'
    ],
    resources=[stream_arn],
    effect=iam.Effect.ALLOW
)

# 파이프 대상(SQS)으로 메시지를 게시하는 것을 허용하는 대상 정책 작성
pipe_target_policy = iam.PolicyStatement(
    actions=['sqs:SendMessage'],
    resources=[sqs_queue.queue_arn],
    effect=iam.Effect.ALLOW
)

# 파이프가 람다 기능을 호출할 수 있도록 하는 보강 정책 생성
pipe_enrichment_policy = iam.PolicyStatement(
    actions=['lambda:InvokeFunction'],
    resources=[get_contact_details.function_arn],
    effect=iam.Effect.ALLOW
)

# 파이프 역할 생성
pipe_role = iam.Role(self, 'PipeRole',
    assumed_by=iam.ServicePrincipal('pipes.amazonaws.com'),
)

# 역할에 세 가지 정책 추가
pipe_role.add_to_policy(pipe_source_policy)
pipe_role.add_to_policy(pipe_target_policy)
pipe_role.add_to_policy(pipe_enrichment_policy)
```

이제 다음과 같은 코드를 사용하여 파이프를 생성할 수 있습니다:

```js
# 다이나모DB의 새 항목에 대한 필터가 있는 이벤트브릿지 파이프 생성
pipe = pipes.CfnPipe(self, 'Pipe',
    role_arn=pipe_role.role_arn,
    source=stream_arn,
    log_configuration=pipes.CfnPipe.PipeLogConfigurationProperty(
        cloudwatch_logs_log_destination=pipes.CfnPipe.CloudwatchLogsLogDestinationProperty(
            log_group_arn=log_group.log_group_arn
        ),
        level='INFO',
    ),
    name='DentistPipe',
    source_parameters=pipes.CfnPipe.PipeSourceParametersProperty(
        dynamo_db_stream_parameters=pipes.CfnPipe.PipeSourceDynamoDBStreamParametersProperty(
            starting_position='LATEST',
        ),
        filter_criteria=pipes.CfnPipe.FilterCriteriaProperty(
            filters=[pipes.CfnPipe.FilterProperty(
                pattern=json.dumps({'eventName': [{ 'prefix': 'INSERT' }])
            )]
        ),
    ),
    enrichment=get_contact_details.function_arn,
    target=sqs_queue.queue_arn,
)
pipe.apply_removal_policy(RemovalPolicy.DESTROY)
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

현재 인프라가 모두 설정되어 있지만 'create_appointment.py' 파일을 시작으로 Lambda 함수 코드를 간단히 살펴보겠습니다.

```js
import json
import os
import uuid
from http import HTTPStatus

import boto3
from boto3.dynamodb.types import TypeSerializer

dynamodb_table = os.getenv('dynamodb_table')
dynamodb_client = boto3.client('dynamodb')
serializer = TypeSerializer()

def handler(event, context):
    try:
        # 이벤트에서 요청 데이터를 구문 분석하고 본문을 가져옵니다.
        request_data = json.loads(event['body'])

        # uuidv4를 사용하여 새로운 약속 ID를 추가합니다.
        request_data['id'] = str(uuid.uuid4())

        # 페이로드를 dynamodb 형식으로 직렬화합니다.
        appointment_data = {k: serializer.serialize(v) for k, v in request_data.items()}

        # 항목을 dynamodb 테이블에 추가합니다.
        dynamodb_client.put_item(TableName=dynamodb_table, Item=appointment_data)

        body = {
            'message': request_data,
            'statusCode': HTTPStatus.CREATED,
        }

        # 응답을 올바른 형식으로 API Gateway로 전송합니다.
        response = {
            'statusCode': HTTPStatus.CREATED,
            'body': json.dumps(body, indent=2),
            'headers': {
                'content-type': 'application/json',
            },
        }

    except Exception as e:
        response = {
            'statusCode': HTTPStatus.INTERNAL_SERVER_ERROR.value,
            'body': f'Exception={e}',
        }

    return response
```

위 코드에서 볼 수 있듯, API Gateway 이벤트에서 약속을 가져와 새로운 고유 ID(uuid)를 추가하고 레코드를 약속 테이블에 기록하는 기본 함수를 생성합니다.

테이블에 레코드가 추가되면 스트림이 호출되어 모든 레코드 변경에 대해 파이프가 변경 사항을 소스로 가져올 것입니다.

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

그 다음으로, 파이프의 일부로 호출되는 Enrichment 람다 함수를 살펴보겠습니다:

위 코드에서 볼 수 있듯이, 스트림 레코드에서 email_address 속성을 가져와 우리가 선호하는 연락처 DynamoDB 테이블에서 조회합니다. 레코드가 존재하면, 선호하는 방법을 가져와 약속 레코드에 추가합니다. 일치하는 레코드가 없으면, preferredMethod를 'none'으로 추가합니다.

엔드 투 엔드로 테스트하려면, 애플리케이션을 배포하고 Contacts DynamoDb 테이블에 다음 항목을 추가하면 preferredMethod를 추가할 수 있습니다:

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

```json
{
  "id": "john.doe@example.com",
  "preferredMethod": "email"
}
```

만약 레코드가 존재하지 않는다면 이전에 언급한 대로 'none'으로 기본 설정됩니다.

# 결론

Amazon EventBridge 파이프를 사용하는 실제 예시로 서버리스 솔루션에서의 사용법을 보여드려 유용하게 찾으셨기를 희망합니다. 파이프를 사용하면 이전에 사용해야 했던 람다 접착 코드를 제거할 수 있고 소스에서 데이터를 추출하여 해당 데이터를 타깃으로 전달하는 훌륭한 방법을 제공합니다.

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

# 마무리 👋🏽

이 글을 즐겁게 보았으면 공유하고 피드백을 남겨주세요!

비슷한 콘텐츠를 더 보시려면 제 YouTube 채널을 구독해주세요!

<img src="/assets/img/2024-06-23-ServerlessEventBridgePipes_7.png" />

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

당신과 어느 하나라도 다음에서도 연결하고 싶어요:

- [LinkedIn](https://www.linkedin.com/in/lee-james-gilmore/)
- [Twitter](https://twitter.com/LeeJamesGilmore)

포스트를 즐겼다면 추가로 포스트/시리즈를 보기 위해 내 프로필 Lee James Gilmore를 팔로우해주세요. 그리고 인사를 건네고 싶다면 절대 잊지 말고 연락해주세요 👋

좋았다면 포스트 하단에 있는 '박수' 기능을 사용해주세요! (한 번 이상 박수를 칠 수 있어요!!)

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

# 제 소개

안녕하세요, 저는 영국에 거주하고 있는 AWS 커뮤니티 빌더이자 블로거인 Lee입니다. AWS 인증 클라우드 아키텍트이며 City Electrical Factors (UK) 및 City Electric Supply (US)에서 글로벌 기술 및 아키텍처 책임자로 일하고 있습니다. 지난 6년간 주로 AWS에서 풀스택 JavaScript를 사용해 왔습니다.

저는 서버리스를 지지하는 사람으로, AWS, 혁신, 소프트웨어 아키텍처, 기술에 대한 애정이 있습니다.

**_ 제공된 정보는 제 개인적인 견해이며 해당 정보 사용에 대한 책임을 지지 않습니다. _**

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

아래 내용도 참고하시면 좋을 것 같아요:
