---
title: "실시간 데이터 파이프라인 마스터하기 PostgreSQL에서 MinIO로 Strimzi Kafka Connect를 사용한 CDC 구현 방법  Part-1"
description: ""
coverImage: "/assets/img/2024-06-30-MasteringReal-TimeDataPipelinesImplementingCDCwithStrimziKafkaConnectfromPostgreSQLtoMinIOPart-1_0.png"
date: 2024-06-30 19:28
ogImage: 
  url: /assets/img/2024-06-30-MasteringReal-TimeDataPipelinesImplementingCDCwithStrimziKafkaConnectfromPostgreSQLtoMinIOPart-1_0.png
tag: Tech
originalTitle: "Mastering Real-Time Data Pipelines: Implementing CDC with Strimzi Kafka Connect from PostgreSQL to MinIO — Part-1"
link: "https://medium.com/@ibrahimhkoyuncu/mastering-real-time-data-pipelines-implementing-cdc-with-strimzi-kafka-connect-from-postgresql-to-8476ad62d5d8"
---


<img src="/assets/img/2024-06-30-MasteringReal-TimeDataPipelinesImplementingCDCwithStrimziKafkaConnectfromPostgreSQLtoMinIOPart-1_0.png" />

안녕하세요! 이번 기사 시리즈에서는 쿠버네티스 환경 내에서 Kafka를 활용한 Change Data Capture (CDC)의 세계로 여러분을 안내할 예정이에요. CDC의 기본 개념을 탐구하고 매력적인 장점을 발견하며 실용적인 구현 가이드를 안내할 거예요. 이 과정에서 Kafka 기반 CDC가 데이터 아키텍처를 혁신시킬 수 있는 실제 시나리오를 탐구해볼 거에요. 실시간 데이터 스트리밍 및 통합 방법을 변화시키기 위해 준비하세요!

본 기사 시리즈는 이론적 통찰과 실용적인 구현 세부 사항을 제공하기 위해 세 가지 부분으로 구성되어 있어요.

부분 1: 이론적 기초 및 일반 아키텍처 : 이 섹션에서는 Change Data Capture (CDC)의 기초 개념과 현대 데이터 아키텍처에서의 중요한 역할을 탐구할 거에요. Kafka를 사용하는 이점을 특히 Strimzi와 함께 사용하여 관계형 및 NoSQL 데이터베이스, 객체 스토리지 및 기타 데이터 저장소에서 실시간 데이터 변경을 캡처하는 중앙 스트리밍 플랫폼으로 사용하는 것의 이점에 대해 논의할 거에요. 이 부분은 CDC 개념을 설명하고 Kafka Connect 클러스터 아키텍처를 심층적으로 살펴보며 CDC의 사용 사례 시나리오를 강조할 거에요. 이론적 기초를 이해함으로써 효율적이고 확장 가능한 데이터 파이프라인을 가능하게 하는 CDC의 중요성을 이해할 수 있으며, 이후 섹션에서의 실제 구현을 위한 준비를 할 수 있어요.

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

부분 2: 실제 구현 — PostgreSQL에서 Kafka로 데이터 수집
다음으로, 실제 구현 세부 사항을 살펴보겠습니다. PostgreSQL로부터 데이터 변경을 Kafka로 수집하도록 구성된 Kafka Connect 클러스터와 같은 Strimzi 리소스를 설정하는 방법을 배우게 됩니다. 저는 커넥터 설정 및 전체 설정을 Kubernetes에 배포하는 방법을 강조하면서 구성 단계를 안내해 드리겠습니다.

부분 3: 실제 구현 — Kafka에서 MinIO로 Apache Parquet 형식으로 데이터 싱크
최종 부분은 데이터를 MinIO로 Apache Parquet 파일로 싱크하는 데 중점을 둡니다. 실시간 처리와 분석에 대비하여 준비된 데이터임을 보장합니다. 데이터 스키마 호환성과 무결성을 유지하기 위해 스키마 레지스트리를 활용하겠습니다. 이 부분에서는 데이터 싱크를 위한 Kafka 커넥터 구성, 데이터 형식 처리, 전체 솔루션을 Kubernetes에 배포하는 방법에 대한 세부 단계가 포함될 것입니다.

이 글을 마치면, 카프카를 활용한 강력한 CDC 파이프라인을 Kubernetes 환경에서 효율적이고 확장 가능하게 구현하는 괜찮은 경험과 포괄적인 이해력을 얻게 될 것입니다. Part-1 주제를 계속해 보겠습니다.

- Change Data Capture (CDC) 및 Kafka와의 중요성 소개
- 사용 사례 시나리오
- Strimzi Kafka란 무엇인가요?
- Kafka Connect는 무엇인가요?
- Kafka Connect 아키텍처

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

## Kafka를 이용한 Change Data Capture(CDC)의 소개 및 중요성

— Change Data Capture(CDC)란 무엇인가요?

Change Data Capture(CDC)는 데이터베이스에서 데이터가 변경되는 것을 식별하고 캡처하는 프로세스로, 이러한 변경 사항이 실시간으로 다른 시스템으로 전파될 수 있도록 보장합니다.

— 왜 Kafka와 CDC가 게임 체인저인가요?

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

- 실시간 데이터 처리: CDC with Kafka를 사용하면 데이터 변경을 계속 캡처하고 스트리밍하여 실시간으로 데이터를 사용 가능하게 하여 분석 및 의사 결정을 위한 즉각적인 데이터 이용이 가능합니다.
- 확장성: Kafka의 분산 아키텍처는 고 처리량과 장애 허용성을 보장하여 여러 소스를 통해 대량의 데이터 변경을 처리하는 확장성을 가집니다.
- 데이터 생성자와 소비자의 분리: Kafka는 중개자 역할을 수행하여 데이터 생성자(데이터베이스)와 데이터 소비자(어플리케이션 및 분석 플랫폼)를 분리하여 독립적인 확장과 개발을 용이하게 합니다.
- 원활한 통합: Kafka Connect는 다양한 데이터 소스 및 싱크와 통합하기 위한 견고한 프레임워크를 제공하여 이질적인 시스템 간에 원활한 데이터 흐름을 보장합니다.
- 데이터 일관성과 신뢰성: CDC에서 데이터 소스 수준에서 변경을 캡처함으로써 데이터 일관성과 신뢰성을 유지하여 분산 시스템 전반에 정확하고 최신의 데이터를 유지합니다.
- 이벤트 주도 아키텍처: CDC with Kafka는 데이터 변경이 실시간 작업과 워크플로를 트리거하는 이벤트 주도 아키텍처를 지원하여 반응성과 자동화를 향상시킵니다.
- 지연 시간 감소: CDC와 Kafka를 사용하면 데이터 생성과 사용 가능성 사이의 지연 시간이 최소화되어 근사 실시간 분석과 운영 지능을 지원합니다.
- 과거 데이터 재생: Kafka의 데이터 변경 저장 및 재생 기능을 통해 과거 데이터 분석 및 복구가 가능하여 유용한 통찰력을 제공하고 시나리오의 백테스팅이 가능합니다.
- 비용 효율성: Kafka의 효율적인 데이터 스트리밍 기능을 활용함으로써 조직은 전통적인 배치 처리와 ETL 작업에 관련된 오버헤드와 복잡성을 줄일 수 있습니다.

## 사용 사례 시나리오

아래 시나리오에서 CDC와 Kafka를 사용하면 실시간 데이터 통합, 향상된 효율성 및 확장성을 제공하여 중요한 데이터가 항상 최신 상태로 여러 시스템 및 어플리케이션에서 접근 가능하도록 보장할 수 있습니다.

1-) 실시간 재고 관리 시스템: 소매 회사가 여러 창고와 상점 전반에 걸쳐 실시간으로 재고를 최신 상태로 유지해야 하는 경우.

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

— Kafka를 이용한 CDC의 장점:

- 실시간 업데이트: 재고 데이터가 지속적으로 업데이트되어 모든 위치에서 재고 부족 또는 과잉 상황을 방지합니다.
- 확장성: 대량의 데이터를 효율적으로 처리하는 Kafka의 능력은 소매 환경에서 일반적인 높은 거래율을 지원합니다.
- 통합성: 관계형 및 NoSQL 데이터베이스 및 어플리케이션과 원활하게 통합되어 전체 시스템 내에서 데이터 일관성과 정확도를 보장합니다.

2-) 고객 활동 추적 시스템: 전자상거래 플랫폼은 고객 활동(페이지 조회, 제품 클릭, 구매 등)을 실시간으로 추적하여 맞춤형 추천을 제공하고자 합니다.

— Kafka를 이용한 CDC의 장점:

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

- 즉각적인 통찰력: 실시간으로 고객 활동을 캡처하고 처리하여 적시적이고 관련성 있는 추천을 제공합니다.
- 향상된 사용자 경험: 실시간 데이터를 활용하여 플랫폼이 맞춤형 쇼핑 경험을 제공하여 고객 만족도와 참여도를 높일 수 있습니다.
- 유연성: Kafka의 Apache Flink와 같은 처리 프레임워크와의 통합을 통해 복합 이벤트 처리와 분석이 가능합니다.

3-) 재무 거래 모니터링: 은행은 사기를 탐지하고 통찰력을 제공하기 위해 실시간으로 재무 거래를 모니터링하고 분석하려고 합니다.

— Kafka와 CDC의 혜택:

- 사기 탐지: 실시간 모니터링을 통해 의심스러운 활동을 즉각적으로 감지하여 사기 거래의 위험을 줄입니다.
- 규정 준수: 모든 거래 데이터가 정확하고 즉각적으로 기록되도록 보장하여 은행이 규제 요건을 충족할 수 있습니다.
- 확장성과 신뢰성: Kafka의 고장 내성 아키텍처를 통해 고부하 조건에서도 지속적인 데이터 흐름과 처리가 가능합니다.

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

4-) 주문 처리 시스템: 온라인 상점이 주문 변경에 실시간으로 반응해야 하는 여러 서비스를 통합하여 주문 처리를 최적화하고자 합니다.

— 카프카를 활용한 변경 데이터 캡처(CDC)의 장점:

- 효율성: 실시간 데이터 흐름을 통해 주문 관련 서비스(재고 업데이트, 알림, 배송)가 신속하게 실행되어 처리 시간을 단축시킵니다.
- 조정: 카프카를 통해 서로 다른 마이크로서비스가 주문 변경을 소비하고 독립적으로 움직이면서도 일관되게 작동하여 전체 시스템의 효율성을 향상시킵니다.
- 일관성: 모든 서비스 간의 데이터 일관성을 유지하여 정확한 주문 추적과 충족을 보장합니다.

5-) 사용자 프로필 동기화: 소셜 미디어 플랫폼이 실시간으로 여러 시스템 간에 사용자 프로필을 동기화해야 합니다.

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

— Kafka와 함께 CDC의 이점:

- 일관성: 사용자 프로필 변경 사항이 모든 시스템에 즉시 전파되어 일관된 사용자 경험을 유지합니다.
- 통합: Kafka의 다양성은 검색 인덱스, 추천 엔진 및 캐싱 레이어와 같은 다양한 하향 시스템과의 데이터 동기화를 원활하게 수행할 수 있습니다.
- 확장성: 소셜 미디어 플랫폼에서 일반적인 프로필 변경의 대량 처리를 다룰 수 있어 신뢰할 수 있고 효율적인 데이터 동기화를 보장합니다.

## Strimzi Kafka란

Strimzi Kafka는 Kubernetes에서 Apache Kafka의 배포, 관리 및 운영을 간소화하는 오픈소스 프로젝트입니다. Strimzi는 Kafka 클러스터를 생성하고 관리하기 위한 Kubernetes 네이티브 자원과 도구를 제공하여 Kafka를 클라우드 네이티브 환경에서 실행하기 쉽도록 합니다. 자동화된 배포, 스케일링, 롤링 업데이트 및 모니터링과 같은 주요 기능을 포함하여 사용자는 Kubernetes의 확장 가능성과 탄력성을 활용하여 Kafka의 강력한 메시징 및 스트리밍 기능을 사용할 수 있습니다. Strimzi를 사용함으로써 기관은 기존 Kubernetes 인프라에 Kafka를 효율적으로 통합하여 데이터 스트리밍 및 처리 기능을 향상시킬 수 있습니다.

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

## 카프카 커넥트 및 커넥트 클러스터 아키텍처란

카프카 커넥트는 카프카 브로커와 외부 시스템(예: 데이터베이스) 간의 데이터를 스트리밍하는 통합 툴킷입니다. 플러그인 아키텍처를 사용하여 커넥터를 구현하며, 이를 통해 다른 시스템에 연결하고 데이터를 조작하기 위한 구성 옵션을 제공합니다. 플러그인에는 커넥터, 데이터 컨버터 및 변환기가 포함됩니다. 커넥터는 특정 유형의 외부 시스템과 작동하며 해당 구성에 대한 스키마를 정의합니다. 카프카 커넥트에 구성을 제공하여 커넥터 인스턴스를 생성하고, 이를 통해 시스템 간 데이터 이동 작업을 정의합니다.

Strimzi는 분산 모드에서 카프카 커넥트를 운영하며, 여러 워커 팟 간에 데이터 스트리밍 작업을 관리합니다. 각 커넥터는 개별 워커에서 실행되며 해당 작업은 워커 그룹에 분산되어 확장 가능한 파이프라인을 제공합니다. 각 워커는 별도의 팟으로 실행되어 오류 허용성이 향상됩니다. 작업의 수가 워커 수를 초과하는 경우, 각 워커에 여러 작업이 할당됩니다. 워커가 실패하면 해당 작업은 자동으로 활성 워커에 재할당되어 지속적인 작동이 보장됩니다.

워커는 데이터를 소스 또는 대상 시스템에 적합한 다른 형식으로 변환합니다. 커넥터 구성에 따라, 워커는 변환기를 적용하여 변환 전에 메시지를 조정하는 경우도 있습니다(예: 데이터 필터링). 카프카 커넥트에는 내장 변환기가 있지만 필요한 경우 플러그인을 통해 추가 변환을 제공할 수도 있습니다.

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

이제 Kafka Connect 클러스터 아키텍처의 소스를 살펴봅시다.

![image](/assets/img/2024-06-30-MasteringReal-TimeDataPipelinesImplementingCDCwithStrimziKafkaConnectfromPostgreSQLtoMinIOPart-1_1.png)

1-) 플러그인 구현:

- 역할: 플러그인은 소스 커넥터를 위한 필요한 구현 자산을 제공합니다.
- 세부 정보: Kafka Connect의 플러그인 아키텍처는 유연성과 확장성을 제공합니다. 플러그인은 커넥터 뿐만 아니라 변환기와 변환도 포함하며, 이러한 구성 요소를 통해 Kafka Connect가 다양한 외부 시스템과 상호 작용하고 필요한 대로 데이터를 조작할 수 있습니다.

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

2-) Worker 및 Connector 초기화:

- 역할: 단일 Worker가 소스 Connector 인스턴스를 초기화합니다.
- 상세 내용: Worker는 Kafka Connect 클러스터의 기본 구성 요소입니다. 각 Worker 파드는 독립적으로 실행되어 설정의 분산 구조를 보장합니다. Connector가 인스턴스화되면 하나의 Worker가 이를 초기화하는 책임을 지며, 이로써 Connector가 올바르게 시작되고 수명 주기 동안 모니터링됩니다.

3-) 작업 생성 및 분배:

- 역할: 소스 Connector는 데이터를 스트리밍하기 위한 작업을 생성합니다.
- 상세 내용: 초기화 후 Connector는 일련의 작업을 정의합니다. 이러한 작업은 실제 데이터 스트리밍을 처리하는 작업 단위입니다. 작업은 사용 가능한 Worker 간에 분산되어 부하를 균형 있게 분배하고 처리량을 극대화합니다. 작업의 개수는 일반적으로 워크로드 및 클러스터의 용량에 따라 정의됩니다.

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

4-) 병렬 작업 실행:

- 역할: 작업은 외부 데이터 시스템을 병렬로 폴링하여 레코드를 반환합니다.
- 세부 내용: 각 작업은 독립적으로 작동하여 외부 소스 시스템에서 데이터를 폴링합니다. 이 병렬 실행은 데이터가 효율적으로 및 실시간으로 흡수되도록 보장합니다. 작업은 계속해서 외부 시스템을 폴링하여 새로운 데이터 변화가 신속히 포착되고 처리되도록 합니다.

5-) 데이터 변환:

- 역할: 변환은 레코드를 조정하여 필터링하거나 다시 레이블링합니다.
- 세부 내용: 데이터가 Kafka로 변환되어 전송되기 전에 변환되어야 할 수 있습니다. 변환은 원치 않는 데이터를 필터링하거나 필드를 다시 레이블링하거나 다른 수정을 적용하는 데 사용될 수 있습니다. Kafka Connect는 내장된 변환을 지원하지만 사용자 정의 변환은 플러그인을 통해 추가할 수 있으며, 데이터가 처리되는 방식에 유연성을 제공합니다.

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

6-) 데이터 변환:

- 역할: 변환기는 레코드를 Kafka에서 사용할 수 있는 형식으로 변환합니다.
- 상세: 변환기는 Kafka와 외부 시스템 간의 데이터를 직렬화하고 역직렬화하는 역할을 맡습니다. 이들은 데이터가 Kafka 토픽에 호환되는 형식으로 처리되도록 보장합니다. 일반적인 변환기에는 JSONConverter, AvroConverter 및 StringConverter가 포함됩니다. 이러한 변환기들은 데이터 형식의 번역을 처리하여 Kafka가 레코드를 저장하고 관리하는 것을 원활하게 만듭니다.

7-) 커넥터 관리:

- 역할: 소스 커넥터는 KafkaConnectors 또는 Kafka Connect API를 사용하여 관리됩니다.
- 상세: Kafka Connectors의 관리는 Strimzi에서 제공하는 Kubernetes 사용자 정의 리소스인 KafkaConnectors 또는 Kafka Connect REST API를 통해 수행될 수 있습니다. 이러한 관리 인터페이스는 관리자가 커넥터와 작업을 생성, 구성 및 모니터링하여 데이터 통합 프로세스에 대한 전체 제어를 제공합니다.

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

이러한 구성 요소와 프로세스를 활용하면 Strimzi Kafka Connect는 견고하고 확장 가능한 데이터 스트리밍 파이프라인을 구축하여 다양한 시스템 간 실시간 데이터 통합 및 처리를 보장합니다.

지금까지 모든 것이 잘 진행되었다면, Sink Kafka Connect 클러스터 아키텍처로 계속 진행합시다 :)

![이미지](/assets/img/2024-06-30-MasteringReal-TimeDataPipelinesImplementingCDCwithStrimziKafkaConnectfromPostgreSQLtoMinIOPart-1_2.png)

사실 대부분의 구성 요소가 소스 커넥트 클러스터와 유사한 기능을 수행합니다. 여기서의 차이점은 작업이 관련 Kafka 토픽에서 데이터를 폴링하고 변환 및 처리 프로세스를 거친 후 외부 데이터 시스템으로 데이터를 전송한다는 것입니다.

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

이론적 기초를 다지면서, 이제 실용적인 부분으로 진입할 준비가 되었습니다. 다가오는 섹션에서는 소망을 걸고 Kafka Connect 클러스터 설정에 실질적으로 착수할 것입니다. 먼저, PostgreSQL에서 데이터를 매끄럽게 수집하는 소스 Kafka Connect 클러스터를 생성할 것이며, 그 다음으로는 Kafka 토픽에서 데이터를 읽어 Apache Parquet 형식으로 MinIO에 저장하는 싱크 Kafka Connect 클러스터를 설정할 것입니다. 이것이 실제로 어떻게 이루어지는지 궁금하신가요? 이 구현 여정에 참여하려면 계속 읽어보세요 :)

Github : https://github.com/menendes

Linkedin : https://www.linkedin.com/in/ibrahim-halil-koyuncu-b1030516a/