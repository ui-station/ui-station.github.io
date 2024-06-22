---
title: "Keras 30 튜토리얼 엔드투엔드 딥 러닝 프로젝트 가이드"
description: ""
coverImage: "/assets/img/2024-05-20-Keras30TutorialEnd-to-EndDeepLearningProjectGuide_0.png"
date: 2024-05-20 20:09
ogImage: 
  url: /assets/img/2024-05-20-Keras30TutorialEnd-to-EndDeepLearningProjectGuide_0.png
tag: Tech
originalTitle: "Keras 3.0 Tutorial: End-to-End Deep Learning Project Guide"
link: "https://medium.com/towards-data-science/keras-3-0-tutorial-end-to-end-deep-learning-project-guide-3552187e3ff5"
---


![이미지](/assets/img/2024-05-20-Keras30TutorialEnd-to-EndDeepLearningProjectGuide_0.png)

# 소개

조금 전부터 Pytorch를 사용하기 시작했지만, 여전히 Keras의 간결한 코드 스타일과 신경망 모델을 몇 줄의 코드로 구현할 수 있던 옛날을 그리워합니다.

그래서 Keras가 지난 11월에 TensorFlow에 추가하여 Pytorch와 Jax를 백엔드로 지원한다고 발표했을 때 매우 흥분했습니다!

<div class="content-ad"></div>

그러나 모든 것이 완벽하지는 않았습니다: 최근에 Keras 3.0이 출시되었기 때문에 관련 튜토리얼과 문서는 아직 따라가지 못한 상태였고, 코드 이관 중에 몇 가지 어려움을 겪었습니다.

다행히도 노력 끝에 이제 버전 3.0을 원활하게 사용하여 다양한 end-to-end 모델 개발을 할 수 있게 되었습니다.

이 글에서는 Keras 3.0을 사용하는 데 도움이 되는 실용적인 경험 몇 가지를 공유하겠습니다. Keras 3.0의 서브클래싱 API를 사용하여 end-to-end 프로젝트를 완전히 처음부터 구축하는 방법을 알려주기 위해 전형적인 인코더-디코더 순환 신경망을 예로 들 것이며, 백엔드로 Pytorch를 사용할 때 고려해야 할 세부 사항에 대해 논의할 것입니다.

자, 시작해 보겠습니다.

<div class="content-ad"></div>

# 프레임워크 설치 및 환경 설정

## 프레임워크 설치

케라스 3.0 (또는 최신 버전)를 설치하는 것은 간단합니다. 공식 웹사이트의 시작 가이드 문서를 따라하면 됩니다.

케라스를 설치하기 전에, 해당 CUDA 버전에 맞춰 Pytorch를 먼저 설치하는 것을 권장합니다. 사용하는 그래픽 카드 드라이버 지원에 따라 CUDA 11.8 또는 CUDA 12.1이 작동합니다.


<div class="content-ad"></div>

파이토치를 백엔드로 사용할 수는 있지만, Keras 설치 과정에서 여전히 기본적으로 Tensorflow 버전 2.16.1이 설치됩니다.

이 버전의 Tensorflow는 CUDA 12.3에 기반하여 컴파일되었기 때문에 Keras를 설치한 후 CUDA가 없다는 경고 메시지를 만날 수 있습니다(이 이슈를 확인하세요).

```js
Could not find cuda drivers on your machine, GPU will not be used.
```

우리는 백엔드로 파이토치를 사용 중이므로 이 경고를 무시하는 것을 권장합니다.

<div class="content-ad"></div>

혹시라도 Tensorflow 로그를 계속 보이지 않도록 영구적으로 설정하고 싶으시면, 시스템 변수를 설정할 수도 있어요.

```js
import os

os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'
```

## 환경 설정

PyTorch와 Keras를 모두 설치한 후에는, Keras의 백엔드를 PyTorch로 설정하기 위해 환경 변수를 설정해야 해요. 이 작업은 두 가지 방법으로 할 수 있어요.

<div class="content-ad"></div>

- 설정 파일을 수정해주세요.
- 환경 변수를 설정해주세요.

