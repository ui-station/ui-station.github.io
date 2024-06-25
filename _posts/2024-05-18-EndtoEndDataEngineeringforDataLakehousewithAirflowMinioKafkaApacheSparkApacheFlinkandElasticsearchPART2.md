---
title: "데이터 레이크하우스를 위한 엔드 투 엔드 데이터 엔지니어링 Airflow, Minio, Kafka, Apache Spark, Apache Flink, 그리고 Elasticsearch  PART 2"
description: ""
coverImage: "/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_0.png"
date: 2024-05-18 16:15
ogImage:
  url: /assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_0.png
tag: Tech
originalTitle: "End to End Data Engineering for Data Lakehouse with Airflow, Minio, Kafka, Apache Spark, Apache Flink, and Elasticsearch — PART 2"
link: "https://medium.com/stackademic/end-to-end-data-engineering-for-data-lakehouse-with-airflow-minio-kafka-apache-spark-apache-f30065f81683"
---

<img src="/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_0.png" />

지난 섹션에서는 데이터 레이크하우스에 대한 중요한 세부 정보, 즉 무엇, 어떻게, 왜 현대 데이터 엔지니어링에서 중요한지에 대해 논의했습니다. 놓치셨다면 여기에서 확인할 수 있어요!

이번 섹션에서는 이번 시리즈 전체를 통해 작업할 시스템의 아키텍처에 대해 논의할 예정입니다.

AWS를 사례로 현대 데이터 엔지니어링과 클라우드를 활용한 경우, 몇 가지 액터가 S3 버킷에 데이터를 넣어 처리를 시작하고 복잡한 분석을 개발할 수 있습니다. 그러나 이 인프라를 계속 사용할수록 상황이 약간 모호해질 수 있고 비용 문제가 커질 수 있습니다.

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

이 아키텍처를 좀 더 자세히 살펴봐요; Airflow, Python, Java 또는 파일 업로드와 같은 몇 가지 액터들이 데이터를 Bronze 레이어/저장소에 저장해요. S3 버킷에 데이터를 업로드하는 것은 본질적으로 무료이기 때문에 이는 비용이 전혀 들지 않아요.

![architecture](/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_1.png)

비디오 워크스루에 관심 있는 분들은 여기를 따라 갈 수 있어요:

더 흥미로운 점은 이러한 서비스 위에 구축된 추가 서비스들의 사용이에요. AWS Glue Crawler와 같은 서비스가 여기에 속하며, 데이터 파일을 구조화된 데이터베이스와 테이블로 변환하는 데 사용돼요.

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

크롤러를 만든 후에는 두 가지 주요 옵션이 있습니다. 특정 간격에서 크롤러를 실행하도록 예약하거나 수동으로 계속해서 트리거하는 것입니다.

AWS 문서에 따르면 AWS Glue 작업의 최소 예약 시간은 5분이며, 이는 더 낮은 대기 시간 처리 요구사항이 있을 경우 도착 시 목적이 무산되는 것을 의미합니다. 안타깝지만!

![이미지](/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_2.png)

그러나 기다려보세요, 탈출할 수 있는 방법이 있습니다...맞습니다, AWS Lambda입니다! Lambda는 크롤러를 트리거하거나 처리를 자체적으로 처리함으로써 도움을 주는 다양한 기능을 제공할 수 있으며, 이로 인해 아래 아키텍처로 이어집니다:

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

<img src="/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_3.png" />

음... 이 문제를 해결하면서 또 다른 흥미로운 비용 측면이 발생했네요! 람다 및 첫 해 동안의 무료 티어(첫 해에 대한 요금 면제)에서 사용된 컴퓨팅 시간에 대해 요금이 부과되지만, 함수에 대한 첫 1백만 요청은 무료이며 그 이후 요청에 대해서는 요청 당 0.0000002달러가 청구됩니다. 이 가격은 여러 함수가 여러 버킷에 대해 실시간 변환을 수행하고 있을 때는 이상적으로 보이지만, 그런 경우에는 그렇게 저렴해 보이지 않습니다.

데이터 레이크하우스 아키텍처에 대해 자세히 이야기하기 전에 현재 구현이 실제로 어떻게 보이는지 살펴보겠습니다.

# S3 버킷 설정

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

AWS 계정이 있으면 검색 메뉴에서 S3를 검색하고 선택한 지역에 S3 버킷을 만들면 됩니다. 저는 버킷 이름으로 codewithyu를 선택했습니다.

![image](/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_4.png)

생성 후에는 버킷에 파일을 업로드하고 Glue Crawler를 생성하여 파일을 처리할 수 있습니다. 버킷에 taxi_project라는 이름의 폴더를 만들어 파일을 업로드할 것입니다.

![image](/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_5.png)

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

# AWS Glue을 사용하여 데이터베이스 및 테이블 생성하기

택시 프로젝트에서 파일 업로드를 완료하면 다음 단계는 AWS Glue로 이동하여 새 크롤러를 추가하는 것입니다.

![image](/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_6.png)

해야 할 일은 크롤러에 이름을 지정하고 데이터 원본을 선택하고 출력을 데이터베이스로 설정하는 것뿐입니다 (데이터베이스가 없는 경우 생성할 수 있는 옵션이 있습니다). 그럼 테이블이 자동으로 채워질 것입니다.

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

![image](/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_7.png)

![image](/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_8.png)

![image](/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_9.png)

Finally, here are the full properties of the crawler:

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

![screenshot 1](/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_10.png)

스크린샷에서 볼 수 있듯이, 출력 및 스케줄링에서는 출력이 "On Demand"로 설정되어 있습니다. 특정 간격에서 작업을 실행하려는 경우 CRON 표현식을 사용해야 합니다 (자세한 내용은 crontab.guru를 방문하세요).

이 단계가 완료되면 다음으로 크롤러를 실행해야 합니다.

![screenshot 2](/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_11.png)

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

# AWS Lambda 통합

크롤러를 성공적으로 만들고 완료까지 성공적으로 실행했으므로, 다음 단계는 버킷에 새 파일이 업로드될 때 크롤러를 트리거하는 것입니다. 이를 달성하는 두 가지 방법이 있습니다. 하나는 AWS Lambda 자체를 통해이를 수행하는 것이고, 다른 하나는 S3를 통해 수행하는 것입니다.

## AWS Lambda 통합 — Lambda

Lambda를 통해 이를 통합하려면 Lambda 서비스로 이동하고 새 함수를 생성해야 합니다.

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

아래와 같이 표 태그를 마크다운 형식으로 변경해주세요.

<img src="/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_12.png" />

이 작업을 완료하려면 함수를 생성한 후 해당 람다 함수에 부여해야 할 권한을 추가해야 합니다.

<img src="/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_13.png" />

권한을 부여하려면 설정 탭으로 이동하여 필요한 권한을 선택하실 수 있습니다.

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

![image1](/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_14.png)

In my case, I have added `AmazonS3FullAccess` and `AWSGlueConsoleFullAccess` permissions to the role attached to the function.

![image2](/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_15.png)

## Writing the Lambda Function Code

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

체크리스트가 거의 완성되어서, 다음 단계는 S3 버킷에서 PUT 이벤트가 발생할 때 트리거될 람다 코드를 작성하는 것입니다.

우리의 경우, 복잡한 변환 작업을 수행하지 않고 감지된 이벤트마다 크롤러를 실행하는 것이 필요합니다.

```js
import json
import boto3

# AWS 클라이언트 초기화
glue = boto3.client('glue')
s3 = boto3.client('s3')

def lambda_handler(event, context):
    # S3 이벤트에서 버킷 이름과 파일 키 가져오기
    bucket_name = event['Records'][0]['s3']['bucket']['name']
    file_key = event['Records'][0]['s3']['object']['key']

    # Glue 크롤러 이름 지정
    crawler_name = 'taxi_crawler'

    # Glue 크롤러 시작
    try:
        response = glue.start_crawler(Name=crawler_name)
        print(f'Glue 크롤러 {crawler_name}이(가) 성공적으로 시작되었습니다.')
    except Exception as e:
        print(f'Glue 크롤러 {crawler_name} 시작 중 오류 발생: {e}')
        raise e
```

