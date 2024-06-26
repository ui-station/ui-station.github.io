---
title: "LLM이 정말 새로운 것을 배울 수 있을까요"
description: ""
coverImage: "/assets/img/2024-05-23-CanaLLMReallyLearnNewThings_0.png"
date: 2024-05-23 18:09
ogImage:
  url: /assets/img/2024-05-23-CanaLLMReallyLearnNewThings_0.png
tag: Tech
originalTitle: "Can a LLM Really Learn New Things"
link: "https://medium.com/gitconnected/can-a-llm-really-learn-new-things-d926b4502522"
---

## 인공지능|LLMS|미세 튜닝|

<img src="/assets/img/2024-05-23-CanaLLMReallyLearnNewThings_0.png" />

대형 언어 모델 (LLMs)은 방대한 텍스트 말뭉치에서 훈련되어 많은 사실적 지식을 습득합니다. 이러한 지식은 그들의 매개변수에 내재되어 필요할 때 사용될 수 있습니다. 이러한 모델의 지식은 훈련의 끝에 "결정"됩니다. 사전 훈련이 끝나면 모델은 사실상 학습을 멈춥니다.

그 후, 모델은 정렬되거나 지시 튜닝을 받아 이 지식을 최상으로 활용하고 사용자의 질문에 더 자연스럽게 응답하는 방법을 배울 수 있습니다. 때로는 모델의 지식이 충분하지 않을 수 있습니다. 왜냐하면 이 질문은 일반적이며 관심 영역에 맞게 맞춰져 있지 않을 수 있기 때문입니다. 모델은 RAG를 통해 외부 메모리에 접근할 수 있지만, 새로운 도메인에 모델을 적응시키는 데 미세 튜닝을 통해 장점을 얻을 수 있다고 여겨집니다. 일반적으로 이 미세 튜닝은 인간 주석자나 다른 LLM들이 작성한 입력을 사용하여 실시됩니다. 이 단계에서 모델은 추가적인 사실적 지식을 만나 매개변수에 통합합니다.

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

실제로, 기계적인 수준에서는 이러한 상호작용이 어떻게 일어나는지 정말로 알지 못합니다. 게다가 일부에 따르면, 이 새로운 지식에 노출되는 것이 모델이 환각을 경험하게 할 수도 있습니다. 이는 모델이 이전에 보유한 지식에 근거하지 않는 사실을 생성하도록 훈련되어 있기 때문입니다(또는 모델의 이전 지식과 충돌할 수도 있습니다). 게다가 앞서 본 것처럼 모델은 훈련 코퍼스에서 덜 빈번한 엔티티(예: 이전 코퍼스에서 덜 빈번한 엔티티)에 대해 어려움을 겪을 수 있습니다.

따라서, 최근에 발표된 연구는 모델이 새로운 지식을 통해 미세 조정되는 과정에서 무슨 일이 일어나는지 분석하는 데 관심을 가졌습니다.

저자들은 모델이 미세 조정을 거치고 새로운 지식을 습득한 후 반응이 어떻게 변하는지에 대해 자세히 조사했습니다.

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

먼저, 그들은 세밀한 조정 후 예시를 위한 지식 수준으로 분류하려고 노력했습니다. 새로운 예시는 모델의 지식과 일치하지 않을 수 있는 지식을 내재적으로 갖고 있습니다. 예시는 알려진 것일 수도 있고 알려지지 않은 것일 수도 있습니다. 알려진 경우에도 고도로 알려진 지식, 어느 정도 알려진 지식 또는 약하게 알려진 지식을 보유하고 있을 수 있습니다.

![이미지](/assets/img/2024-05-23-CanaLLMReallyLearnNewThings_2.png)

저자들은 이 데이터셋으로 모델 (PaLM 2-M) 을 세밀하게 조정하였습니다. 세밀한 조정을 위한 각 예시는 인과적 지식 (주어, 관계, 목적어)으로 구성되어 있습니다. 이를 통해 모델이 이 지식을 특정 질문 (예: "파리는 어디에 위치하나요?")과 실제 정답 (예: "프랑스")으로 질의할 수 있습니다. 즉, 그들은 모델에 새로운 지식을 제공하고 이러한 삼중체를 질문 (질문-응답 쌍)으로 재구성하여 지식을 검증합니다. 이들은 이 모든 예시를 위에서 논의한 범주로 나누고 그 결과를 평가했습니다.

저자들은 모델을 세밀하게 조정하고 환청을 테스트하기로 결정했습니다. 그들에게 알려지지 않은 사실의 높은 비율은 성능 저하를 초래한다고 판단하였으며 (이는 긴 세밀한 조정 시간으로 보상받을 수 없습니다).

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

<img src="/assets/img/2024-05-23-CanaLLMReallyLearnNewThings_3.png" />

사실, 여러 번 에포크로 훈련하는 것은 해로운 효과를 초래할 수 있습니다. 이전 연구에 따르면 더 많은 에포크는 성능 저하로 이어지는 것으로 나타났습니다 (과적합으로 이어질 수 있음). 저자들은 이 효과가 알려지지 않은 사실 비율이 높을수록 증가한다고 합니다.

저자들은 알려지지 않은 사실이 에포크 수가 적을 때 거의 중립적인 효과를 보이지만, 에포크 수가 많을 때 성능을 해친다고 합니다. 따라서, 알려지지 않은 예는 해로울 수 있지만, 그들의 부정적인 영향은 대부분 나중의 훈련 단계에서 실현됩니다. 저자들은 그런 예제들을 핏하는 과정을 연구합니다. 도표는 미세 조정 기간에 대한 데이터 집합 예제의 알려진 부분과 알려지지 않은 부분의 훈련 정확도를 나타냅니다. 모델이 알려지지 않은 예를 나중 단계에서 배운다는 것을 알 수 있습니다.

<img src="/assets/img/2024-05-23-CanaLLMReallyLearnNewThings_4.png" />

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

저자들은 정확도와 알려진 및 알려지지 않은 예제 간의 관골을 양적으로 측정할 수 있는지, 그리고 선형인지 의문을 제기합니다. 결과는 알려지지 않은 예제가 성능에 해를 끼치고, 알려진 예제는 그것을 향상시키는 강력한 선형 관련이 있다는 것을 보여줄 것입니다 (이 선형 회귀의 연관 계수는 거의 같습니다).

![이미지](/assets/img/2024-05-23-CanaLLMReallyLearnNewThings_5.png)

게다가, 이 미세 조정은 특정 케이스에서의 성능을 넘어서 모델 지식에 광범위한 영향을 미칩니다. 저자들은 Out-of-Distribution (OOD) 테스트 세트를 사용하여 알려지지 않은 예제가 OOD 성능에 해를 끼친다는 것을 보여줍니다. 저자들에 따르면 환각의 발생과도 관련이 있습니다.

재미있는 결과는 최상의 결과가 매우 잘 알려진 예제와 함께 얻어지는 것이 아니라 아마도 알려진 예제와 함께 얻어진다는 것입니다. 다시 말해, 이러한 예제들은 모델이 이전 지식을 더 잘 활용할 수 있게 합니다 (너무 잘 알려진 사실은 모델에 유용한 영향을 미치지 않습니다).

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

![Image 6](/assets/img/2024-05-23-CanaLLMReallyLearnNewThings_6.png)

그에 반해, 알려지지 않은 사실과 약하게 알려진 사실은 모델의 성능에 해를 끼치며, 이 하락은 환각 증가에서 유도된 것입니다.

따라서 저자들에 따르면, 이 알려지지 않은 지식은 성능에 해를 끼칠 수 있으며 (따라서 미세 조정은 거의 무용한 것으로 만듭니다). 이 알려지지 않은 지식을 "I don't know"로 표시하여 이 피해를 줄일 수 있다고 초기 결과에 따르면 추정됩니다.

![Image 7](/assets/img/2024-05-23-CanaLLMReallyLearnNewThings_7.png)

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

위의 글을 요약하면, 모델은 파인튜닝 중에 알 수 없는 지식을 받을 경우 손상될 수 있습니다. 게다가, 이 성능 하락은 환각증 증가와 관련이 있습니다. 반면, 알 수 있는 예시는 긍정적인 효과가 있을 수 있습니다. 따라서, 이는 모델이 새로운 지식을 통합하는 데 어려움을 겪는 것을 보여줍니다. 다시 말해, 모델이 배운 것과 새로운 지식을 어떻게 사용하는지 사이에 충돌이 있을 수 있습니다. 이는 조정 및 지시 파인튜닝과 관련이 있을 수 있습니다 (불행히도 이에 대한 연구는 이 연구에서 다루지 않았습니다).

한편, 특정 도메인 지식을 가진 모델을 사용하고 싶다면, 이 연구는 RAG를 사용하는 것이 좋다는 것을 제안합니다. 반면, "알 수 없음"으로 표시된 결과는 파인튜닝의 제약을 극복하기 위한 다른 전략이 있다는 것을 의미합니다.

알 수 있는 사실이 실제로 유익하다는 사실은 안심스럽습니다. 오늘날의 모델은 방대한 양의 텍스트로 훈련됩니다. 이는 많은 지식을 많이 보았다는 것을 의미합니다. 따라서 파인튜닝 데이터셋에 있는 사실 중 많은 것은 이미 모델의 알고 있는 요소일 수 있으며 모델에 해를 끼치지 않을 수 있습니다 (아마도 일부만 알 수 없을 것입니다).

어쨌든, 이 연구는 흥미로우며 파인튜닝과 새로운 지식과 기존 지식 간의 충돌이 어떻게 해소되는지에 대해 여전히 명확하지 않은 요소가 있다는 것을 보여줍니다. 이는 항상 파인튜닝 전후 모델의 결과를 테스트하는 이유 중 하나입니다.

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

## 어떻게 생각하세요? 포인트 파인튜닝 후 환각증이 증가했나요?

# 흥미로운 정보였다면:

다른 기사를 찾아볼 수 있고, LinkedIn에서 연락하거나 저에게 연락할 수 있습니다. 주간 업데이트되는 머신 러닝 및 인공 지능 뉴스를 포함하는 이 저장소를 확인해보세요. 협업과 프로젝트에 관심이 있고 LinkedIn에서 저에게 연락할 수 있습니다. 또한 새 이야기를 게시할 때 알림을 받기 위해 무료로 구독할 수도 있습니다.

여기 저의 GitHub 저장소 링크가 있습니다. 여기서 머신 러닝, 인공 지능 및 기타 관련 자료 및 코드를 모아두고 있습니다.

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

또는 최근에 작성한 제 논문 중 하나에 관심이 있을 수 있습니다:

# 참고 자료

본 문서를 작성하는 데 참고한 주요 참고 자료 목록을 아래에 제시합니다. 각 논문의 저자 이름만 인용하였습니다.

- 황, 2023, 대규모 언어 모델에서 환각에 대한 조사: 원칙, 분류, 도전과 공개 질문, 링크
- 레오가오, 2021, 행동 복제의 미적정성, 링크
- 게크만, 2024, 새로운 지식 기반에서 언어 모델 미세 조정이 환각을 유발하는가?, 링크
