---
title: "고유명사 인식 노출 - 필수 가이드"
description: ""
coverImage: "/assets/img/2024-05-20-NamedEntityRecognitionUnmaskedTheEssentialGuide_0.png"
date: 2024-05-20 20:57
ogImage: 
  url: /assets/img/2024-05-20-NamedEntityRecognitionUnmaskedTheEssentialGuide_0.png
tag: Tech
originalTitle: "Named Entity Recognition Unmasked — The Essential Guide"
link: "https://medium.com/towards-data-science/named-entity-recognition-unmasked-the-essential-guide-404ad0568964"
---


```markdown
![NamedEntityRecognitionUnmaskedTheEssentialGuide](/assets/img/2024-05-20-NamedEntityRecognitionUnmaskedTheEssentialGuide_0.png)

## 소개

알겠어요, 상상해보세요— 정보를 처리하고 싶은 기사, 저널 및 블로그의 산더미가 많이 있습니다. 이제 이 데이터에 작업할 기회가 커뮤니티에 도움이 될 것이라고 생각해보세요. 하지만 이 데이터를 즉시 공유하고 싶지는 않을 것입니다. 왜냐하면 사람들의 동의 없이 공유하면 안 되는 개인 정보가 포함될 수도 있기 때문입니다.

모든 사람으로부터 허락을 받는 것은 현실적이지 않기 때문에 당신은 자신의 기술을 사용하여 FERPA 가이드라인에 따라 개인 정보를 숨기기로 결정합니다. 회사들이 분석이나 데모 목적으로 외부에서 공유할 때 데이터를 가리는 것은 흔한 일이며, 숫자 데이터의 경우 더 쉽습니다. 여기서는 텍스트 데이터를 사용하여 동일한 작업을 하려고 합니다.
```

<div class="content-ad"></div>

지금 여기서는 텍스트 데이터에 대해 이야기하고 있으므로 자연어 처리(NLP) 기술 중 하나를 적용할 것입니다. 그 기술은 Named Entity Recognition (NER)로, 숨겨진 데이터 보물을 찾아내는 신뢰할 수 있는 NLP 탐정입니다. 여기서 목적은 개인 정보를 식별하는 것입니다.

이제 NER이 어떻게 작동하는지, NER 메커니즘의 개념, NER을 구현하는 방법, 선택할 수 있는 해결책 접근 방식 및 그 이유, 그리고 Python에서 이 문제에 대한 해결책을 구현하는 방법에 대해 더 자세히 살펴봅시다.

## Named Entity Recognition (NER): 기술적 분해

간단히 말해, NER은 컴퓨터에게 텍스트 내에서 특정 '개체'를 식별하는 것입니다. 이 경우에는 개인 식별 정보(PII)를 의미합니다. 프로그램에 하이라이터 세트를 제공하는 것과 유사하게 생각해보세요. 이름, 장소, 회사, 대학, 학생 ID, 이메일 주소 또는 개인을 식별할 수 있는 것들을 각각 나타내는 하이라이터가 있다고 상상해보세요. 이제 NER이 어떻게 작동하는지 간단히 살펴보겠습니다:

<div class="content-ad"></div>

- Rule-based Systems: 예전 방식입니다. 우리는 "이름은 일반적으로 대문자로 시작합니다."와 같은 수기 작성 규칙을 만듭니다. 기본적인 경우에는 잘 작동하지만 굉장히 복잡해질 수 있습니다. 또한 많은 규칙이 있다면 더럽고 혼란스러워질 수 있습니다.
- Machine Learning Approach: 통계 모델은 대규모 데이터 세트에서 학습합니다. 여러분의 NER 시스템에 많은 예제를 보여주는 것처럼 생각해보세요. 모델이 스스로 패턴을 찾을 수 있도록 합니다. 이것이 모든 것에 대한 머신 러닝이 작동하는 방식입니다. 그러나 텍스트 데이터에 대한 성능 문제가 발생할 수 있습니다.
- Deep Learning Superstars: 신경망은 텍스트, 이미지 및 비디오 데이터 관련 문제에 대한 가장 유명한 방법론입니다. 우리가 하는 것과 유사한 복잡한 언어를 다룹니다. 이러한 모델은 맥락을 이해하여 매우 정확합니다. 이곳의 유일한 조건은 많은 양의 데이터를 사용해야 한다는 것입니다. 그렇지 않으면 모델은 대부분의 학습 데이터를 기억하게 됩니다 (과적합). 이를 제어하는 기술은 있지만, 여전히 많은 데이터 집합에서 가장 잘 작동합니다.

