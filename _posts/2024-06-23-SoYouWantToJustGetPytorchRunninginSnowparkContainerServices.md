---
title: "Snowpark Container Services에서 Pytorch 실행하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-SoYouWantToJustGetPytorchRunninginSnowparkContainerServices_0.png"
date: 2024-06-23 00:49
ogImage: 
  url: /assets/img/2024-06-23-SoYouWantToJustGetPytorchRunninginSnowparkContainerServices_0.png
tag: Tech
originalTitle: "So You Want To…. Just Get (Pytorch) Running in Snowpark Container Services"
link: "https://medium.com/snowflake/so-you-want-to-just-get-pytorch-running-in-snowpark-container-services-be8501c7981f"
---


![그림](/assets/img/2024-06-23-SoYouWantToJustGetPytorchRunninginSnowparkContainerServices_0.png)

간략하게 말하자면: hello_world와 세계를 무대낼 수 있는 (Snowpark) 컨테이너 기반 제품 사이의 간극을 메우는 약간 더 고급급 튜토리얼

컨테이너는 복잡할 수 있지만, 꼭 그렇지 않아요. 이 복잡성의 한 이유는 "다중 노드 컨테이너 컴퓨팅 풀을 활용하여 무제한 규모와 탄성을 쉽게 구축하기 위해 컨테이너 사용"과 같은 제목의 고급 주제에 바로 뛰어든 가이드가 너무 많기 때문입니다. 이러한 가이드는 종종 컨테이너 전문 지식을 전제로 한, yaml 파일, 도커, 명령줄 등에 대한 가정 지식으로 가득 차 있습니다. 한 마디로, 압도적입니다. 문제가 발생할 때 사용자 에러와 구현 특이점을 구별하기 어려울 수 있습니다. 저와 같은 경우에는 마치 사기 치는 것처럼 느껴질 수 있어서, 더 어려울 수 있습니다.

다행히도 Snowflake는 사용하기 쉬운 Snowpark Container Services로 컨테이너의 세계를 간단하게 만들어 냈습니다. 이 안내서는 기본부터 시작하여 여러분의 실제 목표와 튜토리얼 사이의 간격을 줄이기 위해 설계되었습니다. 컨테이너에 대해 거의 전혀 알지 못하고 튜토리얼과 실제 목표 간의 간극을 메우기 어렵다고 느끼는 것을 가정합니다.

<div class="content-ad"></div>

# 0. 도커 설치

가장 쉬운 방법은 https://docs.docker.com/engine/install/ 로 이동하여 관련 사본을 다운로드하는 것입니다. 이를 백그라운드에서 실행해야 하므로 다운로드하고 설치한 후에는 앱을 더블 클릭해서 실행하는 것이 좋습니다.

# 1. 가장 간단한 튜토리얼 따르기

간단하다고 했지만, 정말로 간단하고 싶다면 저희 문서의 스노우파크 컨테이너 서비스에 있는 헬로 월드 내용을 따르는 게 어느 것도 이길 수 없습니다. 컨테이너에 대해 전혀 들어보지 못했다면 눈을 감고 따라하고 무언가를 실행한 후에 다음 단계로 넘어가서 무슨 일이 벌어지고 있는지 더 배워보세요.

<div class="content-ad"></div>

# 2. 로컬 파일 설정하기 (컨테이너화하기 위해)

이 작업을 모두 수행하는 데는 세 가지가 필요합니다

- Python 스크립트 (main.py는 좋은 시작 이름입니다): 컨테이너에서 실행되는 스크립트로, Dockerfile에서 참조됩니다. 이것은 단일 파일이 아니어도 됩니다. 큰 패키지일 수도 있지만, 지금은 간단하게 유지합시다.
- Dockerfile: Docker 이미지를 빌드하는 방법을 설명하는 파일로, 기본 환경, 패키지 임포트 및 내부 코드 실행 방법과 같은 내용이 포함됩니다. 가상 환경을 구축하는 conda create 과정과 유사한 개념으로 생각해보세요.
- spec.yaml: Snowflake가 컨테이너를 실행하는 방법을 Snowflake에 알려주는 파일로, 주로 하드웨어 구성 요소를 지정합니다. 이 파일은 snowflake에 특화되어 있지만, docker-compose.yaml에 상당히 유사합니다.

Python 스크립트

<div class="content-ad"></div>

여기 쉬운 것부터 시작해봅시다; 파이썬에서 실행할 스크립트를 만들어 보세요. 이는 다른 것을 실행하는 것과 다르지 않을 것입니다; 그저 몇 가지 파이썬의 특이점을 준수해야 합니다, 즉 if __name__ 비트). 아마 이런 식일 것입니다:

```python
# all my fancy code...

def run_job():
    #do something like run a pytorch neural network that references my fancy code

if __name__ == "__main__":
    run_job()
```

너무 길어지지 않게 하려고 전체를 제거했으나 실제 스크립트를 찾을 수 있습니다.

로컬에서는 그냥 main.py를 포함하는 폴더로 이동하여 명령줄에서 "python3 main.py"를 입력하면 실행됩니다 (아래 Dockerfile 설명 참조). 이 중요한 것을 기억하는 것이 중요한데, 이것은 임의로 복잡할 수 있기 때문에 print("hello world")나 오픈AI를 꺾을 새로운 LLM과 같은 것일 수도 있습니다.

<div class="content-ad"></div>

## Dockerfile

요렇게, Dockerfile이에요; 여기서 파일 유형은 보이지 않아요. 이렇게 보일 거에요:

```js
ARG BASE_IMAGE=continuumio/miniconda3:4.12.0   
FROM $BASE_IMAGE
RUN conda install python=3.8 && \
    pip install --upgrade pip && \
    pip install torch==2.0.0 && \
    pip install torchdata==0.6.0 && \
    pip install snowflake-ml-python==1.0.12 && \
    pip install snowflake-snowpark-python==1.9.0 \
    pip install accelerate==0.29.3

COPY main.py ./
ENTRYPOINT ["python", "main.py"]
```

ARG BASE_IMAGE은 이게 기반이 될 환경인데요, 우리 경우에는 "continuumio/miniconda3:4.12.0" 이에요. 더 자세한 내용은 여기에서 찾아볼 수 있어요.

<div class="content-ad"></div>

그 이후에는 pip/conda 설치를 실행합니다. 로컬 환경에서와 같이 실행될 것이므로 main.py 파일에서 사용할 필요가 있는 모든 패키지를 나열해주세요.

그런 다음 COPY main.py를 사용하여 컨테이너가 수행할 실제 작업을 정의하는 로컬 파일을 가져옵니다.

마지막으로, ENTRYPOINT가 Python을 실행하는 데 사용됩니다. 이는 스크립트를 실행하기 위해 명령줄에서 실행하는 것과 다른 점이 없습니다.

## spec.yaml

<div class="content-ad"></div>

좋아요, 마지막으로는 spec.yaml 파일입니다. 여기에서 Snowflake에 이 파일이 작동하도록 실제로 해야 하는 작업을 알려줍니다.

```js
spec:
  container:
  - env:
      MOUNT_PATH: /dev/shm
    image: /tutorial_db/data_schema/tutorial_repository/my_2job_image:latest
    name: main
    resources:
      limits:
        memory: 192G
        nvidia.com/gpu: 4
      requests:
        memory: 188G
        nvidia.com/gpu: 4
    volumeMounts:
    - mountPath: /opt/training-output
      name: training-output
    - mountPath: /dev/shm
      name: dshm
  volumes:
  - name: training-output
    source: '@tutorial_db.data_schema.tutorial_stage'
  - name: dshm
    size: 10Gi
    source: memory
```

첫 번째 눈에는 복잡해 보일 수 있지만, 실제로 들어가보면 꽤 간단합니다. Snowpark Container Services 문서는 좋은 자료입니다. 여기에서 찾을 수 있습니다.

그럼에도 불구하고, 몇 가지 주요 포인트를 소개해 드리겠습니다:

<div class="content-ad"></div>

