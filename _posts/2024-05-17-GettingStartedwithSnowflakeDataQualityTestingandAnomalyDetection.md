---
title: "스노우플레이크 데이터 품질 테스트 및 이상 검출 시작하기"
description: ""
coverImage: "/assets/img/2024-05-17-GettingStartedwithSnowflakeDataQualityTestingandAnomalyDetection_0.png"
date: 2024-05-17 19:08
ogImage:
  url: /assets/img/2024-05-17-GettingStartedwithSnowflakeDataQualityTestingandAnomalyDetection_0.png
tag: Tech
originalTitle: "Getting Started with Snowflake Data Quality Testing and Anomaly Detection"
link: "https://medium.com/stackademic/getting-started-with-snowflake-data-quality-testing-and-anomaly-detection-8dfb4e325961"
---

Snowflake에서 데이터 품질을 개선하는 데 제어 평면을 활용하는 방법에 관심이 있으신가요? 여기서 Orchestra로 시작해보세요 ❄️

# 소개

Generative AI를 효과적으로 활용하려는 모든 기업의 기본 사전 조건은 견고한 데이터 인프라를 확보하는 것입니다.

견고한 데이터 인프라가 없으면 AI에서 가치를 유지하는 것은 불가능합니다. 망가진 대시보드가 영업 실행 책임자를 실망시키는 것만큼, 환각이나 잘못된 정보를 제공하는 챗봇은 어떤 AI 전략의 야망을 소각시킬 수 있습니다.

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

이번 튜토리얼에서는 스노우플레이크(Snowflake)의 데이터 파이프라인을 기존 도구 및 Orchestra를 활용하여 조정하는 방법과 데이터 품질을 모니터링하는 방법을 살펴볼 것입니다. 다양한 데이터 품질 테스트 유형을 다루며 "데이터 품질이란 무엇인가?" 라는 질문에 답할 것입니다.

이 튜토리얼을 마치면 Orchestra와 기존 도구를 활용하여 스노우플레이크에서 데이터 품질을 모니터링하고 유지하는 경제적인 방법이 갖춰집니다. 더불어, AI를 준비하는 데 한 걸음 더 나아가게 될 것입니다. 함께 시작해봅시다.

# 정의

먼저, 데이터 품질이 무엇인지 이해하고 어떻게 향상시킬 수 있는지 알아보는 것이 중요합니다.

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

이것은 널리 인기가 있는 내용입니다 — 예를 들어, 지난 1시간 동안 주문이 없었지만 저는 방금 주문을 넣었을 때, 이겪이 거짓이라는 것을 알 수 있습니다. 중복 주문이 있다면, 이 역시 현실을 반영하지 못하는 것입니다.

특정 시점의 데이터셋이 현실을 정확하게 대변하지 못하는 다양한 방법이 있습니다:

- 최신성 — 데이터셋의 SLA에 따라 최신 데이터가 없음
- 완전성 — 데이터셋에 관측치가 누락됨
- 중복 — 데이터셋에 관측치가 너무 많음
- 부정확성 — 데이터의 값이 부정확하게 기재됨
- 스키마 이탈 — 데이터가 기대하는 스키마와 일치하지 않음

두 번째로, 데이터 품질 테스트의 다양한 유형을 이해하는 것이 도움이 됩니다. 실무에서 테스트는 단순히 데이터가 데이터 품질 기준을 충족하지 못하면 실패하고(통과하지 못한다면), 그렇지 않으면 통과합니다. 데이터 품질 테스트에는 두 가지 유형이 있습니다.

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

데이터 품질 테스트 차단하기

데이터 품질 테스트 비차단하기

최소한의 기능을 갖춘 데이터 품질

좋아요! 그렇게 하면 이제 간단한 파이프라인을 검토하고 Snowflake를 위한 데이터 품질 테스트를 활성화하는 방법을 살펴볼 준비가 되었습니다.

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

# dbt 또는 Coalesce를 사용한 데이터 품질 테스트

