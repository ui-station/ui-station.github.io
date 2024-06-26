---
title: "자신만의 스파이 ADS-B 수신기 없이 실시간 항공기 모니터링하기"
description: ""
coverImage: "/assets/img/2024-05-20-DIYforaSpyReal-timeAircraftMonitoringwithoutADS-Breceiver_0.png"
date: 2024-05-20 19:12
ogImage:
  url: /assets/img/2024-05-20-DIYforaSpyReal-timeAircraftMonitoringwithoutADS-Breceiver_0.png
tag: Tech
originalTitle: "DIY for a Spy: Real-time Aircraft Monitoring without ADS-B receiver"
link: "https://medium.com/illumination/diy-for-a-spy-real-time-aircraft-monitoring-without-ads-b-receiver-a6b7fa96253d"
---

## 항공 전자정보수집 기술

![이미지](/assets/img/2024-05-20-DIYforaSpyReal-timeAircraftMonitoringwithoutADS-Breceiver_0.png)

이전에 작성한 'No Such Agency' 및 'The Machine' 기사 중 하나에서 설명한 대로, 'The Machine'이라는 세계 감시 복합체에는 정부가 전 세계 항공기 움직임과 같은 다양한 전자 활동을 모니터링할 수 있는 기능이 포함되어 있습니다. 이를 ELINT(Electronic Intelligence)라고 합니다. 오늘은 저희가 Frankfurt Airport(FRA)에서 배우자를 특정 시간에 만나기 위해 우리의 사적 임무 중 하나에 대해 논의할 것입니다.

본 기사는 특별한 허가없이도 이 데이터를 활용할 수 있다는 것을 보여주고자 합니다. 오늘날의 디지털 시대에는 방대한 양의 정보가 누구라도 언제 어디서든 완전히 접근 가능하며 사용할 수 있습니다. 특수 지식이나 접근 권한은 종종 방대한 활동을 모니터링하는 데 필요하지 않습니다. 이는 우리가 좋아하는 'The Machine'이 대량의 데이터를 쉽게 수집하고 분석할 수 있는 기회를 제공합니다.

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

오늘, 여러분의 목표를 달성하기 위해 이러한 접근법 중 하나가 사용될 것입니다 — 특정 시간에 공항에서 여자친구를 만나는 것.

## 미션

우리의 미션 효율성을 높이기 위해 몇 가지 주요 인물들이 참여할 것입니다: 공항에서 작전을 수행할 작전 요원인 여러분과 여자 친구와의 만남을 조정할 예정인 두 번째 주요 인물입니다. 또한 항공 기반 시설에 액세스 권한이 있는 컴퓨터 프로그램이 여자 친구의 DXB에서 FRA로 가는 에미레이츠 항공편의 실시간 도착 상황을 모니터링할 것입니다.

- 전설: 여자 친구가 시간을 중요시하며, 중요 작전 후 지친 상태에서 두바이 출장에서 돌아온다는 가정하에, 공항에서 그녀를 만나러 늦은 적이 생각나지 않으시나요? 중동에서 CIA 요원으로 일하는 여자 친구는 그녀를 만나러 늦었을 때 다툼이 생겼던 기억이 있습니다. 여러분은 소프트웨어 엔지니어로서 작업을 처리하는 동안 시간 관리가 어려운 경우가 있죠. 그래서 이번에는 소프트웨어를 활용하기로 했습니다.
- 이것은: 여자 친구 비행 EK47의 활동을 추적하기로 결정한 것으로, 비행 지연 가능성이 있기 때문에 정해진 도착 시간에 의존하지 않습니다. 항공 기기인 ADS-B 수신기를 사용하여 비행기의 실시간 정보를 모니터링하기로 선택했습니다.
- 옵션: 이 목표를 달성할 몇 가지 옵션이 있습니다. 1090 MHz를 청취하기 위해 RTL-SDR 장치를 사용하거나 ADS-B 수신기를 적절하게 설치하여 신호를 캡처하는 것 중 하나일 수 있습니다. 또는 ADS-B Exchange나 FlightRadar24와 같은 ADS-B 데이터 제공업체 중 하나에 연결하여 기존 항공 기반 시설을 활용할 수 있습니다.
- 설정: 이 작업을 간단히 하기 위해 접근 방식은 FlightRadar24 API에 연결하여 비행 EK47의 현재 위치(위도와 경도) 및 지상 속도(노트 단위)에 대한 실시간 데이터를 검색할 것입니다.
- 미션: 여러분의 계산에 따르면, 공항에 정시 도착하기 위해서는 여러분의 위치에서 출발하기 60분 전에 출발해야 합니다. 따라서 비행기가 도착 시간에 근접할 때 60분 전에 여행을 시작해야 함을 알리는 설정으로 구글 클라우드 플랫폼(GCP)에 스크립트를 설치할 것입니다.

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

이제 목표가 설정되었으니, 당신이 동의하신다면 미션이 시작될 수 있어요.

## 준비 사항

우선, 우리는 환경을 준비해야 합니다. 이 중요한 부분에는 세 가지 구성 요소가 포함됩니다:

- 메시지를 편리하게 받기 위해 텔레그램 봇과 채널을 만들어야 합니다. 이 작업에 대한 자세한 내용은 제 다른 글의 준비 사항 섹션에서 볼 수 있어요.
- 여자친구의 항공편인 EK47편에 대한 정보를 FlightRadar24에서 찾아야 해요. 아래 사진처럼, 해당 항공편의 등록 번호 (A6-EVB)와 도착 공항 (FRA)을 찾아야 해요.

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

![DIY for a Spy: Real-time Aircraft Monitoring without ADS-B receiver](/assets/img/2024-05-20-DIYforaSpyReal-timeAircraftMonitoringwithoutADS-Breceiver_1.png)

- GCP 웹 기능 및 작업의 소프트웨어 인프라를 설정하여 1분마다 스크립트를 실행하도록 예약합니다. 이 작업을 간단히 수행하는 방법에 대한 추가 정보는 이 기사에서 확인하세요.

모든 것이 명확하다면, 종속성을 설정해봅시다.

## 설치

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

우리의 솔루션에서는 세 가지 Python 패키지를 활용할 예정입니다: 항공기와 공항 사이의 거리를 계산하기 위해 'geopy', 공항의 실제 좌표를 얻기 위해 'airportsdata', 그리고 ADS-B 인프라에서 실시간 항공편 정보를 가져오기 위해 'FlightRadar24API'를 사용할 것입니다.

```js
geopy;
airportsdata;
FlightRadarAPI;
```

스크립트 설치에 GCP를 사용하기로 결정했으므로, 이 패키지들을 올바르게 설치하기 위해 필요한 모든 것이 GCP 웹 함수의 'requirements.txt' 파일에 이 세 줄 안에 포함되어 있습니다.

## Imports

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

우리 프로그램에서 사용할 다음 패키지 목록을 가져와야 합니다.

```js
import requests
import airportsdata

from geopy.distance import geodesic
from datetime import datetime, timedelta
from FlightRadar24 import FlightRadar24API
```

지금까지 우리가 갖고 있는 것은 다음과 같습니다:

- requests: 이를 사용하여 Telegram Bot에 HTTP 요청을 보내고, 해당 API와 상호 작용합니다.
- airportsdata: IATA 공항 코드로 공항 좌표를 검색합니다.
- geopy.distance.geodesic: 지리적 위치 간의 대원거리를 계산합니다. 여기서는 항공기의 현재 위치와 공항 사이의 거리를 계산합니다.
- datetime: 날짜와 시간 조작에 사용될 것입니다.
- FlightRadar24: FlightRadar24 API용 인터페이스입니다. 실시간 비행 데이터를 가져오는 데 우리 스크립트에서 사용될 것입니다.

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

## 메시지 전송

여자친구를 공항에서 만나러 가라는 약속을 잊지 않게 하기 위해, 우리는 텔레그램 봇과 채널을 활용할 것입니다. 우리의 스크립트는 비행기가 공항에서 1시간 이내로 접근했을 때 메시지를 보냅니다.

```js
TELEGRAM_URL = 'https://api.telegram.org'
TELEGRAM_BOT_ID = 'bot5603010001:III_xNHA14A9xznXjjcUD98GXT-A5zAAxzX' # 여러분의 봇
TELEGRAM_CHAT_ID = '-1008101011052' # 여러분의 공항 알림 채널

def send_message(message):
  response = requests.post(
        f'{TELEGRAM_URL}/{TELEGRAM_BOT_ID}/sendMessage?chat_id={TELEGRAM_CHAT_ID}&parse_mode=Markdown&text={message}')
  return response
```

위 목록에서 보듯이, 여러분은 적절한 TELEGRAM_BOT_ID 및 TELEGRAM_CHAT_ID를 생성하고 교체해야 합니다. 이를 수행하는 방법에 대한 자세한 지침은 다른 기사의 '필수 사항' 섹션에 제공되어 있습니다.

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

## 공항 좌표 가져오기

get_airport_coordinates 함수는 IATA 코드를 사용하여 공항의 위도와 경도를 검색합니다. 먼저 공항 데이터를로드하고 코드가 공항 목록 중에 있는지 확인한 후 좌표를 반올림하여 세 자리로 반환합니다. 코드가 잘못된 경우 None을 반환합니다.

