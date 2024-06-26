---
title: "저렴한 날씨 관측소와 기존 장비로 개인 DevOps 스타일 날씨 대시보드 만드는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-HowIturnedacheapweatherstationandawholelotofthingsIalreadyownintoapersonalDevOps-styleweatherdashboard_0.png"
date: 2024-06-23 00:39
ogImage:
  url: /assets/img/2024-06-23-HowIturnedacheapweatherstationandawholelotofthingsIalreadyownintoapersonalDevOps-styleweatherdashboard_0.png
tag: Tech
originalTitle: "How I turned a cheap weather station (and a whole lot of things I already own) into a personal DevOps-style weather dashboard"
link: "https://medium.com/@jaxzin/how-i-turned-a-cheap-weather-station-into-a-personal-devops-dashboard-5c8820790fd5"
---

# 최종 결과

시각적으로 매력적인 부분부터 시작해 보겠습니다. 아마 여러분을 여기로 인도한 것이 이것일 것입니다. 여기 제 대시보드의 모습입니다...

# 레이아웃

내 홈랩에는 이를 가능하게 하기 위해 많은 부품들이 움직이고 있습니다. 여러분의 버전은 더 간단할 수 있습니다. 제 경우에는 이렇게 작동했습니다. 후속 섹션에서 각 구성 요소를 자세히 살펴보겠지만, 세부 사항에 들어가기 전에 10,000피트 상에서 시작하는 게 낫다고 생각했습니다.

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

<img src="/assets/img/2024-06-23-HowIturnedacheapweatherstationandawholelotofthingsIalreadyownintoapersonalDevOps-styleweatherdashboard_0.png" />

# 하드웨어

여기에는 내 홈 네트워킹 장비를 제외한 내가 사용 중인 하드웨어가 있습니다. 이 프로젝트를 위해 명시적으로 구입한 것은 날씨 센서뿐이며, 그 외의 모든 것은 이미 가지고 있었습니다.

- 날씨 센서 — WS2032 날씨 관측소
  $40~85 미국 달러, 사이트에 따라 다름. 저는 rtl_433 프로젝트를 발견했고 이 장치가 지원되고 있다는 이유로 이를 선택했습니다.
- 라디오 수신기 — RTL-SDR USB 라디오 수신기
  $30 미국 달러, 아마존에서 구입. 날씨 관측소에서 433MHz 라디오 신호를 수신하기 위해 사용됩니다. 이전 프로젝트에서 이미 가지고 있던 것입니다.
- Home Assistant를 실행할 장비 — Home Assistant Blue
  더 이상 판매되지 않지만 Home Assistant Yellow나 라즈베리 파이를 고려해보세요.
- Grafana와 InfluxDB를 실행할 장비 — Synology NAS DSM 918+
  더 최신 모델이 있으며, 도커 컨테이너를 Home Assistant와 같은 하드웨어에서 실행할 수도 있습니다. NAS에서 이 두 가지를 실행했는데 NAS에 더 많은 저장 공간이 있었기 때문입니다.

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

# 날씨 관측소 설정하기

![이미지](https://miro.medium.com/v2/resize:fit:1280/1*BzJX0ldpzm-RtlqcEJO9KQ.gif)

이 단계는 가장 쉽게 진행된 것일지도 모릅니다. WS2032의 조립은 두 개의 AA 건전지와 몇 개의 나사로 꽤 직관적이었습니다. 가격 대비, 홀 효과 센서로 보이는 것들이 사용되어 남풍과 해풍 구성 요소의 위치와 회전 속도를 감지하는 데 사용됩니다. 온도 및 습도 센서는 건전지를 보관하는 중앙 코어에 내장되어 있으며, 이 조립은 직사광선이 직접 비치지 않도록 차단되어 있으며 층층이 쌓인 린 디자인의 열로 냉각됩니다. 그들은 중앙 코어가 물에 젖지 않으면서 여전히 주변의 레이드를 얻을 수 있게하는 층층이 쌓인 디자인으로 보호합니다. 이 설정의 가장 어려운 부분은 정중앙 방향이 위에 나열되어 있는 바람 피리와 일치시키는 것이었습니다. 그러나 장치에서 오는 읽기를 본 후, 정밀도가 45º로 제한되어 있기 때문에 나침반과의 정렬은 매우 용서되는 것 같습니다. WS2032에는 실내용 LCD 표시 장치가 함께 제공되며, 이 장치를 통해 센서가 작동하고 대략적으로 정확한 데이터를 제공하는지 확인할 수 있었습니다.

# 홈 어시스턴트 설정하기

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

HA(Home Assistant)의 전체 설정 튜토리얼은 안내하지 않겠어요. 이미 시작하는 데 도움이 되는 많은 좋은 자료들이 있거든요. 이 섹션에서 다룰 내용은 이미 구동 중인 Home Assistant 인스턴스에 필요한 변경 사항들이에요.

## 하지만, RTL-SDR에 대한 간단한 소개부터

날씨 관측소가 설치되어 데이터를 전송하기 시작했으니, 해당 데이터를 수신할 무언가를 설정할 차례입니다. rtl_433은 여러 가지 센서로부터의 데이터를 파싱하기 위해 만들어진 오픈 소스 프로젝트로, 가장 흔한 주파수인 433 MHz를 통해 데이터를 송수신하는 RF(라디오 주파수) 신호들을 해석합니다. 기타 주파수도 지원합니다.

RF를 사용하는 장치들은 Bluetooth, WiFi, ZigBee, Z-Wave, 또는 Thread 대신 사용되며, 종종 보다 저렴하거나 오래되어 있으며 초기에는 최신 스마트 홈에 통합되도록 설계된 것은 아닐 수 있습니다. 가스 또는 수도계, 누수 탐지기, 심지어 자동차의 타이어 압력 센서와 같은 것들이 rtl_433을 사용하여 감지 및 파싱될 수 있습니다. rtl_433는 주로 여러 가지 USB 라디오 수신기인 소프트웨어 정의 라디오(SDR)를 사용하도록 작성되었습니다.

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

디지털 TV의 발명으로 하드웨어를 디코딩하는 저렴한 칩셋이 등장했습니다. 그 중 하나인 RealTek의 RTL2832U (일반적으로 RTL로 줄여짐)은 여러 무선 주파수를 서비스할 수 있는 해킹이 가능했고, 디지털 무전기 해커들은 컴퓨터로 경찰 및 공항 대역과 같은 것들을 수신할 수 있었고 그렇게 RTL-SDR 운동이 시작되었습니다.

그런 이야기가 우리를 rtl_433로 이끕니다. 이 프로젝트는 RF 신호를 수신하고 그 데이터를 구문 분석하는 것을 목표로 하며, 그중 WS2032 날씨 관측소를 포함합니다.

## rtl_433 설정 방법

만약 이미 Home Assistant가 실행 중이라면 - 특히 Home Assistant OS 배포가 가능케 하는 환경에서는 "애드온"이라 불리는 Docker 컨테이너의 다른 이름 이 커뮤니티에서는 - RF 장치를 Home Assistant로 쉽게 가져오는 방법은 세 가지 추가 기능을 실행하는 것입니다.

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

- Mosquitto - 가장 간단한 설정은 인기있는 MQTT 브로커인 이 공식 애드온을 사용하는 것입니다. 이 애드온을 사용하면 다른 두 애드온이 이미 이를 사용하도록 구성되어 있습니다. 그렇지 않으면 기존의 MQTT 브로커를 가리키도록 두 다른 애드온을 사용자 정의해야 합니다. MQTT 설명은 이 문서의 범위를 벗어납니다만, TL;DR은 이것이 사물 인터넷(IoT) 장치들에서 널리 사용되는 메시징 플랫폼이라는 것입니다.
- rtl_433 - 이 애드온은 RTL-SDR 장치와 USB를 통해 연결하고 RF 데이터를 감지하고 파싱하여 MQTT 메시지로 변환한 후 해당 메시지를 브로커의 특정 주제로 보내는 역할을 합니다. 이는 표준적인 애드온이 아니기 때문에 제 인스턴스에이 애드온을 추가하기 위해서는 먼저 GitHub 사용자 pbkhrv의 rtl_433 애드온 저장소를 추가해야 했습니다. 해당 저장소의 README에는 설치 방법이 포함되어 있습니다.
- rtl_433 Home Assistant MQTT Auto Discovery - 기본적으로 Home Assistant에서 MQTT 통합을 켰더라도 모든 MQTT 메시지를 자동으로 읽고 스마트 홈 장치로 전환하지는 않습니다. 하지만 Home Assistant는 스마트 장치에 의해 Home Assistant 전용으로 사용되는 특별한 MQTT 메시지를 지원하여 이를 자동으로 Home Assistant에 추가하여 제어할 수 있습니다. 이 애드온은 rtl_433 애드온에서 오는 MQTT 메시지를 감지하고 MQTT를 통해 Home Assistant에 자동 발견 메시지를 보내는 역할을 합니다. 이 애드온은 앞서 구성한 전체 rtl_433 애드온 저장소의 일부로 제공됩니다. 마지막 단계는 이 저장소를 사용하여 애드온을 설치하는 것뿐입니다. 헷갈리셨나요? HA에 애드온 저장소를 추가하는 것은 설치할 수 있는 새로운 애드온 인덱스를 추가하는 것과 같습니다. Home Assistant에서 사용 가능한 애드온으로 이제 설치해야 합니다.

여기서 마지막 단계는 이미 하지 않았다면 Home Assistant와 MQTT 통합을 활성화하고 Mosquitto 애드온을 가리키도록 하는 것입니다. Mosquitto 애드온의 README에서 이 설정에 대해 다루고 있지만, 명시적으로 언급하고자 했습니다.

이 시점에서 Home Assistant로 데이터가 흘러들어가고 있어야 합니다. 개발자 도구 패널에 들어가서 States 탭에서 ws2032를 검색해보세요. 다음과 같은 내용을 보게 될 것입니다(단, 이름은 자세하고 "\_mph" 엔티티는 제가 직접 사용자 정의했습니다).

<img src="/assets/img/2024-06-23-HowIturnedacheapweatherstationandawholelotofthingsIalreadyownintoapersonalDevOps-styleweatherdashboard_1.png" />

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

## 엔티티 사용자 정의

위 예시에서 보듯이, 배터리 읽기가 아마도 rtl_433 컨테이너에 의해 자동으로 발견되는 것 중 유사한 것 이외에는 여러분이 볼 수 있는 것과 가장 근접할 것입니다. HA 대시보드에 엔티티를 추가할 때 예쁘게 꾸미기 위해 각 엔티티로 이동하고 친숙한 이름 및 아이콘을 사용자 정의했습니다. 이렇게 하면 번거롭지 않게 카드에 엔티티를 빠르게 추가할 수 있습니다.

다음은 이러한 사용자 정의가 적용된 내 HA 대시보드의 전형적인 카드입니다.

<img src="/assets/img/2024-06-23-HowIturnedacheapweatherstationandawholelotofthingsIalreadyownintoapersonalDevOps-styleweatherdashboard_2.png" />

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

WS2032로부터 나온 풍속과 돌풍 속도 측정값은 km/h로 되어 있는데, 저는 미국인이라 mph로 변환하여 표시하고 싶었습니다. 따라서 그들의 값을 영미 단위로 변환하는 두 개의 템플릿 엔티티를 만들었습니다. 또한 풍향 측정값을 도에서 기본 나침반 방향으로 변환하는 세 번째 엔티티도 만들었습니다.

## 커스텀 HA 대시보드 및 카드

InfluxDB와 Grafana에 착수하기 전에, 이제 수집된 데이터를 사용할 수 있도록 Home Assistant를 설정했습니다. 몇 가지 예시를 보여드리겠습니다:

이젠 제가 흥미를 느끼기 시작했습니다. 더 알고 싶어졌죠...

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

- 홈 어시스턴트의 러블레이스 프론트엔드가 처리할 수 있는 방법 이상으로 데이터를 슬라이스합니다.
- 장기 트렌드를 분석하기 위해 데이터를 영구히 보관합니다.

그래서 나는 가능한 한 오랫동안 데이터를 저장할 수 있도록 이 모든 데이터를 기록하기 위해 InfluxDB를 설정하는 시간이 된 것을 깨닫게 되었고, Grafana를 통해 모두 시각화하기로 했습니다.

# InfluxDB 및 Grafana 설정

가장 빠르게 시작하려면 InfluxDB를 홈 어시스턴트 애드온으로 설치하고 Grafana를 홈 어시스턴트 애드온으로 설치할 수 있습니다. 나는 Synology NAS에서 두 가지를 모두 실행하고 있고, 이 글을 쓰는 시점에서 InfluxDB 2.5.1 및 Grafana 9.2.6을 Docker Hub에서 제공하는 공식 Docker 이미지를 사용하여 도커 컨테이너로 실행하고 있습니다.

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

InfluxDB를 실행한 후에 집을 나타내는 조직과 Home Assistant의 데이터를 보관할 버킷을 만들었어요. 또한 Home Assistant가 사용할 API 토큰 하나와 Grafana가 사용할 다른 API 토큰 두 개를 생성했어요.

## Home Assistant를 InfluxDB에 연결하기

Home Assistant에는 모든 이벤트를 InfluxDB로 보내는 기본 지원이 있어요. 그래서 날씨 대시보드를 만들 뿐만 아니라 Grafana에서 액세스할 수 있는 많은 데이터도 있을 거예요. 이 이벤트 흐름을 HA에서 InfluxDB로 활성화하려면 Home Assistant의 configuration.yaml 파일에 조금의 YAML을 추가하세요.

```js
influxdb:
  api_version: 2
  ssl: false
  host: nas.iot.jaxzin.com
  port: 8086
  token: !secret influxdb_token
  organization: 8d2a3be3fdc94fa2
  bucket: fallen-leaf
  tags:
    source: HA
  tags_attributes:
    - friendly_name
  default_measurement: units
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

## InfluxDB를 Grafana에 데이터 소스로 추가하기

저는 Flux 쿼리 언어를 사용하고 싶었는데, 초기 조사 결과로 알 수 있었듯이 강력한 함수형 언어입니다. 다음 섹션에서 Flux에 대해 자세히 살펴보겠습니다.

## Wind Rose 플러그인 설치

저는 기본 Grafana 설치에 포함되지 않았지만 꼭 필요했던 시각화 유형인 바람 장미를 사용하고 싶었습니다. Grafana에는 몇 가지 바람 장미 플러그인이 있지만, 저는 spectraphilic의 windrose 플러그인을 사용하기로 했습니다. 설치 지침은 README에 있으며, Grafana Docker 지침에는 사용자 정의 플러그인을 추가하는 방법이 설명되어 있습니다.

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

![Dashboard Image](/assets/img/2024-06-23-HowIturnedacheapweatherstationandawholelotofthingsIalreadyownintoapersonalDevOps-styleweatherdashboard_3.png)

# 대시보드 만들기

이제 재미있고 약간 압도되는 부분, 커스텀 Grafana 대시보드를 만드는 것인데, 이는 Flux를 배우고 원하는 모습을 어떻게 구현할지 찾기 위해 많이 구글링하는 과정이 필요합니다.

## 바람의 장미

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

행복한 소식입니다! spectraphilic이 Grafana 플러그인의 README에 Flux 예제를 포함해 주었어요. 그래서 바람 장미를 위한 Flux 쿼리 작성에 빠르게 시작할 수 있었고 효과적으로 진행했어요. 이 예제에서는 WS2032에서 풍향...\_wd 및 풍속...\_ws 데이터를 결합하는 방법을 보여줍니다.

위 코드는 "fallen-leaf" 버킷에서 "entity_id"가 "ws2032_28817_wd"이거나 "ws2032_28817_ws"인 필드를 가진 데이터 중 "value" 필드를 가진 데이터를 선택합니다. 선택한 데이터를 ${period} 주기로 평균을 계산하고 결과 값을 동일한 타임스탬프를 기준으로 row에 배치합니다. 그리고 두 값이 모두 있는 행만 남기고, "ws2032_28817_wd" 컬럼을 "direction", "ws2032_28817_ws" 컬럼을 "speedKmps"로 이름을 변경합니다. 속도를 km/s로 변환하고, 필요한 열만 남기며, 각 창에 대해 첫 번째 행을 샘플링합니다.

결과는 시간, 방향 및 속도 세 가지 열로 구성된 데이터 테이블입니다. 바람 장미 시각화는 이 데이터를 사용하여 속도를 방향별로 묶어낸 후, WS2032의 해상도가 8개 방향이므로 장미를 8개의 "케이크 조각"만 사용하도록 구성했습니다.

이미지는 개인용 DevOps 스타일 날씨 대시보드로 저렴한 기상 관측소와 이미 있는 물품을 활용한 경험을 보여줍니다.

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

## 통계 기간을 위한 대시보드 변수 추가

다른 시각화로 넘어가기 전에, 데이터의 다른 범위를 선택하는 것보다 더 많은 유연성을 원했어요. 바람 데이터는 정말 소음이 많기 때문에 선택된 시간 범위 내에서 샘플링 기간을 쉽게 변경할 수도 있었으면 좋겠어요. 이 샘플링 기간은 평균, 최대, 최소 또는 백분위 등의 통계를 계산할 때 사용될 거예요. 그래서 바람 속도나 방향을 시간별 또는 일별로 나누어 볼 수도 있겠죠. 이를 수행하기 위해 Window Period를 위한 사용자 정의 변수를 대시보드에 추가해야 했어요. 나중의 Flux 쿼리에서 $'period'로 참조되는 것을 보게 될 거예요.

## 바람 속도를 열지도로 표현

한동안 바람 속도 기록의 다양한 시각화를 고민했어요. 한 차트에 돌풍과 바람 속도를 보여주고 싶었고, 속도의 최소, 평균 및 최대 라인을 사용하여 변동 속도를 보다 잘 보여주기 위해 이 시각화 결과물에 결정했어요.

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

표 태그를 Markdown 형식으로 변경하십시오.

<img src="/assets/img/2024-06-23-HowIturnedacheapweatherstationandawholelotofthingsIalreadyownintoapersonalDevOps-styleweatherdashboard_5.png" />

이 효과를 얻으려면 기본적으로 5 세트의 데이터가 필요합니다. 그래프아나와 Flux 쿼리를 사용하면 5개의 쿼리를 실행하는 것만큼 간단하지만(효율적이지는 않을 수 있습니다).

```js
// 풍속: 최대, 평균, 최소
from(bucket: "fallen-leaf")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r["entity_id"] == "ws2032_28817_ws_mph")
  |> filter(fn: (r) => r["_measurement"] == "mph")
  |> filter(fn: (r) => r["_field"] == "value")
  |> keep(columns: ["_time","_value", "value", "_field"])
  |> set(key:"_field", value:"max")
  |> aggregateWindow(every: ${period}, fn: max, createEmpty: true)
  |> yield(name: "max")

from(bucket: "fallen-leaf")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r["entity_id"] == "ws2032_28817_ws_mph")
  |> filter(fn: (r) => r["_measurement"] == "mph")
  |> filter(fn: (r) => r["_field"] == "value")
  |> keep(columns: ["_time","_value", "value", "_field"])
  |> set(key:"_field", value:"mean")
  |> aggregateWindow(every: ${period}, fn: mean, createEmpty: true)
  |> yield(name: "mean")

from(bucket: "fallen-leaf")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r["entity_id"] == "ws2032_28817_ws_mph")
  |> filter(fn: (r) => r["_measurement"] == "mph")
  |> filter(fn: (r) => r["_field"] == "value")
  |> keep(columns: ["_time","_value", "value", "_field"])
  |> set(key:"_field", value:"min")
  |> aggregateWindow(every: ${period}, fn: min, createEmpty: true)
  |> yield(name: "min")

// 돌풍 속도: 최대 및 평균
from(bucket: "fallen-leaf")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r["entity_id"] == "ws2032_28817_gs_mph")
  |> filter(fn: (r) => r["_measurement"] == "mph")
  |> filter(fn: (r) => r["_field"] == "value")
  |> keep(columns: ["_time","_value", "value", "_field"])
  |> set(key:"_field", value:"max (gust)")
  |> aggregateWindow(every: ${period}, fn: max, createEmpty: true)
  |> yield(name: "max_g")

from(bucket: "fallen-leaf")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r["entity_id"] == "ws2032_28817_gs_mph")
  |> filter(fn: (r) => r["_measurement"] == "mph")
  |> filter(fn: (r) => r["_field"] == "value")
  |> keep(columns: ["_time","_value", "value", "_field"])
  |> set(key:"_field", value:"mean (gust)")
  |> aggregateWindow(every: ${period}, fn: mean, createEmpty: true)
  |> yield(name: "mean_g")
```

그러나 여전히 마음에 들지 않았습니다. 노이즈가 있는 데이터를 시각화하는 것 같지 않았습니다. 따라서 히트맵 시각화를 시도해보았고, 풍속의 분산을 보여주면서도 풍속이 대부분 어디에 집중되어 있는지 쉽게 파악할 수 있는 것으로 생각됩니다.

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

![이미지](/assets/img/2024-06-23-HowIturnedacheapweatherstationandawholelotofthingsIalreadyownintoapersonalDevOps-styleweatherdashboard_6.png)

```js
from(bucket: "fallen-leaf")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r["entity_id"] == "ws2032_28817_ws")
  |> filter(fn: (r) => r["_measurement"] == "km/h")
  |> filter(fn: (r) => r["_field"] == "value")
  |> map(fn: (r) => ({r with _value: r._value * 0.618}))
  |> keep(columns: ["_time","_value", "value", "_field"])
```

차트 하나에 바람과 돌풍 속도를 함께 표시할 수 없어서 두 개를 함께 유지했어요. 더 나은 해결책을 찾을 때까지는 이렇게 유지할 거예요.

## 퍼센타일을 사용한 윈드 방향의 의사 토폴로지 차트

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

바람 방향 데이터가 정말 소란스럽기 때문에 통계의 힘을 활용하여 소음 속에서 신호를 찾을 수 있는 시각화를 만들어 보고 싶었습니다. 그래서 각 백분위를 각각의 선으로 그려볼까? 데이터의 방향을 보여주면서 분산을 더 잘 파악할 수 있을까? (참고로, 이것은 위의 히트맵을 활용하기 전에 생각한 것으로, 이 곳에도 좋은 해결책일 수 있습니다).

하지만 각 백분위에 대해 21가지 다른 쿼리를 입력하고 싶지 않았습니다. 각 백분위를 계산하는 동적 Flux 쿼리를 만들 수 있을까요? 이 쿼리를 위해 도움을 요청하기 위해 Flux 포럼을 찾아다니던 중이었고, 최종 쿼리는 다음과 같습니다.

```js
import "experimental/array"

// 주어진 범위 내 모든 풍향 데이터 가져오기
// 이는 data라는 함수를 정의하는 것으로, 나중에 여러 번 호출할 것입니다.
data = () => from(bucket: "fallen-leaf")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r["entity_id"] == "ws2032_28817_wd")
  |> filter(fn: (r) => r["_field"] == "value")
  |> filter(fn: (r) => r["_measurement"] == "°")
  |> map(fn: (r) => ({r with _value: r._value * 400.0/360.0})) // 360을 400으로 매핑하여 Grafana에서 기본 방향이 y축 레이블로 표시될 수 있도록 함
  |> aggregateWindow( // 8방위 방향은 그다지 세분화되지 않습니다. 데이터를 세분화하고 평균을 구합시다.
    every: duration(v: int(v: ${period}) / 12),
    fn: mean
  )

