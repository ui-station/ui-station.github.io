---
title: "AWS Lake Formation에서 데이터 필터를 사용하여 Terraform을 통해 여러 계정 간 액세스 활성화하기"
description: ""
coverImage: "/assets/img/2024-05-18-EnablingCross-AccountAccessforAWSLakeFormationwithDataFiltersUsingTerraform_0.png"
date: 2024-05-18 16:54
ogImage: 
  url: /assets/img/2024-05-18-EnablingCross-AccountAccessforAWSLakeFormationwithDataFiltersUsingTerraform_0.png
tag: Tech
originalTitle: "Enabling Cross-Account Access for AWS Lake Formation with Data Filters Using Terraform"
link: "https://medium.com/@thulasirajkomminar/enabling-cross-account-access-for-lake-formation-with-data-filters-using-terraform-ed6e51528c3a"
---


# 소개

이전 블로그에서는 AWS Lake Formation과 Terraform을 사용하여 다른 계정 간 데이터 공유를 활성화하는 방법을 탐험했습니다. 이번 포스트에서는 데이터 필터를 사용하여 해당 설정을 더욱 향상시키는 방법에 대해 더 깊이 파고들어보겠습니다. Lake Formation 데이터 필터링을 통해 열 수준, 행 수준 및 셀 수준 보안을 구현할 수 있습니다. 이 블로그에서는 특히 셀 수준 보안을 구현하여 데이터 접근 제어를 미세 조정하는 데 중점을 둘 것입니다.

![이미지](/assets/img/2024-05-18-EnablingCross-AccountAccessforAWSLakeFormationwithDataFiltersUsingTerraform_0.png)

## 데이터 필터 수준

<div class="content-ad"></div>

- 열 수준

열 수준 필터링을 사용하여 데이터 카탈로그 테이블에 대한 권한을 부여하면 사용자가 액세스 권한이 있는 특정 열 및 중첩 열만 볼 수 있습니다. 이를 통해 중첩된 열의 부분 하위 구조에 액세스 권한을 부여하는 보안 정책을 정의할 수 있습니다.

- 행 수준

행 수준 필터링을 사용하여 데이터 카탈로그 테이블에 대한 권한을 부여하면 사용자가 액세스 권한이 있는 데이터의 특정 행만 볼 수 있습니다. 필터링은 하나 이상의 열 값에 기반하며, 중첩 열 구조도 행 필터 식에 포함할 수 있습니다.

<div class="content-ad"></div>

- 셀 수준

셀 수준 보안은 행 및 열 필터링을 결합하여 매우 유연한 권한 모델을 제공합니다.

# 소스 계정에 데이터 필터 만들기

이미 이전 블로그에서 자세히 설명한 Source Account에서 Lake Formation 설정을 완료했다고 가정하면, 이제 데이터 필터를 만드는 작업을 진행할 수 있습니다. IIoT 측정을 사용한 예제를 사용해보겠습니다. 여러 사이트에 퍼져있는 장비가 있고 특정 IAM 역할에 특정 사이트 및 열 액세스를 부여해야 하는 경우를 가정해보겠습니다. 이를 Terraform을 사용하여 어떻게 구현할지 알아보겠습니다:

<div class="content-ad"></div>

아래 예시에서:

- 로컬 구성 정의: filter_config 변수는 액세스가 필요한 사이트, 열 및 IAM 역할을 나열합니다.
- AWS 계정 ID 검색: aws_caller_identity 데이터 원본은 현재 AWS 계정 ID를 가져옵니다.
- 데이터 셀 필터 생성: aws_lakeformation_data_cells_filter 자원은 filter_config를 반복하여 각 IAM 역할에 필요한 필터를 생성합니다.

이 설정을 통해 특정 IAM 역할이 정의된 사이트 및 열에만 액세스할 수 있도록 하여 보안과 데이터 관리를 강화할 수 있습니다.

<div class="content-ad"></div>

# 대상 계정과 공유 카탈로그

이제 데이터 필터를 생성했으니, 카탈로그를 공유하는 동안 활용해봅시다. 아래 코드 스니펫에서는 데이터베이스와 테이블을 대상 계정과 공유할 것입니다. 테이블을 공유할 때는 이전 단계에서 생성한 데이터 필터를 함께 포함할 것입니다.

