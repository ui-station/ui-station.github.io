---
title: "모두 처리해야 해요  DuckDB가 데이터를 마스터하는 방법을 포켓몬 트레이너처럼 소개합니다"
description: ""
coverImage: "/assets/img/2024-05-20-GottaprocessemallDuckDBmastersyourdatalikeaPokmontrainer_0.png"
date: 2024-05-20 18:56
ogImage: 
  url: /assets/img/2024-05-20-GottaprocessemallDuckDBmastersyourdatalikeaPokmontrainer_0.png
tag: Tech
originalTitle: "Gotta process ’em all — DuckDB masters your data like a Pokémon trainer"
link: "https://medium.com/data-engineer-things/gotta-process-em-all-duckdb-masters-your-data-like-a-pok%C3%A9mon-trainer-051a38cbc5cd"
---


## DuckDB을 데이터 처리 도구로 사용하는 실용적인 예제

- 소개
- API 개요
  - 포켓몬 가져오기
  - 포켓몬 세부 정보 가져오기
- 프로젝트 설정
- 데모 1: 간단한 선택
- 데모 2: API에서 JSON 읽기
- 데모 3: Unnest
- 데모 4: 세부 정보 가져오는 UDF
- 데모 5: 목록 표현식
- 데모 6: DuckDB를 Pandas로 변환하고 다시 변환하기
- 데모 7: 데이터 유지 및 로드
- 결론

# 소개

캉토 지방을 탐험하여 놀라운 생물을 포획하고 훈련하는 포켓몬 트레이너가 되는 꿈을 꾸어 본 적이 있나요? 90년대에 Game Boy에서 포켓몬 레드 버전과 포켓몬 블루 버전을 즐길 때, 제가 확실히 꾸었던 꿈입니다.

<div class="content-ad"></div>

포켓몬 비디오 게임 시리즈가 시간이 지남에 따라 얼마나 인기 있어졌고 지금도 그랬는지 정말 놀라운 일이에요. 마치 비디오 게임 산업에서 떠오르는 별처럼 말이죠. 그 얘기를 하자면: DuckDB, 진행 중인 SQL 분석 엔진, 지난 몇 년간 데이터 엔지니어링 커뮤니티에서 떠오르는 별처럼 인기를 끌었어요.

[이미지](/assets/img/2024-05-20-GottaprocessemallDuckDBmastersyourdatalikeaPokmontrainer_0.png)

마치 포켓몬 트레이너가 전투를 위해 완벽한 팀을 선택하는 것처럼, 경험이 풍부한 데이터 엔지니어는 작업에 적합한 올바른 도구를 선택하는 중요성을 알아요. 본 문서를 통해 DuckDB가 여러분의 도구 상자에 완벽한 도구가 될 이유를 보여드리고 싶어요.

DuckDB는 큰 데이터베이스와 매끄럽게 통합되어 인메모리 및 영속적 저장 솔루션 사이의 원활한 전환을 도와줘요. 마치 어떤 도전이든 쉽게 적응하는 다재다능한 포켓몬과 같죠. 작은 데이터 세트를 빠르게 분석해야 한다면? DuckDB는 여러분의 마초크, 쉽게 과업을 수행할 준비가 돼 있어요. 더 강력한 처리가 필요한 대규모 데이터 세트와 작업하고 있다면? DuckDB는 마샴으로 변신하여 과중한 분석을 위해 외부 데이터베이스에 매끄럽게 연결할 수 있어요.

<div class="content-ad"></div>

DuckDB를 설치하기 쉽고 휴대용이며 오픈 소스입니다. SQL 방언 측면에서 기능이 풍부하고 CSV, Parquet 및 JSON과 같은 다양한 형식을 기준으로 데이터를 가져오고 내보낼 수 있습니다. 또한 Pandas 데이터프레임과 원활하게 통합되어 데이터 조작 스크립트에서 강력한 데이터 조작 도구로 사용할 수 있습니다.

다음 장에서는 DuckDB를 사용하여 Pokémon API를 활용해 데이터를 처리하는 방법을 예시로 살펴보며 강력한 기능을 보여줄 것입니다.

![이미지](/assets/img/2024-05-20-GottaprocessemallDuckDBmastersyourdatalikeaPokmontrainer_1.png)

## API 개요

<div class="content-ad"></div>

포켓몬 API는 포켓몬 비디오 게임 시리즈와 관련된 JSON 데이터에 대한 RESTful API 인터페이스를 제공합니다. 이 API를 사용하면 포켓몬, 그들의 기술, 능력, 유형 등에 대한 정보를 소비할 수 있습니다.

이 기사는 주로 DuckDB 기능에 초점을 맞춰야 하므로 일부 엔드포인트만 사용할 것입니다. 개요를 얻기 위해 curl과 jq를 사용하여 관련 엔드포인트를 탐색해 보겠습니다. jq를 잘 모르는 경우, 이것은 터미널에서 JSON을 필터링, 수정 또는 간단히 형식화하는 많은 기능을 가진 경량 CLI JSON 프로세서입니다. macOS를 사용하고 Homebrew를 사용 중이라면 brew install jq를 통해 jq를 설치할 수 있습니다.

## 포켓몬 가져오기

```js
curl -s https://pokeapi.co/api/v2/pokemon/ | jq .
```

<div class="content-ad"></div>

```js
{
  "count": 1302,
  "next": "https://pokeapi.co/api/v2/pokemon/?offset=20&limit=20",
  "previous": null,
  "results": [
    {
      "name": "bulbasaur",
      "url": "https://pokeapi.co/api/v2/pokemon/1/"
    },
    {
      "name": "ivysaur",
      "url": "https://pokeapi.co/api/v2/pokemon/2/"
    },
    ...
```

![Gotta Process Em All: DuckDB masters your data like a Pokémon trainer](/assets/img/2024-05-20-GottaprocessemallDuckDBmastersyourdatalikeaPokmontrainer_2.png)

## 포켓몬 세부 정보 가져오기

```js
curl -s https://pokeapi.co/api/v2/pokemon/bulbasaur | jq .
```

<div class="content-ad"></div>

```json
{
  "abilities": [
    {
      "ability": {
        "name": "overgrow",
        "url": "https://pokeapi.co/api/v2/ability/65/"
      },
      "is_hidden": false,
      "slot": 1
    },
    ...
  ],
  "base_experience": 64,
  ...
```

![Screenshot](/assets/img/2024-05-20-GottaprocessemallDuckDBmastersyourdatalikeaPokmontrainer_3.png)

# 프로젝트 설정

새로운 Python 프로젝트를 만들기 시작합니다. 이를 위해 새 폴더를 만듭니다. 이 폴더 내에서 내장된 venv 모듈을 사용하여 가상 환경을 생성합니다:```

<div class="content-ad"></div>

```js
mkdir duckdb-pokemon
cd duckdb-pokemon
python -m venv .venv
source .venv/bin/activate
```

마지막 명령으로 가상 환경을 활성화했습니다. 이는 현재 터미널 세션에서는 시스템 전역 Python 대신 가상 Python을 사용한다는 의미입니다. 이것은 다음에 설치할 종속성을 프로젝트 내에서 격리시키려는 우리의 목적상 중요합니다. 다음 단계는 모든 요구 사항을 설치하는 것입니다:

```js
pip install duckdb
pip install pandas
pip install requests
```

여기까지 준비가 되었습니다. 다음 장에서는 포켓몬 API를 활용한 DuckDB의 일부 기능을 살펴볼 것입니다. 이 코드를 프로젝트 내의 Python 파일로 복사하여 실행할 수 있습니다.```

<div class="content-ad"></div>

# 데모 1: 간단한 선택

첫 번째 데모는 시작하는 방법이 얼마나 간단한지 보여줍니다. DuckDB를 pip install duckdb로 설치한 후에는 직접 가져와서 SQL 문을 실행할 수 있습니다. 복잡한 데이터베이스 설정이나 다른 요구 사항이 없습니다. 광고대로, 이것은 빠른 내장형 분석용 데이터베이스입니다.

```js
import duckdb

duckdb.sql("SELECT 42").show()
```

이 코드를 실행하면 예상한 출력인 하나의 열과 하나의 행이 포함된 값 42를 가진 테이블을 얻을 수 있습니다. 이는 삶, 우주, 그리고 모든 것에 대한 궁극적인 질문의 답입니다.

<div class="content-ad"></div>

```js
┌───────┐
│  42   │
│ int32 │
├───────┤
│    42 │
└───────┘
```

# 데모 2: API로부터 JSON 읽기

이제 상황이 급속하게 변화할 것이니, 포켓몬볼을 준비해두세요. 일반적으로 API에서 JSON 데이터를 가져올 때, requests.get을 시작으로 클라이언트 로직을 구현하여 데이터를 데이터베이스에로드하거나 Pandas와 같은 데이터 처리 프레임워크를 사용할 수 있습니다. 다음 예제는 당신을 확실히 놀라게 할 것입니다. 우리는 데이터를 가져와 SQL을 사용하여 직접 테이블로 만들 것입니다.

```js
import duckdb

duckdb.sql("""
    CREATE TABLE pokemon AS
    SELECT *
    FROM read_json_auto('https://pokeapi.co/api/v2/pokemon?limit=1000');

    SELECT * FROM pokemon;
""").show()
```

<div class="content-ad"></div>

read_json_auto를 사용하면 JSON 데이터를로드하는 가장 간단한 방법을 사용할 수 있습니다. 이는 JSON 리더를 자동으로 구성하고 데이터에서 열 유형을 파생합니다.

위의 코드는 API의 해당 응답에 따라 네 개의 열이 있는 테이블을 제공합니다. 즉: count, next, previous, results이며, results는 구조체의 목록이며 각 구조체는 이름과 URL이 포함된 포켓몬으로 구성됩니다.

유연성은 DuckDB가 정말 빛나는 곳입니다. DuckDB는 여러분의 신속한 피카츄이며 JSON 데이터를로드하는 것은 단지 한 가지 예입니다. CSV 파일이나 심지어 하이브 파티셔닝이 있는 Parquet 파일에서 데이터를 읽을 수도 있습니다: 

<div class="content-ad"></div>

```js
SELECT *
FROM read_parquet("some_table/*/*/*.parquet", hive_partitioning = true);
```

또는 Pandas 데이터프레임에서 데이터를 직접 읽어보세요:

```js
import pandas as pd
import duckdb

df = pd.DataFrame({"some_values" : [42, 7411, 4]})
print(duckdb.query("SELECT SUM(some_values) FROM df").to_df())
```

하지만 일단은 포켓몬으로 계속해 봅시다.```

<div class="content-ad"></div>

# 데모 3: Unnest

이전 예제에서 결과, 즉 실제 포켓몬은 모두 한 행과 열 안에 구조체 목록 형식으로 있습니다. 그러나 추가 처리를 위해 각 포켓몬 당 하나의 행을 원합니다. 이를 위해 unnest 함수를 사용할 수 있습니다.

```js
import duckdb

duckdb.sql("""
    CREATE TABLE pokemon AS
    SELECT unnest(results) AS pokemon
    FROM read_json_auto('https://pokeapi.co/api/v2/pokemon?limit=1000');

    SELECT
        pokemon.name,
        pokemon.url
    FROM pokemon;
""").show()
```

포켓몬 당 한 행씩 이름 및 URL이 포함된 결과를 얻을 수 있습니다.

<div class="content-ad"></div>

```js
┌──────────────┬─────────────────────────────────────────┐
│     name     │                   url                   │
│   varchar    │                 varchar                 │
├──────────────┼─────────────────────────────────────────┤
│ bulbasaur    │ https://pokeapi.co/api/v2/pokemon/1/    │
│ ivysaur      │ https://pokeapi.co/api/v2/pokemon/2/    │
│ venusaur     │ https://pokeapi.co/api/v2/pokemon/3/    │
│    ·         │                   ·                     │
│    ·         │                   ·                     │
│ baxcalibur   │ https://pokeapi.co/api/v2/pokemon/998/  │
│ gimmighoul   │ https://pokeapi.co/api/v2/pokemon/999/  │
│ gholdengo    │ https://pokeapi.co/api/v2/pokemon/1000/ │
├──────────────┴─────────────────────────────────────────┤
│ 1000 rows (20 shown)                         2 columns │
└────────────────────────────────────────────────────────┘
```

# 데모 4: 세부 정보 가져오기를 위한 UDF

피카츄를 라이츄로 진화시킬 수 있을까요? 물론 가능합니다! 이전 예제의 결과를 사용하면, url이라는 열을 가지고 있어 각 포켓몬에 대한 더 많은 세부 정보를 얻기 위해 요청해야 합니다. read_json_auto와 같은 함수는 테이블 수준 함수이기 때문에 각 행에 적용할 수 없습니다.

DuckDB는 Python과 완벽하게 통합되어 있어 열에 있는 각 url 값에 대해 간단히 Python 함수를 호출할 수 있는 방법이 있습니다. 이는 다른 데이터베이스 시스템에서 알 수 있는 사용자 정의 함수(UDF)와 유사합니다.```

<div class="content-ad"></div>

API로부터 데이터를 가져오기 위해 파이썬 함수를 정의해야 합니다:

```python
import requests

def get(url):
    return requests.get(url).text
```

그런 다음 DuckDB에 등록해야 합니다:

```python
from duckdb.typing import VARCHAR

duckdb.create_function("get", get, [VARCHAR], VARCHAR)
```

<div class="content-ad"></div>

create_function을 사용할 때는 SQL 스크립트에서 UDF에 참조할 이름을 지정해야 합니다. 또한 호출할 실제 Python 함수와 매개변수 유형 및 반환 유형 목록을 전달해야 합니다.

그런 다음 SQL에서 다음과 같이 사용할 수 있습니다:

```js
json(get(pokemon.url)) AS details
```

다시 말해, 우리는 포켓몬 목록에서 각 pokemon.url에 대해 Python 함수 get을 호출합니다. 우리가 얻는 것은 JSON 응답 텍스트이며, json 함수를 사용하여 적절한 유형의 결과를 얻습니다.

<div class="content-ad"></div>

다음은 스스로 시도해볼 수 있는 최종 코드입니다:

```js
import duckdb
import requests
from duckdb.typing import VARCHAR


def get(url):
    return requests.get(url).text


duckdb.create_function("get", get, [VARCHAR], VARCHAR)
duckdb.sql("""
    CREATE TABLE pokemon AS
    SELECT unnest(results) AS pokemon
    FROM read_json_auto('https://pokeapi.co/api/v2/pokemon?limit=10');

    WITH base AS (
        SELECT
            pokemon.name AS name,
            json(get(pokemon.url)) AS details
        FROM pokemon
    )
    SELECT *
    FROM base;
""").show()
```

각 포켓몬에 대해 API를 호출하므로 실행에 몇 초가 걸릴 수 있습니다. 결과는 이름과 모든 세부 정보가 JSON으로 표시된 테이블입니다.

<img src="/assets/img/2024-05-20-GottaprocessemallDuckDBmastersyourdatalikeaPokmontrainer_5.png" />

<div class="content-ad"></div>

# 데모 5: 리스트 컴프리헨션

파이썬의 리스트 컴프리헨션은 제가 가장 좋아하는 기능 중 하나에요. DuckDB에서도 리스트 컴프리헨션을 사용할 수 있어요! 우리의 목표는 세부 정보를 추가 처리하여 개별 열로 추출하는 것이에요. 하지만 모든 세부 정보가 아니라, 포켓몬의 ID, 이름, 키, 그리고 무게만 추출할 거에요.

또한 포켓몬이 사용할 수 있는 능력들을 능력 이름으로 이루어진 간단한 리스트로 줄이고 다른 열에 저장할 거에요.

SQL을 더 읽기 쉽게 만들기 위해, 우리는 공통 테이블 표현식 (CTE)도 사용할 거예요.

<div class="content-ad"></div>

```python
import duckdb
import requests
from duckdb.typing import VARCHAR

def get(url):
    return requests.get(url).text

duckdb.create_function("get", get, [VARCHAR], VARCHAR)
duckdb.sql("""
    CREATE TABLE pokemon AS
    SELECT unnest(results) AS pokemon
    FROM read_json_auto('https://pokeapi.co/api/v2/pokemon?limit=10');

    WITH base AS (
        SELECT
            pokemon.name AS name,
            json(get(pokemon.url)) AS details
        FROM pokemon
    ), pokemon_details AS (
        SELECT
            details.id,
            name,
            details.abilities::STRUCT(ability STRUCT(name VARCHAR, url VARCHAR), is_hidden BOOLEAN, slot INTEGER)[] AS abilities,
            details.height,
            details.weight
        FROM base
    )
    SELECT
        id,
        name,
        [x.ability.name FOR x IN abilities] AS abilities,
        height,
        weight
    FROM pokemon_details;
""").show()
```

CTE인 pokemon_details가 필요한 세부 정보를 추출합니다. 그러나 여기에는 한 가지 숨겨진 기능이 더 있습니다: 현재 details 열의 abilities가 JSON 유형이지만 리스트 표현식에는 실제 목록 유형이 필요합니다. 다음 문으로:

```python
details.abilities::STRUCT(ability STRUCT(name VARCHAR, url VARCHAR), is_hidden BOOLEAN, slot INTEGER)[] AS abilities,
```

abilities의 유형을 구조 목록으로 변환합니다. 각 구조에는 더 자세한 정보를 위해 능력 이름과 URL뿐만 아니라, 숨겨진 플래그와 슬롯 번호를 포함하는 또 다른 구조가 포함됩니다.


<div class="content-ad"></div>

지금 능력은 목록이므로 SQL에서 목록 표현을 적용할 수 있습니다. 이는 기본적으로 파이썬의 목록 표현과 동일하게 작동합니다. 따라서 다음 SQL 코드를 사용해보세요:

```js
[x.ability.name FOR x IN abilities] AS abilities,
```

이를 통해 각 포켓몬이 사용할 수 있는 각 능력의 이름만 포함된 새 목록을 만듭니다.

```js
| id   | name       | abilities               | height | weight |
|------|------------|-------------------------|--------|--------|
| 1    | bulbasaur  | [overgrow, chlorophyll] | 7      | 69     |
| 2    | ivysaur    | [overgrow, chlorophyll] | 10     | 130    |
| 3    | venusaur   | [overgrow, chlorophyll] | 20     | 1000   |
| 4    | charmander | [blaze, solar-power]    | 6      | 85     |
| 5    | charmeleon | [blaze, solar-power]    | 11     | 190    |
| 6    | charizard  | [blaze, solar-power]    | 17     | 905    |
| 7    | squirtle   | [torrent, rain-dish]    | 5      | 90     |
| 8    | wartortle  | [torrent, rain-dish]    | 10     | 225    |
| 9    | blastoise  | [torrent, rain-dish]    | 16     | 855    |
| 10   | caterpie   | [shield-dust, run-away] | 3      | 29     |
```

<div class="content-ad"></div>

# 데모 6: DuckDB에서 Pandas로 그리고 다시 되돌아 오기

지금쯤이면 DuckDB가 휴대용 분석 데이터베이스뿐만 아니라 다양한 데이터 조작 도구임이 분명해졌을 것입니다.

DuckDB의 핵심은 SQL 기반 작업과 Pandas와 같은 다른 데이터 처리 도구 간에 원활한 통합을 제공합니다. 이 독특한 기능은 데이터 처리 스크립트 내에서 서로 다른 기술 간에 쉽게 전환할 수 있도록 가능하게 합니다.

전혀 SQL에서 데이터 처리를 하거나 Pandas나 NumPy와 같은 전형적인 라이브러리를 사용하여 Python 스크립트 내에서 데이터 정제를 완전히 구현하는 대신, 복잡한 데이터베이스 통합 설정이 필요 없이 이러한 환경 간을 전환할 수 있습니다.

<div class="content-ad"></div>

지금까지의 예제를 기반으로, Pandas를 사용하여 추가 처리를 수행하고 싶다고 가정해 봅시다. DuckDB를 사용하면 SQL 쿼리의 결과를 데이터프레임으로 쉽게 내보낼 수 있습니다. .df() 함수를 사용하면 됩니다.

또한 DuckDB를 사용하면 Pandas 데이터프레임에서 직접 데이터를 쿼리할 수도 있습니다!

```python
import duckdb
import requests
from duckdb.typing import VARCHAR

def get(url):
    return requests.get(url).text

duckdb.create_function("get", get, [VARCHAR], VARCHAR)

# DuckDB에서 작업 시작
df = duckdb.sql("""
    CREATE TABLE pokemon AS
    SELECT unnest(results) AS pokemon
    FROM read_json_auto('https://pokeapi.co/api/v2/pokemon?limit=10');

    WITH base AS (
        SELECT
            pokemon.name AS name,
            json(get(pokemon.url)) AS details
        FROM pokemon
    ), pokemon_details AS (
        SELECT
            details.id,
            name,
            details.abilities::STRUCT(ability STRUCT(name VARCHAR, url VARCHAR), is_hidden BOOLEAN, slot INTEGER)[] AS abilities,
            details.height,
            details.weight
        FROM base
    )
    SELECT
        id,
        name,
        [x.ability.name FOR x IN abilities] AS abilities,
        height,
        weight
    FROM pokemon_details;
""").df()

# Pandas에서 데이터 처리 계속
df_agg = df.explode("abilities").groupby("abilities", as_index=False).agg(count=("id", "count"))

# DuckDB로 돌아가기
duckdb.sql("""
    SELECT abilities AS ability, count
    FROM df_agg
    ORDER BY count DESC
    LIMIT 8;
""").show()
```

위 코드는 다음 결과를 출력합니다:

<div class="content-ad"></div>

```js
┌─────────────┬───────┐
│   능력       │ 개수  │
│   varchar   │ int64 │
├─────────────┼───────┤
│ blaze       │     3 │
│ chlorophyll │     3 │
│ overgrow    │     3 │
│ rain-dish   │     3 │
│ solar-power │     3 │
│ torrent     │     3 │
│ run-away    │     1 │
│ shield-dust │     1 │
└─────────────┴───────┘
```

앞서 언급한 대로, .df()를 사용하면 SQL 결과를 Pandas 데이터프레임으로 얻을 수 있습니다. 그런 다음 다음과 같이 추가적인 변환을 적용할 수 있습니다.

```js
df_agg = df.explode("abilities").groupby("abilities", as_index=False).agg(count=("id", "count"))
```

그리고 df_agg 변수에 저장된 데이터프레임을 SQL에서 사용할 수 있습니다. 처음 보았을 때 정말 놀랐던 부분이죠.```

<div class="content-ad"></div>

```sql
SELECT abilities AS ability, count
FROM df_agg
ORDER BY count DESC
LIMIT 8;
```

데이터 처리 도구로 DuckDB가 좋은 도구인 이유는 어떤 접착 코드도 추가하지 않기 때문입니다.

# 데모 7: 데이터 유지 및 로드

매번 실행할 때마다 API를 호출하는 것은 가장 효율적인 해결책은 아닙니다. 물론 결과를 기다리는 동안 맛있는 커피 한 잔을 마실 수 있는 기회를 제공하지만, DuckDB는 데이터를 유지할 수도 있습니다. 예를 들어 데이터를 파일로 직렬화하여 파일 시스템에 저장할 수 있습니다.```

<div class="content-ad"></div>

위의 예제를 다음 코드와 함께 확장해 봅시다:

```js
# 판다스에서 데이터 처리 계속하기
df_agg = df.explode("abilities").groupby("abilities", as_index=False).agg(count=("id", "count"))

# 데이터 유지하기
with duckdb.connect(database="pokemon.db") as conn:
    conn.sql("""
        DROP TABLE IF EXISTS pokemon_abilities;
        CREATE TABLE pokemon_abilities AS
        SELECT abilities AS ability, count
        FROM df_agg
        ORDER BY count DESC;
    """)
```

우리는 pokemon.db라는 파일에 연결을 열고 데이터를 저장합니다.

<div class="content-ad"></div>

다른 스크립트에서 데이터를로드하고 사전 처리 된 데이터에 액세스 할 수 있습니다:

```js
import duckdb

# 데이터 로드
with duckdb.connect(database="pokemon.db") as conn:
    conn.sql("""
        SELECT * FROM pokemon_abilities;
    """).show()
```

<img src="/assets/img/2024-05-20-GottaprocessemallDuckDBmastersyourdatalikeaPokmontrainer_7.png" />

# 결론

<div class="content-ad"></div>

DuckDB은 데이터 처리 도전에서 강력한 동료로 나타났습니다. Pandas 데이터프레임과 고급 분석 SQL 기능과의 매끄러운 통합을 제공하여 자원 제한적 환경에서 분석 작업에 적합함을 강조했습니다.

[Github](https://github.com/vojay-dev/duckdb-pokemon)에서 모든 데모를 찾을 수 있어요! 🪄

DuckDB가 함께하면 금방 성공적인 포켓몬 트레이너가 될 거에요!

<img src="/assets/img/2024-05-20-GottaprocessemallDuckDBmastersyourdatalikeaPokmontrainer_8.png" />

<div class="content-ad"></div>

경험을 공유해주시고 즐거운 독서를 즐기세요!