먼저, 설정 파일 방법에 대해 논의해보겠습니다.

Keras의 설정 파일은 ~/.keras/keras.json에 있습니다. Windows를 사용 중이라면, 이 파일은 `사용자 디렉토리`/.keras/keras.json에 있습니다.

물론, KERAS_HOME 환경 변수를 설정하여 .keras 디렉토리의 위치도 변경할 수 있습니다.

<div class="content-ad"></div>

먼저 Keras를 처음 설치한 후에는 .keras 디렉토리를 바로 찾을 수 없을 수 있습니다. 이럴 때는 IPython이나 Jupyter Notebook에서 import keras를 실행하여 디렉토리를 찾을 수 있습니다.

그런 다음, keras.json 파일의 "backend" 키의 값을 "torch"로 변경하십시오.

```js
{
    "floatx": "float32",
    "epsilon": 1e-07,
    "backend": "torch",
    "image_data_format": "channels_last"
}
```

프로덕션 시스템이거나 Colab과 같은 클라우드 환경을 사용하는 경우, 구성 파일을 수정할 수 없을 수 있습니다. 이런 경우에는 환경 변수를 설정하여 이 문제를 해결할 수 있습니다:

<div class="content-ad"></div>

```js
os.environ["KERAS_BACKEND"] = "torch"
```

Keras 백엔드를 설정한 후, 다음 코드로 확인할 수 있어요:

```js
In:  import keras
     keras.config.backend()

Out: 'torch'
```

준비가 완료되면, 오늘의 프로젝트 실습을 공식적으로 시작할 거예요.

<div class="content-ad"></div>

# 프로젝트 실습: 시작부터 끝까지의 예시

프레임워크를 배우는 가장 빠른 방법은 실제 프로젝트를 실습하는 것입니다. 그래서 이제 제 약속을 이행할 시간입니다.

나는 당신에게 서브클래싱 API를 사용하여 신경 기계 번역(NMT) 모델을 구현하는 단계별 안내를 제공하고, Keras 3.0을 사용하는 몇 가지 세부 정보를 설명할 것입니다.

## 이론 소개

<div class="content-ad"></div>

NMT 모델에 익숙하지 않다면, 간단히 소개해 드리겠습니다:

NMT는 인코더-디코더 구조를 기반으로 한 순환 신경망 모델의 한 유형입니다.

이 구조에서는 임베딩 레이어와 RNN(이 글에서는 LSTM을 사용합니다) 레이어가 인코더를 형성하고, 또 다른 임베딩 레이어와 RNN 레이어가 디코더를 형성합니다.

원본 텍스트는 벡터화된 후 인코더 모듈로 입력됩니다. 일련의 단계를 거쳐 최종 상태가 디코더 모듈로 입력됩니다.

<div class="content-ad"></div>

또한 대상 텍스트도 디코더 모듈에 입력되지만, 디코더로 들어가기 전에 한 단계 앞으로 오프셋이 적용됩니다. 따라서 대상 텍스트의 처음 부분은 시작 시퀀스(SOS) 플레이스홀더로 시작합니다.

인코더의 입력 상태와 대상 텍스트의 입력은 디코더에서 순환 계산을 통해 처리되어 최종적으로 Dense 레이어에 출력됩니다. 거기서 각 텍스트 벡터의 확률을 계산해 대상 텍스트의 단어 벡터와 비교하고 손실을 계산합니다.

따라서 대상 텍스트의 끝을 표시하기 위해 대상 텍스트의 끝에 종결 시퀀스(EOS) 플레이스홀더를 추가합니다.

아래 다이어그램에서 전체 아키텍처가 표시됩니다:

<div class="content-ad"></div>

당연히 Transformer 아키텍처의 인기로 인해 케라스의 KerasNLP 패키지도 Bert 및 GPT와 같은 다양한 사전 훈련 모델을 제공합니다. 이 기사는 하지만 Keras 3.0을 사용하는 방법에 중점을 두므로 기본적인 RNN 네트워크를 사용하는 것이 충분합니다.

**모듈 및 플로우차트**

<div class="content-ad"></div>

프로덕션에 완전히 준비된 프로젝트이기 때문에, 우리는 Keras 3.0의 서븁클래싱 API를 기반으로 모듈을 구축합니다.

각 모듈과 그들의 상호작용을 명확히 이해하기 위해, 아래의 플로우차트를 만들었습니다:

![flowchart](/assets/img/2024-05-20-Keras30TutorialEnd-to-EndDeepLearningProjectGuide_2.png)

플로우차트의 디자인에 따라 코드를 작성할 것입니다.

<div class="content-ad"></div>

## 패키지 가져오기

Jupyter Notebook 환경에서는 프로젝트 첫부분에 관련 패키지를 모두 가져오는 것을 좋아해요.

이렇게 하면 중간에 뭔가 빠트린 게 있어도 한 군데에서 추가하면 되니까 import 셀을 찾느라 시간을 낭비할 필요가 없어요:

```js
from pathlib import Path
import pickle

import keras
from keras import layers, utils
import numpy as np

utils.set_random_seed(42)
```

<div class="content-ad"></div>

약간의 소소한 팁이에요: utils.set_random_seed 메서드를 사용하면 Python, Numpy, Pytorch의 랜덤 시드를 한 줄의 코드로 설정할 수 있어서 정말 편리해요.

## 데이터 준비

시작하기 전에 적절한 데이터를 선택해야 해요. 과거의 인코더-디코더 모델과 마찬가지로, 저희는 spa-eng 텍스트 데이터셋을 선택했어요.

다운로드 후, 먼저 spa.txt 파일의 내용을 확인해볼게요:

<div class="content-ad"></div>


비슷한 점이 두드러져요. La similitud es extraña. CC-BY 2.0 (프랑스) 속성: tatoeba.org #2691302 (CM) & #5941808 (albrusgher)
비슷한 점이 엄청나네요. El parecido es asombroso. CC-BY 2.0 (프랑스) 속성: tatoeba.org #2691302 (CM) & #6026125 (albrusgher)
결과가 약속 있어 보여요. Los resultados se antojan prometedores. CC-BY 2.0 (프랑스) 속성: tatoeba.org #8480484 (shekitten) & #8464272 (arh)
부자들은 많은 친구들을 가지고 있어요. Los ricos tienen muchos amigos. CC-BY 2.0 (프랑스) 속성: tatoeba.org #1579047 (sam_m) & #1457378 (marcelostockle)


위 내용을 보시다시피 적어도 세 개의 열을 포함하는 내용이 있습니다. 첫 번째 열은 원문이고 두 번째 열은 대상 텍스트로 탭으로 구분되어 있습니다.

파일이 크지 않기 때문에 numpy의 genfromtxt 메서드를 사용하여 이 데이터셋을 직접 읽어올 수 있습니다.

```python
text_file = Path("./temp/eng-spanish/spa-eng/spa.txt")

pairs = np.genfromtxt(text_file, delimiter="\t", dtype=str,
                     usecols=(0, 1), encoding="utf-8",
                     autostrip=True,
                     converters={1: lambda x: x.replace("¡", "").replace("¿", "")})
np.random.shuffle(pairs)
sentence_en, sentence_es = pairs[:, 0], pairs[:, 1]
```

<div class="content-ad"></div>

다음으로, 처리 결과를 확인해봅시다:

```js
In:   print(f"{sentence_en[0]} => {sentence_es[0]}")

Out:  I'm really sorry. => Realmente lo siento.
```

좋아요, 문제 없어요.

## 데이터 전처리

<div class="content-ad"></div>

다음으로, 텍스트 콘텐츠를 단어 벡터 데이터로 변환하기 위해 전처리해야 합니다.

우선, 몇 가지 상수를 정의합니다:

```js
class Configure:
    VOCAB_SIZE: int = 1000
    MAX_LENGTH: int = 50
    SOS: str = 'startofseq'
    EOS: str = 'endofseq'
```

그런 다음 데이터 처리 파이프라인을 시작합니다.

<div class="content-ad"></div>

Keras 3.0에서는 백엔드로 PyTorch를 선택했더라도 TextVectorization 레이어는 여전히 TensorFlow를 기반으로 구현되어 있습니다.

따라서 Keras 모델 내에서 TextVectorization을 레이어로 사용할 수 없고, 전처리 파이프라인에서 별도로 사용해야 합니다.

이로 인해 문제가 발생합니다. 훈련된 모델을 추론 작업을 위한 프로덕션 시스템으로 이관할 때 TextVectorization 어휘 없이는 벡터화를 수행할 수 없습니다.

그래서 우리는 어휘를 지속시키고 재사용해야 하지만, Keras 3.0의 TextVectorization 지속에 관한 몇 가지 문제가 있습니다. 이 부분에 대해 나중에 논의하겠습니다.

<div class="content-ad"></div>

텍스트 전처리 모듈을 사용하여 벡터화를 수행할 것입니다. 여기에 구체적인 코드가 있습니다:

```js
class TextPreprocessor:
    def __init__(self, 
                 en_config=None, es_config=None):
        if en_config is None:
            self.text_vec_layer_en = layers.TextVectorization(
                Configure.VOCAB_SIZE, output_sequence_length=Configure.MAX_LENGTH
            )
        else:
            self.text_vec_layer_en = layers.TextVectorization.from_config(en_config)
        
        if es_config is None:
            self.text_vec_layer_es = layers.TextVectorization(
                Configure.VOCAB_SIZE, output_sequence_length=Configure.MAX_LENGTH
            )
        else:
            self.text_vec_layer_es = layers.TextVectorization.from_config(es_config)
        
        self.adapted = False
        self.sos = Configure.SOS
        self.eos = Configure.EOS
        
    def adapt(self, en_sentences: list[str], es_sentences: list[str]) -> None:
        self.text_vec_layer_en.adapt(en_sentences)
        self.text_vec_layer_es.adapt([f"{self.sos} {s} {self.eos}" for s in es_sentences])
        self.adapted = True
        
    def en_vocabulary(self):
        return self.text_vec_layer_en.get_vocabulary()
    
    def es_vocabulary(self):
        return self.text_vec_layer_es.get_vocabulary()
        
    def vectorize_en(self, en_sentences: list[str]):
        return self.text_vec_layer_en(en_sentences)
    
    def vectorize_es(self, es_sentences: list[str]):
        return self.text_vec_layer_es(es_sentences)
    
    @classmethod
    def from_config(cls, config):
        return cls(**config)
        
    def get_config(self):
        en_config = self.text_vec_layer_en.get_config()
        en_config['vocabulary'] = self.en_vocabulary()
        es_config = self.text_vec_layer_es.get_config()
        es_config['vocabulary'] = self.es_vocabulary()
        return {'en_config': en_config,
                'es_config': es_config}
    
    def save(self, filepath: str):
        if not self.adapted:
            raise RuntimeError("Layer hasn't been adapted yet.")
        if filepath is None:
            raise ValueError("A file path needs to be defined.")
        if not filepath.endswith('.pkl'):
            raise ValueError("The file path needs to end in .pkl.")
        pickle.dump({
            'config': self.get_config()
        }, open(filepath, 'wb'))
    
    @classmethod    
    def load(cls, filepath: str):
        conf = pickle.load(open(filepath, 'rb'))
        instance = cls(**conf['config'])
        return instance
```

이 모듈이 하는 일을 설명해 드리겠습니다:
   - 원본 텍스트와 대상 텍스트 모두 벡터화해야 하기 때문에, 이 모듈에는 두 TextVectorization 레이어가 포함되어 있습니다.
   - 적응 후에는 이 모듈이 원본 및 대상 텍스트의 어휘를 보유합니다. 이렇게 하면, 프로덕션 시스템에 배포할 때 TextVectorization을 다시 적응시킬 필요가 없습니다.
   - 이 모듈은 영속성을 제공하기 위해 pickle 모듈을 사용합니다. get_config 메서드를 사용하여 두 TextVectorization 레이어의 구성을 가져와 저장할 수 있습니다. 또한 from_config를 사용하여 저장된 구성에서 모듈의 인스턴스를 직접 초기화할 수 있습니다.
   - 그러나 get_config 메서드를 사용할 때 어휘가 검색되지 않았습니다 (현재 Keras 버전 3.3을 사용하고 있으며 이것이 버그인지 확실하지 않습니다), 따라서 어휘를 따로 가져오기 위해 get_vocabulary 메서드를 사용해야 했습니다.

<div class="content-ad"></div>

```js
텍스트 전처리기 = TextPreprocessor()
텍스트_전처리기.적응(영어_문장, 스페인어_문장)
텍스트_전처리기.저장('./데이터/텍스트_전처리기.pkl')
```

두 언어의 어휘를 확인해보세요:

```js
In:   텍스트_전처리기.en_vocabulary()[:10]
Out:  ['', '[UNK]', 'i', 'the', 'to', 'you', 'tom', 'a', 'is', 'he']

In:   텍스트_전처리기.es_vocabulary()[:10]
Out:  ['', '[UNK]', 'startofseq', 'endofseq', 'de', 'que', 'no', 'tom', 'a', 'la']
```

<div class="content-ad"></div>

문제없어요.

TextPreprocessor 모듈이 준비되면 훈련 및 검증 세트를 분할하고 벡터화 작업을 시작할 수 있어요. 대상 텍스트는 디코더 모듈의 입력으로도 작동하기 때문에 X_train_dec 및 X_valid_dec와 같은 두 가지 추가 기능 세트가 있어요:

```js
X_train = text_preprocessor.vectorize_en(sentence_en[:100_000])
X_valid = text_preprocessor.vectorize_en(sentence_en[100_000:])

X_train_dec = text_preprocessor.vectorize_es([f"{Configure.SOS} {s}" for s in sentence_es[:100_000]])
X_valid_dec = text_preprocessor.vectorize_es([f"{Configure.SOS} {s}" for s in sentence_es[100_000:]])

y_train = text_preprocessor.vectorize_es([f"{s} {Configure.EOS}" for s in sentence_es[:100_000]])
y_valid = text_preprocessor.vectorize_es([f"{s} {Configure.EOS}" for s in sentence_es[100_000:]])
```

## 인코더-디코더 모델 구현

<div class="content-ad"></div>

이전 아키텍처 다이어그램에서 설명했듯이 전체 모델은 인코더 및 디코더 부분으로 나뉩니다. 따라서 각 부분에 대해 keras.layers.Layer를 기반으로 두 개의 사용자 정의 하위 클래스를 구현합니다.

각 사용자 정의 Layer에 대해 __init__, call 및 get_config 메서드를 구현하는 것이 중요합니다.

- __init__ 메서드는 Layer의 멤버 변수, 가중치 및 하위 레이어를 초기화합니다.
- call 메서드는 Keras의 Functional API와 유사하게 작동하며 입력을 매개변수로 받아 처리 후 Layer의 출력을 반환합니다.
- get_config 메서드는 모델을 저장할 때 Layer의 구성을 검색하는 데 사용됩니다.

인코더 Layer:

<div class="content-ad"></div>

```js
@keras.saving.register_keras_serializable()
class Encoder(keras.layers.Layer):
    def __init__(self, embed_size: int = 128, **kwargs):
        super().__init__(**kwargs)
        self.embed_size = embed_size
        
        self.encoder_embedding_layer = layers.Embedding(input_dim=Configure.VOCAB_SIZE, 
                                                        output_dim=self.embed_size,
                                                        mask_zero=True)
        self.encoder = layers.LSTM(512, return_state=True)
        
    def call(self, inputs):
        encoder_embeddings = self.encoder_embedding_layer(inputs)
        encoder_outputs, *encoder_state = self.encoder(encoder_embeddings)
        return encoder_outputs, encoder_state
    
    def get_config(self):
        config = {"embed_size": self.embed_size}
        base_config = super().get_config()
        return config | base_config
```

인코더에서는 LSTM의 return_state 매개변수를 True로 설정했습니다. 이렇게 하면 LSTM의 최종 상태를 디코더 레이어가 사용할 수 있도록 출력으로 반환합니다.

디코더 레이어:

```js
@keras.saving.register_keras_serializable()
class Decoder(keras.layers.Layer):
    def __init__(self, embed_size: int = 128, **kwargs):
        super().__init__(**kwargs)
        self.embed_size = embed_size
        
        self.decoder_embedding_layer = layers.Embedding(input_dim=Configure.VOCAB_SIZE,
                                                        output_dim=self.embed_size,
                                                        mask_zero=True)
        self.decoder = layers.LSTM(512, return_sequences=True)
        
    def call(self, inputs, initial_state=None):
        decoder_embeddings = self.decoder_embedding_layer(inputs)
        decoder_outputs = self.decoder(decoder_embeddings,
                                       initial_state=initial_state)
        return decoder_outputs
    
    def get_config(self):
        config = {"embed_size": self.embed_size}
        base_config = super().get_config()
        return config | base_config
```

<div class="content-ad"></div>

Decoder에서 데이터 입력을받는 것 외에도 call 메서드는 초기 상태 함수를 통해 Encoder의 입력도 받아 모듈의 출력을 반환합니다.

또한 keras.layers.Layer와 유사하게 __init__, call 및 get_config 메서드를 구현해야 하는 사용자 정의 모델인 NMTModel을 구현합니다.

```python
@keras.saving.register_keras_serializable()
class NMTModel(keras.models.Model):
    embed_size: int = 128
    
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        
        self.encoder = Encoder(self.embed_size)
        self.decoder = Decoder(self.embed_size)
        
        self.out = layers.Dense(Configure.VOCAB_SIZE, activation='softmax')
        
    def call(self, inputs):
        encoder_inputs, decoder_inputs = inputs
        
        encoder_outputs, encoder_state = self.encoder(encoder_inputs)
        decoder_outputs = self.decoder(decoder_inputs, initial_state=encoder_state)
        out_proba = self.out(decoder_outputs)
        return out_proba
    
    def get_config(self):
        base_config = super().get_config()
        return base_config
```

- Model에서는 Dense 레이어를 초기화하여 Decoder의 출력을 단어 벡터 결과로 변환합니다.
- call 메서드는 두 개의 입력을 가져와서 쉽게 구분할 수 있습니다.
- Layer 및 Model은 모델을 저장할 때 올바른 직렬화를 보장하기 위해 @keras.saving.register_keras_serializable() 데코레이터를 가져야 합니다.

<div class="content-ad"></div>

## 모델 훈련

모델을 정의한 후, 훈련 단계로 진행합니다:

```js
nmt_model = NMTModel()
nmt_model.compile(loss='sparse_categorical_crossentropy',
                  optimizer='nadam',
                  metrics=['accuracy'])
checkpoint = keras.callbacks.ModelCheckpoint(
    './data/nmt_model.keras',
    monitor='val_accuracy',
    save_best_only=True
)
nmt_model.fit((X_train, X_train_dec), y_train, epochs=1,
              validation_data=((X_valid, X_valid_dec), y_valid),
              batch_size=128,
              callbacks=[checkpoint])
```

이 코드 부분에서:

<div class="content-ad"></div>

- 먼저 모델 인스턴스를 컴파일하는 compile 메소드를 호출합니다. 여기서는 손실(loss), 옵티마이저(optimizer), 및 메트릭(metrics) 등을 정의합니다.
- ModelCheckpoint 콜백을 설정하여 훈련 후 최고의 val_accuracy를 가진 모델을 저장합니다.
- fit 메소드를 사용하여 X_train 및 X_train_dec를 x 매개변수에 튜플로 전달하고 validation_data도 유사하게 처리합니다.
- 이것은 따뜻한 모델이라 epochs를 1로 설정했습니다. epochs 및 batch_size 값을 필요에 따라 조정할 수 있습니다.
- Keras 3.0은 Pytorch의 DataLoader도 지원하며 keras.utils.PyDataset을 기반으로 한 backend에 대한 이식 가능한 전처리 파이프라인을 구현할 수도 있습니다. 다음 기사에서 이들을 사용하는 방법을 설명할 수 있어요.

