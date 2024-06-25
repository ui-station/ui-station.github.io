---
title: "단위 테스트 및 코드 모듈화를 위한 Databricks"
description: ""
coverImage: "/assets/img/2024-06-19-UnitTestingandCodeModularizationinDatabricks_0.png"
date: 2024-06-19 12:22
ogImage:
  url: /assets/img/2024-06-19-UnitTestingandCodeModularizationinDatabricks_0.png
tag: Tech
originalTitle: "Unit Testing and Code Modularization in Databricks"
link: "https://medium.com/@mariusz_kujawski/unit-testing-and-code-modularization-in-databricks-33f40c9f6da9"
---

노트북은 Databricks에서 데이터를 다루는 인기 있는 방법입니다. 노트북 사용자는 데이터를 빠르게 읽고 변환하며 상호적으로 탐색할 수 있습니다. 게다가, 노트북을 공유하고 협업하는 것은 간단합니다. 그러나 프로젝트가 확장될수록 코드 중복을 방지하고 재사용성을 용이하게 하는 모듈화 기능이 필요해집니다.

이를 달성하는 한 가지 방법은 공유 함수를 포함하는 노트북을 생성하고 각 노트북의 시작 부분에서 실행하는 것입니다. 또는 모듈을 만들어 일반적인 Python 개발과 유사한 Python import 명령어를 사용할 수 있습니다. 긴 코드 블록을 함수로 나누면 코드의 재사용을 촉진할 뿐만 아니라 테스트도 용이해집니다.

![이미지](/assets/img/2024-06-19-UnitTestingandCodeModularizationinDatabricks_0.png)

## 모듈화

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

파이썬에서 모듈화란 프로그램을 작은 관리 가능한 모듈로 나누는 것을 말합니다. 파이썬에서 코드를 모듈화하는 것에는 여러 가지 이점이 있습니다:

- 재사용성: 모듈은 다른 프로젝트에서 다시 사용할 수 있어 재작성이 필요하지 않습니다.
- 유지보수성: 작은 중점적인 모듈로 인해 업데이트와 디버깅이 쉬워집니다.
- 확장성: 프로젝트가 성장할 때 효율적인 확장이 가능합니다.
- 협업: 다른 개발자들이 동시에 작업하기를 용이하게 합니다.
- 테스트: 단위 테스트가 간소화되어 더 신뢰할 수 있는 코드를 작성할 수 있습니다.
- 가독성: 특정 작업에 집중함으로써 코드 이해가 향상됩니다.

Databricks에서 모듈을 사용하기 위해서는 클래스 또는 함수를 포함한 파일들로 구성된 폴더와 **init**.py 파일을 생성해야 합니다. 이는 Databricks에 모듈임을 알려줍니다. 아래는 공통 모듈과 함께 공유 함수, 변환 로직을 포함한 변환 모듈, 그리고 테스트 데이터가 포함된 내 솔루션의 구조입니다.

코드 구조:

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
워크스페이스
├── test_data
│    └── testdata.csv
├── common
│    └── __init__.py
│    └── utilis.py
├── transform
│    └── __init__.py
│    └── operations.py
├── test_utils.py
├── test_tran.py
├── test
```

testdata.csv:

```js
entity,iso_code,date,indicator,value
United States,USA,2022-04-17,Daily ICU occupancy,
United States,USA,2022-04-17,Daily ICU occupancy per million,4.1
United States,USA,2022-04-17,Daily hospital occupancy,10000
United States,USA,2022-04-17,Daily hospital occupancy per million,30.3
United States,USA,2022-04-17,Weekly new hospital admissions,11000
United States,USA,2022-04-17,Weekly new hospital admissions per million,32.8
```

ulits.py:

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
from pyspark.sql.functions import udf
from pyspark.sql.types import StringType

def mask_func(col_val):
    if col_val is not None:
        if len(col_val)>=16:
            charList=list(col_val)
            charList[4:12]='x'*8
            return "".join(charList)
        else:
            return col_val
    else:
        return col_val
```

operations.py:

```js
from pyspark.sql.window import Window
from pyspark.sql.functions import row_number, col

def deduplicate(df, uniq_col, orderby_col):
    df = df.withColumn("rn", row_number()
        .over(Window.partitionBy(uniq_col)
        .orderBy(col(orderby_col).desc())))

    df = df.filter(col("rn") == 1).drop("rn")
    return df

def clean_clients(df):
    df = df.where(col("name") != "").withColumn("timestamp", col("timestamp").cast("date"))

    return df
```

모듈에서 이러한 함수를 사용하려는 사람은 아래 예시와 같이 import 명령을 사용하여 노트북에 쉽게 추가할 수 있습니다.

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

![UnitTestingandCodeModularizationinDatabricks1](/assets/img/2024-06-19-UnitTestingandCodeModularizationinDatabricks_1.png)

Similarly, it’s possible to import transformation functions from the module and remove duplicated records from the DataFrame.

![UnitTestingandCodeModularizationinDatabricks2](/assets/img/2024-06-19-UnitTestingandCodeModularizationinDatabricks_2.png)

# Unit Testing in Databricks

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

소프트웨어 개발에서 단위 테스트는 코드의 정확성, 안정성, 유지 보수 가능성을 보장하는 중요한 요소입니다. 데이터 처리를 위해 노트북이 일반적으로 사용되는 Databricks에서는 단위 테스트가 더욱 중요해집니다.

Databricks에서 단위 테스트를 시작하려면 코드를 테스트할 수 있는 함수로 분해해야 합니다. 이 프로세스는 코드의 모듈성을 향상시키는 것뿐만 아니라 포괄적인 테스트 스위트를 작성하는 데 도움이 됩니다. Python에서 유닛 테스트를 수행하는 두 가지 인기있는 프레임워크인 Unittest와 pytest가 있습니다.

Unittest 예시:

```python
import unittest

class ExampleTestSuite(unittest.TestCase):

    def test_import(self):
        self.assertTrue(True)

    def test_addition(self):
        self.assertEqual(1 + 2, 3)

    def test_subtraction(self):
        self.assertNotEqual(1 - 2, 0)
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

테이블 태그를 Markdown 형식으로 변경하세요.

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

## 왜 단위 테스팅을 해야 할까요?

처음 테스트 중에 코드가 정상적으로 작동하는 것처럼 보이더라도, 단위 테스트는 여러 가지 이유로 중요한 역할을 합니다:

- 정확성 확인: 단위 테스트는 코드의 개별 단위 기능을 확인하여 다양한 조건에서 예상대로 작동하는지 확인합니다.
- 초기 버그 탐지: 개발 과정 초기에 버그를 식별함으로써, 개발자는 이를 신속히 해결하여 시스템의 다른 부분으로 전파되는 가능성을 줄일 수 있습니다.
- 리팩토링 및 유지보수: 단위 테스트는 코드 리팩토링 및 유지보수 과정에서 안전망 역할을 하며, 개발자가 확신을 갖고 변경을 가할 수 있으면서도 일관된 동작을 보장합니다.
- 회귀 테스트: 단위 테스트는 회귀 테스트로 작용하여 새로운 변경사항이나 기능이 기존의 기능을 망가뜨리지 않도록 하여 시스템의 안정성을 유지합니다.

마스킹 기능에 대한 단위 테스트의 간단한 예제를 살펴보겠습니다. 이 단위 테스트는 입력 숫자가 올바르게 마스킹되거나 None을 반환하는지를 확인하여, 함수가 변경되더라도 예상되는 동작이 유지되도록 합니다.

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

test_utils.py

```python
from common.utils import mask_func

def test_mask_func():
    assert "1234xxxxxxxx4568" == mask_func("1234567891234568")
    assert mask_func(None) is None
```

ETL(Extract, Transform, Load)과 같은 복잡한 프로세스의 경우, 데이터 변환 과정의 다양한 측면을 확인하는 데 개선된 테스트를 개발할 수 있습니다. 이러한 테스트에는 스키마 확인, 데이터프레임 비교, 행 수 유효성 검사 또는 특정 값의 존재 여부 확인이 포함될 수 있습니다.

test_tran.py:

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
import pytest
from transform.operations import *
from pyspark.testing.utils import assertDataFrameEqual
from pyspark.sql import SparkSession
from pyspark.testing import assertDataFrameEqual, assertSchemaEqual
from pyspark.sql.types import *
import pandas as pd

@pytest.fixture()
def spark():
    return SparkSession.builder.appName("integrity-tests").getOrCreate()

@pytest.fixture()
def raw_input_df(spark):
    df = pd.read_csv('test_data/testdata.csv')
    return spark.createDataFrame(df)

@pytest.fixture()
def test_df(spark):

    schema = "name STRING, age INTEGER, city STRING, timestamp STRING"
    input_data = [
        ("John", 25, "New York", "20210101"),
        ("Jane", 30, "Los Angeles", "20210101"),
        ("Jane", 30, "Chicago", "20220101"),
        ("Doe", 40, "New York", "20210101"),
        ("", 39, "New York", "20210101"),
    ]
    df = spark.createDataFrame(input_data, schema)

    return df

def test_deduplicate(test_df, spark):
    schema = "name STRING, age INTEGER, city STRING, timestamp STRING"
    input_data = [
        ("John", 25, "New York", "20210101"),
        ("Jane", 30, "Chicago", "20220101"),
        ("Doe", 40, "New York", "20210101"),
        ("", 39, "New York", "20210101"),
    ]
    df = spark.createDataFrame(input_data, schema)

    df1 = deduplicate(test_df, "Name", "timestamp")
    assertDataFrameEqual(df1, df)

def test_schema_deduplicated(test_df, spark):
    schema = "name STRING, age INTEGER, city STRING, timestamp STRING"
    input_data = [
        ("John", 25, "New York", "20210101"),
        ("Jane", 30, "Chicago", "20220101"),
        ("Doe", 40, "New York", "20210101"),
        ("", 39, "New York", "20210101"),
    ]
    df_expected = spark.createDataFrame(input_data, schema)

    test_df = deduplicate(test_df, "Name", "timestamp")
    assertSchemaEqual(test_df.schema, df_expected.schema)

def test_clean_clients(test_df, spark):
    df = clean_clients(test_df)
    assert df.where("name == '' ").count() == 0

def test_readfromfile(raw_input_df):
    assert raw_input_df.count() > 0
```

## Initializing Spark Session for Tests:

테스트 파일은 주피터 노트북이 아니기 때문에, Spark 세션을 초기화하는 것이 필요합니다. 이를 위해서 `spark` 함수와 `fixture` 데코레이터를 사용해서 Spark 세션을 만들 수 있습니다. `fixture` 데코레이터는 자동으로 실행되며 각 테스트 함수에 해당하는 테스트 객체를 제공해주어 테스트 데이터의 생성 및 공유를 간편하게 할 수 있습니다.

```js
@pytest.fixture()
def spark():
    return SparkSession.builder.appName("integrity-tests").getOrCreate()
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

선언했던 fixture 데코레이터의 동작 방식을 주목하는 것이 중요합니다. 이 기법을 사용하면 테스트 데이터를 원활하게 실행하고 전달할 수 있습니다. 이 기법을 이용하면 Spark 세션을 생성하여 테스트 데이터를 로드하고 이를 테스트 함수 사이에서 공유할 수 있습니다. 테스트 데이터는 목록을 기반으로 생성하거나 테스트 파일에서 로드할 수 있습니다.

```js
@pytest.fixture()
def raw_input_df(spark):
 df = pd.read_csv('test_data/testdata.csv')

 return spark.createDataFrame(df)
```

테스트용 데이터 원본으로 샘플 파일을 사용하는 것도 가능합니다. 그러나 워크스페이스에서 로드해야 하는 경우에는 Spark가 워크스페이스로부터 파일을 직접 로드하는 것을 지원하지 않기 때문에 Pandas를 사용해야 합니다.

## 모듈의 지연 변경 사항 처리하기:

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

모듈을 다룰 때 변경 내용을 구현하는 데 지연이 발생하는 것은 일반적입니다. 이 동작을 해결하기 위해 매직 함수를 사용할 수 있습니다:

```js
%load_ext autoreload

%autoreload 2

%aimport test_tran
```

# Databricks에서 단위 테스트 실행

Databricks에서 단위 테스트를 실행하려면 pytest 모듈을 호출하는 노트북을 생성해야 합니다. 아래는 지정된 저장소에서 테스트 실행을 트리거하는 코드 스니펫입니다.

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

테스트 노트북:

```js
%pip install pytest
```

```js
import pytest
import sys
import os, sys

repo_name = "<저장소 위치>"

notebook_path = dbutils.notebook.entry_point.getDbutils().notebook().getContext().notebookPath().get()
repo_root = os.path.dirname(os.path.dirname(notebook_path))

os.chdir(f"/Workspace/{repo_root}/{repo_name}")
print(os.getcwd())
# 읽기 전용 파일 시스템에 pyc 파일을 쓰지 않도록 설정합니다.
sys.dont_write_bytecode = True

# pytest 실행.
retcode = pytest.main([".", "-v", "-p", "no:cacheprovider"])

# 테스트 실패가 있는 경우 셀 실행 실패 처리합니다.
assert retcode == 0, "pytest 호출에 실패했습니다. 자세한 내용은 로그를 확인하세요."
```

pytest를 실행하면 현재 디렉토리와 서브디렉토리에서 이름이 test\__.py 또는 _\_test.py 패턴을 따르는 모든 파일을 자동으로 실행합니다.

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

스크립트를 실행하면 다음과 비슷한 보고서가 표시됩니다:

![보고서](/assets/img/2024-06-19-UnitTestingandCodeModularizationinDatabricks_4.png)

# 요약

요약하면 모듈화와 유닛 테스팅은 소프트웨어 개발에서 널리 사용되는 관행이며, 이러한 적용은 데이터 엔지니어링 활동에 매끄럽게 확장됩니다. 코드의 모듈화 및 유닛 테스트를 구현하여 데이터 처리 솔루션이 더욱 신뢰성 있고 유연해집니다. 모듈화는 코드 구성 요소의 더 나은 조직화와 재사용을 가능하게 하며, 유닛 테스트는 각 구성 요소가 다양한 조건에서 예상대로 동작하는지 확인합니다. 이러한 기술들이 함께 사용되면 데이터 엔지니어링 솔루션의 전체적인 견고성과 유지보수성에 기여하며, 마지막으로 데이터 처리 파이프라인 및 워크플로의 품질을 향상시킵니다.

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

만약 이 기사를 유익하게 여기셨다면, 'clap' 버튼을 클릭하거나 LinkedIn에서 좋아요를 표시해 주시면 감사하겠습니다. 여러분의 지원을 감사히 여깁니다. 궁금한 점이나 조언이 있으시다면 언제든 LinkedIn에서 연락해 주세요.
