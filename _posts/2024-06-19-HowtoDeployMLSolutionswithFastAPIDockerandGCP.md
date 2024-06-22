---
title: "FastAPI, Docker, 그리고 GCP를 활용하여 ML 솔루션 배포하는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_0.png"
date: 2024-06-19 12:40
ogImage: 
  url: /assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_0.png
tag: Tech
originalTitle: "How to Deploy ML Solutions with FastAPI, Docker, and GCP"
link: "https://medium.com/towards-data-science/how-to-deploy-ml-solutions-with-fastapi-docker-and-gcp-de1bb8bfc59a"
---


Full Stack Data Science의 시리즈 중 5번째 기사입니다. 이 기사에서는 ML 기반 검색 API의 배포 방법을 안내합니다. 무수히 많은 방법으로 수행할 수 있지만, 여기에서는 거의 모든 머신 러닝 솔루션에 적용할 수 있는 간단한 3단계 접근법에 대해 설명합니다. 예시 코드는 GitHub 저장소에서 자유롭게 이용할 수 있습니다.

![이미지](/assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_0.png)

머신 러닝이 모델 훈련에 관한 멋진 모델만이 아니라는 것을 생각하면, 실제로 모델 스스로는 가치를 만들어내지 않습니다. ML 모델을 ML 솔루션(즉, 가치 있는 것)으로 만들기 위해 "배포"해야 합니다.

이를 다양한 형태로 구현할 수 있습니다. 예를 들어 사용자가 모델과 상호 작용할 수 있는 웹 인터페이스를 생성하거나, 기존 소프트웨어 시스템에 모델을 통합하거나, 개발자가 모델에 액세스할 수 있는 API를 설정하는 것 등이 있습니다 (OpenAI와 같은 사례를 생각해보세요).

<div class="content-ad"></div>

여기서 설명하는 3단계 전략은 모든 예제와 호환됩니다. 이는 다음으로 구성됩니다:

- 추론 API 생성 (FastAPI 사용)
- API 컨테이너화 (도커를 통해)
- 클라우드 플랫폼에서 컨테이너 실행 (여기서는 GCP 사용)

# FastAPI

이 배포 전략의 첫 번째 단계는 모델을 API(즉, 응용 프로그램 프로그래밍 인터페이스)로 랩핑하는 것입니다. 간단히 말해, API를 사용하면 응용 프로그램과 프로그래밍 방식으로 상호 작용할 수 있습니다.

<div class="content-ad"></div>

첫 번째 단계를 수행하는 한 가지 방법은 파이썬 함수를 API 엔드포인트로 변환하는 매우 쉬운 방법을 제공하는 Python 라이브러리인 FastAPI를 사용하는 것입니다. 아래 코드는 구체적인 예제를 제공합니다.

## Docker

우리의 추론 API를 직접 클라우드에 배포할 수 있지만, 먼저 "컨테이너화"하는 것이 좋습니다. 이때 Docker가 필요합니다.

Docker를 사용하면 API의 종속성을 모두 포함하는 가벼운 래퍼를 생성할 수 있어서 새로운 컴퓨터에서 더 쉽게 구동할 수 있습니다.

<div class="content-ad"></div>

작업은 2 단계 프로세스를 통해 수행됩니다. 첫째, 우리는 Docker 이미지를 생성합니다. 이는 기본적으로 시스템에 API를 제로에서 어떻게 구동할지 알려주는 레시피입니다 (걱정하지 마세요, 만들기 쉽습니다). 둘째, Docker가 설치된 시스템에서 이미지를 실행할 수 있습니다. 실행 중인 이미지를 컨테이너라고 하며, 이는 더 큰 시스템에 있는 작은 가상 머신과 같습니다.

# Google Cloud Run

마지막으로, Docker 이미지를 실행할 컴퓨팅 리소스가 필요합니다. 물론 노트북에서 이 작업을 수행할 수도 있지만 (아마 좋은 아이디어는 아닙니다), 온프레미스 서버나 클라우드 제공업체를 통해 이를 수행할 수 있습니다.

여기서는 Google Cloud Run을 사용하여 Docker 컨테이너를 실행하는 GCP 서비스를 사용합니다. 이 서비스에는 무료 티어도 있으므로 불필요한 비용이 발생하지 않고 프로젝트를 배포할 수 있습니다.

<div class="content-ad"></div>

# 예시 코드: 시맨틱 검색 API 배포하기

기본 개념을 이해했으니, 실제 코드에서 이 프로세스가 어떻게 보이는지 살펴봅시다. 아래 예시는 이 시리즈의 이전 기사들을 바탕으로 구축되었으며, 제 유튜브 비디오의 제목과 대본을 가져와 텍스트 임베딩으로 변환한 것을 사용합니다.

