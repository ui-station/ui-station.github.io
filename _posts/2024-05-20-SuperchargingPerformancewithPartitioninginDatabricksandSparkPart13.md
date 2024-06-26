---
title: "스파크와 데이터브릭스에서 파티셔닝으로 성능 향상을 달성하세요 파트 13"
description: ""
coverImage: "/assets/img/2024-05-20-SuperchargingPerformancewithPartitioninginDatabricksandSparkPart13_0.png"
date: 2024-05-20 17:02
ogImage:
  url: /assets/img/2024-05-20-SuperchargingPerformancewithPartitioninginDatabricksandSparkPart13_0.png
tag: Tech
originalTitle: "Supercharging Performance with Partitioning in Databricks and Spark (Part 1 3)"
link: "https://medium.com/data-engineer-things/supercharging-performance-with-partitioning-in-databricks-and-spark-part-1-3-aebcfb48c3b"
---

## 데이터 엔지니어가 파티셔닝을 이해해야 하는 이유!

Spark 및 Databricks의 가장 중요한 기능 중 하나를 다루는 세 개의 기사 중 첫 번째 기사입니다: Partitioning에 대해 얘기할 것입니다.

- 이 첫 장은 파티셔닝의 일반 이론과 Spark에서의 파티셔닝에 초점을 맞출 것입니다.
- 제2부에서는 테이블 파티셔닝에 대한 구체적인 내용을 다루고 데이터셋을 준비할 것입니다.
- 제3부에서는 철저한 사례 연구를 다루고 성능 비교를 수행할 것입니다.

# 소개

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

데이터 관련해서 말씀드리면, "빅데이터" 개념은 종종 3V(또는 소스에 따라 가끔 더)로 정의됩니다: 양(volume), 다양성(variety), 그리고 속도(velocity).

- 양은 데이터셋의 크기에 관련되며, 이는 테라바이트 이상까지 될 수 있습니다.
- 다양성은 다양한 데이터 유형과 소스를 처리하는 과제를 의미하며, 효과적으로 종합되어야 합니다.
- 반면에 속도는 데이터를 받아들이는 속도에 관한 것이며, 매초 100MB 파일이 수신된다면 금방 방대한 양의 데이터로 축적될 수 있습니다.

"빅데이터"를 처리하는 높은 성능의 솔루션을 개발하기 위해서는 다양한 기법과 패턴을 사용할 수 있습니다. Partitioning이라는 한 가지 기법이 있습니다. 대용량 데이터셋을 다룰 때 개별 기계의 처리 능력이 한계에 도달할 수 있어, Databricks와 Spark 같은 도구를 통해 제공되는 분산 및 병렬 처리 기능을 사용해야 할 때가 있습니다.

Partitioning은 이것이 가능하게 하는 핵심입니다. 대용량 데이터셋을 파티션이라는 더 작고 관리하기 쉬운 조각으로 나누는 것을 포함합니다.

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

딥레닉과 같은 분산 데이터 처리 시스템에서는 파티션을 사용하여 데이터를 여러 노드에 분산시켜 병렬 처리와 뛰어난 성능을 확보합니다.

데이터를 파티션으로 나누면 각각을 독립적으로 처리할 수 있어 처리 시간을 단축하고 확장성을 향상시킬 수 있습니다.

파티션화는 작업 부하를 균형있게 분산시키고 데이터 이동을 줄이며 데이터의 불균형이나 불균형의 영향을 최소화하는 데도 도움이 됩니다.

반면에 비효율적인 파티셔닝은 성능 병목 현상, 자원 낭비 및 처리 시간 증가로 이어질 수 있습니다. 따라서 딥레닉과 같은 분산 데이터 처리 시스템에서 성능을 최적화하기 위해 올바른 파티셔닝 전략을 선택하는 것이 중요합니다.

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

# Databricks와 Spark에서 파티셔닝 이해하기

먼저, DataFrame / RDD 수준에서의 파티셔닝과 테이블 수준에서의 파티셔닝을 구분해야 합니다.

## RDDs와 DataFrames

Databricks의 계산 엔진은 Spark입니다. Databricks에서 SQL, Scala, R 또는 Python을 사용하여 코드를 작성할 때, 우리는 단지 해당 API를 사용하여 내부 Spark 엔진에 액세스합니다.

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

스파크는 기본 데이터 구조로 RDDs(탄력적 분산 데이터 집합)를 사용합니다. 그들의 이름에서 알 수 있듯이, RDDs는 분산 데이터 집합입니다. 요즘에는 RDDs와 직접 작업하는 것보다는 DataFrame API와 함께 작업하는 것이 인기가 없지만, DataFrame API는 이름을 붙일 수 있는 열을 제공하는 RDDs의 상위 수준 추상화인 DataFrame 객체를 제공합니다. 따라서 DataFrame은 전통적인 관계형 데이터베이스의 테이블과 유사합니다. 게다가, DataFrames는 많은 성능 최적화 옵션을 제공합니다.

