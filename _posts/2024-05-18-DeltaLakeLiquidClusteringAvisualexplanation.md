---
title: "델타 레이크 리퀴드 클러스터링 - 시각적 설명"
description: ""
coverImage: "/assets/img/2024-05-18-DeltaLakeLiquidClusteringAvisualexplanation_0.png"
date: 2024-05-18 16:27
ogImage:
  url: /assets/img/2024-05-18-DeltaLakeLiquidClusteringAvisualexplanation_0.png
tag: Tech
originalTitle: "Delta Lake Liquid Clustering — A visual explanation"
link: "https://medium.com/gitconnected/delta-lake-liquid-clustering-a-visual-explanation-b9d8782a9f33"
---

최소한의 노력으로 레이크하우스 데이터 저장 레이아웃을 최적화하는 방법

![image](/assets/img/2024-05-18-DeltaLakeLiquidClusteringAvisualexplanation_0.png)

# 소개

데이터 레이크하우스는 오픈 테이블 형식을 사용하고 특정 공급 업체에 얽매이지 않아 장점을 누립니다. 그러나 이는 특정 읽기 및 쓰기 작업을 위해 데이터 처리를 최적화하기 위해 파일 저장 레이아웃을 최적화해야 한다는 추가적인 부담과 함께 옵니다. 읽기 또는 쓰기 작업에 의해 처리되는 데이터 양을 최소화하기 위해 가능한 한 많은 파일을 제거함으로써 작업을 효율적으로 만드는 것이 핵심 아이디어입니다. 제거는 특정 파일이 해당 쿼리에 관련이 없다는 암묵적 또는 명시적 메타데이터를 사용하여 발생합니다.

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

옛날에, Hive은 각 데이터 파티션을 HDFS 또는 클라우드 저장소의 단일 폴더로 범위를 지정하여 유명한 Hive 스타일의 파티셔닝을 소개했습니다. 그것은 작동이 잘 됩니다. 그러나 작은 파일 문제가 발생하거나 워크로드 특성 변경으로 인해 파티션 체계를 변경해야 할 때 문제가 발생합니다. 또한 Hive 스타일의 파티셔닝은 고 카디널리티 질의에 대해 도움이 되지 않습니다.

오픈 테이블 형식에서 제공되는 DML 지원으로 인해, 조각 모음이나 GDPR에서 잊혀져야 할 권리와 같은 경우를 관리하기 위해 단일 또는 몇 개의 레코드를 업데이트/삭제하는 것이 매우 일반적해졌습니다. 이러한 시나리오에는 고 카디널리티 질의가 효율적이어야 합니다. 이러한 요구 사항을 충족하기 위해 Delta Lake Z-Ordering과 같은 기술이 소개되었습니다. Z-Ordering은 꽤 좋지만 OPTIMIZE 명령을 다시 실행할 때 전체 테이블(또는 파티션)을 최적화하는 반복적인 노력과 많은 낭비된 컴퓨팅 파워를 도입하는 일부 제한 사항이 있습니다. Delta Lake Z-Order의 더 자세한 탐구를 위해 Z-Order에 대한 저의 글을 살펴보십시오. 그 글에서는 낮은 수준의 세부 사항도 약간 논의됩니다.

Hive 스타일의 파티셔닝과 Z-Ordering의 이러한 제한 사항을 완화하기 위해 Databricks 및 Delta Lake 팀은 액체 클러스터링을 소개했습니다. 작성 시점에서 Delta Lake on Databricks에서는 아직 미리보기 상태이며 OSS Delta Lake에서는 실험적인 기능인 상태입니다. 그러나 설계 문서는 누구나 읽을 수 있습니다. 액체 클러스터링은 레코드-파일 할당 방법으로 Hilbert Curve를 사용할 것으로 예상됩니다. 액체 클러스터링의 비전은 다음과 같은 단일 최적화 기술을 가지는 것입니다:

