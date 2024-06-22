---
title: "Ollama - Langchain을 사용해 챗봇 만들기, 도커로 배포하기"
description: ""
coverImage: "/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_0.png"
date: 2024-06-19 12:53
ogImage: 
  url: /assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_0.png
tag: Tech
originalTitle: "Ollama — Build a ChatBot with Langchain, Ollama , Deploy on Docker"
link: "https://medium.com/@abvijaykumar/ollama-build-a-chatbot-with-langchain-ollama-deploy-on-docker-5dfcfd140363"
---


## 생성적 AI 시리즈

이 블로그는 생성적 AI에 관한 지속적인 시리즈이며 이전 블로그의 연장선입니다. 이 블로그 시리즈에서는 Ollama를 탐색하고 도커를 사용하여 분산 아키텍처에 배포할 수 있는 응용 프로그램을 구축할 것입니다.

Ollama는 강력한 언어 모델을 손쉽게 컴퓨터에서 실행할 수 있도록 도와주는 프레임워크입니다. Ollama 소개에 대해서는 2024년 2월에 A B Vijay Kumar에 의해 작성된 [Ollama — Brings runtime to serve LLMs everywhere. | by A B Vijay Kumar | Feb, 2024 | Medium](링크)를 참조해주세요. 이 블로그에서는 langchain 애플리케이션을 구축하고 도커에 배포할 것입니다.

## Ollama를 위한 Langchain 챗봇 애플리케이션

<div class="content-ad"></div>

Langshan을 사용하여 챗봇 애플리케이션을 개발해 보겠습니다. Python 애플리케이션에서 모델에 액세스하기 위해 간단한 Streamlit 챗봇 애플리케이션을 만들 것입니다. 이 Python 애플리케이션을 컨테이너에 배포하고 다른 컨테이너에서 Ollama를 사용할 것입니다. Docker-compose를 사용하여 인프라를 구축할 것입니다. 만약 Docker 또는 docker-compose를 사용하는 방법을 모르신다면, 계속 진행하기 전에 인터넷에서 몇 가지 자습서를 참고해 주세요.

아래 그림은 컨테이너 간 상호 작용과 접근하는 포트를 보여주는 아키텍처를 보여줍니다.

![아키텍처 그림](/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_0.png)

컨테이너를 2개 생성할 것입니다.

<div class="content-ad"></div>

- Ollama 컨테이너는 모델을 저장하고 로드하기 위해 호스트 볼륨을 사용합니다 (/root/.ollama는 로컬 ./data/ollama로 매핑됩니다). Ollama 컨테이너는 내부적으로 11434로 매핑된 외부 포트인 11434에서 수신 대기합니다.
- Streamlit 챗봇 애플리케이션은 내부적으로 8501로 매핑된 외부 포트인 8501에서 수신 대기합니다.
코딩을 시작하기 전에 Python 가상 환경을 설정하겠습니다.

```shell
python3 -m venv ./ollama-langchain-venv
source ./ollama-langchain-venv/bin/activate
```

다음은 streamlit 애플리케이션의 소스 코드입니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_1.png" />

제가 이전 블로그에서 작성한 것과 매우 유사한 소스 코드입니다. 이 코드가 어떻게 작동하는지에 대한 자세한 내용은 다른 블로그인 "Retrieval Augmented Generation(RAG) - LlamaIndex를 사용한 문서용 챗봇"을 참조하실 수 있어요. 주요 차이점은 Ollama를 사용하고 Ollama Langchain 라이브러리를 통해 모델을 호출한다는 점이에요 (이는 langchain_community의 일부입니다).

requirements.txt에서 종속성을 정의해 봅시다.

<img src="/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_2.png" />

<div class="content-ad"></div>

이제 Streamlit 애플리케이션의 도커 이미지를 빌드하기 위한 Dockerfile을 정의해 보겠습니다.

![image](/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_3.png)

우리는 베이스 이미지로 python 도커 이미지를 사용하고, /app이라는 작업 디렉토리를 생성합니다. 그런 다음 애플리케이션 파일을 해당 디렉토리로 복사하고, pip를 사용하여 모든 종속성을 설치합니다. 그 후에 포트 8501을 노출하고 Streamlit 애플리케이션을 시작합니다.

