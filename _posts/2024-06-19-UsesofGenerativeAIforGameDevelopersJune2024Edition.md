---
title: "게임 개발자를 위한 생성적 AI의 활용 방안 2024년 6월판"
description: ""
coverImage: "/assets/img/2024-06-19-UsesofGenerativeAIforGameDevelopersJune2024Edition_0.png"
date: 2024-06-19 11:53
ogImage:
  url: /assets/img/2024-06-19-UsesofGenerativeAIforGameDevelopersJune2024Edition_0.png
tag: Tech
originalTitle: "Uses of Generative AI for Game Developers (June 2024 Edition)"
link: "https://medium.com/@Glimnote/uses-of-generative-ai-for-game-developers-june-2024-edition-c264e652a290"
---

![image](/assets/img/2024-06-19-UsesofGenerativeAIforGameDevelopersJune2024Edition_0.png)

제너레이티브 AI는 빠르게 발전하고 있고 끊임없이 헤드라인을 내지만, 게임 개발자가 generative AI를 사용하는 것이 정말로 유익한가요? 저희는 2022년부터 이 분야에서의 경험을 기반으로 이 질문에 대한 저희의 견해를 요약했습니다.

\*2024년 6월을 기준으로 한 저희의 관점임을 유의해 주세요. 상황은 계속 변화 중입니다.

# 주요 포인트 요약

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

바쁜 독자들을 위해 먼저 결론을 알려드립니다:

- 생성적 AI의 가장 효과적인 사용은 게임 기획 단계에 있습니다.
- 다음으로 효과적인 영역은 마케팅을 위한 콘텐츠 생성입니다.

![이미지](/assets/img/2024-06-19-GenerativeAI를이용한게임개발자용도2024년6월판_1.png)

반면, 게임에 직접 구현할 수 있는 자산을 생성하는 것은 가장 큰 기대치를 가졌을 수 있지만, 현재 일관성 없는 품질 및 다른 편집 과정과 같은 어려움에 직면하고 있습니다. 이로 인해 이분야 전문가들은 종종 불편함을 느끼며, 현재는 특정 목적으로만 한정적으로 사용되고 있습니다.

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

물론 창의력을 발휘하여 제너레이티브 AI를 제품 개발에 활용할 수 있습니다. 예산 제약, 프로젝트 규모, 혹은 전문분야 외의 영역을 보충하는 데 있어 가치를 제공할 수 있습니다.

위치팟 주식회사에서는 우리가 상담하는 게임 스튜디오와 개발자들에게 기술을 따라잡는 동안 기획 및 마케팅 단계에서 제너레이티브 AI를 활용할 것을 권장합니다. 게임 개발에 대한 AI 도구의 맞춤화부터 상담 서비스까지 다양한 서비스를 제공하고 있으니 언제든지 연락 주세요. (https://www.witchpot.com/contact)

# 전제

게임에서 제너레이티브 AI의 적용을 고려할 때에는 두 가지 주요 접근 방식이 있습니다:

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

- 기존 게임 콘텐츠의 효율성 향상을 위해 AI 활용 주로
- 전통적인 방법과는 다른 새로운 게임 경험 창출을 위해 AI 활용

이 글에서는 전자 방법에 초점을 맞출 것입니다. 후자인 새로운 게임 경험 창출 방법은 다루지 않겠지만, 수요가 있을 경우 별도로 논의할 수 있습니다.

# 생성 AI의 현재 상태 및 과제

게임 분야는 방대하기 때문에 이 글에서는 모든 것을 다 다루지는 않을 것입니다. 그러나 우리는 공개적으로 사용 가능한 시장 맵이 있습니다.

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

여기서는 이미지 및 텍스트 생성의 현재 상태와 도전 과제에 대해 이야기하겠습니다.

이미지 생성의 현재 상태 및 도전 과제

현재 이미지 생성은 특정 캐릭터의 다양한 상황에서 이미지를 생성할 수 있으며, 해당 캐릭터가 동일하다고 인식될 수 있는 수준까지 만들어낼 수 있습니다. 또한 적절한 조정을 통해 이미 존재하는 콘텐츠의 캐릭터에 대해서도 이 작업을 수행할 수 있습니다.

![이미지](/assets/img/2024-06-19-UsesofGenerativeAIforGameDevelopersJune2024Edition_2.png)

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

그러나 도전 과제들이 있습니다. 예를 들어, 문자의 비율이나 몸의 형태에 약간의 차이가 발생할 수 있습니다. 유명한 사례로는 캐릭터들이 여섯 손가락으로 나오는 경우 등이 있습니다. 이러한 문제들은 사람들이 그릴 때는 발생하지 않지만 수정이 필요합니다.

AI로 생성된 이미지는 층으로 분리되지 않고 하나의 이미지로 생성됩니다. 따라서 이미 그림을 잘 그리는 사람들에게는 세부적인 수정이 번거로울 수 있습니다.

텍스트 생성의 현재 상황과 도전 과제

이 비디오에서는 캐릭터의 대화가 생성됩니다.

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

또한, 텍스트 생성은 이야기, 캐릭터 설정, 플레이버 텍스트 및 태그 라인을 작성하는 데 사용될 수 있어 그 적용 범위가 매우 광범위합니다.

여기서 우리는 텍스트 생성을 위해 Glimnote를 사용하지만, ChatGPT와 같은 도구도 올바른 사용법으로 캐릭터의 어조와 성격에 맞는 대화를 생성할 수 있습니다.

그러나, 여전히 일부 어려움이 남아 있습니다. 예를 들어 생성된 텍스트는 종종 지나치게 과장되거나 과도한 찬사를 포함할 수 있습니다. 우리는 개선을 위해 노력하고 있지만, 피드백에 따르면 이러한 텍스트를 그대로 사용하는 것이 여전히 어렵다는 것을 보여줍니다.

이러한 상황에서 많은 기존 게임 개발 팀은 특정 영역마다 전문가를 보유한 특히 AI 도구의 출력이 미흡하고 진화를 기다리는 것으로 발견되었을 때 많은 어려움을 겪고 있습니다.

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

# 기획 단계에서 AI 활용하기

AI 도구의 장점은 그것들의 압도적인 속도와 양이에 있습니다. 새로운 콘텐츠를 기획하는 아이디어 생성 단계에서, 이러한 장점은 상기한 도전적인 부분들보다 상당한 혜택을 제공할 수 있습니다.

![image](/assets/img/2024-06-19-UsesofGenerativeAIforGameDevelopersJune2024Edition_3.png)

실제로 AI는 팀 내에서 빠르게 프로토타입과 초안을 생성함으로써 "우리가 만들고 싶은 것"에 대한 의사 소통을 가속화하는 데 점점 더 활용되고 있습니다.

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

예를 들어, 팀이 새 캐릭터를 만들고 싶다면, 텍스트만 사용하여 세계 설정에 맞는 20개의 새 캐릭터를 몇 분 만에 생성할 수 있습니다. 도구에 익숙해지면, 이 20개 캐릭터에 대한 의도한 예술 스타일의 시각적 이미지도 몇 분에서 몇 시간만에 생성할 수 있습니다.

필요한 시간은 원하는 품질 및 프롬프트 제작 전문성에 따라 다릅니다.

개발팀 내에서 이 20개 캐릭터 프로토타입을 공유하면 종종 어떤 캐릭터를 추가로 발전시킬지에 대한 열정적인 토론이 이루어지며, 더 좋은 프로젝트와 원활한 커뮤니케이션으로 이어집니다.

캐릭터 설정에만 국한되지 않습니다. 예를 들어, 효과음을 명시할 때, 텍스트 설명은 중요하지만, 효과음 AI가 생성한 프로토타입을 공유함으로써 정보 전달이 현저하게 향상됩니다.

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

# 마케팅 콘텐츠 작성

이미지 생성 환경을 구축하고 능숙하게 사용할 수 있는 환경을 구축 한 후에는 게임 내에서 직접 사용하기 어려운 자산이더라도 여전히 소셜 미디어에 자주 게시할 만한 충분한 마케팅 콘텐츠를 만들 수 있습니다.

여기에는 저희 Witchpot이 협력한 SQUADBLAST 게임을 위해 AI를 사용하여 작성된 팬 아트 콘텐츠의 예가 있습니다.

![팬 아트](/assets/img/2024-06-19-UsesofGenerativeAIforGameDevelopersJune2024Edition_4.png)

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

AI 도구를 활용하여 PR 콘텐츠를 제작하면 높은 빈도의 게시물을 유지하여 청중을 계속해서 참여시키고 게임에 대해 정보를 제공할 수 있습니다.

실제 캐릭터 이미지를 생성할 수 있는 환경을 만들 수 있으며, 이를 제한함으로써 팬 아트를 만들 수 있는 환경을 제공할 수 있습니다. 또한 이를 활용하여 커뮤니티를 형성할 수도 있습니다.

# 결론

게임 개발자들이 현재 AI 도구를 기획 및 마케팅 콘텐츠 제작 분야에서 어떻게 활용하고 있는지에 대해 설명했습니다.

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

이러한 분야에서 의도한 결과물을 생성하는 기술을 개발함으로써, 우리는 AI의 혜택을 누리고 가치 있는 통찰을 축적할 수 있습니다. 이를 통해 게임 자산 생성에 AI를 활용하는 것을 권장하며, 수많고 매혹적인 게임을 출시할 수 있게 됩니다.

저희는 계속해서 이 분야의 정보를 공유할 것이므로, 부디 팔로우해 주세요!

무엇이든 자유롭게 저희에게 질문해 주세요!
