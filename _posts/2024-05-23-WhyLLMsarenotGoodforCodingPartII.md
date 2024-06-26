---
title: "LLM이 코딩에 적합하지 않은 이유  2부"
description: ""
coverImage: "/assets/img/2024-05-23-WhyLLMsarenotGoodforCodingPartII_0.png"
date: 2024-05-23 17:40
ogImage:
  url: /assets/img/2024-05-23-WhyLLMsarenotGoodforCodingPartII_0.png
tag: Tech
originalTitle: "Why LLMs are not Good for Coding — Part II"
link: "https://medium.com/towards-data-science/llms-coding-software-development-artificial-intelligence-68f195bb2ad3"
---

![WhyLLMsarenotGoodforCodingPartII_0](/assets/img/2024-05-23-WhyLLMsarenotGoodforCodingPartII_0.png)

이 시리즈의 첫 번째 기사 "LLM은 코딩에 좋지 않은 이유"를 게시한 후에는 소셜 미디어에서 다음과 같은 여러 댓글을 받았어요:

이 기사 시리즈의 목적은 대형 언어 모델(Large Language Models, LLM)을 사용하는 것을 막는 게 아니라, LLM을 더 효과적인 코딩 도우미로 만들기 위해 개선해야 할 주요 분야를 식별하는 것입니다.

ChatGPT와 같은 LLM은 일부 상황에서 유용할 수 있지만, 종종 문법적으로는 정확할 수 있지만 최적이 아니거나 기능적으로 심지어 틀린 코드를 생성합니다.

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

이전 기사에서는 토크나이저의 중요성, 코드에 적용될 때의 문맥 창의 복잡성, 그리고 훈련의 성격이 이러한 모델의 성능에 영향을 미칠 수 있는 방법에 대해 논의했습니다.

이 두 번째 기사에서는 코딩 작업에 사용되는 데 필요한 모델의 훈련 유형을 보다 깊이 있게 탐구하고, LLM이 본래 코딩 업무에 능숙하지 않은 이유 중 하나인 최신 라이브러리 및 코드 기능을 최신 상태로 유지하는 도전도 살펴볼 것입니다.

# 코딩 모델의 훈련

LLM 기반의 코딩 어시스턴트는 현실입니다. GitHub Copilot은 오늘 가장 유명한 제품 중 하나로, 개발 환경 내에서 코드 완성 및 채팅 지원 기능을 제공합니다.

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

개발자들이 말했듯이, "GitHub Copilot은 확률적 결정을 사용하여 제안을 생성합니다." 제품이 작동하는 방법에 대해 더 자세히 살펴보려면 문서를 자세히 살펴보세요:

![GitHub Copilot Screenshot](/assets/img/2024-05-23-WhyLLMsarenotGoodforCodingPartII_1.png)

커서를 어디에 두든지 제안을 받게 됩니다.

그런데 한 가지 더 있습니다.

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

General LLM (Large Language Models)는 토큰 시퀀스가 주어졌을 때 다음 토큰을 예측하는 데 훈련되었습니다. 이는 커서 바로 전 줄까지만 추론에 사용된다는 의미인데요 (왼쪽에서 오른쪽으로 생성한 결과).

양방향 모델은 왼쪽과 오른쪽 컨텍스트를 모두 사용하여 예측을 수행하는 반면, 실제로는 ChatGPT와 같은 단방향 모델이 코드에서 효율적으로 사용되도록 해결책을 사용합니다.

코딩 작업을 위해 기본 LLM을 준비하는 가장 일반적인 기술은 코드 데이터셋에서 적응형 미세 조정을 수행하는 것입니다. 자연어에서 모델을 구체적인 주제에 특화시킬 때 사용되는 기법인데요. DeepMind의 Yujia Li가 "AlphaCode로 대회 수준의 코드 생성"이라는 제목의 기사에서 이 프로세스를 설명했습니다:

![AlphaCode](/assets/img/2024-05-23-WhyLLMsarenotGoodforCodingPartII_2.png)

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

위 다이어그램의 (A) 섹션에서 볼 수 있듯이, 코딩을 위한 일반적인 LLMs는 자연어로 훈련된 기본 모델을 기반으로 구축되며 코드 베이스에서 사전 훈련을 받습니다. 예를 들어, print(", the model is trained to predict the completion hello")라는 문맥이 주어지면, 모델은 좌측에서 우측으로 생성하여 완성 hello를 예측하는 것을 훈련합니다.

그런 다음, 모델은 (B)에 나타난 대로 코딩 문제와 그 해결책을 사용하여 미세 조정됩니다. 따라서 주어진 코딩 문제에 대해 모델은 해결책을 생성하도록 훈련됩니다.

코드 데이터셋에서 LLMs를 사전 훈련시킴으로써, 이러한 모델은 프로그래밍 언어에 특정한 패턴, 스타일 및 구조를 배울 수 있습니다. 또한, 미세 조정 데이터셋에는 코드 완성 및 인필링과 같은 다양한 코딩 작업이 포함되어 있어야 하며, 이는 모델이 앞선 문맥을 기반으로 적절한 코드 조각을 예측하는 데 도움이 될 것입니다.

모델을 훈련한 후, 모델이 올바른 문맥을 인식하도록 보장하기 위한 또 다른 전략은 추론 중에 프롬프트의 일부로 전달하는 것입니다.

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

예를 들어, 코드 채우기를 위해 프롬프트에 주변 코드가 포함될 수 있고, 이를 통해 고유 방향성 능력에 맞게 작업을 재구성할 수 있습니다.

# LLM(Large Language Models)가 코딩 보조 도구로서 직면하는 도전

시리즈의 첫 번째 글에서 LLM의 코드 작성에 대한 제약과 그 원인에 대해 알아보았습니다.

그러나 놓치지 않아야 할 중요한 제한 사항이 있습니다: 최신 패키지 및 기능들과의 최신성 유지입니다.

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

소프트웨어 개발에서는 효과적이고 안전한 코드를 생성하기 위해 이것이 필수적이지만, 실제로는 두 가지 이유로 LLM에 대해 매우 어려울 수 있습니다:

- 정적 지식 베이스: GitHub Copilot은 모든 LLM과 마찬가지로 특정 시점에 촬영된 데이터(코드 및 문서)로 훈련됩니다. 따라서 최신 기능, 사용이 중지된 기능 또는 소프트웨어 라이브러리 및 프레임워크의 보안 패치에 대해 알지 못할 수 있습니다.
- 버전 특수성: 소프트웨어 개발은 라이브러리 및 프레임워크의 특정 버전에 크게 의존합니다. LLM은 실시간으로 업데이트되지 않기 때문에 오래된 코드 스니펫을 제공하여 호환성 문제나 사용 중지된 메서드 사용으로 이어질 수 있습니다.

API와 같은 인터페이스는 자주 변경되며, 새로운 기능이 추가되거나 이전 기능이 제거될 수 있습니다. 오래된 데이터를 기반으로 하는 LLM은 새로운 보안 위험을 알지 못할 수 있어 코드가 위험에 노출될 수 있습니다.

또한, 현대 소프트웨어 개발에서 의존성을 관리하는 것은 복잡하며, 최신 호환성 및 버전 정보가 부족한 경우 LLM이 최상의 조언을 제공하지 못할 수 있습니다.

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

## 모든 문제에는 해결책이 있어요

통합 개발 환경(IDE) 및 다른 코딩 도구는 실시간 업데이트를 통합하고 최신 문서 또는 라이브러리 버전을 가져올 수 있어요. 이는 ChatGPT-4의 Bing 브라우징 기능과 유사하며 지식 베이스를 확장할 수 있어요.

나는 GitHub Copilot의 채팅 기능이 브라우징 기능을 갖고 있다는 것을 알고 있어요. 그럼에도 불구하고, LLMs는 응답 생성을 위해 사전 훈련된 모델에 의존하며, 심지어 GitHub Copilot도 이에 대해 자료에서 경고하고 있어요:

코딩 어시스턴트로 LLMs를 사용할 때, 소프트웨어 업데이트 및 문서를 동적으로 확인하는 기능이 제한될 수 있다는 점을 인식해야 해요.

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

# 최종 생각

대규모 언어 모델은 코드의 기반을 생성하고 구문 및 기본 개념을 보조하는 데 유용할 수 있지만, 그 한계를 인식하고 이러한 제품에 지나치게 의존하지 않도록 주의해야 합니다.

각 코딩 프로젝트는 특정 비즈니스 규칙이나 사용자 요구에 맞게 디자인된 매우 구체적인 솔루션이 필요할 수 있습니다. 이러한 뉘앙스는 LLM이 교육 데이터에서 발견된 일반적인 패턴을 복제하도록 훈련받았기 때문에 매우 어려운 것입니다.

그러나 우리는 코드를 위해 맞춤화된 프롬프트 엔지니어링 전략을 채택하여 LLM을 우리가 원하는 출력물로 이끌 수 있습니다. 관심이 있다면, 아래 기사에서 더 읽을 수 있습니다:

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

## ⚠ 경고 ⚠

올바르게 실행되는 코드를 작성하는 것은 하나의 일이지만, 성능을 최적화한 코드를 작성하는 것은 또 다른 문제입니다.

LLM(Large Language Models)이 항상 가장 효율적인 알고리즘을 제공하거나 특정 하드웨어나 소프트웨어 환경에 대해 코드를 최적화하지는 않을 수 있음을 인지하는 것이 중요합니다. 게다가 AI가 생성한 코드를 실행할 때 항상 보안을 고려하는 것이 중요합니다.

결국, 대형 언어 모델은 최대 우도 추정에 기초하고, 효율적인 소프트웨어 개발은 성능을 고려한 코드 생성 전략에 의존합니다.

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

그게 다야! 읽어주셔서 정말 감사합니다!

LLM을 코딩할 때 도움이 되었으면 좋겠어요!

또한 새로운 콘텐츠를 기다리실 수 있도록 뉴스레터에 가입하실 수도 있습니다.

특히, Large Language Models 및 ChatGPT에 관심이 있다면:
