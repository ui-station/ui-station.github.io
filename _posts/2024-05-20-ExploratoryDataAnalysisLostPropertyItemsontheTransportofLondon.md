---
title: "탐색적 데이터 분석 런던 교통 수속에 잃어버린 물품"
description: ""
coverImage: "/assets/img/2024-05-20-ExploratoryDataAnalysisLostPropertyItemsontheTransportofLondon_0.png"
date: 2024-05-20 18:36
ogImage: 
  url: /assets/img/2024-05-20-ExploratoryDataAnalysisLostPropertyItemsontheTransportofLondon_0.png
tag: Tech
originalTitle: "Exploratory Data Analysis: Lost Property Items on the Transport of London"
link: "https://medium.com/towards-data-science/exploratory-data-analysis-lost-property-items-on-the-transport-of-london-5ffa519b24a6"
---


<img src="/assets/img/2024-05-20-런던 교통수단에서 분실물 품목의 탐색 데이터 분석_0.png" />

여러분께서도 짐을 버스에 두고 내려온 적이 있을 것 같아요. 다섯 분 뒤에 짐이 없어진 것을 깨닫지만 이미 버스가 떠났어요. 집에 돌아온 후 버스 회사 웹사이트를 확인해보니 분실한 짐을 청구할 수 있는 가능성이 있었고, 며칠 후 운종사에게 운종사에게 연락을 해서 짐을 다시 받을 수 있었어요. 저는 암스테르담에 살고 있는데, 여기의 대중교통은 iLost사와 협력 관계에 있어요. 누군가 분실한 물품을 청구할 수 있는 곳이랍니다. 이 사이트는 꽤 명확한 구조를 갖고 있으며 다른 사람들이 잊어버린 물품들을 볼 때에는 심지어 등록할 필요가 없어요 (개인 정보는 물론 숨겨져 있겠죠). 이런 데이터 지향적 사고방식이 있는 저는 ‘유레카’라는 순간을 겪었죠 — 이런 종류의 데이터는 문화 인류학적 관점에서 훌륭하다고 생각해서 대중교통 및 다른 장소에서 분실될 수 있는 물품에 대해 많은 것을 배울 수 있다고 생각했어요. 그러나 iLost의 라이선스 계약은 서면 동의 없이 데이터를 사용하는 것을 허가하지 않았기 때문에 제 질문에 아무도 답변하지 않았어요. 그럼에도 불구하고 이 아이디어를 마음에 품고 있던 저는 온라인에서 대안 소스를 찾기 시작했고 다음과 같은 것을 알게 되었어요:

- 런던 교통 (TfL)도 분실물 품목을 청구하기 위한 좋은 서비스를 갖고 있어요.
- 영국에는 공공 당국이 보유한 정보에 대한 공공 "접근 권리"를 만드는 정보의 자유법이 있어요. 모든 사람은 무료로 요청을 제기할 권리가 있으며 20일 이내에 응답을 받을 수 있어요. 그래서 가능하다면 TfL에게 "분실 물품의 원시 데이터"가 담긴 CSV 파일을 보내달라고 요청했고 2~3주 후에 (약속된 것처럼 빠르지 않았지만:) 파일을 받을 수 있었어요. 또한 TfL에게 이 데이터를 제 TDS 출판물에 사용할 수 있는지 물어봤고 긍정적인 답변을 받았죠.

이것은 좋은 서비스인 것 같아요 (그런데 미국은 1967년 이후로 비슷한 법을 가지고 있어요) 그리고 과학자와 데이터 애호가들에게 연구에 공개 데이터를 활용할 수 있는 좋은 기회인 것 같아요. 그러니 말이 많지 않게, 우리가 어떤 정보를 얻을 수 있는지 한번 살펴봅시다. 원본 파일을 얻어 결과를 재현하고 싶은 분들은 아래에 댓글을 남겨주시면 링크를 공유해드릴게요.

<div class="content-ad"></div>

## 데이터 로딩

우선 데이터를 로드하고 차원 및 유형을 확인해봅시다:

```python
import pandas as pd

df = pd.read_csv("tfl.csv")
display(df.head())
display(df.info(verbose=True)
```

출력 결과는 다음과 같습니다:

<div class="content-ad"></div>

마크다운 형식으로 변경하면 다음과 같습니다:


![Exploratory Data Analysis Lost Property Items on the Transport of London](/assets/img/2024-05-20-ExploratoryDataAnalysisLostPropertyItemsontheTransportofLondon_1.png)

아래에서 볼 수 있듯이 총 5245개의 항목이 있고 데이터프레임에는 NULL 항목이 없습니다. 날짜 형식이 표준이 아닌 것으로 보이므로 변환해보겠습니다:

```python
df["Date Found"] = pd.to_datetime(df["Date Found"], format="%d/%m/%Y")
```