간단한 데이터 파이프라인에서 데이터는 Saslesforce나 Postgres와 같은 소스에서 Snowflake로 이동합니다. 때로는 S3를 경유할 수도 있습니다.

우선, Snowflake로 데이터를 이동하는 프로세스에 대한 로깅 또는 알림을 설정해야 합니다. 이렇게 하면 데이터 팀이 이 프로세스가 실패하는 것을 가장 먼저 알 수 있습니다. 직접적인 데이터 품질 테스트는 아니지만, 데이터가 SLA 내에 있는지의 최근성이 데이터 품질에 영향을 줄 수 있으므로 이를 설정해두는 것이 좋습니다.

두 번째는 소스 및 집계 테스트를 수행하는 것입니다. 이 방식은 신뢰할 수 있는 데이터 제품을 위한 최소한의 테스트를 실행하며, 중간 테이블에 대한 테스트는 무시하고 소스 및 집계 테이블을 집중적으로 테스트하는 것을 포함합니다.

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

dbt를 사용하여 dbt 테스트로 실행하거나 Coalesce에서 테스트로 설정할 수 있습니다.

Orchestra에서는 추가 조치가 필요하지 않습니다. Orchestra는 dbt/Coalesce를 트리거하고 모니터링하며 UI에서 테스트 작업을 렌더링합니다. 이렇게 하면 기존 도구를 사용하여 데이터 품질을 모니터링하면서 end-to-end 데이터 파이프라인에 대한 알림을 통합할 수 있습니다.

참고 - 이러한 것들은 블로킹 테스트입니다. 품질 테스트가 실패하면 파이프라인도 실패합니다. 여기에 일반적인 예제 몇 가지를 포함했습니다.

## 데이터 품질 테스트 스트림

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

스트림에서 데이터 품질 테스트를 쉽게 실행할 수 있습니다. "Run as Data Quality Test" 설정을 활성화하고 쿼리를 활용하면 Orchestra가 이 작업을 처리해줍니다. 스트림에서 테이블로 데이터를 로드하기 전에 필요하다면 임시 테이블을 만들어 이 데이터에 대한 데이터 품질 테스트를 실행할 수 있습니다.

![이미지](/assets/img/2024-05-17-GettingStartedwithSnowflakeDataQualityTestingandAnomalyDetection_0.png)

## 데이터 분석가를 위한 비차단형 데이터 품질 테스트

데이터 제품 소유자는 데이터셋의 품질을 시간이 지남에 따라 모니터링하기 위해, 데이터가 현실을 정확히 반영하는지 여부 이외에도 데이터셋에 대해 더 많이 이해하고 싶어할 수 있습니다.

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

예를 들어, 데이터는 현실을 (Salesforce) 대변할 수 있지만, 그것이 사람들이 보고 싶어하는 것을 반영하지 않을 수도 있습니다. 이는 우리 모두가 공감할 수 있는 예시입니다 — 영업 직원들은 관리 업무를 싫어합니다!

그러므로 데이터 분석가는 이 사실에 주의를 기울일 수도 있습니다. 예를 들어, 연락 주인이 시간 경과에 따라 생성한 연락처 수를 이해하는 것이 도움이 될 수 있습니다. 중요한 변동이 있는 곳에는 엉뚱한 영업사원의 존재가 크게 나타날 것입니다 — 이는 그들의 데이터 청결에 대한 헌신을 우연히 포착하며, 비록 말하자면 이후에야 발견되는 것입니다.

다른 예로는 리드 소스를 살펴볼 수 있습니다 — 예를 들어, 널 값의 수나 리드 소스의 중요한 변동 수를 모니터링할 수 있습니다 (변하지 않아야 할 것). 널 값의 비율이 높다고 해서 차단 테스트가 되어서는 안 됩니다 — 영업부장은 보고서를 필요로 하지만, 널 값을 확인하고 이를 강조하는 것은 반드시 필요합니다.

