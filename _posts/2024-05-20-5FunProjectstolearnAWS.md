---
title: "5가지 재미있는 AWS 프로젝트를 배우는 법"
description: ""
coverImage: "/assets/img/2024-05-20-5FunProjectstolearnAWS_0.png"
date: 2024-05-20 16:45
ogImage:
  url: /assets/img/2024-05-20-5FunProjectstolearnAWS_0.png
tag: Tech
originalTitle: "5 Fun Projects to learn AWS"
link: "https://medium.com/@bubu.tripathy/5-fun-projects-to-learn-aws-e5884400dc89"
---

![Image](/assets/img/2024-05-20-5FunProjectstolearnAWS_0.png)

# 소개

Amazon Web Services (AWS)를 배우는 여정에 돌입하면 흥미롭고 보람찬 시간을 보낼 수 있습니다. AWS를 배우면 클라우드 컴퓨팅 기술을 향상시키고 다양한 가능성을 경험할 수 있습니다. 학습 경험을 더욱 흥미롭게 만들기 위해 AWS의 다양성을 보여주는 5가지 재미있는 프로젝트를 소개합니다. 이를 통해 실제 문제에 대한 해결책을 배포하는 경험을 쌓을 수 있습니다.

# 프로젝트 #1 — Amazon S3에 정적 웹사이트 시작하기

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

위의 텍스트를 친절한 톤으로 한국어로 번역해 드리겠습니다.

저렴하고 효율적인 방법으로 웹 사이트를 호스팅하는 방법을 발견하세요. Amazon S3에 정적 사이트를 배포하여 핵심 AWS 서비스를 소개하는 이상적인 기회입니다. S3 버킷을 만들어 단순성과 확장성을 활용하고, CDN으로 Amazon CloudFront를 사용하여 전 세계적으로 접근성을 향상시킵니다. Amazon Route 53으로 도메인을 원활하게 관리하고, 안전한 HTTPS 연결을 위해 AWS Certificate Manager를 통해 SSL/TLS 보안을 구현하여 웹 사이트의 최적 성능과 안전성을 보장하세요.

## S3 버킷 만들기

- AWS 관리 콘솔로 이동합니다.
- S3로 이동하여 "버킷 생성"을 클릭합니다.
- 고유한 버킷 이름을 입력하고 지역을 선택한 후 "다음"을 클릭합니다.
- "옵션 구성" 페이지에서 "다음"을 클릭합니다.
- "권한 설정" 페이지에서 필요 시 버킷 정책을 구성할 수 있습니다. 검토를 위해 "다음"을 클릭한 후 "버킷 만들기"를 클릭합니다.

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
aws s3api create-bucket --bucket YOUR_UNIQUE_BUCKET_NAME --region YOUR_REGION
```

## S3 버킷에 웹사이트 업로드하기

- 새로 생성한 S3 버킷을 엽니다.
- "업로드" 버튼을 클릭하고 웹사이트 파일을 모두 선택합니다.
- 파일이 공개적으로 접근 가능하도록 설정되었는지 확인합니다. 각 파일을 선택하고 "작업"을 클릭한 후 "공개"를 선택하여 설정할 수 있습니다.

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
aws s3 sync YOUR_LOCAL_WEBSITE_DIRECTORY s3://YOUR_UNIQUE_BUCKET_NAME --acl public-read
```

## S3 버킷에서 정적 웹 사이트 호스팅 활성화하기

- S3 버킷으로 이동한 후 “속성” 탭을 클릭합니다.
- “정적 웹 사이트 호스팅”을 클릭합니다.
- “이 버킷을 웹 사이트 호스팅에 사용”을 선택합니다.
- “인덱스 문서”를 원하는 HTML 파일로 설정합니다 (예: index.html) 그리고 옵션으로 "오류 문서"를 설정할 수 있습니다.
- 변경 사항을 저장합니다.

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
aws s3 웹사이트 s3://당신의_고유한_버킷_이름 --index-document index.html
```

## Amazon CloudFront 배포 구성

- CloudFront 콘솔로 이동합니다.
- "배포 생성"을 클릭합니다.
- "웹" 배포를 선택합니다.
- 다음 구성을 설정합니다:

- 원본 도메인 이름: 드롭다운에서 S3 버킷을 선택합니다.
- 기본 루트 오브젝트: 주 HTML 파일로 설정합니다 (예: index.html).
- 기타 설정은 기본 설정을 유지하거나 요구에 맞게 조정합니다.

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

5. "배포 생성"을 클릭하세요.

또는

```js
import boto3

cloudfront_client = boto3.client('cloudfront')

distribution_config = {
    'CallerReference': '고유한 호출 참조 번호',
    'Origins': {
        'Quantity': 1,
        'Items': [
            {
                'Id': 'S3-origin',
                'DomainName': '당신의_고유한_버킷_이름.s3.amazonaws.com',
                'S3OriginConfig': {
                    'OriginAccessIdentity': ''
                }
            }
        ]
    },
    'DefaultCacheBehavior': {
        'TargetOriginId': 'S3-origin',
        'ForwardedValues': {
            'QueryString': False,
            'Cookies': {'Forward': 'none'},
            'Headers': {'Quantity': 0}
        },
        'TrustedSigners': {'Enabled': False, 'Quantity': 0},
        'ViewerProtocolPolicy': 'allow-all',
        'MinTTL': 0
    },
    'Comment': '당신의 CloudFront 배포 코멘트',
    'Enabled': True
}

response = cloudfront_client.create_distribution(DistributionConfig=distribution_config)
distribution_id = response['Distribution']['Id']

print(f"CloudFront Distribution ID: {distribution_id}")
```

## Amazon Route 53을 업데이트하세요.

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

- Route 53 콘솔로 이동하세요.
- 아직 호스팅된 존이 없다면 도메인을 위한 새로운 호스팅된 존을 만드세요.
- 호스팅된 존을 위해 제공된 네 개의 네임서버를 확인하세요.
- 도메인 등록기의 설정에서 Route 53에서 제공한 네임서버로 업데이트하세요.

## AWS Certificate Manager (ACM)를 사용하여 SSL/TLS 인증서 가져오기

- ACM 콘솔로 이동하세요.
- "인증서 요청"을 클릭하세요.
- 도메인 이름을 입력하고 소유권을 확인하기 위한 지침을 따르세요.
- 확인이 완료되면 새로운 CloudFront 배포를 만들 옵션을 선택하세요.
- 이전에 만든 CloudFront 배포를 선택하고 인증서 요청을 완료하세요.

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
import boto3

acm_client = boto3.client('acm')

certificate_arn = acm_client.request_certificate(
    DomainName='yourdomain.com',
    ValidationMethod='DNS'
)['CertificateArn']

print(f"Certificate ARN: {certificate_arn}")
```

## SSL/TLS 사용하도록 CloudFront 배포 업데이트

- CloudFront 콘솔로 이동합니다.
- 배포를 선택하고 "수정"을 클릭합니다.
- "일반" 탭에서 "뷰어 프로토콜 정책"을 "HTTP를 HTTPS로 리디렉션"으로 변경합니다.
- 변경 사항을 저장합니다.

OR

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
cloudfront_client = boto3.client("cloudfront");

cloudfront_client.update_distribution(
  (DistributionConfig = {
    DistributionConfig: {
      DefaultCacheBehavior: {
        ViewerProtocolPolicy: "redirect-to-https",
      },
    },
    Id: "YOUR_CLOUDFRONT_DISTRIBUTION_ID",
    IfMatch: "your-distribution-config-if-match",
  })
);
```

## 변경 사항 전파 대기

변경 사항이 전파되는 데 시간이 걸릴 수 있습니다. 완료되면 CloudFront를 통해 전 세계적으로 HTTPS를 통해 사용자 정의 도메인을 통해 정적 웹 사이트에 액세스할 수 있습니다.

축하합니다! Amazon S3에 정적 웹 사이트를 성공적으로 배포했고, 전 세계적인 콘텐츠 전달을 위해 CloudFront를 구성하고, Route 53을 통해 DNS를 설정하고, ACM을 통해 SSL/TLS를 구성했습니다.

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

# 프로젝트 # 2 — 클라우드포메이션을 사용하여 아마존 EC2 웹 서버 시작하기

이 프로젝트에서는 AWS 클라우드포메이션을 사용하여 아마존 EC2에 웹 서버를 배포하는 방법을 살펴보겠습니다. 클라우드포메이션을 사용하면 우리가 인프라를 코드로 정의하여 리소스를 생성하고 관리하는 프로세스를 자동화할 수 있습니다. 마지막에는 간단한 웹 페이지를 제공하는 실행 중인 EC2 인스턴스를 갖게 될 것입니다.

## AWS 계정 설정

AWS 계정이 있는지 확인하세요. 계정이 없는 경우 여기에서 가입할 수 있습니다.

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

## 클라우드포메이션 템플릿 생성

ec2-web-server-template.yml이라는 파일을 만드세요. 이 YAML 파일에 클라우드포메이션 템플릿이 포함될 것입니다.

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Resources:
  MyEC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      ImageId: "ami-xxxxxxxxxxxxxxxxx" # 원하는 Amazon Machine Image (AMI)을 지정하세요
      InstanceType: "t2.micro"
      KeyName: "your-key-pair" # 키페어를 지정하세요
      SecurityGroupIds:
        - sg-xxxxxxxxxxxxxxxxx # 보안 그룹 ID를 지정하세요
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash
          echo "Hello from your EC2 instance!" > /var/www/html/index.html
          yum install -y httpd
          service httpd start
          chkconfig httpd on
```

ami-xxxxxxxxxxxxxxxxx, your-key-pair, sg-xxxxxxxxxxxxxxxxx를 원하는 값으로 대체하세요. UserData 스크립트는 Apache를 설치하고 웹 서버를 시작합니다.

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

클라우드포메이션 템플릿은 아마존 EC2 인스턴스를 웹 서버로 프로비저닝합니다. 이는 AMI(아마존 머신 이미지), 인스턴스 유형, 키페어 및 보안 그룹과 같은 인스턴스 속성을 정의합니다. 게다가 UserData 스크립트를 사용하여 Apache를 설치하고 웹 서버를 시작하며 기본 HTML 페이지를 만들어 EC2 기반의 작동하는 웹 서버를 시작하기 위한 완전한 구성을 제공합니다.

## 클라우드포메이션 스택 배포

옵션 1: AWS 관리 콘솔

- AWS 관리 콘솔에 로그인합니다.
- 클라우드포메이션 서비스를 엽니다.
- "스택 생성"을 클릭합니다.
- "템플릿 파일 업로드"를 선택하고 ec2-web-server-template.yml 파일을 업로드합니다.
- "다음"을 클릭합니다.
- 스택 이름을 지정합니다(예: EC2WebServerStack).
- 다음 페이지를 계속 클릭하여 기본 설정을 유지합니다.
- 설정을 검토하고 "스택 생성"을 클릭합니다.

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

옵션 2: AWS CLI

- 터미널을 열고 AWS CLI가 설치되어 있는지 확인하세요.
- 다음 명령을 실행하여 CloudFormation 스택을 생성하세요:

```js
aws cloudformation create-stack --stack-name EC2WebServerStack --template-body file://ec2-web-server-template.yml
```

## 웹 서버 액세스

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

- 스택을 만들었으면 AWS 관리 콘솔의 EC2 서비스로 이동하세요.
- 새로 만든 EC2 인스턴스를 찾으세요.
- 해당 인스턴스의 퍼블릭 IP 또는 퍼블릭 DNS를 확인하세요.
- 웹 브라우저를 열고 주소 표시줄에 퍼블릭 IP 또는 DNS를 입력하세요.

“Hello from your EC2 instance!” 메시지가 표시되면 웹 서버가 실행 중임을 나타냅니다.

여기까지입니다! AWS CloudFormation을 사용하여 웹 서버로 Amazon EC2 인스턴스를 성공적으로 생성하였습니다.

# 프로젝트 # 3 — S3 버킷에 대한 CI/CD 파이프라인 추가

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

프로젝트 # 1에서 생성한 S3 버킷에 대한 CI/CD 파이프라인을 추가해 봅시다.

## AWS CodeBuild 프로젝트 생성

AWS CodeBuild는 Amazon Web Services (AWS)에서 제공하는 완전 관리형 지속적 통합 및 지속적 배포 (CI/CD) 서비스입니다. CodeBuild는 소프트웨어 개발 프로젝트의 빌드, 테스트, 배포 프로세스를 자동화하여 응용 프로그램의 빌드 및 릴리스 단계를 단순화합니다.

AWS CodeBuild 프로젝트를 생성하려면 AWS Management Console, AWS CLI 또는 AWS SDK를 사용할 수 있습니다. AWS CLI를 사용한 예시는 다음과 같습니다:

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
aws codebuild create-project --name YourCodeBuildProjectName \
  --source "type=NO_SOURCE" \
  --artifacts "type=NO_ARTIFACTS" \
  --environment "type=LINUX_CONTAINER,image=aws/codebuild/standard:5.0,computeType=BUILD_GENERAL1_SMALL" \
  --service-role YourCodeBuildServiceRoleArn \
  --region YourRegion
```

실제 값을 지정하여 YourCodeBuildProjectName, YourCodeBuildServiceRoleArn, YourRegion과 같은 자리 표시자를 대체하세요. 이 예제는 소스나 artifact 설정 없이 Linux 컨테이너 이미지를 사용하여 간단한 CodeBuild 프로젝트를 생성합니다. 프로젝트 요구 사항에 따라 소스, artifact, 환경 및 필요한 경우 다른 매개변수를 지정하여 구성을 조정하세요.

지정한 서비스 역할(YourCodeBuildServiceRoleArn)이 빌드 프로세스에서 필요로 하는 리소스에 액세스할 수 있는 권한이 있는지 확인하세요. 이것은 예시입니다. 원하는 역할 이름으로 YourCodeBuildServiceRoleName을 대체하세요:

```js
aws iam create-role \
  --role-name YourCodeBuildServiceRoleName \
  --assume-role-policy-document '{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
          "Service": "codebuild.amazonaws.com"
        },
        "Action": "sts:AssumeRole"
      }
    ]
  }'
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

## AWS CodeStar 프로젝트 설정하기

AWS CodeStar는 AWS에서 애플리케이션의 개발 및 배포를 가속화하는 완전히 관리되는 서비스입니다. 이는 소스 코드 관리, 빌드 및 배포를 포함한 다양한 개발 작업을 관리하고 자동화하는 통합 플랫폼을 제공합니다. CodeStar를 사용하면 팀은 신속하게 지속적 통합/지속적 배포 (CI/CD) 파이프라인을 설정하고 프로젝트에 대해 더 효율적으로 협업할 수 있습니다.

AWS CodeStar 프로젝트의 초기 구성을 설정하여 프로젝트 소스 코드 저장소 및 관련 설정에 대한 필수 정보를 제공해 보겠습니다.

Markdown 형식에 맞게 아래 AWS CodeStar 프로젝트 설정 표를 활용해 주세요.

```bash
aws codestar create-project \
  --name YourCodeStarProject \
  --id YourCodeStarProjectID \
  --description "Your CodeStar Project Description" \
  --repository YourRepository \
  --repository-url YourRepositoryURL \
  --code  {
    "BranchName": "main",
    "Repository": {
      "CodeCommit": {
        "Name": "YourCodeCommitRepositoryName"
      }
    }
  } \
  --region YourRegion
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

AWS CLI 명령어 aws codestar create-project은 AWS CodeStar 프로젝트를 생성하는 데 사용됩니다. 이 예제에서는 해당 명령어가 프로젝트 이름(YourCodeStarProject), 프로젝트 ID(YourCodeStarProjectID), 설명, 소스 코드 저장소 이름(YourRepository), 저장소 URL(YourRepositoryURL) 및 주요 브랜치 이름(main) 및 CodeCommit 저장소 이름(YourCodeCommitRepositoryName)과 같은 코드 관련 세부 정보를 지정합니다. 또한 명령어는 CodeStar 프로젝트가 생성될 AWS 지역(YourRegion)을 지정합니다.

## S3 Bucket을 CodeStar 프로젝트에 연결

```js
aws codestar create-deployment-pipeline \
  --pipeline-name YourPipelineName \
  --pipeline-settings file://pipeline-settings.json \
  --output json \
  --region YourRegion
```

AWS CLI 명령어 aws codestar create-deployment-pipeline은 AWS CodeStar에 배포 파이프라인을 생성합니다. 이는 파이프라인 이름(YourPipelineName), JSON 파일(pipeline-settings.json)에서 파이프라인 설정을 가져와서(output json) 지정하며, 결과 형식을 JSON으로 지정하고 AWS 지역을 YourRegion으로 지정합니다.

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
  "pipeline": {
    "stages": [
      {
        "name": "Source",
        "actions": [
          {
            "actionTypeId": {
              "category": "Source",
              "owner": "AWS",
              "provider": "YourSourceProvider",
              "version": "1"
            },
            "name": "SourceAction",
            "configuration": {
              "Branch": "main",
              "OutputArtifactFormat": "CODEBUILD_CLONE_REF"
            }
          }
        ]
      },
      {
        "name": "Beta",
        "actions": [
          {
            "actionTypeId": {
              "category": "Build",
              "owner": "AWS",
              "provider": "CodeBuild",
              "version": "1"
            },
            "name": "BuildAction",
            "configuration": {
              "ProjectName": "YourCodeBuildProject"
            }
          }
        ]
      },
      {
        "name": "Prod",
        "actions": [
          {
            "actionTypeId": {
              "category": "Deploy",
              "owner": "AWS",
              "provider": "S3",
              "version": "1"
            },
            "name": "DeployAction",
            "configuration": {
              "BucketName": "YourS3BucketName",
              "Extract": "true"
            }
          }
        ]
      }
    ]
  }
}
```

실제 값으로 YourSourceProvider, YourCodeBuildProject, 그리고 YourS3BucketName과 같은 자리 표시자를 대체하세요. 이 구성은 소스, 빌드, 그리고 배포 작업을 포함하는 간단한 세 단계 파이프라인을 나타냅니다. 사용 사례와 요구 사항에 따라 조정하십시오.

YourSourceProvider는 일반적으로 사용 중인 버전 관리 시스템 또는 소스 코드 저장소 제공업체를 가리킵니다. 일반적인 소스 제공자에는 AWS CodeCommit, GitHub, Bitbucket 등이 포함됩니다. 다음은 소스 제공자로 CodeCommit을 사용하는 예시입니다:

```json
"Source": {
  "type": "CODECOMMIT",
  "location": "YourCodeCommitRepositoryName"
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

YourCodeCommitRepositoryName을 실제 CodeCommit 리포지토리 이름으로 바꿔주세요. GitHub을 사용한다면, 아래와 같이 설정할 수 있어요:

```js
"Source": {
  "type": "GITHUB",
  "location": "https://github.com/yourusername/yourrepository",
  "gitCloneDepth": 1
}
```

https://github.com/yourusername/yourrepository을 사용자의 GitHub 리포지토리 URL로 대체해주세요.

## AWS CodePipeline 구성하기

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

AWS CodePipeline은 빌드, 테스트 및 배포 프로세스의 단계를 자동화하는 완전 관리형 지속적 통합 및 지속적 전달(CI/CD) 서비스입니다. 애플리케이션의 워크플로우를 정의하고 시각화할 수 있어 코드 변경사항이 소스에서 프로덕션까지 원활하게 이동할 수 있는 경로를 제공합니다.

```js
aws codepipeline create-pipeline \
  --pipeline-name YourPipelineName \
  --role-arn YourIAMRoleARN \
  --output json \
  --region YourRegion
```

## 파이프라인 테스트

웹사이트 코드를 변경하고 저장소에 커밋합니다. 파이프라인은 코드 변경을 자동으로 감지하고 빌드를 트리거하며 업데이트된 코드를 S3 버킷으로 배포합니다.

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

# 프로젝트 # 4 — AWS Lambda를 사용하여 Amazon CloudWatch 메트릭을 CSV 파일로 게시

AWS Lambda를 사용하여 Amazon CloudWatch 메트릭을 CSV 파일로 게시하는 것은 몇 가지 단계를 거쳐 이루어집니다. 람다 함수를 작성하고 권한을 구성하며 CloudWatch 메트릭을 가져와 CSV 파일에 저장하는 코드를 작성하는 과정이 포함됩니다.

## AWS Lambda를 위한 IAM 역할 생성

```js
# 신뢰 정책 문서 생성 (trust-policy.json)
echo '{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}' > trust-policy.json

# IAM 역할 생성
aws iam create-role \
  --role-name YourRoleName \
  --assume-role-policy-document file://trust-policy.json

# AWSLambdaBasicExecutionRole 정책을 역할에 부착
aws iam attach-role-policy \
  --role-name YourRoleName \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
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

이 명령을 실행한 후 Lambda 실행을 위한 필수 권한을 가진 IAM 역할인 YourRoleName이 생성됩니다. 이 명령을 실행하려면 AWS CLI가 설치되어 있고 이 명령을 실행할 필요한 권한이 구성되어 있어야 합니다.

## 람다 함수 생성

lambda_function.py라는 파일을 만들어 다음 코드를 붙여넣어 CloudWatch 메트릭을 검색하고 CSV 파일에 저장한 후 S3 버킷에 저장합니다. YourNamespace 및 YourMetricName과 같은 자리 표시자를 구체적인 CloudWatch 네임스페이스와 메트릭 이름으로 교체해 주세요.