// 계산하려는 백분위
p = [1,5,10,15,20,25,30,35,40,45,50,55,60,65,70,75,80,85,90,95,99]

// 함수 'q' 정의: 'data'를 호출하고 한 백분위 'v'를 계산함
q = (v) => {
  d = data ()
    |> aggregateWindow(
        column: "_value",
        every: ${period},
        fn: (column, tables=<-) => tables |> quantile(q: float(v: v)/100.0, column: column),
    )
    |> set(key: "_field", value: "p${if v < 10 then "0" else ""}${v}")
  return d
}

// 원하는 각 백분위에 대해 'q'를 호출하고 결과를 연결함
union(tables:  p
  |> array.map(fn: (x) => (  q(v: x))))
  |> keep(columns: ["_time", "_field", "_value"])
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

와우, 함수형 프로그래밍 언어는 정말 강력할 수 있죠! 이 구문을 좋아하고 몇 줄의 코드로 21개의 데이터 세트를 가져올 수 있었던 것이 정말 좋았어요. influxData의 Jay Clifford에게 이 쿼리를 작성하는 데 도움을 준 것에 대해 큰 감사를 전해요.

## 색상 및 스타일 사용자 정의

이제 데이터를 가졌지만 기본적으로 Grafana Time Series 시각화는 무지개 색상으로 된 선으로 표시돼요. 중앙값(즉, p50)을 더 밝은 실선으로 강조하고 다른 백분위수는 대시 선으로 표시하고 싶었어요. 또한 중앙값을 기준으로 위와 아래를 강조하기 위해 일부 배경을 채우고 싶었어요.