아래에 표시된 대로 docker build 명령을 사용하여 도커 이미지를 빌드할 수 있습니다.

<div class="content-ad"></div>

![docker_image_4](/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_4.png)

도커 이미지가 빌드되었는지 확인할 수 있어야 합니다. 다음에 표시된 것처럼 `docker images` 명령어를 사용하세요.

![docker_image_5](/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_5.png)

이제 스트림릿 애플리케이션 및 올라마 컨테이너의 네트워크를 정의하는 docker-compose 구성 파일을 만들어보겠습니다. 이렇게 하면 두 애플리케이션이 상호 작용할 수 있습니다. 또한 위의 그림에서 보듯이 다양한 포트 구성을 정의할 것입니다. 올라마에 대해서는 모델들이 영구적으로 유지되도록 볼륨 매핑도 수행할 것입니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_6.png" />

도커 컴포즈 업 명령을 실행하면 어플리케이션을 실행할 수 있습니다. 도커 컴포즈 업을 실행하면 아래 스크린샷에 표시된 대로 두 컨테이너가 모두 실행되고 있는 것을 확인할 수 있어야 합니다.

<img src="/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_7.png" />

아래 스크린샷에 표시된 대로 도커 컴포즈 ps 명령을 실행하여 컨테이너가 실행 중인 것을 확인할 수 있어야 합니다.

<div class="content-ad"></div>

![image1](/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_8.png)

올라마가 실행 중인지 확인하려면 아래 스크린샷에 표시된 대로 http://localhost:11434을 호출해야 합니다.

![image2](/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_9.png)

이제 아래에 표시된 대로 docker exec 명령을 사용하여 도커 컨테이너에 로그인하여 필요한 모델을 다운로드해 봅시다.

<div class="content-ad"></div>

```js
docker exec -it ollama-langchain-ollama-container-1 ollama run phi
```

우리는 모델 phi를 사용 중이므로 해당 모델을 가져와서 실행하여 테스트 중입니다. 아래 스크린샷을 참고하세요. phi 모델이 다운로드되고 실행을 시작할 것입니다 (-it 플래그를 사용하므로 샘플 프롬프트로 상호작용 및 테스트가 가능해집니다).

<img src="/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_10.png" />

로컬 폴더 ./data/ollama에서 다운로드된 모델 파일 및 매니페스트를 확인할 수 있어야 합니다. 이 폴더는 내부적으로 컨테이너에 매핑되어 있으며(Ollama가 다운로드된 모델을 제공하기 위해 찾는 위치인 /root/.ollama에 매핑됨)

<div class="content-ad"></div>

아래는 Markdown 형식으로 변경한 테이블입니다.


| `<img src="/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_11.png" />` |
|---|
| Lets now run access our streamlit application by opening [http://localhost:8501](http://localhost:8501) on the browser. The following screenshot shows the interface |

| `<img src="/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_12.png" />` |
|---|
| Lets try to run a prompt “generate a story about dog called bozo”. You shud be able to see the console logs reflecting the API calls, that are coming from our Streamlit application, as shown below |


<div class="content-ad"></div>

아래 스크린샷에서 확인할 수 있듯이, 내가 보낸 프롬프트에 대한 응답을 받았어요.

도커 컴포즈 다운을 호출하여 배포를 중지할 수 있어요.

<div class="content-ad"></div>

다음 스크린샷은 결과를 보여줍니다.

![output](/assets/img/2024-06-19-OllamaBuildaChatBotwithLangchainOllamaDeployonDocker_15.png)

여기 있습니다. 이 블로그를 준비하면서 랭체인과 함께 Ollama가 작동하고 Docker-Compose를 사용하여 Docker에 배포하는 것이 정말 즐거웠어요.

도움이 되었기를 바랍니다. 더 많은 실험으로 돌아오겠습니다. 그 동안 즐기고 코딩하세요!!! 곧 다시 만나요!!!

<div class="content-ad"></div>

여기서 전체 소스 코드에 액세스할 수 있습니다. abvijaykumar/ollama-langchain (github.com)

참고 자료

- Ollama
- Docker Compose 개요 | Docker 문서
- Docker 문서
- Ollama — LLM을 어디서나 제공하는 런타임을 제공합니다. | 작성자: A B Vijay Kumar | 2024년 2월 | Medium