---
title: "2024년에 알아야 할 시간을 절약하는 SQL 키워드 하나"
description: ""
coverImage: "/assets/img/2024-05-17-OneTime-SavingSQLKeywordYouNeedtoKnowin2024_0.png"
date: 2024-05-17 19:11
ogImage: 
  url: /assets/img/2024-05-17-OneTime-SavingSQLKeywordYouNeedtoKnowin2024_0.png
tag: Tech
originalTitle: "One Time-Saving SQL Keyword You Need to Know in 2024"
link: "https://medium.com/learning-sql/one-time-saving-sql-keyword-you-need-to-know-in-2024-28eeb4cc472d"
---


## SQL

![Image 1](/assets/img/2024-05-17-OneTime-SavingSQLKeywordYouNeedtoKnowin2024_0.png)

![Image 2](/assets/img/2024-05-17-OneTime-SavingSQLKeywordYouNeedtoKnowin2024_1.png)

'GROUP BY... WITH ROLLUP'란 무엇인가요? — 예제와 함께 설명합니다!

<div class="content-ad"></div>

SQL, 여러분들도 잘 아시다시피, 관계형 데이터베이스와 상호 작용하기 위해 사용하는 언어입니다. 필요한 데이터를 추출하고 변환하는 데 사용됩니다.

데이터 변환에 관해서 얘기할 때, 데이터 집계는 가장 중요한 개념 중 하나입니다. 데이터 집계를 생각해 볼 때 GROUP BY를 사용하지 않을 수 없습니다.

그러므로 GROUP BY는 중요한 것뿐만 아니라, 대규모 데이터 세트에서 의미 있는 것을 만들고 데이터로부터 가치 있는 통찰을 얻기 위해 필수적입니다.

지난 번 글 중 하나에서 이미 GROUP BY를 쉽게 사용하고 얼마든지 숙달시킬 수 있는 방법을 설명했습니다.

<div class="content-ad"></div>

읽어 주셨으면 좋겠어요!

지금 이 글에서는 한 발자국 더 나아가서 GROUP BY에서 추가 키워드인 WITH ROLLUP을 사용하여 조금 더 시간을 절약하는 방법을 배울 거에요.

WITH ROLLUP을 사용하면 하나의 쿼리로 집계 및 초계수준의 데이터 통찰력을 얻을 수 있어요.

무슨 뜻인지 보여 드릴게요.

<div class="content-ad"></div>

아래와 같이 간단한 9999 x 11 데이터셋이 있습니다.

![Dataset](/assets/img/2024-05-17-OneTime-SavingSQLKeywordYouNeedtoKnowin2024_2.png)

이 데이터셋에서 각 제품 카테고리별로 각 판매 관리자가 각 달에 선적한 총 수량을 파악하고 싶다고 가정해 봅시다.

이는 일반적인 GROUP BY와 집계 함수인 SUM을 사용한 전형적인 예시입니다.

<div class="content-ad"></div>

```sql
SELECT sales_manager
        , product_category
        , EXTRACT(MONTH FROM order_date) AS month_name
        , SUM(quantity) AS total_quantity -- Get total number of products shipped
FROM alldata.salesdata
WHERE 1=1
AND sales_manager IN ("Pablo", "Sofia")
AND product_category IN ("Healthcare", "Fashion")
GROUP BY sales_manager
        , product_category
        , month_name;
```

데이터셋이 크기 때문에 결과를 간단하게 유지하기 위해 'Healthcare' 및 'Fashion' 제품 카테고리 및 'Pablo' 및 'Sofia'를 판매 관리자로 선택했습니다.

<img src="/assets/img/2024-05-17-OneTime-SavingSQLKeywordYouNeedtoKnowin2024_3.png" />

그러나 이 출력을 받은 후 이 두 제품 카테고리에서 'Sofia'나 'Pablo'가 모든 달에 얼마나 많은 제품을 배송했는지 정확히 알고 싶어질 것입니다.```

<div class="content-ad"></div>

한 가지 방법은 상기 결과로부터 total_quantity 열을 수동으로 합산하는 방법이 있습니다. 이 방법은 다소 지루할 수 있습니다.

그렇지만, 만약 계산기를 사용하거나 수동으로 작업해야 한다면 SQL 데이터 변환의 의미가 무엇인가요?

그래서 또 다른 방법으로는 SQL을 통해 sales_manager와 product_category만으로 행을 그룹화하는 또 다른 SQL 쿼리를 작성하는 것입니다.

```sql
SELECT sales_manager
        , product_category
        -- , EXTRACT(MONTH FROM order_date) AS month_name
        , SUM(quantity) AS total_quantity -- 물품 출하량의 총 개수 가져오기
