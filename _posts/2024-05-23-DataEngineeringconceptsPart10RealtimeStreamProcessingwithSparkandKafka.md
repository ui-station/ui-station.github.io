---
title: "데이터 엔지니어링 개념 제10부, 스파크와 카프카를 활용한 실시간 스트림 처리"
description: ""
coverImage: "/assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_0.png"
date: 2024-05-23 14:05
ogImage:
  url: /assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_0.png
tag: Tech
originalTitle: "Data Engineering concepts: Part 10, Real time Stream Processing with Spark and Kafka"
link: "https://medium.com/@mudrapatel17/data-engineering-concepts-part-10-stream-processing-with-spark-and-kafka-42a69fd23f0c"
---

이것은 데이터 엔지니어링 개념 10부작 시리즈의 마지막 부분입니다. 이번에는 스트림 처리에 대해 이야기할 것입니다.

목차:

1. 스트림 처리란
2. 카프카의 특징
3. 카프카 구성
4. 카프카 서비스 — 카프카 스트림, ksqlDB, 스키마 레지스트리
5. 스파크 구조화 스트리밍 API
6. 데이타브릭스 델타 레이크
7. 실전 프로젝트

이전 데이터 보안 부분으로 이동하는 링크입니다:

## 스트림 처리란?

<div class="content-ad"></div>

일괄 처리는 데이터 처리를 일정한 간격으로, 한 번에 대량의 데이터를 처리하는 데 사용되며 즉각적인 통찰력이 필요하지 않은 경우에 사용됩니다. 한편, 스트림 처리는 이 기사에서 논의할 다른 시나리오에 적합할 것입니다.

![데이터 엔지니어링 개념 시리즈 10부: Spark와 Kafka를 이용한 실시간 스트림 처리](/assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_0.png)

스트림 처리는 사기 탐지, IoT 센서 모니터링, 실시간 맞춤형 추천, 교통 데이터 분석을 통한 혼잡 감지 및 교통 흐름 최적화와 같은 사용 사례에 필수적입니다. 이러한 작업들은 신속한 응답, 실용성 및 자원 이용 효율성을 필요로 합니다. 그러나 이러한 시스템을 구현하는 것은 높은 지연 환경에서 기대되는 데이터 무결성, 보안 및 장애 허용성 때문에 복잡할 수 있습니다. 또한 항상 켜져 있는 하드웨어 때문에 확장/세밀 조정 및 모니터링이 비싸게 들 수 있습니다. 그러므로 최고 수준의 신뢰할 수 있는 인프라 및 저 레이턴시를 유지할 수 있도록 잘 분할된 데이터베이스가 필요합니다.

카프카는 스트리밍 기능을 제공하는 가장 널리 사용되는 도구 중 하나이며, 여기에서 자세히 알아보겠습니다.

<div class="content-ad"></div>

## 카프카 특징

- 견고함 — 한 대의 브로커(서버)가 다운되어도 데이터 손실을 방지하기 위해 복제됨.
- 유연성 — AVRO, JSON과 같은 다양한 데이터 형식을 처리할 수 있으며, 다양한 크기와 생산자 및 소비자 수가 다른 토픽을 가질 수 있음.
- 확장성 — 데이터 흐름이 증가함에 따라 성장함. 이 기능을 용이하게 하기 위해 다음 기술들이 구현됨:

  - 파티셔닝: 병렬 처리를 위해 토픽을 추가로 파티션(샤드)으로 분할할 수 있음. 사람들을 다른 섹션으로 나누어 꽉 차지 않게하고, 모든 사람이 음악을 들을 수 있도록 하는 것과 같다고 생각해보세요.
  - 수평 확장: 클러스터에 더 많은 브로커를 추가하여 증가하는 데이터 양을 처리할 수 있음. 관객이 증가함에 따라 콘서트 장소에 더 많은 스피커를 추가한다고 상상해보세요.

## 카프카 구성

<div class="content-ad"></div>

카프카 구성은 모든 카프카 클러스터의 속성 및 동작 설정을 지원하여 성능, 신뢰성 및 확장성을 향상시킵니다.

- 파티션 — 토픽을 병렬 처리하기 위해 여러 파티션으로 나눌 수 있습니다. 각 파티션은 변경할 수 없는 메시지의 순서가 지정된 세트입니다.
- 복제 — 여러 브로커 노드에 걸쳐 Kafka 토픽 파티션의 복제 횟수를 제공하여 장애 허용성을 제공합니다.
- 유지 기간 — 보관할 로그 세그먼트 파일의 최대 크기 또는 메시지 보관 기간 시간을 나타냅니다.
- 자동 오프셋 재설정 — 파티션에서 읽기 위해 오프셋은 시작점으로 제공되며, 자동 오프셋이 처음일 경우 시작부터 모든 메시지를 읽고, 최신으로 설정되어 있으면 최신 메시지만 읽습니다.
- ack — ack 구성은 생산자가 메시지를 수신한 후 소비자로부터 확인을 받는지 여부를 결정합니다. acks=0은 확인 없음, ack=1은 리더가 확인을 보내고, ack=all은 파티션의 모든 복제본이 확인을 보내는 것을 의미합니다.

## 다른 카프카 서비스

Kafka Streams — 이는 스트리밍 애플리케이션을 작성하는 데 사용되는 Java 라이브러리입니다. 선언적 언어 형식을 사용하여 스트리밍 처리 논리를 작성할 수 있는 API로, 다양한 기능을 포함하여 부가 코드를 피하고 낮은 대기 시간, 탄력성 및 암호화 기능을 제공합니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_1.png" />

ksqlDB — ksqlDB은 스트림 데이터를 처리하고 SQL 쿼리를 통해 상호 작용하는 데 특별히 설계된 데이터베이스로, 필터링, 집계 및 다른 토픽을 결합하는 기능을 포함합니다. ksqlDB에서 사용되는 두 가지 주요 구조는 변경할 수 없는 append only 이벤트의 이전 이벤트의 컬렉션인 스트림과 데이터 스트림의 현재 상태를 나타내는 데이터의 불변한 스냅샷인 테이블입니다.

<img src="/assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_2.png" />

스키마 레지스트리 — 스키마 레지스트리는 생산자와 소비자 서비스가 준수해야 하는 사용자가 정의한 스키마의 중앙 저장소입니다. 버전 관리, 데이터 유효성 검사, 데이터 손실 또는 손상 위험 감소 및 호환성 검사와 같은 기능을 제공합니다.

<div class="content-ad"></div>

![이미지](/assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_3.png)

## Spark Structured Streaming API

Spark Structured Streaming은 Spark SQL 엔진을 기반으로하며, 이 스트리밍 모델은 DataFrame/Dataset API를 기반으로합니다. 입력 데이터 스트림은 미리 정의된 간격으로 흡수되며 사용자가 정의한 쿼리의 결과는 무한한 테이블에서 업데이트(증분)됩니다. 출력 모드(append, complete, update)는 사용자가 구현하고자 하는 사용 사례에 따라 정의할 수 있습니다.

![이미지](/assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_4.png)

<div class="content-ad"></div>

따라서, 이 접두어 무결성(어떤 시점에서든 응용 프로그램의 출력 = 데이터의 접두어 일괄 작업)은 일관성(출력물은 항상 접두어의 결과이며 순서대로 처리됨), 장애 내성(시스템이 실패해도 결과 상태를 유지) 및 스트림 데이터의 순서를 유지하는 여러 이점이 있습니다.

또한, 이는 다른 스트리밍 시스템과 비교했을 때 다음과 같이 나타납니다:

![이미지](/assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_5.png)

## Databricks Delta Lake

<div class="content-ad"></div>

Databricks는 스파크의 창조자들에의해 설계된 클라우드 기반 플랫폼으로, 생산 등급 솔루션을 구축, 배포, 공유 및 유지하는 데 사용되는 통합 데이터 분석 처리 매체로 Spark 생태계의 모든 기능과 비즈니스 인텔리전스, 머신러닝 및 AI를 위한 추가 리소스가 구비되어 있습니다. Databricks의 모든 하위 시스템의 기본 저장 형식은 Delta Lake입니다.

Delta Lake 테이블은 Databricks에 구현된 Delta Lakehouse 아키텍쳐의 기반입니다. 이를 통해 일괄 및 스트리밍 데이터의 병렬 처리가 공유 파일 저장소에서 가능하며, delta lake는 원시 형식에서 구조화된 형식으로 데이터의 연속적인 흐름을 가능하게 하며, 들어오는 신선한 데이터와 함께 작업할 수 있도록 분석 및 ML 응용프로그램을 지원합니다.

스키마 강제 및 데이터 버전 관리를 제공하여 유효성 검사를 지원하고 재사용성 및 감사 기능을 제공합니다. 더불어, Spark 구조화 스트리밍 API와 웰 매칭하여 규모 있는 점진적 처리를 가능하도록 최적화되었습니다.

<img src="/assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_6.png" />

<div class="content-ad"></div>

## 실시간 스트리밍 아키텍처

카프카, 스파크 및 델타 레이크와 같은 스트리밍 기술을 활용하여 실시간 스트리밍 플랫폼을 구축할 예정입니다.

문제 정의: 실시간 환경 이슈 보고서를 수집하고, 변환 및 분석해서 심각성 수준 및 위치 기준에 따라 관련 환경 당국이 사용할 수 있는 데이터를 만드는 응용 프로그램을 개발 중입니다. 중복 보고서를 필터링하고 트렌드를 파악하기 위해 역사적 분석도 수행할 수 있습니다.

<div class="content-ad"></div>

이 아키텍처를 구현하려면 다음 단계를 따를 것입니다:

- 단계 1: Confluent Cloud 계정 및 Kafka 클러스터 생성
- 단계 2: 토픽 생성
- 단계 3: 클러스터 API 키 쌍 생성

위 3 단계를 구현하기 위해 아래 링크를 사용하십시오-

- 단계 4: 환경 보고서를 생성하는 Streamlit 웹 앱을 만듭니다.

<div class="content-ad"></div>


![Image](/assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_8.png)

Use the following link to set up a Python client in streamlit app to push the incidents to the Kafka topics:

- Step 5: Go to the Confluent Cloud dashboard and verify if the topics are being populated

![Image](/assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_9.png)


<div class="content-ad"></div>

- 단계 6: Databricks Community Edition 계정 및 컴퓨팅 인스턴스 생성

- 단계 7: Kafka 토픽에서 스트림 읽기

```js
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("Environmental Reporting").getOrCreate()

kafkaDF = spark \
    .readStream \
    .format("kafka") \
    .option("kafka.bootstrap.servers", "abcd.us-west4.gcp.confluent.cloud:9092") \
    .option("subscribe", "illegal_dumping") \
    .option("startingOffsets", "earliest") \
    .option("kafka.security.protocol","SASL_SSL") \
    .option("kafka.sasl.mechanism", "PLAIN") \
    .option("kafka.sasl.jaas.config", """kafkashaded.org.apache.kafka.common.security.plain.PlainLoginModule required username="" password="";""") \
    .load()

processedDF = kafkaDF.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")

display(processedDF)
```

- 단계 8: 변환 적용 — 처리된 데이터프레임에서 데이터를 가져 오기 위해 SQL 쿼리 작성

<div class="content-ad"></div>

```js
import pyspark.sql.functions as F
from  pyspark.sql.functions import col, struct, to_json
from pyspark.sql.types import StructField, StructType, StringType, MapType

json_schema = StructType(
  [
    StructField("incident_type", StringType(), nullable = False),
    StructField("location", StringType(), nullable = False),
    StructField("description", StringType(), nullable = True),
    StructField("contact", StringType(), nullable = True)
  ]
)

# Using Spark SQL to write queries on the streaming data in processedDF

query = processedDF.withColumn('value', F.from_json(F.col('value').cast('string'), json_schema))  \
      .select(F.col("value.incident_type"),F.col("value.location"))
display(query)
```

We will now add another column called Region which would be mapped from the Location column, helping the authorities assign the issues to appropriate regional centres.

Define a UDF(User Defined Function) to find out the region from the location:

```js
from pyspark.sql.functions import udf
from pyspark.sql.types import StringType

# Define the regions_to_states dictionary
regions_to_states = {
    'South': ['West Virginia', 'District of Columbia', 'Maryland', 'Virginia',
              'Kentucky', 'Tennessee', 'North Carolina', 'Mississippi',
              'Arkansas', 'Louisiana', 'Alabama', 'Georgia', 'South Carolina',
              'Florida', 'Delaware'],
    'Southwest': ['Arizona', 'New Mexico', 'Oklahoma', 'Texas'],
    'West': ['Washington', 'Oregon', 'California', 'Nevada', 'Idaho', 'Montana',
             'Wyoming', 'Utah', 'Colorado', 'Alaska', 'Hawaii'],
    'Midwest': ['North Dakota', 'South Dakota', 'Nebraska', 'Kansas', 'Minnesota',
                'Iowa', 'Missouri', 'Wisconsin', 'Illinois', 'Michigan', 'Indiana',
                'Ohio'],
    'Northeast': ['Maine', 'Vermont', 'New York', 'New Hampshire', 'Massachusetts',
                  'Rhode Island', 'Connecticut', 'New Jersey', 'Pennsylvania']
}

#from geotext import GeoText
from geopy.geocoders import Nominatim

# Define a function to extract state names from location text
def extract_state(location_text):
    geolocator = Nominatim(user_agent="my_application")
    location = geolocator.geocode(location_text)
    #print(location)
    #print(type(location.raw))
    if location:
        state = location.raw['display_name'].split(',')[-2]
        return state
    else:
        return "Unknown"

# Create a UDF to map states to regions
@udf(StringType())
def map_state_to_region(location):
    state = extract_state(location).strip()
    for region, states in regions_to_states.items():
        if state in states:
            return region
    return "Unknown"  # Return "Unknown" for states not found in the dictionary

# Apply the UDF to map states to regions
df_with_region = query.withColumn("region", map_state_to_region(query["location"]))

display(df_with_region)
```


<div class="content-ad"></div>

- 단계 9: 분석 수행 — 우리는 Description의 감정을 분석하여 사건 심각도를 나타내는 다른 열을 추가합니다. 이는 당국이 노력을 우선순위로 정하는 데 도움이 될 것입니다.

```js
from pyspark.sql.functions import udf
from pyspark.sql.types import StringType
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer

# VADER 감정 분석기 초기화
analyzer = SentimentIntensityAnalyzer()

# Description 텍스트에 대한 감정 분석을 수행하는 함수 정의
def analyze_sentiment(description):
    # VADER에서 compound 감정 점수 가져오기
    sentiment_score = analyzer.polarity_scores(description)['neg']

    # 감정 점수를 기반으로 심각도 분류
    if sentiment_score >= 0.4:
        return "High"
    elif sentiment_score >= 0.2 and sentiment_score < 0.4:
        return "Medium"
    else:
        return "Low"

# 감정 분석을 위한 UDF 생성
sentiment_udf = udf(analyze_sentiment, StringType())

# 처리된 DataFrame(processedDF)의 description 열에 UDF 적용
# 실제 DataFrame 및 열 이름으로 "processedDF" 및 "description_column"을 대체합니다.
processedDF_with_severity = query.withColumn("severity", sentiment_udf("description"))

# 추가된 심각도 열이 있는 DataFrame 표시
display(processedDF_with_severity)
```

환경 위험 데이터로 훈련된 ML 분류 모델을 사용하거나 심각도 수준을 식별하기 위해 LLMs를 사용함으로써 심각도 UDF가 크게 개선될 수 있습니다. 그러나 간단함을 위해 여기에서는 vaderSentiment 분석기를 사용한 감정 분석을 사용했습니다.

데이터에 대한 다양한 통계 분석을 수행할 수도 있습니다:

<div class="content-ad"></div>

![Real-time Stream Processing](/assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_10.png)

위와 같이 위치별로 그룹화하고 심각도를 기준으로 정렬하여 해당 카운트의 빈도를 얻을 수도 있습니다:

또한 임시 뷰를 생성하고 해당 뷰에서 SQL 쿼리를 수행할 수도 있습니다:

```js
# 스트리밍 DataFrame을 임시 뷰로 등록
processedDF_with_severity.createOrReplaceTempView("incident_reports")

# 집계를 위한 SQL 쿼리 정의
total_incidents_query = """
    SELECT
        incident_type,
        COUNT(*) AS total_incidents
    FROM
        incident_reports
    GROUP BY
        incident_type
"""

severity_incidents_query = """
    SELECT
        incident_type,
        severity,
        COUNT(*) AS severity_incidents
    FROM
        incident_reports
    GROUP BY
        incident_type, severity
"""

# 집계 수행
total_incidents_df = spark.sql(total_incidents_query)
severity_incidents_df = spark.sql(severity_incidents_query)

```

<div class="content-ad"></div>



![Data Engineering Concepts](/assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_11.png)

Spark 구조화된 스트리밍 분석에 대한 일부 제한 사항:

1. 비 시간 열에 대한 윈도우 함수를 수행할 수 없습니다.
2. 전체 출력 모드에서 조인을 수행할 수 없습니다. 추가 모드에서만 가능합니다.

- 단계 10: 델타 테이블을 만들고 분석 결과를 해당 테이블에 저장합니다

```js
# Delta Lake에 스트리밍 데이터를 저장할 경로 정의
delta_table_path = "`result_delta_table`"

# 스트리밍 쿼리에 대한 체크포인트 위치 설정
checkpoint_location = "/FileStore/tables/checkpoints"

# 체크포인트 및 트리거를 사용하여 스트리밍 쿼리 시작
streaming_query = processedDF_with_severity.writeStream\
  .outputMode("append")\
  .option("checkpointLocation", checkpoint_location)\
  .trigger(processingTime='10 seconds')\ # 10초ごとに 데이터의 미크로배치를 처리할 트리거 정의
  .format("delta")\
  .toTable(delta_table_path)
```



<div class="content-ad"></div>

그러면 Delta 테이블에서 데이터프레임으로 스트림을 읽을 수 있습니다:

![Delta Table as Dataframe](/assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_12.png)

또는 Delta 테이블을 쿼리하기 위해 SQL을 사용할 수도 있습니다:

![Query Delta Table with SQL](/assets/img/2024-05-23-DataEngineeringconceptsPart10RealtimeStreamProcessingwithSparkandKafka_13.png)

<div class="content-ad"></div>

읽어 주셔서 감사합니다 :) 실시간 스트리밍 데이터 아키텍처에 대한 빠른 통찰이 마음에 들었으면 좋겠네요. 데이터 엔지니어링 모험을 위해 행운을 빕니다! 여기 GitHub 저장소 링크입니다:

