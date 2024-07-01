---
title: "데이터 시각화로 스토리텔링 하는 방법"
description: ""
coverImage: "/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_0.png"
date: 2024-07-01 16:00
ogImage: 
  url: /assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_0.png
tag: Tech
originalTitle: "How to Make Your Data Visualizations Tell a Story"
link: "https://medium.com/@sdbutalla/how-to-make-your-data-visualizations-tell-a-story-58472d40fa2d"
---


- 소개
- 규칙 0: 데이터의 유형을 고려하세요
- 규칙 1: 면적 당 정보를 최대화하세요
- 규칙 2: 중요한 축 레이블을 무시하지 마세요
- 규칙 3: 가능한 경우 오차 막대 추가
- 규칙 4: 모든 것의 글꼴 크기를 키우세요
- 규칙 5: 축 척도를 적절히 설정하세요
- 규칙 6: 소표시선과 그리드를 사용하세요
- 규칙 7: 범례를 잊지 마세요
- 규칙 8: 명확성이 중요합니다
- 규칙 9: 일관성이 중요합니다
- 규칙 10: 지나치게 혼잡해지지 않도록 주의하세요
- 규칙 11: 적합 매개변수를 요약하는 텍스트 상자 추가
- 규칙 12: 그림을 나란히 표시할 때 동일한 축 척을 사용하세요
- 규칙 13: 텍스트 상자를 절약해서 사용하세요
- 규칙 14: 그림은 .pdf 파일로 저장하세요
- 요약 및 결론

# 소개

한 명의 현명한 사람이 말했습니다: "그림은 어떤 설명 없이도 스스로 설명이 가능해야 합니다." 즉, 제목이나 저자의 설명 없이 독자에게 제공된 그림은 그 안에 담긴 정보를 해독하고 의미 있는 결론을 추출할 수 있어야 합니다. 저는 다수의 그림을 제작했으며, 이 중 많은 그림이 '원자력 계측 방법', '계측 학지', '원자력 과학 심포지엄 및 IEEE 의료 영상 학술 대회' 등의 학술지에 게재되어 전국 및 국제 학회에 발표되었습니다. 학업 생활을 시작할 때 나는 어떻게 그림을 만드는지 (좋은 그림은 더욱) 몰랐습니다. 과학적 공동 작업 그룹에 가입하고 논문을 투고하면서야 그렇게 배울 수 있었습니다. 논문 본문과 모든 도표/표는 공동 작업 그룹 내부 검토를 여러 번 거쳤을 뿐만 아니라 투고 과정에서도 학술지에서 승인을 받아야 했습니다. 데이터 시각화에 대한 접근 방식이 완전히 틀렸다는 사실을 빨리 깨달았습니다. 그것은 꽤 충격적인 깨달음이었는데, 그림 (및 글쓰기)의 품질은 외부 세계에 대한 귀하의 커뮤니케이션 기술의 즉각적인 반영입니다.

저는 지난 7년 동안 배운 지식을 공유하고자 이 기사를 쓰려고 합니다. 제가 겪은 고통을 당신에게 납득시킬 수 있기를 바래봅니다. 데이터를 시각화하는 것은 과학적 노력뿐만 아니라 예술입니다; 심미적 선택은 이러한 과학적 노력에서 더욱 큰 역할을 합니다. 이러한 객관적 및 주관적 영향의 혼재를 풀어내기 위해, 나는 그림 작성 철학을 14가지 간단한 규칙으로 요약했습니다. 여러분이 유용하게 사용하길 바랍니다.

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

저는 다양한 데이터를 다룰 예정이에요; 예제에 더 잘 맞게 하기 위해 수동으로 생성하는 데이터도 있고 캐글에서 가져오는 데이터셋도 있을 거예요. 하지만 모든 데이터셋에 대해 csv 파일을 다운로드할 수 있는 링크를 포함하거나 데이터를 어떻게 생성했는지 보여드릴 거에요. 시작하기 전에 이 글에서 데이터를 생성/플로팅하는 데 사용된 모든 import 문을 나열할 거에요:

```js
import matplotlib as mpl
import matplotlib.pyplot as plt
from matplotlib import cm
from matplotlib.lines import Line2D
import matplotlib.patches as mpatches
from matplotlib.ticker 
import numpy as np
import pandas as pd
from scipy.optimize import curve_fit
from sklearn.neighbors import KernelDensity
```

참고로, 저는 Python 버전 3.12.2를 사용하고 있고, 아래에서 모든 패키지 버전을 나열할 거에요:

```js
matplotlib==3.8.2
numpy==1.26.3
pandas==2.2.0
scikit-learn==1.4.0
scipy==1.12.0
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

마지막으로, Matplotlib의 전역 매개변수 몇 가지도 변경하고 싶습니다. 예를 들어, 주요 및 보조 눈금 표시 마커의 크기를 키우고 싶습니다. 그래서 실행 시 구성(RC) 매개변수(Parameter)의 재정의를 아래에 나열해 두겠습니다:

```js
mpl.rcParams['xtick.major.size']  = 5
mpl.rcParams['xtick.major.width'] = 2
mpl.rcParams['xtick.minor.size']  = 4
mpl.rcParams['xtick.minor.width'] = 1

mpl.rcParams['ytick.major.size']  = 5
mpl.rcParams['ytick.major.width'] = 2
mpl.rcParams['ytick.minor.size']  = 4
mpl.rcParams['ytick.minor.width'] = 1
```

# Rule 0: 데이터의 유형을 고려하세요

이 규칙은 데이터 시각화에 가장 중요한 규칙으로, 별도의 기사로도 충분히 다루어질 만큼 중요합니다. 하지만 여기서 간결하게 요약하겠습니다. 플롯의 특별한 내용을 고려하기 전에, 먼저 데이터를 가장 잘 표시할 수 있는 플롯 유형을 고려해야 합니다. 이를 정확하게 파악하는 것은 어려울 수 있지만, 데이터를 플로팅할 때 따를 수 있는 몇 가지 기본적인 지침을 설명하겠습니다. 이 토론을 간결하게 유지하기 위해, 데이터 탐색 및 변수 관계에 적합한 데이터 시각화 방법만 고려하겠으며, 플로우, 아크 또는 트리 형태의 플롯으로 해결하는 것이 가장 적합한 문제에 대해서는 고려하지 않겠습니다. 두 가지 더 중요한 점은 다음과 같습니다: 이 목록은 모든 플롯 유형을 갖춘 것이 아니며, 이러한 지침은 절대적인 것이 아니지만 시작점을 찾는 데 도움이 되는 공리입니다.

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

## 히스토그램 및 상자, 바이올린, 및 확률 밀도 그림

히스토그램: 데이터 탐색을 위한 가장 쉬운 옵션이자 처음 익히면 좋은 것은 겸손한 히스토그램입니다. 이것은 분포를 빠르게 표시하는 데 사용되는 지금까지 믿음직한 방법이죠. 우리가 다루는 모든 새로운 데이터셋에 대해 우리는 데이터가 어떻게 분포되어 있는지 보고 싶어합니다. 예를 들어, 극값이 어디에 위치하는지, 분포의 모양(단일 모양, 이분 모양 등), 시각적으로 추정할 수 있는 몇 가지 기초 통계 (평균, 중앙값, 분산 등) 등을 알고 싶어합니다. 그러나 히스토그램은 1차원 플롯입니다. x축에 하나의 피처를 표시하며, y축은 각 bin에서 발생하는 횟수를 인코딩하여 우리에게 기초 분포의 좋은 감각을 제공합니다. 따라서 많은 피처를 가진 데이터셋의 경우 일반적으로 최적의 옵션이 아닙니다 (물론 여러 피처를 동일한 축에 플롯할 수 있을 때는 제외하고요. 그렇지 않으면 데이터셋의 각 피처에 대해 히스토그램이 필요합니다). 이 플롯 유형 설명의 마지막에 히스토그램을 상단 왼쪽 패널에 표시한 플롯을 참조하세요.

히스토그램은 신뢰할 만한 도구이지만 완벽하지는 않습니다. bin 크기는 분포 형태에 상당한 영향을 미칠 수 있습니다. 나중에는 히스토그램에 전적으로 헌신된 기사를 작성할 것입니다 (데이터를 가장 잘 표시하는 방법, bin 크기 선택 방법 등에 관한 것 등), 하지만 지금은 bin 크기를 조정하여 분포 모양이 어떻게 영향을 받는지 확인해보세요.

상자 수염 그림: 히스토그램 대신에 사용할 수 있는 대안은 상자 수염 그림입니다. 이것은 데이터가 플로팅되는 방식을 표준화함으로써 bin-의존 상태를 약간 향상시킵니다. 상자는 제 1사분위부터 제 3사분위까지 (1Q 및 3Q 각각)를 나타내며 이를 사분위범위 (IQR)라고도 합니다. IQR 내에서 중앙값을 나타내는 선이 그어지고, 종종 마커가 평균값을 나타냅니다. 수염은 위에서 제3사분위부터 상단 3Q + (1.5 • IQR)까지, 및 하단에서 1사분위부터 하단 1Q - (1.5 • IQR)까지 연장됩니다. 이 범위의 이상치는 수염 밖에 그려진 마커로 나타납니다. 처음에는 약간 이상하게 보일 수 있지만, 상자 수염 그림이 키 특징이 표시된 히스토그램의 요약일 뿐이라는 것을 깨달을 때 이해하기가 완전히 가능해지며 유틸리티가 급격하게 증가합니다. 아래 상자수염 그림과 정규 분포의 대조를 살펴보세요:

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

![이미지](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_0.png)

바이올린 플롯: 상자그림의 확장인 바이올린 플롯은 데이터 집합의 최솟값과 최댓값을 가리키는 수직선 왼쪽과 오른쪽에 확률 밀도 함수(probability density function, PDF)를 겹쳐 놓습니다. 아래 예시에서는 기본 바이올린 플롯의 유용성을 확대하기 위해 중앙값(주황색 선)과 평균값(빨간 다이아몬드)을 표시했습니다.

확률 밀도 함수 그래플: 바이올린 플롯의 구성 요소 중 하나로 논의된 PDF 그래프는 대상 데이터만으로 생성되는 KDE(커널 밀도 추정) 과정에 따라 단순히 PDF만 보여줍니다. 이러한 종류의 그래플은 통계에 익숙하지 않은 사람들에게는 해석하기 어려울 수 있지만, 통계를 아는 사람들에게는 히스토그램보다 유용한 경우도 있습니다. 왜냐하면 데이터에 존재하는 미묘한 변동을 완화시키기 때문입니다. KDE 과정은 다음과 같이 요약될 수 있습니다: 분포 내 각 데이터 포인트에 대해 커널(대부분의 경우 가우스 함수)이 적합되고, 이러한 개별 가우시안 분포가 합산되고 평활화되어 확률 밀도 분포가 생성됩니다. (실제 KDE 과정은 이보다 복잡하지만, 일단 이 설명으로 충분합니다.) 이 그래플의 독특한 이점은 적분(곡선 아래 영역)이 확률을 나타낸다는 것입니다. 아래 예시를 사용해 이것의 물리적 해석은 "샘플링된 개인이 일부 혈중 콜레스테롤 범위를 가질 확률은 얼마인가" 입니다. (중요성 때문에, KDE 방법에 대해 보다 철저한 설명이 필요하므로 전체 논문을 쓸 예정입니다.)

아래 예시에 표시된 예제에 사용된 데이터 집합은 Kaggle의 심장 데이터셋(데이터셋 링크)에서 가져온 것입니다. 아래 그래프에서는 전체 데이터 집합(남성과 여성 모두 포함)의 콜레스테롤이 표시되어 있습니다.

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
## 데이터 전처리
df_heart = pd.read_csv('heart.csv')
chol = df_heart.chol.to_numpy()

## KDE 방법을 사용하여 PDF 계산
kde = KernelDensity(kernel='gaussian', bandwidth=10).fit(chol.reshape(-1, 1))
log_dens = kde.score_samples(np.arange(0, 600).reshape(-1, 1))

## 서브플롯 간격 매개변수
left = 0.125   # 그림의 서브플롯의 왼쪽
right = 0.9    # 그림의 서브플롯 오른쪽
bottom = 0.1   # 그림의 서브플롯의 아래쪽
top = 0.9      # 그림의 서브플롯의 위쪽
wspace = 0.5   # 서브플롯 사이의 공백 너비
hspace = 0.4   # 서브플롯 사이의 공백 높이

matplotlib.rcParams.update({'font.size': 24})  # 전역 글꼴 크기 설정
meanprops = dict(marker='D', markeredgecolor='black',
                 markerfacecolor='crimson') # 평균 마커를 위한 상자그림 속성

fig, ax = plt.subplots(2, 2, figsize=(14, 13))

## 히스토그램
ax[0, 0].grid('both', zorder=0)
ax[0, 0].hist(chol, bins=40, alpha=0.8, color='forestgreen', edgecolor='black', zorder=2)

ax[0, 0].set_xlim(0, 600)
ax[0, 0].set_ylim(0, 40)
ax[0, 0].set_xlabel('콜레스테롤 수준 (mg/dl)', loc='right')
ax[0, 0].set_ylabel('빈도', loc='top')
ax[0, 0].set_title('히스토그램')
ax[0, 0].xaxis.set_minor_locator(AutoMinorLocator())
ax[0, 0].yaxis.set_minor_locator(AutoMinorLocator())
ax[0, 0].tick_params(which='major', length=6, width=2)
ax[0, 0].tick_params(which='minor', length=3.5, width=1)

...

fig.subplots_adjust(left=left, bottom=bottom, right=right, top=top,
                    wspace=wspace, hspace=hspace) # 서브플롯 간 공백 조정
fig.savefig('distribution_plots.pdf')
```

각 플롯 유형은 거의 동일한 것을 디스플레이하며, 각각의 장단점이 있습니다. 이러한 플롯 유형의 데이터는 연속적이지만 필요에 따라 한 축을 이산화하거나 몇 가지 값을 구간으로 나눌 수 있으며, 결과적으로 히스토그램과 유사한 것을 생성할 수 있습니다. 모든 경우에, 이러한 플롯 유형은 계층화된 데이터 간 분포를 비교하는 데 매우 유용하며, 특히 상자 그림과 바이올린 플롯이 있습니다. 연구 대상 남성과 여성 참가자의 콜레스테롤 수준을 비교해보세요:

<img src="/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_2.png" />

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
## 데이터 전처리
m_chol = df[df['sex'] == 1].chol.to_numpy() # 남성 콜레스테롤 값 가져오기
f_chol = df[df['sex'] == 0].chol.to_numpy() # 여성 콜레스테롤 값 가져오기

mpl.rcParams.update({'font.size': 24}) # 전역 글꼴 크기 설정
fig, ax = plt.subplots(2, 2, figsize=(14, 13)) # 2x2 서브 플롯 생성

## 히스토그램 그리기
ax[0, 0].grid('both', zorder=0)
ax[0, 0].hist(m_chol, bins=40, alpha=0.6, color='forestgreen', edgecolor='black', zorder=2)
ax[0, 0].hist(f_chol, bins=40, alpha=0.6, color='xkcd:cerulean', edgecolor='black', zorder=2)

ax[0, 0].set_xlim(0, 600)
ax[0, 0].set_ylim(0, 22)
ax[0, 0].set_xlabel('콜레스테롤 수치 (mg/dl)', loc='right')
ax[0, 0].set_ylabel('빈도', loc='top')
ax[0, 0].set_title('히스토그램')

...

## 일부 요소 생략
```

히스토그램이나 확률 밀도 함수(PDF)가 겹칠 때 그래프가 지저분해질 수 있습니다. 이럴 경우, 맷플롯립이 제공하는 다른 그리기 방법 중 하나인 barstacked나 step 옵션을 사용하는 게 좋습니다. (아래 그림 참조)

<img src="/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_3.png" />

```js
mpl.rcParams.update({'font.size': 24})
## 사용자 정의 범례 항목
legend = [mpatches.Patch(color='forestgreen', label='남성', alpha=0.6),
          mpatches.Patch(color='xkcd:cerulean', label='여성', alpha=0.6)
         ]

legend_line = [Line2D([0], [0], color='forestgreen', lw=2.5, label='남성'),
               Line2D([0], [0], color='xkcd:cerulean', lw=2.5, label='여성')
              ]

fig, ax = plt.subplots(1, 2, figsize=(12, 8))
ax[0].grid('both', zorder=0)
ax[0].hist([m_chol,f_chol], bins=25, alpha=0.6, linewidth=2.3, edgecolor='k', color=['forestgreen', 'xkcd:cerulean'], histtype='barstacked',zorder=2)
ax[1].hist([m_chol,f_chol], bins=25, linewidth=2.5, color=['forestgreen', 'xkcd:cerulean'], histtype='step',zorder=2)

...

## 일부 요소 생략
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

플롯이 지저분하고 해석하기 어렵다면, 아래 두 개의 PDF처럼 서로 다른 데이터를 별도의 서브플롯에 그릴 수 있어요:

![PDFs](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_4.png)

```js
## 데이터 처리
log_dens   = [log_dens_m, log_dens_f]
pdf_colors = ['forestgreen', 'xkcd:cerulean']
pdf_labels = ['남성', '여성']

mpl.rcParams.update({'font.size': 30})
fig, ax = plt.subplots(1, 2, figsize=(15, 8))

#ax[1, 1].fill(x_data, kde_y)
for subplot in np.arange(2):
    ax[subplot].plot(np.arange(0, 600), np.exp(log_dens[subplot]), color=pdf_colors[subplot], linewidth=2)
    ax[subplot].set_xlabel('콜레스테롤 농도 (mg/dl)', loc='right')
    ax[subplot].set_ylabel('확률 밀도', loc='top')
    ax[subplot].grid('both', zorder=0)
    ax[subplot].fill(np.arange(0, 600), np.exp(log_dens[subplot]), color=pdf_colors[subplot], alpha=0.4)
    ax[subplot].set_xlim(-25, 625)
    ax[subplot].set_ylim(-0.0005, 0.012)
    legend = [mpatches.Patch(color=pdf_colors[subplot], label=pdf_labels[subplot], alpha=0.6)]
    ax[subplot].legend(handles=legend)

fig.tight_layout()
fig.savefig('pdf_comparison_plots.pdf')
```

## 산점도

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

산포도(Scatter plot)는 데이터를 군집화하거나 데이터 집합 내에서 다양한 특성 간의 관계를 찾는 데 매우 유용한 옵션입니다. 아래의 산포도를 살펴보십시오. 이 그래프는 뉴욕의 주택 가격을 면적의 함수로 보여줍니다. (데이터셋은 Kaggle에서 확인 가능합니다):

