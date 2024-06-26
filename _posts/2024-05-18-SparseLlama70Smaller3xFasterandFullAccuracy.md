---
title: "희소 라마 70 더 작고, 3배 빠르며, 완벽한 정확도"
description: ""
coverImage: "/assets/img/2024-05-18-SparseLlama70Smaller3xFasterandFullAccuracy_0.png"
date: 2024-05-18 20:23
ogImage:
  url: /assets/img/2024-05-18-SparseLlama70Smaller3xFasterandFullAccuracy_0.png
tag: Tech
originalTitle: "Sparse Llama: 70% Smaller, 3x Faster, and Full Accuracy"
link: "https://medium.com/@bnjmn_marie/sparse-llama-70-smaller-3x-faster-and-full-accuracy-5e76c4bdb761"
---

Cerebras와 Neural Magic은 가지치기 기술과 희소 사전 훈련을 결합하여, 정확도를 희생하지 않고 매개변수를 최대 70%까지 줄일 수 있었다.

예를 들어, Llama 2를 50-70%로 희소화하여 어려운 하위 작업의 정확도를 유지하면서도 성공적으로 수행되었다. Neural Magic의 DeepSparse 엔진은 밀집 모델 대비 최대 3배 더 빠른 추론을 제공한다.

깊은 학습의 희소화는 계산 및 메모리 비용을 줄이는 데 목적이 있다. 가지치기가 컴퓨터 비전 모델의 크기를 효과적으로 줄였지만, LLMs에 대해서는 유사한 결과를 내지 못했다. LLMs는 매개변수가 많고, 가지치기는 매개변수 사이의 섬세한 균형을 방해할 수 있어서, 채팅 및 코딩과 같은 작업에서 큰 정확도 손실을 야기할 수 있다. 이러한 복잡성으로 인해, 중요한 LLMs는 희소성을 거의 활용하지 않는다.

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

Llama 모델을 70%로 희소화하는 것은 인상적이지만, 모델의 정확도를 유지하기 위한 프로세스는 매우 복잡합니다. 새 데이터셋에 대해 후속 사전 훈련을 수행해야 합니다.

![이미지](/assets/img/2024-05-18-SparseLlama70Smaller3xFasterandFullAccuracy_1.png)

그 결과, LLM을 희소화하는 것은 비용이 많이 듭니다. 이미 희소화된 모델을 저장하는 모델 동물원을 가지고 있습니다. 이것이 Llama 3와 같은 보다 최근의 LLM이 아직 희소 모델로 제공되지 않는 이유입니다. 변환이 시간이 걸립니다.

희소 모델로 효율적 추론을 하기 위해, 그들은 vLLM을 수정했습니다:

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

- GitHub: neuralmagic/nm-vllm

그들은 자신들의 방법을 설명한 기술 보고서를 발표했습니다:

효율적인 사전 학습 및 배포로 높은 희소성 LLama 모델 활성화

내 작업을 지원하려면, 최신 AI 발전에 대한 더 많은 기사/튜토리얼을 보려면 내 뉴스레터를 구독해 주세요.
