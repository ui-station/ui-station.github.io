---
title: "아저라 데이터브릭스 SQL 웨어하우스 인스턴스의 실제 비용을 계산하는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-HowToCalculatetheRealCostofAzureDatabricksSQLWarehouseInstances_0.png"
date: 2024-06-19 12:28
ogImage: 
  url: /assets/img/2024-06-19-HowToCalculatetheRealCostofAzureDatabricksSQLWarehouseInstances_0.png
tag: Tech
originalTitle: "How To Calculate the Real Cost of Azure Databricks SQL Warehouse Instances"
link: "https://medium.com/@gmusumeci/how-to-calculate-the-cost-of-azure-databricks-sql-warehouse-instances-8baa73411057"
---


<img src="/assets/img/2024-06-19-HowToCalculatetheRealCostofAzureDatabricksSQLWarehouseInstances_0.png" />

이야기에서는 Azure Databricks SQL Warehouse 인스턴스의 비용을 계산하는 방법에 대해 배워보겠습니다.

Databricks SQL Warehouse는 Azure Databricks에서 데이터를 쿼리하고 탐색할 수 있는 컴퓨팅 리소스입니다.

현재 Azure Databricks에서는 3가지 유형의 SQL Warehouse를 제공합니다:

<div class="content-ad"></div>

- SQL Warehouse Classic: SQL Warehouse Classic의 컴퓨팅 레이어는 저희 Azure 구독 계정에 존재하며 Photon을 지원하지만 Predictive IO나 Intelligent Workload Management은 지원하지 않습니다.
- SQL Warehouse Pro: SQL Warehouse Pro의 컴퓨팅 레이어는 저희 Azure 구독 계정에 존재하며 Photon과 Predictive IO를 지원하지만 Intelligent Workload Management는 지원하지 않습니다.
- SQL Warehouse Serverless: Azure Databricks 서버리스 아키텍처를 사용하여 Databricks SQL Warehouse Serverless가 Azure Databricks 계정에 존재하며 Databricks SQL의 모든 성능 기능(Phton, Predictive IO 및 Intelligent Workload Management)을 지원합니다.

위 목록에서 볼 수 있듯이, SQL Warehouse Classic과 SQL Warehouse Pro 사이의 가장 중요한 차이점은 컴퓨팅 레이어가 저희 Azure 구독 계정에 있고, SQL Warehouse Serverless가 저희 Azure Databricks 계정에 있다는 것입니다.

## 관련 이야기:
- Microsoft 및 Databricks API를 사용하여 Azure Databricks 클러스터의 비용을 최적화하고 90%까지 줄이는 방법

<div class="content-ad"></div>

# 1. DIY 방법

첫 번째 섹션은 비용 분석 논리를 이해하고 아마도 자신만의 도구나 스크립트를 작성하여 Azure Databricks SQL Warehouse 인스턴스의 비용을 계산하고자 하는 개발자 또는 기술 직군을 위해 제공됩니다.

# 1.1. DIY 방법 — SQL Warehouse Classic

Azure Databricks SQL Warehouse Classic 또는 Azure Databricks SQL Warehouse Pro의 비용을 계산하려면 다음 구성 요소를 고려해야 합니다:

<div class="content-ad"></div>

- SQL 계산 시간: SQL Warehouse가 실행되었던 시간.
- Databricks Unit (DBU) 시간: SQL Warehouse에서 사용된 컴퓨팅 단위가 시간당 청구됩니다.
- 저장 비용: 적용된 경우 데이터 저장에 연관된 비용.
- 대역폭 비용: 적용된 경우 대역폭 전송에 연관된 비용.

# 1.1.1. Databricks API에서 SQL Warehouse Classic 인스턴스 목록 가져 오기

첫 번째 단계는 Databricks API "/api/2.0/sql/warehouses"를 사용하여 SQL Warehouse 인스턴스 목록을 검색하는 것입니다.

API 호출을 실행한 후, 모든 Databricks SQL Warehouse가 포함 된 JSON 응답을 받게됩니다.

<div class="content-ad"></div>

