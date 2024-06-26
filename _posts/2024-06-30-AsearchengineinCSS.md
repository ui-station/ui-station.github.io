---
title: "CSS만으로 검색 엔진 만들기 방법"
description: ""
coverImage: "/assets/img/2024-06-30-AsearchengineinCSS_0.png"
date: 2024-06-30 22:36
ogImage: 
  url: /assets/img/2024-06-30-AsearchengineinCSS_0.png
tag: Tech
originalTitle: "A search engine in CSS"
link: "https://medium.com/algolia-stories/a-search-engine-in-css-b5ec4e902e97"
---


작년 11월에 dotCSS에서 발표하는 기회와 즐거움을 가졌어요. 모든 dotConferences는 고품질이지만 dotCSS가 제가 가장 좋아하는 행사에요. 거기서 본 강연들로 매년 영감을 받아왔어요. 대규모 청중들과 함께 무대에 서서 내 지식을 공유하는 것은 매우 즐거운 경험이었어요.

# 4월 만우절 장난

제가 한 발표는 지난 4월에 Algolia에서 한 만우절 장난의 기술적 설명이었어요. 4월 1일에 우리는 우리의 새로운 CSS API 클라이언트를 공개했고, 우리의 API 서버를 호출하지 않는 첫 번째 API 클라이언트를 발명했다고 발표했어요.

이건 물론 장난이었지만, 우리는 사람들이 우리가 농담을 하는지 진지한 것인지 궁금해하기 시작할 만큼 매력적인 환상을 만들려고 노력했어요. 그 노력의 중심에는 실시간 데모가 있었어요.

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


