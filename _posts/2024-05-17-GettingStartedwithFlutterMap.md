---
title: "플러터 맵 시작하기"
description: ""
coverImage: "/assets/img/2024-05-17-GettingStartedwithFlutterMap_0.png"
date: 2024-05-17 17:59
ogImage:
  url: /assets/img/2024-05-17-GettingStartedwithFlutterMap_0.png
tag: Tech
originalTitle: "Getting Started with Flutter Map"
link: "https://medium.com/@raphaeldelio/getting-started-with-flutter-map-9cf4113f22e9"
---

<img src="/assets/img/2024-05-17-GettingStartedwithFlutterMap_0.png" />

몇 일 전에, 각 나라에서 입력된 단어의 번역을 보여주는 앱을 만들어보기로 한 아이디어를 갖게 되었습니다. 나는 언어가 지리적으로 어떻게 발전하고 변하는지를 확인하고 싶었습니다.

![image](https://miro.medium.com/v2/resize:fit:1400/1*UIc4JVMEX-FV6U4DJkI2Pg.gif)

이전에 모바일 앱을 만들어본 적이 없었고 지도를 다룬 적도 없지만, 어쨌든 한 번 시도해 보고 싶었습니다.

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

여러 코드 베이스에 의존하지 않고 iOS, Android 및 Web으로 코드를 컴파일할 수있는 능력은 Flutter로 개발하기로 결정했던 이유였습니다. Flutter에는 다양한 라이브러리가 있어 시작하기 쉬울 것이라고 생각했기 때문이죠.

Flutter Map은 매우 사용자 정의 가능한 맵 위젯을 제공하는 Flutter용 오픈 소스 패키지입니다. Flutter Map을 사용하면 개발자들은 플러터 애플리케이션에서 상호 작용하는 맵을 만들 수 있으며, 마커, 다각형, 폴리 라인, 및 타일 레이어와 같은 기능을 포함할 수 있습니다.

위에서 몇 가지 단어를 강조 했음을 보실 수 있습니다. 이것이 나의 문제가 시작된 시점이었습니다. Flutter를 배우는 것이 가장 큰 도전일 것으로 생각했었지만, 맵이 실제로 어떻게 작동하는 지를 배우는 것이 조금 더 까다로웠습니다. 그러나 그 이유를 설명하기 전에, Flutter Map을 사용하여 맵을 표시하는 것이 그 유일한 목적인 앱을 만들어 봅시다.

# 단계 1: 맵 표시하기

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

첫 번째로 구현한 화면입니다. 살짝 놀 수 있는 간단한 지도입니다. 확대 및 축소 및 이동하면, 지도가 자동으로 조정되어 적용한 확대 정도에 따라 더 많거나 더 적은 정보를 표시합니다.

지도를 표시하는 것은 다음 위젯을 구현하는 것만큼 쉽습니다:

```js
return Scaffold(
      body: Stack(
        children: [
          FlutterMap(
            options: MapOptions(
              center: LatLng(51.509364, -0.128928),
              zoom: 3.2,
            ),
            children: [
              TileLayer(
                  urlTemplate: 'https://tile.openstreetmap.org/{z}/{x}/{y}.png',
                  userAgentPackageName: 'com.example.app',
              ),
            ],
          ),
        ],
      ),
    );
```

이렇게 하면 다음과 같은 화면이 나타납니다:

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

![image](https://miro.medium.com/v2/resize:fit:1400/1*Jy9dt5a-0eCznLHubosUdA.gif)

정말 멋지죠? 제가 방금 내 첫 번째 앱을 만들었어요, 하지만 어떻게 만든 건지는 잘 모르겠어요.

그래서 한 발 물러서서 방금 복사하여 붙여넣은 코드를 분석해 봅시다. 체계적인 방법으로, Flutter Map의 문서에서 가져온 코드를 분석해 봅시다.

## 플러터의 기본 원리 이해하기 (플러터에 익숙하다면 이 부분은 건너뛰세요)

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

위젯 구성을 보면 Scaffold로 구성된 것을 볼 수 있습니다. Scaffold의 본문은 Stack이며 자식은 FlutterMap입니다.

```js
return Scaffold(
      body: Stack(
        children: [
          FlutterMap(
            [...]
          ),
        ],
      ),
    );
```

Flutter에서 모든 것은 위젯이며, 위젯은 완전한 앱을 만들기 위해 결합할 수 있는 작은 UI 조각입니다. Flutter에서 앱을 개발하는 것은 레고 세트를 조립하는 것과 비슷합니다. 조각을 하나씩 쌓아가는 것입니다.

만약 Stack 내에 여러 자식이 있다면, 실제 스택처럼 위로 쌓인 상태로 표시될 것입니다. 만약 Stack 위젯 대신에 Row나 Column을 사용했다면, 그들의 자식은 이름대로 표시될 것입니다. 단순히 위젯을 조작하여 앱에서 원하는 대로 표시되도록 조정하면 됩니다.

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

실제로, 이 간단한 애플리케이션에서는 FlutterMap 위젯 뒤에 Stack과 Scaffold 위젯이 필요하지 않았습니다. FlutterMap 위젯만 반환했다면 결과는 동일했을 것입니다.

```js
return FlutterMap(
      [...]
);
```

## FlutterMap으로 돌아가기

FlutterMap 위젯은 Flutter Map 라이브러리에서 제공됩니다. 옵션과 자식들로 구성되어 있는 것을 볼 수 있습니다.

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

옵션을 통해 위젯에게 이 맵을 런던 (51.509364, -0.128928)을 중심으로 초기화하고 줌 레벨을 3.2로 설정하고 싶다고 알려줍니다.

우리 자식들은 맵의 레이어입니다. 이 예제에서는 Open Street Maps에서 맵 이미지를 가져오는 Tile Layer 하나만 필요합니다.

## 타일 서버 URL

TileLayer를 주목하면 urlTemplate이라는 매개변수 중 하나를 볼 수 있습니다.

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
TileLayer(
    urlTemplate: 'https://tile.openstreetmap.org/{z}/{x}/{y}.png',
    userAgentPackageName: 'com.example.app',
),
```

이러한 종류의 URL을 "타일 서버 URL"이라고 합니다. 많은 맵 타일 공급 업체가 맵 타일을 매핑 소프트웨어에 전달하기 위해 사용하는 표준 형식입니다. URL에서 'z', 'x', 'y' 자리 표시자는 각각 타일의 줌 레벨, X 좌표 및 Y 좌표를 지정하는 데 사용됩니다. 맵이 표시될 때 매핑 소프트웨어는 현재 보이는 화면을 표시하는 데 필요한 각 타일에 대해 타일 서버 URL에 요청을 보냅니다.

다음은 포르투갈 전체를 표시하는 두 개의 타일입니다:

<img src="/assets/img/2024-05-17-GettingStartedwithFlutterMap_1.png" />

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

![그림](/assets/img/2024-05-17-GettingStartedwithFlutterMap_2.png)

우리의 지도는 실제로 옆에 배치된 여러 정적 이미지를 기반으로 생성됩니다. 주변을 돌아다니며 확대 및 축소할 때, 앱은 지도 화면을 메우기 위해 타일 서버 URL에서 새 이미지를 가져옵니다.

## 다른 타일 서버 URL 제공업체

타일 서버 URL에 대한 학습이 나를 다음 문제로 이끌었습니다. Open Street Maps는 확대할수록 너무 많은 정보를 표시합니다. 정치적 국경, 국가 이름, 도로, 강... 이 모든 정보는 내 번역을 알아보기 힘들게 만들 것입니다. 이들을 제거해야 했습니다. 그러나 이미지가 정적이기 때문에 OpenStreetMaps에서 제공하는 타일 서버를 사용할 수 없었습니다. 다른 것을 찾아야 했습니다.

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

구글 검색을 통해 여러 지도 제공업체를 찾을 수 있었어요. 유료도 있고 무료도 있더라구요.

저는 간단한 것을 찾았어요. 국가의 테두리가 있는 세계지도를 원했어요. 제가 원한 것에 제일 가까운 것은 "Toner"라는 이름의 Tile Server를 제공하는 Stamen Design이었어요. 그들은 여섯 가지 다른 스타일을 제공하고 있고, 그 중 하나는 라벨이 없는 배경만 있는 스타일이었어요. 제가 필요했던 것과 가깝더라구요.

![이미지](/assets/img/2024-05-17-GettingStartedwithFlutterMap_3.png)

"배경" 스타일인 것이 제게 딱 맞았어요. 그래서 Tile Server URL을 얻었어요:

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

md

```js
https://stamen-tiles.a.ssl.fastly.net/toner-background/{z}/{x}/{y}.png
```

그리고 위젯의 urlTemplate을 교체함으로써 즉시 결과를 확인할 수 있습니다:

```js
FlutterMap(
  options: MapOptions(
    center: LatLng(51.509364, -0.128928),
    zoom: 3.2,
  ),
  children: [
    TileLayer(
      urlTemplate: 'https://stamen-tiles.a.ssl.fastly.net/toner-background/{z}/{x}/{y}.png',
      userAgentPackageName: 'com.example.app',
    ),
  ],
);
```

<img src="https://miro.medium.com/v2/resize:fit:1400/1*_PqsB8kI_Por68rMI0jUzA.gif" />

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

# 단계 2: 지도에 레이블 표시하기

내 아이디어는 단어를 입력하면 해당 단어의 번역을 각 나라의 언어로 보여주는 앱을 만드는 것입니다. 이제 지도가 표시되었으므로 특정 좌표에 레이블을 표시하는 방법을 찾아야 했습니다.

## 지도 레이어

이전 단락을 다시 살펴봅시다:

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

저희 지도는 서로 겹쳐진 레이어로 구성되어 있어요. 이전 예제에서는 TileLayer 하나만 필요했지만, Flutter Map 라이브러리에는 MarkerLayer, PolygonLayer, PolylineLayer, CircleLayer, 그리고 AttributionLayer와 같은 다른 유형의 레이어가 있어요.

## PolygonLayer

Polygon 레이어는 지도 위에 다각형을 표시하는 데 사용돼요. 함께 살펴볼까요?

```js
PolygonLayer(
  polygonCulling: false,
  polygons: [
    Polygon(
      points: [
        LatLng(36.95, -9.5),
        LatLng(42.25, -9.5),
        LatLng(42.25, -6.2),
        LatLng(36.95, -6.2),
      ],
      color: Colors.blue.withOpacity(0.5),
      borderStrokeWidth: 2,
      borderColor: Colors.blue,
      isFilled: true
    ),
  ],
)
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

위의 예제는 지도상에 하나 이상의 다각형을 표시하는 데 사용되는 PolygonLayer 위젯을 생성하고 있습니다.

PolygonLayer 위젯은 다각형 객체 목록을 polygons 매개변수로 사용합니다. 이 경우 목록에 정의된 다각형이 하나뿐입니다.

다각형 클래스는 지도 상의 다각형을 정의하는 데 사용되며, 다각형의 모양, 위치 및 모양을 정의하는 데 여러 매개변수가 필요합니다.

polygonCulling 매개변수는 뷰어 영역에서 완전히 벗어난 다각형을 제거할지 여부를 지정하는 부울 값입니다. false로 설정하면 지도의 뷰어 영역에서 벗어나더라도 모든 다각형이 렌더링됩니다.

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

이 예제에서는 다각형이 points 매개변수로 정의되는데, 이는 다각형의 꼭지점을 정의하는 LatLng 객체의 목록입니다. color 매개변수는 다각형의 채우기 색상을 지정합니다. 이 경우, 우리는 포르투갈 위에 사각형을 배치하고 있습니다. 실제로 어떻게 보이는지 확인해보세요:

![Image](https://miro.medium.com/v2/resize:fit:1400/1*yqz4hBmE_hlbFu5LCjXmrQ.gif)

## PolylineLayer

```js
PolylineLayer(
  polylines: [
    Polyline(
      points: [
        LatLng(38.73, -9.14), // 리스본, 포르투갈
        LatLng(51.50, -0.12), // 런던, 영국
        LatLng(52.37, 4.90), // 암스테르담, 네덜란드
      ],
      color: Colors.blue,
      strokeWidth: 2,
    ),
  ],
)
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

위 예제는 PolylineLayer 위젯을 생성하는데 사용되며, 이 위젯은 지도 상에 하나 이상의 폴리라인을 표시하는 데 사용됩니다.

PolylineLayer 위젯은 polylines 매개변수로 Polyline 객체들의 목록을 가져옵니다. 이 경우에는 목록에 정의된 폴리라인이 하나뿐입니다.

Polyline 클래스는 지도 상의 폴리라인을 정의하는 데 사용되며, 폴리라인의 모양, 위치, 외형을 정의하는 데 여러 매개변수를 가져옵니다.

이 예제에서 Polyline은 points 매개변수로 정의되는데, 이는 폴리라인의 꼭지점을 정의하는 LatLng 객체들의 목록입니다. color 매개변수는 폴리라인의 색상을 지정합니다. 따라서 우리는 포르투갈, 잉글랜드, 네덜란드의 수도를 연결하는 선을 그리고 있습니다.

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

![CircleLayer](https://miro.medium.com/v2/resize:fit:1400/1*sA7zHaI8crg5nLnLCz5xDg.gif)

## CircleLayer

```js
CircleLayer(
  circles: [
    CircleMarker(
      point: LatLng(52.2677, 5.1689), // 't Gooi의 중심
      radius: 5000,
      useRadiusInMeter: true,
      color: Colors.red.withOpacity(0.3),
      borderColor: Colors.red.withOpacity(0.7),
      borderStrokeWidth: 2,
    )
  ],
)
```

위의 예제는 한 개 이상의 원을 지도상에 표시하는 데 사용되는 CircleLayer 위젯을 생성합니다.

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

CircleLayer 위젯은 circles 매개변수로 CircleMarker 객체의 리스트를 가져옵니다. 이 경우에는 리스트에 정의된 원이 하나뿐입니다.

CircleMaker 클래스는 맵 상에 원을 정의하는 데 사용되며, 원의 모양, 위치 및 모양을 정의하는 데 여러 매개변수를 사용합니다.

이 예제에서 CircleMarker는 point 매개변수로 정의되어 있으며, 이는 LatLng 객체로 맵 상의 마커 위치를 지정합니다. color 매개변수는 원의 색상을 지정하고, radius 매개변수는 원의 크기를 나타냅니다.

저희는 네덜란드의 't Gooi 지역 위에 반지름 5km인 원을 그리고 있습니다.

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

<img src="https://miro.medium.com/v2/resize:fit:1400/1*BQu45aeiixZQD744zeWvVw.gif" />

## MarkerLayer

마커 레이어는 가장 간단한 레이어입니다. 특정 좌표에 위젯을 표시하는 데 사용합니다. 한번 살펴보죠:

```js
MarkerLayer(
    markers: [
        Marker(
          point: LatLng(51.509364, -0.128928),
          width: 80,
          height: 80,
          builder: (context) => FlutterLogo(),
        ),
    ],
)
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

위의 예시에서는 지도에 하나 이상의 마커를 표시하는 데 사용되는 MarkerLayer 위젯을 생성합니다.

MarkerLayer 위젯은 markers 매개변수로 Marker 객체들의 목록을 받습니다. 이 경우 목록에는 하나의 마커만 정의되어 있습니다.

Marker 클래스는 지도에 표시할 마커를 정의하는 데 사용되며, 마커의 위치, 크기 및 모양을 정의하는 데 여러 매개변수를 사용합니다.

이 예시에서는 Marker가 point 매개변수로 정의되어 있으며, 이는 LatLng 객체로서 지도상의 마커 위치를 지정합니다. 너비와 높이 매개변수는 마커의 크기를 픽셀로 지정하고, builder 매개변수는 마커의 외관을 정의하기 위해 Widget을 반환하는 함수를 사용합니다.

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

이 예시에서 빌더 함수는 FlutterLogo 위젯을 사용하여 포인트 매개변수로 지정된 위치, 즉 런던에 Flutter 로고를 표시합니다.

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*Yt7KTM6Pf91zvsyzRiGBgQ.gif)

# 지도에 단어 배치하기

이전에 말한대로, 제 목표는 지도에 단어를 배치하는 것이며, MarkerLayer가 그에 대한 완벽한 해결책입니다.

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

마커를 만들기 전에 텍스트 위젯을 만들어 보겠습니다. 먼저 스타일을 설정해 봅시다:

```js
TextStyle getDefaultTextStyle() {
  return const TextStyle(
    fontSize: 12,
    backgroundColor: Colors.black,
    color: Colors.white,
  );
}
```

이 메소드는 글꼴 크기, 배경 색상 및 글꼴 색상을 정의하는 TextStyle 객체를 반환합니다. 다음과 같은 방식으로 사용될 것입니다:

```js
Container buildTextWidget(String word) {
  return Container(
      alignment: Alignment.center,
      child: Text(
          word,
          textAlign: TextAlign.center,
          style: getDefaultTextStyle()
      )
  );
}
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

텍스트 위젯은 단어를 텍스트로받아 Container 위젯 내에 배치됩니다.

```js
Marker buildMarker(LatLng coordinates, String word) {
  return Marker(
      point: coordinates,
      width: 100,
      height: 12,
      builder: (context) => buildTextWidget(word)
  );
}
```

buildTextWidget 함수는 buildMarker 함수 내에서 호출되어 MarkerLayer 위젯의 하위인 Markers를 생성합니다. 이들은 배치될 좌표와 표시할 단어로 생성됩니다.

```js
MarkerLayer(
  markers: [
    buildMarker(LatLng(39.3999, -8.2245), "Amor"), // Portugal
    buildMarker(LatLng(55.3781, -3.4360), "Love"), // England
    buildMarker(LatLng(46.2276, 2.2137), "Aimer"), // France
    buildMarker(LatLng(52.1326, 5.2913), "Liefde"), // Netherlands
    buildMarker(LatLng(51.1657, 10.4515), "Liebe"), // Germany
  ],
)
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

그리고 마지막으로:

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*RmUkKINlQ1WSTGEalOVkcQ.gif)

# 결론

이야기에서 우리는 지도가 렌더링되는 기본 원리와 Tile 서버에 관련된 몇 가지 가능성을 배웠습니다.

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

또한, 우리는 지도가 어떻게 레이어로 나누어지는지 학습하고, 이를 활용하여 특정 좌표에 마커, 폴리곤, 폴리라인 및 원을 배치하는 방법도 배웠어요.

마무리로, 우리는 실제 예제를 통해 지도 위의 특정 국가에 텍스트 레이블을 표시하는 방법을 배웠어요.

# 다음은 무엇인가요?

저는 플러터와의 여정이 막 시작된 것 뿐이에요. 이것은 제가 만들고 있는 첫 번째 앱의 표면에 불과해요.

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

다음 이야기에서는 사용자가 단어를 입력하고 해당 단어를 여러 언어로 번역하여 지도에 표시하는 방법을 소개할 거에요.

또한, 지도를 더 고급스럽게 다루는 방법도 알려드릴 거예요. 예를 들어 확대 및 축소할 때 레이블 크기를 늘리거나 줄이는 방법, 또한 지도를 제어하기 위해 제스처 대신 버튼을 사용하는 지도 컨트롤러를 구현하는 방법에 대해서 보여드릴 거에요.

계속 주목해 주세요!

# 최종 앱

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

테이블 태그를 Markdown 형식으로 변경해보세요.

| Header 1 | Header 2 |
| -------- | -------- |
| Cell 1   | Cell 2   |

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

글을 쓰는 데에는 시간과 노력이 들어갑니다. 저는 글쓰기를 사랑하고 지식을 나누는 것을 좋아하지만 물론 청구서도 내야 합니다. 제 작품을 좋아하신다면 Buy Me a Coffee를 통해 후원해 주시기를 부탁드립니다: [https://www.buymeacoffee.com/RaphaelDeLio](https://www.buymeacoffee.com/RaphaelDeLio)

또는 비트코인을 보내주셔도 됩니다: 1HjG7pmghg3Z8RATH4aiUWr156BGafJ6Zw

# 소셜 미디어에서 나를 팔로우해요

저와 함께 플러터의 세계로 더 깊게 파고들어보세요! 주요 소셜 플랫폼에서 제 여정을 팔로우하여 독점 콘텐츠, 팁, 그리고 토론에 참여해보세요.

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

Twitter | LinkedIn | YouTube | Instagram