![scatter plot](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_5.png)


## 데이터 로딩 및 전처리
df_housing = pd.read_csv('NY-House-Dataset.csv')

price = df_housing['PRICE'].to_numpy()
sqft  = df_housing['PROPERTYSQFT'].to_numpy()

mpl.rcParams.update({'font.size': 32})
fig, ax = plt.subplots(figsize=(9,9))
ax.scatter(sqft, price, c='xkcd:cherry', s=12)
ax.set_xscale('log')
ax.set_yscale('log')
ax.set_xlabel('면적 (ft$^{2}$)', loc='right')
ax.set_ylabel('가격 (USD)', loc='top')
fig.tight_layout()
fig.savefig('scatter_plot_example.pdf')


위 예시를 선형-로그 척도로 확인하면 아웃라이어의 존재를 더 쉽게 식별할 수 있지만, 데이터 분포의 퍼짐도를 고려하면 로그-로그 척도가 더 적합합니다. 주택 가격과 총 면적이 지수 함수 관계임을 확인할 수 있어, 산포도가 왜 유용한지 잘 드러나는 사례입니다. 추가로, 2000 평방피트 근처에서 여러 주택 가격이 동일한 것을 볼 수 있습니다. 이는 데이터의 오류일 수도 있고, 더 큰 기저 패턴이 있을 수도 있습니다.

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

Scatter plot을 사용하면 더 많은 데이터나 다른 데이터 세트를 포함시킬 수 있습니다. 예를 들어, 다른 지리적 위치를 다른 포인트 집합으로 추가하거나, 데이터 세트에서 다른 기능을 표시하기 위해 두 번째 x-축이나 y-축을 추가할 수도 있습니다. 아래와 같이 설명되어 있습니다:

![Scatter Plot Image](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_6.png)

```python
mpl.rcParams.update({'font.size': 32})

## 데이터 전처리
bedrooms = df_housing['BEDS'].to_numpy()

fig, ax = plt.subplots(figsize=(9,9))
ax.scatter(sqft, price, c='xkcd:cherry', s=12)
ax.set_yscale('log')
ax.set_xscale('log')
ax.set_xlabel('Area (ft$^{2}$)', loc='right')
ax.set_ylabel('Price (USD)', loc='top')
ax.tick_params(axis='x',  which='both', colors='xkcd:cherry') 
ax.xaxis.label.set_color('xkcd:cherry')
ax.set_title('NY House Prices')

ax2 = ax.twiny() # 두 번째 x-축 추가
ax2.scatter(bedrooms, price, c='xkcd:cerulean', s=12, label='Bedrooms')
ax2.set_xticks(np.arange(0, 60, 10))
ax2.set_xlabel('Bedrooms', loc='right')
ax2.xaxis.label.set_color('xkcd:cerulean')
ax2.minorticks_on()
ax2.tick_params(axis='x', which='both', colors='xkcd:cerulean') 
fig.tight_layout()
fig.savefig('scatter_plot_example_double_x_axes.pdf')
```

두 번째 x-축이 이산적이기 때문에 가장 명확한 예시는 아니지만, 침실 수가 주택 가격의 가장 좋은 예측자가 아님을 추론할 수 있습니다.

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

## 막대 그래프 및 파이 차트

범주형 데이터의 경우, 막대 그래프나 심지어 파이 차트와 같은 플롯 유형이 적합합니다. 그러나 저는 다양한 종류의 데이터를 표시하는 더 많은 유연성을 제공하기 때문에 막대 차트를 선호합니다. 파이 차트는 각 데이터 범주가 전체에 기여하는 비율을 쉽게 시각화하여 해석하기 쉽고 빠르게 만들어주는 독특한 장점을 가지고 있지만, 이해하기 쉽고 빠르게 만들어주는 독특한 장점을 가지고 있습니다. 그러나 막대 차트가 제공하는 다방면의 유연성이 파이 차트의 명료함을 상쇄시킵니다. 아래는 2019 회계연도의 넷플릭스 서비스의 네 개 주요 지역에서 넷플릭스 스트리밍 수익을 보여주는 막대 차트(왼쪽)와 파이 차트(오른쪽)를 고려해 보세요. 해당 데이터셋은 이곳에서 찾을 수 있습니다(Kaggle):

![그림](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_7.png)

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

원 그래프는 전체의 백분율을 두 가지 서로 다른 시각적 표현으로 표시하여 해석이 쉽게 됩니다(백분율로 표시된 것과 전체 원의 기하학적 비율로 표시된 것 두 가지입니다). 이는 빠르게 핵심 데이터 시각화를 제시해야 하는 프리젠테이션에서 더 나은 선택일 수 있습니다. 이에 비해 막대 차트는 각 막대 위에 (10억 달러 단위로) 순수한 수치를 제공하면서 각 막대에서 읽을 수 있는 공통 스케일을 제공하여 양을 기하학적으로 나타냅니다. 이렇게 함으로서 이러한 데이터를 시각화하는 비슷한 방법을 제공합니다. 그러나 플롯의 차원을 늘리고, 예를 들어 여러 해를 표시한다면 막대 차트는 훨씬 더 유용합니다. 다음에 나오는 플롯을 고려해 보십시오. 이 플롯은 여러 해 동안의 스트리밍 수익을 보여줍니다:

![Netflix 스트리밍 수익 (2019-2023)](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_8.png)

번역된 코드:
```js
## 여러 막대 그래프를 위한 데이터 처리
year_separated = {}
for region in total_separated[year].keys():
    temp = []
    for year in total_separated.keys():
        if year == 2024: # 2024년은 1분기만 사용 가능하므로 건너뜁니다
            pass
        else:
            temp.append(total_separated[year][region])
    year_separated[region] = temp

mpl.rcParams.update({'font.size': 32})

막대의 너비     = 0.2
막대의 오프셋    = -0.4
레이블들        = list(region_labels.values())
색상들          = list(region_colors.values())
x_ticks       = np.arange(len(total_separated.keys()) - 1) 
x_tick_labels = [str(year) for year in total_separated.keys()]
x_tick_labels = x_tick_labels[:-1]

fig, ax = plt.subplots(figsize=(10,9))

for region, cnt in zip(year_separated.keys(), np.arange(len(year_separated.keys()))):
    data_scaled = np.array(year_separated[region]) / 1e9
    ax.bar(x_ticks + bar_offset, data_scaled, width=0.2, label=region_labels[region], color=region_colors[region]) 
    ax.set_ylim(0, 20)
    ax.set_xlabel('Region', loc='right') 
    ax.set_ylabel('Revenue (BUSD)', loc='top') 
    ax.yaxis.set_minor_locator(ticker.AutoMinorLocator())
    
    if np.average(data_scaled) >= 8: # 막대의 숫자에 대한 올바른 y-오프셋 설정
        text_yoffset = 1.4
    else:
        text_yoffset = 1.2
        
    for bar in np.arange(x_ticks.shape[0]):
        ax.text(x_ticks[bar] + bar_offset, data_scaled[bar] + text_yoffset, '%.1f' % data_scaled[bar],
                rotation=90, verticalalignment='center',horizontalalignment='center', fontsize=24)

    bar_offset += bar_width

ax.set_xticks(x_ticks, x_tick_labels)
ax.set_title('Netflix 스트리밍 수익 (2019-2023)', fontsize=32, pad=15)
ax.legend(fontsize=24, ncols=2, loc='upper left', columnspacing=0.6)
fig.tight_layout()
fig.savefig('netflix_streaming_revenue_2019_2023.pdf')
```

이것은 원 그래프로는 불가능합니다; 같은 플롯에 여러 원 그래프가 필요할 것이며 —공간의 비효율적 사용입니다!

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

## 라인 플롯

또는 이 데이터 세트를 라인 플롯으로 표시할 수도 있습니다. 라인 플롯을 사용하면 데이터를 보다 세밀하게 살펴볼 수 있습니다:

![line plot](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_9.png)

```js
## 2019년부터 2024년까지 모든 날짜에 대한 지역 데이터 추출
dates = netflix_data['Date']
line_plot_data = {}
for region in region_labels.keys():
    line_plot_data[region] = netflix_data[region].to_numpy()

markers = ['o', 's', 'D', 'v']
region_markers = {region: marker for region, marker in zip(region_labels.keys(), markers)}

mpl.rcParams.update({'font.size': 32})

fig, ax = plt.subplots(figsize=(11,9))

labels        = list(region_labels.values())
colors        = list(region_colors.values())
x_ticks       = np.arange(len(total_separated.keys()) - 1) 
x_tick_labels = [str(year) for year in total_separated.keys()]
x_tick_labels = x_tick_labels[:-1]

for region in line_plot_data.keys():
    ax.plot(dates, line_plot_data[region], color=region_colors[region],
            marker=region_markers[region], label=region_labels[region],
            linewidth=2.2, markersize=7)

ax.set_ylim(0, 4.6e9)
ax.set_xlabel('Region', loc='right') 
ax.set_ylabel('Revenue (USD)', loc='top') 
ax.minorticks_on()
ax.grid(which='major', color='xkcd:cool grey', linestyle='-', alpha=0.7)
ax.grid(which='minor', color='xkcd:light grey', linestyle='--', alpha=0.5)
ax.set_title('넷플릭스 스트리밍 수익', fontsize=32, pad=15)
ax.legend(fontsize=27, ncols=2, loc='upper left', columnspacing=0.6)
fig.tight_layout()
fig.savefig('netflix_streaming_revenue_2019_2023.pdf')
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

## 2D 히스토그램 / 히트맵

이전에 논의한 히스토그램 패밀리의 주의점 중 하나는 2D 히스토그램입니다. 이 플롯 유형은 범주형 데이터의 두 차원을 표시하거나 이미지 데이터를 시각화하는 데 사용할 수 있습니다 [예 : 컴퓨터 비전이나 합성곱 신경망과 작업하고 원시 데이터가 어떻게 보이는지 보고 싶을 때; 편리한 플로팅 라이브러리로 Python Image Library (Pillow) 패키지가 여기에 있습니다]. 아래는 클래식 iris 데이터 집합을 사용한 2D 히스토그램의 예입니다 (Kaggle에서 여기에서 제공).

![2D Histogram Example](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_10.png)

```python
import pandas as pd
import matplotlib.pyplot as plt

