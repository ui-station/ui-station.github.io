---
title: "Read the Docs 트래픽 분석을 위해 Vizro-AI를 활용한 재사용 가능한 대시보드를 구축했어요"
description: ""
coverImage: "/assets/img/2024-05-17-IBuiltaReusableDashboardforReadtheDocsTrafficAnalyticsUsingVizro-AI_0.png"
date: 2024-05-17 19:00
ogImage:
  url: /assets/img/2024-05-17-IBuiltaReusableDashboardforReadtheDocsTrafficAnalyticsUsingVizro-AI_0.png
tag: Tech
originalTitle: "I Built a Reusable Dashboard for Read the Docs Traffic Analytics Using Vizro-AI"
link: "https://medium.com/towards-data-science/i-built-a-reusable-dashboard-for-read-the-docs-traffic-analytics-using-vizro-47dc15dc04f8"
---

## (50 줄 미만의 코드로)

<img src="/assets/img/2024-05-17-IBuiltaReusableDashboardforReadtheDocsTrafficAnalyticsUsingVizro-AI_0.png" />

이 글에서는 기술 작성자로서 유지 관리하는 문서의 트래픽 데이터를 시각화하기 위해 대시보드를 구축하는 방법을 설명하겠습니다. 디자인 스킬이 부족하고 파이썬 경험이 제한적이어서 유지하는 문서의 영향과 사용량을 보여주기 위한 간단하고 로우코드 접근 방식이 필요했습니다. 이것은 오픈 소스 솔루션인 비즈로(Vizro)를 로우코드 대시보드의 템플릿으로 사용하고, 비즈로-AI(Vizro-AI)를 통해 생성적 AI로 개별 차트를 구축하는 것으로 나타났습니다.

## 요약?

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

만약 당신이 바로 시작하고 싶다면, 내 GitHub 레포지토리에서 대시보드에 대한 Jupyter Notebook 코드를 찾을 수 있어.

## Read the Docs 대시보드 프로젝트

만약 나와 같이 Read the Docs (RTD)를 사용하여 오픈 소스 문서 프로젝트를 관리한다면, 아마도 프로젝트 대시보드에서 지난 90일치의 트래픽 데이터를 CSV 형식으로 다운로드할 수 있는 것을 발견했을 것입니다. 대시보드에는 페이지 뷰 합계 차트도 표시되어 있습니다, 아래와 같은 차트가 있죠.

![RTD Traffic Chart](/assets/img/2024-05-17-IBuiltaReusableDashboardforReadtheDocsTrafficAnalyticsUsingVizro-AI_1.png)

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

추가적인 시각적 출력을 위해 Google Analytics (GA)를 활용할 수 있습니다. 그러나 일부 프로젝트에서는 유럽 연합(EU)에서 특히 논란이 되는 일반 데이터 보호 규정(GDPR)과의 준수 때문에 GA를 사용하지 않기를 선호하기도 합니다.

## 코드 및 데이터 가져오기

아래 예시에서 사용된 가짜 CSV 트래픽 데이터는 저희 프로젝트의 트래픽을 비공개로 유지하기 위해 OpenAI의 도움을 받아 생성한 것입니다. 이 가짜 데이터는 진짜 RTD 데이터와 동일한 필드를 가지고 있어서 RTD 대시보드에서 다운로드한 데이터로 대시보드를 다운로드하고 사용할 수 있습니다.

예시를 직접 실행하려면 가짜 데이터(또는 직접 다운로드한 데이터)와 Jupyter Notebook 코드가 필요합니다. 이는 기본 수준에서 쉽게 진행할 수 있지만 보다 고급 사용자는 확장할 수 있습니다. 개선된 버전을 만드신 경우 알려주시면 감사하겠습니다!

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

# Vizro와 Vizro-AI란 무엇인가요?

Vizro는 Plotly와 Dash를 기반으로 한 프레임워크로, 사용자 정의 대시보드 레이아웃을 지정하기 위해 구성 접근법을 사용합니다. Vizro 대시보드는 Vizro와 별도로 구성된 Vizro-AI가 생성한 차트로 채울 수 있습니다. Vizro-AI는 시각화 프로세스를 단순화하기 위해 생성적 AI를 활용하는 독립적인 패키지입니다.

이 예에서, 저는 데이터와 자연어 지시사항을 제공했고, Vizro-AI가 Python 코드를 생성하고 요청한 차트를 생성했습니다. 이것은 저에게 쓰기 작업을 하는 측면에서 잘 작동했습니다. 왜냐하면 저는 프론트엔드 디자인 기술이 없고 Plotly를 잘 모르기 때문입니다. 하지만 OpenAI로부터 적절한 생성적 AI 프롬프트를 작성하고 차트를 얻는 것도 즐거운 일이라고 생각합니다.

## Vizro-AI 설정하기

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

노트북 코드를 실행하기 전에 Vizro-AI를 Python 3.9 이상의 가상 환경 안에 설정해야 합니다. pip install vizro_ai 명령을 사용하여 패키지를 설치해주세요.

다음으로 OpenAI에 접속하기 위해 API 키가 필요합니다. 계정이 없다면 먼저 생성하고, 무료 버전을 사용할 수 없기 때문에 모델을 이용하기 위해 일부 크레딧을 구매해야 합니다. API 키를 생성하고 환경에 추가하여 코드를 통해 OpenAI에 성공적으로 호출할 수 있게 해주세요. OpenAI 문서에 간단한 지침이 있고, Vizro-AI LLM 설정 가이드에도 이 과정이 포함되어 있습니다.

## 차트 생성

이 시점에서 주피터 노트북을 열어 첫 차트를 만들거나, 제 저장소에서 노트북을 열어 내가 작성한 코드를 차례로 살펴보고, RTD 데이터(또는 제공한 가짜 데이터)를 pandas DataFrame에 불러와주세요. 아래 코드에서는 df로 이름을 지었습니다.

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

아래 코드는 Vizro-AI에 요청을 제출하여 Read the Docs 프로젝트 대시보드의 차트와 유사한 차트를 생성하는 방법을 보여줍니다. 이 차트는 날짜별 조회수를 보여주며 문서의 안정 버전과 최신 버전으로 데이터를 분할하여 두 개의 추적 값으로 나뉩니다:

Vizro-AI는 "가장 최신 및 안정 버전의 각 날짜별 조회 수 행을 결합한 후 가장 최신 및 안정 버전의 조회수를 비교하는 선 그래프를 그리세요"라는 자연어 쿼리와 데이터프레임을 모델에 전달합니다. 위 예제에서는 gpt-4 모델을 지정했습니다. Vizro-AI는 가격이 낮고 더 빠른 답변을 제공하기 위해 기본적으로 gpt-3.5-turbo를 사용하지만, 가장 정교한 차트 제공이 불가능합니다. 그래서 명시적으로 gpt-4 모델을 사용할 것을 요청했습니다.

차트 출력은 데이터 및 쿼리 제출 시점에서 OpenAI로부터 받은 출력에 따라 달라집니다. explain=True 매개변수는 Vizro-AI에게 결과 차트 생성 방식을 설명하도록 요청하며, 해당 설명은 쥬피터 노트북에서 출력되며 show() 명령어에 의해 표시되는 차트와 함께 표시됩니다.

Vizro-AI가 제공하는 인사이트 텍스트는 트래픽 데이터 조작 방법을 설명합니다. 코드 섹션은 코드 스니펫이 요청된 선 그래프를 생성하는 방법에 따라 표시됩니다.

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

<img src="/assets/img/2024-05-17-IBuiltaReusableDashboardforReadtheDocsTrafficAnalyticsUsingVizro-AI_2.png" />

아래에 표시된 차트는 다음과 같습니다:

<img src="/assets/img/2024-05-17-IBuiltaReusableDashboardforReadtheDocsTrafficAnalyticsUsingVizro-AI_3.png" />

## 더 많은 차트 만들기

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

Vizro-AI가 제공하는 코드를 활용하여 추가 차트를 작성했습니다. 다음과 같이 교통량을 자세히 설명하는 몇 가지 차트를 작성했습니다.

Vizro-AI가 데이터를 조작하고 차트를 생성하는 코드를 생성해 주어서 작업을 간편하게 해 주었습니다. 차트 자체만으로 유용하며, 더욱 유용한 것은 이를 조합하여 한 화면에 통합된 대시보드를 만드는 것입니다.

## Vizro 대시보드 만들기

Vizro-AI 코드와 동일한 Jupyter Notebook에서 Vizro를 사용할 수 있습니다. Vizro 설명서에 설명된대로 pip install vizro를 수행해 주세요. 여기에는 차트 생성이 없는 간단한 대시보드의 구조를 위한 코드가 있습니다:

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

이 시점에서 두 가지 옵션이 있습니다:

- 대시보드를 생성할 때마다 Vizro-AI를 사용하여 차트를 생성합니다.
- Vizro-AI가 반환한 Python 코드를 직접 Plotly로 호출합니다.

첫 번째 옵션은 더 적은 코드를 필요로 하지만 반환 속도가 느리고 더 비싸며, Vizro-AI를 사용하여 OpenAI를 호출하기 때문에 더 많은 비용이 소요됩니다. 두 번째 옵션은 더 빠르지만 코드 조작이 더 많이 필요합니다.

다음은 대시보드 코드를 포함하는 셀입니다. 이 코드는 Vizro-AI를 통해 호출하는 함수를 사용하여 첫 번째 옵션을 보여줍니다. (자신의 실행을 계획하고 있다면, 이 코드를 실행하려면 내 레포지토리의 노트북을 사용하고 데이터를 로드하고 Vizro-AI에 대한 호출 설정을 설정하는 셀을 실행해야 합니다):

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

약간 다른 버전을 제공해본다. 여기서 두 번째 옵션을 사용하여 차트 중 하나를 생성했다. Plotly 조작은 제한되어 있어서 Python 코드를 약간 수정하여 라인의 색을 변경했다. (여러분이 직접 실행하려는 경우, 내 저장소의 노트북을 사용하고 데이터를 로드하고 차트 생성 함수를 설정하는 셀을 실행했는지 확인하세요).

자신만의 Read the Docs 데이터로 대시보드를 시도해보기 위해 주피터 노트북을 다운로드할 수 있어요. 제가 제공한 가짜 데이터로 만든 대시보드는 다음과 같이 보입니다.

제 동료 중 한 명(Nadija 감사합니다!)가 제게 팁을 줬어요. 대시보드를 노트북에서 실행한 다음 다음과 같이 선택한 포트를 보고 별도의 브라우저 창에서 볼 수 있다고 해요:

```js
Vizro().build(dashboard).run(port=8006) # 브라우저에서 localhost8006
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

또 다른 방법(Anthony 님 감사합니다!)으로, 위의 두 번째 대시보드 예제에서 보여 드린 대로, 대시보드를 보기 위한 클릭 가능한 링크를 생성할 수 있습니다:

```js
Vizro()
  .build(dashboard)
  .run((jupyter_mode = "external"));
```

# 마무리

이 예에서는 Vizro-AI를 사용하여 문서 트래픽을 시각화하기 위한 Plotly 차트를 생성하고, 그 차트를 Vizro 대시보드에 구축하는 방법을 보여드렸습니다.

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

만약 데이터 과학과 파이썬 기술이 있고 디자인에 재능이 있다면, Plotly와 Dash로 대시보드를 구축하는 도전에 도전해 보고 싶을 것입니다. 하지만 이런 기술이 없는 사람들에게는 OpenAI를 활용하여 위와 같은 결과물을 얻을 수 있다는 것이 정말 큰 변화입니다. 이제 50줄 정도의 코드로 Read the Docs 트래픽 데이터에 대한 유용한 시각화를 얻었습니다. 전문적으로 보이며 확장 가능하고 상대적으로 쉽게 공유할 수 있습니다. 추가적인 노력으로 필터, 매개변수 또는 별도의 탐색 가능한 페이지와 같은 사용자 정의 기능을 추가하여 더 개선할 수 있습니다.

더 나아가, 동료들과 협업하여 대시보드 코드를 다른 Read the Docs 프로젝트에 맞게 수정할 수 있습니다. 프로젝트를 쉽게 설명하기 위해 주피터 노트북을 사용했지만, 이 방식은 파이썬 스크립트에서도 잘 작동하여 쉽게 공유하고 버전 관리를 할 수 있습니다. 또한 대시보드를 배포하여 동료들이 코드를 실행하지 않고 직접 액세스할 수도 있습니다.

저희 팀은 이제 하루만에 기술 작가에 의해 구축된 문서 영향을 추적하는 데 유용하고 사용할 수 있는 대시보드를 소유하고 있습니다. 더 바랄 것이 무엇이 있을까요?

이 글을 작성하는 동안 여러 차례 리뷰 피드백을 주신 동료들, 특히 Nadija와 Anna 그리고 Joe에게 감사드립니다.
