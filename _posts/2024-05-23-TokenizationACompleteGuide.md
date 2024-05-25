---
title: "토큰화 - 완벽한 가이드"
description: ""
coverImage: "/assets/img/2024-05-23-TokenizationACompleteGuide_0.png"
date: 2024-05-23 18:17
ogImage: 
  url: /assets/img/2024-05-23-TokenizationACompleteGuide_0.png
tag: Tech
originalTitle: "Tokenization — A Complete Guide"
link: "https://medium.com/@bradneysmith/tokenization-llms-from-scratch-1-cedc9f72de4e"
---


## 바이트 페어 인코딩, 워드피스 등과 같은 것들과 함께 Python 코드!

“LLMs from Scratch” 시리즈 중 제1부 — 대형 언어 모델을 이해하고 구축하는 완벽한 안내서입니다. 이 모델이 어떻게 작동하는지 더 자세히 알아보고 싶다면 다음을 읽어보는 것을 권장합니다:

- 프롤로그: 대형 언어 모델의 간단한 역사
- 파트 1: 토크나이제이션 — 완벽한 안내서
- 파트 2: Python에서 워드투벡으로부터 스크래치로 단어 임베딩
- 파트 3: 코드로 설명하는 셀프 어텐션
- 파트 4: 코드로 이해하는 BERT의 완벽한 안내서

![이미지](/assets/img/2024-05-23-TokenizationACompleteGuide_0.png)

<div class="content-ad"></div>

만약 이 콘텐츠가 도움이 되었다면, 아래 방법으로 저를 지원해주십시오:

- 기사에 Clap(박수)을 보내세요
- 저를 Medium이나 LinkedIn에서 팔로우하여 향후 게시물에 대한 업데이트를 받으세요

## 서두

대형 언어 모델 (LLM)은 2022년 11월 OpenAI의 ChatGPT가 출시된 이후 매우 인기를 얻었습니다. 그 이후로 이러한 언어 모델의 사용이 급증했으며, HuggingFace의 Transformer 라이브러리와 PyTorch와 같은 라이브러리의 도움을 받았습니다.

<div class="content-ad"></div>

하지만 모든 이들 준비하고 있는 완제품 도구들로 인해, 기본 수준에서 무슨 일이 일어나고 있는지 추상화하는 것이 쉽습니다. 그 결과로 많은 온라인 튜토리얼들이 당신이 자체 모델을 생성할 때 '무엇'을 알려주고 '왜'는 알려주지 않는 경우가 많습니다. 이 기사 시리즈는 이를 해결하고자 합니다. '처음부터 LLMs 만들기'는 대형 언어 모델을 구성하는 구성 요소를 분해하고, 내부 작동 방식을 설명합니다. 그의 목표는 다음과 같습니다:

- 수학의 직관적 이해를 포함한, LLMs가 어떻게 작동하는지의 기본적인 이해 구축
- 각 구성 요소가 어떻게 작동하는지를 보여주며, Python에서 처음부터 구현 방법을 보여줌
- 불필요한 추상화를 줄이기 위해 가급적이면 최소한의 라이브러리 사용

말이 다 되었으니, 시작해보겠습니다.

# 토크나이저란 무엇인가?

<div class="content-ad"></div>

자연어 처리 문제는 텍스트 데이터를 사용하는데, 기계가 즉시 이해하기 어렵습니다. 컴퓨터가 언어를 처리하려면 먼저 텍스트를 숫자 형식으로 변환해야 합니다. 이 프로세스는 토크나이저라는 모델에 의해 주로 두 단계로 수행됩니다.

단계 1: 입력 텍스트를 토큰으로 분할

토크나이저는 먼저 텍스트를 가져와 단어, 단어 부분 또는 개별 문자가 될 수 있는 작은 조각으로 나눕니다. 이러한 작은 텍스트 조각을 토큰이라고 합니다. 스탠포드 NLP 그룹은 토큰을 더 엄격하게 정의합니다.

단계 2: 각 토큰에 식별자 할당

<div class="content-ad"></div>

토크나이저가 텍스트를 토큰으로 분리한 후, 각 토큰에 토큰 ID라고 불리는 정수 번호를 할당할 수 있습니다. 예를 들어, "cat"이라는 단어가 15라는 값으로 할당될 수 있고, 따라서 입력 텍스트의 모든 cat 토큰은 숫자 15로 표시됩니다. 텍스트 토큰을 숫자 표현으로 교체하는 과정을 인코딩이라고 합니다. 비슷하게, 인코딩된 토큰을 다시 텍스트로 변환하는 과정을 디코딩이라고 합니다.

단일 숫자를 사용하여 토큰을 표현하는 것에는 단점이 있다는 것을 알 수 있습니다. 그래서 이러한 코드들은 단어 임베딩을 생성하기 위해 추가로 처리되며, 이것은 이 시리즈의 다음 기사의 주제입니다. 

# 토큰화 방법

텍스트를 토큰으로 나누는 세 가지 주요 방법이 있습니다:

<div class="content-ad"></div>

- 단어 기반
- 문자 기반
- 부분어 기반

## 단어 기반 토크나이저:

단어 기반 토크나이제이션은 세 가지 토큰화 방법 중 가장 간단한 방법입니다. 여기서 토크나이저는 문장을 단어로 분할하는데 각 공백 문자를 기준으로 나눕니다(때로는 '화이트스페이스 기반 토큰화'라고도 함) 또는 유사한 규칙 세트(구두점 기반 토큰화, 트리뱅크 토큰화 등)에 따라 분할할 수도 있습니다 [12].

예를 들어, 다음과 같은 문장:

<div class="content-ad"></div>

</br>

고양이들은 멋지지만, 개들이 더 좋아요!

띄어쓰기 문자로 분할하면:

[`Cats`, `are`, `great,`, `but`, `dogs`, `are`, `better!`]

또는 구두점과 공백을 기준으로 분할하면:

<div class="content-ad"></div>

[`Cats`, `are`, `great`, `,`, `but`, `dogs`, `are`, `better`, `!`]

위 간단한 예제를 통해 분할을 결정하는 데 사용하는 규칙이 중요하다는 것을 분명히 이해할 수 있습니다. 공백 접근 방식은 잠재적으로 희귀한 토큰 `better!`를 제공하며, 두 번째 분할은 덜 희귀한 토큰 `better`와 `!`을 생성합니다. 문장부호를 완전히 제거하지 않도록 주의해야 합니다. 문장부호에는 매우 구체적인 의미가 있을 수 있기 때문입니다. 그 중 하나는 ‘작은따옴표(apostrophe)’입니다. 작은따옴표는 단수와 소유 형태를 구별할 수 있습니다. 예를 들어 “book's”는 책의 속성을 가리키며 “the book's spine is damaged”와 같이 사용되고, “books”는 여러 권의 책을 가리킵니다. 

토큰을 생성한 후, 각 토큰에 번호를 할당할 수 있습니다. 토큰 생성기가 이미 본 토큰을 생성할 때 다음에 볼 토큰은 그 단어에 지정된 번호를 간단히 할당할 수 있습니다. 예를 들어 위 문장에서 `great`가 1이라는 값으로 할당된 경우, 이후의 `great` 단어는 모두 1의 값으로 할당됩니다.

단어 기반 토크나이저의 장단점:

<div class="content-ad"></div>

워드 기반 방법으로 생성된 토큰들은 각각 의미론적 및 문맥 정보를 포함하고 있어 많은 정보를 담고 있습니다. 그러나 이 방법의 가장 큰 단점 중 하나는 매우 유사한 단어가 완전히 다른 토큰으로 처리된다는 것입니다. 예를 들어, cat과 cats 간의 연결은 존재하지 않으며, 이들은 별개의 단어로 처리됩니다. 이는 많은 단어를 포함하는 대규모 응용 프로그램에서 문제가 될 수 있습니다. 모델 어휘의 가능한 토큰 수가 매우 커질 수 있기 때문입니다. 영어는 약 17만 단어가 있으며, 각 단어에 대한 복수형이나 과거형과 같은 다양한 문법 형태를 포함하면 폭발적인 어휘 문제가 발생할 수 있습니다. TransformerXL 토크나이저가 사용하는 공백 기반 분할은 어휘 크기가 25만 개를 초과하도록 이끌었습니다.

