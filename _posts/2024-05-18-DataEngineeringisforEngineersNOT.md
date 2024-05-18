---
title: "데이터 엔지니어링은 엔지니어를 위한 것입니다  아니에요"
description: ""
coverImage: "/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_0.png"
date: 2024-05-18 18:24
ogImage: 
  url: /assets/img/2024-05-18-DataEngineeringisforEngineersNOT_0.png
tag: Tech
originalTitle: "Data Engineering is for Engineers — NOT!"
link: "https://medium.com/stackademic/data-engineering-is-for-engineers-not-49fe0dc22497"
---


## 글을 쓰는 사람이 데이터 엔지니어링도 배울 수 있을 거라고 생각해요.

"이게 무슨 일이죠."

처음 데이터 엔지니어링 업무를 맡았을 때 나는 이렇게 생각했어요. 하지만 얼마 후, 우리가 하나씩 위키피디아에서 집어낼 몇 가지 숫자를 가져오는 것이 첫 번째 단계였다는 것을 깨달았죠.

파이썬에서 작업을 해주셔야겠다고 말씀하셔야 했던 첫 번째 어려움이 있었어요! 즉, 데이터를 수동으로 가져오는 날이 끝났다는 거죠. 인내심을 가지고 자신에게 이런 말을 해보세요. 괜찮아, 이 일 잘 할 수 있어요!

<div class="content-ad"></div>

문제: 사회과학 졸업생이 데이터 엔지니어링을 능숙하게 수행할 수 있을까요? 자동화된 데이터 파이프라인을 구축하고 안전하게 클라우드에 보관할 수 있을까요?

귀무가설: 엔지니어는 생업으로 글을 쓰는 사람만큼 데이터 엔지니어링을 할 수 있습니다.

이 가설을 기각해야 할까요? 내 직감은 "예"라고 말했습니다. IT 비전문가인 나 같은 사람이 그렇게 할 수는 없을 것 같아요.

대안 가설은 아마도 이렇게 할 수 있을 것 같아요: 생업으로 글쓰는 사람도 엔지니어처럼 데이터 엔지니어링을 배울 수 있다면요.

<div class="content-ad"></div>

여기서 시작해요...

## 우리 비즈니스 케이스

이 작업은 주로 전기 이동성에 관련된 사업을 하는 "Gans" 회사를 위한 데이터베이스를 생성하는 것을 포함하고 있습니다. 특정 시간에 충분한 이동성 단위를 제공하기 위해 날씨 요소를 기반으로 수요를 측정하기 위한 데이터 과학자를 필요로 합니다.

회사의 주요 사업은 독일에서 스쿠터를 대여하는 것이기 때문에 비가 오거나 눈이 오는 경우에는 일반적으로 수요가 줄어들게 됩니다. 비나 눈이 예보된 경우, 임시 수요도 증가할 것입니다.

<div class="content-ad"></div>

따라서, 현재 과제는 도시와 날씨 정보로 채워진 간단한 SQL 데이터베이스를 구축하는 것입니다. 회사의 운영 부서는 매일이 이 데이터베이스에 액세스하여 이동 가능 차량의 지리적 가용성에 관한 판단을 내릴 수 있을 것입니다.

나중에 데이터베이스는 관광객 방문 예상 등을 제공하기 위해 공공 교통 도착 정보(예: 항공편, 기차, 또는 버스 API 사용)가 포함될 수 있습니다. 이들은 회사의 잠재적 고객 중 일부입니다.

다음은 날씨와 항공편 API를 포함한 지도 프로젝트의 모습입니다:

<img src="/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_0.png" />

<div class="content-ad"></div>

이 기사에서는 날씨 데이터 통합만 다루고 나중에 전송 부분은 나중에 개선할 것입니다.

시작하기 전에 Python에서 필요한 모든 라이브러리를 가져와야 합니다:

```python
import pandas as pd
import requests
from bs4 import BeautifulSoup
from datetime import datetime
from pytz import timezone
```

## 웹 스크래핑 101: HTML 코드를 가져오는 방법?

<div class="content-ad"></div>

먼저, 다른 테이블과 관련하는 주요 테이블로 작용하는 도시 정보가 포함된 테이블을 만들어야 합니다. 이 테이블은 주기적으로 업데이트할 필요가 없는 정적 테이블이 될 것입니다. 이 테이블을 "cities_info"라고 부를 것입니다.

각 도시에 대해 고유한 도시 ID, 도시 이름, 독일의 소속 주(State), 위도 및 경도가 포함된 5개 열이 있을 것입니다. 각 도시에 대한 이 정보는 영어 위키피디아 사이트에서 스크래핑하여 얻을 수 있습니다. 예산이 허용한다면, 신뢰할 수 있는 데이터와 방법론적 정보가 포함된 도시의 API를 구매할 수 있습니다. 이 프로젝트에서는 위키피디아를 사용하여 스크래핑 기술을 연습할 것입니다.

처음에는 각 웹사이트마다 HTML 코드를 수동으로 내보내고 Vs Code에 복사해야 한다는 생각이 압도적이었습니다. 그러나 다행히 파이썬의 "requests" 라이브러리를 사용하면 자동으로 수행할 수 있음을 알게 되어 안도했습니다:

```js
import requests
berlin_url = "https://en.wikipedia.org/wiki/Berlin"
berlin_response = requests.get(berlin_url)
berlin_soup = BeautifulSoup(berlin_response.content, 'html.parser')
print(berlin_soup.prettify)
```

<div class="content-ad"></div>

파이썬: 1 - 나: 0

## 루프와 함수

이터레이션의 성배입니다. 이들 없이는 동일한 코드를 재생산하고 다른 URL에 동시에 적용할 수 없습니다. 이것이 웹 스크레이핑을 자동화하는 열쇠입니다.

여기에는 각 위키피디아 사이트의 HTML 코드를 검색하기 위해 사용한 루프의 예시가 있습니다:

<div class="content-ad"></div>

```js
cities_list = ["Berlin", "Hamburg", "Munich", "Cologne", "Frankfurt"]

for city in cities_list:
  url = f"https://www.wikipedia.org/wiki/{city}"   # url을 f 문자열로 변환하여 도시를 변수로 넣어 해당 도시에서 다른 도시로 변경됨
  response = requests.get(url)          # 위키백과 페이지의 모든 내용을 가져와 response에 저장
  city_soup = BeautifulSoup(response.content, 'html.parser')
```

지금은 함수가 무서워서 최대한 피하고 있어요 😅

파이썬: 1,000 — 나: 0

## HTML에서 무엇을 접근하나요?

<div class="content-ad"></div>

HTML에서 "p 태그"라는 것이 있어요. 이를 액세스하는 데 유용합니다. 다음 코드를 사용하여 그렇게 할 수 있어요:

```js
print(soup.p)   # 첫 번째 p 태그 자체에 접근
print(soup.p.string)  # 첫 번째 p 태그와 관련된 문자열에 접근
```

```js
for child in soup.div:  # 1번째 div에서 각 자식을 찾아서 인쇄하는 예제
    print(child)
```

## Python에서 데이터프레임 및 SQL에서 해당 테이블 만들기

<div class="content-ad"></div>

HTML 및 웹 스크래핑 기술을 연마한 후, 첫 두 테이블에 필요한 모든 정보를 얻기 위해 이 루프를 만들었어요.

첫 번째 데이터프레임은 정적 인덱스로 작동할 것이고 (즉, 정보를 정기적으로 업데이트할 필요가 없어요): cities_info

```js
cities_list = ["베를린", "함부르크", "뮌헨", "쾰른", "프랑크푸르트"]
states = []
latitudes = []
longitudes = []

for city in cities_list:
  url = f"https://www.wikipedia.org/wiki/{city}"   
  city_soup = BeautifulSoup(response.content, 'html.parser')    # (위키백과 사이트의 내용을 city_soup 변수에 저장하는) 내용을 구문 분석합니다.

  # 도시가 속한 주를 검색합니다
  if city not in ["함부르크", "베를린"]:         # 베를린의 경우 일반적인 .find 공식도 작동할 거예요! 주 섹션이 없는 "함부르크"만이 예외에요
    city_state = city_soup.find("table", class_="vcard").find(string="주").find_next("td").get_text()  # 다른 주 이름을 가진 도시에 대한 주를 검색합니다
  else:
    city_state = city     # 함부르크와 베를린의 경우, 동명의 도시 이름을 가져올 거예요
  states.append(city_state)

  # 각 도시의 위도를 검색하여 위도 열에 추가합니다
  city_latitude = city_soup.find(class_="latitude").get_text()
  latitudes.append(city_latitude)  

   # 각 도시의 경도를 검색하여 경도 열에 추가합니다
  city_longitude = city_soup.find(class_="longitude").get_text()
  longitudes.append(city_longitude)  

cities_info_non_rel = pd.DataFrame({         # 이것이 cities_info 데이터프레임이에요
    "도시 이름": cities_list,
    "독일 주": states,
    "위도": latitudes,
    "경도": longitudes
})  

display(cities_info_non_rel)    # 표시하면 테이블이 더 예쁘게 보여요
```

