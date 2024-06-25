---
title: "실시간 데이터 파이프라인 구축하기"
description: ""
coverImage: "/assets/img/2024-05-20-BuildingaReal-TimeDataPipeline_0.png"
date: 2024-05-20 18:45
ogImage:
  url: /assets/img/2024-05-20-BuildingaReal-TimeDataPipeline_0.png
tag: Tech
originalTitle: "Building a Real-Time Data Pipeline"
link: "https://medium.com/@nydas/building-a-real-time-data-pipeline-5eff6c6d8a3c"
---

## 카프카(Kafka), 폴라스(Polars), 델타 레이크(Delta Lake)를 활용한 실시간 분석

데이터 엔지니어라면 언젠가는 실시간 데이터에 대한 비즈니스 요구사항을 직면해야 할 것입니다. 이는 빨리 오겠다는 것이 확실합니다. 실시간 데이터 처리는 기업이 신속하게 정보 기반 결정을 내릴 수 있도록 필수적으로 중요해지고 있으며, 아파치 카프카는 확장 가능하고 오류 허용성 있는 실시간 데이터 파이프라인을 구축하기 위한 인기 있는 플랫폼으로 부상했습니다.

이 기사에서는 카프카, 폴라스, 델타 레이크를 사용하여 실시간 데이터 파이프라인을 만드는 방법을 실제로 보여드리겠습니다. 그리 어렵지 않습니다! 자신도 이를 시도할 수 있는 코드는 여기에서 확인하실 수 있습니다. 유용하다고 느끼신다면 ⭐️을 주세요.

기술적인 기사가 될 예정이니, 코드 스니펫이 많이 포함될 것입니다.

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

# 따라오기 시작하기 위해 설정해야 할 사항

시작하기 위해 몇 가지 구성 요소를 설정해 두었습니다:

Kafka 브로커: Docker Compose를 사용하여 Kafka와 Zookeeper 컨테이너를 실행했습니다. docker-compose.yml 파일에는 필요한 서비스와 구성이 정의되어 있습니다. 이에 대해 자세히 다루지는 않겠습니다. 이 파일은 어디에서든 다운로드할 수 있는 기본적인 docker-compose.yml입니다. 데이터 엔지니어링 팀이 Kafka 환경을 관리하는 것으로 간주하지 않겠습니다. 당신이 담당하고 있는 플랫폼 또는 데브옵스 팀이 그 역할을 맡고 있을 것이라고 가정하겠습니다.

발행자: Python 스크립트인 publisher.py를 만들었습니다. 이 스크립트는 일련의 csv 파일에 저장된 샘플 데이터를 Kafka 주제에 발행합니다.

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

구독자님: 새로운 Python 스크립트인 subscriber.py를 만들었어요. 이 스크립트는 Kafka 토픽에서 메시지를 가져와 Polars를 사용하여 Delta 테이블에 쓰는 역할을 해요.

질문: 추가적으로 한 가지 더, query.py라는 마지막 Python 스크립트를 만들었어요. 이 스크립트는 Delta 테이블에 포함된 데이터를 가져와 조인하고, 조인된 데이터를 포함한 데이터프레임을 화면에 출력합니다.

매개변수: Python 스크립트 내에 설정 매개변수를 저장하는 대신 toml 파일을 사용했어요. 이렇게 하면 매개변수를 변경하고 싶어도 코드를 손대지 않아도 돼요.

# 단순히 실행만 하려면

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

원하는 부분으로 건너뛰고 싶으세요? 알겠어요! 여기 실행하는 순서입니다:

먼저 Kafka 브로커를 실행하려면 Docker가 설치되어 있어야 합니다. 메인 폴더 내에서 docker-compose up을 실행할 수 있어요. 먼저 몇 가지 파일을 다운로드해야 하며, 그 후에 프로세스들이 시작되면 화면에 많은 텍스트가 표시될 거예요:

![이미지](/assets/img/2024-05-20-BuildingaReal-TimeDataPipeline_0.png)

그 다음으로, publisher용 새 터미널 세션을 열고, subscriber용 또 다른 터미널 세션을 열어 보세요. Docker 컨테이너를 설정하지는 않았지만, 이들을 독립적인 Docker 컨테이너로 설정할 방법에 대해 알아보고 싶다면 이 기사를 확인해 보세요. 대신, 새로운 가상 Python 환경을 만들어서 그 안에 설치할 걸 권장드려요. python3 -m venv venv로 가상 환경을 생성한 다음 venv/bin/activate를 입력하여 새로운 가상 환경으로 전환하고, requirements.txt에 정의된 필수 패키지를 pip install -r로 설치하시면 됩니다.

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

모든 준비가 완료되었습니다. 먼저 터미널 창에서 subscriber.py를 실행한 후, 준비가 되면 publisher.py를 실행하세요. publisher에서 전송된 마이크로 배치를 볼 수 있으며, 동시에 subscriber가 실시간으로 수신하는 것을 볼 수 있을 겁니다. 그러나 subscriber와 publisher를 올바른 순서로 로드해야 합니다. subscriber가 준비되기 전에 publisher가 모두 보내버리는 일이 없도록 주의하세요. 이 경량 데모에서는 보관 기간을 설정하지 않았으니 주의해주세요.

아래 스크린샷을 보면, 왼쪽에 publisher가 있고 오른쪽에 subscriber가 있는 분할 된 터미널이 있습니다. 왼쪽에서 전송되는 일괄처리된 데이터를 보고, 오른쪽에서는 개별 레코드가 처리되는 것을 볼 수 있습니다.

![Publisher 및 Subscriber 스크린샷](/assets/img/2024-05-20-BuildingaReal-TimeDataPipeline_1.png)

모든 레코드가 전송된 후에는 publisher가 연결을 종료할 것입니다. subscriber는 프로세스가 종료될 때까지 계속 수신 대기 상태에 머무를 것입니다.

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

마지막 단계는 query.py 파일을 실행하여 다른 화면을 로드하고 쿼리 결과를 확인하는 것입니다. 화면에 DataFrame이 출력되는데, 이겇이 우리의 작업 결과입니다. 이러한 csv 파일 각각이 Kafka를 통해 Delta 테이블로 이동되었으며, 이제 올바른 데이터 유형과 필드 이름을 가진 단일 DataFrame으로 변환되었습니다. 필요하다면, 발행물을 시작하는 동시에 실행할 수 있으며, 여러 번 실행하면 데이터가 흐르는 것을 확인할 수 있습니다.

![이미지](/assets/img/2024-05-20-BuildingaReal-TimeDataPipeline_2.png)

여기까지입니다! 몇 개의 csv 파일을 Kafka를 통해 마이크로 배치로 로드한 다음 이를 Delta 테이블로 다운스트림으로 스트리밍했습니다. 그 후 데이터를 쿼리하고 변환하여 직원, 부서, 고객, 판매 일자, 판매 지역 및 판매 금액을 모두 포함한 단일 DataFrame으로 통합했습니다.

만약 궁금하시다면, 각 테이블의 기본 키를 기반으로 파일을 로드하기 위해 병합 프로세스를 사용했습니다. 그래서 데이터를 변경하고 다시로드하면 Delta 테이블에서 데이터를 우아하게 업데이트할 수 있습니다. 아래에서 csv에서 Finance 부서 레코드를 FinTech로 변경하고 두 번째로 실행했더니 어떻게 변경되는지 확인해보세요:

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

![이미지](/assets/img/2024-05-20-BuildingaReal-TimeDataPipeline_3.png)

계속 읽어 보면 어떻게 작동하는지 알 수 있어요...

# 발행자

발행자 스크립트는 데이터를 Kafka로 보내는 데 사용됩니다. 전체 파일은 여기에서 찾을 수 있어요. 일부 구성을 설정한 후, 파일을 시작하는 지점인 아래쪽에서 여정을 시작해요:

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
if __name__ == "__main()":
    producer = create_producer(bootstrap_servers)

    try:
        process_files_and_send(producer, topics, batch_size)
    finally:
        producer.close()
```

카프카 프로듀서를 producer로 인스턴스화하고 서버 세부 정보를 전달했습니다. 그런 다음 파일을 처리하려고 시도하고 완료되면 연결을 닫습니다. 간단하죠! 이제 파일의 맨 위로 돌아가서 각 함수를 하나씩 작업할 수 있습니다.

## create_producer

```js
def create_producer(bootstrap_servers):
    return KafkaProducer(
        bootstrap_servers=bootstrap_servers,
        value_serializer=lambda v: json.dumps(v).encode("utf-8")
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

여기에는 그다지 많은 내용이 없어요. 저는 단순히 KafkaProducer를 함수로 래핑하여 제 개인적인 것으로 만들었어요. 서버 세부 정보를 전달하고, 매개변수인 v를 취하고 해당 매개변수로 전달된 내용을 JSON으로 변환하는 람다 함수를 사용했어요. 그 JSON은 UTF-8로 인코딩되어요. 많은 데이터 처리 함수가 문자열 대신 바이트 형식의 데이터를 예상하기 때문에 이것은 필수적입니다.

## send_messages

```js
def send_messages(producer, topic, messages):
    try:
        for message in messages:
            producer.send(topic, value=message)
        producer.flush()
        logging.info(f"Batch of messages sent to topic '{topic}'.")
    except Exception as e:
        logging.error(f"Failed to send messages to topic '{topic}'. Error: {e}")
```

저는 아직 클래스를 사용하지 않았기 때문에 다음에 이것을 설정하고 있어요. 실제로 사용될 때 미리 로드되고 준비되어 있어야 해요. 여기서는 Kafka로 데이터를 전송하려고 노력해요. 각 메시지마다 Kafka Producer를 사용하고 메시지를 주제로 보내요. 그런 다음 보낸 것을 기록하거나 필요한 경우 발생한 오류를 기록해 내용이 잘못됐는지 조사할 수 있어요.

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

## process_files_and_send

```js
def process_files_and_send(producer, topics, batch_size):
    for topic in topics:
        try:
            with open(f"{raw_path}/{topic}.csv", encoding="utf-8-sig") as csvfile:
                csvreader = csv.DictReader(csvfile)
                batch = []

                for rows in csvreader:
                    batch.append(rows)
                    if len(batch) >= batch_size:
                        send_messages(producer, topic, batch)
                        batch = []

                # Send any remaining messages in the last batch
                if batch:
                    send_messages(producer, topic, batch)

        except FileNotFoundError:
            logging.error(f"File not found: {raw_path}/{topic}.csv")
        except Exception as e:
            logging.error(f"Error processing file for topic '{topic}': {e}")
```

여기가 번잡한 부분이고, 데이터를 보내는 작업을 마치게 될 거야.

제가 프로듀서를 맨 처음에 인스턴스화할 때 전달하고, 토픽들도 함께 전달해주는 함수를 만들었어요. 그런 다음 각 토픽을 순회하면서 처리해요. 각 토픽은 csv 파일과 관련이 있기 때문에, 먼저 파일을 열려고 시도하는 거죠.

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

배치 크기를 입력 매개변수로 정의했다는 것을 알 수 있을 겁니다. 이건 정말 필수적인 것은 아니지만, csv 파일에서 데이터를 가져오는 대신에 웹페이지나 다른 스트리밍 서비스에서 가져오는 것이 아니기 때문에, 메시지를 배치로 묶어서 그룹으로 보내고 싶었습니다. 이렇게 하면 작업이 빨라집니다. 저는 toml 파일 내에서 배치 크기를 10으로 설정했는데, 이는 사실상 한 번에 csv 파일의 10줄에 해당합니다.

그런 다음 각 행을 배치에 추가하면서 배치 한도에 도달할 때까지 작업을 수행한 후 데이터를 전송하고 배치를 비웁니다. 작업을 마치면 마지막 메시지로 배치를 정리합니다 (남은 레코드가 5개만 남았을 수 있지만, 그래도 모두 보내고 싶습니다).

마지막으로, raw 파일을 찾을 수 없는 경우나 발생할 수 있는 기타 일반적인 오류에 대한 오류 처리가 있습니다.

이것이 publisher.py 파일의 내용입니다. 이해가 되시겠나요? 이제 subscriber.py 파일로 넘어가 봅시다!

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

# 구독자

구독자는 Kafka 주제를 청취하고, 새 레코드를 발견했을 때 특정 작업을 수행하는 데 사용됩니다. 이 경우, 해당 레코드의 내용을 가져와 Delta 테이블에 저장합니다. 여기서 전체 코드를 찾을 수 있는 코드를 통해 코드를 하나씩 살펴보겠습니다.

```js
if __name__ == "__main__":
    consumer = create_consumer(topics, bootstrap_servers)
    for message in consumer:
        process_message(message.topic, message.value)
```

발행자와 비슷하게, 첫 번째로 하는 일은 주제 및 서버 세부 정보를 구성 변수로 제공하고, 이를 전달하여 소비자를 인스턴스화하는 것입니다. 그런 다음 소비한 각 메시지를 처리합니다. 간단하지요. 하지만 함수별로 좀 더 자세히 살펴보겠습니다.

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

## 소비자 생성

```js
def create_consumer(topics, bootstrap_servers):
    return KafkaConsumer(
        *topics,
        bootstrap_servers = bootstrap_servers,
        value_deserializer = lambda v: json.loads(v.decode("utf-8"))
```

우리 Python의 진입점에서 첫 번째 줄은 소비자를 인스턴스화하는 것이었습니다. 이것은 기본적으로 KafkaConsumer의 래퍼일 뿐입니다. 퍼블리셔와 다른 것은 아무 것도 하지 않습니다.

## 메시지 처리

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

```python
def process_message(topic, data):
    df = pl.from_records([data])
    delta_table_path = f"{delta_path}/{topic}"
    if not os.path.exists(delta_table_path):
        write_delta_table(df, delta_table_path, "insert")
    else:
        merge_into_delta_table(df, delta_table_path)
```

메시지를 받을 때마다 그것을 처리하는 방법을 알아야 합니다. 저는 여기서 두 가지 방법을 선택했습니다. 먼저 Delta 테이블이 저장된 경로를 가져와 해당 경로가 존재하는지 확인합니다. 경로가 존재하지 않으면 새 레코드를 삽입하기 위해 insert 매개변수를 사용하여 write_delta_table 함수를 호출합니다. 그러나 경로가 이미 존재한다면, merge_into_delta_table 함수를 호출합니다. 이를 통해 데이터를 여러 번로드할 수 있게 하면서 단순히 테이블을 점점 더 크게 만들지 않습니다. 이미 변경된 기존 항목을 업데이트하고 새 레코드만 추가합니다.

이러한 함수들을 좀 더 자세히 살펴봅시다.

## write_delta_table

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

```python
def write_delta_table(df, path, mode):
    try:
        df.write_delta(target=path, mode=mode)
        logging.info(f"{mode.capitalize()}ed message to Delta table: {df}")
    except Exception as e:
        logging.error(f"Failed to {mode} message to Delta table: {df}")
        logging.error(f"Error: {e}")
```

테이블 태그를 마크다운 형식으로 변경해주시기 바랍니다.

여기서는 DataFrame, 주제 경로, 그리고 모드(삽입)를 간단히 전달하고 네이티브 Polars 기능을 사용하여 Delta 테이블에 작성합니다. 작업이 성공했음을 로그에 남기거나 실패했을 때 오류를 캡처하여 조사할 수 있도록 합니다.

## merge_into_delta_table

여기에는 조금 더 있습니다.

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

```python
def merge_into_delta_table(df, path):
    try:
        df.write_delta(
            target=path, mode="merge",
            delta_merge_options={
                "predicate": "s.id = t.id",
                "source_alias": "s",
                "target_alias": "t",
            }
        ).when_matched_update_all().when_not_matched_insert_all().execute()
        logging.info(f"Merged message to Delta table: {df}")
    except Exception as e:
        logging.error(f"Failed to merge message to Delta table: {df}")
        logging.error(f"Error: {e}")
```

여기에서 모드를 전달하지 않아도 됩니다. 왜냐하면 모드가 "merge"로 설정될 것이라는 것을 알기 때문입니다. 제 모든 테이블에는 id라는 동일한 이름을 가진 기본 키가 있으므로 한 번 정의하고 이 함수를 완전히 재사용하기 쉽습니다. 일치하는 경우 데이터가 업데이트되고, 일치하지 않는 경우 데이터가 삽입됩니다. 이것이 기본적으로 upsert입니다. 성공과 실패를 여전히 기록했습니다.

여기가 파이프라인의 전부입니다. 정말 간단합니다. 데이터를 게시하고 데이터를 구독하여 디스크에 저장합니다. 다음에는 해당 데이터를 쿼리하고 모든 것을 함께 가져오는 DataFrame을 가져오는 방법을 살펴볼 것입니다.

# 쿼리

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

이 시점에서 Kafka는 더 이상 사용되지 않습니다. 모든 것이 Delta 테이블에 있으며 쿼리만 필요합니다. 전체 코드는 여기에 있습니다. Delta 테이블을 각각 데이터프레임 집합으로 불러오기 시작했습니다:

```js
client = pl.read_delta(f"{delta_path}/client")
department = pl.read_delta(f"{delta_path}/department")
employee = pl.read_delta(f"{delta_path}/employee")
sale = pl.read_delta(f"{delta_path}/sale")
```

client와 employee 데이터프레임에는 first_name과 last_name 속성이 같습니다. 데이터프레임 이름 외에는 구별할 요소가 없습니다. 각각에 first_name과 last_name을 붙여 공백을 구분자로 사용한 새로운 속성을 추가하고 적절히 이름을 지었습니다. 이렇게 하면 두 데이터프레임을 하나의 데이터프레임으로 결합할 때 무엇이 무엇인지 알 수 있습니다:

```js
client = client.with_columns(
  pl
    .concat_str(["first_name", "last_name"], (separator = " "))
    .alias("client_name")
);

employee = employee.with_columns(
  pl
    .concat_str(["first_name", "last_name"], (separator = " "))
    .alias("employee_name")
);
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

직원 DataFrame에는 department_id라는 추가 속성이 포함되어 있습니다. 이를 사용하여 직원과 부서를 함께 결합한 새 DataFrame을 생성할 수 있어요:

```js
employee_dept = employee
  .join(
    department,
    (left_on = "department_id"),
    (right_on = "id"),
    (how = "inner")
  )
  .select(["id", "employee_name", "department_id", "department"]);
```

직원 DataFrame을 기반으로, 부서 DataFrame과 조인을 생성했는데, 직원 DataFrame의 department_id와 부서 DataFrame의 id를 일치하는 조인 조건으로 지정했어요. 모든 직원이 부서를 가져야 하기 때문에 INNER JOIN을 조인 유형으로 선택했고, 이후 유지할 속성을 선택했어요.

마지막으로, 한 마지막 문장으로 모두 함께 결합할 수 있어요:

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
complete = sale
  .join(employee_dept, (left_on = "employee_id"), (right_on = "id"))
  .join(client, (left_on = "client_id"), (right_on = "id"), (how = "inner"))
  .select(
    pl.col("id").str.to_integer(),
    pl.col("employee_name"),
    pl.col("department"),
    pl.col("client_name"),
    pl.col("date").str.to_date("%d/%m/%Y"),
    pl.col("region"),
    pl.col("sale").str.to_decimal(2)
  );
```

저는 sale DataFrame에서 시작하여 방금 생성한 employee_dept DataFrame에 조인을 수행합니다. 이제 이는 sale, employee 및 department를 결합하는 것입니다. 그런 다음 client DataFrame을 다시 조인한 다음 원하는 속성을 선택합니다. 그러나 이 경우 데이터 유형을 변경했습니다. 데이터는 csv를 통해 가져온 것이기 때문에 원래 데이터 유형이 없었습니다. 따라서 id는 정수이어야 한다고 지정했습니다. 날짜는 날짜 유형이어야 하며, 형식을 지정했으며, sale 속성이 두 자리 소수점(달러 및 센트)인 것을 지정했습니다.

마지막 단계는 결과를 출력하는 것입니다:

<img src="/assets/img/2024-05-20-BuildingaReal-TimeDataPipeline_4.png" />

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

# 결론

본 글에서는 Kafka, Polars, 그리고 Delta Lake를 활용하여 실시간 데이터 파이프라인을 구축하는 방법을 살펴보았습니다. 메시지 스트리밍을 위해 Kafka를 활용하고, 데이터 처리를 위해 Polars를 사용하며, 안정적인 저장소로 Delta Lake를 활용하여 확장 가능하고 오류 허용성 있는 데이터 파이프라인을 만들 수 있습니다.

저장소의 코드 샘플은 파이프라인의 기본 설정과 기능을 보여줍니다. 데이터 보존 정책 구성, S3 객체 저장소에 데이터 보관, 다른 시스템과 통합하는 등 특정 요구 사항에 맞게 코드를 확장하고 사용자화할 수 있습니다.

이 아키텍처를 채택함으로써 기업은 실시간 데이터를 효율적으로 처리하고 분석하여 데이터 기반 결정을 내리고 신속하게 변화에 대응할 수 있습니다.

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

이 글이 유용했길 바랍니다. 만약 그렇다면 👏 한 번 눌러주시겠어요? 그리고 더 보고 싶으면 구독해주세요. 매주 한 두 편의 글을 업로드하고 있어요.
