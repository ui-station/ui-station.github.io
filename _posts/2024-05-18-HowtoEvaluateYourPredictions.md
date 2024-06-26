---
title: "예측 결과를 평가하는 방법"
description: ""
coverImage: "/assets/img/2024-05-18-HowtoEvaluateYourPredictions_0.png"
date: 2024-05-18 20:16
ogImage:
  url: /assets/img/2024-05-18-HowtoEvaluateYourPredictions_0.png
tag: Tech
originalTitle: "How to Evaluate Your Predictions"
link: "https://medium.com/towards-data-science/how-to-evaluate-your-predictions-cef80d8f6a69"
---

## 선택한 측정 기준을 신중하게 고려하세요

![이미지](/assets/img/2024-05-18-HowtoEvaluateYourPredictions_0.png)

머신 러닝 모델의 성능을 테스트하고 벤치마킹하는 것은 배포 후에도 중요합니다. 이를 위해 예측과 테스트 포인트를 사용하여 얼마나 성공적인 예측인지 측정하는 값을 할당하는 측정 기준이 필요합니다. 그러나 어떤 스코어링 기준을 선택할지 신중히 고려해야 합니다. 특히 예측을 평가하기 위한 방법을 선택할 때 적절한 스코어링 규칙을 고수해야 합니다. 이 아이디어에 대한 개괄적인 정의만 제공하지만, 기본적으로 우리는 측정하려는 대상에서 최소화되는 점수를 원합니다!

예측하고자 하는 변수인 랜덤 변수 Y를 X의 공변량 벡터에서 예측하고자 하는 경우를 고려해보세요. 아래 예제에서 Y는 소득이고 X는 나이와 교육과 같은 특성일 것입니다. 우리는 일부 학습 데이터에서 예측자 f를 학습하고 이제 Y를 f(x)로 예측합니다. 보통 가장 잘 예측하려면 x가 주어졌을 때 y의 기댓값을 예측합니다. 즉, f(x)는 E[Y | X=x]를 근사해야 합니다. 그러나 보다 일반적으로 f(x)는 중앙값, 다른 백분위수, 또는 전체 조건부 분포 P(Y | X=x)의 추정량일 수 있습니다.

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

이제 새로운 테스트 지점 y에 대해 우리는 예측을 점수화하려고 합니다. 즉, 당신이 할 수 있는 최선의 일을 할 때 최소화되는 함수 S(y,f(x))를 원합니다. 예를 들어, 우리가 E[Y | X=x]를 예측하려면, 이 점수는 MSE로 주어집니다: S(y, f(x))= (y-f(x))².

여기서는 (y_i,x_i)의 테스트 세트에서 예측자 f의 점수화 원리를 자세히 연구합니다. 모든 예시에서 이상적인 추정 방법을 명백히 잘못된 또는 순진한 다른 것과 비교하며, 우리의 점수가 그들이해야 하는 일을 수행하는 것을 보여줍니다.

## 예시

사항을 설명하기 위해 수입 데이터를 모방하는 간단한 데이터셋을 시뮬레이션할 것입니다. 이 간단한 예제를 통해 개념을 설명하는 데 사용할 것입니다.

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

```r
library(dplyr)


#Create some variables:
# 100명의 개인에 대한 데이터 시뮬레이션
n <- 5000

# 20세에서 60세 사이의 나이 생성
age <- round(runif(n, min = 20, max = 60))

# 교육 수준 정의
education_levels <- c("High School", "Bachelor's", "Master's")

# 교육 수준 확률 시뮬레이션
education_probs <- c(0.4, 0.4, 0.2)

# 확률에 기반한 교육 수준 샘플링
education <- sample(education_levels, n, replace = TRUE, prob = education_probs)

# 나이와 관련된 경험을 약간의 랜덤 오차와 상관시켜 경력 시뮬레이션
experience <- age - 20 + round(rnorm(n, mean = 0, sd = 3))

# 임금을 위한 비선형 함수 정의
wage <- exp((age * 0.1) + (case_when(education == "High School" ~ 1,
                                 education == "Bachelor's" ~ 1.5,
                                 TRUE ~ 2)) + (experience * 0.05) + rnorm(n, mean = 0, sd = 0.5))

hist(wage)
```

이 시뮬레이션은 과도하게 단순화된 것일 수 있지만, 나이가 많을수록, 고등 교육을 받았을수록, 그리고 경험이 많을수록 임금이 높아지는 특성을 나타냅니다. "exp" 연산자의 사용으로 인해 임금 분포가 매우 왜곡되는 것을 확인할 수 있습니다. 이는 이러한 데이터 세트에서 일관되게 관찰되는 현상입니다.

<img src="/assets/img/2024-05-18-HowtoEvaluateYourPredictions_1.png" />

중요한 점은 이 왜곡이 나이, 교육, 경험을 특정 값으로 고정했을 때에도 발생한다는 점입니다. 특정 인물 Dave를 살펴보자. 그는 30세이고, 경제학 학사 학위를 소지하며 10년의 경험이 있습니다. 데이터 생성 프로세스에 따른 실제 소득 분포를 살펴봅시다.

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

```r
나이데이브 <- 30
학력데이브 <- "학사학위"
경험데이브 <- 10

임금데이브 <- exp((나이데이브 * 0.1) +
           (case_when(학력데이브 == "고등학교" ~ 1,
                      학력데이브 == "학사학위" ~ 1.5,
                      TRUE ~ 2)) +
           (경험데이브 * 0.05) + rnorm(n, mean = 0, sd = 0.5))

hist(임금데이브, main="데이브의 임금 분포", xlab="임금")
```

![Wage Distribution for Dave](/assets/img/2024-05-18-HowtoEvaluateYourPredictions_2.png)

그래서 우리가 알고 있는 데이브에 대한 가능한 임금 분포는 여전히 매우 비대칭적입니다.

또한 여러 사람에 대한 테스트 세트를 생성합니다.

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

```R
## 테스트 세트 생성
ntest <- 1000

# 20에서 60 사이의 나이 생성
agetest <- round(runif(ntest, min = 20, max = 60))

# 확률에 따라 학력 수준 샘플링
educationtest <- sample(education_levels, ntest, replace = TRUE, prob = education_probs)

# 나이와 상관 관계 있는 경험 시뮬레이션 (랜덤 오차 포함)
experiencetest <- agetest - 20 + round(rnorm(ntest, mean = 0, sd = 3))

## 예측하려는 ytest 생성:

wagetest <- exp((agetest * 0.1) + (case_when(educationtest == "High School" ~ 1,
                                             educationtest == "Bachelor's" ~ 1.5,
                                             TRUE ~ 2)) + (experiencetest * 0.05) + rnorm(ntest, mean = 0, sd = 0.5))
```

이제 간단하게 시작하여 평균과 중앙값 예측에 대한 점수를 살펴봅니다.

## 평균 및 중앙값 예측 점수

데이터 과학과 머신 러닝에서는 종종 예측하려는 분포의 "중심" 또는 "가운데"를 나타내는 단일 숫자에 중점을 둡니다. 즉, (조건부) 평균 또는 중앙값입니다. 이를 위해 평균 제곱 오차(MSE)가 있습니다.

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

![How to Evaluate Your Predictions 3](/assets/img/2024-05-18-HowtoEvaluateYourPredictions_3.png)

and the mean absolute error (MAE):

![How to Evaluate Your Predictions 4](/assets/img/2024-05-18-HowtoEvaluateYourPredictions_4.png)

An important takeaway is that the MSE is the appropriate metric for predicting the conditional mean, while the MAE is the measure to use for the conditional median. Mean and median are not the same thing for skewed distributions like the one we study here.

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

위 예제에 매우 간단한 예측 변수를 사용하여 설명하겠습니다 (실제 상황에서는 이런 변수에 접근할 수 없겠지만), 예시를 위해:

```js
conditionalmeanest <-
  function(age, education, experience, N = 1000) {
    mean(exp((age * 0.1) + (
      case_when(
        education == "High School" ~ 1,
        education == "Bachelor's" ~ 1.5,
        TRUE ~ 2
      )
    ) + (experience * 0.05) + rnorm(N, mean = 0, sd = 0.5)
    ))
  }

conditionalmedianest <-
  function(age, education, experience, N = 1000) {
    median(exp((age * 0.1) + (
      case_when(
        education == "High School" ~ 1,
        education == "Bachelor's" ~ 1.5,
        TRUE ~ 2
      )
    ) + (experience * 0.05) + rnorm(N, mean = 0, sd = 0.5)
    ))
  }
```

즉, 나이, 교육 및 경험의 고정된 값에 대해 모델을 시뮬레이션한 후 (이는 올바른 조건부 분포에서의 시뮬레이션일 것입니다) 평균/중앙값을 간단히 취하여 평균 및 중앙값을 추정합니다. Dave에게 이를 시험해 봅시다:

```js
hist(wageDave, (main = "Dave의 임금 분포"), (xlab = "임금"));
abline(
  (v = conditionalmeanest(ageDave, educationDave, experienceDave)),
  (col = "darkred"),
  (cex = 1.2)
);
abline(
  (v = conditionalmedianest(ageDave, educationDave, experienceDave)),
  (col = "darkblue"),
  (cex = 1.2)
);
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

![How to Evaluate Your Predictions](/assets/img/2024-05-18-HowtoEvaluateYourPredictions_5.png)

평균과 중간값이 다르다는 것을 알 수 있습니다. 소득 분포에서 기대되는 것처럼, 사실 평균은 중간값보다 높습니다(고값의 영향을 더 많이 받기 때문).

이제 이러한 추정기를 테스트 세트에 적용해 봅시다:

```js
Xtest<-data.frame(age=agetest, education=educationtest, experience=experiencetest)

meanest<-sapply(1:nrow(Xtest), function(j)  conditionalmeanest(Xtest$age[j], Xtest$education[j], Xtest$experience[j])  )
median<-sapply(1:nrow(Xtest), function(j)  conditionalmedianest(Xtest$age[j], Xtest$education[j], Xtest$experience[j])  )
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

이것은 다양한 조건부 평균/중앙값 범위를 제공합니다. 이제 평균 제곱 오차(MSE)와 평균 절대 오차(MAE)를 계산해 봅시다:

```js
(MSE1<-mean((meanest-wagetest)^2))
(MSE2<-mean((median-wagetest)^2))

MSE1 < MSE2
### 메소드 1(진정한 평균 추정기)이 메소드 2보다 더 나아요!

# 하지만 실제로 평균 절대 오차는 메소드 1이 더 나쁩니다!
(MAE1<-mean(abs(meanest-wagetest)) )
(MAE2<-mean( abs(median-wagetest)))

MAE1 < MAE2
### 메소드 2(진정한 중앙값 추정기)이 메소드 1보다 더 나아요!
```

이는 이론적으로 알려진 것을 보여줍니다: MSE는 (조건부) 기대값 E[Y | X=x]에서 최소화되고, MAE는 조건부 중앙값에서 최소화됩니다. 일반적으로, 평균 예측을 평가할 때 MAE를 사용하는 것은 의미가 없습니다. 많은 적용 연구 및 데이터 과학에서 사람들은 평균 예측을 평가하기 위해 MAE를 사용하거나 둘 다 사용합니다(내가 직접 해 봤기 때문에 알고 있습니다). 이것은 특정 응용 분야에서는 타당할 수 있지만, 위 예제에서 보았듯이 비대칭 분포에 대해서는 심각한 결과를 초래할 수 있습니다: MAE를 살펴보면, 메소드 1이 메소드 2보다 더 나쁘게 보입니다. 그러나 전자는 사실상 평균을 올바르게 추정합니다. 사실, 이 예시에서와 같이 매우 비대칭인 경우에는 메소드 1이 메소드 2보다 낮은 MAE를 가져야 합니다.

## 분위수 및 구간 예측에 대한 점수

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

가정해보겠습니다. 우리는 양도 q_x의 추정치 f(x)를 점수 매길 때

![image1](/assets/img/2024-05-18-HowtoEvaluateYourPredictions_6.png)

![image2](/assets/img/2024-05-18-HowtoEvaluateYourPredictions_7.png)

이 경우, 우리는 백분위수 점수를 고려할 수 있습니다:

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

![Image](/assets/img/2024-05-18-HowtoEvaluateYourPredictions_8.png)

whereby

![Image](/assets/img/2024-05-18-HowtoEvaluateYourPredictions_9.png)

To unpack this formula, we can consider two cases:

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

(1) y가 f(x)보다 작습니다:

![image](/assets/img/2024-05-18-HowtoEvaluateYourPredictions_10.png)

즉, y가 f(x)에서 멀어질수록 부과하는 벌금이 커집니다.

(2) y가 f(x)보다 큽니다:

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

이미지 태그를 다음과 같이 수정해주세요:

![이미지](/assets/img/2024-05-18-HowtoEvaluateYourPredictions_11.png)

즉, y가 f(x)로부터 멀어질수록 벌점이 커집니다.

가중치가 높은 알파의 경우, 추정된 분위수 f(x)가 y보다 작은 경우 더 많이 벌점을 받습니다. 이것은 의도된 것으로, 올바른 분위수가 실제로 y에 대한 S(y, f(x))의 기댓값 최소화자임을 보장합니다. 이 점수는 사실 분위수 손실(2배 곱해진 상태)입니다. quantile_score 함수가 포함된 패키지 scoringutils의 R 구현체를 참조하세요. 마지막으로, alpha=0.5인 경우:

![이미지](/assets/img/2024-05-18-HowtoEvaluateYourPredictions_12.png)

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

MAE를 단순히! 이것은 0.5 분위가 중앙값이기 때문에 의미가 있습니다.

분위수를 예측할 수 있는 능력이 있으면 예측 구간을 구축할 수도 있습니다. (l_x, u_x)를 고려해보세요. 여기서 l_x ≤ u_x이고 다음이 성립합니다.

[이미지](/assets/img/2024-05-18-HowtoEvaluateYourPredictions_13.png)

사실, l_x가 alpha/2 분위이고 u_x가 1-alpha/2 분위인 경우 이를 만족합니다. 따라서 이제 이 두 분위수를 추정하고 점수를 매길 수 있습니다. f(x)=(f_1(x), f_2(x))로 취급하면, 여기서 f_1(x)은 l_x의 추정값이고 f_2(x)는 u_x의 추정값입니다. 우리는 "이상적" 추정자와 "단순한" 추정자를 제공합니다. 이상적 추정자는 참 프로세스에서 다시 시뮬레이션한 다음 필요한 분위를 예측하는 것이고, "단순한" 추정자는 정확한 커버리지를 가지지만 너무 큽니다:

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

```js
library(scoringutils)

## 조건부 분위수 추정 정의
conditionalquantileest <-
  function(probs, age, education, experience, N = 1000) {
    quantile(exp((age * 0.1) + (
      case_when(
        education == "High School" ~ 1,
        education == "Bachelor's" ~ 1.5,
        TRUE ~ 2
      )
    ) + (experience * 0.05) + rnorm(N, mean = 0, sd = 0.5)
    )
    , probs =
      probs)
  }

## 필요한 커버리지를 갖추는 매우 순진한 추정자 정의
lowernaive <- 0
uppernaive <- max(wage)

# 관심 있는 분위수 정의
alpha <- 0.05

lower <-
  sapply(1:nrow(Xtest), function(j)
    conditionalquantileest(alpha / 2, Xtest$age[j], Xtest$education[j], Xtest$experience[j]))
upper <-
  sapply(1:nrow(Xtest), function(j)
    conditionalquantileest(1 - alpha / 2, Xtest$age[j], Xtest$education[j], Xtest$experience[j]))



## 두 추정자에 대한 점수 계산

# 1. alpha/2 분위수 추정의 점수 매기기
qs_lower <- mean(quantile_score(wagetest,
                           predictions = lower,
                           quantiles = alpha / 2))
# 2. 1 - alpha/2 분위수 추정의 점수 매기기
qs_upper <- mean(quantile_score(wagetest,
                           predictions = upper,
                           quantiles = 1 - alpha / 2))

# 1. alpha/2 분위수 추정의 점수 매기기
qs_lowernaive <- mean(quantile_score(wagetest,
                                predictions = rep(lowernaive, ntest),
                                quantiles = alpha / 2))
# 2. 1 - alpha/2 분위수 추정의 점수 매기기
qs_uppernaive <- mean(quantile_score(wagetest,
                                predictions = rep(uppernaive, ntest),
                                quantiles = 1 - alpha / 2))

# 점수 평균을 통해 구간 점수 산출
(interval_score <- (qs_lower + qs_upper) / 2)
# 이상적인 추정자의 점수: 187.8337

# 점수 평균을 통해 구간 점수 산출
(interval_scorenaive <- (qs_lowernaive + qs_uppernaive) / 2)
# 순진한 추정자의 점수: 1451.464
```

다시 한 번 평균적으로 올바른 추정자가 순진한 추정자보다 점수가 훨씬 낮은 것을 명확히 볼 수 있어요!

따라서 분위수 점수를 통해 개별 분위수 예측을 신뢰할 수 있는 방법으로 평가할 수 있습니다. 그러나 상한 및 하한 분위수 점수를 평균하여 예측 구간에 대한 점수를 평균 내는 방식이 조금 임의적으로 보일 수 있습니다. 다행히도 이는 이른바 구간 점수로 이어지는 것으로 밝혀졌습니다:

<img src="/assets/img/2024-05-18-HowtoEvaluateYourPredictions_14.png" />

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

따라서 대수적인 매직을 통해 우리는 alpha/2와 1-alpha/2 분위의 점수를 평균하여 예측 구간의 점수를 매길 수 있습니다. 흥미로운 점은, 결과적인 구간 점수가 좁은 예측 구간을 장려하며, 관측값이 그 구간에서 벗어나면 alpha에 따라 패널티가 부여된다는 점입니다. 분위점 점수의 평균을 사용하는 대신, 패키지 scoringutils로 이 점수를 직접 계산할 수도 있습니다.

```js
alpha <- 0.05
mean(interval_score(
  wagetest,
  lower=lower,
  upper=upper,
  interval_range=(1-alpha)*100,
  weigh = T,
  separate_results = FALSE
))
# 이상적인 추정기의 점수: 187.8337
```

위에서 두 구간의 점수를 평균할 때 얻은 정확히 같은 숫자입니다.

## 분포 예측에 대한 점수

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

점점 더 다양한 분야에서는 분포 예측과 관련된 문제를 다뤄야 합니다. 다행히 이 문제를 해결하는 데 도움이 되는 척도가 있습니다. 특히, 여기서는 '에너지 점수'라고 불리는 것에 초점을 맞춥니다:

for f(x) being an estimate of the distribution P(Y | X=x). The second term takes the expectation of the Eucledian distance between two independent samples from f(x). This is akin to a normalizing term, establishing the value if the same distribution was compared. The first term then compares the sample point y to a draw X from f(x). In expectation (over Y drawn from P(Y | X=x)) this will be minimized if f(x)=P(Y | X=x).

따라서 단순히 평균이나 분위수를 예측하는 대신, 이제 우리는 각 테스트 지점에서 월급의 전체 분포를 예측하려고 합니다. 본질적으로 우리는 Dave를 위해 그래프로 나타낸 조건부 분포를 예측하고 평가하려고 합니다. 이것은 조금 더 복잡합니다. 정확히 학습된 분포를 어떻게 나타내어야 할까요? 실제로 이는 예측된 분포로부터 샘플을 얻을 수 있는 것으로 해결됩니다. 따라서 예측된 분포에서 얻은 N개의 샘플을 단일 테스트 지점과 비교합니다. R에서는 scoringRules 패키지의 es_sample을 사용하여 이를 수행할 수 있습니다:

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

라이브러리(scoringRules)

## 이상적인 "추정": 각 샘플 포인트 x에 대한 참조 조건부 분포 P(Y | X=x)에서 단순히 샘플링

```js
distributionestimate <-
  function(age, education, experience, N = 100) {
    exp((age * 0.1) + (
      case_when(
        education == "High School" ~ 1,
        education == "Bachelor's" ~ 1.5,
        TRUE ~ 2
      )
    ) + (experience * 0.05) + rnorm(N, mean = 0, sd = 0.5))
  }

## 단순 추정: 개인의 정보를 고려하지 않고 오차 분포만에서 샘플링
distributionestimatenaive <-
  function(age, education, experience, N = 100) {
    exp(rnorm(N, mean = 0, sd = 0.5))
  }


scoretrue <- mean(sapply(1:nrow(Xtest), function(j)  {
  wageest <-
    distributionestimate(Xtest$age[j], Xtest$education[j], Xtest$experience[j])
  return(scoringRules::es_sample(y = wagetest[j], dat = matrix(wageest, nrow=1)))
}))

scorenaive <- mean(sapply(1:nrow(Xtest), function(j)  {
  wageest <-
    distributionestimatenaive(Xtest$age[j], Xtest$education[j], Xtest$experience[j])
  return(scoringRules::es_sample(y = wagetest[j], dat = matrix(wageest, nrow=1)))
}))

## scoretrue: 761.026
## scorenaive: 2624.713
```

위의 코드에서 "완벽한" 추정(즉, 참조 분포 P(Y | X=x)에서 샘플링)을 매우 단순한 추정과 비교합니다. 여기서 "매우 단순한" 추정은 급여, 교육 또는 경험에 관한 정보를 고려하지 않습니다. 다시 한 번, 점수는 두 방법 중 더 나은 방법을 신뢰할 수 있게 식별합니다.

## 결론

우리는 예측 평가 방법에 대해 다양한 방법을 살펴보았습니다. 올바른 측정 항목에 대해 생각하는 것은 중요합니다. 잘못된 측정 항목은 예측 작업에 잘못된 모델을 선택하고 유지하게 할 수 있습니다.

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

특히 분포 예측을 위해서는 이러한 점수 부여가 어려운 작업이며 실제로는 그렇게 큰 영향력을 가지지 못할 수 있음을 알아두어야 합니다. 즉, 심한 개선을 이끌어내는 방법조차도 조금 더 낮은 점수만을 갖는 경우가 있을 수 있습니다. 그러나 실제로는 두 가지 방법 중 어느 것이 더 나은지 신뢰할 수 있는 방법으로 식별할 수 있다면 이는 본질적인 문제가 아닙니다.

## 참고 자료

[1] Tilmann Gneiting & Adrian E Raftery (2007) Strictly Proper Scoring Rules, Prediction, and Estimation, Journal of the American Statistical Association, 102:477, 359–378, DOI: 10.1198/016214506000001437

## 부록: 코드 모두 한 곳에

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

```R
library(dplyr)

# 몇 가지 변수 생성:
# 100명의 개인에 대한 데이터 시뮬레이션
n <- 5000

# 20에서 60 사이의 나이 생성
age <- round(runif(n, min = 20, max = 60))

# 교육 수준 정의
education_levels <- c("고등학교", "학사", "석사")

# 교육 수준 확률 시뮬레이션
education_probs <- c(0.4, 0.4, 0.2)

# 확률에 기반한 교육 수준 샘플링
education <- sample(education_levels, n, replace = TRUE, prob = education_probs)

# 나이와 상관된 경험 시뮬레이션 및 랜덤 오차 추가
experience <- age - 20 + round(rnorm(n, mean = 0, sd = 3))

# 월급에 대해 비선형 함수 정의
wage <- exp((age * 0.1) + (case_when(education == "고등학교" ~ 1,
                                     education == "학사" ~ 1.5,
                                     TRUE ~ 2)) + (experience * 0.05) + rnorm(n, mean = 0, sd = 0.5))

hist(wage)

ageDave <- 30
educationDave <- "학사"
experienceDave <- 10

wageDave <- exp((ageDave * 0.1) + (case_when(educationDave == "고등학교" ~ 1,
                                             educationDave == "학사" ~ 1.5,
                                             TRUE ~ 2)) + (experienceDave * 0.05) + rnorm(n, mean = 0, sd = 0.5))

hist(wageDave, main="데이브의 급여 분포", xlab="급여")

# 테스트 세트 생성
ntest <- 1000

# 20에서 60 사이의 나이 생성
agetest <- round(runif(ntest, min = 20, max = 60))

# 확률에 기반한 교육 수준 샘플링
educationtest <- sample(education_levels, ntest, replace = TRUE, prob = education_probs)

# 나이와 상관된 경험 시뮬레이션 및 랜덤 오차 추가
experiencetest <- agetest - 20 + round(rnorm(ntest, mean = 0, sd = 3))

# ytest 생성
wagetest <- exp((agetest * 0.1) + (case_when(educationtest == "고등학교" ~ 1,
                                             educationtest == "학사" ~ 1.5,
                                             TRUE ~ 2)) + (experiencetest * 0.05) + rnorm(ntest, mean = 0, sd = 0.5))
...
```