- 저 및 고 카디널리티 질의에 모두 잘 작동합니다.
- "이미 최적화된" 파일을 최적화할 필요가 없습니다.
- 클러스터링 열을 변경하면 전체 테이블을 다시 빌드할 필요가 없습니다.

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

이 게시물에서는 액체 클러스터링이 레코드를 파일에 할당하는 방식을 시각적으로 보여줄 것입니다. 목표는 기술적인 세부 내용을 파헤치는 대신에 해당 기술의 매우 높은 수준의 이해를 갖는 데 초점을 맞추는 것입니다. 여전히 상황이 조리실에 있기 때문에 세부 사항에 대해 심층적으로 파고들지 않습니다.

# 기본으로 돌아가기 — 레코드를 파일에 할당하는 방법은?

우리는 N개의 레코드가 있고 이를 M개의 파일에 쓰려고 한다고 가정해 봅시다. 파일 가지치기의 아이디어를 기억한다면, 비슷한 레코드를 동일한 파일에 저장하는 것이 필수적입니다. 작업 부하에 따라 비슷한 레코드는 같은 픽업 동네의 택시 여행이거나 같은 고객의 은행 거래일 수 있습니다.

아래 streamlit 앱은 이 문제를 처리하는 데 3가지 방법을 보여줍니다. N 및 M에 대한 다양한 값을 사용하고 배치 방식을 조정하여 레코드가 파일에 할당되는 방식을 시각적으로 확인할 수 있습니다. 이 간단한 앱에서는 모든 레코드가 필드 그룹을 갖고 있지만 우리는 2차원 평면 상 좌표인 x 및 y라는 두 개의 정수 필드에 대한 쿼리를 최적화해야 합니다. 레코드는 N개의 레코드를 생성하도록 x와 y의 쌍별 조합을 균등하게 다루기 위해 생성됩니다. 배치 방법 선택에 따라 각 지점(레코드)이 특정 파일에 할당되며 이 할당은 파일 색상을 사용하여 지정됩니다.

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

- 랜덤 할당

이 방식은 사용자 정의 로직을 거의 사용하지 않습니다. 레코드가 무작위로 파일에 할당됩니다.

![Image](/assets/img/2024-05-18-DeltaLakeLiquidClusteringAvisualexplanation_1.png)

2. Z-Ordering 할당

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

다음 방법은 Z-Ordering을 사용하여 각 레코드 (x와 y의 조합)마다 Z-Order 값을 계산하는 것입니다. 이는 평면 상의 이차원 점을 선 상의 점으로 효과적으로 변환합니다. 그런 다음 선을 M개의 세그먼트로 나눌 수 있으며, 각 세그먼트는 하나의 파일을 나타냅니다. 레코드가 Z-Order 값 z를 갖고 있다면, 파일 z % M에 할당됩니다. 이제 점들은 일차원 관련 값을 갖고 있기 때문에, 그러한 값들을 선으로 연결하여 매핑이 어떻게 이루어지는지 시각적으로 확인할 수 있습니다. 각 점 위에 마우스를 올려놓으면 선형 순서 값을 볼 수 있습니다.

![이미지](/assets/img/2024-05-18-DeltaLakeLiquidClusteringAvisualexplanation_2.png)

공간적으로 서로 가까운 레코드들은 Z-Order 라인 상에서 서로 가까이 배치됩니다. 예를 들어, 위 스크린샷을 보면, 점 (2,4)와 (3,4)는 각각 36과 37의 Z-Order 값을 가지고 있습니다. (0,4)에서 (7,3)으로 이동하는 것과 같이, 공간적으로 멀리 떨어져 있지만 연이은 Z-Order 값을 가진 큰 점프가 보이기도 합니다. 그럼에도 불구하고, Z-Ordering은 좋은 데이터 로컬리티 할당을 생성합니다.

3. 힐버트 곡선 할당

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

세 번째 방법은 레코드마다 두 차원 x와 y 값에 기초한 일차원 값을 할당하기 위해 힐버트 곡선을 사용하는 것입니다.

![image](/assets/img/2024-05-18-DeltaLakeLiquidClusteringAvisualexplanation_3.png)

