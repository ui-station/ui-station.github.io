---
title: "에어비앤비 브랜도미터 인공지능을 활용한 소셜 미디어 데이터에서의 브랜드 인식 측정하기"
description: ""
coverImage: "/assets/img/2024-05-20-AirbnbBrandometerPoweringBrandPerceptionMeasurementonSocialMediaDatawithAI_0.png"
date: 2024-05-20 20:19
ogImage: 
  url: /assets/img/2024-05-20-AirbnbBrandometerPoweringBrandPerceptionMeasurementonSocialMediaDatawithAI_0.png
tag: Tech
originalTitle: "Airbnb Brandometer: Powering Brand Perception Measurement on Social Media Data with AI"
link: "https://medium.com/airbnb-engineering/airbnb-brandometer-powering-brand-perception-measurement-on-social-media-data-with-ai-c83019408051"
---


<img src="/assets/img/2024-05-20-AirbnbBrandometerPoweringBrandPerceptionMeasurementonSocialMediaDatawithAI_0.png" />

소셜 미디어 플랫폼에서 브랜드 인식을 어떻게 딥 러닝으로 정량화하는지에 대해 알아보겠습니다.

작성자: Tiantian Zhang, Shuai Shao (Shawn)

# 소개

<div class="content-ad"></div>

에어비앤비에서는 소셜 미디어 데이터를 기반으로 브랜드 인식을 이해하는 최첨단 자연어 이해(NLU) 기술인 Brandometer를 개발했습니다.

브랜드 인식은 고객들이 기업에 대한 일반적인 감정과 경험을 의미합니다. 브랜드 인식을 계량화하는 것은 매우 어려운 과제입니다. 전통적으로 우리는 고객 설문 조사를 의존하여 고객들이 회사에 대해 어떻게 생각하는지를 파악합니다. 이러한 질적 연구의 단점은 샘플링 편향과 데이터 규모의 제한입니다. 반면에 소셜 미디어 데이터는 사용자들이 자신의 경험을 공유하는 최대 소비자 데이터베이스이며, 브랜드 인식을 포착하는 데 이상적인 보완적인 소비자 데이터입니다.

Brandometer는 동시성 추출 및 카운트 기반 상위 관련 주제를 포함하는 전통적인 방법과 비교하여 단어 임베딩을 학습하고 임베딩 거리를 활용하여 브랜드 인식의 관련성을 측정합니다(예: '소속', '연결', '신뢰할 수 있는'). 단어 임베딩은 실수 값 벡터 형식으로 단어를 표현하며, 단어의 의미와 관련성을 효과적으로 유지하는 데 우수한 성과를 보입니다. 심층 신경망에서 얻은 단어 임베딩은 NLU 분야에서 가장 인기 있는 진화된 접근법 중 하나로 여겨집니다. 우리는 Word2Vec 및 FastText와 같은 전형적인 알고리즘부터 최신 언어 모델인 DeBERTa까지 다양한 단어 임베딩 모델을 탐색하고, 신뢰할 수 있는 브랜드 인식 점수를 생성하는 측면에서 이를 비교했습니다.

단어로 표현된 개념에 대해, 해당 임베딩과 "에어비앤비"의 임베딩 간의 유사성을 사용하여 개념이 에어비앤비 브랜드에 대해 얼마나 중요한지를 측정합니다. 이는 인식 점수라고 불리는 것으로, 브랜드 인식은 에어비앤비와 특정 키워드 간의 코사인 유사성으로 정의됩니다:

<div class="content-ad"></div>

이미지 태그를 Markdown 형식으로 변경해주세요.

```markdown
![image](https://miro.medium.com/v2/resize:fit:602/1*fGrdGlIidRgdt6jT0XavYg.gif)

where

![image](https://miro.medium.com/v2/resize:fit:662/1*cB95joQHnMYOsbcrrIWLMQ.gif)

이 블로그 글에서는 소셜 미디어 데이터를 어떻게 처리하고 이해하는지, 딥러닝을 통해 브랜드 인식을 파악하고 코사인 유사성을 보정된 브랜도미터 지표로 '변환'하는 방법을 소개하겠습니다. 또한 브랜도미터 지표로부터 얻은 통찰을 공유할 예정입니다.
```

<div class="content-ad"></div>

# 브랜도미터 방법론

## 문제 설정 및 데이터

소셜 미디어에서 브랜드 인식을 측정하기 위해, 우리는 19개 플랫폼(X - 이전에는 트위터, 페이스북, 레딧 등으로 알려졌음)에서 모든 Airbnb 관련 언급을 분석하고 최첨단 모델로 단어 임베딩을 생성했습니다.

