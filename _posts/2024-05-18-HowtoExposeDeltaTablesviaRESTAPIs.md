---
title: "Delta 테이블을 REST API를 통해 노출하는 방법"
description: ""
coverImage: "/assets/img/2024-05-18-HowtoExposeDeltaTablesviaRESTAPIs_0.png"
date: 2024-05-18 18:06
ogImage:
  url: /assets/img/2024-05-18-HowtoExposeDeltaTablesviaRESTAPIs_0.png
tag: Tech
originalTitle: "How to Expose Delta Tables via REST APIs"
link: "https://medium.com/towards-data-science/how-to-expose-delta-tables-via-rest-apis-53b4dd7afa4e"
---

## 델타 테이블을 제공하기 위해 토론 및 테스트된 세 가지 아키텍처

![이미지](/assets/img/2024-05-18-HowtoExposeDeltaTablesviaRESTAPIs_0.png)

# 1. 소개

메달리온 아키텍처 내의 델타 테이블은 일반적으로 데이터 제품을 생성하는 데 사용됩니다. 이러한 데이터 제품은 데이터 과학, 데이터 분석 및 보고를 위해 사용됩니다. 그러나 데이터 제품을 REST API를 통해 노출하는 것도 일반적인 문제입니다. 이 아이디어는 이러한 API를 더 엄격한 성능 요구 사항을 갖춘 웹 앱에 내장하는 것입니다. 중요한 질문은 다음과 같습니다:

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

데팔타 테이블에서 데이터를 읽는 것이 웹 애플리케이션에 빠르게 서비스할 수 있을까요?
솔루션을 확장할 수 있는 컴퓨팅 레이어가 필요할까요?
엄격한 성능 요구 사항을 충족시키기 위한 스토리지 레이어가 필요할까요?

이러한 질문에 대해 심층적으로 다루기 위해 세 가지 아키텍처가 다음과 같이 평가됩니다: 아키텍처 A — API의 라이브러리, 아키텍처 B — 컴퓨팅 레이어 및 아키텍처 C — 스토리지 레이어. 아래 이미지 참조하세요.

![image](/assets/img/2024-05-18-HowtoExposeDeltaTablesviaRESTAPIs_1.png)

블로그 글의 나머지 부분에서 세 가지 아키텍처에 대한 설명을 제공하고, 배포 및 테스트를 수행한 후 결과를 도출합니다.

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

# 2. 아키텍처 설명

## 2.1 아키텍처 A: DuckDB와 PyArrow를 사용한 API 내 라이브러리

이 아키텍처에서는 API가 직접 델타 테이블에 연결되어 있으며 중간에 계산 레이어가 없습니다. 이는 데이터가 API 자체의 메모리와 계산을 사용하여 분석된다는 것을 의미합니다. 성능을 향상시키기 위해 내장 데이터베이스 DuckDB와 PyArrow의 Python 라이브러리를 사용합니다. 이러한 라이브러리는 API에서 필요한 열만로드되도록 보장합니다.

![이미지](/assets/img/2024-05-18-HowtoExposeDeltaTablesviaRESTAPIs_2.png)

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

이 아키텍처의 장점은 데이터를 중복으로 만들 필요가 없으며 API와 델탔 테이블 사이에 필요한 레이어가 없다는 것입니다. 이는 구성 요소가 적다는 것을 의미합니다.

이 아키텍처의 단점은 확장하기 어렵고 모든 작업을 API의 컴퓨팅 및 메모리에서 처리해야 한다는 것입니다. 특히 많은 양의 데이터를 분석해야 하는 경우에는 특히 도전적입니다. 이는 많은 레코드, 큰 컬럼 또는 많은 동시 요청에서 나올 수 있습니다.

## 2.2 아키텍처 B: Synapse, Databricks 또는 Fabric을 사용하는 컴퓨팅 레이어

이 아키텍처에서 API는 컴퓨팅 레이어에 연결되고 델탔 테이블에 직접 연결되지 않습니다. 이 컴퓨팅 레이어는 델타 테이블에서 데이터를 가져와 데이터를 분석합니다. 컴퓨팅 레이어는 Azure Synapse, Azure Databricks 또는 Microsoft Fabric일 수 있으며 일반적으로 잘 확장됩니다. 데이터는 컴퓨팅 레이어로 중복되지 않지만 컴퓨팅 레이어에서 캐싱을 적용할 수 있습니다. 이 블로그의 남은 부분에서는 Synapse Serverless로 테스트 되었습니다.

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

![image](/assets/img/2024-05-18-HowtoExposeDeltaTablesviaRESTAPIs_3.png)

이 아키텍처의 장점은 데이터를 중복하여 저장할 필요가 없으며 아키텍처가 잘 확장된다는 것입니다. 또한 대규모 데이터 세트를 처리하는 데 사용할 수 있습니다.

