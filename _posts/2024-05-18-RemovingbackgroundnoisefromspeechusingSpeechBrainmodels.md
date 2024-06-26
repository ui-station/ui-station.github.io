---
title: "스피치브레인 모델을 사용하여 음성에서 배경 소음 제거하기"
description: ""
coverImage: "/assets/img/2024-05-18-RemovingbackgroundnoisefromspeechusingSpeechBrainmodels_0.png"
date: 2024-05-18 20:36
ogImage:
  url: /assets/img/2024-05-18-RemovingbackgroundnoisefromspeechusingSpeechBrainmodels_0.png
tag: Tech
originalTitle: "Removing background noise from speech using SpeechBrain models"
link: "https://medium.com/@jaimonjk/removing-background-noise-from-speech-using-speechbrain-models-e5546d103355"
---

SpeechBrain은 음성 처리를 위해 설계된 오픈 소스, 올인원 툴킷입니다. PyTorch를 기반으로 구축되었으며 음성 인식, 화자 식별 및 음성 향상을 포함한 다양한 음성 관련 작업을 위한 포괄적인 도구 모음을 제공합니다. 모듈식이며 유연한 구조로 연구자와 개발자 모두에게 접근하기 쉽도록 설계되어 최신 음성 처리 모델로의 신속한 개발과 실험을 가능하게 합니다.

SpeechBrain은 2021년에 음성 기술 분야를 발전시키고자 열정을 가진 연구자와 엔지니어들에 의해 세상에 소개되었습니다. 이 프로젝트는 오픈 소스의 성격과 견고한 성능으로 빠르게 주목을 받았습니다. 몇 년 동안, SpeechBrain은 전 세계 AI 커뮤니티로부터 다수의 기여를 받아 음성 처리의 최신 기술 발전을 지속적으로 통합해왔습니다.

SpeechBrain은 매우 모듈식으로, 사용자가 자신의 요구에 따라 툴킷을 쉽게 사용자 정의하고 확장할 수 있습니다. 데이터 전처리부터 모델 훈련 및 평가까지 음성 처리 파이프라인의 각 구성 요소는 독립적으로 수정하거나 대체할 수 있습니다. 이 툴킷은 특정 작업에 대해 즉시 사용하거나 미세 조정할 수 있는 다양한 사전 훈련된 모델을 제공합니다. 이러한 모델은 최신 아키텍처에 기반하고 대규모 데이터셋에서 훈련되어 높은 성능을 보장합니다.

# 음성 분리를 위해 SpeechBrain 사용하기

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

Jupyter Notebook에 SpeechBrain을 설치하는 방법부터 시작하겠습니다.

```js
!pip install speechbrain
```

그런 다음 필요한 라이브러리를 가져올 것입니다.

```js
from speechbrain.inference.separation import SepformerSeparation as separator
import torchaudio
from IPython.display import Audio
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

이제 HuggingFace에서 모델을 다운로드할 것입니다. 우리는 SpeechBrain으로 구현된 sepformer-wham16k-enhancement 모델을 사용할 예정입니다.

```js
model = separator.from_hparams(
  (source = "speechbrain/sepformer-wham16k-enhancement"),
  (savedir = "pretrained_models/sepformer-wham16k-enhancement")
);
```

이제 모델을 사용하여 오디오에서 배경 소음을 분리할 준비가 끝났습니다.

```js
audio_sources = model.separate_file((path = "original_audio.wav"));
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

모델은 텐서 출력을 생성합니다. 우리는 torchaudio를 사용하여 텐서를 오디오 파일로 변환할 수 있습니다.

```python
torchaudio.save("converted_audio.wav", audio_sources[:, :, 0], 16000)
```

# 샘플 오디오 파일로 테스트하기

SpeechBrain의 효과를 테스트하기 위해 Wikimedia에서 제공하는 두 개의 샘플 오디오 파일을 사용할 것입니다: 배경 소음이 많이 섞인 파일과 배경 소음이 적은 파일입니다. 이 오디오를 SpeechBrain을 사용하여 처리하고 결과를 비교할 것입니다.

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

## 샘플 오디오 1: 가벼운 백그라운드 소음

오디오 원본:

이 오디오 파일의 출처 페이지입니다. 우리는 처음 10초를 사용하고 있습니다.

처리된 오디오:

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

## 샘플 오디오 2: 강한 백그라운드 소음

원본 오디오:

이 오디오 파일의 출처 페이지입니다. 처음 10초를 사용하고 있습니다.

처리된 오디오:

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

# 발견 사항

SpeechBrain의 소음 제거 능력을 평가한 결과가 복합적이었습니다. 낮은 배경 소음이 포함된 샘플에 모델을 적용했을 때, 모델은 만족스럽게 작동하여 소음을 효과적으로 줄이고 음성의 선명도를 향상시켰습니다. 이는 SpeechBrain이 적당한 수준의 소음을 다루는 데 강점을 가지고 있으며, 일상적인 오디오 개선 작업에 유용한 도구임을 보여줍니다.

그러나, 고 배경 소음이 포함된 샘플에서는 모델이 어려움을 겪었습니다. 일부 소음을 줄이기는 했지만, 출력물은 오디오 클리핑으로 인해 음성의 이해를 저해하는 문제가 있었습니다. 이는 SpeechBrain의 현재 소음 제거 모델이 극도로 시끄러운 배경 환경에 적합하지 않을 수 있다는 것을 나타냅니다.

# 이상적인 사용 사례

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

이러한 결과를 고려해봤을 때, SpeechBrain은 다음과 같은 경우에 적합할 것으로 생각됩니다:

- 팟캐스트 및 보이스오버: 비교적 조용한 환경에서 녹음하는 콘텐츠 크리에이터들에게, SpeechBrain은 오디오를 깔끔하게 정리하여 전문적인 음질을 보장할 수 있습니다.
- 원격 회의 및 통화: 주변 소음이 적당한 전문적인 환경에서는 SpeechBrain이 발화의 명확성을 향상시킬 수 있어서 참여자들이 방해 없이 서로 이해하기 쉬워집니다.
- 교육 컨텐츠: 교육자 및 온라인 강좌 제작자들은 SpeechBrain을 사용하여 녹화한 콘텐츠를 깔끔하게 정리함으로써 강의와 발표가 명확하고 이해하기 쉽도록 할 수 있습니다.

위의 테스트와 결과에 대해 어떻게 생각하시는지 아래에 알려주세요.

모두를 위한 오픈 소스 대화형 AI. SpeechBrain. (출처: https://speechbrain.github.io/)

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

Speechbrain/Sepformer-WHAM16K-enhancement · Hugging Face. speechbrain/sepformer-wham16k-enhancement · Hugging Face. (n.d.). [https://huggingface.co/speechbrain/sepformer-wham16k-enhancement](https://huggingface.co/speechbrain/sepformer-wham16k-enhancement)
