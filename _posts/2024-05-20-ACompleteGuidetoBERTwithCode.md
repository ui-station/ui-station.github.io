---
title: "BERT 코드와 함께하는 완벽 가이드"
description: ""
coverImage: "/assets/img/2024-05-20-ACompleteGuidetoBERTwithCode_0.png"
date: 2024-05-20 20:59
ogImage:
  url: /assets/img/2024-05-20-ACompleteGuidetoBERTwithCode_0.png
tag: Tech
originalTitle: "A Complete Guide to BERT with Code"
link: "https://medium.com/towards-data-science/a-complete-guide-to-bert-with-code-9f87602e4a11"
---

## 역사, 아키텍처, 사전 훈련 및 미세 조정

"LLMs from Scratch" 시리즈의 제4부 - 대형 언어 모델을 이해하고 구축하는 완벽한 가이드입니다. 이러한 모델이 어떻게 작동하는지 알아보고 싶다면 아래 내용을 읽어보시기를 권장합니다:

- Prologue: LLMs와 Transformers의 간단한 역사
- 제1부: 토큰화 - 전체 가이드
- 제2부: Python에서 처음부터 word2vec으로 단어 임베딩
- 제3부: Self-Attention으로 Transformer 임베딩 생성
- 제4부: 코드와 함께 BERT의 완전 가이드 - 역사, 아키텍처, 사전 훈련 및 미세 조정

# 소개

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

버트(Bidirectional Encoder Representations from Transformers)는 구글 AI Language에서 개발한 대규모 언어 모델(Large Language Model, LLM)로, 자연어 처리(Natural Language Processing, NLP) 분야에서 중요한 발전을 이루고 있습니다. 최근 몇 년간 많은 모델이 버트에 영감을 받아 발전하거나 직접적인 개선을 하였는데, RoBERTa, ALBERT, DistilBERT 등이 대표적입니다. 최초의 버트 모델은 OpenAI의 Generative Pre-trained Transformer (GPT) 이후 빠르게 공개되었으며, 둘 다 그 전년에 제안된 Transformer 아키텍처에 기반을 두었습니다. GPT는 자연어 생성(Natural Language Generation, NLG)에 초점을 맞추었지만, 버트는 자연어 이해(Natural Language Understanding, NLU)에 우선순위를 두었습니다. 이 두 개발은 NLP의 지형을 재편하며 기계 학습의 진전에 주목할 만한 이정표로 자리 잡았습니다.

다음 글은 버트의 역사를 살펴보고, 창조 당시의 환경을 자세히 소개할 것입니다. 이를 통해 논문 저자들이 한 아키텍처적 결정 뿐만 아니라 산업 및 취미용 응용 프로그램에서 버트를 훈련하고 세밀 조정하는 방법을 이해할 수 있는 완전한 그림을 제공할 것입니다. 우리는 다이어그램으로 아키텍처를 자세히 살펴보고, 감정 분석 작업을 위해 버트를 세밀 조정하는 코드를 처음부터 작성해볼 것입니다.

# 목차

1 — 버트의 역사 및 주요 기능

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

2 - 아키텍처 및 사전 훈련 목표

3 - 감정 분석을 위한 BERT 파인 튜닝

4 - 결론

5 - 더 많은 독해

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

# 1 — BERT의 역사 및 주요 기능

BERT 모델은 다음 네 가지 주요 기능에 의해 정의될 수 있습니다:

- 인코더 전용 구조
- 사전 훈련 접근 방식
- 모델 미세 조정
- 양방향 문맥 활용

이러한 기능 각각은 논문의 저자들이 만든 설계 선택사항이며, 이 모델이 생성된 시기를 고려하여 이해할 수 있습니다. 다음 섹션에서는 이러한 기능 각각을 살펴보고, 이러한 기능이 BERT의 동시대인 Transformer와 GPT에서 영감을 받았거나 그들을 개선하기 위한 것임을 보여줄 것입니다.

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

## 1.1 — 인코더 전용 아키텍처

2017년 트랜스포머의 등장으로 혁신적인 디자인을 바탕으로 한 새로운 모델을 생산하기 위한 레이스가 시작되었습니다. OpenAI는 2018년 6월 첫 번째로 GPT를 만들어내 역량 있는 NLG를 자랑하며 나중에는 ChatGPT를 구동하는 모델을 발표했습니다. 구글은 이에 4개월 후인 BERT를 공개하여 NLU를 위해 설계된 인코더 전용 모델을 선보였습니다. 이 두 아키텍처 모두 매우 뛰어난 모델을 생산할 수 있지만 수행할 수 있는 작업들은 약간 다를 수 있습니다. 각 아키텍처에 대한 개요는 아래에서 제공됩니다.

디코더 전용 모델:

- 목표: 입력 시퀀스에 대한 새로운 출력 시퀀스 예측
- 개요: 트랜스포머의 디코더 블록은 인코더에 제공된 입력을 바탕으로 출력 시퀀스를 생성하는 역할을 합니다. 디코더 전용 모델은 인코더 블록을 완전히 생략하고 여러 디코더를 단일 모델에 쌓아 올려 생성됩니다. 이러한 모델은 입력으로 프롬프트를 받아들이고, 다음 가장 확률이 높은 단어를 하나씩 예측함으로써 응답을 생성하는 작업으로 알려진 Next Token Prediction (NTP)이라는 작업을 수행합니다. 그 결과, 디코더 전용 모델은 대화형 챗봇, 기계 번역 및 코드 생성과 같은 NLG 작업에서 뛰어난 성능을 발휘합니다. 이러한 종류의 모델은 ChatGPT에서 구동되는 디코더 전용 모델 (GPT-3.5 및 GPT-4)의 광범위한 사용으로 인해 일반 대중에게 가장 익숙할 것입니다.

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

인코더 전용 모델:

- 목표: 입력 시퀀스 내의 단어에 대한 예측 수행
- 개요: Transformer의 인코더 블록은 입력 시퀀스를 수용하고 각 단어(또는 좀 더 구체적으로, 각 토큰)에 대한 풍부한 숫자 벡터 표현을 생성하는 역할을 합니다. 인코더 전용 모델은 디코더를 생략하고 여러 Transformer 인코더를 쌓아 하나의 모델을 생성합니다. 이러한 모델은 프롬프트를 수용하지 않고, 예측을 수행할 입력 시퀀스(예: 시퀀스 내의 빠진 단어를 예측)를 받습니다. 인코더 전용 모델은 새로운 단어 생성을 위해 디코더를 사용하지 않기 때문에 GPT와 같이 대화형 챗봇 애플리케이션에 사용되지 않습니다. 대신, 인코더 전용 모델은 대부분 NLU 작업인 Named Entity Recognition (NER) 및 감성 분석에 주로 사용됩니다. 인코더 블록에서 생성된 풍부한 벡터 표현은 BERT가 입력 텍스트를 심층적으로 이해하는 데 기여합니다. BERT 저자들은 이 구조적 선택이 BERT의 성능을 향상시킬 것이라고 주장했으며, 특히 디코더 전용 구조는 GPT와 비교하여 BERT의 성능을 향상시킬 것이라고 기술했습니다.

Transformer, GPT 및 BERT를 위한 아키텍처 다이어그램:

지금까지 논의한 세 모델에 대한 아키텍처 다이어그램이 아래에 나와 있습니다. 이는 원본 Transformer 논문 "Attention is All You Need" [2]의 아키텍처 다이어그램을 적응하여 작성되었습니다. 모델의 인코더 또는 디코더 블록 수는 N으로 표시됩니다. 원래 Transformer에서 인코더와 디코더 각각은 쌓인 6개의 인코더 및 디코더 블록으로 이루어져 있기 때문에, N은 각각 6입니다.

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

![image](/assets/img/2024-05-20-ACompleteGuidetoBERTwithCode_0.png)

## 1.2 — Pre-training Approach

GPT은 BERT의 개발에 여러 가지 방식으로 영향을 미쳤습니다. 모델이 첫 번째 디코더 전용 변형 모델이었을 뿐만 아니라 GPT는 또한 모델 사전 훈련을 인기 있게 만들었습니다. 사전 훈련은 언어의 넓은 이해를 얻기 위해 단일 대형 모델을 훈련하는 것을 포함하며 이는 단어 사용 및 문법적 패턴과 같은 측면을 아우릅니다. 그 결과, 작업에 중립적인 기본 모델을 생성합니다. 위 다이어그램에서, 기본 모델은 선형 레이어 아래의 구성 요소들로 이루어져 있습니다 (보라색으로 표시됨). 훈련된 후, 이 기본 모델의 복사본은 특정 작업을 해결하기 위해 미세 조정될 수 있습니다. 미세 조정은 선형 레이어만 훈련하는 것을 의미합니다: 작은 피드포워드 신경망으로 종종 분류 헤드 또는 헤드라고 불립니다. 모델의 나머지 부분(즉, 기초 부분)에 있는 가중치와 바이어스는 변경되지 않거나 고정됩니다.

아날로지:

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

간략한 비유를 만들어보겠습니다. 감성 분석 작업을 생각해보세요. 여기서 목표는 표현된 감정에 기반하여 텍스트를 긍정 또는 부정으로 분류하는 것입니다. 예를 들어, 어떤 영화 리뷰에서 "이 영화를 사랑했다"라는 텍스트는 긍정으로 분류되고, "이 영화를 싫어했다"라는 텍스트는 부정으로 분류될 것입니다. 전통적인 언어 모델링 접근 방식에서는 이 작업에 특화된 새로운 아키텍처를 일반적으로 처음부터 학습할 것입니다. 이것은 영화 리뷰를 보여주는 것으로 영어를 처음부터 가르치는 것과 같다고 생각할 수 있습니다. 물론, 이것은 느리고 비용이 많이 들며 많은 학습 예제가 필요합니다. 게다가, 그 결과로 얻는 분류기는 여전히 이 한 가지 작업에만 능숙할 것입니다. 대조적으로, 사전 훈련 접근 방식에서는 범용 모델을 가져와서 이를 감성 분석 작업에 대해 미세 조정합니다. 이것은 이미 영어에 능숙한 사람에게 현재 작업에 익숙해지도록 모자란 수의 영화 리뷰를 보여주는 것으로 생각할 수 있습니다. 아마도 두 번째 접근 방식이 훨씬 더 효율적하다는 것이 직관적일 것입니다.

**사전 훈련에 대한 이전 시도:**

사전 훈련 개념은 OpenAI에 의해 발명된 것이 아니며, 그 이전 몇 년 동안 다른 연구자들에의해 탐구되어왔습니다. 한 가지 주목할만한 예는 Allen Institute의 연구자들이 개발한 ELMo 모델(Embeddings from Language Models)입니다. 이전 시도에도 불구하고, OpenAI의 큰 논문에서처럼 다른 연구자들은 사전 훈련의 효과를 명백하게 입증하지 못했습니다. 그들 자신의 말에 따르면, 팀은 이 발견으로 사전 훈련 패러다임을 앞으로의 언어 모델링에서 우세한 접근 방식으로 확립시켰습니다. 이 추세와 일치하도록, BERT 저자들도 사전 훈련 접근 방식을 완전히 채택했습니다.

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

## 1.3 — 모델 파인튜닝

파인튜닝의 장점:

파인튜닝은 오늘날 흔한 일로, 이러한 접근 방식이 주목받기 전 얼마나 최신인지 쉽게 간과하기 쉽습니다. 2018년 이전까지는 각기 다른 NLP 작업을 위해 새로운 모델 아키텍처를 도입하는 것이 일반적했습니다. 훈련을 사전에 시작함으로써 신규 모델 개발에 필요한 훈련 시간과 컴퓨팅 비용이 급격히 감소했을 뿐만 아니라 필요한 훈련 데이터의 양도 줄어들었습니다. 언어 모델을 처음부터 완전히 재설계하고 재훈련하는 대신 제네릭 모델인 GPT를 소량의 작업별 데이터로 파인튜닝함으로써 해당 시간을 분수로 줄일 수 있습니다.

작업에 따라서 분류 헤드를 수정하여 다른 수의 출력 뉴런을 포함시킬 수 있습니다. 이는 감정 분석과 같은 분류 작업에 유용합니다. 예를 들어 BERT 모델의 원하는 출력이 리뷰가 긍정인지 부정인지 예측하는 것이라면, 헤드를 두 개의 출력 뉴런이 있는 형태로 변경할 수 있습니다. 각각의 활성화는 리뷰가 긍정인지 부정인지일 확률을 나타냅니다. 10 클래스로 구성된 다중 분류 작업의 경우, 헤드를 10개의 뉴런을 가진 출력 레이어로 변경할 수 있습니다. 이렇게 함으로써 BERT를 더 다양하게 활용할 수 있어 여러 하류 작업에 기본 모델이 사용될 수 있습니다.

BERT의 파인튜닝:

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

BERT은 GPT의 발자취를 따르며 사전 훈련/미세 조정 접근 방식을 채택했습니다. 구글은 BERT의 두 가지 버전을 출시했는데, Base와 Large로 하드웨어 제약에 따라 모델 크기를 유저에게 유연하게 제공했습니다. 양쪽 모델 모두 많은 TPUs(텐서 처리 장치)에서 약 4일이 걸렸다고 하는데, BERT Base는 16개의 TPUs에서 훈련되었고 BERT Large는 64개의 TPUs에서 훈련되었습니다. 대부분의 연구자, 취미로 연구하는 사람들 및 산업 실무자들에게는 이 수준의 훈련이 현실적이지 않을 수 있습니다. 그래서 어떤 특정 작업에 기초 모델을 몇 시간 동안 미세 조정하는 아이디어는 훨씬 더 매력적인 대안입니다. 원래 BERT 아키텍처는 다양한 작업과 데이터셋에 걸쳐 수천 번의 미세 조정 이터레이션을 거쳤는데, 이 중 많은 것들이 Hugging Face와 같은 플랫폼에서 공개적으로 다운로드할 수 있습니다.

## 1.4 — 양방향 컨텍스트 사용

언어 모델로, BERT는 이전 단어가 관찰되었을 때 특정 단어가 관측될 확률을 예측합니다. 이 기본적인 측면은 아키텍처와 의도된 작업에 관계없이 모든 언어 모델이 공유하는 것입니다. 그러나 모델이 이러한 확률을 활용하는 것이 모델의 특정 작업에 적합한 행동을 부여합니다. 예를 들어 GPT는 시퀀스에서 다음으로 가장 가능성이 높은 단어를 예측하도록 훈련됩니다. 즉, 모델은 이전 단어가 관찰되었을 때 다음 단어를 예측합니다. 다른 모델들은 감정 분석에 훈련될 수 있고, 긍정적 또는 부정적과 같은 텍스트 라벨을 사용하여 입력 시퀀스의 감정을 예측할 수 있습니다. 텍스트에 대한 어떠한 의미 있는 예측을 하려면 주변 컨텍스트가 이해되어야 합니다. 특히 NLU 작업에서는 BERT가 이러한 이해를 보장하여 양방향성이라는 핵심 특성 중 하나를 통해 우수한 이해를 제공합니다.

양방향성은 아마도 BERT의 가장 중요한 특징인데, NLU 작업에서 BERT의 높은 성능의 중요한 이유이며, 또한 모델의 인코더 전용 아키텍처의 주요 동기입니다. Transformer 인코더의 self-attention 메커니즘은 양방향 컨텍스트를 계산하지만, 이와 같은 것을 할 수 없는 디코더는 단방향 컨텍스트를 생산합니다. BERT 저자들은 GPT의 이 양방향성 부족으로 인해 BERT만큼의 언어 표현 깊이를 달성하지 못한다고 주장했습니다.

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

양방향성 정의하기:

그렇다면 "양방향성"이라는 맥락이 정확히 무엇을 의미할까요? 여기서 양방향성은 입력 시퀀스의 각 단어가 앞뒤 단어(좌측 맥락과 우측 맥락이라고 함)로부터 맥락을 확보할 수 있다는 것을 의미합니다. 기술적인 용어로는 어텐션 메커니즘이 각 단어에 대해 이전 및 이후의 토큰에 주의를 기울일 수 있다고 말합니다. 이를 분해해보면 BERT는 입력 시퀀스 내 단어에 대해서만 예측을 하고 GPT처럼 새로운 시퀀스를 생성하지는 않습니다. 따라서 BERT가 입력 시퀀스 내의 단어를 예측할 때, 모든 주변 단어에서 문맥적 단서를 접목할 수 있습니다. 이는 양방향으로 문맥을 제공하므로 BERT가 보다 정보를 기반으로 한 예측을 할 수 있도록 도와줍니다.

이를 GPT와 같은 디코더 전용 모델과 대조해보면, 여기서 목표는 출력 시퀀스를 생성하기 위해 한 번에 새로운 단어를 예측하는 것입니다. 각 예측된 단어는 이전 단어가 제공한 맥락(좌측 맥락)만 활용할 수 있으며, 그 이후의 단어(우측 맥락)는 아직 생성되지 않았습니다. 그러므로 이러한 모델을 단방향으로 지칭합니다.

![이미지]("/assets/img/2024-05-20-ACompleteGuidetoBERTwithCode_1.png")

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

이미지 설명:

위의 이미지는 양방향 컨텍스트를 사용하는 전형적인 BERT 작업과 단방향 컨텍스트를 사용하는 전형적인 GPT 작업의 예시를 보여줍니다. BERT에서는 이 [MASK]로 표시된 가리킨 가림막된 단어를 예측하는 것이 작업입니다. 이 단어는 왼쪽과 오른쪽에 단어가 있기 때문에 양쪽의 단어를 사용하여 컨텍스트를 제공할 수 있습니다. 만약 당신이 인간으로써 이 문장을 왼쪽 또는 오른쪽 컨텍스트만 가지고 읽는다면, 가리킨 가림막된 단어를 본인이 예측하기 어려울 것입니다. 그러나 양방향 컨텍스트를 사용하면 가림막된 단어가 'fishing'이라는 것을 추측하는 것이 훨씬 더 가능해집니다.

GPT의 경우, 목표는 전통적인 NTP 작업을 수행하는 것입니다. 이 경우, 목적은 입력 시퀀스 및 이미 생성된 단어를 기반으로 새로운 시퀀스를 생성하는 것입니다. 입력 시퀀스가 모델에게 시를 쓰라는 지시를 준 상황에서 이미 생성된 단어가 "Upon a" 인 것을 고려한다면, 다음 단어는 "river"가 될 것으로 예상해볼 수 있습니다. 여러 후보 단어가 있을 때 GPT(언어 모델로서)는 어휘 중 각 단어가 다음에 나타날 가능성을 계산하고 훈련 데이터를 기반으로 가장 가능성이 높은 단어 중 하나를 선택합니다.

## 1.5 — BERT의 한계

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

양방향 모델로서 BERT는 두 가지 주요 단점을 가지고 있습니다:

1. 훈련 시간이 증가합니다:

트랜스포머 기반 모델의 양방향성은 당시 주류였던 좌측에서 우측으로의 문맥 모델에 대한 직접적인 개선으로 제안되었습니다. GPT는 입력 시퀀스에 대한 문맥 정보를 일방적으로만 얻을 수 있기 때문에 단어 사이의 인과 관계에 대해 완전히 파악하지 못했습니다. 그러나 양방향 모델은 단어 간의 인과 관계를 보다 포괄적으로 이해할 수 있으므로 NLU 작업에서 보다 나은 결과를 볼 수 있습니다. 과거에 양방향 모델이 탐구되었지만, 늦은 1990년대의 양방향 RNN과 같은 한계가 있었습니다. 일반적으로 이러한 모델들은 훈련을 위해 더 많은 계산 리소스를 요구하기 때문에 동일한 계산 성능을 달성하기 위해서는 더 큰 단방향 모델을 훈련해야 합니다.

2. 언어 생성 작업에서 성능이 저하됩니다:

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

BERT는 NLU 작업을 해결하기 위해 특별히 설계되었으며, 디코더 및 새로운 시퀀스를 생성하는 능력을 포기하고 인코더 및 입력 시퀀스에 대한 풍부한 이해를 개발하는 것으로 대체했습니다. 결과적으로 BERT는 NER, 감정 분석 등과 같은 NLP 작업 부분에 최적화되어 있습니다. 특히, BERT는 프롬프트를 수용하지 않고 대신 입력 시퀀스를 처리하여 예측을 작성합니다. BERT는 기술적으로 새로운 출력 시퀀스를 생성할 수 있지만, LLM(언어 모델)의 설계적 차이를 인지하고 중대하게 생각해야 합니다. ChatGPT 시대 이후의 LLM들을 생각할 때와 BERT 설계의 현실과의 차이를 인지하는 것이 중요합니다.

## 2 — 아키텍처 및 사전 훈련 목표

### 2.1 — BERT의 사전 훈련 목표 개요

양방향 모델을 훈련시키려면 좌/우 컨텍스트가 모두 예측에 활용되는 작업이 필요합니다. 따라서 저자들은 BERT의 언어 이해를 강화하기 위해 주의 깊게 2가지 사전 훈련 목표를 구성했습니다. 이것들은 Masked Language Model(MLM) 작업과 Next Sentence Prediction(NSP) 작업이었습니다. 각각의 훈련 데이터는 당시 사용 가능한 모든 영어 위키피디아 기사 (25억 단어)와 BookCorpus 데이터셋의 추가 11,038권의 책 (8억 단어)로 구성되었습니다. 초기 데이터는 구체적인 작업에 따라 전처리되어야 했지만, 아래에서 설명한 대로 수행되었습니다.

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

## 2.2 — 마스크된 언어 모델링 (MLM)

MLM 개요:

마스크된 언어 모델링 작업은 양방향 모델을 훈련해야 하는 필요를 직접적으로 해결하기 위해 만들어졌습니다. 이를 위해 모델은 입력 시퀀스의 좌측 문맥과 우측 문맥을 모두 사용하여 예측을 수행하도록 훈련되어야 합니다. 이는 훈련 데이터에서 15%의 단어를 무작위로 마스크하고 BERT를 사용하여 누락된 단어를 예측하도록 훈련함으로써 달성됩니다. 입력 시퀀스에서 마스크된 단어는 [MASK] 토큰으로 대체됩니다. 예를 들어, 책 코퍼스에서 발견된 원시 훈련 데이터 중에 A man was fishing on the river 라는 문장이 있다고 가정해보겠습니다. MLM 작업에 대한 훈련 데이터로 원시 텍스트를 변환할 때, 단어 fishing이 무작위로 마스크되어 [MASK] 토큰으로 대체될 수 있으며, 이는 훈련 입력 A man was [MASK] on the river with target fishing을 제공합니다. 따라서 BERT의 목표는 단일 누락된 단어 fishing을 예측하는 것이며, 누락된 단어가 채워진 입력 시퀀스를 재생산하는 것이 아닙니다. 마스킹 프로세스는 MLM 작업의 훈련 데이터를 작성할 때 모든 가능한 입력 시퀀스(예: 문장)에 반복적으로 적용될 수 있습니다. 이 작업은 이전에 언어학 문헌에서 존재했었으며 Cloze 작업으로 알려져 있습니다.[8] 그러나 기계 학습 맥락에서는 BERT의 인기로 인해 MLM으로 흔히 언급됩니다.

미세 조정과 사전 훈련 간 불일치 완화 방법:

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

저자들은 그러나 [MASK] 토큰은 훈련 데이터에만 나타나고 라이브 데이터(추론 시간)에는 나타나지 않기 때문에 사전 훈련과 세부 튜닝 간에 불일치가 있을 것이라고 지적했습니다. 이를 완화하기 위해 모든 마스킹된 단어가 [MASK] 토큰으로 대체되는 것은 아닙니다. 저자들은 대신 다음과 같이 설명합니다:

예측 단어와 목표 단어 사이의 오차 계산:

BERT는 BERT Base 및 BERT Large 모두 최대 512개의 토큰을 입력으로 받습니다. 시퀀스에서 최대 토큰 수보다 적은 경우, [PAD] 토큰을 사용하여 패딩이 추가되어 최대 512개에 도달합니다. 출력 토큰 수도 입력 토큰 수와 정확히 일치합니다. 입력 시퀀스의 i 위치에 마스크 토큰이 있는 경우, BERT의 예측은 출력 시퀀스의 i 위치에 있을 것입니다. 훈련 목적으로 다른 모든 토큰은 무시되므로 모델의 가중치 및 편향에 대한 업데이트는 입력 시퀀스의 i 위치에 있는 예측 토큰과 목표 토큰 간의 오차를 기반으로 계산됩니다. 이 오차는 Cross Entropy Loss(음의 로그 우도) 함수를 사용하여 계산됩니다. 이러한 내용은 나중에 보게 될 것입니다.

## 2.3 — 다음 문장 예측 (NSP)

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

개요:

BERT의 사전 학습 작업 중 두 번째는 Next Sentence Prediction입니다. 이 작업은 하나의 세그먼트(일반적으로 문장)가 다른 세그먼트를 논리적으로 잇는지 분류하는 것을 목표로 합니다. NSP를 사전 학습 작업으로 선택한 이유는 MLM을 보완하고 BERT의 NLU 능력을 향상시키기 위한 것이며, 저자들은 다음과 같이 설명합니다:

NSP에 대해 사전 학습함으로써, BERT는 채의 흐름에 대한 이해를 발전시킬 수 있으며, 이는 많은 NLU 문제에 유용합니다. 예를 들어 다음과 같은 것들이 있습니다:

- 내용을 바꿔 말한 문장 쌍
- 추론을 위한 가설-전제 쌍
- 질문 응답에서 질문-통과 문장 쌍

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

BERT에서의 NSP 구현:

NSP의 입력은 첫 번째와 두 번째 세그먼트(표시된 A 및 B)로 구성되며 두 번째 [SEP] 토큰이 있는 [SEP] 토큰으로 구분되고 끝에 두 번째 [SEP] 토큰이 있습니다. BERT는 실제로 NSP를 수행하든 아니든 입력 시퀀스 당 최소한 하나의 [SEP] 토큰을 예상합니다. 이 토큰은 시퀀스의 끝을 나타내며, MLM 작업에 대한 입력의 끝에 이러한 토큰 중 하나가 추가됩니다. 또한, NSP가 포함되지 않은 다른 모든 작업들에 대해서도 동일한 처리가 이루어집니다. NSP는 분류 문제를 형성하며, 출력은 A 세그먼트가 B 세그먼트를 논리적으로 따르는 경우 IsNext에 해당하고, 그렇지 않은 경우 NotNext에 해당합니다. 교육 데이터는 단어 조각(WordPiece) 토크나이저가 50%의 경우에는 다음 문장과 함께 문장을 선택하고, 나머지 50%의 경우에는 무작위 문장을 선택함으로써 어떤 단일 언어 말뭉치에서 쉽게 생성할 수 있습니다.

BERT의 입력 임베딩:

BERT의 입력 임베딩 과정은 위치 인코딩, 세그먼트 임베딩 및 토큰 임베딩 세 단계로 구성됩니다(아래 다이어그램 참조).

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

위치 인코딩:

Transformer 모델과 마찬가지로 각 토큰의 임베딩에 위치 정보가 주입됩니다. 그러나 Transformer와 달리 BERT의 위치 인코딩은 고정되어 있고 함수에 의해 생성되지 않습니다. 이는 BERT가 BERT 베이스와 BERT 라지의 입력 시퀀스에서 512개의 토큰으로 제한된다는 것을 의미합니다.

세그먼트 임베딩:

각 토큰이 속한 세그먼트를 인코딩하는 벡터도 추가됩니다. MLM 사전 훈련 작업 또는 다른 NSP 작업(단일 [SEP] 토큰만 있는 경우)의 경우 입력의 모든 토큰은 세그먼트 A에 속하는 것으로 간주됩니다. NSP 작업의 경우, 두 번째 [SEP] 이후의 모든 토큰은 세그먼트 B로 표시됩니다.

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

