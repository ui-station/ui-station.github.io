---
title: "10 분 만에 Google Dork를 사용하여 NASA를 해킹한 방법"
description: ""
coverImage: "/assets/img/2024-05-20-HowIHackedNASAUsingGoogleDorkinJust10Minutes_0.png"
date: 2024-05-20 21:22
ogImage:
  url: /assets/img/2024-05-20-HowIHackedNASAUsingGoogleDorkinJust10Minutes_0.png
tag: Tech
originalTitle: "How I Hacked NASA Using Google Dork in Just 10 Minutes"
link: "https://medium.com/@gaurish.main/how-i-hacked-nasa-using-google-dork-in-just-10-minutes-6ce3b3401512"
---

안녕, 보안 애호가 여러분!👋

난 고리쉬야, 버그 사냥 세계의 초보자야. 그리고 이제 뭘 알아? 최근 NASA 웹사이트에서 개인 식별 정보 노출 취약점을 발견했어. 이 글은 내 여정을 공유하고 여러분 중 일부에게 도움이 되기를 희망하는 내 작은 노력이야. 그래서, 준비되고 몇몇 웃음 (아마도 약간의 당황)을 준비해보고 나의 첫 중요한 발견까지 어떻게 했는지 알려줄게.

겸손한 시작들✨

자, 솔직하게 말하자면. 신참으로서 버그 사냥에 뛰어들 때는 기어가기도 전에 수영을 배우려는 것 같은 느낌일 수 있어. 그렇지만 당신이 프로처럼 느끼게 해주는 하나의 도구가 있어: Google Dorking. 미숙한 사람들을 위한, Google Dorking은 구글 검색을 스테로이드를 맞은 것과 같아 - 몇 가지 똑똑한 검색 쿼리로 민감한 정보의 보물을 발견할 수 있어.

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

초기 탐사🚀

이 분야에 새로운 입문자로서, NASA를 해킹하는 꿈이 조금 과대한 것 같다는 것을 알고 있었지만, 누가 크게 꿈을 꾸지 않겠어요? 버그 사냥은 보통 많은 인내와 튼튼한 기초 지식이 필요한데, 특히 NASA와 같이 거대한 기관을 대상으로 할 때는 더 그렇죠. 그래도 낮은 수준의 버그 사냥 팁 몇 가지를 갖고 미루어 본 결과, 직접 뛰어들어 보기로 결심했습니다. 똑똑한 선택이었나요? 아마 그렇지 않았을 겁니다. 즐거웠나요? 절대로요.

Google Dorking을 이용해 NASA 정찰 임무를 시작했습니다. Google Dorking이란 해킹의 저격수 소총과 같은 것으로 생각하면 되요 - 다재다능하고 놀랍도록 강력한 도구입니다.

여기에 제가 사용한 초기 도크가 있어요:

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

intitle:"index of /" site:nasa.gov

이 쿼리는 NASA의 도메인에서 디렉토리 목록을 찾는 데 사용됩니다. 놀랍게도, 100개 이상의 디렉토리 목록을 발견했습니다. 탐험할 잠재적 취약성이 많이 남아 있네요!

검색 범위 축소하기🧐

너무 많은 디렉토리가 있어서 소음을 걸러내는 방법이 필요했어요. 특정 키워드에 집중하기로 결정했습니다. 먼저 데이터베이스를 찾아보는 것으로 시작해보았습니다:

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

지난 NASA의 숨겨진 데이터베이스를 발견하는 꿈은 실패했습니다 - 아무것도 나오지 않았죠. 다음으로, 어드민 페이지를 찾아보려고 시도해 보았습니다.

intitle:"index of /" "admin" site:nasa.gov

여전히 운이 없었어요. 이 시점에서 저는 우주가 저를 조소하고 있다고 생각하기 시작했습니다. 그런데 그때 갑자기 뇌피셜이 번졌어요: PII (개인 식별 정보). 방법을 바꿔서 연락처를 찾아보았습니다:

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

intitle:"index of /" "contact" site:nasa.gov

## 대박!🤯

그곳에 있었지요 - nasa의 서브도메인에 contacts.asc라는 파일이 있었습니다. 제 마음은 두근두근하였고, 파일을 클릭하여 내 시스템으로 다운로드 받았습니다. 그 안에는 황금같은 민감한 정보가 포함된 120명 이상의 화성 패스파인더 미션 관련 직원들의 이름, 이메일, 전화번호, 팩스번호, 주소 등이 있었습니다.

<img src="/assets/img/2024-05-20-HowIHackedNASAUsingGoogleDorkinJust10Minutes_0.png" />

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

버그 신고

나는 시간을 낭비하지 않고 문제를 Bugcrowd를 통해 신고했습니다. 4시간이라는 고통받는 대기 후, 응답을 받았어요: 문제가 재현되었고 P3 심각도로 처리되었습니다. 다음 날 새벽 1시쯤에 내가 기대했던 확인을 받았어요 - 내 문제가 검증되었고, NASA로부터 첫 번째 명예의 전당 언급을 획득했습니다.

얻은 교훈

여기 내 모험으로부터 얻은 몇 가지 교훈이 있습니다:

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

1. 끈기가 있으면 결실을 맺습니다: 처음 실패에 실망하지 마세요. 다양한 키워드와 방법으로 실험을 계속해보세요.

2. 구글 도킹은 강력합니다: 초심자들에게 특히 효과적인 재정 방법이지만 과소평가되고 있습니다.

3. 책임있게 보고하세요: 무언가를 발견하면 적절한 경로를 통해 보고하세요. 윤리적 해킹은 인터넷을 보다 안전한 곳으로 만들기 위한 것입니다.

종합적으로

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

버그를 찾는 것은 나와 같은 초보자에게도 흥미로운 경험이 될 수 있어요. 만약 나가다가 NASA에서 중요한 버그를 우연히 발견할 수 있다면, 당신도 할 수 있어요. 그러니 나가서 탐험해보고, 버그들이 당신과 함께하기를 바라요!

모두 행복한 해킹 되세요!

의견이나 경험을 공유하셔도 좋아요. 함께 배우고 웃을 수 있기를 기대해요!

제 LinkedIn과 연락할 수 있어요.

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

해당 글은 순수히 교육 목적으로 작성되었습니다. 항상 윤리적 가이드라인을 준수하고 취약점을 책임있게 보고하십시오.

이 여정을 저만큼 즐겁게 보내셨길 바랍니다. 다음에 또 만나요. 호기심을 가지고 지속적으로 노력해 주세요!
