---
title: "AWS Step Function을 사용한 직원 온보딩 프로세스 구축 방법"
description: ""
coverImage: "/assets/img/2024-06-30-BuildinganAWSStepFunctionforEmployeeOnboardingProcess_0.png"
date: 2024-06-30 19:26
ogImage: 
  url: /assets/img/2024-06-30-BuildinganAWSStepFunctionforEmployeeOnboardingProcess_0.png
tag: Tech
originalTitle: "Building an AWS Step Function for Employee Onboarding Process"
link: "https://medium.com/@meghnakha18/building-an-aws-step-function-for-employee-onboarding-process-b9cef7875d9b"
---


이번 튜토리얼에서는 직원 입사 절차를 자동화하기 위해 AWS Step Function을 만들어보겠습니다. 이 과정에는 직원 레코드 작성, 문서 확인, 환영 이메일 발송, 필요 장비 할당 등이 포함됩니다.

# 단계 1: 서버리스 프로젝트 설정하기

먼저, Serverless Framework가 설치되어 있는지 확인하세요. 만약 없다면 npm을 사용하여 설치할 수 있습니다:

```js
npm install -g serverless
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

다음으로 새로운 Serverless 프로젝트를 만들어보세요:

```js
serverless create --template aws-nodejs --path employee-onboarding
cd employee-onboarding
```

# 단계 2: Serverless Step Functions 플러그인 설치하기

프로젝트에 Serverless Step Functions 플러그인을 추가해보세요. 이 플러그인은 Serverless Framework를 사용하여 Step Functions을 정의하고 배포하는 데 필요합니다.

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
npm install --save serverless-step-functions
```

# 단계 3: Serverless Framework 구성

serverless.yml 파일에서 서비스를 Step Functions 플러그인을 사용하도록 구성하고 AWS 리소스와 함수를 정의합니다.
```js
service: employee-onboarding
frameworkVersion: '3'
provider:
  name: aws
  runtime: nodejs18.x
  stage: ${opt:stage, 'dev'}
  region: us-east-1
  iamRoleStatements:
    - Effect: "Allow"
      Action:
        - "dynamodb:*"
      Resource: !GetAtt EmployeeTable.Arn
    - Effect: "Allow"
      Action:
        - "sns:*"
      Resource: "arn:aws:sns:us-east-1:*:NotifyEmployee"
functions:
  createEmployeeRecord:
    handler: handler.createEmployeeRecord
    environment:
      EMPLOYEE_TABLE: !Ref EmployeeTable
  verifyDocuments:
    handler: handler.verifyDocuments
  sendWelcomeEmail:
    handler: handler.sendWelcomeEmail
  assignEquipment:
    handler: handler.assignEquipment
plugins:
  - serverless-step-functions
stepFunctions:
  stateMachines:
    EmployeeOnboardingStateMachine:
      name: EmployeeOnboardingStateMachine
      definition:
        Comment: "A state machine for onboarding new employees."
        StartAt: CreateEmployeeRecord
        States:
          CreateEmployeeRecord:
            Type: Task
            Resource: !GetAtt createEmployeeRecord.Arn
            ResultPath: "$.employee"
            Next: VerifyDocuments
          VerifyDocuments:
            Type: Task
            Resource: !GetAtt verifyDocuments.Arn
            ResultPath: "$.documentVerification"
            Next: DocumentCheck
          DocumentCheck:
            Type: Choice
            Choices:
              - Variable: "$.documentVerification.status"
                StringEquals: "verified"
                Next: SendWelcomeEmail
            Default: DocumentError
          DocumentError:
            Type: Pass
            Result: "Document verification failed"
            End: true
          SendWelcomeEmail:
            Type: Task
            Resource: !GetAtt sendWelcomeEmail.Arn
            ResultPath: "$.emailStatus"
            Next: AssignEquipment
          AssignEquipment:
            Type: Task
            Resource: !GetAtt assignEquipment.Arn
            ResultPath: "$.equipmentStatus"
            End: true
resources:
  Resources:
    EmployeeTable:
      Type: AWS::DynamoDB::Table
      Properties:
        TableName: EmployeeTable-${self:provider.stage}
        AttributeDefinitions:
          - AttributeName: employeeId
            AttributeType: S
        KeySchema:
          - AttributeName: employeeId
            KeyType: HASH
        BillingMode: PAY_PER_REQUEST
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

# 상태 머신의 자세한 설명

## 1. CreateEmployeeRecord

- 유형: 작업
- createEmployeeRecord 람다 함수를 호출하여 작업 수행
- 리소스: !GetAtt createEmployeeRecord.Arn
- createEmployeeRecord 람다 함수의 ARN
- ResultPath: $.employee
- 이 작업의 출력을 상태 입력의 employee 필드에 저장

- 다음: VerifyDocuments
- 이 작업이 완료된 후 VerifyDocuments 상태로 전환

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

## 2. VerifyDocuments

- verifyDocuments Lambda 기능을 호출하여 작업을 수행합니다.

## 3. DocumentCheck

- 유형: Choice
- 변수 값에 따라 분기 로직을 추가합니다.
- 선택지:
  - documentVerification.status 필드를 평가합니다.
  - 값이 확인된 경우 SendWelcomeEmail 상태로 전환합니다.
  - 기본값: DocumentError
  - 선택지와 일치하는 것이 없는 경우 DocumentError 상태로 전환합니다.

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

## 4. DocumentError

- 유형: Pass
- 입력을 수정하지 않고 출력으로 전달합니다.
- 결과: "문서 검증 실패"
- 이 상태의 출력 값입니다.
- 종료: true
- 이 상태를 최종 상태로 표시합니다.

## 5. SendWelcomeEmail

- sendWelcomeEmail Lambda 함수를 호출하여 작업을 수행합니다.

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

## 6. 장비할당

- assignEquipment 람다 함수를 호출하여 작업을 수행합니다.

# 단계 4: 람다 함수 구현

handler.js 파일을 만들어 람다 함수를 정의하세요.

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

직원 레코드 생성

```js
'use strict';
const AWS = require('aws-sdk');
const docClient = new AWS.DynamoDB.DocumentClient();
module.exports.createEmployeeRecord = async (event) => {
  const EMPLOYEE_TABLE = process.env.EMPLOYEE_TABLE;
  const employee = JSON.parse(event.body);
  const params = {
    TableName: EMPLOYEE_TABLE,
    Item: {
      employeeId: employee.employeeId,
      name: employee.name,
      role: employee.role,
      email: employee.email,
    },
  };
  await docClient.put(params).promise();
  return {
    statusCode: 200,
    body: JSON.stringify(employee),
  };
};
```

문서 확인

```js
module.exports.verifyDocuments = async (event) => {
  const { employeeId, documents } = JSON.parse(event.body);
  const status = documents && documents.length > 0 ? 'verified' : 'unverified';
  return {
    statusCode: 200,
    body: JSON.stringify({ employeeId, status }),
  };
};
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

