---
title: "2024년에 경력있는 Java 개발자를 위한 카프카 인터뷰 질문"
description: ""
coverImage: "/assets/img/2024-05-23-KafkaInterviewQuestionforexperiencedJavadeveloperin2024_0.png"
date: 2024-05-23 12:57
ogImage:
  url: /assets/img/2024-05-23-KafkaInterviewQuestionforexperiencedJavadeveloperin2024_0.png
tag: Tech
originalTitle: "Kafka Interview Question for experienced Java developer in 2024"
link: "https://medium.com/@rathod-ajay/kafka-interview-question-for-experienced-developer-in-2024-01b95d126cdd"
---

## 안녕하세요 여러분, 카프카와 관련된 또 다른 기사에 오신 것을 환영합니다. 요즘 대부분의 기술 인터뷰에서 자바, 프런트엔드, 백엔드 및 풀스택 개발자 인터뷰에서 카프카 관련 질문이 나온다고 하죠. 하지만 왜 그럴까요? 분산 환경에서 여러 애플리케이션을 연결하기 위해 미들웨어가 필요하고 카프카는 인기 있는 미들웨어이면서 매우 강력합니다. 업계는 이를 신속하게 채택했기 때문에 카프카는 인터뷰에서 중요하다고 할 수 있습니다. 이제 물어볼 수 있는 질문 유형으로 들어가보겠습니다. 초보자나 신입 개발자는 이 부분을 건너 뛰어도 됩니다.

![이미지](/assets/img/2024-05-23-KafkaInterviewQuestionforexperiencedJavadeveloperin2024_0.png)

## 왜 카프카가 기술 산업에서 사용되며 왜 인기가 많을까요?

아파치 카프카는 기술 산업에서 인기가 많은데, 그 이유는 몇 가지 주요한 이유로 인해입니다:

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

- 높은 처리량과 낮은 지연 시간: Kafka는 대량의 메시지를 신속하게 처리하여 실시간 데이터 처리에 적합합니다.
- 확장성: Kafka의 분산 아키텍처로 수평적 확장이 가능하며, 더 많은 노드를 추가함으로써 데이터 요구량을 수용할 수 있습니다.
- 고장 허용성 및 신뢰성: Kafka는 복제와 파티셔닝을 통해 데이터의 내구성과 신뢰성을 보장하여 일부 노드가 실패해도 작업을 유지합니다.
- 스트림 처리: Kafka Streams를 사용하면 복잡한 실시간 데이터 처리 응용프로그램을 구축할 수 있습니다.
- 데이터 통합: Kafka는 다양한 소스에서 데이터를 통합하고 여러 시스템에 분산하는 중앙 허브 역할을 합니다.
- 오픈 소스 및 커뮤니티 지원: Kafka는 계속해서 기능을 개선하고 확장하는 거대하고 활발한 커뮤니티의 혜택을 받습니다.
- 다양한 사용 사례: Kafka는 로그 집계, 실시간 분석, 이벤트 소싱, 메시징 및 메트릭 수집에 사용됩니다.
- 대형 데이터 생태계와의 호환성: Kafka는 하둡, 스파크, 엘라스틱서치와 같은 기술과 잘 통합되어 종합적인 데이터 파이프라인을 용이하게 합니다.
- 간소화된 데이터 처리: Kafka는 비동기 처리를 통해 시스템 성능과 신뢰성을 향상시킵니다.

LinkedIn, Netflix, Uber, Airbnb와 같은 주요 기술 기업은 Kafka를 강력하고 확장 가능하며 효율적인 실시간 데이터 처리 능력으로 사용하며, 생산 환경에서의 효과를 입증하고 있습니다.

경험이 풍부한 개발자로서, 면접에서 Kafka 지식에 대한 강한 수요를 알아챘습니다. 다른 분들이 준비하는 데 도움이 되도록, Kafka에 관한 질문 및 답변 세션을 게시할 예정입니다. 만나서 질문과 의견을 자유롭게 남겨주세요!

## Kafka의 주요 구성 요소는 무엇인가요? (프로듀서, 컨슈머, 브로커, 토픽, 주키퍼)

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

프로듀서: 이들은 데이터 스트림을 Kafka에 발행하는 애플리케이션이나 서비스입니다. 특정 카테고리나 피드인 토픽에 메시지를 작성합니다 (아래에서 설명됨). 프로듀서들은 소비자와 직접 상호작용하지 않고 Kafka로 데이터를 발행합니다.

컨슈머: 이들은 토픽을 구독하고 프로듀서가 발행한 데이터 스트림을 읽는 애플리케이션이나 서비스입니다. 컨슈머들은 메시지 처리 방법을 조정하기 위해 컨슈머 그룹에 속할 수 있습니다 (아래 설명됨).

토픽: 토픽을 데이터 스트림을 위한 명명된 카테고리나 피드로 생각해보세요. 프로듀서들은 특정 토픽에 데이터를 발행하고, 컨슈머들은 해당 토픽을 구독하여 데이터를 받습니다.

브로커: 이들은 Kafka 클러스터를 형성하는 서버들입니다. 단일 Kafka 클러스터는 함께 작동하는 하나 이상의 브로커를 가질 수 있습니다. 브로커들은 프로듀서가 발행한 메시지를 저장하고 이를 컨슈머에 제공합니다. 메시지 복제, 파티션 관리 (아래에서 설명됨) 및 전체 Kafka 클러스터 조정을 담당합니다.

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

Zookeeper: 카프카 클러스터의 상태를 관리하는 외부 서비스인 Zookeeper. 토픽, 브로커, 소비자 그룹을 추적하여 클러스터 내에서 모든 것이 원활하게 작동하도록 합니다. 조정에 중요하지만, Zookeeper 자체는 실제 데이터 메시지를 저장하지는 않습니다.

이러한 구성 요소는 실시간 데이터 파이프라인을 처리하는 강력하고 확장 가능한 플랫폼으로 함께 작동합니다.

## Kafka는 내구성과 오류 허용을 어떻게 보장하나요? (복제)

Kafka는 주로 복제라는 기술을 통해 데이터 내구성과 오류 허용을 보장합니다. 작동 방식은 다음과 같습니다:

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

