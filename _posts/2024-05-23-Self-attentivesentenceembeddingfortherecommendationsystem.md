---
title: "자기 주의적 문장 임베딩을 사용한 추천 시스템"
description: ""
coverImage: "/assets/img/2024-05-23-Self-attentivesentenceembeddingfortherecommendationsystem_0.png"
date: 2024-05-23 17:13
ogImage:
  url: /assets/img/2024-05-23-Self-attentivesentenceembeddingfortherecommendationsystem_0.png
tag: Tech
originalTitle: "Self-attentive sentence embedding for the recommendation system"
link: "https://medium.com/towards-data-science/self-attentive-sentence-embedding-for-the-recommendation-system-fc8af5817035"
---

![Self-attentive sentence embedding](/assets/img/2024-05-23-Self-attentivesentenceembeddingfortherecommendationsystem_0.png)

# 소개

트랜스포머 레이어와 그의 어텐션 메커니즘은 자연어처리(NLP) 커뮤니티에서 가장 중요한 아이디어 중 하나입니다. 최근 세계를 휩쓴 ChatGPT와 LLaMA와 같은 대규모 언어 모델에서 핵심 역할을 합니다.

하지만 NLP 커뮤니티에서 시작된 다른 흥미로운 아이디어가 있는데, 그 영향은 주로 추천 시스템 분야에서 실현됩니다. 바로 자기주의적 문장 임베딩(self-attentive sentence embedding)입니다. 이 기사에서는 자기주의적 문장 임베딩[1]과 추천 시스템에 적용하는 방법을 살펴볼 것입니다.

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

# 작동 방식

## 전체 아이디어

이 논문의 주된 아이디어는 문장을 여러 임베딩으로 인코딩하여 문장의 다양한 측면을 포착할 수 있는 더 나은 방법을 찾는 것입니다. 구체적으로, 저자들은 문장을 단일 임베딩으로 인코딩하는 대신 각 행 임베딩이 문장의 다른 측면을 포착하는 2D 행렬로 인코딩하고자 합니다.

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

문장 임베딩을 얻으면, 문장 분석, 작가 프로파일링, 텍스트 함의 등 다양한 하위 작업에 사용할 수 있습니다.

## 모델 아키텍처

모델 입력은 문장 배치입니다. 각 문장은 n개의 토큰을 가지고 있습니다. 우리는 i번째 문장을 다음과 같이 표현할 수 있습니다:

![image](/assets/img/2024-05-23-Self-attentivesentenceembeddingfortherecommendationsystem_2.png)

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

d는 표현의 숨겨진 차원을 나타내며, 우리는 문장 s를 n by d 행렬 H로 인코딩할 수 있습니다:

![image](/assets/img/2024-05-23-Self-attentivesentenceembeddingfortherecommendationsystem_3.png)

여기서 F는 문장의 토큰을 임베딩으로 인코딩하는 모델 함수를 나타냅니다. 논문에서는 단어 임베딩(Word2Vec을 사용하여 초기화)을 이용하여 토큰을 인코딩하고 이를 양방향 LSTM을 통해 전달합니다. 토큰을 임베딩으로 인코딩하는 다양한 방법이 있기 때문에 일반화를 위해 여기서 F를 사용했습니다.

다음으로, 그들은 임베딩 H를 입력으로 사용하여 어텐션 가중치 행렬 A를 학습합니다:

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

