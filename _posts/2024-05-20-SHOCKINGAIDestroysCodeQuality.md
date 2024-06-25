---
title: "글 제목 스타일로 번역하면 충격 AI가 코드 품질을 파괴한다 입니다"
description: ""
coverImage: "/assets/img/2024-05-20-SHOCKINGAIDestroysCodeQuality_0.png"
date: 2024-05-20 16:31
ogImage:
  url: /assets/img/2024-05-20-SHOCKINGAIDestroysCodeQuality_0.png
tag: Tech
originalTitle: "SHOCKING🤯. AI Destroys Code Quality"
link: "https://medium.com/@tsecretdeveloper/shocking-ai-destroys-code-quality-bd0ae50624e2"
---

![이미지](/assets/img/2024-05-20-SHOCKINGAIDestroysCodeQuality_0.png)

소프트웨어 엔지니어링에서 가장 큰 적은 기술 부채가 아닙니다. 코드 회전율이죠. 코드가 작성된 직후에 바로 변경되는 것입니다. 이는 문제의 지표뿐만 아니라 문제의 원인이 될 수도 있습니다.

따라서 이 그래프를 보면 무언가 잘못되었음을 알 수 있습니다. 알아요 무엇 인가요? AI입니다. AI가 우리의 코드를 파괴하고 있어요. 조금 더 이야기를 나누어서 우리가 할 수 있는 대책을 찾아봐요.

![이미지](/assets/img/2024-05-20-SHOCKINGAIDestroysCodeQuality_1.png)

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

# 증거를 살펴봅시다

GitClear는 AI 코드 생성 도구가 코드 품질에 어떤 영향을 미쳤는지에 대한 백서를 공개했습니다. 그 영향이 좋지 않다는 것을 알아야 합니다 (그들은 1억 5천만 줄 이상의 코드를 바탕으로 판단하고 있습니다).

그들이 발견한 급증하는 코드 변동은 심각한 문제를 가리킵니다. AI는 "복사-붙여넣기"를 촉진하고 추가적인 코드 줄을 얼마나 빨리 추가할 수 있는지에 초첨을 맞추고 있습니다. 우리는 전혀 코딩 속도를 평가 지표로 삼아서는 안됩니다.

# 속도 != 품질

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

GitHub은 코파일로 코드를 "55% 더 빠르게" 작성할 수 있다고 주장합니다. 개발자들은 그것을 좋아하고 새로운 코드를 만들고 기능을 제공하는 것을 즐긴다.

문제는 로버트 C. 마틴(저는 그의 의견에 동의하는 편이 거의 없습니다)이 코드가 작성되는 것보다 10배 더 많은 시간을 읽는다고 주장한다는 것입니다.

제 경험을 듣는 편이 더 좋을 것 같아요. 초보 개발자들이 "코드를 밀어붙이기"에 열중하는 것을 보았습니다. 그들은 예전보다 더 빠르게 진행하고 우리 코드베이스에 더 많은 요소를 추가하고 있습니다. 그것은 나중에 그 코드를 정리해야 하기 때문에 짜증납니다. 재사용 가능한 요소를 추출하는 작업 등이 필요합니다. 최근 팀과 몇 시간 동안 싸워야 했어요. 어떤 재사용 가능한 코드가 사실 재사용 가능하지 않고 우리 코드베이스로 넣지 말아야 할 거라고 막기 위해서 싸워야 했던 일이 있었습니다.

![image](/assets/img/2024-05-20-SHOCKINGAIDestroysCodeQuality_2.png)

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

더 이상 신중하고 깊은 사고 없이 코딩하는 방식으로의 전환은 우리 저장소의 품질을 희석시킬 뿐만 아니라, 위대한 소프트웨어 개발을 정의하는 장인 정신을 약화시키는 것입니다. AI에게 직장을 잃어버릴 걱정은 없어요. 이런 상황이 계속된다면 분야에서 훌륭한 소프트웨어 개발자가 남지 않을 것을 걱정합니다.

# 복사 붙여넣기의 매력

복사 붙여넣기 생활. 스택 오버플로우나 좋아하는 AI를 통해 코드를 찾아서 코드베이스에 넣습니다. 개발자는 기능을 개발함으로써 도파민 분비를 받고 모든 것이 잘 되어 있습니다.

개발자들은 새로운 기능을 만드는 것을 좋아하지만, 복사 붙여넣은 코드는 종종 유효기간을 초과하여 지속됩니다. 중복 코드를 제거할 권한이 있는 팀원이 있나요? 불행하게도, 우리 팀에는 중복 코드를 제거하기 위한 문화가 없어서 코드베이스가 고통받고 있습니다.

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

나는 이것이 기술 부채 문제뿐만 아니라 문화적 문제라고 생각해요.

## ‘소방관 스타일’ 개발자 치료하기

코드를 코드베이스에 넣는 것은 견고한 습관과 지식의 한 줌이 필요해요. '의존적 결정'을 내리는 AI를 훈련시키는 대신에 품질 높은 개발자를 얻을 수 있어요.

나는 개발자를 위한 훈련 프로그램을 서두르라는 의미가 아니라 BOK를 개발하고 소프트웨어 개발자에게 그들의 지식과 성과에 대한 책임을 물어야 한다는 의미에요. 유지보수 가능한 코드를 생산할 전략을 이해하고 문제를 품질 높은 방식으로 해결할 지혜 있는 팀이 필요해요.

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

그러면 우리 고문들은 낙차 코드를 이해하는 데 많은 시간과 노력을 들이지 않아도 되고, 그 코드를 유지하는 데 어려움을 겪지 않을 거에요. 어때요?

# 결론

우리가 이 AI 기술을 이용한 미래를 항해하는 동안, 좋은 소프트웨어 개발을 오랫동안 이끌어 온 원칙들인 명확성, 유지보수성, 그리고 무엇보다도 품질에 대한 헌신을 잊지 말아야 해요. 앞으로의 여정은 AI로 포장되어 있을 수 있지만, 우리의 작품의 질을 희생하지 않으면서 목적지에 도착할 수 있도록 하는 것은 인간의 손길이 될 거에요.

# 저자 소개

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

프로 소프트웨어 개발자 "비밀 개발자"는 Twitter에서 @TheSDeveloper로 찾을 수 있으며 주로 Medium.com을 통해 기사를 게시합니다.

비밀 개발자는 여전히 Copilot보다 빠릅니다.