- 토픽 및 파티션: Kafka 토픽은 데이터 스트림의 범주입니다. 내부적으로 각 토픽은 파티션이라고 하는 작은 순서대로 정렬된 세그먼트로 나뉩니다. 이 파티션은 병렬 처리와 확장성을 가능하게 합니다.
- 복제 팩터: 각 파티션은 Kafka 클러스터 내의 여러 브로커에 걸쳐 복제됩니다. 파티션의 복사본 수는 복제 팩터라는 구성 매개변수에 의해 결정됩니다. 더 높은 복제 팩터는 더 큰 장애 허용성을 보장합니다.
- 리더와 팔로워: 파티션의 복사본 중 하나의 브로커가 리더로 지정됩니다. 리더는 프로듀서로부터 쓰기를 받아들이고, 이를 다른 복제본인 팔로워에게 복제합니다.
- 데이터 지속성: 모든 브로커는 데이터(메시지)를 디스크에 지속합니다. 이를 통해 브로커가 실패하더라도 데이터가 손실되지 않습니다. 팔로워는 리더와 파티션 데이터의 동기화를 유지합니다.
- 리더 장애 및 복구: 리더 브로커가 실패하면 Kafka는 자동으로 리더 선출 프로세스를 트리거합니다. 동기화된 팔로워 중 하나가 새 리더가 되며, 데이터 복제가 계속됩니다. 소비자는 새로운 리더에서 데이터를 최소한의 중단으로 계속 읽을 수 있습니다.

프로듀서 확인: 추가로, Kafka는 내구성 보증에 영향을 주는 프로듀서 확인 설정을 제공합니다:

- acks=all: 이 설정은 최대 내구성을 보장합니다. 프로듀서는 모든 복제본으로부터 확인을 받기 전에 쓰기를 성공으로 간주합니다.
- acks=1 (기본값): 프로듀서는 리더 복제본만 확인을 받기를 기다립니다. 이는 내구성과 성능 사이의 균형을 제공합니다.

복제 및 확인 전략을 통해 Kafka는 브로커 장애로 인한 데이터 손실을 방지합니다. 브로커가 다운되어도 해당 데이터는 복제본에 유지되어 시스템이 복구하고 운영을 계속할 수 있습니다.

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

카프카의 내결함 허용성에 기여하는 다른 요소들이 있지만, 복제가 핵심 메커니즘입니다.

## 카프카에서 리더 및 팔로워 복제본의 차이점을 설명해주세요.

데이터가 토픽으로 구성되고 확장성을 위해 파티션으로 더 나눠지는 카프카 클러스터에서, 리더 및 팔로워 복제본은 데이터 가용성과 내결함성을 보장하기 위해 중요한 역할을 합니다. 이들 간 주요 차이점은 다음과 같습니다:

리더 복제본:

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

- 책임:
  - 배정된 파티션에 대한 생산자로부터 쓰기(새 메시지)를 수락합니다.
  - 받은 메시지를 같은 파티션에 대한 모든 팔로워 레플리카에 복제합니다.
  - 안전하게 저장되고 소비할 수 있는 메시지의 지점을 나타내는 확정 오프셋을 결정합니다.
  - 소비자로부터의 읽기 요청을 처리합니다 (일반적으로 소비자는 아래에서 설명하는 동기화된 레플리카 세트와 주로 상호 작용합니다).
- 선출: 리더는 파티션 내의 레플리카 중에서 선출됩니다. 이 선출은 브로커 시작 시 자동으로, 리더의 실패 시 또는 리더가 심각하게 뒤처지는 경우에 발생합니다.

Follower 레플리카:

- 책임:
  - 리더로부터 복제된 메시지를 수동으로 소비합니다.
  - 받은 메시지를 자체 로그에 적용하여 해당 파티션 데이터의 사본을 리더와 동기화 상태로 유지합니다.
  - 복제가 성공한 경우 리더를 승인합니다.
- 중요성: 팔로워는 리더의 실패 시 데이터 가용성을 보장하고 중복성을 제공합니다. 리더 선출이 발생할 때 팔로워는 파티션 기능을 유지하기 위해 새 리더가 될 수 있습니다.

## 소비자 그룹이란 무엇이며 어떻게 작동합니까?

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

소비자 그룹은 Kafka에서 데이터 스트림의 병렬 처리를 가능하게 하고 각 메시지가 그룹 내 정확히 하나의 소비자에 전달되도록 하는 기본적인 개념입니다. 다음은 그들이 작동하는 방식입니다:

소비자 그룹 지정하기:

- 소비자들은 group.id라는 고유 식별자를 사용하여 그룹화될 수 있습니다. 동일 그룹에 속한 소비자들은 소비자 그룹으로 식별됩니다.
- 소비자 인스턴스는 구성 중에 그룹 소속을 지정합니다.

부하 분산 및 병렬 처리:

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

- 소비자 그룹이 주제를 구독하면 해당 주제의 파티션은 그룹 내의 소비자들 사이에 자동으로 나눠집니다. 이 분배는 소비자와 파티션의 수에 기반하여 균형을 유지하도록 지능적으로 이루어집니다.
- 그룹 내 각 소비자는 할당된 파티션에서 메시지를 처리하는 책임이 있습니다. 이 병렬 처리를 통해 소비자 그룹이 대량의 데이터를 효율적으로 처리할 수 있습니다.

소비자 독점성:

- 소비자 그룹의 중요한 측면은 그룹 내 하나의 소비자에게만 메시지가 전달된다는 것입니다. 이를 통해 중복 처리가 방지되고 데이터 일관성이 보장됩니다. Kafka는 각 소비자 그룹에 대한 상태를 유지하고 현재 파티션 할당을 추적함으로써 이를 달성합니다.

소비자 리밸런싱:

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

- 파티션의 분배는 소비자들 사이에서 동적으로 변경될 수 있습니다. 이는 다음과 같은 시나리오에서 발생합니다:
  - 소비자가 그룹에 가입하거나 나가는 경우.
  - 브로커의 장애로 인해 파티션 재할당이 필요한 경우.
  - 카프카가 이러한 상황에서 소비자 리밸런싱이라는 프로세스를 트리거합니다. 리밸런싱은 나머지 소비자들 사이에서 파티션 할당을 재조정하여 균형있는 처리를 유지하는 과정을 말합니다.

소비자 그룹의 장점:

- 병렬 처리: 대용량 데이터 스트림을 효율적으로 처리할 수 있습니다.
- 확장성: 그룹에서 소비자를 추가하거나 제거함으로써 쉽게 소비자 처리를 확장할 수 있습니다.
- 내고장성: 소비자가 실패하면 해당 소비자의 파티션이 그룹 내 다른 소비자로 재할당되어 데이터 처리가 계속됩니다.
- 정확히 한 번 전달 (구성에 따라): 올바르게 구성된 경우, 소비자 그룹은 그룹 내의 각 메시지가 정확히 한 번만 정확히 한 소비자에게 전달되도록 보장할 수 있습니다.

소비자 그룹 사용 사례:

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

소비자 그룹은 여러 소비자 간에 데이터 처리를 병렬화해야 하는 다양한 시나리오에서 가치가 있습니다. 예를 들어 다음과 같은 경우에 사용됩니다:

- 로그 집계: 여러 소비자가 중앙 주제에서 로그 데이터를 병렬로 처리할 수 있습니다.
- 스트림 처리: 소비자 그룹은 데이터 스트림을 실시간 분석 작업에 배포하는 데 사용될 수 있습니다.
- 마이크로서비스 통신: 소비자 그룹은 관련 주제를 구독하고 메시지를 동시에 처리할 수 있도록 허용하여 마이크로서비스 간의 통신을 용이하게 합니다.

## Kafka에서 오프셋이란 무엇이며 어떻게 커밋되는가?

Kafka에서 오프셋은 소비자 그룹 또는 각각의 소비자가 토픽 파티션 내에서 진행 상황을 추적하는 포인터 역할을 합니다. 이것들은 본질적으로 각 파티션 내에서 0부터 시작하는 순차적으로 할당된 정수로, 각 메시지에 대한 진행 상황을 나타냅니다. 여기에 오프셋에 대한 더 깊은 내용과 커밋 방법이 나와 있습니다.

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

**Offset 이해:**

- 파티션별 추적: 오프셋은 특정한 컨슈머 그룹이나 컨슈머, 그리고 토픽 내 특정 파티션에 대한 것입니다. 이를 통해 각 컨슈머는 읽은 각 파티션에 대해 독립적으로 진행 상황을 추적할 수 있습니다.
- 소비 재개: 오프셋은 중단 후에 소비를 재개하는 데 중요한 역할을 합니다. 컨슈머가 재시작하거나 그룹에 합류할 때, 지정된 파티션에서 메시지를 읽기 시작할 위치를 결정하기 위해 커밋된 오프셋을 사용합니다. 이를 통해 컨슈머는 이미 처리한 메시지를 다시 처리하지 않게 됩니다.

**오프셋 커밋:**

- 컨슈머 책임: 컨슈머는 주기적으로 오프셋을 커밋하는 책임이 있습니다. 이는 컨슈머가 성공적으로 처리한 마지막 메시지에 대해 Kafka에 알립니다. 오프셋을 커밋하는 다양한 전략이 있으며, 각각의 전략은 메시지 전달 의미론(메시지가 전달되는 방식에 대한 보장)을 가지고 있습니다:
  - 적어도 한 번: 이것은 기본 설정입니다. 컨슈머는 메시지 처리 후 오프셋을 커밋합니다. 그러나 컨슈머가 오프셋을 커밋하기 전에 충돌하면 메시지가 다시 전달될 수 있어 재시작 시 중복 처리 가능성이 있습니다.
  - 최대 한 번: 컨슈머는 메시지를 받자마자 오프셋을 커밋합니다. 처리가 실패하면 메시지가 다시 시도되지 않을 수 있어 데이터 손실 가능성이 있습니다.
  - 정확히 한 번 (트랜잭션): 이것은 가장 복잡하지만 각 메시지가 정확히 한 번 전달 및 처리됨을 보장합니다. Kafka 트랜잭션과 Kafka Streams API를 사용해야 합니다.
- 오프셋 커밋 과정: 일반적으로 컨슈머는 Kafka가 유지하는 "\_\_consumer_offsets"라는 특별한 내부 토픽에 오프셋을 커밋합니다. 이 토픽은 모든 컨슈머 그룹과 파티션에 대한 커밋된 오프셋을 저장합니다.

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

오프셋을 커밋하는 중요성:

- 진행 상황 추적: 커밋된 오프셋을 통해 소비자는 장애 또는 재시작 후 올바른 지점에서 처리를 계속할 수 있습니다.
- 중복 방지 (at-least-once와 함께): at-least-once 전략을 사용하여 정기적으로 커밋하면 이미 소비자가 처리한 메시지의 재처리를 방지할 수 있습니다.

오프셋 커밋 전략 선택:

오프셋 커밋 전략의 선택은 응용 프로그램의 요구 사항에 따라 달라집니다. 데이터 손실이 절대 허용되지 않는 경우, 정확히 한 번의 전달이 필요하며 (더 복잡하지만) 구현해야 합니다. 어느 정도의 메시지 중복은 허용되는 경우, at-least-once 전략이 더 간단한 접근 방식입니다.

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

## Kafka가 높은 처리량과 낮은 지연 시간을 어떻게 달성하는가?

Kafka는 설계 선택과 기술의 조합을 통해 높은 처리량과 낮은 지연 시간을 달성합니다. 여기에 중요한 요소 몇 가지가 있습니다:

확장성과 병렬성:

- 분산 아키텍처: Kafka의 분산 아키텍처는 클러스터 내 여러 브로커에 부하를 분산시켜 대규모 데이터 양을 처리할 수 있도록 합니다. 이를 통해 필요에 따라 더 많은 머신을 추가함으로써 수평 확장이 가능합니다.
- 분할: Kafka의 주제는 파티션이라는 더 작은 단위로 나누어집니다. 프로듀서는 이러한 파티션에 병렬로 메시지를 발행할 수 있으며, 이는 전체 처리량을 향상시킵니다. 소비자는 할당된 파티션에서 메시지를 동시에 소비함으로써 처리를 병렬화할 수도 있습니다.

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

효율적인 데이터 저장 및 액세스:

- Append-only Logs(추가 전용 로그): 데이터는 각 브로커의 추가 전용 로그에 저장됩니다. 이렇게 하면 쓰기 작업이 간소화되고 무작위 디스크 액세스의 부하를 피할 수 있습니다. 새로운 데이터가 로그의 끝에 씌여지기 때문에 효율적으로 액세스할 수 있습니다.
- 배칭: 프로듀서는 여러 메시지를 브로커로 보내기 전에 함께 배칭할 수 있습니다. 이렇게 하면 네트워크 왕복 횟수가 줄어들고 데이터 전송의 효율성이 향상됩니다.
- Zero-Copy 처리: 가능한 경우에는 Kafka가 불필요한 데이터 복사를 피하기 위한 zero-copy 기술을 활용합니다. 이렇게 하면 CPU 부하가 줄어들고 처리 속도가 향상됩니다.
- OS 기능 활용: Kafka는 Linux 페이지 캐시를 활용하여 자주 액세스되는 데이터를 메모리에 저장함으로써 디스크 I/O를 줄이고 소비자가 더 빠르게 메시지를 검색할 수 있도록 합니다.

분리된 통신:

- 프로듀서 및 소비자: 프로듀서와 소비자는 독립적으로 작동합니다. 프로듀서는 소비자가 구독한 것을 알 필요 없이 주제에 메시지를 발행합니다. 이 분리는 프로듀서가 소비자 확인을 기다리지 않기 때문에 전체 대기 시간이 줄어듭니다.

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

비동기 통신:

- 생산자, 중개업자 및 소비자간의 통신은 비동기적으로 이루어집니다. 이는 생산자가 중개업자가 수신 확인을 기다리는 동안 블록되지 않고, 소비자가 새 메시지를 기다리는 동안 블록되지 않음을 의미합니다. 이는 전반적으로 응답성을 향상시킵니다.

소비자 중심 최적화:

- 미리 가져오기: 소비자는 로컬 버퍼로 구성된 메시지를 사전에 가져올 수 있습니다. 이는 후속 메시지 가져오기의 지연 시간을 줄이며 데이터가 메모리에 이미 사용 가능하게 함.
- 효율적인 소비자 그룹 관리: 소비자 그룹은 다시 조정 알고리즘을 활용하여 파티션을 효율적으로 소비자들 사이에 분배합니다. 이로써 균형 잡힌 부하 및 전반적인 처리 시간을 줄입니다.

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

## Kafka의 실제 사용 사례는 무엇인가요? (로그 집계, 마이크로서비스 통신)

아파치 카프카의 실제 사용 사례 중 일부를 살펴보면, 실시간 데이터 파이프라인을 처리하는 능력을 강조할 수 있습니다:

로그 집계 및 모니터링:

- Kafka는 다양한 분산 애플리케이션, 서비스 및 마이크로서비스에서 로그를 수집하는 데 뛰어납니다. 이러한 로그는 Kafka의 특정 토픽으로 스트림으로 발행됩니다.
- 중앙 집계된 로그를 사용하면 애플리케이션의 실시간 분석, 문제 해결 및 성능 모니터링이 가능해집니다. ELK 스택(Elasticsearch, Logstash, Kibana)과 같은 도구를 Kafka와 통합하여 로그 데이터를 소비하고 시각화하여 더 깊은 인사이트를 얻을 수 있습니다.

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

마이크로서비스 통신:

- 카프카는 마이크로서비스 아키텍처의 중추신경계 역할을 합니다. 마이크로서비스는 이벤트나 데이터 업데이트를 카프카의 관련 토픽에 발행할 수 있습니다.
- 다른 마이크로서비스는 이러한 토픽을 구독하고 발행된 이벤트에 반응하여 서비스 간에 비동기적이고 느슨하게 결합된 통신을 가능케 합니다. 이는 마이크로서비스 배포에서 확장성과 유연성을 증진시킵니다.

스트림 처리와 분석:

- 카프카는 대량 데이터 스트림을 처리할 수 있는 능력으로 실시간 분석 애플리케이션에 이상적입니다.
- 아파치 플링크나 아파치 스파크 스트리밍과 같은 스트림 처리 프레임워크를 카프카와 통합하여 토픽에서 데이터 스트림을 소비하고 실시간 연산, 필터링 또는 변환을 수행할 수 있습니다.
- 이를 통해 애플리케이션은 사기 탐지, 이상 분석 또는 추천 엔진을 위한 실시간 데이터 통찰에 반응할 수 있습니다.

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

IoT 데이터 수집 및 처리:

- 사물 인터넷(IoT) 영역에서 Kafka는 센서와 장치가 생성하는 대량 및 고속의 데이터를 효율적으로 처리할 수 있습니다.
- 센서 데이터는 메시지로 Kafka 토픽에 발행될 수 있어 이 데이터의 실시간 처리, 집계 및 분석이 가능합니다.
- 이는 예측 현상 유지, 원격 모니터링 또는 IoT 센서 데이터의 실시간 시각화에 활용될 수 있습니다.

이벤트 소싱:

- Kafka는 마이크로서비스 및 응용 프로그램을 위한 중앙 이벤트 저장소로 사용될 수 있습니다. 상태 변경 또는 작업을 나타내는 이벤트는 Kafka 토픽에 메시지로 발행됩니다.
- 이 이벤트 로그는 분산 시스템에서 언제든지 응용 프로그램 상태를 재구성하거나 최종 일관성 패턴을 구현하는 데 사용될 수 있습니다.

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

**실시간 사기 탐지:**

- 카프카의 고 처리량과 낮은 지연 시간은 실시간 사기 탐지 시스템을 구축하기에 적합합니다.
- 거래 데이터는 카프카 토픽으로 스트리밍될 수 있으며, 스트림 처리 응용 프로그램은이 데이터를 실시간으로 분석하여 의심스러운 패턴이나 잠재적인 사기 활동을 식별할 수 있습니다.

이것들은 몇 가지 예시일 뿐이며, 카프카의 사용 사례는 기업이 실시간 데이터 파이프라인 및 응용 프로그램을 위한 능력을 활용함에 따라 계속 발전하고 있습니다.

## 카프카 소비자가 뒤처지는 상황을 어떻게 처리하시겠어요? (소비자 리밸런싱, 구성 조정)

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

카프카 소비자가 생산자를 따라잡지 못하고 메시지 처리에서 뒤처지기 시작하는 상황을 해결하는 방법을 다음과 같이 제시해 드립니다.

원인 파악하기:

- 소비자 랙 관찰: 먼저, 카프카 소비자 모니터링 도구나 내장된 소비자 그룹 랙 정보를 활용하여 어떤 소비자 그룹과 파티션이 랙을 겪고 있는지 식별해 보세요.
- 소비자 성능 분석: 문제가 되는 소비자의 성능 메트릭스인 CPU, 메모리 사용량 및 처리 시간을 조사하여 소비자 애플리케이션 자체에서 발생하는 잠재적인 병목 현상을 파악해 보세요.

소비자 측 해결책:

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

- 소비자 코드 최적화: 소비자 코드를 검토하고 개선할 부분을 식별합니다. 이 작업에는 다음이 포함될 수 있습니다:
  - 메시지 처리 논리를 최적화하여 메시지당 처리 시간을 줄입니다.
  - 여러 메시지를 한꺼번에 처리하기 위해 메시지 처리를 일괄 처리합니다.
- 소비자 병렬화 증가: 소비자 응용 프로그램이 처리할 수 있다면, 소비자 그룹 내의 소비자 인스턴스 수를 증가시키는 것을 고려해보세요. 이렇게 하면 작업이 분산되어 뒤처진 작업을 처리할 수 있습니다.
- 소비자 페치 크기 조정: 소비자 페치 크기는 각 요청에서 브로커로부터 검색되는 데이터 양을 제어합니다. 페치 크기를 늘림으로써(합리적인 한계 내에서) 처리량을 향상시키고 지연을 줄일 수 있습니다.

카프카 구성 조정:

- 소비자 리밸런스: 소비자 그룹 내에서 부분적으로 파티션이 분산되어 지연이 발생하는 경우, Kafka 소비자 그룹 관리 도구를 사용하여 소비자 리밸런스를 수동으로 트리거할 수 있습니다. 이렇게 하면 파티션이 소비자 사이에서 재분배되어 과도하게 작업이 부담스럽게 된 소비자의 지연이 완화될 수 있습니다.
- 오토 오프셋 리셋: 극단적인 경우, 지연되는 파티션용으로 소비자 오프셋을 재설정해야 할 수도 있습니다. 그러나 이 접근 방식은 메시지 중복 (at-least-once 의미론)이나 데이터 손실 (at-most-once 의미론)로 이어질 수 있으므로 신중하게 사용해야 합니다.

## 카프카에서 한 소비자로 중복 메시지 소비 방지하기를 선택하는 방법?

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

정확히 한 번만 메시지가 전달되고 소비자에 의해 한 번만 처리되는 정확히 한 번의 의미를 달성하려면 Apache Kafka에서는 적어도 한 번 또는 최대 한 번 전달보다 더 많은 노력이 필요합니다. Kafka에서 단일 소비자에 의한 중복 처리를 피하기 위한 두 가지 주요 방법은 다음과 같습니다:

- Kafka Streams API를 사용한 트랜잭션 소비자:

- 이것은 정확히 한 번의 처리 보장을 위한 추천 접근 방식입니다. Kafka Streams는 Kafka Consumer를 기반으로 구축된 고수준 API로 스트림 처리 작업을 간단화합니다.
- 이는 Kafka 트랜잭션을 활용하여 프로듀서에 의한 메시지 쓰기와 소비자의 오프셋 커밋을 원자적 단위로 처리합니다. 쓰기 또는 커밋 중 하나가 실패하면 전체 트랜잭션이 롤백되어 부분 처리와 잠재적인 중복을 방지합니다.

프로세스의 분해는 다음과 같습니다:

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

- 소비자는 메시지를 소비하기 전에 Kafka 트랜잭션을 시작합니다.
- 소비자는 메시지를 처리하고 필요한 작업을 수행합니다.
- 처리가 성공하면, 소비자는 트랜잭션 내에서 오프셋을 커밋합니다.
- Kafka는 트랜잭션 내의 모든 작업이 성공하거나 모두 실패하도록 보장하여 중복을 방지합니다.
- 기억해둘 중요한 점들:
- 이 접근 방식은 메시지 소비를 위해 Kafka Streams API를 사용해야 합니다.
- 한 번만 적용되는 의미론은 생산자 측의 트랜잭션 기능이 필요합니다.

2. 수동 오프셋 관리를 통한 멱등성:

- 이 방법은 소비자 응용 프로그램 내에서 멱등성을 구현하고 오프셋을 수동으로 관리하는 것을 포함합니다. 멱등성은 의도하지 않은 부작용 없이 작업을 여러 번 반복할 수 있음을 보장합니다.

여기에 일반적인 아이디어가 있습니다:

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

- 사용자는 받은 각 메시지에 고유 식별자(동질성 키)를 할당합니다.
- 메시지를 처리하기 전에 사용자는 동일한 동질성 키를 가진 메시지를 이미 처리했는지 확인합니다. 이를 위해 처리된 키를 데이터베이스나 분산 캐시에 저장하여 확인할 수 있습니다.
- 메시지가 새로운 경우(고유 키), 사용자는 처리하고 나중을 위해 키를 저장합니다.
- 메시지가 중복인 경우(키가 이미 존재), 사용자는 더 이상 처리하지 않고 삭제합니다.
- 성공적인 처리 후에 사용자는 오프셋을 커밋합니다.

고려해야 할 주요 사항:

- 동질성 논리를 구현하는 것은 사용자 애플리케이션에 복잡성을 추가합니다.
- 동질성 키를 저장하고 관리하기 위한 적합한 메커니즘을 선택해야 합니다.
- 수동으로 오프셋을 커밋하면 데이터 손실이나 중복을 피하기 위해 신중히 처리해야 합니다.

적절한 방법 선택하기:

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

- 트랜잭션 소비자(카프카 스트림 API)는 간단하고 정확히 한 번 배송이 보장되는 접근 방식으로 일반적으로 권장됩니다.
- 수동 오프셋 관리와 이덤포턴스는 대안일 수 있지만, 이의 효율을 위해서 더 많은 개발 노력이 필요하며, 이덤포턴시 키 및 오프셋을 관리하는 데 잠재적인 복잡성을 도입할 수 있습니다.

## 동일한 메시지를 다른 컨슘어에서 듣지 않도록 어떻게 보장할까요?

기본적으로, 컨슈머 그룹 내의 카프카 컨슈머는 동일한 메시지를 듣지 않습니다. 카프카는 이를 컨슈머 그룹과 오프셋 관리라는 개념을 통해 실현합니다. 작동 방식은 다음과 같습니다:

컨슈머 그룹:

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

- 소비자는 "group.id"라는 고유 식별자를 사용하여 그룹화될 수 있습니다. 동일한 그룹에 속하는 모든 소비자는 소비자 그룹을 형성합니다.
- 각 소비자 인스턴스는 구성 중에 해당하는 그룹 소속을 지정합니다.

파티션 및 오프셋 추적:

- Kafka의 토픽은 파티션이라는 작은 단위로 분할됩니다.
- 각 소비자 그룹은 가입한 각 파티션에 대한 오프셋 기록을 유지합니다. 오프셋은 파티션 내 메시지의 고유 식별자로, 본질적으로 0부터 시작하는 카운터입니다.
- 소비자 그룹이 토픽을 구독하면 Kafka는 파티션을 그룹의 소비자들 사이에 분산시키기 위해 소비자 리밸런싱을 수행합니다. 이를 통해 고률한 부하 분산이 보장됩니다.

독점 청취:

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

- 각 주제 내의 각 파티션은 한 번에 그룹 내의 하나의 소비자에게 할당됩니다. 이는 그룹 내에서 중복 처리를 방지합니다.
- 소비자가 메시지를 처리하는 동안 주기적으로 오프셋을 커밋합니다. 이를 통해 Kafka에게 각 파티션의 마지막으로 성공적으로 처리한 메시지를 알립니다.

장점:

- 중복 방지: 파티션을 배정하고 오프셋을 추적함으로써 Kafka는 각 메시지가 그룹 내의 하나의 소비자에게만 전달되도록 보장하여 중복 처리를 방지합니다.
- 확장성: 그룹에서 소비자를 추가하거나 제거하여 소비자 처리를 쉽게 확장할 수 있습니다. Kafka는 파티션을 자동으로 리밸런싱하여 균형 잡힌 부하를 유지합니다.

예시:

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

두 명의 소비자(소비자 A 및 소비자 B)가 3개 파티션을 가진 주제에 가입한 소비자 그룹을 상상해보세요. Kafka는 파티션을 다음과 같이 분배할 수 있습니다:

- 소비자 A: 파티션 0 및 1 수신
- 소비자 B: 파티션 2 수신

이렇게 함으로써 각 메시지가 그룹 내 하나의 소비자에게만 전달됩니다.

중요 참고사항:

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

카프카는 소비자 그룹 내에서 중복 처리를 방지하지만, 동일한 주제에 두 개의 소비자 그룹이 모두 구독되어 있다면 메시지가 여러 소비자 그룹으로 전달될 수 있습니다. 소비자 그룹에 관계없이 메시지가 한 번만 처리되도록 보장해야 한다면, 이벤트 중복성과 같은 기술을 활용하여 소비자 내에서 추가 로직을 구현해야 합니다 (이전 질문에 다루어졌습니다).

## 소비자 측의 장애 복구 방법

카프카에서 소비자 측의 장애를 복구하는 것은 데이터 손실 없이 메시지 처리가 원활하게 계속되도록 보장하기 위해 몇 가지 주요 전략을 활용해야 합니다. 다음은 취할 수 있는 접근 방법에 대한 설명입니다:

1. 카프카 오프셋 커밋과 소비자 리발란싱 활용하기:

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

- 오프셋 커밋: 카프카는 소비자 측 오프셋 커밋을 활용하여 진행 상황을 추적합니다. 소비자들은 주기적으로 오프셋을 커밋하여 구독 중인 각 파티션에서 마지막으로 성공적으로 처리한 메시지를 나타냅니다.
- 소비자 리밸런싱: 소비자가 실패하거나 새로운 소비자가 그룹에 가입하면, 카프카는 소비자 리밸런싱을 트리거합니다. 이 과정은 나머지 소비자들 사이에서 파티션을 재분배합니다.

복구 프로세스:

- 다시 시작할 때, 실패한 소비자는 이전에 책임지고 있던 파티션에 대한 커밋된 오프셋을 검색합니다.
- 카프카는 해당 파티션을 다시 소비자에게 할당하여 리밸런싱 동안 (같은 소비자 그룹에 다시 가입할 경우) 이어서 처리합니다.
- 소비자는 커밋된 오프셋에서 메시지 처리를 계속하며, 장애로 인한 데이터 손실이 발생하지 않도록 합니다.

2. 소비자에서 오류 처리 및 재시도 구현:

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

- 에러 처리: 소비자 애플리케이션에서 예외나 처리 실패를 감지하기 위한 견고한 에러 처리 메커니즘을 갖추는 것이 중요합니다.
- 재시도 논리: 에러가 발생할 때 소비자는 메시지 처리를 다시 시도하는 재시도 논리를 구현해야 합니다. 이는 일시적 에러의 경우 브로커를 과도하게 사용하지 않도록 백오프 전략을 사용하여 재시도하는 것을 포함할 수 있습니다.
- 데드 레터 큐 (선택 사항): 중요한 메시지나 지속적인 오류의 경우 데드 레터 큐 (DLQ)를 구현해야 합니다. 실패한 메시지는 DLQ로 전송되어 수동 개입이나 후속 처리 시도가 가능합니다.

3. 카프카 컨슈머 오프셋 관리 도구 활용:

- 카프카는 컨슈머 오프셋을 수동으로 관리하기 위한 도구와 API를 제공합니다. 이는 특정 시나리오에서 유용할 수 있습니다:
- 오프셋 재설정: 극도의 경우에는 컨슈머 그룹이나 특정 파티션의 오프셋을 수동으로 재설정해야 할 수 있습니다. 그러나 이를 신중하게 사용해야 하며, 이로 인해 메시지 중복(at-least-once 의미론)이나 데이터 손실(at-most-once 의미론)이 발생할 수 있습니다.
- 컨슈머 일시 중지/재개: 유지보수나 디버깅 목적으로 일시적으로 컨슈머나 컨슈머 그룹을 일시 중지할 수 있는 도구를 사용할 수 있습니다. 이를 통해 메시지 전달과 오프셋 관리를 제어할 수 있습니다.

4. 올바른 오프셋 커밋 전략 선택:

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

