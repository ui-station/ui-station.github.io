---
title: "당신만의 개인 AI 코드 어시스턴트 로컬 머신에서 오프라인 LLM 사용 초보자 가이드"
description: ""
coverImage: "/assets/img/2024-05-18-YourPrivateAICodeAssistantABeginnersGuidetoOfflineLLMonYourLocalMachine_0.png"
date: 2024-05-18 21:11
ogImage:
  url: /assets/img/2024-05-18-YourPrivateAICodeAssistantABeginnersGuidetoOfflineLLMonYourLocalMachine_0.png
tag: Tech
originalTitle: "Your Private AI Code Assistant: A Beginner’s Guide to Offline LLM on Your Local Machine"
link: "https://medium.com/@jaroslav-pantsjoha/your-private-ai-playground-a-beginners-guide-to-offline-models-on-your-local-machine-7a078f70bbef"
---

# 당신의 개인 AI 코딩 어시스턴트 — 인터넷 연결이 필요 없음

![이미지](/assets/img/2024-05-18-YourPrivateAICodeAssistantABeginnersGuidetoOfflineLLMonYourLocalMachine_0.png)

로컬 머신에 개인 생성 AI 모델을 설정하고 사용하는 방법을 배우세요 — 완전 무료입니다. 기본적인 "Mac을 어떻게 사용하나요" 지식은 포함되어 있습니다 — 어쨌든 우리가 설정하는 것은 코드 어시스턴트니까요.

이 비교적 빠른 가이드는 클라우드 서비스로 데이터가 업로드되지 않는 개인용 로컬 오프라인 대규모 언어 모델 (LLM)을 시작하는 방법을 설명합니다.

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

- 이 프로세스가 작동하는 방식부터 시작해서 모델을 선택하는 데 도움을 드립니다.
- 노트북에 구성하는 방법을 설명해 드립니다.

## 지구를 위해 비용을 지불하지 않는 로컬 오프라인 GenAI 어시스턴트가 필요하세요?

지금 제가 여러분이 "생성적 AI"를 손끝에서 경험하고 싶어하실 것이라고 확신합니다. 하지만 아마 염려하시는 것은... 여러분이 실행 중인 메가 비밀 코드가 StackOverflow에는 없을 것이고, 여러분이 처음부터 복사한 곳에 데이터가 훈련되고 있다는 점이 아닌가요. 맞죠? 그렇죠, 맞죠?
그러나 여러분은 여전히 특별하고 안전한 느낌을 가지길 원하시며, '집에서 일하기' #밴라이프 여행 중에 숲, 들판, 해변 또는 고원에서 전화를 기다리는 동안 항상 누군가와 대화할 사람 — 맞죠, 코드와 함께 대화할 사람이 필요하실 것입니다.

하지만, 여기서 얼마 후에 우리는 공감을 얻을 수 있을 거라고 확신합니다. Google Astra 또는 GPT-4의 실제 리테일 사용이 가능해질 때에 대해 별도로 다룰 것입니다.

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

요즘 우리는 개인용 개인 AI 어시스턴트를 찾고 있죠. 개인 정보를 존중하며 인터넷 연결이 없어도 마법을 부리는 일이 가능한 어시스턴트 말이에요. 이제 과학 소설이 아니에요. 노트북에서 강력한 AI 모델을 직접 실행하는 것은 가능할 뿐만 아니라 놀랍게도 쉽답니다.

이번 달에는 Gemma, Llama3, Phi3와 Mixtral의 변형체(예: Dolphin) 등 새로운, 놀라운 능력을 가진 모델이 나왔어요. 이들은 Apple의 M-시리즈 칩으로 작동하는 일상적인 개발용 머신에서 원활하게 작동할 만큼 충분히 작아졌어요.

자, 이제 직접 개인용 개인 AI 어시스턴트를 만드는 방법을 알아볼까요?

# 여기서 시작해보세요 — 기본 사항을 먼저 알아봐요

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

![이미지](/assets/img/2024-05-18-YourPrivateAICodeAssistantABeginnersGuidetoOfflineLLMonYourLocalMachine_1.png)

## "최고의 모델"이란 것은 없어요 — 항상 절충이 필요해요.

가장 풍부한 모델, 다시 말해 매개변수가 가장 많은 모델은 일반적으로 크고 무겁고 실행하기 비싸며, CPU 및 메모리를 소모하여 마치 난로로 사용할 수 있을 정도로 비효율적일 수 있어요.

이 글을 쓰는 시점에서 Huggingface의 LLM Repo에는 658,281개의 모델이 있어요. 맞아요, 잘 이해하셨어요.

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

![image](/assets/img/2024-05-18-YourPrivateAICodeAssistantABeginnersGuidetoOfflineLLMonYourLocalMachine_2.png)

It’s mostly found on Huggingface, but there are other repos like Kaggle — Think of it as a “Dockerhub for LLMs”. We won`t go into LLM Model formats available, as we can be here all day, and this post will shortly be long enough.

## Checkout Model Leaderboard to help pick the LLMs you’re after

Huggingface Does have handy leaderboards, where you can pick the “Best Performing Model for it’s purpose” be it for chat, reasoning, Coding and other use cases, may want to poke around here to have an idea of what model you may want to pick for You use case — perhaps even in the future.

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

![이미지](/assets/img/2024-05-18-YourPrivateAICodeAssistantABeginnersGuidetoOfflineLLMonYourLocalMachine_3.png)

신나시나요? 정말 멋진 거죠?
하지만 너무 흥분하지 마세요. 알고 계셔야 할 것은 여러 LLM/모델이 있습니다. 이것들 중에서는 일부가 클라우드에서 온디맨드로 실행되어야 하기 때문에 당신의 노트북에서 실행할 수 없을 수도 있어요.

우리는 성능 우수한 것을 로컬에서 오프라인으로 실행하고 싶어요. 저는 여러분을 도울 거예요!

## 들어보세요. 주의해야 할 점이 있어요. 쉽거나 어려울 수 있어요.

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

생성적(오프라인) AI 모델의 세계에서 "최고"를 찾는 것은 단순히 벤치마킹에서 성능이 우수한 것을 선택하는 것만큼 간단하지 않습니다. 성능과 기계가 처리할 수 있는 것 사이의 적절한 균형을 찾는 것입니다.

그것은 미묘합니다.

예를 들어, M 클래스 MacBook을 가진 개발자로써 여러 능숙한 모델을 실행할 수 있는 능력이 있습니다. 그러나 이상적인 모델을 선택하는 것은 귀하의 특정한 요구 사항에 따라 달라집니다.

- 빠른 JavaScript 스니펫을 생성하기 위한 가벼운 모델을 찾고 있습니까, 아니면 복잡한 작업을 위한 보다 강력한 모델을 찾고 있습니까?

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

Kaggle 및 Hugging Face와 같은 리소스는 모델 기능 및 요구 사항에 대한 자세한 정보를 제공합니다. 평가 결과를 찾아보고 모델 크기 및 계산 요구 사항을 고려할 수 있습니다.

# 이제 뭘 해야 할까요?

알았어요, 알았어요, — 우리에게 "시작"은 간단함을 의미합니다. 그리고 위의 내용이 너무 많다고 느낀다면, 2-3단계로 더 요약된 옵션을 제시해 드리겠습니다.

## 1. Ollama로 시작해보세요. 다운로드하세요

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

LM Studio와 유사하지만 내 개인 의견으로 Ollama는 훨씬 간단하고 깔끔하다고 생각해요.

![image](/assets/img/2024-05-18-YourPrivateAICodeAssistantABeginnersGuidetoOfflineLLMonYourLocalMachine_4.png)

- LLMs 다운로드 - 문서를 확인해보세요. 하지만, 터미널에서 'ollama3'을 실행하면 최소한의 작업량으로 즉시 로컬 오프라인 모델과 대화할 수 있어요. 웹사이트의 정갈하게 정리된 목록에서 볼 수 있는 어떤 모델이든 빠르고 쉽게 설치할 수 있는 방법이죠.
- 선택 사항: 특정 포트에 로컬로 LLM 서비스 제공. 저는 RAG/스크립트를 사용하여 동일한 포트를 사용하는 LM Studio에 대한 별칭(alias)이 있어요. 그래서 터미널에서 'ollamaserve'는 'OLLAMA_HOST=0.0.0.0:11434 ollama serve'로 설정되어 있어요.

## 2. IDE에 로컬 모델 LLM 확장프로그램 다운로드하기.

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

- 저는 VSCode를 사용하고 있어요. Continue.dev를 좋아하며, 또는 Cody LLM을 사용할 수도 있어요. 이것 또한 시작하기 쉽고 깔끔해요.

![image1](/assets/img/2024-05-18-YourPrivateAICodeAssistantABeginnersGuidetoOfflineLLMonYourLocalMachine_5.png)

- 확장 프로그램을 구성하여 Ollama/Locally 호스트된 모델을 사용해 보세요.

![image2](/assets/img/2024-05-18-YourPrivateAICodeAssistantABeginnersGuidetoOfflineLLMonYourLocalMachine_6.png)

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

- 완료

여기 Ollama와 함께 사용할 수 있는 내 모델에 대한 내 견해입니다. 내가 원하는 목적에 맞게 특정 모델을 선택하고 선택할 수 있습니다.

다양한 모델의 장단점을 이해함으로써, AI 지원 개발 목표를 달성하면서 시스템을 압도하지 않는 모델을 선택할 수 있습니다.

# 마무리. 끝났고, 설정되었으니 — 행운을 빕니다!

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

상상해보세요 — 창의적인 텍스트 생성, 빠른 코드 작성, 아이디어 떠올리기, 심지어 매력적인 대화를 나누기까지, 모두 클라우드로 데이터를 보내지 않아도 가능합니다. 이것이 오프라인 생성 AI 모델을 실행하는 마법입니다.

이것이 명백한 사실이 될 수도 있고 아닐 수도 있지만, 혜택을 다시 요약해보겠습니다:

- 개인정보 보호: 데이터에 완전한 통제권 — 제3자 접근에 대한 우려 없음.
- 비용 효율성: 구독료나 사용량 제한 없음. 완전히 무료입니다! (최대한 멍청한 질문을 해도 부담 없고 아무도 알아차리지 못합니다.)
- 오프라인 신뢰성: 그리드에서 벗어나도 AI 도구에 액세스 가능합니다.
- 맞춤 설정: 모델을 필요에 맞게 세밀조정하고 선호도에 맞게 조정합니다. (나만의 데이터셋과 맥락에 바탕을 둬보세요 — 고급 사용법)

도움이 되었으면 좋겠습니다. LinkedIn에서 #GenAI의 #지수 및 #혁신적 시대에 대한 나의 생각을 공유해주시기 바랍니다. 좋아요, 공유하기, 구독하기, 팔로우해주시면 감사하겠습니다.

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

# OwnYourAI를 통해 AI를 소유하여 당신과 조화를 이루세요.

JP