이 문제를 해결하는 한 가지 방법은 모델이 학습할 수 있는 토큰 수에 하드 리미트를 부여하는 것입니다(예: 1만). 이는 가장 빈도가 높은 1만개의 토큰을 벗어나는 모든 단어를 어휘 외로 처리하고, 숫자 값 대신 UNKNOWN 토큰 값을 할당하는 것입니다(UNK로 축약되기도 합니다). 이는 많은 알려지지 않은 단어가 있는 경우에 성능에 영향을 줄 수 있지만, 데이터에 대부분의 일반적인 단어가 포함된 경우에는 적합한 타협안이 될 수 있습니다.

장점 요약:
- 간단한 방법
- 각 토큰에 저장된 높은 정보량
- 주로 일반적인 단어를 포함하는 데이터셋과 잘 작동하는 어휘 크기 제한 가능

<div class="content-ad"></div>

요약:

- 비슷한 단어에 대해 별도의 토큰이 생성됩니다 (예: cat과 cats)
- 매우 큰 어휘를 만들 수 있습니다.
- 어휘를 제한하면 드문 단어가 많은 데이터셋에서 성능이 크게 저하될 수 있습니다.

## 문자 기반 토크나이저:

문자 기반 토크나이제이션은 글자, 숫자 및 구두점과 같은 특수 문자를 포함하여 텍스트를 각 문자 단위로 분할합니다. 이는 영어 언어를 단어 기반 접근법에서 필요한 17만 개 이상의 어휘 대신 약 256개의 토큰으로 표현할 수 있도록 어휘 크기를 크게 줄입니다 [5]. 중국어 및 일본어와 같은 동아시아 언어도 자신들의 문자 시스템에서 수천 개의 고유 문자를 포함하지만 어휘 크기가 크게 축소될 수 있습니다.

<div class="content-ad"></div>

문자 기반 토크나이저에서는 다음과 같은 문장을 아래와 같이 변환할 수 있습니다:

[`C`, `a`, `t`, `s`, ` `, `a`, `r`, `e`, ` `, `g`, `r`, `e`, `a`, `t`, `,`, ` `, `b`, `u`, `t`, ` `, `d`, `o`, `g`, `s`, ` `, `a`, `r`, `e`, ` `, `b`, `e`, `t`, `t`, `e`, `r`, `!`]

<div class="content-ad"></div>

캐릭터 기반 토크나이저의 장단점:

단어 기반 방법과 비교할 때, 캐릭터 기반 접근 방식은 훨씬 작은 어휘 크기를 가지며, 많은 수의 OOV(Out-Of-Vocabulary) 토큰을 생성하지 않는다. 심지어 맞춰 쓰인 단어들이 아닌 오타가 있는 단어들조차도 토큰화할 수 있다는 장점이 있습니다(다만 해당 단어의 올바른 형태와는 다르게 토큰화됩니다). 또한, 빈도 기반 어휘 제한 때문에 단어가 즉시 제거되는 것을 방지합니다.

하지만 이 접근 방식에는 몇 가지 단점도 있습니다. 먼저, 캐릭터 기반 방법으로 생성된 단일 토큰에 저장된 정보량은 매우 적습니다. 이는 단어 기반 방식의 토큰과 달리 의미론적이거나 문맥적인 의미가 캡처되지 않기 때문입니다(특히, 알파벳 기반 언어인 영어와 같은 언어에서). 마지막으로 이 방식은 입력 텍스트를 인코딩하기 위해 많은 수의 숫자가 필요하기 때문에, 언어 모델에 투입할 수 있는 토큰화된 입력의 크기에 제약이 생깁니다.

장점 요약:

<div class="content-ad"></div>

- 어휘 크기가 작음
- 철자가 틀린 단어를 제거하지 않음

단점 요약:

- 각 토큰에 저장되는 정보량이 적으며, 알파벳 기반의 글쓰기 체계에서는 문맥적 또는 의미적 의미가 거의 없음
- 언어 모델에 입력되는 크기가 제한되며, 텍스트를 토큰화하는 데 필요한 숫자가 훨씬 더 많아짐 (단어 기반 접근 방식과 비교했을 때)

## Subword-Based Tokenizers:

<div class="content-ad"></div>

서브워드 기반 토큰화는 단어 기반 및 문자 기반 방법의 이점을 모두 활용하면서 그들의 단점을 최소화하려는 목표를 가지고 있어요. 서브워드 기반 방법은 단어 내에서 텍스트를 분할하여 의미 있는 토큰을 생성하려는 시도를 통해 중간 지점을 취하고 있어요, 심지어 그것들이 완전한 단어가 아니더라도요. 예를 들어, 토큰 ing와 ed는 문법적인 의미를 가지고 있지만 그 자체로 완전한 단어는 아니에요.

이 방법은 단어 기반 방법보다 작은 어휘 크기를 갖게 하지만, 문자 기반 방법보다 큰 어휘 크기를 갖게 해요. 또한 매 토큰 내에 저장된 정보 양도 두 가지 이전 방법으로 생성된 토큰 사이에 위치하게 되요. 서브워드 접근 방식은 다음 두 지침을 사용해요:

- 자주 사용되는 단어를 서브워드로 분리하지 말고 전체 토큰으로 저장해야 함
- 드물게 사용되는 단어를 서브워드로 분리해야 함

드물게 사용되는 단어만 분리함으로써 활용어나 복수형 등이 그 구성 요소로 분해되는 기회를 주면서 토큰 사이의 관계를 보존하게 돼요. 예를 들어 cat은 데이터셋에서 매우 흔한 단어지만 cats는 덜 흔할 수 있어요. 이 경우 cats는 cat과 s로 분리되어, cat은 이제 다른 모든 cat 토큰과 동일한 값을 갖게 되고, s는 다른 값을 갖게 됩니다. 이는 복수성의 의미를 인코딩할 수 있다는 것이에요. 또 다른 예시로는 단어 토큰화인데요, 이는 루트 단어 토큰과 접미사 ization으로 분할될 수 있어요. 이 방법은 구문 및 의미 유사성을 보존할 수 있습니다. 이러한 이유로, 서브워드 기반 토큰화기는 현재 많은 NLP 모델에서 널리 사용됩니다.

<div class="content-ad"></div>

# 정규화 및 사전 토크나이제이션

토크나이제이션 과정에서는 사전 처리 및 사후 처리 단계가 필요한데, 이 모든 것이 토크나이제이션 파이프라인을 이룹니다. 이것은 로우 텍스트를 토큰으로 변환하는 데 필요한 일련의 조치들을 설명합니다. 이 파이프라인의 단계는 다음과 같습니다:

- 정규화
- 사전 토큰화
- 모델
- 후 처리

여기서 토큰화 방법(서브워드 기반, 문자 기반 등)은 모델 단계에서 이루어집니다 [7]. 이 섹션에서는 서브워드 기반 토큰화 방식을 사용하는 토크나이저에 대해 각 단계를 다룰 것입니다.

<div class="content-ad"></div>

중요한 알림: 토큰화 파이프라인의 모든 단계는 Hugging Face의 토크나이저 및 트랜스포머 라이브러리와 같은 라이브러리에서 토크나이저를 사용할 때 자동으로 사용자 대신 처리됩니다. 전체 파이프라인은 Tokenizer라는 단일 객체에 의해 수행됩니다. 이 섹션은 대부분의 사용자가 NLP 작업을 수행할 때 직접 처리할 필요가 없는 코드 내부 작업에 대해 다룹니다. 나중에는 토크나이저 라이브러리의 기본 토크나이저 클래스를 사용자 정의하는 단계도 제시되어 필요한 경우 특정 작업용으로 토크나이저를 목적에 맞게 만들 수 있습니다.

## 정규화 방법

정규화는 텍스트를 토큰으로 분할하기 전에 정리하는 과정입니다. 이 과정에는 각 문자를 소문자로 변환하거나 문자에서 강세 기호를 제거하는 단계(예: é가 e가 됨), 불필요한 공백을 제거하는 것 등이 포함됩니다. 예를 들어, 문자열 ThÍs is áN ExaMPlé sÉnteNCE는 정규화 후에는 this is an example sentence가 됩니다. 서로 다른 정규화기는 서로 다른 단계를 수행하며, 사용 사례에 따라 유용할 수 있습니다. 예를 들어, 일부 상황에서는 대소문자나 강세 기호를 유지해야 할 수도 있습니다. 선택한 정규화기에 따라이 단계에서 다양한 효과를 얻을 수 있습니다.

Hugging Face의 tokenizers.normalizers 패키지에는 대규모 모델의 일부로서 다양한 토큰화기에서 사용되는 여러 기본 정규화기가 포함되어 있습니다. 아래는 NFC 유니코드, 소문자 및 BERT 정규화기입니다. 이들은 예제 문장에 다음과 같은 효과를 보여줍니다:

<div class="content-ad"></div>

- NFC: 대문자를 변환하지 않거나 악센트를 제거하지 않습니다.
- Lower: 대문자를 변환하지만 악센트를 제거하지 않습니다.
- BERT: 대문자를 변환하고 악센트를 제거합니다.

```js
from tokenizers.normalizers import NFC, Lowercase, BertNormalizer

# 정규화할 텍스트
text = 'ThÍs is  áN ExaMPlé     sÉnteNCE'

# 정규화 객체 인스턴스화
NFCNorm = NFC()
LowercaseNorm = Lowercase()
BertNorm = BertNormalizer()

# 텍스트 정규화
print(f'NFC:   {NFCNorm.normalize_str(text)}')
print(f'Lower: {LowercaseNorm.normalize_str(text)}')
print(f'BERT:  {BertNorm.normalize_str(text)}')
```

```js
NFC:   ThÍs is  áN ExaMPlé     sÉnteNCE
Lower: thís is  án examplé     séntence
BERT:  this is  an example     sentence
```

위의 정규화기들은 Hugging Face transformers 라이브러리에서 가져올 수 있는 토크나이저 모델에서 사용됩니다. 아래 코드 셀은 Tokenizer.backend_tokenizer.normalizer를 통해 점 표기법(dot notation)을 사용하여 정규화기에 액세스하는 방법을 보여줍니다. 서로 다른 정규화 방법을 강조하기 위해 일부 비교를 보여줍니다. 이 예시들에서는 FNet 정규화기만 불필요한 공백을 제거합니다.

<div class="content-ad"></div>

```js
from transformers import FNetTokenizerFast, CamembertTokenizerFast, \
                         BertTokenizerFast

# Text to normalize
text = 'ThÍs is  áN ExaMPlé     sÉnteNCE'

# Instantiate tokenizers
FNetTokenizer = FNetTokenizerFast.from_pretrained('google/fnet-base')
CamembertTokenizer = CamembertTokenizerFast.from_pretrained('camembert-base')
BertTokenizer = BertTokenizerFast.from_pretrained('bert-base-uncased')

# Normalize the text
print(f'FNet Output:      \
    {FNetTokenizer.backend_tokenizer.normalizer.normalize_str(text)}')

print(f'CamemBERT Output: \
    {CamembertTokenizer.backend_tokenizer.normalizer.normalize_str(text)}')

print(f'BERT Output:      \
    {BertTokenizer.backend_tokenizer.normalizer.normalize_str(text)}')
```

```js
FNet Output:      ThÍs is áN ExaMPlé sÉnteNCE
CamemBERT Output: ThÍs is  áN ExaMPlé     sÉnteNCE
BERT Output:      this is  an example     sentence
```

## Pre-Tokenization Methods

The pre-tokenization step is the first splitting of the raw text in the tokenization pipeline. The split is performed to give an upper bound to what the final tokens could be at the end of the pipeline. That is, a sentence can be split into words in the pre-tokenization step, then in the model step some of these words may be split further according to the tokenization method (e.g. subword-based). So the pre-tokenized text represents the largest possible tokens that could still remain after tokenization.
```

<div class="content-ad"></div>

정규화와 마찬가지로이 단계를 수행하는 여러 가지 방법이 있습니다. 예를 들어, 문장은 매 공백, 모든 공백 및 일부 구두점 또는 매 공백 및 모든 구두점을 기준으로 분할될 수 있습니다.

아래 셀은 기본 Whitespacesplit 프리 토크나이저와 Hugging Face 토크나이저의 pre_tokenizers 패키지에서 약간 더 복잡한 BertPreTokenizer 간의 비교를 보여줍니다. 공백 프리 토크나이저의 출력은 구두점을 그대로 두고 이웃하는 단어에 여전히 붙어 있는 것을 보여줍니다. 예를 들어, "includes:"는 이 경우에는 단일 단어로 처리됩니다. 반면 BERT 프리 토크나이저는 구두점을 개별 단어로 취급합니다 [8].

```js
from tokenizers.pre_tokenizers import WhitespaceSplit, BertPreTokenizer

# 텍스트 정규화
text = ("this sentence's content includes: characters, spaces, and " \
        "punctuation.")

# 프리 토큰화된 출력을 표시하는 도우미 함수 정의
def print_pretokenized_str(pre_tokens):
    for pre_token in pre_tokens:
        print(f'"{pre_token[0]}", ', end='')

# 프리 토크나이저 인스턴스화
wss = WhitespaceSplit()
bpt = BertPreTokenizer()

# 텍스트를 프리 토큰화
print('Whitespace Pre-Tokenizer:')
print_pretokenized_str(wss.pre_tokenize_str(text))

print('\n\nBERT Pre-Tokenizer:')
print_pretokenized_str(bpt.pre_tokenize_str(text))
```

```js
Whitespace Pre-Tokenizer:
"this", "sentence's", "content", "includes:", "characters,", "spaces,", 
"and", "punctuation.", 

BERT Pre-Tokenizer:
"this", "sentence", "'", "s", "content", "includes", ":", "characters", 
",", "spaces", ",", "and", "punctuation", ".",
```

<div class="content-ad"></div>

정규화 방법들과 마찬가지로 GPT-2와 ALBERT (A Lite BERT) 토크나이저와 같은 일반적인 토크나이저에서 사전 토큰화 방법을 직접 호출할 수 있습니다. 이들은 위에서 보여진 표준 BERT 사전 토큰화 방법과 약간 다른 방식을 사용합니다. 토큰을 분할할 때 공백 문자를 제거하지 않고 특수 문자로 대체합니다. 그 결과, 공백 문자를 처리할 때 무시할 수 있지만 필요할 경우 원래 문장을 검색할 수 있습니다. GPT-2 모델은 Ġ 문자를 사용하며, 이는 위에 점을 찍은 대문자 G가 특징입니다. ALBERT 모델은 밑줄 문자를 사용합니다.

```python
from transformers import AutoTokenizer

# 사전 토큰화할 텍스트
text = ("this sentence's content includes: characters, spaces, and " \
        "punctuation.")

# 사전 토큰화 객체 생성
GPT2_PreTokenizer = AutoTokenizer.from_pretrained('gpt2').backend_tokenizer \
                    .pre_tokenizer

Albert_PreTokenizer = AutoTokenizer.from_pretrained('albert-base-v1') \
                      .backend_tokenizer.pre_tokenizer

# 텍스트를 사전 토큰화
print('GPT-2 사전 토크나이저:')
print_pretokenized_str(GPT2_PreTokenizer.pre_tokenize_str(text))
print('\n\nALBERT 사전 토크나이저:')
print_pretokenized_str(Albert_PreTokenizer.pre_tokenize_str(text))
```

```python
GPT-2 사전 토크나이저:
"this", "Ġsentence", "'s", "Ġcontent", "Ġincludes", ":", "Ġcharacters", ",",
"Ġspaces", ",", "Ġand", "Ġpunctuation", "." 

ALBERT 사전 토크나이저:
"▁this", "▁sentence's", "▁content", "▁includes:", "▁characters,", "▁spaces,",
"▁and", "▁punctuation."
```

위의 예제 문장에 대한 BERT 사전 토큰화 단계 결과를 수정 없이 출력한 내용이 아래에 나와 있습니다. 반환된 객체는 원본 입력 텍스트에서 문자열의 시작 및 끝 색인을 포함하는 파이썬 리스트입니다. 문자열의 시작 색인은 포함되며, 끝 색인은 배타적입니다.

<div class="content-ad"></div>

```js
from tokenizers.pre_tokenizers import WhitespaceSplit, BertPreTokenizer

# Pre-tokenizer 인스턴스 생성
text = ("this sentence의 내용은: characters, spaces, 그리고 " \
        "punctuation이 포함되어 있습니다.")