![Image #1](/assets/img/2024-05-23-Self-attentivesentenceembeddingfortherecommendationsystem_4.png)

Here, the softmax() is applied to the second dimension of its input. We can view the formula as a 2-layer MLP without bias.

As we can see from the above formula, the attention weight A matrix will have a shape of r by n where r is the number of aspects a sentence can have and n is the sentence length. The authors argue that there are many aspects that make up the semantics of a sentence. Thus, they need r embeddings to focus on different parts of the sentence. In other words, each embedding in A is the sentence attention weight:

![Image #2](/assets/img/2024-05-23-Self-attentivesentenceembeddingfortherecommendationsystem_5.png)

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

Transformer처럼 이 행렬 A의 시각화를 통해 문장에 대한 각 측면의 주의를 더 잘 이해할 수 있습니다.

마지막으로, 우리는 H와 A를 곱하여 r by d 행렬 M을 얻음으로써 문장 임베딩을 생성합니다:

![image](/assets/img/2024-05-23-Self-attentivesentenceembeddingfortherecommendationsystem_6.png)

M의 각 행은 토큰 임베딩과 그 토큰에 대한 측면의 가중치의 가중 합입니다. 시각적으로는 이렇게 보입니다:

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

![image](/assets/img/2024-05-23-Self-attentivesentenceembeddingfortherecommendationsystem_7.png)

## Regularization

In the paper, they also introduce a new regularization term:

![image](/assets/img/2024-05-23-Self-attentivesentenceembeddingfortherecommendationsystem_8.png)

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

표를 마크다운 형식으로 변경해주세요.

F는 행렬의 프로베니우스 노름을 나타냅니다.

정규화 항은 2가지 목적을 제공합니다:

- 측면 임베딩이 겹칠 수 있기 때문에 다양성을 높입니다. 즉, 유사할 수 있음을 의미합니다.
- 각 관심사가 가능한 적은 토큰에 초점을 맞추도록 만듭니다.

이 글에서는 정규화가 중점이 아니기 때문에 정규화가 어떻게 작동하는지에 대해 더 읽어볼 수 있습니다.

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

# 추천 시스템에서의 다중 관심사

셀프 어텐티브 문장 임베딩이 어떻게 작동하는지 이해하면, 추천 시스템에서 어떻게 활용할 지에 대해 집중할 수 있습니다.

대규모 추천 시스템에서, 보통 두 개의 타워 모델 아키텍처를 사용합니다. 하나는 사용자 정보를 인코딩하고, 다른 하나는 후보 정보를 인코딩합니다. 사용자 타워에는 사용자의 과거 행동인 클릭, 좋아요, 공유 순서와 사용자 프로필을 사용합니다. 후보 타워에는 아이템 ID와 아이템 카테고리와 같은 후보 특징을 사용합니다.

사용자 임베딩과 후보 임베딩을 내적하여 후보 아이템이 사용자에게 얼마나 관련 있는지를 반영합니다. 레이블은 사용자 시퀀스에서 다음 상호작용할 아이템입니다. 따라서 모델 목표는 사용자가 다음에 상호작용할 아이템을 예측하는 것입니다.

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

![Self-attentive sentence embedding for the recommendation system](/assets/img/2024-05-23-Self-attentive-sentence-embedding-for-the-recommendation-system_9.png)

위 이미지에서 확인할 수 있듯이 사용자 타워의 출력은 모든 사용자 정보를 포함하는 임베딩입니다. 그러나 단일 사용자 임베딩은 모든 사용자의 다양한 관심사를 포착하는 데 좋지 않습니다. 따라서 더 나은 해결책은 사용자의 관심사를 여러 임베딩으로 인코딩하는 것입니다.

사용자의 다양한 관심사를 어떻게 포착할지에 대한 많은 연구가 이루어졌습니다. 가장 두드러지는 두 가지 방법은 self-attentive 임베딩(SA) [2]과 dynamic routing (DR) [3]입니다. 두 방법 모두 비슷한 성능을 보이지만, self-attentive 방법이 더 안정적이고 훈련 속도가 빠릅니다.

self-attentive 방법이 어떻게 작동하는지 이해하면 추천 시스템에 적용하는 것은 간단합니다. 입력으로 문장 토큰 대신에 유튜브에서 시청한 비디오 ID 목록이나 이커머스 플랫폼에서 클릭/주문한 상품 ID와 같은 사용자 행동을 사용합니다. 출력은 각 임베딩이 문장에서의 측면이 아니라 사용자 관심사를 인코딩합니다!

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

![Table 1](/assets/img/2024-05-23-Self-attentivesentenceembeddingfortherecommendationsystem_10.png)

In the ComiRec paper [2], the authors compare the self-attentive method with the dynamic routing method along with other popular models that produce a single user interest:

![Table 2](/assets/img/2024-05-23-Self-attentivesentenceembeddingfortherecommendationsystem_11.png)

As the table shows, the self-attentive method produces results comparable to those of the dynamic routing method. Still, both multi-interest embedding solutions are significantly better than their single-interest embedding counterparts.

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

# 마무리

여러 관심사를 포함한 임베딩을 사용하여 모델을 교육하고 제공하는 것에는 많은 미묘한 점이 있습니다. 이 기사에서는 자가 주목 방법이 작동하는 방법과 이를 추천 시스템에서 어떻게 사용하는지에 대해 안내했습니다. 이러한 모델을 교육하고 제공하는 데 대한 더 자세한 내용은 논문을 읽는 것만큼 좋은 자료는 없습니다. 이 기사는 추천 시스템을 위한 다중 관심 프레임워크를 이해하고자 하는 여정에서 참고할 만한 자료입니다.

# 참고

[1] Lin, Zhouhan, et al. “A structured self-attentive sentence embedding.” arXiv preprint arXiv:1703.03130 (2017).

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

## References

[1] Cen, Yukuo, et al. “Controllable multi-interest framework for recommendation.” Proceedings of the 26th ACM SIGKDD International Conference on Knowledge Discovery & Data Mining. 2020.

[2] Li, Chao, et al. “Multi-interest network with dynamic routing for recommendation at Tmall.” Proceedings of the 28th ACM international conference on information and knowledge management. 2019.