```js
def get_airport_coordinates(iata_code):
  airports = airportsdata.load('IATA')
  if iata_code in airports:
    airport = airports[iata_code]
    lat = round(airport['lat'], 3)
    lon = round(airport['lon'], 3)
    return (lat, lon)
  else:
    return None
```

우리는 이 함수를 주요 스크립트에서 사용하여 'FRA'라는 IATA 코드에 대한 공항 위치를 얻습니다. 이 코드는 프랑크푸르트 공항에 해당합니다.

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

## 비행기 이력 상세 정보 가져오기

get_aircraft_trail_details 함수는 등록 번호(A6-EVB)를 사용하여 비행기의 실시간 이력 세부 정보를 검색합니다. 이 함수는 FlightRadar24 API를 사용하여 비행 데이터를 가져오고 현재 지리 위치, 속도(노트) 및 비행 번호(EK47)와 같은 정보를 추출합니다.

```js
def get_aircraft_trail_details(reg):
  fr_api = FlightRadar24API()
  flights = fr_api.get_flights(registration=reg)
  if flights:
    flight_details = fr_api.get_flight_details(flights[0])
    if flight_details:
      aircraft_lat = round(flight_details['trail'][0]['lat'], 3)
      aircraft_lon = round(flight_details['trail'][0]['lng'], 3)
      aircraft_kts = int(flight_details['trail'][0]['spd'])
      flight_num = flight_details['identification']['number']['default']
      return (aircraft_lat, aircraft_lon, aircraft_kts, flight_num)
  return None
```

이 FlightRadar24 API를 통해 ADS-B 지상 수신기로 추적된 비행기의 이력을 방송하여 비행기 모니터링을 가능하게 합니다.

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

## 도착 시간 계산

`calculate_time_to_reach_airport` 함수는 현재 좌표와 그라운드 스피드(노트 단위)에 기반하여 항공기가 공항에 도착하는 데 필요한 시간을 추정합니다. 이 함수는 도착까지의 분 수를 반환합니다.

`get_flight_eta` 함수는 항공기의 등록 번호(A6-EVB)에 기반하여 특정 항공편의 추정 도착 시간(항공기 선원이 산출한)을 검색합니다.

```js
def calculate_time_to_reach_airport(airport_coords, aircraft_coords, groundspeed_kts):
  groundspeed_kmh = groundspeed_kts * 1.852
  distance_km = geodesic(airport_coords, aircraft_coords).kilometers
  time_minutes = int(distance_km / groundspeed_kmh * 60)
  return time_minutes

def get_flight_eta(reg):
  eta = 0
  fr_api = FlightRadar24API()
  flights = fr_api.get_flights(registration=reg)
  if flights:
    flight_details = fr_api.get_flight_details(flights[0])
    if flight_details:
      arrival_time = datetime.fromtimestamp(flight_details['time']['estimated']['arrival'])
      current_time = datetime.now()
      time_difference = (arrival_time - current_time).total_seconds() / 60
      eta = time_difference
  return int(eta)
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

아래 그림에 표시된 대로, 조종실에서 제공하는 도착 예정 시간은 API의 JSON 응답에서 제공됩니다.

![그림](/assets/img/2024-05-20-DIYforaSpyReal-timeAircraftMonitoringwithoutADS-Breceiver_2.png)

우리는 도착 예정 시간을 추가 데이터로 활용하며 완전히 의존하지는 않습니다. 이 접근 방식은 뒤로 가면 '공항 알림'이라는 주요 기능에서 확인할 수 있습니다.

## 공항 알림

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

메인 기능인 aircraft_approaches_airport_go를 살펴보겠습니다. 이 함수는 우리 스파이 응용 프로그램의 핵심 로직을 실행하는 함수로, GCP 스케줄러로부터 요청을 받습니다. 이 요청에는 공항의 IATA 코드(airport_iata_code), 항공기의 등록 번호(aircraft_reg) 및 알림 메시지를 보낼 시간(remind_me)이 포함된 JSON 데이터가 포함되어 있습니다.

변수 초기화 바로 다음에는 함수가 제공된 IATA 코드를 사용하여 공항의 좌표를 검색하고, 항공기의 등록 번호를 사용하여 항공기의 상세한 트레일 정보를 검색합니다. 항공기의 속도(노트)와 공항까지의 거리를 사용하여 항공기의 예상 도착 시간(ETA)을 계산합니다.

또한 조종원이 제공한 예상 도착 시간(ETA)을 얻기 위해 get_flight_eta 함수를 사용합니다.

```python
def aircraft_approaches_airport_go(request):
  request_json = request.get_json()

  airport_iata_code = request_json.get('airport_iata_code')
  aircraft_reg = request_json.get('aircraft_reg')
  remind_me = int(request_json.get('remind_me'))

  airport_coords = get_airport_coordinates(airport_iata_code)
  if airport_coords:
      print(f'{airport_iata_code} 공항 좌표: {airport_coords}')

  acrd = get_aircraft_trail_details(aircraft_reg)
  if acrd:
      print(f'{aircraft_reg} 항공기의 트레일 세부 정보: {acrd}')

  minutes = calculate_time_to_reach_airport(airport_coords, (acrd[0], acrd[1]), acrd[2])
  print(f'{acrd[3]} 편의 {airport_iata_code} 공항에 도착 예상 시간: {minutes} 분')

  minutes_eta = get_flight_eta(aircraft_reg)
  if minutes_eta > 0:
    print(f'{acrd[3]} 편의 {airport_iata_code} 공항에 도착 예상 시간: {minutes_eta} 분')

  average_estimation = int((minutes + minutes_eta) / 2)
  print(f'{acrd[3]} 편의 평균 ETA: {average_estimation} 분')

  if remind_me >= average_estimation:
    print('출발해야 합니다....')
    message = f'*공항 알림*: *{airport_iata_code}* 공항으로의 도착 예상 시간이 평균 {average_estimation}분보다 더 적다는 것을 알려드립니다.' + \
    f'\n\t_시간 맞춰 여자친구를 공항에서 맞으러 가야 합니다!_'
    send_message(message)

  return f'공항 알림: 완료!'
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

