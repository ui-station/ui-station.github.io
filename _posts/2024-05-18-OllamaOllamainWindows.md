---
title: "윈도우에서의 오마이 , 오마이"
description: ""
coverImage: "/assets/img/2024-05-18-OllamaOllamainWindows_0.png"
date: 2024-05-18 17:46
ogImage: 
  url: /assets/img/2024-05-18-OllamaOllamainWindows_0.png
tag: Tech
originalTitle: "Ollama , Ollama in Windows"
link: "https://medium.com/@danushidk507/ollama-ollama-in-windows-384899e054e4"
---


Ollama는 주로 대형 언어 모델 (LLM)과 작업하는 데 사용되는 프레임워크 및 라이브러리를 가리킵니다.

로컬에서 LLM 실행을 위한 프레임워크: Ollama는 Llama 2, Mistral, Gemma와 같은 대형 언어 모델을 쉽게 로컬 컴퓨터에서 실행할 수 있는 가벼우면서 확장 가능한 프레임워크입니다. 이는 LLM과 실험하고자 하는 개발자나 제어 환경에서 그들의 동작을 연구하고자 하는 연구자들에게 유용할 수 있습니다.

## Ollama란?

Ollama는 로컬 사용을 위한 오픈 소스 모델을 획득하도록 도와줍니다. 최적의 소스에서 모델을 자동으로 가져오며, 컴퓨터에 전용 GPU가 있는 경우 수동 구성이 필요하지 않고 GPU 가속을 자동으로 사용합니다. 프롬프트를 수정함으로써 모델을 쉽게 사용자 정의할 수 있으며 이를 위해 Langchain은 필수가 아닙니다. 또한 Ollama는 Docker 이미지로 액세스할 수 있어 사용자 지정 모델을 Docker 컨테이너로 배포할 수 있습니다.

![이미지](/assets/img/2024-05-18-OllamaOllamainWindows_0.png)

<div class="content-ad"></div>

## Framework

Ollama은 여러 오픈 소스 LLM을 사용자의 컴퓨터에서 간편하게 설정하고 실행할 수 있는 가벼운 방법을 제공합니다. 이를 통해 복잡한 설정이나 외부 서버에 의존하지 않아도 되므로 다양한 목적에 적합합니다:

- 개발: 클라우드에 배포할 필요 없이 개발자가 LLM 프로젝트를 빠르게 실험하고 반복할 수 있습니다.
- 연구: 연구자들은 Ollama를 사용하여 제어된 환경에서 LLM의 행동을 연구하고 심층적인 분석을 용이하게 할 수 있습니다.
- 개인 정보 보호: 로컬에서 LLM을 실행하면 데이터가 사용자의 기기를 벗어나지 않아므로 중요한 정보에 대한 보호가 보장됩니다.

Ollama 프레임워크:

<div class="content-ad"></div>

- 간단한 설정: Ollama는 복잡한 구성 파일이나 배포의 필요성을 제거합니다. 모델 파일은 모델 가중치, 구성 및 데이터와 같은 필요한 구성 요소를 정의하여 설정 프로세스를 간소화합니다.
- 사용자 정의: Ollama를 사용하면 LLM 경험을 사용자 정의할 수 있습니다. 배치 크기, 시퀀스 길이 및 빔 검색 설정과 같은 매개변수를 조정하여 모델을 사용자의 특정 요구에 맞게 세밀하게 조정할 수 있습니다.
- Multi-GPU 지원: Ollama는 기계에서 여러 GPU를 활용하여 성능이 중요한 작업에 대해 더 빠른 추론 및 향상된 성능을 제공합니다.
- 확장 가능한 아키텍처: 프레임워크는 모듈식 및 확장 가능하도록 설계되었습니다. 사용자 지정 모듈을 쉽게 통합하거나 기능을 확장하기 위해 커뮤니티에서 개발된 플러그인을 탐색할 수 있습니다.

## 라이브러리:

Ollama에는 다음과 같은 사전 빌드된 훈련된 언어 모델 라이브러리가 포함되어 있습니다:

- Llama 2: 텍스트 생성, 번역, 질문에 응답과 같은 다양한 작업을 수행할 수 있는 대규모 언어 모델입니다.
- Mistral: 방대한 텍스트와 코드 데이터셋에서 훈련된 사실 기반 언어 모델입니다.
- Gemma: 매력적인 대화를 위해 설계된 대화형 언어 모델입니다.
- LLaVA: 채팅 및 설명 사용 사례에 대해 훈련된 견고한 모델입니다.

<div class="content-ad"></div>

이 라이브러리를 사용하면 미리 훈련된 모델을 쉽게 응용 프로그램에 통합할 수 있어서 처음부터 훈련시킬 필요가 없어 시간과 리소스를 절약할 수 있어요.

# Ollama의 기능

사용 편의성:

- 간편한 설치: Ollama은 복잡한 구성을 없애는 미리 정의된 "모델 파일"을 활용하여 설치와 설정을 제공됩니다. 기술적 지식이 제한된 사용자도 쉽게 사용할 수 있어요.
- 사용자 친화적 API: Ollama은 직관적인 API를 통해 미리 훈련된 모델과 상호 작용하여 개발자가 Python 응용 프로그램에 쉽게 LLMs를 통합할 수 있어요.

<div class="content-ad"></div>

확장성:

- 사용자 정의 가능한 모델: Ollama는 다양한 매개변수의 조절을 허용하여 사용자가 특정 작업과 선호도에 맞게 모델을 세밀하게 조정할 수 있도록 합니다.
- 모듈화된 아키텍처: 이 프레임워크는 사용자 정의 모듈과 커뮤니티에서 개발한 플러그인을 지원하여 개별적인 요구에 맞춰 확장성과 사용자 정의를 촉진합니다.

강력한 기능:

- 사전 훈련된 모델: Ollama는 텍스트 생성, 번역, 질문 응답 및 코드 생성과 같은 다양한 작업을 수행할 수 있는 사전 훈련된 LLM 라이브러리를 제공합니다.
- 로컬 실행: LLM은 완전히 사용자의 장치에서 실행되어 클라우드 배포가 필요 없으며 데이터 개인 정보 보호를 보장합니다.
- 멀티-GPU 지원: Ollama는 여러 GPU를 활용하여 추론 속도를 높이고 자원 집약적인 작업에서 성능을 향상시킵니다.

<div class="content-ad"></div>

오픈소스 및 협업:

- 무료 제공: Ollama의 오픈소스 성격은 누구나 기여하고 커뮤니티 기반 개선을 받아들일 수 있습니다.
- 지속적인 발전: Ollama는 적극적으로 유지보수되며 지속적인 업데이트와 향상이 정기적으로 출시됩니다.

추가 기능:

- 가벼움: Ollama는 효율적으로 작동하여 하드웨어 자원이 제한된 컴퓨터에 적합합니다.
- 오프라인 기능: 사전 훈련된 모델은 인터넷 연결 없이도 사용할 수 있어 유연성과 접근성을 제공합니다.

<div class="content-ad"></div>

# 윈도우에서의 Ollama:

![Ollama 이미지](/assets/img/2024-05-18-OllamaOllamainWindows_1.png)

Ollama가 미리보기로 Windows에 제공되어 이제 새로운 네이티브 Windows 경험에서 대형 언어 모델을 끌어모으고 실행하고 생성하는 것이 가능해졌습니다. Windows에서의 Ollama에는 내장 GPU 가속, 전체 모델 라이브러리 접근 및 OpenAI 호환을 포함한 Ollama API가 포함되어 있습니다.

# 하드웨어 가속

<div class="content-ad"></div>

Ollama은 NVIDIA GPU 및 AVX 및 AVX2와 같은 현대 CPU 명령어 세트를 사용하여 모델을 가속화합니다. 구성이나 가상화 설정이 필요하지 않습니다!

![image](/assets/img/2024-05-18-OllamaOllamainWindows_2.png)

![image](/assets/img/2024-05-18-OllamaOllamainWindows_3.png)

