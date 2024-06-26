---
title: "트위터가 4000억 이벤트를 처리하는 방법을 개선한 비결"
description: ""
coverImage: "/assets/img/2024-06-23-HowTwitterimprovedtheprocessingof400billionevents_0.png"
date: 2024-06-23 22:06
ogImage:
  url: /assets/img/2024-06-23-HowTwitterimprovedtheprocessingof400billionevents_0.png
tag: Tech
originalTitle: "How Twitter improved the processing of 400 billion events."
link: "https://medium.com/@mayank.sharma2796/how-twitter-improved-the-processing-of-400-billion-events-621b3456c9dc"
---

# 소개

Twitter는 약 400억 건의 이벤트를 실시간으로 처리하고 매일 페타바이트의 데이터를 생성합니다. Twitter는 분산 데이터베이스, Kafka, Twitter 이벤트 버스 등과 같은 다양한 이벤트 소스에서 데이터를 소비합니다. 여기에서 블로그의 구현을 찾을 수도 있습니다.

![이미지](/assets/img/2024-06-23-HowTwitterimprovedtheprocessingof400billionevents_0.png)

이 블로그에서는 다음을 이해하려고 노력할 것입니다:

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

- 트위터가 이벤트를 처리하는 방식 및 이 방식에 대한 문제점은 무엇이 있었나요?
- 어떤 비즈니스와 고객 영향으로 인해 트위터가 새 아키텍처로 전환하게 되었나요?
- 새 아키텍처
- 이전 및 새로운 아키텍처의 성능 비교

트위터는 다음과 같은 내부 도구 세트를 보유하고 있습니다.

- Scalding은 트위터가 배치 처리에 사용하는 도구입니다.
- Heron은 트위터의 스트리밍 엔진입니다.
- TimeSeriesAggregator(TSAR)는 배치 및 실시간 처리에 사용됩니다.

이벤트 시스템이 어떻게 발전했는지 자세히 파헤치기 전에, 네 개의 내부 도구에 대해 간략히 알아보겠습니다.

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

- Scalding

스콜딩(Scalding)은 하둡 맵리듀스 작업을 쉽게 지정할 수 있도록 도와주는 스칼라 라이브러리입니다. 스콜딩은 하둡의 하위 세부 사항을 추상화하는 자바 라이브러리인 카스케이딩(Cascading) 위에 구축되어 있습니다. 스콜딩은 Pig와 비교할 수 있지만, 스칼라와 강하게 통합되어 있어 스칼라의 장점을 하둡 맵리듀스 작업에 가져다줍니다.

2. Heron

아파치 헤론(Heron)은 트위터의 자체 스트리밍 엔진으로, 페타바이트에 이르는 대량의 데이터를 처리하고, 개발자 생산성을 향상시키고, 디버깅을 단순화해야 하는 시스템의 필요성으로 개발되었습니다.

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

헤론에서 스트리밍 애플리케이션을 위한 구조를 토폴로지라고 합니다. 토폴로지는 데이터-컴퓨팅 요소를 나타내는 노드와 해당 요소 간에 흐르는 데이터 스트림을 나타내는 엣지로 이루어진 방향성 비순환 그래프입니다.

노드에는 2 종류가 있습니다:

- 스파우트: 데이터 원본에 연결되어 데이터를 스트림에 삽입합니다.
- 볼트: 수신된 데이터를 처리하고 데이터를 방출합니다.

![그림](/assets/img/2024-06-23-HowTwitterimprovedtheprocessingof400billionevents_1.png)

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

더 많은 정보를 보시려면 여기를 참고해주세요: [https://blog.x.com/engineering/en_us/a/2015/flying-faster-with-twitter-heron](https://blog.x.com/engineering/en_us/a/2015/flying-faster-with-twitter-heron)

3. TimeSeriesAggregator

![Image](/assets/img/2024-06-23-HowTwitterimprovedtheprocessingof400billionevents_2.png)

Twitter의 데이터 엔지니어링 팀은 매일 일괄 및 실시간으로 수조 건의 이벤트를 처리하는 과제에 직면했습니다. TSAR는 프레임워크 기반의 강력하고 확장 가능한 실시간 이벤트 시간대 시계열 집계 도구로, tweet과의 상호 작용을 집계하며 장치, 참여 유형 등 다양한 차원을 따라 분할하여 주로 참여 모니터링을 위해 구축되었습니다.

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

트위터에서 어떻게 일이 처리되었는지 대략적으로 살펴보겠습니다. 트위터의 모든 기능은 전 세계에 10만 개 이상의 인스턴스로 퍼져 있는 마이크로서비스에 의해 지원됩니다. 이들은 이벤트를 생성하고, 이를 이벤트 집계 레이어로 보내는 역할을 합니다. 이 이벤트 집계 레이어는 메타에서 제공하는 오픈 소스 프로젝트를 기반으로 구축되었으며, 이벤트를 그룹화하고 집계 작업을 실행하며 데이터를 HDFS에 저장하는 역할을 합니다. 그런 다음 이러한 이벤트를 처리하고 형식을 변환하여 데이터를 다시 압축하여 잘 생성된 데이터 세트를 만들어냅니다.

![이미지](/assets/img/2024-06-23-HowTwitterimprovedtheprocessingof400billionevents_3.png)

# 이전 아키텍처

![이미지](/assets/img/2024-06-23-HowTwitterimprovedtheprocessingof400billionevents_4.png)

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

트위터의 구형 아키텍처는 람다 아키텍처를 기반으로 하고 있었어요. 이는 배치 레이어, 스피드 레이어 및 서빙 레이어로 구성되어 있어요. 배치 컴포넌트는 클라이언트가 생성한 로그이며, 이벤트 처리 후 하둡 분산 파일 시스템(HDFS)에 저장돼요. 트위터는 여러 스케일링 파이프라인을 구축하여 가공되지 않은 로그를 전처리하고 오프라인 소스로 Summingbird 플랫폼에 넣었어요. 실시간 컴포넌트 소스는 스피드 레이어의 일부인 Kafka 토픽들이에요.

데이터가 처리되면 배치 데이터는 맨해튼 분산 시스템에 저장되며, 실시간 데이터는 트위터의 자체 분산 캐시인 Nighthawk에 저장돼요. TSAR 시스템(예: TSAR 쿼리 서비스)은 캐시와 데이터베이스 둘 다 쿼리하는 서빙 레이어의 일부가 돼요.

트위터는 세 개의 다른 데이터 센터에 실시간 파이프라인과 쿼리 서비스를 가졌어요. 배치 컴퓨팅 비용을 줄이기 위해, 트위터는 한 데이터 센터에서 배치 파이프라인을 실행하고 데이터를 다른 두 데이터 센터로 복제해요.

실시간 데이터를 캐시에 저장했을까요? 데이터베이스에 저장하는 것보다 더 좋은 이유가 떠오르시나요?

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

# 오래된 아키텍처의 도전 과제

이 아키텍처가 실시간 이벤트의 경우 어떤 도전 과제를 가질 수 있는지 이해해 봅시다.

![이미지](/assets/img/2024-06-23-HowTwitterimprovedtheprocessingof400billionevents_5.png)

예를 통해 이해해 봅시다:

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

대규모 이벤트가 발생하면 FIFA 월드컵과 같은 큰 이벤트가 발생할 수 있습니다. 트윗 소스는 트윗 토폴로지로 많은 이벤트를 보낼 것입니다. 그러나 파싱 트윗 볼트가 이벤트를 적시에 처리하지 못해 토폴로지 내에서 백프레셔가 발생합니다. 시스템이 장기간 백프레셔 상태에 놓이면 헤론 볼트가 스파우트 라그를 축적할 수 있어 시스템의 지연 시간이 늘어날 수 있습니다. 트위터는 이러한 경우에 토폴로지 랙이 줄어드는 데 매우 오랜 시간이 걸린다고 관찰했습니다.

팀이 사용한 운영 솔루션은 헤론 컨테이너를 다시 시작하여 데이터 스트림 처리를 다시 시작하는 것이었습니다. 이로 인해 이행 중에 이벤트 손실이 발생할 수 있으며, 이는 캐시의 집계된 카운트에 부정확성을 초래할 수 있습니다.

이제 배치 이벤트 예제를 이해해 봅시다. 트위터는 PB 규모의 데이터를 처리하는 몇 가지 무거운 계산 파이프라인을 운영하고 맨해튼 데이터베이스에서 데이터를 시간별로 동기화합니다. 이제 시스템 동기화 작업이 1시간 이상 소요되고 다음 작업이 시작되기로 예정된 경우를 상상해 보십시오. 이는 시스템에 백프레셔가 증가하여 데이터 손실을 초래할 수 있는 상황으로 이어질 수 있습니다.

TSAR 쿼리 서비스는 맨해튼과 캐시 서비스를 통합하여 클라이언트에 데이터를 제공합니다. 실시간 데이터 손실 가능성으로 인해 TSAR 서비스는 고객들에게 부정확한 메트릭을 제공할 수 있습니다.

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

고객과 비즈니스에 미치는 영향을 이해하는 것을 시도해 봅시다:

- 트위터 광고 서비스는 트위터의 주요 수익 모델 중 하나이며, 그 성능이 영향을 받으면 그들의 비즈니스 모델에 직접적인 영향을 끼칩니다.
- 트위터는 인상과 관련 지표에 대한 정보를 검색할 수 있는 다양한 데이터 제품 서비스를 제공하며, 이러한 서비스는 부정확한 데이터로 인해 영향을 받을 수 있습니다.
- 이 경우에 또다른 문제는 이벤트 생성부터 사용 가능할 때까지 배치 처리 작업으로 인해 몇 시간이 걸릴 수 있다는 점입니다. 이는 클라이언트가 수행해야 할 데이터 분석이나 기타 작업이 최신 데이터를 사용할 수 없게됨을 의미합니다. 몇 시간의 시차가 발생할 수 있습니다.

이제 이는 사용자가 이벤트를 생성하고 사용자가 생성하는 이벤트에 기반한 사용자의 타임라인을 업데이트하려거나 트위터 시스템과 상호 작용하는 방식에 따라 사용자에 대한 행동 분석을 수행하려면 클라이언트가 배치 작업이 완료될 때까지 기다려야함을 의미합니다.

# 새로운 아키텍처

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

![2024-06-23-HowTwitterimprovedtheprocessingof400billionevents_6](/assets/img/2024-06-23-HowTwitterimprovedtheprocessingof400billionevents_6.png)

Twitter의 새로운 아키텍처는 Twitter 데이터 센터 서비스와 Google Cloud 플랫폼 양쪽에 구축되었습니다. Twitter는 카파 주제를 퍼브 서브 주제로 변환하는 이벤트 처리 파이프 라인을 구축했으며, 이는 Google Cloud로 전송되었습니다. Google Cloud에서 실시간 집계를 수행하고 데이터를 BigTable에 싱크하는 스트리밍 데이터 플로우 작업이 수행되었습니다.

![2024-06-23-HowTwitterimprovedtheprocessingof400billionevents_7](/assets/img/2024-06-23-HowTwitterimprovedtheprocessingof400billionevents_7.png)

서빙 레이어에는 Twitter가 Twitter 데이터 센터에 프런트 엔드와 Bigtable 및 BigQuery에 백엔드가 있는 LDC 쿼리 서비스를 사용합니다. 전체 시스템은 초당 수백만 건의 이벤트를 스트리밍할 수 있으며 지연 시간이 ~10밀리초까지 낮을 수 있습니다. 높은 트래픽 시 확장이 쉽습니다.

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

이 새로운 아키텍처는 일괄 파이프라인을 구축하는 비용을 절약해 줄 뿐만 아니라, 실시간 파이프라인에서는 트위터가 더 높은 집계 정확도와 안정적인 낮은 대기 시간을 달성할 수 있습니다. 또한, 그들은 여러 데이터 센터에서 다른 실시간 이벤트 집계를 유지할 필요가 없습니다.

# 성능 비교

![이미지](/assets/img/2024-06-23-HowTwitterimprovedtheprocessingof400billionevents_8.png)

새 아키텍처는 구 방식인 Heron 토폴로지와 비교하여 낮은 대기 시간을 제공하며 더 높은 처리량을 제공합니다. 또한, 새 아키텍처는 지연된 이벤트 계산을 처리하고 실시간 집계 중에 이벤트 손실이 없습니다. 또한, 새 아키텍처에는 일괄 구성 요소가 없어 구 방식에 있던 그것보다 설계를 단순화하고 컴퓨팅 비용을 줄입니다.

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

TSAR로 구축된 이전 아키텍처를 트위터 데이터 센터와 구글 클라우드 플랫폼의 하이브리드 아키텍처로 이전함으로써 트위터는 수십억 이벤트를 실시간으로 처리하고 엔지니어들에게 낮은 대기 시간, 높은 정확성, 안정성, 아키텍처의 단순성 및 운영 비용 감소를 달성할 수 있었습니다.

Linkedin: [Mayank Sharma의 Linkedin 프로필](https://www.linkedin.com/in/mayank-sharma-2002bb10b/)

저와 모의 시스템 디자인 인터뷰 일정을 잡으려면 : [Meetapro에서 예약하기](https://www.meetapro.com/provider/listing/160769)

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

나의 웹사이트: imayanks.com

참고 자료:
