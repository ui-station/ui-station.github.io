---
title: "Docker를 사용하여 GPU에서 Ollama를 로컬로 실행하는 방법"
description: ""
coverImage: "/assets/img/2024-06-30-HowtorunOllamalocallyonGPUwithDocker_0.png"
date: 2024-06-30 22:20
ogImage: 
  url: /assets/img/2024-06-30-HowtorunOllamalocallyonGPUwithDocker_0.png
tag: Tech
originalTitle: "How to run Ollama locally on GPU with Docker"
link: "https://medium.com/@srpillai/how-to-run-ollama-locally-on-gpu-with-docker-a1ebabe451e0"
---


대형 언어 모델(LLMs)을 사용해보고 싶지만 토큰, 구독료 또는 API 키를 지불하고 싶지 않으신가요? 자신의 노트북(또는 전용 하드웨어)에서 실행하고 Gen AI의 강력함을 누리고 싶으신가요? 그렇다면 Ollama를 확인해보세요. 이 블로그에서는 다음을 안내해 드리겠습니다.

- 도커를 사용하여 빠르게 노트북(Windows 또는 Mac)에 Ollama를 설치하는 방법
- Ollama WebUI를 실행하고 Gen AI 플레이그라운드를 즐기는 방법
- 더 빠른 추론을 위해 노트북의 Nvidia GPU 활용
- Ollama를 사용하여 Python Streamlit Gen AI 응용프로그램 빌드하는 방법

# 사전 요구 사항

본 안내서를 통해 Ollama를 로컬에서 실행하려면 다음이 필요합니다.

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

- 도커 및 도커-컴포즈 또는 도커 데스크톱을 사용해주세요.
- NVIDIA GPU — GPU를 사용할 경우에는 그래픽 카드를 사용하고, 그렇지 않을 경우 노트북의 CPU를 사용합니다.
- Python 버전 3이 필요합니다.
- Ollama를 실행하기 위해 충분한 디스크 공간이 필요합니다. 모델 파일은 적어도 10GB의 여유 공간이 필요하지만, 이것만으로는 충분하지 않습니다. 총 디스크 공간의 20%를 여유롭게 남겨 두어야 합니다. 그렇지 않으면 모델 파일에 충분한 공간이 있더라도 Ollama를 시작할 때 문제가 발생할 수 있습니다.
- Docker 데스크톱을 구성할 때, Docker에 충분한 양의 CPU 및 메모리를 제공해주세요.

# Ollama 설치 방법

Ollama를 로컬에 실행하려면 다음 저장소를 클론하고 아래와 같이 docker-compose를 사용하여 실행해주세요,

```js
git clone git@github.com:sujithrpillai/ollama.git
cd ollama/ollama
docker-compose up -d
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

만약 docker-compose.yml 파일을 보면, 로컬 폴더인 models가 /root/.ollama/models로 매핑되어 있는 것을 알 수 있습니다. 이 폴더는 다운로드된 모델을 저장하는 곳입니다. 따라서 컨테이너를 다시 배포해도 모델 파일을 다시 다운로드할 필요가 없습니다.

또한 genai-network라는 도커 브릿지 네트워크를 사용하고 있음을 알 수 있습니다. 이는 컨테이너 간 연결성에 매우 중요합니다. 나중에 GenAI 애플리케이션을 실행할 때 Ollama 컨테이너의 DNS 이름을 프로그램에서 사용할 것입니다.

# 모델 파일 다운로드

Ollama는 다양한 LLM 모델을 지원하고 있으며 목록은 계속 늘어나고 있습니다. 현재 제공되는 모델을 확인하려면 여기를 확인하세요: https://ollama.com/library. 모델 파일 크기는 다음을 확인할 수 있습니다: https://github.com/ollama/ollama?tab=readme-ov-file#model-library

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

우리는 Gen AI 애플리케이션에 사용할 텍스트 모델 llama3과 임베딩 모델 all-minilm을 불러올 것입니다.

```js
docker-compose exec -it ollama bash
ollama pull llama3
ollama pull all-minilm
```

다운로드가 완료되면 단순히 exit를 입력하여 컨테이너 셸을 빠져나오세요.

노트북의 models 폴더로 이동하면 LLM 모델을 위한 파일 세트가 생성된 것을 확인할 수 있습니다. curl http://localhost:11434 로 애플리케이션이 작동 중인지 확인해보세요. 아래와 같이 Ollama가 실행되고 있는 것을 보여줘야 합니다.

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

<img src="/assets/img/2024-06-30-HowtorunOllamalocallyonGPUwithDocker_0.png" />

Ollama의 웹 사용자 인터페이스에 액세스하려면 http://localhost:3000 으로 이동하십시오. 처음에는 가짜 이메일 및 비밀번호로 가입해야 합니다. 그런 다음 콘솔에 로그인하십시오. 드롭다운에서 GenAI 모델을 선택하고 다양한 프롬프트로 테스트할 수 있는 플레이그라운드에서 사용할 수 있습니다.

<img src="/assets/img/2024-06-30-HowtorunOllamalocallyonGPUwithDocker_1.png" />

# 추론을 위한 GPU 사용

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

노트북의 GPU를 사용하여 추론하려면 docker-compose.yml 파일을 약간 수정할 수 있습니다. 도커에서 GPU를 사용하는 자세한 정보는 이 설명서를 참조하세요. 해야 할 일은 docker-compose.yml 파일에서 ollama 서비스를 수정하는 것뿐입니다. 아래와 같이 수정하세요.

```js
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: all
              capabilities: [gpu]
```

제가 제공한 docker-compose.yml 파일에서 이러한 줄 (11번째 줄부터 17번째 줄)은 주석 처리되어 있습니다. 활성화하려면 주석 처리를 해제하세요.

컨테이너 내에서 ollama ps 명령을 실행하여 차이를 확인할 수 있습니다.

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

Mac M1 Pro에서는 GPU가 없습니다:


![GPU 이미지](/assets/img/2024-06-30-HowtorunOllamalocallyonGPUwithDocker_2.png)


Windows에서 Nvidia GPU를 사용하는 경우:


![GPU 이미지](/assets/img/2024-06-30-HowtorunOllamalocallyonGPUwithDocker_3.png)


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

# Gen AI RAG 애플리케이션

간단한 GenAI RAG 애플리케이션을 사용하여 텍스트 모델(llama3)과 임베딩 모델(all-minilm)을 모두 사용하여 애플리케이션을 구축해 봅시다.

이전 단계에서 설정한 Ollama가 LLM 모델을 다운로드하여 실행 중인지 확인하세요. 리포지토리의 앱 폴더로 이동하고 docker-compose up -d 명령을 실행하여 실행하세요.

이렇게 함으로써 RAG를 사용하는 매우 작은 Gen AI 애플리케이션이 실행됩니다. 이 애플리케이션에는 PDF 파일을 업로드할 수 있는 UI 요소가 제공됩니다. 파일을 업로드한 후 애플리케이션은 파일을 텍스트로 변환하고 벡터화하여 In-memory FAISS 벡터 데이터베이스에 저장합니다. 그런 다음 텍스트 입력 UI 요소를 사용하여 LLM에 질문을 하게 됩니다. 질문은 벡터 데이터베이스에서 유사성 검색을 수행하는 데 사용됩니다. 질문, 검색 결과 및 컨텍스트가 LLM에 전달되어 의미 있는 답변을 생성합니다. 그런 다음 UI에 답변이 표시됩니다.

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

테스트 목적으로 이상한 데이터가 들어 있는 샘플 imaginary_world.pdf를 제공했어요. RAG가 정말 작동되는지 확인할 수 있어요.

애플리케이션 코드에서 LLM이 정의되는 방식을 알아볼 수 있어요. 우리는 Langchain을 사용해서 이 작업을 수행했어요 (간단한 표현을 위해 변수들을 제거했기 때문에 아래의 파이썬 파일에 작성된 방식과 차이가 있을 수 있어요.)

```js
from langchain_community.llms import Ollama
llm = Ollama(base_url= http://ollama:11434, model=llama3)
from langchain_community.embeddings import OllamaEmbeddings
embeddings = OllamaEmbeddings(base_url= http://ollama:11434, model=all-minilm)
```

다른 방법으로도 llm을 직접 가져와서 사용하는 방법이 있어요. 아래에 그 방법을 보여드릴게요.

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
from ollama import Client
ollama_client = Client(host='http://ollama:11434')
user_input = “Your question goes here”
llm_response = ollama_client.chat(
     model=llama3, messages=[{'role': 'user','content': user_input,},]
)
```

애플리케이션은 http://localhost:5000에서 액세스할 수 있습니다. 아래는 애플리케이션에서의 샘플 응답입니다,

<img src="/assets/img/2024-06-30-HowtorunOllamalocallyonGPUwithDocker_4.png" />

다양한 프롬프트로 플레이해보세요.

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

# 참고 자료

이 내용을 작성하는 동안 사용한 참고 자료는 다음과 같습니다.

- Ollama 웹사이트 — https://ollama.com/
- Ollama Open WebUI — https://github.com/open-webui/open-webui
- Ollama Github 저장소 — https://github.com/ollama/ollama
- Streamlit — https://streamlit.io/
- 도커 데스크톱 GPU 지원 — docs.docker.com/desktop/gpu/

자신만의 환경을 구축하여 Gen AI 프로젝트를 테스트할 수 있는 방법을 간단히 살펴봤을 거예요. 댓글로 의견을 공유해주세요. 즐거운 코딩 되세요!