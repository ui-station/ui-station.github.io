---
title: "퀵, 퀵, 캐-칭 덕DB를 통해 Snowflake 쿼리하여 비용 절감하기"
description: ""
coverImage: "/assets/img/2024-05-23-QuackQuackKa-ChingCutCostsbyQueryingSnowflakefromDuckDB_0.png"
date: 2024-05-23 15:52
ogImage:
  url: /assets/img/2024-05-23-QuackQuackKa-ChingCutCostsbyQueryingSnowflakefromDuckDB_0.png
tag: Tech
originalTitle: "Quack, Quack, Ka-Ching: Cut Costs by Querying Snowflake from DuckDB"
link: "https://medium.com/datamindedbe/quack-quack-ka-ching-cut-costs-by-querying-snowflake-from-duckdb-f19eff2fdf9d"
---

## 덕이 신용카드로 도망갑니다.

스노우플레이크는 최근 오픈 테이블 형식 아이스버그에 대한 광범위한 지원을 출시했습니다. 오픈 형식을 사용하면 데이터의 민첩성이 향상되고 락인이 줄어듭니다. 이 게시물은 이 유연성을 활용하여 덕디비(DuckDB)를 사용하여 스노우플레이크의 높은 컴퓨트 비용을 줄이는 방법을 탐색합니다.

# Apache Iceberg란 무엇인가요?

아파치 아이스버그는 2017년 넷플릭스에 의해 개발된 테이블 형식 명세서입니다. 2018년 넷플릭스는 아이스버그 프로젝트를 오픈소스화하고 아파치 소프트웨어 재단에 기증했습니다.

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

넷플릭스는 Iceberg를 개발하여 일반 파티션이 적용된 데이터 파일과 최소한의 메타데이터를 포함하는 데이터 레이크의 한계를 극복했습니다. 이를 하이브 형식 테이블이라고도 합니다. 이러한 제한 사항에는 성능 문제(많은 파일 목록, 많은 파티션, 제한된 가지치기) 및 데이터 웨어하우스에서 흔히 제공되는 시간 여행, 스키마 진화 및 ACID 트랜잭션과 같은 기능이 빠져 있는 것이 포함되었습니다.

## 테이블 형식 명세

테이블 형식 명세는 테이블을 정의하는 메타데이터를 작성하는 표준 방법입니다. 메타데이터는 데이터 집합에 어떤 내용이 있는지를 알려줌으로써 도구가 전체 데이터를 읽지 않고도 데이터 내용을 파악할 수 있게 합니다. 그러나 이러한 데이터에 다른 의미를 할당할 수도 있습니다. 예를 들어 현재로 표시하는 방식 등이 그에 해당합니다.

Apache Iceberg는 저장 형식이 아닙니다. Iceberg 테이블의 데이터를 Parquet, ORC, 또는 Avro와 같은 형식으로 저장할 수 있습니다. Iceberg는 이러한 데이터 파일 옆에 메타데이터를 구성하는 표준 방법입니다.

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

## 도구 상자 열고 상호 운용성

많은 엔진과 도구들이 Iceberg 명세를 구현합니다. 동일한 명세를 구현하는 도구들은 모두 동일한 Iceberg 테이블과 상호 작용할 수 있습니다. 이것이 Apache Iceberg가 "다중 엔진"인 이유입니다. AWS Athena, Trino (Starburst), DuckDB, Snowflake와 같은 주요 엔진들은 Iceberg를 지원합니다.

이 상호 운용 가능한 접근 방식은 이전과는 근본적으로 다른 방식입니다. Oracle, Vertica, BigQuery 등과 같은 데이터베이스는 과거에 일반적으로 저장된 메타데이터와 데이터를 독점적인 형식으로 보관하여 매끄러운 상호 운용성에 도전을 제공했으며, 많은 데이터 복사가 필요했고, 잠재적으로 공급 업체에 구속될 가능성이 있었습니다.

## 패러다임 전환

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

중앙에서 액세스할 수있는 형식에 독립적으로 작업함으로써, 컴퓨팅 엔진이 상호 교체 가능해집니다. 이를 통해 특정 작업에 가장 적합한 컴퓨팅 엔진을 사용할 수 있으며 데이터를 옮길 필요가 없습니다. 한 도구로 작성된 데이터는 즉시 다른 도구에서 읽을 수 있습니다.

이 아키텍처는 다른 컴퓨팅 엔진 간 중복 데이터 복제보다 데이터 공유를 선호하는 패러다임 변화를 가져옵니다.

![이미지](/assets/img/2024-05-23-QuackQuackKa-ChingCutCostsbyQueryingSnowflakefromDuckDB_0.png)

## 다양한 기능을 갖춘 레이크하우스

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

Apache Iceberg은 상호 운용성을 촉진하는 것 외에도 데이터 호수와 데이터 웨어하우스 간의 기능 차이를 좁히는 데 도움이 되는 무수히 많은 기능을 지원하여 레이크하우스로 알려진 것이 됩니다. 시간 여행, ACID 트랜잭션, 파티션 진화, 숨겨진 파티셔닝, 스키마 진화, 객체 저장 비용 절감 등이 포함됩니다. 이 블로그 글에서는 상호 운용성에만 초점을 맞춥니다.

# Apache Iceberg와 Snowflake

2023년 12월 4일, Snowflake는 Apache Iceberg 통합이 Public preview 상태에 있다는 블로그 글을 발표했습니다.

Snowflake는 이제 Iceberg 테이블을 사용하는 두 가지 방법을 제공합니다:

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

- 외부 카탈로그. 이러한 테이블들은 Apache Spark, Apache Flink 또는 심지어 Trino과 같은 도구에 의해 외부적으로 작성되며, 객체 저장소에 등록되어 Hive 메타스토어, AWS Glue 데이터 카탈로그 또는 Nessie와 같은 외부 카탈로그에 등록됩니다. 이 모드에서 Snowflake로부터 테이블은 읽기 전용입니다.
- Snowflake 카탈로그. 이러한 테이블들은 Snowflake로부터 읽기-쓰기가 가능하며 외부로부터는 읽기 전용입니다.

두 경우 모두, Snowflake는 모든 데이터와 Iceberg 메타데이터를 고객의 자체(클라우드) 객체 저장소에 저장합니다. Iceberg와 함께 작업하는 두 가지 방법은 각각의 장단점을 가지고 있습니다. 귀하의 상황에 맞게 가장 적합한 방법은 명확히 알아야 합니다.

Snowflake 카탈로그를 사용하여 Iceberg 테이블을 사용할 때, Snowflake는 항상 그랬던 것처럼 작동합니다. 이는 "제로 옵스(Zero-Ops)" 데이터 웨어하우스로 남아 있으며, Snowflake가 요약 데이터, 만기된 스냅샷 및 고아 파일 청소와 같은 저장소 유지 관리 작업을 수행함으로써 여유롭게 있을 수 있습니다. Iceberg 테이블은 Snowflake 내부 테이블과 거의 동일하게 작동하지만 확인할 필요가 있는 일부 제한 사항이 있을 수 있습니다.

본 문은 귀하의 데이터가 Snowflake에 저장되며 대규모 처리가 진행되는 곳이 Snowflake라고 가정합니다. Snowflake 카탈로그를 사용하는 것이 올바른 선택일 것입니다.

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

![Iceberg Catalog](/assets/img/2024-05-23-QuackQuackKa-ChingCutCostsbyQueryingSnowflakefromDuckDB_1.png)

## 아이스버그 카탈로그

스노우플레이크 카탈로그와 함께 아이스버그 테이블을 사용할 때, "카탈로그"는 스노우플레이크 쪽에 남아 있습니다. 데이터와 직접 상호 작용하는 능력에 제약이 있는지 확인하려면, 메타데이터 카탈로그가 무엇을 하는지 알아야 합니다. 결국 테이블의 메타데이터는 아이스버그의 메타데이터 파일에 저장되지 않습니까? 카탈로그는 테이블에 적어도 두 가지를 제공합니다 (말장난이 아닙니다):

