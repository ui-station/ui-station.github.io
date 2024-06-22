---
title: "AWS Glue CI CD 간소화하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-StreamliningAWSGlueCICD_0.png"
date: 2024-06-23 00:23
ogImage: 
  url: /assets/img/2024-06-23-StreamliningAWSGlueCICD_0.png
tag: Tech
originalTitle: "Streamlining AWS Glue CI CD"
link: "https://medium.com/data-engineer-things/streamlining-aws-glue-ci-cd-298edda9e844"
---


## 포괄적인 청사진

끊임없이 변화하는 데이터 관리 환경에서, 생 데이터를 실행 가능한 통찰로 변환하는 것은 전 세계 비즈니스에게 중요합니다. AWS Glue는 바로 이를 위한 AWS 내의 서비스로, 추출(Extract), 변환(Transform), 로드(Load) 작업에 특화된 서비스입니다.

Glue는 본질적으로 전통적으로 복잡하고 시간이 많이 소요되는 ETL 작업을 간편화하여 핵심 작업을 자동화하고 중요 인프라를 추상화하며 직관적인 인터페이스를 제공합니다.

그러나 대부분의 클라우드 서비스와 마찬가지로 Glue ETL 작업의 테스트, 배포, 조정에는 코드 버전 관리, 의존성 관리, 인프라 설정 및 액세스 제어 등의 복잡성이 포함되어 여러 도전 과제가 발생합니다. 이러한 도전 과제를 해결하기 위해서는 AWS Glue에 특화된 잘 구조화된 CI/CD 전략이 필요합니다.

<div class="content-ad"></div>

이 블로그 포스트는 이러한 전략을 탐구합니다. 구체적으로, 우리는 AWS Glue CI/CD 워크플로우를 최적화하고 자동화하기 위해 설계된 포괄적인 청사진을 살펴봅니다. 이는 동반 GitHub 저장소를 통해 이용할 수 있습니다.

# GitHub 저장소 개요

이 청사진의 GitHub 저장소는 집단 학습 과정을 통해 생산되었으며, 주요 컨셉의 예시를 제공합니다. Glue 기반 데이터 레이크 구현의 출발점으로 활용하고 있으며, 데이터 엔지니어들의 교육을 가속화하고 팀원들과 함께 다양한 배포 접근 방식을 검증하는 데 도움이 됩니다. 이는 다음과 같이 구성되어 있습니다:

<div class="content-ad"></div>

```js
aws-glue-ci-cd-blueprint
├──.github/
   └──workflows/ (CI/CD configurations)
├──infrastructure/
   └──environments/ (deployment environment configurations)
      └──dev/
      └──prod/
      └──qa/
      └──staging/
   └──modules/ (reusable infrastructure components)
      └──athena/
      └──core/
      └──glue/
├──src/ (ETL code written in Python)
├──tests/ (unit tests for the Python code)
├──...
```

주요 구성 요소와 프레임워크는 다음과 같습니다:

- 지속적 통합/지속적 전달: GitHub Actions
- 인프라스트럭처 코드: Terraform
- 코드 품질 보증 (Python): Unittest + Pytest, Black 및 Flake8

이 설계도를 활용하기 위해 도구를 숙달하는 것보다 개념을 잘 이해하는 것이 중요하다는 점을 강조할 가치가 있습니다. 예를 들어, GitHub Actions 코드는 쉽게 GitLab CI로 변환할 수 있습니다. Terraform과 AWS Cloud Formation에도 동일하게 적용되며, 선택한 프로그래밍 언어와 QA 도구에도 물론 그와 같이 적용됩니다.

<div class="content-ad"></div>

# 청사진 시작하기

아래 이미지를 살펴보세요. 인프라 및 Python 코드를 위한 두 개의 수영선이 있음을 알 수 있습니다. 이러한 코드 조각들은 데이터 파이프라인 개발 수명주기 동안 서로 다른 사람이 다루거나 적어도 구분된 시기에 처리되는 경험을 보여주고 있기 때문에 우리는 이들을 별도로 다루기로 결정했습니다. 일반적인 경험에 따르면 인프라는 어떤 ETL 작업을 배포하기 전에 프로비저닝되어야 합니다.

![image](/assets/img/2024-06-23-StreamliningAWSGlueCICD_1.png)

## 자동화

<div class="content-ad"></div>