이 함수를 Test 및 새 이벤트 생성을 선택하여 테스트할 수 있습니다. 템플릿에서 S3 아래 putTestEvent를 선택하면 다음과 같은 템플릿이 나타납니다. 버킷 이름 및 폴더를 나의 S3 버킷 및 폴더에 연결하도록 이름을 업데이트했습니다.

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

```json
{
  "Records": [
    {
      "eventVersion": "2.0",
      "eventSource": "aws:s3",
      "awsRegion": "us-east-1",
      "eventTime": "1970-01-01T00:00:00.000Z",
      "eventName": "ObjectCreated:Put",
      "userIdentity": {
        "principalId": "EXAMPLE"
      },
      "requestParameters": {
        "sourceIPAddress": "127.0.0.1"
      },
      "responseElements": {
        "x-amz-request-id": "EXAMPLE123456789",
        "x-amz-id-2": "EXAMPLE123/5678abcdefghijklambdaisawesome/mnopqrstuvwxyzABCDEFGH"
      },
      "s3": {
        "s3SchemaVersion": "1.0",
        "configurationId": "testConfigRule",
        "bucket": {
          "name": "codewithyu",
          "ownerIdentity": {
            "principalId": "EXAMPLE"
          },
          "arn": "arn:aws:s3:::codewithyu"
        },
        "object": {
          "key": "taxi_test/filename.parquet",
          "size": 1024,
          "eTag": "0123456789abcdef0123456789abcdef",
          "sequencer": "0A1B2C3D4E5F678901"
        }
      }
    }
  ]
}
```

작업을 완료한 후에는 호출을 선택하고 크롤러가 실행 중인지 확인하세요. 이제 크롤러가 실행되기 시작했을 것으로 예상됩니다.

## S3와 Lambda 연결

Lambda를 S3에 연결하려면 Configuration 및 Trigger를 클릭하고 Add Trigger를 선택하여 새 트리거를 추가할 수 있습니다. taxi_project/ 접두사를 추가하는 것을 잊지 마세요.

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

![Image 1](/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_16.png)

Once done, click Add and you're all set!

![Image 2](/assets/img/2024-05-18-EndtoEndDataEngineeringforDataLakehousewithAirflowMinioKafkaApacheSparkApacheFlinkandElasticsearchPART2_17.png)

Once a file is uploaded to the taxi_project folder, the taxi_crawler should be automatically triggered.

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

다음 기사에서는 비용 효율적인 새로운 데이터 레이크하우스 아키텍처를 살펴볼 것입니다.

# 그리고 마지막으로!

아래 주제 중에 관심 있는 주제가 있다면:

- Python
- 데이터 엔지니어링
- 데이터 분석
- 데이터 과학
- SQL
- 클라우드 플랫폼 (AWS/GCP/Azure)
- 기계 학습
- 인공 지능

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

모든 플랫폼에서 제 프로필을 좋아하고 팔로우해 주세요:

- Github: airscholar
- Twitter: @YusufOGaniyu
- LinkedIn: Yusuf Ganiyu
- Youtube: CodeWithYu
- Medium: Yusuf Ganiyu

LinkedIn, X, Medium 및 YouTube에서 매일 컨텐츠를 정기적으로 공유하고 있어요.

데이터 엔지니어링 여정을 가속화하려면 Data Mastery Lab를 꼭 확인해 주세요!

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

안녕하세요! 제 미디엄 팔로우를 통해 저와 소통해주세요:

# Stackademic 🎓

끝까지 읽어주셔서 감사합니다. 떠나시기 전에:

- 좋아요도 눌러주시고 작가를 팔로우해주세요! 👏
- 다음 링크를 통해 팔로우할 수 있습니다: X | LinkedIn | YouTube | Discord
- 다른 플랫폼도 방문해보세요: In Plain English | CoFeed | Venture | Cubed
- 알고리즘 콘텐츠를 다루는 블로깅 플랫폼에 지쳤나요? Differ를 시도해보세요
- Stackademic.com에서 더 많은 콘텐츠를 확인할 수 있습니다