또한, 연간 한 번씩 업데이트될 것으로 예상되는 인구 데이터를 포함하는 두 번째 데이터프레임도 생성했어요: cities_population

<div class="content-ad"></div>

```sql
/***************************
환경 설정
***************************/

-- 데이터베이스가 이미 있는 경우 삭제
DROP DATABASE IF EXISTS gans;

-- 데이터베이스 생성
CREATE DATABASE gans;

-- 데이터베이스 사용
USE gans;


/***************************
첫 번째 테이블 생성
***************************/

-- 'cities_info' 테이블 생성
CREATE TABLE cities_info (
    cities_id INT AUTO_INCREMENT, -- 도시마다 자동으로 생성된 ID
    city_name VARCHAR(255) NOT NULL, -- 도시 이름
    german_state VARCHAR(255) NOT NULL, -- 주 이름
    latitude VARCHAR(255) NOT NULL, -- 위도 좌표
    longitude VARCHAR(255) NOT NULL, -- 경도 좌표
    PRIMARY KEY (cities_id) -- 각 도시를 고유하게 식별하기 위한 기본 키
    );


/***************************
두 번째 테이블 생성
***************************/ 

CREATE TABLE cities_population (
    cities_id INT,
    population INT, -- 인구수
    year_data_retrieved INT, -- 인구 데이터를 검색한 해당 년도
    FOREIGN KEY (cities_id) REFERENCES cities_info(cities_id)
);
```

이제 Python에서 데이터를 첫 번째 SQL 테이블로 전송합니다:```

<div class="content-ad"></div>

```js
schema = "gans"     # 데이터베이스 이름
host = "xxx.x.x.x"
user = "root"               # 사용자 이름
password = "xxxx"           # SQL 암호 직접 지정 또는 다른 노트북에서 가져오기 ("from xxxfile import my_password")
port = 3306

connection_string = f'mysql+pymysql://{user}:{password}@{host}:{port}/{schema}'    
# 이건 파이썬 노트북을 SQL Workbench에 연결하는 방법이에요
```

```js
cities_info_non_rel.to_sql('cities_info',   # 파이썬에서 SQL로 데이터를 보내는 방법
                  if_exists='append',       # 덮어쓰지 않고 기존 데이터에 추가하기 위함
                  con=connection_string,    # SQL Workbench에 연결할 때 필요한 인자
                  index=False)    
```

첫 번째 시도는 로컬에서 이루어졌습니다. 나중에 Google Cloud Platform 인스턴스를 추가하면 "host" 필드를 편집하여 이 데이터를 클라우드에 직접 전송할 수 있습니다.

SQL에 첫 번째 테이블이 생성되면 cities_info에 포함된 데이터를 검색하여 두 번째 cities_population 데이터 프레임에서 해당 cities_id 열을 인덱스로 사용할 수 있습니다:```

<div class="content-ad"></div>

```js
cities_info = pd.read_sql("cities_info", con=connection_string)   # 이 코드는 SQL에 저장된 정보를 "읽어옵니다"
cities_info
```

다음은 cities_info의 모습입니다:

<img src="/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_1.png" />

두 번째 데이터프레임의 내용을 SQL로 넣기 전에, 새로 생성된 cities_id 열을 사용하여 cities_populations 데이터프레임에 추가해야 합니다.

<div class="content-ad"></div>

아래는 칼럼 순서를 재조정하고, 이제 cities_populations 데이터프레임에 cities_id 칼럼이 있기 때문에 더 이상 city_name 칼럼이 필요하지 않으므로 삭제했습니다:

```js
# 다른 인구 데이터프레임에 cities_id 칼럼을 추가합니다
cities_population["cities_id"] = cities_info_non_rel["cities_id"]
cities_population

# city_name 칼럼은 더 이상 필요하지 않기 때문에 삭제하고 칼럼 순서를 바꿔 더 직관적으로 만듭니다
cities_population = cities_population[["cities_id", "population", "year_data_retrieved"]]
cities_population
```

그리고 제 두 번째 데이터프레임이 있습니다. 이 데이터프레임은 일부 동적입니다(데이터가 때때로 업데이트됩니다. 예: 연간 한 번):

![데이터 엔지니어링은 엔지니어를 위한 것](/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_2.png)
```

<div class="content-ad"></div>

당분간은, 다음 해까지 업데이트되지 않을 동적이 아닌 cities_population 테이블로 간주하겠습니다.

이제 우리는 로컬 SQL 인스턴스로 내용을 푸시할 준비가 되었습니다:

```js
# 두 번째 테이블 내용을 SQL로 푸시합니다
cities_population.to_sql('cities_population',   # 이렇게 하면 Python에서 SQL로 푸시합니다
                  if_exists='append',       # 덮어쓰기를 원하지 않으므로, 기존 데이터에만 데이터를 추가합니다
                  con=connection_string,    # con은 sql workbench에 연결하기 위해 필요한 인자입니다
                  index=False)    
```

SQL에서 모든 것을 실행하고 "역공학" 기능을 사용하면, 우리의 스키마의 가장 초기 버전을 얻을 수 있습니다:

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_3.png" />

우리는 학습 곡선 속으로 더 깊이 파고들고 있어요.

다음 단계: API를 데이터베이스에 통합하기. 지구상의 모든 데이터 과학자의 본질이라고 할 수 있죠.

AI가 따라잡기 전에 어떻게 해야 할지 배워야겠네요! (또는 외계인이 그들의 데이터 요구를 어떻게 처리하는지를 보일 때까지) 👽

<div class="content-ad"></div>

## 날씨 API 사용하기

이 프로젝트의 세 번째 큰 단계는 현재 존재하는 데이터 저장소에서 날씨 데이터를 추출하고 SQL 데이터베이스에 통합하는 것입니다. 이러한 저장소들은 API를 통해 접근할 수 있습니다. API는 Application Programming Interface의 약자로, 서로 다른 소프트웨어 응용 프로그램 간에 통신할 수 있도록 하는 규칙과 프로토콜의 집합입니다.

이를 위해 우리는 openweathermap.org 웹사이트를 사용할 것입니다. 이 사이트를 통해 전 세계 어느 위치의 무료 날씨 예보에도 접근할 수 있습니다. 우리는 5일 날씨 예보 API를 사용할 것이며, 3시간 간격의 예보 데이터를 포함합니다: [https://openweathermap.org/forecast5](https://openweathermap.org/forecast5)

이제 우리 프로젝트의 핵심 작업에 직면했습니다: 우리가 원하는 날씨 데이터를 검색하고 SQL 데이터베이스로 전송하기 위한 필요한 코드를 작성하는 것입니다. 수십 시간 동안 고군분투한 결과, 가장 관련성 있는 날씨 데이터를 추출하기 위한 이 코드를 개발해냈습니다:

<div class="content-ad"></div>

```js
def get_weather_data(cities):
  # 클라우드에서 작업할 예정이므로 컴퓨터는 어디에 있을지 모릅니다 - 컴퓨터 시간대를 우리 지역 시간대로 수정합시다.
  berlin_timezone = timezone("Europe/Berlin")
  API_key = "7e5623c79f102b6c08b15c8hjib4cc9l"    # 이것은 실제 값이 아닙니다.
  weather_items = []

  for city in cities:
    url = (f"http://api.openweathermap.org/data/2.5/forecast?q={city}&appid={API_key}&units=metric")
    response = requests.get(url)
    json = response.json()

    # 예보를 작성한 시간을 알기 위해 검색 시간을 추가했습니다.
    retrieval_time = datetime.now(berlin_timezone).strftime("%Y-%m-%d %H:%M:%S")

    # 이제 우리의 관련 데이터베이스에서 데이터를 사용하므로, 도시는 도시 이름이 아닌 도시 ID를 반영해야 합니다.
    city_id = cities_info.loc[cities_info["city_name"] == city, "city_id"].values[0]  # 여기에서 값들을 검색해야 하며, 그렇지 않으면 시리즈를 보여줍니다.

    for item in json["list"]:
        weather_item = {
            # 여러 도시를 고려할 때 정보를 명확히 알 수 있게 도시 이름을 추가했습니다.
            "city_id": city_id,
            "forecast_time": item.get("dt_txt", None),
            "temperature": item["main"].get("temp", None),
            "feels_like": item["main"].get("feels_like", None),
            "forecast": item["weather"][0].get("main", None),
            "humidity": item["main"].get("humidity", None),
            "rain_in_last_3h": item.get("rain", {}).get("3h", 0),
            "risk_of_rain": item["pop"],
            "snow_in_last_3h": item.get("snow", {}).get("3h", 0),      
            "wind_speed": item["wind"].get("speed", None),
            "data_retrieved_at": retrieval_time
        }

        weather_items.append(weather_item)
  
  weather_df = pd.DataFrame(weather_items)
  weather_df["forecast_time"] = pd.to_datetime(weather_df["forecast_time"])
  weather_df["data_retrieved_at"] = pd.to_datetime(weather_df["data_retrieved_at"])
  weather_df["snow_in_last_3h"] = pd.to_numeric(weather_df["snow_in_last_3h"], downcast="float.")

  return weather_df

weather_df = get_weather_data(["Berlin", "Hamburg", "Munich", "Cologne", "Frankfurt"])
weather_df     # 함수를 사용하여 새로운 데이터프레임 생성
```

날씨 데이터프레임 및 테이블의 중요 기능 중 하나는 데이터 검색 시간을 기록하는 열을 포함해야 한다는 것입니다. 이를 통해 필터링 및 일정한 기간 후 자동으로 이전 데이터가 삭제되도록 SQL에서 프로시저나 함수를 작성하거나 만들 수 있습니다. 이는 데이터 일관성 목적으로 날씨와 데이터 검색 시간이 항상 예보에 대한 날짜/시간과 다른 것임을 항상 인식할 수 있도록 하기 위한 것입니다.

이것이 Python에서 새로운 날씨 데이터프레임이 보이는 방식입니다:

<img src="/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_4.png" />
```

<div class="content-ad"></div>

일단 날씨 데이터프레임 구조를 가지고 있었을 때, SQL에서 날씨 테이블을 생성하는 작업을 시작했습니다:

```js
/***************************
날씨 테이블을 생성합니다
***************************/ 

CREATE TABLE weather (
    city_id INT,
    forecast_time datetime,
    temperature float,
    feels_like float,
    forecast VARCHAR(255) NOT NULL,
    humidity INT,
    rain_in_last_3h FLOAT,
    risk_of_rain FLOAT,
    snow_in_last_3h FLOAT,
    wind_speed FLOAT,
    data_retrieved_at DATETIME,
    FOREIGN KEY (city_id) REFERENCES cities_info(city_id)
    );

-- TRUNCATE TABLE weather;   -- 테이블이 너무 커지고 데이터가 오래되었을 때 모든 행을 지워야 할 수도 있습니다
```

이것은 엔지니어링을 통해 역 공학을 거쳐 업데이트된 SQL 스키마입니다:

<img src="/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_5.png" />

<div class="content-ad"></div>

## 지역에서 전역으로

우리의 코드를 클라우드에 올리기 전에, 날씨 데이터 검색 코드가 로컬에서 작동하는지 확인해야 합니다. Python으로 코드를 작성한 후, 로컬 SQL 인스턴스로 전송해보겠습니다:

```python
weather_df.to_sql("weather",          
                  if_exists='append',      
                  con=connection_string,
                  index=False)    
```

만약 새 데이터가 우리의 SQL 테이블에 추가된다면, 작동하는 것입니다!

<div class="content-ad"></div>

파이썬을 통해 로컬 데이터를 입력한 후 SQL 날씨 테이블이 어떻게 보이는지는 다음과 같습니다:

![Weather Table](/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_6.png)

## Google Cloud Platform 통합

로컬에서 코드가 작동하는 것을 확인하면, 이제 클라우드에 올려보는 시간입니다.

<div class="content-ad"></div>

구글 클라우드 플랫폼(GCP) 계정을 열고 클라우드 인스턴스를 설정해야 합니다. 여기서는 자세히 다루지는 않겠지만 MySQL을 사용한 좋은 단계별 설명을 찾을 수 있습니다: https://support.google.com/appsheet/answer/10107301?hl=en

제게 가장 중요한 단계 중 하나는 파이썬 노트북에서 로컬 IP 호스트를 구글 IP 호스트로 변경하는 것이었습니다. 구체적으로, 데이터를 SQL로 전송하는 코드 블록에서 변경했습니다:

```js
schema = "gans"         # 데이터베이스 이름
host = "XX.XXX.XX.XX"   # 로컬 호스트:"xxx.x.x.x" (클라우드로 변경하기 전)
user = "root"           # 사용자 이름 (가이드 참조)
password = "XXXX"       # 비밀번호 직접 입력하거나 다른 노트북에서 가져옵니다 ("from xxxfile import my_password")
port = 3306

connection_string = f'mysql+pymysql://{user}:{password}@{host}:{port}/{schema}'     # 이 부분이 파이썬 노트북을 SQL 워크벤치에 연결하는 역할을 합니다
```

이렇게 함으로써, 이전에 생성한 정적 테이블을 클라우드에 자동으로 업로드하고 파이썬에서 코드를 다시 실행하지 않고 이미 존재하는 SQL 테이블에 데이터를 채울 수 있었습니다. 아니면 적어도 파이썬에서 코드를 다시 실행할 필요가 없습니다. 혹은 새로운 비동적 테이블(도시 정보 및 도시 인구)을 업데이트하기로 결정할 때까지입니다.

<div class="content-ad"></div>

이 프로젝트의 목적으로, Python에서 이 두 표를 GCP로 업로드하려면 호스트 IP를 변경하는 것으로 충분합니다. 또 다른 방법은 두 표를 만드는 코드를 클라우드에 업로드하는 것입니다. 이 경우, 우리는 클라우드에 동적 표 날씨를 만들고 채우는 코드만 올릴 것입니다.

GCP의 "Cloud Functions" 필드에 함수를 만든 후 여러 번의 시행착오 끝에 코드가 마침내 작동했습니다:

![cloud_function_image](/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_7.png)

하지만 코드가 작동하기 전에 클라우드 인스턴스에 함수를 연결해야합니다. 아래 단계를 따라야합니다:

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_8.png" />

작업이 잘되었는지 어떻게 알 수 있을까요? 가장 좋은 소식은 다음과 같이 보입니다:

<img src="/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_9.png" />

로컬에서 테스트한 코드와 비교해 상당한 수정이 필요했습니다. 최종 코드는 이렇게 보입니다:

<div class="content-ad"></div>

```python
import functions_framework
import pandas as pd
import sqlalchemy
import requests  
from pytz import timezone
from datetime import datetime 

@functions_framework.http
def insert(request):
  connection_string = connection()
  insert_into_weather(connection_string)
  return 'Data successfully added'

def connection():
  connection_name = "flying-dove-416317:europe-west1:wbs-mysql-db"    # this is not a real one
  db_user = "root"
  db_password = "xxxx"   # fill in with your SQL password
  schema_name = "gans"

  driver_name = 'mysql+pymysql'
  query_string = {"unix_socket": f"/cloudsql/{connection_name}"}

  db = sqlalchemy.create_engine(
      sqlalchemy.engine.url.URL(
          drivername = driver_name,
          username = db_user,
          password = db_password,
          database = schema_name,
          query = query_string,
      )
  )
  return db


# HERE STARTS THE WEATHER DATA RETRIEVAL FUNCTION:

def extract_city(connection_string):
    return pd.read_sql("cities_info", con=connection_string)

def get_weather_data(cities_df):
  berlin_timezone = timezone("Europe/Berlin")
  API_key = "7e5623c79f102b6c08b15c8hjib4cc9l"    # this is not a real one
  weather_items = []
    
  for city in cities_df["city_name"]:
    url = (f"http://api.openweathermap.org/data/2.5/forecast?q={city}&appid={API_key}&units=metric")
    response = requests.get(url)
    json = response.json()

    # Added the time retrieved so we know when the forecast was made
    retrieval_time = datetime.now(berlin_timezone).strftime("%Y-%m-%d %H:%M:%S")

    # As we are now using the data from our relational database, the city should reflect the city_id and not the city name
    city_id = cities_df.loc[cities_df["city_name"] == city, "city_id"].values[0]  # here we need to retrieve the values, otherwise it shows us the series

    for item in json["list"]:
        weather_item = {
            # Added the city name, so the information is clear when looking at multiple cities
            "city_id": city_id,
            "forecast_time": item.get("dt_txt", None),
            "temperature": item["main"].get("temp", None),
            "feels_like": item["main"].get("feels_like", None),
            "forecast": item["weather"][0].get("main", None),
            "humidity": item["main"].get("humidity", None),
            "rain_in_last_3h": item.get("rain", {}).get("3h", 0),
            "risk_of_rain": item["pop"],
            "snow_in_last_3h": item.get("snow", {}).get("3h", 0),      
            "wind_speed": item["wind"].get("speed", None),
            "data_retrieved_at": retrieval_time
        }

        weather_items.append(weather_item)
  
  weather_df = pd.DataFrame(weather_items)
  weather_df["forecast_time"] = pd.to_datetime(weather_df["forecast_time"])
  weather_df["data_retrieved_at"] = pd.to_datetime(weather_df["data_retrieved_at"])
  weather_df["snow_in_last_3h"] = pd.to_numeric(weather_df["snow_in_last_3h"], downcast="float")

  return weather_df

def insert_into_weather(connection_string):
  cities_df = extract_city(connection_string)
  weather_df = get_weather_data(cities_df)    # we create the new dataframe using the function
  weather_df.to_sql("weather", 
            if_exists="append",
            con=connection_string,
            index=False)
```

