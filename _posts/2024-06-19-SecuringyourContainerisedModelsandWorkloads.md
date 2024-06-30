---
title: "컨테이너화된 모델과 작업 보안하기"
description: ""
coverImage: "/assets/img/2024-06-19-SecuringyourContainerisedModelsandWorkloads_0.png"
date: 2024-06-19 12:37
ogImage:
  url: /assets/img/2024-06-19-SecuringyourContainerisedModelsandWorkloads_0.png
tag: Tech
originalTitle: "Securing your Containerised Models and Workloads"
link: "https://medium.com/towards-data-science/securing-your-containerised-models-and-workloads-3bff4d90a07b"
---

컨테이너화는 이제 많은 어플리케이션을 배포하는 주요 수단이 되었으며, Docker가 이를 주도하며 보급되고 있습니다. 그 인기에 따라 공격 위험이 증가하고 있습니다. 따라서 Docker 어플리케이션을 안전하게 지킬 필요가 있습니다. 이를 위한 가장 기본적인 방법은 컨테이너 내 사용자를 루트 사용자가 아닌 일반 사용자로 설정하는 것입니다.

```js
컨텐츠
========

왜 루트 사용자가 아닌 사용자를 사용해야 하는가?

기본 일반 사용자로서 할 수 있는 일과 할 수 없는 일

네 가지 시나리오
  1) 호스트에서 모델 제공 (읽기 전용)
  2) 데이터 처리 파이프라인 실행 (컨테이너 내에서 쓰기)
  3) 라이브러리가 자동으로 파일 작성 (컨테이너 내에서 쓰기)
  4) 훈련된 모델 저장 (호스트에 쓰기)

요약
```

# 왜 루트 사용자가 아닌 사용자를 사용해야 하는가?

혹은 왜 루트 사용자를 사용하지 말아야 하는가? 아래의 가짜 아키텍처 예제를 살펴봅시다.

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

![Containerized Security](/assets/img/2024-06-19-SecuringyourContainerisedModelsandWorkloads_0.png)

보안은 종종 다층 접근법으로 간주됩니다. 공격자가 컨테이너에 들어갈 경우 사용자로서 가지는 권한이 첫 번째 방어층이 됩니다. 만약 컨테이너 사용자가 루트 액세스를 할당받는다면, 공격자는 컨테이너 내 모든 것을 자유롭게 제어할 수 있습니다. 이러한 넓은 액세스로 인해 잠재적인 취약점을 이용하여 호스트로 탈출하고 모든 연결된 시스템에 완전한 액세스를 획들할 수도 있습니다. 그 결과는 심각하며 다음과 같습니다:

- 저장된 비밀 정보를 회수
- 트래픽을 가로채거나 방해
- 암호화 채굴과 같은 악성 서비스 실행
- 데이터베이스와 같은 연결된 민감한 서비스에 액세스 획득

![Containerized Security](/assets/img/2024-06-19-SecuringyourContainerisedModelsandWorkloads_1.png)

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

와우, 그건 정말 무섭게 들리네요! 그러나 해결 방법은 간단합니다. 컨테이너를 루트 사용자가 아닌 다른 사용자로 변경하세요!

우리가 나머지 기사를 읽기 전에, 리눅스 권한과 액세스 권한에 대한 좋은 이해가 없다면, 제 이전 기사를 꼭 확인해 보세요 [2].

# 기본 비루트 사용자로서 할 수 있고 할 수 없는 것

기본 비루트 사용자로 간단한 도커 어플리케이션을 만들어 보겠습니다. 아래의 도커 파일을 사용하세요.

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
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

# create a dummy py file
RUN echo "print('I can run an existing py file')" > example.py