SQL Warehouse Classic의 JSON은 다음과 같습니다:

```js
"warehouses":[
{
  "id":"2b1613d995c81e7d",
  "name":"Classic Warehouse",
  "size":"XXSMALL",
  "cluster_size":"2X-Small",
  "min_num_clusters":1,
  "max_num_clusters":1,
  "auto_stop_mins":45,
  "auto_resume":true,
  "creator_name":"terraform@kopicloud.net",
  "creator_id":1036471128901988,
  "tags":{ },
  "spot_instance_policy":"COST_OPTIMIZED",
  "enable_photon":true,
  "channel":{
    "name":"CHANNEL_NAME_CURRENT"
  },
  "enable_serverless_compute":false,
  "warehouse_type":"CLASSIC",
  "num_clusters":0,
  "num_active_sessions":0,
  "state":"STOPPED",
  "jdbc_url":"jdbc:spark://adb-xxxxxxxx.x.azuredatabricks.net:443/default;transportMode=http;ssl=1;AuthMech=3;httpPath=/sql/1.0/warehouses/2b1613d995c81e7d;",
    "odbc_params":{
      "hostname":"adb-xxxxxxxx.x.azuredatabricks.net",
      "path":"/sql/1.0/warehouses/2b1613d995c81e7d",
      "protocol":"https",
      "port":443
    }
   }
  }
]
```

위와 유사한 데이터를 얻을 수 있습니다(쉽게 이해할 수 있도록 형식화됨)

<img src="/assets/img/2024-06-19-HowToCalculatetheRealCostofAzureDatabricksSQLWarehouseInstances_1.png" />

<div class="content-ad"></div>

# 1.1.2. Microsoft API를 통해 SQL Warehouse Classic 자원의 비용 가져오기

검색한 사용 데이터와 비용 정보를 결합하여 SQL Warehouse Classic의 비용을 계산해야 합니다.

저희는 Microsoft Generate Cost Details Report API를 사용하여 Databricks 클러스터가 실행되는 Azure 구독에 대한 모든 데이터를 가져올 것입니다.

API를 사용할 때 주의할 점들:

<div class="content-ad"></div>

- Azure 구독의 비용 세부 정보 데이터를 가져올 때, Databricks와 관련된 데이터만 필터링하고 다른 서비스와 비용이 연관되지 않은 데이터는 제거해야 합니다.
- 결과를 Pay-as-you-go 또는 Enterprise 유형의 Azure 구독에 따라 조정해야 하며, 결과가 서로 다릅니다.
- API는 한 달 이하의 데이터만 가져오도록 허용하며 13개월 이전의 데이터는 제공하지 않습니다.

Microsoft API에서 데이터를 추출할 때, 보고서에서 다음 열을 선택해야 합니다:
- SqlEndpointId: SQL Warehouse 인스턴스의 ID
- ProductName: 사용된 Azure 리소스의 설명
- MeterName: 청구되는 서비스 또는 리소스 유형을 식별하는 레이블
- CostInBillingCurrency: 사용된 Azure 리소스의 비용
- BillingCurrency: 청구 통화 코드 (USD, EUR 등)

# 1.1.3. SQL Warehouse 클래식 인스턴스 비용 계산

<div class="content-ad"></div>

Microsoft Databricks 및 Azure API에서 데이터를 검색한 후, 마지막 단계는 SQL Endpoint ID를 사용하여 Azure 비용 데이터를 필터링하고 Databricks API의 SQL Endpoint ID와 일치시키는 것입니다.

우리는 이와 유사한 데이터를 받을 것입니다 (이해하기 쉽게 형식화된 데이터입니다):

![이미지](/assets/img/2024-06-19-HowToCalculatetheRealCostofAzureDatabricksSQLWarehouseInstances_2.png)

SQL Warehouse Classic 인스턴스에서 제품은 Azure Databricks - Premium - SQL Analytics이며, 미터 이름은 Premium SQL Analytics DBU입니다.

<div class="content-ad"></div>

그럼, SQL Warehouse 인스턴스의 최종 비용을 계산하기 위해 모든 레코드를 추가합니다.

# 1.2. 직접 계산 방법 — SQL Warehouse Pro

Azure Databricks SQL Warehouse Pro의 비용을 계산하기 위해 다음 구성 요소를 고려해야 합니다:

- SQL 컴퓨트 시간: SQL Warehouse가 실행된 시간입니다.
- 데이터브릭스 유닛(DBU) 시간: SQL Warehouse에서 사용된 컴퓨트 유닛으로, 매 시간마다 청구됩니다.
- 저장 비용: 데이터 저장에 관련된 비용(해당하는 경우).
- 대역폭 비용: 대역폭 전송에 관련된 비용(해당하는 경우).

<div class="content-ad"></div>

API 호출을 실행한 후, 모든 Databricks SQL Warehouse에 대한 JSON 응답을 받게 됩니다.

다음은 SQL Warehouse Pro의 JSON입니다:

```js
"warehouses":[
{
  "id":"aedd502a582f673a",
  "name":"Starter Warehouse",
  "size":"XXSMALL",
  "cluster_size":"2X-Small",
  "min_num_clusters":1,
  "max_num_clusters":1,
  "auto_stop_mins":10,
  "auto_resume":true,
  "creator_name":"terraform@kopicloud.net",
  "creator_id":1036471128901988,
  "tags":{ },
  "spot_instance_policy":"COST_OPTIMIZED",
  "enable_photon":true,
  "channel":{ },
  "enable_serverless_compute":false,
  "warehouse_type":"PRO",
  "num_clusters":0,
  "num_active_sessions":0,
  "state":"STOPPED",
  "jdbc_url":"jdbc:spark://adb-xxxxxxxx.x.azuredatabricks.net:443/default;transportMode=http;ssl=1;AuthMech=3;httpPath=/sql/1.0/warehouses/aedd502a582f673ad;",
    "odbc_params":{
      "hostname":"adb-xxxxxxxx.x.azuredatabricks.net",
      "path":"/sql/1.0/warehouses/aedd502a582f673a",
      "protocol":"https",
      "port":443
    }
   }
  }
]
```

이와 유사한 데이터를 얻게 될 것입니다.

<div class="content-ad"></div>

![image](/assets/img/2024-06-19-HowToCalculatetheRealCostofAzureDatabricksSQLWarehouseInstances_3.png)

# 1.2.2. Microsoft API를 통한 SQL Warehouse PRO 자원 비용 가져오기

SQL Warehouse Pro 비용을 계산하기 위해서는 검색된 사용 데이터와 비용 정보를 결합하여 총 비용을 계산해야 합니다.

Microsoft Generate Cost Details Report API를 사용하여 Databricks 클러스터가 실행 중인 Azure 구독에 대한 모든 데이터를 가져올 것입니다.

<div class="content-ad"></div>

API를 사용할 때 중요한 사항:

- Azure 구독에 대한 비용 세부 데이터를 가져올 때, Databricks와 관련된 데이터만 필터링하고 다른 서비스와 관련된 데이터 또는 비용이 연관되지 않은 데이터를 제거해야 합니다.
- Azure 구독 유형(유연한 요금제 또는 기업용)에 따라 결과를 조정해야 합니다. 왜냐하면 출력 결과물이 다르기 때문입니다.
- API는 한 달 이하의 데이터만 가져올 수 있으며 13개월 이전의 데이터는 가져올 수 없습니다.

Microsoft API에서 데이터를 추출할 때, 보고서에서 다음 열을 선택해야 합니다.

- SqlEndpointId: SQL Warehouse 인스턴스의 ID
- ProductName: 사용된 Azure 리소스의 설명
- MeterName: 청구되는 서비스 또는 리소스 유형을 식별하는 레이블
- CostInBillingCurrency: 사용된 Azure 리소스의 비용
- BillingCurrency: 청구 통화 코드 (USD, EUR 등)

<div class="content-ad"></div>

# 1.2.3. SQL Warehouse PRO Instances 비용 계산하기

