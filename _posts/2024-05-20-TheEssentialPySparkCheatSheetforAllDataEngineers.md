---
title: "모든 데이터 엔지니어를 위한 필수 PySpark 차트 시트"
description: ""
coverImage: "/assets/img/2024-05-20-TheEssentialPySparkCheatSheetforAllDataEngineers_0.png"
date: 2024-05-20 16:59
ogImage: 
  url: /assets/img/2024-05-20-TheEssentialPySparkCheatSheetforAllDataEngineers_0.png
tag: Tech
originalTitle: "The Essential PySpark Cheat Sheet for All Data Engineers."
link: "https://medium.com/towards-artificial-intelligence/simplify-your-data-engineering-journey-the-essential-pyspark-cheat-sheet-for-success-69db0c38b31e"
---


## 모든 Pyspark 스크립트 작성을 수정하고, 시간을 절약한 상태로 현명하게 연습하세요.

"일을 맹목적으로 끝내는 것보다는 최적의 효율성과 효과적인 방법으로 실행하는 것이 중요합니다."

여기 제공된 치트 시트는 PySpark의 주요 측면을 신속하게 검토하고, 인터뷰 준비 또는 Databricks나 Python 기반 코딩 환경과 같은 다양한 플랫폼에서 데이터 분석 작업에 대비할 수 있게 돕습니다.

이 도구를 이용하면 PySpark 및 관련 프레임워크에 필수적인 중요한 변환 기술 및 데이터 분석 방법론을 신속하게 검토할 수 있습니다.

<div class="content-ad"></div>

## 시작해봅시다

PySpark를 사용하여 변환 작업이나 데이터 분석 작업을 시작하기 전에 Spark 세션을 설정하는 것이 중요합니다. 이 초기 단계는 Spark 프레임워크 내에서 코드 실행의 기반을 제공합니다. 더 중요한 것은 후속 작업을 위한 기반을 구축하고 Spark 기능과 원활하게 상호 작용할 수 있도록 합니다.

먼저 의도한 작업에 필요한 모듈을 가져와야 하며, 모든 중요한 구성 요소가 사용 가능하도록 보장해야 합니다. 이 기본 절차를 준수함으로써 개발자와 데이터 전문가는 PySpark의 강력한 기능을 활용하여 데이터 처리와 분석 작업을 원활하게 수행할 수 있습니다.

## 시작해봅시다!!

<div class="content-ad"></div>

```md
from pyspark.sql import SparkSession
from pyspark.sql import types
from pyspark.sql.types import SparkType, StructField, StringType, IntegerType, DataType
from pyspark.sql.functions import col, date, year, time, sum, avg, upper, count, Broadcast, expr
from pyspark.sql import window
from pyspark.sql import functions as F
```

```md
spark = SparkSession.builder.appName("application").getOrCreate()
# read any file as given either csv, excel, parquet, or Avro any format of data
data = spark.read.csv("filePath", header=True, inferschema=True) # if we want given data types as it is
schema = StructType([StructField("id", IntegerType), StructField("name", StringType),
StructField("dept", StringType)]) # if we want our required data types then we use this 
# also for better performance of executions we will be using our custom schema rather depending on inferschema
```

Let’s kickstart our PySpark application by first creating a Spark Session, the entry point to PySpark functionality.

We’ll then proceed with performing various transformations and analyses on sample data.


<div class="content-ad"></div>

```js
데이터 = [(1, 'mahi', 100), (2, 'mahendra', 200), (3, 'harish', 300), (4, 'desh', 400)]

스키마 = ['id', 'name', 'salary']
# 데이터 프레임 생성
df = spark.createDataFrame(data, schema)
df.head()
df.show()
display(df)
```

![image](/assets/img/2024-05-20-TheEssentialPySparkCheatSheetforAllDataEngineers_0.png)

PySpark에서 윈도우 함수를 사용하여 급여의 누적 합을 계산할 수 있습니다.

```js
a = Window().orderBy('id')
누적_합 = df.withColumn("cumulative_sum", sum("salary").over(a))
결과 = cumulative_sum.orderBy('id')
결과.show()
```

<div class="content-ad"></div>

```python
a = Window().orderBy('id')
누적합 = df.withColumn("누적합", avg("salary").over(a))
결과 = 누적합.orderBy('id')
결과.show()
```

![이미지](/assets/img/2024-05-20-TheEssentialPySparkCheatSheetforAllDataEngineers_1.png)

```python
emp=[(1,'마히', 100,1),(2,'마헨드라', 200,2),(3,'하리쉬',300,3),
(4,'데시',400,4)]

스키마=['id', 'name', 'salary', 'dept_id']
# 데이터 프레임 생성
df=spark.createDataFrame(data,schema)
df.head()
df.show()
display(df)

dept=[(1,'인사'),(2,'영업'),(3,'데이터 분석'),(4,'IT')]
스키마=['dept_id', 'department']
부서=spark.createDataFrame(dept,schema)
display(부서)
```

![이미지](/assets/img/2024-05-20-TheEssentialPySparkCheatSheetforAllDataEngineers_2.png)


<div class="content-ad"></div>

공통 속성인 dept_id를 사용하여 두 개의 데이터 프레임을 결합해 봅시다. 예시를 들어보겠습니다:

```js
df=employee.join(department, "dept_id", "inner").select('id','name','salary','department')
display(df)
```

```js
df=employee.join(department, "dept_id", "right").select('name','department')
display(df)
```

<img src="/assets/img/2024-05-20-TheEssentialPySparkCheatSheetforAllDataEngineers_3.png" />

<div class="content-ad"></div>

위에서는 다양한 PySpark 변환, 액션 및 기능에 대해 상세히 설명하겠습니다. 실행 가능한 코드 예제와 함께 다룰 예정이에요.

기본적인 변환인 필터링 및 그룹화부터 창 함수와 같은 고급 기술까지 다양한 작업을 다룰 거에요.

각 코드 조각은 개념을 포괄적으로 이해할 수 있도록 설명하는 문장과 함께 제공됩니다.

데이터 조작 작업부터 시작하여 PySpark 데이터 프레임을 사용하여 데이터 필터링, 선택 및 집계를 살펴보겠습니다. 그 후에는 조인, 정렬 및 창 함수와 같은 변환을 통해 다수의 테이블에서 데이터를 조작하고 분석하는 방법을 살펴볼 것입니다.

<div class="content-ad"></div>

또한, 우리는 샘플 데이터셋을 활용하여 모델 학습과 평가를 통해 머신러닝 작업을 지원하는 Pyspark을 소개할 것입니다.

이 치트 시트를 통해 각 코드 스니펫은 해당 개념의 실제 시연을 제공하여 빠른 참고와 이해를 돕게 됩니다.

이 예제를 따라가며 사용자들은 Pyspark 능력을 향상시키고 데이터 엔지니어링 및 데이터 과학 인터뷰나 실제 데이터 처리 작업에 대비할 수 있습니다.

## 필터링, 선택, 집계, 그룹화 및 정렬 조건:

<div class="content-ad"></div>

```js
df = orders.join(products, "order_id", "inner") # 어떤 조인 적용
df.join(df2, '공통 열').groupBy('표시할 열').count().orderBy(desc('count'))


df1=df.groupBy("cust_id").agg(sum("amount").alias("bill")) # 그룹화 함수 적용 및 집계 조건을 지정

df.groupBy("col1").agg(count("col2").alias("count"), 
                          sum("col2").alias("sum"),
                          max("col2").alias("maximum"),
                          min("col2").alias("minimum"), 
                          avg("col2").alias("average")).show()


df.drop("column_name1", "column_name2", "column_name3") # 열 삭제
df.drop(col("column_name")) # 다른 열 삭제 방법

df.createOrReplaceTempView("원하는 이름 지정") # 데이터 프레임을 테이블로 변환

df.orderBy(F.desc("column_name")).first() # 특정 열(예: 급여)의 내림차순 첫 번째 행 반환
df.orderBy(col("column_name").desc()).first() # 가장 높은 값을 반환하는 또 다른 방법
df.orderBy(col("column_name").desc()).limit(5) # 상위 5개의 값을 반환

# 원하는 열에 필터 적용
df.filter(df.column_name==값).show()

# 필터링된 결과와 함께 필요한 열 선택
df.select("column1", "column2", "column3").where(col("any column")=="any value")
df.select("column1").where(col("column1")> 값).show(5)
df.sort("원하는 열 이름")

# 열 이름 변경
df.withcolumn Renamed("기존 열 이름", "원하는 열 이름으로 변경")
```

PySpark는 데이터 프레임 내에서 날짜 속성을 추출하고 조작하는 편리한 방법을 제공하며, 사용자가 연도, 월, 일과 같은 다양한 기준으로 통찰력을 얻을 수 있도록 합니다.

또한, 이러한 속성들은 오름차순이나 내림차순으로 정렬하여 분석과 시각화를 용이하게 할 수 있습니다.

## 날짜 열에서 일, 월, 연도 추출하기: 

<div class="content-ad"></div>


# 데이터 프레임에서 연도, 월, 일 세부 정보 추출하기
df.select(year("date column").distinct().orderBy(year("date column")).show()
df.select(month("date column").distinct().orderBy(month("date column")).show()
df.select(day("date column").distinct().orderBy(day("date column")).show()

df.withColumn("orderyear", year("df.date column"))
df.withColumn("ordermonth", month("df.date column"))
df.withColumn("orderday", day("df.date column"))
df.withColumn("orderquarter", quarter("df.date column"))


특정 열에서 null 값을 필터링하고 그 다음에 지정된 순서로 그룹화 작업을 수행하기 위해 조건을 적용할 수 있습니다.


df.select("column name we want to retrieve").where(col("column name we want to retrieve").isNotNull())\
.groupBy("column name we want to retrieve").count().orderBy("count", ascending=False).show(10)


## 함수 작성하기:```

<div class="content-ad"></div>

```js
df.write.format("CSV").mode("overwrite").save("원하는 파일 저장 경로")
df.write.format("CSV").mode("append").save("원하는 파일 저장 경로")
df.write.format("Parquet").mode("overwrite").save("원하는 파일 저장 경로")
df.write.format("parquet").mode("append").save("원하는 파일 저장 경로")
```

## 윈도우 함수:

```js
wind_a=Window.partitionBy("col1").orderBy("col2").rangeBetween(Window.unboundedpreceeding, 0)

df_w_coloumn= df.withColumn("col_sum", F.sum("salary").over(wind_a) #롤링 합계 또는 누적 합계:


#행 번호
a=Window.orderBy("date_column") #예시로 날짜 열을 고려하였지만 원하는 열을 선택할 수 있습니다
sales_data=df.withColumn("row_number", row_number().over(a))

#순위
b=Window.partitionBy("date").orderBy("sales")
sales_data=df.withColumn("sales_rank", rank() over(b))

#조밀한 순위
b=Window.partitionBy("date").orderBy("sales")
sales_data=df.withColumn("sales_dense_rank", desne_rank() over(b))


#지연
c=Window.partitionBy("Item").orderBy("date") #예시 열을 고려하여 원하는 열을 선택할 수 있습니다
sales_data=df.withColumn("pre_sales", lag(col("sales"),1).over(c))


#리드
d=Window.partitionBy("Item").orderBy("date") #예시 열을 고려하여 원하는 열을 선택할 수 있습니다
sales_data=df.withColumn("next_sales", lead(col("sales"),1).over(d))
```

<div class="content-ad"></div>

이 기사는 데이터 엔지니어링 면접에 대비하는 개인들에게 가치 있는 도구로 작용하며, Databricks 플랫폼을 위해 구체적으로 맞춘 PySpark 함수와 수식들에 대한 간결하면서 포괄적인 요람을 제공합니다.

구조화된 레이아웃과 각 예제와 함께 제공되는 자세한 설명으로, 본 자료는 독자들이 10분만 투자하여 주요 개념들을 효율적으로 검토하고 숙지할 수 있도록 도와줍니다.

이러한 기본 기능들을 숙달함으로써 희망하는 데이터 엔지니어들은 자신감을 키우고 다양한 면접 상황을 쉽게 해결할 수 있는 준비를 갖추게 되며, 이를 통해 더욱 흥미로운 데이터 엔지니어링 직무를 얻을 성공 기회를 극대화할 수 있습니다.

![이미지](/assets/img/2024-05-20-TheEssentialPySparkCheatSheetforAllDataEngineers_4.png)

<div class="content-ad"></div>

아래에서 데이터 엔지니어 인터뷰를 준비하는 데 도움이 되는 몇 가지 더 많은 기사를 찾을 수 있습니다.

- SQL 사용 사례 - 데이터 엔지니어 인터뷰
- 데이터 엔지니어 인터뷰에서 가장 일반적으로 묻는 빅데이터(Apache Spark) 개념
- 데이터 엔지니어 인터뷰를 위한 Python 코딩 문제 제1부 (쉬운 난이도)

<div class="content-ad"></div>

제 소중한 내용을 더 많이 공유할 수 있도록 함성 소리로 응원해주신다면 감사하겠습니다. 

저를 팔로우하고 구독하여 제 소식을 즉시 받아보세요. 

감사합니다 :)