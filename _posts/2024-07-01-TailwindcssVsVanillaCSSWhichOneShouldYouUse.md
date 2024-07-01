---
title: "Tailwindcss vs Vanilla CSS 어떤 것을 사용해야 할까"
description: ""
coverImage: "/assets/img/2024-07-01-TailwindcssVsVanillaCSSWhichOneShouldYouUse_0.png"
date: 2024-07-01 16:26
ogImage: 
  url: /assets/img/2024-07-01-TailwindcssVsVanillaCSSWhichOneShouldYouUse_0.png
tag: Tech
originalTitle: "Tailwindcss Vs Vanilla CSS: Which One Should You Use"
link: "https://medium.com/@godwinemeribe23/tailwindcss-vs-vanilla-css-which-one-should-you-use-38e2ff7790bc"
---


<img src="/assets/img/2024-07-01-TailwindcssVsVanillaCSSWhichOneShouldYouUse_0.png" />

프론트엔드 개발의 세계는 단순한 HTML, CSS 및 JavaScript만 사용하는 시대를 넘어섰어요. 많은 멋진 도구와 기술이 개발되어 우리 개발자의 업무를 쉽게 해주었답니다. 어머나! ☺

Tailwindcss는 그 중 하나로, Tailwindcss팀이 "HTML을 떠나지 않고 현대적인 웹사이트를 빠르게 구축하는 데 사용되는 유틸리티 우선 CSS 프레임워크"로 설명하고 있어요.

이것은 단순히 Tailwindcss가 제공하는 매우 구체적이고 독특한 기능을 갖는 미리 정의된 클래스를 적용할 때, 빌드 프로세스를 거쳐 HTML 및 CSS 파일 사이를 번갈아가며 작업할 필요 없이 실제 CSS 코드로 변환된다는 뜻이에요.

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

좋아 보이네요. 근데 정말 그럴까요? 🤔

일부 개발자들에게는 Tailwindcss로 작성한 HTML이 CSS 클래스로 가득 찬 것처럼 보이고, 학습 곡선이 있다고 주장하는 사람도 있습니다. 그들이 틀리지 않은 건 맞아요!

하지만 다른 사람들은 저 포함해서 꽤 멋지다고 생각해요. 이것은 Tailwindcss가 HTML과 CSS 페이지 사이를 오가는 것을 우리에게 절약해 주는데 더불어 특정한 것에 가장 잘 맞는 CSS 이름을 생각하고 중복되지 않는지 확인하는 지루하고 피곤한 과정을 우리에게 해주기 때문입니다. 우크! 🤮

<img src="/assets/img/2024-07-01-TailwindcssVsVanillaCSSWhichOneShouldYouUse_1.png" />

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

예를 들어, React.js 프로젝트를 작업할 때는 tailwindcss를 사용하여 각 구성 요소를 별도로 스타일링하고 CSS 클래스 이름을 고민하는 데 소비되는 시간을 낭비하지 않아도 됩니다.🙂

React.js는 구성 요소 중심의 성격 때문에 좋아하는 라이브러리입니다. 이를 통해 구성 요소를 재사용하고 코드를 중복하지 않을 수 있습니다. 'DRY' 원칙을 기억하시나요? React.js는 많은 사랑을 받으며, 신입 기술 업계의 개발자들이 실제 프로젝트에서 경험을 쌓는 인기 있는 HNG 인턴십 프로그램에서 사용되는 프런트엔드 라이브러리로 활약하고 있습니다.

Tailwindcss를 좋아하는 이유 중 하나는 모바일 우선이라는 점입니다. 먼저 작은 화면에 대한 디자인을 고려하게 됩니다. 또한 클래스 이름과 접두사만으로 서로 다른 미디어 쿼리에 대한 스타일을 작성할 수 있습니다. 스타일 작성이 더 나아질 수 있는 방법일까요?

그러나 이 모든 것 중에서도 가장 좋은 점은 tailwindcss와 바닐라 css가 프로젝트에서 상호 배타적이지 않다는 것입니다.

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

예를 들어, 저는 순수 CSS로 애니메이션을 다루는 것이 더 편하다고 느껴서 CSS 파일로 이동하여 애니메이션에 해당하는 스타일을 작성하지만, 나머지 스타일은 Tailwindcss로 처리하도록 남겨둡니다.

그래서 제가 HNG의 프론트엔드 개발 인턴들에게 주는 조언은, 편한 도구를 사용하는 것입니다. 일이 효율적이고 적절하게 처리된다면 어떤 도구를 사용하든 상관없습니다. 🤝