Microsoft Databricks와 Azure API에서 데이터를 검색한 후, 최종 단계는 SQL Endpoint ID를 사용하여 Azure 비용 데이터를 필터링하고 Databricks API에서 SQL Endpoint ID와 일치시키는 것입니다.

다음과 유사한 데이터를 얻게 됩니다:

![이미지](/assets/img/2024-06-19-HowToCalculatetheRealCostofAzureDatabricksSQLWarehouseInstances_4.png)

<div class="content-ad"></div>

SQL Warehouse Pro 인스턴스에서 Product가 Azure Databricks Regional — Premium — SQL Compute Pro이고 MeterName이 Premium SQL Compute Pro DBU인 경우, SQL Warehouse 인스턴스의 최종 비용을 계산하기 위해 모든 레코드를 추가합니다.

# 1.3. 직접 만들기 방법 — SQL Warehouse Serverless

Azure Databricks SQL Warehouse Serverless 인스턴스는 Azure Databricks 계정 내에서 실행되므로 Databricks Unit (DBU) 시간 단위로 청구됩니다.

<div class="content-ad"></div>

API 호출을 실행한 후, Databricks SQL Warehouse에 관한 JSON을 받게 됩니다.

다음은 Serverless SQL Warehouse의 JSON입니다:

```js
"warehouses":[
{
  "id":"e6e7601bde7c3a43",
  "name":"Serveless Warehouse",
  "size":"XXSMALL",
  "cluster_size":"2X-Small",
  "min_num_clusters":1,
  "max_num_clusters":1,
  "auto_stop_mins":10,
  "auto_resume":true,
  "creator_name":"terraform@kopicloud.net",
  "creator_id":1036471128901988,
  "tags":{ },
  "spot_instance_policy":"COST_OPTIMIZED",
  "enable_photon":true,
  "enable_serverless_compute":true,
  "warehouse_type":"PRO",
  "num_clusters":0,
  "num_active_sessions":0,
  "state":"STOPPED",
  "jdbc_url":"jdbc:spark://adb-xxxxxxxx.x.azuredatabricks.net:443/default;transportMode=http;ssl=1;AuthMech=3;httpPath=/sql/1.0/warehouses/e6e7601bde7c3a43;",
    "odbc_params":{
      "hostname":"adb-xxxxxxxx.x.azuredatabricks.net",
      "path":"/sql/1.0/warehouses/e6e7601bde7c3a43",
      "protocol":"https",
      "port":443
    }
   }
  }
]
```

이처럼 유사한 데이터를 얻을 수 있습니다:

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-HowToCalculatetheRealCostofAzureDatabricksSQLWarehouseInstances_5.png" />

# 1.3.2. Microsoft API를 통해 SQL Warehouse Serverless 리소스 비용 얻기

검색된 사용량 데이터를 비용 정보와 결합하여 SQL Warehouse Serverless 비용을 계산해야 합니다.

Databricks 클러스터가 실행 중인 Azure 구독의 모든 데이터를 가져 오기 위해 Microsoft Generate Cost Details Report API를 사용할 것입니다.

<div class="content-ad"></div>

API를 사용할 때 중요한 사항:

- Azure 구독에 대한 비용 세부 내역 데이터를 가져올 때, Databricks와 관련된 데이터만 필터링하고 다른 서비스와 관련 없는 데이터를 제거해야 합니다.
- 결과를 조정해야 하는 이유는 Azure 구독 유형(사용한 만큼 지불 또는 기업 서비스)에 따라 출력이 다르기 때문입니다.
- API는 한 달 이하의 데이터만 검색할 수 있으며 13개월 이전의 데이터는 가져올 수 없습니다.

Microsoft API에서 데이터를 추출할 때 다음 보고서 열을 선택해야 합니다:

- SqlEndpointId: SQL Warehouse 인스턴스의 ID
- ProductName: 사용한 Azure 리소스 설명
- MeterName: 청구되는 서비스 또는 리소스 유형을 식별하는 레이블
- CostInBillingCurrency: 사용한 Azure 리소스 비용
- BillingCurrency: 청구 통화 코드 (USD, EUR 등)