## Lessons learned from making our code cloud-worthy

### 1. Dependencies:

We need to add the right dependencies to the `requirements.txt` file. This was one of the main initial reasons preventing our code from working properly. It is important to note that some libraries are already uploaded on GCP by default and should not be included in the `.txt` file, but still need to be added as a library in our source code, e.g.:
```

<div class="content-ad"></div>

```js
from pytz import timezone
from datetime import datetime
```

만약 배포를 시도한 후에 빨간 배너 에러가 발생한다면, 대부분의 경우 requirements.txt 파일에 명시되어 있지 않은 라이브러리를 import하는 것이 원인일 수 있습니다:

![image](/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_10.png)

requirements 섹션에서는 Python 모듈이 아닌 패키지만 추가해야 합니다. 미리 알려드리자면, 여기 Python 모듈들의 종합 목록이 있습니다: [Python 모듈 목록](https://docs.python.org/3/py-modindex.html)```

<div class="content-ad"></div>

다른 유용한 팁은 코드에서 사용 중인 모든 외부 라이브러리를 출력하는 것입니다. 노트북에서 함수를 호출한 후에는 다음을 실행해야 합니다:

```js
print('\n'.join(f'{m.__name__}=={m.__version__}' for m in globals().values() if getattr(m, '__version__', None)))
```

그런 다음 적절한 종속성으로 requirements.txt에 직접 복사하여 붙여넣을 수 있습니다:

![이미지](/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_11.png)

<div class="content-ad"></div>

이 프로젝트를 위해 필요한 의존성은 다음과 같습니다 .txt 파일:

```js
functions-framework==3.*
SQLAlchemy==1.4.37
PyMySQL==1.0.2
pandas==1.5.2
requests==2.31.0
```

## 2. 연결 코드 테스트

데이터 검색 함수를 추가하기 전에 연결 코드가 작동하는지 먼저 테스트하는 것이 좋습니다. 이렇게 하면 문제가 연결 설정에 있는지 아닌지 확인할 수 있습니다.

<div class="content-ad"></div>

로컬 스크립트를 GCP로 이동할 때 좋은 시작 방법은 "더미" 함수를 만들고 테스트하는 것입니다. 예를 들어:

```js
import functions_framework
import pandas as pd
import sqlalchemy

@functions_framework.http
def insert(request):
  connection_string = connection()
  insert_into_test_table(connection_string)
  return 'Data successfully added'

def connection():
  connection_name = "YOUR_DB_CON_NAME"  # 변경해주세요
  db_user = "root"                      # 변경
  db_password = "YOUR_PASSWORD"         # 변경
  schema_name = "test_schema"           # 변경

  driver_name = 'mysql+pymysql'
  query_string = {"unix_socket": f"/cloudsql/{connection_name}"}

  db = sqlalchemy.create_engine(
      sqlalchemy.engine.url.URL(
          drivername = driver_name,
          username = db_user,
          password = db_password,
          database = schema_name,
          query = query_string,
      )
  )
  return db

def insert_into_test_table(con_str):
  data = {'FirstName': ['Function', 'Test'],
          'City': ['Cloud', 'Complete']}
  df = pd.DataFrame(data)
  df.to_sql(name="test_table", con=con_str, if_exists='append', index=False)
```

이 간단한 함수가 작동하면 연결이 작동하는 것이고, 할 일은 실제 코드를 추가하고 통합하는 것뿐입니다.