이러한 사용자 정의의 해답은 Grafana 오버라이드입니다. 두 개의 선 사이를 채우려면, 데이터 시리즈의 더 높은 부분에서 "아래로 채우기"라는 새 속성 오버라이드를 선택하고 낮은 데이터 시리즈를 선택하세요. 이제 시각화에는 두 시리즈 사이에 동적 배경 채우기가 포함될 거에요.

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

저는 table 태그를 Markdown 형식으로 변경했고, 이번에는 그 위에 정규식 오버라이드를 사용하여 필드 이름이 p[0-4]\d(즉, p01-p49)인 줄은 보라색으로, p[5-9]\d|p5[1-9] (즉, p51-p99)인 줄은 초록색으로 칠하도록 설정했습니다.

중앙값을 강조하기 위해 줄을 기본적으로 점선으로 설정하고, p50 줄에 대한 오버라이드를 추가하여 선 스타일, 색상 및 두께를 사용자 정의했어요.

## 각도 대신 나침반 방향으로 생각하기

여기서 작업이 거의 끝났지만, 측정값 및 y-축이 여전히 각도로 되어 있었습니다. 그러나 바람 방향을 고려할 때 나는 기본 (N, E, S, W) 및 중간 (NE, SE, SW, NW) 방향으로 생각하고 있었기 때문에 시각화가 그것을 반영하도록 변경하고 싶었어요.

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

## 값 매핑

시계열 시각화는 숫자 값을 다른 값으로 매핑할 수 있는 기능이 있습니다. 이는 기본적으로 기본 및 서수 방향과 같은 간단한 텍스트를 포함합니다. 특정 값을 매핑하거나 범위 또는 정규식을 사용한 매핑의 더 복잡한 정의를 사용할 수 있습니다. 이 매핑은 데이터 포인트 위에 마우스를 올렸을 때 툴팁에 표시되는 값을 영향을 미칩니다. 또한 y-축에 표시되는 값에도 영향을 줍니다. 그래서 나는 각도 범위를 나침반 방향으로 매핑했습니다.