<div class="content-ad"></div>

# 1.3.3. SQL Warehouse Serveless 인스턴스 비용 계산하기

Microsoft Databricks와 Azure API에서 데이터를 검색한 후, 최종 단계는 SQL Endpoint ID를 사용하여 Azure 비용 데이터를 필터링하고 Databricks API에서 SQL Endpoint ID를 매칭하는 것입니다.

우리가 얻게 되는 데이터는 이와 비슷할 것입니다:

![이미지](/assets/img/2024-06-19-HowToCalculatetheRealCostofAzureDatabricksSQLWarehouseInstances_6.png)

<div class="content-ad"></div>

SQL 웨어하우스 서버러스 인스턴스에서, 제품은 Azure Databricks Regional — Premium — Serverless SQL이고 미터 이름은 프리미엄 서버러스 SQL DBU입니다.

그런 다음 SQL 웨어하우스 인스턴스의 최종 비용을 계산하기 위해 모든 레코드를 추가합니다.

## 2. 쉬운 방법

SQL 웨어하우스의 비용을 분 단위로 결정해야하는 경우, KopiCloud Azure Databricks 비용 도구를 사용하는 것이 쉽습니다.

<div class="content-ad"></div>

내가 API 데이터를 가져오고 필터링하며 추출하는 경험을 토대로, Microsoft 및 Databricks API에서 검색한 데이터를 읽고 관리하는 프로세스를 간소화하기 위해 이 도구를 개발했습니다.

이 도구는 간단한 사용자 인터페이스를 사용하며, 몇 분 내에 서식이 지정된 데이터를 검색하려는 FinOps 전문가들을 위해 설계되었습니다.

먼저 "Databricks 비용 찾아보기" 버튼을 클릭합니다.

도구는 Microsoft 및 Databricks API와 연결하고 화면 및 Excel 파일에서 서식이 지정된 데이터를 생성할 것입니다.

<div class="content-ad"></div>

![2024-06-19-HowToCalculatetheRealCostofAzureDatabricksSQLWarehouseInstances_7.png](/assets/img/2024-06-19-HowToCalculatetheRealCostofAzureDatabricksSQLWarehouseInstances_7.png)

두 번째로 마지막 단계에서는 Cost per SQL Warehouse Report 버튼을 클릭하여 SQL Warehouse 및 해당 비용 목록을 생성합니다.

이 도구는 화면에 정보를 표시하고 Excel 파일로 내보냅니다.

![2024-06-19-HowToCalculatetheRealCostofAzureDatabricksSQLWarehouseInstances_8.png](/assets/img/2024-06-19-HowToCalculatetheRealCostofAzureDatabricksSQLWarehouseInstances_8.png)

<div class="content-ad"></div>

도구는 모든 데이터를 Excel 파일로 출력하여 데이터를 조작하거나 사용자 정의 보고서를 작성할 수 있도록 합니다.

![이미지](/assets/img/2024-06-19-HowToCalculatetheRealCostofAzureDatabricksSQLWarehouseInstances_9.png)

또한 도구는 원시 및 형식화된 데이터를 생성하고 일일 및 총 일일 비용을 로컬 스토리지나 Azure Blob Storage에 저장하여 사용자 정의 PowerBI 보고서를 생성할 수 있습니다.

그게 다에요. 만약 이 이야기가 마음에 드셨다면 👏을 눌러주세요. 읽어 주셔서 감사합니다!

<div class="content-ad"></div>

- KopiCloud Azure Databricks Cost 도구는 KopiCloud 웹사이트에서 다운로드할 수 있어요.
- 만약 Databrick 클러스터 비용을 줄이는 데 도움이 필요하다면 Linkedin에서 저에게 연락해 주세요.
- 게시된 이미지는 Flaticon의 Dewi Sari가 만든 Cost 아이콘을 사용하여 생성되었어요.

## 관련 이야기:

- Microsoft 및 Databricks API를 사용하여 Azure Databricks 클러스터 비용을 최대 90%까지 최적화하고 줄이는 방법