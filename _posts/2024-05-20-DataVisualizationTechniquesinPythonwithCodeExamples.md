---
title: "파이썬에서 데이터 시각화 기술과 코드 예제"
description: ""
coverImage: "/assets/img/2024-05-20-DataVisualizationTechniquesinPythonwithCodeExamples_0.png"
date: 2024-05-20 18:34
ogImage:
  url: /assets/img/2024-05-20-DataVisualizationTechniquesinPythonwithCodeExamples_0.png
tag: Tech
originalTitle: "Data Visualization Techniques in Python with Code Examples"
link: "https://medium.com/@pythonfundamentals/data-visualization-techniques-in-python-with-code-examples-418d24c84877"
---

데이터 시각화는 데이터에서 통찰을 얻고 전달하는 데 강력한 도구입니다. 이 기사에서는 Python을 사용하여 타이타닉 데이터셋을 중심으로 다양한 데이터 시각화 기법을 탐색하겠습니다. Matplotlib 및 Seaborn과 같은 인기있는 라이브러리를 사용하여 유의미한 시각화를 만들 것입니다. 추가적으로, 모든 플롯에는 시각적 명확성과 일관성을 높이기 위해 빨간색을 사용할 것입니다.

![image](/assets/img/2024-05-20-DataVisualizationTechniquesinPythonwithCodeExamples_0.png)

## 데이터셋 소개

타이타닉 데이터셋은 다양한 데이터 분석 및 기계 학습 작업에 사용되는 고전적인 데이터셋입니다. 타이타닉호 승객에 대한 정보를 포함하며 그들의 인구 통계, 티켓 등급, 요금 및 생존 상태에 대한 정보가 포함되어 있습니다. 타이타닉 데이터셋을 로드하고 구조를 간단히 살펴보겠습니다:

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

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# 타이타닉 데이터셋 불러오기
titanic = sns.load_dataset('titanic')
```

# 1. 숫자 변수에 대한 히스토그램

히스토그램은 숫자 변수의 분포를 시각화하는 데 유용합니다. 나이와 요금에 대한 히스토그램을 그려보겠습니다:

```python
# 나이에 대한 히스토그램 그리기
plt.figure(figsize=(8, 6))
sns.histplot(titanic['age'].dropna(), bins=30, kde=True, color='red')
plt.title('나이 분포')
plt.xlabel('나이')
plt.ylabel('빈도')
plt.show()
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

<img src="/assets/img/2024-05-20-DataVisualizationTechniquesinPythonwithCodeExamples_1.png" />

```js
# 요금에 대한 히스토그램 작성
plt.figure(figsize=(8, 6))
sns.histplot(titanic['fare'], bins=30, kde=True, color='blue')
plt.title('요금 분포')
plt.xlabel('요금')
plt.ylabel('빈도')
plt.show()
```

<img src="/assets/img/2024-05-20-DataVisualizationTechniquesinPythonwithCodeExamples_2.png" />

히스토그램을 통해 승객들의 나이와 요금 분포에 대한 통찰을 얻을 수 있습니다.

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

# 2. 범주형 변수에 대한 막대 플롯

막대 플롯은 범주형 변수의 분포를 시각화하는 데 효과적입니다. 탑승객 클래스와 생존 상태에 대한 막대 플롯을 그려보겠습니다:

```js
# 승객 클래스에 대한 막대 플롯 그리기
plt.figure(figsize=(6, 4))
sns.countplot(x='class', data=titanic, color='red')
plt.title('승객 클래스 분포')
plt.xlabel('클래스')
plt.ylabel('수')
plt.show()
```

<img src="/assets/img/2024-05-20-DataVisualizationTechniquesinPythonwithCodeExamples_3.png" />

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

# 생존 상태에 대한 막대 그래프 플롯하기

plt.figure(figsize=(8, 6))
sns.countplot(x='survived', data=titanic, color='red')
plt.title('생존 상태 분포')
plt.xlabel('생존 상태')
plt.ylabel('카운트')
plt.show()

![이미지](/assets/img/2024-05-20-DataVisualizationTechniquesinPythonwithCodeExamples_4.png)

막대 그래프는 승객들이 다른 클래스 및 생존 상태에 분포된 정보를 제공합니다.

# 3. 아웃라이어 감지를 위한 상자 그림(Box Plot)

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

상자 수염 도표는 이상치를 감지하고 숫자 변수의 분포를 시각화하는 데 유용합니다. 나이와 요금에 대한 상자 수염 도표를 그려보겠습니다:

```js
# 나이에 대한 상자 수염 도표 그리기
plt.figure(figsize=(8, 6))
sns.boxplot(x='age', data=titanic, color='red')
plt.title('나이 분포 (상자 수염 도표)')
plt.xlabel('나이')
plt.show()
```

<img src="/assets/img/2024-05-20-DataVisualizationTechniquesinPythonwithCodeExamples_5.png" />

```js
# 요금에 대한 상자 수염 도표 그리기
plt.figure(figsize=(8, 6))
sns.boxplot(x='fare', data=titanic, color='green')
plt.title('요금 분포 (상자 수염 도표)')
plt.xlabel('요금')
plt.show()
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

![Box plots](/assets/img/2024-05-20-DataVisualizationTechniquesinPythonwithCodeExamples_6.png)

Box plots는 이상값을 식별하고 연령 및 요금의 분포를 이해하는 데 도움이 됩니다.

# 결론

데이터 시각화는 데이터로부터 통찰력을 얻고 발견한 결과를 효과적으로 전달하는 데 중요합니다. 이 기사에서는 Matplotlib 및 Seaborn과 같은 Python 라이브러리를 사용하여 다양한 데이터 시각화 기술을 살펴보았습니다. 숫자형 및 범주형 변수의 분포를 시각화하고 이상값을 감지하며 Titanic 데이터셋에 대한 통찰력을 얻었습니다. 모든 플롯에 대해 빨간색을 사용함으로써 시각적 명확성과 시각화의 일관성을 보장했습니다.

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

복잡한 데이터셋을 이해하고 정보를 얻기 위해서는 효과적인 데이터 시각화가 필수적입니다. 데이터에서 숨겨진 패턴과 관계를 발견하기 위해 다양한 시각화 기술을 실험해보세요.

향후 기사에서는 고급 시각화 기술에 대해 더 깊이 파고들고 더 복잡한 데이터셋을 탐색할 것입니다. 더 많은 통찰력 있는 콘텐츠를 기대해 주세요!

# Python Fundamentals

소중한 시간과 관심에 감사드립니다! 🚀
Python Fundamentals에서 더 많은 콘텐츠를 찾아보실 수 있습니다. 💫
