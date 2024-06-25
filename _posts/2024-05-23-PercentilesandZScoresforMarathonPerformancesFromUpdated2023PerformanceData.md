---
title: "2023년 업데이트된 성적 데이터로부터 마라톤 성적에 대한 백분위 및 Z 점수"
description: ""
coverImage: "/assets/img/2024-05-23-PercentilesandZScoresforMarathonPerformancesFromUpdated2023PerformanceData_0.png"
date: 2024-05-23 15:38
ogImage:
  url: /assets/img/2024-05-23-PercentilesandZScoresforMarathonPerformancesFromUpdated2023PerformanceData_0.png
tag: Tech
originalTitle: "Percentiles and Z Scores for Marathon Performances From Updated 2023 Performance Data"
link: "https://medium.com/runners-life/percentiles-and-z-scores-for-marathon-performances-from-updated-2023-performance-data-bdde85cc3c7c"
---

![image](/assets/img/2024-05-23-PercentilesandZScoresforMarathonPerformancesFromUpdated2023PerformanceData_0.png)

다른 연령대의 경주 결과를 공정하게 비교하는 방법이 무엇인가요?

그것이 저가 지난 몇 달 동안 연구해 온 질문입니다.

문제는 나이가 모두에게 온다는 것입니다. 어느 순간, 얼마나 열심히 훈련해도 우리는 속도를 줄이기 시작합니다. 시작과 속도는 각기 다르겠지만, 이는 불가피한 일이죠.

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

나이 등급의 장점은 다른 연령 그룹 간의 레이스 결과를 비교할 수 있는 방법을 제공하여 마스터 러너들이 경쟁력을 유지할 수 있는 기회를 제공한다는 것입니다.

하지만 이 약속을 지키는 것일까요?

다른 러너들로부터 불만을 들은 적이 있고, 몇 가지 결과를 살펴본 후에 나이 등급에는 몇 가지 결함이 있는 것으로 보입니다. 한 가지로는 일반 러너에 대한 많은 차별을 제공하지 않는다는 점이며, 다른 한 가지로는 충분히 보정되어 있지 않아 몇몇 연령 그룹에 이점을 줄 뿐만 아니라 다른 그룹에는 불이익을 주고 있다는 점입니다.

더 나은 방법이 있을까요?

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

저는 두 가지 대안을 테스트해 보았어요: Z 점수와 백분위수. 2010년부터 2019년까지의 대량의 데이터를 수집하여 이러한 방법을 시도했는데, 특히 백분위수에는 많은 가능성이 있다고 생각했어요.

오늘은 업데이트된 데이터셋으로 돌아와, 각 대안을 다시 살펴볼 거에요.

# 데이터 출처

우리가 작업하는 데이터는 무엇인가요?

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

2023년 미국에서 열린 모든 마라톤 결과를 수집했어요. 이 결과들은 Marathon Guide, Athlinks 및 각각의 레이스 웹사이트에서 스크랩한 개인 성적들로 이루어져 있어요.

이를 통해 약 40만 개인 완주 기록을 얻었어요 — 러너의 나이, 성별 및 완주 시간을 포함하고 있어요. 데이터를 정리하고 잘못된 정보가 있는 몇 개의 레코드를 제외하고 BAA의 자격 취득을 위해 사용되는 연령대로 정리했어요.

결과적으로, 609개의 개별 레이스에서 38만 8,560개의 완주 기록을 얻었어요.

이 데이터 세트에 대한 자세한 내용은 여기에서 확인할 수 있어요. 우리 목적에 맞게 말씀드리자면, 여성 75-79세 그룹은 상대적으로 작다는 점을 언급해드릴게요 (157개 완주). 80대의 러너들이 더 적었기 때문에 이 분석에 포함하지 않기로 결정했어요.

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

# 2023 자료로 Z 점수 계산하기

결과를 점수화하고 비교하는 한 가지 방법은 Z 점수를 사용하는 것입니다.