간단히 말해, 텍스트 임베딩은 텍스트의 의미론적인 의미 있는 숫자 표현으로, 새로운 종류의 검색(시맨틱 검색이라고도 함)을 가능하게 합니다.

여기에서는 제 유튜브 비디오의 모든 제목과 대본에 대한 시맨틱 검색 시스템을 위한 API를 구축하고 배포할 것입니다. 이 API는 Hugging Face spaces에서 실행되는 실시간 검색 애플리케이션의 백엔드입니다.

<div class="content-ad"></div>

이 앱의 백엔드와 프론트엔드의 코드 저장소가 모두 무료로 제공됩니다.

## 단계 1: API 만들기

FastAPI를 사용하면 기존의 Python 스크립트를 몇 줄의 추가 코드로 API로 변환하는 것이 매우 쉽습니다. 이게 바로 그 모습입니다.

먼저 유용한 라이브러리를 import할 거에요.

<div class="content-ad"></div>

```js
from fastapi import FastAPI
import polars as pl
from sentence_transformers import SentenceTransformer
from sklearn.metrics import DistanceMetric
import numpy as np
from app.functions import returnSearchResultIndexes
```
이제 우리는 시맨틱 검색 기능의 구성 요소를 정의할 겁니다. 구체적으로 텍스트 임베딩 모델, 우리가 검색하려는 비디오들의 제목 및 대본 임베딩, 그리고 사용자 쿼리에 가장 관련성 높은 비디오를 평가하는 거리 측정 기준입니다. 시맨틱 검색에 대해 더 깊게 알아보고 싶다면, 이에 대해 이전에 다룬 글을 참조해주세요.

```js
# define model info
model_name = 'all-MiniLM-L6-v2'

# load model
model = SentenceTransformer(model_name)

# load video index
df = pl.scan_parquet('app/data/video-index.parquet')

# create distance metric object
dist_name = 'manhattan'
dist = DistanceMetric.get_metric(dist_name)
```
이제 API 작업을 정의합니다. 여기서 3개의 GET 요청을 생성할 것입니다. 첫 번째 요청은 아래 코드 블록에 표시되어 있습니다.

<div class="content-ad"></div>

```python
# FastAPI 객체 생성
app = FastAPI()

# API 동작
@app.get("/")
def health_check():
    return {'health_check': 'OK'}
```

위 블록에서는 FastAPI() 클래스를 사용하여 새 FastAPI 애플리케이션을 초기화하고 "health check" 엔드포인트를 만듭니다.

이를 위해 입력이 없고 "health_check" 키와 값이 "OK"인 사전을 반환하는 Python 함수를 정의합니다. 이 함수를 API 엔드포인트로 변환하려면 단순히 데코레이터를 추가하고 엔드포인트의 경로를 지정하면 됩니다. 여기서는 루트인 즉, "/"를 사용합니다.

또 다른 예제를 살펴보겠습니다. 여기서는 API에 대한 자세한 정보를 반환하는 info라는 엔드포인트가 있습니다.


<div class="content-ad"></div>

```js
@app.get("/info")
def info():
    return {'name': 'yt-search', 'description': "Shaw Talebi의 YouTube 비디오를 검색하는 API입니다."}
```

이 엔드포인트는 헬스 체크와 매우 유사한 것을 볼 수 있습니다. 그러나 이것은 "/info" 엔드포인트에 위치합니다.

마지막으로 사용자 쿼리를 받아 가장 관련 있는 비디오의 제목과 ID를 반환하는 검색 엔드포인트를 만들어 봅시다.

```js
@app.get("/search")
def search(query: str):
    idx_result = returnSearchResultIndexes(query, df, model, dist)
    return df.select(['title', 'video_id']).collect()[idx_result].to_dict(as_series=False)
```

<div class="content-ad"></div>

이 엔드포인트에는 입력이 필요합니다: 사용자의 쿼리입니다. 이 쿼리는 다른 스크립트에 정의된 또 다른 Python 함수로 전달됩니다. 이 함수는 검색에 대한 모든 수학 연산을 수행합니다. 여기서 자세히 다루지는 않겠지만, 궁금한 독자는 코드를 GitHub에서 볼 수도 있고 YouTube에서 검색 함수의 코드 설명을 볼 수도 있습니다.

이 함수는 검색 결과의 행 번호만 df 데이터프레임에서 반환하므로, 우리는 이 출력을 사용하여 관심 있는 제목과 비디오 ID를 가져와서 이를 Python 사전으로 반환해야 합니다. API 엔드포인트의 모든 출력이 사전이어야 하는데요, 이는 API의 표준 JSON 형식을 준수하기 때문입니다.

