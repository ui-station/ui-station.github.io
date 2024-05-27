---
title: "델타 레이크에서의 스키마 진화 - 데이터브릭스"
description: ""
coverImage: "/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_0.png"
date: 2024-05-27 17:09
ogImage: 
  url: /assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_0.png
tag: Tech
originalTitle: "Schema Evolution in Delta Lake - Databricks"
link: "https://medium.com/@oindrila-chakraborty88/schema-evolution-in-delta-lake-databricks-ccb567262ea9"
---


요즘 현대의 빅 데이터 세계에서는 클라이언트가 소스 빅 데이터 파일을 보내서 처리하는 경우가 많습니다. 이 소스 파일의 "구조"는 시간이 지남에 따라 계속 변화합니다.

소스 빅 데이터 파일과 처리할 때 "스키마 불일치"를 처리하는 적절한 메커니즘이 사용되지 않으면, 데이터가 최종적으로 저장되는 "대상 테이블"과 소스 파일로부터 데이터를 처리하는 전체 "파이프라인"이 실패할 수 있습니다.

"스키마 불일치" 상황을 처리하기 위해, Databricks는 "스키마 병합(Merge Schema)"이라는 기능을 제공합니다.

# "스키마 진화(Schema Evolution)" 소개

<div class="content-ad"></div>

가정해보겠습니다. 도착한 소스 대규모 데이터 파일의 데이터 처리를 브론즈 레이어의 델타 테이블로 처리하는 "파이프라인"이 있습니다. 이 "구조"는 다음과 같습니다 -

![structure](/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_0.png)

또한, 이와 같은 "구조"를 가진 델타 테이블이 브론즈 레이어에 생성되었습니다.

![schema](/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_1.png)

<div class="content-ad"></div>

특정 시간이 지난 후에, 도착하는 소스 대용량 데이터 파일의 "구조"가 다음과 같이 추가 열 "City"를 수용할 수 있도록 변경됩니다 -

![image](/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_2.png)

하지만, 이 새로운 추가 열 "City"는 도착하는 소스 대용량 데이터 파일의 초기 "구조"를 기반으로 생성된 Bronze Layer의 Delta Table에 수용할 수 없습니다.
따라서 도착하는 소스 대용량 데이터 파일의 데이터 처리를 Bronze Layer의 Delta Table로 처리하는 "파이프라인"은 "스키마 불일치" 상황으로 인해 실패할 것입니다.

더욱이, 나중에 도착하는 소스 대용량 데이터 파일의 "구조"가 다시 변경되어 기존 열 "LastName"을 제거하고 추가 열 "Company"를 수용할 수 있게 될 수도 있습니다.

<div class="content-ad"></div>

![2024-05-27-SchemaEvolutioninDeltaLake-Databricks_3.png](/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_3.png)

만약 "Bronze Layer"의 델타 테이블의 "스키마"가 "City" 열을 수용할 수 있도록 수동으로 변경되었다면, 이전에 업데이트된 "스키마"에 "Company" 열의 정보가 없기 때문에 "Bronze Layer"의 델타 테이블은 여전히 새롭게 도착한 "Company" 열을 수용할 수 없습니다.
따라서, 도착하는 소스 대규모 데이터 파일의 데이터 처리를 담당하는 "파이프라인"은 여전히 "스키마 불일치" 상황으로 인해 실패할 것입니다.

## "스키마 진화"란?

도착하는 소스 대규모 데이터 파일의 "구조"가 변경되는 경우, 추가 열을 수용하거나 이미 존재하는 열을 제거하기 위해 델타 테이블의 "스키마"를 조정하는 것을 "스키마 진화"라고 합니다.

<div class="content-ad"></div>

# 예시로 “Schema Evolution” 설명하기

단계 1: 다음의 “Spark SQL” 쿼리를 사용하여 “practice” 데이터베이스를 생성합니다 -

```js
%sql
USE hive_metastore;
CREATE DATABASE IF NOT EXISTS practice
```

데이터베이스는 Databricks 워크스페이스에 생성됩니다 -

<div class="content-ad"></div>

```markdown
![이미지](/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_4.png)

단계 2: 다음 "Spark SQL" 쿼리를 사용하여 외부 델타 테이블 "person_bronze"을 생성합니다 -

```js
%sql
-- 브론즈 테이블 "person_bronze" 생성
CREATE TABLE IF NOT EXISTS hive_metastore.practice.person_bronze
(
  Id INT,
  FirstName STRING,
  LastName STRING
)
LOCATION "/mnt/iobdatabronze/practice-zone/delta-table/person_bronze"
```

외부 델타 테이블은 Databricks 워크스페이스의 "practice" 데이터베이스 내에 생성됩니다 -
```

<div class="content-ad"></div>

```markdown
![2024-05-27-SchemaEvolutioninDeltaLake-Databricks_5](/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_5.png)

단계 3: 다음의 "Spark SQL" 쿼리를 사용하여 Delta Table "person_bronze"에 다음 두 레코드를 수동으로 삽입합니다 -

```js
%sql
INSERT INTO hive_metastore.practice.person_bronze (Id, FirstName, LastName) VALUES(1, 'Souvik', 'Roy');
INSERT INTO hive_metastore.practice.person_bronze (Id, FirstName, LastName) VALUES(2, 'Swaralipi', 'Roy');
```

"Just inserted data"가 Bronze Table "person_bronze"에 있는지 "Spark SQL" 쿼리를 사용하여 확인합니다 -
```

<div class="content-ad"></div>

```js
%sql
SELECT * FROM hive_metastore.practice.person_bronze;
```

Output -

![Schema Evolution in Delta Lake](/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_6.png)

Step 4: 고객이 새로운 소스 빅 데이터 파일을 추가하여 새로운 열 "City"를 보냅니다. 새로운 소스 빅 데이터 파일의 내용은 다음과 같습니다 -

<div class="content-ad"></div>

```markdown
![이미지](/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_7.png)

이제, 새로운 소스 빅 데이터 파일을 DataFrame으로 읽어 들입니다.

```python
from pyspark.sql.functions import *
from pyspark.sql.types import *

# 도착한 새로운 소스 빅 데이터 파일의 구조 정의
person_schema = StructType([
    StructField("Id", IntegerType(), False),
    StructField("FirstName", StringType(), False),
    StructField("LastName", StringType(), False),
    StructField("City", StringType(), False)
])

# 도착한 새로운 소스 빅 데이터 파일의 내용 읽기
df = spark.read.format("csv").option("header", "true").schema(person_schema).load("/mnt/iobdatalanding/practice-zone/input-files/Person_1.csv")
display(df)
```

출력 -
```

<div class="content-ad"></div>

`<img src="/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_8.png" />`

DataFrame의 내용을 Bronze Layer의 Delta Table "person_bronze"에 추가하려고 시도해보세요. 다음과 같은 오류 "Delta 테이블에 쓸 때 스키마 불일치가 감지되었습니다"가 발생합니다 -

```js
df.write.format("delta").mode("append").saveAsTable("hive_metastore.practice.person_bronze")
```

Output -

<div class="content-ad"></div>

위의 이미지에서 알 수 있듯이, 새로 도착한 "City" 열의 정보는 브론즈 레이어의 델타 테이블 "person_bronze"의 "Schema"에 포함되어 있지 않습니다.
이것이 바로 브론즈 레이어의 Delta 테이블 "person_bronze"에 DataFrame의 내용을 추가하는 "append" 작업이 실패한 이유입니다.

단계 5: 시간이 지남에 따라 소스 파일의 "구조"가 변경된 시나리오를 처리하기 위해, 변경된 열을 "대상 Delta 테이블"에 추가하기 위해 Databricks에서 제공하는 "mergeSchema" 기능을 사용하여 DataFrame의 내용을 Delta 테이블에 쓰는 코드에서 "mergeSchema" 기능을 "true"로 설정합니다. 이를 위해 다음 구문을 사용합니다 -

```js
df.write.format("delta").option("mergeSchema", "true").mode("append").saveAsTable("hive_metastore.practice.person_bronze")
```

<div class="content-ad"></div>

위의 PySpark 코드가 오류를 발생시키지 않고 성공적으로 실행되었습니다.

"Spark SQL" 쿼리를 사용하여 브론즈 테이블 "person_bronze"에 방금 삽입된 데이터가 있는지 확인해보세요.

```js
%sql
SELECT * FROM hive_metastore.practice.person_bronze;
```

결과 -

<div class="content-ad"></div>

위 이미지에서 볼 수 있듯이 새로운 소스 빅 데이터 파일에서 가져온 새 콘텐츠가 Bronze Layer의 Delta Table에 성공적으로 삽입된 것을 확인할 수 있습니다. 새로운 추가 열 "City"가 추가되었습니다.
새로 추가된 열 "City"에 대해 기존의 Bronze Layer의 Delta Table의 기존 행에는 값이 제공되지 않았기 때문에 기존 행의 "City" 열 값에 "NULL"이 설정되었습니다.

단계 6: 클라이언트가 다시 새로운 소스 빅 데이터 파일을 보내왔는데, 이번에는 새로운 추가 열 "Company"와 이전에 있던 열 "LastName"이 삭제되었습니다. 새로운 소스 빅 데이터 파일의 내용은 다음과 같습니다 -

![이미지](/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_11.png)

<div class="content-ad"></div>

이제 새로운 소스 빅데이터 파일을 DataFrame으로 읽어봅시다.

```python
from pyspark.sql.functions import *
from pyspark.sql.types import *

# 도착한 새로운 소스 빅데이터 파일의 구조 정의
person_schema = StructType([
    StructField("Id", IntegerType(), False),
    StructField("FirstName", StringType(), False),
    StructField("City", StringType(), False),
    StructField("Company", StringType(), False)
])
# 도착한 새로운 소스 빅데이터 파일의 내용 읽기
df = spark.read.format("csv").option("header", "true").schema(person_schema).load("/mnt/iobdatalanding/practice-zone/input-files/Person_2.csv")
display(df)
```

결과 -

<img src="/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_12.png" />

<div class="content-ad"></div>

이제 Databricks에서 제공하는 "mergeSchema" 기능을 사용하여 DataFrame의 내용을 Bronze 레이어의 Delta Table "person_bronze"에 추가해 보세요.

```js
df.write.format("delta").option("mergeSchema", "true").mode("append").saveAsTable("hive_metastore.practice.person_bronze")
```

위의 작업은 오류가 발생하지 않고 성공적으로 실행될 것입니다.

삽입된 데이터가 Bronze Table "person_bronze"에 올바르게 입력되었는지 확인하려면 "Spark SQL" 쿼리를 사용하세요 -

<div class="content-ad"></div>

```js
%sql
SELECT * FROM hive_metastore.practice.person_bronze;
```

Output -

![Schema Evolution in Delta Lake](/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_13.png)

위 이미지에서 새로운 소스의 빅 데이터 파일에서 가져온 새로운 내용이 브론즈 레이어의 델타 테이블에 성공적으로 삽입되었음을 확인할 수 있습니다. 새로 추가된 열 "Company"가 추가되었습니다.
새로 추가된 열 "Company"를 위해 기존 델타 테이블의 이미 존재하는 행들에는 값이 제공되지 않았기 때문에, 기존 행들의 "City" 열 값으로 "NULL"이 설정되었습니다.
또한, "LastName" 열은 이미 브론즈 레이어의 델타 테이블의 "스키마"에 존재하지만, 새로운 소스의 빅 데이터 파일의 내용에는 존재하지 않기 때문에, 새로운 소스에서 오는 행들에 대해 "LastName" 열 값으로 "NULL"이 설정되었습니다.```

<div class="content-ad"></div>

# Databricks의 "mergeSchema" 기능의 제한

## 소스 파일의 열 이름과 대상 테이블의 열 이름이 같지만 두 열의 데이터 유형이 다른 경우:

고객이 새로운 소스 빅 데이터 파일을 전송했습니다. 열의 수 및 열의 이름은 Bronze 레이어의 Delta 테이블과 동일하지만 새 소스 빅 데이터 파일의 "Company" 열의 데이터 유형은 "Integer"입니다.
새 소스 빅 데이터 파일의 내용은 다음과 같습니다 -

![이미지](/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_14.png)

<div class="content-ad"></div>

이제 새로운 소스 빅데이터 파일을 DataFrame으로 읽어보세요.

```python
from pyspark.sql.functions import *
from pyspark.sql.types import *

# 도착한 새로운 소스 빅데이터 파일의 구조 정의
person_schema = StructType([
    StructField("Id", IntegerType(), False),
    StructField("FirstName", StringType(), False),
    StructField("LastName", StringType(), False),
    StructField("City", StringType(), False),
    StructField("Company", IntegerType(), False),
])

# 도착한 새로운 소스 빅데이터 파일의 내용 읽기
df = spark.read.format("csv").option("header", "true").schema(person_schema).load("/mnt/iobdatalanding/practice-zone/input-files/Person_3.csv")
display(df)
```

출력 -

<img src="/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_15.png" />

<div class="content-ad"></div>

Databricks에서 제공하는 "mergeSchema" 기능을 사용하여 Bronze 레이어의 Delta Table "person_bronze"에 DataFrame의 내용을 추가해 보세요.

```js
df.write.format("delta").option("mergeSchema", "true").mode("append").saveAsTable("hive_metastore.practice.person_bronze")
```

이 작업은 다음과 같은 오류로 실패할 것입니다. "Company"와 "Company" 필드를 병합하지 못했습니다. StringType 및 IntegerType과(와) 호환되지 않는 데이터 유형을 병합하지 못했습니다.

![이미지](/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_16.png)

<div class="content-ad"></div>

# 데이터 소스 파일의 열 이름과 대상 테이블의 열 이름이 동일하지만 대/소문자만 다른 경우에 대해 어떻게 처리되는지 알아봅시다.

고객이 새로운 데이터 소스 대용량 파일을 보내는 상황을 가정해봅시다. 여기서 열의 수, 열의 이름, 그리고 열의 데이터 유형은 브론즈 레이어의 델타 테이블과 동일하지만, 새로운 데이터 소스 대용량 파일에서 "Company" 열의 이름이 "company"로 표시됩니다.
새로운 데이터 소스 대용량 파일의 내용은 다음과 같습니다 -

![이미지](/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_17.png)

이제 새로운 데이터 소스 대용량 파일을 DataFrame으로 읽어봅시다.

<div class="content-ad"></div>

```markdown
from pyspark.sql.functions import *
from pyspark.sql.types import *

# 도착한 새로운 소스 빅 데이터 파일의 구조 정의
person_schema = StructType([
    StructField("Id", IntegerType(), False),
    StructField("FirstName", StringType(), False),
    StructField("LastName", StringType(), False),
    StructField("City", StringType(), False),
    StructField("company", StringType(), False),
])
# 도착한 새로운 소스 빅 데이터 파일의 내용 읽기
df = spark.read.format("csv").option("header", "true").schema(person_schema).load("/mnt/iobdatalanding/practice-zone/input-files/Person_4.csv")
display(df)
```

출력 -

<img src="/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_18.png" />

이제 Databricks에서 제공하는 "mergeSchema" 기능을 사용하여 DataFrame의 내용을 Bronze Layer의 Delta Table "person_bronze"에 추가해 보세요.
```

<div class="content-ad"></div>

```js
df.write.format("delta").option("mergeSchema", "true").mode("append").saveAsTable("hive_metastore.practice.person_bronze")
```

모든 작업이 오류 없이 성공적으로 실행되었습니다.

"Spark SQL" 쿼리를 사용하여 "person_bronze" Bronz 테이블에 방금 삽입된 데이터가 있는지 확인해보세요 -

![Image](/assets/img/2024-05-27-SchemaEvolutioninDeltaLake-Databricks_19.png)

<div class="content-ad"></div>

위의 이미지에서 볼 수 있듯이, 새로운 소스 빅데이터 파일에서 콘텐츠의 열 이름 "회사(Company)"이 "company"로 표시되었음에도 불구하고 데이터가 브론즈 레이어의 델타 테이블에 성공적으로 삽입되었습니다. 이것은 "아파치 스파크(Apache Spark)"가 기본적으로 대소문자를 구분하지 않기 때문에 가능했습니다. 따라서 "회사(Company)"와 "company" 열 모두 동일하게 처리되었습니다.