Z 점수는 특정 값이 평균보다 얼마나 높거나 낮은지를 측정한 것입니다. 표준화된 측정값이기 때문에 Z 점수는 다른 그룹간에 비교하는 데 사용될 수 있습니다.

반면에 전통적인 연령 등급은 최상의 결과를 기준으로 사용합니다. 각 결과는 해당 최상의 시간과 비교되며 비교용으로 사용될 표준화된 점수가 계산됩니다.

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

z 점수를 계산하기 위해 Python의 Pandas 패키지를 사용하여 결과를 성별과 연령 그룹으로 그룹화했습니다. 그런 다음, 각 그룹의 평균과 표준 편차를 계산했습니다(초 단위). 마지막으로, 개별 결과에서 평균을 빼고 표준 편차로 나누어 표준화된 점수를 얻었습니다. 음수 값은 점수가 평균보다 낮음을 나타내고, 양수 값은 평균보다 높음을 나타냅니다.

예를 들어, 한 연령 그룹의 평균 완주 시간이 4시 30분이고 표준 편차가 1시간이라고 합시다. 완주 시간이 3시 30분인 경우, 평균 (4시 30분)을 시간 (3시 30분)에서 빼서 -1시간을 얻습니다. 이를 표준 편차 (1시간)로 나누어 z-점수가 -1(평균보다 한 표준 편차 낮음)임을 얻습니다.

z-점수를 사용하는 데 두 가지 잠재적인 문제가 있습니다.

첫째, z-점수는 평균에 기반을 두고 있습니다. 평균은 이상치로부터 영향을 받을 수 있습니다. 특히 작은 연령 그룹에 이상치가 있는 경우 문제가 발생할 수 있습니다.

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

두 번째로, 각 연령 그룹이 평균을 중심으로 비슷한 범위의 가능성을 갖는다고 가정합니다. 표준 편차가 평균과 함께 조정된다면 이는 아마도 사실일 것입니다. 그러나 표준 편차가 평균과 관계없이 비슷하다면 문제가 발생할 수 있습니다.

여기 각 연령 그룹의 평균 완주 시간을 나타내는 그래프가 있습니다. 녹색 점선은 여성을, 주황색 점선은 남성을 나타냅니다.

남성의 결과를 나타내는 곡선은 꽤 깔끔해 보입니다. 이는 교과서 그래프처럼 보이며, 이것이 연령 그룹 간 실제 관계를 대변할 수 있는 것으로 생각됩니다.

하지만 여성의 경우 변동이 더 많습니다. 곡선 형태로 깔끔하게 증가하는 대신 기울기가 자주 변합니다. 아마도 몇 가지 이상치가 평균을 끌어 올리거나 내리는 역할을 하고 있는 것일수도 있습니다 — 또는 그냥 깔끔하게 나타나지 않는 것일 수도 있습니다.

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

그런 다음 표준 편차 문제가 있습니다. 각 연령 그룹에서 평균 범위(1:00에서 1:05)에 있습니다. 평균 시간이 4:15인 젊은 남성의 경우, 평균(2:15) 아래로 표준 편차가 두 개 이상 있을 공간이 없습니다. 그러나 5:30의 평균을 가진 노년 여성의 경우, 러너들이 평균을 능가할 여지가 훨씬 많습니다.

위 그래프는 각 연령 그룹별로 평균에서 1, 2 또는 3 표준 편차 아래로 완주한 러너의 비율을 보여줍니다. 드롭다운은 남성과 여성을 전환할 수 있습니다.

여성 중에서는 평균보다 2 표준 편차 아래에서 완주한 여성의 비율이 꽤 안정적입니다. 그러나 평균보다 1 표준 편차 아래에서 완주한 비율은 연령이 들수록 줄어듭니다. 70-74세 및 75-79세 연령 그룹(매우 적음)을 무시하더라도, 더 늙은 러너와 더 젊은 러너 사이에는 차이가 있습니다.