↓

웰컴 이메일 전송

```js
const sns = new AWS.SNS();
module.exports.sendWelcomeEmail = async (event) => {
  const { email } = JSON.parse(event.body).employee;
  const params = {
    Message: '회사에 오신 것을 환영합니다!',
    Subject: '환영합니다',
    TopicArn: process.env.SNS_TOPIC_ARN,
  };
  await sns.publish(params).promise();
  return {
    statusCode: 200,
    body: JSON.stringify({ status: '이메일 전송됨' }),
  };
};
```

장비 할당

```js
module.exports.assignEquipment = async (event) => {
  const { employeeId } = JSON.parse(event.body);
  // 장비 로직 여기에 작성
  return {
    statusCode: 200,
    body: JSON.stringify({ status: '장비 할당됨', employeeId }),
  };
};
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

# 단계 4: 서버리스 서비스 배포하기

서버리스.yml 파일 구성을 완료한 후 서버리스 서비스를 배포하세요. 이 단계에서는 람다 함수를 배포하고, DynamoDB 테이블과 같은 필요한 AWS 리소스를 생성하며, Step Functions 스테이트 머신을 배포합니다.

```js
serverless deploy
```

배포 중에는 서버리스 프레임워크가 제공된 설정에 따라 Step Functions 스테이트 머신을 만듭니다.

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

# 단계 5: 상태 머신의 ARN 가져오기

배포된 후에는 AWS 관리 콘솔 또는 CLI에서 Step Functions 상태 머신의 ARN (Amazon 리소스 이름)을 얻을 수 있습니다.

# 단계 6: IAM 역할 문장 업데이트

serverless.yml 파일의 IAM 역할 문장을 Step Functions 상태 머신의 ARN으로 업데이트하세요.

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

```yaml
iamRoleStatements:
  - Effect: "Allow"
    Action:
      - "states:*"
    Resource: "arn:aws:states:us-east-1:123456789012:stateMachine:EmployeeOnboardingStateMachine"
```

# 단계 7: 서버리스 서비스 다시 배포하기

IAM 역할 문장을 업데이트한 후에 변경 사항을 적용하기 위해 서버리스 서비스를 다시 배포하세요.

```sh
serverless deploy
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

# 요약

이 튜토리얼은 직원 온보딩 프로세스를 자동화하기 위한 AWS Step Function을 생성하는 방법을 보여줍니다. Serverless Framework와 Serverless Step Functions 플러그인을 활용하여 작업, 분기 논리, 오류 처리를 포함한 복잡한 워크플로우를 정의하고 배포하여 온보딩 프로세스를 효율적이고 확장 가능하게 만들 수 있습니다.