이 아키텍처의 단점은 API와 델타 테이블 사이에 추가적인 레이어가 필요하다는 것입니다. 이는 더 많은 이동 부품을 유지 및 보안해야 한다는 의미입니다.

## 2.3 아키텍처 C: Azure SQL이나 Cosmos DB를 사용한 최적화된 저장 레이어

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

이 아키텍처에서 API는 델타 테이블에 직접 연결되지 않고, 델타 테이블이 복제된 다른 저장 계층에 연결됩니다. 다른 저장 계층은 Azure SQL 또는 Cosmos DB일 수 있습니다. 이 저장 계층은 데이터를 빠르게 검색하기 위해 최적화될 수 있습니다. 이 블로그의 나머지 부분에서는 Azure SQL을 사용하여 테스트를 진행합니다.

![이미지](/assets/img/2024-05-18-HowtoExposeDeltaTablesviaRESTAPIs_4.png)

이 아키텍처의 장점은 저장 계층이 인덱스, 파티셔닝 및 머티얼라이즈드 뷰를 사용하여 데이터를 빠르게 읽을 수 있도록 최적화될 수 있다는 것입니다. 이는 주로 요청-응답 웹 앱 시나리오에서 요구 사항입니다.

이 아키텍처의 단점은 데이터가 중복되어야 하며 API와 델타 테이블 사이에 추가적인 계층이 필요하다는 것입니다. 이는 더 많은 구성 요소를 유지보수하고 보안해야 한다는 의미입니다.

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

블로그의 나머지 부분에서 아키텍처를 배포하고 테스트합니다.

# 3. 아키텍처 배포 및 테스트

## 3.1 아키텍처 배포