남성의 경우, 상황은 훨씬 안정적입니다. 평균에서 세 표준 편차 이상 낮게 점수를 받는 러너가 거의 없습니다(75-79세를 제외하고), 그리고 분포는 60대 중반까지 모두 꽤 유사합니다.

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

마지막으로, z 점수가 -2, -1, 0, +1, 그리고 +2일 때의 완주 시간에 대한 그래프가 있습니다. 이는 각각의 개인 시간이 어떻게 배치되는지에 대한 아이디어를 제공합니다.

예를 들어, 젊은 남성의 경우 z 점수가 -2인 경우 2:16이고 젊은 여성의 경우 2:39입니다. 여자들에게는 조금 불리한 것 같지만 미친 듯하게 흥분한 것 같진 않아요.

그러나 55-59세에서, 같은 비교를 하면 남성은 2:34이고 여성은 3:03입니다. 여기서는 상황이 좀 더 이상해 보입니다.

55-59세 남성들의 2:34라는 시간은 놀랍긴 합니다. 지난 해 시카고와 보스턴에서 그 나이 그룹의 최고의 남성은 2:35를 뛴 것을 고려하면요.

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

그 동안, 해당 연령 그룹의 여러 여성들이 3:03보다 빨리 뛰었습니다. 분명 인상적인 시간이지만 더 이루기 쉬운 시간이죠.

다시 말해, z 점수는 잘 보정되어 있는 것 같지 않습니다. 특히 연로한 나이에서 여성들을 남성들보다 유리하게 만들 수도 있습니다.

# 2023 데이터를 이용한 백분위 계산

결과를 점수화하는 또 다른 방법은 백분위를 사용하는 것입니다. 2010년부터 2019년까지의 데이터를 분석한 결과, 이 방법이 제 선호하는 방법입니다.

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

백분위수를 사용하면 다른 모든 선수들과의 성적을 비교하여 특정 시간을 이기는 선수가 몇 퍼센트나 되는지 확인할 수 있어요. 90번째 백분위 수에 있는 선수는 다른 선수들 중 90%보다 빨리 결승선을 통과하죠.

각 연령 그룹이 비교적 경쟁적이라고 가정하면, 특정 백분위에서의 점수를 얻는 것은 대체로 비슷한 난이도라는 거죠. 다만, 문제가 될 수 있는 부분은 만약 어떤 연령 그룹이 경쟁력이 떨어진다면, 더 높은 백분위에서 점수를 얻는 것이 쉬워질 수 있어요.

저는 이전 데이터를 분석하면서 발견한 다른 문제는 백분위가 극단에서는 신뢰성이 크게 떨어진다는 거였어요. 분포의 중간 지점에서는 꽤 잘 작동하지만, 90% 이상부터 몇몇 연령 그룹에서 이상한 결과가 나오기 시작해요. 그리고 99% 이상에서는 대부분의 곡선이 더 뾰족하고 신뢰성이 낮아집니다.

위의 시각화 자료는 백분위 수 표의 세 단계를 거칩니다.

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

제가 시작했을 때는 0에서 99.99 백분위까지 각 결과를 직접 계산했습니다. 90%에서 99.99% (위 그림 참조)까지 대규모, 젊은 연령 그룹에서 곡선이 상당히 신뢰할 수 있습니다. 하지만 몇몇 부분에서는 뾰족하고 신뢰할 수 없는 것이죠.

이 프로세스의 두 번째 단계에서는 SciPy 패키지를 사용하여 Savitzky-Golay 필터를 곡선에 적용했습니다. 이는 관측된 값들을 더 부드러운 곡선에 적합시키는 역할을 합니다.

마지막으로, 일부 곡선들이 범위의 매우 끝에서 급격하게 감소하지 않는 것을 발견했습니다. 이는 매우 젊은 남성과 비교해 99.9 백분위에서 높은 점수를 받기가 훨씬 더 쉬워진다는 것을 의미합니다.

더 조정하기 위해, 각 연령 그룹과 99백분위에서의 남자 선수 간의 백분율 차이를 찾아 그를 사용하여 각 연령 그룹에 대한 예상 결과를 계산하고 99에서 99.99까지의 각 백분위에 대한 실제 결과와 평균을 계산했습니다.

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