Orchestra에서는 관련 데이터 품질 테스트를 수행하는 별도의 파이프라인을 생성하고 해당 파이프라인을 동일한 데이터 제품에 추가함으로써 이를 수행할 수 있습니다. 이렇게 하면 데이터 품질 테스트 파이프라인이 필요할 때마다 항상 실행되며, 분석가들은 시간에 따른 데이터 품질 결과를 가질 수 있지만, 추가적인 코드나 조직적인 허들이 필요하지 않으며, 비싼 데이터 관측 도구도 필요하지 않습니다.

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

# 생성 AI와 기계 학습을 활용한 이상 탐지

많은 관측 도구는 플랫폼의 주요한 독특한 세일즈 포인트로서 이상을 탐지하는 능력을 강조합니다. 사실, Anomalo와 같은 소프트웨어 회사들은 특히 이를 위해 설계된 것처럼 보입니다.

만약 당신이 Snowflake의 고객이라면 이러한 도구를 구매하는 것은 심각한 실수일 수 있습니다. 이상 탐지 모니터를 직접 작성하는 것이 번거로울 것으로 생각할 수 있지만, 그렇게 어렵지 않으며 할 수 있습니다. 그러나 실제 문제는 Snowflake 외부에서 이상 탐지 모니터를 실행하면서 발생하는 추가 비용입니다.

이상 탐지 메커니즘은 Snowflake로부터 데이터를 가져 와야 하며(전송), 모델을 학습해야 합니다(연산), 다시 Snowflake로부터 데이터를 가져와야 하며(전송), 그리고 예측 서비스를 제공해야 합니다.

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

따라서, 제3자 이상 탐지 도구는 추가적인 Snowflake 비용이 들어갑니다 (오케스트레이션에 대한 생각을 하지 않았을 때입니다).

걱정하지 마세요! Snowflake는 구글의 행적(Google’s stead)을 따라 (Vertex AI와 함께) 개발하여 자체적으로 Cortex AI를 개발했습니다.

여기에 Snowflake에서 Cortex AI를 활용하는 방법이 있습니다.

## Cortex AI를 사용한 Snowflake의 이상 감지

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

먼저, 이상 탐지가 무엇이며 어떻게 작동하는지 이해하는 것이 중요합니다.

이상은 관찰과 관련이 있습니다. 예를 들어, 체중이 기재된 100명의 명부가 있다면, 분포의 꼬리에 위치한 사람을 “이상” 또는 “이상점”으로 쉽게 식별할 수 있습니다.

![이미지](/assets/img/2024-05-17-GettingStartedwithSnowflakeDataQualityTestingandAnomalyDetection_1.png)