- 데이터베이스 추상화. 아이스버그는 테이블 수준의 기술적 메타데이터 사양이며, 아이스버그 메타데이터 파일은 데이터 파일과 함께 저장됩니다. 테이블 사양은 테이블 이름, 스키마, 데이터베이스 또는 컬렉션이라는 개념을 인식하지 않습니다. 메타데이터 카탈로그를 사용하면 테이블의 "테이블 묶음"을 접두어로 테이블 이름을 매핑하여 데이터베이스처럼 고려할 수 있습니다.
- 현재 테이블 버전을 가리키는 포인터. 아이스버그 테이블을 변경할 때, 새 데이터와 메타데이터 파일이 추가되어 이전 파일 옆에 저장됩니다. 카탈로그는 테이블 접두어를 추적하지만 "현재"인 메타데이터 파일도 알아야 합니다.

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

TL;DR: 현재 테이블 버전을 알기 위해서는 카탈로그에 액세스해야 하며, 테이블 이름으로 테이블에 액세스하고 쿼리를 작성해야 합니다.

![이미지](/assets/img/2024-05-23-QuackQuackKa-ChingCutCostsbyQueryingSnowflakefromDuckDB_2.png)

## 아이스버그 카탈로그 SDK

Spark를 사용하여 Iceberg 테이블을 읽고 싶다면 행운이 따릅니다! Snowflake는 Spark용 Iceberg Catalog SDK를 출시했습니다. 이 SDK는 (그 외 문서화되지 않은) Snowflake Catalog API를 사용하여 Spark의 카탈로그 인터페이스를 구현합니다. 현재 이 Snowflake 기능은 무료이며 실행 중인 데이터웨어하우스가 필요하지 않으며 "서버리스 크레딧" 비용이 필요하거나 "클라우드 서비스" 요금이 발생하지 않습니다.

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

Snowflake의 공지는 쉽게 사용할 수 있는 샘플 코드를 제공하고 Spark가 고객이 관리하는 스토리지 계정에서 Iceberg 메타데이터 및 Parquet 파일을 직접 읽는 것을 확인했습니다.

안타깝게도, 이는 DuckDB에서 쿼리하는 데 즉시 도움이 되지는 않습니다. DuckDB용 Snowflake 카탈로그 SDK가 없습니다. 다행히도, 우리는 파일 시스템을 직접 사용하여 데이터를 읽을 수 있습니다.

![이미지](/assets/img/2024-05-23-QuackQuackKa-ChingCutCostsbyQueryingSnowflakefromDuckDB_3.png)

## Iceberg 파일 시스템 카탈로그

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

파일 시스템이나 객체 저장소 위에 카탈로그를 구현하는 것이 간단한 네이밍 규칙을 통해 가능해 보인다면, 그것이 가능한 이유는 그렇다고요! 실제로, Iceberg의 Hadoop 카탈로그가 바로 그것입니다. 해당 클래스 문서에는 다음과 같이 설명되어 있습니다:

Iceberg는 최신 메타데이터를 알기 위해 파일 시스템 테이블의 메타데이터 파일이 단조로운 증가 버전 번호 함수로 설정된 이름을 가지도록 기대합니다. 또한, 새로운 버전을 가리키는 선택적 version-hint.text 파일을 찾습니다.

Snowflake는 아마도 백엔드에서 독점적이고 높은 성능을 가진 카탈로그 구현을 사용하고 있습니다. 그러나 고객이 관리하는 객체 저장소에 데이터 및 메타데이터를 Hadoop 카탈로그와 호환되는 방식으로 구현하는 것은 충분히 좋습니다. 심지어 현재 버전을 가리키는 version-hint.text 파일을 유지하고 있죠! 이 호환성은 Iceberg Hadoop 카탈로그를 지원하는 모든 리더가 객체 저장소 시스템의 Iceberg 웨어하우스 루트를 가리키도록 설정하여 Snowflake 데이터를 직접 읽을 수 있다는 의미입니다.

