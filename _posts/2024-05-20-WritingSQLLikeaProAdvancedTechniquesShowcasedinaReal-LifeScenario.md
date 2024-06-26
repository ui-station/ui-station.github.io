---
title: "SQL 전문가처럼 쓰기 실생활 시나리오에서 소개되는 고급 기술"
description: ""
coverImage: "/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_0.png"
date: 2024-05-20 19:03
ogImage:
  url: /assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_0.png
tag: Tech
originalTitle: "Writing SQL Like a Pro: Advanced Techniques Showcased in a Real-Life Scenario"
link: "https://medium.com/learning-sql/writing-sql-like-a-pro-advanced-techniques-showcased-in-a-real-life-scenario-2811a848d34a"
---

## Google의 BigQuery를 사용하여 고급 SQL 기술을 활용하여 두 개의 히스토리 테이블을 병합하는 방법

![이미지1](/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_0.png)

![이미지2](/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_1.png)

지난 주, Analytics Engineer인 Arnaud Stephan이 고급 사용 사례에 대해 도움을 요청했는데, 그것은 매우 흥미로운 것으로 발견했습니다. 그의 도움을 받기 위해 몇 시간을 보낸 후, 블로그 글 주제로 적합하다고 생각했고, 이 고급 실제 문제를 공개적으로 논의하고 관련 코드를 게시할 수 있는지 허락을 요청했습니다.

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

자, 함께 Google BigQuery에서 고급 SQL 실전을 준비해 보시죠!

(참고: 개인 정보 보호를 위해 ID 및 값은 더미로 대체했습니다.)

저자 소개:

SQL 및 빅 데이터 분야에서 12년 이상 경력을 가진 프리랜서 분석 엔지니어 / 데이터 플랫폼 엔지니어입니다. 복잡한 SQL/DataFrame 주제를 다루는 것을 좋아하며, SQL로 풀지 못하는 어려운 문제에 막혔다면 언제든 도움을 요청해 주세요. 도와드릴 수도 있을 거예요.

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

# 문제

먼저 상황을 이해하기 위해 맥락을 제공해 드리겠습니다: 식료품 소매업체에서 다수의 상품을 다수의 가게에서 판매하고 있습니다. 각 가게 내의 각 상품에 대해 해당 상품의 가격 이력을 추적하고 있습니다. 이러한 데이터는 다음과 같이 표 형식으로 표시된 sell_price_history 테이블에 포함되어 있습니다:

![테이블 이미지](/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_2.png)

그러나 각 가게는 어떤 상품에 대해 프로모션을 생성할 수도 있습니다. 이 경우, 프로모션 값은 원래의 상품 가격을 대체합니다. promotion_history라는 두 번째 테이블에 해당 데이터가 포함되어 있습니다:

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

<img src="/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_3.png" />

그 다음 문제는 다음과 같습니다. 이벤트 테이블 두 개를 하나의 테이블로 병합하여 각 기사에 대한 가격 이력 및 프로모션 효과를 제공하는 단일 테이블을 얻고 싶습니다.

다시 말해, 우리가 원하는 기대 결과는 다음과 같아야 합니다:

<img src="/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_4.png" />

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

더 시각적인 설명을 위해, 두 개의 이벤트 범위를 설명하는 표를 병합하려고 합니다.

![table](/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_5.png)

# (잘못된) 해결책

조금 더 생동감 있게 만들기 위해 문제 해결 과정을 안내해 드리겠습니다. 각 표를 "이벤트 타임라인"의 형태로 분해하고 두 타임라인을 병합하여 공백을 채워야 한다는 생각이 들었습니다. 지금은 조금 흐릿해 보일 수 있지만, 문제 해결을 시작할 때도 그랬습니다. 걱정하지 마세요. 곧 더 명확해질 것입니다.

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

단계 1: 데이터 공유

문제를 해결하는 방법에 대해 생각하기 전에 나는 두 테이블을 결합하고 shop_id와 article_id로 데이터를 그룹화했습니다. 아래와 같이:

![image](/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_6.png)

BigQuery에서 중첩 구조에 익숙하지 않은 분들을 위해 처음에는 약간 어색할 수 있지만, 데이터 구조가 어떻게 구성되어 있는지에 대한 상세한 설명을 제공하겠습니다.

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

![SQL](/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_7.png)

이 방식을 선호하는 이유는 여러 가지입니다:

- 간단함: 각 기사의 데이터를 함께 두면 한 행을 기준으로 추론할 수 있습니다.
- 성능: 미리 그룹화하면 수행할 모든 계산이 행 수준에서 이루어집니다. MapReduce에 익숙한 사람들에게는 Map 단계만 있을 뿐이며 Reduce도 Shuffle도 없습니다. 일반적으로 가장 리소스 집약적인 부분인 Shuffle이 없습니다.
  BigQuery에서는 이것이 "핵심"은 아니지만, 쿼리 가격은 처리된 양이 아닌 읽은 데이터 양에만 기반합니다. 다른 엔진에서는 중요할 수 있으며, 저는 코드를 최적화하는 것을 좋아합니다.
- 저장 효율성: shop_id 및 article_id가 각 줄마다 반복되지 않으므로 이 표현은 동일한 양의 정보를 위해 더 적은 저장 공간을 차지합니다. 이러한 것은 이와 같은 장난감 데이터세트에서 큰 의미가 없지만, 예상을 얻기 위해 말씀드리면, 두 개의 입력 테이블의 총 크기는 720b이지만 (동일한 정보를 포함하는) 출력 테이블의 크기는 412b에 불과했습니다. 이를 수십억 행으로 곱하면 가격 차이를 알 수 있을 것입니다.

주요 단점은 대부분의 사람들이 이에 익숙하지 않아서 적절하게 쿼리하는 방법을 배우는 데 약간의 노력이 필요할 수 있으며, 많은 메타데이터 도구(데이터 카달로그, 데이터 차이 등)가 아직 중첩 구조를 잘 지원하지 않는다는 것입니다.

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

그만 이야기하고 넘어가볼게요. 그 주제만으로도 전체 기사를 쓸 가치가 있는 정도니까, 이제 작업으로 돌아갈게요.

단계 2: 두 개의 히스토리 병합하기

다음으로, 두 개의 히스토리 테이블을 다음과 같이 합칩니다:

![테이블 이미지](/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_8.png)

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

Step 3: 비어 있는 칸 채우기

마침내, 우리는 last_value 윈도우 함수를 사용하여 이벤트 주문에서 마지막 비어 있지 않은 값을 유지하고, 혹시 NULL을 채우는 데 사용합니다.

![image](/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_9.png)

이 결과는 우리가 찾고 있는 결과처럼 보이지만, 똑똑한 독자라면 이미 이것이 실제로 잘못되었다는 것을 발견했을 것입니다.

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

왜 이 작업이 작동하지 않는지:

프로모션들이 중단 없이 계속됐다면 이 작업이 잘 작동했을 겁니다. 그러나 우리의 입력 데이터를 더 자세히 살펴보면 이렇게 되지 않았음을 알 수 있습니다. 첫 번째와 두 번째 프로모션 사이에 중단이 있습니다. 이 중단은 이전 스크린샷에서는 보이지 않습니다. 파란색으로 강조된 줄에서 확인할 수 있습니다.

![Screenshot](/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_10.png)

# 올바른 해결책

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

이제, 이전 (잘못된) 솔루션이 어떻게 수정될 수 있는지 보겠습니다. 무엇이 일어나는지에 대한 더 자세한 설명이 포함되어 있습니다.

단계 1: 데이터 공동 배치 [수정할 내용 없음]

단계 1은 준비 단계였습니다. 그대로 유지할 수 있습니다.

단계 2: 날짜 범위를 타임라인으로 변환하고 병합하기

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

우리가 두 개의 타임라인을 병합하는 직감은 좋았지만, 날짜 범위가 연속되지 않을 수 있다는 사실을 놓치고 있었어요. 이 문제를 해결하기 위해 우리가 먼저 이룰 첫 번째 단계는 "시작일부터 종료일까지"의 날짜 범위로 이루어진 "히스토리"를 정보의 손실 없이 단순히 이벤트만 있는 이벤트 타임라인으로 변환하는 것이에요.

다음과 같이 스키매틱하게 보이겠죠:

![이미지](/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_11.png)

여기서 "누락된" 값들은 마법의 숫자인 -1로 표시되었어요. 이것은 3단계에서 윈도우 함수인 last_value(… ignore nulls)를 사용할 때 NULL과 같은 방식으로 처리하지 않기를 원하기 때문이에요. 아마도 이것은 코드 스멜일지 모르겠지만, 이 기사의 단순성을 위해 이대로 유지할 거에요.

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

이 작업이 완료되면 두 개의 타임라인을 다음과 같이 UNION과 함께 병합할 것입니다:

![image](/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_12.png)

그러면 코드는 어떻게 되는 걸까요? 일단 프로모션에만 초점을 맞춰 봅시다. 첫 번째 단계는 다음 이벤트의 "start_datetime"을 검색하는 것입니다. 이것은 불연속성이 언제 있는지 감지하는 데 도움이 될 것입니다:

```js
SELECT
  ...
  ARRAY(
    SELECT AS STRUCT
      *,
      LAG(start_datetime) OVER (ORDER BY start_datetime DESC) as next_event_datetime
    FROM UNNEST(promotion_history)
    ORDER BY start_datetime
  ) as promotion_history,
  ...
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

두 번째 단계는 이렇게 비연속성이 발생할 때마다 새 이벤트를 도입하는 것으로 구성되어 있어요:

```js
SELECT
  ...
  ARRAY (
    SELECT AS STRUCT
      promotion_value,
      start_datetime,
    FROM UNNEST(promotion_history)
    UNION ALL
    SELECT AS STRUCT
      -1 as promotion_value,
      end_datetime,
    FROM UNNEST(promotion_history)
    WHERE end_datetime < next_event_datetime OR next_event_datetime IS NULL
  ) as events
  ...