# create & switch to non-root user
RUN adduser --no-create-home nonroot
USER nonroot
```

Make sure to build the image and create a container using the following commands:

```js
docker build -t test .
docker run -it test bash
```

Once you are inside the container, feel free to try out various commands. Keep in mind that certain actions like writing to restricted directories or installing software may not be permitted due to restricted permissions.

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

<img src="/assets/img/2024-06-19-SecuringyourContainerisedModelsandWorkloads_2.png" />

반대로, 우리는 모든 종류의 읽기 권한을 실행할 수 있습니다.

<img src="/assets/img/2024-06-19-SecuringyourContainerisedModelsandWorkloads_3.png" />

파이썬이 설치되어 있기 때문에 약간 독특합니다. ls -l $(which python)을 실행하면 파이썬 인터프리터에 완전한 권한이 있음을 볼 수 있습니다. 따라서 Dockerfile에서 처음에 만든 example.py 파일과 같은 기존의 파이썬 파일을 실행할 수 있습니다. 심지어 파이썬 콘솔에 들어가 간단한 명령을 실행할 수도 있습니다. 그러나 비 루트 사용자로 전환하면 다른 시스템 쓰기 권한이 제거된 것을 알 수 있습니다. 그렇기 때문에 스크립트를 생성하거나 수정하거나 파이썬을 사용해 쓰기 명령을 실행할 수 없음을 확인할 수 있습니다.

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

![image](/assets/img/2024-06-19-SecuringyourContainerisedModelsandWorkloads_4.png)

시스템 전반적인 제한은 보안에 좋지만, 특정 파일 및 디렉터리에 대한 쓰기 권한이 필요한 경우가 많이 발생하며, 그러한 허용 사항에 대응해야 합니다.

다음 섹션에서는 기계 학습 운영 수명 주기의 네 가지 시나리오 예제를 제공합니다. 이러한 예제를 통해 대부분의 다른 경우에 대한 구현 방법을 이해할 수 있을 것입니다.

# 네 가지 시나리오

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

## 1) 호스트에서 모델 제공하기 — 읽기 전용

모델을 제공할 때, 추론 및 서빙 스크립트를 활용하여 모델을 로드하고 API를 통해 노출시킵니다 (예: Flask, FastAPI) 입력을 받도록 합니다. 때로는 모델이 호스트 머신에서 로드되어 이미지와 분리되어 이미지 크기가 최적으로 작고, 이미지를 다시 로드할 경우 반복적인 모델 다운로드 없이 최적으로 빠르게 할 수 있도록 합니다. 그런 다음 모델은 바인드-마운트 볼륨을 통해 컨테이너로 전달되어 로드되고 제공됩니다.

<img src="/assets/img/2024-06-19-SecuringyourContainerisedModelsandWorkloads_5.png" />

이것은 비루한 사용자를 구현하는 가장 번거롭지 않은 방법일 것입니다. 기본적으로 모든 사용자에게 부여되는 읽기 권한만 필요하기 때문입니다. 아래는 그 작업이 어떻게 이루어지는지를 보여주는 샘플 Dockerfile입니다.

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

# Dockerfile

FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip3 install --no-cache-dir --upgrade pip~=23.2.1 \
 && pip3 install --no-cache-dir -r requirements.txt

COPY ./project/ /app

# add non-root user ---------------------

RUN adduser --no-create-home nonroot

# switch from root to non-root user -----

USER nonroot

CMD ["python", "inference.py"]

이 Dockerfile은 먼저 nonroot라는 새로운 시스템 사용자를 만드는 두 가지 간단한 명령어를 가지고 있습니다. 두 번째로, 마지막 CMD 라인 바로 전에 루트에서 nonroot 사용자로 전환됩니다. 기본 non-root 사용자의 경우 쓰기 및 실행 권한이 없기 때문에, 이전 단계에서 필요한 파일을 설치하거나 복사하거나 조작할 수 없습니다.

이제 Docker에서 non-root 사용자를 할당하는 방법을 알았으니, 다음 단계로 넘어가 봅시다.

## 2) 데이터 처리 파이프라인 실행하기 — 컨테이너 내에서 작성

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

가끔은 작업을 실행하기 위해 일시적인 파일을 저장하고 싶을 때가 있습니다. 예를 들어, 데이터 전처리 작업을 한다고 가정해봅시다. 파일을 추가하고 삭제하는 작업으로 이루어져 있죠. 파일이 영구적이지 않기 때문에 이런 작업은 컨테이너 내에서 수행할 수 있습니다.

![이미지](/assets/img/2024-06-19-SecuringyourContainerisedModelsandWorkloads_6.png)

그러나 루트가 아닌 사용자를 사용한다면 쓰기 권한이 필요할 것입니다. 이를 위해 chown(소유자 변경) 명령을 사용하여 쓰기 액세스가 필요한 특정 폴더에 소유권을 할당해야 합니다. 이 작업을 완료하면 사용자를 루트가 아닌 사용자로 전환할 수 있습니다.

```js
# Dockerfile

