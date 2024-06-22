---
title: "Poetry로 초고속 Python Docker 빌드 하는 방법 "
description: ""
coverImage: "/assets/img/2024-06-23-BlazingfastPythonDockerbuildswithPoetry_0.png"
date: 2024-06-23 00:42
ogImage: 
  url: /assets/img/2024-06-23-BlazingfastPythonDockerbuildswithPoetry_0.png
tag: Tech
originalTitle: "Blazing fast Python Docker builds with Poetry 🏃"
link: "https://medium.com/@albertazzir/blazing-fast-python-docker-builds-with-poetry-a78a66f5aed0"
---


## 느린 번거로운 도커 빌드를 원활한 작업으로 바꿀 수 있는 방법

![image](/assets/img/2024-06-23-BlazingfastPythonDockerbuildswithPoetry_0.png)

프로젝트의 도커 이미지를 빌드하는 것은 일반적으로 재현 가능하고 결정론적인 방식으로 종속성을 설치하는 작업을 포함합니다. 파이썬 커뮤니티에서 Poetry는 이를 달성하기 위한 가장 확실한 도구 중 하나입니다. 그러나 도커 빌드에서 Poetry를 최적으로 활용하지 않으면 성능이 저하되고 긴 빌드 시간으로 개발자 생산성이 저하될 수 있습니다.

본 문서는 이미 Poetry와 도커, 특히 도커 레이어 캐싱 작동 방식에 대해 익숙한 독자들을 가정하고 있으며 빌드를 최적화하기 위한 방법을 찾고 있는 독자를 위해 작성되었습니다. 이 글은 각 최적화의 영향을 이해할 수 있도록 순진한 해결책부터 최적화된 해결책까지 구조화되어 있습니다. 소개는 여기까지, 이제 몇 개의 Dockerfile을 살펴보겠습니다! 💪

<div class="content-ad"></div>

# 0. 프로젝트 구조

우리는 이야기할 작은 프로젝트를 사용해 볼까요? 그 이름은 제가 본 산 중에서 최고인 알나푸르나 산으로 임의로 지었습니다 ⛰ 극소 프로젝트 구성은 pyproject.toml, 관련된 poetry.lock, 코드 및 Dockerfile을 포함할 것입니다.

```js
.
├── Dockerfile
├── README.md
├── annapurna
│   ├── __init__.py
│   └── main.py
├── poetry.lock
└── pyproject.toml
```

단순함을 위해, 유명한 fastapi 웹 서버를 poetry add fastapi를 통해 설치하고, 제 프로젝트에서 일반적으로 사용하는 몇 가지 린터들을 설치해보았습니다.

<div class="content-ad"></div>


[tool.poetry]
name = "annapurna"
version = "1.0.0"
description = ""
authors = ["Riccardo Albertazzi <my@email.com>"]
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.11"

fastapi = "^0.95.1"


[tool.poetry.group.dev.dependencies]
black = "^23.3.0"
mypy = "^1.2.0"
ruff = "^0.0.263"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"


## 1. The naive approach 😐

What our Docker build needs to do is having Python and Poetry installed, getting our code, installing the dependencies and setting the project’s entrypoint. This is exactly what we are doing in here:

```dockerfile
FROM python:3.11-buster

RUN pip install poetry

COPY . .

RUN poetry install

ENTRYPOINT ["poetry", "run", "python", "-m", "annapurna.main"]
```

<div class="content-ad"></div>

이 간단한 Dockerfile은 일을 해내고, 단순한 `docker build .` 명령으로 이미 동작하는 이미지를 얻을 수 있습니다. 실제로 이것은 자습서와 오픈 소스 프로젝트에서 보게 되는 전형적인 Dockerfile로, 이해하기 쉽기 때문입니다. 그러나 프로젝트가 성장하면 번거로운 빌드와 거대한 Docker 이미지로 이끌게 될 것입니다. 저의 결과 Docker 이미지는 실제로 1.1GB입니다! 우리가 볼 최적화는 캐싱을 활용하고 최종 이미지 크기를 줄이는 방향으로 이루어집니다.

## 2. 워밍업 🚶

워밍업을 위한 몇 가지 개선 사항부터 시작해봅시다:

- Poetry 버전을 고정하세요. Poetry는 마이너 버전 간에 중요한 변경 사항이 포함될 수 있으므로 새 버전이 출시될 때 빌드가 갑자기 실패하지 않도록 하려면 로컬에서 사용 중인 버전과 동일한 버전으로 명확히 고정해야 합니다.
- 필요한 데이터만 COPY하세요. 이렇게 하면 예를 들어 로컬 가상 환경(.venv에 위치)의 불필요한 복사를 피할 수 있습니다. README.md 파일이 없으면 Poetry가 경고를 표시할 것이지만(저는 이 선택을 공유하지 않습니다), 따라서 비어있는 파일을 생성합니다. 로컬 파일을 복사할 수도 있지만 수정할 때마다 Docker 레이어 캐싱을 효과적으로 방지하게 됩니다.
- 개발 의존성을 설치하지 마세요. 실제로 프로덕션 환경에서는 린터(linters)와 테스트 스위트(test suites)가 필요하지 않으므로 `poetry install --without dev`로 개발 의존성을 설치하지 마세요.

<div class="content-ad"></div>

```Dockerfile
FROM python:3.11-buster

RUN pip install poetry==1.4.2

WORKDIR /app

COPY pyproject.toml poetry.lock ./
COPY annapurna ./annapurna
RUN touch README.md

RUN poetry install --without dev

ENTRYPOINT ["poetry", "run", "python", "-m", "annapurna.main"]
```

이로써 이미 1.1GB에서 959MB로 줄었습니다. 그리 많지는 않지만, 성실하게 작업한 결과네요.

# 3. Poetry 캐시 정리 🧹

기본적으로 Poetry는 다운로드한 패키지를 캐시하여 이후의 설치 명령에 재사용할 수 있게 합니다. 하지만 우리는 Docker 빌드에서 이를 신경쓰지 않아도 됩니다 (그렇죠?) 그래서 중복 저장소를 제거할 수 있습니다.

<div class="content-ad"></div>

- 시는 --no-cache 옵션도 지원하므로 왜 사용하지 않는 걸까요? 나중에 이에 대해 알아보겠죠 ;)
- 캐시 폴더를 제거할 때는 동일한 RUN 명령어에서 수행해야 합니다. 별도의 RUN 명령어에서 수행하면 캐시는 이전 Docker 레이어 (poetry install을 포함하는 레이어)의 일부로 남아 있어서 최적화가 무용지물이 될 수 있습니다.

이 작업을 수행하는 동안 빌드의 결정적 특성을 더 강화하기 위해 몇 가지 Poetry 환경 변수를 설정합니다. 그 중에서도 가장 논란이 되는 것은 POETRY_VIRTUALENVS_CREATE=1입니다. 도커 컨테이너 내에서 가상 환경을 만들고 싶은 이유가 뭘까요? 솔직히 이 플래그를 비활성화하는 것보다 이 솔루션을 선호합니다. 이 플래그를 비활성화하는 것보다 환경이 가능한 한 격리되고, 무엇보다도 설치가 시스템 Python이나 심지어 Poetry 자체와 충돌하지 않도록 해줍니다.

```js
FROM python:3.11-buster

RUN pip install poetry==1.4.2

ENV POETRY_NO_INTERACTION=1 \
    POETRY_VIRTUALENVS_IN_PROJECT=1 \
    POETRY_VIRTUALENVS_CREATE=1 \
    POETRY_CACHE_DIR=/tmp/poetry_cache

WORKDIR /app

COPY pyproject.toml poetry.lock ./
COPY annapurna ./annapurna
RUN touch README.md

RUN poetry install --without dev && rm -rf $POETRY_CACHE_DIR

ENTRYPOINT ["poetry", "run", "python", "-m", "annapurna.main"]
```

# 4. Installing dependencies before copying code 👏

<div class="content-ad"></div>

지금까지 잘 진행되고 있지만,
Docker 빌드가 여전히 매우 고통스러운 문제를 겪고 있어요: 코드를 수정할 때마다 의존성을 다시 설치해야 합니다! 이것은 우리가 코드를 COPY(이것이 Poetry가 프로젝트를 설치하는 데 필요한 것)하기 전에 RUN poetry install 명령을 실행하기 때문입니다. Docker 레이어 캐싱이 작동하는 방식 때문에 COPY 레이어가 무효화될 때마다 다음 레이어도 다시 빌드해야 합니다. 프로젝트가 점점 커질수록 코드 한 줄을 변경하더라도 매우 지루하고 빌드 시간이 길어질 수 있습니다.