## DuckDB

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

DuckDB은 Iceberg Hadoop 카탈로그 및 파일 시스템 테이블에 대한 부분적인 지원을 제공합니다. 유감스럽게도 DuckDB는 아직 전체 데이터 웨어하우스를 읽는 기능을 지원하지는 않지만, 테이블 접두사를 가리키도록 설정할 수 있습니다. DuckDB는 버전 힌트 텍스트 파일을 파악하고 테이블의 최신 버전을 읽을 것입니다.

# Iceberg 테이블 생성하기

Snowflake를 사용하여 클라우드에 Iceberg 테이블을 생성하려면 일부 구성이 필요합니다. 아래 예제는 S3를 저장 레이어로 사용하지만, Snowflake는 Google Cloud Storage 및 Azure Storage도 지원합니다. S3에 대한 플레이북을 여기에서 찾을 수 있습니다:

일반적으로 이렇게 진행해야 합니다:

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

- 스토리지 프로비저닝: S3 버킷을 생성하고 Snowflake용 IAM 역할을 만들어 IAM 역할이 버킷에 액세스할 수 있는 필요한 권한이 부여되도록 합니다.
- 스노우플레이크와 스토리지 연결: Snowflake 외부 볼륨을 생성합니다. S3의 경우, 외부 볼륨은 Snowflake 계정에 IAM 사용자를 생성합니다. IAM 사용자가 S3 버킷에 액세스할 수 있는 권한이 있는 역할을 가정할 수 있도록 신뢰 관계를 만들어야 합니다.

이제 스노우플레이크에서 CREATE ICEBERG TABLE로 네이티브 아이스버그 테이블을 생성할 수 있으며, Parquet과 Iceberg 메타데이터 파일은 S3 버킷에 저장됩니다.

# DuckDB에서 데이터 읽기

S3와 Snowflake 간에 안전한 연결을 설정하고 Snowflake에 아이스버그 테이블을 생성한 후, DuckDB가 이를 쿼리하는 방법을 살펴봅시다.

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

DuckDB의 얼음산 확장 기능을 사용하여 Snowflake에서 직접 S3로 만든 얼음산 테이블을 읽습니다. 여기서 플레이북을 찾을 수 있어요. 주요 기능은 다음 얼음산 스캔 메서드로 제공됩니다:

```js
select * from iceberg_scan('s3://chapter-platform-iceberg/icebergs/line_item';)
```

얼음산 스캔 메서드는 S3에서 테이블을 가져옵니다. 현재 manifest.json 파일을 명시적으로 가리키지 않아도 됩니다. 왜냐하면 version-hint.text가 테이블의 현재 버전을 가리키고 있기 때문입니다.

이제 오픈 테이블 형식의 진정한 힘을 발휘했습니다. Snowflake 및 해당 카탈로그의 편리함을 활용하면서 DuckDB에서 단일 노드 쿼리를 수행하여 비용을 절약할 수 있습니다.

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

# 눈송이가 이렇게 하는 이유는 뭘까요?

눈송이에서 아이스버그 테이블을 사용하는 것은 눈송이가 결제를 지원해주면서 케이크를 먹는 것과 비슷합니다. 그렇다면 왜 눈송이가 이 통합을 만들었을까요? 이 동작은 Databricks로부터의 치열한 경쟁 상황에서 이해할 수 있습니다. 이 두 기업 거물은 모두 시스템을 개방하여 고객을 유치하려고 하고 있습니다.

![이미지](/assets/img/2024-05-23-QuackQuackKa-ChingCutCostsbyQueryingSnowflakefromDuckDB_4.png)