FROM alldata.salesdata
WHERE 1=1
AND sales_manager IN ("Pablo", "Sofia")
AND product_category IN ("Healthcare", "Fashion")
GROUP BY sales_manager
        , product_category;
```

<div class="content-ad"></div>

```markdown
![OneTime-SavingSQLKeywordYouNeedtoKnowin2024](/assets/img/2024-05-17-OneTime-SavingSQLKeywordYouNeedtoKnowin2024_4.png)

여기에서 원하는 것을 얻을 수 있지만, 여전히 당신이나 동료들이 생각할 문제가 있습니다: '소피아'나 '파블로'가 총으로 몇 개의 제품을 배송했는지 정확히 알고 싶다면?

그에 대답하기 위해, 계산기를 사용하거나 다음과 같은 다른 SQL 문을 작성할 수 있습니다:

```js
SELECT sales_manager
        -- , product_category
        -- , EXTRACT(MONTH FROM order_date) AS month_name
        , SUM(quantity) AS total_quantity -- 전체 제품 수를 얻습니다
FROM alldata.salesdata
WHERE 1=1
AND sales_manager IN ("Pablo", "Sofia")
AND product_category IN ("Healthcare", "Fashion")
GROUP BY sales_manager;
```

<div class="content-ad"></div>

```md
![SQL Keyword](/assets/img/2024-05-17-OneTime-SavingSQLKeywordYouNeedtoKnowin2024_5.png)

많은 노력이 필요했죠, 맞죠?

그렇죠! 이 키워드 — WITH ROLLUP으로 가능합니다. GROUP BY 절에 간단히 추가하세요.

```js
SELECT sales_manager
        , product_category
        , EXTRACT(MONTH FROM order_date) AS month_name
        , SUM(quantity) AS total_quantity -- 운송된 제품의 총 수량 획득
FROM alldata.salesdata
WHERE 1=1
AND sales_manager IN ("Pablo", "Sofia")
AND product_category IN ("Healthcare", "Fashion")
GROUP BY sales_manager
        , product_category
        , month_name WITH ROLLUP;
``` 
```

<div class="content-ad"></div>

![image](/assets/img/2024-05-17-OneTime-SavingSQLKeywordYouNeedtoKnowin2024_6.png)

와~ 빠르네요!

내 컴퓨터에서는 WITH ROLLUP이 없는 쿼리와 실행하는 데 걸리는 시간이 동일했어요.

위 출력에서 빨간색으로 강조된 행은 2번 질문에 대한 답을 제공합니다: 이 두 제품 카테고리 전체 월에 ‘Sofia’ 또는 ‘Pablo’가 보낸 제품 수는 얼마나 될까요? 파란색으로 강조된 행은 3번 질문에 답하는 데 도움이 됩니다: ‘Sofia’ 또는 ‘Pablo’가 총 보낸 제품 수는 얼마일까요?

<div class="content-ad"></div>

지난 행은 아직 물어보지 않았지만 답변하는 질문을 다루고 있어요: ‘Pablo’와 ‘Sofia’가 이 두 제품 카테고리에서 함께 몇 개의 제품을 발송했는지요?

보시다시피, WITH ROLLUP은 더 높은 수준의 집계를 얻도록 도와줍니다. 이는 여러 쿼리를 작성할 때 시간을 절약할 뿐만 아니라 여러 수준의 집계에 기반한 질문에도 단일 쿼리로 답변할 수 있도록 도와줍니다.

WITH ROLLUP은 데이터의 2가지 보기를 옆에 나란히 제공하는데, 한 가지는 데이터를 여러 카테고리로 집계하고 다른 하나는 전반적인 그림을 보여줍니다.

이러한 종류의 집계는 특히 데이터를 이해하고 그 안에 있는 트렌드와 패턴을 식별하는 데 도움이 될 수 있어요.

<div class="content-ad"></div>

여기까지...

이야기를 좋아해주셨으면 좋겠고 유용하게 느꼈으며 빠르게 끝내셨으면 좋겠어요.

GROUP BY 절에 WITH ROLLUP을 추가하면 하나의 SQL 쿼리로 상세한 통찰과 큰 그림을 함께 얻을 수 있습니다. 이를 사용하여 데이터의 숨겨진 트렌드를 탐색하고 최종적으로 정보 기반 결정을 내릴 수 있습니다.

유용하게 느껴진 부분 강조하고 생각에 대해 의견을 나눠주세요.

<div class="content-ad"></div>

더 많은 전문가 팁과 노하우를 위해 팔로우해주세요. 이 글을 꼭 여러분의 네트워크와 공유해주세요!

읽어주셔서 감사합니다.