해답은 Poetry에 가상 환경을 빌드하는 데 필요한 최소한의 정보를 제공하고 나중에 코드베이스를 COPY 하는 것입니다. 이를 위해 --no-root 옵션을 사용하여 현재 프로젝트를 가상 환경에 설치하지 않도록 지시할 수 있어요.


FROM python:3.11-buster

RUN pip install poetry==1.4.2

ENV POETRY_NO_INTERACTION=1 \
    POETRY_VIRTUALENVS_IN_PROJECT=1 \
    POETRY_VIRTUALENVS_CREATE=1 \
    POETRY_CACHE_DIR=/tmp/poetry_cache

WORKDIR /app

COPY pyproject.toml poetry.lock ./
RUN touch README.md

RUN poetry install --without dev --no-root && rm -rf $POETRY_CACHE_DIR

COPY annapurna ./annapurna

RUN poetry install --without dev

ENTRYPOINT ["poetry", "run", "python", "-m", "annapurna.main"]


이제 애플리케이션 코드를 수정해보세요. 마지막 3개의 레이어만 다시 계산되는 것을 볼 수 있어요. 빌드가 엄청나게 빨라졌죠! 🚀

<div class="content-ad"></div>

- 가상 환경에서 프로젝트를 설치하려면 추가적인 실행할 `poetry install --without dev` 명령이 필요합니다. 이는 사용자 정의 스크립트를 설치하는 데 유용할 수 있습니다. 프로젝트에 따라 이 단계가 필요하지 않을 수도 있습니다. 어쨌든, 이미 프로젝트 종속성이 설치되어 있기 때문에 이 계층 실행은 매우 빠를 것입니다.

# 5. 다중 단계 Docker 빌드 사용 🏃‍♀

지금까지 빌드는 빠르지만 여전히 큰 Docker 이미지가 생성됩니다. 이 문제를 해결하기 위해 다중 단계 빌드를 사용할 수 있습니다. 최적화는 올바른 작업에 대해 올바른 베이스 이미지를 사용함으로써 달성됩니다:

- Python buster는 개발 의존성이 포함된 큰 이미지이며, 이를 사용하여 가상 환경을 설치할 것입니다.
- Python slim-buster는 Python을 실행하는 데 필요한 최소한의 종속성만 포함된 작은 이미지이며, 우리 애플리케이션을 실행하는 데 사용할 것입니다.

<div class="content-ad"></div>

다단계 빌드를 통해 한 단계에서 다른 단계로 정보를 전달할 수 있습니다. 특히 구축 중인 가상 환경입니다. 주목해 주세요:

- 실행 단계에는 심지어 Poetry가 설치되어 있지 않습니다. 사실 Python 응용 프로그램이 가상 환경이 구축된 후에 실행하는 데 불필요한 종속성이에요. 환경 변수 (예: VIRTUAL_ENV 변수)를 조정하여 Python이 올바른 가상 환경을 인식하도록 만들어주기만 하면 돼요.
- 간단하게 말씀드리면, 장난감 프로젝트에는 필요 없으므로 두 번째 설치 단계 (RUN poetry install --without dev)를 제거했어요. 하지만 실행 이미지에 여전히 한 번의 명령어로 추가할 수 있어요: RUN pip install poetry && poetry install --without dev && pip uninstall poetry.

Dockerfile이 복잡해지면 새로운 빌드 백엔드인 Buildkit을 사용하는 것도 제안드려요. 빠르고 안전한 빌드를 원한다면 그것을 사용하는 게 좋아요.

```js
DOCKER_BUILDKIT=1 docker build --target=runtime .
```

<div class="content-ad"></div>


# 빌더 이미지, 가상 환경을 구축하는 데 사용됨
FROM python:3.11-buster as builder

RUN pip install poetry==1.4.2

ENV POETRY_NO_INTERACTION=1 \
    POETRY_VIRTUALENVS_IN_PROJECT=1 \
    POETRY_VIRTUALENVS_CREATE=1 \
    POETRY_CACHE_DIR=/tmp/poetry_cache

WORKDIR /app

COPY pyproject.toml poetry.lock ./
RUN touch README.md

RUN poetry install --without dev --no-root && rm -rf $POETRY_CACHE_DIR

# 실행 이미지, 가상 환경만 실행하는 용도로 사용
FROM python:3.11-slim-buster as runtime

ENV VIRTUAL_ENV=/app/.venv \
    PATH="/app/.venv/bin:$PATH"

COPY --from=builder ${VIRTUAL_ENV} ${VIRTUAL_ENV}

COPY annapurna ./annapurna

ENTRYPOINT ["python", "-m", "annapurna.main"]


결과는? 런타임 이미지가 6배나 작아졌어요! 6배나! ` 1.1 GB에서 170 MB로 줄었어요.

# 6. Buildkit 캐시 마운트 ⛰

이미 작은 Docker 이미지와 코드 변경 시 빠른 빌드를 얻었는데, 더 얻을 수 있는 게 있을까요? 음… 의존성이 변경되었을 때도 빠른 빌드를 얻을 수 있어요 😎


<div class="content-ad"></div>

이 최종 팁은 다른 기능들과 비교했을 때 상대적으로 최근에 도입된 것이기 때문에 많은 사람들이 알지 못합니다. 이 기능은 Buildkit 캐시 마운트를 활용하며, 기본적으로 Buildkit에게 캐싱 목적으로 폴더를 마운트하고 관리하도록 지시합니다. 흥미로운 점은 이러한 캐시가 빌드 간에 지속될 것이라는 것입니다!

이 기능을 Poetry 캐시와 연결하면 (이젠 왜 캐싱을 유지하길 원했는지 알겠죠?) 사실상 빌드할 때마다 의존성 캐시를 얻게 됩니다. 동일한 환경에서 여러 번 이미지를 빌드할 때 의존성 빌드 단계가 빠르게 완료될 것입니다.

Poetry 캐시가 설치 후에 지워지지 않는 점에 유의해야 합니다. 이렇게 함으로써 캐시를 저장하고 빌드 간에 다시 사용할 수 있게 됩니다. 빌드된 이미지에 관리된 캐시를 영속화하지 않을 것이므로 (게다가 런타임 이미지도 아닙니다.) 이것은 괜찮은 접근입니다.

```js
FROM python:3.11-buster as builder

RUN pip install poetry==1.4.2

ENV POETRY_NO_INTERACTION=1 \
    POETRY_VIRTUALENVS_IN_PROJECT=1 \
    POETRY_VIRTUALENVS_CREATE=1 \
    POETRY_CACHE_DIR=/tmp/poetry_cache

WORKDIR /app

COPY pyproject.toml poetry.lock ./
RUN touch README.md

RUN --mount=type=cache,target=$POETRY_CACHE_DIR poetry install --without dev --no-root

FROM python:3.11-slim-buster as runtime

ENV VIRTUAL_ENV=/app/.venv \
    PATH="/app/.venv/bin:$PATH"

COPY --from=builder ${VIRTUAL_ENV} ${VIRTUAL_ENV}

COPY annapurna ./annapurna

ENTRYPOINT ["python", "-m", "annapurna.main"]
```

<div class="content-ad"></div>

이 최적화의 단점은 무엇일까요? 현재 Buildkit은 캐시 마운트가 매우 CI 친화적이지 않습니다. 왜냐하면 Buildkit은 캐시의 저장 위치를 제어할 수 없기 때문입니다. Buildkit 저장소에서 가장 투표를 많이 받은 오픈 GitHub 이슈라니 놀라운 일이네요 😄

# 요약

이미지를 몇 분 만에 1GB로 만드는 간단하지만 형편없는 Dockerfile을 최적화된 버전으로 변경하여 몇 초 만에 몇 백 MB의 이미지를 생성하는 방법을 살펴보았습니다. 모든 최적화는 주로 몇 가지 Docker 빌드 매니피스를 활용합니다:

- 계층을 작게 유지하여 복사하고 설치하는 항목의 양을 최소화합니다.
- Docker 레이어 캐싱을 활용하고 캐시 미스를 최대한 줄입니다.
- 느리게 변경되는 것(프로젝트 의존성)은 빠르게 변경되는 것(애플리케이션 코드)보다 먼저 빌드해야 합니다.
- Docker 다단계 빌드를 사용하여 런타임 이미지를 최대한 가볍게 만듭니다.

<div class="content-ad"></div>

파이썬 프로젝트를 Poetry로 관리할 때 이러한 원칙을 적용하는 방법을 안내드렸는데, 이와 유사한 원칙은 다른 종속성 관리자(PDM 등) 및 다른 언어에도 적용할 수 있습니다.

빌드가 빨라지고 용량이 작아지는 것을 보고 기쁨의 눈물을 흘리시기를 바랍니다. 추가로 알고 계신 Docker 관련 팁이 있으면 댓글에 남겨주세요! 👋