눈송이는 (잠재적인) 고객들에게 눈송이를 선택하더라도 하나의 공급 업체에 얽매이지 않아도 되며 잠금 상태의 위험이 없다는 메시지를 전송합니다. 눈송이를 선택하면 원하는 때에 컴퓨트 엔진을 전환할 수 있는 옵션이 항상 제공된다고 합니다. Databricks도 마찬가지로 Delta Lake 형식을 공개하고 UniForm을 통해 Hudi와 아이스버그를 더 잘 지원하고 있습니다.

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

스노우플레이크는 여전히 가능한 한 많은 컴퓨팅을 자체 시스템에서 유지하고 싶어합니다. 외부 메타데이터를 Iceberg 카탈로그로 이동하는 명확한 방법이 있지만, 그 반대 방향으로 이동하는 것은 훨씬 어렵습니다. 메타데이터 카탈로그를 소유함으로써, 스노우플레이크는 선호되는 컴퓨팅 엔진이자唯一의 작성자로 남게 됩니다. 시스템을 공개하지 않았다면 스노우플레이크는 락인을 두려워하는 많은 고객을 잃었을 것으로 예상됩니다.

# 결론

Iceberg와 같은 오픈 테이블 형식은 컴퓨팅과 스토리지를 진정으로 분리할 수 있게 해줍니다. 스노우플레이크의 Iceberg 테이블을 사용함으로써, 스노우플레이크의 강력하고 운영이 필요 없는 기능을 계속 즐길 수 있으면서 가끔은 "벽에 가둔 정원"을 벗어날 수 있게 됩니다. Iceberg가 Parquet과 함께 갖는 특성과 기능이 스노우플레이크의 네이티브 테이블과 매우 유사하며 효율적인 압축, 파티션 프루닝, 스키마 진화 등과 같은 기능을 제공한다는 점에서, 그리고 스노우플레이크가 이를 위한 지원을 구현했기 때문에, 성능이나 기능에 중대한 영향을 미치지 않는 한 Iceberg 테이블을 네이티브 테이블 대신 사용할 수 있어야 합니다. 따라서 우리는 스노우플레이크에서 기본적으로 Iceberg 테이블을 사용하는 것을 제안합니다.

이 게시물은 비용이 많이 드는 스노우플레이크 컴퓨팅 대신 DuckDB에서 쿼리를 실행하는 방법이 얼마나 쉬운지를 보여 주었습니다. 여러분의 객체 저장소에서 Snowflake가 관리하는 데이터를 직접 가리키는 것으로 독립적으로 실행할 수 있습니다. 거기서는 Snowflake 웨어하우스에 없는 데이터와 조합할 수도 있습니다. 비슷한 성능을 가진 DuckDB 인스턴스에서 실행 시 비용이 대략 스노우플레이크 웨어하우스의 10% 정도인 것을 고려하면, 이러한 방식은 상당한 비용 절감을 가져올 수 있습니다. 물론 DuckDB가 스노우플레이크를 대체한다는 것은 의미하지 않습니다. 이것은 상호 운용성의 힘을 잘 보여 주는 좋은 데모라고 생각합니다.

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

이 게시물은 Jelle De Vleminck, Robbert, Moenes Bensoussia, 그리고 Jonathan Merlevede의 공동 노력의 결과입니다.

- 👏 만약 이 게시물을 좋아하셨다면 갈채 해 주세요
- 🗣️ 의견을 공유해 주세요; 우리는 답변하겠습니다
- 🗞️ 클라우드, 플랫폼, 데이터, 그리고 소프트웨어 엔지니어링에 대한 게시물을 더 보시려면 datamindedbe를 팔로우하고 구독해 주세요
- 👀 Data Minded에 대해 더 알고 싶다면, 저희 웹사이트를 방문해 주세요.

![이미지](/assets/img/2024-05-23-QuackQuackKa-ChingCutCostsbyQueryingSnowflakefromDuckDB_5.png)

![이미지](/assets/img/2024-05-23-QuackQuackKa-ChingCutCostsbyQueryingSnowflakefromDuckDB_6.png)