# ....

# 루트가 아닌 사용자 추가 및 처리 폴더에 소유권 부여
RUN adduser --no-create-home nonroot && \
    mkdir processing && \
    chown nonroot processing

# 루트에서 루트가 아닌 사용자로 전환
USER nonroot

CMD ["python", "preprocess.py"]
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

## 3) 라이브러리가 파일을 자동으로 작성하는 경우 - 컨테이너 내에서 작성

이전 예시에서는 우리가 직접 만든 파일을 작성하는 방법을 보여줬어요. 그러나 사용하는 라이브러리가 파일과 디렉토리를 자동으로 만드는 경우가 흔합니다. 컨테이너를 실행해 보고 쓰기 권한이 거부되는 것을 알 수 있을 때 그것들이 만들어진 것임을 알게 될 거예요.

저는 두 가지 예시를 보여드릴 거에요. 하나는 여러 프로세스를 관리하는 데 사용되는 supervisor에서 가져왔고, 다른 하나는 huggingface에서 모델을 다운로드할 때 사용하는 huggingface-hub에서 가져왔어요. 이러한 권한 오류들은 우리가 루트가 아닌 사용자로 전환할 때 볼 수 있을 거에요.

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

![Securing your Containerised Models and Workloads](/assets/img/2024-06-19-SecuringyourContainerisedModelsandWorkloads_8.png)

![Securing your Containerised Models and Workloads](/assets/img/2024-06-19-SecuringyourContainerisedModelsandWorkloads_9.png)

두 개의 슈퍼바이저 파일에 대해서 먼저 빈 파일로 생성하고 소유권 권한을 할당할 수 있습니다. Huggingface-hub 다운로드 문제에 대해 이미 오류 로그에서 TRANSFORMERS_CACHE 변수를 통해 다운로드 디렉토리를 변경할 수 있다는 힌트가 있었습니다. 따라서 먼저 디렉토리 변수를 할당하고, 디렉토리를 생성한 후 소유권을 할당할 수 있습니다.

```js
# Dockerfile

# ....

# non-root 사용자 추가 ................
# huggingface 다운로드 디렉토리 변경
ENV TRANSFORMERS_CACHE=/app/model

RUN adduser --no-create-home nonroot && \
    # 슈퍼바이저 파일 및 huggingfacehub 디렉토리 생성
    touch /app/supervisord.log /app/supervisord.pid && \
    mkdir $TRANSFORMERS_CACHE && \
    # 슈퍼바이저 및 huggingfacehub 쓰기 권한 부여
    chown nonroot /app/supervisord.log && \
    chown nonroot /app/supervisord.pid && \
    chown nonroot $TRANSFORMERS_CACHE
USER nonroot

CMD ["supervisord", "-c", "conf/supervisord.conf"]
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

물론 여기에 제시된 것과 약간 다른 다른 예제가 있을 수 있습니다만, 쓰기 권한을 최소화하는 개념은 동일할 것입니다.

## 4) 훈련된 모델 저장하기 — 호스트에 쓰기

모델을 훈련하는 데 컨테이너를 사용하고 그 모델을 호스트에 쓰기를 원한다고 가정해 봅시다. 예를 들어, 모델을 준비하여 다른 작업에서 평가하거나 배포하기 위해 호스트에 쓰려고 하는 경우입니다. 이 경우에는 모델 파일을 쓰기 위해 컨테이너 디렉토리를 호스트 디렉토리에 연결하여 모델 파일을 기록해야 합니다. 이를 바인드 마운트라고도 합니다.

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

먼저, 우리는 nonroot를 위한 그룹과 사용자를 만들어야 합니다. 각각에 대해 고유한 ID를 지정하는데, 이 경우에 우리는 1001을 사용합니다 (1000 이상의 아무 숫자나 상관없습니다). 그런 다음, 모델을 저장할 모델 디렉토리를 생성합니다.

Scenario 2와 비교하여 여기서의 차이점은 모델 디렉토리에 대해 쓰기 권한을 설정하는 데 chown이 필요하지 않다는 것입니다. 왜냐하면?

```js
# Dockerfile