df_iris = pd.read_csv('Iris.csv')
sepal_length = df_iris['SepalLengthCm']
sepal_width  = df_iris['SepalWidthCm']

fig, ax = plt.subplots(figsize=(9,9))

hist = ax.hist2d(sepal_length, sepal_width, vmin=0, vmax=10, bins=10)
ax.set_xlabel('Sepal length', loc='right')
ax.set_ylabel('Sepal width', loc='top')

cbar = fig.colorbar(hist[3])
cbar.set_label('Counts', loc='top')
fig.tight_layout()
fig.savefig('hist2d_example.pdf')
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

이 예시에서는 붓꽃의 측정된 꽃받침 길이와 너비의 가장 일반적인 값들을 식별하기 위해 이 2D 히스토그램을 사용할 수 있습니다. 2D 히스토그램/히트맵은 데이터의 밀도 분포를 나타내는 지표가 될 수 있습니다. 이는 3D 히스토그램으로 확장되어 적용될 수 있으며, 3D 히스토그램에서 z-축(2D 바이닝의 높이)은 색상 막대의 기능을 대체합니다.

## 플롯 유형의 테이블

편의상, 위에서 논의된 각 플롯 유형에 대한 데이터 유형, 사용 용도, 장단점을 표로 정리했습니다:

최종적으로는 경험을 쌓고 상황이 발생했을 때 기술적이고 미적인 판단을 내릴 수 있는 능력에 달려 있습니다.

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

# 규칙 1: 면적 당 정보를 극대화하세요

어떤 플롯이든 축(맷플롯립 용어로 "spines"라 불림)의 범위 내에서 제시되는 정보를 최대화하는 것이 목표입니다. 너무 많은 여백은 낭비된 영역을 의미하며, 플롯 생성은 사용 가능한 공간을 효율적으로 사용하는 예술입니다. 아래의 플롯은 이 규칙을 지키지 않은 사례를 보여줍니다 (Kaggle에서 데이터셋 확인 가능):

![Plot Image](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_11.png)

```python
## 데이터 전처리
df_power = pd.read_csv('Power_data.csv')
pressure = df_power['Ambient pressure'].to_numpy()
temp = df_power['Avg temperature'].to_numpy()
humidity = df_power['Relative humidity'].to_numpy()
power = df_power['Net hourly electrical energy output'].to_numpy()

mpl.rcParams.update({'font.size': 32})
fig, ax = plt.subplots(figsize=(11,9))

ax.minorticks_on()
ax.grid(which='major', color='xkcd:cool grey', linestyle='-', alpha=0.7, zorder=0)
ax.grid(which='minor', color='xkcd:light grey', linestyle='--', alpha=0.5, zorder=0)
ax.scatter(temp, power, s=5, label='Temperature ($^{\circ}$C)', c='xkcd:hot pink', zorder=3)
ax.scatter(humidity, power, s=5, label='Relative humidty (%)', c='xkcd:blue purple', zorder=3)
ax.scatter(pressure, power, s=5, label='Pressure (mbar)', c='xkcd:deep sky blue', zorder=3)
ax.set_xlabel('Variable', loc='right')
ax.set_ylabel('Power output (MW)', loc='top')

ax.legend(fontsize=25, markerscale=4, handletextpad=0.3, loc='upper center')
fig.tight_layout()
fig.savefig('power_output_scatter.pdf')
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

서로 다른 변수들 간의 큰 간격은 서로 다른 스케일을 가진 여러 변수를 동일한 플롯/축에 플롯하려고 시도했기 때문에 발생했습니다. 이 경우 x축에 로그 스케일을 적용해도 변화가 없습니다. 왜냐하면 모든 변수의 분포 폭이 충분히 넓지 않기 때문입니다. 세 가지 옵션이 있습니다: (1) 서로 다른 데이터를 세 개의 패널로 분할하고 각 변수를 별도로 표시하거나 (2) 온도와 상대습도를 동일한 플롯에 유지하고 압력 데이터를 자체 축으로 할당하거나 (3) 플롯에 두 번째 x축을 추가합니다. 옵션(1)이 가장 좋습니다. 이는 각 피쳐를 개별 x축에 배치하여 출력과 해당 피쳐 간의 관계를 더 잘 시각화하게 하며 전반적으로 플롯을 이해하기 쉽고 조직적으로 만듭니다.

![Visualization](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_12.png)

```js
mpl.rcParams.update({'font.size': 38})

fig, ax = plt.subplots(1, 3, figsize=(26,12))
for axis in np.arange(3):
    ax[axis].minorticks_on()
    ax[axis].grid(which='major', color='xkcd:cool grey', linestyle='-', alpha=0.7, zorder=0)
    ax[axis].grid(which='minor', color='xkcd:light grey', linestyle='--', alpha=0.5, zorder=0)
    ax[axis].set_ylabel('Power output (MW)', loc='top')
    ax[axis].set_ylim(418, 500)

ax[0].scatter(temp, power, s=5, label='Temperature ($^{\circ}$C)', c='xkcd:hot pink', zorder=3)
ax[1].scatter(humidity, power, s=5, label='Relative humidty (%)', c='xkcd:blue purple', zorder=3)
ax[2].scatter(pressure, power, s=5, label='Pressure (mbar)', c='xkcd:deep sky blue', zorder=3)

ax[0].set_xlabel('Temperature ($^{\circ}$C)', loc='right')
ax[1].set_xlabel('Relative humidty (%)', loc='right')
ax[2].set_xlabel('Pressure (mbar)', loc='right')

ax[0].set_xlim(0, 40)
ax[1].set_xlim(0, 110)

fig.tight_layout()
fig.savefig('power_output_scatter_improved.pdf')
```

# Rule 2: 중요한 축 라벨을 절대 무시하지 마세요.

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

일반적으로 간과되는 경우가 있어 축 레이블을 추가하는 것을 잊을 때가 종종 있습니다. 종종 이는 협업자들이 각 축에 무엇이 플로팅되고 있는지 알고 있거나 추론할 수 있기 때문일 수 있습니다. 그러나 우리의 작업을 비롯한 외부의 관찰자나 우리와 같은 분야의 누군가에게는 축 레이블이 없으면 시각화된 내용을 이해하는 데 필수적인 맥락이 제거될 수 있습니다. 예를 들어, 아래 플롯을 살펴보세요:

![히스토그램](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_13.png)

플롯을 훑어본 빠르게 확인만으로 이것이 확실히 히스토그램이라는 것을 알 수 있으므로 y축에 무엇이 플로팅되고 있는지를 추론할 수 있습니다(회수/빈도). 그러나 x축에는 무엇이 플로팅되고 있는지요? 이것은 다양한 양에 대한 것이 될 수 있으며, 데이터 집합을 사전에 알 경우에만 추론을 시작할 수 있습니다. 협업자들과 같은 내부 사용을 위한 플롯을 보낼 때에도, 즉, 내용을 추측하는 것을 독자에게 맡기면 안 됩니다.

![플롯](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_14.png)

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

다음 반복 작업은 상당히 향상되었습니다. 하지만 독자는 콜레스테롤 수치가 어떤 단위인지 여전히 알지 못합니다. 이 수치는 무게/부피의 어떤 조합일 수 있습니다(예: 마이크로그램/리터, 나노그램/리터 등). 이 중요한 세부 내용을 추가하면 플롯의 최종 버전이 됩니다:

![image](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_15.png)

```js
## 데이터 포맷팅
chol = df.chol.to_numpy()

## 플로팅
matplotlib.rcParams.update({'font.size': 31})
fig, ax = plt.subplots(figsize=(10, 8))

ax.grid('both', zorder=0)
ax.hist(chol, bins=30, edgecolor='k', facecolor='forestgreen', zorder=2)
ax.set_xlim(0, 600)
ax.set_ylim(0, 40)
ax.set_xticks(np.arange(0, 700, 100))
ax.set_xlabel('콜레스테롤 농도 (mg/dl)', loc='right', fontsize=36)
ax.set_ylabel('빈도', loc='top', fontsize=36)
#ax.set_title('히스토그램')
#ax.tick_params(axis='x', which='minor', bottom=True)#, bottom=False)
plt.minorticks_on()
#ax.tick_params(axis='both', which='major', labelsize=4)
#ax.tick_params(axis='both', which='minor', labelsize=2)


fig.tight_layout()
fig.savefig('labeled_hist_1.pdf')
```

물론, 플로팅되는 변수에 단위가 없는 경우 특성 이름을 추가하는 것이 적절합니다. 일부 경우에서 축 레이블의 단위 부분으로 "임의 단위"를 사용하는 것도 허용되지만, 이는 대부분 중복된 추가입니다.

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

# 규칙 3: 가능한 경우 오차 막대 추가하기

일반적으로 오차 막대가 없는 선 그래프를 볼 때 가장 먼저 떠오르는 의문점은 "어디에 오차 막대가 있는 거죠?" 입니다. 가능한 경우 작성자는 반드시 오차 막대를 포함해야 합니다. 물론, 오차 막대가 너무 작아서 마커로 가려지는 경우는 캡션이나 설명과 함께 명시되어야 합니다. 예를 들어, 첫 눈에 왼쪽 그래프에서 독립 변수가 증가함에 따라 두 곡선이 분명히 서로 다르게 변화하는 것을 볼 수 있습니다:

![Error Bars Example](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_16.png)

```js
## 데이터 생성
x_vals   = np.arange(0, 20, 1)
num_vals = x_vals.shape[0] 

y0     = 1.4 * x_vals + (np.random.rand(num_vals) + 3.3)
y1     = 1.6 * x_vals + (np.random.rand(num_vals) + 3.5)
y_data = [y0, y1]

error0     = x_vals * 0.1
error1     = x_vals * 0.28
error_data = [error0, error1]

colors = ['xkcd:azure', 'xkcd:carmine']

mpl.rcParams.update({'font.size': 34})

fig, ax = plt.subplots(1, 2, figsize=(16,9))
for plot in np.arange(2):
    for dataset in np.arange(2):
        if plot == 0:
            ax[plot].plot(x_vals, y_data[dataset], marker='o', color=colors[dataset], label='Dataset %d' % dataset)
        else:
            ax[plot].errorbar(x_vals, y_data[dataset], error_data[dataset], marker='o', color=colors[dataset], label='Dataset %d' % dataset)
    
    ax[plot].set_xlim(-2, 22)
    ax[plot].set_ylim(0, 42)
    ax[plot].set_xlabel('독립 변수', loc='right')
    ax[plot].set_ylabel('종속 변수', loc='top')
    ax[plot].legend(fontsize=30)
    ax[plot].minorticks_on()
    ax[plot].set_xticks(np.arange(0, 25, 5))
    ax[plot].set_yticks(np.arange(0, 45, 5))
    
fig.tight_layout()
fig.savefig('error_bars_example.pdf')
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

그러나 오른쪽 그림에 오차 막대가 있는 그래프를 확인해보면, 이러한 이질성이 꼭 그렇지 않을 수 있음을 알 수 있습니다: x 값이 증가할수록 데이터 집합 1의 오차가 단조롭게 증가합니다. 이로써 두 데이터 집합이 모두 이질적이라는 주장이 의심스러운 것입니다. 이를 덧없이 언급할 때, 오직 조금 더 정교한 통계적 검정 없이 이 관측 결과만으로 (즉, 더 나은 통계 검정 없이) 우리는 이전과 같이 두 선이 모두 이질적임을 결론 지을 수 없습니다.

# 규칙 4: 모든 것의 글꼴 크기를 키우세요

작고 괴롭히는 글꼴 크기의 큰 그림은 성가신 일입니다. 특히 인쇄용이거나 발표용으로 서식이 지정된 그래프를 볼 때는 더욱 그렇습니다. 회의나 회의가 진행될 때 발표를 들으면 눈에 꺼내어 x 및 y축 레이블을 읽기 위해 눈을 잔뜩 까놓아야 할 때 즐거움이 있을 리가 없습니다. 그림의 "전체적인 그림"은 확대하거나 눈을 찌푸리지 않아도 파악할 수 있어야 합니다. 현대 기술과 (규칙 14를 참조하십시오)에서 그림이 적절한 형식으로 저장된 경우에는 크게 문제가 되지 않을 것입니다. 그러나 규칙 14를 깨야 하는 경우에는, 그림이 합리적으로 수용할 수 있는 한 큰 글꼴 크기를 늘리는 것이 가장 좋습니다. 또한, 제 시각적인 의견으로는, 더 큰 글꼴 크기가 더 잘 보인다고 생각합니다. 아래 그림을 고려해보십시오 (Kaggle 데이터 세트는 여기에서 사용 가능합니다):

<img src="/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_17.png" />

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
housing_prices_2 = pd.read_csv('Housing_Price_Data.csv')

price_no_ac_df = housing_prices_2[housing_prices_2['airconditioning'] == 'no']
price_with_ac_df = housing_prices_2[housing_prices_2['airconditioning'] == 'yes']

mpl.rcParams.update({'font.size': 10})  # Matplotlib의 기본 글꼴 크기를 10으로 설정

fig, ax = plt.subplots(figsize=(9, 9))

ax.scatter(price_no_ac_df['area'], price_no_ac_df['price'], c='xkcd:vibrant green', s=12, marker='o', label='에어컨 없음')
ax.scatter(price_with_ac_df['area'], price_with_ac_df['price'], c='xkcd:orangered', s=12, marker='D', label='에어컨 있음')

ax.set_xlabel('면적 (ft$^{2}$)', loc='right')
ax.set_ylabel('가격 (USD)', loc='top')
ax.set_xlim(1000, 20000)
ax.set_ylim(1e6, 2.1e7)
ax.set_yscale('log')
ax.set_xscale('log')
ax.legend(loc='upper left', markerscale=2.4)
fig.tight_layout()
fig.savefig('small_font_example.pdf')
```

이 그래프는 Matplotlib의 글꼴 크기를 기본값 10으로 사용하여 만들었습니다. 눈금과 축 레이블을 읽기 어렵게 만들어 전체적으로 해석하기 어렵게 합니다. 동일한 그래프를 글꼴 크기를 26으로 크게 한 것으로 생각해보세요:

![Large Font Example](https://cdn.url/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_18.png)

```python
mpl.rcParams.update({'font.size': 26})

fig, ax = plt.subplots(figsize=(8, 8))

ax.scatter(price_no_ac_df['area'], price_no_ac_df['price'], c='xkcd:vibrant green', s=12, marker='o', label='에어컨 없음')
ax.scatter(price_with_ac_df['area'], price_with_ac_df['price'], c='xkcd:orangered', s=12, marker='D', label='에어컨 있음')

ax.set_xlabel('면적 (ft$^{2}$)', loc='right')
ax.set_ylabel('가격 (USD)', loc='top')
ax.set_xlim(1000, 20000)
ax.set_ylim(1e6, 2.1e7)
ax.set_yscale('log')
ax.set_xscale('log')
ax.legend(loc='upper left', markerscale=2.4)
fig.tight_layout()
fig.savefig('large_font_example.pdf')
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

독자들이 폰트 크기를 늘려줘서 감사할 거예요.

# Rule 5: 축의 스케일을 적절하게 설정하세요

여러 개 주문에 걸친 데이터는 거의 항상 로그 축을 사용하여 플로팅해야 합니다. 어느 시점에서는, 두 개 이상의 수주에 걸쳐 변환해야 하는 경우도 있을 것입니다. 이는 그래프의 하나, 또는 모든 축에 적용될 수 있습니다. 이 예제에서는 계수 a에 의해 스케일링된 자연 로그 곡선을 그릴 것입니다:

![그림](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_19.png)

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
## 데이터 생성
a_coeff = 1.5

x_data = np.arange(10, 48000, 0.1)
y_data = a_coeff * np.log(x_data)

mpl.rcParams.update({'font.size': 34})

fig, ax = plt.subplots(figsize=(9,8))
ax.plot(x_data, y_data, linewidth=2.4, c='xkcd:indigo')
ax.grid(which='major', color='xkcd:cool grey', linestyle='-', alpha=0.7, zorder=0)
ax.grid(which='minor', color='xkcd:light grey', linestyle='--', alpha=0.5, zorder=0)
ax.set_xlabel('$x$', loc='right')
ax.set_ylabel('$f(x)$', loc='top')
ax.minorticks_on()
ax.set_ylim(0, 20)

props = dict(boxstyle='round', facecolor='xkcd:indigo', alpha=0.3)

ax.text(0.062, 0.81, '$f(x) = a\cdot\mathrm{ln}(x)$\na = 1.5', fontsize=26, bbox=props, transform=ax.transAxes)
fig.tight_layout()
fig.savefig('no_log_example.pdf')
```

x-축이 여러 개의 자릿수에 걸쳐 있어 공간이 효율적으로 사용되지 않는 것을 알 수 있습니다. 그래프의 우측 하단 부분에는 많은 빈 공간이 있습니다. 독립 데이터에 로그 변환을 적용할 수 있는 완벽한 상황입니다. 우리는 이 변환을 직접 계산할 필요가 없습니다. Matplotlib가 내부적으로 처리해 줄 것입니다.

![image](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_20.png)

```js
mpl.rcParams.update({'font.size': 34})

fig, ax = plt.subplots(figsize=(9,8))
ax.grid(which='major', color='xkcd:cool grey', linestyle='-', alpha=0.7, zorder=0)
ax.grid(which='minor', color='xkcd:light grey', linestyle='--', alpha=0.5, zorder=0)
ax.plot(x_data, y_data, linewidth=2.4, c='xkcd:indigo')
ax.set_xlabel('$x$', loc='right')
ax.set_ylabel('$f(x)$', loc='top')
ax.minorticks_on()
ax.set_xscale('log')
ax.set_ylim(0, 20)
ax.set_xlim(5, 9e4)

props = dict(boxstyle='round', facecolor='xkcd:indigo', alpha=0.3) # 텍스트 상자 속성
ax.text(0.062, 0.81, '$f(x) = a\cdot\mathrm{ln}(x)$\na = 1.5', fontsize=26, bbox=props, transform=ax.transAxes)
fig.tight_layout()
fig.savefig('semi_log_example.pdf')
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

x-축에 로그 스케일을 적용하여 사용 가능한 공간을 훨씬 더 효율적으로 활용할 수 있습니다. 최상의 예시는 아니지만, 많은 단위 범위를 다루는 데이터에 대해 로그 축이 더 적합한 이유를 보여줍니다.

# 규칙 6: 보조 눈금 및 그리드 사용

보조 눈금을 사용하면 독자에게 귀중한 도움이 됩니다. 로그-노멀 또는 로그-로그 축을 다룰 때는 그리드가 심사숙고하여 구현될 때 더 유용한 기능이 될 수 있습니다. 아래 예시를 고려해보세요:

![이미지](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_21.png)

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
## 데이터 생성
x_data   = np.arange(0.1, 20.1, 0.1)
exp_data = (1/200) * np.power(x_data, 3)

mpl.rcParams.update({'font.size': 34})
fig, ax=plt.subplots(figsize=(10,8))
ax.plot(x_data, exp_data, linewidth=2, color='xkcd:deep sky blue')
ax.set_xlabel('x',    loc='right', fontsize=40)
ax.set_ylabel('f(x)', loc='top',   fontsize=40)
ax.set_xlim(0, 22)
ax.set_ylim(0, 42)
props = dict(boxstyle='round', facecolor='white', alpha=0.7)
ax.text(0.08, 0.83, "$f(x) = \dfrac{x^{3}{200}$", fontsize=32, bbox=props, transform=ax.transAxes)
fig.tight_layout()
fig.savefig('four_x_cubed_no_minor_ticks.pdf')
```

위의 그림은 x축과 y축이 모두 선형 스케일로 나타난 곡선을 보여줍니다. 그러나 플롯의 왼쪽에 많은 여백이 있어 곡선에서 x 및 y 값을 정확히 파악하기 어렵습니다. 이 그림을 보다 쉽게 읽을 수 있도록 몇 가지 마이너 틱을 추가할 수 있습니다:

<img src="/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_22.png" />

```python
mpl.rcParams.update({'font.size': 34})

fig, ax=plt.subplots(figsize=(10,8))
ax.plot(x_data, exp_data, linewidth=2, color='xkcd:deep sky blue')
ax.set_xlabel('x',    loc='right', fontsize=40)
ax.set_ylabel('f(x)', loc='top',   fontsize=40)
ax.set_xlim(0, 22)
ax.set_ylim(0, 42)
ax.minorticks_on()
props = dict(boxstyle='round', facecolor='white', alpha=0.7)
ax.text(0.08, 0.83, "$f(x) = \dfrac{x^{3}{200}$", fontsize=32, bbox=props, transform=ax.transAxes)
fig.tight_layout()
fig.savefig('four_x_cubed_with_minor_ticks.pdf')
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

이렇게 하면 더 쉬워지지만, 그리드 사용은 시각적 안내에 더 효과적일 수 있습니다. 아래의 두 그래프를 비교해보세요: 왼쪽에는 주 그리드 라인만 표시되고, 오른쪽에는 소 그리드와 주 그리드 라인이 모두 표시됩니다:

![Grid Comparison](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_23.png)

```python
mpl.rcParams.update({'font.size': 42})
props = dict(boxstyle='round', facecolor='white', alpha=0.7)

fig, ax=plt.subplots(1, 2, figsize=(20,10))
ax = ax.ravel()

for plot in np.arange(2):
    ax[plot].plot(x_data, exp_data, linewidth=2, color='xkcd:deep sky blue')
    ax[plot].set_xlabel('x',    loc='right', fontsize=40)
    ax[plot].set_ylabel('f(x)', loc='top',   fontsize=40)
    ax[plot].set_xlim(0, 22)
    ax[plot].set_ylim(0, 42)
    ax[plot].minorticks_on()
    ax[plot].set_xticks(np.arange(0, 25, 5))
    if plot == 0:
        ax[plot].grid()
    else:
        plt.grid(True, which="both")

    ax[plot].text(0.08, 0.83, "$f(x) = \dfrac{x^{3}{200}$", fontsize=38, bbox=props, transform=ax[plot].transAxes)
fig.tight_layout()
fig.savefig('four_x_cubed_with_minor_ticks_major_minor_grid.pdf')
```

오른쪽에 있는 그래프가 우리에게 가장 유용한 정보를 제공합니다: 소 그리드 라인이 시각을 안내하여 곡선 상의 점을 식별하기가 훨씬 쉬워집니다. 그러나 주 그리드 라인과 소 그리드 라인의 선 종류 및 색상을 사용자 정의하는 것으로 한 발짝 더 나아갈 수 있습니다:

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
mpl.rcParams.update({'font.size': 34})


fig, ax=plt.subplots(figsize=(10,8))
ax.plot(x_data, exp_data, linewidth=2, color='xkcd:deep sky blue')
#ax.set_xscale('log')
#ax.set_yscale('log')


ax.set_xlabel('x', loc='right', fontsize=40)
ax.set_ylabel('f(x)', loc='top', fontsize=40)
ax.set_xlim(0, 22)
ax.set_ylim(0, 42)
ax.minorticks_on()
props = dict(boxstyle='round', facecolor='white', alpha=0.7)
ax.text(0.08, 0.83, "$f(x) = \dfrac{x^{3}{200}$", fontsize=32, bbox=props, transform=ax.transAxes)

ax.grid(which='major', color='xkcd:cool grey', linestyle='-', alpha=0.7)
ax.grid(which='minor', color='xkcd:light grey', linestyle='--', alpha=0.5)
fig.tight_layout()
fig.savefig('four_x_cubed_with_minor_ticks_major_minor_grid_custom.pdf')
```

사용자 정의 그리드를 추가하여 주요 그리드 선을 미세 그리드 선보다 강조하는 것은 꽤 차이를 만들 수 있어요!

# Rule 7: Don’t forget the legend

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

뻔한 말이지만, 제가 본 플롯 중에는 이 중요한 요소가 생략된 경우가 많았습니다. 특이한 피처를 가진 플롯의 경우에는 보통 이것이 문제가 되지 않습니다 — 보통 x-축과 y-축 레이블에서 데이터의 신원을 추론할 수 있습니다; 그러나 어쨌든 범례를 추가하는 것이 가장 좋습니다. 이 선택적인 피처는 단일 플롯에 여러 곡선, 산포도 등을 그릴 때 필수적인 요소가 됩니다. 아래의 막대 플롯을 고려해보세요:

![그림](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_25.png)

```js
## 데이터 생성
categories = np.array(['A','B','C','D', 'E']) 
group_a    = np.array([5, 22, 34, 10, 7]) 
group_b    = np.array([15, 4, 22, 8, 12])
group_c    = np.array([30, 10, 9, 17, 23])

mpl.rcParams.update({'font.size': 32})

fig, ax = plt.subplots(figsize=(10,8))
x_ticks   = np.arange(categories.shape[0]) 
bar_width = 0.2

ax.bar(x_ticks - 0.2, group_a, width=0.2, label='Group A', color='xkcd:pumpkin orange') 
ax.bar(x_ticks + 0.0, group_b, width=0.2, label='Group B', color='xkcd:goldenrod') 
ax.bar(x_ticks + 0.2, group_c, width=0.2, label='Group C', color='xkcd:carmine') 

ax.set_xticks(x_ticks, categories) 
ax.set_xlabel('Category', loc='right') 
ax.set_ylabel('Number of ocurrences', loc='top') 

ax.yaxis.set_minor_locator(ticker.AutoMinorLocator())
fig.tight_layout()
fig.savefig('bar_chart_with_legend.pdf')
```

분명하게, 여기에서 다섯 가지 다른 카테고리에 대해 세 개의 개별 데이터셋이 플롯되어 있습니다. 범례가 없으면 이 플롯은 해독할 수 없습니다; 우리는 세 가지 다른 막대 색상이 무슨 의미인지 이해해야 합니다. 범례를 추가하면 마침내 제시된 정보를 해석할 수 있습니다:

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
## 데이터 생성
categories = np.array(['A','B','C','D', 'E']) 
group_a    = np.array([5, 22, 34, 10, 7]) 
group_b    = np.array([15, 4, 22, 8, 12])
group_c    = np.array([30, 10, 9, 17, 23])

mpl.rcParams.update({'font.size': 32})

fig, ax = plt.subplots(figsize=(10,8))
x_ticks   = np.arange(categories.shape[0]) 
bar_width = 0.2

ax.bar(x_ticks - 0.2, group_a, width=0.2, label='Group A', color='xkcd:pumpkin orange') 
ax.bar(x_ticks + 0.0, group_b, width=0.2, label='Group B', color='xkcd:goldenrod') 
ax.bar(x_ticks + 0.2, group_c, width=0.2, label='Group C', color='xkcd:carmine') 

ax.set_xticks(x_ticks, categories) 
ax.set_xlabel('Category', loc='right') 
ax.set_ylabel('Number of ocurrences', loc='top') 
ax.legend(fontsize=24) # 범례 추가
ax.yaxis.set_minor_locator(ticker.AutoMinorLocator())
fig.tight_layout()
fig.savefig('bar_chart_with_legend.pdf')
```

