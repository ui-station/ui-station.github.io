---
title: "실무에서 활용하는 MAX_BY의 DBSQL 사용 사례 - Gold Layer Views"
description: ""
coverImage: "/assets/img/2024-05-18-RealWorldUseCaseforMAX_BYinDBSQLGoldLayerViews_0.png"
date: 2024-05-18 16:29
ogImage:
  url: /assets/img/2024-05-18-RealWorldUseCaseforMAX_BYinDBSQLGoldLayerViews_0.png
tag: Tech
originalTitle: "Real World Use Case for MAX_BY in DBSQL — Gold Layer Views"
link: "https://medium.com/dbsql-sme-engineering/real-world-use-case-for-max-by-in-dbsql-gold-layer-views-43cd8ad1e170"
---

제가 SQL에서 MAX_BY의 가장 좋아하는 사용 사례는 데이터의 골드 레벨 뷰를 열별로 제어하는 것입니다.

![image](/assets/img/2024-05-18-RealWorldUseCaseforMAX_BYinDBSQLGoldLayerViews_0.png)

## 작성자:

Cody Austin Davis

## 소개

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

이 글은 MAX_BY/MIN_BY 집계 함수를 사용하는 가장 일반적인 실제 사용 사례 중 하나를 설명하는 짧고 간결한 글입니다. 이 함수는 종종 발견되지 않는다고 생각되며, 주로 사람들이 존재를 모르기 때문에 덜 주목 받습니다. 이 함수는 알려지지 않은 훌륭한 함수일 뿐만 아니라, 한 번 알게 되면 데이터를 효과적으로 사용하는 여러 방법을 보게 될 것입니다. 오늘은 가장 많이 본다고 생각하는 하나를 다룰 것입니다 — 데이터 소스 간에 데이터에 대한 통합된 뷰를 만드는 것입니다.

## 어떤 사용 사례인가요?

기업들은 자주 여러 데이터 소스를 갖습니다. CRM, 앱 데이터, 공개 데이터 소스, 심지어 공급망에서 다양한 고객/이해관계자로부터 받은 데이터도 마찬가지입니다. 이럴 때, 여러 데이터 소스에서 동일한 엔터티를 나타내는 데이터가 실제로 여러 곳에서 들어온다는 것은 흔합니다. 예를 들어, "고객"이라는 엔터티라고 가정해봅시다. 소매 웹사이트를 운영 중이라면, 여러 곳에서 고객 데이터를 얻을 수 있으며, 더 나아가 이러한 데이터 소스 간에 해당 고객에 대한 다양한 데이터 포인트를 얻을 수 있습니다. 주문, 고객 프로필, 반품, 리뷰 등이 있습니다. 분석에서 가장 가치 있는 통찰은 모든 이러한 데이터 소스를 특정 고객에 대한 완전하고 통합된 뷰로 펼치는 경우에만 얻을 수 있습니다. 이때 MAX_BY가 도움이 됩니다!

예제를 살펴보겠습니다. 여러 데이터 소스에서 고객 정보를 받아 "고객" 테이블에 입력하는 시스템을 가정해보겠습니다. 그런 다음 모든 이러한 데이터 소스에서 가져온 모든 고객 레코드를 "통합된 뷰"로 통합하여 각 "마스터 고객"에 대한 가장 최근의 정의를 나타내는 방식입니다.

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

![Customer Table Example 1](/assets/img/2024-05-18-RealWorldUseCaseforMAX_BYinDBSQLGoldLayerViews_1.png)

Here is an example output of the customer table, assuming each record is from a separate data source:

![Customer Table Example 2](/assets/img/2024-05-18-RealWorldUseCaseforMAX_BYinDBSQLGoldLayerViews_2.png)

Usually, this requires a sort of fuzzy matching Entity Resolution (blog coming soon on this topic) system for this type of use case, but for the sake of brevity, we will assume that we are already able to “stitch” these disparate records together to identify entities that are the same and link them with a master_customer_id. This can also apply to other scenarios where we just want to create a unified view for an event across entities as well (customers, orders, returns, etc). We will review 3 ways to create the “Unified View”.

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

## 1 — 가장 최근 고객 레코드 선택하기

가장 명백한 아이디어입니다. 우리는 그냥 가장 최근 업데이트된 레코드를 "마스터 고객" 레코드로 만들면 됩니다. 이것은 쉽고 이해하기 쉽지만, 모든 데이터 소스의 데이터를 동시에 효과적으로 활용할 수 없다는 단점이 있습니다. 예를 들어, Shipment 데이터 소스에 전화번호가 누락되어 있지만 Order 데이터 소스에는 전화번호가 있지만 Shipment 데이터 소스가 가장 최근 레코드인 경우, 우리의 뷰는 마스터 뷰에서 전화번호를 NULL로 표시할 것입니다. 우리의 그림은 완전하게 보이지 않고 견고하지 않습니다. 이 뷰와 출력의 SQL 예제는 다음과 같습니다:

```js
-- 윈도우 함수 및 QUALIFY를 사용하여 가장 최근 마스터 id 레코드 선택하기
SELECT
*
FROM customers
QUALIFY ROW_NUMBER()
  OVER (PARTITION BY master_customer_id ORDER BY update_timestamp DESC) = 1;
```

<img src="/assets/img/2024-05-18-RealWorldUseCaseforMAX_BYinDBSQLGoldLayerViews_3.png" />

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

이 방법에는 중요한 제한이 있습니다. 작은 예제에서는 일부 데이터가 누락되었습니다. SSN을 살펴보세요. 다른 데이터 소스에 이 속성이 있지만 가장 최근 레코드가 아닌 경우, 해당 데이터는 사용되지 않을 것입니다. 이 문제를 해결해 보겠습니다.

## 2 — “마스터” 엔티티로 그룹화하고 각 열에서 랜덤 레코드 선택하기

이것은 가장 일반적으로 볼 수 있는 구현 방법입니다. 일부 사용 사례에 적합하며, 단일 출력 레코드를 위해 모든 고객 데이터 소스에서 데이터를 활용할 수 있습니다. 따라서 1개의 데이터 소스에서 속성이 누락되었지만 다른 데이터 소스에 해당 속성이 있는 경우(예: Shipment 데이터 소스에 전화 번호가 누락되어 있지만 주문 데이터 소스에 전화 번호가 있는 경우), 데이터 소스 간에 공란을 채워 더 완전한 그림을 얻을 수 있습니다. 하지만 이는 각 열에 대해 가장 최근 레코드 업데이트를 선택하는 것은 아니므로 아주 강력한 구현은 아닙니다. 단지 각 열의 non-null 값을 선택하기 위해 MAX/MIN과 같은 단순한 집계 함수를 사용하는 것뿐입니다. 이 구현은 다음과 같습니다:

```js
SELECT
  master_customer_id,
  MAX(customer_name) AS customer_name,
  MAX(state) AS state,
  MAX(city) AS city,
  MAX(postcode) AS postcode,
  MAX(street) AS street,
  MAX(`number`) AS number,
  MAX(ship_to_address) AS ship_to_address,
  MAX(ssn) AS ssn,
  MAX(phone_number) AS phone_number
FROM raw_customers
GROUP BY master_customer_id;
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

이렇게 하시면 좋습니다! 보다 복잡한 데이터 세트를 갖게 되었지만, 여전히 한 가지 문제가 남아 있습니다: 알파벳 순서 외에는 이 쿼리가 어떤 레코드를 선택하는지에 대한 제어권이 없습니다. 고객에 대한 통합된 뷰는 낡았거나 관련성이 낮은 데이터를 포함할 수 있습니다.

3 — MAX_BY를 사용하여 제어 및 목적으로 선택된 레코드를 정의

이것은 의도적으로 고객(또는 다른 레코드)에 대한 뷰를 만드는 가장 좋은 방법입니다. 우리는 각 열에 대해 가장 최근의 NOT NULL 레코드를 선택하기 위해 MAX_BY 함수와 선택적인 FILTER 절을 사용할 것입니다. 이 방법으로, 뷰는 데이터 소스와 상관없이 각 열별로 가장 최근의 NOT NULL 레코드를 선택하여 사용할 수 있게 됩니다. 그 방법은 다음과 같습니다:

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
-- "final" 정리된 마스터 뷰에 나쁜 데이터가 들어가지 않도록 필터링 규칙 추가
SELECT
  master_customer_id,
  MAX_BY(customer_name, update_timestamp) FILTER (WHERE customer_name IS NOT NULL) AS customer_name,
  MAX_BY(state, update_timestamp) FILTER (WHERE state IS NOT NULL) AS state,
  MAX_BY(city, update_timestamp) FILTER (WHERE city IS NOT NULL) AS city,
  MAX_BY(postcode, update_timestamp) FILTER (WHERE postcode IS NOT NULL) AS postcode,
  MAX_BY(street, update_timestamp) FILTER (WHERE street IS NOT NULL) AS street,
  MAX_BY(`number`, update_timestamp) FILTER (WHERE `number` IS NOT NULL) AS number,
  MAX_BY(ship_to_address, update_timestamp) FILTER (WHERE ship_to_address IS NOT NULL) AS ship_to_address,

-- 예시의 형식을 확인함
  MAX_BY(ssn, update_timestamp)
      FILTER (WHERE ssn IS NOT NULL
              AND length(regexp_replace(ssn, '\\D','')) = 9 ) AS ssn,
  MAX_BY(phone_number, update_timestamp)
      FILTER (WHERE phone_number IS NOT NULL
                AND length(regexp_replace(phone_number, '\\D','')) = 10) AS phone_number
FROM raw_customers
GROUP BY master_customer_id;
```

이 코드는 더 견고하며 각 열의 가장 최근 레코드 값을 가져올 뿐만 아니라 선택된 레코드의 품질을 제어하기 위해 선택적인 FILTER 절을 추가할 수 있게 해줍니다. 위 예시에서는 각 열의 가장 최근 레코드를 선택합니다. 또한, 더 중요한 열에 대해 레코드를 선택하기 전에 품질을 확인하는 더 많은 규칙을 추가할 수 있습니다. 위 예시에서는 null이 아닌 가장 최근의 전화번호와 소셜 넘버를 선택합니다. 그리고 이는 데이터 자체의 컨텍스트에 따라 유효한 값을 가지는 레코드만 선택합니다. 이것은 Entity의 단일 뷰를 만드는 가장 견고한 방법입니다.

여기까지입니다! MAX_BY는 흔히 보기 어렵지만, 아마도 이것이 데이터 모델을 효과적이고 창의적으로 구축하는 데 도움이 될 것입니다.

노트북 예시의 전체 코드는 여기서 확인하실 수 있습니다.