현대 데이터 팀은 데이터 파이프라인을 개발, 유효성 검사 및 전달하는 데 다른 환경을 사용합니다. 이 환경은 클라우드에서 실행되며 많은 연결된 리소스에 의존합니다. 자동화 없이 이러한 환경 간에 리소스 구성을 복제하는 것은 거의 불가능합니다. 그렇지만 청사진은 인프라 및 코드 품질 보증을 위한 자동화 안내를 제공합니다.

IaC 측면에서 Terraform은 데이터 파이프라인과 관련된 모든 AWS 리소스를 설정하는 역할을 맡습니다. 예를 들어, S3 버킷, 최소 권한 IAM 역할, Glue 작업, 워크플로, 데이터 카탈로그를 공급하는 크롤러 및 Athena를 위한 잘 정의된 IAM 정책 등이 있습니다.

ETL 코드에 대해 단위 테스트, 형식, 및 린트 체크는 새로운 커밋이 Pull Requests에 푸시되거나 dev 및 main 브랜치로 병합될 때마다 실행되어 파이프라인을 깨뜨릴 수 있는 변경을 방지합니다. 그리고 모든 것이 잘 진행된다면 코드는 적절한 위치로 배포됩니다.

GitHub에서 실행되는 단계는 GitHub Actions를 통해 구성됩니다. 자세한 내용은 aws-glue-ci-cd-blueprint/.github/workflows를 참조해 주세요; on-iac-로 시작하는 파일은 IaC의 CI/CD에 속하며 on-으로 시작하는 파일은 Python 코드의 CI/CD에 속합니다.

<div class="content-ad"></div>

여전히 개발자들은 특히 개발 환경(“배포 전략” 섹션에 설명된)을 사용할 때 Terraform, AWS CLI 및 품질 보증 명령을 로컬에서 실행할 수 있습니다.

## 테스트

Terraform 구성에는 세 가지 유형의 확인이 적용되며, 그 중 하나라도 실패하는 경우 인프라 배포 파이프라인이 즉시 중지됩니다 (참조용으로 iac-pr-against-dev.yaml 참조):

- 스타일 확인: terraform fmt -check
- 코드 정확성: terraform validate
- 실행 계획 실현 가능성: terraform plan

<div class="content-ad"></div>

파이썬 코드에는 세 가지 유형의 체크가 적용되며, 실패 시 배포 파이프라인을 중단합니다 (참고용: on-pr-against-dev.yaml):

- 단위 테스트: pytest
- 스타일 체크: black --check ./src ./tests
- 린터: flake8 ./src ./tests

## 배포 전략

아래에 설명된 네 가지 환경을 다루는 청사진입니다.

<div class="content-ad"></div>


![이미지](/assets/img/2024-06-23-StreamliningAWSGlueCICD_2.png)

프로젝트 요구 사항에 따라 모든 환경을 사용하는 것은 과도할 수 있지만, 적어도 개발 및 프로덕션 환경을 갖는 것이 좋습니다. 이렇게 하면 개발자와 사용자 경험을 더 향상시킬 수 있습니다.

## 사용 지침

주의: 이 부분에서 설명된 실습 가이드를 처음 사용하는 경우, 도구에 대한 전문 지식에 따라 몇 시간이 걸릴 수 있습니다. 그러나 블루프린트에 익숙해지는 것은 가치가 있습니다. 더 짧은 전달 주기 및 더 높은 품질 기준의 혜택은 몇 차례 상호 작용 후에 옵니다.


<div class="content-ad"></div>

개념적 개요를 이해한 후에 블루프린트로 놀아보려 합니다. 저희는 Glue 작업을 설정할 것입니다. 해당 작업은 `awsglue-datasets` 공개 S3 버킷에서 `examples/us-legislators/all/persons.json` 파일을 지정하신 개인 버킷으로 복사할 것입니다. 그 다음 단계에서 Crawler가 테이블을 검사하고 데이터 카탈로그 항목을 생성할 것입니다. 이 작업 흐름은 간단하지만 시연 목적으로 충분합니다. 이미지 3는 Terraform 모듈, AWS 리소스 및 그들 간의 관계를 설명합니다.

![image](/assets/img/2024-06-23-StreamliningAWSGlueCICD_3.png)

다음을 전제로 합니다:

- `aws-glue-ci-cd-blueprint` 리포지토리의 `main` 및 `dev` 브랜치를 복사하셨습니다.
- 로컬 환경에 Terraform 및 AWS CLI가 설치되어 있습니다.
- Glue, IAM 및 S3에 대한 관리 권한이 있는 것으로 가정합니다. 그렇지 않으면 다음 단계를 따를 때 도움이 필요할 수 있습니다. Athena 접근 권한이 있다면 더 좋습니다.

<div class="content-ad"></div>

테라폼은 생성하는 모든 AWS 리소스에 dev|qa|staging|prod 접미사를 추가합니다. 이렇게 함으로써 네 개의 배포 환경을 하나의 계정에서 구성할 수 있습니다. 이는 교육 목적에 적합하지만 적어도 운영 환경은 별도로 존중받고 적절히 관리되는 계정에서 사용되어야 합니다.

이제 몇 가지 수동 단계가 필요합니다... 다음 권한 정책을 가진 IAM 사용자가 필요합니다:

- AmazonS3FullAccess
- AWSKeyManagementServicePowerUser
- AWSGlueConsoleFullAccess
- IAMFullAccess
- kms:EnableKeyRotation 및 kms:EnableKeyDeletion을 실행할 수 있는 사용자 정의 정책

이 사용자는 로컬 개발 환경에서 테라폼과 AWS CLI에서만 사용될 예정이므로 콘솔 액세스가 필요하지 않습니다. 이 사용자를 위해 액세스 키를 생성하고 해당 ID 및 비밀을 안전하게 보관하세요.

<div class="content-ad"></div>

Terraform 상태 파일을 저장하기 위한 S3 Bucket도 필요합니다. infrastructure/environments/*/provider.tf 파일을 확인하면 glue-ci-cd-terraform 버킷이 블루프린트를 위해 구성되어 있다는 것을 알 수 있으니, 이 파일들을 업데이트해주세요.

또한, infrastructure/environments/*/variables.tf 파일에서 다음 버킷들을 위한 다른 이름을 설정해주세요: data_bucket_name, glue_assets_bucket_name, glue_scripts_bucket_name, athena_query_results_bucket_name. 이름만 제공하면, Terraform이 버킷을 생성할 것입니다.

이제 보여주는 시간입니다! 리포지토리 루트 폴더에서 터미널에서 다음 명령어를 실행하세요:

```js
export AWS_ACCESS_KEY_ID=<당신의-액세스-키-ID>
export AWS_SECRET_ACCESS_KEY=<당신의-비밀-액세스-키>

export TF_VAR_aws_access_key=$AWS_ACCESS_KEY_ID
export TF_VAR_aws_secret_key=$AWS_SECRET_ACCESS_KEY

cd infrastructure/environments/dev/
terraform init
terraform plan
terraform apply
```

<div class="content-ad"></div>

샘플 ETL 파이프라인 인프라를 구성하는 약 20개의 AWS 리소스가 개발 환경(dev 접미사)에서 생성됩니다. 이에는 S3 버킷, IAM 정책 및 역할, Glue 작업, 데이터베이스, 크롤러 및 워크플로우가 포함됩니다.

개발자 설정을 마무리하기 위해 ETL 코드를 스크립트 버킷에 복사해주세요:

```js
cd ../../..
aws s3 sync --delete ./src s3://<YOUR-GLUE-SCRIPTS-BUCKET>
```

그런 다음 AWS 콘솔에서 Glue 워크플로우 페이지로 이동하여 glue-ci-cd-us-legislators-dev를 선택합니다. 워크플로를 실행하고 완료될 때까지 약 10분 정도 기다려주세요.

<div class="content-ad"></div>


아래 Markdown 형식의 표를 참조하세요.

![이미지](/assets/img/2024-06-23-StreamliningAWSGlueCICD_4.png)

데이터 버킷을 확인하고 원본 데이터가 JSON 형식으로 저장된 bronze 폴더를 찾으세요. 또한 Parquet 형식으로 데이터가 저장된 silver 폴더도 있습니다. JSON에서 Parquet으로 변환하는 것이 샘플 파이프라인의 한 단계입니다. Glue Workflow에는 silver 테이블을 검토하는 크롤러가 있으며 관련된 데이터 카탈로그 항목을 생성합니다.

![이미지](/assets/img/2024-06-23-StreamliningAWSGlueCICD_5.png)