```js
import boto3
import csv
from datetime import datetime, timedelta

def lambda_handler(event, context):
    # CloudWatch 클라이언트 설정
    cloudwatch = boto3.client('cloudwatch')

    # S3 클라이언트 설정
    s3 = boto3.client('s3')

    # CSV 파일 및 작성자 설정
    csv_columns = ['MetricName', 'Timestamp', 'Value']
    csv_data = []

    # CloudWatch 매개변수 정의
    namespace = 'YourNamespace'
    metric_name = 'YourMetricName'
    start_time = datetime.utcnow() - timedelta(days=1)
    end_time = datetime.utcnow()
    period = 300  # 5분 간격

    # CloudWatch 메트릭 가져오기
    response = cloudwatch.get_metric_data(
        MetricDataQueries=[
            {
                'Id': 'm1',
                'MetricStat': {
                    'Metric': {
                        'Namespace': namespace,
                        'MetricName': metric_name,
                    },
                    'Period': period,
                    'Stat': 'Average',
                    'Unit': 'Count',
                },
                'ReturnData': True,
            },
        ],
        StartTime=start_time,
        EndTime=end_time,
    )

    # CSV 데이터 준비
    for result in response['MetricDataResults'][0]['Timestamps']:
        csv_data.append({
            'MetricName': metric_name,
            'Timestamp': result['Timestamp'].strftime('%Y-%m-%d %H:%M:%S'),
            'Value': result['Value'],
        })

    # CSV 데이터를 S3 버킷에 작성
    s3_bucket = 'your-s3-bucket-name'
    s3_key = 'cloudwatch_metrics.csv'

    s3.put_object(
        Bucket=s3_bucket,
        Key=s3_key,
        Body='\n'.join([','.join(map(str, row.values())) for row in csv_data]),
        ContentType='text/csv'
    )

    print(f"S3 버킷에 저장된 CSV 파일: s3://{s3_bucket}/{s3_key}")
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

```js
# 배포 패키지를 만듭니다 (Python 코드가 lambda_function.py 파일에 있는 것으로 가정)
zip deployment-package.zip lambda_function.py

# 람다 함수를 만듭니다
aws lambda create-function \
  --function-name YourFunctionName \
  --runtime python3.8 \
  --role arn:aws:iam::YourAWSAccountID:role/YourRoleName \
  --handler lambda_function.lambda_handler \
  --zip-file fileb://deployment-package.zip
```

람다 함수와 관련된 IAM 역할에 CloudWatch 권한을 추가하려면 다음 명령어를 사용할 수 있습니다:

```js
aws iam attach-role-policy \
  --role-name YourRoleName \
  --policy-arn arn:aws:iam::aws:policy/CloudWatchReadOnlyAccess
```

이 명령은 CloudWatchReadOnlyAccess 정책을 지정된 IAM 역할에 첨부하여 필요한 권한을 부여합니다.

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

## 람다 함수 테스트

- 람다 콘솔의 "테스트" 버튼을 클릭하여 함수를 수동으로 트리거하세요.
- 오류나 문제가 있는지 확인하기 위해 CloudWatch Logs 및 람다 콘솔을 확인하세요.

## CloudWatch Events 설정 (선택 사항)

Amazon CloudWatch Events는 사용자가 시스템 이벤트에 응답하고 AWS 환경에서 워크플로우를 자동화할 수 있는 완전 관리형 서비스입니다. 다양한 AWS 서비스에서 이벤트를 다른 대상(예: AWS 람다 함수 또는 SNS 토픽)으로 라우팅하는 확장 가능하고 유연한 방법을 제공하여 이벤트 중심 아키텍처를 구축할 수 있습니다.

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

AWS CLI를 사용하여 CloudWatch Events를 설정하려면 다음 명령어를 사용할 수 있어요. 이를 통해 일정한 기간(예: 매일)에 람다 함수를 트리거하는 규칙을 만들 수 있습니다.

```js
# 규칙 생성
aws events put-rule \
  --name YourRuleName \
  --schedule-expression "rate(1 day)"

# 람다 함수를 규칙의 대상으로 추가
aws events put-targets \
  --rule YourRuleName \
  --targets "Id"="1","Arn"="arn:aws:lambda:your-region:your-account-id:function:YourFunctionName"

# 규칙 활성화
aws events enable-rule --name YourRuleName
```

이 명령어들은 다음을 수행합니다:

- 매일 규칙을 트리거하는 스케줄 표현을 가진 CloudWatch Events 규칙을 생성합니다.
- 람다 함수를 규칙의 대상으로 추가합니다.
- 규칙을 활성화하여 활성화합니다.

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

CloudWatch Events로 Lambda 함수를 트리거할 수 있는 필요한 권한을 가지고 있는지 확인해주세요.

```js
# Lambda 함수의 실제 이름으로 YourFunctionName을 교체해주세요.
lambda_function_name="YourFunctionName"

# Lambda 함수에 연결된 현재 IAM 역할 가져오기
lambda_role_arn=$(aws lambda get-function-configuration --function-name $lambda_function_name --query 'Role' --output text)