초보용 예제이지만 의도적으로 선택한 것은 명확한 필요성을 보여주는 범례입니다.

마지막으로, 범례가 표시 중인 데이터와 겹치지 않도록 주의하십시오! 이렇게 되면 혼란스러울 수 있습니다(데이터를 얼마나 많이 차지하는지에 따라 다를 수 있음) 그리고 전달하려는 내용을 방해할 수도 있습니다. 플롯 내에 범례를 맞게 배치하도록 축 스케일을 조정하거나 범례 글꼴 크기를 약간 줄이세요(즉, Rule 4을 어기지 마세요!).

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

# 규칙 8: 명확함이 중요합니다

현재 시대에는 그래픽 출판물을 위해 구별 가능한 색상(또는 회색의 음영)을 선택해야 했던 시절은 더 이상 흔하지 않지만 각 데이터가 서로 다른 색상, 표식 및 선 유형(실선, 대시 등)으로 구별되어야 하는 것이 여전히 중요합니다. 서로 겹치지 않거나 쉽게 구별되는 곡선이 있는 그래프의 경우 다른 색상만으로 충분합니다. 내 의견으로는 곡선에 표식이 없을 때 이 전략이 더 잘 작동합니다:

```js
## 데이터 생성
x_data       = np.arange(1, 20, 1)
x_data_gauss = np.arange(1, 20, 0.5)

slope0 = 1.5 
slope1 = 3
slope2 = 6
int0   = 1
int1   = 2
int2   = 3
int3   = 5
int4   = 4
sigma  = 2
scale4 = 1.5
power4 = 1.5
y_data0 = slope0 * x_data + int0
y_data1 = slope1 * x_data + int1
y_data2 = slope2 * x_data + int2
y_data3 = (1000 / (2 * np.pi * np.sqrt(np.power(sigma, 2)))) * np.exp(-1 * (np.power((x_data_gauss - 10), 2) / (2 * np.power(sigma, 2))))
y_data4 = scale4 * np.power(x_data, power4) + int4

mpl.rcParams.update({'font.size': 30})
fig, ax = plt.subplots(figsize=(8,8))

ax.plot(x_data, y_data0,       markersize=7, linewidth=2, color='xkcd:azure',       label='$f(x)=%.1fx + %d$' % (slope0, int0))
ax.plot(x_data, y_data1,       markersize=7, linewidth=2, color='xkcd:carmine',     label='$f(x)=%.1fx + %d$' % (slope1, int1))
ax.plot(x_data, y_data2,       markersize=8, linewidth=2, color='xkcd:blue violet', label='$f(x)=%.1fx + %d$' % (slope2, int2))
ax.plot(x_data_gauss, y_data3, markersize=7, linewidth=2, color='xkcd:true green',  label='$f(x)=80e^{-(x-10)^{2}/8}$')
ax.plot(x_data, y_data4,       markersize=8, linewidth=2, color='xkcd:cyan',        label='$f(x)=%.1fx^{.1f} + %d$' % (scale4, power4, int4))
ax.set_xlabel('x', loc='right')
ax.set_ylabel('f(x)', loc='top')
ax.legend(loc='upper left', fontsize=20)
ax.set_xlim(0, 22)
ax.set_ylim(0, 160)
ax.set_xticks(np.arange(0, 25, 5))
ax.grid(which='major', color='xkcd:cool grey', linestyle='-', alpha=0.5)
ax.grid(which='minor', color='xkcd:light grey', linestyle='--', alpha=0.5)
ax.minorticks_on()
fig.tight_layout()
fig.savefig('legend_example_colors_only_no_markers.pdf')
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

각 색깔은 충분히 다르기 때문에 쉽게 구별할 수 있지만, 이 경우 선 스타일을 변경하는 것도 나쁘지 않습니다. 특히 곡선의 수가 색상과 마커/마커 채움 스타일을 쉽게 구별할 수 있는 한계점에 접근할 때 추가가 가치가 있습니다.

내 의견으로는 각 곡선에 동일한 마커를 추가하면 플롯이 혼란스러워집니다. 색상은 다르지만 그렇습니다.

```js
mpl.rcParams.update({'font.size': 30})
fig, ax = plt.subplots(figsize=(8,8))

ax.plot(x_data, y_data0,       marker='o', markersize=7, linewidth=2, color='xkcd:azure',       label='$f(x)=%.1fx + %d$' % (slope0, int0))
ax.plot(x_data, y_data1,       marker='o', markersize=7, linewidth=2, color='xkcd:carmine',     label='$f(x)=%.1fx + %d$' % (slope1, int1))
ax.plot(x_data, y_data2,       marker='o', markersize=8, linewidth=2, color='xkcd:blue violet', label='$f(x)=%.1fx + %d$' % (slope2, int2))
ax.plot(x_data_gauss, y_data3, marker='o', markersize=7, linewidth=2, color='xkcd:true green',  label='$f(x)=80e^{-(x-10)^{2}/8}$')
ax.plot(x_data, y_data4,       marker='o', markersize=8, linewidth=2, color='xkcd:cyan',        label='$f(x)=%.1fx^{.1f} + %d$' % (scale4, power4, int4))
#ax.plot(x_data, y_data1, marker='s', color='xkcd:carmine')
ax.set_xlabel('x', loc='right')
ax.set_ylabel('f(x)', loc='top')
ax.legend(loc='upper left', fontsize=20)
ax.set_xlim(0, 22)
ax.set_ylim(0, 160)
ax.set_xticks(np.arange(0, 25, 5))
ax.grid(which='major', color='xkcd:cool grey', linestyle='-', alpha=0.5)
ax.grid(which='minor', color='xkcd:light grey', linestyle='--', alpha=0.5)
ax.minorticks_on()
fig.tight_layout()
fig.savefig('legend_example_colors_only.pdf')
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

마커와 채우기 스타일(또는 선 스타일)을 다르게 설정하면 도표를 더 쉽게 읽을 수 있습니다:

![Plot Image](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_29.png)

```js
mpl.rcParams.update({'font.size': 30})
fig, ax = plt.subplots(figsize=(8,8))

ax.plot(x_data, y_data0, marker='o', markersize=7, linewidth=2, color='xkcd:azure', label='$f(x)=%.1fx + %d$' % (slope0, int0))
ax.plot(x_data, y_data1, marker='s', markersize=7, linewidth=2, color='xkcd:carmine', label='$f(x)=%.1fx + %d$' % (slope1, int1))
ax.plot(x_data, y_data2, marker='^', markersize=8, linewidth=2, color='xkcd:blue violet', label='$f(x)=%.1fx + %d$' % (slope2, int2))
ax.plot(x_data_gauss, y_data3, marker='D', markersize=7, linewidth=2, color='xkcd:true green', label='$f(x)=80e^{-(x-10)^{2}/8}$', fillstyle='none',)
ax.plot(x_data, y_data4, marker='P', markersize=8, linewidth=2, color='xkcd:cyan', label='$f(x)=%.1fx^{.1f} + %d$' % (scale4, power4, int4), fillstyle='none',)
ax.set_xlabel('x', loc='right')
ax.set_ylabel('f(x)', loc='top')
ax.legend(loc='upper left', fontsize=20)
ax.set_xlim(0, 22)
ax.set_ylim(0, 160)
ax.set_xticks(np.arange(0, 25, 5))
ax.grid(which='major', color='xkcd:cool grey', linestyle='-', alpha=0.5)
ax.grid(which='minor', color='xkcd:light grey', linestyle='--', alpha=0.5)
ax.minorticks_on()
fig.tight_layout()
fig.savefig('legend_example_colors_markers.pdf')
```

# Rule 9: 일관성은 중요합니다

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

슬라이드 세트, 학회 포스터 또는 저널 논문에 서로 다른 스타일의 플롯이 포함되어 있는 것은 이상하게 보일 수 있습니다. 이겈 엄밀하게 보일 수 있지만, 일관된 스타일을 유지하는 것은 플롯 간의 연속성을 유지하고 작업을 보다 통일되게 보이게 도와줍니다. 이 스타일은 상황에 따라 달라질 수 있습니다. 예를 들어 일부 저널은 플롯(그리고 테이블 등)을 특정 형식으로 선호할 수 있습니다. 또는 많은 과학적 협업에서는 특정한 플롯 스타일이 논문을 저널에 제출하기 위해 요구될 수 있습니다. 더불어, 여러분의 작업이 더 유요하게 보이게 하고 발표 자료들 사이의 일관된 스타일을 유지하기 위해 도움이 됩니다. 그러므로, 여러분만의 플롯 스타일을 개발하거나 요구된 스타일을 채택한다면 작업의 일관성을 높일 수 있습니다.

# Rule 10: 과적합에 주의하세요

이것은 Rule 1과 모순된 것처럼 보일 수도 있지만, 모든 것은 균형을 이루어야 합니다. 하나의 플롯 용량은 무한하지 않습니다. 너무 많은 데이터나 텍스트 상자와 같은 부착물을 추가하면 메시지가 혼란스러워질 수 있습니다. 이러한 데이터를 서브플롯으로 분리하는 것은 그림의 가독성을 높이는 데 도움이 될 뿐입니다. 아래의 플롯은 이 효과를 완벽히 보여줍니다:

<img src="/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_30.png" /> 

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