나이가 들어가면서 고령자 그룹이 끝에서 조금 더 경쟁력 있게 되는 곡선을 효과적으로 내려놓습니다.

위의 시각화 자료는 중앙값(50번째)에서부터 99번째 백분위까지 다양한 백분위에서의 실제 완주 시간을 보여줍니다.

99번째 백분위는 확실히 좋은 시간이지만, 위의 -2 개의 Z점수만큼은 경쟁력이 부족합니다. 최상의 시간에 도달하려면 99% 이상의 십분의 일과 백분의 일을 더 살펴봐야 합니다.

그러나 99% 미만에서는 이는 상당히 공정한 동등한 시간대인 것으로 보입니다. 특히 고령 여성들의 경우 99번째 백분위에서 약간 어긋나는 점이 있습니다. 그러나 대부분의 주자들인 90번째 백분위 이하에서는 비교를 하는 데 꽤 효과적인 방법으로 보입니다.

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

# 결과 예시 비교

그래서 실제로 어떻게 보이는지 궁금하시죠?

결과가 세 가지 방법에 따라 어떻게 다른지 확인하기 위해 몇 가지 경주를 살펴보겠습니다.

아래는 2023년 저지 시티 마라톤에서 백분위별로 상위 15명의 완주자 목록이 있는 표입니다. 나는 이 레이스에 참가했지만... 이 상위 15명 목록에는 랭크되지 않았습니다.

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

| Gender | Age | Finish   | zScore | Percentile | Age Grade |
| ------ | --- | -------- | ------ | ---------- | --------- |
| F      | 25  | 02:32:50 | -2.1   | 99.92      | 87.72     |
| M      | 27  | 02:17:32 | -1.98  | 99.86      | 88.45     |
| M      | 25  | 02:18:28 | -1.96  | 99.83      | 87.86     |
| F      | 39  | 02:45:59 | -1.85  | 99.77      | 83.08     |
| M      | 30  | 02:22:03 | -1.9   | 99.7       | 85.64     |
| F      | 23  | 02:43:08 | -1.93  | 99.69      | 82.18     |
| M      | 25  | 02:23:30 | -1.88  | 99.65      | 84.77     |
| M      | 26  | 02:23:32 | -1.87  | 99.64      | 84.75     |
| F      | 55  | 03:17:42 | -1.77  | 99.62      | 79.73     |
| M      | 27  | 02:24:45 | -1.85  | 99.59      | 84.04     |
| F      | 30  | 02:46:47 | -1.87  | 99.58      | 80.38     |
| M      | 50  | 02:47:20 | -1.63  | 99.57      | 80.08     |
| M      | 41  | 02:36:12 | -1.64  | 99.53      | 80.02     |
| M      | 27  | 02:26:11 | -1.83  | 99.51      | 83.22     |
| F      | 27  | 02:48:45 | -1.84  | 99.49      | 79.45     |

여기에는 많은 중복이 있습니다. 선택한 방식에 관계없이 상위 세 명은 동일했을 것입니다. 하지만 25세 여성들은 나이 등급을 사용했다면 세 번째 자리로 밀려났을 겁니다.

그러나 나이 등급으로 상위 15명을 선정하면, 포함된 가장 낮은 나이 등급은 81.63입니다. 따라서 여기에는 79~80 나이 등급을 가진 몇 명의 러너가 포함되었습니다.

백분위를 사용하면, 상위 15명은 남성 9명과 여성 6명으로 구성됩니다. 한편, 나이 등급을 사용하면 남성 11명과 여성 4명으로 구성됩니다.

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

퍼센타일을 사용하여 상위 마무리자 중 11명이 35세 미만이고 4명이 35세 이상입니다. 나이 등급별로는 상위 마무리자 중 13명이 35세 미만이며 마스터 연령 그룹 출신은 2명뿐입니다.

