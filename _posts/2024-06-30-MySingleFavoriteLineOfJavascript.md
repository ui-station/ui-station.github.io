---
title: "내가 가장 좋아하는 자바스크립트 코드 한 줄"
description: ""
coverImage: "/assets/img/2024-06-30-MySingleFavoriteLineOfJavascript_0.png"
date: 2024-06-30 21:57
ogImage: 
  url: /assets/img/2024-06-30-MySingleFavoriteLineOfJavascript_0.png
tag: Tech
originalTitle: "My Single Favorite Line Of Javascript"
link: "https://medium.com/debugger-by-medium/my-single-favorite-line-of-javascript-304b2e9632ea"
---


![MySingleFavoriteLineOfJavascript_0](/assets/img/2024-06-30-MySingleFavoriteLineOfJavascript_0.png)

몇 년 전, Quora에서 누군가가 "지금까지 쓰여진 가장 강력한 코드 라인은 무엇인가?"라고 물었습니다.

대답들은 정말 재미있었고, 대부분은 예상한 것들이었습니다 (개발자라면요). 어떤 사람들은 while과 for 루프를 칭찬했는데, 컴퓨터가 반복 작업을 수행하도록 하는 중요한 구성 요소이며 "우리 생활 중 가장 지루한 부분을 처리해 주기 때문에 감사한 역할"이라고 말했습니다. 다른 개발자들은 컴퓨터가 말을 하게 만드는 print 문의 힘(컴퓨터 발화라고도 함), if-else 문(자동화된 의사 결정!), 또는 다른 사람의 오픈 소스 코드를 자동으로 흡수하고 나의 앱에서 사용할 수 있게 하는 import 명령에 대해 이야기했습니다.

확실히 멋진 선택들입니다. 그러나 저에게는 기쁨을 주는 한 줄의 명령어가 있습니다:

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

의사 난수 생성기! 대부분의 프로그래밍 언어에는 그런 것이 있습니다. 자주 사용하는 Javascript 나 Node에서 작성 중이라면, 이 함수는 ...

나는 이것을 존경스럽게 생각하며, 우연하게도 유용한 존재 중에서 가장 이상하고 마법 같은 코드라고 후보로 지명합니다.

![MySingleFavoriteLineOfJavascript_1](/assets/img/2024-06-30-MySingleFavoriteLineOfJavascript_1.png)

Math.random()은 마법 같은데, 왜냐하면 우리가 똑같이 형편없이 하는 게 내재하는 랜덤 숫자를 생성하기 때문이죠.

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

누구나 정확히 이것을 왜 이렇게 못하는지는 알지 못합니다. 아마도 너무 많이 과도하게 생각하기 때문이죠. 예를 들어, 사람들에게 1에서 20까지의 임의의 숫자를 고르라고 부탁하면, 그들은 대부분 17을 선택할 것입니다. 그 이유는 뭘까요? 아마도 홀수이자 소수이기 때문에 무작위로 느껴지기 때문이겠지만, 우리가 그 결론에 이르기까지 논리적/직관적으로 사고하기 때문에, 그 외에 어떠한 것도 아닙니다.

이것이 바로 왜 우리가 운명의 게임이 흔히 주사위와 같은 의사난수 생성 도구에 의존했는지, 컴퓨터가 현대의 주사위 던지기인 이유입니다. 한 줄의 코드로 필요한 만큼의 의사난수를 얻을 수 있죠.

그리고 'random' 명령어는 제게 매우 유용한 것으로 나타났습니다.

저는 특히 이 트위터 봇이 하루에 세 번 시를 생성하거나, "Weird Old Book Finder" 같은 이상한 오래된 책을 찾는 프로젝트와 같이 독특한 문화 프로젝트를 만드는 취미 코더입니다.

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

문화 앱을 만들 때, 무작위성은 창의적인 힘이 될 수 있어요. 

예를 들어, 제 트위터봇을 위해 하이쿠 모양의 시를 900줄 이상 직접 썼어요. 매일 세 번, 봇이 무작위로 세 줄을 골라서 합쳐요.

봇을 만드는 어려운 점은 그 모든 줄을 쓰는 것이었어요. 각 줄을 만들 때는 그것이 독립적으로 성립할 수 있을 뿐만 아니라 다른 줄과 쉽게 결합될 수 있도록 공들여야 했거든. 그것이 대부분의 작업이었습니다. 

![내가 가장 좋아하는 자바스크립트 한 줄](/assets/img/2024-06-30-MySingleFavoriteLineOfJavascript_2.png)

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

하지만 소프트웨어 측면에서는 얼마나 재미있고 복잡하지 않은 일인가요? 코드가 네 줄 밖에 안 되었어요. 단 한 줄의 Math.random() 명령어가 모든 일을 처리해 주는 거죠.

그런데 정말 대단한 건, 잘 작동한다는 거예요! 이 봇은 거의 다섯 년 동안 계속 트윗을 올리고 있고, Math.random()이 만들어내는 조합에 여전히 놀랍습니다. 제가 직접 모든 줄을 작성했음에도 왠지 모르게 이 조합들은 제 스스로 생각하지 못했을 거예요.

시와 예술에서 무작위성을 사용하는 것은 당연히 제 창작물이 아니에요. 이전 디지털 시대로 거슬러 올라가면 (예를 들면 OULIPO 시에서 명사를 대체하는 "N+7" 기법처럼), 계휴한 우렁찬 전통이 있었고, 컴퓨터 시대에 들어서면서 더욱 놀라운 발전을 이루었어요. 많은 비디오 게임에서 봐도, 종종 간단한 무작위성을 이용해서 게임을 끝없이 즐길 수 있게 만들어요. 수많은 창작자들이 계산되는 무작위성을 즐거워해요.

Matthew Siu의 말을 빌리자면...

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

하지만 Math.random()을 유용하게 활용하는 또 다른, 아마도 미묘한 방법이 있습니다: 결정을 내릴 때 사용하기.

구체적으로, '선택의 역설'을 극복하는 데 사용합니다.

이 이론은 우리가 매우 다양한 옵션을 제공 받을 때 결정하기가 더 어려워지며, 최종적으로 내린 결정에 덜 만족하게 됩니다. 이것은 흔치 않은 이론이며 최근 몇 년 동안 논쟁이 있었습니다. 그러나 제 개인적인 경험에는 맞다는 것 같습니다. 책방에 들어가면 "최근 출간된" 제목들의 거대한 배열을 보게 되면, 제 주의가 흐려지고 멀어지는 것을 느낍니다. 저는 책방 직원이 고른 10권의 작은 표를 선택하는 것이 훨씬 쉽다고 생각합니다.

그래서 "Weird Old Book Finder"를 만들 때, 선택의 역설을 우회하는 방법으로 무작위성을 사용했습니다. 사용자의 검색 질의를 받아 구글 도서 API에 전달하고, 결과가 반환되면 1920년대 중반 이전에 출판된 제목을 필터링하여 공공 도메인에 속하고 즉시 전문을 읽을 수 있는 책들을 찾습니다.

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

문제는 Google Books API가 최대 40개의 제목을 보내다는 것입니다. 너무 많아요. 선택 목록을 제공하고 싶지 않았어요 — 선택 광선을 유발할 수도 있으니까요!

그래서 그냥 사용자에게 돌려보낼 책을 랜덤하게 하나 선택해요.

또 다시, 이건 정말 간단한 기술이에요. 한 줄의 코드 뿐이에요. 하지만 이로 인해 검색 엔진이 재미있는 퀄리티를 얻게 해줘요. 동일한 검색어를 여러 번 실행해도 서로 다른 책을 얻어요. 이는 문화의 슬롯 머신이 되는거에요.

![MySingleFavoriteLineOfJavascript_3.png](/assets/img/2024-06-30-MySingleFavoriteLineOfJavascript_3.png)

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

Math.random()은 일반적으로 사람들이 되지 못하는 방식으로도 재미있는 요소를 제공합니다.

책 판매자나 사서에게 특정 주제의 책을 요청한다면 "음, 해당 주제의 섹션으로 가서 아무거나 가져와"라고 말하지 않을 텐데요. 이를 하기엔 너무 어색할 것입니다. 사람들은 의미 있는 행동과 의미창출을 선호합니다. 그들은 당신이 선택을 할 수 있도록 안내하는 것이 더 편할 것입니다.

하지만 Math.random()은 이런 어색함이 없죠. 그냥 아무거나 줄 수 있습니다.

그래서 제가 가장 강력한 코드 줄에 투표한 것입니다:

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

Math.random().

클라이브 톰슨은 주간 세 번의 게시물을 미디엄에 게시하며, 여러분께 이메일로 각 게시물을 받아보실 수 있습니다 - 그리고 미디엄 회원이 아닌 경우, 여기에서 가입할 수 있습니다.

클라이브는 뉴욕 타임스 매거진의 기고자이자, 와이어드와 스미스소니언 매거진의 칼럼니스트로 활동하며, Mother Jones에 정기 기고자로 활동하고 있습니다. 그는 Coders: The Making of a New Tribe and the Remaking of the World과 Smarter Than You Think: How Technology is Changing our Minds for the Better의 저자이기도 합니다. 트위터와 인스타그램에서는 @pomeranian99를 팔로우할 수 있습니다.