# AWSLambdaRole 정책을 Lambda 역할에 연결하기
aws iam attach-role-policy \
  --role-name ${lambda_role_arn##*/} \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaRole
```

이 스크립트는 다음 작업을 수행합니다:

- Lambda 함수에 현재 연결된 IAM 역할을 가져옵니다.
- Lambda 역할에 AWSLambdaRole 정책을 연결합니다.

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

# 프로젝트 # 5 — AWS Amplify를 사용하여 간단한 React 웹 애플리케이션 배포하기

## 전제 조건

- AWS 계정: AWS 계정이 설정되어 있는지 확인하세요.
- 로컬 컴퓨터에 Node.js와 npm이 설치되어 있어야 합니다.
- AWS CLI가 설치되고 AWS 자격 증명으로 구성되어 있어야 합니다.

## React 애플리케이션 설정하기

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

Create React App을 사용하여 새 React 애플리케이션을 만들어보세요:

```js
npx create-react-app my-react-app
cd my-react-app
```

## AWS Amplify

AWS Amplify는 풀스택 웹 및 모바일 애플리케이션을 빌드하고 배포하는 프로세스를 간소화하는 포괄적인 개발 플랫폼입니다. 사용자 인증, API 통합, 저장, 호스팅과 같은 작업을 단순화하는 일련의 도구와 서비스를 제공합니다. Amplify를 통해 개발자들은 최소한의 구성으로 다양한 AWS 서비스를 활용하여 확장 가능하고 안전한 애플리케이션을 빠르게 만들 수 있습니다.

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

Amplify 프로젝트를 초기화하기 전에, Amplify CLI가 설치되어 있는지 확인해주세요. 다음 명령어를 사용하여 전역으로 설치할 수 있습니다:

```js
npm install -g @aws-amplify/cli
```

이제 터미널에서 React 애플리케이션의 루트 디렉토리로 이동한 후 아래 명령어를 실행해주세요:

```js
amplify init
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

`amplify init` 명령어는 Amplify 프로젝트 설정을 초기화하고 여러 프롬프트를 통해 안내해줍니다:

- 프로젝트에 이름을 입력하세요: 프로젝트에 고유한 이름을 제공하세요.
- 환경 선택: 기본 환경(일반적으로 dev)을 선택하세요.
- 기본 편집기 선택: 선호하는 코드 편집기를 선택하세요.
- 빌드 중인 앱 유형 선택: React 애플리케이션에 대해 javascript를 선택하세요.
- 사용 중인 JavaScript 프레임워크 선택: react를 선택하세요.
- 소스 디렉토리 경로: 기본값(src)을 유지하세요.
- 배포 디렉토리 경로: 기본값(build)을 유지하세요.
- 빌드 명령어: 기본값(npm run-script build)을 유지하세요.
- 시작 명령어: 기본값(npm run-script start)을 유지하세요.

이러한 세부 정보를 제공한 후에 Amplify는 프로젝트를 초기화하고 필요한 구성 파일 및 디렉토리를 설정합니다.

## Amazon Cognito를 사용한 인증

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

아마존 코그니토는 AWS가 완전히 관리하는 서비스로, 안전한 사용자 식별 및 액세스 관리를 제공합니다. 이는 개발자가 웹 및 모바일 앱에 사용자 가입, 로그인 및 액세스 제어를 쉽게 추가할 수 있게 해줍니다. 다중 인증 및 소셜 신원 연합과 같은 기능을 통해 아마존 코그니토는 견고한 인증 및 권한 부여 메커니즘의 구현을 간편화합니다.

초기화 이후, amplify add 명령을 사용하여 인증, API, 저장소 및 호스팅과 같은 백엔드 서비스를 추가할 수 있습니다. 예를 들어, 인증을 추가하려면 다음을 실행하십시오:

```js
amplify add auth
```

인증 제공자 및 고급 설정을 포함한 인증 설정을 구성하려면 프롬프트를 따르세요.

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

백엔드 서비스를 구성한 후 다음 명령을 사용하여 클라우드에 배포하십시오:

```js
amplify push
```

Amplify는 배포할 변경 사항에 대한 요약을 제시할 것입니다. 프로젝트에 필요한 AWS 리소스를 프로비저닝하기 위해 배포를 확인하십시오.

Amplify 프로젝트를 확인하고 관리하기 위해 다음 명령을 사용하여 Amplify 콘솔을 열어보세요:

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
amplify console
```

이 명령을 실행하면 기본 웹 브라우저에서 Amplify Console이 열리며, 배포된 서비스를 모니터링하고 관리할 수 있는 시각적 인터페이스가 제공됩니다.

## API를 위한 AWS AppSync 설정

AWS AppSync는 확장 가능하고 안전한 GraphQL API 개발을 간소화하는 관리형 서비스입니다. 개발자는 AppSync를 사용하여 애플리케이션을 다양한 데이터 원본에 쉽게 연결할 수 있으며, DynamoDB, Lambda 또는 HTTP 데이터 소스를 포함한 AWS 서비스에 연결할 수 있습니다. AppSync는 실시간 데이터 동기화 및 오프라인 기능을 제공하여, 반응성 있고 협업을 위한 애플리케이션을 구축하는 데 효율적입니다.

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

터미널을 열고 다음 명령을 실행하여 Amplify CLI를 사용하여 새 API를 추가하세요:

```js
amplify add api
```

- API 유형으로 GraphQL을 선택하세요.
- API에 이름을 제공하세요.
- 권한 유형을 선택하세요. 간단하게 API 키를 선택하시면 됩니다.
- 스키마 및 리졸버와 같은 추가 설정을 구성하세요.

Amplify CLI는 GraphQL 스키마를 편집하도록 안내할 것입니다. amplify/backend/api/'API_NAME'/schema 디렉토리에서 생성된 schema.graphql 파일을 열고 GraphQL 스키마를 정의하세요.

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

API를 구성한 후 다음 명령을 사용하여 배포하십시오:

```js
amplify push
```

- Amplify에서 GraphQL API를 위한 코드를 생성하고 싶은지 물어볼 것입니다. GraphQL 작업을 위한 코드를 생성할지 선택할 수 있으며, 이는 API와 상호작용하기 위해 프로젝트에 필요한 파일을 생성합니다.
- 배포를 계속하고 싶은지 확인하십시오.

Amplify는 AWS에서 AppSync API, DynamoDB 테이블(필요한 경우), 관련 IAM 역할을 포함하여 필요한 리소스를 프로비저닝할 것입니다.

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

배포가 완료되면 AWS AppSync 콘솔에서 새로 만들어진 API를 살펴볼 수 있습니다. 다음 명령을 실행하여 콘솔을 열 수 있습니다:

```js
amplify console api
```

이 명령을 실행하면 AWS AppSync 콘솔이 기본 웹 브라우저에서 열립니다. 여기에서 GraphQL 스키마를 볼 수 있고 추가 설정을 구성하거나 내장된 쿼리 편집기를 사용하여 API를 테스트할 수 있습니다.

React 애플리케이션에서 API를 사용하려면 필요한 종속성을 설치하세요.

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
npm install aws-amplify @aws-amplify/ui-react
```

React 앱에서 Amplify를 설정하려면 다음과 같이 하세요:

```js
// src/index.js
import Amplify from "aws-amplify";
import config from "./aws-exports";
Amplify.configure(config);
```

이제 React 컴포넌트에서 생성된 GraphQL 작업을 사용할 수 있습니다.

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
// React 컴포넌트 예시
import { API } from 'aws-amplify';

async function fetchData() {
  try {
    const result = await API.graphql({ query: /* 여러분의 GraphQL 쿼리 */ });
    console.log(result);
  } catch (error) {
    console.error(error);
  }
}
```

AWS AppSync를 API에 성공적으로 설정하고 배포하였으며 React 애플리케이션을 Amplify를 사용하여 API와 상호작용하도록 연결하였습니다.

## 데이터베이스로 Amazon DynamoDB 설정

Amazon DynamoDB는 AWS에서 제공하는 완전히 관리되는 NoSQL 데이터베이스 서비스로, 높은 성능과 확장 가능한 응용 프로그램을 위해 설계되었습니다. 데이터에 대한 낮은 지연 시간 액세스와 함께 처리량 및 스토리지의 신속하고 자동적인 확장을 제공합니다. DynamoDB는 문서 및 키-값 데이터 모델을 모두 지원하며 작은 응용 프로그램부터 대규모로 분산된 글로벌 시스템까지 다양한 사용 사례에 적합합니다.

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

터미널에서 다음 명령을 실행하여 Amplify 프로젝트에 새로운 DynamoDB 테이블을 추가하세요:

```js
amplify add storage
```

- 스토리지 유형으로 NoSQL 데이터베이스를 선택하세요.
- NoSQL 데이터베이스로 Amazon DynamoDB를 선택하세요.
- 테이블 이름을 지정하세요.
- 테이블의 기본 키를 정의하세요. 예를 들어, 문자열 형식의 id를 파티션 키로 사용할 수 있습니다.

Amplify는 추가적인 DynamoDB 테이블 설정을 구성하도록 안내할 것입니다.

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

- 인덱스 추가: 응용 프로그램의 쿼리 요구 사항에 따라 전역 또는 로컬 보조 인덱스를 추가할 수 있습니다.
- 고급 설정 지정: 읽기 및 쓰기 용량 단위와 같은 추가 구성을 설정하거나 기본 값을 사용할 수 있습니다.

DynamoDB 테이블을 구성한 후 AWS 클라우드에 DynamoDB 테이블을 프로비저닝하려면 변경 사항을 배포하십시오:

```js
amplify push
```

알림을 받으면 배포를 확인하십시오.

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

지금 DynamoDB가 설정되어 있습니다. React 애플리케이션에서 이에 접근할 수 있습니다. Amplify는 자동으로 DynamoDB 테이블을 위한 일련의 GraphQL 변이 및 쿼리를 생성합니다. 이들은 프로젝트의 src/graphql 디렉토리에서 찾을 수 있습니다. 예를 들어 Todo 테이블을 추가한 경우, createTodo, updateTodo와 같은 변이 및 listTodos와 같은 쿼리가 있을 수 있습니다.

생성된 쿼리 및 변이를 사용하여 React 컴포넌트에서 DynamoDB와 상호 작용할 수 있습니다:

```js
// 예시: 새 Todo 생성
import { API, graphqlOperation } from "aws-amplify";

async function createTodo() {
  const todoDetails = {
    name: "새로운 할 일",
    description: "새 할 일에 대한 설명",
  };

  try {
    const result = await API.graphql(
      graphqlOperation(createTodo, { input: todoDetails })
    );
    console.log("할 일이 생성되었습니다:", result.data.createTodo);
  } catch (error) {
    console.error("할 일 생성 중 에러 발생:", error);
  }
}
```

AWS Management Console에서 DynamoDB 테이블을 살펴볼 수도 있습니다. 다음 명령어를 실행하여 DynamoDB 콘솔을 열 수 있습니다:

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
amplify console storage
```

이 명령은 기본 웹 브라우저에서 DynamoDB 콘솔을 열어서 DynamoDB 테이블을 확인하고 관리할 수 있게 해줍니다.

Amplify 프로젝트에 Amazon DynamoDB를 성공적으로 설정하고 React 애플리케이션에 통합했습니다. 이제 생성된 GraphQL 작업을 사용하여 DynamoDB 테이블에서 CRUD 작업을 수행할 수 있습니다.

## Amazon S3 및 CloudFront 설정하기

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

터미널에서 다음 명령어를 실행하여 Amplify 프로젝트에 호스팅을 추가하세요:

```js
amplify add hosting
```

- 호스팅 서비스로 Amazon S3와 Amazon CloudFront를 선택하세요.
- 호스팅 환경에 이름을 제공하세요.
- 인덱스 및 오류 문서와 같은 추가 설정을 구성하세요.

호스팅 환경을 구성한 후, 필요한 S3 버킷 및 CloudFront 배포를 생성하기 위해 배포하세요:

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
amplify publish
```

물어볼 때 배포를 확인하세요.

파일 저장을 위해 Amazon S3를 사용하려면(Amazon S3에서 사용자 업로드를 저장하는 등) Amplify 프로젝트 설정을 수정해야 합니다. 프로젝트의 src 디렉터리에 있는 aws-exports.js 파일을 열어 다음 구성을 추가하세요:

```js
// aws-exports.js

const awsmobile = {
  ...
  storage: {
    AWSS3: {
      bucket: '당신의-s3-버킷-이름',
      region: '당신의-지역',
    },
  },
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

`your-s3-bucket-name`을 고유한 S3 버킷 이름으로, `your-region`을 S3 버킷을 생성할 AWS 지역으로 변경해주세요.

S3 및 CloudFront를 설정하면 React 애플리케이션에서 정적 자산을 제공하고 파일을 호스팅하며 콘텐츠 전달에 활용할 수 있습니다.

예를 들어, S3에 파일을 업로드하는 방법은 다음과 같습니다:

```js
// 예시: 파일을 S3에 업로드하기
import { Storage } from "aws-amplify";

async function uploadFile(file) {
  try {
    await Storage.put("파일경로", file, {
      contentType: "image/jpeg", // 파일 유형에 따라 콘텐츠 유형을 조정하세요
    });
    console.log("파일이 성공적으로 업로드되었습니다!");
  } catch (error) {
    console.error("파일 업로드 중 오류 발생:", error);
  }
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

AWS Management Console에서 S3 버킷을 탐색할 수도 있습니다. 다음 명령을 실행하여 S3 콘솔을 열 수 있습니다:

```js
amplify console storage
```

기본 웹 브라우저에서 이 명령을 실행하면 S3 콘솔이 열리며 S3 버킷에 있는 파일을 볼 수 있고 관리할 수 있습니다.

호스팅 환경을 배포한 후, Amplify는 CloudFront 배포 URL을 제공합니다. Amplify 콘솔이나 amplify publish를 실행한 후 출력에서 이 URL을 찾을 수 있습니다. 이 CloudFront URL을 사용하여 전 세계에 배포된 React 애플리케이션에 액세스할 수 있습니다.

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

아마존 S3와 CloudFront를 Amplify 프로젝트에 설정하여 스토리지 및 콘텐츠 전달을 성공적으로 구축했습니다. React 애플리케이션이 호스팅되고 정적 자원이 효율적으로 CloudFront를 통해 전달됩니다.

## React 애플리케이션에 Amplify 통합하기

터미널에서 React용 필수 Amplify 라이브러리를 설치하세요:

```js
npm install aws-amplify @aws-amplify/ui-react
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

당신의 React 애플리케이션에서는 Amplify를 구성하기 위해 다음 코드를 애플리케이션의 진입점에 추가하세요 (일반적으로 src/index.js 또는 src/index.tsx):

```js
// src/index.js

import React from "react";
import ReactDOM from "react-dom";
import Amplify from "aws-amplify";
import awsConfig from "./aws-exports"; // 프로젝트에 이 파일이 존재하는지 확인하세요

Amplify.configure(awsConfig);

ReactDOM.render(
  <React.StrictMode>
    {/* 여러분의 주 애플리케이션 컴포넌트 */}
  </React.StrictMode>,
  document.getElementById("root")
);
```

Amplify는 일반적인 인증 흐름을 위한 미리 제작된 UI 컴포넌트를 제공합니다. 이러한 컴포넌트를 React 애플리케이션에 통합하여 사용자 인증을 할 수 있습니다. 예를 들어, withAuthenticator를 사용하여 주 애플리케이션 컴포넌트를 감쌀 수 있습니다:

```js
// src/App.js

import React from "react";
import { withAuthenticator } from "@aws-amplify/ui-react";

function App() {
  return (
    <div>
      <h1>Your React App</h1>
      {/* 여러분의 앱 콘텐츠 */}
    </div>
  );
}

export default withAuthenticator(App, { includeGreetings: true });
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

앱에 인증 기능을 추가하게 됩니다. 회원 가입, 로그인 및 로그아웃과 같은 기능이 포함됩니다.

인증된 사용자에 대한 정보에 액세스하려면 Amplify에서 제공하는 Auth 모듈을 사용할 수 있습니다:

```js
// 예: 사용자 정보에 액세스
import { Auth } from "aws-amplify";

async function getUserInfo() {
  try {
    const user = await Auth.currentAuthenticatedUser();
    console.log("인증된 사용자:", user);
  } catch (error) {
    console.error("사용자 정보 가져오기 오류:", error);
  }
}
```

Amplify는 인증 이상의 다양한 기능을 제공합니다. API 상호 작용, 저장소 및 실시간 데이터 동기화와 같은 기능이 포함되어 있습니다. Amplify 문서에서 이러한 기능을 살펴볼 수 있습니다.

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

로컬에서 React 애플리케이션을 실행하여 Amplify와의 통합을 테스트해보세요:

```js
npm start
```

애플리케이션에 만족하면 다음 명령어를 사용하여 클라우드에 배포하세요:

```js
amplify publish
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

React 애플리케이션을 구성된 Amplify 서비스와 함께 AWS에 배포할 것입니다.

축하합니다! Amplify를 React 애플리케이션에 성공적으로 통합하여 인증과 다른 Amplify 서비스에 액세스하는 기능을 활성화했습니다. 이제 AWS Amplify 능력이 원할하게 통합된 앱을 배포할 준비가 되었습니다.

# 결론

다섯 가지 프로젝트는 AWS의 다양한 능력을 탐험하고 공유하는 경로로서 선별한 컬렉션입니다. 이 여정을 마칠 때, AWS의 다양한 성격을 이해할 뿐만 아니라 효과적으로 그 능력을 활용할 자신감까지 얻으실 것입니다.