추가 예로, 여러분에게 2023년 에리 마라톤에서 제주도의 상위 15명 마무리자를 소개해 드리겠습니다.

```js
| 성별   | 나이 | 마무리 시간 | z 점수 | 백분위 | 나이 등급 |
|--------|-----|-------------|--------|---------|-----------|
| 여자   |  36 | 02:49:47    | -1.79  | 99.64   | 79.62     |
| 남자   |  40 | 02:40:24    | -1.57  | 99.16   | 77.36     |
| 남자   |  57 | 02:57:29    | -1.62  | 99.16   | 80.09     |
| 남자   |  57 | 02:59:10    | -1.59  | 98.92   | 79.35     |
| 남자   |  38 | 02:39:31    | -1.56  | 98.7    | 76.67     |
| 여자   |  31 | 02:59:23    | -1.67  | 98.51   | 74.74     |
| 남자   |  43 | 02:45:36    | -1.49  | 98.43   | 76.61     |
| 남자   |  26 | 02:36:38    | -1.66  | 98.4    | 77.67     |
| 남자   |  32 | 02:37:31    | -1.64  | 98.27   | 77.23     |
| 남자   |  59 | 03:04:56    | -1.5   | 98.13   | 78.25     |
| 여자   |  40 | 03:10:10    | -1.5   | 98.07   | 73.03     |
| 여자   |  30 | 03:07:44    | -1.53  | 97.36   | 71.41     |
| 남자   |  46 | 02:55:36    | -1.39  | 97.32   | 73.92     |
| 여자   |  34 | 03:08:30    | -1.52  | 97.24   | 71.12     |
| 여자   |  36 | 03:11:34    | -1.46  | 97.18   | 70.57     |
```

이리는 제시 시티보다 작은 규모의 마라톤이므로 일부 낮은 점수가 상위 15명으로 진입했습니다. 또한 마스터 러너들이 더 잘 대표됩니다.

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

백분위로 보면 상위 완주자 중 9명이 남성이며, 상위 완주자 중 5명이 35세 미만입니다. 연령별로 보면, 상위 완주자 중 13명이 남성이며, 상위 15명 중 6명이 35세 미만입니다.

최고의 성과를 식별하기 위해 z-점수를 사용했다면, 저지 시티에서 상위 완주자 15명 중 14명과 에리에서 상위 완주자 15명 중 10명이 35세 미만일 것입니다. 이러한 연령 그룹은 가장 낮은 표준 편차를 가지고 있어서 이들이 이점을 가질 것으로 예상됩니다.

# 전체 러너들의 전체 분포에 대비하여 점수 비교하기

마지막으로 살펴볼 점은 최고의 성과가 전체 러너들의 전체 분포를 어떻게 향상시키는지 입니다.

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

연령 평가의 전체적인 목적은 마스터 러너들이 계속해서 경쟁할 수 있도록 하는 것입니다. 따라서 모든 러너들의 분포와 정확히 일치하지는 않겠지만, 대략적으로는 비슷해야 합니다.

특정 연령 그룹이 최상위 성적에 전혀 나타나지 않는 경우, 해당 그룹에 편향된 시스템이 있다는 신호일 수 있습니다(어떤 이유로든).

그렇다면 상위 1,000개 성적의 분포를 연령 평가, 백분위수, 그리고 Z-점수별로 살펴본다면 어떤 일이 벌어질까요?

이 그래프에서 파란 막대는 1,000명의 러너 중 특정 연령 그룹의 인원 수를 나타냅니다. 다음 세 개의 막대는 연령 평가, 백분위수, 그리고 Z-점수에 따른 최상위 1,000개 성적 러너의 수를 보여줍니다.

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

드롭다운 메뉴에 유의하세요. 이 메뉴를 사용하여 남성과 여성을 전환할 수 있습니다.

연령 등급은 어떻게 되나요?