다음 오류가 발생하면 우리의 코드에 문제가 있음을 가정할 수 있습니다.

<div class="content-ad"></div>

## 3. 우리 실제 코드의 문제점

내 코드를 온라인에 올릴 때 실수한 주요한 부분은 "더미" 템플릿을 기반으로 코드를 조정하고 실제로 정의되지 않은 "insert_into_weather" 함수를 "@functions_framework.http" 아래에서 호출했다는 것이었습니다.

이 함수를 추가한 후에도 코드가 작동하지 않았습니다. 이는 필요한 도시를 수동으로 명명했기 때문에 도시 정보 데이터프레임에서 이미 가져오는 대신에 도시 이름을 수동으로 지정해서 오는 점과 관련이 있었습니다. 그래서 나는 먼저 "extract_city"라는 또 다른 함수를 추가해야 했고, 이 함수가 로컬에서 작동하는 것을 확인 후에 코드를 온라인에 배포해야 했습니다.

도시 이름을 얻기 위해 cities_info 테이블을 사용한 후에, 도시 이름을 추출하지 않은 전 반복문을 업데이트하지 않은 다른 실수를 했습니다. 이제 도시 이름을 이전에 목록에서 가져오지 않고 데이터프레임에서 가져와야 했고, 이전 인수로는 도시 이름을 찾지 못했습니다.

<div class="content-ad"></div>

# 마지막 단계

우리 프로젝트를 완전히 기능적이고 자동화하기 위해 주기적으로 실행할 함수를 예약해야 합니다. GCP의 "Cloud Scheduler" 메뉴를 사용하면 상당히 쉽습니다.

이를 위해 "작업 예약"을 하고 몇 가지 매개변수를 제공하고 빈도를 설정해야 합니다(매주 한 번 또는 Cron 표현식을 사용하여 특정 시간에 특정 요일에 실행하도록 설정). 이렇게 하면 해당 시간에 함수가 실행되어 SQL 테이블에 필요한 데이터를 채웁니다. 예를 들어, 여기서는 매주 월요일 오후 3시에 실행되도록 설정했습니다:

![이미지](/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_12.png)

<div class="content-ad"></div>

SQL에서 작동하는지 확인할 수 있어요:

![image](/assets/img/2024-05-18-DataEngineeringisforEngineersNOT_13.png)

첫 번째 SQL-to-Python-to-GCP 프로젝트와 작별하기 전에, 무료 크레딧을 소비하지 않도록 GCP 계정에서 데이터를 삭제해야 해요. 이는 클라우드 인스턴스, 함수 및 예약된 작업을 포함해요.

# 끝

<div class="content-ad"></div>

이 글을 쓰면서 SQL, Python 및 클라우드 컴퓨팅을 결합한 매우 기본적인 데이터베이스를 구축하는 방법을 배웠어요.

이 프로젝트를 시작할 때 구글 클라우드 플랫폼 또는 다른 클라우드 컴퓨팅 플랫폼에 대해 전혀 알지 몰랐어요. 서버, 호스트, 인스턴스 또는 소프트웨어 연결에 대해 아무것도 모르고 있었어요 (지금도요). SQL과 Python의 기본적인 지식만 있었어요.

이렇게 초보자로 시작했을 때, 자동화된 데이터베이스를 생성하고 주기적으로 가치 있는 데이터로 채우는 일이 너무 어려울 것이라고 회의적이었어요. 이제 내가 할 수 있다는 것을 알게 되었어요!

앞으로의 계획은 다른 API에서 데이터를 가져와 GCP 기능에 결합하여 더 완전하고 유용한 최종 제품을 만들고 싶어해요.

<div class="content-ad"></div>

글을 쓰는 사람이라고 하더라도 데이터 엔지니어링을 소프트웨어 엔지니어만큼 잘 할 수는 없을 지도 모르겠어요. 하지만 우리는 기초를 배우고 거기서부터 성장할 수 있죠.

# Stackademic 🎓

끝까지 읽어주셔서 감사합니다. 떠나시기 전에:

- 저자를 클랩하시고 팔로우해주시면 감사하겠습니다! 👏
- 우리를 팔로우해주세요 X | LinkedIn | YouTube | Discord
- 다른 플랫폼에서도 만나보세요: In Plain English | CoFeed | Venture | Cubed
- 더 많은 콘텐츠는 Stackademic.com에서 확인하세요