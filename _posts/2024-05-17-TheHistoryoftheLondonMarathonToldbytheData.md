---
title: "런던 마라톤의 역사를 데이터로 만나보세요"
description: ""
coverImage: "/assets/img/2024-05-17-TheHistoryoftheLondonMarathonToldbytheData_0.png"
date: 2024-05-17 19:02
ogImage:
  url: /assets/img/2024-05-17-TheHistoryoftheLondonMarathonToldbytheData_0.png
tag: Tech
originalTitle: "The History of the London Marathon Told by the Data"
link: "https://medium.com/runners-life/the-history-of-the-london-marathon-told-by-the-data-bca252794a44"
---

![이미지](/assets/img/2024-05-17-TheHistoryoftheLondonMarathonToldbytheData_0.png)

몇 달 전, 저는 미국 마라톤과 관련된 이십 년의 데이터를 탐구하는 일련의 기사를 작성했습니다.

그 시리즈의 일부로, 저는 미국에서 열리는 세 개의 세계 마라톤 메이저인 보스턴, 시카고, 뉴욕에 대해 깊이 파헤쳤습니다.

당시 일부 독자들은 런던 마라톤의 역사에 대해 물었지만, 제 초점은 미국 마라톤에 있었습니다. 이는 편의상 그랬다고 할 수 있습니다. 미국 마라톤에 대한 데이터에 쉽게 접근할 수 있었지만 전 세계의 레이스에 대한 데이터를 수집하는 것은 더 어려워지고 있었기 때문입니다.

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

하지만 제 할 일 목록에 있었어요. 그리고 지난 주말에 런던 마라톤이 열려서, 마침내 이를 처리하기에 좋은 시기인 것 같아요.

그렇다면 데이터는 런던 마라톤의 역사와 지난 몇 십 년간 어떻게 변화했는지에 대해 우리에게 무엇을 말해줄까요?

# 사용 데이터는 무엇인가요?

이 분석에서는 Athlinks의 데이터를 사용했습니다.

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

그들의 데이터베이스에는 2001년부터 시작된 런던 마라톤 개인 결과가 포함되어 있습니다. 런던 마라톤 웹사이트에는 일부 오래된 경주 결과가 있지만, 해당 결과는 2014년부터의 것만 있습니다.

저는 Athlinks에서 각 참가자의 성별, 나이 및 완주 시간을 수집한 후, Python의 Pandas 패키지를 사용하여 데이터를 분석하였습니다.

나이가 보고된 방식에 일관성이 없는 것이 있었습니다. 많은 해에 걸쳐, 참가자의 나이를 해당 연령 그룹의 시작으로 내림하여 기재한 것으로 보입니다. 20대의 참가자는 18세(18~39세 범위)로 보고되었습니다. 그래서 저는 참가자들을 40세 미만부터 시작해서 5년 단위 연령 그룹을 거쳐 80세 이상까지 범주화했습니다.

2006년의 데이터에는 뭔가 이상한 것이 있는 것으로 보입니다. 40대의 참가자들이 40세로 보고되어, 그들은 40세 이하 연령 그룹에 포함된 것으로 보입니다. 그래서 해당 연도의 연령 데이터 옆에 정신적인 별표를 해 주세요. 그러나 나머지 연도는 신뢰할 만한 것으로 보입니다.

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

이 데이터를 통해 몇 가지 질문을 탐색해 볼 수 있어요:

- 이 경주가 몇 년 동안 어떻게 성장했나요?
- 러너들의 연령과 성별과 같은 인구 통계가 어떻게 변화했나요?
- 러너들이 더 빠르거나 느려졌거나, 아니면 비슷했는지?

그러면 자세히 알아보도록 할게요.

# 시간이 흘러 필드가 어떻게 변화했는가

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

현대 런던 마라톤의 첫 대회는 1981년에 열렸어요.

주최자들은 뉴욕 시티 마라톤에서 영감을 받았어요 — 작은 대회에서 대규모 참가 이벤트로의 전환을 주도한 데서 따왔으니까 적절한 선택이었죠.

런던은 Polytechnic Marathon과 함께 자체적인 풍부한 마라톤 역사를 가지고 있어요. 그러나 대부분의 오래된 마라톤과 같이 몇 백 명 이상은 참여하지 않는 작은 이벤트였어요.

런던 마라톤은 이를 바꿨어요. 첫 런던 마라톤에는 6,000명 이상의 러너가 완주했고, 1980년대를 통해 대규모로 성장했어요. 단 두 번째 해에만 뉴욕보다 많은 완주자가 있었답니다. 비록 뉴욕이 가장 큰 마라톤의 왕관을 되찾았지만, 그 이후 런던은 전 세계에서 가장 큰 레이스 중 하나가 되었어요.

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

## 마라톤의 성장

따라서 이 맥락을 고려하면 데이터를 자세히 살펴보겠습니다.

매년 몇 명이 런던 마라톤을 완주하나요? 그리고 2001년 이후로 그 수는 어떻게 변했나요?

지난 스무 해 동안에는 완주자 수가 조금씩 늘고 있는 것을 볼 수 있습니다.

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

2001년에는 거의 30,000명의 참가자가 있었습니다. 몇 년 동안 변동이 있지만, 2019년까지 꾸준히 증가하다가 레이스가 40,000명을 초과하는 성장이 이루어졌습니다.

그 성장세는 2020년에 코로나19로 중단되었습니다. 세계적으로 일시 중단된 이 시기에도 런던에서는 60명의 엘리트 선수들을 위한 소규모 이벤트가 열렸지만, 대규모 마라톤은 개최되지 않아서 차트에서는 빠져 있습니다.

하지만 2021년에는 빠르게 회복되었고, 2023년까지는 2019년에 세운 이전 최고 기록을 훌쩍 넘는 종료자 수를 기록했습니다. 레이스는 거의 50,000명에 이르는 종료자를 기록했는데, 이는 세계에서 가장 큰 마라톤 대회 목록 상위에 위치하게 됐습니다.

## 여성 참가자 증가

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

런던 마라톤에 여성이 처음으로 참가한 것은 있었지만, 여성들의 러닝은 아직 어린 나이였습니다. 여성 이벤트가 올림픽에 포함된 것은 1984년에 이루어졌죠.

80년대와 90년대를 통해, 마라톤은 여전히 남성들에 의해 주도되었지만, 천천히 변화하기 시작했습니다. 지난 20년 동안, 여성들의 마라톤 참가는 급격하게 증가했습니다.

이런 변화가 런던에서는 어떻게 나타났을까요?

위 시각화 자료는 완주자 수를 보여줍니다 — 빨간 막대는 남성을, 초록 막대는 여성을 나타냅니다.

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

청색 선은 여성 참가자들이 차지하는 비율을 나타냅니다.

초반에는 남성이 압도적이었습니다. 2001년에는 마친 참가자들 중 23%만 여성이었습니다.

하지만 여성 수는 2001년의 약 6,000명에서 상당히 증가했습니다. 2010년에는 거의 두 배로 늘었습니다. 2023년에는 세 배가 넘게 증가했고, 여성 참가자 수가 20,000명을 처음으로 넘어섰습니다.

이 기간 동안 백분율도 지속적으로 증가했습니다. 2019년 이후 상대적으로 안정되어 왔으며, 최근 몇 년 동안은 완주자의 약 40%가 여성이었습니다.

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

레전드에서 '여성'을 클릭하면 해당 막대를 숨기고 분야의 남성 부분을 격리할 수 있습니다. 흥미로운 점은 지난 20년간의 남성 완주자 수가 꾸준히 유지되어 왔다는 것입니다.

2023년에는 28,000명이 넘는 남성 완주자가 발생한 급증이 있었지만, 나머지 시기는 20,000명에서 25,000명 사이로 변동했습니다. 본질적으로 경주의 성장은 거의 모두 여성들로부터 왔습니다.

## 분야의 점차적 그레이징

보스턴 마라톤의 데이터를 분석할 때, 러너들이 나이를 먹고 있다는 것을 알게 되었습니다. 이 패턴은 다른 경주에서도 나타나고 있습니다.

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

런던에서도 비슷한 일이 일어나고 있나요?

이 차트에서 각 색상은 서로 다른 연령 그룹의 완주자 수를 나타냅니다. 연령 그룹은 가장 어린 사람(40세 미만)이 왼쪽에 있고 가장 나이 많은 사람(80세 이상)이 오른쪽에 있습니다.

이전에 언급한 대로, 2006년의 소스 데이터에 문제가 있습니다. 그러므로 그곳에서 보이는 이상치를 무시하십시오. 러너들의 연령이 결과 집합에서 잘못 표시되었기 때문일 가능성이 높습니다.

드롭다운 메뉴를 사용하여 남성과 여성을 번갈아 선택할 수 있습니다.

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

남성들 중에서, 조깅을 하는 총 인원은 (2023년을 제외하고) 크게 변하지 않았어요.

40대 초반의 남성(보라색) 인원은 처음 몇 년 동안 늘어났다가, 이후에는 4,000명 정도에서 안정되는 것으로 보입니다. 다만, 다른 연령 그룹(45-49세 이상)은 시간이 흐를수록 계속해서 늘어나는 추세입니다.

여성들 중에서는, 2001년부터 2023년까지 전체 참가자 수가 계속해서 늘어나면서, 상대적인 변화를 파악하기 어려워요. 중장년 러너들이 더 많아지고 있지만, 20대와 30대 러너들도 늘어나고 있어요.

그래프의 대체 버전은 각 연령 그룹을 전체 성별 인원의 백분율로 표시하고 있어요.

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

여기서 젊은 남성의 비율이 천천히 감소하는 것과 비교하여 45세 이상의 남성 비율이 천천히 증가하는 것이 더 쉽게 볼 수 있어요.

여성 중에서는 변화가 더 극심해요. 초기에는 여성 중 3분의 2가 40세 미만이에요. 하지만 마지막에는 이 비율이 50%에 가까워지고, 모든 연령 그룹에서 증가했어요.

이 변화는 코로나 이후에 더욱 확대됐어요. 2023년에는 여성 중 10% 이상이 55세 이상이었는데, 2001년에는 5% 미만이었어요.

이러한 변화를 볼 수 있는 한 가지 방법은 각 연령 그룹 중 여성 비율을 그래프로 나타내어 시간이 지남에 따라 어떻게 변하는지 보는 것이에요.

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

조건부 분석에 대한 그래프와 같이 여기서 빨간색은 남성을 나타내고 초록색은 여성을 나타냅니다. 파란색은 여성 참가자의 백분율을 보여줍니다. 다른 점은 연령 그룹별로 분리되어 있다는 것입니다. 슬라이더를 드래그하거나 재생 버튼을 눌러 시간이 지남에 따라 어떻게 변하는지 살펴볼 수 있습니다.

시간이 지남에 따라 여성 백분율이 증가하지만 언제나 젊은 연령 그룹 중에서 더 높습니다. 그러나 시간이 흐름에 따라 차이가 상당히 줄어듭니다.

2019년까지, 20대, 30대, 40대 및 50대의 참가자들 간의 차이가 훨씬 줄어들었습니다. 2023년으로 빨리 이동하면, 이러한 연령 그룹 모두 유사한 비율을 가지고 있습니다 — 남성 60%와 여성 40% 정도입니다.

시간이 지남에 따라 남성과 여성 사이의 참가자들 간의 균형이 더 맞추어졌지만, 변화가 되기 까지는 최고 연령 그룹에 따라 따라잡는 시간이 필요했습니다.

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

# 런던 마라톤의 완주 시간

마침내, 완주 시간은 어떻게 변화했나요?

런던은 악명높은 빠른 코스이며, 한 개 이상의 세계 신기록이 세워졌습니다.

## 역대 우승자들

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

아래 시각화는 매년 남성 (빨강)과 여성 (녹색)의 우승 시간을 그래프로 표시합니다.

남성들 중에는 약간의 하향 트렌드가 있습니다. 초기에는 남성 우승자들이 모두 2:06에서 2:07 범위에 있었습니다. 2008년 이후 대부분의 우승자들이 2:05 또는 더 빨리 완주했지만 더디게 완주한 해도 몇 해 있었습니다.

남성 중에서 가장 빠른 시간은 2023년에 있었는데, 켈빈 킵툼이 2:01:25로 완주했습니다.

한편, 여성은 2003년에 최고의 시간을 보였습니다. 그 해, 폴라 래드클리프가 2:15:25로 우승했습니다. 그녀는 2019년까지 계속되는 세계 신기록을 세웠습니다.

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

그 경주에서 여자 엘리트 선수들은 레드클리프를 자극하여 그러한 뛰어난 시간을 선보이도록 돕는 남성 페이서들과 함께 출발했습니다. 이 종류의 경주가 올해 읽었을 수도 있는 “혼성”과 “여성 전용” 세계 기록 사이의 차이의 이유입니다. 2003년 경주 중계를 여기서 시청할 수 있습니다.

최근 몇 년간 여자 우승자는 종종 2:17부터 2:18 정도의 시간대에 올랐습니다 — 많은 해보다는 빠르지만 레드클리프의 2003년 시간보다는 빠르지 않습니다.

## 총 평균 완주 시간

하지만 우리는 일반인이고, 로마 마라톤에서 우승하는 것은 아닙니다. 더 흥미로운 질문은 그들 뒤의 일반 대중들 사이에서 무슨 일이 벌어지고 있는지입니다.

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

위의 그래프는 경기장의 세 지점에서의 완주 시간을 보여줍니다 — 90번째 백분위수 (다른 선수들보다 90% 이상 빠른 선수), 75번째 백분위수 (다른 선수들보다 75% 이상 빠른 선수), 그리고 중앙값(팩의 중간에 있는 선수).

전체 필드를 보는 것은 속임수가 될 수 있습니다. 왜냐하면 인구 통계의 급격한 변화가 평균 시간에 영향을 미칠 수 있기 때문입니다.

하지만 이 그래프가 보여주는 것은 2018년에 날씨가 얼마나 나빴는지라는 것입니다. 그 해가 기록상으로 가장 더운 경주였고, 2007년도 그다지 뒤떨어지지 않았습니다. 이 두 해에 그래프가 솟은 이유가 분명히 그것 때문일 것입니다.

## 성별을 고려한 경우

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

결과를 성별로 세분화하면 가능한 추세를 식별할 수 있어요. 나이는 여전히 잠재적으로 혼동을 줄 수 있는 변수이지만, 인구통계학적 변화 중 가장 큰 변화가 성별 사이에서 일어났어요.

세 줄은 동일한 장소를 나타내지만, 이 시각적으로 빨간 점은 남성이고 초록 점은 여성이에요.

특히 더 빠른 남성들 중에서 시간이 개선되고 있어요. 최고의 여성 선수들 중에서는 약간의 개선이 있지만, 느린 여성 선수들 중에서는 트렌드가 보이지 않는 것 같아요.

## 나이의 느려짐을 고려하기

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

그럼 만약 연령과 성별로 모두 자세히 살펴본다면 어떨까요?

이 시각적 자료를 통해 여성들의 세 가지 포지션을 왼쪽에서, 남성들의 세 가지 포지션을 오른쪽에서 확인할 수 있습니다. 그러나 위쪽의 버튼들을 사용하여 결과를 연령 그룹으로 필터링할 수 있습니다.

젊은 남성들 중에서는 최근 몇 년 동안 시간이 분명히 개선되었습니다. 마지막에는 상위 10%가 3시간 15분에서 3시간 20분 아래로 개선되었습니다. 젊은 여성들도 약간 개선되었는데, 특히 2012년부터 개선되었습니다. 마지막에는 상위 10%가 3시 30분대에 마칩니다.

연령 그룹이 높아짐에 따라 40대와 50대에서 약간의 하향 추세가 보입니다. 그 이상으로 올라가면 약간 무작위로 보이기 시작합니다. 그때 연령 그룹이 충분히 작아서 매년 안정적인 관계가 되기 힘들 수도 있습니다.

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

만약 이 시간들을 미국 메이저 대회인 보스턴, 뉴욕, 그리고 시카고의 시간과 비교한다면, 런던의 시간들은 보스턴과 시카고 사이에 위치하게 될 거에요. 보스턴의 시간들은 런던의 것보다 빠르며(아마도 더 많은 예선 참가자 수에 의해), 그러나 런던의 시간들은 시카고의 것보다 빠르죠.

하지만 조금 이상한 것은 2019년 이후에는 큰 차이가 없다는 거에요. 대부분의 대규모 미국 대회에서는 이 지점에서 시간이 크게 단축되었거든요. 저는 항상 대량 시장에 슈퍼 슈즈가 확산되고 있다는 것이 이 현상을 촉진하고 있다고 가정했었어요.

하지만 이 효과는 런던에는 존재하지 않는 것으로 보여요.

# 런던 2024는 어디에 속할까요?

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

그래서 그것이 역사이자 기반이 되었습니다.

이제 지난 스물 해간 동안 런던에서 어떤 변화가 있었는지를 알게 되었으니, 이번 해 레이스의 데이터를 자세히 살펴보겠습니다.

올해 참가자가 더 많아졌을까요? 성별로 더 균형을 이루었을까요? 참가자들은 더 나이가 많아졌을까요?

해당 분석 결과는 일주일 후쯤 다시 확인해주세요.

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

만약 위에 대한 관심이 있다면 — 또는 다른 말로 말해, 마라톤에 관한 데이터 중심 이야기에 관심이 있다면 — 이메일 업데이트를 받기 위해 구독하는 것을 잊지 마세요.

저는 열렬한 러너이자 데이터 애호가입니다. 올해의 보스턴 마라톤은 컷오프 타임 때문에 놓쳤지만, 2025년에는 거기 참가하기를 희망하고 있습니다. 제가 무슨 활동을 하는지 계속해서 따라갈 수 있는 방법은 다음과 같습니다:

- 저의 훈련에 대한 소식을 들으려면 Running with Rock을 팔로우하세요.
- 마라톤 훈련 계획 선택에 대한 이 팁들을 읽어보세요.
- Strava에서 저를 쫓아보세요.