마지막 단계는 CI/CD를 위해 GitHub 저장소를 구성하는 것입니다. 저장소 설정 페이지로 이동하여 환경을 선택하세요. quality-assurance, staging, production 세 개의 환경을 만드세요. 각각에 대해 AWS_ACCOUNT_ID 비밀 및 AWS_REGION 및 GLUE_SCRIPTS_S3_BUCKET 변수를 설정하세요. 여기에서는 GitHub Action 실행기가 특정 IAM 역할을 가정하기 때문에 액세스 키나 시크릿을 제공해야 할 필요가 없다는 점을 주목하세요.

<div class="content-ad"></div>

CI/CD YAML 파일 중 하나를 다시 확인하고 GitHub Action 러너가 GlueCICDGitHubActionsServiceRole을 가정하는지 확인해주세요. 이 IAM 역할은 아직 AWS 계정에 생성되지 않았으므로 이를 생성하고 Amazon Web Services에서 OpenID Connect 구성 지침을 따라 설정을 완료하세요. 이전에 생성된 IAM 사용자에 부여된 동일한 권한을 부여해주세요.

작업을 완료하면 “배포 전략” 섹션에 설명된 규칙에 따라 나머지 세 개 환경에서 자동화 및 품질 체크를 활용할 수 있습니다. 스크립트를 변경하고 파이프라인에 새 단계를 추가하며, 저장소에 풀 리퀘스트를 생성하고 이를 dev 브랜치로 병합한 뒤 dev를 main에 병합하여 결과를 확인해주세요. 또한 CI/CD 실행 예제를 참조하기 위해 블루프린트 저장소를 참고할 수도 있습니다.

![AWS GlueCICD](/assets/img/2024-06-23-StreamliningAWSGlueCICD_6.png)

# 도전과 고려 사항

<div class="content-ad"></div>

제공된 청사진은 AWS Glue CI/CD 파이프라인을 간소화하기 위한 유용한 자원을 제공하지만, 항상 개선할 부분이 있습니다. 앞으로 진행할 때 극복하고자 하는 주요 장벽은 다음과 같습니다:

- 다중 개발 및 QA 환경 지원. 현재는 개발 및 품질 보증 환경이 각각 하나씩만 있어 대규모 팀에 대해 동시성 문제를 발생시킬 수 있습니다. 더 나은 해결책은 각 엔지니어마다 하나의 개발 환경 및 각 Pull Request에 대해 하나의 QA 환경을 지원하는 것입니다.
- 데이터 품질 기능. DataOps는 데이터에 대해 적용된 단순한 DevOps 이상을 의미합니다. 현재 청사진은 처음 릴리스된 Glue 데이터 파이프라인용 DevOps일 뿐입니다. 자동으로 데이터 품질 점검을 설정하는 것이 DataOps 범주로 승격하는 데 도움이 될 것입니다.
- 통합 테스트. 유닛 테스트는 Python 코드에 대해 잘 작동하지만, ETL 파이프라인은 일반적으로 단위 테스트에 적합하지 않은 SQL 문에 의존합니다. 통합 또는 시스템 테스트 지원을 추가하는 것이 적절할 것입니다.

# 결론

데이터 관리의 동적 영역에서 AWS Glue의 ETL 프로세스를 용이하게 하는 역할은 조직이 데이터의 잠재력을 효과적으로 활용할 수 있도록 하는 중요한 힘으로 남아 있습니다. 이 탐험을 통해 AWS Glue 작업에 대한 CI/CD 관리의 내재적인 도전 과제를 극복하기 위한 포괄적인 청사진을 소개하면서 이러한 복잡성에 직면하는 것을 목표로 했습니다.

<div class="content-ad"></div>

변화를 받아들이는 것은 때로는 시간과 노력을 투자하는 것이 필요합니다. 사용 설명서에 상세히 설명된 실습 단계들은 특히 이 도구에 익숙하지 않은 사람들에게는 시간이 좀 걸릴 수 있습니다. 그러나 걱정하지 마세요, 이 노력의 결실은 처음 투자한 가치가 충분히 있을 것입니다.

글을 마무리하며, 데이터 민첩성 여정에 참여하실 것을 초대합니다. GitHub 저장소를 살펴보고, 구현 세부사항에 익숙해지고, 이 원칙을 프로젝트 요구에 맞게 적용해 보세요. 도움이 필요하시면 언제든지 연락 주세요.

귀하의 데이터 프로젝트가 효율성과 성공으로 가득하길 기원합니다!