훈련이 완료되면 모델을 저장해야 합니다.

## 추론 작업

훈련 후, 저장된 어휘 및 모델과 함께 해당 코드 모듈을 제품 시스템으로 추론 작업을 위해 배포할 수 있습니다.

<div class="content-ad"></div>

모델의 Dense 레이어가 어휘 사전의 각 단어 벡터에 대한 확률을 출력하기 때문에 추론된 각 단어를 이전 결과와 병합하여 원본 텍스트와 함께 다음 단어를 예측하기 위해 다시 입력해야 합니다:

```js
preprocessor = TextPreprocessor.load('./data/text_preprocessor.pkl')
nmt_model = keras.saving.load_model('./data/nmt_model.keras')

def translate(sentence_en):
    translation = ""
    for word_index in range(50):
        X = preprocessor.vectorize_en([sentence_en])
        X_dec = preprocessor.vectorize_es([Configure.SOS + " " + translation])
        y_proba = nmt_model.predict((X, X_dec), verbose=0)[0, word_index]
        predicted_word_id = np.argmax(y_proba)
        predicted_word = preprocessor.es_vocabulary()[predicted_word_id]
        if predicted_word == Configure.EOS:
            break
        translation = translation + " " + predicted_word
    return translation.strip()
```

간단한 테스트 결과를 확인하기 위해 다음 메소드를 작성해 봅시다:

```js
In:   translate("It was pretty cool.")
Out:  'era bastante [UNK]'
```

<div class="content-ad"></div>

비록 매우 정확하지는 않지만, 이 글의 목표는 Keras 3.0 서브클래싱 API를 사용하는 방법을 배우는 것이므로 여전히 이 모델을 최적화할 여지가 많습니다, 맞죠?

# 결론

Keras 3.0의 출시로 Keras의 간결한 API를 사용하여 Pytorch 또는 Jax를 백엔드로 사용하면서 효율적으로 모델을 구현할 수 있게 되었습니다.

그러나 최신 버전이 출시된 지 얼마 되지 않아 도움이 되는 문서가 아직 완벽하지 않으므로 새로운 버전을 시도할 때 어려움을 겪을 수도 있습니다.

<div class="content-ad"></div>

이 기사는 끝에서 끝으로 실용적인 예제를 통해 Keras 3.0의 환경 설정과 기본 개발 프로세스를 설명하여 빠르게 시작할 수 있도록 도와줍니다.

안타깝게도, Keras 3.0 프로젝트는 아직 초기 단계에 있어 TensorFlow에 대한 의존성을 완전히 벗어날 수 없으며 TensorFlow의 일부 이해할 수 없는 문제도 있습니다.

하지만 저는 여전히 이 버전에 낙관적입니다. 시간이 지나면서 다중 백엔드 지원이 개선되면서 Keras가 살아나고, 딥러닝 기술을 더 쉽게 이용할 수 있게 하고, 딥러닝의 학습 곡선을 줄일 수 있는데 도움을 줄 것이라고 믿습니다.

Keras 3.0에 대해 더 알고 싶은 사항이 있으시면 자유롭게 댓글을 남기고 토론해 주세요.

<div class="content-ad"></div>

이 글을 즐겨 읽으셨나요? 더 많은 최신 데이터 과학 팁을 받으시려면 지금 구독해주세요! 피드백과 질문은 언제나 환영합니다 — 댓글에서 함께 이야기해요!

본 기사는 Data Leads Future에서 원문으로 게시되었습니다.

좋은 소식! Scikit-learn은 이제 Display 클래스를 제공하여 from_estimator와 from_predictions와 같은 메서드를 사용할 수 있게 해주어 다양한 상황에 맞는 그래프를 그리는 것이 훨씬 쉬워졌어요: