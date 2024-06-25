---
title: "ChatGPT-4는 무엇이 특별한가요"
description: ""
coverImage: "/assets/img/2024-05-20-WhatMakesChatGPT-4oSpecial_0.png"
date: 2024-05-20 20:21
ogImage:
  url: /assets/img/2024-05-20-WhatMakesChatGPT-4oSpecial_0.png
tag: Tech
originalTitle: "What Makes ChatGPT-4o Special?"
link: "https://medium.com/@ignacio.de.gregorio.noblejas/what-makes-chatgpt-4o-special-af11a8c208a2"
---

이미 아시다시피, OpenAI는 GPT-4 이후 1년여 만에 새로운 모델을 출시했습니다. 여전히 GPT-4의 변형이지만 이전에는 볼 수 없었던 다중 모달 기능을 갖추고 있습니다.

이 모델은 실시간 비디오 처리와 같이 강력한 기능을 포함하고 있는데, 이는 강력한 가상 어시스턴트를 실시간으로 지원하여 일상생활에 도움을 줄 수 있는 중요한 기능입니다. 그러나 이러한 기능은 비싸고 느릴 것으로 보이는데, 모델이 빠르고 무료로 사용할 수 있다는 점을 고려하면 설명이 되지 않습니다.

그렇다면 무슨 일이 벌어지고 있는 걸까요?

OpenAI가 아직 우리가 모르는 무언가를 깨닫고, 우리가 오늘 논의하는 지혜로운 설계 결정은 저렴한 비용으로 훨씬 더 똑똑한 모델을 만들 수 있다는 것을 깨닫게 된 것 같습니다.

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

그래서, 이 모든 것이 어떻게 의미가 있고, 미래의 당신에게 무엇을 의미하나요?

# 다중 모달 입력, 다중 모달 출력

그래서, ChatGPT-4o가 특별한 이유가 뭘까요? 그것은 역사상 최초로 완전히 "다중 모달 입력/다중 모달 출력" 모델입니다.

그런데 그게 무슨 의미일까요?

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

진정한 다중 모달 모델에서는 모델에 오디오, 텍스트, 이미지 또는 비디오를 보내면 모델이 요구 사항에 따라 텍스트, 이미지 또는 오디오(아직 비디오는 안 됨)로 응답할 수 있습니다.

하지만 당신이 생각하는 것을 알고 있어요: 이전 ChatGPT 또는 Gemini 버전들이 이미 이미지나 오디오를 처리하고 생성했던 것 아니었나요? 네, 그렇지만 주의할 점이 있어요: 그들은 독립적인 외부 구성 요소를 통해 그렇게 했었죠. 그게 친구야, 모든 것을 바꾸는 것이죠.

## 이전 모델들은 실제로 생각했던 것보다 더 나은 것처럼 보였어

이전에 모델에 오디오를 보낼 때, 이것이 표준 프로세스였습니다:

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

![WhatMakesChatGPT-4oSpecial_0](/assets/img/2024-05-20-WhatMakesChatGPT-4oSpecial_0.png)

이 절차에서는 자연어 음성에서 파생된 억양, 리듬, 프로소디, 전달된 감정 및 중요한 중단점이 손실되었습니다. 음성을 텍스트로 전사하는 Whisper 구성 요소의 영향으로 LLM이 이후 처리할 수 있었습니다.

그런 다음, LLM은 텍스트 응답을 생성하여 다른 구성 요소, 텍스트 음성 모델에 보내어 최종적으로 전달되는 음성을 생성했습니다.

자연스럽게, 인간은 단어 이외에 음성을 통해 훨씬 더 많은 정보를 전달하므로 매우 중요한 정보가 많이 손실되었으며 이는 이상적이지 못한 대기 시간으로 이어졌습니다. 분리된 요소 간에 정보를 전송해야 했기 때문입니다.

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

하지만 ChatGPT-4o를 사용하면 모든 것이 동일하지만 동시에 완전히 다르다는 것을 알게 될 거에요. 왜냐하면 모든 것이 동일한 장소에서 발생하기 때문이죠.

![이미지](/assets/img/2024-05-20-WhatMakesChatGPT-4oSpecial_1.png)

처음에는 많은 변화가 없는 것처럼 보일 수 있어요. 하지만 구성 요소가 거의 변하지 않았음에도 (보이스 코덱과 오디오 디코더는 이전에 보여드린 텍스트 음성 변환 모델의 부분이 될 것입니다), 이러한 구성 요소가 얼마나 정보 손실의 정도를 완전히 바꾸는지 그 차이가 있어요.

특히 이제 LLM은 원시 텍스트 대신 의미적인 발화 표현을 볼 수 있어요. 평범한 말로 하자면, "너를 죽이고 싶어!"라는 텍스트만 보던 것에서 이제 모델이 다음과 같은 정보도 받게 되었어요:

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

```js
{
 transcribed speech: "내가 너를 죽이고 싶어!";
 emotion: "행복함";
 tone: "기쁨";
}
```

모델은 메시지의 세부 사항을 캡처하여 일반 텍스트뿐만 아니라 감정까지 반영합니다.

따라서 LLM은 실제 상황에 뿌리를 둔 응답을 생성하며, 단어 뿐만 아니라 메시지의 주요 특성을 포착합니다.

이 응답은 이후 오디오 디코더로 전송되며, 이를 사용하여 아마도 Mel 스펙트로그램을 생성하고, 이는 마지막으로 보코더로 오디오를 생성하는 데 사용됩니다.

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

그런데, 모든 이 내용은 이미지 처리 및 생성 또는 비디오 처리에도 적용됩니다. 모든 구성 요소를 단일 모델로 통합하여 오디오 뿐만 아니라 다른 모달리티에서 정보를 수집합니다.

ChatGPT-4o는 이제 텍스트 외에도 키포인트 오디오, 이미지 또는 비디오 신호를 활용하여 더 관련성 있는 답변을 생성합니다. 간단히 말해, 이제는 데이터가 어떤 형태로 들어오든 상관없이 맥락과 필요에 따라 어떻게 답변해야 하는 지를 결정합니다.

그러나 이 변화가 얼마나 중요한지 여전히 설득되지 않았을 수도 있습니다. 그래서 이제 제대로 설명해 드리겠습니다.

# 의미 공간 이론

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

현재 AI에서 가장 아름다운 개념 중 하나는 잠재 공간(latent space)입니다. 모델이 세상을 이해하는 공간이죠. 간단히 말해, 우리 모델이 다중 모드(multimodal)인 경우 잠재 공간으로 가서 그것이 실제로 그런지 확인합니다.

예를 들어, Hume.ai가 다양한 음성 표현을 연구한 과정에서 만든 놀라운 대화형 시각화를 사용하여 어떻게 잠재 공간이 보이는지 볼 수 있습니다.

![이미지](/assets/img/2024-05-20-WhatMakesChatGPT-4oSpecial_2.png)

그러나 Hume의 예제와는 달리, GPT-4o의 잠재 공간은 다중 모드입니다. 따라서 ChatGPT-4o가 입력을 보면 원래 형식에 관계없이 압축된 표현이 됩니다.

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

다시 말해, 모델은 입력을 변환하여 데이터의 주요 속성을 여전히 포착하면서, 핵심적으로 숫자만 해석할 수 있는 기계에서 처리할 수 있게 만듭니다.

잠재 공간을 다스리는 하나의 원칙: 유사성(또는 OpenAI가 정의한 관련성). 우리의 세계와 마찬가지로, 중력과 같은 개념이 모든 것을 지배하는 것처럼, 의미론적 유사성은 다중 모달 LLMs 세계에서 모든 것을 지배합니다.

평범한 사람들을 위해 이것은 잠재 공간에서 의미론적으로 유사한 것들이 가깝고, 유사하지 않은 개념들이 멀리 밀려난다는 것을 의미합니다. '개'와 '고양이'는 여러 속성(동물, 포유류, 가정적 등)을 공유하기 때문에 그들의 표현은 유사할 것이며, 휴메의 잠재 공간에서 슬픔의 다른 음성 표현이 그룹화된 것처럼 비슷하다.

사실, 이미지, 오디오, 또는 비디오 인코더가 하는 것은 각각의 데이터 유형을 벡터로 변환하는 것입니다.

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

![image](/assets/img/2024-05-20-WhatMakesChatGPT-4oSpecial_3.png)

따라서 '개'라는 개념은 다양한 방법으로 표현될 수 있습니다: 텍스트, 허스키의 이미지 또는 짖는 소리를 통해. 이것이 우리가 진정한 다중 모달성을 원하는 근본적인 이유입니다.

이전에는 ChatGPT에게 개는 말 그대로 '개'라는 단어였습니다. 그러나 GPT-4o에게 오디오, 이미지, 텍스트 및 비디오가 이제 모델의 본질적인 부분으로 포함되었습니다.

따라서:

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

- 이제 모델은 황금 리트리버의 이미지가 '개'임을 알고 있습니다.
- 짖는 말리누아의 오디오도 '개'를 나타냅니다.
- 라브라도르가 뛰어다니는 비디오도 '개'입니다.

등등. 다중 모드로, 모델의 세계에 대한 이해력은 사람이 해석하는 방식과 유사해집니다: 다중 모달. 따라서 이제 모델이 '더 똑똑해졌다'는 것은 다중 모드를 통해 이제 모든 모드를 동등하게 추론할 수 있기 때문입니다.

하지만 '여러 모드 간 추론'이란 무엇을 의미할까요?

Meta의 ImageBind를 예로 들어보면, 정말 다중 모달 잠재 공간을 목표로 하는 최초의 연구 논문 중 하나로, 이러한 모델들이 세계 개념을 복잡하게 이해하는 방식에 대한 증거를 찾을 수 있습니다.

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

이전에 언급된 강아지 예시를 사용하면, 우리가 모델에 개가 수영장에 있는 이미지와 개가 짖는 소리만 제공하면, 모델은 그 소리의 원천을 매우 높은 확신으로 올바르게 식별합니다:

![image](/assets/img/2024-05-20-WhatMakesChatGPT-4oSpecial_4.png)

또한 시계의 이미지와 교회 종 소리를 추가하면, 모델은 교회 종 소리의 이미지를 식별할 수 있습니다:

![image](/assets/img/2024-05-20-WhatMakesChatGPT-4oSpecial_5.png)

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

하지만 ImageBind가 이를 어떻게 수행하는지 궁금하실 겁니다. 아마도 유추하셨겠지만, 그들은 각 데이터 유형의 표현을 계산하고 벡터 간의 거리를 측정합니다.

간단히 표현하자면, 이미지의 '개' 또는 좀 더 정확히 말하면 개가 있는 이미지 패치는 짖는 말리노이 오디오 파일의 벡터와 매우 유사할 것입니다. 이것은 모델에게 두 경우 모두 '개'임을 알려주며, 신기한 점은 이러한 벡터를 결합, 빼거나 보간하여 새로운 개념을 만들 수 있다는 것입니다.

요약하면, ChatGPT-4o는 모델에 더 많은 권한을 부여하는 것이 아니라, 모델이 세계를 다양한 데이터 유형을 통해 해석하는 데 도움을 주는 강력하고 복잡한 잠재 공간을 만들었다는 것을 보여주는 것입니다. 이는 인간이 하는 것처럼 이해를 도와주어 모델이 더 나은 추론을 할 수 있도록 돕습니다.

# 옳은 방향으로의 훌륭한 한 걸음

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

트루 멀티모달리티 달성은 OpenAI에서 세계에 강렬한 메시지를 보냈습니다:

모델의 백본인 LLM 자체를 더 지능적으로 만들지 않아도, 여러 모달리티를 걸쳐 추론할 수 있는 모델은 더 지능적일 수밖에 없습니다. 모델은 더 많은 기능을 갖추고 서로 다른 데이터 유형 간에 지식을 전달할 수 있는 능력이 있기 때문입니다.

사람들이 모든 감각을 사용하는 능력은 지능의 중요한 요소로 간주되며, AI도 그 능력을 갖추려고 합니다.

큰 장점으로는 모델이 추론에서 훨씬 효율적으로 동작할 수 있게 해줍니다(적용할 수 있는 특정 효율성을 제외하고). 여러 외부 구성 요소를 결합하는 통신 오버헤드를 제거하면 모델이 훨씬 더 빨라지는 것 같습니다.

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

그래서 ChatGPT-4o가 특별한 이유입니다. 우리는 이 모델이 정말 얼마나 똑똑한지 완전히 알 수 없지만, 우리가 본 적이 없기 때문에 첫 인상은 매우 매우 유망하다고 할 수 있어요.