```

여일은 이전에 누락된 "종료 이벤트"를 다시 도입하는 부분입니다:

<img src="/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_13.png" />

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

모든 것을 다시 합치면 다음과 같은 쿼리가 나옵니다:

![Query](/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_14.png)

단계 3: 공백 채우기 + 매직 값 대체

이제 이 작업이 완료되었으므로, 단계 3에서 이전 쿼리를 사용하여 우리가 원하는 결과와 거의 비슷한 결과를 얻을 수 있습니다:

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

last_value(… ignore nulls)라는 창 함수를 사용하면 빈 칸을 채울 수 있고, lead라는 창 함수를 사용하여 end_dates를 검색하여 date ranges를 다시 형성할 수 있습니다.

이제 해야 할 일은 매직 값인 -1과 ""을 제거하기 위해 데이터를 정리하는 것 뿐입니다:

![이미지](/assets/img/2024-05-20-WritingSQLLikeaProAdvancedTechniquesShowcasedinaReal-LifeScenario_15.png)

price_with_promotion_included를 계산하는 최종 쿼리를 실행하고 결과를 평평하게 만들기 위해 마지막으로 unnest를 수행합니다(shop_id 및 article_id가 모든 행마다 반복되어 나타남).

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

그게 다야, 끝났어!

# 결론

우리는 우리가 원한 것을 4 또는 5 단계로 계산하는 데 성공했어요. 처음에는 간단해 보이지만 SQL로 하는 것이 꽤 까다로운 것 중 하나라고 생각해요. 이런 문제는 순수 SQL로 로직을 작성하는 것 대비 사용자 정의 함수(UDF)을 다른 언어(Python, Scala, Javascript 또는 무엇이든)로 작성하는 걸 고려해볼만한 고급 변환의 예시에요. UDF는 코드를 보다 일반적이고 안전하게 만든다는 장점이 있지만(단위 테스트로), 순수 SQL 대비 효율성이 떨어지며 더 비용이 많이 듭니다.

연습으로, 만약 실습을 원한다면, 이 연습을 다음 변형 중 하나로 다시 써보는 것을 추천해요:

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

- 이 값을 마술 값으로 사용하지 않고 다시 작성해보세요.
  (힌트: "ON" 또는 "OFF" 값을 취할 수 있는 event_status 열을 추가하십시오.)
- ARRAYS 또는 STRUCT를 사용하지 않고 다시 작성해보세요.
  (힌트: 이것은 충분히 가능하지만, 창은 추가적인 "PARTITION BY shop_id, article_id"를 필요로 할 수 있습니다.)
- 선호하는 dbt와 유사한 도구를 사용하여 다시 작성하십시오.
  (이 게시물을 대부분의 사람들이 이해할 수 있도록 dbt를 사용하지 않았습니다.)
- 완전히 일반적인 방식으로 작성하려고 해보세요: 쿼리는 입력 테이블의 열 수에 관계없이 작동해야 하며, 쿼리를 업데이트하지 않고 새로운 열을 입력 테이블에 추가해도 계속 작동해야 합니다.
  (힌트: 순수 SQL에서 이것이 가능한지 확신하지는 못하지만 제가 시도해본 결과 BigQuery는 NULL을 복잡한 유형으로 자동으로 캐스팅하지 못하는 것으로 나타났습니다. 그러나 bigquery-dataframes 또는 bigquery-frames로 이 작업을 수행하는 것은 멋진 도전이 될 수 있습니다.)
- 데이터프레임을 사용하면 최종적으로 여러 히스토리 테이블에서 자동으로 작동하는 완전히 일반적인 변환을 만드는 것도 가능할 것입니다.

마지막으로, 배열을 다루는 방법에 대해 더 알고 싶다면 BigQuery 문서의 멋진 페이지를 참고하고, 그 주제에 대해 더 많은 기사를 보고 싶다면 댓글을 남겨주시면 감사하겠습니다.

오늘은 여기까지입니다. 읽어 주셔서 감사합니다! 이 글을 쓰는 데 즐거움을 느끼신 만큼 즐겁게 읽으셨기를 바랍니다.

이 글에서 사용된 쿼리 전체(CTE로 다시 작성한 것)와 설명 그래프가 함께 제공됩니다. 코드 및 데이터 샘플은 여기에서 확인하실 수 있습니다. 즐겁게 ;-)

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

# 링크

이 기사에서 공유된 모든 링크가 여기 있습니다:

- python-bigquery-dataframes: BigQuery에서 실행되는 Python 기반 DataFrames의 Google 공식 프로젝트입니다.
- bigquery-frames: BigQuery에서 실행되는 Python 기반 DataFrames의 제 개인 프로젝트입니다.
- Google BigQuery 문서: 배열 작업.
- 해당 기사 코드가 있는 Git gist.