- Kafka의 기본 옵셋 커밋 전략은 적어도 한 번은 입니다. 이는 메시지가 최소한 한 번은 전달되도록 보장하지만, 소비자가 오프셋을 커밋하기 전에 실패할 경우 중복이 발생할 수 있습니다.
- 더 엄격한 전달 보증을 위해 Kafka Transactions 및 Kafka Streams API를 사용하여 정확히 한 번만 전달되는 의미론을 고려하십시오. 이 방식은 각 메시지가 한 번만 전달되고 처리되도록 보장하지만, 더 복잡한 구성 및 개발 노력이 필요합니다.

## 스프링 부트 앱에서 Kafka 구성하는 방법은?

- Kafka 종속성 추가:

필요한 Kafka 종속성을 pom.xml(Maven용)이나 build.gradle(Gradle용) 파일에 추가하십시오. Spring Boot은 핵심 Kafka 종속성이 포함된 편리한 spring-kafka 스타터를 제공합니다.

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
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-kafka</artifactId>
</dependency>
```

2. Kafka 속성 구성하기:

Spring Boot는 application.yml 또는 application.properties와 같은 애플리케이션 프로퍼티 파일을 사용하여 Kafka 속성을 편리하게 구성할 수 있는 방법을 제공합니다. 여기에 몇 가지 중요한 속성이 있습니다:

- spring.kafka.bootstrap-servers: 이 속성은 Kafka 브로커 주소의 쉼표로 구분된 목록을 지정합니다.
- spring.kafka.consumer.group-id: 이 속성은 응용 프로그램의 소비자 그룹 ID를 정의합니다. 동일한 그룹 ID로 동일한 주제에 구독된 소비자는 소비자 그룹을 형성하고 메시지를 효율적으로 병렬 처리합니다.
- spring.kafka.producer.key-serializer: 이 속성은 응용 프로그램에서 생성한 메시지 키의 직렬화에 사용되는 직렬화기를 지정합니다. 기본적으로 StringSerializer가 사용됩니다. JSON 키의 경우와 같이 메시지 키 데이터 유형에 따라 다른 직렬화기를 선택할 수 있습니다 (예: JSON 키의 경우의 경우 JsonSerializer).
- spring.kafka.producer.value-serializer: 이 속성은 응용 프로그램에서 생성한 메시지 값의 직렬화에 사용되는 직렬화기를 정의합니다. 키 직렬화기와 유사하게, 메시지 값 데이터 유형에 따라 적절한 직렬화기를 선택하세요.
- (선택 사항) spring.kafka.consumer.auto-offset-reset: 이 속성은 소비자 그룹이 리밸런스되거나 장애 발생 후 다시 시작할 때 소비자 오프셋이 어떻게 처리되는지를 제어합니다. 기본값은 earliest로, 이는 소비자가 파티션의 처음부터 읽기를 시작함을 의미합니다. 최신 메시지부터 읽기를 시작하려면 latest로 설정할 수 있습니다.

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

3. Kafka 프로듀서와 컨슈머 생성하기:

- 스프링 부트는 Kafka 프로듀서와 컨슈머를 위한 추상화를 제공합니다. @Autowired를 사용하여 주입하고 Kafka 토픽과 상호 작용하는 데 사용할 수 있습니다:

```java
@SpringBootApplication
public class MyKafkaApp {

    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;

    public void sendMessage(String topic, String message) {
        kafkaTemplate.send(topic, message);
    }

    @KafkaListener(topics = "myTopic")
    public void receiveMessage(String message) {
        // 받은 메시지 처리
    }