![image](/assets/img/2024-05-18-OllamaOllamainWindows_4.png)

<div class="content-ad"></div>

# 모델 라이브러리의 전체 액세스

Windows에서 실행할 수 있는 전체 Ollama 모델 라이브러리를 사용할 수 있습니다. LLaVA 1.6과 같은 비전 모델을 실행할 때는, 이미지를 Ollama 실행창으로 끌어다 놓아 메시지에 추가할 수 있습니다.

![이미지1](/assets/img/2024-05-18-OllamaOllamainWindows_5.png)

![이미지2](/assets/img/2024-05-18-OllamaOllamainWindows_6.png)

<div class="content-ad"></div>

```markdown
![이미지](/assets/img/2024-05-18-OllamaOllamainWindows_7.png)

# 항상 켜져 있는 Ollama API

Ollama의 API는 자동으로 백그라운드에서 실행되며 http://localhost:11434에서 제공됩니다. 도구 및 응용 프로그램은 추가 설정 없이 연결할 수 있습니다.

![이미지](/assets/img/2024-05-18-OllamaOllamainWindows_8.png)
```

<div class="content-ad"></div>

예를 들어, PowerShell을 사용하여 Ollama의 API를 호출하는 방법은 다음과 같습니다:

```js
(Invoke-WebRequest -method POST -Body '{"model":"llama2", "prompt":"Why is the sky blue?", "stream": false}' -uri http://localhost:11434/api/generate ).Content | ConvertFrom-json
```

Windows에서 Ollama는 다른 플랫폼과 동일한 OpenAI 호환성을 지원하며, Ollama를 통해 로컬 모델을 사용하여 OpenAI용으로 작성된 기존 툴을 사용할 수 있도록 합니다.

Windows 미리보기에서 Ollama를 시작하려면:

<div class="content-ad"></div>

- Windows 용 Ollama 다운로드
- OllamaSetup.exe를 더블 클릭하여 설치 프로그램을 실행하십시오.
- 설치 후 좋아하는 터미널을 열고 ollama run llama2를 실행하여 모델을 실행하십시오.

새 버전이 출시되면 Ollama가 업데이트를 알려줍니다.

ollama serve:

이 명령은 Ollama 서버를 시작하여 다운로드한 모델을 API를 통해 접근할 수 있게 합니다. 이를 통해 웹 브라우저, 모바일 앱 또는 사용자 지정 스크립트와 같은 다양한 응용 프로그램에서 모델과 상호 작용할 수 있습니다.

<div class="content-ad"></div>

한가지 비유를 들어 볼게요. Ollama는 당신의 책(LMMs)을 보관하는 도서관 역할을 합니다. ollama serve를 실행하면 도서관을 열어 다른 사람들이 해당 도서관 시스템(API)을 통해 책에 접근하여 읽을(상호 작용할) 수 있게 됩니다.

![이미지](/assets/img/2024-05-18-OllamaOllamainWindows_9.png)

ollama run phi:

이 명령어는 특히 "phi" 모델을 다운로드하고 로컬 머신에서 실행하는 데 사용됩니다. "phi"란 Ollama 도서관에 존재하는 사전 학습된 LMM으로, GPT-3와 유사한 기능을 갖추고 있습니다.

<div class="content-ad"></div>

아래에 있는 것이 유사성 확장입니다: 
만약 ollama serve가 도서관을 열면, ollama run phi는(이하 phi)이라는 특정 책을 (Ollama)사서로부터 요청하고(다운로드된 모델 필요) 그것을 읽는(모델 실행) 것이 로컬 머신(당신의 컴퓨터) 내에서 일어나는 일 같습니다.

- ollama serve는 미리 다운로드된 모델이 요구됩니다. 특정 모델을 다운로드하려면 ollama pull `model_name`을 사용하세요.
- ollama run phi는 “phi”모델을 특정하게 다운로드하고 실행합니다.
- ollama serve는 API를 통해 다운로드된 모델에 접근할 수 있게 해주는데 반해, ollama run phi는 로컬에서 특정 모델을 실행하는 데 중점을 둡니다.