이 Python 라이브러리를 사용하여 두 차원 점에 대한 힐버트 곡선 값을 도출했습니다. 이는 Z-Order와 비슷한데, 서로 가까운 포인트들은 동일한 파일에 들어가게 되지만, 일차원 할당에서 멀리 떨어진 지점이 연속적으로 배치되는 급격한 점프가 없다는 추가적인 이점이 있습니다.

이제 우리는 Z-Order와 힐버트 곡선과 같은 공간 채우기 곡선을 사용하여 파일에 포인트를 할당하는 방법에 대한 아이디어가 생겼으니, Databricks에서 Liquid Clustering을 살펴보겠습니다.

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

# Liquid clustering in action

이 섹션의 실험은 최소한이지만 대표적입니다. 필요한 설정은 다음과 같습니다:

- Azure 무료 평가판 계정 및 무료 Databricks 계정
- Liqud 클러스터링을 지원하는 최신 DBR인 DBR 13.3을 사용하는 단일 노드 Databricks 클러스터
- 빠른 속도와 모자이크가 작동하기 위해 클러스터에서 photon을 활성화

우리는 유명한 뉴욕시 택시 데이터 세트를 사용하고 아래 워크로드를 위해 최적화할 것입니다:

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

- 대부분의 쿼리는 데이터의 한 해 또는 몇 해에 대해서 작동할 것입니다.
- 많은 쿼리는 픽업 위치(위도 및 경도)를 기반으로 필터링하는 것이 포함될 것입니다.

Databricks 워크스페이스에 파이썬 노트북을 만들고 샌드박스 데이터베이스를 생성하는 방법을 시작해보세요.

```js
%sql
CREATE DATABASE liquid_db;
```

다음으로, 맨해튼 섬 주변의 경계 상자를 기준으로 뉴욕시 택시 데이터셋을 기반으로 하는 테이블을 생성해보세요. 이 글의 몇 가지 미학적 이유로 테이블은 초기에 액체 클러스터링을 사용할 수 있지만, 모든 작업이 기본적으로 데이터를 클러스터링하는 것은 아님을 인식하셔야 합니다. 예를 들어, 데이터가 MERGE 작업으로 변경되면, 데이터를 클러스터링하기 위해 OPTIMIZE 작업을 실행해야 할 것입니다.

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

```sql
CREATE TABLE liquid_db.trips
    CLUSTER BY (pickup_datetime, pickup_latitude, pickup_longitude)
AS
SELECT *
FROM delta.`dbfs:/databricks-datasets/nyctaxi/tables/nyctaxi_yellow`
WHERE
    pickup_longitude between -74.05186503267184 and -73.83200446816883 AND
    pickup_latitude between 40.69286486137213 and 40.91947608519337
```

클러스터링 열 목록에서 첫 번째 열은 타임스탬프 열인 픽업 일시임을 주목해주세요. 우리는 하이브 스타일의 파티셔닝을 사용하기 위해 명시적으로 연도 열을 생성할 필요가 없습니다.

나중에 특정 Delta Lake 트랜잭션에서 생성된 파일이 클러스터링되었는지 여부를 감지하는 방법을 보여줄 텐데요, 제 경우에는 파일이 액체 클러스터링되지 않았기 때문에 직접 클러스터링해야 했습니다.

```sql
OPTIMIZE liquid_db.trips
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

델타 레이크 Z-오더링과 달리, 리퀴드 클러스터링을 사용하여 테이블을 최적화할 때, 파일이 최적화되었는지 여부를 알려주는 트랜잭션 로그 메타데이터가 있습니다. 따라서 나중에 OPTIMIZE 명령을 실행하여 새로운 데이터를 클러스터링할 때 파일을 건너뛸 수 있습니다. 이러한 경우에 대해 더 많은 아이디어가 있지만, 핵심적인 차이점은 ADD 프로토콜 액션의 태그 부분에 LIQUID_METADATA_ID라는 새 메타데이터 항목이 있는 것입니다.

```js
import pyspark.sql.functions as F
second_log_file = "dbfs:/user/hive/warehouse/liquid_db.db/trips/_delta_log/00000000000000000001.json"
(
    spark.read
    .json(second_log_file)
    .where("add is not null")
    .select("add.size", "add.tags.*")
    .withColumn("size", F.expr("cast(size/1024/1024 as int)"))
    .withColumnRenamed("size", "size_mb")
    .display()
)
```

위 스니펫의 출력에서 제 경우 191개의 파일이 나오며 대부분의 크기는 100에서 300MB 범위에 있습니다.

<img src="/assets/img/2024-05-18-DeltaLakeLiquidClusteringAvisualexplanation_4.png" />

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

이제 OPTIMIZE 작업을 위해 트랜잭션 로그 엔트리 안에 수집된 최대 및 최소 메타데이터 값을 검토해 봅시다.

```js
import pyspark.sql.functions as F
def load_stats_from_commit(commit_file):
  df = spark.read.json(commit_file).where("add is not null")
  add_schema = """
  struct
    <
      numRecords:long,
      minValues: struct<pickup_latitude: double,pickup_longitude: double, pickup_datetime: timestamp>,
      maxValues: struct<pickup_latitude: double,pickup_longitude: double, pickup_datetime: timestamp>
    >
  """

  stats = (
    df
      .select("add.path", "add.size",
          F.from_json("add.stats", add_schema).alias("stats")
      )
      .selectExpr(
        "substring(path, 1, 10) as file",
        "size",
        "stats.minValues.pickup_datetime as min_pickup_datetime",
        "stats.maxValues.pickup_datetime as max_pickup_datetime",
        "stats.minValues.pickup_latitude as min_pickup_latitude",
        "stats.maxValues.pickup_latitude as max_pickup_latitude",
        "stats.minValues.pickup_longitude as min_pickup_longitude",
        "stats.maxValues.pickup_longitude as max_pickup_longitude"
      )
  )

  stats = (
    stats
      .withColumn("rect", F.expr(
        """
          concat('POLYGON ((' ,
            min_pickup_longitude, ' ', min_pickup_latitude, ',' ,
            max_pickup_longitude, ' ', min_pickup_latitude, ',' ,
            max_pickup_longitude, ' ', max_pickup_latitude, ',' ,
            min_pickup_longitude, ' ', max_pickup_latitude, ',' ,
            min_pickup_longitude, ' ', min_pickup_latitude,
          '))'
          )
      """))
  )

  return stats

stats = load_stats_from_commit(second_log_file).orderBy("min_pickup_datetime", "max_pickup_datetime")
stats.display()
```

위의 "난해한" 코드 스니펫은 몇 가지 작업을 수행합니다:

- 픽업 시간, 위도 및 경도에 대한 최소 및 최대 값 수집
- 가독성 목적을 위해 파일 이름의 처음 10자를 고유 식별기로 사용
- 파일 내의 모든 여행을 포함하는 경계 상자의 GeoJSON 표현 생성 (픽업 위치에 따라)

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

상단의 표 출력물은 특별히 흥미로운 것은 아니며, 파일이 클러스터링 열에 따라 어떻게 배치되었는지 쉽게 전달하지 않습니다. 그러나 이를 어떤 종류의 간트 차트로 시각화한다면, 파일이 시간별 범위를 포함하는 그룹으로 클러스터링되었음이 명백해질 것입니다. 파일 중첩이 발생할 수 있지만, 일반적인 주제는 시간 범위를 기반으로 한 클러스터링을 보여줍니다.

```js
import plotly.express as px
fig = px.timeline(stats.toPandas(),
    x_start="min_pickup_datetime",
    x_end="max_pickup_datetime",
    y="file")
