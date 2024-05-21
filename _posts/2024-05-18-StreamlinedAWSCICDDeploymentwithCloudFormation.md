---
title: "AWS CloudFormation을 활용한 간편한 CICD 배포 설정 설정"
description: ""
coverImage: "/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_0.png"
date: 2024-05-18 16:09
ogImage: 
  url: /assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_0.png
tag: Tech
originalTitle: "Streamlined AWS CI CD Deployment with CloudFormation"
link: "https://medium.com/@jayshenkar27apr/streamlined-aws-ci-cd-deployment-with-cloudformation-e1077fef0634"
---


<img src="/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_0.png" />

# CI/CD란 무엇인가요?

CI 및 CD는 지속적 통합(Continuous Integration) 및 지속적 전달/지속적 배포(Continuous Delivery/Continuous Deployment)를 의미합니다. CI/CD 파이프라인은 소프트웨어 전달 프로세스를 자동화하여 코드 빌드, 테스트 실행, 새로운 소프트웨어 버전의 안전한 배포와 같은 작업을 포함합니다. 자동화는 인적 오류를 줄이고 소프트웨어 릴리스 프로세스에서 일관성을 보장하기 위한 것입니다.

# 시작하기

<div class="content-ad"></div>

이 기사의 목표는 AWS를 사용하여 애플리케이션을 구축, 테스트 및 배포하는 과정을 실제 예제를 통해 안내하는 것입니다.

다음은 배포하는 애플리케이션에 맞게 조정된 단계입니다. 구체적으로 Java Spring MVC 웹 애플리케이션 (Java 버전 17)입니다.

사용된 AWS 서비스 -

- EC2
- S3
- VPC
- CodeCommit
- CodeBuild
- CodeDeploy
- CodePipeline
- CloudFormation

<div class="content-ad"></div>

# AWS 아키텍처 다이어그램

![AWS Architecture Diagram](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_1.png)

# 시작해봅시다

## 단계 1: CodeCommit

<div class="content-ad"></div>

AWS CodeCommit은 사용자가 소스 코드 및 자산을 안전하고 확장 가능하게 저장, 관리 및 버전 관리할 수 있도록 하는 AWS의 관리형 소스 제어 서비스입니다. 이는 GitHub이 하는 것과 마찬가지로 작동합니다.

![이미지](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_2.png)

## 단계 2: AWS IAM에서 git 자격 증명 설정하기.

‘AWS CodeCommit을 위한 HTTPS Git 자격 증명’ 섹션에서 ‘자격 증명 생성’을 클릭하세요.

<div class="content-ad"></div>

더 나아가, 이 자격 증명을 다운로드하고, Git 자격 증명을 입력하라는 메시지가 표시됩니다. 앞서 다운로드한 사용자 이름과 비밀번호를 입력하세요.

## 3단계: 프로젝트를 CodeCommit 저장소에 커밋하십시오

저장소를 복제할 디렉터리로 이동합니다. 다음 명령을 실행하세요:

```js
git clone <your-codecommit-repo-clone-https-url>
```

<div class="content-ad"></div>

로컬 브랜치에서 CodeCommit 리포지토리로 변경 사항을 푸시하려면 다음 명령을 사용하세요:

```js
git push origin master
```

CodeCommit 리포지토리로 변경 사항이 제대로 푸시되었는지 확인해주세요.

![이미지](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_3.png)

<div class="content-ad"></div>

## 단계 4: S3 버킷 생성

버킷 이름을 입력하고 버킷을 생성하고자 하는 지역을 선택하십시오. 이 버킷에 대한 블록 공개 액세스 설정 섹션에서 모든 공개 액세스 차단 상자를 해제하십시오. 다른 설정은 그대로 두고 '버킷 만들기'를 클릭하여 버킷을 생성하십시오.

![이미지](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_4.png)

버킷이 성공적으로 생성되었는지 확인하십시오.

<div class="content-ad"></div>


![Step 5: CodeBuild](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_5.png)

As you can see our S3 bucket has been successfully created. Now, let’s dive into our AWS CodeBuild service

![Step 5: CodeBuild](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_6.png)


<div class="content-ad"></div>

AWS CodeBuild 서비스로 이동하여 아래와 같이 빌드 프로젝트를 생성하세요. 원하는 프로젝트 이름을 지정해주세요.

![CodeBuild 프로젝트 생성](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_7.png)

이제 소스 제공자로 AWS CodeCommit을 선택하여 프로젝트 파일을 CodeCommit 저장소에 저장했기 때문에 해당 저장소를 드롭다운 옵션으로 선택하세요.

![CodeCommit 저장소 선택](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_8.png)

<div class="content-ad"></div>

환경 설정을 기본값으로 유지하십시오.

다음으로 새 서비스 역할을 선택하고 다른 설정은 그대로 유지하십시오.

![이미지](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_9.png)

빌드 사양에서는 buildspec.yml 파일을 사용할 것입니다. buildspec 파일의 이름을 지정하십시오.

<div class="content-ad"></div>

참고: buildspec.yml 파일은 CodeCommit 리포지토리에 저장되어 있어야 합니다.

![이미지](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_10.png)

아래는 우리 프로젝트에서 사용한 buildspec.yml 파일입니다:

```js
version: 0.2

phases:
  install:
    runtime-versions:
      java: corretto17
  pre_build:
    commands:
      - echo "Maven 종속성 설치 중..."
      - mvn clean install -DskipTests=true
  build:
    commands:
      - echo "Spring MVC 애플리케이션 빌드 중..."
      - mvn package
  post_build:
    commands:
      - echo "빌드가 성공적으로 완료되었습니다!"
artifacts:
  files:
    - target/*.war
    - scripts/*.sh
    - appspec.yml
  discard-paths: yes
```

<div class="content-ad"></div>

이제 아티팩트 섹션에서 아티팩트 유형으로 Amazon S3를 선택하고 "Bucket Name" 아래에서 생성한 버킷 이름을 선택하세요. 또한 버킷 내에서 빌드 아티팩트를 저장할 폴더 이름을 지정해주세요.

참고: 아티팩트 패키징은 .zip으로 사용하세요.

아래는 테이블 태그입니다.


<img src="/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_11.png" />


나머지 구성은 기본값으로 남겨두고 "빌드 프로젝트 생성"을 클릭하세요. "빌드 프로젝트 생성"을 클릭하면 새 인터페이스로 리디렉션되어 프로젝트 빌드를 시작할 수 있습니다.

<div class="content-ad"></div>


![이미지](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_12.png)

빌드 시작을 클릭하면 빌드 프로세스가 시작됩니다.

![이미지](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_13.png)

빌드 소요 시간은 buildspec.yml 파일의 구성에 따라 다를 수 있습니다.


<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_14.png" />

프로젝트가 성공적으로 빌드되었습니다. 모든 단계에서 성공 상태가 확인되었습니다. 빌드가 성공적으로 완료되었습니다!

이제 빌드 아티팩트가 S3 버킷에 저장되었는지 확인해 봅시다.

<img src="/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_15.png" />

<div class="content-ad"></div>

저희 빌드 아티팩트는 이제 "cloud-project-codebuild-1.zip"라는 폴더 아래 버킷에 안전하게 저장되어 있습니다. 이 지정된 버킷은 프로젝트 후속 단계에서 아티팩트를 배포하는 믿을 수 있는 저장소 역할을 할 것입니다.

## 단계 6: 인프라 구축 설정

AWS CloudFormation 템플릿의 도움으로 인프라를 시작했습니다. 자동화를 통해 품질을 향상시키고 비용을 절감하며 유연성을 향상시킬 수 있습니다. 원한다면 수동으로 리소스를 시작할 수도 있습니다.

다음 링크로 CFT를 확인할 수 있습니다:

<div class="content-ad"></div>

노트: 위 템플릿으로 리소스를 출시하는 경우에는 반드시 AMI ID, 역할 및 기타 매핑을 사용하도록 해주세요.


![Image 16](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_16.png)

![Image 17](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_17.png)

![Image 18](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_18.png)


<div class="content-ad"></div>

스택이 성공적으로 시작되었고 모든 리소스가 준비되었습니다.

## 단계 7: CodeDeploy

CodeDeploy를 시작하려면 먼저 2가지 역할을 생성해야 합니다.

- CodeDeploy 역할: 기본 CodeDeploy 권한
- EC2를 위한 역할: AWSCodeDeployFullAccess 또는 EC2RoleForCodeDeploy AWS 관리 정책을 역할에 부여하세요. 이제 이 역할을 이전에 시작한 EC2 인스턴스에 부여하세요.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_19.png" />

<img src="/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_20.png" />

CodeDeploy는 기본적으로 appspec.yml이라는 YAML 파일을 사용하며 소스 코드 루트에서 찾을 수 있습니다. 파일 참조는 여기에 있습니다.

```yaml
version: 0.0
os: Linux

files:
  - source: /
    destination: /home/ec2-user/server

permissions:
  - object: /
    pattern: "**"
    owner: ec2-user
    group: ec2-user

hooks:
  BeforeInstall:
    - location: server_clear.sh
      timeout: 300
      runas: ec2-user
  AfterInstall:
    - location: fix_privileges.sh
      timeout: 300
      runas: ec2-user
  ApplicationStart:
    - location: server_start.sh
      timeout: 20
      runas: ec2-user
  ApplicationStop:
    - location: server_stop.sh
      timeout: 20
      runas: ec2-user
```

<div class="content-ad"></div>

The appspec.yml file uses shell scripts to manage the hooks. You can find these scripts here.

Next, navigate to the CodeDeploy service and create an application by specifying the application name.

![Image 1](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_21.png)

![Image 2](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_22.png)

<div class="content-ad"></div>

어플리케이션이 생성된 후에 이제 배포 그룹을 만듭니다. 배포 그룹에 이름을 지정하세요.

![image](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_23.png)

이제 코드 배포 권한을 가진 역할을 선택하여 AWS CodeDeploy가 대상 인스턴스에 액세스할 수 있도록 합니다.

배포 유형 선택중:

<div class="content-ad"></div>

- 변경 대상: 수정 사항이 적용될 때 애플리케이션이 잠시 오프라인 상태가 됩니다.
- Blue/Green (선호): 수정 사항이 적용된 새 인스턴스가 시작되는 동안 이전 인스턴스는 여전히 사용자에게 서비스를 제공합니다. 여기서 업데이트는 다운타임 없이 이루어집니다.

우리가 시작하는 애플리케이션은 그다지 중요하지 않으며 잠시 오프라인 상태가 되는 몇 초의 다운타임을 견딜 수 있기 때문에 In-Place 배포 방식을 선택할 것입니다.

<img src="/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_24.png" />

배포 설정에서 다른 구성은 기본 설정으로 유지합니다. 이제 배포 그룹을 생성하세요.

<div class="content-ad"></div>

![image1](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_25.png)

이제 "배포 생성"을 클릭하여 전진하세요. 모든 설정은 그대로 두고 아래 그림과 같이 리비전 유형만 선택하세요.

참고: S3에 저장된 artifact의 URI를 복사하여 리비전 위치로 사용합니다.

![image2](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_26.png)

<div class="content-ad"></div>

이제 출발할 준비가 되었습니다. 배포를 시작하세요.

![image](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_27.png)

성공했습니다! 내 애플리케이션이 EC2 인스턴스에 배포되고 Auto Scaling 그룹에 의해 스케일링되었습니다. ALB DNS를 통해 애플리케이션에 액세스할 수 있습니다.

## 단계 8: CodePipeline

<div class="content-ad"></div>

AWS 콘솔의 CodePipeline으로 이동하여 "파이프라인 생성"을 클릭하세요.

![이미지](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_28.png)

CodeCommit 리포지토리, CodeBuild 프로젝트 및 CodeDeploy 응용 프로그램을 언급하여 파이프라인을 생성하세요.

![이미지](/assets/img/2024-05-18-StreamlinedAWSCICDDeploymentwithCloudFormation_29.png)

<div class="content-ad"></div>

모든 단계가 성공적으로 완료된 후, 엔드포인트 URL을 방문해주세요. 여러분의 웹 애플리케이션이 이제 라이브 상태여야 합니다.

축하드립니다! 여러분은 원활한 최고급 AWS CICD 파이프라인을 성취하셨습니다 🔥

# 결론

이 글에서는 AWS와 CloudFormation을 사용하여 Java-17 Maven 애플리케이션의 CI/CD 파이프라인을 설정하고 최적화하는 과정을 살펴보았습니다. 빌드, 테스트 및 배포 단계를 자동화함으로써 더 효율적이고 안정적이며 확장 가능한 배포 프로세스를 구현했습니다. 이는 인간 에러 가능성을 줄일뿐만 아니라 소프트웨어 전달에 일관된 접근 방식을 보장합니다.

<div class="content-ad"></div>

저희 프로젝트에서 소중한 협업을 해준 Mohit Jaiswal에게 감사의 말씀을 전하고 싶습니다. 함께 작업함으로써 배포 프로세스를 효율적으로 간소화하고 응용 프로그램을 최고 품질 기준으로 전달할 수 있었습니다.

읽어주셔서 감사합니다! 이 안내서가 여러분이 AWS와 CloudFormation을 활용하여 CI/CD를 마스터하는 데 도움이 되기를 바라며, 궁금한 점이나 피드백이 있으시면 아래 댓글을 남겨주세요.

즐거운 배포되시길 바랍니다!