bpt = BertPreTokenizer()
bpt.pre_tokenize_str(text)
```

```js
[('this', (0, 4)),
 ('sentence', (5, 13)),
 ("'", (13, 14)),
 ('s', (14, 15)),
 ('content', (16, 23)),
 ('includes', (24, 32)),
 (':', (32, 33)),
 ('characters', (34, 44)),
 (',', (44, 45)),
 ('spaces', (46, 52)),
 (',', (52, 53)),
 ('and', (54, 57)),
 ('punctuation', (58, 69)),
 ('.', (69, 70))]
```

# 서브워드 토큰화 방법

토큰화 파이프라인의 모델 단계는 토큰화 방법이 사용되는 곳입니다. 이전에 설명한대로 여기서 선택할 수 있는 옵션은: 단어 기반, 문자 기반, 서브워드 기반입니다. 서브워드 기반 방법이 일반적으로 선호되는데, 이 방법들은 단어 기반 및 문자 기반 접근법의 한계를 극복하기 위해 설계되었습니다.
```

<div class="content-ad"></div>

트랜스포머 모델에는 하위 단어 기반 토큰화를 구현하는 데 일반적으로 사용되는 세 가지 토크나이저 방법이 있습니다. 이 방법들은 다음과 같습니다:

- 바이트 페어 인코딩 (BPE)
- 워드피스
- 유니그램

각각의 방법은 빈도가 낮은 단어를 더 작은 토큰으로 분리하기 위해 약간 다른 기술을 사용합니다. BPE 및 워드피스 알고리즘의 구현 방법도 여기에 소개되어 있어서 접근 방식 사이의 유사점과 차이점을 강조하는 데 도움이 될 것입니다.

## 바이트 페어 인코딩

<div class="content-ad"></div>

바이트 페어 인코딩 알고리즘은 GPT 및 GPT-2 모델 (OpenAI), BART (Lewis et al.) 및 기타 많은 트랜스포머 모델에서 발견되는 일반적으로 사용되는 토크나이저입니다 [9-10]. 이 알고리즘은 원래 텍스트 압축 알고리즘으로 설계되었지만, 언어 모델의 토큰화 작업에 매우 효과적으로 작동한다는 것이 밝혀졌습니다. BPE 알고리즘은 텍스트 문자열을 참조 말뭉치(토큰화 모델을 훈련하는 데 사용되는 텍스트)에서 빈번히 나타나는 부분 단어 단위로 분해합니다 [11]. BPE 모델은 다음과 같이 훈련됩니다:

## 단계 1) 말뭉치 작성

입력 텍스트는 정규화 및 사전 토큰화 모델에 제공되어 깨끗한 단어를 생성합니다. 단어는 BPE 모델에 제공되어 각 단어의 빈도를 결정하고, 이 빈도를 단어와 함께 목록인 말뭉치에 저장합니다.

## 단계 2) 어휘 작성

<div class="content-ad"></div>

말뭉치에서 단어들은 개별 문자로 분해되어 "어휘(vocabulary)"라는 비어있는 목록에 추가됩니다. 알고리즘은 어떤 문자 쌍을 함께 병합할 수 있는지를 결정할 때마다 이 어휘에 계속 추가합니다.

단계 3) 문자 쌍의 빈도 찾기

그런 다음, 말뭉치의 각 단어에 대해 문자 쌍의 빈도가 기록됩니다. 예를 들어, 단어 "cats"는 문자 쌍 "ca", "at", "ts"를 가집니다. 이와 같은 방식으로 모든 단어가 검사되어 전역 빈도 카운터에 기여합니다. 따라서 토큰 중에서 어떤 ca가 발견되는 경우, ca 쌍에 대한 빈도 카운터가 증가합니다.

단계 4) 병합 규칙 작성

<div class="content-ad"></div>

각 문자 쌍의 빈도가 알려진 경우, 가장 빈번한 문자 쌍이 어휘에 추가됩니다. 어휘는 이제 토큰 내의 모든 개별 문자와 가장 빈번한 문자 쌍으로 구성됩니다. 또한 모델이 사용할 수있는 병합 규칙이 제공됩니다. 예를 들어 모델이 ca가 가장 빈번한 문자 쌍이라는 것을 학습하면, 모델은 말뭉친 c와 a의 모든 인접 인스턴스를 ca로 병합해 ca를 제공합니다. 이제 이를 나머지 단계의 단일 문자 ca로 취급할 수 있습니다.

단계 5) 단계 3과 4 반복

그런 다음 단계 3과 4를 반복하여 더 많은 병합 규칙을 찾고 어휘에 더 많은 문자 쌍을 추가합니다. 이 프로세스는 교육 시작 시 지정된 대상 크기에 도달 할 때까지 계속됩니다.

BPE 알고리즘이 교육되었으므로 (즉, 모든 병합 규칙이 찾아졌다), 모델은 모든 텍스트를 토큰화하기 위해 모든 단어를 각 문자로 분할하고, 그런 다음 병합 규칙에 따라 병합하여 사용할 수 있습니다.

<div class="content-ad"></div>

위의 표는 마크다운 형식으로 변경하겠습니다. 

아래는 BPE 알고리즘의 Python 구현입니다. 위에서 설명한 단계를 따르고 있습니다. 그 후에는 이 모델을 장난감 데이터세트에서 훈련하고 몇 가지 예제 단어에서 테스트합니다.

```py
class TargetVocabularySizeError(Exception):
    def __init__(self, message):
        super().__init__(message)

class BPE:
    '''Byte Pair Encoding tokenizer의 구현.'''

    def calculate_frequency(self, words):
        ''' 주어진 단어 목록에서 각 단어의 빈도를 계산합니다.

            문자열로 저장된 단어 목록을 받아서, 각 단어의 빈도를 나타내는 정수를 값을 가진 튜플의 목록을 반환합니다.

            매개변수:
                words (list): 어떠한 순서로든 단어들(문자열)의 목록입니다.

            반환값:
                corpus (list[tuple(str, int)]): 단어 목록의 각 단어를 나타내는 첫 번째 요소가 문자열이고,
                  두 번째 요소가 목록에서 단어의 빈도를 나타내는 정수인 튜플의 목록입니다.
        '''
        freq_dict = dict()

        for word in words:
            if word not in freq_dict:
                freq_dict[word] = 1
            else:
                freq_dict[word] += 1

        corpus = [(word, freq_dict[word]) for word in freq_dict.keys()]

        return corpus

    # 나머지 코드 생략
```

BPE 알고리즘은 '고양이'에 관한 몇 가지 단어가 포함된 장난감 데이터세트에서 훈련됩니다. 토크나이저의 목표는 데이터세트의 단어의 가장 유용하고 의미 있는 하위 단위를 결정하여 토큰으로 사용하는 것입니다. 검사 결과, 'cat', 'eat', 'ing' 등의 단위가 유용한 하위 단위가 될 것임이 분명합니다.

21개의 대상 어휘 크기로 토크나이저를 실행하면(이는 5회 병합만 필요합니다), 위에서 언급한 모든 원하는 하위 단위를 포착하는 데 충분합니다. 더 큰 데이터세트의 경우 대상 어휘도 훨씬 더 높아지겠지만, 이는 BPE 토크나이저가 얼마나 강력한지를 보여줍니다.```

<div class="content-ad"></div>

```js
# Training set
words = ['cat', 'cat', 'cat', 'cat', 'cat',
         'cats', 'cats',
         'eat', 'eat', 'eat', 'eat', 'eat', 'eat', 'eat', 'eat', 'eat', 'eat',
         'eating', 'eating', 'eating',
         'running', 'running',
         'jumping',
         'food', 'food', 'food', 'food', 'food', 'food']

# Instantiate the tokenizer
bpe = BPE()
bpe.train(words, 21)

# Print the corpus at each stage of the process, and the merge rule used
print(f'INITIAL CORPUS:\n{bpe.corpus_history[0]}\n')
for rule, corpus in list(zip(bpe.merge_rules, bpe.corpus_history[1:])):
    print(f'NEW MERGE RULE: Combine "{rule[0]}" and "{rule[1]}"')
    print(corpus, end='\n\n')
```

```js
INITIAL CORPUS:
[(['c', 'a', 't'], 5), (['c', 'a', 't', 's'], 2), (['e', 'a', 't'], 10),
(['e', 'a', 't', 'i', 'n', 'g'], 3), (['r', 'u', 'n', 'n', 'i', 'n', 'g'], 2), 
(['j', 'u', 'm', 'p', 'i', 'n', 'g'], 1), (['f', 'o', 'o', 'd'], 6)]

