---
title: "ML, 데이터 팀을 위한 Gen AI"
description: ""
coverImage: "/assets/img/2024-05-18-MLGenAIfordatateams_0.png"
date: 2024-05-18 18:10
ogImage:
  url: /assets/img/2024-05-18-MLGenAIfordatateams_0.png
tag: Tech
originalTitle: "ML , Gen AI for data teams"
link: "https://medium.com/@mikldd/ml-gen-ai-for-data-teams-b38d6f07c28e"
---

## 고전적인 ML 사용 사례와 Gen AI를 위한 신뢰성 있는 설계 구축

AI와 ML은 대부분의 데이터 팀에게 중요한 주제입니다. 회사들은 AI로 실질적인 영향을 얻고 있으며, 데이터 팀은 이 중심에 있어 자신의 작업을 ROI에 결부시키는 원하는 방법을 얻고 있습니다.

최근 예로, AI가 스웨덴의 '지금 살고 나중에 지불' 핀테크 Klarna를 위해 700명의 정근 연애를 자동화하는 데 도움을 주었습니다. Intercom은 이제 AI 중심의 고객 서비스 플랫폼이 되었으며, 임원들은 Gen AI 사용 사례를 구현하는 데 직접적으로 연관된 OKR을 가지고 있습니다.

이 게시물에서는 데이터 팀에서 일하는 경우 이것이 무슨 의미를 하는지 살펴보겠습니다.

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

# 데이터 팀에서의 AI 현황

AI는 많이 발전했습니다. 실제로 그렇습니다. 스탠포드 대학의 2024 AI 지수 보고서에 따르면 AI는 이미지 분류, 시각적 추론, 그리고 영어 이해와 같은 여러 벤치마크에서 인간의 성능을 넘어섰다고 합니다.

![이미지](/assets/img/2024-05-18-MLGenAIfordatateams_0.png)

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

요즘에는 ML 및 AI에 대한 수요가 급증하여 많은 데이터 팀이 업무 우선 순위를 재조정하게 되었습니다. 이는 ML 및 AI에서 데이터 팀의 역할에 대한 질문을 답하지 못한 채 남아 있습니다. 저희 경험상 데이터가 소유한 부분과 엔지니어가 소유한 부분 사이의 경계가 여전히 모호한 상황입니다.

dbt가 최근 수천 명의 데이터 실무자를 대상으로 조사한 결과, 데이터 팀이 AI 및 ML에 참여하는 정도에 대한 정보를 얻을 수 있었습니다.

AI 도입의 신호는 있지만, 대부분의 데이터 팀은 아직 일상적인 업무에 AI를 사용하고 있지 않습니다. 현재 응답자 중 1/3만이 오늘날 AI 모델 훈련을 위한 데이터를 관리하고 있습니다.

![그림](/assets/img/2024-05-18-MLGenAIfordatateams_1.png)

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

이것은 곧 변경될 수 있습니다. 55%의 사람들이 곧 AI가 자가 데이터 탐색을 위해 혜택을 누리기를 기대하고 있습니다.

![AI 및 ML use cases](/assets/img/2024-05-18-MLGenAIfordatateams_2.png)

이는 우리가 1,000개 이상의 데이터 팀과 대화한 경험을 반영한 것입니다. 현재의 노력은 주로 데이터 분석을 위한 데이터 준비, 대시보드 유지 및 이해관계자 지원에 집중되어 있지만, AI 및 ML에 투자하고자 하는 욕망이 있습니다.

# AI 및 ML 사용 사례

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

ML과 AI가 수십 년 동안 존재해왔다는 것을 알아야 합니다. 최신 AI 모델인 Gen AI 모델은 텍스트에서 SQL 코드를 생성하거나 비즈니스 질문에 자동으로 답변하는 것과 같은 첨단 사용 사례에 가장 적합할 수 있지만, 분류 및 회귀 모델과 같은 더 검증된 방법들도 중요한 목적을 가지고 있습니다.

가장 인기 있는 기술들 중 일부는 다음과 같습니다.

![이미지](/assets/img/2024-05-18-MLGenAIfordatateams_3.png)

# 고전적인 머신 러닝 사용 사례

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

대부분의 팀은 아직 전통적인 머신러닝 방법을 사용하지 않고 있습니다. 예를 들어, 분류, 회귀, 이상 감지와 같은 방법들이 있습니다. 이러한 방법들은 특히, 당신이 예측하고자 하는 명확한 결과 (예: 위험한 고객)와 예측 기능 (예: 가입 국가, 나이, 이전 사기)이 명확한 감독 학습에 유용할 수 있습니다.

이러한 시스템들은 종종 설명하기 쉽고, 각 기능의 상대적 중요성을 추출할 수 있어 이를 통해 이유를 설명하기 쉽습니다. 이로써 이해관계자에게 고위험 고객을 거부하는 결정이 내려진 이유를 설명할 수 있게 됩니다.

아래의 머신러닝 시스템은 고객 위험 점수 모델을 강조하며, 새로 가입한 사용자가 고위험 고객인지 거부해야 할 가능성이 얼마나 높은지를 예측합니다.

![image](/assets/img/2024-05-18-MLGenAIfordatateams_4.png)

다양한 소스에서 수집된 원시 데이터를 활용하여 예측 기능을 구축하며, 이는 데이터 과학자의 전문 지식과 모델이 식별한 예상치 못한 패턴을 결합합니다. 핵심 개념은 다음과 같습니다:

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

- Sources 및 데이터 마트: 시스템에서 추출된 원시 및 가공되지 않은 데이터로 데이터 과학자가 관련성이 있는 것으로 판단한 것
- 특성: ML 모델에 공급되는 전처리된 데이터 (예: 대도시의 거리, 나이, 이전 사기)
- 레이블: 이전 위험한 고객을 기반으로 한 목표 출력 (예/아니오)
- 트레이닝: 기계 학습 모델에 내부 매개변수나 가중치를 레이블된 예시에 기반하여 조정하여 정확한 예측을 수행할 수 있도록 가르치는 반복적인 프로세스
- 추론: 트레이닝 단계 이후 새로운, 보이지 않은 데이터에 대해 예측이나 분류를 수행하기 위해 훈련된 기계 학습 모델을 사용하는 것

데이터 팀과의 협업을 통해, 전통적인 ML 작업 흐름의 많은 부분이 데이터 웨어하우스로 이동되어 데이터 소스 및 피처 저장소의 기반이 되는 것을 볼 수 있습니다. 주요 데이터 웨어하우스는 이를 직접 제공하도록 시작했으며(예: BigQuery ML), 미래에는 전체적인 ML 작업 흐름이 데이터 웨어하우스로 완전히 이동할 것을 시사합니다.

전통적인 ML 모델의 성공을 위한 일반적인 도전 과제는 다음과 같습니다:

- 이용 가능한 데이터를 바탕으로 모델이 원하는 결과를 정확하고 적합한 수준으로 예측할 수 있는가
- 달성된 정확도와 적합도 수준이 비즈니스에 대한 ROI로 충분한가
- 이 작업을 수행하기 위해 우리가 해야 하는 트레이드 오프는 무엇인가(예: 위험한 고객을 검토하기 위해 더 많은 운영 직원)
- 모델 유지 및 모니터링에 대한 유지와 모니터링의 비용은 얼마인가

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

# 차세대 및 Gen AI 사용 사례

최근 몇 년간 차세대 및 특히 Gen AI 사용 사례에 대한 이야기가 소개되었으며 ChatGPT 3의 효율성으로 유명해졌습니다. 이 분야는 새로운 것이며 비즈니스 ROI가 아직 증명되지 않았지만 잠재력은 매우 큽니다.

아래는 데이터 팀을 위해 본 Gen AI 사용 사례 중에서 가장 인기 있는 몇 가지입니다.

![이미지](/assets/img/2024-05-18-MLGenAIfordatateams_5.png)

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

오늘의 사용 사례는 크게 두 가지 영역으로 그룹화될 수 있어요.

- 비즈니스 가치 향상 — 고객 지원 챗봇에서 간단한 고객 상호 작용을 자동화하거나 고객 답변을 관련 지식 베이스 기사와 매칭하는 등 비즈니스 프로세스를 자동화하거나 최적화합니다.
- 데이터 팀 생산성 향상 — 근본적인 데이터 워크플로우를 단순화하여 기술에 능통하지 않은 분석가가 ‘텍스트를 SQL로’ 쓸 수 있도록 하거나 비즈니스 이해자가 제시한 자연어 질문에서 답변을 생성함으로써 비즈니스 이해자의 즉각적인 요청을 줄입니다.

아래는 비즈니스에 관련된 특정 데이터 말뭉치를 기반으로 ChatGPT의 사용자 버전을 설정하는 샘플 아키텍처입니다. 시스템은 두 부분으로 구성됩니다: (1) 도메인 데이터의 데이터 적재 및 (2) 실시간으로 질문에 답변할 수 있도록 데이터를 쿼리합니다.

![image](/assets/img/2024-05-18-MLGenAIfordatateams_6.png)

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

