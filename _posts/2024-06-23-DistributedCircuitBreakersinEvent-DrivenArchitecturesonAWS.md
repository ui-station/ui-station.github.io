---
title: "AWS에서 이벤트 기반 아키텍처에 분산 회로 차단기 적용 방법"
description: ""
coverImage: "/assets/img/2024-06-23-DistributedCircuitBreakersinEvent-DrivenArchitecturesonAWS_0.png"
date: 2024-06-23 00:26
ogImage:
  url: /assets/img/2024-06-23-DistributedCircuitBreakersinEvent-DrivenArchitecturesonAWS_0.png
tag: Tech
originalTitle: "Distributed Circuit Breakers in Event-Driven Architectures on AWS"
link: "https://medium.com/@sodkiewiczm/distributed-circuit-breakers-in-event-driven-architectures-on-aws-95774da2ce7e"
---

![image]("https://miro.medium.com/v2/resize:fit:1280/1*gGh7HhACzjtw6wplLPUyQQ.gif")

# 소개

저는 이벤트 기반 아키텍처에서 이벤트 실패를 처리하는 방법에 대해 발표 자료를 준비하고 있었습니다. 어느 순간에 이를 설명할 때 회로 차단기가 필요한 이유에 대해 깊이 들어가게 되었습니다. 제 프로젝트에서 Elasticache를 기반으로 한 사용자 정의 구현을 사용했다는 것을 깨달았습니다. 그들을 설정하는 더 "가벼운" 방법에 대해 고민하기 시작했을 때, 서버리스 아키텍처에서 회로 차단기를 설정하는 메커니즘이 없다는 것을 깨달았습니다. 좀 더 연구해보고 이 주제에 대한 제 생각을 공유하려고 합니다.

## 왜 서버리스가 특별한가요?

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

서킷 브레이커는 상태를 가지고 있습니다. 호출하기 전에 해당 상태를 확인하고 모든 요청을 추적해야 합니다. 왜냐하면 서킷 브레이커를 단일 호출 기반이 아니라 실패율(RATE) 기준으로 열기 때문입니다.

우리는 람다 인스턴스 전체에 분산된 상태 대신 단일 위치에 상태를 추적해야 합니다. 다시 처음으로 돌아가 봅시다.

# 왜 서킷 브레이커가 필요한가요?

## 시나리오

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

이벤트 처리를 상상해보세요. 여기서는 대기열에서 작업을 가져와서 제3자로부터 데이터를 로드한 다음 데이터에 대해 "무언가"를 수행합니다. 이제 이 데이터를 DynamoDB 테이블에 지속시키기로 했다고 가정해 봅시다.

![이미지](/assets/img/2024-06-23-DistributedCircuitBreakersinEvent-DrivenArchitecturesonAWS_0.png)

## 모든 것이 실패할 때

어느 날, 우리의 3rd 파티가 좋지 않은 날을 보내서 실패하기 시작할 수도 있습니다. 그 순간, 그것은 완벽할 것이고 모두가 우리가 예의 바르게 3rd 파티 시스템을 사용하는 데 존경을 기울이면 좋을 것입니다. 우리는 그들이 많은 압박을 받고 있다는 것을 알았으므로 그들에게 계속해서 요청을 보내지 않는 것이 좋을 것입니다. 돈과 자원, 그리고 SRE 팀 멤버들의 신경을 아끼게 되겠죠.

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

![image](https://miro.medium.com/v2/resize:fit:1280/1*d6R2_OewGnnrbMdLNLht8A.gif)

## 그럼 우리가 할 수 있는 것은 무엇일까요? 백오프 아이디어

첫 번째 떠오르는 아이디어는 소비자들에게 백오프 전략을 적용하는 것입니다. 받은 메시지와 실패한 메시지의 가시성 제한 시간을 변경할 수 있습니다. 여기 명심해야 할 몇 가지 중요한 점이 있습니다:

- 최대 지연 시간 값을 설정하는 것,
- 그리고 지터를 적용하는 것입니다.

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

아래와 같은 메시지를 받았다고 가정하고 있다면, ChangeMessageVisibility API를 사용하여 간단히 처리할 수 있습니다.

![이미지](/assets/img/2024-06-23-DistributedCircuitBreakersinEvent-DrivenArchitecturesonAWS_1.png)

우리는 메시지의 처리 시도 횟수에 대한 정보를 포함하고 있는 ApproximateReceiveCount 값을 추출하고, 이를 기반으로 백오프와 지터를 계산할 수 있습니다. 아래 스니펫과 같이요.

다음으로, 각 레코드의 receiptHandle을 사용하여 AWS SQS SDK를 이용해 시각성 제한 시간을 변경해야 합니다. 아래 코드 스니펫처럼요.

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

## 결과

백오프는 메시지를 확장된 간격으로 다시 시도합니다. 이를 통해 루프에서 항상 같은 메시지를 처리하지 않고, 제3자 시스템에 호흡 공간을 줄 수 있어서 그 사이에 좋아질 것을 희망할 수 있습니다. 또한, 최대 재시도가 제한된 경우 죽은 편지 대기열을 저장할 수 있습니다.

자랑스럽게 여겨질 수 있고, 자신을 칭찬할 수 있지만, 우리는 크게 변하지 않았습니다. 심각한 중단이 있을 경우 AWS는 SQS 소비자 호출을 제한할 수 있지만, 고 처리량 시스템의 경우 3rd party 시스템 관점에서는 여전히 같은 상황에 있습니다.

더 심각한 문제는 이 모든 람다 호출에 돈을 쓰고 있으며, 우리의 제3자 시스템이 다운된 경우 호출은 우리의 타임아웃만큼 느려져 많은 비용이 발생할 수 있습니다. 이를 어떻게 방지할 수 있을까요?

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

## 회로 차단기 이용하기

회로 차단기는 전기 회로의 회로 차단기처럼 작동합니다. 문제를 감지하면 차단이 되어 더 이상 요청이 전달되지 않습니다. 서드 파티 시스템이 다시 온라인 상태가 되면 회로를 닫고 싶습니다.

그 현지 조사는 "정찰" 요청을 보내면서 반 열린 상태로 수행됩니다. 이 요청은 회로 차단기 뒤에 있는 시스템이 여전히 다운된 상태인지 확인하는 작은 그룹으로, 시스템이 다운된 경우 회로는 열린 상태를 유지하고 다시 작동하면 회로를 닫고 평상시로 돌아갑니다.

아래 애니메이션을 살펴보시면 더 이해하기 쉬울 것입니다.

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

아래의 다이어그램을 확인하면 프로세스에 대해 여전히 헷갈리는 부분이 있을 수 있습니다.

![다이어그램](/assets/img/2024-06-23-DistributedCircuitBreakersinEvent-DrivenArchitecturesonAWS_2.png)

# 게임의 서버리스 상태

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

서버에서는 크게 문제가 되지 않습니다. 회로 차단 장치 구현 중 하나를 사용할 수 있으니 걱정 마세요. 서버에 상태를 로컬로 저장합니다. 물론 모든 인스턴스가 동기화되지는 않겠지만, 보통 큰 문제가 되지는 않습니다. 서버리스의 경우 상황은 매우 다릅니다. 많은 인스턴스가 있고, 각 작은 인스턴스 간에 상태를 전달할 수 없습니다.

이곳에서 주요 고려 사항은 무엇인가요?

- 솔루션의 복잡성
- 읽기 작업 전에 상태를 검사해야 하므로 솔루션 비용
- 작업 후 호출 결과를 지속

서버리스 공간에서 대다수 사용자에게 쉽게 작동할 수 있는 솔루션을 고민 중입니다. 일반적으로 저는 Circuit Breaker 상태를 Elasticache에 저장합니다. 왜냐하면 존재하고, 속도가 매우 빠르며 – 사용자 정의 구현 외에 – 사용하기 쉽기 때문입니다. 반면에 모든 서버리스 시스템이 VPC에서 실행되고 심한 부하를 겪지는 않는다는 것을 이해하고 있습니다. 그렇지만 여기서 가장 흥미로운 부분이 "고부하"입니다.

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

인터넷에서 발견된 구현 방법들과 제가 생각해낸 것에 대해 살펴보겠습니다.

# 일반적인 옵션: Jeremy Daly의 클래식

서킷 브레이커에 관한 거의 모든 기사가 Jeremy Daly의 기사를 언급합니다... 그러니 시작해보죠.

![이미지](/assets/img/2024-06-23-DistributedCircuitBreakersinEvent-DrivenArchitecturesonAWS_3.png)

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

저는 사용 중이고 방금 말한 패턴입니다. 중앙 집중식 저장소에 상태를 가진 클래식 회로 차단기일 뿐입니다. 훌륭하게 작동하며 실전 검증을 받았습니다. 분산 시스템에서 회로 차단기를 설정하는 가장 좋은 방법이라고 믿습니다. 다음을 제공합니다:

- 여러 CB를 지원하는 매우 세분화된 솔루션
- 로직이 코드에 있기 때문에 대체값을 반환할 수 있습니다.
- 어떤 이벤트 소스 매핑 및 호출 유형과도 사용할 수 있습니다.

이 패턴은 Elasticache를 필요로 하므로 일반적으로 VPC도 필요합니다. VPC에서 솔루션을 구축하고 싶지 않다면 어떻게 할까요?

## 비-VPC 변형

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

DynamoDB 고려 사항
저자는 비-VPC 람다와 함께 DynamoDB를 사용할 수도 있다고 언급합니다. 고수준 시스템에서 이 옵션을 고려할 가치가 있을까요? 물론, 이것은 귀하의 규모에 달려 있습니다. 왜냐하면:

- 파티션당 1000 WCU 제한 - 따라서 1000 RPS 이상의 회로 차단기 상태를 유지하는 데 꼼수를 사용해야 합니다 (Tycko Franklin에게 그것을 지적해 준 것을 칭찬합니다)
- 회로 차단기 유지의 비용은 고처럼 높을 수 있습니다.

![이미지](/assets/img/2024-06-23-DistributedCircuitBreakersinEvent-DrivenArchitecturesonAWS_4.png)

대안적 접근 방식? Momento!

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

Momento를 사용하면 람다 함수를 VPC에 넣을 필요가 없고 동시에 비용을 절약할 수 있습니다. 또한 매우 확장 가능하고 저지연 솔루션이기도 합니다.

![2024-06-23-DistributedCircuitBreakersinEvent-DrivenArchitecturesonAWS_5.png](/assets/img/2024-06-23-DistributedCircuitBreakersinEvent-DrivenArchitecturesonAWS_5.png)

## 주의할 점

잘못된 구현
인터넷을 뒤져본 결과 잘못된 구현이 많은 것 같습니다. 제가 잘못된 부분이 있다면 지적해주세요. 가장 인기 있는 구현조차도 분산 시스템에서 단일 인스턴스의 상태를 덮어씌우기 때문에 많은 문제를 야기할 수 있습니다.

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

구현: https://github.com/gunnargrosch/circuitbreaker-lambda

잘못된 정보 소스입니다
SSM 및 헬스 체크 상태를 사용하라는 제안을 봤는데, 해당 구현에 들어가기 전 꼭 모든 호출 전에 SSM을 확인하지 말아주세요. 비용이 많이 발생할 수 있어요. 그러나 어떤 해결책은 있습니다. 아래 링크된 스레드를 살펴보세요.

# ESM Circuit Breaker Patterns

일부 회로 차단 방식은 이전 패턴만큼 범용적이지 않으며 ESM (이벤트 소스 매핑)으로 실행되는 이벤트 컨슈머에 특화되어 있습니다.

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

## Christoph Gerkens의 전문화된 ESM 회로 차단기

이 문서는 흥미로운 아이디어를 다루고 있습니다. 계획은 SQS Consumer ESM을 회로 차단기로 사용하는 것입니다. 우리는 ESM 상태를 활성화/비활성화하여 회로를 열고 닫을 수 있습니다.

![이미지](/assets/img/2024-06-23-DistributedCircuitBreakersinEvent-DrivenArchitecturesonAWS_6.png)

"Half Open" 상태와 "스카웃 요청"을 보낼 때, 작성자는 "Trial Message Poller"라는 추가 람다를 사용하는 것을 제안합니다. 이 람다는 큐에서 메시지 중 하나를 읽어 SQS Consumer 람다로 보냅니다. 결과에 따라 회로를 닫을지 열어둘지 결정하게 됩니다.

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

이 방법은 우리의 람다에서 아무것도 할 필요가 없으며, 그것은 완전히 그 로직에서 분리되어 있다는 점에서 훌륭합니다.

비용
상점이 없으므로 저렴해야 할 텐데요, 맞죠? 저자는 고해상도 메트릭 사용을 권장합니다. 미처리 드리프트가 몇 분 동안 지속되는 차단기 문제를 피하려면 꼭 필요합니다. 동의합니다만, 여기서는 고처리량 처리 렌즈를 통해 보는 것이기도 합니다.

이 경우 많은 메트릭을 수집하면 비용이 많이 들 수 있고, 이러한 메트릭을 저장하는 것은 신중하게 처리해야 합니다. PutMetricData API는 1000번의 호출당 0.01달러로 상당히 비싸니, EMF로 전환하는 것을 고려해볼 수 있습니다. 그 가격표와 매 호출 후 이벤트 추적을 고려하면 상태 저장과 함께 적절한 차단기를 사용하는 것이 더 저렴할 수 있다. 미리 계산해보세요.

복잡성
"시험 메시지 폴러"은 추가 복잡성을 도입하며 저는 유지하고 싶어하는 것이 아닙니다. 이것은 전체 솔루션과 결합돼 있어야 하며 다른 유형의 트리거와 재사용할 수 없습니다. SQS에서도 시각 제한 또는 부분 실패로 인한 잠재적인 문제가 있다고 상상할 수 있습니다.

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

잠재적인 메시지 손실
메시지가 대기열에서로드되지 않고 장기적이고 심각한 서드파티 오류의 경우, 우리는 메시지를 잃을 수 있습니다. 네, 메시지가 maxReceiveCount에 도달하지 않고 rententionPeriod가 경과하면 메시지가 데드 레터 대기열로 이동되지 않습니다.

## 간소화된 ESM 회로 차단기

Christoph Gerken의 논문에서 영감을 받아, 저는 그 구조적 패턴을 간소화하는 것을 고려했습니다.

이 원리는 전체 회로 차단기 상태를 CloudWatch 경보 상태에 반영하고 이를 회로의 상태로 사용하는 것에 기반합니다.

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

![Screenshot](/assets/img/2024-06-23-DistributedCircuitBreakersinEvent-DrivenArchitecturesonAWS_7.png)

원본 글에 정의된 메트릭(호출 및 오류를 사용하여 실패율을 계산)은 기본적으로 무료로 고해상도로 제공되므로 우리는 이를 사용하여 실패율을 추적하고 10초 간격으로 경보를 정의할 수 있습니다. (이 솔루션은 여러분의 요구에 따라 어떤 경보라도 기반으로 작동할 수 있으며, ESM 관리자는 다양한 경보의 변경 사항을 듣고 있어야 합니다.)

Half-open 상태는 Step Functions를 통해 관리할 필요가 없습니다. CloudWatch 경보의 INSUFFICIENT_DATA 상태를 사용할 수 있습니다. 여기에는 한 가지 단점이 있습니다 — half-open 기간은 큐에서 가져온 단일 메시지 대신 SQS 소비자가 사용한 메시지의 제한된 샘플을 기반으로 합니다. 또한, TreatMissingData: missing을 사용하는 것이 중요합니다.

```js
FailureRateAlarm:
  Type: AWS::CloudWatch::Alarm
  Properties:
    Metrics:
      - Expression: "100 * errors / MAX([errors, invocations])"
        Id: "failureRate"
        Label: "failureRate"
        ReturnData: true
      - Id: "errors"
        MetricStat:
          Metric:
            MetricName: Errors
            Namespace: AWS/Lambda
            Dimensions:
              - Name: FunctionName
                Value: !Ref YourFunction
          Period: 10
          Stat: Sum
        ReturnData: false
      - Id: "invocations"
        MetricStat:
          Metric:
            MetricName: Invocations
            Namespace: AWS/Lambda
            Dimensions:
              - Name: FunctionName
                Value: !Ref YourFunction
          Period: 10
          Stat: Sum
        ReturnData: false
    EvaluationPeriods: 5
    DatapointsToAlarm: 3
    TreatMissingData: missing
    Threshold: 80
    ComparisonOperator: GreaterThanOrEqualToThreshold
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

상태 관리
알람이 트리거되면 회로가 열리며 람다 호출이 발생하지 않습니다. 그럼 알람은 INSUFFICIENT_DATA 상태로 전환됩니다. 그 후에는 큐의 제한된 동시성 처리를 통해 "스카웃 요청"을 보낼 수 있으며, 실패한 후에는 처리가 성공적으로 이루어진 후에 서킷을 열거나 닫을 수 있습니다.

![image](/assets/img/2024-06-23-DistributedCircuitBreakersinEvent-DrivenArchitecturesonAWS_8.png)

기본 AWS 람다 메트릭을 사용할 경우, 새로운 접근 방식을 절약할 수 있지만, 이전 옵션에서의 "메시지 손실 가능성" 문제에 여전히 취약합니다.

구현
ESM을 활성화하고 비활성화하는 것은 쉽습니다. 유일하게 누락된 것은 ESM에 메타데이터를 추가할 수 있는 옵션이었습니다. 해당 기능이 누락되었으므로 스택에 매개변수로 추가해야 합니다.

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

내 구현은 go로 작성되었고 일부 사용자 지정 instrumentation 및 OTEL 통합이 되어 있어요. 하지만 여기 스니펫에서처럼 간단한 것을 사용할 수도 있어요.

내 구현은 SQS를 기반으로 하고 있지만 모든 다양한 이벤트 주도형 ESMs 구성에 적응시킬 수 있어요. 작은 사용자 정의 후처리 후 반-열린 상태로 커스터마이즈된 구성을 이용해요.

테스트
해당 솔루션은 여러 다른 알람 구성을 이용해 FIS로 테스트했어요. 아래에서는 예제 알람 상태 전이의 동작을 확인할 수 있어요.

이 접근 방식은 반-열린 상태로 이동하는 빈도를 제어하는 유연성이 부족하지만, 코드에 액세스할 수 없거나 람다 핸들러에 복잡성을 추가하고 싶지 않은 경우에는 좋아요.

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

# 요약

분산 시스템에서 서킷 브레이커를 올바르게 구현하는 것은 매우 어려울 수 있으며 고려해야 할 다양한 트레이드오프와 접근 방식이 많이 있습니다. 경우에 따라 분산된 서킷 브레이커가 전혀 필요하지 않을 수도 있습니다. 예를 들어, 작은 람다 소비자 풀의 경우에는 서킷 상태 메모리가 충분할 수도 있습니다.

만약 당신의 케이스에 해당하지 않는다면, 이 글이 당신에게 결정을 도와주거나 구현 중 고려해야 할 사항에 대한 아이디어를 제공해 줄 수 있기를 바랍니다. 아래에서는 당신을 위한 결정 트리를 만들어 보았습니다. 전통적인 접근 방식이 여전히 최선이라고 믿지만 ESM 기반 서킷 브레이커의 사용 사례도 볼 수 있습니다.

해당 주제에 대해 토의하고 싶다면, #believeinserverless 커뮤니티에 가입하여 이곳에서 커뮤니티와 논의할 수 있습니다.

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

시간 내어 주셔서 감사합니다! 이 주제에 대한 여러분의 생각을 듣고 싶습니다! 연락을 주세요:

## 부가 설명

또 다른 접근법
이곳에서 읽을 수 있는 Sheen Brisals가 시도한 Circuit Breakers with retries 및 archiving events에 대한 접근법: [https://sbrisals.medium.com/amazon-eventbridge-archive-replay-events-in-tandem-with-a-circuit-breaker-c049a4c6857f](https://sbrisals.medium.com/amazon-eventbridge-archive-replay-events-in-tandem-with-a-circuit-breaker-c049a4c6857f) 또는 여기서 저자가 아이디어를 제시하는 동영상을 시청하세요(멋진 이야기입니다):