토큰 임베딩:

원래의 Transformer와 마찬가지로 각 토큰에 대해 학습된 임베딩은 위치 및 세그먼트 벡터에 추가되어 BERT에서 자기 주의 메커니즘에 전달되는 최종 임베딩을 만들어 내며 문맥 정보를 추가합니다.

![이미지](/assets/img/2024-05-20-ACompleteGuidetoBERTwithCode_2.png)

## 2.5 — 특별 토큰

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

위의 이미지에서 볼 수 있었듯이 입력 시퀀스에는 [CLS] (분류) 토큰이 앞에 추가되었습니다. 이 토큰은 전체 입력 시퀀스의 의미론적 의미를 요약하고, BERT가 분류 작업을 수행하는 데 도움이 됩니다. 예를 들어, 감성 분석 작업에서 최종 층의 [CLS] 토큰은 입력 시퀀스의 감정이 긍정적인지 부정적인지 예측 추출하기 위해 분석될 수 있습니다. [CLS] 및 [PAD] 등은 BERT의 특수 토큰 예시입니다. 여기서 BERT의 특수 토큰은 총 다섯 개 있습니다. 아래에 요약을 제공합니다:

- [PAD] (토큰 ID: 0) — 512개 토큰으로 구성된 입력 시퀀스의 총 수를 맞추기 위해 사용되는 패딩 토큰.
- [UNK] (토큰 ID: 100) — BERT 어휘에 없는 토큰을 나타내기 위해 사용되는 알 수 없는 토큰.
- [CLS] (토큰 ID: 101) — 분류 토큰으로 기대되는 것은 각 시퀀스의 시작 부분에 나타냅니다. 이러한 토큰은 분류 작업을 위한 클래스 정보를 캡슐화하며, 종합적인 시퀀스 표현으로 생각할 수 있습니다.
- [SEP] (토큰 ID: 102) — 단일 입력 시퀀스의 두 세그먼트를 구분하는 데 사용되는 구분자 토큰 (예: Next Sentence Prediction). 적어도 입력 시퀀스 당 하나의 [SEP] 토큰이 필요하며, 최대 두 개까지 사용 가능합니다.
- [MASK] (토큰 ID: 103) — 마스크 토큰은 BERT를 마스킹된 언어 모델링 작업으로 훈련하거나 마스크된 시퀀스에 대한 추론을 수행하는 데 사용됩니다.

## 2.4 — BERT Base와 BERT Large의 아키텍처 비교

BERT Base와 BERT Large는 아키텍처적으로 매우 유사합니다. 두 모델 모두 WordPiece 토크나이저를 사용하여(따라서 앞에서 설명한 동일한 특수 토큰을 사용) 최대 시퀀스 길이가 512 토큰입니다. BERT의 어휘 크기는 30,522이며, 그 중 약 1,000 개의 토큰은 "사용되지 않은" 상태를 유지합니다. 사용되지 않은 토큰은 사용자가 전체 토크나이저를 다시 훈련하지 않고 사용자 지정 토큰을 추가할 수 있도록 고의로 비워둡니다. 의료 및 법률 용어와 같은 도메인별 어휘와 함께 작업할 때 유용합니다. BERT Base와 BERT Large는 원래 Transformer의 embedding 차원 (d_model)보다 높은 수를 갖습니다. 이는 모델의 어휘에 대해 학습된 벡터 표현의 크기에 해당합니다. BERT Base의 d_model은 768이며, BERT Large의 d_model은 1024입니다(원래 Transformer의 512를 두 배 증가시킨 값).

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

두 모델은 주로 네 가지 범주에서 차이가 있습니다:

- 인코더 블록의 수, N: 서로 쌓인 인코더 블록의 수입니다.
- 인코더 블록 당 어텐션 헤드 수: 어텐션 헤드는 입력 시퀀스의 문맥 벡터 임베딩을 계산합니다. BERT는 멀티헤드 어텐션을 사용하므로, 이 값은 인코더 레이어 당 헤드 수를 나타냅니다.
- 피드포워드 네트워크의 은닉층 크기: 선형 레이어는 고정된 뉴런 수(예: BERT Base의 경우 3072)를 가진 은닉층으로 구성되며, 다양한 크기의 출력 레이어로 이어집니다. 출력 레이어의 크기는 작업에 따라 다릅니다. 예를 들어, 이진 분류 문제는 단지 두 개의 출력 뉴런이 필요하고, 10개 클래스를 가진 다중 클래스 분류 문제는 10개의 뉴런이 필요합니다.
- 총 매개변수: 모델 내 가중치와 바이어스의 총 수입니다. 당시 수억 개의 모델이 매우 큰 것으로 여겨졌지만, 오늘날 기준에서는 이 값들이 상대적으로 작습니다.

이러한 범주별 BERT Base와 BERT Large간의 비교는 아래 이미지에서 확인할 수 있습니다.

![이미지](/assets/img/2024-05-20-ACompleteGuidetoBERTwithCode_3.png)

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

# 3 — 감정 분석을 위한 BERT Feine-Tuning

이 섹션에서는 파이썬을 사용하여 BERT를 세밀 조정하는 실제 예제를 다룹니다. 코드는 작업에 무관한 세밀 조정 파이프라인 형태로, 파이썬 클래스에 구현되어 있습니다. 그런 다음 이 클래스의 객체를 생성하고 이를 사용하여 감정 분석 작업에 대해 BERT 모델을 세밀 조정할 것입니다. 이 클래스는 질문 응답, 개체 인식 등 다른 작업에 대해 BERT를 세밀 조정하는 데 재사용할 수 있습니다. 섹션 3.1에서 3.5는 세밀 조정 과정을 설명하며, 섹션 3.6에서는 전체 파이프라인을 보여줍니다.

## 3.1 — 세밀 조정 데이터 집합 로드 및 전처리

세밀 조정의 첫 번째 단계는 특정 작업에 적합한 데이터 집합을 선택하는 것입니다. 이 예제에서는 스탠퍼드 대학이 제공하는 감정 분석 데이터 세트를 사용할 것입니다. 이 데이터 세트에는 인터넷 영화 데이터베이스 (IMDb)에서 가져온 5만 개의 온라인 영화 리뷰가 포함되어 있으며, 각 리뷰는 긍정적 또는 부정적으로 레이블이 지정되어 있습니다. 스탠퍼드 대학 웹사이트에서 데이터 세트를 직접 다운로드할 수도 있고, Kaggle에서 노트북을 만들어 작업 결과를 다른 사람들과 비교할 수도 있습니다.

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
import pandas as pd

df = pd.read_csv('IMDB Dataset.csv')
df.head()
```

![image](/assets/img/2024-05-20-ACompleteGuidetoBERTwithCode_4.png)

이제 이전의 NLP 모델과 달리 BERT와 같은 Transformer 기반 모델은 최소한의 전처리가 필요합니다. 불용어(stop words) 및 구두점을 제거하는 단계는 경우에 따라 오히려 역효과를 낼 수 있습니다. 이러한 요소들은 BERT가 입력 문장을 이해하기 위한 중요한 맥락을 제공하기 때문입니다. 그럼에도 불구하고 텍스트를 검사하여 형식 문제나 원치 않는 문자가 있는지 확인하는 것이 여전히 중요합니다. 전반적으로 IMDb 데이터셋은 꽤 깨끗합니다. 그러나 스크래핑 프로세스에서 남은 몇 가지 흔적, 예를 들면 HTML 태그(`<br />`)나 불필요한 공백과 같은 것들을 제거해야 합니다.

```js
# 줄바꿈 태그(<br />) 제거
df['review_cleaned'] = df['review'].apply(lambda x: x.replace('<br />', ''))

# 불필요한 공백 제거
df['review_cleaned'] = df['review_cleaned'].replace('\s+', ' ', regex=True)

# 첫 번째 리뷰의 72자를 비교하여 클리닝 전/후 출력
print('클리닝 전:')
print(df.iloc[1]['review'][0:72])

print('\n클리닝 후:')
print(df.iloc[1]['review_cleaned'][0:72])
```

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
Before cleaning:
A wonderful little production. <br /><br />The filming technique is very

After cleaning:
A wonderful little production. The filming technique is very unassuming-
```

감정 인코딩:

전처리의 마지막 단계는 각 리뷰의 감정을 부정인 경우 0 또는 긍정인 경우 1로 인코딩하는 것입니다. 이러한 레이블은 나중에 미세 조정 프로세스에서 분류 헤드를 훈련하는 데 사용될 것입니다.

```js
df['sentiment_encoded'] = df['sentiment'].\
    apply(lambda x: 0 if x == 'negative' else 1)
df.head()
```

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

<img src="/assets/img/2024-05-20-ACompleteGuidetoBERTwithCode_5.png" />

## 3.2 — 데이터 파인튜닝에 토큰화 적용하기

전처리된 이후, 데이터를 파인튜닝할 수 있도록 토큰화가 진행됩니다. 이 과정은 리뷰 텍스트를 개별 토큰으로 분리하고, [CLS] 및 [SEP] 특수 토큰을 추가하며, 패딩을 처리합니다. 모델에 적합한 토크나이저를 선택하는 것이 중요합니다. 다양한 언어 모델은 서로 다른 토큰화 단계가 필요하기 때문입니다 (예: GPT는 [CLS] 및 [SEP] 토큰을 요구하지 않습니다). 본 가이드에서는 Hugging Face transformers 라이브러리의 BertTokenizer 클래스를 사용할 것입니다. 이 클래스는 BERT 기반 모델과 함께 사용하도록 설계되었습니다. 토큰화가 작동하는 방식에 대한 더 자세한 설명은 본 시리즈의 Part 1을 참조하세요.

transformers 라이브러리의 Tokenizer 클래스들은 from_pretrained 메서드를 사용하여 사전 훈련된 토크나이저 모델을 간단히 생성할 수 있는 방법을 제공합니다. 이 기능을 사용하려면: 토크나이저 클래스를 가져와서 인스턴스화하고, from_pretrained 메서드를 호출하고, Hugging Face 모델 저장소에 호스팅된 토크나이저 모델 이름을 나타내는 문자열을 전달하면 됩니다. 또는 토크나이저가 요구하는 어휘 파일이 포함된 디렉토리 경로를 전달할 수도 있습니다. 이 예시에서는 모델 저장소에서 사전 훈련된 토크나이저를 사용할 것입니다. BERT를 다룰 때 주요한 옵션은 네 가지가 있으며, 각각 구글의 사전 훈련 토크나이저 어휘를 사용합니다.

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

- bert-base-uncased — BERT의 작은 버전에 대한 어휘 사전으로 대소문자를 구분하지 않습니다 (예: 토큰 Cat과 cat은 동일하게 처리됩니다)
- bert-base-cased — BERT의 작은 버전에 대한 어휘 사전으로 대소문자를 구분합니다 (예: 토큰 Cat과 cat은 동일하게 처리되지 않습니다)
- bert-large-uncased — BERT의 큰 버전에 대한 어휘 사전으로 대소문자를 구분하지 않습니다 (예: 토큰 Cat과 cat은 동일하게 처리됩니다)
- bert-large-cased — BERT의 큰 버전에 대한 어휘 사전으로 대소문자를 구분합니다 (예: 토큰 Cat과 cat은 동일하게 처리되지 않습니다)

BERT Base와 BERT Large는 동일한 어휘 사전을 사용하므로 bert-base-uncased와 bert-large-uncased 사이에는 차이가 없으며, bert-base-cased와 bert-large-cased 사이에도 차이가 없습니다. 다른 모델의 경우에는 이와 같지 않을 수 있으므로 확실하지 않을 경우 동일한 토크나이저와 모델 크기를 사용하는 것이 가장 좋습니다.

대소문자 사용 여부 선택:

대소문자 사용 여부를 선택하는 것은 데이터셋의 특성에 따라 달라집니다. IMDb 데이터셋은 인터넷 사용자가 작성한 텍스트를 포함하고 있으며 대문자 사용에 일관성이 없는 경우가 있을 수 있습니다. 예를 들어 일부 사용자는 예상대로 대문자를 생략하거나 강조를 위해 대문자를 사용할 수 있습니다. 이러한 이유로 대소문자를 무시하고 bert-base-uncased 토크나이저 모델을 사용하기로 결정했습니다.

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

다른 상황에서는 케이스를 고려하여 성능상의 이점을 볼 수 있습니다. 여기 예제 중 하나는 Named Entity Recognition 작업에서, 즉 사람, 조직, 위치 등을 텍스트에서 식별하는 것이 목표인 경우입니다. 이 경우 대문자의 존재는 단어가 누군가의 이름인지 아니면 장소인지를 식별하는 데 매우 도움이 될 수 있어서, 이 상황에서는 bert-base-cased를 선택하는 것이 더 적절할 수 있습니다.

Markdown 형식으로 표를 변경합니다.

```js
from transformers import BertTokenizer

tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
print(tokenizer)
```

```js
BertTokenizer(
  (name_or_path = "bert-base-uncased"),
  (vocab_size = 30522),
  (model_max_length = 512),
  (is_fast = False),
  (padding_side = "right"),
  (truncation_side = "right"),
  (special_tokens = {
    unk_token: "[UNK]",
    sep_token: "[SEP]",
    pad_token: "[PAD]",
    cls_token: "[CLS]",
    mask_token: "[MASK]",
  }),
  (clean_up_tokenization_spaces = True)
),
  (added_tokens_decoder = {
    0: AddedToken(
      "[PAD]",
      (rstrip = False),
      (lstrip = False),
      (single_word = False),
      (normalized = False),
      (special = True)
    ),

    100: AddedToken(
      "[UNK]",
      (rstrip = False),
      (lstrip = False),
      (single_word = False),
      (normalized = False),
      (special = True)
    ),

    101: AddedToken(
      "[CLS]",
      (rstrip = False),
      (lstrip = False),
      (single_word = False),
      (normalized = False),
      (special = True)
    ),

    102: AddedToken(
      "[SEP]",
      (rstrip = False),
      (lstrip = False),
      (single_word = False),
      (normalized = False),
      (special = True)
    ),

    103: AddedToken(
      "[MASK]",
      (rstrip = False),
      (lstrip = False),
      (single_word = False),
      (normalized = False),
      (special = True)
    ),
  });
```

인코딩 프로세스: 텍스트를 토큰으로 변환하여 토큰 ID로 변환하기.

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

다음으로, 토크나이저를 사용하여 정제된 파인 튜닝 데이터를 인코딩할 수 있어요. 이 과정은 각 리뷰를 토큰 ID들의 텐서로 변환할 거예요. 예를 들어, 리뷰인 'I liked this movie'는 다음 단계를 통해 인코딩되어요:

1. 리뷰를 소문자로 변환하기 (우리가 'bert-base-uncased'를 사용하고 있으니)

2. 리뷰를 'bert-base-uncased' 어휘에 따라 개별 토큰으로 분리하기: [`i`, `liked`, `this`, `movie`]

3. BERT가 기대하는 특수 토큰을 추가하기: [`[CLS]`, `i`, `liked`, `this`, `movie`, `[SEP]`]

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

3. 병합된 토큰을 bert-base-uncased 어휘에 따라 해당 토큰 ID로 변환합니다. (예: [CLS] - 101, i - 1045 등)

BertTokenizer 클래스의 encode 메서드는 위 과정을 사용하여 텍스트를 인코딩하고, PyTorch 텐서, Tensorflow 텐서 또는 NumPy 배열로 된 토큰 ID의 텐서를 반환할 수 있습니다. 반환 텐서의 데이터 유형은 return_tensors 인자를 사용하여 지정할 수 있으며, 각각 pt, tf, np 값을 취합니다.

```js
# 샘플 입력 문장을 인코딩합니다
sample_sentence = 'I liked this movie'
token_ids = tokenizer.encode(sample_sentence, return_tensors='np')[0]
print(f'Token IDs: {token_ids}')

# 특수 토큰이 추가된 토큰 ID를 토큰으로 다시 변환하여 확인합니다
tokens = tokenizer.convert_ids_to_tokens(token_ids)
print(f'Tokens   : {tokens}')
```

```js
Token IDs: [ 101 1045 4669 2023 3185  102]
Tokens   : ['[CLS]', 'i', 'liked', 'this', 'movie', '[SEP]']
```

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

Truncation and Padding:

BERT Base 및 BERT Large는 정확히 512토큰의 입력 시퀀스를 처리하기 위해 설계되었습니다. 그러나 입력 시퀀스가 이 제한에 맞지 않는 경우 어떻게 해야 할까요? 그 답은 Truncation과 Padding입니다! Truncation은 특정 길이 이상의 어떠한 토큰도 간단히 제거하여 토큰 수를 줄입니다. encode 메서드에서 truncation을 True로 설정하고 모든 인코딩된 시퀀스에 길이 제한을 부여할 max_length 인수를 지정할 수 있습니다. 이 데이터 세트의 여러 항목은 512토큰 제한을 초과하므로 여기서 max_length 매개변수가 모든 리뷰에서 가능한 가장 많은 텍스트를 추출하기 위해 512로 설정되었습니다. 리뷰가 512토큰을 초과하는 경우가 없다면 max_length 매개변수를 설정하지 않고 남기면 모델의 최대 길이로 기본 설정됩니다. 또는 Feine-Tuning 중에 학습 시간을 줄이기 위해 512보다 작은 최대 길이를 여전히 강제로 지정할 수 있지만 모델 성능의 손실이 발생합니다. (대부분이 그렇지만) 512토큰보다 짧은 리뷰의 경우 패딩 토큰이 추가되어 인코딩된 리뷰가 512토큰으로 확장됩니다. 이렇게 설정하는 방법에 대해서는 패딩 매개변수를 max_length로 설정하면 됩니다. encode 매서드에 대한 자세한 내용은 Hugging Face 문서를 참조하십시오 [10].

```js
review = df["review_cleaned"].iloc[0];

token_ids = tokenizer.encode(
  review,
  (max_length = 512),
  (padding = "max_length"),
  (truncation = True),
  (return_tensors = "pt")
);

print(token_ids);
```

```js
tensor([
  [
    101,
    2028,
    1997,
    1996,
    2060,
    15814,
    2038,
    3855,
    2008,
    2044,
    3666,
    2074,
    1015,
    11472,
    2792,
    2017,
    1005,
    2222,
    2022,
    13322,

    ...0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
  ],
]);
```

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

Attention Mask을 encode_plus와 함께 사용하기:

위의 예제는 데이터셋에서 첫 번째 리뷰의 인코딩을 보여줍니다. 이 리뷰에는 119개의 패딩 토큰이 포함되어 있습니다. 현재 상태로 fine-tuning에 사용된다면, BERT는 패딩 토큰에 주의를 기울일 수 있어 성능이 떨어질 수 있습니다. 이를 해결하기 위해 입력에서 특정 토큰(이 경우 패딩 토큰)을 무시하도록 BERT에 지시하는 attention mask를 적용할 수 있습니다. 위의 코드를 수정하여 encode_plus 메소드를 사용하면 이러한 attention mask를 생성할 수 있습니다. encode_plus 메소드는 표준 encode 메소드 대신 사용되며 Batch Encoder(허깅페이스의 용어)라고 불리는 딕셔너리가 반환됩니다. 이 딕셔너리는 다음과 같은 key를 포함합니다:

- input_ids — 표준 encode 메소드로부터 반환된 동일한 토큰 ID
- token_type_ids — 문장 A (id = 0)와 문장 B (id = 1)를 구분하는 데 사용되는 세그먼트 ID(예: Next Sentence Prediction과 같은 문장 쌍 작업)
- attention_mask — 특정 토큰이 attention 과정에서 무시되어야 하는지 (0을 의미) 아니면 무시되어서는 안 되는지 (1을 의미) 나타내는 0과 1의 리스트

```js
review = df["review_cleaned"].iloc[0];

batch_encoder = tokenizer.encode_plus(
  review,
  (max_length = 512),
  (padding = "max_length"),
  (truncation = True),
  (return_tensors = "pt")
);

print("Batch encoder keys:");
print(batch_encoder.keys());

print("\nAttention mask:");
print(batch_encoder["attention_mask"]);
```

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

벡터 인코더 키:
dict_keys(['input_ids', 'token_type_ids', 'attention_mask'])

주의 마스크:
tensor([[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,

                                      ...

         0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
         0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
         0, 0, 0, 0, 0, 0, 0, 0]])

모든 리뷰 인코딩:

토큰화 단계의 마지막 단계는 데이터 세트의 모든 리뷰를 인코딩하고 토큰 ID 및 해당하는 주의 마스크를 텐서로 저장하는 것입니다.

```python
import torch

토큰 ID = []
주의 마스크 = []

# 각 리뷰 인코딩
for review in df['review_cleaned']:
    batch_encoder = tokenizer.encode_plus(
        review,
        max_length = 512,
        padding = 'max_length',
        truncation = True,
        return_tensors = 'pt')

    token_ids.append(batch_encoder['input_ids'])
    attention_masks.append(batch_encoder['attention_mask'])

# 토큰 ID 및 주의 마스크 목록을 PyTorch 텐서로 변환
토큰 ID = torch.cat(토큰 ID, dim=0)
주의 마스크 = torch.cat(주의 마스크, dim=0)
```

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

## 3.3 — 트레인 및 검증 데이터로더 생성하기

이제 각 리뷰가 인코딩되었으므로 데이터를 훈련 세트와 검증 세트로 분할할 수 있습니다. 검증 세트는 피니튜닝 프로세스의 효과를 평가하는 데 사용될 것이며, 프로세스 중에 성능을 지속적으로 모니터링할 수 있도록 합니다. 에폭이 진행됨에 따라 손실이 감소하고 모델 정확도가 증가할 것으로 예상됩니다. 에폭이란 트레인 데이터를 전부 한 번 통과하는 것을 의미합니다. BERT 저자들은 플레튜닝을 위해 2~4 에폭을 권장하며, 이는 분류 헤더가 모든 리뷰를 2~4번 보게 됨을 의미합니다.

데이터를 분할하기 위해, SciKit-Learn의 model_selection 패키지에서 제공하는 train_test_split 함수를 사용할 수 있습니다. 이 함수는 분할할 데이터셋, 테스트 세트(또는 우리의 경우에는 검증 세트)로 할당할 아이템의 비율, 그리고 데이터를 무작위로 섞을지에 대한 옵션 인수를 필요로 합니다. 재현성을 위해 무작위로 섞는 매개변수를 False로 설정할 것입니다. 테스트 크기에는 0.1이라는 작은 값(10%에 해당)을 선택할 것입니다. 모델을 평가하고 수행을 정확히 파악하기 위해 충분한 데이터를 사용하는 것과 모델을 훈련하고 성능을 향상시키는 데 충분한 데이터를 유지하는 것 사이에 균형을 유지하는 것이 중요합니다. 따라서 0.1과 같은 작은 값이 종종 선호됩니다. 토큰 ID, 어텐션 마스크, 라벨을 분리한 후, 훈련 및 검증 텐서를 PyTorch TensorDatasets에 함께 그룹화할 수 있습니다. 그런 다음 이 TensorDatasets를 배치로 분할하여 훈련 및 검증용 PyTorch DataLoader 클래스를 생성할 수 있습니다. BERT 논문에서는 16 또는 32의 배치 크기를 권장합니다(즉, 모델에 16개의 리뷰와 해당 감정 라벨을 제시하고 분류 헤더에서 가중치와 바이어스를 다시 계산하기 전에). DataLoader를 사용하면 병렬 처리를 활용하여 피니튜닝 프로세스 중에 데이터를 효율적으로 모델로 로드할 수 있으며, 다중 CPU 코어를 활용할 수 있습니다.

```js
from sklearn.model_selection import train_test_split
from torch.utils.data import TensorDataset, DataLoader

val_size = 0.1

# 토큰 ID 분할
train_ids, val_ids = train_test_split(
                        token_ids,
                        test_size=val_size,
                        shuffle=False)

# 어텐션 마스크 분할
train_masks, val_masks = train_test_split(
                            attention_masks,
                            test_size=val_size,
                            shuffle=False)

# 라벨 분할
labels = torch.tensor(df['sentiment_encoded'].values)
train_labels, val_labels = train_test_split(
                                labels,
                                test_size=val_size,
                                shuffle=False)

# 데이터로더 생성
train_data = TensorDataset(train_ids, train_masks, train_labels)
train_dataloader = DataLoader(train_data, shuffle=True, batch_size=16)
val_data = TensorDataset(val_ids, val_masks, val_labels)
val_dataloader = DataLoader(val_data, batch_size=16)
```

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

## 3.4 — BERT 모델 인스턴스화

다음 단계는 미세 조정할 사전 훈련된 BERT 모델을 로드하는 것입니다. 우리는 이전과 마찬가지로 Hugging Face 모델 저장소에서 모델을 가져올 수 있습니다. Hugging Face에는 이미 분류 헤드가 연결된 여러 버전의 BERT가 있어 이 프로세스가 매우 편리합니다. 미리 구성된 분류 헤드를 가진 일부 모델의 예는 다음과 같습니다:

- BertForMaskedLM
- BertForNextSentencePrediction
- BertForSequenceClassification
- BertForMultipleChoice
- BertForTokenClassification
- BertForQuestionAnswering

물론, PyTorch 또는 Tensorflow에서 headless BERT 모델을 가져와 직접 분류 헤드를 만들 수도 있습니다. 그러나 우리의 경우에는 이미 필요한 선형 레이어를 포함하는 BertForSequenceClassification 모델을 가져와 사용할 수 있습니다. 이 선형 레이어는 무작위 가중치와 바이어스로 초기화되며, 미세 조정 중에 훈련될 것입니다. BERT Base는 768개의 임베딩 차원을 사용하므로, 숨겨진 레이어에는 모델의 최종 인코더 블록에 연결된 768개의 뉴런이 포함되어 있습니다. 출력 뉴런의 수는 num_labels 인수에 의해 결정되며, 고유한 감정 레이블의 수와 일치합니다. IMDb 데이터셋은 긍정과 부정만 포함하므로 num_labels 인수가 2로 설정됩니다. 중립 또는 혼합과 같은 레이블을 포함하여 보다 복잡한 감정 분석을 수행하려면 num_labels 값을 쉽게 조절할 수 있습니다.

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
transformers 패키지에서 BertForSequenceClassification을 가져옵니다.

model = BertForSequenceClassification.from_pretrained(
    'bert-base-uncased',
    num_labels=2)
```

## 3.5 — Optimizer, Loss Function, 및 Scheduler 인스턴스화

Optimizer:

분류 head가 학습 데이터의 일괄 처리를 만나면, 선형 레이어의 가중치와 바이어스를 업데이트하여 그 입력에 대한 모델 성능을 향상시킵니다. 여러 배치와 여러 epoch 동안, 이러한 가중치와 바이어스가 최적값으로 수렴하도록 하는 것이 목표입니다. 각 가중치와 바이어스에 필요한 변경 사항을 계산하기 위해 옵티마이저가 필요하며, PyTorch의 `optim` 패키지에서 가져올 수 있습니다. Hugging Face는 자신들의 예제에서 AdamW 옵티마이저를 사용하므로, 여기서도 이 옵티마이저를 사용할 것입니다 [13].

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

손실 함수:

옵티마이저는 분류 헤드의 가중치 및 편향에 대한 변경이 손실 함수에 어떤 영향을 미칠지 결정함으로써 작동합니다. 이 손실 함수라 불리는 점수 함수에 대한 손실은 PyTorch의 nn 패키지에서 쉽게 가져올 수 있습니다. 언어 모델은 일반적으로 교차 엔트로피 손실 함수(음의 로그 우도 함수라고도 함)를 사용하며, 따라서 여기에서는 이 손실 함수를 사용할 것입니다.

스케줄러:

학습 속도라 불리는 매개변수는 분류 헤드의 가중치와 편향에 대한 변경의 크기를 결정하는 데 사용됩니다. 초기 배치와 에포크에서는 무작위로 초기화된 매개변수가 상당한 조정이 필요할 수 있으므로 큰 변경이 유용할 수 있습니다. 그러나 학습이 진행됨에 따라 가중치와 편향이 향상되면서 큰 변경이 역효과적일 수 있습니다. 스케줄러는 학습 과정이 계속되는 동안 학습 속도를 점차 감소시켜 각 가중치 및 편향에 대한 각 최적화 단계에서 발생하는 변경 크기를 줄이도록 설계되었습니다.

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

```python
from torch.optim import AdamW
import torch.nn as nn
from transformers import get_linear_schedule_with_warmup