- 이미지: 이는 우리가 4단계와 5단계에서 빌드하고 푸시하는 동일한 이미지입니다
- 리소스: 우리는 4대의 Nvidia GPU(4개의 A10G)를 지정한 것에 유의합니다. 이는 컴퓨트 풀을 만들 때 GPU_NV_M을 사용할 것이기 때문입니다. 해당 풀에는 Nvidia A10G가 4대 들어 있습니다. 자세한 내용은 여기를 참조하세요.
- 볼륨: 이는 Snowflake 내에서 작업 실행간에 무언가를 지속시킬 수 있는 능력을 제공합니다.

# 3. Snowflake 준비하기

Snowflake 문서의 자습서에서 잘 다루고 있지만, 완벽성을 위해 SQL도 여기에 포함했습니다:

```js
USE ROLE ACCOUNTADMIN;

CREATE ROLE test_role;

CREATE DATABASE IF NOT EXISTS tutorial_db;
GRANT OWNERSHIP ON DATABASE tutorial_db TO ROLE test_role COPY CURRENT GRANTS;

CREATE OR REPLACE WAREHOUSE tutorial_warehouse WITH
  WAREHOUSE_SIZE='X-SMALL';
GRANT USAGE ON WAREHOUSE tutorial_warehouse TO ROLE test_role;

CREATE SECURITY INTEGRATION IF NOT EXISTS snowservices_ingress_oauth
  TYPE=oauth
  OAUTH_CLIENT=snowservices_ingress
  ENABLED=true;

GRANT BIND SERVICE ENDPOINT ON ACCOUNT TO ROLE test_role;

CREATE COMPUTE POOL tutorial_gpu_pool
  MIN_NODES = 1
  MAX_NODES = 1
  INSTANCE_FAMILY = GPU_NV_M;
GRANT USAGE, MONITOR ON COMPUTE POOL tutorial_gpu_pool TO ROLE test_role;

GRANT ROLE test_role TO USER admin --로그인 사용자명

USE ROLE test_role;
USE DATABASE tutorial_db;
USE WAREHOUSE tutorial_warehouse;

CREATE SCHEMA IF NOT EXISTS data_schema;
CREATE IMAGE REPOSITORY IF NOT EXISTS tutorial_repository;
CREATE OR REPLACE STAGE tutorial_stage;
```

<div class="content-ad"></div>

일부 고려할 사항은 다음과 같습니다:

- ACCOUNTADMIN으로 모든 작업을 실행할 수 있다고 생각하지 마세요. 그럴 수 없어요. 이것은 의도된 것이며, 해당 역할이 제공하는 신의 모드 권한을 즐기던 우리에게는 새로운 변화입니다. test_role이 꼭 필요합니다 (또는 다른 동등한 권한).
- Compute Pools는 이 목록에서 아무 것이나 될 수 있습니다. 저희의 경우 GPU를 사용하고 있습니다 (GPU_NV_M, 이는 4개의 Nvidia A10G를 포함하고 있음) 이는 신경망이 빠르게 실행됩니다.

# 4. 로컬 환경에서 컨테이너 생성하기

먼저, Snowflake에서 다음을 실행하세요.

<div class="content-ad"></div>


이미지 저장소 표시

이렇게 보일 것입니다:

![image](/assets/img/2024-06-23-SoYouWantToJustGetPytorchRunninginSnowparkContainerServices_1.png)

저장소 URL이 있습니다 (추론할 수 있지만, 확실한 것이 무릎에 허물지 않는다는 것을 기억하세요)


<div class="content-ad"></div>

다음으로, 명령 프롬포트 또는 터미널에서 로컬 머신의 DockerFile 위치로 이동한 다음 다음을 실행하세요:

```js
docker build --rm --platform linux/amd64 -t repository_url/my_2job_image:latest .
```

여기서 repository_url은 위의 URL로 대체됩니다. 즉, 제 경우에는

sfseeurope-eu-demo211.registry.snowflakecomputing.com/tutorial_db/data_schema/tutorial_repository 입니다.

<div class="content-ad"></div>

- 도커를 호출하는 것은 도커라고 생각하면 됩니다.
- 빌드는 도커에 이미지를 빌드하라는 것입니다.
- --rm은 빌드로 생성된 중간 컨테이너를 제거합니다.
- --platform은 이를 위해 의도된 플랫폼을 지정합니다.
- repository_url/my_2job_image:latest .은 만들고 있는 REPOSITORY입니다.

참고로, 마침표(“.”)를 끝에 포함했는지 확인하세요. Docker에 익숙하지 않다면 쉽게 놓칠 수 있습니다.

여기서 중요한 점 몇 가지 주의할 점이 있습니다. 여기서 저는 낯익은 "latest" 태그를 사용하고 있지만, 실질적으로 명시적 버전 태그를 사용하는 것이 훨씬 좋습니다. 자세한 설명은 여기를 참고하세요.

- 이제 컨테이너가 생성되었으니, 진짜 생성되었는지 두 번 확인해보겠습니다. 명령줄에서 다음을 실행해주세요:

<div class="content-ad"></div>

```js
도커 이미지 ls
```

하시면 도커가 로컬로 보유한 이미지 목록을 확인할 수 있어요.

# 5. 명령줄/터미널을 통한 도커 로그인

Snowflake로 컨테이너를 푸시하려면 도커에 로그인해야 해요. 터미널/명령줄에서 다음 명령을 실행해보세요.

<div class="content-ad"></div>

```bash
docker login <registry_hostname> -u <username>
```

- registry_hostname은 `orgname`-`acctname`.registry.snowflakecomputing.com 형식을 취하며, sfseeurope-eu_demo1234.registry.snowflakecomputing.com와 유사한 형태를 가질 것입니다.
- username은 Snowflake GUI에 로그인하는 데 사용하는 사용자 이름입니다.
- 암호는 docker가 암호를 요청할 것이며, GUI에 로그인하는 데 사용하는 동일한 암호입니다.

# 6. Snowflake로 컨테이너 푸시하기

로컬 이미지도 좋지만, 우리는 그것들을 Snowflake로 옮기고 싶어요! 여기도 간단한 단계입니다:

<div class="content-ad"></div>

```js
도커 푸시 저장소_주소/my_2job_image:latest
```

# 7. Snowflake에 YAML 추가하기

거의 다 왔어요. 이제 YAML을 Snowflake로 가져와야 합니다. 이 작업을 하는 가장 좋은 방법은 SNOWSQL을 통해 하는 것이에요. 다운로드 페이지로 이동해주세요. 다운로드를 완료한 후에는 다음과 같이 계정 세부 정보로 로그인해야 합니다.

```js
snowsql -a 계정_이름
```

<div class="content-ad"></div>

계정 이름은 "sfseeurope-eu_demo1234"와 같은 형식일 것입니다 (적어도 제 경우에는요).

사용자 이름과 암호를 입력한 후 CLI에서 SQL을 입력할 수 있습니다. 다음을 수행하려면 아래와 같이 하세요:

```js
USE ROLE test_role;
USE DATABASE tutorial_db;
USE SCHEMA data_schema;
PUT file:///the_file_location @tutorial_stage
  AUTO_COMPRESS=FALSE
  OVERWRITE=TRUE;
```

또한 Snowflake GUI에서 결과를 확인할 수 있습니다.

<div class="content-ad"></div>

아래는 Markdown 형식으로 변환한 내용입니다. 😊


![이미지](/assets/img/2024-06-23-SoYouWantToJustGetPytorchRunninginSnowparkContainerServices_2.png)

# 8. 컨테이너 실행 (서비스 작업으로)

그런 다음 제공된 SQL 명령을 실행하면 됩니다. 데이터베이스, 롤, 스키마 등에 대해 명시적으로 언급했습니다. 이는 올바른 것들을 사용하고 있음을 확실히 하기 위한 것입니다. Snowpark Container Services는 롤 관리에 특히 민감하며 Snowflake의 다른 부분보다 더 그렇습니다. 잠재적인 문제를 피하고 머리아픈 일을 피하려면 다음과 같이 명시적으로 하세요:

```js
USE ROLE test_role;
USE DATABASE tutorial_db;
USE SCHEMA data_schema;
USE WAREHOUSE tutorial_warehouse;
EXECUTE JOB SERVICE
IN COMPUTE POOL tutorial_gpu_pool
NAME=tutorial_MT_job_service
FROM @tutorial_stage
SPEC='my_job_spec.yaml';
``` 


<div class="content-ad"></div>

# 9. 잘못된 부분을 해결해보세요

컨테이너는 뭔가 잘못될 때 매우 봉쇄적으로 느껴질 수 있어요. 특히 클라우드 플랫폼에서 실행되고 있는 상황에서 말이죠. 하지만 사실은, 몇 가지 중요한 부분의 코드만 추가했다면 간단합니다. 중요도 순으로 살펴보겠습니다:

- 로컬에서 작동하는지 확인하세요: 당연한 얘기이지만, 가끔은 처음으로 컨테이너에서 무언가를 실행할 때, 컨테이너에 문제가 있거나 코드에 문제가 있을 수 있기 때문에 확인이 필요합니다.
- 빠르고 더러운 방법: 코드에 몇 가지 프린트 문을 추가하여 진행 상황을 체크하고 코드를 디버깅할 수 있습니다.
- 로깅: "탄탄한" 방법은 로깅 함수를 설정하는 것이지만, 실제로는 print() 문을 사용하는 것과 유사합니다.

2번과 3번은 결과물을 검토할 곳이 있다는 가정하에 이루어집니다. 다행히도, 확인할 공간이 있습니다! 필요한 것은 다음을 실행하는 것뿐입니다:

<div class="content-ad"></div>

```sql
SELECT SYSTEM$GET_SERVICE_LOGS('TUTORIAL_DB.data_schema.tutorial_MT_job_service', 0, 'main');
```

이 명령을 실행하면 여러분의 시도에서 발생한 로그 파일이 반환되며, 무슨 문제가 발생했는지 디버깅할 수 있습니다.

# 마무리

이 안내서가 여러분이 컨테이너 사용을 시작하고 복잡한 애플리케이션을 배포할 때 자신감을 갖게 해주어서, 이미 "장난감" 이상의 것을 만들었다는 것을 알 수 있기를 바랍니다.

<div class="content-ad"></div>

최종적으로, 컨테이너를 모델 훈련의 수단으로만 보는 분들에게 한 마디 전하고 싶어요. 최근 Snowflake에서는 컨테이너 실행을 더 쉽게 만들어주는 프라이빗 프리뷰를 시작했어요. 노트북에서 일반적인 파이썬 실행과 2번의 클릭만으로 컨테이너 내에서 코드 실행이 가능해졌죠. 앞으로 더 쉬워질 것이니 이 공간을 주의깊게 지켜보세요.

# 추가 자료

전체 문서는 여기에서 확인할 수 있고, 제 동료들은 Snowpark 컨테이너 서비스 활용 방법에 관한 멋진 블로그 포스트를 작성했어요. 위 작업을 완료했다면 다음 단계로 가는 것이 좋겠죠:

- [Snowflake에서 책임 있는 AI: Snowflake Cortex, LLMS, Snowpark 컨테이너 서비스 활용](https://medium.com/snowflake/responsible-ai-on-snowflake-snowflake-cortex-llms-snowpark-container-services-snowflake-a0acc3e93909)
- [Snowpark 컨테이너 서비스를 활용한 OCR 포함 문서 추출 및 GPU 가속화](https://medium.com/@michaelgorkow/document-extraction-incl-ocr-with-gpu-acceleration-in-snowpark-container-services-d3d9f4d4764a)
- [Snowflake Cortex 기능 및 Snowpark 컨테이너 서비스를 활용한 콜센터 분석](https://medium.com/snowflake/call-centre-analytics-with-snowflake-cortex-function-and-snowpark-container-services-5e06b4baef46)

<div class="content-ad"></div>

또한 — 리뷰 코멘트를 제공해준 Caleb Baechtold에게 감사드립니다.