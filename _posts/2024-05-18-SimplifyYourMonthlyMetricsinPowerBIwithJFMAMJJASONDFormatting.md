---
title: "파워 BI에서 JFMAMJJASOND 형식으로 매월 메트릭을 간편하게 만들어보세요"
description: ""
coverImage: "/assets/img/2024-05-18-SimplifyYourMonthlyMetricsinPowerBIwithJFMAMJJASONDFormatting_0.png"
date: 2024-05-18 17:53
ogImage:
  url: /assets/img/2024-05-18-SimplifyYourMonthlyMetricsinPowerBIwithJFMAMJJASONDFormatting_0.png
tag: Tech
originalTitle: "Simplify Your Monthly Metrics in Power BI with JFMAMJJASOND Formatting"
link: "https://medium.com/microsoft-power-bi/simplify-your-monthly-metrics-in-power-bi-with-jfmamjjasond-formatting-c541072f840e"
---

## 이 문서는 월별 메트릭 보고서를 시각적으로 매력적으로 만드는 방법을 안내합니다. 이를 위해 월 이름을 약어로 변환하여 더 깔끔한 모양을 만들어봅시다.

## Metrics vs. KPIs

![이미지](/assets/img/2024-05-18-SimplifyYourMonthlyMetricsinPowerBIwithJFMAMJJASONDFormatting_0.png)

메트릭이란 무엇인가요?

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

메트릭(Metric)은 특정 활동을 측정함으로써 비즈니스 성과를 추적하는 방법입니다. 목표에 얼마나 가까운지를 보여주는 점수표와 같은 역할을 합니다. 전닻 활동에서 다운로드 횟수부터 새로운 리드가 영업으로 이어지는 횟수까지 거의 모든 것을 측정할 수 있습니다.

KPI란 무엇인가요?

KPI(Key Performance Indicator)는 메트릭과 유사하게 측정 가능한 값에 관련된 지표입니다. 그러나 KPI는 한 발짝 더 나아갑니다. 이러한 측정을 비즈니스의 특정 전략적 목표와 연결합니다. KPI는 메트릭이 이러한 대표적 목표 달성을 향해 어떻게 추적되는지 알려주는 기준으로 생각할 수 있습니다. 예를 들어, KPI는 시장 점유율을 어떤 비율로 증가시키는 것일 수 있고, 추적하는 메트릭(예: 영업 수치)이 목표에 부합하는지 여부를 보여줍니다.

이들은 어떻게 관련되어 있나요?

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

피라미드를 상상해보세요. 피라미드의 바닥은 메트릭으로 이루어져 있습니다. 이는 추적하는 모든 개별 측정값입니다. 이러한 메트릭은 그 다음으로 가는 피라미드 위쪽의 구성 요소인 KPI로 전달됩니다. KPI는 그러한 메트릭을 가져와 구체적인 목표에 연결합니다. 마지막으로, 피라미드의 꼭대기에는 전반적인 비즈니스 목표가 있습니다. 따라서 메트릭은 기초를 제공하고, KPI는 그러한 측정값에 목적을 부여하며, 둘 다 함께 작동하여 큰 그림 목표를 달성하는 데 도움이 됩니다.

참고:

이 문서는 월간 메트릭을 JFMAMJJASOND 약어를 사용하여 간단하고 깔끔한 형식으로 쉽게 변환하는 방법을 안내합니다. 4가지 쉬운 단계로 나눠 설명할 것입니다.

![이미지](/assets/img/2024-05-18-SimplifyYourMonthlyMetricsinPowerBIwithJFMAMJJASONDFormatting_1.png)

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

단계 1: 월 번호 추출하기

테이블에 새 열을 추가하고 다음 공식을 입력하여 날짜 열에서 숫자 월 값을 추출하십시오. 월 약자를 나중에 날짜순으로 정렬할 때 이 월 번호가 유용합니다:

```js
MonthNumber = MONTH("Table1"[YourDateColumn]);
```

단계 2: 월 이름 추출하기

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

날짜 열에서 전체 월 이름을 추출하세요. 나중에 이러한 이름을 해당하는 JFMAMJJASOND 약어로 변환할 것입니다. 이를 위해 다른 계산 열을 추가하고 다음 수식을 사용하세요:

```js
MonthNameLong = FORMAT("Table1"[YourDateColumn], "MMMM");
```

단계 3: 월 이름을 약어로 변환

전체 월 이름을 JFMAMJJASOND 약어로 변환하고 정렬 문제를 피하기 위해 세 번째 계산 열을 만드세요. 요렇게 합니다: 중복 약어가 있는 월에 대해 첫 글자 뒤에 공백을 추가해야 합니다 (예: 'January'는 'J', 'June'는 'J ' (한 칸 띄우기), 'July'는 'J ' (두 칸 띄우기)로 변환). 이렇게 하면 정확한 정렬을 보장할 수 있습니다.

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

다음 SWITCH 문은 이 작업을 수행합니다:

```js
MonthAbbr = SWITCH(
  Table1[MonthNameLong],
  "January",
  "J",
  "February",
  "F",
  "March",
  "M",
  "April",
  "A",
  "May",
  "M ",
  "June",
  "J ",
  "July",
  "J  ",
  "August",
  "A ",
  "September",
  "S",
  "October",
  "O",
  "November",
  "N",
  "December",
  "D"
);
```

단계 4: 월 약어를 월 번호로 정렬

마지막 단계에서는 'MonthNumber' 열에 따라 'MonthAbbr' 열을 정렬하여 월이 원하는 JFMAMJJASOND 순서로 표시되도록합니다. Power BI의 'Sort By Column' 기능을 사용하여 이 작업을 쉽게 수행할 수 있습니다.

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

![image1](/assets/img/2024-05-18-SimplifyYourMonthlyMetricsinPowerBIwithJFMAMJJASONDFormatting_2.png)

Now, simply replace your current month field with the newly created ‘MonthAbbr’ column, and your metrics will have a cleaner, more streamlined look. You’re done!

![image2](/assets/img/2024-05-18-SimplifyYourMonthlyMetricsinPowerBIwithJFMAMJJASONDFormatting_3.png)

![image3](/assets/img/2024-05-18-SimplifyYourMonthlyMetricsinPowerBIwithJFMAMJJASONDFormatting_4.png)

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

이 접근법을 dimDate 테이블에도 적용할 수 있습니다. dimDate 테이블을 만드는 데 관련된 자세한 정보는 이 문서를 참고하실 수 있습니다:

독자 여러분의 관심을 가져 주셔서 감사합니다! 이 안내서가 유익하게 느껴졌으면 좋겠습니다. 궁금한 점이나 문제가 발생하면 언제든지 연락 주세요.

참고로, .pbix 파일은 여기서 다운로드할 수 있습니다.
