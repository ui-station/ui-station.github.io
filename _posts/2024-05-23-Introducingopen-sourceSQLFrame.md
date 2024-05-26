---
title: "오픈소스 SQLFrame을 소개합니다 "
description: ""
coverImage: "/assets/img/2024-05-23-Introducingopen-sourceSQLFrame_0.png"
date: 2024-05-23 16:00
ogImage:
  url: /assets/img/2024-05-23-Introducingopen-sourceSQLFrame_0.png
tag: Tech
originalTitle: "Introducing open-source SQLFrame! 🎉"
link: "https://medium.com/towardsdev/sqlframe-turning-pyspark-into-a-universal-dataframe-api-e06a1c678f35"
---

13년간 데이터 엔지니어로 근무하면서 변화에 익숙해졌어요. 클라우드 이전과 같은 중요한 변화나 노트북 활용과 같은 작은 트렌드와 같은 것들이죠. 이 모든 변화 속에서도 하나는 불변해 왔어요: SQL이죠. 스타트업부터 FAANG까지 다양한 회사에서 일한 경험을 통해 알게 된 것은 SQL을 잘 이해하고 작성해야 한다는 것이었어요. SQL은 모든 데이터 전문가들을 통합하는 보편적 언어이며, 복잡한 분산 처리의 세부사항을 처리하는 쿼리 플래너와 옵티마이저를 통해 효율적인 데이터 파이프라인을 구축할 수 있게 해줘요.

SQL의 강점에도 불구하고, 이 언어는 종종 데이터 파이프라인 유지 관리에는 적합하지 않아 보일 수 있어요. 이 언어는 일반적인 작업을 추상화하거나 코드의 특정 세그먼트에 대한 단위 테스트를 지원하지 않아서, 많은 사람들이 임시 방법으로 Jinja를 사용하곤 해요. Jinja SQL은 SQL의 Pig Latin과 같은 관계로, 작은 량에서는 재미있을 수도 있지만, 대규모로 확장하면 이해하기 어려워지기도 해요. 더구나, SQL의 반복적인 특성은 열을 반복해서 지정해야 하는 것으로, 종종 데이터 전문가들 사이에서 피로감을 일으킬 수 있어요.
결국, 데이터 전문가들은 SELECT \*의 유혹에 반응하며 불확실성의 바다에서 침몰하게 되기도 해요.

이로써 데이터 전문가들이 어려운 선택을 하게 되었어요: 접근성을 우선시하여 SQL로 파이프라인을 작성할 것인가요, 아니면 유지보수성을 우선시하여 Python으로 작성할 것인가요? 오늘부터는 더 이상 선택할 필요가 없어요. 이제 여러분은 동시에 케이크를 먹고 가질 수 있게 되었어요.

![image](https://miro.medium.com/v2/resize:fit:1400/1*JZ4jUIBrQAf-oovf3IFN1w.gif)

<div class="content-ad"></div>

# 오픈소스 SQLFrame 소개! 🎉

![이미지](/assets/img/2024-05-23-Introducingopen-sourceSQLFrame_0.png)

SQLFrame은 데이터 전문가들이 SQL 및 PySpark 데이터프레임과 상호 작용하는 방식을 혁신합니다. 전통적인 PySpark과 달리 SQLFrame은 DataFrame 작업을 직접 SQL로 변환하여 개발 중에 실시간 SQL 스크립트 생성을 가능하게 합니다. 작동 방법은 다음과 같습니다:

공개적으로 접근 가능한 출생 데이터를 기반으로 단일 아동을 선택한 새 가족의 수를 분석하는 시나리오를 고려해 보겠습니다.

<div class="content-ad"></div>

js
from sqlframe.bigquery import BigQuerySession
from sqlframe.bigquery import functions as F
from sqlframe.bigquery import Window

# SQLFrame에서 제공하는 고유 기능: 빅쿼리에 직접 연결할 수 있는 능력
session = BigQuerySession(default_dataset="sqlframe.db1")
table_path = "bigquery-public-data.samples.natality"
df = (
    session.read.table(table_path)
    .where(F.col("ever_born") == 1)
    .groupBy("year")
    .agg(F.count("*").alias("num_single_child_families"))
    .withColumn("percent_change", 1 - F.lag(F.col("num_single_child_families"), 1).over(Window.orderBy("year")) / F.col("num_single_child_families"))
    .orderBy(F.abs(F.col("percent_change")).desc())
    .select(
        F.col("year").alias("Year"),
        F.format_number("num_single_child_families", 0).alias("number of new families single child"),
        F.format_number(F.col("percent_change") * 100, 2).alias("percent change"),
    )
    .limit(5)
)
# SQLFrame에서 제공하는 고유 기능: DataFrame의 SQL 확인 가능
df.sql()


SQLFrame를 사용하면 특별한 빅쿼리 클래스를 활용하여 빅쿼리 환경과 시스템을 원활하게 통합할 수 있습니다. DataFrame 작업은 PySpark에서 수행하는 것과 유사하지만 SQLFrame를 이용하면 df.sql() 메서드를 사용하여 실시간으로 생성 및 검토하는 대응하는 SQL 쿼리도 볼 수 있습니다.

js
WITH `t94228` AS (
  SELECT
    `natality`.`year` AS `year`,
    COUNT(*) AS `num_single_child_families`
  FROM `bigquery-public-data`.`samples`.`natality` AS `natality`
  WHERE
    `natality`.`ever_born` = 1
  GROUP BY
    `natality`.`year`
), `t34770` AS (
  SELECT
    `t94228`.`year` AS `year`,
    `t94228`.`num_single_child_families` AS `num_single_child_families`,
    1 - LAG(`t94228`.`num_single_child_families`, 1) OVER (ORDER BY `t94228`.`year`) / `t94228`.`num_single_child_families` AS `percent_change`
  FROM `t94228` AS `t94228`
  ORDER BY
    ABS(`percent_change`) DESC
)
SELECT
  `t34770`.`year` AS `year`,
  FORMAT('%\'.0f', ROUND(CAST(`t34770`.`num_single_child_families` AS FLOAT64), 0)) AS `number of new families single child`,
  FORMAT('%\'.2f', ROUND(CAST(`t34770`.`percent_change` * 100 AS FLOAT64), 2)) AS `percent change`
FROM `t34770` AS `t34770`
LIMIT 5


이 기능은 이해를 증진시킬 뿐만 아니라 SQL 출력이 결정론적이어서 버전 관리에 적합하게 만듭니다. 이렇게 함으로써 파이썬 및 SQL 파이프라인의 표현을 모두 버전 관리할 수 있고 동료들이 가장 잘 맞는 형식을 선택할 수 있게 합니다!



<div class="content-ad"></div>



![SQLFrame](https://miro.medium.com/v2/resize:fit:808/1*y_ZC1qkDPllTA3Yk3XiC8A.gif)

SQLFrame은 SQL을 생성하는 것 이상을 제공합니다: PySpark DataFrame API가 모든 주요 데이터 웨어하우스에서 네이티브 DataFrame API처럼 느껴지게 하는 것이 목표입니다. 따라서 사용자들은 스파크 클러스터나 라이브러리 없이 데이터 웨어하우스에서 DataFrame API 파이프라인을 직접 실행할 수 있습니다!

예를 들어, .sql()을 .show()로 바꾸면 파이프라인에서 빅쿼리에서 결과를 직접 표시할 수 있습니다. 이는 PySpark에서와 같은 방식으로 작동합니다.

python
>>> df.show()
+------+-------------------------------------+----------------+
| year | number of new families single child | percent change |
+------+-------------------------------------+----------------+
| 1989 |              1,650,246              |     20.01      |
| 1974 |               783,448               |     12.66      |
| 1977 |              1,057,379              |     10.22      |
| 1985 |              1,308,476              |     10.03      |
| 1975 |               868,985               |      9.84      |
+------+-------------------------------------+----------------+


`

<div class="content-ad"></div>

많은 카탈로그 작업이 지원되며 listColumns와 같은 것이 지원됩니다:

```js
>>> columns = session.catalog.listColumns(table_path)
>>> print("\n".join([f"Name: {x.name}, Data Type: {x.dataType}, Desc: {x.description}" for x in columns]))
Name: source_year, Data Type: INT64, Desc: Four-digit year of the birth. Example: 1975.
Name: year, Data Type: INT64, Desc: Four-digit year of the birth. Example: 1975.
Name: month, Data Type: INT64, Desc: Month index of the date of birth, where 1=January.
Name: day, Data Type: INT64, Desc: Day of birth, starting from 1.
Name: wday, Data Type: INT64, Desc: Day of the week, where 1 is Sunday and 7 is Saturday.
Name: state, Data Type: STRING, Desc: The two character postal code for the state. Entries after 2004 do not include this value.
```

따라서 SQLFrame은 단순히 DataFrame 파이프라인을 더욱 접근 가능하게 만들 뿐만 아니라, PySpark DataFrame API를 보다 범용의 DataFrame API로 변환하여 모든 데이터 전문가가 즐길 수 있습니다!

<img src="https://miro.medium.com/v2/resize:fit:720/1*JQ7uBfQn-4VWWWlfl5D_sA.gif" />

<div class="content-ad"></div>

SQLFrame은 현재 BigQuery, DuckDB 및 Postgres를 지원하고 있으며, Clickhouse, Redshift, Snowflake, Spark 및 Trino가 개발 중에 있습니다. 다른 엔진을 위한 SQL 생성 실험을 원하는 경우 Standalone 세션에서 유연한 테스트 환경을 제공합니다.

SQLFrame을 시작하려면 GitHub 리포지토리를 확인해보세요!

![SQLFrame 소개](/assets/img/2024-05-23-Introducingopen-sourceSQLFrame_1.png)
