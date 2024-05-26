---
title: "파이썬을 사용하여 쌓인 지역 효과 플롯ALE에 대해 깊이 파헤쳐 보기"
description: ""
coverImage: "/assets/img/2024-05-23-DeepDiveonAccumulatedLocalEffectPlotsALEswithPython_0.png"
date: 2024-05-23 15:34
ogImage:
  url: /assets/img/2024-05-23-DeepDiveonAccumulatedLocalEffectPlotsALEswithPython_0.png
tag: Tech
originalTitle: "Deep Dive on Accumulated Local Effect Plots (ALEs) with Python"
link: "https://medium.com/towards-data-science/deep-dive-on-accumulated-local-effect-plots-ales-with-python-0fc9698ed0ee"
---

고도로 상관된 기능들은 모델 해석에 혼란을 야기할 수 있습니다. 이러한 기능들은 많은 XAI 방법의 가정을 위반하며, 특성과 타겟 간의 관계를 이해하기 어렵게 만듭니다. 동시에 이러한 기능들을 제거하지 않고는 성능에 영향을 미치지 않으면서 제거할 수 없는 경우도 있습니다. 다중공선성이 있어도 명확한 해석을 제공할 수 있는 방법이 필요합니다. 다행히 ALE(Accumulated Local Effects)에 의존할 수 있습니다.

ALE은 전역 해석 방법입니다. PDP와 비슷하게 모델에 포착된 추세를 보여줍니다. 즉, 특성이 타겟 변수와 선형적, 비선형적 또는 상관 관계가 없는지를 보여줍니다. 그러나 이러한 추세를 식별하는 방법이 매우 다르다는 것을 알게 될 것입니다.

- ALE가 생성되는 방식에 대한 직관을 제공합니다.
- ALE을 생성하는 데 사용되는 알고리즘을 형식적으로 정의합니다.
- Alibi Explain 패키지를 사용하여 ALE을 적용합니다.

<div class="content-ad"></div>

타 도움말 비교했을 때 SHAP, LIME, ICE Plots, 그리고 프리드만의 H 통계치와 달리, ALE(Accumulated Local Effects)은 다중공선성에 강건한 해석을 제공합니다.
해당 주제에 대한 이 비디오도 즐길 수 있을 것입니다. 그리고 더 배우고 싶다면, XAI with Python과 같은 내 코스도 확인해보세요. 뉴스레터 가입 시 무료로 액세스할 수 있습니다.

# ALE 이해하기

우리는 전복 데이터셋을 사용하여 ALE이 어떻게 작용하는지 이해할 것입니다. 전복은 조개류의 한 종입니다. 우리는 조개 안의 반지 수를 예측하고 싶은데, 그때 조개 쉘 무게와 살 총량(살의 무게)과 같은 특징을 사용합니다. 이 데이터셋 내 모든 숫자형 특징에 대한 상관 관계 열지도를 보여주는 그림 1을 확인하세요. 우리는 상당히 상관관계가 높은 특징들을 다뤄야 합니다!

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-DeepDiveonAccumulatedLocalEffectPlotsALEswithPython_1.png" />

그림 2는 이러한 두 가지 기능에 대한 산포도를 제공합니다. – 셀과 껍질 무게. 이들이 0.9의 상관 값이 있는 이유를 볼 수 있습니다. 이제 빨간 색의 인스턴스를 고려해보세요. 이것은 0.2의 껍질 무게를 가지고 있으며, 이와 유사한 인스턴스들은 대략 0.2에서 0.5 정도의 껍질 무게를 가질 것입니다. 많은 XAI 방법들은 이를 고려하지 않을 것입니다.

- PDPs는 전체 범위의 껍질 무게를 샘플링할 것입니다.
- 순열 특성 중요도는 껍질 무게 값을 무작위로 섞을 것입니다.

다시 말해, 이러한 방법을 사용하면 발생하기 힘든 또는 불가능한 특성 쌍이 발생할 수 있습니다. 이 문제를 해결하는 핵심은 범위를 고려하는 것입니다.

<div class="content-ad"></div>

![Image](/assets/img/2024-05-23-DeepDiveonAccumulatedLocalEffectPlotsALEswithPython_2.png)

상관관계는 두 특성의 전체 범위를 사용하여 계산할 수 있습니다. 작은 간격 내의 인스턴스만을 살펴보면 상관관계는 무의미해집니다. Figure 3에서 이를 확인할 수 있습니다. 우리는 1.5에서 2.5 사이의 조개 무게를 갖는 인스턴스들만을 고려하여 붉은 색 인스턴스 주변의 간격을 만들었습니다. 오른쪽의 그래프에서 이 간격 내에서 상관관계가 명확하지 않음을 볼 수 있습니다. 더 작은 간격에서는 더 명확하지 않을 것입니다. ALEs는 이를 활용합니다.

![Image](/assets/img/2024-05-23-DeepDiveonAccumulatedLocalEffectPlotsALEswithPython_3.png)

상관관계를 피하기 위해 이 간격 내에서 조개 무게의 영향을 결정할 수 있습니다. 이를 위해 우리는 간격 내 모든 인스턴스에서 두 개의 샘플을 만듭니다. 이를 샘플 쌍이라고 부릅니다. Figure 4에서 볼 수 있듯이, 간격 내에서의 최소 및 최대 조개 무게 값으로 대체하여 이를 생성합니다. 다른 모든 특성 값은 동일하게 유지됩니다.

<div class="content-ad"></div>


![Image](/assets/img/2024-05-23-DeepDiveonAccumulatedLocalEffectPlotsALEswithPython_4.png)

다음 단계는 샘플 쌍의 두 샘플에 대한 블랙박스 모델 예측을 가져오는 것입니다. 그런 다음 최소 샘플(주황색)의 예측값을 최대 샘플(녹색)의 예측값에서 뺍니다. 이 작업을 모든 샘플 쌍에 대해 수행하고 평균을 계산합니다. 이는 이 간격 내에서 껍질 무게의 변화로 인한 예측에 대한 추정치를 제공합니다. 중요한 점은 살아있는 무게와의 상관관계가 이 추정치를 왜곡시키지 않는다는 것입니다.

![Image](/assets/img/2024-05-23-DeepDiveonAccumulatedLocalEffectPlotsALEswithPython_5.png)

좋아요, 이는 특정 간격 내에서의 효과를 제공합니다. 전반적인 추세를 얻으려면 기능 범위 내의 모든 연속적인 간격에 대해 이 작업을 수행하고 개별 효과를 더해야 합니다. 새로운 간격으로 이동할 때마다 효과를 누적 효과에 추가하고 점을 그립니다. 이를 수행하면 껍질 무게에 대한 ALE를 얻을 수 있습니다. 이제 이름의 유래를 확인할 수 있습니다. 간겭(지역) 내에서 기능 효과를 누적하고 있는 것입니다.


<div class="content-ad"></div>

ALEs를 바라보는 또 다른 방법은 적분 또는 적어도 적분을 근사하는 Riemann 합과 유사하다는 것입니다. 지역 효과는 함수의 변화율 또는 도함수입니다. 효과를 누적함으로써 우리는 블랙 박스 모델 곡선을 찾아갑니다. 간격이 작아질수록 우리는 진정한 곡선에 더 가까워집니다. 안타깝게도 ALE에 대해 우리는 간격을 무한히 작게 만들 수 없습니다.

![image](/assets/img/2024-05-23-DeepDiveonAccumulatedLocalEffectPlotsALEswithPython_6.png)

# 형식적인 알고리즘

ALE에 대한 수학적 공식이 있습니다. 이번에는 그걸 넘기겠습니다. 그래도 ALE 알고리즘을 좀 더 형식적으로 정의하는 것은 가치가 있습니다.

<div class="content-ad"></div>


![Deep Dive on Accumulated Local Effect Plots with Python](/assets/img/2024-05-23-DeepDiveonAccumulatedLocalEffectPlotsALEswithPython_7.png)

다양한 구현 방법은 이 알고리즘에 약간의 차이를 줄 수 있습니다. 예를 들어, 제 2 단계에서 간격을 정의하는 방식입니다. 아래 구현은 각 간격에 최소한의 피처가 포함되도록 정의합니다. 또한 각 간격의 너비가 일정한 것으로 정의할 수도 있습니다.

# Alibi를 사용한 ALE 적용

ALE을 적용하기 위해 alibi 패키지를 사용할 것입니다. 이 패키지는 다양한 XAI 방법을 제공합니다. 현재는 ALE 및 plot_ale 함수 (라인 8-9)에 관심이 있습니다. 이 패키지를 적용하고 플롯을 해석하는 방법을 살펴볼 것이며, 여러 ALE을 결합하고 간격 길이를 변경하는 방법에 대해 알아볼 것입니다.


<div class="content-ad"></div>

#데이터셋 및 모델

이전에 언급한 전복 데이터 세트에 이 방법을 적용할 것입니다. 데이터 세트를 로드하고 타겟을 선택합니다. 또한 몇 가지 특성 엔지니어링을 진행합니다. 먼저, 직경과 전체 무게를 특성 목록에서 제외합니다. 그 이유는 Figure 1에서 다른 특성들과의 상관 관계가 1임을 확인했기 때문입니다. 마지막으로 성별 특성에 대해 원-핫 인코딩을 생성합니다. 최종 특성 세트의 스냅샷을 Figure 5에서 확인할 수 있습니다.

```js
#데이터셋을 가져옵니다
data = pd.read_csv("../../data/abalone.data",
                  names=["sex","length","diameter","height","whole weight",
                         "shucked weight","viscera weight","shell weight","rings"])

y = data["rings"]
X = data[["sex", "length", "height", "shucked weight", "viscera weight", "shell weight"]]

# 더미 변수 생성
X['sex.M'] = [1 if s == 'M' else 0 for s in X['sex']]
X['sex.F'] = [1 if s == 'F' else 0 for s in X['sex']]
X['sex.I'] = [1 if s == 'I' else 0 for s in X['sex']]
X = X.drop('sex', axis=1)

X.head()
```

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-DeepDiveonAccumulatedLocalEffectPlotsALEswithPython_8.png" />

우리는 이러한 특성들을 사용하여 모델을 훈련시켜 반지의 개수를 예측합니다 (lines 2-3). 모델을 훈련할 때 특성 매트릭스를 numpy 배열로 변환합니다. ALEs를 생성할 때 경고 메시지가 표시되지 않도록 하기 위함입니다.

```js
# 모델 훈련
model = RandomForestRegressor()
model.fit(X.to_numpy(), y)
```

## ALE 그래프 그리기

<div class="content-ad"></div>

ALE 플롯을 생성하려면, 먼저 ale 객체를 생성해야 합니다 (2번째 줄). 이를 위해 모델의 예측 함수 (model.predict), 피처 이름 및 타겟 이름을 전달합니다. 그런 다음 이 객체를 사용하여 X 피처 매트릭스에 대한 설명 (exp)을 생성합니다 (3번째 줄). 설명 함수를 사용하려면 이 매트릭스가 넘파이 배열이어야 합니다.

```js
# ALE 설명 가져오기
ale = ALE(model.predict, feature_names=X.columns, target_names=['rings'])
exp = ale.explain(X.to_numpy())
```

ALE 플롯을 생성하려면 설명과 표시하려는 피처를 plot_ale에 전달합니다. 위치 배열 [0,1,2]를 사용하면 처음 3개 피처에 대한 ALE를 표시합니다. Figure 6에서 확인할 수 있습니다.

```js
# 처음 3개 피처에 대한 ALE 설명 플롯하기
plot_ale(exp, features=[0,1,2], fig_kw={'figwidth':15, 'figheight': 5})
```

<div class="content-ad"></div>

![image](/assets/img/2024-05-23-DeepDiveonAccumulatedLocalEffectPlotsALEswithPython_9.png)

제 6번 그림에서 얻을 수 있는 몇 가지 결론은 다음과 같습니다:

- 길이와 높이가 예측된 링 개수에 미치는 영향은 껍질 무게와 비교할 때 낮습니다.
- 껍질 무게에 대한 내려가는 선은 껍질 무게가 증가함에 따라 예측된 링 개수가 감소하는 경향을 보입니다.

또한 플롯에서 개별 점을 해석할 수도 있지만, 먼저 플롯이 어떻게 중심화되었는지 이해해야 합니다. Figure 7에서 껍질 무게 관계에 초점을 맞추어 봅시다.

<div class="content-ad"></div>

![그림 7](/assets/img/2024-05-23-DeepDiveonAccumulatedLocalEffectPlotsALEswithPython_10.png)

ALE는 0을 중심으로 설정되었습니다. 이것은 각 ALE의 미중심 누적 지역 효과에서 평균을 뺌으로써 수행됩니다. 처음에는 혼란스러울 수 있습니다. ALE의 평균을 찾기 위해서는 먼저 ALE의 각 지점에서 누적 지역 효과를 합산해야 합니다. 결과적으로 ALE의 각 지점은 해당 특징값과 해당 특징의 평균 효과를 비교했을 때의 효과를 나타냅니다. 또는 더 간단히 말하면, 평균 예측과 비교했을 때의 효과입니다.

따라서, 그림 7의 터플 중량에 대한 플롯을 살펴보면 다음과 같은 결론을 내릴 수 있습니다:

- 터플 중량이 0이면 평균 예측 대비 6개의 고리 예측이 증가합니다.
- 터플 중량이 1.4인 경우에 비해 0의 터플 중량은 예측이 12개 증가합니다.

<div class="content-ad"></div>

## ALE 결합하기

그럼 alibi 패키지로 무엇을 더 할 수 있는지 알아보겠습니다. 특징들이 유사한 값을 갖고 있다면, ALE을 동일한 축에 플롯하는 것이 유용할 수 있습니다. 아래에서는 이를 3개의 무게 특징에 대해 수행합니다. 그림 8을 보면, 이러한 특징들의 영향을 비교하는 것이 얼마나 쉬운지 알 수 있습니다.

```python
# 무게 특징에 대한 ALE 플롯
fig, ax = plt.subplots(1, 1, figsize=(8, 4))

plot_ale(exp, features=[2], ax=ax, line_kw={'label': 'shucked weight'})
plot_ale(exp, features=[3], ax=ax, line_kw={'label': 'viscera weight'})
plot_ale(exp, features=[4], ax=ax, line_kw={'label': 'shell weight'})

ax.set_xlabel('weight')
```

shucked weight와 shell weight가 예측에 상당한 영향을 미치는 것을 알 수 있습니다. 그러나 그들은 반대 방향에 있습니다. 흥미로운 사실입니다! 이러한 특징들은 상호 관련이 높지만 예측과는 서로 다른 관계를 가지고 있습니다. 이는 두 특징 간의 상호 작용 때문입니다 — 이를 알아보기 위해 H-통계를 사용할 수 있습니다(향후 게시될 기사를 기대해주세요).

<div class="content-ad"></div>


![Screenshot](/assets/img/2024-05-23-DeepDiveonAccumulatedLocalEffectPlotsALEswithPython_11.png)

여기서 변수 간격이 다른 것을 알 수 있습니다. 이는 패키지가 간격을 선택하는 방식과 관련이 있습니다. 기본적으로 적어도 4개의 인스턴스가 포함된 간격을 선택합니다. 따라서 ALEs에서 끝에서 두 번째와 마지막 포인트 사이에 상대적으로 큰 거리를 볼 수 있습니다. 이러한 가중치 값에 대해 데이터 세트가 희소해지고 적어도 4개의 인스턴스를 포착하기 위해 더 큰 간격이 필요합니다.

## 간격 길이 증가

아래 코드에서는 동일한 차트를 만들되 한 가지 주요 차이가 있습니다. 간격 내의 최소 인스턴스 수를 50으로 변경했습니다 (3번째 줄). 이는 min_bin_points 매개변수를 사용하여 수행됩니다. Figure 9에서 볼 수 있듯이 결과는 더 부드러운 ALE 및 큰 간격입니다.


<div class="content-ad"></div>

```python
# 간격 조정
ale = ALE(model.predict, feature_names=X.columns, target_names=['rings'])
exp = ale.explain(X.to_numpy(), min_bin_points=50)

fig, ax = plt.subplots(1, 1, figsize=(8, 4))

plot_ale(exp, features=[2], ax=ax, line_kw={'label': 'shucked weight'})
plot_ale(exp, features=[3], ax=ax, line_kw={'label': 'viscera weight'})
plot_ale(exp, features=[4], ax=ax, line_kw={'label': 'shell weight'})

ax.set_xlabel('weight')
```

![image](/assets/img/2024-05-23-DeepDiveonAccumulatedLocalEffectPlotsALEswithPython_12.png)

`min_bin_points`는 ALE를 만들 때 trade-off를 도입합니다. 이 값을 줄이면 간격의 크기가 줄어듭니다. 이는 곡선의 진정한 모양에 더 가까워질 것입니다. 그러나 이러한 간격 내에서 효과를 추정하는 데 사용할 수 있는 샘플 크기가 줄어들어 불확실성에 직면하게 됩니다. 일반적으로 이 불확실성으로 인해 구간 간 변화보다는 전반적인 추세에 중점을 두어야 합니다.

다른 고려사항은 ALE의 해석은 명료하지만 해당 해석을 얻는 방법을 설명하기는 복잡할 수 있다는 것입니다. PDP가 어떻게 생성되는지 비교할 때 적어도 그렇습니다. 따라서 두 방법을 함께 사용하는 것이 유용합니다. 결과가 일치하면 언제든 PDP를 제시할 수 있습니다. 이렇게 하면 평균 누적 로컬 효과가 무엇인지 설명하는 머리 아픈 작업이 절약될 수 있습니다!



<div class="content-ad"></div>

만약 PDPs가 어떻게 만들어지는지 알고 싶다면 이 기사를 참조해보세요:

다른 XAI 기사들도 유용하게 사용할 수 있을 거예요:

이 기사를 즐겁게 읽으셨으면 좋겠어요! 무료로 파이썬 XAI 코스에 접근할 수 있는 Threads | YouTube | Newsletter에서 저를 찾아보세요

## 참고문헌

<div class="content-ad"></div>

[1] Daniel W Apley 및 Jingyu Zhu. 블랙 박스 지도 학습 모델에서 예측 변수의 효과를 시각화하는 방법. Journal of the Royal Statistical Society Series B: Statistical Methodology, 82(4):1059–1086, 2020.