아키텍처를 배포하기 위해 이전 장에서 논의한 세 가지 솔루션을 배포하는 GitHub 프로젝트가 생성되었습니다. 해당 프로젝트는 아래 링크에서 확인할 수 있습니다:

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
https://github.com/rebremer/expose-deltatable-via-restapi
```

다음은 GitHub 프로젝트를 실행할 때 배포될 내용입니다:

- 표준 테스트 데이터 세트 WideWorldImporterdDW full에서 시작한 델타 테이블. 테스트 데이터 세트는 50백만 건의 레코드와 22개 열로 구성되어 있으며 1개의 큰 설명 열이 있습니다.
- 모든 아키텍처: API로 작용하는 Azure Function.
- 아키텍처 B: 컴퓨팅 계층으로 작용하는 Synapse Serverless.
- 아키텍처 C: 최적화된 저장 계층으로 작용하는 Azure SQL.

배포된 후 테스트를 실행할 수 있습니다. 다음 단락에서 테스트에 대해 설명하겠습니다.

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

## 3.2 테스트 아키텍처

아키텍처를 테스트하기 위해 다양한 유형의 쿼리 및 다른 스케일링을 적용할 것입니다. 다양한 유형의 쿼리는 다음과 같이 설명할 수 있습니다:

- 11개의 작은 열(char, integer, datetime)을 포함하는 20개 레코드를 조회합니다.
- 각 필드당 500자 이상을 포함하는 큰 설명 열이 포함된 2개 열을 사용하여 20개 레코드를 조회합니다.
- 그룹별 데이터 집계, having, max, average를 사용한 데이터 집계.

아래에서 쿼리를 설명합니다.

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

```sql
-- 쿼리 1: 대형 텍스트 없이 11개 열의 포인트 조회
SELECT SaleKey, TaxAmount, CityKey, CustomerKey, BillToCustomerKey, SalespersonKey, DeliveryDateKey, Package
FROM silver_fact_sale
WHERE CityKey=41749 and SalespersonKey=40 and CustomerKey=397 and TaxAmount > 20
-- 쿼리 2: 500자 이상의 Description 열
SELECT SaleKey, Description
FROM silver_fact_sale
WHERE CityKey=41749 and SalespersonKey=40 and CustomerKey=397 and TaxAmount > 20
-- 쿼리 3: 집계
SELECT MAX(DeliveryDateKey), CityKey, AVG(TaxAmount)
FROM silver_fact_sale
GROUP BY CityKey
HAVING COUNT(CityKey) > 10
```

다음과 같이 스케일링이 가능합니다:

- 아키텍처 A의 경우, 데이터 처리는 API 자체에서 수행됩니다. 이는 API의 컴퓨트 및 메모리가 앱 서비스 플랜을 통해 사용된다는 것을 의미합니다. SKU Basic(1코어 및 1.75GB 메모리) 및 SKU P1V3 SKU(2코어, 8GB 메모리)로 테스트될 것입니다. 아키텍처 B 및 C의 경우에는 처리가 다른 곳에서 이루어지기 때문에 이러한 정보는 해당하지 않습니다.
- 아키텍처 B의 경우, Synapse Serverless가 사용됩니다. 스케일링은 자동으로 이루어집니다.
- 아키텍처 C의 경우, 표준 티어의 Azure SQL 데이터베이스가 125 DTU로 사용됩니다. CityKey에 인덱스가 없는 상태와 CityKey에 인덱스가 있는 상태에서 테스트될 것입니다.

다음 단락에서 결과가 설명됩니다.

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

## 3.3 결과

아키텍처를 배포하고 테스트한 후에는 결과를 얻을 수 있습니다. 다음은 결과 요약입니다:

![Results](/assets/img/2024-05-18-HowtoExposeDeltaTablesviaRESTAPIs_5.png)

아키텍처 A는 SKU B1로 배포할 수 없습니다. 만약 SKU P1V3가 사용된다면, 컬럼 크기가 크지 않다면 결과는 15초 이내에 계산될 수 있습니다. 모든 데이터를 API 앱 서비스 계획에서 분석한다는 점을 유의하십시오. 너무 많은 데이터가로드되면(많은 행, 큰 컬럼 및/또는 많은 동시 요청으로),이 아키텍처는 확장하기 어려울 수 있습니다.

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

아키텍처 B는 Synapse Serverless를 사용하여 10-15초 내에 작동합니다. 계산은 데이터를 가져와 분석하기 위해 자동으로 조정되는 Synapse Serverless에서 이루어집니다. 성능은 세 가지 유형의 쿼리에 대해 일관되게 유지됩니다.

아키텍처 C는 Azure SQL을 사용할 때 인덱스가 생성되면 가장 잘 작동합니다. 조회 쿼리 1과 2의 경우 API는 대략 1초 내에 응답합니다. 쿼리 3은 전체 테이블 스캔이 필요하며 성능은 다른 솔루션과 거의 동일합니다.

# 3. 결론

중재 아키텍처의 Delta 테이블은 일반적으로 데이터 제품을 생성하는 데 사용됩니다. 이러한 데이터 제품은 데이터 과학, 데이터 분석 및 보고서 작성에 사용됩니다. 그러나 일반적으로 Delta 테이블을 REST API를 통해 노출하는 것도 자주 묻는 질문 중 하나입니다. 이 블로그 포스트에서는 이와 같은 장단점을 갖는 세 가지 아키텍처가 설명되어 있습니다.

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

Architecture A: DuckDB 및 PyArrow를 사용하여 API 내 라이브러리를 활용하는 아키텍처입니다.
이 아키텍처에서는 API가 직접 델타 테이블에 연결되어 중간 계층이 없습니다. 이는 모든 데이터가 메모리에서 분석되고 Azure Function의 연산을 함께 함을 의미합니다.

- 이 아키텍처의 장점은 추가 리소스가 필요하지 않다는 것입니다. 이는 유지 및 보안해야 하는 부분이 적기 때문에 이점으로 작용합니다.
- 이 아키텍처의 단점은 API 자체에서 모든 데이터를 분석해야 하기 때문에 확장성이 떨어진다는 것입니다. 따라서 소량의 데이터에만 사용해야 합니다.

Architecture B: Synapse, Databricks 또는 Fabric을 사용한 컴퓨팅 레이어.
이 아키텍처에서는 API가 컴퓨팅 레이어에 연결됩니다. 이 컴퓨팅 레이어는 델타 테이블에서 데이터를 가져와 분석합니다.

- 이 아키텍처의 장점은 확장성이 좋고 데이터가 중복되지 않습니다. 집계를 수행하며 대량의 데이터를 분석하는 쿼리에 적합합니다.
- 이 아키텍처의 단점은 조회 쿼리에 일관되게 5초 이내의 응답을 받는 것이 불가능하다는 것입니다. 또한 추가 리소스를 보안 및 유지해야 합니다.

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

아키텍처 C: Azure SQL 또는 Cosmos DB를 사용한 최적화된 저장 계층입니다.

이 아키텍처에서는 API가 최적화된 저장 계층에 연결됩니다. 델타 테이블이 미리 이 저장 계층으로 복제되며 데이터를 검색하고 분석하는 데 사용됩니다.

- 이 아키텍처의 장점은 인덱스, 파티셔닝, 머티얼라이즈드 뷰를 사용하여 룩업의 빠른 쿼리를 위해 최적화될 수 있다는 것입니다. 이것은 종종 요청-응답 웹 앱에 필요한 요구사항입니다.
- 이 아키텍처의 단점은 데이터가 다른 저장 계층으로 중복되어 동기화가 유지되어야 한다는 것입니다. 또한 추가 자원을 보안하고 유지해야 합니다.

안타깝게도, 완벽한 해결책은 없습니다. 이 글은 REST API를 통해 델타 테이블을 노출하는 데 가장 적합한 아키텍처를 선택하는 데 도움을 주기 위한 가이드를 제시했습니다.