우리의 데이터를 이러한 DataFrame으로 로드할 때, 이미 파티션되어 있습니다. 이를 아래의 스크린샷에서 볼 수 있습니다. 만약 DataFrame의 기본 RDD에 액세스하면, .rdd.getNumPartitions()를 사용하여 파티션 수를 얻을 수 있습니다.

![이미지](/assets/img/2024-05-20-SuperchargingPerformancewithPartitioninginDatabricksandSparkPart13_0.png)

RDDs 또는 DataFrames로 작업할 때, 스파크는 파티션 프로세스를 자동으로 처리해 줍니다. 심지어 우리가 명시적으로 지정하지 않았더라도요. 이미 파티션된 테이블에서 데이터를 가져올 때, 기존 파티션 구조가 고려된다는 점을 언급하는 것이 중요합니다.

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

화면 캡처에서 보듯이 나는 파티션이 없는 테이블에서 데이터를 로드하고 있습니다. 이는 Spark가 아래에서 다룰 여러 구성에 기반하여 파티셔닝을 처리할 것을 의미합니다.

또한 파티션과 파일을 구분해야 합니다. 왜냐하면 이 둘은 같은 것이 아니기 때문입니다. 파티션은 분산 컴퓨팅 환경에서 데이터의 논리적 분할을 의미합니다. 반면 파일은 실제로 디스크에 저장된 데이터의 저장 단위입니다.

데이터를 섞는 일부 작업을 수행할 때, Spark는 "spark.sql.shuffle.partitions" 구성을 기반으로 데이터를 파티션으로 분할합니다.

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

그러나 이 같은 속성은 데이터를 간단히 읽을 때 파티션 수도 제어합니다. 디폴트로 200으로 설정되어 있어 Spark는 200개의 파티션을 생성합니다. 그러나 "spark.sql.files.maxPartitionBytes" 구성 설정도 이 프로세스에 한도를 두고 있습니다.

각 파티션의 실제 저장 크기는 사용 가능한 메모리와 데이터셋의 크기와 같은 다양한 요인에 따라 달라집니다. 그러나 Databricks는 "spark.sql.files.maxPartitionBytes" 구성 속성에 의해 정의된 최대 크기로 파티션을 생성합니다. 기본값으로 이 값은 128MB로 설정되어 있습니다.

이미지 링크:
![이미지](/assets/img/2024-05-20-SuperchargingPerformancewithPartitioninginDatabricksandSparkPart13_2.png)

데이터프레임 파티션의 크기를 제어하는 다른 방법은 .repartition() 또는 .coalesce() 메서드를 사용하여 DataFrame을 다시 파티션하고 파티션 수 또는 파티션화하려는 열을 지정하는 것입니다.

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

- repartition()은 DataFrame 또는 RDD의 파티션 수를 증가 또는 감소시키는 데 사용됩니다. .repartition()을 사용하면 Spark가 데이터를 섞고 지정된 파티션 수에 기반하여 새로운 파티션을 생성합니다.
- coalesce()는 DataFrame 또는 RDD의 파티션 수를 감소시키는 데 사용됩니다. .coalesce()를 사용하면 Spark가 기존 파티션을 결합하여 새로운 파티션을 생성하려고 합니다. repartition()과 달리, coalesce()는 데이터를 섞지 않습니다.

# 요약

파티셔닝은 성능이 우수한 "빅 데이터" 솔루션을 구축하는 데 필수적인 기술이며, 적절한 파티셔닝 전략을 선택하는 것은 좋은 성능과 확장성을 달성하는 데 중요합니다. 그러나 최적의 파티셔닝 설정을 조사할 리소스가 없는 경우, Databricks의 기본 최적화 설정과 옵션을 사용하는 것이 좋습니다.

모든 테이블에 대한 보편적인 해결책은 없으며, 우선 순위와 목표를 균형 있게 유지해야 합니다. 각 시나리오는 요구 사항과 도전에 대응하기 위한 맞춤형 접근 방식이 필요합니다.

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

DataFrame / RDD 수준의 파티셔닝은 데이터를 병렬 처리를 위해 클러스터 노드에 분산하는 것을 다루고, 테이블 수준의 파티셔닝은 저장 시스템 내에서 데이터를 조직화하여 쿼리 성능을 최적화하는 데 초점을 맞춥니다.

이제 파티셔닝의 중요성과 RDD 및 DataFrame이 어떻게 파티셔닝되는지 이해했으니, 다음 글에서는 Databricks의 테이블이 어떻게 파티션되는지 설명하고 최종 챕터를 위해 데이터를 준비할 것입니다.

읽어주셔서 감사합니다!
