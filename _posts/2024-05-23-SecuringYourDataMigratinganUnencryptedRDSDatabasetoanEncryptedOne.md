---
title: "데이터 안전 보장 암호화되지 않은 RDS 데이터베이스를 암호화된 데이터베이스로 이전하기"
description: ""
coverImage: "/assets/img/2024-05-23-SecuringYourDataMigratinganUnencryptedRDSDatabasetoanEncryptedOne_0.png"
date: 2024-05-23 13:59
ogImage:
  url: /assets/img/2024-05-23-SecuringYourDataMigratinganUnencryptedRDSDatabasetoanEncryptedOne_0.png
tag: Tech
originalTitle: "Securing Your Data: Migrating an Unencrypted RDS Database to an Encrypted One"
link: "https://medium.com/@fatmazribi/securing-your-data-migrating-an-unencrypted-rds-database-to-an-encrypted-one-3c47e65dcb76"
---

![태그](/assets/img/2024-05-23-SecuringYourDataMigratinganUnencryptedRDSDatabasetoanEncryptedOne_0.png)

Amazon RDS DB 인스턴스에 대한 암호화는 생성 시에만 활성화할 수 있습니다. 기존 인스턴스를 암호화하려면 스냅샷을 생성한 후 해당 스냅샷의 암호화된 복사본을 만들어 새로운 암호화된 인스턴스로 복원합니다. 다운타임이 허용되는 경우에는 새 인스턴스로 애플리케이션을 전환하십시오. 최소한의 다운타임을 위해 AWS 데이터베이스 마이그레이션 서비스(AWS DMS)를 사용하여 데이터를 지속적으로 마이그레이션하고 복제함으로써 새로운 암호화된 데이터베이스로의 원활한 전환을 허용할 수 있습니다.

## 1. 암호화 상태 확인

첫 번째 단계는 현재 사용 중인 RDS 데이터베이스가 암호화되었는지 확인하는 것입니다. AWS 관리 콘솔에 로그인하고 RDS 서비스로 이동합니다. 대상 데이터베이스를 찾고 "구성" 탭을 클릭합니다. "암호화" 섹션을 찾아보십시오. 만약 "활성화되지 않음"으로 표시된다면 데이터베이스가 암호화되지 않았음을 의미합니다.

<div class="content-ad"></div>

![이미지](/assets/img/2024-05-23-SecuringYourDataMigratinganUnencryptedRDSDatabasetoanEncryptedOne_1.png)

## 2. 스냅샷 생성

암호화하려는 인스턴스의 DB 스냅샷을 만듭니다. 스냅샷을 만드는 데 걸리는 시간은 데이터베이스의 크기에 따라 다릅니다. 이제 왼쪽 메뉴에서 "스냅샷" 옵션으로 이동하고 "스냅샷 촬영"을 클릭합니다. 쉽죠?

![이미지](/assets/img/2024-05-23-SecuringYourDataMigratinganUnencryptedRDSDatabasetoanEncryptedOne_2.png)

<div class="content-ad"></div>

이제 스냅샷을 만들 데이터베이스를 선택해야 해요:

![image](/assets/img/2024-05-23-SecuringYourDataMigratinganUnencryptedRDSDatabasetoanEncryptedOne_3.png)

이를 "UnencryptedSnapshot"이라고 이름 짓을 수 있어요. 실제 데이터베이스의 사본이 될 거에요. 스냅샷이 생성되기를 기다려 주세요. 제 경우에는 약 2분이 걸렸어요.

## 3. 사본 암호화

<div class="content-ad"></div>

이제 생성한 DB 스냅샷을 선택해 주세요. 작업에서 '스냅샷 복사'를 선택하세요. 대상 AWS 지역 및 DB 스냗샷 사본의 이름을 해당 필드에 제공해 주세요. '암호화 사용' 확인란을 선택하세요. 마스터 키로는 DB 스냅샷 사본을 암호화하는 데 사용할 KMS 키 식별자를 지정하세요. '스냅샷 복사'를 선택하세요.

그리고 보시다시피 제가 암호화를 활성화했으며 (기본) aws/rds를 AWS KMS 키로 선택했습니다.

![이미지](/assets/img/2024-05-23-SecuringYourDataMigratinganUnencryptedRDSDatabasetoanEncryptedOne_4.png)

두 번째 스냅샷이 완료될 때까지 다시 기다릴 것입니다. (참고: 5분이 걸렸습니다)

<div class="content-ad"></div>

## 4. 암호화된 데이터베이스 복원

스냅샷을 사용할 수 있는 상태가 되면, 데이터베이스를 복원해야 합니다. "암호화된 스냅샷"을 선택한 후 "작업"을 클릭한 다음 "스냅샷 복원"을 선택하세요. DB 인스턴스 식별자에는 새로운 DB 인스턴스를 위한 고유한 이름을 제공하세요. 동일한 구성을 유지하겠으며 필요에 따라 편집할 수도 있습니다. 모든 옵션을 확인한 후 복원을 클릭하세요.

지금은 DB가 생성 중인 상태입니다:

![image](/assets/img/2024-05-23-SecuringYourDataMigratinganUnencryptedRDSDatabasetoanEncryptedOne_5.png)

<div class="content-ad"></div>

## 5. DMS를 사용한 데이터 마이그레이션

마지막 단계는 DMS로 이동하여 작업을 만들어야 합니다. 그러기 전에 소스 엔드포인트, 대상 엔드포인트 및 복제 인스턴스를 만들어야 합니다.

먼저 복제 인스턴스에서 시작해야 합니다. 왼쪽 메뉴 탭에서 '복제 인스턴스'를 선택하고 "복제 인스턴스 생성"을 선택하세요.

![이미지](/assets/img/2024-05-23-SecuringYourDataMigratinganUnencryptedRDSDatabasetoanEncryptedOne_6.png)

<div class="content-ad"></div>

다른 구성에 대한 선호도를 포함할 수 있습니다. 인스턴스가 생성되었으므로 엔드포인트로 진행할 수 있습니다.

소스 엔드포인트의 경우 DMS 콘솔에서 "엔드포인트"를 선택하고 "엔드포인트 생성"을 해야 합니다.

아래 이미지에서 보이는 대로 소스 엔드포인트로 비암호화된 RDS를 선택해야 합니다:

![image](/assets/img/2024-05-23-SecuringYourDataMigratinganUnencryptedRDSDatabasetoanEncryptedOne_7.png)

<div class="content-ad"></div>

![이미지](/assets/img/2024-05-23-SecuringYourDataMigratinganUnencryptedRDSDatabasetoanEncryptedOne_8.png)

수동으로 자격 증명을 추가하는 경우, 비밀번호를 추가해야할 것입니다. 비밀번호를 추가하려면 "검색"을 클릭하면 모든 데이터베이스 정보를 찾을 수 있는 시크릿 매니저를 확인해야 합니다.

![이미지](/assets/img/2024-05-23-SecuringYourDataMigratinganUnencryptedRDSDatabasetoanEncryptedOne_9.png)

이번에도 대상 엔드포인트에 대해 동일한 단계를 수행할 것입니다. 새 데이터베이스를 선택하기만 하면 됩니다. 이제 두 개의 엔드포인트가 준비되었으므로 작업을 생성할 수 있습니다:


<div class="content-ad"></div>

아래는 Markdown 형식의 텍스트입니다.


![이미지](/assets/img/2024-05-23-SecuringYourDataMigratinganUnencryptedRDSDatabasetoanEncryptedOne_10.png)

작업을 만들려면 왼쪽 메뉴에서 "데이터 마이그레이션 작업"으로 이동하여 작업을 만들어야 합니다:

![이미지](/assets/img/2024-05-23-SecuringYourDataMigratinganUnencryptedRDSDatabasetoanEncryptedOne_11.png)

소스, 대상, 복제 인스턴스 및 마이그레이션 유형을 포함한 설정을 올바르게 구성하고, "기존 데이터 마이그레이션 및 지속적인 변경 복제"를 선택해야 합니다:


<div class="content-ad"></div>

아래의 표태그를 Markdown 형식으로 변경해주세요.

<div class="content-ad"></div>

https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/encrypt-an-existing-amazon-rds-for-postgresql-db-instance.html

https://docs.aws.amazon.com/dms/latest/userguide/Welcome.html
