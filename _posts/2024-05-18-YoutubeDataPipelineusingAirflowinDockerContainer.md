---
title: "YouTube 데이터 파이프라인 구축하기 Docker 컨테이너에서 Airflow 사용하기"
description: ""
coverImage: "/assets/img/2024-05-18-YoutubeDataPipelineusingAirflowinDockerContainer_0.png"
date: 2024-05-18 18:03
ogImage: 
  url: /assets/img/2024-05-18-YoutubeDataPipelineusingAirflowinDockerContainer_0.png
tag: Tech
originalTitle: "Youtube Data Pipeline using Airflow in Docker Container"
link: "https://medium.com/@rajugt49/youtube-data-pipeline-using-airflow-in-docker-container-c26fba76e060"
---


그게 많은 양이겠죠! 조금씩 나눠서 살펴봐요.

![YoutubeDataPipeline](/assets/img/2024-05-18-YoutubeDataPipelineusingAirflowinDockerContainer_0.png)

## 사용 사례: —

상상해봐요! 성장하는 YouTube 채널을 운영하는 콘텐츠 크리에이터라고 상상해봐요. 시청자들의 댓글과 답글을 통해 시청자를 이해하는 것은 귀중한 통찰력을 제공할 수 있어요. 그러나 수많은 동영상의 댓글을 수동으로 분류하는 것은 지칠 수 있죠. 이 프로세스를 자동화할 수 있는 방법이 있다면 어떨까요?

<div class="content-ad"></div>

## 제안된 해결책: —

위의 그림을 보시면, YouTube 비디오에서 댓글과 답글을 추출하기 위한 자동화된 솔루션을 안내해 드리겠습니다. 이 과정에는 여러 가지 주요 구성 요소가 포함됩니다:

— YouTube 데이터 API용 Python 라이브러리: YouTube 데이터 API와 상호 작용하기 위해 Python 라이브러리를 사용하여 댓글과 답글을 프로그래밍 방식으로 가져올 수 있습니다.

— 작업 관리를 위한 Airflow: 데이터 추출 및 처리 작업을 체계적으로 관리하기 위해 Apache Airflow를 Docker 컨테이너 내에서 사용할 것입니다.

<div class="content-ad"></div>

- 데이터 저장을 위한 AWS S3: 마지막으로, boto3 라이브러리를 사용하여 처리된 데이터를 AWS S3에 저장하게 됩니다. 나중에 쉽게 액세스하고 분석할 수 있습니다.

이 솔루션은 추출 프로세스를 자동화하는 데 그치지 않고 데이터가 구성되어 안전하게 저장되어 나중에 깊이 있는 분석을 위해 준비되어 있음을 보장합니다. 이제 이 워크플로우를 설정하고 실행하는 자세한 내용을 살펴보겠습니다.

## 구현

구현은 주로 두 가지 작업으로 구성되어 있습니다. 첫 번째는 인프라 구축, 두 번째는 코드 작업입니다. 그래서 이제 인프라 설정을 먼저 살펴보겠습니다.

<div class="content-ad"></div>

도커 데스크톱을 설치해보세요 — https://www.docker.com/get-started/

머신에 도커를 설치한 후에는 다음 명령어를 확인하여 설치가 성공적으로 이루어졌는지 확인하세요.

```js
docker --version
Docker version 20.10.23, build 7155243
```

최신 apache/airflow 이미지를 받아보세요

<div class="content-ad"></div>

```sh
도커 pull apache/airflow
```

다음 명령어를 사용하여 아파치 에어플로우 컨테이너를 시작하세요.

```sh
도커 run -p 8080:8080 -v /Users/local_user/airflow/dags:/opt/airflow/dags -v /Users/local_user/airflow/creds:/home/airflow/.aws -d apache/airflow standalone
```

AWS 자격 증명을 생성하여 원격 s3 버킷과 통신하여 날짜를 쓸 수 있습니다. 다음 링크를 통해 생성하세요 — 루트 사용자를 위한 액세스 키 생성하기
```

<div class="content-ad"></div>

```json
config

```json
[default]
region = ap-south-1
```

credentials

```json
[default]
aws_access_key_id = AKIB******AXPCMO
aws_secret_access_key = 4D7HkaIBsqu***********+0AT2a8j
```

<div class="content-ad"></div>

지역 사용자의 경우 두 파일을 로컬 머신의 /Users/local_user/airflow/creds/ 폴더로 복사해주세요. 이렇게 함으로써 이 파일들이 컨테이너에서 /home/airflow/.aws/ 경로에 마운트되도록 할 수 있습니다.

도커 데스크톱 애플리케이션을 열고 Airflow 컨테이너를 선택해주세요. 컨테이너 내에서 standalone_admin_password.txt 파일을 찾아주세요. 이 파일을 열고 Airflow 포털에 로그인하기 위한 비밀번호를 복사해주세요.

![이미지](/assets/img/2024-05-18-YoutubeDataPipelineusingAirflowinDockerContainer_1.png)

웹 브라우저를 열고 `http://localhost:8080` 주소로 이동해주세요. username에 admin을 입력하고 이전 단계에서 복사한 비밀번호로 로그인해주세요.

<div class="content-ad"></div>

`aws_write_utility.py` 파일을 만들 때 평소처럼 진행하시면 됩니다.

<div class="content-ad"></div>


```js
import boto3
import json
import uuid

def write_json_to_s3(json_data, bucket_name, key_name):

    # S3 클라이언트 초기화
    s3 = boto3.client('s3')
    
    # JSON 데이터를 바이트로 변환
    json_bytes = json.dumps(json_data).encode('utf-8')
    
    # JSON 데이터를 S3에 쓰기
    s3.put_object(Bucket=bucket_name, Key=key_name, Body=json_bytes)


def generate_uuid():
    """UUID와 유사한 문자열 생성."""
    return str(uuid.uuid4())
```

youtube_comments.py

```js
# -*- coding: utf-8 -*-

# youtube.commentThreads.list를 위한 샘플 Python 코드
# 이 코드 샘플을 로컬에서 실행하는 방법은 다음 링크를 참고하세요:
# https://developers.google.com/explorer-help/code-samples#python

import os

import googleapiclient.discovery
import aws_write_utility
from aws_write_utility import write_json_to_s3

def start_process():
    # 로컬에서 실행 시 OAuthlib의 HTTPS 확인 비활성화
    # 제품 환경에서는 이 옵션을 활성화하지 마세요.
    os.environ["OAUTHLIB_INSECURE_TRANSPORT"] = "1"

    api_service_name = "youtube"
    api_version = "v3"
    DEVELOPER_KEY = "AIzaS*****************PiwBdaP_IE"

    youtube = googleapiclient.discovery.build(
        api_service_name, api_version, developerKey=DEVELOPER_KEY)

    request = youtube.commentThreads().list(
        part="snippet,replies",
        videoId="r_K*****PKU"
    )
    response = request.execute()

    process_comments(response)


def process_comments(response_items):

    # 예시 S3 버킷 및 키 이름
    bucket_name = 'youtube-comments-analysis'
    key_name = 'data/{}.json'.format(aws_write_utility.generate_uuid())

    comments = []
    for comment in response_items['items']:
        author = comment['snippet']['topLevelComment']['snippet']['authorDisplayName']
        comment_text = comment['snippet']['topLevelComment']['snippet']['textOriginal']
        publish_time = comment['snippet']['topLevelComment']['snippet']['publishedAt']
        comment_info = {'author': author, 'comment': comment_text, 'published_at': publish_time}
        comments.append(comment_info)
    print(f'총 {len(comments)}개의 댓글 처리 완료.')
    write_json_to_s3(comments, bucket_name, key_name)
```

youtube_dag.py


<div class="content-ad"></div>

```python
from datetime import datetime, timedelta
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from youtube_comments import start_process

# 기본 인수 정의
default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': datetime(2024, 5, 16),
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

# DAG 객체 생성
dag = DAG(
    'youtube_python_operator_dag',
    default_args=default_args,
    description='Python 함수를 호출하는 간단한 DAG',
    schedule_interval=timedelta(days=1),
)

# PythonOperator 작업 생성
python_task = PythonOperator(
    task_id='my_python_task',
    python_callable=start_process,
    dag=dag,
)

# 작업 간 의존성 정의
python_task

# DAG 등록
dag
```

위의 .py 파일을 로컬 머신의 /Users/local_user/airflow/dags로 복사하세요. 이렇게 함으로써 컨테이너 내의 경로 /opt/airflow/dags로 마운트됩니다.

좋아요!!

이제 Airflow에서 DAG 페이지를 새로고침하세요. 위의 DAG가 표시될 것입니다. 실행해보고 문제가 있는지 로그를 확인해보세요. 녹색으로 변하면 작업이 완료된 것입니다.


<div class="content-ad"></div>

이 구현은 AWS EC2 인스턴스에 Airflow를 설정하여 수행할 수도 있습니다.