    // ... (다른 애플리케이션 로직)
}
```

## Kafka 작업 시 개발자들이 자주 하는 일반적인 실수는 무엇인가요?

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

설정 및 사용에 대한 오류:

- 소비자 그룹 이해 부족: 소비자 그룹의 개념과 그들이 작업 부하를 어떻게 분산시키는지를 이해하지 못하면 비효율적인 처리나 중복된 메시지가 발생할 수 있습니다.
- 올바르지 않은 오프셋 커밋 전략: 적절하지 않은 오프셋 커밋 전략(적어도 한 번, 최대 한 번 또는 정확히 한 번)을 선택하면 데이터 손실이나 메시지 중복이 발생할 수 있습니다.
- 적절하지 않은 직렬화기 선택: 메시지 키와 값의 데이터 유형에 맞는 적합한 직렬화기/역직렬화기를 선택하지 않으면 직렬화 오류나 예기치 않은 동작이 발생할 수 있습니다.
- 불필요한 수동 오프셋 관리: 수동으로 오프셋을 관리하는 것은 오류가 발생하기 쉽고 복잡할 수 있습니다. 가능한 경우 카프카의 자동 오프셋 관리를 활용하십시오.

성능 및 확장성 문제:

- 파티셔닝 무시: 주제 파티셔닝을 효과적으로 활용하지 않으면 모든 그룹의 소비자가 단일 파티션에서 읽기 때문에 성능 병목이 발생할 수 있습니다.
- 충분하지 않은 소비자 병렬성: 그룹 내 소비자가 너무 적으면 처리 지연이 발생할 수 있으며, 특히 대용량 데이터 스트림의 경우에는 더욱 그렇습니다.
- 비효율적인 소비자 코드: 최적화되지 않은 소비자 코드는 메시지 처리 속도를 늦추고 전반적인 처리량을 저해할 수 있습니다.
- 소비자 랙 모니터링 미흡: 소비자 랙을 모니터링하지 않으면 잠재적인 병목 현상이나 부하 분배의 불균형에 대해 알지 못할 수 있습니다.

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

개발 및 오류 처리:

- 정확히 한 번만 Guarantee 무시: 귀하의 애플리케이션이 엄격한 데이터 일관성을 필요로 하는 경우, Kafka Streams 및 트랜잭션을 사용하여 정확히 한 번 전달 보장을 무시하면 데이터 불일치가 발생할 수 있습니다.
- 부족한 오류 처리: 생산자와 소비자의 견고한 오류 처리 메커니즘을 구현하지 않으면 응용 프로그램이 예외와 데이터 손실에 취약해질 수 있습니다.
- 테스트 부족: 특히 오류 시나리오에서 적절한 테스트를 누락하면 Kafka 응용 프로그램의 오류 처리 및 복구 프로세스의 약점이 드러날 수 있습니다.

Kafka 대체 제품 및 비교:

메시지 스트리밍 세계에서 Kafka가 우세한 플레이어이지만 귀하의 특정 요구 사항에 따라 고려할 수 있는 다른 옵션도 있습니다. 여기에는 Kafka와 두 가지 인기 있는 대체 제품인 RabbitMQ 및 Apache Pulsar의 비교가 포함됩니다:

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

RabbitMQ:

- 초점: RabbitMQ는 사용하기 쉽고 유연성으로 유명한 가벼운 메시지 브로커입니다. 메시지 라우팅 및 발행/구독, RPC(원격 프로시저 호출), 팬아웃과 같은 메시지 교환 패턴에서 우수한 성과를 내며, 애플리케이션 간 유연한 통신을 지원합니다.

장점:

- 간단함: 간단하게 설정하고 관리하고 사용할 수 있어 복잡하지 않은 메시지 전달 요구에 적합합니다.
- 가벼움: Kafka와 비교했을 때 더 작은 풋프린트로 리소스가 제한된 환경에 이상적입니다.
- 유연성: 다양한 메시지 교환 패턴을 지원하여 다양한 통신 시나리오에 적합합니다.
- 성숙하고 안정적인: 큰 커뮤니티와 포괄적인 실전 테스트로 지탱되어 안정성이 뛰어납니다.

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

약점:

- 확장성: Kafka보다 수평적으로 확장하는 데 있어서 극도로 높은 메시지 양을 다루는 데 비교적 부족할 수 있습니다.
- 처리량: 아키텍처로 인해 매우 높은 처리량 데이터 스트림에서 고난을 겪을 수 있습니다.
- 제한된 스트림 처리: Kafka Streams나 Pulsar Functions과 비교하여 내장된 스트림 처리 기능이 부족합니다.
- 사용 사례:
- 작업 대기열: RabbitMQ는 작업 대기열을 관리하고 애플리케이션에서 백그라운드 작업을 트리거하는 데 적합합니다.
- 마이크로서비스 통신: RabbitMQ는 마이크로서비스 간 가벼운 통신 및 데이터 교환에 사용될 수 있습니다.
- 통합: RabbitMQ는 서로 다른 응용 프로그램 및 시스템 간 통합을 구현하는 데 좋은 선택입니다.

Apache Pulsar:

- 초점: Pulsar는 상대적으로 새로운 오픈 소스 메시지 스트리밍 플랫폼으로 고성능, 확장성 및 낮은 지연 시간을 위해 설계되었습니다. Kafka와 유사한 기능을 제공하지만 멀티테넌시와 지리적 복제에 중점을 둡니다.

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

장점:

- 고성능: 높은 처리량 및 낮은 지연 시간을 지원하여 요구되는 실시간 데이터 파이프라인에 적합합니다.
- 확장성: 대용량 데이터 처리를 위해 수평적으로 높은 확장성을 제공합니다.
- 다중 테넌시(Multi-tenancy): 단일 Pulsar 클러스터를 다중 테넌트 또는 조직간에 안전하게 공유할 수 있습니다.
- 지역 복제(Geo-replication): 지리적으로 분산된 지역 간 데이터 복제를 지원하여 재해 복구 및 글로벌 배포를 가능하게 합니다.
- 스트림 처리(Stream Processing): Kafka Streams와 유사한 내장형 스트림 처리 기능을 제공하여 플랫폼 내에서 데이터 변환 및 분석이 가능합니다.

단점:

- 성숙도: Kafka의 확립된 생태계와 비교할 때, Pulsar는 비교적 신생 프로젝트로 생태계 및 툴 통합이 덜 성숙할 수 있습니다.
- 복잡성: 고급 기능으로 인해 Kafka보다 설정 및 관리가 다소 복잡할 수 있습니다.

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

사용 사례:

- 실시간 분석: 고 처리량과 낮은 지연 시간으로, Pulsar는 실시간 분석 파이프라인을 구축하는 데 좋은 선택지입니다.
- IoT 데이터 스트리밍: Pulsar는 IoT 장치에서 생성된 대량 및 고속 데이터를 효율적으로 처리할 수 있습니다.
- 클라우드 네이티브 배포: 다중 테넌시와 지오-레플리케이션 기능을 갖춘 Pulsar는 클라우드 네이티브 배포에 적합합니다.

적절한 도구 선택:

Kafka, RabbitMQ 및 Pulsar 중에서 최상의 선택을 하려면 특정 요구 사항을 고려해야 합니다. 다음은 간략한 가이드라인입니다:

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

- 간단한 메시지 라우팅 및 가벼운 통신에는 RabbitMQ를 사용하세요.
- 고 처리량과 낮은 지연 시간, 확장성을 갖춘 스트리밍에는 Kafka 또는 Pulsar를 사용하세요.
- 멀티 테넌시, 지오 레플리케이션, 그리고 클라우드 네이티브 배포에는 Pulsar를 사용하세요.
- 기존의 Kafka 생태계와 성숙한 도구를 활용하기 위해서는 Kafka를 사용하세요.

더 많은 구성 옵션, 고급 기능, 그리고 Spring Boot를 활용한 Kafka 애플리케이션 구축을 위한 모범 사례에 대해 알아보려면 Spring Kafka 문서(https://spring.io/projects/spring-kafka)를 참고하세요.

여기까지 읽어 주셔서 감사합니다. 여기에서 모두를 도울 수 있는 실제 Kafka 질문들을 공유할 수 있는 코멘트 섹션에서 스레드를 만들어 볼까요?

# 읽어 주셔서 감사합니다

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

- 👏 이야기에 박수를 보내 주시고 저를 팔로우하세요 👉
- 📰 제 Medium 페이지에서 더 많은 콘텐츠를 읽어보세요 (Java 개발자 면접을 위한 60개의 이야기)

제 책은 여기서 찾아볼 수 있어요:

- 아마존에서 Guide To Clear Java Developer Interview (킨들 북) 또는 Gumroad( PDF 형식)을 확인하세요.
- Gumroad( PDF 형식) 또는 아마존(킨들 전자책)에서 Guide To Clear Spring-Boot Microservice Interview를 확인하세요.
- 🔔 팔로우해 주세요: LinkedIn | Twitter | Youtube