일반 명령어:

- ollama list: 시스템에 다운로드된 모든 모델을 나열합니다.
- ollama rm `model_name`: 시스템에서 다운로드된 모델을 제거합니다.
- ollama cp `model_name1` `model_name2`: 다운로드된 모델을 새 이름으로 복사합니다.
- ollama info `model_name`: 다운로드된 모델에 대한 정보를 표시합니다.
- ollama help: 사용 가능한 모든 명령어에 대한 도움말 문서를 제공합니다.

<div class="content-ad"></div>

모델 관리:

- `ollama pull model_name`: Ollama 모델 허브에서 모델을 다운로드합니다.

모델 실행:

- `ollama run model_name`: 로컬에서 다운로드한 모델을 실행합니다.
- `ollama serve`: Ollama 서버를 시작하여 API를 통해 다운로드한 모델에 액세스할 수 있게 합니다.

<div class="content-ad"></div>

추가 명령:

- ollama update: Ollama를 최신 버전으로 업데이트합니다.
- ollama config: Ollama 구성 설정을 관리합니다.

```markdown
<img src="/assets/img/2024-05-18-OllamaOllamainWindows_10.png" />
```

# Langchain을 통한 Ollama:

<div class="content-ad"></div>

```python
from langchain_community.llms import Ollama

llm = Ollama(model="llama2")

llm.invoke("Tell me a joke")
```

```python
"물론이죠! 여기 빠른 하나 있어요:\n\n과학자들이 원자를 믿지 않는 이유는 뭘까요?\n왜냐하면 그들이 모든 것으로 이루어져 있으니까요!\n\n당신의 얼굴에 미소를 띄우길 바랍니다!"
```

토큰을 스트리밍하려면 .stream(...) 메서드를 사용하세요:

```python
query = "Tell me a joke"

for chunks in llm.stream(query):
    print(chunks)
```

<div class="content-ad"></div>

```js
확실해요, 여기 하나 있어요:

왜 과학자들이 원자를 믿지 않을까요?
왜냐하면 그들이 모든 것을 이루기 때문이에요!

저렇게 즐겁게 느껴지셨으면 좋겠어요! 더 듣고 싶나요?
```

# Multi-modal

Ollama는 bakllava와 llava와 같은 멀티 모달 LLM을 지원합니다.

ollama pull bakllava

<div class="content-ad"></div>

Ollama를 업데이트하여 멀티 모달을 지원하는 최신 버전을 사용해보세요.

```js
from langchain_community.llms import Ollama

bakllava = Ollama(model="bakllava")
```

```js
import base64
from io import BytesIO

from IPython.display import HTML, display
from PIL import Image


def convert_to_base64(pil_image):
    """
    PIL 이미지를 Base64로 인코딩된 문자열로 변환

    :param pil_image: PIL 이미지
    :return: 리사이즈된 Base64 문자열
    """

    buffered = BytesIO()
    pil_image.save(buffered, format="JPEG")  # 필요시 형식 변경 가능
    img_str = base64.b64encode(buffered.getvalue()).decode("utf-8")
    return img_str


def plt_img_base64(img_base64):
    """
    Base64로 인코드된 문자열을 이미지로 표시

    :param img_base64:  Base64 문자열
    """
    # Base64 문자열을 소스로 사용하는 HTML img 태그 생성
    image_html = f'<img src="data:image/jpeg;base64,{img_base64}" />'
    # HTML을 렌더링하여 이미지 표시
    display(HTML(image_html))


file_path = "../../../static/img/ollama_example_img.jpg"
pil_image = Image.open(file_path)
image_b64 = convert_to_base64(pil_image)
plt_img_base64(image_b64)
```

<img src="/assets/img/2024-05-18-OllamaOllamainWindows_11.png" />

<div class="content-ad"></div>

```js
llm_with_image_context = bakllava.bind(images=[image_b64])
llm_with_image_context.invoke("달러 기반 총 유지율은 얼마입니까:")
```

```js
'90%'
```