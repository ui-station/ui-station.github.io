---
title: "라즈베리 파이에서 로컬 LLMs 및 VLMs 실행하기"
description: ""
coverImage: "/assets/img/2024-05-17-RunningLocalLLMsandVLMsontheRaspberryPi_0.png"
date: 2024-05-17 19:32
ogImage:
  url: /assets/img/2024-05-17-RunningLocalLLMsandVLMsontheRaspberryPi_0.png
tag: Tech
originalTitle: "Running Local LLMs and VLMs on the Raspberry Pi"
link: "https://medium.com/towards-data-science/running-local-llms-and-vlms-on-the-raspberry-pi-57bd0059c41a"
---

## Phi-2, Mistral, 그리고 LLaVA와 같은 모델을 Raspberry Pi에서 Ollama를 사용하여 로컬에서 실행하기

![이미지](/assets/img/2024-05-17-RunningLocalLLMsandVLMsontheRaspberryPi_0.png)

자신의 기기에서 큰 언어 모델(LLMs)이나 시각 언어 모델(VLMs)을 실행해 보고 싶은 적이 있었나요? 아마 그렇겠죠. 그러나 모든 것을 처음부터 설정하고 환경을 관리하고 적절한 모델 가중치를 다운로드해야 한다는 생각, 그리고 기기가 해당 모델을 처리할 수 있는지에 대한 미심쩍음 때문에 조금 망설이셨을 겁니다.

그보다 한 발 더 나아가보죠. 크기가 신용카드보다 작은 기기인 Raspberry Pi에서 자체 LLM 또는 VLM을 운영하고 있다고 상상해보세요. 불가능한 것일까요? 전혀 그렇지 않아요. 내가 이 게시물을 작성하고 있으니까 가능한 일이죠.

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

## 가능하다면, 그렇게 해도 괜찮아요. 하지만 왜 그걸 하고 싶으세요?

현재에는 가장자리의 LLM이 매우 비현실적으로 보입니다. 하지만 특정한 이러한 예외적인 사용 사례는 시간이 지나면 성숙해질 것이며, 기기에서 실행되는 올 로컬 생성형 AI 솔루션을 이용해 멋진 가장자리 솔루션이 구축될 것입니다.

이것은 무엇이 가능한지 알아보고자 하는 것입니다. 만약 이것이 컴퓨팅 규모의 극단단에 가능하다면, 라즈베리 파이와 강력한 서버 GPU 사이의 어느 수준에서든 가능할 것입니다.

전통적으로, 가장자리 AI는 컴퓨터 비전과 밀접하게 관련되어 왔습니다. LLMs와 VLMs의 배포를 탐색함으로써 가장자리에 이러한 새로운 면을 추가하면 이 분야에서 떠오르고 있는 흥미로운 측면을 발견할 수 있습니다.

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

가장 중요한 것은, 최근에 얻은 Raspberry Pi 5에서 뭔가 재미있는 것을 하고 싶었습니다.

그래서, Raspberry Pi에서 이 모든 것을 어떻게 구현할 수 있을까요? Ollama를 사용해서!

## Ollama가 무엇인가요?

Ollama는 처음부터 설정하기 귀찮은 일 없이 개인 컴퓨터에서 로컬 LLM을 실행하는 데 최적의 솔루션이 되어 나타났습니다. 몇 가지 명령어로 모든 것을 문제없이 설정할 수 있습니다. 모든 것이 독립적으로 구성되어 있으며, 몇 가지 기기 및 모델에서의 제 경험에 따르면 훌륭하게 작동합니다. Raspberry Pi에서 실행시켜 놓고 원한다면 다른 응용프로그램 및 기기에서 호출할 수 있는 모델 추론을 위한 REST API까지 노출합니다.

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

<img src="/assets/img/2024-05-17-RunningLocalLLMsandVLMsontheRaspberryPi_1.png" />

또한, 명령 줄 인터페이스에 대해 걱정하는 사람들을 위해 웅장한 AI UI/UX인 Ollama 웹 UI도 있습니다. 이것은 바로 지금 기본적으로 로컬 ChatGPT 인터페이스입니다, 만약 당신이 원한다면요.

이 두 오픈 소스 소프트웨어는 현재 최상의 로컬 호스팅 LLM 경험을 제공한다고 생각합니다.

Ollama 및 Ollama 웹 UI는 LLaVA와 같은 VLM도 지원하며, 이는 엣지 생성적 AI 사용 사례에 대한 더 많은 가능성을 엽니다.

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

## 기술 요구 사항

다음만 있으면 됩니다:

- Raspberry Pi 5 (속도가 느린 설정을 위해 4 버전 사용 가능) — 7B 모델에 맞게 8GB RAM 모델을 선택하세요.
- SD 카드 — 최소한 16GB, 크기가 클수록 더 많은 모델을 넣을 수 있습니다. Raspbian Bookworm이나 Ubuntu와 같은 적합한 OS가 이미 로드되어 있어야 합니다.
- 인터넷 연결

이전에 언급했듯이, Raspberry Pi에서 Ollama를 실행하는 것은 이미 하드웨어 스펙트럼의 극단에 가깝습니다. 기본적으로 Raspberry Pi보다 강력한 장치는 Linux 배포판을 실행하고 유사한 메모리 용량을 가지고 있다면, 이론적으로는 Ollama와 이 포스트에서 논의된 모델을 실행할 수 있어야 합니다.

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

## 1. Ollama 설치하기

Raspberry Pi에 Ollama를 설치하기 위해서는 리소스를 절약하기 위해 Docker를 사용하지 않을 것입니다.

터미널에서 다음 명령을 실행하세요.

```js
curl https://ollama.ai/install.sh | sh
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

위의 명령을 실행한 후 아래 이미지와 유사한 내용을 보게 될 것입니다.

![image](/assets/img/2024-05-17-RunningLocalLLMsandVLMsontheRaspberryPi_2.png)

출력에 따라서 Ollama가 실행 중인지 확인하려면 0.0.0.0:11434로 이동하십시오. 'WARNING: No NVIDIA GPU detected. Ollama will run in CPU-only mode.'라는 메시지가 표시되는 것은 Raspberry Pi를 사용하고 있기 때문에 정상입니다. 그러나 NVIDIA GPU가 있는 것으로 알려진 장치에서 이 지침을 따르고 있다면, 무언가 잘못되었습니다.

문제 또는 업데이트가 있는 경우 Ollama GitHub 리포지토리를 참조하십시오.

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

## 2. 명령 줄을 통한 LLM 실행

Ollama 공식 모델 라이브러리에서 Ollama를 사용하여 실행할 수 있는 모델 목록을 살펴보세요. 8GB 라즈베리 파이에서 7B보다 큰 모델은 맞지 않습니다. 마이크로소프트의 2.7B 크기인 Phi-2 LLM을 MIT 라이센스로 사용하겠습니다.

기본 Phi-2 모델을 사용할 것이지만, 여기에서 찾을 수 있는 다른 태그 중에서 필요한 것을 자유롭게 사용해도 됩니다. Phi-2 모델 페이지를 확인하여 상호 작용하는 방법을 살펴보세요.

터미널에서 다음을 실행하세요:

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
올라마 편리해요
```

아래 출력물과 유사한 것을 볼 때, 이미 라즈베리 파이에서 LLM이 작동 중임을 확인했습니다! 정말 간단해요.

![이미지](/assets/img/2024-05-17-RunningLocalLLMsandVLMsontheRaspberryPi_3.png)

![이미지](/assets/img/2024-05-17-RunningLocalLLMsandVLMsontheRaspberryPi_4.png)

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

기타 모델인 Mistral, Llama-2 등도 시도해 볼 수 있어요. 다만 모델 가중치를 저장할만큼 SD 카드에 충분한 공간이 있어야 해요.

모델이 클수록 출력이 느려질 수 있어요. 예를 들어, Phi-2 2.7B에서는 초당 약 4토큰을 얻을 수 있어요. 하지만 Mistral 7B를 사용하면 세대 속도가 초당 약 2토큰으로 느려집니다. 토큰은 대략 한 단어와 동일한 양이에요.

![이미지](/assets/img/2024-05-17-RunningLocalLLMsandVLMsontheRaspberryPi_5.png)

이제 Raspberry Pi에서 LLMs가 실행되고 있지만 아직 끝나지 않았어요. 터미널이 모두에게 익숙한 것은 아니에요. Ollama 웹 UI도 실행해 보겠어요!

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

## 3. Ollama 웹 UI 설치 및 실행

공식 Ollama 웹 UI GitHub 저장소의 지침을 따라 Docker 없이 설치할 것입니다. 최소한 Node.js 버전이 `= 20.10 이 되어야 한다고 권장하므로, 해당 권장사항을 따를 것입니다. 또한 Python은 적어도 3.11 이어야 한다고 권장하나, Raspbian OS에는 이미 그 버전이 설치되어 있습니다.

먼저 Node.js를 설치해야 합니다. 터미널에서 다음을 실행하세요.

```js
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash - &&\
sudo apt-get install -y nodejs
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

만약 향후 독자들을 위해 20.x를 더 적합한 버전으로 변경하려면 변경하십시오.

그런 다음 아래 코드 블록을 실행하십시오.

```js
git clone https://github.com/ollama-webui/ollama-webui.git
cd ollama-webui/

# 필요한 .env 파일 복사
cp -RPp example.env .env

# Node를 사용하여 프론트엔드 빌드
npm i
npm run build

# 백엔드와 함께 프론트엔드 제공
cd ./backend
pip install -r requirements.txt -- break-system-packages
sh start.sh
```

이는 GitHub에서 제공된 것을 약간 수정한 것입니다. 가상 환경을 사용하거나 --break-system-packages 플래그를 사용하는 등의 최선의 방법을 따르지 않고 간단하게하고자 하였으니 유의하시기 바랍니다. uvicorn을 찾을 수 없다는 오류가 발생하면 터미널 세션을 재시작하십시오.

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

모든 것이 제대로 진행되면 라즈베리 파이의 8080 포트를 통해 http://0.0.0.0:8080로 Ollama 웹 UI에 액세스할 수 있거나, 동일한 네트워크에서 다른 장치를 통해 접속하고 있다면 http://라즈베리 파이의 로컬 주소:8080/을 통해 접속할 수 있습니다.

![이미지 설명](/assets/img/2024-05-17-RunningLocalLLMsandVLMsontheRaspberryPi_6.png)

계정을 생성하고 로그인한 후, 아래 이미지와 유사한 화면이 보여야 합니다.

![이미지 설명](/assets/img/2024-05-17-RunningLocalLLMsandVLMsontheRaspberryPi_7.png)

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

모델 가중치를 이전에 다운로드했다면, 아래와 같이 드롭다운 메뉴에 표시될 것입니다. 그렇지 않은 경우 설정으로 이동하여 모델을 다운로드할 수 있습니다.

![이미지 1](/assets/img/2024-05-17-RunningLocalLLMsandVLMsontheRaspberryPi_8.png)

![이미지 2](/assets/img/2024-05-17-RunningLocalLLMsandVLMsontheRaspberryPi_9.png)

전체적으로 사용자 인터페이스는 매우 깔끔하고 직관적이므로 설명할 것이 별로 없어요. 정말로 아주 훌륭하게 구현된 오픈 소스 프로젝트입니다.

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

![image](/assets/img/2024-05-17-RunningLocalLLMsandVLMsontheRaspberryPi_10.png)

## 4. Ollama 웹 UI를 통해 VLM 실행하기

이 글을 시작할 때 언급했듯이, VLM도 실행할 수 있습니다. LLaVA를 실행해보겠습니다. 이것은 Ollama에서도 지원되는 인기 있는 오픈 소스 VLM입니다. 실행하려면 인터페이스를 통해 'llava'를 가져와서 가중치를 다운로드하면 됩니다.

안타깝게도, LLM과는 달리 Raspberry Pi에서 이미지를 해석하는 설정에 꽤 많은 시간이 걸립니다. 아래 예시는 처리하는 데 약 6분 정도 걸렸습니다. 대부분의 시간은 아마 이미지 측면이 아직 제대로 최적화되지 않았기 때문인데, 이는 앞으로 확실히 변경될 것입니다. 토큰 생성 속도는 초당 약 2토큰입니다.

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

![image](/assets/img/2024-05-17-RunningLocalLLMsandVLMsontheRaspberryPi_11.png)

## 모든 것을 감싸며

지금까지 이 기사의 목표를 거의 달성한 상태입니다. 요약하자면, 우리는 Ollama와 Ollama Web UI를 사용하여 Raspberry Pi에서 Phi-2, Mistral, LLaVA와 같은 LLMs와 VLMs를 실행하는 방법을 성공적으로 이끌어냈습니다.

Raspberry Pi나 다른 작고 가장한 장치에서 실행되는 로컬 호스팅 LLMs에 대한 다양한 사용 사례들을 상상할 수 있습니다. 특히, 4토큰/초가 Phi-2 크기의 모델을 위해 일부 사용 사례에 대해 스트리밍 속도로는 합당해 보입니다.

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

'작은' LLM 및 VLM의 분야는 얼마 전에 다소 모순적으로 이름이 지어졌지만 '큰' 지칭을 받는 활발한 연구 분야입니다. 최근에는 꽤 많은 모델이 출시되었습니다. 이 새로운 트렌드가 계속되어 더 효율적이고 간결한 모델들이 계속 출시되기를 바랍니다! 다가오는 몇 달 동안 살펴봐야 할 분야입니다.

면책 성명: 저는 Ollama나 Ollama Web UI와 어떠한 관련도 없습니다. 모든 의견과 견해는 제 개인적인 것이며 어떤 조직도 대표하지 않습니다.