![이미지](/assets/img/2024-05-18-EnablingCross-AccountAccessforAWSLakeFormationwithDataFiltersUsingTerraform_2.png)

이 스니펫에서는:

<div class="content-ad"></div>

- 데이터베이스 권한 공유: aws_lakeformation_permissions 리소스는 IIoTDataLake 데이터베이스를 대상 계정과 공유하여 DESCRIBE 권한을 부여합니다.
- 테이블 권한 공유: 비슷하게, 해당 리소스는 측정 테이블을 대상 계정과 공유하며 SELECT 권한을 부여합니다. 이전에 생성한 데이터 필터도 포함되어 대상 계정이 정의된 기준에 따라 필터링된 데이터에만 액세스할 수 있도록 보장합니다.

이 설정을 통해 카탈로그에서 대상 계정과 특정 데이터를 안전하게 공유하여 규정 준수 및 데이터 무결성을 보장할 수 있습니다.

# 대상 계정에서 액세스용 리소스 링크 생성

대상 계정에 카탈로그와 테이블을 데이터 필터와 함께 공유한 후, 공유된 카탈로그 데이터에 액세스하기 위해 대상 계정으로 이동하여 리소스 링크를 설정합니다.

<div class="content-ad"></div>


![Image](/assets/img/2024-05-18-EnablingCross-AccountAccessforAWSLakeFormationwithDataFiltersUsingTerraform_3.png)

In this setup:

- **Retrieve Source Account ID:** The `aws_caller_identity` data source fetches the AWS account ID of the source account.
- **Create Resource Link:** The `aws_glue_catalog_database` resource establishes a database resource link named `IIoTDataLake-Target` in the target account. It links to the `IIoTDataLake` database in the source account, enabling access to the shared catalog data.

By creating this resource link, you enable seamless access to the shared data catalog from the target account, facilitating data utilization and analysis across accounts while maintaining security and compliance measures.


<div class="content-ad"></div>

# IAM 역할에 권한 부여

이제 우리가 리소스 링크를 생성했으니, 리소스 링크 및 공유 카탈로그에 액세스를 부여할 수 있습니다. 이 단계 이후에 IAM 역할은 소스 계정에서 공유된 필터링된 데이터에 액세스할 수 있게 될 것입니다.

![Image](/assets/img/2024-05-18-EnablingCross-AccountAccessforAWSLakeFormationwithDataFiltersUsingTerraform_4.png)

이 구성에서:

<div class="content-ad"></div>

- 데이터베이스 권한 부여: aws_lakeformation_permissions 리소스는 IIoTDataLake-Target 데이터베이스의 IAM 역할에 DESCRIBE 권한을 부여합니다. 이를 통해 역할은 데이터베이스 구조와 메타데이터를 설명할 수 있습니다.
- 테이블 권한 부여: 마찬가지로, 해당 리소스는 공유 카탈로그의 measurements 테이블에 대한 SELECT 권한을 IAM 역할에 부여합니다. 이를 통해 역할은 테이블에서 데이터를 선택하고 읽을 수 있습니다.

이러한 권한이 부여된 상태에서 IAM 역할은 이제 소스 계정에서 공유된 필터링된 데이터에 액세스할 수 있어 타겟 계정 내에서 매끄러운 데이터 분석과 활용이 가능해졌습니다.

# 결론

이 블로그에서는 AWS Lake Formation과 Terraform을 사용하여 계정간 데이터 공유의 복잡성에 대해 살펴보았습니다. 데이터 필터를 구현하고 리소스 링크를 설정함으로써, 공유 데이터에 안전한 액세스를 보장하면서 권한을 세밀하게 제어할 수 있었습니다. 이러한 간소화된 접근 방식은 계정간 협업적인 데이터 분석을 용이하게하며, 팀이 효과적으로 통찰력을 얻을 수 있도록 도와주면서 데이터 보안 및 규정 준수 기준을 유지합니다.

<div class="content-ad"></div>

# 참고 자료

데이터 필터링 및 셀 레벨 보안 - AWS Lake Formation (amazon.com)