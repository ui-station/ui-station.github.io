---
title: "고객 이탈 예측"
description: ""
coverImage: "/assets/img/2024-05-18-CUSTOMERCHURNPREDICTION_0.png"
date: 2024-05-18 19:26
ogImage:
  url: /assets/img/2024-05-18-CUSTOMERCHURNPREDICTION_0.png
tag: Tech
originalTitle: "CUSTOMER CHURN PREDICTION"
link: "https://medium.com/@agrawalpalak308/customer-churn-prediction-aa81fb86390d"
---

이것은 이진 분류 문제이며, 은행 데이터 세트를 사용했습니다. 고객이 은행을 떠날 때에 대한 정보가 포함되어 있으며, 이를 사용하여 미래에 은행을 떠날 가능성이 있는 고객을 예측해야 합니다.

![이미지](/assets/img/2024-05-18-CUSTOMERCHURNPREDICTION_0.png)

우리는 인공 신경망을 구축할 것입니다. 이러한 문제에 접근하는 단계는 다음과 같습니다 —

- 특정 라이브러리 가져오기
- 데이터 세트 로드, 데이터 세트에 대한 가능한 정보 찾기(예: 데이터 세트에 결측값이 있는지, 중복된 값의 존재 여부)

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

<img src="/assets/img/2024-05-18-CUSTOMERCHURNPREDICTION_1.png" />

- 고객이 나간 수를 확인하기 위해 동일한 것을 나타내는 이 코드를 사용했습니다.

<img src="/assets/img/2024-05-18-CUSTOMERCHURNPREDICTION_2.png" />

- 이제 데이터 세트를 분석하고 ('RowNumber', 'CustomerId', 'Surname')와 같은 열이 예측에 큰 영향을 미치지 않으므로 삭제할 수 있습니다.

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

![데이터1](/assets/img/2024-05-18-CUSTOMERCHURNPREDICTION_3.png)

- 추가로, ONE HOT ENCODING을 사용하여 범주형 값들을 변환하겠습니다. get_dummies() 및 (drop_first=True)를 사용하면 지리와 성별에서 다른 하나를 삭제할 수 있습니다(예: 프랑스 및 여성).

![데이터2](/assets/img/2024-05-18-CUSTOMERCHURNPREDICTION_4.png)

- 모델을 훈련 및 테스트 데이터셋으로 분할합니다.
- 이제 값들을 스케일링할 것입니다. 'balance'와 'estimated_salary'의 값이 매우 크기 때문에 발생하는 문제를 방지하기 위해 StandardScaler()를 사용합니다.

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

<img src="/assets/img/2024-05-18-CUSTOMERCHURNPREDICTION_5.png" />

- 케라스 라이브러리를 사용하여 순차적 모델에 대한 'model' 객체를 만듭니다.
- 그런 다음 레이어(은닉, 출력)를 추가합니다.
- 시그모이드 활성화 함수를 사용하고, 입력이 11(탈퇴 제외)인 3개 노드를 갖는 밀집 은닉 레이어를 추가합니다.
- 출력 레이어를 추가합니다.
- summary를 확인하면 매개변수(가중치 + 편향)를 제공합니다.

<img src="/assets/img/2024-05-18-CUSTOMERCHURNPREDICTION_6.png" />

- 이제 모델을 컴파일해야 합니다. 어떤 손실 함수, 옵티마이저를 사용할 것인지 지정해야 합니다. 바이너리 분류 문제이므로 사용되는 손실 함수는 크로스 엔트로피/로그 손실입니다. 다양한 옵티마이저(경사 하강법, 확률적 경사 하강법, RMSprop 등)를 사용할 수 있지만 아담(적응 모멘트 추정)이 잘 작동합니다.
- 모델을 적합하고 10회 반복(에포크)하며 'history'라는 딕셔너리에 저장합니다. Validation_split은 모델을 훈련하는 지점을 처리하는데 사용됩니다. 예를 들어 8000개의 항목이있는 경우 이를 나누고 2000개의 항목을 제거하며 실행 중에 2000개의 포인트를 동시에 확인하고 정확성을 알려줍니다.

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

![Customer Churn Prediction 7](/assets/img/2024-05-18-CUSTOMERCHURNPREDICTION_7.png)

- 이제 배열 안에 가중치(weights)와 편향(biases)을 얻을 수 있습니다.

![Customer Churn Prediction 8](/assets/img/2024-05-18-CUSTOMERCHURNPREDICTION_8.png)

- 이제 예측이 나오게 됩니다. 시그모이드 함수를 사용하기 때문에 출력은 (0-1)의 범위에 있을 것입니다, 확률입니다. 우리는 이 확률을 0 또는 1로 변환해야 합니다, 그러기 위해 임계값을 정해야 합니다 (예를 들면 0.5, 만약 확률이 0.5보다 작으면 고객이 은행을 떠나지 않고, 확률이 0.5보다 크면 그들은 은행을 떠날 것입니다.) 임계값은 일반적으로 도표를 통해 결정되지만, 여기서는 추측하고, 0.5로 설정되어 있습니다.
- 그러면 모델의 정확도 점수를 찾을 준비가 됩니다.

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

<img src="/assets/img/2024-05-18-CUSTOMERCHURNPREDICTION_9.png" />

- 또한 matplotlib을 사용하여 그래프를 그릴 수 있습니다.

## 참고 — 정확도를 높이기 위해 다음을 증가시킬 수 있습니다:

- epoch의 수를 증가시킴.
- 은닉층의 활성화 함수를 relu로 설정.
- 은닉층의 노드 수를 증가시킴.
- 또는 은닉층의 수를 증가시킴(과적합이 발생할 수 있으므로 적당히).
