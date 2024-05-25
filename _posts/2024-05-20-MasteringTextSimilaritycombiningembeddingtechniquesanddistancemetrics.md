---
title: "텍스트 유사성 마스터하기 임베딩 기술과 거리 측정 결합하기"
description: ""
coverImage: "/assets/img/2024-05-20-MasteringTextSimilaritycombiningembeddingtechniquesanddistancemetrics_0.png"
date: 2024-05-20 20:54
ogImage: 
  url: /assets/img/2024-05-20-MasteringTextSimilaritycombiningembeddingtechniquesanddistancemetrics_0.png
tag: Tech
originalTitle: "Mastering Text Similarity: combining embedding techniques and distance metrics"
link: "https://medium.com/eni-digitalks/mastering-text-similarity-combining-embedding-techniques-and-distance-metrics-98d3bb80b1b6"
---


"집중하고 있니?" "집중하고 있니?" 이 두 문장은 같은 의미인가요? 기사를 읽고 알고리즘의 답변을 찾아보세요!

![image](/assets/img/2024-05-20-MasteringTextSimilaritycombiningembeddingtechniquesanddistancemetrics_0.png)

두 개의 텍스트를 비교하는 것은 보편적이고 중요한 작업이라고 보는 경우가 많습니다. 고객 서비스에서 AI 시스템은 동의어적 의미를 이해하여 인간 대화의 유동성을 반영하는 응답을 생성할 수 있어야 합니다. 예를 들어, "비밀번호를 어떻게 복구할 수 있나요?" 또는 "비밀번호를 잊었어요. 다시 로그인하는 방법이 뭐에요?"와 같은 질문은 의미가 유사하며 동일한 응답을 요구합니다. 게다가, 고객 상호작용 중에 대리인이 계약이나 제안에 대한 정보를 정확하게 전달하는지 확인해야 하는 경우가 종종 있습니다. 또한, 검색 엔진이나 Stack Overflow와 같은 플랫폼에서 이전에 질문이 제기되었는지 알아내야 할 필요가 있습니다. 본질적으로 텍스트 유사성을 빠르게 계산할 수 있는 능력은 효율성 향상과 고객 관계 향상의 기초가 됩니다.

인간은 의미론적 및 문법적 관계를 본성적으로 쉽게 이해하지만, 기계는 같은 작업을 수행하는 데 더 복잡한 도전에 직면합니다.

<div class="content-ad"></div>

먼저, 유사성을 더 구체적으로 정의해 봅시다: 유사성은 두 데이터 객체가 얼마나 다르거나 비슷한지를 측정한 것입니다. 거리가 짧을수록 객체들은 높은 수준의 유사성을 갖는다고 말하며, 그 반대도 마찬가지입니다.

텍스트 유사성은 두 텍스트 조각이 어휘적으로(사용된 단어)와 의미론적으로(단어의 의미) 얼마나 가까운지를 나타냅니다. 예를 들어, "병이 비어 있습니다"와 "병 안에는 아무 것도 없습니다"라는 문장은 의미론적으로는 동일하지만 어휘적으로는 다릅니다.

기계가 텍스트 유사성을 계산할 수 있도록하기 위해, 먼저 기계의 언어인 숫자를 기반으로 한 언어를 사용해야 합니다.

그러므로, 텍스트 유사성을 평가하는 핵심 단계 중 하나는 텍스트를 벡터로 변환하는 것입니다. 벡터는 공간에서 크기와 방향을 나타내는 숫자 요소이기도 합니다. 이 프로세스는 텍스트 벡터화 또는 텍스트 인코딩이라고 알려져 있습니다. 유사성을 평가하기 위한 후속 단계는 이러한 벡터들 간의 거리를 측정하는 것입니다. 이 거리를 계산하는 데 선택된 측정 항목이 중요합니다.

<div class="content-ad"></div>

이 기사에서는 텍스트를 임베딩하고 거리를 계산하는 다양한 기술의 장단점을 포괄적으로 탐구하여 귀하의 요구에 맞는 최적의 조합을 찾을 수 있는 포괄적인 안내서를 제공합니다.

자세히 들어가기 전에 이 기사의 상위 수준 색인으로 볼 수 있는 다음 그림을 살펴보겠습니다.

![image](/assets/img/2024-05-20-MasteringTextSimilaritycombiningembeddingtechniquesanddistancemetrics_1.png)

텍스트 표현은 4가지의 주요 방법으로 군집화될 수 있습니다: 문자 기반, 의미론적 텍스트 일치, 말뭉치 기반(말뭉치는 언어 분석에 사용되는 텍스트 문서의 집합을 가리킵니다), 및 그래프 구조입니다.

<div class="content-ad"></div>

문자열 거리는 길이 거리, 분포 거리 및 의미적 거리로 나뉠 수 있습니다.

우리는 위 그림에서 볼드 처리된 주제들에 대해서만 다룰 것입니다. 가장 쉬운 방법부터 가장 복잡한 방법까지 시작하겠습니다.

## 문자열 기반 텍스트 표현

### 자카드 유사도

<div class="content-ad"></div>

자카드 유사도, 또는 교집합 오버 유니온이라고도 알려지고, 두 개의 텍스트의 유사성을 측정하는 방법으로, 공통 단어의 개수를 전체 단어 수로 나눈 비율로 표현됩니다.

다음과 같은 수식으로 설명됩니다:

앞서 언급한 두 문장을 고려해 봅시다:

- A: 병은 비어 있습니다
- B: 병 안에는 아무것도 없습니다

<div class="content-ad"></div>

![image](/assets/img/2024-05-20-MasteringTextSimilaritycombiningembeddingtechniquesanddistancemetrics_2.png)

자카드 유사도에 따르면, 문장 A와 B는 달리 의미를 가지고 있습니다. 이는 단순히 문장을 리터럴 레벨에서 비교하기 때문입니다.

자카드 유사도는 쉽게 계산할 수 있지만 의미적 관계를 포착하지 못하므로, 텍스트에서 사용된 단어를 비교하는 것이 필수적이지 않은 이상 권장되지 않습니다.

자카드 유사도에 따르면, 문장 A와 B는 달리 의미를 가지고 있습니다.

<div class="content-ad"></div>

표 태그를 Markdown 형식으로 변경하겠습니다.

# Corpus based text representation

앞서 언급한 대로, 단어가 숫자로 표현되는 방식은 유사성을 평가하는 데 중요합니다. 이제 텍스트 표현을 위한 다양한 기술을 탐색해보겠습니다.

## Bag of words

<div class="content-ad"></div>

Bag of words 기반 기술은 단어의 순서와 관계없이 문서를 단어들의 조합으로 나타냅니다. 이 가족 중에서 가장 간단한 방법은 원핫 인코딩입니다.

원핫 인코딩에 따르면 고유 단어의 총 수만큼의 크기를 가진 벡터가 생성됩니다. 각 단어의 값은 해당하는 인덱스에 1이 할당되고 나머지는 0입니다.

이는 말뭉치의 다양성이 적고 데이터 간 의미 및 통계적 관계를 나타내는 필요가 없는 상황에서 일반적으로 사용됩니다. 큰 문서의 경우, 방대하고 희소한 벡터로 이어질 수 있습니다.

조금 더 세련된 방법은 TF-IDF (단어 빈도-역문서 빈도)입니다. 이는 빈도가 높은 단어는 중요성이나 의미가 적다는 아이디어에 기반합니다.

<div class="content-ad"></div>

수학적으로 말하면:

TF = 문서에 단어가 나타나는 횟수 / 문서 내 전체 단어 수

IDF = log(N/n)

여기서 N은 전체 문서 수이고, n은 대상 용어가 나타나는 문서 수입니다.

<div class="content-ad"></div>

0s를 제거하려면 모든 문서에 단어가 있는 경우 TF*IDF 곱에 1을 더하므로, 벡터의 0은 단어의 부재를 나타냅니다.

예제를 통해 이해해 봅시다. 다음과 같은 문장들을 고려해 보겠습니다:

- He is Walter
- He is William
- He isn’t Peter or September

TF 벡터는 다음과 같습니다:

<div class="content-ad"></div>

- [0.33, 0.33, 0.33]
- [0.33, 0.33, 0.33]
- [0.20, 0.20, 0.20, 0.20, 0.20]

