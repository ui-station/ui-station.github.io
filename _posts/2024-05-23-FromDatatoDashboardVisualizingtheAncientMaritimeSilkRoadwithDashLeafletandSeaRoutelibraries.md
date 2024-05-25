---
title: "데이터부터 대시보드까지 Dash Leaflet 및 SeaRoute 라이브러리를 사용해 고대 해상 실크로드 시각화하기"
description: ""
coverImage: "/assets/img/2024-05-23-FromDatatoDashboardVisualizingtheAncientMaritimeSilkRoadwithDashLeafletandSeaRoutelibraries_0.png"
date: 2024-05-23 15:29
ogImage: 
  url: /assets/img/2024-05-23-FromDatatoDashboardVisualizingtheAncientMaritimeSilkRoadwithDashLeafletandSeaRoutelibraries_0.png
tag: Tech
originalTitle: "From Data to Dashboard: Visualizing the Ancient Maritime Silk Road with Dash Leaflet and SeaRoute libraries"
link: "https://medium.com/towards-data-science/from-data-to-dashboard-visualizing-the-ancient-maritime-silk-road-with-dash-leaflet-and-searoute-ac8a521ac4e9"
---


![img](/assets/img/2024-05-23-FromDatatoDashboardVisualizingtheAncientMaritimeSilkRoadwithDashLeafletandSeaRoutelibraries_0.png)

저의 박사학위 중 어려운 부분 중 하나는 선박의 해양 노선을 보여주는 대화형 지도 시각화물을 만드는 것이었습니다. 출발지와 도착지 항구 사이의 선박 노선은 육 지역을 건너가지 않고 오직 바다 상에서의 경로여야 했습니다. 이중 seemingly straightforward 였던 작업이 파이썬에서 처음부터 구현하려고 시도할 때 상당히 어려움이 있었습니다 🤷‍♀️. 유사한 작업을 수행할 수 있는 상용 솔루션이 Marine Traffic과 같이 있지만, 저는 오랜 시간 동안 찾을 수 없는 오픈 소스 대안을 찾고 있었습니다. 마침내 2022년 말에 SeaRoute 라이브러리가 Python을 위해 출시되었고(이전에는 Java만 지원되고 있었음), 그로 인해 제 삶이 훨씬 수월해졌습니다. 이 기사에서는 Dash 앱을 위한 대화형 지도 시각화물을 만드는 과정을 안내하겠습니다. Dash Leaflet 및 SeaRoute Python 라이브러리를 사용하여 해상 경로를 표시할 수 있도록 할 것입니다.

# Dash, Dash Leaflet, SeaRoute에 대해 어떻게 생각하시나요?

Dash는 React.js를 기반으로 만들어진 강력한 Python 프레임워크로, Python의 모든 계산 능력을 통합하고 있습니다. Dash가 무엇이며 무엇을 할 수 있는지, 어떻게 첫 번째 Dash 앱을 만들고 실행할 수 있는지에 대한 간단한 소개는 이전에 작성한 포스트를 참조해 보세요. Dash에 새로 오신 분이라면, 먼저 이것을 읽어보는 것을 추천합니다...

<div class="content-ad"></div>

이 게시물에서는 고대 해상 실크로드 경로를 예시로 사용하여 Dash Leaflet 라이브러리와 SeaRoute 라이브러리의 사용을 소개하겠습니다.

Dash Leaflet은 상호작용하는 Leaflet 스타일 지도를 Dash 앱에 통합할 수 있는 포꺠 갑을 제공하는 넓은 범위의 지도 시각화 Python 라이브러리입니다. Dash 생태계 내에서 Leaflet.js의 래퍼로, 마커, 다각형, 팝업, 레이어 등과 같은 다양한 기능을 갖춘 지도를 만들고 사용자 정의할 수 있는 구성 요소를 제공합니다.

SeaRoute는 해상에서 점 간 경로를 계산하는 Python 라이브러리로, 예를 들어 휴스턴과 로테르담 항구 사이의 경로를 계산할 수 있습니다. Python에서 이를 처음부터 계산하려면 해양 지역을 나타내는 해양 형상 파일(예: 해상 국경)을 얻고 지도상에 그린 후 출발점과 도착점 사이의 땅정 경로를 계산하여 해당 해양 지역만 횡단하도록 제약을 설정해야 합니다. 이는 넉넉히 말해 매우 복잡한 작업인데, 각 나라마다 자체 데이터를 다른 형식과 소스로 제공하는 등, 통일되고 표준적인 데이터 소스가 아직 존재하지 않기 때문입니다. 시각화만을 위해 이러한 작업을 해야 한다는 것이 좀 과한 것 같죠. 그렇다면 아름다운 지도 시각화가 필요한 여성은 이번 생애에서 어떻게 해결해야 할까요😠? 다행히 2022년 말에 SeaRoute 라이브러리가 출시되며 시같에 경로를 쉽게 계산하여 시같화할 수 있는 문제를 해결했습니다. 아래 지도에서도 확인할 수 있습니다😇.

따라서 이 게시물의 나머지 부분에서는:

<div class="content-ad"></div>

- SeaRoute를 사용하여 바다 상의 두 지점 사이의 경로 좌표를 계산하세요.
- Dash Leaflet을 사용하여 지도상에 경로를 시각화하세요.
- 모든 것을 Dash 앱으로 묶어보세요.

일단 시작해봐요! 🤸‍♀️️

# 해상 실크로드에 대해서 어떻게 생각하세요?

<div class="content-ad"></div>

해양 실크로드는 고대 시대에 아시아, 아프리카 및 유럽 대륙 간 다양한 문명을 연결한 중요한 해상 무역로였습니다. 이것은 더 넓고 잘 알려진 실크로드 네트워크의 연장선으로, 육지 및 해상 노선을 포함하며 동서양 간 무역, 문화 교류 및 아이디어 및 기술의 전파를 용이하게 했습니다.

중국의 한나라 시대(기원전 206년 ~ 서기 220년)에 시작된 해양 실크로드는 통(618~907년)과 송(960~1279년) 시대에 절정에 이르렀습니다. 남중국해와 인도양을 건너는 중국 선원들은 동남아시아, 인도, 아라비아 반도, 동아프리카 등지의 상인들과 실크, 도자기, 차 등과 같은 상품을 거래했습니다. 그에 대한 보답으로 중국은 향신료, 귀금속, 보석, 이국적인 상품을 수입함으로써 문화와 경제를 발전시켰습니다. 해양 실크로드의 쇠퇴는 14세기경 즈음에 시작되어 해상 무역로가 변화하고 새로운 무역 경로가 나타남에 따라 17세기에는 전통적인 실크로드와 해양 실크로드의 경로가 대부분 사라지며 유럽의 지배력으로 새로운 세계 무역로들이 등장했습니다.

해양 실크로드를 따라가는 몇 가지 대표적인 경로 (이후 이 게시물의 나머지 부분에서 지도로 시각화할 예정)는 다음과 같습니다:

- Quanzhou ` Malacca ` Calicut ` Aden ` Alexandria
- Guangzhou ` Manila ` Brunei ` Surabaya ` Jakarta ` Singapore
- Hangzhou ` Ningbo ` Nagasaki ` Busan ` Hakata ` Osaka
- Guangzhou ` Hanoi ` Da Nang ` Singapore ` Colombo ` Muscat
- Xiamen ` Taiwan ` Okinawa ` Yokohama ` Kobe ` Nagasaki ` Busan

<div class="content-ad"></div>

해양로 라이브러리는 여러 가지 흥미로운 계산을 제공합니다. 예를 들어, 루트의 길이나 주어진 속도로 여행하는 데 걸리는 시간을 계산할 수 있습니다. 이번 포스트에서는 중국 존크(해양 실크로드 시대에 널리 사용된 선박 종류)가 평균 5 노트의 속도로 항해했다고 가정해 봅시다.

![image](/assets/img/2024-05-23-FromDatatoDashboardVisualizingtheAncientMaritimeSilkRoadwithDashLeafletandSeaRoutelibraries_1.png)

# 대시 앱 구축

## 환경 설정

<div class="content-ad"></div>

먼저 모든 것에 앞서, 필요한 라이브러리가 설치되어 있는지 확인해야 합니다. 이러한 라이브러리는 Dash, Dash-Leaflet 및 SeaRoute이며, pip를 사용하여 쉽게 설치할 수 있습니다:

```js
pip install dash dash-leaflet searoute
```

그런 다음, 다음과 같이 가져올 수 있습니다:

```js
import dash
from dash import dcc, html
import dash_leaflet as dl
from dash.dependencies import Input, Output
from searoute import SeaRoute
```

<div class="content-ad"></div>

다음으로, 간단히 Dash 앱의 빈 인스턴스를 초기화할 수 있습니다:

```js
app = dash.Dash(__name__)
```

일반적으로, Dash 앱은 두 가지 주요 구성 요소로 구성됩니다: 레이아웃(layout)과 콜백(callbacks). 레이아웃 구성 요소는 앱의 시각적 및 구조적 부분을 정의하며, 콜백 구성 요소는 앱의 상호 작용을 설명합니다. 그러나 앱의 레이아웃에 더 들어가기 전에 요구되는 데이터가 사용 가능한지 확인해야 합니다.

## 데이터 가져오기

<div class="content-ad"></div>

먼저, 해상 실크로드의 일부 지표적인 항구 및 해당 좌표, 간단한 설명을 담은 표를 작성했어요. 항구 좌표는 추정치이며 OpenStreetMap에서 가져온 것이며, 오픈 데이터베이스 라이선스(ODbL)를 따라 사용되었습니다.

<img src="/assets/img/2024-05-23-FromDatatoDashboardVisualizingtheAncientMaritimeSilkRoadwithDashLeafletandSeaRoutelibraries_2.png" />

```js
import pandas as pd

# 항구, 좌표 및 설명을 포함하는 표 작성
ports_data = {
    '항구': ['아덴', '알렉산드리아', '브루나이', '부산', '칼리컷',
            '콜롬보', '다낭', '광저우', '하카타', '항저우',
             '하노이', '자카르타', '고베', '말라카', '마닐라',
             '무스카트', '나가사키', '닝보', '오키나와', '오사카',
             '전주', '싱가포르', '수라바야', '대만', '시안멘', '요코하마'],
    '국가': ['예멘', '이집트', '브루나이', '대한민국', '인도',
                '스리랑카', '베트남', '중국', '일본', '중국',
                '베트남', '인도네시아', '일본', '말레이시아', '필리핀',
                '오만', '일본', '중국', '일본', '일본',
                '중국', '싱가폴', '인도네시아', '대만', '중국', '일본'],
    '위도': [12.799, 31.2001, 4.5353, 35.1796, 11.2588,
                 6.9271, 16.0544, 23.1291, 33.5904, 30.2741,
                 21.0285, -6.2088, 34.6901, 2.1896, 14.5995,
                 23.6102, 32.7467, 29.8683, 26.2041, 34.6937,
                 24.8798, 1.3521, -7.2575, 23.6978, 24.4798, 35.4437],
    '경도': [45.0289, 29.9187, 114.7277, 129.0756, 75.7804,
                  79.8612, 108.2022, 113.2644, 130.4017, 120.1551,
                  105.8542, 106.8456, 135.1955, 102.2501, 120.9842,
                  58.5922, 129.8734, 121.544, 127.6476, 135.5023,
                  118.5876, 103.8198, 112.7521, 120.9605, 118.0894, 139.638],
    '설명': ['붉은해 상거래 경로의 중요한 중심지', '지중해 주요 항구, 유럽과의 연결', '향균판매의 주요 중지점, 이슬람 영향', '실크로드에 대한 한국의 관문', '번성한 무역 중심지, 향신료 무역의 중심',
                    '인도양 무역로의 주요 항구', '잠바 왕조 시기 중요한 항구', '고대 중국 무역 항구, 산동이라 불림', '실크로드 무역에 중요한 일본의 항구', '대운하의 끝, 실크 생산 중심지',
                    '베트남의 수도, 고대 무역 도시', '인도네시아의 수도, 자바의 주요 항구', '중요한 일본의 항구, 교토로 향하는 관문', '전략적인 해협, 무역의 십자로', '아시아에서 스페인 무역의 중심지',
                    '중요한 아라비아 해상 무역 기지', '세계로 향하는 일본의 관문, 네덜란드 무역의 중심', '주요 해상 항구, 중국 무역에 필수', '동아시아 무역로의 중요한 경유지', '일본의 주요항구, 역사적 무역 중심지',
                    '중국의 주요 항구, 주요 무역 중심지', '전략적인 해협, 주요 무역 기지', '자바의 중요한 항구, 인도네시아 무역에 중요', '해상 무역용 섬 중간지점', '중국의 역사적 항구, 후난으로가는 관문', '외국무역에 개방된 주요 일본의 항구']
}

ports = pd.DataFrame(ports_data)
```

그 위에, 지도에서 시각화하고 싶은 일부 지표적인 경로(항구 순서)를 정의하고 그것을 DataFrame에 저장합니다:

<div class="content-ad"></div>

```js
route_1 = ['Quanzhou','Malacca','Calicut','Aden','Alexandria']
route_2 = ['Guangzhou','Manila','Brunei','Surabaya','Jakarta','Singapore']
route_3 = ['Hangzhou','Ningbo','Nagasaki','Busan','Hakata','Osaka']
route_4 = ['Guangzhou','Hanoi','Da Nang','Singapore','Colombo','Muscat']
route_5 = ['Xiamen','Taiwan','Okinawa','Yokohama','Kobe','Nagasaki','Busan']

routes = pd.DataFrame({
    'Route': ['route_1', 'route_2', 'route_3', 'route_4', 'route_5'],
    'Port_Sequence': [route_1, route_2, route_3, route_4, route_5]
})
```

## 경로 계산

필요한 계산을 수행하기 위해 일반적인 route_var 변수를 가정하고, 이 경로에 포함된 각 항구의 이름과 좌표를 DataFrame으로 구조화합니다. 보다 구체적으로, 이 코드는 주어진 경로의 각 항구를 반복하고 그들의 좌표 및 설명을 검색합니다.

```js
route_rows = [] 

for port in route_var: # 경로에 있는 항구를 반복하면서
  port_name = port
  lat = ports.loc[ports['Port'] == port, 'Latitude'].iloc[0]
  lon = ports.loc[ports['Port'] == port, 'Longitude'].iloc[0]
  row = {'Port': port_name, 'lat': lat, 'lon': lon}
  route_rows.append(row)

route_df = pd.concat([pd.DataFrame(row, index=[0]) for row in route_rows], ignore_index=True)
```

<div class="content-ad"></div>

지도 시각화를 시작하려면 Dash-Leaflet 라이브러리의 dl.Marker(), dl.Tooltip 및 dl.LayerGroup() 구성 요소를 사용하여 포트를 마커로 시각화하기 위한 지도 객체를 쉽게 생성할 수 있습니다.

```js
    # 계산된 포트 마커에서 지도 객체 생성
    markers = []
    for i in range(len(route_df)):
        # 각 포트 마커에 대한 툴팁 생성
        tooltip = route_df.loc[i, 'Port'] + ', ' + ports.loc[ports['Port'] == route_df.loc[i, 'Port'], 'Description'].iloc[0]
        markers.append(  # 마커 계산
            dl.Marker(
                position=(route_df.loc[i, 'lat'], route_df.loc[i, 'lon']),
                children=[dl.Tooltip(tooltip)]
            )
        )
    cluster = dl.LayerGroup(children=markers)
```

이 방법을 사용하면 선택한 경로의 각 포트에 대해 dl.Marker()를 사용하여 마커를 생성하고 dl.Tooltip()을 사용하여 툴팁을 만듭니다. 그런 다음 dl.LayerGroup()를 사용하여 이러한 마커를 단일 레이어 그룹으로 그룹화합니다. LayerGroup() 구성 요소는 여러 지도 요소(마커 등)를 단일 레이어로 그룹화하는 데 사용됩니다. 이를 통해 이러한 요소를 함께 관리하고 제어할 수 있습니다. 예를 들어, 사용자가 한 번의 동작으로 모든 마커를 표시하거나 숨길 수 있으며, 마커를 하나씩 선택하는 대신 모두 선택할 수 있습니다.

바다에서의 경로 계산으로 넘어가면, 아래와 같이 SeaRoute 라이브러리를 사용하여 이를 달성할 수 있습니다:```

<div class="content-ad"></div>

```js
# 바다에서 경로 계산하기
markers_line = []
length = 0
duration_hours = 0
for i in range(0, len(route_df) - 1):
    origin = [route_df.loc[i, 'lon'], route_df.loc[i, 'lat']]
    destination = [route_df.loc[i+1, 'lon'], route_df.loc[i+1, 'lat']]
    searoutes_coords = sr.searoute(origin, destination, append_orig_dest=True, speed_knot=2)
    searoutes_coords_transposed = [[coord[1], coord[0]] for coord in searoutes_coords['geometry']['coordinates']]
    markers_line += searoutes_coords_transposed
   
    length += searoutes_coords['properties']['length']
    duration_hours += searoutes_coords['properties']['duration_hours']
duration_days = duration_hours / 24
```

이전에 언급했듯이 SeaRoute 라이브러리를 사용하면 경로 거리와 항해 시간을 계산하는 것과 같은 추가 속성을 계산할 수 있습니다. 여기에서 속도가 sr.searoute() 함수에서 speed_knot = 5로 정의된 것을 확인하세요.

SeaRoute로 맵 좌표를 계산한 후 dl.Polyline() 구성 요소를 사용하여 맵에 시각화할 수 있습니다. 또한 dl.PolylineDecorator() 구성 요소를 사용하여 선 방향을 나타내는 화살표를 추가할 수 있습니다. patterns 변수에서 간단한 화살표를 직접 정의합니다.

```js
# 계산된 바다 경로용 맵 객체 생성
line = dl.Polyline(
    positions=markers_line,
    smoothFactor=1.0,
    color='ForestGreen',
    weight=1,
    lineCap='round',
    lineJoin='round'
)
patterns = [dict(offset='5%', repeat='30px', endOffset='10%', arrowHead=dict(pixelSize=8, polygon=False, pathOptions=dict(stroke=True, color='ForestGreen', weight=1, opacity=10, smoothFactor=1)))]
dline = dl.PolylineDecorator(children=line, patterns=patterns)
```

<div class="content-ad"></div>

또한, 각 선택된 노선마다 지도의 중심과 영역을 다시 계산하는 것이 적절하다고 생각했습니다. 이렇게 하면 사용자가 선택한 각 노선마다 지도가 다시 초점을 맞춰서, 마커와 선이 적절하게 표시됩니다.

```js
# 경계 계산
min_lat = min(lat for lat, lon in markers_line) - 2
max_lat = max(lat for lat, lon in markers_line) + 2
min_lon = min(lon for lat, lon in markers_line) - 2
max_lon = max(lon for lat, lon in markers_line) + 2
bounds = [[min_lat, min_lon], [max_lat, max_lon]]

# 중심 계산
x, y = zip(*markers_line)
centroid = [sum(x) / len(x), sum(y) / len(y)]
```

마지막으로, 이러한 요소들을 하나의 함수로 묶어 보겠습니다.

```js
# 노선 포트의 해상 경로 지도 마커 및 경로 계산 함수 정의
def get_route_line(route_var):
    
    route_rows = [] 
    for port in route_var: # 노선 내 포트를 반복
        port_name = port
        lat = ports.loc[ports['Port'] == port, 'Latitude'].iloc[0]
        lon = ports.loc[ports['Port'] == port, 'Longitude'].iloc[0]
        row = {'Port': port_name, 'lat': lat, 'lon': lon}
        route_rows.append(row)
   
    route_df = pd.concat([pd.DataFrame(row, index=[0]) for row in route_rows], ignore_index=True)
   
    # 계산된 포트 마커에서 맵 개체 생성
    markers = []
    for i in range(len(route_df)):
        # 각 포트 마커에 대한 툴팁 생성
        tooltip = route_df.loc[i, 'Port'] + ', ' + ports.loc[ports['Port'] == route_df.loc[i, 'Port'], 'Description'].iloc[0]
        markers.append(  # 마커 계산
            dl.Marker(
                position=(route_df.loc[i, 'lat'], route_df.loc[i, 'lon']),
                children=[dl.Tooltip(tooltip)]
            )
        )
    cluster = dl.LayerGroup(children=markers)
   
    # 해상 경로 계산
    markers_line = []
    length = 0
    duration_hours = 0
    for i in range(0, len(route_df) - 1):
        origin = [route_df.loc[i, 'lon'], route_df.loc[i, 'lat']]
        destination = [route_df.loc[i+1, 'lon'], route_df.loc[i+1, 'lat']]
        searoutes_coords = sr.searoute(origin, destination, append_orig_dest=True, speed_knot=2)
        searoutes_coords_transposed = [[coord[1], coord[0]] for coord in searoutes_coords['geometry']['coordinates']]
        markers_line += searoutes_coords_transposed
       
        length += searoutes_coords['properties']['length']
        duration_hours += searoutes_coords['properties']['duration_hours']
    duration_days = duration_hours / 24

    # 계산된 해상 경로를 위한 맵 개체 생성
    line = dl.Polyline(
        positions=markers_line,
        smoothFactor=1.0,
        color='ForestGreen',
        weight=1,
        lineCap='round',
        lineJoin='round'
    )
    patterns = [dict(offset='5%', repeat='30px', endOffset='10%', arrowHead=dict(pixelSize=8, polygon=False, pathOptions=dict(stroke=True, color='ForestGreen', weight=1, opacity=10, smoothFactor=1)))]
    dline = dl.PolylineDecorator(children=line, patterns=patterns)
    
    # 경계 계산
    min_lat = min(lat for lat, lon in markers_line) - 2
    max_lat = max(lat for lat, lon in markers_line) + 2
    min_lon = min(lon for lat, lon in markers_line) - 2
    max_lon = max(lon for lat, lon in markers_line) + 2
    bounds = [[min_lat, min_lon], [max_lat, max_lon]]
    
    # 중심 계산
    x, y = zip(*markers_line)
    centroid = [sum(x) / len(x), sum(y) / len(y)]

    return cluster, dline, centroid, bounds, duration_days
```

따라서, 모든 계산은 두 개의 함수로 처리됩니다: 
```

<div class="content-ad"></div>

## 레이아웃 생성

get_route_line() 함수를 정의한 후, 이제 앱의 레이아웃 컴포넌트를 구성할 차례입니다. 아래 이미지와 유사한 레이아웃을 만들려고 합니다:

![이미지](/assets/img/2024-05-23-FromDatatoDashboardVisualizingtheAncientMaritimeSilkRoadwithDashLeafletandSeaRoutelibraries_3.png)

구체적으로는 다음 사항을 통합하고 싶습니다:

<div class="content-ad"></div>

- 루트 선택 컨테이너는 루트를 선택할 수있는 드롭다운 메뉴와 선택한 루트, 가정된 선박 속도, 그리고 예상 항해 기간과 같은 각 루트에 대한 정보를 표시합니다.
- 선택한 루트의 항구 및 해당 해상 경로를 표시하는 지도 시각화가 있습니다.

드롭다운 패널은 다음과 같이 정의할 수 있습니다:

```js
# 드롭다운 및 루트 정보를 위한 왼쪽 패널
    html.Div([
        html.H1('고대 해상 실크로드'),
        html.Div([
            dcc.Dropdown(
                id='route_dropdown',
                options=[{'label': route, 'value': route} for route in routes['Route']],
                placeholder='루트 선택'
            )
        ], style={'display': 'block', 'height': '30%', 'justify-content': 'center', 'color': 'gray'}),
        html.Div(id='route_info', style={'height': '100%'})
    ], style={'display': 'inline-block', 'height': '100%', 'width': '15%', 'background-color': '#17408B', 'color': 'white', 'padding': '2%', 'position': 'relative'}),
```

더 구체적으로, 드롭다운 메뉴는 이전에 정의한 routes DataFrame에 의해 채워집니다. 또한, 루트 정보 패널은 초기에 비어 있고 드롭다운 메뉴에서 루트를 선택하면 콜백을 통해 채워질 것입니다.

<div class="content-ad"></div>

지도 시각화에 관한 내용은 다음과 같이 정의할 수 있습니다:

```js
# 지도 우측 패널
    html.Div([
        dl.Map(children=dl.LayersControl(
            [
                dl.BaseLayer(dl.TileLayer(url='https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png'), id='map_base', checked=True, name='기본 맵')
            ] +
            [
                dl.Overlay(children=[], id='route_lines', checked=True, name='경로 방향'),
                dl.Overlay(children=[], id='route_markers', checked=True, name='항구')
            ]
        ), id='routess_map', zoom=3)
    ], style={'display': 'inline-block', 'height': '100%', 'width': '85%', 'background-color': 'white', 'box-sizing': 'border-box'})
]
```

dl.Map() 구성 요소가 dl.BaseLayer() 구성 요소를 통해 선택한 기본 지도를 포함하고 있음에 주목하세요. 또한 dl.Overlay()로 정의된 다른 지도 객체도 포함됩니다. 여기서도 dl.Overlay()는 초기에 비어 있으며, 이전에 정의한 get_route_line() 함수를 사용하여 드롭다운 메뉴에서 경로를 선택하면 내용이 채워집니다.

마지막으로 드롭다운 메뉴와 지도 컨테이너를 모두 부모 컨테이너에 포함시켜 앱의 레이아웃 구성 요소로 할당할 수 있습니다:

<div class="content-ad"></div>

```js
# Dash 앱 초기화
app = Dash(__name__)

# 레이아웃 정의
app.layout = html.Div([
    # 드롭다운 및 경로 정보용 왼쪽 패널
    html.Div([
        html.H1('고대 해상 실크로드'),
        html.Div([
            dcc.Dropdown(
                id='route_dropdown',
                options=[{'label': route, 'value': route} for route in routes['Route']],
                placeholder='경로를 선택하세요'
            )
        ], style={'display': 'block', 'height': '30%', 'justify-content': 'center', 'color': 'gray'}),
        html.Div(id='route_info', style={'height': '100%'})
    ], style={'display': 'inline-block', 'height': '100%', 'width': '15%', 'background-color': '#17408B', 'color': 'white', 'padding': '2%', 'position': 'relative'}),
    
    # 지도용 오른쪽 패널
    html.Div([
        dl.Map(children=dl.LayersControl(
            [
                dl.BaseLayer(dl.TileLayer(url='https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png'), id='map_base', checked=True, name='기본 지도')
            ] +
            [
                dl.Overlay(children=[], id='route_lines', checked=True, name='경로 방향'),
                dl.Overlay(children=[], id='route_markers', checked=True, name='항구')
            ]
        ), id='events_map', zoom=3)
    ], style={'display': 'inline-block', 'height': '100%', 'width': '85%', 'background-color': 'white', 'box-sizing': 'border-box'})
], style={'display': 'flex', 'height': '100vh', 'width': '100vw', 'position': 'fixed', 'margin': '-8px', 'justify-content': 'center', 'boxSizing': 'border-box'})
```

## 콜백 설정

Dash 앱의 레이아웃을 설정했으니, 다음 단계는 앱의 상호작용성을 정의하는 것입니다. 드롭다운 메뉴에서 경로를 선택하면 지도에 해당 마커 및 라인이 나타나며, 해당 경로 정보도 표시됩니다. 다음과 같이 하나의 콜백 함수로 이를 구현할 수 있습니다:

```js
@app.callback(
    Output('route_markers', 'children'),
    Output('route_lines', 'children'), 
    Output('routes_map', 'center'), 
    Output('routes_map', 'bounds'),
    Output('route_info', 'children'),
    Input('route_dropdown', 'value')
)
def update_map_lines(selected_route):
    if selected_route is None:
        bounds = [[-50, -80], [50, 80]]
        centroid = [0, 0]
        return [], [], centroid, bounds, []
    else:
        route_var = routes.loc[routes['Route'] == selected_route, 'Port_Sequence'].iloc[0]
        cluster, dline, centroid, bounds, duration_days, length = get_route_line(route_var)
        
        route_name = selected_route.replace('_', ' ').title()
        route_info = [
            html.P([html.B("경로: "), route_name]),
            html.P([html.B("거리: "), f"{length:.0f} km"]),
            html.P([html.B("속도: "), "2 knots"]),
            html.P([html.B("소요 시간: "), f"{duration_days:.0f} days"]),           
        ]
        
        return cluster, [dline], centroid, bounds, route_info
```

<div class="content-ad"></div>

이 콜백은 이전에 만들었던 get_route_line() 함수를 사용하여 마커와 라인 지도 객체를 생성하고, 맵의 중심과 경계를 다시 계산하며 표시할 경로 정보를 계산합니다.

## 앱 테스트

레이아웃과 콜백 구성 요소를 정의한 후에, 우리의 앱은 준비가 되어 있고 다음 코드를 작성하여 실행할 수 있습니다:

```js
if __name__ == '__main__':
    app.run_server(debug=True)
```

<div class="content-ad"></div>

그러면 전체 앱 파일을 실행할 수 있습니다. 모든 것이 올바르게 완료되었다면 이와 유사한 결과가 나올 것입니다:

![image](/assets/img/2024-05-23-FromDatatoDashboardVisualizingtheAncientMaritimeSilkRoadwithDashLeafletandSeaRoutelibraries_4.png)

✨그리고 와라✨

Dash 앱은 로컬호스트 서버에서 실행되며 표시된 URL을 통해 웹 브라우저에서 액세스할 수 있습니다. 이렇게 하면 앱의 완전히 작동하는 인스턴스를 볼 수 있고 디버깅할 수 있습니다.

<div class="content-ad"></div>

# 내 생각 속으로

데이터 분석과 시각화에서 Dash와 같은 사용자 정의 보고 도구가 유연성과 사용 편의성으로 인해 인기를 얻고 있습니다. Power BI나 Tableau와 같은 셀프 서비스 도구와 달리 미리 구축된 시각화 옵션을 많이 제공하는 Dash는 보고서 디자인과 기능에 대해 완전한 제어를 제공합니다. 이를 통해 특정 사용자 요구 사항을 충족하기 위해 완전히 사용자 정의된 보고서와 시각화를 작성할 수 있습니다.

예를 들어, 이 게시물에서 보이는 지도 시각화는 사용자 지정 데이터 시각화 도구를 사용하지 않으면 상당히 어렵거나 불가능할 수 있습니다. 우리는 Tableau와 같은 도구를 사용한다면 루트 좌표를 따로 계산하고 저장한 다음 지도 위에 시각화해야 합니다. 심지어 방향성 있는 선을 생성하는 것조차 꽤 번거로울 것입니다. 이러한 수준의 사용자 정의는 데이터 전문가들에게 Dash가 점점 선호되는 이유를 강조합니다.

✨읽어 주셔서 감사합니다!✨

<div class="content-ad"></div>

이 게시물을 즐겼나요? 함께 친구가 되어요!

💌 저와 함께 Medium이나 LinkedIn에서 만나요!

💼 Upwork에서 저와 함께 일해보세요!