나중에 함수는 계산된 ETA와 검색된 ETA의 평균을 내어 평균 ETA를 계산합니다. 만약 남은 시간이 평균 ETA보다 크거나 같으면, 공항으로 가실 시간을 알려주는 리마인더 메시지를 보냅니다.

메세지를 받았다면 서두르세요!

## GCP 함수와 스케줄러

이 섹션에 도달했다면, Google Cloud Functions 및 예약된 작업을 만드는 능력이 있다고 가정합니다. 그렇지 않다면, 다른 내 기사 중 하나에서 예시를 찾아보세요.

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

해당 기사를 읽은 후에는 쉽게 'Airport-reminder-launcher' 스케줄러를 만들고, 아래 그림에 나와있는 것처럼 'Airport-reminder' 기능을 설정할 수 있을 겁니다.

![Airport-reminder 설정](/assets/img/2024-05-20-DIYforaSpyReal-timeAircraftMonitoringwithoutADS-Breceiver_3.png)

여기서 강조하고 싶은 점은, 이 섹션을 IATA 코드 (airport_iata_code)를 `FRA`, 항공기 등록 번호 (aircraft_reg)를 `A6-EVB`, 그리고 공항에 도착하는 데 필요한 분 (remind_me)을 각자의 도시의 거리와 교통 상황에 따라 직접 계산하여 설정해야 한다는 것입니다. 저는 60분을 사용했지만, 여러분은 각자의 상황에 맞게 조절할 수 있습니다.

## 결과

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

좋아요. 이 기사에서 설명한 모든 것을 따르면 아래 그림에 표시된 것과 유사한 텔레그램 메시지를 받게 됩니다. 비행기가 도착 60분 전에 나타나면 매 분마다 해당 메시지를 받게 됩니다.

![이미지](/assets/img/2024-05-20-DIYforaSpyReal-timeAircraftMonitoringwithoutADS-Breceiver_4.png)

공항으로 여행을 시작하는 것을 상기시켜주는 친절한 알림입니다. 그렇지 않으면 실제로 슈퍼걸인 사랑스러운 여자친구와 마지막으로 다투었던 상황을 기억하실 겁니다.

[수퍼걸들은 울지 않아요..., 수퍼걸들은 그냥 날아요]

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

그렇게 사소한 기술들과 이 실험에서 우리가 한 모든 것들로, 나는 당신이 공항에 시간 맞춰 도착해 그녀를 만나기 준비를 할 것이라 믿어요 :)

![이미지](/assets/img/2024-05-20-DIYforaSpyReal-timeAircraftMonitoringwithoutADS-Breceiver_5.png)

## X-Files

당신의 장치에서 이 스크립트들을 시도해보려면 아래에 나열된 소스 코드를 다운로드하고 학습해야 합니다.

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

- GCP용 main.py: main.py
- GCP용 requirements.txt: requirements.txt
- 소스 코드: Google Research Colab

## 문의 사항이 있으시면

본문에서 논의된 개념이나 다른 아이디어에 대한 질문이 있으시면 언제든지 트위터로 연락해 주세요.

트위터: https://twitter.com/dmytro_sazonov

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

![2024-05-20-DIYforaSpyReal-timeAircraftMonitoringwithoutADS-Breceiver_6](/assets/img/2024-05-20-DIYforaSpyReal-timeAircraftMonitoringwithoutADS-Breceiver_6.png)