위 코드 블록에서 설명한대로, 두 개의 외부 파일인 app/functions.py 및 app/data/video-index.parquet을 참조합니다. 이는 다음 디렉토리 구조를 시사합니다.

![image](https://example.com/assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_1.png)

<div class="content-ad"></div>

이 API를 로컬에서 실행하려면 루트 디렉토리로 이동하여 다음 명령을 실행할 수 있습니다.

```js
uvicorn app.main:app --host 0.0.0.0 --port 8080
```

uvicorn은 FastAPI를 사용하여 작성한 웹 애플리케이션을 실행할 수 있게 해주는 파이썬 라이브러리입니다. 이 명령은 이 API를 로컬에서 http://0.0.0.0:8080에서 실행합니다. 나중에 Google Cloud Run에 배포할 때 이 호스트와 포트를 사용하는 이유를 나준내게 될 것입니다.

## 단계 2: 도커 이미지 생성

<div class="content-ad"></div>

우와, 우리 API가 로컬에서 작동하고 있어요! 이제 클라우드에서 실행할 수 있도록 다음 단계를 진행해 봐요.

이를 위해서 API용 Docker 이미지를 생성할 거에요. 이를 위해 Dockerfile, requirements.txt, 그리고 app/__init__.py 이 3가지 파일을 만들어야 해요. 우리 디렉토리는 아래와 같이 보여야 해요.

![이미지](/assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_2.png)

Dockerfile은 Docker 이미지를 실행하는 단계별 지침을 포함하고 있어요. requirements.txt는 API를 실행하는 데 필요한 Python 라이브러리(버전 포함)를 지정해요. 마지막으로 app/__init__.py 파일은 app 폴더를 Python 패키지로 지정해주어, 컨테이너에서 실행될 때 Python이 API 코드를 찾고 적절하게 가져올 수 있도록 해줘요.

<div class="content-ad"></div>

다음은 Dockerfile 내부의 내용입니다.

```js
# python 기본 이미지에서 시작
FROM python:3.10-slim

# 작업 디렉토리 변경
WORKDIR /code

# 요구 사항 파일을 이미지에 추가
COPY ./requirements.txt /code/requirements.txt

# Python 라이브러리 설치
RUN pip install --no-cache-dir --upgrade -r /code/requirements.txt

# Python 코드 추가
COPY ./app/ /code/app/

# 기본 명령어 지정
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8080"]
```

첫 번째 줄은 기존에 Python 3.10이 설치된 이미지 위에 저희의 이미지를 부트스트랩합니다. 다음으로 작업 디렉토리를 루트에서 /code로 변경합니다.

그런 다음 요구 사항 파일을 코드베이스에서 Docker 이미지로 복사합니다. 이를 통해 pip를 사용하여 요구 사항을 설치할 수 있습니다.

<div class="content-ad"></div>

그 다음으로, API에 대한 모든 코드를 복사합니다.

참고: Python 패키지를 먼저 복사하고 설치했습니다. 이렇게 함으로써 요구 사항의 설치를 캐시할 수 있습니다. 개발 중에 빠르게 Docker 이미지를 실행할 때 의존성을 설치하는 데 몇 분을 기다릴 필요가 없도록 도와줍니다.

마지막으로, 개발 중에 API를 실행할 때 지역에서 실행했던 것과 동일한 기본 명령을 지정합니다.

## 단계 3: Google Cloud에 배포

<div class="content-ad"></div>

이 시점에서 우리는 Docker 이미지를 빌드하고 Docker Hub에 푸시하여 여러 다른 클라우드 서비스에 쉽게 배포할 수 있습니다. 하지만 여기서는 대안 전략을 따를 거에요.

대신에, 우리 모든 코드를 GitHub에 푸시할 거에요. 그럼 바로 이 코드를 Google Cloud Run에 컨테이너 배포할 수 있어요. 이 방법에는 두 가지 주요 장점이 있어요.

첫째로, 로컬 시스템과 Google Cloud Run이 사용하는 시스템 아키텍처 간의 차이를 해결하느라 시간을 들일 필요가 없어요 (특히 저는 Mac이 ARM64로 동작하기 때문에 이 문제가 있었어요). 둘째로, GitHub 리포지토리에서 배포함으로써 지속적인 배포가 가능해져요. 그래서 API를 업데이트하고 싶다면, 그냥 새 코드를 리포지토리에 푸시하면 새 컨테이너가 자동적으로 생성돼요.

우리는 새 GitHub 리포지토리를 만들면서 시작해볼게요.

<div class="content-ad"></div>


<img src="/assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_3.png" />

이제 리포지토리를 복제하고 코드를 추가한 후 GitHub로 푸시합니다. 코드를 추가한 후 디렉토리 구조는 다음과 같이 보입니다.

<img src="/assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_4.png" />

코드가 준비되었으면 새 Google Cloud Platform 프로젝트를 만들 수 있습니다. GCP 콘솔로 이동하여 프로젝트 목록을 클릭한 다음 "새 프로젝트"를 선택하여 진행합니다.


<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_5.png" />

프로젝트가 생성되면 해당 프로젝트를 열고 검색 창에 "cloud run"을 입력할 수 있습니다.

<img src="/assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_6.png" />

그것을 열면 "CREAT SERVICE"를 클릭할 것입니다. 이렇게 하면 서비스를 구성할 수 있는 페이지가 열립니다.

<div class="content-ad"></div>

먼저 GitHub에서 서비스를 배포할 옵션을 선택합니다. 그런 다음 "CLOUD BUILD로 설정"을 클릭하세요.

[이미지](/assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_7.png)

저장소 소스로는 GitHub을 선택하고 방금 만든 저장소를 선택합니다.

[이미지](/assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_8.png)

<div class="content-ad"></div>

지점을 ^main$으로 유지하고 "Build Type"을 Dockerfile로 선택합니다.

![이미지](/assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_9.png)

다음으로, 서비스 구성 화면으로 돌아갑니다. 서비스의 이름을 마음대로 지정할 수 있습니다 (저는 자동 생성된 이름을 그대로 두겠습니다). 지역은 us-central1로 남겨두겠습니다. 이 지역은 가장 저렴한 컴퓨팅 옵션을 제공하는 Tier 1이기 때문에 이 예시에서는 무료입니다.

간단히 유지하려면 "인증되지 않은 호출 허용"을 선택합니다. 물론 대부분의 시스템에는 인증이 필요할 것입니다. 그런 다음, 나머지를 기본값으로 남겨둡니다.

<div class="content-ad"></div>

이제 "컨테이너, 볼륨, 네트워킹, 보안" 아래에서 컨테이너를 편집하여 1 GiB의 메모리를 할당하세요. Dockerfile에서 구성한 대로 PORT가 8080으로 설정되어 있기 때문에 이것을 변경하시면 안 됩니다.

나머지 설정은 기본값으로 그대로 두고 화면 아래의 "생성"을 클릭하세요. 수 분 후 컨테이너가 활성화될 것입니다!

<div class="content-ad"></div>

![image](/assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_12.png)

그런 다음 페이지 상단 근처에 지정된 URL을 사용하여 API에 액세스할 수 있습니다. 링크를 클릭하면 루트 엔드포인트가 열립니다. 이 엔드포인트는 건강 상태 확인이었습니다.

![image](/assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_13.png)

다음 URL을 사용하여 검색 API에 대한 GET 요청을 수동으로 실행할 수 있습니다: [여기에 앱의 URL 입력]/search?query=LLMs. 이를 통해 LLMs와 관련된 비디오를 검색할 수 있습니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-HowtoDeployMLSolutionswithFastAPIDockerandGCP_14.png" />

## 보너스: UI에 통합하기

API 백엔드를 설정한 후, 사용자 친화적 인터페이스에 연결할 수 있습니다. 저는 Hugging Face Spaces를 통해 이를 수행합니다. 이 곳은 완전히 무료로 ML 앱을 호스팅합니다.

이것이 같은 검색이 UI를 통해 어떻게 보이는지입니다. 여기서 UI를 테스트하고 코드를 확인할 수 있습니다.

<div class="content-ad"></div>

![image](https://miro.medium.com/v2/resize:fit:1200/1*St0OCAxBpqORc95WDpq4Pg.gif)

# 결론

데이터 과학은 멋진 모델을 훈련시키는 것 이상의 의미를 가지고 있습니다. 문제를 해결하고 가치를 창출하는 것이 중요합니다. 종종, 이를 위해서는 모델을 배포하여 최대 효과를 발휘할 수 있는 환경으로 이전해야 합니다. 여기에서는 FastAPI, Docker 및 GCP를 사용하여 ML 모델을 배포하는 간단한 3단계 전략을 살펴보았습니다.

풀 스택 데이터 과학 시리즈를 마치는 글이지만, 이 글들은 이 검색 도구를 생성하는 과정에 관련된 실험에 대한 보너스 비디오가 포함된 YouTube 플레이리스트와 함께 제공됩니다.

<div class="content-ad"></div>

Full Stack Data Science에 관해 더 알아보기 👇

# 자료

연결하기: [내 웹사이트](링크) | 전화 상담 예약

소셜 미디어: [YouTube 🎥](링크) | [LinkedIn](링크) | [Twitter](링크)

<div class="content-ad"></div>

Support: 커피 한 잔 사주세요 ☕️