35세 미만의 여성들 중 상위 1,000명에 속하는 비율은 그들이 전체 그룹에서 차지하는 비율과 유사합니다. 그러나 35세부터 54세까지, 그들은 명백히 소수입니다. 60세에서 79세 사이에는 반대로 말하면 - 만약 모든 것이 무작위로 분산되어 있다면, 상위 1,000명 중 여성이 더 많을 것입니다.

남성들을 살펴보면, 35세 미만 연령 그룹은 과대표현(368 대 221)되어 있습니다. 40대의 남성들은 약간 소수로 표현되는 것으로 보이며, 그 외의 연령 그룹들은 그리 나쁘지 않습니다.

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

만약 백분위수를 사용한다면 어떨까요?

여성의 경우, 모든 연령 그룹이 상당히 잘 대표되고 있습니다. 35세 미만 연령 그룹은 조금 더 일반적이지만(198 대 161), 극단적이진 않습니다. 나머지 연령 그룹들은 모두 무작위 분포와 상당히 유사합니다.

남성의 경우, 상황은 더욱 대표적입니다. 일부 살짝 다른 차이가 있지만, 어느 연령 그룹도 현저하게 과소 또는 과대 표현되어 보이지 않습니다.

한편, Z 점수는 전혀 대표적이지 않습니다. 남성과 여성 모두에게 35세 미만 연령 그룹이 매우 과대 표현되어 있습니다. (65-69세까지의) 대부분의 다른 남성 연령 그룹들은 심하게 과소표현되어 있습니다. 마스터 여성들은 조금 나은 상황이지만, 대부분은 전혀 과소표현되어 있습니다.

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

3개의 방법 중 백분위 점수 산정은 최고 선수의 분포가 러너들 전체 분포와 가장 유사합니다.

# 결론 및 앞으로의 방향

2023년 데이터를 분석한 결과 - 나이에 따라 변하는 보스턴 예선 시간을 포함한 이 기사를 읽은 후에도 나이 등급 시스템이 업데이트가 필요하다고 확신합니다.

여성을 중심으로 보정에 문제가 있어 몇몇 그룹을 다른 그룹보다 우대하는 것이 있습니다. 그리고 80세 이상 나이 등급 점수를 받는 사람들의 경우는 소수에 불과하기 때문에 많은 차별적인 기능을 제공하지 않습니다.

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

원래 2010년부터 2019년까지의 데이터를 사용하여 백분위수를 계산했을 때, 교정 문제가 있었습니다. 하지만 이 분석의 최종 결과는 더 균형을 이루는 것 같아요.

여러 선수들이 99번째 백분위수 이상을 얻는 경우에는 절대 최고의 성적을 결정하는 최선의 방법이 아닐 수도 있지만, 평균 이상의 선수들을 비교하는 데 훨씬 나은 방법입니다.

다음은 무엇일까요?

지금 데이터 탐색과 분석을 마쳤으니, 이를 좀 생각해볼 시간을 갖고 싶어요. 생각을 정리한 후에 기존 연령 등급 및 백분위수의 장단점을 제시한 마지막 기사를 준비할 거예요.

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

한편, 2023 연령 요소, z-점수 및 백분위를 활용한 연령 계산기를 업데이트할 예정이며, Kaggle에 데이터셋을 공유할 준비도 하고 있어요.

최종 기사에서 업데이트된 계산기와 공개 데이터셋 링크를 꼭 포함할 거니까, 관심 있으시면 이메일 업데이트를 구독해주세요.

이번 주말에 보스턴에서 참가하는 모든 분들에게 행운을 빕니다! BAA가 발표하면 숫자들을 분석하고, 다음 주에 흥미로운 정보를 공유할 거에요.

저는 열정적인 러너이자 데이터 애호가에요. 방금 40살이 되었어요, 그래서 연령 그룹 간 결과를 비교하는 것이 나에게 특히 흥미롭답니다. 제 활동을 계속 지켜볼 수 있는 방법은 다음과 같아요:

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

- 러닝 위드 락 팔로우해서 내 훈련 소식 듣기
- 마라톤 훈련 계획 선택에 대한 팁 읽기
- Strava에서 나를 스토킹하기