## 자세한 기술: NER 뒤의 두뇌

NER은 다양한 기술을 활용할 수 있음을 확인했습니다. 각각의 장점을 가진 기술에 대해 자세히 알아봅시다:

- Conditional Random Fields (CRFs):

<div class="content-ad"></div>

NER 시스템을 위치를 인식하도록 가르치고 있다고 상상해보세요. "10 Made UP Street, London, UK."와 같은 주소 예시를 보여줍니다. CRF는 이를 뛰어넘는 데 뛰어납니다. 왜냐하면 단어들과 그들 사이의 관계에 대해 전체 시퀀스를 고려하기 때문입니다. 숫자를 따르는 "London"과 도시를 따르는 "UK"를 이어받으면 이것은 강한 위치 엔티티를 시사합니다. 이러한 이유로, CRF는 맥락이 매우 중요한 NER과 같은 작업에 강력합니다.

TDS에서 이에 대한 수학적 이론인 CRF를 설명한 Nikos Kafritsas의 훌륭한 기사를 읽어보세요.

2. LSTM 네트워크 (Long Short-Term Memory):

텍스트에서 사람 이름을 식별하고자 한다고 가정해봅시다. LSTMs는 RNN 이후에 크게 발명되었습니다. 왜냐하면 특별한 능력을 갖고 있기 때문입니다 — 기억! 네, 그들은 기억이나 맥락을 가지고 예측할 수 있습니다. CRF가 (현재 단어만 고려하는) 반면, LSTMs는 이전 단어를 기억하고 맥락을 잃지 않는 특성이 있습니다. 이것은 NER에 중요합니다. 왜냐하면 이것이 회사인 Apple인지 과일인 Apple인지를 이해하는 데 도움이 될 것입니다.

<div class="content-ad"></div>

또 다른 예시를 살펴보죠: "Dr. Smith is a renowned cardiologist"라는 문장에서 LSTM은 "Dr."이라는 칭호를 기억하고 그 맥락을 사용하여 "Smith"를 사람 이름으로 올바르게 분류할 수 있습니다. 

실제 세계 예시를 보겠습니다: 사람들이 언급된 기사를 기반으로 분류하는 뉴스 분류기 모델을 만든다고 상상해보세요. LSTM 기반 NER 시스템이 "Barack Obama"나 "Elon Musk"와 같은 엔티티를 정확하게 식별할 수 있습니다. 이들의 이름이 복잡한 문장 안에 나타나고 분류되어 있더라도 말이죠. 훌륭한 구현 방법이죠, 그렇죠?

Rian Dolphin의 LSTM에 대한 포괄적인 소개 기사를 읽어보세요.

3. Transformers:

<div class="content-ad"></div>

트랜스포머는 현재 자연어 처리의 핫한 주제이며, 개체명 인식(NER)도 예왌이 아닙니다. 이러한 모델은 특정 세부사항에 모든 주의를 집중하는 것과 같이 주의 메커니즘을 사용합니다. 그들이 하는 것은 전체 문장 전반에 걸쳐 관련 단어에 주의를 기울이는 것입니다. 인간이 처음 보는 텍스트를 읽는 것처럼 상상해보세요. 여기저기 훑어보면서 (스포트라이트처럼) 각 부분에 마음을 집중하고 의미를 파악하는 것입니다. 이 기술을 통해 그들은 복잡한 관계를 이해하고 미비한 개체조차 식별할 수 있습니다.

예를 들어, "Acme Corp의 CEO가 캘리포니아를 기반으로 새 제품 출시를 발표했습니다." 라는 문장을 생각해보세요. 트랜스포머 기반 NER 시스템은 "CEO"와 "Acme Corp"에 주의를 기울일 수 있습니다. 이들이 여러 단어로 분리되어 있는데도요. 그런 다음 이 주의를 사용하여 "Acme Corp"를 조직으로 올바르게 분류할 수 있습니다.

이 능력은 트랜스포머를 의료 연구 논문에서 의학 용어를 식별하거나 소셜 미디어 데이터에서 특정 제품명을 식별하는 것과 같은 작업에 이상적으로 만들어줍니다.

제임스 브릭스의 기사 "NER with Transformers and Spacy"를 읽어보세요. 그리고 아직 '주의'에 대해 궁금하다면, 아륀 사르카의 "Attention과 Transformers에 대한 이해 - 파트 1"이라는 기사부터 읽어보는 것도 좋은 시작입니다.

<div class="content-ad"></div>

기본을 넘어선 발전 기술

NER의 세계는 끊임없이 진화하고 있습니다. 주목해야 할 몇 가지 흥미로운 발전 사항을 소개합니다:

- 양방향 LSTM(BiLSTM): 이들은 LSTM을 더욱 강력하게 만든 버전으로, 텍스트를 앞뒤로 처리합니다. 이로 인해 더 깊은 맥락을 이해할 수 있습니다. 이 기법에는 단점이 있습니다. 앞뒤로 문장을 모두 입력하기 때문에 예측이 어렵습니다. 그러나 시스템은 맥락에 대해 알고 있습니다.
- 명명된 엔티티 구별(NED): 다시 말해, "Apple"이라는 단어를 본다고 상상해 봅시다. 이것이 기술 거대 기업을 가리키는 것인지 아니면 과일을 가리키는 것인지 구분해야 합니다. NER은 NED와 결합하여 콘텍스트에서 가장 가능성이 높은 의미를 식별할 수 있습니다.

이러한 기술을 이해하고 최신 발전 사항을 파악함으로써, 텍스트 데이터에서 가치 있는 개인 정보를 발굴하고 연구 노력을 지원하는 데 NER의 힘을 활용할 수 있습니다.

<div class="content-ad"></div>

## NER 작업 실습: 프로젝트 코드 미리보기

손을 더럽혀 볼 시간이에요! 만약 Python과 훌륭한 spaCy 라이브러리를 사용한다고 가정해봅시다:

Python

```js
python -m spacy download en_core_web_trf
```

<div class="content-ad"></div>

```Markdown
```js
pip install spacy
pip install nltk
```

```js
import spacy
from spacy import displacy
import nltk  # NLTK를 추가 작업에 사용할 예정
from nltk.corpus import stopwords  # NLTK 활용 예시

# 강력한 사전 훈련된 NER 모델 로드 (필요에 따라 조정)
nlp = spacy.load("en_core_web_trf")

# 분석할 텍스트 정의
text = """
Jane Doe, a researcher at Stanford University, recently published a paper on 
Natural Language Processing.  Dr. John Smith from MIT will be collaborating on the 
project.  They can be reached at jane.doe@stanford.edu and john_smith@mit.edu.
"""

# NER로 텍스트 처리
doc = nlp(text)

# 식별된 엔티티 출력
print("식별된 엔티티:")
for entity in doc.ents:
    print(entity.text, entity.label_)

# NER 결과 시각화
displacy.render(doc, style="ent", jupyter=True)
```  

<div class="content-ad"></div>

위에서 확인할 수 있듯이 NER은 이름과 조직을 완벽하게 식별할 수 있었지만 이메일 주소를 놓쳤습니다. 이 현상이 발생한 이유를 살펴보고 어떻게 수정할 수 있는지 알아봅시다.

이메일 주소를 놓치는 이유

- NER 모델 한계: 표준 NER 모델은 일반적으로 이름, 조직, 위치 등과 같은 범주로 훈련됩니다. 몇 가지 이메일 패턴을 잡아낼 수 있을 수 있지만 이것이 주된 강점이 아닙니다. 따라서 이 경우에는 그것을 놓쳤습니다.
- 이메일 주소의 복잡성: 이메일 형식은 놀랍도록 다양할 수 있습니다. "Gmail 및 Yahoo"와 같이 간단한 것들은 인식될 수 있지만 더 복잡한 패턴은 놓칠 수 있습니다. 예를 들어 gmail ID를 잡아낼 수 있지만 특정 조직별 ID를 놓칠 수 있습니다. 다시 말해, 이 경우에 발생한 것과 같습니다.

이유를 알고 있지만, 문제를 어떻게 수정할지에 더 초점을 맞출 수 있을 것입니다!

<div class="content-ad"></div>

다섯가지 기술 중 하나를 사용하여 작업을 개인화하고 해결합시다:

- 정규 표현식 (Regex): 정규 표현식을 사용하여 이메일 주소에 일치하는 특정 패턴을 작성할 수 있습니다. 이 기술은 예전부터 개발되어 많이 사용되고 있습니다. 이를 프로그래밍에서 패턴을 인식하기 위한 어렵게 코딩된 것으로 생각할 수 있습니다. 다음은 기본적인 예제입니다:

```js
import re

email_regex = r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b'
emails = re.findall(email_regex, text)
print(emails)
```

- 전문 라이브러리 사용: email_validator와 같은 라이브러리를 사용할 수 있습니다. 이러한 라이브러리들은 이메일 식별 및 유효성 검사에 전념하고 있습니다. 이메일을 유효성 검사하는 것이 당신의 경우라면 이 옵션을 사용할 수 있습니다.
- NER 모델 개선: 기존 모델을 세밀하게 조정하여 이메일 주소 예제를 추가 엔티티 유형으로 제공할 수 있습니다. 그러나 이 작업에는 더 많은 데이터와 복잡한 모델 훈련이 필요합니다. BERT 등과 같은 사전 훈련된 모델을 사용하는 것도 포함됩니다. 다시 한번, James Briggs의 'NER with Transformers and Spacy' 라는 글을 읽어보세요. 이 글은 roBERTA를 세밀하게 조정하고 spaCy를 사용하는 방법에 대해 이야기합니다. 이 옵션을 확실하게 이해하실 것입니다.

<div class="content-ad"></div>

첫 번째 방법을 선택하여 시연하고 코드에 구현해 보겠습니다. 이메일 추출을 위한 섹션을 아래와 같이 추가할 수 있습니다:

Python

```js
import re

email_regex = r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b'
emails = re.findall(email_regex, text)
print("찾은 이메일:", emails)
```

결과

<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-NamedEntityRecognitionUnmaskedTheEssentialGuide_2.png" />

가장 적합한 방법 선택하기

이상적인 해결책은 프로젝트의 특정성에 따라 다릅니다:

- 간단한 이메일 + 정확도: 정규식(Regex)이 충분할 것으로 예상됩니다.
- 복잡한 이메일 + 신뢰성: 전문 이메일 유효성 검사 라이브러리가 가장 안전합니다.
- NER 재학습: 다른 엔티티의 NER 정확도가 중요하고 이메일 중심 데이터가 많이 있는 경우, 모델을 다시 학습시키는 것이 장기적인 해결책이 될 수 있습니다. 당신이 상상한 대로, BERT 사전 훈련된 모델을 파인튜닝하여 사용하는 등의 멋진 기술을 사용할 수 있습니다. 이를 사용하기 전 고려해야 할 중요한 점은:

- 데이터: 파인튜닝을 위해서는 상당량의 레이블이 지정된 데이터가 필요합니다. 데이터가 제한적인 경우, 다른 기술(예: 정규식)이 처음에는 더 실용적일 수 있습니다.
- 복잡성: 파인튜닝은 설정 및 계산 리소스가 정규식이나 기본 라이브러리보다 많이 필요할 수 있습니다.

<div class="content-ad"></div>

## NER 스킬을 강화할 수 있는 리소스들

- spaCy: 훌륭한 NER 지원을 갖춘 우수한 NLP 라이브러리 (https://spacy.io/).
- NLTK: 클래식한 NLP 툴킷 (https://www.nltk.org/).
- Stanford CoreNLP: 강력한 NLP 도구 모음 (https://stanfordnlp.github.io/CoreNLP/)

앞으로의 길

NER은 아직 핫한 연구 분야입니다 — 복잡한 관계를 이해하고 사용자 정의 엔티티를 식별하며 다국어로 작동하는 모델에 대비하세요! 이 기술은 우리 주변의 방대한 텍스트에서 정보를 추출하고 활용하는 방식을 혁신하고 있습니다.

<div class="content-ad"></div>

# 작성자 소개

친애하는 독자 여러분, 저는 이 주제에 열정적이며 데이터 과학 주제와 생각거리 글에 대해 쓰는 것을 즐깁니다. 가장 중요한 것은 피드백을 받을 준비가 되어 있다는 것입니다!

여러분의 의견을 알고 싶습니다. 이 글이 어떤 식으로든 도움이 되었거나 피드백이 있다면 망설이지 마시고 남겨주세요! 또한, 이 주제에 대한 추가 설명이 필요하다면 언제든지 댓글을 남겨 주세요. 저는 여기서 해결하려고 하거나 다른 글을 쓸 것입니다!

<div class="content-ad"></div>

내 정보에 대해 더 알고 싶다면, 이곳에 내 소개 글이 있어:

함께 얘기 나눠보자...