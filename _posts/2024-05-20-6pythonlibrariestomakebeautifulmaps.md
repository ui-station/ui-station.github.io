---
title: "아름다운 지도를 만들기 위한 6가지 파이썬 라이브러리"
description: ""
coverImage: "/assets/img/2024-05-20-6pythonlibrariestomakebeautifulmaps_0.png"
date: 2024-05-20 18:32
ogImage: 
  url: /assets/img/2024-05-20-6pythonlibrariestomakebeautifulmaps_0.png
tag: Tech
originalTitle: "6 python libraries to make beautiful maps"
link: "https://medium.com/@alexroz/6-python-libraries-to-make-beautiful-maps-9fb9edb28b27"
---


```markdown
![image](/assets/img/2024-05-20-6pythonlibrariestomakebeautifulmaps_0.png)

어느 순간 모든 데이터 과학자는 지리 공간 데이터를 분석하거나 모델링할 필요에 직면하게 되며, 결정적인 시각적 부분 없이는 수행할 수 없습니다. 저는 지도를 좋아하는 사람이라서, 여기에서 정보를 제공하게 된 이 6가지 멋진 라이브러리를 공유해 주어 기쁩니다. 여기에서 공유하는 라이브러리들 중 일부는 정적 시각화에 더 적합하고, 다른 것들은 대화식 시각화에 더 적합하기 때문에 해결할 수 있는 문제 범위가 넓습니다.

# 1. Cartopy

![image](/assets/img/2024-05-20-6pythonlibrariestomakebeautifulmaps_1.png)
```

<div class="content-ad"></div>

Cartopy는 스칼라 또는 폴리곤 데이터를 사용하여 정적 지도를 그리는 데 완벽한 강력한 잘 알려진 라이브러리입니다. 땅, 물 및 행정 국경에 많은 내장된 레이어를 제공합니다. 매우 쉽게 사용할 수 있으며 직관적인 명령어 세트가 있습니다.

예를 들어, MODIS 데이터를 시각화해 보겠습니다. 함께 따라오고 싶다면 코드를 여기에서 찾을 수 있습니다.

패키지를 설치하려면 다음과 같이 pip로 정규 표현식을 사용할 수 있습니다:

```js
!pip install cartopy
```

<div class="content-ad"></div>

이제 데이터를 로드해 봅시다:

```js
import numpy as np
import matplotlib.pyplot as plt

lats = np.load('lats.npy')
lons = np.load('lons.npy')
data = np.load('data.npy')
```

그 후에 데이터를 바로 플롯할 수 있어요:

```js
proj = ccrs.PlateCarree() #지도 투영을 설정해 봅시다

fig, ax = plt.subplots(subplot_kw=dict(projection=proj), figsize=(10, 20))#미리 설정한 투영과 크기를 가진 figure를 생성해야 해요

ax.set_extent([-160, -105, 40 ,70], crs=ccrs.PlateCarree())#MODIS 제품 지역만 포함하도록 좌표를 제한합시다

plt.contourf(lons, lats, data,
             transform=ccrs.PlateCarree(), cmap = 'summer') #matplotlib을 사용하여 데이터의 등고선을 추가합시다
'''좋은 cartopy 기능 추가하기'''
ax.add_feature(cfeature.BORDERS, edgecolor='black', linewidth=1) 
ax.add_feature(cfeature.LAKES,  alpha=0.5)
ax.add_feature(cfeature.LAND)
ax.add_feature(cfeature.COASTLINE, edgecolor='black', linewidth=1)
ax.add_feature(cartopy.feature.RIVERS, edgecolor='blue', linewidth=0.5)
states_provinces = cfeature.NaturalEarthFeature(
            category='cultural',  name='admin_1_states_provinces',
            scale='10m', facecolor='none')
ax.add_feature(states_provinces, edgecolor='black', zorder=10, linestyle = '-', linewidth=0.5)


ax.gridlines(draw_labels=True)#그리드 형식 지정

lon, lat = -122.8414, 55.1119 
ax.plot(lon,lat,  'bo', markersize=6, color = 'red', transform=ccrs.Geodetic())#지도에 임의의 마커 추가하기
```

<div class="content-ad"></div>

```
![Image 1](/assets/img/2024-05-20-6pythonlibrariestomakebeautifulmaps_2.png)

위 결과에서 알 수 있듯이, cartopy는 맵을 사용자 정의하는 다양한 방법을 제공합니다. 색상, 선 두께, 밀도 및 레이어의 다른 매개변수를 수동으로 설정할 수 있습니다. 게다가 코드 자체가 정말 직관적이고 이해하기 쉽습니다.

이 라이브러리의 또 다른 큰 장점은 사용할 수 있는 다양한 투영법들인데, cartopy를 사용하여 시각화할 수 있는 데이터 범위가 매우 넓습니다!

![Image 2](/assets/img/2024-05-20-6pythonlibrariestomakebeautifulmaps_3.png)
```

<div class="content-ad"></div>

```markdown
![Python libraries to make beautiful maps](/assets/img/2024-05-20-6pythonlibrariestomakebeautifulmaps_4.png)

Cartopy is one of the tools I regularly use in my work, and I hope that you’ll find it extremely helpful as well!

## 2. Folium

This library is probably the most popular in the industry, since it’s interactive (it has JS under the hood) and highly customizable. And to start plotting (after installation) you can simply call:
```

<div class="content-ad"></div>

```md
import folium
map = folium.Map(location=(50, 0), zoom_start=8) # 위치 - 맵의 중앙, 확대 수준 - 해상도
map
```

![이미지](/assets/img/2024-05-20-6pythonlibrariestomakebeautifulmaps_5.png)

다른 기본 타일 또는 사용자 정의 타일을 사용할 수 있습니다:

```md
map = folium.Map(location=(50, 0), zoom_start=8, tiles="Cartodb Positron")
map
```

<div class="content-ad"></div>

```markdown
![image](/assets/img/2024-05-20-6pythonlibrariestomakebeautifulmaps_6.png)

이제 세계 국가들을 추가해보겠습니다. 그러려면 기본 geopandas 데이터프레임을 사용할 거에요:

```python
import geopandas as gpd
df = gpd.read_file(gpd.datasets.get_path('naturalearth_lowres'))

map = folium.Map(zoom_start=4, tiles="Cartodb Positron")
gdf_json = df.to_json()

folium.GeoJson(gdf_json).add_to(map)
map
```

![image](/assets/img/2024-05-20-6pythonlibrariestomakebeautifulmaps_7.png)
```

<div class="content-ad"></div>

# 3. Plotly

Plotly는 아름다운 대화형 차트로 유명한 또 다른 라이브러리입니다. 다양한 기능 중에는 px.choropleth, px.choropleth_mapbox, px.scatter_mapbox, px.scatter_geo 등의 지도를 그리는 여러 함수가 있습니다. 더 자세한 내용은 [여기](https://plotly.com/python/)에서 확인할 수 있습니다.

실제로 같은 지리 데이터셋을 사용하여 gdp_md_est 변수를 시각화해 보겠습니다. 다음 코드 몇 줄로 쉽게 수행할 수 있습니다:

```python
import plotly.express as px

fig = px.choropleth(df, locations='iso_a3', hover_name='name', 
                    color='gdp_md_est',
                    projection='natural earth')
fig.show()
```

<div class="content-ad"></div>

```markdown
![image](https://miro.medium.com/v2/resize:fit:1400/1*XGb8bY8LL26sKv2Wmkb-AQ.gif)

# 4. ipyleaflet

네 소개할 라이브러리는 'ipyleaflet'입니다. 이것은 상호작용형 지도를 만들기 위한 또 다른 훌륭한 JS 기반 라이브러리입니다. 이 패키지가 좋은 이유 중 하나는 타일의 종류가 많다는 것이에요. 그러니 기본적인 것부터 시작해봅시다:

```js
from ipyleaflet import Map

m = Map(center=(45, 2), zoom=5)
m
```
```

<div class="content-ad"></div>

```markdown
![image](/assets/img/2024-05-20-6pythonlibrariestomakebeautifulmaps_8.png)

별 거 없죠? 그럼 이제 MODIS Terra 제품을 배경지도로 사용해 봅시다!

```js
m = Map(basemap=basemap_to_tiles(basemaps.NASAGIBS.ModisTerraTrueColorCR, "2023-08-08"),
    center=(45, 2), zoom=5)
m
```

![image](/assets/img/2024-05-20-6pythonlibrariestomakebeautifulmaps_9.png)
```

<div class="content-ad"></div>

VIIRS 데이터를 사용하여 밤에 지구를 시각화할 수도 있어요:

```js
m = Map(basemap=basemaps.NASAGIBS.ViirsEarthAtNight2012,
    center=(45, 2), zoom=5)
m
```

<img src="/assets/img/2024-05-20-6pythonlibrariestomakebeautifulmaps_10.png" />

다른 옵션들도 함께 살펴보세요! 옵션이 많이 있답니다.

<div class="content-ad"></div>

이제 실제 데이터를 시각화해 봅시다. 발견한 라이브러리 중 가장 인상적인 기능 중 하나는 속도 시각화입니다. 이를 위해 1° 해상도를 가진 NCEP(WMC) 예측 데이터를 사용할 수 있습니다. 해당 데이터셋은 2016년 4월 30일 06:00에 가져와졌고, ipyleaflet 라이브러리의 문서에서 제공되었습니다. 이를 읽기 위해 netCDF4 파일을 읽는 데 적합한 xarray를 사용할 것입니다.

```js
from ipyleaflet.velocity import Velocity
import xarray as xr
import os
import requests

if not os.path.exists('wind-global.nc'):
  url = 'https://github.com/benbovy/xvelmap/raw/master/notebooks/wind-global.nc'
  r = requests.get(url)
  wind_data = r.content
  with open('wind-global.nc', 'wb') as f:
      f.write(wind_data)

m = Map(center=(45, 2), zoom=4, interpolation='nearest', basemap=basemaps.CartoDB.DarkMatter)

ds = xr.open_dataset('wind-global.nc')

wind = Velocity(data=ds,
                zonal_speed='u_wind',
                meridional_speed='v_wind',
                latitude_dimension='lat',
                longitude_dimension='lon',
                velocity_scale=0.01,
                max_velocity=20)
m.add(wind)

m
```

<img src="https://miro.medium.com/v2/resize:fit:1400/1*ImNN4p5UxlISf-p22XbGGQ.gif" />

보시다시피 결과물은 단순히 상호작용형 지도가 아니라 애니메이션 지도입니다. 그렇기 때문에 데이터의 표현력을 향상시키고 데이터가 말하도록 만들어 줍니다!

<div class="content-ad"></div>

# 5. geemap

Geemap은 Google Earth Engine과 통합된 대화식 맵핑을 위한 패키지입니다. 따라서 Python GEE 패키지인 ee 라이브러리와 함께 사용할 때 매우 편리합니다.

데모로 북유럽 한 섬에서 Dynamic World 제품의 토지 피복 데이터를 수집해 보겠습니다:

```js
import ee

반지름 = 1250
지점 = ee.Geometry.Point([19.9, 60.2])
roi = 지점.buffer(반지름) # 관심 지점 주변에 원 만들기

DW = ee.ImageCollection("GOOGLE/DYNAMICWORLD/V1")\
                  .filterDate(start = '2022-07-08', end='2022-08-30')\
                  .filterBounds(roi) # 데이터 가져오기
DW_list = DW.toList(DW.size()) # 데이터를 GEE 리스트로 변환
```

<div class="content-ad"></div>

이제 그래픽 플로팅을 할 수 있어요:

```js
m = geemap.Map(center=[60.2, 19.9], zoom=14)

m.add_basemap('HYBRID') # Sentinel-2 이미지 레이어를 추가하고 있어요
viz_params = {'bands':'label', 'min':0, 'max':8,
'palette':['419bdf',
    '397d49',
    '88b053',
    '7a87c6',
    'e49635',
    'dfc35a',
    'c4281b',
    'a59b8f',
    'b39fe1']}
m.add_ee_layer(ee.Image(DW_list.get(9)), viz_params) # 구름 커버리지가 낮은 이미지 №9를 추가하였어요
m.add_legend(title="Dynamic World Land Cover", builtin_legend='Dynamic_World')
display(m)
```

<img src="https://miro.medium.com/v2/resize:fit:1400/1*Rj0DI3lXmhlSdmRK7IiRKg.gif" />

geemap은 GEE와 함께 사용하기 훌륭한 도구라고 생각해요. 다양한 기능을 제공하여 다양한 작업을 해결할 수 있어요. 주요하고 유일한 단점은 사용자 친화적이지 않다는 점이에요. geemap을 사용하기 전에 ee 라이브러리 문법을 알아야 하고, GEE가 어떻게 작동하는지 일반적으로 이해해야 해요.

<div class="content-ad"></div>

# 6. ridgemap

이 라이브러리는 마지막이자 정말로 제가 가장 좋아하는 라이브러리입니다. 왜냐하면 이 라이브러리를 사용하면 예술작품 같은 독특한 플롯을 만들 수 있기 때문이죠.

그래프를 그리기 전에 두 라이브러리를 설치해보겠습니다:

```js
!pip install ridge_map mplcyberpunk
```

<div class="content-ad"></div>

이제 맵을 만들어 봅시다:

```js
import matplotlib.pyplot as plt
from ridge_map import FontManager, RidgeMap
import ridge_map as rm
import mplcyberpunk
import matplotlib.font_manager as fm

plt.style.use("cyberpunk")
plt.rcParams["figure.figsize"] = (16,9)

fm = FontManager('https://github.com/google/fonts/blob/main/ofl/arbutusslab/ArbutusSlab-Regular.ttf?raw=true')

r = RidgeMap(bbox=(-15, 32, 45,90), font=fm.prop) # 맵 생성

values = r.get_elevation_data(num_lines=200) # 고도 데이터 가져오기
values = r.preprocess(values=values, # 하이퍼파라미터 설정
   water_ntile=70,
   vertical_ratio=40,
   lake_flatness=3)

r.plot_map(values, label="Europe", label_x=0.4,label_y=-0.05, label_size=60, line_color=plt.get_cmap('inferno'), background_color="#212946")
mplcyberpunk.add_glow_effects() # 빛나는 효과 추가
```

<img src="/assets/img/2024-05-20-6pythonlibrariestomakebeautifulmaps_11.png" />

내 의견으로는 이것이 정말 멋지네요! 이 라이브러리를 확인하고 다른 시각화를 찾아보고 자신만의 시각화를 올려보세요 :)

<div class="content-ad"></div>

나중에 이 라이브러리들이 유용하고 당신의 도구상자에 포함할 가치가 있다고 느끼길 바랍니다.

지도를 만드는 데 사용하는 라이브러리는 무엇인가요? 댓글로 공유해주세요👇

===========================================

부. (지리)데이터 과학, 머신러닝/인공지능, 기후 변화에 열정적입니다. 그래서 어떤 프로젝트에서 함께 일하고 싶다면 여기나 LinkedIn에서 연락주세요.

<div class="content-ad"></div>

더 많은 내용을 보려면 팔로우해주세요! 🚀🌌