이는 Snowflake에서 간단하게 수행할 수 있습니다 — SQL 쿼리를 작성하여 백분위수를 계산하고 해당 값을 초과하는 것을 쉽게 가져올 수 있습니다.

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
-- `your_table`에서 `value` 열의 90 백분위수 계산
SELECT PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY value) AS percentile_90
FROM your_table;
```

그러나 시계열 데이터의 경우 조금 더 복잡해질 수 있습니다. 이는 두 가지 요인 때문에 발생합니다.

- 다변량 시계열 데이터: 여러 기계의 시간에 따른 출력 같은 여러 시계열을 동시에 테스트하고 싶어할 수 있습니다.
- 계절성: 모든 시계열 추세가 선형적이지는 않습니다.

따라서 일반적으로 이상점 테스트 메커니즘은 먼저 데이터를 특징별로 분할한 후 (예: 이 예제에서는 기계로), "적합"할 과거(임의의) 데이터 하위 집합을 선택합니다. 일반적으로 이러한 곡선은 자기회귀 이동 평균(ARIMA) 또는 계절성 자기회귀 이동 평균(SARIMA)과 같은 모델을 사용하여 보정됩니다.

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

남은 데이터 포인트는 예측됩니다. 합당한 한계를 벗어나는 것들은 (사용자가 결정할) 이상으로 간주됩니다.

따라서, Snowflake에서 시계열을 위한 이상 감지를 활용하기 위해 다음과 같은 입력이 필요합니다.

- 훈련 데이터셋
- 다변량 특성
- 시간 열
- 패턴 (선형 시간 추세인가요? 계절적인가요? 다른 것인가요?)
- 몇 퍼센트가 이상으로 간주될까요?

이 정보를 갖고 있으면, 준비된 것입니다.

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

## Snowflake에서 데이터 품질 테스트 및 이상 감지 구현하기

저희는 Snowflake에서 이상 감지를 실행하는 방법을 보여주는 간단하고 유익한 비디오를 제작했습니다.

Orchestra에 이상 감지를 추가하는 것도 간단합니다:

이 비디오들에서 Snowflake의 강력함을 보실 수 있습니다. 그러나 이러한 강력한 기능들을 스키마 테스트 및 이상 감지에 통합하는 것은 엔드 투 엔드 파이프라인을 구축할 때 비 쉽습니다.

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

오케스트라가 등장하는 곳입니다. 오케스트라의 Snowflake 특화 작업을 통해 누구나 손쉽게 Snowflake에서 데이터 파이프라인을 구축할 수 있으며, 데이터 품질 및 이상 징후 테스트를 위해 Cortex ML Functions을 활용할 수 있습니다.

# 결론

잘 했어요! 여기까지 오신 여러분은 이제:

- Snowflake에서 dbt 테스트 및 Coalesce 테스트를 활용하여 엄격한 소스 테스트를 구현하는 방법을 알게 되었습니다.
- Snowflake Streams와 Tasks에 효율적으로 데이터 품질 테스트를 수행하는 방법을 알게 되었습니다.
- 데이터 세트에 대한 이상 징후 테스트를 할 수 있습니다.
- 블로킹되지 않는 데이터 품질 테스트를 실행할 수 있습니다.
- 조직 내에서 모든 것을 모니터링하고 비즈니스에 작업 내용을 자랑스러이 보여줄 수 있는 강력한 UI가 오케스트라에 있다는 것을 알게 되었습니다.

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

시작 준비가 되셨나요? 여기로 이동해주세요.

이 튜토리얼을 즐겁게 즐겼으면 좋겠고, 별도의 도구가 필요하지 않다는 점이 잘 드러났으면 좋겠어요. 데이터 품질 테스트를 실행하고 모니터링할 것이 필요하지만, 변환 도구가 이미 있을 것입니다.

Orchestra와 같은 통합 제어 패널이 없다면 — 어쩌면 한 번 고려해 볼 필요가 있을지도 모릅니다. Orchestration과 Observability를 통합하는 여러 이점이 있지만, 그것은 나중에 다른 글에 포함시켜야 할 주제인 것 같아요.

행복한 테스트 진행되길 바래요! ⏰

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

## 추가 자료

더 많은 정보를 얻고 싶다면, 이 튜토리얼의 비디오 버전도 확인해보세요.

- Snowflake 데이터 품질 및 이상 감지 (파트 1) [링크]
- Snowflake 데이터 품질 및 이상 감지 (파트 2) [링크]

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

오케스트라를 사용하여 스노우플레이크에서 스키마 확인하십시오 (링크)

오케스트라에서 스노우플레이크 데이터 품질 테스트 추가하기 (링크)

# 스택데믹 🎓

끝까지 읽어주셔서 감사합니다. 떠나시기 전에:

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

- 이 글을 읽어주셔서 감사합니다! 👏
- 팔로우하고 작가를 응원해주세요!
- 저희를 팔로우해주세요: X | LinkedIn | YouTube | Discord
- 다른 플랫폼에서 저희를 만나보세요: In Plain English | CoFeed | Venture | Cubed
- 더 많은 콘텐츠는 Stackademic.com에서 확인할 수 있습니다.