fig.update_yaxes(categoryorder="min ascending")
fig.show()
```

<img src="/assets/img/2024-05-18-DeltaLakeLiquidClusteringAvisualexplanation_5.png" />

Jan 2009부터 April 2010까지의 파일을 살펴보겠습니다.

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
file_group = (
  stats
    .where("""
    min_pickup_datetime >= '2009-01-01' AND
    max_pickup_datetime <= '2010-04-30'
    """
    )
)
file_group.count()
# 29개의 파일이 인쇄됩니다.
```

해당 Date Range를 공유하는 이 파일들이 커버하는 지리 공간 영역을 시각화하고 싶습니다.

```js
%pip install databricks-mosaic==0.4.0
```

```js
import mosaic as mos
spark.conf.set("spark.databricks.labs.mosaic.index.system", "H3")
mos.enable_mosaic(spark, dbutils)
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

%%mosaic_kepler
file_group "rect" "geometry"

![Image](/assets/img/2024-05-18-DeltaLakeLiquidClusteringAvisualexplanation_6.png)

동일한 파일 그룹에 대해 특정 날짜 범위를 포괄하는 파일들의 경우, 해당 파일들의 레코드는 잔여 클러스터링 키인 위도 및 경도를 기반으로 클러스터링됩니다. 이러한 공간 클러스터링을 통해 지구상의 특정 지점을 커버하는 파일 수가 현저히 줄어들어 파일 가지치기가 크게 향상됩니다.

# 가지치기 혜택

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

아래와 같은 쿼리를 실행하면 194개 파일 중 126개 파일을 제거합니다 (첫 번째 커밋에서 최적화되지 않은 파일이 3개 발생했습니다).

```js
SELECT payment_type, sum(total_amount) as total_amount
FROM liquid_db.trips
WHERE pickup_datetime >= '2011-01-01' AND pickup_datetime < '2012-01-01'
GROUP BY payment_type
```

![이미지](/assets/img/2024-05-18-DeltaLakeLiquidClusteringAvisualexplanation_7.png)

위의 쿼리는 8년 데이터 중 1년치의 집계 결과입니다. 순수 Hive 파티셔닝이면 더 좋은 프루닝이 가능할 수도 있지만, 여전히 집계 쿼리에 대한 일정한 값은 얻을 수 있습니다.

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

만약 같은 해를 사용하지만 타임스 스퀘어 근처의 몇 가지 특정 레코드를 찾아보려 한다면, 더 나은 가지치기를 할 수 있어요.

```js
SELECT *
FROM liquid_db.trips
WHERE pickup_datetime >= '2011-01-01' AND pickup_datetime < '2012-01-01'
AND pickup_latitude BETWEEN 40.757816 AND 40.757832
AND pickup_longitude BETWEEN -73.985143 AND -73.985105
```

![이미지](/assets/img/2024-05-18-DeltaLakeLiquidClusteringAvisualexplanation_8.png)

특정 워크로드에 유용한지 확인하기 위해 철저한 테스트와 벤치마킹이 필요하지만, 전반적으로 Delta Lake 테이블의 관리를 간편하게 해주는 Liquid 클러스터링은 매우 유망해 보입니다.

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

# 마무리

리퀴드 클러스터링을 사용할 때 고려해야 할 측면이 많으며 특정 사용 사례에 맞는 동작을 조정하기 위해 많은 구성 값을 조정해야 할 것입니다. 본 게시물은 리퀴드 클러스터링이 어떻게 작동하는지를 높은 수준에서 시각적으로 보여주는 작은 시도입니다. 단순화된 사용 사례는 Hive 스타일의 파티셔닝과 Z-Order의 혜택을 결합하여 단일 최적화 방법을 사용하는 것입니다.

liquid_db를 삭제하고 정리하려면 DROP DATABASE liquid_db CASCADE를 실행하는 것을 잊지 마십시오.

# 추가 읽을거리

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

- 델타 테이블에 리퀴드 클러스터링 사용하기
- [디자인 문서] [공개] 리퀴드 클러스터링 — Google Docs
- 힐버트 곡선 코딩 (youtube.com)
- Yousry Mohamed의 미디엄에서 A부터 Z까지의 델타 레이크 Z-오더링
