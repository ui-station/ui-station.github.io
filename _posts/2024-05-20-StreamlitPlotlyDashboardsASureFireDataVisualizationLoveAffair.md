---
title: "Streamlit Plotly 대시보드 데이터 시각화를 위한 확실한 방법"
description: ""
coverImage: "/assets/img/2024-05-20-StreamlitPlotlyDashboardsASureFireDataVisualizationLoveAffair_0.png"
date: 2024-05-20 18:25
ogImage:
  url: /assets/img/2024-05-20-StreamlitPlotlyDashboardsASureFireDataVisualizationLoveAffair_0.png
tag: Tech
originalTitle: "Streamlit Plotly Dashboards: A Sure Fire Data Visualization Love Affair"
link: "https://medium.com/gitconnected/streamlit-plotly-dashboards-a-sure-fire-data-visualization-love-affair-e441b5339888"
---

![StreamlitPlotlyDashboardsASureFireDataVisualizationLoveAffair](/assets/img/2024-05-20-StreamlitPlotlyDashboardsASureFireDataVisualizationLoveAffair_0.png)

Python Streamlit은 인터랙티브 웹 인터페이스를 만드는 놀라운 프레임워크입니다.

Python Plotly는 지도 및 차트와 같은 데이터 시각화를 효율적으로 만드는 훌륭한 라이브러리입니다. 데이터 시각화를 아름답게 표시합니다.

Streamlit과 Plotly는 함께하면 천생연분입니다.

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

우리는 CSV 데이터 세트에서 여러 데이터 시각화를 웹 인터페이스에서 생성할 수 있고 다중 상호 작용 레이어(예: 슬라이더 및 드롭다운 메뉴)를 추가할 수 있습니다.

모두 100 줄 미만의 코드로 가능합니다! 어떻게 할 수 있는지 보고 싶으세요?

종합적이고 무료로 이용할 수 있는 데이터 세트를 사용하여 모든 것을 함께 해보겠습니다.

# 데이터 세트 — UNHCR 난민 데이터

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

UNHCR(UN High Commission for Refugees)은 전 세계의 난민 이동에 대한 통계를 추적합니다.

그들의 데이터는 여기에서 무료로 이용할 수 있습니다.

다운로드 페이지에 도착한 후에는 선택한 데이터에 대해 자세히 볼 수 있습니다:

![이미지](/assets/img/2024-05-20-StreamlitPlotlyDashboardsASureFireDataVisualizationLoveAffair_1.png)

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

이 프로젝트에서는 각 난민의 출신 국가와 피난국을 검색해 보겠습니다.

이 데이터를 사용하여 다음을 보여주는 전 세계 맵과 차트를 만들 수 있습니다:

- 출신 국가로부터 — 피난민이 어디로 가고 있는지
- 피난국으로부터 — 피난민이 어디에서 왔는지

데이터셋을 다운로드한 후, 스프레드시트 형식으로 열어서 다루고 있는 내용을 확인할 수 있습니다:

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

<img src="/assets/img/2024-05-20-StreamlitPlotlyDashboardsASureFireDataVisualizationLoveAffair_2.png" />

이 프로젝트에서 관심 있는 데이터 필드는 다음과 같습니다:

- 출신 국가(3자리 ISO 코드 포함) — 망명을 찾는 사람이 어디에서 왔는지
- 망명 국가(3자리 ISO 코드 포함) — 실제로 망명을 찾는 사람이 있는 곳
- 인정된 결정 — 망명을 찾는 사람이 수락되었는지 여부(국가별 숫자 합계)

출신 국가와 망명 국가 모두 3자리 ISO 코드를 가지고 있어서 등치지도를 만드는 데 사용할 수 있습니다.

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

이것 정말 유용해요! 지도 만드는 과정을 크게 간소화해줘요. 이제 코딩에 돌입해봅시다!

## 단계 1: 라이브러리 가져오기 및 환경 설정

우선, 필요한 라이브러리를 가져와 Streamlit 애플리케이션의 페이지 레이아웃을 설정해야 합니다.

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
import streamlit as st
import pandas as pd
import plotly.express as px

# 페이지 레이아웃을 넓게 설정합니다
st.set_page_config(layout="wide")
```

- streamlit: 대화형 웹 애플리케이션을 만드는 데 사용됩니다.
- pandas: 데이터 조작 및 분석에 사용됩니다.
- plotly.express: 시각화를 생성하는 데 사용됩니다.

또한 Streamlit을 지도와 차트의 시각화를 위해 넓은 레이아웃으로 구성합니다.

## 단계 2: 데이터셋 로드하기 — UNHCR 데이터베이스에서 글로벌 통계

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

이제 우리는 피난 심사 결정을 포함한 데이터셋을 로드합니다.

```js
# 데이터셋 로드
file_path = 'asylum-decisions.csv'
df = pd.read_csv(file_path)
```

우리는 pandas를 사용하여 CSV 파일을 DataFrame으로 읽어옵니다.

데이터 프레임을 만든 후에는 맵과 차트에 필요한 데이터를 설정하기 위해 가공할 수 있습니다.

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

## 단계 3: 고유 년도 및 국가 추출

우선적으로 데이터셋에서 고유한 년도와 국가를 추출합니다. 이 단계의 목적은 Streamlit 인터페이스의 슬라이더 및 드롭다운 메뉴를 채우는 것입니다:

```python
# 고유한 연도 추출 및 두 열의 고유 국가를 결합하여 드롭다운 메뉴 생성
years = sorted(df['Year'].unique())
countries = sorted(set(df['Country of origin']).union(set(df['Country of asylum'])))
```

unique() 함수는 슬라이더에 중복된 연도가 없고 원천/유학국에 중복된 국가가 없도록합니다.

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

origin/asylum에 대한 연합을 수행함으로써 데이터셋에서 어떤 국가도 빠뜨리지 않도록 보장합니다.

## 단계 4: Streamlit 인터페이스 생성

데이터를 준비한 후에는 Streamlit 사용자 인터페이스 구성 요소를 선택하는 데 사용할 수 있습니다.

```js
# Streamlit 인터페이스
st.subheader("Asylum Decisions Visualization")

# 연도 및 국가 선택 슬라이더 및 드롭다운
selected_year = st.slider("연도 선택", min_value=int(years[0]), max_value=int(years[-1]), step=1, key="year_slider")
selected_country = st.selectbox("국가 선택", countries, key="country_select")
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

첫 번째 줄은 작은 헤더를 만드는 것입니다. 그런 다음 다음을 만들어야 합니다:

- 슬라이더: 사용자가 이전에 작성한 연도 데이터 프레임에서 연도를 선택할 수 있게 합니다.
- 드롭다운(선택 상자): 사용자가 이전에 생성한 국가 데이터 프레임에서 국가를 선택할 수 있게 합니다.

## 단계 5: 데이터 필터링

선택한 연도와 피난국을 기반으로 데이터셋을 필터링하여 시각화를 위해 준비합니다.

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
# 선택한 연도와 국가를 기준으로 데이터 세트 필터링하기
filtered_df_origin = df[(df['연도'] == 선택한_연도) & (df['출신 국가'] == 선택한_국가)]
filtered_df_asylum = df[(df['연도'] == 선택한_연도) & (df['안보 국가'] == 선택한_국가)]

# 0 값을 포함한 출신 국가 데이터
origin_data = filtered_df_asylum.groupby('출신 국가')['인정된 결정'].sum().reset_index()
all_countries_origin = pd.DataFrame(countries, columns=['출신 국가'])
origin_data = all_countries_origin.merge(origin_data, on='출신 국가', how='left').fillna(0)

# 0 값을 포함한 안보 국가 데이터
asylum_data = filtered_df_origin.groupby('안보 국가')['인정된 결정'].sum().reset_index()
all_countries_asylum = pd.DataFrame(countries, columns=['안보 국가'])
asylum_data = all_countries_asylum.merge(asylum_data, on='안보 국가', how='left').fillna(0)
```

여기서 첫 번째 단계는 데이터를 연도별로 분리하는 것입니다. 이는 슬라이더로 선택한 연도에 따라 각 국가로 분리된 데이터를 만드는 과정입니다. 그 다음으로 모든 국가에 값이 있는지 확인하기 위해 fillna() 함수를 사용하여 모든 빈 열에 0을 추가합니다.

## 단계 6: 코로플레스 맵 생성하기

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

우리가 원산지 및 피난처 국가별 데이터 프레임을 가지고 나면, 각 데이터 프레임을 시각화하기 위해 두 개의 코로플레스 맵을 생성합니다:

```js
# 맵 생성
fig_origin = px.choropleth(origin_data, locations="Country of origin", locationmode="country names",
                           color="Recognized decisions", hover_name="Country of origin",
                           projection="natural earth", color_continuous_scale="YlOrRd",
                           title="원산지 국가", template="plotly_dark")

fig_asylum = px.choropleth(asylum_data, locations="Country of asylum", locationmode="country names",
                           color="Recognized decisions", hover_name="Country of asylum",
                           projection="natural earth", color_continuous_scale="YlOrRd",
                           title="피난처 국가", template="plotly_dark")
```

우리가 사용하는 메서드 및 속성에 대한 몇 가지 세부 정보:

- px.choropleth(): 코로플레스 맵을 만드는 주요 plotly 메서드. 이 메서드를 사용하여 원산지 및 피난처 2개의 맵을 그립니다.
- locations: 위치 이름이 있는 열을 지정합니다 (첫 번째 맵의 경우 원산지 국가이고, 두 번째 맵의 경우 피난처 국가입니다).
- locationmode: 위치 이름을 해석하는 방법을 지정합니다.
- color: 맵을 색칠하는 데 사용할 열을 지정합니다 (Recognized decisions 열의 값에 기초합니다).
- hover_name: 위치를 가리킬 때 표시할 열을 지정합니다.
- projection: 맵 투영 유형을 지정합니다.

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

우리는 또한 제목, 색상 궁합(YlOrRd) 및 템플릿을 설정했습니다.

![이미지](/assets/img/2024-05-20-StreamlitPlotlyDashboardsASureFireDataVisualizationLoveAffair_3.png)

각 지도는 위 예제와 유사하게 보일 것입니다(데이터에 따라 다름). 이 예제는 2017년 캐나다로의 망명 신청자를 위한 것입니다.

## 7단계: 막대 차트 생성하기

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

다음으로, 인정된 결정에 따른 상위 10개 국가에 대한 수평 막대 차트를 생성합니다:

```js
# 상위 10개 국가에 대한 막대 차트 생성
top_origin_data = origin_data.nlargest(10, 'Recognized decisions')  # 출신 국가 상위 10개
top_asylum_data = asylum_data.nlargest(10, 'Recognized decisions')  # 피난 국가 상위 10개

fig_bar_origin = px.bar(top_origin_data, x='Recognized decisions', y='Country of origin',
                        orientation='h', color='Recognized decisions', color_continuous_scale='YlOrRd',
                        title='출신 국가 상위 10개')

fig_bar_asylum = px.bar(top_asylum_data, x='Recognized decisions', y='Country of asylum',
                        orientation='h', color='Recognized decisions', color_continuous_scale='YlOrRd',
                        title='피난 국가 상위 10개')
# 막대 순서 변경
fig_bar_origin.update_layout(yaxis=dict(categoryorder='total ascending'))
fig_bar_asylum.update_layout(yaxis=dict(categoryorder='total ascending'))
```

여기서는 나라 이름의 길이가 다양하기 때문에 수평 막대 차트가 가장 적합합니다. 이 코드 조각에 대해:

- px.bar: 막대 차트 생성 (출신 데이터와 피난 데이터 각각 하나씩)
- orientation='h': 막대 차트가 수평임을 지정합니다.
- categoryorder='total ascending': 막대 순서를 반전시킵니다. 각 차트를 미학적으로 강조하기 위한 비필수적인 단계입니다.

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

<img src="/assets/img/2024-05-20-StreamlitPlotlyDashboardsASureFireDataVisualizationLoveAffair_4.png" />

위 예제와 비슷한 차트가 생성될 것입니다.

멋지네요. 이제 2개의 지도와 2개의 차트를 생성했으니, Streamlit 대시보드에 이 모든 것을 함께 표시할 수 있습니다.

## 단계 8: 지도 및 막대 차트 표시하기

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

끝으로, Streamlit 인터페이스에서 지도와 막대 차트를 옆에 나란히 표시합니다.

```js
# 지도와 막대 차트를 옆에 표시하기
col1, col2 = st.columns(2)

with col1:
    st.plotly_chart(fig_origin, use_container_width=True)
    st.plotly_chart(fig_bar_origin, use_container_width=True)

with col2:
    st.plotly_chart(fig_asylum, use_container_width=True)
    st.plotly_chart(fig_bar_asylum, use_container_width=True)
```

이 코드 스니펫에 대한 설명:

- st.columns: 시각화 요소를 옆에 배치할 수 있는 열을 생성합니다.
- st.plotly_chart: Streamlit 앱에서 Plotly 차트를 표시합니다.

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

와 놀라운 결과가 있습니다:

![이미지](/assets/img/2024-05-20-StreamlitPlotlyDashboardsASureFireDataVisualizationLoveAffair_5.png)

이 정말 멋집니다.

우리는 이 모두를 코드가 100줄 미만으로(내 예제 Python 파일에는 82줄) 구현했습니다.

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

잘 진행되었으면 좋겠네요. 함께 와 주셔서 감사합니다.

# 요약하면...

이 Python 코딩 단계를 따라가며, Streamlit과 Plotly를 사용하여 망명 결정을 시각화하는 대화형 웹 애플리케이션을 만들어 보았습니다.

사용자들은 슬라이더를 사용하여 국가를 선택하고, 해당 국가로 이동하는 난민의 움직임에 대한 다중 시각적 집중을 제공할 수 있습니다.

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

Python의 Streamlit과 Plotly를 함께 사용하는 큰 장점 중 하나는 놀라울만큼 효율적인 코드를 만들 수 있다는 것입니다.

Plotly는 지도 및 차트 시각화를 생성하는 데 최적화되어 있고 Streamlit은 웹 인터페이스를 생성하는 데 최적화되어 있습니다.

완벽한 조합이라고 말할 수 있겠죠.

읽어 주셔서 감사합니다.

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

만약 이 유형의 이야기가 당신의 취향이고, 저를 작가로 지원하고 싶다면, 제 Substack를 구독해주세요.

Substack에서는 매주 뉴스레터와 다른 플랫폼에는 없는 기사들을 발행합니다.
