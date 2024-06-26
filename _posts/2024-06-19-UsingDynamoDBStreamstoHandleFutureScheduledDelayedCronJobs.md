---
title: "미래 예약된 지연 크론 작업 처리를 위해 DynamoDB Streams 사용하기"
description: ""
coverImage: "/assets/img/2024-06-19-UsingDynamoDBStreamstoHandleFutureScheduledDelayedCronJobs_0.png"
date: 2024-06-19 12:13
ogImage:
  url: /assets/img/2024-06-19-UsingDynamoDBStreamstoHandleFutureScheduledDelayedCronJobs_0.png
tag: Tech
originalTitle: "Using DynamoDB Streams to Handle Future Scheduled Delayed Cron Jobs"
link: "https://medium.com/@chrisrbailey/using-dynamodb-streams-to-handle-future-scheduled-delayed-cron-jobs-ea0d3a10e936"
---

크론 작업, "백그라운드 작업" 또는 클라우드워치 이벤트 대신 유연한 일정에 필요한 작업을 수행하는 대체 수단입니다.

많은 소프트웨어 시스템은 작업을 예약할 수 있는 메커니즘이 필요하며, "크론" (또는 AWS 클라우드의 클라우드워치 이벤트/예약된 작업) 또는 "지연 작업" 시스템은 일반적인 해결책입니다. 그러나 특정 유형의 작업에 대해 꽤 좋거나 분명히 크론보다 나은 대안이 있습니다. 해당 대안은 TTL(생존 기간)이 있는 DynamoDB 레코드 및 람다 함수로 처리된 DynamoDB 스트림을 사용하는 것입니다. 레코드의 TTL이 만료될 때 람다가 트리거되며, 그때 레코드를 필요에 따라 처리할 수 있습니다. 이러한 종류의 작업에는 어떤 작업을 할 수 있을까요? 어떤 작업에서는 잠재적으로 변경 가능하거나 동적이며 또는 "x 분 후" 유형의 일정이 필요한 경우가 있습니다. 가능한 고정된 크론 스타일 일정(예: 매주 화요일 정오에 실행) 대신에 유연한 일정을 원하는 작업에 적합합니다. 또한 개별화되거나 희소한 상황에 대해 매우 효율적이고 비용 효율적인 개별 레코드를 기반으로 트리거하는 것이 모든 레코드를 대상으로 프로세스를 실행하는 것보다 우수합니다.

몇 가지 사용 사례 예시:

- 이벤트 전 X일 또는 마감일 이후 X일 후에 메시지를 전송하는 알림 메시지 보내기.
- X 시간 동안 독자적 상태/상태를 변경하거나 읽지 않은 경우 상태 변경하기.

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

TTL의 정밀도에 관한 중요한 주의 사항이 있습니다. 아래 중요한 주의 사항 섹션을 참조해주세요.

# 기본 사용법

기본 패턴은 특정 이벤트를 기반으로 DynamoDB 레코드를 생성/업데이트하고, 그 후 미래의 특정 시간에 어떤 일이 발생하길 원할 때입니다. 레코드에 TTL(생존 기간)을 설정하여 그 시점에 만료되도록 하고, 만료되면 DynamoDB가 레코드를 삭제합니다. 그런 다음 DynamoDB 테이블에 스트림을 활성화하고, 해당 삭제 이벤트를 처리할 람다를 스트림에 연결합니다. 람다에는 이 상황에 대한 특정 이벤트만 수신하도록 필터가 있어야 합니다(REMOVE 및 TTL 삭제 vs. 코드 삭제를 구분하는 필드 및 주 키에 대한 필터 설정이 있어야 합니다).

![image](/assets/img/2024-06-19-UsingDynamoDBStreamstoHandleFutureScheduledDelayedCronJobs_0.png)

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

이 경우 DynamoDB의 추가적인 이점은 이러한 레코드에 대해 PUT을 수행함으로써 이것이 "업서트"로 작동한다는 것입니다. 즉, 이를 유지하려면 이미 존재하는 레코드인지 확인하고 TTL을 업데이트하거나 새로운 레코드를 만들 필요가 없습니다 (예를 들어 대부분의 RDBMS에서 해야 하는 작업과 같은).

# 예시

위의 경우 #2를 기준으로 예시를 살펴보겠습니다. 이 경우가 더 복잡하여 이 기술의 장점을 잘 보여줍니다. 장치 읽기를 수신하는 시스템을 상상해보세요. 보통 하루에 한 번씩 읽기가 발생하며, 읽기가 일주일 동안 없는 경우 상태를 업데이트하고 경고를 생성하고자 합니다. 이러한 장치들은 높은 빈도로 읽기가 발생하지 않습니다(이 경우 농업 관수 시스템이거나, 원격 환자 모니터링 장치일 수 있습니다. 즉, 환자가 집에서 매일 혈압을 측정하겠다는 것입니다). 다음을 수행해야 합니다:

- 읽기가 없는 경우 3일 후에 장치 상태를 "연결 불가"로 설정
- 장치 관리자에게 7일 후에 경고를 발생시키고, 10일 후에도 여전히 오프라인인 경우에 다시 경고를 보냅니다.

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

PUT 메소드를 사용하여 시간 프레임(3, 7, 10일)마다 DynamoDB(DDB) 레코드를 세 개 PUT하면 이를 달성할 수 있습니다. 첫 번째 레코드는 도달할 수 없는 상태를 트리거하기 위한 것이고, 나머지 두 개는 경고를 위한 것입니다. 기기에서 읽기 값을 받을 때마다 이 작업을 수행하는데, 이렇게 함으로써 TTL을 연장합니다 (PUT은 같은 기본 키의 기존 레코드를 덮어씁니다). 그러나 갑자기 몇 일 동안 읽기 값을 받지 못하게 되었을 때, 즉 레코드가 업데이트되지 않았을 때는 우선 3일 항목의 TTL이 만료되어 DynamoDB 스트림으로 REMOVE 이벤트를 보내고, 람다로 전달하여 프로세스를 시작합니다.

그래서 우리는 파티션 키(PK)와 소트 키(SK) 설계가 모두 있는 DDB 기본 키를 사용할 수 있는데, 이는 다음과 같습니다:

PK: DEVICE#`소유자 ID`

SK: `기기 ID`#`이벤트 ID`

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

TTL: 유닉스 시대 시간, 초 단위 해상도입니다.

당신의 요구에 따라 추가적인 속성을 갖고 있을 수 있으며, 일반적으로 PK 및 SK의 값에 대한 명시적 속성도 가지고 있습니다. 그렇게 함으로써 PK/SK에서 그 값을 분리해 내지 않아도 됩니다. 예를 들어, OwnerID, DeviceID, EventID와 종종 데브리 책에서 설명한 것처럼 유용한 유형 필드가 있을 것입니다.

게다가, 장치가 더 이상 사용되지 않는 경우, 단순히 PK = DEVICE#`ownerid` 및 디바이스 ID의 begins_with를 사용하는 SK에 대한 DDB 레코드를 모두 삭제하여 이를 처리할 수 있습니다.

DynamoDB 테이블에서 스트림을 활성화하고, 스트림에 람다를 연결하여 이벤트를 처리해야 합니다. AWS 문서 "DynamoDB Streams"와 "AWS Lambda Trigger"를 참조하세요. 기본적으로 람다는 DynamoDB "이벤트"를 모두 받아오게 되므로 삽입, 업데이트 및 삭제를 다루게 됩니다. 여기서 필터링이 필요합니다. 여기서는 최소한 REMOVE(삭제) 이벤트만 필터링하고자 합니다. 필터링은 람다에서 수행할 수 있지만, 이는 소요 비용이 큽니다. 왜냐하면 매번 레코드를 유지하기 위해 이 디자인으로 정기적으로 수행하고 있는 삽입/업데이트가 발생할 때마다 람다가 호출되기 때문입니다. 다행히도 "Lambda Event Filtering"을 통해 이벤트와 일치하지 않으면 람다가 호출되지 않도록 할 수 있습니다.

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

만약 이벤트 전용 DynamoDB 테이블을 따로 사용한다면, 삭제 이벤트에 대한 필터만 필요합니다. 그러나 테이블 내에 다른 아이템이 있는 경우 (예: 단일 테이블 디자인을 사용하는 경우) 특정 레코드로 제한하기 위해 필터를 추가해야 합니다. 이 경우 PK가 'DEVICE#' 접두사를 가진 레코드만 일치하도록 "접두사" 필터를 사용하여 이 작업을 수행할 수 있습니다. AWS의 Lambda 이벤트 필터링을 다룬 이 튜토리얼을 참고하시기 바랍니다. 마지막으로, 반드시 해야 할 중요한 추가 필터링 사항이 있습니다. userIdentity 필드를 확인해야 합니다. DynamoDB Streams 및 Time To Live 문서에서 이에 대해 설명되어 있으며, 필터 구문을 보여줍니다. 예를 들어, Serverless Framework를 사용하는 경우, DynamoDB 스트림을 처리하는 람다는 다음과 같은 이벤트 정의를 갖게 됩니다:

```js
    events:
      - stream:
          type: dynamodb
          batchSize: 20
          enabled: true
          arn:
            Fn::GetAtt: [DeviceMonitorTable, StreamArn]
          filterPatterns:
            - eventName: [REMOVE]
              dynamodb:
                Keys:
                  PK:
                    S:
                      - prefix: 'DEVICE#'
              userIdentity:
                type:
                  - Service
                principalId:
                  - dynamodb.amazonaws.com
```

이 필터를 통해 람다가 처리해야 할 이벤트만 받게 됩니다. 그 후, 람다는 받은 레코드에 적절한 처리를 수행합니다 (아마도 이벤트 ID나 레코드 내의 다른 관련 데이터에 기반하여). 그 후에 장치가 계속해서 측정치를 갖지 않는 상태로 유지되거나 시리즈의 다음 DDB 레코드가 TTL에 도달하거나 등의 상황이 발생할 수 있습니다. 장치가 다시 사용되고 모든 레코드가 업데이트되거나 (첫 번째 레코드부터 시작하여 TTL에 도달한 레코드의 수에 따라) 다시 생성될 수 있습니다.

# 중요한 한 가지 주의사항!

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

이 솔루션에서 주의해야 할 중요한 점은 TTL이 정확하지 않고 만료 후 "만료일로부터 몇 일 이내에" 발생한다는 것이다. 이 문서에 따르면 정확한 타이밍을 기대하는 중요한 작업에는 사용하지 않는 것이 좋다. 그런데, 실제로, 두 가지 서로 다른 앱에서 확인했을 때, 이 작업은 대개 TTL만료 후 몇 분 안에 트리거된다. 테이블의 사용량에 따라 (즉, 정기 사용은 만료된 레코드의 정기적인 정리를 의미한다) 결정되는 것 같다. 이에 대해 더 나은 정보가 있다면, 의견을 달아주시거나 말씀해주세요!

# 마지막으로

이러한 지연 작업 시스템을 구축하는 것은 크론 스타일 접근 방식으로는 정말 고통스럽습니다. 하루에 한 번 또는 모든 시간 간격을 확실히 포함할 때의 빈도로 크론 작업을 수행해야 하기 때문입니다. 위의 예와 같은 "일" 단위 간격의 경우에는 그렇게 나쁘지 않을 수도 있습니다. 그러나 더 정확한 타이밍이 필요한 경우, 그냥 작동하지 않을 수도 있습니다. 게다가, 이러한 이벤트가 발생하는 빈도가 더 드문 경우, 필요 이상으로 크론 작업을 실행할 수도 있습니다. AWS 생태계에 속해 있다면, 저장 실행 작업에 대신 제3자나 패키지를 가져올 경우에 비해 사용하기가 매우 매력적으로 보입니다.

게다가, 타이밍 간격이 구성 가능하면, 이것은 크론 스타일 시스템보다 처리하기가 훨씬 쉽습니다. 여러분의 수용할 수 있는 한계와 지정된 동작에 따라서, 기존 항목을 그대로 둘 수 있고 다음 장치 읽기(또는 DDB 레코드 쓰기를 트리거하는 것)에서 TTL을 간단히 업데이트하거나, 영향을 받는 레코드만 조정할 수도 있습니다(PK+SK 콤보를 사용하여 실제 시간 양에 의존하지 않는 것을 확인하세요. 따라서 저는 SK에서 이 측면을 "이벤트 ID"로 지정했습니다. "3일" 또는 다른 것이 변할 수 있는 "3일" 같은 것 대신 "alert1"을 사용할 것입니다).

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

이 방식은 이벤트 주도 스타일을 제공하며 놀라운 확장성을 제공하고 물론 서버리스 시스템과 잘 어울립니다.
