---
title: "파이썬 제너레이터 데이터베이스에서 효율적으로 데이터를 가져오는 방법"
description: ""
coverImage: "/assets/img/2024-05-23-PythonGeneratorsHowToEfficientlyFetchDataFromDatabases_0.png"
date: 2024-05-23 14:16
ogImage: 
  url: /assets/img/2024-05-23-PythonGeneratorsHowToEfficientlyFetchDataFromDatabases_0.png
tag: Tech
originalTitle: "Python Generators: How To Efficiently Fetch Data From Databases"
link: "https://medium.com/gitconnected/python-generators-how-to-efficiently-fetch-data-from-databases-25f1947f56c0"
---


![image](/assets/img/2024-05-23-PythonGeneratorsHowToEfficientlyFetchDataFromDatabases_0.png)

# 온디맨드 코스 | 추천

몇몇 독자들이 데이터 엔지니어로 성장하는 데 도움이 될 온디맨드 코스를 요청했습니다. 제가 추천하는 3가지 좋은 자원은 다음과 같습니다:

- 데이터 엔지니어링 나노디그리 (UDACITY)
- 아파치 카프카 & 아파치 스파크를 이용한 데이터 스트리밍 나노디그리 (UDACITY)
- 파이스파크를 이용한 스파크 및 파이썬 빅데이터 과정 (UDEMY)

<div class="content-ad"></div>

아직 Medium 회원이 아니신가요? Medium이 제공하는 모든 것에 액세스하려면 매월 $5로 가입하는 것을 고려해보세요!

# 소개

데이터 엔지니어로써 우리는 종종 운영 데이터베이스에서 특히 큰 데이터 집합을 가져와서 일련의 변환을 수행한 후에 분석 데이터베이스나 S3 버킷과 같은 클라우드 객체 저장소에 기록해야 하는 상황에 직면합니다.

이 경우 Airflow 인스턴스에서 사용 가능한 메모리의 큰 부분을 사용하여 데이터 팀의 다른 동료들의 작업에 영향을 주지 않고 작업을 수행하는 방법을 찾아야 합니다.

<div class="content-ad"></div>

파이썬 생성기가 메모리 피크를 피하면서 데이터베이스에서 데이터를 효율적으로 가져올 때 사용될 수 있는 좋은 옵션이 될 수 있습니다.

실제로, 본 자습서에서는 생성기를 사용하는 것이 데이터 엔지니어에게 현명한 접근 방식인 두 가지 실용적인 사용 사례를 살펴볼 것입니다. 이를 위해 Docker 컨테이너를 구동하여 실제 엔드 투 엔드 데이터 워크플로를 시뮬레이션하기 위해 세 가지 서비스(포스트그레스 데이터베이스, 주피터 노트북 및 MinIO)를 실행할 것입니다.

# 파이썬에서 생성기의 장점

파이썬에서 표준 함수는 단일 값 계산 후 종료되지만, 생성기는 필요에 따라 일시 중지하고 다시 시작하면서 시간이 지남에 따라 값 시퀀스를 생성할 수 있습니다.

<div class="content-ad"></div>

제너레이터는 값을 시퀀스로 생성하기 위해 return 대신 yield 문을 사용하는 특별한 함수입니다. 값은 한 번에 하나씩 생성되며 전체 시퀀스를 메모리에 저장할 필요가 없습니다.

제너레이터 함수가 호출되면 제너레이터에 의해 생성된 값의 시퀀스를 반복할 수 있는 이터레이터 객체가 반환됩니다.

예를 들어, 0부터 입력 변수 n 사이의 숫자들의 제곱을 생성하는 squares_generator(n) 함수를 만들어 봅시다:

```js
def squares_generator(n):
  num = 0
  while num < n:
    yield num * num
    num += 1
```

<div class="content-ad"></div>

함수를 호출하면 이터레이터만 반환됩니다:

```js
squares_generator(n)

#출력:
# <generator object squares_generator at 0x10653bdd0>
```

모든 값의 시퀀스를 가져오려면 제너레이터 함수를 루프 안에서 호출해야합니다:

```js
for num in squares_generator(5):
  print(num)

#출력:
0
1
4
9
16
```

<div class="content-ad"></div>

더 효율적이고 세련된 옵션은 함수 대신 한 줄로 작성된 생성기 표현식을 만드는 것입니다:

```js
n = 5 
generator_exp = (num * num for num in range(n))
```

이제 값을 next() 메서드를 사용하여 직접 접근할 수 있습니다:

```js
print(next(generator_exp)) # 0
print(next(generator_exp)) # 1
print(next(generator_exp)) # 4
print(next(generator_exp)) # 9
print(next(generator_exp)) # 16
```

<div class="content-ad"></div>

우리가 볼 수 있듯이, 제너레이터 함수에서 값이 반환되는 방식은 일반적인 파이썬 함수와는 즉각적으로 직관적이지 않습니다. 아마도 그것이 많은 데이터 엔지니어들이 발생해야 할 정도로 제너레이터를 사용하지 않는 이유일 것입니다.

다음 섹션에서 두 가지 일반적인 사용 사례를 설명해보겠습니다.			               

# 목표 및 설정

이 자습서의 목표는 다음과 같습니다:

<div class="content-ad"></div>

- Postgres DB로부터 데이터를 가져와서 pandas 데이터프레임으로 저장합니다.
- pandas 데이터프레임을 Parquet 형식으로 S3 버킷에 씁니다.

각 목표는 일반 함수와 제너레이터 함수를 사용하여 모두 달성될 것입니다.

이러한 워크플로우를 시뮬레이션하기 위해 세 가지 서비스가 있는 도커 컨테이너를 실행합니다:

- Postgres DB = 데이터를 가져올 소스 운영 데이터베이스로 사용될 서비스입니다. Docker-compose가 mainDB를 생성하고 transactions이라는 테이블에 5백만 개의 모의 레코드를 삽입하는 작업을 수행합니다. 참고: 이 튜토리얼을 위한 자료를 준비하는 동안, 더 큰 데이터셋을 시뮬레이션하기 위해 5천만 개, 1억 개의 행을 시도해 보았지만 Docker 서비스의 성능에 영향을 미쳤습니다.
- MinIO = AWS S3 버킷을 시뮬레이션하는 데 사용될 서비스로, awswrangler 패키지를 사용하여 pandas 데이터프레임을 Parquet 형식으로 쓸 때 도움이 될 것입니다.
- Jupyter Notebook = 익숙한 컴파일러를 통해 Python 코드 조각을 대화식으로 실행하는 데 사용될 서비스입니다.

<div class="content-ad"></div>

지금까지 설명한 내용을 시각적으로 보여주는 그래프입니다:

![그래프](/assets/img/2024-05-23-PythonGeneratorsHowToEfficientlyFetchDataFromDatabases_1.png)

첫 번째 단계로는 프로젝트의 GitHub 리포지토리를 복제하고 해당 폴더로 이동합니다:

```js
git clone git@github.com:anbento0490/projects.git &&
cd fetch_data_with_python_generators
```

<div class="content-ad"></div>

그러면 세 가지 서비스를 시작하는 도커 컴포즈를 실행할 수 있어요:

```js
docker compose up -d

[+] Running 5/5
 ⠿ Network shared-network                 Created                                                 0.0s
 ⠿ Container jupyter-notebooks            Started                                                 1.0s
 ⠿ Container minio                        Started                                                 0.7s
 ⠿ Container postgres-db                  Started                                                 0.9s
 ⠿ Container mc                           Started                                                 1.1s
```

最終적으로 확인할 수 있어요:

- 포스트그레스 데이터베이스에 transactions 테이블이 생성되었고 5백만 개의 레코드가 포함되어 있어요:

<div class="content-ad"></div>

```js
docker exec -it postgres-db /bin/bash

root@9632469c70e0:/# psql -U postgres

psql (13.13 (Debian 13.13-1.pgdg120+1))
도움말을 보려면 "help"를 입력하세요.

postgres=# \c mainDB
데이터베이스 "mainDB"에 사용자 "postgres"로 연결되었습니다.

mainDB=# select count(*) from transactions;
  count
---------
 5000000
(1 로우)
```

- MinIO UI는 localhost:9001 포트에서 접속할 수 있습니다. 자격 증명을 요청 받을 때 (관리자 및 비밀번호를 입력)를 사용하고 generators-test-bucket이라는 빈 버킷이 생성되었습니다:

![image](/assets/img/2024-05-23-PythonGeneratorsHowToEfficientlyFetchDataFromDatabases_2.png)

- Jupyter Notebook UI는 localhost:8889에서 접근할 수 있으며 아래에 토큰을 검색하여 액세스할 수 있습니다:

<div class="content-ad"></div>

```bash
docker exec -it jupyter-notebooks /bin/bash

root@eae08d1f4bf6:~# jupyter server list

현재 실행 중인 서버:
http://eae08d1f4bf6:8888/?token=8a45d846d03cf0c0e4584c3b73af86ba5dk9e83c8ac47ee7 :: /home/jovyan
```

![Python Generators](/assets/img/2024-05-23-PythonGeneratorsHowToEfficientlyFetchDataFromDatabases_3.png)

좋아요! Jupyter에서 몇 가지 코드를 실행할 준비가 모두 끝났어요.

하지만 그 전에 MinIO의 버킷과 상호 작용하려면 새로운 access_key와 secret_access_key를 생성해야 합니다:```

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-PythonGeneratorsHowToEfficientlyFetchDataFromDatabases_4.png" />

알림: MinIO 버킷의 가장 멋진 기능 중 하나는 AWS S3 버킷처럼 상호 작용할 수 있다는 것입니다 (예: boto3, awswrangler 등을 사용하여). 하지만 이러한 기능은 비용이 발생하지 않으며, 로컬 환경에만 존재하므로 비밀을 노출할 걱정이 없습니다. 컨테이너가 중지될 때까지 유지되지 않으므로 데이터가 계속 유지되지 않습니다.

이제 생성기 노트북에서 다음 코드를 실행해 봅시다 (비밀 정보를 꼭 교체해주세요):

```python
import psycopg2
import pandas as pd
import boto3
import awswrangler as wr

#######################################################
######## PG DB에 연결하고 커서 생성 #######
connection = psycopg2.connect(user="postgres",
                              password="postgres",
                              port="5432",
                              database="mainDB")
cursor = connection.cursor()

query = "select * from transactions;"

#######################################################
######## MINIO 버킷에 연결 ###################

boto3.setup_default_session(aws_access_key_id='your_access_key',
                            aws_secret_access_key='your_secret_key')

bucket = 'generators-test-bucket'
folder_gen = 'data_gen'
folder_batch = 'data_batch'
parquet_file_name = 'transactions'
batch_size = 1000000

wr.config.s3_endpoint_url = 'http://minio:9000'
```

<div class="content-ad"></div>

이것은 mainDB에 연결하고 쿼리를 실행하기 위한 커서를 만듭니다. 또한 generators-test-bucket와 상호 작용하기 위한 기본 세션이 설정됩니다.

# 사용 사례 #1: 데이터베이스에서 읽기

데이터 엔지니어로서 데이터베이스 또는 외부 서비스에서 대규모 데이터 세트를 Python 파이프라인으로 가져올 때, 다음 사항 사이의 균형을 찾아야 합니다:

- 메모리: 한꺼번에 전체 데이터 세트를 가져오면 OOM 오류가 발생하거나 전체 인스턴스/클러스터의 성능에 영향을 줄 수 있습니다.
- 속도: 행을 하나씩 가져오는 것도 비싼 I/O 네트워크 작업을 초래할 수 있습니다.

<div class="content-ad"></div>

## 방법 #1: 일괄적으로 데이터 가져오기

실무에서 자주 사용하는 합리적인 절충안은 사용 가능한 메모리와 데이터 파이프라인의 속도 요구 사항에 따라 배치로 데이터를 가져오는 것입니다:

```js
# 1.1. 배치를 사용하여 DF 생성
def create_df_batch(cursor, batch_size):

    print('생성 중...')
    colnames = ['transaction_id', 
                'user_id', 
                'product_name', 
                'transaction_date', 
                'amount_gbp']
    
    df = pd.DataFrame(columns=colnames)
    cursor.execute(query)

    while True:
        rows = cursor.fetchmany(batch_size)
        if not rows:
            break
        # 일부 변환
        batch_df = pd.DataFrame(data = rows, columns=colnames)        
        df = pd.concat([df, batch_df], ignore_index=True)

    print('DF 생성 완료!\n')

    return df
```

위 코드는 다음을 수행합니다:

<div class="content-ad"></div>

- 빈 df를 생성;
- 쿼리를 실행하고 전체 결과를 커서 객체에 캐싱;
- while 루프를 초기화하여 매 반복마다 지정된 배치 크기(이 경우 1백만 행)와 동일한 행 수를 가져와 이 데이터를 사용하여 배치_df를 생성합니다.
- 최종적으로 배치_df가 주 df에 추가됩니다. 전체 데이터셋이 통과될 때까지 이 프로세스가 반복됩니다.

분명히 말하자면, 이것은 기본적인 예시이며, 단순히 한 번에 한 배치씩 df를 생성하는 것 외에도 while 루프의 일부로 다른 많은 작업(필터링, 정렬, 집계, 데이터를 다른 위치로 쓰기 등)을 수행할 수 있었습니다.

노트북에서 함수를 실행하면 다음과 같이 결과를 얻을 수 있습니다:

```js
%%time 
df_batch = create_df_batch(cursor, batch_size)
df_batch.head()

결과:

생성 중...
DF 생성 완료!

CPU 시간: 사용자 9.97초, 시스템: 13.7초, 총: 23.7초
실제 시간: 25초
```

<div class="content-ad"></div>

```markdown
![Python Generators](/assets/img/2024-05-23-PythonGeneratorsHowToEfficientlyFetchDataFromDatabases_5.png)

## Method #2: Using Generators

A less common -but powerful- strategy for data engineers is to fetch data as a stream using generators:

```python
# AUXILIARY FUNCTION
def generate_dataset(cursor):
    
    cursor.execute(query)
    
    for row in cursor.fetchall():
        # some transformation
        yield row

# 2.1. CREATE DF USING GENERATORS
def create_df_gen(cursor):
    print('Creating pandas DF using generator...')

    colnames = ['transaction_id',
                'user_id',
                'product_name',
                'transaction_date',
                'amount_gbp']
                
    df = pd.DataFrame(data=generate_dataset(cursor), columns=colnames)

    print('DF successfully created!\n')
    
    return df
```
```

<div class="content-ad"></div>

위의 코드 스니펫에서는 쿼리를 실행하고 행을 시퀀스로 반환하는 'generate_dataset' 보조 함수를 생성합니다. 이 함수는 'pd.DataFrame()' 절의 데이터 인수에 직접 전달되며, 내부적으로 모든 검색된 레코드를 순회하고 행이 소진될 때까지 요소를 생성합니다.

다시 말하지만, 이 예제는 매우 기본적이며(주로 설명 목적으로), 보조 함수 내에서 어떤 종류의 필터링이나 변환을 수행할 수 있습니다. 함수를 실행하면 다음과 같은 결과가 나옵니다:

```js
%%time 
df_gen = create_df_gen(cursor)
df_gen.head()

팬더스 데이터프레임 생성 중...
DF가 성공적으로 생성되었습니다!

CPU 소요 시간: 사용자 9.04초, 시스템 2.1초, 총 11.1초
실제 시간: 14.4초
```

<img src="/assets/img/2024-05-23-PythonGeneratorsHowToEfficientlyFetchDataFromDatabases_6.png" />

<div class="content-ad"></div>

두 가지 방법 모두 데이터 프레임이 반환되기 때문에 메모리 사용량이 동일할 것 같지만, 이는 사실이 아닙니다. 데이터 프레임이 생성되는 동안 데이터 처리 방식이 다르기 때문입니다:

- 방법 #1의 경우, 데이터 교환 과정이 다소 비효율적으로 이루어지고 네트워크를 통해 데이터가 교환되어 더 높은 최대 메모리가 발생합니다.
- 방법 #2의 경우, 필요할 때만 값을 계산하고 하나씩 처리하기 때문에 더 작은 메모리 공간을 사용합니다.

# 사용 사례 #2: 클라우드 객체 저장소에 쓰기

가끔 데이터 엔지니어는 데이터베이스에 저장된 대량의 데이터를 가져와서 이러한 레코드를 외부(예: 규제기관, 감사인, 파트너)와 공유해야 할 수 있습니다.

<div class="content-ad"></div>

일반적인 해결책은 클라우드 객체 저장소를 생성하는 것입니다. 데이터가 전달되어 제 3자(적절한 액세스 권한이 부여된)가 데이터를 읽고 자신의 시스템으로 복사할 수 있게 합니다.

사실, 우리는 데이터가 parquet 형식으로 작성될 버킷인 generators-test-bucket을 생성했습니다. 이는 awswrangler 패키지를 활용하여 데이터가 저장될 것입니다.

awswrangler의 장점은 pandas 데이터프레임과 매우 잘 작동하며 데이터 집합 구조를 유지한 채로 데이터프레임을 parquet 형식으로 변환할 수 있다는 것입니다.

## 방법 #1: 일괄 처리를 사용하기

<div class="content-ad"></div>

첫 번째 사용 사례의 경우, 일반적으로 데이터를 일괄적으로 가져와서 쓰는 것이 일반적이며 전체 데이터 집합이 순회될 때까지 계속됩니다 :

```js
# 1.2 WRITING DF TO MINIO BUCKET IN PARQUET FORMAT USING BATCHES
def write_df_to_s3_batch(cursor, bucket, folder, parquet_file_name, batch_size):
    colnames = ['transaction_id', 
                'user_id', 
                'product_name', 
                'transaction_date', 
                'amount_gbp']
    cursor.execute(query)
    batch_num = 1
    while True:
        rows = cursor.fetchmany(batch_size)
        if not rows:
            break
        print(f"Writing DF batch #{batch_num} to S3 bucket...")
        wr.s3.to_parquet(df= pd.DataFrame(data = rows, columns=colnames),
                         path=f's3://{bucket}/{folder}/{parquet_file_name}',
                         compression='gzip',
                         mode = 'append',
                         dataset=True)
        print('Batch successfully written to S3 bucket!\n')
        batch_num += 1
```

write_df_to_s3_batch() 함수를 실행하면 각각 100만 개의 레코드를 포함하는 5개의 파케이 파일이 해당 버킷에 생성됩니다 :

```js
write_df_to_s3_batch(cursor, bucket, folder_batch, parquet_file_name, batch_size)

Writing DF batch #1 to S3 bucket...
Batch successfully written to S3 bucket!

Writing DF batch #2 to S3 bucket...
Batch successfully written to S3 bucket!

Writing DF batch #3 to S3 bucket...
Batch successfully written to S3 bucket!

Writing DF batch #4 to S3 bucket...
Batch successfully written to S3 bucket!

Writing DF batch #5 to S3 bucket...
Batch successfully written to S3 bucket!
```

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-PythonGeneratorsHowToEfficientlyFetchDataFromDatabases_7.png" />

## 방법 #2: 제너레이터 사용하기

대안으로, 제너레이터를 활용하여 데이터를 추출하고 버킷에 작성할 수 있습니다. 제너레이터는 데이터를 가져오고 이동하는 동안 메모리 비효율성을 야기하지 않으므로 전체 DataFrame을 한 번에 쓰기를 결정할 수도 있습니다:

```js
# 2.2 GENERATOR를 사용하여 PARQUET 형식으로 DF를 MINIO 버킷에 쓰기
def write_df_to_s3_gen(cursor, bucket, folder, parquet_file_name):
    print('DF를 S3 버킷에 쓰는 중...')

    colnames = ['transaction_id', 
                'user_id', 
                'product_name', 
                'transaction_date', 
                'amount_gbp']
    
    wr.s3.to_parquet(df=pd.DataFrame(data=generate_dataset(cursor), columns=colnames),
             path=f's3://{bucket}/{folder}/{parquet_file_name}',
             compression='gzip',
             mode='append',
             dataset=True)
    print('데이터가 성공적으로 S3 버킷에 쓰여졌습니다!\n')
```

<div class="content-ad"></div>

```python
def wirte_df_to_s3_gen(cursor, bucket, folder_gen, parquet_file_name):

Writing DF to S3 bucket...
Data successfully written to S3 bucket!
```

![Python Generators](/assets/img/2024-05-23-PythonGeneratorsHowToEfficientlyFetchDataFromDatabases_8.png)

# 결론

<div class="content-ad"></div>

일반적인 Python 함수보다 직관성이 떨어지는 제너레이터는 메모리를 적게 차지하면서도 좋은 성능을 제공하기 때문에 덜 사용되지만 이점이 많습니다.

실제로 이 자습서에서는 데이터 엔지니어가 Python 제너레이터를 활용해 데이터베이스에서 데이터를 효율적으로 검색하는 방법을 연구하기 위해 세 가지 로컬 서비스(포스트그레스DB, 주피터 노트북, MinIO)를 도커를 통해 구동하여 데이터를 일괄로 처리하는 대신 데이터를 효율적으로 가져올 수 있는 두 가지 실제 예시를 공유했습니다.