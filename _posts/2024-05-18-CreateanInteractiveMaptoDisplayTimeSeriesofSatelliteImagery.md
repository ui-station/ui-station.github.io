---
title: "위치 기반의 위성 이미지의 시계열을 표시하는 대화형 지도 만들기"
description: ""
coverImage: "/assets/img/2024-05-18-CreateanInteractiveMaptoDisplayTimeSeriesofSatelliteImagery_0.png"
date: 2024-05-18 17:58
ogImage: 
  url: /assets/img/2024-05-18-CreateanInteractiveMaptoDisplayTimeSeriesofSatelliteImagery_0.png
tag: Tech
originalTitle: "Create an Interactive Map to Display Time Series of Satellite Imagery"
link: "https://medium.com/towards-data-science/create-an-interactive-map-to-display-time-series-of-satellite-imagery-e9346e165e27"
---



![image](/assets/img/2024-05-18-CreateanInteractiveMaptoDisplayTimeSeriesofSatelliteImagery_0.png)

# Table of Contents

- 🌟 Introduction
- 📌 Area of Interest (AOI)
- 💾 Loading Sentinel-2 Imagery
- ⏳ Extracting Time Series from Satellite Imagery
- 🌍 Developing an Interactive Map with Time Series
- 📄 Conclusion
- 📚 References

## 🌟 Introduction


<div class="content-ad"></div>

한동안 사용자가 특정 위치를 클릭할 때 상호작용 맵에 시계열 데이터가 표시되는 쉽고 직관적인 방법을 찾고 있었습니다. 저는 folium 라이브러리를 탐색하고 위성 이미지에서 추출한 시계열 데이터베이스를 folium과 연결하는 방법을 알아냈습니다. 오늘은 내 방법을 공유하겠습니다.

이 게시물에서는 두 가지 함수를 작성할 것입니다. 첫 번째 함수는 위성 데이터를 다운로드하지 않고 로드하고, 두 번째 함수는 데이터와 타임스탬프를 추출하여 데이터 형식의 시계열을 생성합니다. 그런 다음 AOIs(관심 지역)를 순환하여 두 함수를 실행하고 AOI에 대한 시계열 데이터를 추출할 것입니다. 마지막으로 생성된 시계열 데이터와 folium 라이브러리를 사용하여 이를 상호작용적인 지도에 지리적으로 표시할 것입니다.

이 게시물을 마치면 어떤 변수나 매개변수에 대해 추출된 시계열 데이터를 상호작용적인 맵과 시각적으로 표시할 수 있게 될 것입니다. 예를 들어, 캘리포니아 호수 면적의 시계열을 추출하고 상호작용적 지도에 표시하겠습니다. 그러나 흥미를 갖거나 이러한 조언과 꿀팁을 찾고 있었다면 계속 읽어보세요!

## 📌 관심 지역 (AOI)

<div class="content-ad"></div>

소개에서 언급한 대로 상호작용적 지도에서 어떤 위치의 변수에 대한 시계열 데이터를 표시하려면 다음 단계를 따를 수 있습니다. 이 예시에서는 캘리포니아의 몇 개 호수의 물 픽셀을 계수하고 2024년에 촬영된 모든 Sentinel-2 이미지를 사용하여 표면적을 계산할 것입니다. QGIS에서 그 호수 주변에 폴리곤을 그리고 그것들을 shapefile로 저장했습니다. 관심 영역 (AOI)에 대한 shapefile을 만드는 방법을 배우고 싶다면, Medium의 첫 번째 스토리에서 "🛠️ QGIS에서 Shapefile 생성"이라는 섹션을 참조해주세요.

다음은 QGIS에서 호수 주변에 그린 폴리곤의 스냅샷입니다:

![lakes](/assets/img/2024-05-18-CreateanInteractiveMaptoDisplayTimeSeriesofSatelliteImagery_1.png)

## 💾 Sentinel-2 영상 불러오기

<div class="content-ad"></div>

이 섹션의 목표는 다운로드 없이 아카이브된 위성 이미지를 메모리에 로드하는 것입니다. 특정 지역에 대한 오랜 기간에 걸친 위성 이미지 다운로드는 시간이 많이 소요되며 계산 비용이 많이 들며 비효율적일 수 있습니다. 특히 전체 장면에서 작은 영역의 변화를 탐색하려면 문제가 될 수 있습니다.

