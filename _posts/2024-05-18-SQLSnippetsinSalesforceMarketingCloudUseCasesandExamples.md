---
title: "세일즈포스 마케팅 클라우드에서 SQL 스니펫 활용하기 사용 사례 및 예시"
description: ""
coverImage: "/assets/img/2024-05-18-SQLSnippetsinSalesforceMarketingCloudUseCasesandExamples_0.png"
date: 2024-05-18 15:34
ogImage:
  url: /assets/img/2024-05-18-SQLSnippetsinSalesforceMarketingCloudUseCasesandExamples_0.png
tag: Tech
originalTitle: "SQL Snippets in Salesforce Marketing Cloud: Use Cases and Examples."
link: "https://medium.com/@rruchi49/sql-snippets-in-salesforce-marketing-cloud-use-cases-and-examples-c004583f553d"
---

![SQL Snippets in Salesforce Marketing Cloud](/assets/img/2024-05-18-SQLSnippetsinSalesforceMarketingCloudUseCasesandExamples_0.png)

Salesforce Marketing Cloud (SFMC)은 마케터가 고객 여정을 관리하고 최적화할 수 있는 강력한 플랫폼입니다. 그 중요한 도구 중 하나는 SQL인데, 이를 사용하여 SFMC의 데이터 익스텐션 내에서 데이터를 쿼리하고 조작할 수 있습니다. 이 블로그에서는 유용한 SQL 스니펫, 사용 사례 및 예제를 살펴보며 SFMC에서 SQL을 효과적으로 활용하는 데 도움이 될 것입니다.

# SFMC에서 SQL이란?

SQL(Structured Query Language)은 데이터베이스를 관리하고 조작하는 데 사용되는 표준 프로그래밍 언어입니다. SFMC에서 SQL을 사용하여 데이터 익스텐션에 저장된 데이터를 검색, 업데이트 및 관리하는 쿼리를 작성합니다. SFMC에서 SQL을 사용하면 개인화된 마케팅 캠페인에 중요한 복잡한 데이터 작업을 수행할 수 있습니다.

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

# SQL을 Salesforce Marketing Cloud(SFMC)에서 활용하는 사례

특정 기준을 기반으로 고객 세그먼트를 생성하는 것은 타겼팅 마케팅에 중요합니다. SQL을 사용하면 고객을 효율적으로 필터링하고 분류할 수 있습니다.

데이터 확장에서 발생하는 오류를 식별하고 수정하여 데이터의 정확성과 일관성을 유지하는 데 SQL을 사용하세요.

이메일 오픈 및 클릭과 같은 참여 데이터를 쿼리하여 마케팅 캠페인의 성능을 분석하세요.

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

특정 고객 데이터를 검색하여 이메일, SMS 및 기타 통신을 개인화하세요.

# SQL 조각과 예제

## 1. 최근 구매일을 기준으로 고객 세분화

사용 사례: 최근 30일간 구매를 한 고객 식별

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

## SQL Snippet:

```js
SELECT;
CustomerID, FirstName, LastName, EmailAddress, LastPurchaseDate;
FROM;
Customers;
WHERE;
LastPurchaseDate >= DATEADD(day, -30, GETDATE());
```

## 2. Data Cleansing – Removing Duplicates

Use Case: Remove duplicate email addresses from a data extension.

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

## SQL Snippet:

```js
SELECT
    EmailAddress,
    MIN(CustomerID) AS CustomerID,
    FirstName,
    LastName
FROM
    Customers
GROUP BY
    EmailAddress,
    FirstName,
    LastName
HAVING
    COUNT(*) = 1
```

## 3. 캠페인 성과 분석

사용 사례: 각 캠페인별 이메일 오픈 수를 검색합니다.

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

## SQL 코드 일부:

```js
SELECT
    CampaignName,
    COUNT(EmailOpen) AS OpenCount
FROM
    EmailTracking
WHERE
    EmailOpen = 1
GROUP BY
    CampaignName
```

## 4. 개인화 - 고객 선호도 조회

사용 사례: 맞춤 상품 추천을 위해 고객이 선호하는 제품 카테고리를 가져옵니다.

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

## SQL 코드 스니펫:

```js
SELECT
    CustomerID,
    FirstName,
    LastName,
    PreferredCategory
FROM
    CustomerPreferences
WHERE
    PreferredCategory IS NOT NULL
```

저를 따라와요:

Ruchika Sandolkar (함께 성장해요) 🫱🏻‍🫲🏽

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

우리의 WhatsApp 커뮤니티에 가입하셔서 Salesforce에 관한 매일 업데이트를 받아보세요.

![Salesforce](/assets/img/2024-05-18-SQLSnippetsinSalesforceMarketingCloudUseCasesandExamples_1.png)