이제 IDF 점수를 계산해 봅시다.

- "He": Log(3/3) = 0,
- "is": Log(3/2) = 0.1761,
- "or, Peter, ..": Log(3/1) = 0.4771 ..

결과 벡터는:

<div class="content-ad"></div>

- [1. , 1.1761 , 1.4771 , 0. , 0. , 0. , 0. , 0.],
- [1. , 1.1761 , 0. , 1.4771 , 0. , 0. , 0. , 0.],
- [1. , 0. , 0. , 0. , 1.4771 , 1.4771, 1.4771 , 1.4771]

이 방법은 통계적 관계를 가치화하지만 의미론적 관계를 대변하지 않고, 긴 문서에 적합하지 않습니다. 이는 고차원 벡터로 이어집니다.

일반적으로, 대부분의 단어 가방 접근 방식은 의미론적 관계를 중요시하지 않고, 큰 문서에 대해 데이터 희소성으로 이어질 수 있습니다. 따라서, 텍스트 표현에 대한 복잡한 기술인 단어 임베딩 중 하나로 분류될 수 있는 더 복잡한 기술에 대해 심층적으로 살펴보겠습니다.

## 창 기반 방법

<div class="content-ad"></div>

이 방법은 통계적 관계를 중요시하지만 의미론적 관계를 나타내지는 않고, 긴 문서에는 적합하지 않습니다. 따라서 고차원 벡터로 이어질 수 있습니다.

일반적으로 단어 가방 접근 방식 중 대부분은 의미적 관계를 중요시하지 않을 수 있고, 대규모 문서의 데이터 희소성으로 이어질 수 있습니다. 그러니 텍스트 표현에 대해 더 복잡한 기법으로 다양한 단어 표현 기술 중 하나로 분류될 수 있는 기법을 자세히 살펴보도록 하겠습니다.

## Word2Vec

W2V는 미리 훈련된 두 개의 레이어로 이루어진 신경망입니다. W2V 방식은 기계 학습에서 흔히 사용되는 속임수를 사용합니다: 단일 숨겨진 레이어를 가진 신경망이 특정 작업을 수행하도록 훈련되지만 최종 작업에 사용되지는 않습니다. 실제로 목표는 숨겨진 레이어의 가중치를 학습하는 것뿐입니다.

<div class="content-ad"></div>

W2V는 두 가지 사전 훈련 모델을 가지고 있어요: 연속 단어 주머니 (Continuous Bag Of Words, CBOW)와 스킵-그램.

![이미지](/assets/img/2024-05-20-MasteringTextSimilaritycombiningembeddingtechniquesanddistancemetrics_3.png)

사진에서 볼 수 있듯이, CBOW에서는 대상 단어에 인접한 단어가 입력으로 주어지고 대상 단어를 예측하는 작업을 하며, 스킵-그램에서는 대상 단어가 입력으로 주어지고 이웃하는 단어들을 출력으로 예측해야 합니다. 이웃 단어로 고려할 단어 수를 "윈도우 크기"라고 하며, 이는 알고리즘의 매개변수입니다 (윈도우 크기의 일반적인 값은 5일 수 있어요).

임베딩이 어떻게 생성되는지 이해하기 위해, 스킵-그램에 집중해봅시다.

<div class="content-ad"></div>

훈련 작업의 첫 번째 단계는 문서에 있는 단어들을 인코딩하는 것인데, 일반적으로 이는 원핫인코딩 방식으로 수행됩니다.

![image](/assets/img/2024-05-20-MasteringTextSimilaritycombiningembeddingtechniquesanddistancemetrics_4.png)

출력은 코퍼스에 있는 단어 수와 동일한 길이의 단일 벡터이며, 각 요소는 입력 단어의 이웃 단어가 될 가능성을 나타냅니다.

만약 두 단어가 매우 유사한 문맥을 가진다면, 주변에 같은 단어들이 있을 가능성이 높다는 것을 의미하며, 모델은 이러한 단어들에 대해 유사한 결과를 생성하여야 합니다. 네트워크가 이를 달성하는 방법 중 하나는 단어 벡터가 유사하도록 하는 것입니다. 그러므로 두 단어가 유사한 문맥을 보여줄 때, 네트워크는 이러한 단어들에 대해 유사한 단어 벡터를 모으도록 자극을 받게 됩니다.

<div class="content-ad"></div>

두 종류의 네트워크를 비교해 보면, CBOW는 문법적 관계 학습에 뛰어나지만 Skip-gram은 의미적 관계를 파악하는 데 좀 더 우수합니다. 예를 들어, CBOW는 복수형과 같이 형태적으로 유사한 단어에 초점을 맞추지만 Skip-gram은 형태적으로 다른데 의미적으로 관련 있는 단어를 고려합니다. 게다가, Skip-gram은 빈번한 단어의 과적합에 덜 민감하며, 단어 하나만을 입력으로 사용하기 때문에 최적의 성능을 위한 문서 요구 사항 측면에서 더 효율적입니다.

W2V 임베딩은 Spacy나 Genism에서 구현됩니다.

이 접근법은 고차원 문제를 해결하고 의미론적 및 문법적 관계를 고려하지만, 단어의 맥락을 고려하지 않는 한계가 있어서 다의성의 경우 성능이 떨어질 수 있습니다. 예를 들어, "current"라는 단어는 다음 두 문장에서 각각 다른 의미를 가집니다:

- 현재 사안 프로그램입니다.
- 시내는 다리 아래로 빨리 흐릅니다.

<div class="content-ad"></div>

W2V 방법에 따르면 세계의 현재 상태는 하나의 표현만을 갖게 될 것입니다.

맥락 모델은 해당 문서의 모든 단어의 순서를 고려하여 대상 단어를 포함하는데 사용됩니다.

자연어 처리 세계에서 가장 중요한 알고리즘 중 하나를 탐색해봅시다: BERT.

## BERT

<div class="content-ad"></div>

알고리즘에 대한 간결한 개요를 제공하겠습니다. 아키텍처 및 훈련의 중요 측면을 강조하여 문맥적 임베딩의 성취를 이해하는 데 필요한 것을 설명하겠습니다. BERT를 보다 자세히 탐구하려면 이 글의 마지막에 링크된 추가 자료를 참고하시기를 권합니다.

BERT는 Bidirectional Encoder Representation from Transformers의 약자입니다. 이름에서 알 수 있듯이 BERT 아키텍처는 transformers를 기반으로 하며 실제로 양방향 언어 transformers를 사용하여 언어 표현을 합니다.

BERT는 두 가지 다른 작업을 위해 사전 훈련되었습니다: Masked Language Modelling (MLM) 및 Next Sentence Prediction (NSP).

첫 번째 작업인 MLM부터 시작해 보겠습니다. 말뭉치에 있는 단어 중 15%가 "마스킹"되었다고 가정됩니다. 이들 중에서:

<div class="content-ad"></div>

- 단어의 80%가 가리킨 토큰 [MASK]로 대체됩니다.
- 10%는 무작위 단어로 대체됩니다.
- 10%는 바뀌지 않은 채로 남겨집니다.

해당 작업은 마스킹된 단어를 예측하는 것입니다. 특히 각 토큰을 마스킹하는 방식은 모형에 매우 중요합니다:

- 단어를 토큰 [MASK]로 대체하면 일반 토큰을 제공하는 대신 주변 텍스트에서만 토큰을 추론할 수 있도록 합니다.
- 샘플링된 단어를 텍스트 내에서 무작위 단어로 대체하고 예측을 강화하면 모형이 잘못된 토큰에 대응하는 강건함을 향상시킵니다. 모형은 예상치 못한 또는 맥락을 벗어난 단어를 효과적으로 다룰 수 있도록 합니다.
- 샘플링된 단어를 바꾸지 않고 예측하면 모형이 텍스트의 원래 의미론적 및 구문 구조를 유지합니다. 모형은 잘못된 맥락에 대처하는 강건함을 향상시킵니다.

이 세 가지 마스킹 단어 방식의 결합은 모형을 다양한 NLP 작업에서 강건하고 다재다능하게 만들어줍니다.

<div class="content-ad"></div>

두 번째 과제인 NSP는 문장 간의 관계를 학습하는 데 초점을 맞춥니다: 코퍼스 내 문장 쌍의 50%에 대해 두 번째 문장이 실제로 다음 문장인 경우가 있고, 나머지 쌍에 대해서는 두 번째 문장이 무작위로 선택된 문장입니다. 첫 번째 경우는 "isNext"로 레이블이 지정되고, 두 번째 경우는 "NotNext"로 레이블이 지정됩니다. 이 과제는 올바른 레이블을 예측하도록 하는 것으로, BERT가 문장 간 관계(예: 질문과 답변)를 학습할 수 있게 합니다.

BERT 모델의 훈련 과정에서 Masked Language Model(MLM) 및 Next Sentence Prediction(NSP) 구성 요소는 함께 훈련되어, 이 두 전략에서 발생하는 결합 손실 함수를 최소화하도록 하고 올바른 레이블을 예측합니다.

BERT의 강점은 이러한 작업을 수행하기 위해 입력 모델을 어떻게 모델링하는지에 있습니다.

각 입력 임베딩은 3가지 임베딩의 조합으로 이루어져 있습니다:

<div class="content-ad"></div>

- 위치 임베딩: 문장 내 단어의 위치를 표현하는 데 사용되는 임베딩입니다. 이러한 요소들은 Transformer의 제약을 해결하기 위해 도입되었는데, 순환 신경망과 달리 순차적 정보를 포착하는 능력이 없는 Transformer의 한계를 극복하기 위해 도입되었습니다.
- 세그먼트 임베딩: 문장 쌍을 식별하는 임베딩입니다. BERT는 모델이 두 문장을 구분할 수 있도록 첫 번째 문장과 두 번째 문장에 대해 고유한 임베딩을 학습합니다. 아래 그림에서 EA로 표시된 모든 토큰은 문장 A에 속하며, EB도 비슷하게 문장 B에 속합니다.
- 토큰 임베딩: 첫 번째 문장의 시작 부분에 [CLS] 토큰이 삽입되고, 각 문장의 끝 부분에는 [SEP] 토큰이 삽입됩니다.

위 3가지 임베딩을 합산하여 각 입력을 얻습니다.

<img src="/assets/img/2024-05-20-MasteringTextSimilaritycombiningembeddingtechniquesanddistancemetrics_5.png" />

미리 학습된 후, 모델은 특정 말뭉치에서 세밀하게 튜닝될 수 있습니다.

<div class="content-ad"></div>

사전 훈련된 BERT의 버전은 분류(감성 분석), 질의응답, Named Entity Recognition과 같은 특정 작업에 사용할 수 있습니다. 그러나 저희는 임베딩 기법 중 한 가지로 이 알고리즘을 언급했기 때문에, 어떻게 이를 이용하는지 간단히 설명하겠습니다. 이 응용 프로그램의 아이디어는 W2V에 언급된 것과 유사합니다. 사실, 우리는 사전 훈련된 목적으로 그 모델을 사용하지 않습니다. BERT 베이스 모델은 12개의 트랜스포머 인코더 레이어를 사용하며, 각 레이어에서 각 토큰의 출력을 단어 임베딩으로 사용할 수 있습니다. 경험적인 연구를 바탕으로, 저자들은 매우 효과적인 접근 방식은 마지막 4개 레이어의 출력을 합하는 것임을 결정했습니다. BERT를 사용한 임베딩은 Hugging Face의 오픈 소스 라이브러리를 사용하여 Python에서 쉽게 구현할 수 있습니다. 이 라이브러리는 BERT를 PyTorch 또는 TensorFlow에서 사용할 수 있도록 제공합니다.

# 거리 측정 방법

지금까지 우리의 탐구는 텍스트 유사도를 측정하는 한 가지 방법(자카드 유사도)과 텍스트를 벡터로 변환하는 여러 기술에만 집중해 왔습니다. 소개에서 언급했듯이, 단어를 벡터로 변환하는 것은 유사도를 평가하기 위한 예비 단계에 불과하며, 거리 측정 방법의 계산이 필요합니다. 다음 섹션에서는 이러한 거리 측정 방법에 대해 포괄적으로 검토할 것입니다.

## 길이 거리 측정 방법

<div class="content-ad"></div>

길이 거리 측정 방법에 따르면, 거리는 텍스트의 수치적 특성을 이용하여 측정됩니다. 그 중 가장 인기 있는 측정 방법은 확실히 유클리드 거리입니다.

유클리드 거리

유클리드 거리는 두 점 사이의 거리를 계산하기 위해 피타고라스의 정리를 사용합니다.

길이가 n인 두 벡터를 고려해 보면, 유클리드 거리는 다음 공식으로 설명됩니다:

<div class="content-ad"></div>

아래 이미지에서 확인할 수 있습니다:

![이미지1](/assets/img/2024-05-20-MasteringTextSimilaritycombiningembeddingtechniquesanddistancemetrics_6.png)

두 벡터 간의 거리 d가 클수록 유사도 점수가 낮아지고 그 반대도 마찬가지입니다.

<div class="content-ad"></div>

유클리드 거리에는 몇 가지 제한이 있습니다. 먼저, 비교할 대상이 없다면 이해하기 어려운 값이 계산됩니다. 이 문제를 해결하기 위해 거리를 정규화할 수 있지만, 가장 잘 알려진 공식

![image](/assets/img/2024-05-20-MasteringTextSimilaritycombiningembeddingtechniquesanddistancemetrics_8.png)

은 이상치의 영향을 매우 민감하게 받습니다.

둘째, 유클리드 거리는 텍스트의 크기에 강하게 영향을 받기 때문에 희소 벡터(예: 원핫 인코딩으로 생성된 벡터)와는 잘 작동하지 않습니다.

<div class="content-ad"></div>

코사인 유사도

코사인 유사도는 두 벡터의 유사성을 측정하는 것으로, 벡터 사이의 각도의 코사인을 측정합니다. 두 점 사이의 거리를 측정하는 대신 두 벡터가 같은 방향으로 향하는지 확인합니다. 따라서 이는 벡터의 크기에 영향을 받지 않습니다.

코사인 유사도는 다음과 같은 공식을 통해 계산됩니다:
```js
\[ \text{cosine similarity} = \frac{{\textbf{A} \cdot \textbf{B}}}{{\lVert \textbf{A} \rVert \times \lVert \textbf{B} \rVert}} \]
```
<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-MasteringTextSimilaritycombiningembeddingtechniquesanddistancemetrics_10.png" />

벡터의 영향을 받아 이러한 지표를 비교하는 것에 대한 의미를 더 잘 이해해 봅시다.

두 개의 논문을 비교해보자고 가정해보겠습니다. 한 쪽은 정치와 관련된 것이고, 다른 한 쪽은 스포츠와 관련된 것이라고 가정해보겠습니다. "야구"라는 단어가 논문 1에 논문 2보다 더 많이 나오는 경우, 유클리드 거리에 따르면, 논문 1이 스포츠와 관련이 더 있습니다. 그러나 논문 1이 그냥 논문 2보다 더 길었을 수도 있습니다. 결과적으로 논문 2가 논문 1보다 스포츠와 더 관련된 경우도 있을 수 있습니다.

대부분의 텍스트 유사성 사용 사례들은 길이에 민감하지 않습니다. 따라서 일반적으로 코사인 유사도가 유클리드 거리보다 선호됩니다. 유클리드 거리가 선호될 수 있는 사용 사례는 길이에 민감한 표절 탐지입니다.

<div class="content-ad"></div>

## 의미적 거리

텍스트 간에 공통된 단어가 많지 않은 상황에서는, 길이나 분포에 의존하는 거리 측정으로 유도된 유사성은 비교적 낮게 나타날 수 있습니다. 이러한 경우 의미적 거리 계산을 선택하는 것이 좋습니다.

이를 위한 주요 방법은 Word Mover's Distance로, 이는 텍스트 간의 의미적 근접성을 결정하는 데 도움을 줍니다.

Word mover's distance

<div class="content-ad"></div>

단어 이동 거리는 두 텍스트 문서 간에 유사성을 정의하는데, 한 문서의 임베드된 단어가 다른 문서의 임베드된 단어에 도달하기 위해 이동해야 하는 최소 거리를 나타냅니다. 따라서 유사성의 측정은 운송 문제가 됩니다: 텍스트1을 텍스트2로 운송하는 비용을 최소화합니다.

단어 이동 거리는 확률 분포 간 유사성을 측정하는 최적화 문제인 Earth Mover's Distance에서 비롯되었습니다. 이는 한 분포를 다른 분포로 변환하는 비용을 고려하여 유사성을 측정합니다.

WMD를 계산하기 위한 첫 번째 단계는 정규화된 Bag of Words로의 단어 임베딩입니다. 둘째, 유클리드 거리가 계산되고 마지막으로 최적화 문제가 계산됩니다. 최적화 문제의 목적 함수는 한 문서에서 다른 문서로 이동하는 데 필요한 거리를 최소화하는 것이며, "질량"의 총량이 보존되어야 한다는 제약조건이 따릅니다. 다시 말해, 제약 조건은 두 문서의 전체 내용이 고려되고 한 단어에서 다른 단어로 이동하는 과정에서 어떤 단어도 중복되거나 손실되지 않도록 보장합니다.

긴 문서에 대해 특히 계산 비용이 많이 드는 WMD는 모든 단어의 존재 여부를 사용하는 방법으로, 순서에 상관없이 문법적인 변경사항에 강하지 않습니다. 그러나 단어의 유사성을 임베딩 공간에서 고려하기 때문에 공통 단어가 거의 없는 문서에 대해서는 매우 효과적입니다.

<div class="content-ad"></div>

# 결론

![이미지](/assets/img/2024-05-20-MasteringTextSimilaritycombiningembeddingtechniquesanddistancemetrics_11.png)

의미론적 유사성을 측정하는 것은 자연어 처리 세계에서 가장 복잡한 도전 중 하나입니다. 텍스트 유사성 측정의 공간을 탐험하면 각각 독특한 장단점이 있는 다양한 방법을 발견할 수 있습니다.

문자열 기반 방법은 간단하고 구현하기 쉽지만 의미 관계를 다루지 않습니다. 그에 반해 말뭉치 기반 방법은 더 복잡하지만 의미적이고 통계적인 관계를 중요시하며 여러 언어에 대해 다재다능합니다.

<div class="content-ad"></div>

거리 측정에 관한 것은 일부가 길이에 민감하고, 다른 일부는 분포에 의존하거나 의미에 중점을 둡니다. 이러한 측정 방법과 텍스트 표현의 조합은 무한합니다.

계속 발전하는 이 환경에서 완벽한 모델을 찾는 노력이 계속되는 중에도, 하나의 진리는 명확히 남아 있습니다: 최적의 방법을 선택하는 것은 각 사용 사례의 고유한 요구 사항과 깊은 관련이 있습니다. 우리가 앞으로 나아가면서, 표현학습과 거리 계산 사이의 시너지는 계속 발전하는 텍스트 벡터의 길을 열어주며, 의미 유사성에 대한 우리의 이해력에 새 시대를 열어줍니다.

# 참고문헌

[1] Qiu, Xipeng, 등. "자연어 처리를 위한 사전 훈련된 모델: 설문." Science China Technological Sciences 63.10 (2020): 1872-1897.

<div class="content-ad"></div>

- Goldberg, Yoav, and Omer Levy. "word2vec Explained: deriving Mikolov et al.'s negative-sampling word-embedding method." arXiv preprint arXiv:1402.3722 (2014).
- Devlin, Jacob, et al. "Bert: Pre-training of deep bidirectional transformers for language understanding." arXiv preprint arXiv:1810.04805 (2018).
- Wang, Jiapeng, and Yihong Dong. "Measurement of text similarity: a survey." Information 11.9 (2020): 421.
- Kusner, Matt, et al. "From word embeddings to document distances." International conference on machine learning. PMLR, 2015.

<div class="content-ad"></div>

[6] [Online]. Available: [Newscatcher API - Ultimate Guide to Text Similarity with Python](https://www.newscatcherapi.com/blog/ultimate-guide-to-text-similarity-with-python).

[7] McCormick, Chris. “Word2vec tutorial-the skip-gram model.” Apr-2016. [Online]. Available: [Word2vec Tutorial - The Skip-Gram Model](http://mccormickml.com/2016/04/19/word2vec-tutorial-the-skip-gram-model) (2016).