다음 단계로 어떤 종류의 카테고리가 있는지 알아보겠습니다:


<div class="content-ad"></div>

```js
display(df["Category"].unique())
```

위 결과에서 숫자가 그리 크지 않다는 것을 보여줍니다:

```js
array(['Bags', 'Electronics & Technology', 'Wallets & Purses',
       'Baby & Nursery', 'ID & Personal Documents', 'Health & Beauty',
       'Tools / Garden / DIY', 'Jewellery', 'Travel Cards & Ticket',
       'Household & General Items', 'Keys', 'Sports & Leisure', 'Eyewear',
       'Currency (cash)', 'Stationery & Books', 'Clothing',
       'Financial Documents'], dtype=object)
```

시각화를 더 잘 보이게 하기 위해 "Electronics & Technology" 카테고리를 "Electronics"로 변경하고 모든 "Document" 카테고리를 한 가지로 결합하기로 결정했습니다:

<div class="content-ad"></div>

```js
def update_category(name: str) -> str:
    """카테고리 이름 업데이트"""
    if "문서" in name:
        return "문서"
    if "전자제품" in name:
        return "전자제품"
    return name

df["Category"] = df["Category"].map(update_category)
```

날짜와 카테고리 변환을 마친 후, 최종 데이터프레임은 다음과 같습니다:

<img src="/assets/img/2024-05-20-ExploratoryDataAnalysisLostPropertyItemsontheTransportofLondon_2.png" />

## 하루 평균 아이템


<div class="content-ad"></div>

이제 재미를 봐봅시다. 모든 항목을 날짜별로 그룹화하고 막대 차트를 그려봅시다:

```js
import plotly.express as px

gr_day = df[["Date Found"]].groupby(["Date Found"], as_index=False).size()

fig = px.bar(gr_day, x="Date Found", y="size",
             title="<span style='font-size:18px;'><b>TfL, Lost Items Per Day</b></span><b></b>",
             width=1280, height=500)
fig.update_xaxes(tickformat="%a, %d-%m-%Y", showline=True, linecolor="black")
fig.update_layout(xaxis_title=None, yaxis_title=None, plot_bgcolor="#F5F5F5",
                  margin=dict(l=50, r=50, t=30, b=50))
fig.show()
```

여기서는 오픈 소스 라이브러리 Plotly를 사용하여 차트를 작성했습니다. 이 라이브러리는 plotly.js를 기반으로 하므로 HTML 형식을 사용하여 스타일을 변경할 수 있습니다. 또한 출력물은 대화형이기 때문에 노트북에서 이미지를 바로 확대 또는 이동할 수 있습니다.

출력물은 다음과 같습니다:

<div class="content-ad"></div>

![이미지](/assets/img/2024-05-20-ExploratoryDataAnalysisLostPropertyItemsontheTransportofLondon_3.png)

이 결과를 보면 대중교통에서 물건을 분실할 확률을 계산하는 것이 흥미로울 것 같아요. 이 특정 날짜의 승객 수는 정확히 모르지만, 일반적으로 런던의 대중교통은 하루에 약 600만 명의 승객을 처리한다는 것을 알고 있습니다. 그래프에서 볼 수 있듯이, 분실 물품의 수는 꽤 일정합니다. 간단한 계산을 통해 대중교통에서 물건을 분실할 확률이 약 0.01% 정도라는 것을 알 수 있습니다. 이 값 자체는 크지 않지만, 매일 적어도 10,000 명의 사람 중 한 명은 이러한 사건을 겪을 수 있습니다.

또 다른 흥미로운 발견은 목요일에 뾰족한 부분이 있습니다. 왜 이렇게 발생하는지는 모르겠지만, 다른 TfL 보도자료에서도 목요일을 가장 많은 승객이 있는 날로 언급했어요. 이에 대한 사회적 설명이 있는지 알지 못하지만, 흥미롭게 보입니다.

## 카테고리

<div class="content-ad"></div>

다음 단계로 카테고리와 하위 카테고리별로 모든 상품을 그룹화해 보겠습니다:

```js
gr_cat = df[["Category",
             "Sub-Category"]].groupby(["Category",
                                       "Sub-Category"], as_index=False).size()
```

다양한 종류의 시각화를 시도해 보았고, 그 중에서도 가장 유익하다고 생각되는 두 가지를 소개하겠습니다. 먼저, 2차원 원형 차트와 같은 선버스트 차트를 그려보겠습니다:

```js
fig = px.sunburst(gr_cat, width=1280, height=800,
                  path=["Category", "Sub-Category"], values="size",
                  color="Category",
                  title="<span style='font-size:18px;'><b>TfL, Lost Items Chart</b></span><b></b>"
                  )
fig.update_layout(font_size=10, margin=dict(l=10, r=10, t=30, b=50))
fig.update_traces(textinfo="label+percent parent")
fig.show()
```

<div class="content-ad"></div>

다음처럼 출력됩니다:

![이미지](/assets/img/2024-05-20-ExploratoryDataAnalysisLostPropertyItemsontheTransportofLondon_4.png)

다양한 세그먼트를 쉽게 비교할 수 있습니다. 예를 들어, “가방”과 “전자제품”이 “유실 차트”의 상단에 있음을 확인할 수 있습니다(각각 19%와 15%). “가방” 카테고리에서는 승객들이 가장 자주(36%) 배낭을 잃어버립니다. 이는 배낭을 가지고 서거나 앉는 것이 불편하고, 사람들이 벗어 두기 때문에 이해할 수 있습니다. “전자제품” 카테고리에서는 모바일 폰이 가장 많습니다(58%). 다른 카테고리에서는 10%의 불운한 승객이 문서를 분실했고, 4%가 열쇠를 분실했습니다.

선셋 차트가 흥미로워 보이지만, 좁은 세그먼트를 읽기 어려울 수 있습니다. 다른 대안은 트리맵 차트입니다:

<div class="content-ad"></div>

```python
fig = px.treemap(gr_cat, width=1280, height=800,
                 path=['Category', 'Sub-Category'], values='size',
                 color='Category')
fig.update_traces(textinfo="label+percent parent")
fig.show()
```

위의 코드를 실행하면 이와 같은 출력물이 나옵니다:

<img src="/assets/img/2024-05-20-ExploratoryDataAnalysisLostPropertyItemsontheTransportofLondon_5.png" />

이 경우, 마우스를 호버하여 작은 하위 카테고리의 데이터를 쉽게 읽을 수 있습니다. 반면, 파이 차트를 사용하면 다른 카테고리 간의 상대적인 크기를 더 쉽게 이해할 수 있습니다.

<div class="content-ad"></div>

## 하위 카테고리

"전자제품" 카테고리를 자세히 살펴보겠습니다. 이를 위해 데이터프레임을 필터링하고, 모든 항목을 하위 카테고리별로 그룹화하여 막대 그래프를 그릴 수 있습니다:

```js
df_ = df[df["Category"] == "전자제품"]
gr_electronics = df_[["하위 카테고리"]].groupby(["하위 카테고리"], as_index=False).size().sort_values(by="size", ascending=True)

fig = px.bar(gr_electronics, width=1280, height=600, 
             title="<span style='font-size:18px;'><b>TfL, 분실물 주간별 전자제품</b></span><b></b>",
             x="size", y="하위 카테고리", orientation="h")
fig.update_layout(xaxis_title="수량", yaxis_title=None,
                  plot_bgcolor="#F5F5F5",
                  margin=dict(l=50, r=50, t=30, b=50))
fig.show()
```

여기에서는 라벨을 읽기 쉽게하기 위해 가로 막대 차트를 사용했습니다. 결과는 다음과 같습니다:

<div class="content-ad"></div>


![image](/assets/img/2024-05-20-ExploratoryDataAnalysisLostPropertyItemsontheTransportofLondon_6.png)

우리가 볼 수 있듯이, 대부분의 승객이 핸드폰이나 핸드폰 액세서리를 분실한 것이 분명합니다. 어떤 사람들은 노트북, 태블릿 또는 e-리더를 분실했으며, 한 명의 승객이 MP3 플레이어를 분실했습니다(요즘에는 조금 특이한 장치입니다). 놀랍게도 숫자가 상당히 많습니다. 데이터 집합은 일주일 동안의 데이터를 나타내며, 우리가 보기로는 이 기간 동안 총 467명의 승객이 핸드폰을 분실했습니다.

## 위치

간단한 연습으로 데이터를 위치별로 그룹화하고 어떤 물품이 발견된 상위 10곳을 확인해 봅시다:


<div class="content-ad"></div>

```js
gr_location = df[["Location"]].groupby(['Location'], as_index=False).size().sort_values(by="size", ascending=False)
display(gr_location[:10])
```

The output looks like this:

![Map Image](/assets/img/2024-05-20-ExploratoryDataAnalysisLostPropertyItemsontheTransportofLondon_7.png)

I have been to London several times, but my knowledge of London stations is not that good. Let’s draw all locations on the map, so it will be easier to see the places. I will use a Python geopy library to get the coordinates:```

<div class="content-ad"></div>

```python
from geopy.geocoders import Nominatim

geolocator = Nominatim(user_agent="Python3.9")

@lru_cache(maxsize=None)
def get_coord_lat_lon(full_addr: str) -> Tuple[float, float]:
    """ Get coordinates for address """
    pt = geolocator.geocode(full_addr + ", London, UK")
    return (pt.latitude, pt.longitude) if pt else (None, None)
```

여기서는 여러 번 코드를 실행하고 싶을 때 도움이 될 수 있는 lru_cache 데코레이터를 사용했어요. 데이터는 새로운 API 호출 대신 캐시에서 가져옵니다. 또한 처리 중에 진행률 표시줄을 볼 수 있게 해주는 tqdm Python 라이브러리를 사용했어요. 이는 처리가 몇 분 정도 걸리기 때문에 유용해요.

처리가 완료된 후에 데이터프레임은 다음과 같이 보입니다:


<div class="content-ad"></div>


![map](/assets/img/2024-05-20-ExploratoryDataAnalysisLostPropertyItemsontheTransportofLondon_9.png)

Now, we're ready to draw a map. I will use the folium Python library for that:

```python
import folium
from branca.element import Figure

fig = Figure(width=1024, height=600)
fmap = folium.Map(location=(51.5, -0.104), tiles="openstreetmap", zoom_start=12)

for _, row in gr_location.iterrows():
    point = row["Coordinates"]
    name, amount = row["Location"], row["size"]
    if point[0] is not None or point[1] is not None:
        add_to_map(fmap, name, point, amount)

fig.add_child(fmap)
display(fig)
```

I also created `add_to_map` and `value_to_color` methods, which help to add a station to the map:


<div class="content-ad"></div>

```python
def value_to_color(value: int) -> str:
    """ 값을 HTML 색상으로 변환합니다 """
    norm = matplotlib.colors.Normalize(vmin=0, vmax=255, clip=True)
    mapper = colormap.ScalarMappable(norm=norm, cmap=colormap.inferno)
    r, g, b, _ = mapper.to_rgba(value, alpha=None, bytes=True)
    return "#" + f"{(r << 16) + (g << 8) + b:#08x}"[2:]


def add_to_map(fmap: folium.Map, name: str,
               location: Tuple[float, float],
               value: int):
    """ 지도에 점을 추가합니다 """
    color_str = value_to_color(value)
    folium.Circle(
        location=location,
        radius=10*value//2,
        popup = name + ": " + str(value),
        color=color_str,
        fill=True,
        fill_color=color_str
    ).add_to(fmap)
```

결과는 다음과 같습니다:

<img src="/assets/img/2024-05-20-ExploratoryDataAnalysisLostPropertyItemsontheTransportofLondon_10.png" />

이 지도는 물건이 발견된 장소를 보여줍니다. 물건을 분실한 장소에 대해서는 알 수 없습니다 (특히 빠르게 이동하는 기차에서), 하지만 결과를 보는 것은 여전히 흥미로울 것입니다. 지도의 가장 큰 원들은 마지막 기차나 버스 정류장인 것으로 보입니다. 그러나 다른 역에서도 많은 물건들이 발견되었습니다. 독자들은 색상 맵 (제가 "Inferno" 팔레트를 사용했습니다) 및 원 크기와 같은 매개변수를 변경하여 더 나은 시각화를 얻을 수도 있습니다.


<div class="content-ad"></div>

## 결론

본 글에서는 공식 기관에게 공공 데이터를 요청하는 가능성과 이를 분석하여 런던 교통 수단에서 잃어버린 물품에 대한 흥미로운 결과를 얻을 수 있었습니다. 일반적으로 대중에게 공공 정보에 액세스할 수 있는 기회를 제공하는 것은 연구원과 데이터 애호가들이 흥미로운 데이터 조각을 찾는 데 도움이 되는 좋은 아이디어입니다. 어쨌든 통계는 우리에 관한 과학이죠. 결과적으로, 대중 교통에서 무언가를 분실할 확률은 0.01%에 해당한다는 것을 알 수 있었고, 앞으로 이 통계의 일부로 포함되지 않기를 바라며 모든 독자분들께 간청합니다 ;)

사회 데이터 분석에 관심 있는 분들은 다음 기사들도 읽어보세요:

- 데이터 탐색적 분석: 유튜브 채널에 대해 우리가 아는 것은 무엇인가
- 독일 주택 임대 시장: Python으로 수행하는 데이터 탐색적 분석
- 기후에 관해 사람들이 무엇을 쓰는지: Python에서 트위터 데이터 클러스터링
- 트위터 글에서 시간적인 패턴 찾기: Python을 사용한 데이터 탐색적 분석
- Python 데이터 분석: 팝송에 대해 우리가 아는 것은 무엇인가?

<div class="content-ad"></div>

만약 이 이야기를 즐겼다면 Medium을 구독해보세요. 그러면 새 글이 올라올 때 알림을 받을 수 있을 뿐만 아니라 다른 작가들의 수천 개 이야기에도 완전한 액세스 권한을 얻을 수 있어요. LinkedIn을 통해 연락하셔도 좋고요. 이와 다른 포스트들의 전체 소스 코드를 얻고 싶으시다면 제 Patreon 페이지를 방문해주세요.

읽어주셔서 감사합니다.