# ....
# add non-root group/user & create model folder
ENV UID=1001
RUN addgroup --gid $UID nonroot && \
    adduser --uid $UID --gid $UID --no-create-home nonroot && \
    mkdir model

# switch from root to non-root user
USER nonroot

CMD ["python", "train.py"]
```

이는 bind-mounted 디렉토리의 권한이 호스트 디렉토리에서 결정되기 때문입니다. 따라서 우리는 호스트에서 다시 동일한 사용자를 만들어야 하며, 사용자 ID가 동일한지 확인해야 합니다. 그런 다음에 호스트에 모델 디렉토리를 만들고 nonroot 사용자에게 소유자 권한을 부여합니다.

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
# 호스트 터미널에서

# 동일한 사용자 및 그룹 추가
addgroup --gid 1001
adduser --uid 1001 --gid 1001 --no-create-home nonroot
# 바인드 마운트할 모델 디렉토리 만들고 nonroot를 소유자로 설정
mkdir /home/model
chown nonroot /home/model
```

바인드 마운트는 보다 유연성을 제공하기 위해 일반적으로 docker-compose.yml 파일이나 docker run 명령어에서 지정됩니다. 아래는 전자의 예시입니다.

```js
version: "3.5"

services:
    modeltraining:
        container_name: modeltraining
        build:
            dockerfile: Dockerfile
        volumes:
            - type: bind
              source: /home/model # 호스트 디렉토리
              target: /app/model  # 컨테이너 디렉토리
```

그리고 후자에 대한 예시는 다음과 같습니다:

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
docker run -d --name modeltraining -v /home/model:/app/model <image_name>
```

아무거나 실행하시면, 비루트 사용자로 스크립트를 실행할 수 있음을 확인하실 수 있을 거예요.

# 요약

우리는 비루트 사용자를 할당하고도 컨테이너가 원하는 작업을 수행할 수 있는 방법을 살펴보았어요. 이는 특정 쓰기 권한이 필요할 때 주로 관련이 있어요. 그저 두 가지 기본 개념만 알면 돼요.

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

- 컨테이너에서의 쓰기 권한을 위해서는 Dockerfile에서 chown을 사용하세요.
- 바인드 마운트를 위한 쓰기 권한은 호스트에서 동일한 비루트 사용자를 생성하고 호스트 디렉토리에서 chown을 사용하세요.

루트 사용자로 일부 테스트를 실행하기 위해 도커 컨테이너로 들어가야할 때 다음 명령어를 사용할 수 있어요.

```js
docker exec -it -u 0 <컨테이너_아이디/이름> bash
```

# 참고자료

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

- [1] Wong et al. (2023) 컨테이너 보안에 관한: 위협 모델링, 공격 분석 및 완화 전략. 컴퓨터 및 보안, 제 128권.
- [2] Linux 권한 및 접근 권한에 관한 이전 게시물: https://medium.com/@teosiyang/securing-linux-servers-with-two-commands-de5b565dc104