![Image](https://miro.medium.com/v2/resize:fit:1400/1*FGZ2GoRtZPVMnuvsqLBF-A.gif)

# 해킹 정신

동료들과 함께 친근한 챌린지를 시작했는데, CSS로 검색 엔진의 기능을 모방하는데 얼마나 나아갈 수 있는지 보기 위한 것이었습니다. 그 결과로 실제로 작동하는 것을 얻을 수 있었어요!

전체 데모는 해킹 정신으로 이루어졌습니다. 여기서 해킹은 취약점을 찾는 행위가 아니라, 시스템의 제한을 극복하여 새로운 결과를 얻는 행위로서 이루어졌습니다. 이 경우에는 시스템과 그 제한이 CSS이었고, 새로운 결과는 검색 엔진을 구축하는 것이었습니다.


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

만약 우리가 검색 엔진의 본질에 다가간다면, 그저 키워드를 입력으로 기대하고 결과를 출력으로 제공하는 단순한 기계일 뿐이라는 것을 빨리 깨닫게 될 것입니다. 기계 안에서 어떤 일이 일어나느냐에 따라 검색 경험이 좋거나 평범해질 것입니다.

물론 '그저 평범한' 검색 경험에 만족할 수 없었습니다. 좋은 검색을 이루는 세 가지 요소인 관련성, 속도 및 사용자 경험을 갖추어야 한다는 사실을 알고 있었습니다. 데모 결과에 대해 만족스러웠으며, 이 세 가지 요소를 충족시켰다고 생각했습니다. 물론 아직 향상될 여지는 있지만, CSS 해킹에 대해 꽤 우직한 결과라고 생각합니다.

# CSS의 한계 극복하기

CSS는 스타일링 언어이기 때문에 그 구성 요소는 고전적인 프로그래밍 언어와 다릅니다. Ruby, PHP, C++ 또는 JavaScript를 살펴보면 변수, 함수, 루프, 조건문 및 정규 표현식이 언어의 핵심 구성 요소로 있는 것을 확인할 수 있습니다.

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

CSS에는 그런 것이 없거나 적어도 그런 것이 다른 언어들처럼 표준으로 존재하지 않습니다. 검색 엔진을 만들기 시작할 때 변수, 반복문 및 정규 표현식이 필수적이라고 생각할 수 있습니다. 그리고 만약 언어가 이러한 기능을 제공하지 못한다면, 언어로는 검색 엔진을 만들 수 없을 것이라고 생각할 수도 있습니다.

저는 실제로 CSS로 검색 엔진을 만들고 싶었기 때문에 이것이 저를 막게 두지 않으려 했습니다. 이것을 수행할 수 없다고 생각하며 프로젝트를 시작할 수 없었습니다. 그래서 CSS가 할 수 없는 것에 초점을 맞추지 않고, 오히려 제대로 수행할 수 있는 부분에 집중하기로 결정했습니다.

빠르게 깨달은 것은 CSS의 주요 강점이 선택자 엔진에 있다는 것입니다. CSS는 요소를 타겟팅하기 위해 태그 이름, 클래스 이름, 아이디, 속성 값 또는 심지어 마크업에서 조상 또는 형제 요소를 사용할 수 있습니다. 그리고 무엇보다, 이러한 선택자를 조합하여 매우 정확한 것을 만들 수 있습니다.

CSS는 스스로 작동할 수 없습니다. 항상 CSS를 적용할 HTML이 필요합니다. 그러나 HTML이 가장 지저분하고 의미가 없는 파일이더라도, 원하는 것을 정확하게 타겟팅할 수 있는 완벽한 CSS 선택자를 만들 수 있습니다. HTML에 수많은 잠재적이고 관련없는 요소가 있더라도, CSS는 여전히 당신이 관심 있는 요소만 타겟팅할 수 있습니다.

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

크고 다양한 선택지에서 관련 항목만 선택할 수 있는 능력은 매우 중요합니다. 이것이 바로 검색 엔진에서 기대하는 바와 일치합니다.

![검색 엔진 이미지](/assets/img/2024-06-30-AsearchengineinCSS_0.png)

검색 엔진을 사용하면 많은 결과가 나올 수 있지만, 단어와 관련된 것만 관심이 있습니다.

이것을 깨달았을 때, 무언가를 알아냈다고 느꼈습니다. CSS의 강점을 검색 엔진의 원하는 결과와 일치시킬 수 있다면, 이 실제 데모를 만들 수 있을 것이라고 생각했습니다.

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

# 작은 작업부터 마크업 제작을 시작해 보세요

전체 해킹은 매우 간단한 마크업을 기반으로 합니다. 필요한 것은 `input` (검색 바 역할)과 비어 있는 `div` (결과를 보유할 것) 뿐입니다:

`input[value=”tim” i]`를 선택기로 사용하면 실제로 현재 값에 따라 input을 선택할 수 있습니다. 여기서 ` i`를 사용하여 대소문자를 구분하지 않는 것을 지정합니다.

거기에 `~ #result`를 선택기에 추가할 수도 있습니다. 이렇게 하면 이제 입력란 뒤에 위치한 ``div id=”result”``를 선택합니다. 이제 비어 있는 div를 대상으로할 수 있으므로 `:before` 의사요소와 일부 `content`를 사용하여 스타일과 내용을 완전히 변경할 수 있습니다.

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

최종 CSS 선택자는 이렇게 생겼어요:

여기까지 오면 페이지의 어딘가에 있는 입력 요소의 값에 따라, 다른 완전히 관련없는 요소의 내용과 스타일을 변경할 수 있는 CSS 선택자가 생기게 됩니다.

이 긴 선택자는 전체 해킹의 중심이지만 작은 단점이 있어요: 실제로 잘 작동하지 않아요.

# JavaScript가 해결책이에요

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

처음 페이지를 로드할 때 검색 창이 실제로 비어 있기 때문에 그렇게 동작하지 않습니다. 따라서 HTML의 `value` 속성은 빈 문자열로 설정됩니다.

입력란에 무언가를 입력하기 시작하면 `input` 요소의 HTML `value` 속성이 업데이트되지 않습니다. 입력란에 입력하는 것은 동적 값만 업데이트하며, 마크업에 있는 정적 값은 업데이트되지 않습니다. 정말 안타까운 일이지요. 왜냐하면 CSS가 읽는 것이 바로 이 정적 값이기 때문이죠.

그 시점에서, Enter를 눌러 간단히 양식을 제출할 수 있었을 겁니다. 서버 측에서 양식 값을 가로채어 마크업에 다시 작성하고 페이지를 다시 렌더링했을 텐데요. 동작은 되었겠지만 빠르지 않았을 겁니다. 그리고 저는 정말 즉각적인 결과를 원했기 때문에 JavaScript를 사용해야 했습니다.

JavaScript를 사용하지 않고 순수 CSS 솔루션을 유지하고 싶었지만 방법을 찾지 못했습니다. 만약 여러분 중에 JavaScript를 사용하지 않는 방법을 아시는 분이 계시다면 알려주세요!

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

어쨌든, 제가 추가한 JavaScript는 정말 최소한이었어요. 입력란에 간단한 `oninput` 핸들러가 있어요. 이 핸들러는 동적 값읽고 이를 HTML `value` 속성에 설정할 거예요. 입력이 변할 때마다 트리거되어, 내가 원하는 대로 즉각적인 결과를 얻을 수 있도록 해줄 거에요.

# 여러 결과

지금까지 내가 가진 마크업은 올바른 키워드를 입력할 때마다 한 결과만 표시할 수 있어요. 전부 일치하는 결과를 표시하고 싶어요. 예를 들어, "Alexandre"를 검색하면 회사에 "Alexandre"라는 이름을 가진 모든 사람을 표시하고 싶어요.

그것을 위해, 마크업을 약간 수정하면 돼요. 결과를 보유할 빈 `div` 하나 대신 150개의 빈 `div`를 만들어요. 잠재적 결과 하나당 하나씩, 즉 하나당 직원 한 명씩 만들면 돼요.

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

또한, 각 `div`에 직원의 이름을 미리 채워 넣을 것입니다. 여기서도 `:before`와 `content` 기법을 사용할 것이지만, 이러한 `div`들을 실제로 표시하지는 않을 거에요. 기본적으로 모든 `div`는 `display: none`을 가지게 될 거에요.

특정한 상황에서만 표시할 겁니다. 일치하는 키워드가 입력될 때만 해당 `div`의 `display`를 `block`으로 변경할 것이에요.

이제 `Alexandre`를 입력할 때, 실제로 `#result15`, `#result16`, 그리고 `#result17`이 표시 될 거에요. 나머지는 숨겨져 있을 거에요.

# 검색 엔진 이해하기

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

그 시점에서 난 이미 결과에 대해 꽤 만족했어요. 입력한 키워드에 기반한 결과를 표시하는 방법을 알았거든. 이제 질문은 "어떤 키워드가 결과를 내야 하는가?"로 바뀌었죠.

내가 그 문제에 도달했을 때, 나는 이미 크게 만족한 상태였어요. 입력한 키워드에 기반한 결과를 표시하는 방법을 알고 있었거든. 이제 질문은 "어떤 키워드가 결과를 내야 하는가?"로 전환되었어요.

그때, 나는 프로젝트에서 한 걸음 물러나서 내가 무엇을 달성하려고 하는 지 정말로 이해하려고 결정했어요. 지금까지 나는 대부분 영리한 CSS 해킹을 사용하여 도전에 대처했었거든. 그러나 더 나아가고 싶다면, 내가 무엇을 구축하고 있는지 실제로 더 잘 이해해야 했어요.

다행히도, Algolia에서 일하면서 하루 종일 검색 엔진을 사용하고 구축하는 사람들에게 둘러싸여 있어요. 몇몇 동료와 함께 앉아서 검색 엔진이 실제로 어떻게 작동하는지 설명해 주었어요.

Algolia, ElasticSearch 또는 Solr와 같은 모든 검색 엔진은 두 부분으로 구성되어 있어요: 인덱싱과 검색이에요.

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

검색은 가장 명백할 수도 있습니다. 검색 바를 사용할 때마다 우리가 직면하는 것입니다. 검색 바에 무언가를 입력할 때마다 우리는 내적으로 "제가 `alex`를 입력하면 무엇을 찾을까?"라는 질문을 하게 됩니다.

하지만 실제로 검색 엔진을 구축할 때는 매우 다른 집합의 질문을 해야 합니다. 모든 잠재적인 결과를 고려하고 "사용자가 `Alexandre Meunier`를 찾기 위해 무엇을 입력해야 할까?"라고 스스로 물어보셔야 합니다.

![이미지](/assets/img/2024-06-30-AsearchengineinCSS_1.png)

그래서 제가 이 직원 목록을 가져와보았습니다. 그들을 찾기 위해 무엇을 입력해야 하는지 알아보고 싶었습니다. `alex`라고 입력하면 Alexandre라는 이름을 가진 모든 사람을 찾고 싶었습니다. 사실, `alex`는 Alexander, Alexandra 또는 Alexandria 같이 이름이 있는 사람들도 찾아야 합니다...

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

더 나아가보면 좋겠죠. `ale`, `al` 또는 `a`만 입력하면 'alex'라는 이름을 가진 모든 사람을 찾을 수 있어야 해요. 또한 `alexandr`을 입력하면 Alexandre, Alexandra 및 Alexandria도 모두 찾을 수 있어야 해요.

그러나 성과 직급으로도 사람들을 찾고 싶었어요...

# n-그램 구축

내가 한 결정은 직원 목록에서 결과로 이끌 수 있는 모든 잠재적 n-그램을 생성한 거에요. n-그램은 결과로 이끌 수 있는 문자 시퀀스를 나타내요. 예를 들어 `t`, `ti`, `tim`은 모두 `Tim`을 찾을 수 있는 n-그램이에요.

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

물론, 수작업으로 그것을 하지 않았어요 — 문자열에서 n-그램을 생성하는 데 도움을 주는 루비 스크립트를 작성했어요. 이 스크립트를 직원 이름에 적용하고 그 결과를 사용하여 다음과 같이 보이는 긴 CSS 선택기 목록을 작성했어요:

![CSS Selector](/assets/img/2024-06-30-AsearchengineinCSS_2.png)

이런 식으로 선택기를 사용하면 어떤 성을 입력해도 일치하는 결과가 표시됩니다. 매우 상세하지만 실제로 작동해요.

생성된 최종 CSS는 매우 길어요. 150명의 모든 직원의 이름, 성, 직책에 대해 이 작업을 수행해야 하기 때문이에요. 이 단계에서 더 많은 기능을 추가하기 시작해요. 해당 해킹을 사용하기 쉽게 만들고자 악센트 문자, 동의어 및 일부(제한된) 오탈자 허용 기능도 추가했어요.

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

# 디스플레이 개선

마지막으로 추가한 세부 정보는 검색 엔진을 더 쉽게 사용할 수 있도록 만들었습니다. 'order' 속성을 사용하여 결과를 정렬했습니다. 관련된 결과를 함께 그룹화했습니다: 이름이 일치하는 경우 성과 일치하는 경우보다 더 높은 순위로 표시됩니다.

또한 입력한 키워드가 결과에서 굵게 표시되도록 강조 기능을 추가했습니다. 강조 기능은 종종 간과되는 검색 엔진의 중요한 부분입니다. 이는 사용자에게 해당 결과가 포함되어 있는 이유를 설명하는 방법입니다.

여기서 어려운 점은 모든 콘텐츠를 ':before'와 'content'를 통해 추가했기 때문에 HTML이 없이 순수한 텍스트만 CSS에 이미 포함되어 있었다는 것입니다.

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

특정 부분을 굵게 표시하려면 조금 교묘하게 해야 했어요. 일반 글ꔿ과 굵은 글ꔿ을 합쳐 새로운 폰트를 만들었죠. 모든 굵은 글리프를 UTF8의 사설 사용 영역에 넣었어요. 이 부분은 어떤 글리프든 넣을 수 있는 특정한 이름 공간이에요. 결과적으로 저에게는 일반 문자의 굵은 버전과 똑같이 보이는 글리프 세트가 생겼어요. 그리주 우스꽃 코드 포인트를 통해 접근할 수 있어요.

![이미지](/assets/img/2024-06-30-AsearchengineinCSS_3.png)

필요할 때 일반 글리프를 그들의 굵은 버전으로 교체하기만 하면 돼요. CSS는 완전히 해독할 수 없게 되는데 잘 작동돼요. 다만 이 방법을 이전에 생성된 각 n-gram에 대해 해야 하기에 CSS 파일 크기가 더욱 커지는 단점이 있어요.

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

최종 CSS 파일은 8MB였는데, minify를 하여 5MB로 줄였어요. 그래도 여전히 너무 많아요. 그래서 누구에게나 이를 제작용으로 사용하지 말아 당부드릴게요!

프로젝트 전체가 처음에는 미친 것처럼 또는 불가능한 것처럼 보일 수도 있었지만, 제가 항상 말하듯이:

![AsearchengineinCSS](/assets/img/2024-06-30-AsearchengineinCSS_4.png)

이처럼 이러한 프로젝트를 만들면서 배운 한 가지는, 언어를 사용하여 의도되지 않은 일을 할 때 놀라운 학습 경험이 된다는 것이에요. CSS의 강점과 약점에 대해 많이 배웠고, 그 한계까지 밀어내면서 많은 것을 배우게 되었어요.

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

저절로 만들다세요, CSS(또는 어떤 다른 언어든지)로 열정을 쏟아붓는 것을 권장하고 싶네요. 이런 시도를 통해 해당 언어를 사용하는 가장 좋은 방법을 명확히 이해할 수 있을 거에요.

몇몇 분들에게 영감을 줄 수 있었다면 좋겠고, 여러분이 무엇을 만들 것인지 궁금하네요 — 언제든 댓글이나 트윗을 남겨주세요!