NEW MERGE RULE: Combine "a" and "t"
[(['c', 'at'], 5), (['c', 'at', 's'], 2), (['e', 'at'], 10), 
(['e', 'at', 'i', 'n', 'g'], 3), (['r', 'u', 'n', 'n', 'i', 'n', 'g'], 2), 
(['j', 'u', 'm', 'p', 'i', 'n', 'g'], 1), (['f', 'o', 'o', 'd'], 6)]

NEW MERGE RULE: Combine "e" and "at"
[(['c', 'at'], 5), (['c', 'at', 's'], 2), (['eat'], 10), 
(['eat', 'i', 'n', 'g'], 3), (['r', 'u', 'n', 'n', 'i', 'n', 'g'], 2), 
(['j', 'u', 'm', 'p', 'i', 'n', 'g'], 1), (['f', 'o', 'o', 'd'], 6)]

NEW MERGE RULE: Combine "c" and "at"
[(['cat'], 5), (['cat', 's'], 2), (['eat'], 10), (['eat', 'i', 'n', 'g'], 3), 
(['r', 'u', 'n', 'n', 'i', 'n', 'g'], 2), 
(['j', 'u', 'm', 'p', 'i', 'n', 'g'], 1), (['f', 'o', 'o', 'd'], 6)]

NEW MERGE RULE: Combine "i" and "n"
[(['cat'], 5), (['cat', 's'], 2), (['eat'], 10), (['eat', 'in', 'g'], 3), 
(['r', 'u', 'n', 'n', 'in', 'g'], 2), (['j', 'u', 'm', 'p', 'in', 'g'], 1), 
(['f', 'o', 'o', 'd'], 6)]

NEW MERGE RULE: Combine "in" and "g"
[(['cat'], 5), (['cat', 's'], 2), (['eat'], 10), (['eat', 'ing'], 3), 
(['r', 'u', 'n', 'n', 'ing'], 2), (['j', 'u', 'm', 'p', 'ing'], 1), 
(['f', 'o', 'o', 'd'], 6)]
```

크게 작은 데이터셋으로 BPE 알고리즘을 학습했으므로 이제 예제 단어를 토큰화하는 데 사용할 수 있습니다. 아래 셀은 토크나이저가 이전에 본 단어들 및 이전에 보지 못한 단어들을 토큰화하는 데 사용되는 것을 보여줍니다. 토크나이저는 동사 접미사 "ing"을 학습했으므로 이를 토큰으로 분리할 수 있습니다. 이 때문에 훈련 데이터에는 'eat'이 포함되어 있어 'eat'이 중요한 토큰임을 학습했습니다. 그러나 모델은 'run'과 'ski'라는 단어를 본 적이 없기 때문에 이를 성공적으로 토큰화하지 못합니다. 이는 토크나이저를 훈련시킬 때 다양하고 광범위한 훈련 세트의 중요성을 강조합니다.

```js
print(bpe.tokenize('eating'))
print(bpe.tokenize('running'))
print(bpe.tokenize('skiing'))
```

<div class="content-ad"></div>

```js
['먹', '어', '•', 'ᆼ']
['', 'ᄂ', 'ᄂ', '•', 'ᆼ']
['', '스', '키', '•', 'ᆼ']
```

BPE 토크나이저는 훈련 데이터에 나타난 문자만 인식할 수 있습니다. 예를 들어, 위의 훈련 데이터에는 고양이에 대해 이야기할 때 필요한 문자만 포함되어 있어서 z가 필요하지 않았습니다. 따라서 해당 토크나이저 버전은 z 문자를 어휘에 포함시키지 않으며, 실제 데이터를 토큰화할 때 해당 문자를 알 수 없는 토큰으로 변환합니다 (실제로, 오류 처리가 없어 모델이 알 수 없는 토큰을 생성하도록 지시하는 기능도 없으므로 모델이 충돌할 것이지만, 제품화된 모델에서는 이런 일이 발생할 수 있습니다).

GPT-2 및 RoBERTa에서 사용되는 BPE 토크나이저는 이 문제가 없으며 코드 내에 한 가지 속임수가 있습니다. Unicode 문자를 기반으로 훈련 데이터를 분석하는 대신, 문자의 바이트를 분석합니다. 이를 Byte-Level BPE라고 하며, 소규모 기본 어휘를 사용하여 모델이 볼 수 있는 모든 문자를 토큰화할 수 있게 합니다.

## WordPiece
```

<div class="content-ad"></div>

WordPiece는 구글이 개발한 토큰화 방법으로, 그들의 중요한 BERT 모델 및 이로부터 파생된 모델들인 DistilBERT 및 MobileBERT에서 사용됩니다.

WordPiece 알고리즘의 전체 세부 내용은 공개되지 않았기 때문에 여기서 제시하는 방법론은 Hugging Face에 의해 제시된 해석을 기반으로 합니다. WordPiece 알고리즘은 BPE와 유사하지만 병합 규칙을 결정하는 데 다른 지표를 사용합니다. 가장 빈도가 높은 문자 쌍을 선택하는 대신 각 쌍에 대해 점수가 계산되고, 가장 높은 점수를 가진 쌍이 병합될 문자를 결정합니다. WordPiece는 다음과 같이 훈련됩니다.

단계 1) 말뭉치 구축

다시 한 번 입력 텍스트는 정규화 및 사전 토큰화 모델에 제공되어 깨끗한 단어를 생성합니다. 단어는 WordPiece 모델에 제공되어 각 단어의 빈도를 결정하고, 이 번호를 단어와 함께 "말뭉치"라고 불리는 리스트에 저장합니다.

<div class="content-ad"></div>

단계 2) 어휘 구성

BPE와 같이 코퍼스에서 단어를 개별 문자로 분해한 후, 단어들은 비어 있는 어휘 목록에 추가됩니다. 그러나 이번에는 단순히 각 개별 문자를 저장하는 대신, 두 개의 # 기호가 사용되어 문자가 단어의 시작에서 발견되었는지 또는 단어의 중간/끝에서 발견되었는지를 표시하는 마커로 사용됩니다. 예를 들어, 단어 cat은 BPE에서 [`c`, `a`, `t`]로 분할되지만 WordPiece에서는 [`c`, `##a`, `##t`]로 나타납니다. 이 시스템에서는 단어의 시작에서의 c와 단어의 중간 또는 끝에서의 ##c가 다르게 처리됩니다. 알고리즘은 매번 어떤 문자 쌍을 함께 병합할 수 있는지 결정할 때마다 이 어휘에 추가됩니다.

단계 3) 인접 문자 쌍의 쌍 점수 계산

BPE 모델과 달리, 이번에는 각 문자 쌍에 대해 점수가 계산됩니다. 먼저, 코퍼스에서 각 인접 문자 쌍을 식별하고, `c##a`, ##a##t 등이 계산됩니다. 그리고 빈도가 계산됩니다. 각 문자의 빈도도 결정됩니다. 이러한 값들을 알면, 다음 공식에 따라 쌍 점수를 계산할 수 있습니다:

<div class="content-ad"></div>

```markdown
![Tokenization Guide](/assets/img/2024-05-23-TokenizationACompleteGuide_1.png)

이 메트릭은 함께 자주 나타나지만 개별적으로나 다른 문자와 자주 나타나지 않는 문자에 더 높은 점수를 할당합니다. 이것이 WordPiece와 BPE 사이의 주된 차이점인데, BPE는 개별 문자의 전체 빈도를 고려하지 않습니다.

단계 4) 병합 규칙 생성

높은 점수는 자주 함께 나타나는 문자 쌍을 나타냅니다. 즉, c##a가 높은 쌍 점수를 가지면 c와 a가 말뭉치에서 함께 자주 나타나고 개별적으로는 그리 자주 나타나지 않는 것입니다. BPE와 마찬가지로, 병합 규칙은 가장 높은 점수를 가진 문자 쌍에 의해 결정됩니다. 이번에는 빈도가 점수를 결정하는 대신 쌍 점수로 결정됩니다.
```

<div class="content-ad"></div>

## 단계 5) 단계 3과 4를 반복합니다.

그런 다음 단계 3과 4를 반복하여 더 많은 병합 규칙을 찾고 어휘에 더 많은 문자 쌍을 추가합니다. 이 프로세스는 교육의 시작 부분에서 지정된 목표 크기에 도달할 때까지 계속됩니다.

아래는 이전에 작성한 BPE 모델을 상속하는 WordPiece의 구현입니다.

```js
class WordPiece(BPE):

    def add_hashes(self, word):
        ''' 단어의 각 문자에 # 기호 추가

            문자열로 된 단어를 받아서 처음을 제외한 각 문자에 # 기호를 추가합니다.
            결과를 반환하며 각 요소가 처음 문자만 일반 문자이고 나머지는 # 기호가
            앞에 붙은 문자인 리스트로 반환합니다.

            인수:
                word (str): # 기호를 추가할 단어

            반환값:
                hashed_word (list): # 기호를 추가한 문자의 목록
        '''
        hashed_word = [word[0]]

        for char in word[1:]:
            hashed_word.append(f'##{char}')

        return hashed_word


    def create_merge_rule(self, corpus):
        ''' 병합 규칙을 만들어 self.merge_rules 목록에 추가합니다.

            인수:
                corpus (list[tuple(list, int)]): 단어 목록에서 단어의 개별 문자
                    (또는 나중 반복에서 단어의 하위단어)를 표현하는 요소 및 단어
                    빈도를 나타내는 정수를 두 번째 요소로 하는 튜플의 목록

            반환값:
                없음
        '''
        pair_frequencies = self.find_pair_frequencies(corpus)
        char_frequencies = self.find_char_frequencies(corpus)
        pair_scores = self.find_pair_scores(pair_frequencies, char_frequencies)

        highest_scoring_pair = max(pair_scores, key=pair_scores.get)
        self.merge_rules.append(highest_scoring_pair.split(','))
        self.vocabulary.append(highest_scoring_pair)


    def create_vocabulary(self, words):
        ''' 단어 목록에서 고유 문자 목록을 생성합니다.

            BPE 알고리즘과 달리 각 문자를 일반적으로 저장하는 대신 단어의 시작
            문자 (표시되지 않음)와 단어의 중간 또는 끝에 있는 문자('##'로 표시)를
            구분합니다. 예를 들어, 단어 'cat'은 ['c', '##a', '##t']로 분할됩니다.

            인수:
                words (list): 입력 텍스트의 단어를 포함하는 문자열의 목록

            반환값:
                vocabulary (list): 입력 단어 목록의 모든 고유 문자 목록
        '''
        vocabulary = set()
        for word in words:
            vocabulary.add(word[0])
            for char in word[1:]:
                vocabulary.add(f'##{char}')

        # 나중에 추가할 수 있도록 목록으로 변환
        vocabulary = list(vocabulary)
        return vocabulary


    def find_char_frequencies(self, corpus):
        ''' 코퍼스에서 각 문자의 빈도수를 찾습니다.

            코퍼스를 순환하고 문자의 빈도수를 계산합니다.
            'c'와 '##c'는 서로 다른 문자임에 유의하세요.
            'c'는 단어의 시작 문자를 나타내고, '##c'는 단어의 중간 또는 끝을
            나타냅니다. 각 문자 쌍을 키로, 해당 빈도를 값으로 하는 사전 반환합니다.

            인수:
                corpus (list[tuple(list, int)]): 단어 목록에서 단어의 개별 문자
                    (또는 나중 반복에서 단어의 하위단어)를 표현하는 요소 및 단어
                    빈도를 나타내는 정수를 두 번째 요소로 하는 튜플의 목록

            반환값:
                char_frequencies (dict): 입력 코퍼스의 문자 및 해당 빈도수를
                    키와 값으로 하는 사전
        '''
        char_frequencies = dict()

        for word, word_freq in corpus:
            for char in word:
                if char in char_frequencies:
                    char_frequencies[char] += word_freq
                else:
                    char_frequencies[char] = word_freq

        return char_frequencies


    def find_pair_scores(self, pair_frequencies, char_frequencies):
        ''' 코퍼스에서 각 문자 쌍에 대한 쌍 점수를 찾습니다.

            pair_frequencies 사전을 순환하고 코퍼스에서 각 인접 문자 쌍의 쌍
            점수를 계산합니다. 점수를 사전에 저장하고 반환합니다.

            인수:
                pair_frequencies (dict): 코퍼스에서 인접 문자 쌍을 키로, 각
                    쌍 빈도수를 값으로 하는 사전

                char_frequencies (dict): 코퍼스에서 문자를 키로, 해당 빈도수를
                    값으로 하는 사전

            반환값:
                pair_scores (dict): 입력 코퍼스의 인접 문자 쌍을 키로, 해당
                    쌍 점수를 값으로 하는 사전
        '''
        pair_scores = dict()

        for pair in pair_frequencies.keys():
            char_1 = pair.split(',')[0]
            char_2 = pair.split(',')[1]
            denominator = (char_frequencies[char_1] * char_frequencies[char_2])
            score = (pair_frequencies[pair]) / denominator
            pair_scores[pair] = score

        return pair_scores


    def get_merged_chars(self, char_1, char_2):
        ''' 가장 높은 점수의 쌍을 병합하고 self.merge 메서드에 반환합니다.

            필요에 따라 # 기호를 제거하고 가장 높은 점수의 쌍을 병합한 후
            병합된 문자를 self.merge 메서드에 반환합니다.

            인수:
                char_1 (str): 가장 높은 점수의 쌍에서 첫 번째 문자
                char_2 (str): 가장 높은 점수의 쌍에서 두 번째 문자

            반환값:
                merged_chars (str): 병합된 문자
        '''
        if char_2.startswith('##'):
            merged_chars = char_1 + char_2[2:]
        else:
            merged_chars = char_1 + char_2

        return merged_chars


    def initialize_corpus(self, words):
        ''' 각 단어를 문자로 분할하고 단어 빈도수를 계산합니다.

            입력 단어 목록의 각 단어를 모든 문자로 분할합니다. 각 단어에 대해
            분할된 단어를 튜플의 첫 번째 요소로 리스트로 저장합니다.
            단어의 빈도수는 정수로 튜플의 두 번째 요소로 저장합니다.
            이 작업을 수행한 후 결과인 'corpus' 목록 반환합니다.

            인수:
                없음

            반환값:
                corpus (list[tuple(list, int)]): 단어 목록에서 단어의 개별 문자
                    (또는 나중 반복에서 단어의 하위단어)를 표현하는 요소와 단어
                    목록에서 해당 단어의 빈도수를 나타내는 정수를 표시하는
                    두 번째 요소로 하는 튜플의 목록
        '''
        corpus = self.calculate_frequency(words)
        corpus = [(self.add_hashes(word), freq) for (word, freq) in corpus]
        return corpus

    def tokenize(self, text):
        ''' 텍스트를 토큰 목록으로 만듭니다.

            인수

<div class="content-ad"></div>

WordPiece 알고리즘은 BPE 알고리즘에 주어진 장난감 데이터세트와 동일한 데이터세트로 아래에서 훈련됩니다. 이번에 학습한 토큰은 매우 다른 것을 알 수 있습니다. WordPiece는 문자가 서로 더 자주 함께 나타나는 경우를 선호하며, 그래서 데이터세트에 함께만 존재하고 홀로 존재하지 않는 'm'과 'p'는 즉시 병합됩니다. 여기서 이 아이디어는 모델이 문자를 병합함으로써 무엇이 손실되는지 고려하도록 강요하는 것입니다. 즉, 이러한 문자들이 항상 함께 있는가요? 그렇다면, 전혀 하나의 단위로 명백하게 병합되어야 합니다. 또는, 코퍼스에서 문자가 매우 빈번한가요? 그렇다면, 문자는 그냥 일반적이며 데이터세트 안에서 풍부하게 나타나므로 다른 토큰 옆에 나타날 것입니다.

```js
wp = WordPiece()
wp.train(words, 30)

print(f'INITIAL CORPUS:\n{wp.corpus_history[0]}\n')
for rule, corpus in list(zip(wp.merge_rules, wp.corpus_history[1:])):
    print(f'NEW MERGE RULE: Combine "{rule[0]}" and "{rule[1]}"')
    print(corpus, end='\n\n')
```

```js
초기 코퍼스:
[(['c', '##a', '##t'], 5), (['c', '##a', '##t', '##s'], 2), 
(['e', '##a', '##t'], 10), (['e', '##a', '##t', '##i', '##n', '##g'], 3), 
(['r', '##u', '##n', '##n', '##i', '##n', '##g'], 2), 
(['j', '##u', '##m', '##p', '##i', '##n', '##g'], 1), 
(['f', '##o', '##o', '##d'], 6)]

NEW MERGE RULE: "##m"과 "##p" 병합
[(['c', '##a', '##t'], 5), (['c', '##a', '##t', '##s'], 2), 
(['e', '##a', '##t'], 10), (['e', '##a', '##t', '##i', '##n', '##g'], 3), 
(['r', '##u', '##n', '##n', '##i', '##n', '##g'], 2), 
(['j', '##u', '##mp', '##i', '##n', '##g'], 1), 
(['f', '##o', '##o', '##d'], 6)]

(이하 생략)
```

이제 WordPiece 알고리즘이 훈련되었으므로(즉, 모든 병합 규칙이 발견되었으므로), 모델은 모든 텍스트를 토큰화하기 위해 각 단어를 모든 문자로 분리한 다음 문자열의 처음부분에 대해 알려진 토큰을 찾을 수 있는 최대 토큰을 찾아서, 나머지 부분은 찾을 수 있는 최대 토큰을 찾는 방식으로 사용할 수 있습니다. 이 과정은 더 이상 훈련 데이터로부터 알려진 토큰과 일치하지 않을 때까지 반복되며, 따라서 문자열의 남은 부분은 최종 토큰으로 취합니다.

<div class="content-ad"></div>

학습 데이터가 제한적이지만, 모델은 여전히 유용한 토큰을 학습했습니다. 그러나 많은 추가 학습 데이터가 필요함을 명백히 알 수 있습니다. 이 토크나이저를 유용하게 만들기 위해 더 많은 학습 데이터가 필요합니다. 예시 문자열에 대한 성능을 테스트할 수 있습니다. 예시로 'jumper' 단어로 시작해보겠습니다. 먼저 문자열은 ['jump', 'er']로 분리됩니다. 왜냐하면 jump는 단어의 시작에서 발견할 수 있는 가장 큰 토큰이기 때문입니다. 다음으로 er 문자열은 각각의 문자 e와 r로 나뉩니다. 

```js
print(wp.tokenize('jumper'))
```

```js
['jump', 'e', 'r']
```

## 단일 토큰화

<div class="content-ad"></div>

Unigram 토크나이저는 BPE와 WordPiece와 다른 방식으로 작동합니다. 큰 어휘로 시작하여 원하는 크기에 도달할 때까지 반복적으로 줄여나갑니다.

Unigram 모델은 각 단어 또는 문자의 확률을 고려하는 통계적 방법을 사용합니다. 예를 들어, "Cats are great but dogs are better"라는 문장은 [`Cats`, `are`, `great`, `but`, `dogs`, `are`, `better`] 또는 [`C`, `a`, `t`, `s`, `_a`, `r`, `e`, `_g`,`r`, `e`, `a`, `t`, `_b`, `u`, `t`, `_d`, `o`, `g`, `s` `_a`, `r`, `e`, `_b`, `e`, `t`, `t`, `e`, `r`]로 분할될 수 있습니다. 문장이 문자로 분할된 경우, 새로운 단어의 시작을 나타내기 위해 각 문자의 시작 부분에 밑줄이 추가됩니다.

이러한 목록의 각 요소는 토큰 t로 간주될 수 있으며, t1, t2, ..., tn의 일련의 토큰이 발생할 확률은 다음과 같습니다:

<img src="/assets/img/2024-05-23-TokenizationACompleteGuide_2.png" />

<div class="content-ad"></div>

Unigram 모델은 다음 단계를 통해 훈련됩니다:

단계 1) 코퍼스 구성

언제나처럼 입력 텍스트는 정규화 및 사전 토크나이제이션 모델에 전달되어 깨끗한 단어가 생성됩니다. 그런 다음 단어들은 Unigram 모델에 전달되어 각 단어의 빈도를 결정하고, 이 숫자를 단어와 함께 코퍼스라는 목록에 저장합니다.

단계 2) 어휘 구성

<div class="content-ad"></div>

Unigram 모델의 어휘 크기는 매우 크게 시작되고, 원하는 크기에 도달할 때까지 반복적으로 감소합니다. 초기 어휘를 구성하려면 말뭉치에서 가능한 모든 부분 문자열을 찾습니다. 예를 들어, 말뭉치의 첫 번째 단어가 'cats'인 경우, 부분 문자열 ['c', 'a', 't', 's', 'ca', 'at', 'ts', 'cat', 'ats']이 어휘에 추가됩니다.

3단계) 각 토큰의 확률 계산

토큰의 확률은 말뭉치에서 토큰의 발생 횟수를 찾아 총 토큰 발생 횟수로 나누어 근사적으로 계산됩니다.

![이미지](/assets/img/2024-05-23-TokenizationACompleteGuide_3.png)

<div class="content-ad"></div>

4단계) 단어의 모든 가능한 세분화 찾기

학습 말뭉치에서 단어가 cat인 경우를 고려해보겠습니다. 이는 다음과 같이 세분화될 수 있습니다:

[`c`, `a`, `t`]

[`ca`, `t`]

<div class="content-ad"></div>

[`c`, `at`]

[`cat`]

단계 5) 말뭉치에서 발생 가능한 각 세분화의 근사 확률 계산

위의 방정식들을 결합하면 각 토큰 시리즈에 대한 확률을 얻을 수 있습니다. 예를 들어, 이것은 다음과 같이 보일 수 있습니다:

<div class="content-ad"></div>

```markdown
<img src="/assets/img/2024-05-23-TokenizationACompleteGuide_4.png" />

가장 높은 확률 점수를 가진 세그먼트 [`c`, `at`]가 사용되어 단어를 토크나이즈했습니다. 따라서 단어 cat은 [`c`, `at`]으로 토큰화됩니다. 단어가 긴 경우 토큰화시 단어 내 여러 곳에서 분할이 발생할 수 있습니다. 예를 들어 [`token`, `iza`, tion] 또는 [`token`, `ization`] 같은 경우도 있을 수 있습니다.

6단계) 손실 계산

손실이란 모델의 점수를 나타내며, 중요한 토큰이 어휘에서 제거되면 손실이 크게 증가하지만 중요하지 않은 토큰이 제거되면 손실은 크게 증가하지 않습니다. 모델에서 각 토큰을 제거했을 때 손실이 얼마나 되는지 계산하여, 어휘 중에서 가장 쓸모없는 토큰을 찾을 수 있습니다. 훈련 세트 말뭉치에서 가장 유용한 토큰만 남도록 어휘 크기가 감소할 때까지 반복적으로 수행할 수 있습니다. 손실은 다음과 같이 주어집니다: 
```

<div class="content-ad"></div>

```markdown
![Tokenization Guide](/assets/img/2024-05-23-TokenizationACompleteGuide_5.png)

필요한 양만큼 문자가 제거되어 어휘를 원하는 크기로 줄일 때, 교육은 완료되고 모델을 사용하여 단어를 토큰화할 수 있습니다.

## BPE, WordPiece 및 Unigram 비교

학습 세트 및 토큰화해야 할 데이터에 따라 어떤 토크나이저가 다른 것보다 더 잘 작동할 수 있습니다. 언어 모델에 대한 토크나이저를 선택할 때, 특정 사용 사례에 사용된 학습 세트를 실험하여 최상의 결과를 얻는 것이 가장 좋을 수 있습니다. 그러나 이 세 가지 토크나이저의 일반적인 경향에 대해 논의하는 것이 유용할 수 있습니다.
```

<div class="content-ad"></div>

세 가지 중에서 BPE가 현재 언어 모델 토크나이저로 가장 인기 있는 선택인 것으로 보입니다. 그러나 변화가 빠르게 일어나는 이 분야에서는 앞으로 변동이 있을 수 있습니다. 사실, SentencePiece와 같은 다른 서브워드 토크나이저들이 최근에 훨씬 더 인기를 얻고 있습니다.

WordPiece는 BPE와 Unigram에 비해 더 많은 단어 토큰을 생성하는 것으로 보입니다. 그러나 모델 선택과 관계 없이 어휘 크기가 커질수록 모든 토크나이저가 더 적은 토큰을 생성하는 것으로 보입니다.

최종적으로, 토크나이저의 선택은 모델과 함께 사용하려는 데이터셋에 따라 다릅니다. 안전한 선택은 BPE 또는 SentencePiece를 시도하고, 그 이후에 실험하는 것일 수 있습니다.

## 후처리

<div class="content-ad"></div>

토큰화 파이프라인의 마지막 단계는 후처리입니다. 여기서 필요한 경우 출력에 최종 수정을 가할 수 있습니다. BERT는 이 단계를 사용하여 두 가지 추가 토큰을 추가하는 데 유명합니다:

- [CLS] - 이 토큰은 `classification`를 나타내며 입력 텍스트의 시작을 표시하는 데 사용됩니다. 이는 BERT에서 필요한 것인데, 이 토큰의 이름에서 알 수 있듯이 이를 사용하여 분류 작업이 수행되었기 때문입니다. 분류 작업에 사용되지 않을 때도 모델에서 여전히 이 토큰을 예상합니다.
- [SEP] - 이 토큰은 `separation`을 나타내며 입력에서 문장을 분리하는 데 사용됩니다. BERT가 수행하는 많은 작업에 유용하며, 동일한 프롬프트에서 동시에 여러 지시사항을 처리할 때도 사용됩니다.

# Python 라이브러리의 토크나이저

Hugging Face는 Python을 포함한 여러 프로그래밍 언어에서 사용할 수 있는 토크나이저 라이브러리를 제공합니다. 이 라이브러리에는 사용자가 사전 훈련된 모델을 사용할 수 있는 일반 Tokenizer 클래스가 포함되어 있으며, 전체 목록은 Hugging Face 웹사이트에서 확인할 수 있습니다. 게다가, 라이브러리에는 사용자가 자체 데이터로 훈련할 수 있는 네 가지 사전 제작되지만 미학습된 모델도 포함되어 있습니다. 이는 특정 유형의 문서에 튜닝된 특정 토크나이저를 작성하는 데 유용합니다. 아래 셀은 Python에서 사전 훈련된 및 미학습된 토크나이저를 사용하는 예시를 보여줍니다.

<div class="content-ad"></div>

프리트레인 토크나이저 사용하기

토크나이저 라이브러리를 사용하면 프리트레인 토크나이저를 쉽게 사용할 수 있습니다. Tokenizer 클래스를 가져와서 from_pretrained 메소드를 호출하고 사용할 토크나이저의 모델 이름을 전달하면 됩니다. 모델의 목록은 [16]에서 확인할 수 있습니다.

```js
from tokenizers import Tokenizer

tokenizer = Tokenizer.from_pretrained('bert-base-cased')
```

토크나이저 학습하기

<div class="content-ad"></div>

원하는 토큰 만들기되지만 미학습 토크나이저를 사용하려면 tokenizers 라이브러리에서 원하는 모델을 가져와서 모델 클래스의 인스턴스를 만들면 됩니다. 위에서 설명한대로 라이브러리에는 네 가지 모델이 포함되어 있습니다:

- BertWordPieceTokenizer - 유명한 Bert 토크나이저인 WordPiece를 사용합니다.
- CharBPETokenizer - 원래의 BPE(BPE) 
- ByteLevelBPETokenizer - BPE의 바이트 레벨 버전
- SentencePieceBPETokenizer - SentencePiece에서 사용하는 BPE 구현과 호환되는 버전

모델을 학습하려면 train 메서드를 사용하고 학습 데이터가 포함된 파일의 경로(또는 파일 경로 목록)를 전달하면 됩니다. 학습을 마치면 모델은 encode 메서드를 사용하여 일부 텍스트를 토큰화하는 데 사용할 수 있습니다. 마지막으로 학습된 토크나이저는 save 메서드를 사용하여 저장할 수 있으므로 학습을 다시 수행할 필요가 없습니다. 아래는 Hugging Face Tokenizers GitHub 페이지에서 제공되는 예제를 수정한 예시 코드입니다 [17].

```js
# 토크나이저 가져오기
from tokenizers import BertWordPieceTokenizer, CharBPETokenizer, \
                       ByteLevelBPETokenizer, SentencePieceBPETokenizer

# 모델 인스턴스화
tokenizer = CharBPETokenizer()

# 모델 학습
tokenizer.train(['./path/to/files/1.txt', './path/to/files/2.txt'])

# 텍스트 토큰화
encoded = tokenizer.encode('I can feel the magic, can you?')

# 모델 저장
tokenizer.save('./path/to/directory/my-bpe.tokenizer.json')
```

<div class="content-ad"></div>

토크나이저 라이브러리는 이전 섹션에서 보여주었던 것과 같이 처음부터 전체 모델을 구현할 필요 없이 매우 빠르게 사용자 정의 토크나이저를 만들 수 있는 구성 요소도 제공합니다. 아래 셀에는 Hugging Face GitHub 페이지 [17]에서 가져온 예시가 표시되어 있습니다. 해당 예시에서는 토크나이저의 사전 토크나이제이션 및 디코딩 단계를 사용자 정의하는 방법을 보여줍니다. 이 경우, 사전 토크나이제이션 단계에서 접두어 공백이 추가되었고, 디코더로는 ByteLevel 디코더가 선택되었습니다. Hugging Face 문서 [18]에는 사용자 정의 옵션의 전체 목록이 제공됩니다.

```js
from tokenizers import Tokenizer, models, pre_tokenizers, decoders, trainers, \
                       processors

# 토크나이저 초기화
tokenizer = Tokenizer(models.BPE())

# 사전 토크나이제이션 및 디코딩 사용자 정의
tokenizer.pre_tokenizer = pre_tokenizers.ByteLevel(add_prefix_space=True)
tokenizer.decoder = decoders.ByteLevel()
tokenizer.post_processor = processors.ByteLevel(trim_offsets=True)

# 그리고 학습
trainer = trainers.BpeTrainer(
    vocab_size=20000,
    min_frequency=2,
    initial_alphabet=pre_tokenizers.ByteLevel.alphabet()
)
tokenizer.train([
    "./path/to/dataset/1.txt",
    "./path/to/dataset/2.txt",
    "./path/to/dataset/3.txt"
], trainer=trainer)

# 그리고 저장
tokenizer.save("byte-level-bpe.tokenizer.json", pretty=True)
```

# 결론

토큰화 파이프라인은 언어 모델의 중요한 부분이며, 어떤 종류의 토크나이저를 사용할지 결정할 때 신중한 고려가 필요합니다. 요즘에는 Hugging Face와 같은 라이브러리의 개발자들이 우리를 대신하여 많은 이러한 결정을 내려주고 있습니다. 이를 통해 사용자는 빠르게 사용자 지정 데이터로 언어 모델을 학습하고 사용할 수 있습니다. 그러나 토큰화 방법에 대한 탄탄한 이해는 모델을 미세 조정하고 다양한 데이터셋에서 추가 성능을 얻는 데 귀중합니다.

<div class="content-ad"></div>

# 참고 자료

[1] 표지 이미지 — Stable Diffusion Web

[2] 토큰 정의 — Stanford NLP 그룹

[3] 단어 토크나이저 — Towards Data Science

<div class="content-ad"></div>

- [4] TransformerXL 논문 — ArXiv

- [5] Tokenizers — Hugging Face

- [6] 단어 기반, 서브워드, 문자 기반 토크나이저 — Towards Data Science

- [7] 토큰화 파이프라인 — Hugging Face

<div class="content-ad"></div>

\[8\] Pre-tokenizers — Hugging Face

\[9\] Language Models are Unsupervised Multitask Learners — OpenAI

\[10\] BART Model for Text Autocompletion in NLP — Geeks for Geeks

\[11\] Byte Pair Encoding — Hugging Face

<div class="content-ad"></div>

[12] WordPiece 토큰화 — Hugging Face

[13] 두 분 NLP — 토큰화 방법론의 분류 — Medium

[14] 서브워드 토크나이저 비교 — Vinija AI

[15] BERT가 단어 맥락 관계를 배우는 데 어떻게 Attention 메커니즘과 Transformer를 활용하는가 — Medium

<div class="content-ad"></div>

[16] 사전 훈련된 모델 목록 — Hugging Face

[17] Hugging Face Tokenizers 라이브러리 — GitHub

[18] 사전 토크나이제이션 문서 — Hugging Face