EPOCHS = 2

# Optimizer
optimizer = AdamW(model.parameters())

# Loss function
loss_function = nn.CrossEntropyLoss()

# Scheduler
num_training_steps = EPOCHS * len(train_dataloader)
scheduler = get_linear_schedule_with_warmup(
    optimizer,
    num_warmup_steps=0,
    num_training_steps=num_training_steps)
```

## 3.6 — Fine-Tuning Loop

CUDA를 사용하여 GPU 활용:

NVIDIA에서 만든 CUDA(Compute Unified Device Architecture)는 과학 및 공학 분야의 응용 프로그램 성능을 향상시키기 위한 컴퓨팅 플랫폼입니다 [14]. PyTorch의 cuda 패키지를 사용하면 Python에서 CUDA 플랫폼을 활용하여 머신 러닝 모델을 훈련할 때 GPU를 사용할 수 있습니다. torch.cuda.is_available 명령을 사용하여 GPU의 가용성을 확인할 수 있습니다. GPU가 없는 경우 코드는 가속 계산을 위해 그래픽 처리 장치 (GPU)를 사용할 수 없도록 기본 설정됩니다. 이후의 코드 스니펫에서는 PyTorch Tensor.to 메서드를 사용하여 텐서(모델 가중치 및 편향 등이 포함됨)를 더 빠른 계산을 위해 GPU로 이동합니다. 장치가 cpu로 설정된 경우 텐서가 이동되지 않고 코드에 영향을 미치지 않습니다.

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

# GPU 사용 가능 여부 확인하여 빠른 학습 시간을 위한 준비

if torch.cuda.is_available():
device = torch.device('cuda:0')
else:
device = torch.device('cpu')

학습 프로세스는 두 개의 for 루프를 통해 이루어집니다: 각 epoch마다 프로세스를 반복하는 외부 루프(모델이 모든 학습 데이터를 여러 번 보게하는 역할)와 각 배치마다 손실 계산 및 최적화 단계를 반복하는 내부 루프가 있습니다. 학습 루프를 설명하기 위해 아래 단계를 고려해 보세요. 학습 루프의 코드는 Chris McCormick과 Nick Ryan의 훌륭한 블로그 글[15]에서 적용되었으며 매우 추천합니다.

각 epoch에 대해:

1. 모델을 train 모드로 변경합니다. 모델 객체의 train 메서드를 사용하여 모델이 평가 모드일 때와는 다르게 작동하도록 합니다. 특히 batchnorm과 dropout 레이어와 함께 작업할 때 유용합니다. 이전에 BertForSequenceClassification 클래스의 소스 코드를 살펴봤다면, 분류 헤드에 실제로 dropout 레이어가 포함되어 있는 것을 보았을 겁니다. 따라서 fine-tuning 시에 training 및 evaluation 모드를 올바르게 구분해야 합니다. 이러한 종류의 레이어는 학습 중에만 활성화되어야 하며 추론 중에는 활성화되지 않아야 합니다. 따라서 학습과 추론을 위해 서로 다른 모드로 전환할 수 있는 기능은 유용한 기능입니다.

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

2. 에포크 시작 시 훈련 손실을 0으로 설정하세요. 이것은 이후 에포크에서 모델의 훈련 데이터 손실을 추적하는 데 사용됩니다. 훈련이 성공적이라면 각 에포크마다 손실이 감소해야 합니다.

각 배치에 대해:

BERT 저자들의 권장에 따라, 각 에포크의 훈련 데이터를 배치로 나누세요. 각 배치마다 훈련 프로세스를 반복하세요.

3. 가능한 경우 토큰 ID, 어텐션 마스크 및 레이블을 GPU로 이동하여 처리 속도를 높이세요. 그렇지 않으면 이러한 데이터는 CPU에 유지됩니다.

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

4. 이 루프의 이전 반복에서 계산된 그래디언트를 재설정하기 위해 zero_grad 메서드를 호출하세요. PyTorch에서 기본 동작이 아닌 이유가 명확하지 않을 수 있지만, 이는 Recurrent Neural Networks와 같은 모델이 반복 사이에 그래디언트를 재설정해선 안 되는 이유로 제안됩니다.

5. 배치를 모델에 전달하여 로짓(현재의 분류기 가중치와 편향에 기반한 예측)과 손실을 계산하세요.

6. 에폭별 총 손실을 증가시키세요. 모델에서 손실이 PyTorch 텐서로 반환되므로 `item` 메서드를 사용하여 부동 소수점 값을 추출하세요.

7. 모델에 역전파를 수행하고 분류기 헤드를 통해 손실을 전파하세요. 이를 통해 모델은 배치에 대한 성능을 향상시키기 위해 가중치와 편향을 조정해야 하는지를 결정할 수 있습니다.

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

8. 모델이 폭발하는 그래디언트 문제를 겪지 않도록 그래디언트를 1.0보다 크게 만들지 마세요.

9. 역전파에 따라 오차 표면 방향으로 옵티마이저를 호출하여 한 단계씩 진행하세요.

각 배치 훈련 후:

10. 에포크에서의 평균 손실과 소요 시간을 계산하세요.

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

for epoch in range(0, EPOCHS):

    model.train()
    training_loss = 0

    for batch in train_dataloader:

        batch_token_ids = batch[0].to(device)
        batch_attention_mask = batch[1].to(device)
        batch_labels = batch[2].to(device)

        model.zero_grad()

        loss, logits = model(
            batch_token_ids,
            token_type_ids = None,
            attention_mask=batch_attention_mask,
            labels=batch_labels,
            return_dict=False)

        training_loss += loss.item()
        loss.backward()
        torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)
        optimizer.step()
        scheduler.step()

    average_train_loss = training_loss / len(train_dataloader)

외부 루프 내에서 검증 단계가 수행되므로 각 epoch마다 평균 검증 손실을 계산합니다. epoch 숫자가 증가함에 따라 검증 손실이 감소하고 분류기 정확도가 증가할 것으로 기대됩니다. 검증 프로세스 단계는 아래에 설명되어 있습니다.

에포크의 검증 단계:

11. evaluation 메서드를 사용하여 모델을 평가 모드로 전환합니다. 이렇게 하면 드롭아웃 레이어가 비활성화됩니다.

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

12. 검증 손실을 0으로 설정하세요. 이 값은 후속 에포크에서 모델의 검증 데이터에 대한 손실을 추적하는 데 사용됩니다. 학습이 성공적이었다면 손실은 각 에포크마다 감소해야 합니다.

13. 검증 데이터를 배치로 나누세요.

각 배치에 대해:

14. 사용 가능한 경우 토큰 ID, 어텐션 마스크 및 레이블을 GPU로 이동하여 처리 속도를 높이세요. 그렇지 않으면 이러한 값들은 CPU에 유지됩니다.

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

15. 여기서는 최적화 단계를 수행하지 않고 추론만 할 것이기 때문에 모델이 그라디언트를 계산하지 않도록 no_grad 메서드를 호출하세요.

16. 배치를 모델에 전달하여 로짓(현재 분류기의 가중치와 편향을 기반으로 한 예측)와 손실을 계산하세요.

17. 모델에서 로짓과 레이블을 추출하여 CPU로 이동하세요 (이미 CPU에 있지 않은 경우).

18. 손실을 증가시키고 검증 데이터로더의 실제 레이블에 기반하여 정확도를 계산하세요.

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

19. 손실 및 정확도의 평균을 계산하세요.

```js
    model.eval()
    val_loss = 0
    val_accuracy = 0

    for batch in val_dataloader:

        batch_token_ids = batch[0].to(device)
        batch_attention_mask = batch[1].to(device)
        batch_labels = batch[2].to(device)

        with torch.no_grad():
            (loss, logits) = model(
                batch_token_ids,
                attention_mask = batch_attention_mask,
                labels = batch_labels,
                token_type_ids = None,
                return_dict=False)

        logits = logits.detach().cpu().numpy()
        label_ids = batch_labels.to('cpu').numpy()
        val_loss += loss.item()
        val_accuracy += calculate_accuracy(logits, label_ids)

    average_val_accuracy = val_accuracy / len(val_dataloader)
```

위의 코드 스니펫의 끝에서 calculate_accuracy 함수를 사용하고 있지만 아직 정의하지 않았으니, 이제 정의해 보겠습니다. 모델의 검증 세트에서의 정확도는 옳은 예측의 비율로 주어집니다. 따라서 모델에 의해 생성된 로짓 값, 즉 변수 logits에 저장된 값을 사용할 수 있고, 이를 NumPy의 argmax 함수를 이용할 수 있습니다. argmax 함수는 배열에서 가장 큰 요소의 인덱스를 반환합니다. 텍스트 I liked this movie에 대한 로짓이 [0.08, 0.92]인 경우, 0.08은 텍스트가 부정일 확률을 나타내고 0.92는 텍스트가 긍정일 확률을 나타내므로 argmax 함수는 인덱스 1을 반환할 것입니다. 모델은 텍스트가 부정보다 긍정일 가능성이 더 높다고 판단합니다. 그런 다음 해당 레이블을 Section 3.3(19번째 줄)에서 이미 인코딩한 labels 텐서와 비교하면 됩니다. 로짓 변수에는 배치(총 16개)의 모든 리뷰에 대한 긍정 및 부정 확률 값이 포함되므로 모델의 정확도는 최대 16개의 옳은 예측 중 계산됩니다. 위의 코드는 val_accuracy 변수가 각 정확도 점수를 기록하고, 검증을 마친 후에 모델의 검증 데이터에 대한 평균 정확도를 결정하기 위해 나누는 것을 보여줍니다.

```js
def calculate_accuracy(preds, labels):
    """ 모델 예측과 실제 레이블의 정확도를 계산합니다.

    매개변수:
        preds (np.array): 모델의 예측된 레이블
        labels (np.array): 실제 레이블

    반환값:
        정확도 (float): 올바른 예측의 백분율로 정확도가 반환됩니다.
    """
    pred_flat = np.argmax(preds, axis=1).flatten()
    labels_flat = labels.flatten()
    accuracy = np.sum(pred_flat == labels_flat) / len(labels_flat)

    return accuracy
```

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

## 3.7 — 완벽한 파인튜닝 파이프라인

그렇게 해서 우리는 파인튜닝의 설명을 마쳤습니다! 아래 코드는 위의 모든 것을 하나의 재사용 가능한 클래스로 가져와서 BERT를 사용하는 모든 NLP 작업에 사용할 수 있습니다. 데이터 전처리 단계는 작업에 따라 다르기 때문에 이는 파인튜닝 클래스 밖으로 뺐습니다.

IMDb 데이터셋을 이용한 감정 분석을 위한 데이터 전처리 함수:

```python
def preprocess_dataset(path):
    """ 불필요한 문자를 제거하고 감정 레이블을 인코딩합니다.

    필요한 전처리 유형은 데이터셋에 따라 변경됩니다. IMDb 데이터셋의 경우, 리뷰 텍스트에는 스크래핑 과정에서 남아 있는 HTML 줄 바꿈 태그 (<br/>)와 일부 불필요한 공백이 있습니다. 이를 제거합니다. 마지막으로, "부정"에 대한 감정 값을 0으로, "긍정"에 대한 감정 값을 1로 인코딩합니다. 이 메서드는 데이터셋 파일에 "review" 및 "sentiment" 헤더가 포함되어 있다고 가정합니다.

    매개변수:
        path (str): 감정 분석 데이터셋을 포함하는 데이터셋 파일의 경로입니다. 파일 구조는 다음과 같아야 합니다:
            리뷰 텍스트를 담은 "review" 열과, ground truth 레이블을 담은 "sentiment" 열이 하나씩 있는 한 열인 파일입니다.
            레이블 옵션은 "부정"과 "긍정"이어야 합니다.

    반환:
        df_dataset (pd.DataFrame): self.dataset 경로에서 로드한 원시 데이터가 있는 DataFrame입니다. "review"와 "sentiment" 열 외에도 다음이 포함됩니다:
            - review_cleaned: "review" 열의 사본이고 HTML 줄 바꿈 태그와 불필요한 공백이 제거된 컬럼
            - sentiment_encoded: "sentiment" 열의 사본이며 "부정" 값을 0으로 매핑하고 "긍정" 값을 1로 매핑한 컬럼



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

작업에 중립적인 파인튜닝 파이프라인 클래스:


from datetime import datetime
import numpy as np
import pandas as pd
from sklearn.model_selection import train_test_split
import torch
import torch.nn as nn
import torch.nn.functional as F
from torch.optim import AdamW
from torch.utils.data import TensorDataset, DataLoader
from transformers import (
    BertForSequenceClassification,
    BertTokenizer,
    get_linear_schedule_with_warmup)


class FineTuningPipeline:

    def __init__(
            self,
            dataset,
            tokenizer,
            model,
            optimizer,
            loss_function = nn.CrossEntropyLoss(),
            val_size = 0.1,
            epochs = 4,
            seed = 42):

        self.df_dataset = dataset
        self.tokenizer = tokenizer
        self.model = model
        self.optimizer = optimizer
        self.loss_function = loss_function
        self.val_size = val_size
        self.epochs = epochs
        self.seed = seed

        # Check if GPU is available for faster training time
        if torch.cuda.is_available():
            self.device = torch.device('cuda:0')
        else:
            self.device = torch.device('cpu')

        # Perform fine-tuning
        self.model.to(self.device)
        self.set_seeds()
        self.token_ids, self.attention_masks = self.tokenize_dataset()
        self.train_dataloader, self.val_dataloader = self.create_dataloaders()
        self.scheduler = self.create_scheduler()
        self.fine_tune()

    def tokenize(self, text):
        """ Tokenize input text and return the token IDs and attention mask.

        Tokenize an input string, setting a maximum length of 512 tokens.
        Sequences with more than 512 tokens will be truncated to this limit,
        and sequences with less than 512 tokens will be supplemented with [PAD]
        tokens to bring them up to this limit. The datatype of the returned
        tensors will be the PyTorch tensor format. These return values are
        tensors of size 1 x max_length where max_length is the maximum number
        of tokens per input sequence (512 for BERT).

...


감정 분석을 위한 클래스 사용 예 (IMDb 데이터셋):

# 매개변수 초기화
dataset = preprocess_dataset('IMDB Dataset Very Small.csv')
tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
model = BertForSequenceClassification.from_pretrained(
    'bert-base-uncased',
num_labels=2)
optimizer = AdamW(model.parameters())

# 클래스를 사용하여 모델을 파인튜닝
fine_tuned_model = FineTuningPipeline(
    dataset=dataset,
    tokenizer=tokenizer,
    model=model,
    optimizer=optimizer,
    val_size=0.1,
    epochs=2,
    seed=42
)

# 유효성 검사 데이터셋을 사용하여 일부 예측 수행
model.predict(model.val_dataloader)

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

# 4 — 결론

이 글에서는 BERT의 여러 측면을 탐색했습니다. BERT의 창시 시점의 배경, 모델 아키텍처의 자세한 분석 및 감성 분석을 사용하여 시업 무관한 미세 조정 파이프라인 작성을 포함했습니다. BERT는 가장 초기의 LLM 중 하나임에도 불구하고, 오늘날에도 여전히 중요하며 연구 및 산업 분야에서 응용 프로그램을 발전시키고 있습니다. BERT를 이해하고 NLP 분야에 미치는 영향을 이해하면 최신 고품질 모델을 다루는 데 튼실한 기반을 다질 수 있습니다. 미세 조정 및 사전 훈련이 LLM의 지배적 패러다임으로 유지되고 있으므로, 이 글이 여러분의 프로젝트에 적용해 가며 가치 있는 통찰을 제공했기를 바랍니다!

# 5 — 추가 자료

[1] J. Devlin, M. Chang, K. Lee, and K. Toutanova, BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (2019), North American Chapter of the Association for Computational Linguistics

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

[2] A. Vaswani, N. Shazeer, N. Parmar, J. Uszkoreit, L. Jones, A. N. Gomez, Ł. Kaiser, and I. Polosukhin, Attention is All You Need (2017), Advances in Neural Information Processing Systems 30 (NIPS 2017)

[3] M. E. Peters, M. Neumann, M. Iyyer, M. Gardner, C. Clark, K. Lee, and L. Zettlemoyer, Deep contextualized word representations (2018), Proceedings of the 2018 Conference of the North American Chapter of the Association for Computational Linguistics: Human Language Technologies, Volume 1 (Long Papers)

[4] A. Radford, K. Narasimhan, T. Salimans, and I. Sutskever (2018), Improving Language Understanding by Generative Pre-Training,

[5] Hugging Face, Fine-Tuned BERT Models (2024), HuggingFace.co

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

## 논문 및 참고 자료 목록

- M. Schuster 및 K. K. Paliwal, Bidirectional recurrent neural networks (1997), IEEE Signal Processing 트랜잭션 45

- Y. Zhu, R. Kiros, R. Zemel, R. Salakhutdinov, R. Urtasun, A. Torralba 및 S. Fidler, Aligning Books and Movies: Towards Story-like Visual Explanations by Watching Movies and Reading Books (2015), 2015 IEEE International Conference on Computer Vision (ICCV)

- L. W. Taylor, “Cloze Procedure”: A New Tool for Measuring Readability (1953), Journalism Quarterly, 30(4), 415–433.

- Hugging Face, Pre-trained Tokenizers (2024) HuggingFace.co

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

[10] Hugging Face, Pre-trained Tokenizer Encode Method (2024) HuggingFace.co

[11] T. Vo, PyTorch DataLoader: Features, Benefits, and How to Use it (2023) SaturnCloud.io

[12] Hugging Face, Modelling BERT (2024) GitHub.com

[13] Hugging Face, Run Glue, GitHub.com

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

[14] NVIDIA, CUDA Zone (2024), Developer.NVIDIA.com

[15] C. McCormick and N. Ryan, BERT Fine-tuning (2019), McCormickML.com
```