## 임계값

다른 것 중에서 내가 주목한 것은 x/y 그래프에서 카디널 방향과 같은 방향과 가까운 값이 어디에 있는지 시각적으로 판단하기 어려웠다는 점이었습니다. 그래서 나는 이 네 가지 방향의 각각에 수평 점선 형태의 지시자를 그래프에 추가하기로 했습니다. 이러한 유형의 수평 지시자 추가는 임계값이라고 하며, 그냥 수평선을 추가하는 것보다 강력합니다. 임계값은 값에 따라 선의 색상을 지정하거나 배경을 채울 수 있습니다. 따라서 나는 카디널 방향에 대한 임계값을 추가하여 선과 배경을 표시합니다.

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

## 데이터 변환을 통해 y-축 레이블 수정하기

(Grafana의 기대에 맞추기 위해)

모든 것이 잘 보였지만, y-축에 360º의 최대값을 추가했을 때 N, E, S, W의 임계값이 명확하게 표시되지 않았어요. 여러 포럼을 찾아보다가, 90º, 180º, 270º 및 360º과 같은 이 비슷하지만 끝나지 않는 숫자들을 y-축에 레이블링하는 것이 어려울 것이라고 점점 희망을 잃고 있었죠. 100º, 200º, 300º 등의 레이블링을 하려는 것 같아요. 그래서 영감이 스쳤어요. 이미 표시된 값을 매핑하고 있었는데, 원래의 각도 값을 Grafana의 예상에 맞게 조정하고 빠른 조치로 문제를 해결할 수 있을까요? 그래서 이 코드 라인이 존재하는 이유입니다.

```js
...
|> map(fn: (r) => ({r with _value: r._value * 400.0/360.0}))
...
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

N, E, S, W를 0, 100, 200, 300 및 400(N)으로 다시 매핑하면 y-축이 기본적으로 언급하는 기준치를 나타낼 수 있도록 설정할 수 있었어요. 데이터가 이제 더이상 0º에서 360º까지가 아니어도 스크린 표시에는 큰 영향이 없어요. 하지만 생 데이터를 살펴보면 360º 이상의 값이 보이면 조금 이상할 수도 있어요.

# 다음 단계는?

- 바람 데이터와 그릴 온도 상관 시키기
  그래서 날씨 관측소를 산 이유가 있어요! 추수감사절 때 그릴에 칠한 칠면조를 연기로 익혔는데 바람이 그릴 온도에 정말 영향을 미친다는 것을 알았어요 (내 MEATER+로 Home Assistant를 통해 추적했어요). 그래서 호기심이 생겨 날씨 관측소를 샀어요. 역사적인 그릴 온도와 바람 속도를 상호 참조하는 대시보드를 만들고 싶어요.
- 강우 센서 추가 및 습도 센서 수리
  이제 더 많은 데이터를 원해요! 그리고 습도 센서가 때때로 오작동하는 것 같아서 더 신뢰할 수 있게 할 수 있는지 조사해야 해요.
- 광량 센서 추가
  얼마 전부터 실외 밝기를 사용해서 실내 조명을 조절하고 싶었어요. 온도 시각화에 태양 고도를 추가한 것처럼, 대쉬보드에서 광량도 흥미로운 데이터 포인트일 수 있다고 상상할 수 있어요. 내 집의 조명 개수와 야외 광량 사이에 상관 관계가 있을까요? 아마도 있지만 증거를 보고 싶어요.
- 계속 조정하기!
  끊임없는 실험의 공간이 될 것 같아요.
- 이 프로젝트에서 배운 것을 내 직장에 적용하기
  나는 DevOps 팀의 엔지니어 관리자로 일하는 날 개인 자동화 프로젝트들을 많이 활용해서 무언가 새로운 것을 배우는 테스트베드로 사용해요. Grafana의 강력함과 InfluxDB와 같은 데이터 소스를 추상화하지 않고 각각의 쿼리 언어를 활용하는 점이 정말 강력하다는 것을 알게 되었어요.
