---
title: "100일간의 스팀 세일 분석"
description: ""
coverImage: "/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_0.png"
date: 2024-05-23 13:36
ogImage:
  url: /assets/img/2024-05-23-100DaysofSteamSalesAnalysis_0.png
tag: Tech
originalTitle: "100 Days of Steam Sales: Analysis"
link: "https://medium.com/@simon.nordon/100-days-of-steam-sales-analysis-aa28a183eedf"
---

게임 개발 산업에 종사한 사람이라면, 성공적인 게임을 만드는 것이 얼마나 어려운지 알 것입니다. 누구도 그 공식을 알아내지 못했습니다. 모든 것을 올바르게 해도 실패할 수 있죠.

우리는 소중한 성공 사례에 감격합니다. Stardew Valley, Lethal Company, Undertale. 가능합니다. 그러나 성공 사례마다 수천 개의 잊혀진 실패 사례가 있습니다.

우리는 우리의 열정적인 프로젝트에 수백 시간을 투자합니다. 고용주들이 수십만 달러를 쓸 것이라고 확신할 수도 있는 노동이죠. 대신 우리는 모든 걸 스스로에게 걸어, 대부분 몇 달러만 돌려받게 됩니다.

우리는 용감한 건지, 그냥 바보인 건지요?

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

이 질문을 해결하고 동료 개발자들에게 희망을 줄 수 있기를 바라며, 2023년 1월 1일부터 스팀에서 출시된 게임 100일을 살펴보고 판매 성적을 분석하여 모두가 꿈을 이루는 확률을 알아보았습니다.

# 개요

100일 동안 총 2823개의 유료 게임이 출시되었으며, 하루 평균 새로운 게임은 28개입니다.

스팀 수수료를 고려한 평균 매출은 228,723달러(미국 달러)였습니다.

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

처음에는 좋아 보일 수 있지만, 스팀 세일은 공격적인 파레토 분포를 따릅니다. 이는 일반적으로 '80:20' 규칙으로 알려진데, 입력의 20%가 결과의 80%를 만든다는 의미입니다.

스팀 세일은 조금 더 극단적입니다:

![Steam Sales Analysis](/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_0.png)

중앙값 수익, 즉 평균 게임이 벌어들인 금액에 더 관심이 있습니다. 이를 시각화하기 쉽게 만들기 위해 10만 달러 이상 벌어든 모든 게임을 제거해 보겣습니다.

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

![Image](/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_1.png)

`스팀`의 수익 중앙값은 `대단한` $799 미국 달러였습니다. 처참해 보일 수 있지만, 이 데이터를 다른 말로 바꾸면 더 희망적으로 보일 수 있습니다.

출시된 게임 중 1개당 $1,000,000 이상을 벌어들인 게임은 42개 중 1개이며, 매주 4개의 초대 성공적인 게임을 나타냅니다.

게임 중 1개당 $100,000 이상을 벌어들인 게임은 12개 중 1개이며, 주당 17개의 성공적인 게임을 나타냅니다.

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

만약 당신의 게임이 상위 8%에 들면 생계를 유지할 수 있어요. 상위 2%에 들면 거액의 수익을 올릴 수 있어요.

어렵긴 하지만 불가능하진 않아요.

안타깝게도, 5개 중 1개 게임은 $100 미만 수익을 올렸어요. 매주 40개의 실패한 게임이 있어요.

**방법**

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

Steams API를 사용하여 100일 간의 게임 출시를 가져 와 파싱하기 위해 파이썬을 사용했습니다. 나쁜 항목을 제거하고 JSON 구조를 단순화 한 후 CSV로 변환하여 다음과 같이 구글 스프레드 시트로 가져왔습니다:

[구글 스프레드 시트 링크](https://docs.google.com/spreadsheets/d/1T-Tw6PwxEAqYMnos5pYwRbwcNVacYn9zCt6HpL9jLfQ/edit?usp=sharing)

<img src="/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_2.png" />

스팀 API에서 얻을 수 없는 유일한 측정 항목은 수익이었습니다. 그러나 스팀 게임이 소유자 대 리뷰 비율이 30 대 1로 매우 잘 알려져 있으며 이는 모든 게임에 90%의 상관 관계를 가지고 있습니다.

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

소스: https://vginsights.com/insights/article/further-analysis-into-steam-reviews-to-sales-ratio-how-to-estimate-video-game-sales

스팀은 플랫폼의 매출에서 30%의 수수료를 가져갑니다. 이는 순수 매출로 이어집니다.

# 양적 데이터

장르, 콘텐츠 등급 및 VR 지원과 같은 기능과 같은 요인별 중앙값 매출을 분석해 봅시다. 먼저 장르부터 시작합니다.

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

<img src="/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_3.png" />

친구들 게임이 MMO RPG보다 현저히 성적이 안 좋다는 것을 알 수 있어요. 하지만 이러한 모바일 게임들이 몇 일 만에 만들어질 수 있다는 사실을 고려해야 해요. 한편 MMO는 큰 예산과 수 년의 생산 시간을 요구해요.

다음은 플랫폼 분포를 살펴봅시다.

<img src="/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_4.png" />

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

파레토 분포에 관한 보편적인 사실 중 하나는 그의 공격적인 정도가 데이터 세트의 크기와 비례한다는 것입니다. 다시 말해, 경쟁이 더 많은 시장에서는 불평등이 더 많이 나타납니다.

간단히 맥(mac)의 게임이 더 적기 때문에 경쟁이 적고, 구매자가 게임을 선택할 기회가 더 많습니다.

재미있게도, VR의 수익성은 크게 감소했습니다. 일정 시점에는 게임 수가 적어서 매출 중앙값이 $150,000에 이르렀습니다.

마지막으로 콘텐츠 등급을 살펴보겠습니다.

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

![image](/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_5.png)

데이터 세트에서 대략 10%의 게임이 '성인 전용'으로 표시되어 있습니다. 이들은 평균적인 게임만큼 잘 할 수 있었습니다. 다른 한편으로, 폭력은 명백히 경쟁에서 우위를 차지했습니다.

이 데이터의 타당성도 살펴보겠습니다. 각 게임이 출시 월에 따라 얼마나 많은 수익을 올렸는지부터 시작해 보죠.

![image](/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_6.png)

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

2023년 1월 1일을 선택한 이유는 데이터를 최근 것으로 유지하면서도 게임이 수익을 얻을 수 있는 충분한 시간을 주기 위해서예요.

여기서 볼 수 있듯이 중간 수준의 게임은 한 해 후에도 월 평균 90달러 정도의 수익을 올리고 있어요.

마지막으로, 상관관계와 인과관계를 다루고 있음을 강조하기 위해, 게임 이름의 첫 글자로 매출을 시각화한 것이 재미있을 것 같아서요.

![그림](/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_7.png)

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

당신이 게임 제목을 'y'로 시작하게 하면 'v'로 시작하는 것보다 수익이 15배 많아진다는 정말인가요?

아마 그렇지 않을 거에요.

데이터를 살펴보는 것이 중요하지만, 이렇게 많은 불확실성 속에서 세세한 부분에 대해 파고들어가는 것은 그다지 유용하지 않을 수 있어요.

# 질적 데이터

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

저는 확실히 첫 번째로 스팀 세일을 분석하려는 사람은 아니에요. 이미 그에 관한 전체 웹사이트들이 있어요. 이 연구를 시작한 후에야 알게 된 사실이에요:

하지만, 질적 연구에 참여하는 사람을 별로 보지 못해요. 즉, 개별 항목을 보다 자세히 살피는 것이죠. 제 데이터 세트에 게임 스크린샷을 추가한 이유 중 하나는 어떤 게임이 잘 되고 있는지 감을 잡을 수 있도록 도와주고자 했기 때문이에요.

그래서 이제 3가지 예시를 질적으로 분석해보려고 해요:

- 데이터를 기반으로 매우 성공적인 게임이지만, 사실 그렇지 않아야 했던 게임.
- 대부분의 개발자에게 이룰 수 있는 것처럼 보이는 성공한 게임들.
- 실패한 게임(또는 숨겨진 보석). 실패했지만, 더 나은 성과를 이루었을지도 모르는 게임들.

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

## 놀랍게 성공한

Pizza Tower ($20,033,360):

![Pizza Tower](/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_8.png)

Pizzas Tower는 GameMaker로 만든 '잘 못 그린' 예술이 특징인 2D 플랫포머 게임입니다. 이런 상황에도 불구하고 Pizza Tower는 풍자, 주제, 혁신을 적절히 조합하여 성공을 거두었습니다.

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

2D 플랫포머는 가장 경쟁력이 있고 매출 성적이 가장 낮은 장르 중 하나입니다. Pizza Tower는 모든 규칙을 깨고 어떤 꿈의 게임이든 가능함을 보여줍니다.

4년 개발 주기로 2,000만 달러의 매출을 달성했어요.

## 중간 성공적이고 가능성 있는 게임

Into the Flames ($406,627)

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

<img src="/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_9.png" />

https://store.steampowered.com/app/1222300/Into_The_Flames/

Into the Flames는 활발히 개발 중인 소방 시뮬레이션 게임으로, 한 명의 개발자가 개발하고 있을 수 있습니다. 그래픽은 최고는 아니지만, 어떤 흥미를 끌고 있는 특정 주제를 다루고 있습니다.

제 꿈의 세팅($255,085)

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

~~<img src="/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_10.png" />~~

[링크](https://store.steampowered.com/app/2200780/My_Dream_Setup/)

내 꿈의 세팅은 간단하지만 매우 정교한 시뮬레이션 게임으로, 최고의 게이밍 룸을 만드는 데 초점을 맞추고 있습니다.

이는 모든 게이머가 익숙한 주제를 다루고 있으며, $5의 가격표로 쉽게 구매할 수 있습니다.

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

Beton Brutal ($117,945)

![Screenshot](/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_11.png)

Another game from a solo developer. A single player parkour game with some beautiful, brutalist inspired architecture.

Finding success with any of the above games would, at the very least, fund your solo development endeavors for years to come.

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

## 실패한 게임 (숨은 보물들)

지금까지 카테고리 예시를 찾는 것은 쉬웠습니다. 왜냐하면 우리는 게임 중 상위 8%만 살펴보면 되기 때문이죠. 이제 우리는 그 꿈을 이루지 못한 2600개의 다른 타이틀로 깊게 파고들어봅니다.

좋은 소식은 무언가 돋보이는 게임을 발견하려고 수천 개의 게임을 스크롤해야 했다는 것입니다. 즉, 대부분의 게임들이 열등한 위치에 있는게 타당하다는 것을 뜻하는 더 좋은 방법입니다.

A Rum Tale ($63)

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

![이미지](/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_12.png)

[A Rum Tale](https://store.steampowered.com/app/2246310/A_Rum_Tale/)

이 게임은 정말 잘 만들어진 플랫포머 게임입니다. 하지만 리뷰가 단 3개밖에 없네요.

그래픽이 탁 트이고 체적 안개 기능까지 갖추고 있는데, 이 정도면 좀 더 많은 관심을 받았으면 하는데요. 소규모 팬덤도 없는 것이 놀랍네요.

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

그 정보를 찾기 어려울 수 있습니다. 이는 마케팅 실패를 보여줄 수 있습니다.

플레임 키퍼 ($9,933)

![이미지](/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_13.png)

[링크](https://store.steampowered.com/app/1374230/Flame_Keeper/)

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

귀엽고 테마가 매력적인 액션 로그 라이트 게임입니다. $9,000에 매력적인 요소를 보였지만, 개발자가 투자금을 회수했는지 의문스럽습니다.

아마도 75%의 리뷰 평점 때문에 인기를 얻지 못한 것 같아요.

킹에게 공격 ($198)

![이미지](/assets/img/2024-05-23-100DaysofSteamSalesAnalysis_14.png)

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

https://store.steampowered.com/app/2108410/Attack_on_King_Reloaded/

이 VR 게임은 RPG 전략 장르를 완전히 몰입형으로 조합한 흥미로운 작품입니다. 정교한 그래픽을 갖추고 있는 것으로 보입니다.

그러나 독특한 스타일에도 불구하고 주목을 받지 못하고 있다고 하네요.

# 결론

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

이 연구 결과를 기반으로, 이것은 나에게 그리고 다른 인디 개발자들에게 제안할 팁입니다.

## 큰 그림을 그리라아니면 집에 귀환하라.

게임을 작게 유지하는 것에 대한 모든 관습적인 조언에도 불구하고, 자신이 감당할 수 있는 것 이상의 일을 성공적으로 해낸 개발자들이 가장 높은 곳에 도달하는 것처럼 보입니다.

왜냐하면 이 세계는 승자가 모두를 가져가는 것이기 때문입니다. '괜찮은' 게임을 만들었다고해서 어떤 칭찬도 받을 수 없는 것이죠.

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

게임이 복잡해야 한다는 뜻은 아니에요. 간단한 게임도 충분하죠. 그저 꼭 필요한 것만 잘 갖추어 주면 돼요. 예를 들어, 예술, 상점 페이지, 음악, 시각 효과, 게임 플레이, 플레이어 경험 등이 선택한 장르에 맞게 최고 수준이어야 해요.

## 향수를 활용하세요

개발자로서 우리는 창의성과 독창성을 자랑스러워해요. 여러분이 좋아하는 게임을 그대로 복사해서 만들라는 건 아니에요. 그런 게임들은 대체로 성공하지 않거든요. 하지만 사랑받는 시리즈에서 영감을 받으면 플레이어들과 감성적으로 연결될 수 있어요.

만일 여러분의 게임 이미지가 플레이어들에게 좋은 추억을 떠올리게 하면서 동시에 새롭고 흥미로운 것을 제공한다면, 누르는 클릭을 유도하는 데 성공할 가능성이 높아요.

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

오늘날에도 '뱀파이어 생존자' 장르 중에서 새로운 게임들이 약간 다르고 조금 더 나은 점으로 성공을 거두고 있어요.

새로운 장르를 창조하는 것은 판매를 한다고 느껴질 수도 있지만, 플레이어들에게 자신의 게임을 이해하고 그 시간과 돈을 투자하는 가치가 있다고 설득하는 것은 어렵다는 문제가 있어요.

## 위대함은 부정할 수 없어요.

매주 스팀에서는 매출이 100만 달러 이상이 될 4개의 게임이 출시되어요. 그것은 연간 200개 이상이에요.

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

200 명 중 한 명이 될 수 있다고 믿나요? 제가 그렇게 생각해요. 그게 아니었다면 이 글을 읽고 있지 않으셨을 거예요.

위대한 게임은 부정할 수 없어요. 그것들은 피할 수 없어요. 그만큼 열려 있는 가능성이 크거든요.

당신의 게임에서 '출시' 버튼을 누르면, 만약 그 날 다른 27명이 출시한 게임보다 나은 퀄리티라면, 당신은 성공했다고 볼 수 있어요.

그렇지 않다면, 내일이 또 있을 거예요. 함께 힘내봐요!