브랜드 인식을 측정하기 위해 의미 있는 단어 임베딩을 생성하기 위해 소셜 미디어 데이터를 활용할 때, 두 가지 문제를 극복했어요:

<div class="content-ad"></div>

- 품질: 소셜 미디어 게시물은 대부분 사용자가 생성하며 상태 공유 및 리뷰와 같은 다양한 콘텐츠가 포함되어 있어 매우 시끄러울 수 있습니다.
- 양: 소셜 미디어 게시물의 희소성은 또 다른 도전입니다. 특정 활동 및 이벤트에 대한 소셜 미디어 사용자의 데이터 생성에 일정 시간이 소요되므로 월 별 롤링 창은 신속성과 감지 가능성 사이의 균형을 유지합니다. 월별 데이터셋은 일반적인 좋은 품질의 단어 임베딩을 훈련하는 데 사용되는 일반적인 데이터셋과 비교할 때 상대적으로 작습니다 (약 2천만 단어). 사전 훈련된 모델에서의 웜 스타트는 도메인 내 데이터가 학습된 임베딩을 거의 변경하지 않아서 도움이 되지 않았습니다.

데이터 품질을 향상시키기 위해 여러 데이터 정리 프로세스를 개발했습니다. 동시에 단어 임베딩 품질에 영향을 미치는 데이터 양과 품질을 완화하기 위해 모델링 기술을 혁신했습니다.

데이터 외에도 브랜드 인식 점수를 신뢰할 수 있는 것으로 만들기 위해 다양한 단어 임베딩 훈련 기술을 탐색하고 비교했습니다.

## Word2Vec

<div class="content-ad"></div>

Word2Vec은 2013년 이후로 가장 간단하면서도 널리 사용되는 단어 임베딩 모델 중 하나입니다. 우리는 Gensim을 사용하여 CBOW 기반 Word2Vec 모델을 구축했습니다. Word2Vec은 도메인 내 단어 임베딩을 어느 정도 만들어내고, 무엇보다도 유추 개념을 생성했습니다. 우리의 도메인별 단어 임베딩에서는 Airbnb 도메인의 유추를 포착할 수 있었습니다. "호스트" - "제공" + "손님" ~= "필요", "도시" - "쇼핑몰" + "자연" ~= "공원"과 같은 예시가 있습니다.

## FastText

FastText는 단어의 내부 구조를 고려하며, 어휘에 없는 단어나 더 작은 데이터셋에 대해 더 견고합니다. 또한 Sense2Vec에서 영감을 받아 우리는 단어를 감정(즉, 긍정적, 부정적, 중립적)과 연관시켜 브랜드 인식 개념을 감정 수준에서 형성합니다.

## DeBERTa

<div class="content-ad"></div>

최근의 transformer 기반 언어 모델(예: BERT)의 발전은 문맥화된 단어 임베딩을 생성하는 장점으로 NLU 작업의 성능을 현저히 향상시켰습니다. 우리는 DeBERTa 기반 단어 임베딩을 개발했는데, 이는 작은 데이터셋에서 더 잘 작동하며, 분리된 어텐션 메커니즘을 통해 주변 컨텍스트에 더 많은 주의를 기울입니다. 우리는 Transformer를 사용하여 모든 것을 처음부터 훈련시켰고, 연결된 마지막 어텐션 레이어 임베딩이 우리 경우에 가장 좋은 단어 임베딩으로 나타났습니다.

## 브랜드 인식 점수 안정화 및 보정

단어 임베딩의 변동성은 널리 연구되어 왔습니다(Borah, 2021). 그 원인은 딥러닝 모델의 기저 확률적 성격(예: 단어 임베딩의 임의 초기화, 지역 최적화로 이어지는 임베딩 학습)부터 데이터 말뭉치의 양과 질이 시간에 따라 변하는 것에 이르기까지 다양합니다.

Brandometer를 사용할 때 임베딩 간의 변동성을 줄여 안정적인 시계열 추적을 생성해야 합니다. 안정적인 임베딩 거리는 시계열 데이터에 존재하는 고유한 패턴과 구조를 보존하는 데 도움이 되며, 따라서 추적 프로세스의 예측 가능성을 향상시킵니다. 게다가, 이는 노이즈 국면에 강건한 추적 프로세스를 만들어냅니다. 우리는 영향을 미치는 요소를 연구하고 변동성을 줄이기 위해 다음 단계를 취했습니다:

<div class="content-ad"></div>

- 부트스트랩 샘플링을 사용하여 반복적인 훈련을 통한 점수 평균화
- 순위 기반 인식 점수