이러한 문제를 극복하기 위해 12줄의 코드만 사용하여 다운로드 없이 위성 이미지를 로드하는 방법을 보여주는 포스트를 작성했습니다. 해당 포스트에서 확인할 수 있습니다.

이 섹션에서는 해당 포스트에서 제시된 템플릿을 사용하여 함수를 작성할 것입니다. 이 함수를 사용하면 특정 기간에 대한 AOI 위성 이미지를 쉽게 로드할 수 있습니다. 해당 기간이 길든 짧든 상관없이:

```python
from pystac_client import Client
from odc.stac import load

def search_satellite_images(collection="sentinel-2-l2a",
                            bbox=[-120.15,38.93,-119.88,39.25],
                            date="2023-01-01/2023-03-12",
                            cloud_cover=(0, 10)):
    """
    Collection, 범위, 날짜 범위 및 구름 덮개를 기반으로 위성 이미지를 검색합니다.

    :param collection: Collection 이름 (기본값: "sentinel-2-l2a").
    :param bbox: 경계 상자 [min_lon, min_lat, max_lon, max_lat] (기본값: Lake Tahoe 지역).
    :param date: 날짜 범위 "YYYY-MM-DD/YYYY-MM-DD" (기본값: "2023-01-01/2023-12-30").
    :param cloud_cover: 구름 덮개 범위를 나타내는 Tuple (최소, 최대) (기본값: (0, 10)).
    :return: 검색 기준에 따라 로드된 데이터.
    """
    # 검색 클라이언트 정의
    client=Client.open("https://earth-search.aws.element84.com/v1")
    search = client.search(collections=[collection],
                            bbox=bbox,
                            datetime=date,
                            query=[f"eo:cloud_cover<{cloud_cover[1]}", f"eo:cloud_cover>{cloud_cover[0]}"])

    # 일치하는 항목 수 출력
    print(f"발견된 이미지 수: {search.matched()}")

    data = load(search.items(), bbox=bbox, groupby="solar_day", chunks={})

    print(f"데이터에서의 날짜 수: {len(data.time)}")

    return data
```

<div class="content-ad"></div>

이 함수를 사용하면 위성 이미지 검색을 위한 매개변수를 지정할 수 있어서 다양한 관심 영역 및 시간대에 유연하고 쉽게 사용할 수 있습니다. 수집, 경계 상자, 날짜 범위 및 구름 양 등과 같은 기준에 따라 위성 이미지를 검색합니다. 이는 이미지를 찾기 위해 Earth Search API를 사용하고 일치하는 수를 출력하며 큐브 형식으로 클립 된 이미지를 반환합니다.

## ⏳ 위성 영상에서 시계열 추출

이제 AOI를 위해 클립 된 이미지를로드하는 함수가 있으므로 찾고있는 정보를 추출하는 두 번째 함수를 정의해야합니다. 이미지와 함께 필요한 정보를 추출하고 맵에 표시하는 다음 단계에서 사용할 수 있도록 DataFrame에 넣어 시계열 데이터베이스로 고려할 수 있습니다. 다시 한 번 필요한 데이터를 추출 할 수 있지만, 전체 캘리포니아 호수의 표면적을 볼 수있는 것이 흥미로울 것으로 생각되어 2024 년에 Sentinel-2로 촬영 된 모든 이미지에서 최근 이미지를 포함하여 주요 캘리포니아 호수의 표면적을 볼 수 있습니다.

이를 위해 Sentinel-2 이미지의 씬 분류 레이어에 있는 물 픽셀로 분류 된 픽셀을 추출 할 수 있습니다. 다시 말해, 각 씬에서 물 픽셀 수를 세어야합니다. 픽셀 해상도가 10m x 10m임을 감안하면 수를 100 제곱 미터 (10m x 10m)로 곱하면 각 호수의 표면적이 나올 것입니다. 그러나 여기서는 위성에 의해 촬영 된 이미지가 각 씬에서 전체 호수를 커버하도록해야합니다. 이를 설명하기 위해 1 월 7 일과 1 월 4 일에 촬영 된 이 두 장의 이미지 중 하나에 캡처된 호수 중 하나를 살펴 보겠습니다.

<div class="content-ad"></div>


<div class="content-ad"></div>

```python
import pandas as pd
import numpy as np

def count_water_pixels(data,lake_id):
    """
    각 시점의 Sentinel-2 SCL 데이터에서 물 픽셀 수를 계산합니다.

    :param data: Sentinel-2 SCL 데이터가 포함된 xarray Dataset입니다.
    :return: 날짜, 물 횟수 및 눈 횟수가 포함된 DataFrame입니다.
    """

    water_counts = []
    date_labels = []
    water_area = []
    coverage_ratio = []

    # 시간 단계 수를 확인합니다.
    numb_days = len(data.time)

    # 각 시간 단계를 반복합니다.
    for t in range(numb_days):
        scl_image = data[["scl"]].isel(time=t).to_array()
        dt = pd.to_datetime(scl_image.time.values)
        year = dt.year
        month = dt.month
        day = dt.day

        date_string = f"{year}-{month:02d}-{day:02d}"
        print(date_string)

        # 물에 해당하는 픽셀 수를 계산합니다.
        count_water = np.count_nonzero(scl_image == 6)  # 물

        surface_area = count_water * 10 * 10 / (10 ** 6)

        count_pixels = np.count_nonzero((scl_image == 1) | (scl_image == 2) | (scl_image == 3) | (scl_image == 4) | (
                    scl_image == 5) | (scl_image == 6) | (scl_image == 7) | (scl_image == 8) | (scl_image == 9) | (
                                               scl_image == 10) | (scl_image == 11))
        total_pixels = data.dims['y'] * data.dims['x']

        coverage = count_pixels * 10 * 10 / 1e6
        lake_area = total_pixels * 10 * 10 / 1e6

        ratio = coverage / lake_area

        print(coverage)
        print(lake_area)
        print(ratio)

        if ratio < 0.8:
            continue

        # 추가
        water_counts.append(count_water)
        date_labels.append(date_string)
        water_area.append(surface_area)
        coverage_ratio.append(ratio)

    # 날짜 레이블을 pandas datetime 형식으로 변환합니다.
    datetime_index = pd.to_datetime(date_labels)

    # DataFrame을 구성하기 위한 딕셔너리 생성
    data_dict = {
        'Date': datetime_index,
        'ID': lake_id,
        'Water Counts': water_counts,
        'Pixel Counts': count_pixels,
        'Total Pixels': total_pixels,
        'Coverage Ratio': coverage_ratio,
        'Water Surface Area': water_area
    }

    # DataFrame 생성
    df = pd.DataFrame(data_dict)

    return df
```

이 함수는 데이터셋의 각 시간 단계를 반복하여 물 픽셀 수를 계산하고 표면적을 계산하며 커버리지 비율을 계산합니다. 커버리지 비율이 80% 미만이면 시간 단계가 건너뜁니다. 그런 다음 횟수, 날짜, 표면적 및 커버리지 비율을 리스트에 추가하고 해당 값과 물 ID 및 총 픽셀 수가 포함된 DataFrame을 반환합니다.

커버리지 문제와 해결하는 속임수에 대해 자세히 알아보려면 이 포스트의 섹션 (📈 통계 파일에서 대염해 지역의 시계열)을 참조해주세요:

## 🌍 시계열과 함께 상호작용하는 지도 개발하기

<div class="content-ad"></div>

이 섹션에서는 세 개의 스크립트를 작성할 것입니다. 첫 번째 스크립트는 다각형(AOI)의 바운딩 박스와 중심 좌표를 추출하는 함수입니다. 첫 번째 함수(search_satellite_images)를 실행하려면 바운딩 박스가 필요하며, 맵에 호수를 표시하는 데 중심 좌표가 필요합니다. 다음 코드로 이 작업을 수행할 수 있습니다:

```js
import geopandas as gpd
import pandas as pd

def get_centroids_and_bboxes(shapefile_path):
    """
    shapefile을 처리하여 각 다각형의 ID, 중심점, 바운딩 박스(bbox)를 포함하는 DataFrame을 반환합니다.
    :param shapefile_path: shapefile의 경로.
    :return: 각 다각형의 ID, 중심점, 및 bbox가 있는 pandas DataFrame.
    """

    # shapefile 불러오기
    gdf = gpd.read_file(shapefile_path)

    # EPSG:4326으로 재투영
    gdf_proj = gdf.to_crs("EPSG:4326")

    centroids = []
    bboxes = []

    # 각 다각형을 처리하여 중심점과 bbox 얻기
    for index, row in gdf_proj.iterrows():
        # 중심점
        centroid_lat = row.geometry.centroid.y
        centroid_lon = row.geometry.centroid.x
        centroids.append((centroid_lat, centroid_lon))

        # 바운딩 박스
        minx, miny, maxx, maxy = row.geometry.bounds
        bbox = (minx, miny, maxx, maxy)
        bboxes.append(bbox)

    # DataFrame 생성
    df = pd.DataFrame({
        'ID': gdf_proj.index,
        'Centroid_Lat': [lat for lat, lon in centroids],
        'Centroid_Lon': [lon for lat, lon in centroids],
        'BBox_Min_Lon': [bbox[0] for bbox in bboxes],
        'BBox_Min_Lat': [bbox[1] for bbox in bboxes],
        'BBox_Max_Lon': [bbox[2] for bbox in bboxes],
        'BBox_Max_Lat': [bbox[3] for bbox in bboxes]
    })

    return df

shapefile_path = "lakes_boundry.shp"
lakes_df = get_centroids_and_bboxes(shapefile_path)
print(lakes_df)
```

위 단계를 따르고 코드를 성공적으로 실행하면, 다음 형식의 다각형에 대한 유사한 DataFrame이 표시될 것입니다:

<img src="/assets/img/2024-05-18-CreateanInteractiveMaptoDisplayTimeSeriesofSatelliteImagery_4.png" />

<div class="content-ad"></div>

다음 스크립트는 2024년 센티넬-2에 의해 촬영된 모든 이미지를 호수 위에서 루핑하고 두 번째 함수를 실행하여 커버리지 비율이 80%보다 높은 경우 각 이미지에서 표면적을 계산하며 각 호수의 표면적을 시계열로 보여주는 DataFrame을 보고하는 것을 포함합니다:

```python
import matplotlib.pyplot as plt
import pandas as pd
from datetime import datetime
import numpy as np

all_water_pixels_dfs = [] 

for lake_id in lakes_df.ID:
    print(lake_id)
    lake_df = lakes_df[lakes_df['ID'] == lake_id]

    if not lake_df.empty:
        bbox = [lake_df.iloc[0].BBox_Min_Lon, lake_df.iloc[0].BBox_Min_Lat,
                lake_df.iloc[0].BBox_Max_Lon, lake_df.iloc[0].BBox_Max_Lat]

        data = search_satellite_images(collection="sentinel-2-l2a",
                                       date="2024-01-01/2024-05-14",
                                       cloud_cover=(0, 5),
                                       bbox=bbox)
        # Pass the lake_id
        water_pixels_df = count_water_pixels(data, lake_id)

        # Append
        all_water_pixels_dfs.append(water_pixels_df)

# Concatenate all DataFrames into a single DataFrame
final_df = pd.concat(all_water_pixels_dfs, ignore_index=True)
```

최종 DataFrame은 이미지 날짜, 물 픽셀 수, 총 픽셀 수, 커버리지 비율 및 표면적을 요약하여 다음과 같이 보여집니다:

![이미지](/assets/img/2024-05-18-CreateanInteractiveMaptoDisplayTimeSeriesofSatelliteImagery_5.png)


<div class="content-ad"></div>

거의 다 왔어요!

지도 상에서 시계열을 보기 위해 마지막 한 단계가 남았습니다. 이제 표면적의 시계열 데이터가 있으므로 Folium 라이브러리를 사용하여 두 가지를 표시할 수 있습니다: (1) 지도상의 호수 중심을 지점으로 표시하고 (2) 각 호수를 클릭하면 팝업으로 표면적의 시계열을 보여주는 그래프를 표시합니다. 다음 코드로 이 작업을 수행할 수 있습니다:

```js
import folium
import plotly.express as px
import os

# 시계열 플롯을 그려 HTML로 저장하는 함수
def plot_timeseries_for_spot(spot_id, ts_df):
    df_spot = ts_df[ts_df['ID'] == spot_id]
    print(df_spot)
    fig = px.line(df_spot, x='Date', y='Water Surface Area', title=f'Time Series for Lake {spot_id}')

     # X 및 Y 축 레이블 추가
    fig.update_layout(
        xaxis_title="Date",
        yaxis_title="Water Surface Area (sq km)"
    )

    filepath = f'tmp_{int(spot_id)}.html'
    fig.write_html(filepath, include_plotlyjs='cdn')
    return filepath

# 지도 생성
m = folium.Map(location=[35.5, -119.5], zoom_start=7)

# Plotly 시계열 팝업이 있는 마커 추가
for index, row in lakes_df.iterrows():
    html_path = plot_timeseries_for_spot(row['ID'], final_df)
    iframe = folium.IFrame(html=open(html_path).read(), width=500, height=300)
    popup = folium.Popup(iframe, max_width=2650)
    folium.Marker([row['Centroid_Lat'], row['Centroid_Lon']], popup=popup).add_to(m)

m.save('map_with_timeseries.html')

# 임시 HTML 파일 정리
for spot_id in lakes_df['ID']:
    os.remove(f'tmp_{spot_id}.html')
```

이 스크립트에서는 함수가 각 호수의 시계열 데이터를 필터링하고, Plotly를 사용하여 라인 플롯을 생성하고, 플롯을 HTML 파일로 저장합니다. 다음으로 Folium을 사용하여 지도를 초기화합니다. 그런 다음 호수 DataFrame을 반복하면서, 각 호수의 중심 좌표에 마커를 추가하고, 각 마커에 팝업을 연결하여 시계열 플롯을 표시합니다. 최종 지도는 HTML 파일로 저장됩니다. 마지막으로, Plotly 플롯에 생성된 임시 HTML 파일을 삭제하여 정리합니다.

<div class="content-ad"></div>

완료되었습니다!

콘텐츠 폴더에 생성된 HTML 파일을 열면 지도에 표시된 각 호수의 중심 좌표를 볼 수 있습니다.

![지도](/assets/img/2024-05-18-CreateanInteractiveMaptoDisplayTimeSeriesofSatelliteImagery_6.png)

각 호수를 클릭하여 시계열이 표시되는지 확인해 보겠습니다.

<div class="content-ad"></div>

이 모든 노력 끝에 이렇게 실용적인 지도가 만들어졌네요, 맞나요? :D

## 📄 결론

<div class="content-ad"></div>

거의 매달 새로운 패키지와 라이브러리들이 나와서 데이터를 추출하고 분석하며 표시하고 시각화하는 법을 실용적으로 제공합니다. 그러나 이 분야에서 아직 남아 있는 두 가지 과제가 있습니다. 첫 번째는 데이터를 정확하게 분석하기 위해서는 테라바이트 또는 페타바이트에서 추출된 데이터가 정확한지 확인하기 위해 충분한 경험이 필요합니다. 두 번째는 이러한 라이브러리들을 연결하여 의미 있는 것을 만들어내는 아키텍처를 만드는 것입니다.

이미지 처리에서는 처리된 데이터에서의 간단한 실수가 중대한 오류로 이어질 수 있는 점을 강조해보았습니다. 시각화 부분에서는 Folium, Plotly, 그리고 새로운 이미지를 추출하기 위한 API를 연결하여, 리모트 센싱 관측을 사용하여 다양한 현상을 모니터링하는 유용한 도구를 만들 수 있음을 보여주었습니다. 이 글을 읽는 데 즐거움을 느끼시기를 바라며, 궁금한 사항이 있으시면 언제든지 연락 주세요.

## 📚 참고 자료

https://github.com/stac-utils/pystac-client/blob/main/docs/quickstart.rst

<div class="content-ad"></div>

https://www.element84.com/earth-search/examples/

Sentinel 데이터용 Copernicus Sentinel 데이터 [2024]

Copernicus 서비스 정보용 Copernicus 서비스 정보 [2024]

📱 더 많은 흥미로운 콘텐츠를 제공하는 다른 플랫폼에서 저와 소통하세요! LinkedIn, ResearchGate, Github 및 Twitter.

<div class="content-ad"></div>

이 링크를 통해 확인할 수 있는 관련 게시물이 있습니다: