---
title: "현재 날짜나 시간을 기준으로 데이터브릭에서 여러 파일을 동적으로 로드하는 방법"
description: ""
coverImage: "/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_0.png"
date: 2024-05-27 17:13
ogImage:
  url: /assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_0.png
tag: Tech
originalTitle: "How To Dynamically Load Multiple Files in Databricks Based on Current Date or Hour"
link: "https://medium.com/@oindrila-chakraborty88/how-to-dynamically-load-multiple-files-in-databricks-based-on-current-date-or-hour-21244bfe34e4"
---

Batch 시스템에서는 여러 번 클라이언트가 이미 추출된 파일을 동일한 경로에 유지하고, 도착한 추출 파일이 Bronze 레이어에 로드되기를 원하는 경우가 많습니다.

따라서 이 경우, 개발자는 디렉토리에 있는 파일 목록 중에서 현재 날짜나 현재 시간에 도착한 파일만 고려하여 해당 파일의 내용을 Bronze 레이어에만 로드할 수 있도록 해야 합니다.

이미지를 보면 오늘 날짜는 2024년 5월 25일임을 알 수 있습니다.

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

2024년 5월 22일에 첫 번째 파일이 도착했습니다.
2024년 5월 23일에 두 번째 파일이 도착했습니다.
2024년 5월 24일에 세 번째 파일이 도착했습니다.
나머지 네 개의 파일은 오늘인 2024년 5월 25일에 도착했습니다.

오늘 도착한 첫 번째 파일 내용은 다음과 같습니다 -

![이미지](/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_1.png)

오늘 도착한 두 번째 파일 내용은 다음과 같습니다 -

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

![Third File](/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_3.png)

The content of the Fourth File arrived today is as follows -

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

<img src="/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_4.png" />

# 현재 날짜 기반으로 데이터브릭에서 여러 파일 동적으로 로드하는 방법

그래서, 작업은 최근 네 개의 파일을 처리하고 그 네 개 파일의 내용을 브론즈 레이어의 테이블에 추가 모드로 로드하는 것입니다.

단계 1: 브론즈 테이블 생성하기 -

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

먼저 다음 "Spark SQL" 쿼리를 사용하여 "practice"라는 데이터베이스를 생성하십시오 -

```js
%sql
USE hive_metastore;
CREATE DATABASE IF NOT EXISTS practice
```

데이터베이스는 Databricks 워크스페이스에 생성됩니다 -

![이미지](/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_5.png)

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

그럼 다음 "Spark SQL" 쿼리를 사용하여 외부 델타 테이블 "person_bronze"을 생성하세요 -

```js
%sql
CREATE TABLE IF NOT EXISTS hive_metastore.practice.person_bronze
(
  FirstName STRING NOT NULL,
  LastName STRING NOT NULL,
  City STRING NOT NULL,
  Company STRING NOT NULL
)
LOCATION "dbfs:/mnt/iobdatabronze/practice-zone/delta-table/person_bronze"
```

외부 델타 테이블은 Databricks 워크스페이스의 "practice" 데이터베이스 내부에 생성됩니다 -

![image](/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_6.png)

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

생성된 External Delta Table "person_bronze"의 폴더 경로는 제공된 위치에 ADLS에 생성되었습니다-

![image](/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_7.png)

단계 2: 현재 날짜인 2024년 5월 25일을 기준으로 Databricks에서 가장 최근 네 개의 파일을 로드하는 Python 코드를 작성해 보겠습니다.

단계 2.1: Python의 "datetime" 모듈을 사용하여 "현재 날짜"의 값을 가져와, 도착 파일의 이름에 사용된 형식과 일치하도록 "현재 날짜"의 값을 포맷팅해 주세요.

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
import os
from datetime import datetime
from functools import reduce

# Python의 "datetime" 모듈을 사용하여 현재 날짜 가져오기
current_date = datetime.now()
print(current_date)

# 현재 날짜의 값을 파일 이름 형식에 맞게 포맷팅하여 출력
file_name_date_format = current_date.strftime("%Y%m%d")
print(file_name_date_format)
```

출력 -

<img src="/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_8.png" />

단계 2.2: 파일이 보관된 ADLS 디렉토리의 "마운트된 경로"를 지정합니다.
그런 다음, 해당 지정된 디렉토리에서 모든 파일을 나열합니다.
마지막으로, 그 지정된 디렉토리의 파일 이름에 "현재 날짜"가 포함된 파일만 걸러내어 Python List에 저장합니다.

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
# 파일이 위치한 ADLS 디렉토리의 마운트 경로 지정하기
directory_path = "/mnt/iobdatalanding/practice-zone/input-files/"

# 지정된 디렉토리에 있는 모든 파일 나열하기
all_files = os.listdir("/dbfs" + directory_path)
print(all_files)

# 현재 날짜가 파일 이름에 포함된 파일만 필터링하여 Python 리스트에 저장하기
matching_files = [matching_file for matching_file in all_files if file_name_date_format in matching_file]
print(matching_files)
```

출력 -

<img src="/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_9.png" />

단계 2.3: 먼저 "빈 Python 리스트"를 생성하세요.
그런 다음 각 일치하는 파일의 내용을 각각 별도의 DataFrame에 로드하세요.
마지막으로 각 DataFrame을 이미 생성된 "Python 리스트"에 "객체"로 추가하세요.

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
# 빈 리스트 생성
list_of_dfs = []

# 각 일치하는 파일을 각각 별도의 데이터프레임으로 로드
for file_name in matching_files:
    # 실제 파일 경로 생성
    file_path = os.path.join(directory_path, file_name)
    # 각 파일마다 데이터프레임 생성
    df = spark.read.format("csv").option("header", "true").load(file_path)
    # 각 데이터프레임을 빈 리스트에 객체로서 각각 저장
    list_of_dfs.append(df)

print(list_of_dfs)
```

출력 -

<img src="/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_10.png" />

2.4단계: 각 데이터프레임의 모든 값들을 단일 데이터프레임으로 연결하고, 각 데이터프레임의 값들이 이제 "파이썬 리스트"의 각 객체로 되는 단일 데이터프레임을 생성하기 위해 "reduce()" 함수와 "union()" 메서드를 사용합니다.

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
# 각 데이터프레임의 모든 값을 연결하여 하나의 데이터프레임으로 만들기
# 이때 각 데이터프레임의 값은 이제 Python List의 각 객체이며 "reduce()" 함수를 사용하여 "union()" 메서드와 함께 결합합니다.
final_df = reduce(lambda df1, df2: df1.union(df2), list_of_dfs)
display(final_df)
```

결과 -

<img src="/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_11.png" />

따라서 위 이미지에서 오늘 도착한 네 개의 파일에서 모든 레코드의 조합이 포함된 "final_df" 데이터프레임이 있음을 확인할 수 있습니다.

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

당신이 개발자십에 관심이 있나봐요! 친철한 톤으로 번역해 드리겠습니다.

스텝 2.5: 'final_df' DataFrame의 내용을 Bronze Table "person_bronze"에 삽입하세요.

```js
# 'final_df' DataFrame의 내용을 Bronze Table "person_bronze"에 삽입
final_df.write.format("delta").mode("append").saveAsTable("hive_metastore.practice.person_bronze")
```

다음 "스파크 SQL" 쿼리를 사용하여 'person_bronze' Bronze Table에 방금 삽입된 데이터가 있는지 확인하세요 -

```js
%sql
-- 'person_bronze' Bronze Table에 데이터가 있는지 확인
SELECT * FROM hive_metastore.practice.person_bronze;
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

출력 -

<img src="/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_12.png" />

# 현재 시간을 기반으로 한 Databricks에서 여러 파일 로드하기

<img src="/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_13.png" />

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

지금은 2024년 5월 25일이고, 현재 시간은 오후 8시입니다. 위 그림에서 볼 수 있듯이, 마지막 두 파일이 "현재 시간"인 즉, 8시에 도착했습니다.

그러므로, 마지막 두 파일을 처리하고 그 두 파일의 내용을 브론즈 레이어의 테이블에 추가 모드로 로드하는 작업입니다.

# 현재 날짜 또는 시간에 따라 Databricks에서 여러 파일을 동적으로 로드하는 방법

배치 시스템에서는 고객이 이미 추출된 파일을 도착한 추출된 파일과 동일한 경로에 유지하고 브론즈 레이어에 로드하길 원하는 경우가 많습니다.

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

따라서 현재 일 또는 현재 시간에 도착한 파일만 고려하여 디렉터리에있는 파일 목록에서 해당 파일의 내용을 처리하고 브론즈 계층으로 로드하는 개발자의 책임이 있습니다.

![이미지](/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_14.png)

오늘은 2024년 5월 25일입니다. 위 이미지에서 볼 수 있듯이 -

첫 번째 파일은 2024년 5월 22일에 도착했습니다.
두 번째 파일은 2024년 5월 23일에 도착했습니다.
세 번째 파일은 2024년 5월 24일에 도착했습니다.
나머지 네 번째 파일은 2024년 5월 25일, 즉 오늘 도착했습니다.

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

오늘 도착한 첫 번째 파일 내용은 다음과 같습니다 -

![First File](/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_15.png)

오늘 도착한 두 번째 파일 내용은 다음과 같습니다 -

![Second File](/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_16.png)

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

현재 시간에 도착한 첫 번째 파일의 내용은 다음과 같습니다 -

![](/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_17.png)

현재 시간에 도착한 두 번째 파일의 내용은 다음과 같습니다 -

![](/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_18.png)

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

Step 1: Create a Bronze Table -

이미 첫 번째 부분에서 생성되었습니다.

Step 2: 오늘 현재 시간 기준으로 Databricks에서 마지막 두 파일을로드하는 Python 코드 작성 시작, 즉, 2024년 5월 25일 오후 8시.

Step 2.1: Python의 "datetime" 모듈을 사용하여 "현재 날짜"의 "현재 시간" 값을 가져와서 "현재 날짜"의 "현재 시간" 값을 적시되어 파일 도착 이름 및 "날짜" 및 "시간" 부분과 일치하도록 형식화하세요.

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

```python
import os
from datetime import datetime
from functools import reduce

# Python의 "datetime" 모듈을 사용하여 현재 날짜의 현재 시간을 가져옵니다.
current_date_and_time = datetime.now()
print(current_date_and_time)

# 현재 시간의 값을 파일 이름의 날짜 및 시간 형식과 일치하도록 형식화합니다.
file_name_date_and_hour_format = current_date_and_time.strftime("%Y%m%d%H")
print(file_name_date_and_hour_format)
```

출력 -

<img src="/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_19.png" />

단계 2.2: 파일이 보관된 ADLS 디렉토리의 "Mounted Path"를 지정합니다.
그런 다음, 해당 지정된 디렉토리에서 모든 파일을 나열합니다.
마지막으로 해당 지정된 디렉토리의 파일 이름 중 "현재 날짜"의 "현재 시간"이 있는 파일만 필터링하여 Python List에 저장합니다.

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
# 파일이 위치한 ADLS 디렉토리의 마운트 경로를 지정합니다
directory_path = "/mnt/iobdatalanding/practice-zone/input-files/"

# 지정된 디렉토리에 있는 모든 파일을 나열합니다
all_files = os.listdir("/dbfs" + directory_path)
print(all_files)

# 현재 날짜의 현재 시간을 파일 이름에 포함하는 파일만 필터링합니다
matching_files = [matching_file for matching_file in all_files if file_name_date_and_hour_format in matching_file]
print(matching_files)
```

Output -

![다이나믹 파일로드 방법](/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_20.png)

단계 2.3: 먼저 "빈 Python List"를 생성합니다.
그런 다음 각 일치하는 파일의 내용을 각각 별도의 DataFrame으로 로드합니다.
마지막으로, 각 해당 DataFrame을 이미 생성된 "Python List"에 "객체"로서 추가합니다.

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
# 빈 목록 생성
list_of_dfs = []

# 각 일치하는 파일을 각각 별도의 데이터프레임으로 불러오기
for file_name in matching_files:
    # 실제 파일 경로 생성
    file_path = os.path.join(directory_path, file_name)
    # 각 파일에 대한 데이터프레임 생성
    df = spark.read.format("csv").option("header", "true").load(file_path)
    # 각 데이터프레임을 빈 목록에 객체로 각각 저장
    list_of_dfs.append(df)

print(list_of_dfs)
```

출력 -

<img src="/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_21.png" />

단계 2.4: 각 데이터프레임의 모든 값들을 단일 데이터프레임으로 연결합니다. 각 데이터프레임의 값은 이제 "Python List"의 각 객체이며, "reduce()" 함수와 "union()" 메소드를 사용합니다.

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

# 각 DataFrame의 모든 값들을 Python 리스트의 각 객체로 사용하여 하나의 DataFrame으로 결합하십시오. "reduce()" 함수를 사용하고 "union()" 메서드를 함께 사용하십시오.

final_df = reduce(lambda df1, df2: df1.union(df2), list_of_dfs)
display(final_df)

출력 -

<img src="/assets/img/2024-05-27-HowToDynamicallyLoadMultipleFilesinDatabricksBasedonCurrentDateorHour_22.png" />

따라서 위 이미지에서 현재 시간에 도착한 두 파일의 레코드를 모두 포함하는 "final_df" DataFrame을 확인할 수 있습니다.

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

스텝 2.5: "final_df" 데이터프레임의 내용을 첫 번째 부분에 표시된 PySpark 코드를 사용하여 Bronze 테이블 "person_bronze"에 삽입합니다.

마지막으로, 첫 번째 부분에 표시된 "Spark SQL" 쿼리를 사용하여 방금 삽입한 데이터가 Bronze 테이블 "person_bronze"에 있는지 확인하세요.