각 달의 데이터마다 동일한 초매개변수를 가진 N개의 모델을 훈련시켜 N개의 인식 점수 평균을 각 개념의 최종 점수로 삼았습니다. 한편, 각 모델이 월별로 동일한 수의 데이터 포인트에서 반복할 수 있도록 업샘플링을 수행했습니다.

변동성을 다음과 같이 정의했습니다:

![이미지](https://miro.medium.com/v2/resize:fit:922/1*pJeFFkML9OLgYyYVAChJIA.gif)

<div class="content-ad"></div>

<table>
    <img src="https://miro.medium.com/v2/resize:fit:668/1*O9bfbXD2k2yCGHAl0LmaXg.gif" />
</table>

CosSim(w)은 방정식 1에 정의된 코사인 유사도 기반 인식 점수를 나타내며, A는 알고리즘을 나타내고, M은 시간 창(즉, 월)을 나타내며, V는 어휘를 나타내며, |V|는 어휘 크기를 나타내며, n은 반복적으로 훈련된 모델의 수를 나타냅니다. 

N이 30에 가까워질수록 점수 변동 값은 수렴하여 좁은 간격 내에 안정화됩니다. 따라서 우리는 모든 것에 대해 N = 30을 선택했습니다.

<div class="content-ad"></div>

```markdown
![Image 1](/assets/img/2024-05-20-AirbnbBrandometerPoweringBrandPerceptionMeasurementonSocialMediaDatawithAI_1.png)

![Image 2](/assets/img/2024-05-20-AirbnbBrandometerPoweringBrandPerceptionMeasurementonSocialMediaDatawithAI_2.png)

마리아 안토니악의 작업을 바탕으로, 우리는 단어 임베딩의 안정성을 측정하기 위해 가장 가까운 이웃들 간의 중첩을 사용했습니다. 상대적 거리가 하류 작업에서 절대 거리 값보다 중요하기 때문입니다. 따라서 유사성 기반 점수보다 안정성이 큰 순위 기반 점수를 개발했습니다.

각 단어에 대해, 우리는 먼저 코사인 유사도를 내림차순으로 순위를 매겼습니다(Eq. 1). 순위 기반 유사성 점수는 그 후 1/rank(w)로 계산되며 여기서 w∈V입니다. 더 관련 있는 개념일수록 순위 기반 인식 점수가 높아집니다.
```  

<div class="content-ad"></div>

점수 변동성은 등식 2의 Variability(A, M, V)와 동일하게 정의되지만 RankSim(w)는 순위 기반 지각 점수를 나타냅니다. 순위 기반 점수로, N이 30에 접근할 때, DeBERTa의 경우 특히 점수 변동성 값이 훨씬 좁은 간격으로 수렴합니다.

![이미지](https://miro.medium.com/v2/resize:fit:694/1*WTrXi2M45e5zIDJg8gl0GA.gif)

![이미지](/assets/img/2024-05-20-AirbnbBrandometerPoweringBrandPerceptionMeasurementonSocialMediaDatawithAI_3.png)

<div class="content-ad"></div>

![image](/assets/img/2024-05-20-AirbnbBrandometerPoweringBrandPerceptionMeasurementonSocialMediaDatawithAI_4.png)

## 디자인된 측정 항목에 따른 점수 출력 선택

이 프로젝트의 한 가지 어려움은 브랜드 인식에 대한 객관적인 '진실'이 없기 때문에 어떤 점수 출력이 더 나은지 결론을 내릴 수 있는 단순하고 궁극적인 방법이 없다는 것이었습니다. 대신, 점수의 특성을 학습하기 위해 새로운 메트릭을 정의했습니다.

다양한 기간을 통한 평균 분산 (AVADP)

<div class="content-ad"></div>

- 먼저 에어비앤비의 대한 상위 관련 브랜드 인식 그룹으로 '호스트', '휴가', '임대', '사랑', '머무름', '집', '예약', '여행', '손님'을 선정했습니다.
- 높은 값은 서로 다른 기간에 걸쳐 변동성이 더 큼을 나타내며, 이는 선택된 브랜드 인식이 상대적으로 안정적이라고 가정되므로 월별로 크게 변동하지 않아야 한다는 것을 의미합니다.

![AirbnbBrandometerPoweringBrandPerceptionMeasurementonSocialMediaDatawithAI_5](/assets/img/2024-05-20-AirbnbBrandometerPoweringBrandPerceptionMeasurementonSocialMediaDatawithAI_5.png)

![AirbnbBrandometerPoweringBrandPerceptionMeasurementonSocialMediaDatawithAI_6](/assets/img/2024-05-20-AirbnbBrandometerPoweringBrandPerceptionMeasurementonSocialMediaDatawithAI_6.png)

<div class="content-ad"></div>

위의 교정된 결과를 바탕으로 이 통계를 확인했습니다. 랭크 기반 점수가 유사성 기반 점수에 비해 우승자인 것을 확인할 수 있습니다:

- 낮은 AVADP: 다른 기간에 비해 비순위 평가간의 변동이 더 자주 일어납니다 — 선택한 브랜드 인식이 비굴한 것으로 가정되므로 월별로 크게 변하지 않아야 하는 것으로 생각됩니다.

# 브랜도미터의 사용 사례

브랜드 측정 문제를 해결하려고 시작했지만, 사용 사례는 그 이상으로 확장될 수 있다고 믿습니다:

<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-AirbnbBrandometerPoweringBrandPerceptionMeasurementonSocialMediaDatawithAI_7.png" />

## 사용 사례 심층 분석

산업 분석: 핵심 플레이어들 중 최고의 브랜드 인식 [월간 최고 인식]

에어비앤비는 "Stay"와 "Home"과 같은 최고 인식을 통해 "소속감"이라는 브랜드 이미지를 제공하며, 우리의 미션 성명과 독특한 공급 재고를 반영합니다. 다른 회사들은 "Rental", "Room", "Booking"과 같이 기능성을 설명하는 것이 아닌 인간적 감각이 아닌 기능을 설명하고 있습니다.

<div class="content-ad"></div>

```markdown
![이미지](/assets/img/2024-05-20-AirbnbBrandometerPoweringBrandPerceptionMeasurementonSocialMediaDatawithAI_8.png)

Top Emerging Perception은 월간 상위 인식에서 온라인에서 논의되는 중요한 이벤트를 보여줍니다.

상위 10가지 인식은 일반적으로 매월 안정적입니다. 최상위 인식에는 Home, Host, Stay, Travel, Guest, Rental 등이 포함됩니다.
```

<div class="content-ad"></div>

한편, 우리는 Brandometer를 사용하여 최상위 목록으로 올라가는 신흥 인식을 모니터링합니다. 이는 브랜드나 사용자 선호도 변화와 관련된 주요 이벤트를 반영할 수 있습니다.

주요 캠페인 모니터링(시계열 추적)

기업은 제품을 홍보하고 브랜드 이미지를 확장하기 위해 캠페인을 만듭니다. 한 관련 캠페인 이후에 특정 브랜드 테마의 인식 변화를 포착할 수 있었습니다.

![이미지](/assets/img/2024-05-20-AirbnbBrandometerPoweringBrandPerceptionMeasurementonSocialMediaDatawithAI_9.png)

<div class="content-ad"></div>

이러한 사용 사례는 시작에 불과합니다. 기본적으로 이것은 커뮤니티의 요구 사항과 인식을 배우는 과정에서 대규모 온라인 의견을 수집하는 혁신적인 방법입니다. 저희는 이러한 통찰을 어떻게 활용하여 계속해서 에어비앤비 경험을 향상시키는지에 대해 지속적으로 고민하고 반성할 것입니다.

# 다음 단계

에어비앤비의 혁신적인 브랜도미터는 이미 소셜 미디어 데이터로부터 브랜드 인식을 성공적으로 캡처해왔습니다. 향후 개선 방향은 다음과 같습니다:

- 보다 명확하고 간결한 통찰을 위한 더 나은 콘텐츠 세분화.
- 소셜 미디어 브랜드 인식을 반영하는 더 많은 지표 개발.
- Airbnb뿐만 아니라 동일 시장 세그먼트의 다른 기업들에 대한 데이터 기반 강화하여 포괄적인 통찰을 얻기.

<div class="content-ad"></div>

만약 이런 종류의 작업이 매력적으로 들린다면, 오픈된 역할들을 확인해보세요 - 우리는 채용 중이에요!

## 감사의 말씀

Airbnb 브랜더미터를 향상시키고 완성하는 데 최고의 아이디어를 제공해준 Mia Zhao, Bo Zeng, Cassie Cao에게 감사드립니다. 사회적 미디어 데이터 통합을 지지해준 Jon Young, Narin Leininger, Allison Frelinger에게 감사드립니다. 피드백과 제안을 해주신 Linsha Chen, Sam Barrows, Hannah Jeton, Irina Azu에게 감사드립니다. 블로그 글의 내용을 검토하고 다듬는 데 도움을 준 Lianghao Li, Kelvin Xiong, Nathan Triplett, Joy Zhang, Andy Yasutake에게 감사드립니다. 리더십 지원에 감사드리기 위해 Joy Zhang, Tina Su, Andy Yasutake에게 감사드립니다!

특별히 아이디어를 시작해준 Joy Zhang에게 모든 영감을 주고 계속된 지도와 지원에 대해 특별히 감사드립니다!