플롯의 혼란이 x ≥ 30에서 해소되지만 이 범위 아래의 값들은 데이터 포인트와 교차하는 선들이 꼬리를 치는 메시가 됩니다. 그 결과 플롯의 메시지가 흐려지게 되죠. 다음 규칙(규칙 11)에서 말했듯이, 데이터에 맞는 함수는 적합한 매개변수, 그 오차, 적합도 측정값을 나열한 텍스트 상자와 함께 사용되어야 합니다. 위의 플롯에서는 이러한 텍스트 상자를 넣을 공간이 없어서 플롯의 x 또는 y 한계를 불필요하게 연장시키게 되며, 결과적으로 규칙 13(“텍스트 상자를 절약하여 사용”)를 위반합니다. 이 문제를 해결하기 위해, 데이터셋과 해당 적합선을 개별 부분 그림으로 나누면 됩니다.

여기에서 각 데이터셋이 명확하게 분리되어 있으며, 범례도 덜 복잡하며, 선형 최적 적합선과 일치하는 텍스트 상자가 추가되어 그림을 더 잘 정리하고 결과적으로 플롯을 훨씬 더 읽기 쉽게 만들 수 있습니다.

# 규칙 11: 적합 매개변수를 요약하는 텍스트 상자 추가하기

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

텍스트 상자는 플롯에 완벽한 보충물이 될 수 있어요. 예를 들어, 적합이 적용된 데이터는 항상 적합 매개변수를 설명하는 텍스트 상자를 가져야 합니다. 또한 적합의 품질에 관한 어떤 측정 항목(예: 결정 계수, R², 또는 카이 제곱 통계의 자유도 수에 대한 비율, χ²/dof)이 표시돼야 해요.

아래에 표시된 것처럼:

![이미지](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_32.png)

```python
## 적합 함수 정의
def func(x_data, m_val, b_val):
    return (m_val * x_data) + b_val
    
popt, pcov = curve_fit(func, x_data, y_data) # 함수 적합

## 결정 계수 계산
res          = y_data - func(x_data, *popt) # 잔차 계산
sum_res_sq   = np.sum(np.power(res, 2)) # 잔차 제곱의 합
sum_sq_total = np.sum(y_data - np.power(np.mean(y_data), 2))
r_squared    = 1 - (sum_res_sq / sum_sq_total) # 결정 계수 계산

mpl.rcParams.update({'font.size': 32})
props = dict(boxstyle='round', facecolor='xkcd:scarlet', alpha=0.3)

fig, ax = plt.subplots(figsize=(9, 8))
ax.minorticks_on()
ax.grid('both', zorder=0)
...
(fig 설정 및 플롯)

ax.text(0.085, 0.75, "$f(x) = mx + b$\n$m = %.3f\pm%.3f$\n$b = %.3f\pm%.3f$\n$R^{2} = %.2f$" % (popt[0], pcov[0, 0], popt[1], pcov[1,1], r_squared), fontsize=22, bbox=props, transform=ax.transAxes)
fig.tight_layout()
fig.savefig('linear_fit_example.pdf')
```

이 규칙은 히스토그램에서도 유용할 수 있어요. 종종 관련 통계 정보(평균, 표준 편차, 중앙값, 총 항목 수 등)를 요약하는 텍스트 상자를 추가하는 것이 매우 도움이 될 수 있어요.

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

# Rule 12: 동시에 플롯을 비교할 때는 동일한 축 척도를 사용하세요

데이터 시각화의 목표가 서로 다른 데이터 세트를 비교하는 것이라면, 이러한 데이터들을 동일한 축 척도를 사용하여 플로팅하는 것이 매우 중요합니다. 아래의 플롯을 살펴보세요. 이 플롯은 남성과 여성 콜레스테롤 분포의 확률 밀도 함수(PDFs)를 비교합니다 (Rule 0에서 사용된 'heart' 데이터셋):

![PDF Comparison Plots](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_33.png)

```js
## 데이터 전처리
log_dens   = [log_dens_m, log_dens_f]
pdf_colors = ['forestgreen', 'xkcd:cerulean']
pdf_labels = ['남성', '여성']

mpl.rcParams.update({'font.size': 28})
fig, ax = plt.subplots(1, 2, figsize=(15, 8))

for subplot in np.arange(2):
    ax[subplot].minorticks_on()
    ax[subplot].grid(which='major', color='xkcd:cool grey', linestyle='-', alpha=0.7)
    ax[subplot].grid(which='minor', color='xkcd:light grey', linestyle='--', alpha=0.5)
    ax[subplot].plot(np.arange(0, 600), np.exp(log_dens[subplot]), color=pdf_colors[subplot], linewidth=2)
    ax[subplot].set_xlabel('콜레스테롤 농도 (mg/dl)', loc='right')
    ax[subplot].set_ylabel('확률 밀도', loc='top')
    ax[subplot].grid('both', zorder=0)
    ax[subplot].fill(np.arange(0, 600), np.exp(log_dens[subplot]), color=pdf_colors[subplot], alpha=0.4)
    ax[subplot].set_xlim(-25, 625)
    
    legend = [mpatches.Patch(color=pdf_colors[subplot], label=pdf_labels[subplot], alpha=0.6)]
    ax[subplot].legend(handles=legend)

fig.tight_layout()
fig.savefig('pdf_comparison_plots_different_scales.pdf')
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

각 서브플롯의 y-축이 공통 범위를 공유하지 않기 때문에 두 PDF를 직접적으로 일대일로 비교하기 어렵습니다. 이 문제를 수정함으로써 이제 두 서브플롯을 더 쉽게 비교할 수 있습니다.

![이미지](/assets/img/2024-07-01-HowtoMakeYourDataVisualizationsTellaStory_34.png)

# 규칙 13: 텍스트 상자를 절약해서 사용하세요

이 규칙은 텍스트 상자를 과하게 사용하지 않도록 미세한 알림입니다. 이 규칙은 실제로 규칙 10("플롯을 너무 가득 채우지 마세요")의 확장성에 불과합니다. 일부 데이터셋과 플롯에서는 나란히 플로팅된 데이터셋의 매개변수/서술자를 포함하는 여러 텍스트 상자를 추가하는 것이 유용할 수 있지만, 공간이 제한되어 있거나 플롯이 이미 균형을 이루고 있는 경우, 추가 텍스트 상자를 맞추기 위해 축을 늘리지 마세요. 해당 정보는 그림 설명에 추가할 수 있습니다. 예를 들어, 규칙 10의 첫 번째 예제에 모든 세 맞춤 매개변수에 대한 텍스트 상자를 추가하는 것은 부적절합니다.

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

# 규칙 14: 플롯을 .pdf 파일로 저장하세요

이것은 제 개인적인 짜증 나는 점 중 하나 입니다. .png, .jpg, .gif 또는 .tiff와 같은 형식은 래스터(비트맵) 이미지 형식의 가족에 속하며 일반적으로 인치당 도트 (dpi) 또는 픽셀당 인치 (ppi)로 지정된 해상도를 사용하여 이미지를 구성합니다. 이는 확대할 때 픽셀의 난리를 볼 수 있다는 불행한 상황으로 이어집니다. 반면에 .pdf, .svg 또는 .eps와 같은 벡터 그래픽 형식은 정보를 객체로 저장합니다 (예: 객체와 공간 상의 위치에 대한 설명). 결과적으로 복잡한 플롯에서 정보를 추출하는 것이 훨씬 쉬워지기 때문에 무한대로 확대할 수 있습니다. 대부분의 플롯이 어차피 컴퓨터에서 볼텐데, 사용자가 무한대로 확대할 수 있는 자유를 허용하는 것이 좋지 않을까요?

# 요약 및 결론

이 기사를 쓰기 시작했을 때는 훨씬 짧을 것이라고 생각했지만, 이러한 규칙과 그와 관련된 예제에 대해 더 많이 쓰고 생각한 후에는 이 글이 완전한 변론으로 번지게 되었습니다. 이렇게까지 오신 여러분께 진심으로 감사드리며, 이 금구가이나 그 예제들에서 의미 있는 정보를 추출할 수 있었으면 좋겠습니다. 제가 준수하는 데이터 시각화 규칙이나 일반 원칙을 빠뜨렸다면, 댓글을 남겨 알려주세요!

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

요약하면, 아래 규칙을 나열합니다:

규칙 0: 데이터 유형을 고려해야 합니다.
규칙 1: 면적 당 정보를 극대화해야 합니다.
규칙 2: 중요한 축 레이블을 무시해서는 안 됩니다.
규칙 3: 가능한 경우 오차 막대를 추가해야 합니다.
규칙 4: 모든 것의 글꼴 크기를 키워야 합니다.
규칙 5: 축 스케일을 적절히 설정해야 합니다.
규칙 6: 작은 눈금선과 격자를 사용해야 합니다.
규칙 7: 범례를 잊어서는 안 됩니다.
규칙 8: 명확성이 매우 중요합니다.
규칙 9: 일관성이 필수입니다.
규칙 10: 지나치게 혼잡해지지 않도록 주의해야 합니다.
규칙 11: 적합 매개변수를 요약하는 텍스트 상자를 추가해야 합니다.
규칙 12: 그림을 제품 측면에 표시할 때 동일한 축 스케일을 사용해야 합니다.
규칙 13: 텍스트 상자를 절약해서 사용해야 합니다.
규칙 14: 그림을 .pdf 파일로 저장해야 합니다.

이 기사를 좋아하셨다면 더 많이 보시고 싶다면, 저를 팔로우하시고 이메일 알림을 받아보시는 것을 고려해 주세요!