예제 정보 검색 시스템 (출처: Langchain)

첫 번째 단계는 문서를 벡터 저장소에 로드하는 것입니다. 이 과정에는 서로 다른 소스에서 데이터를 결합하거나 엔지니어들과 함께 생 데이터를 다루는 것, 그리고 모델이 교육받지 않아도 되는 데이터를 수동으로 제거하는 것(예: 고객 만족도 낮은 지원 응답)이 포함될 수 있습니다.

- 특정 텍스트 말뭉치에서 텍스트로 데이터 소스 로드
- 전처리하고 텍스트를 작은 조각으로 나누기
- 단어들의 유사성에 따라 단어의 벡터 공간을 만들기 위해 임베딩 만들기
- 임베딩을 벡터 저장소에 로드하기

임베딩에 익숙하지 않다면, 단어나 문서의 숫자적 표현이고 이들 사이에 존재하는 의미와 관계를 포착하는 것이다. 아래 코드 스니펫을 실행하면 실제로 무엇인지 볼 수 있습니다.

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
from gensim.models import Word2Vec
# 문장 말뭉치 정의
corpus = [
    "the cat sat on the mat",
    "the dog barked loudly",
    "the sun is shining brightly"
]
# 문장 토큰화
tokenized_corpus = [sentence.split() for sentence in corpus]
# Word2Vec 모델 학습
model = Word2Vec(sentences=tokenized_corpus, vector_size=3, window=5, min_count=1, sg=0)
# 단어 임베딩 획득
word_embeddings = {word: model.wv[word].tolist() for word in model.wv.index_to_key}
# 단어 임베딩 출력
for word, embedding in word_embeddings.items():
    print(f"{word}: {embedding}")
```

도메인 데이터를 벡터 저장소에 입력한 후, 사전에 학습된 LLM을 세밀하게 조정하여 도메인과 관련된 질문에 답변하는 시스템을 확장할 수 있습니다.

![이미지](/assets/img/2024-05-18-MLGenAIfordatateams_7.png)

예시 정보 검색 시스템 (출처: Langchain)

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

사용자는 채팅과 새로운 질문을 결합하여 후속 질문을 할 수 있습니다.
위의 임베딩 및 벡터 저장소를 사용하여 유사 문서를 찾을 수 있습니다.
큰 언어 모델(ChatGPT와 같은)을 사용하여 유사 문서를 활용하여 응답을 생성할 수 있습니다.

다행히도 Meta와 Databricks와 같은 기업들이 교육 및 오픈소스 모델을 제공하고 있으므로 (Huggingface는 현재 1000여 개 이상의 Llama 3 오픈소스 모델을 보유하고 있습니다) 자체 모델을 교육시키기 위해 수백만 달러를 소비할 필요가 없습니다. 대신 기존 모델을 데이터로 세밀하게 조정하세요.

위와 같은 LLM(Large Language Model) 기반 시스템의 효과는 그들에게 주어지는 데이터의 품질에 달려 있습니다. 따라서 데이터 전문가들은 여러 소스에서 가져온 가능한 많은 데이터를 피드하는 것이 장려되며, 이들 소스가 어디에서 오는지 추적하고 데이터가 예상대로 흐르는지 확인하는 것이 최우선 과제여야 합니다.

Gen AI 모델의 성공을 위한 전형적인 도전 과제는:

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

- 모델을 충분히 훈련할 만한 데이터가 있나요? 개인정보 문제로 사용이 제한되는 데이터가 있나요?
- 모델이 해석 가능하고 설명 가능해야 하는가요? 예를 들어 고객이나 규제기관을 위해
- LLM을 훈련하고 세부 조정하는 것에 대한 잠재적 비용은 무엇인가요? 그 혜택이 이 비용을 상회하나요?

# AI와 ML에서 데이터 품질의 중요성

당신의 주요 데이터 전달은 의사 결정에 도움을 주는 BI 대시보드를 위해 무작위 통찰을 제공할 때, 인간이 개입합니다. 인간의 직관과 기대로 인해 데이터 문제나 설명할 수 없는 추세가 종종 발견됩니다 — 그리고 아마도 몇 일 안에 해결됩니다.

ML과 AI 시스템은 다릅니다.

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

ML 시스템이 수백 개 또는 수천 개의 다양한 소스에서 가져온 기능에 의존하는 것은 흔한 일입니다. 간단한 데이터 문제처럼 보일 수 있는 것들 — 누락된 데이터, 중복, 널 값 또는 빈 값, 이상치 — 이들은 비즈니스에 중대한 문제를 일으킬 수 있습니다. 이를 세 가지 다른 방법으로 생각해 볼 수 있습니다.

- 비즈니스 중단 — 모든 사용자 ID가 비어 있는 중대한 오류는 새 사용자 가입 승인 비율이 90% 감소할 수 있습니다. 이러한 유형의 문제는 비용이 많이 들지만 종종 초기에 발견됩니다.
- 드리프트 또는 '잠재적' 문제 — 이는 고객 분포의 변경이나 특정 세그먼트에 대한 누락된 값을 포함할 수 있으며, 이로 인해 체계적으로 부정확한 예측이 발생할 수 있습니다. 이러한 문제는 발견하기 어려우며, 몇 달 또는 몇 년 동안 지속될 수 있습니다.
- 체계적인 편향 — Gen AI와 같은 경우, 데이터 수집에 대한 인간의 판단이나 결정으로 편향이 발생할 수 있습니다. 구글의 Gemini 모델에서 발생한 편견과 같이 최근 예들은 이러한 결과가 가져다 줄 수 있는 결과를 강조했습니다.

회귀 모델을 지원하거나 LLM을 위한 새로운 텍스트 말뭉치를 작성 중이더라도, 새로운 모델을 개발하는 연구자가 아닌 한, 업무의 대부분은 데이터 수집 및 전처리에 관련될 것입니다.

![이미지](/assets/img/2024-05-18-MLGenAIfordatateams_8.png)

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

ML 시스템은 모델이 하나의 부분에 불과한 대규모 생태계입니다. — Google on Production ML Systems

일반적으로, 화면 왼쪽에 위치할수록 오류를 모니터링하기 어려울 수 있습니다. 수백 개의 입력 및 원시 소스가 있어서 때로는 데이터 관련 전문가의 통제 영역을 벗어날 수 있으며, 데이터는 수천 가지 방법으로 잘못될 수 있습니다.

![MLGenAIfordatateams_9](/assets/img/2024-05-18-MLGenAIfordatateams_9.png)

모델 성능은 ROC, AUC 및 F1 점수와 같이 잘 알려진 메트릭을 사용하여 간단히 모니터링할 수 있으며, 이러한 메트릭은 모델 성능의 단일 측정 항목을 제공합니다.

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

상류 데이터 품질 문제의 예시

- 결측 데이터: 데이터셋의 불완전하거나 없는 값은 모델이 일반화하고 정확한 예측을 하는 능력에 영향을 미칠 수 있습니다.
- 일관성 없는 데이터: 서로 다른 소스 또는 시간에 따라 다양한 형식, 단위 또는 표현으로 인한 데이터 변이는 모델 학습 및 추론 중 혼동과 오류를 유발할 수 있습니다.
- 이상치: 대부분의 관측치와 유별난 점이 큰 데이터의 이상치 또는 특이치는 모델 학습에 영향을 주고 편향적이거나 부정확한 예측을 유발할 수 있습니다.
- 중복 레코드: 데이터셋에 중복된 항목이 들어 있는 경우 모델의 학습 과정을 왜곡시킬 수 있으며, 모델이 훈련 데이터에서 성능이 우수하지만 새로운, 보지 못한 데이터에서는 성능이 저하될 수 있습니다.

데이터 이동의 예시

- 계절별 제품 선호도: 계절에 따른 고객 선호도의 변화가 전자 상거래 추천에 영향을 미칩니다.
- 금융 시장 변동: 경제적 사건으로 인한 시장의 급격한 변동이 주식 가격 예측 모델에 영향을 미치는 것입니다.

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

LLM에 대한 텍스트 데이터의 데이터 품질 문제 예시

- 품질이 낮은 입력 데이터: 챗봇은 정확한 과거 사례 해결을 기반으로 작동합니다. 이 데이터의 정확성에 따라 봇의 효과가 결정되며, 잘못된 정보를 배우는 것을 피해야 합니다. 고객 만족도나 해결 점수가 낮은 답변은 모델이 잘못된 정보를 학습했을 수 있다는 신호일 수 있습니다.
- 오래된 데이터: 의료 상담 봇은 오래된 정보에 의존할 수 있어서 관련성이 적은 권장 사항을 제공할 수 있습니다. 특정 일자 이전에 작성된 연구는 더 이상 목적에 부합하지 않을 수 있음을 나타낼 수 있습니다.

# 신뢰할 수 있는 머신 러닝 및 인공지능 시스템 구축

우리는 데이터 팀이 소프트웨어 엔지니어링과 비교했을 때 신뢰할 수 있는 데이터 시스템을 제공하는 데 신뢰받지 못한다고 믿습니다. 인공지능 파동은 "쓰레기를 넣으면 쓰레기가 나온다" 모델과 그 모든 함의를 기하급수적으로 확장하고 있습니다. 모든 기업이 경쟁 우위를 위한 데이터를 활성화하는 새로운 방법을 찾는 압박 속에 있을 때입니다.

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

특정 도구와 시스템이 모델 성능을 모니터링하기 위해 사용되지만, 이러한 도구들은 종종 데이터 웨어하우스의 상위 소스와 데이터 변환을 고려하지 않습니다. 데이터 신뢰성 플랫폼은 이를 위해 구축되었습니다.

<img src="/assets/img/2024-05-18-MLGenAIfordatateams_10.png" />

# 안정적인 ML 및 AI 시스템 구축을 위한 다섯 가지 요추

고품질의 제품용 ML 및 AI 시스템을 지원하고 유지하기 위해 데이터 팀은 엔지니어들의 최상의 실천 방법을 채택해야 합니다.

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

![img](/assets/img/2024-05-18-MLGenAIfordatateams_11.png)

- 성실한 테스트 — ML 및 AI 시스템에 공급되는 상위 소스 및 출력이 의도적으로 테스트되어야 함 (이상값, 널 값, 분포 변화, 품질)
- 소유자 관리 — ML 및 AI 시스템은 명확한 소유자가 할당되어 문제를 통지받고 조치를 취하기를 기대해야 함
- 사건 처리 — 심각한 문제는 명확한 SLA 및 에스컬레이션 경로를 가진 사건으로 취급되어야 함
- 데이터 제품 마인드셋 — ML 및 AI 시스템으로 공급되는 전체 가치 사슬을 하나의 제품으로 고려해야 함
- 데이터 품질 메트릭스 — 데이터 팀은 ML 및 AI 시스템의 가동 시간, 오류, SLA 등 핵심 메트릭을 보고할 수 있어야 함

한 축에만 집중하는 것은 드물게 충분하지 않습니다. 명확한 소유권이 없는 채로 테스트에 과도하게 투자하면 문제가 슬립할 수 있습니다. 소유에 투자하지만 의도적으로 사건을 관리하지 않으면 심각한 문제가 너무 오랫동안 해결되지 않을 수 있습니다.

![img](/assets/img/2024-05-18-MLGenAIfordatateams_12.png)

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

중요한 점은 다섯 가지 데이터 신뢰성 기둥을 구현하는 데 성공한다고 해도 문제가 발생하지 않는다는 것이 아니라, 단지 미리 발견할 가능성이 더 높아지고 자신감을 키워 고객에게 시간이 지남에 따라 어떻게 개선되고 있는지 전달할 수 있다는 것입니다.

# 요약

현재 데이터 팀 중 33%만이 AI 및 ML 모델을 지원하지만 대부분은 가까운 미래에 지원할 것으로 예상합니다. 이러한 변화는 데이터 팀이 비즈니스 중요 시스템을 지원하고 소프트웨어 엔지니어처럼 더 많이 일해야 한다는 새로운 세계에 적응해야 한다는 것을 의미합니다.

- 데이터 팀에서의 AI 상황 - AI 시스템은 이미지 분류, 시각적 추론 및 영어 이해와 같은 여러 기준에서 성능이 향상되고 있습니다. 현재 데이터 팀 중 33%가 생산 중인 AI 및 ML을 사용하지만 55%의 팀이 예상됩니다.
- AI 사용 사례 - 분류 및 회귀에서 Gen AI까지 다양한 ML 및 AI 사용 사례가 있습니다. 각 시스템은 도전적인 과제를 제기하지만 "고전적인 ML"과 Gen AI 간의 차이는 명백합니다. 우리는 이를 고전적인 고객 위험 예측 모델과 정보 검색 챗봇을 통해 살펴봤습니다.
- AI 및 ML 시스템의 데이터 품질 - 데이터 품질은 ML 및 AI 프로젝트의 성공에 가장 중요한 위험 중 하나입니다. AI 및 ML 모델이 종종 수백 개의 데이터 소스에 의존하는데, 문제를 수동으로 감지하는 것은 거의 불가능합니다.
- 믿을 수 있는 데이터를 위한 다섯 가지 단계 - ML 및 AI 시스템을 지원하고 유지하기 위해 데이터 팀은 엔지니어처럼 더 많이 일해야 합니다. 이에는 지속적인 테스트, 명확한 소유권, 사건 관리 프로세스, 데이터 제품 마인드셋 및 가동 시간 및 SLA와 같은 지표에 대한 보고 능력이 포함됩니다.
