---
title: "자신의 PC에서 대규모 언어 모델 실행하기 개인 정보 보호 및 그 이상"
description: ""
coverImage: "/assets/img/2024-05-20-RunningLargeLanguageModelsonyourPCprivacyandbeyond_0.png"
date: 2024-05-20 21:36
ogImage: 
  url: /assets/img/2024-05-20-RunningLargeLanguageModelsonyourPCprivacyandbeyond_0.png
tag: Tech
originalTitle: "Running Large Language Models on your PC: privacy and beyond"
link: "https://medium.com/@stansotirov-ai/running-large-language-models-on-your-pc-privacy-and-beyond-2d4afb9c2ea6"
---


![Running LLM on Your PC: Privacy and Beyond](/assets/img/2024-05-20-RunningLargeLanguageModelsonyourPCprivacyandbeyond_0.png)

이 첫 번째 기사를 통해, 인터넷 연결 없이 PC에 로컬로 내 LLM을 설치하고 설정하고 실행하는 여정을 문서화할 일련의 기사를 시작합니다.

내 PC에서 이 프로젝트를 구축하고 개인적으로 사용하기로 결정한 이유는 이 기사에서 확인할 수 있습니다.

Chat-GPT 3.5와 비슷하거나 심지어 동일한 기능을 가지려고 합니다. 이것은 이루기 어렵게 들릴 수 있지만, 기술은 매일 발전하고 있으며, 적절한 하드웨어, 소프트웨어 및 LLM의 조합을 통해 가능할 것으로 생각합니다. 이 과정에서 취하는 단계, 최적화 및 조정, 실수와 성공, 사용하는 소스, 그리고 향후 하드웨어 업그레이드에 대해 공유할 것입니다.

<div class="content-ad"></div>

이 여정이 끝날 때 모든 기사들은 사용자 안내서로 요약될 것이며, 저는 이를 무료로 발행하여 누구나 이 경험에서 혜택을 받고 직접 시도해볼 수 있도록 할 것입니다. 물론 이 과정에서 모든 의견, 제안 및 지침은 환영합니다.

이 여정이 시작되기 전에, 로컬 및 프라이빗 LLM을 위한 기반이 될 하드웨어와 소프트웨어인 나의 출발점을 공유할 것입니다.

나의 초기 컴퓨터 구성은 다음과 같습니다:

- CPU: AMD Ryzen 9 7950X3D, 16코어 @ 4.20 GHz
- RAM: Corsair VENGEANCE DDR5, 64GB (2 X 32 GB)
- HDD: 삼성 990 EVO NVMe™ M.2 SSD (1 TB 각 2개)
- 비디오: Nvidia GeForce RTX 4070, 12GB (평택 에디션)

<div class="content-ad"></div>

이 설정의 소프트웨어 구성 요소는 다음과 같습니다:

- Windows 11 Pro, ver. 23H2 (주 OS)
- Ubuntu 22.04 (Windows PowerShell 콘솔에 설치 및 실행됨)
- Ollama (LLM을 위해)
- Docker (컨테이너용)
- Python 3.11 (데이터 세트 및 일부 라이브러리 및 구성 요소 용)
- Visual Studio Code (필요할 때 코딩을 위해)

Windows PowerShell 콘솔에 Ubuntu를 설치해야 합니다. Windows에서 "시작" 메뉴를 열고 "실행"을 입력하고 새 창에서 "PowerShell"을 입력한 후 Enter 키를 누르십시오. 콘솔에서 다음 명령을 입력하십시오:

```js
wsl --install
```

<div class="content-ad"></div>

설치 프로세스를 시작하고, 완료되면 Linux용 사용자 이름과 비밀번호를 입력하라는 메시지가 표시됩니다. 성공적으로 설치되면 다음과 같은 화면을 볼 수 있습니다:

![이미지](/assets/img/2024-05-20-RunningLargeLanguageModelsonyourPCprivacyandbeyond_1.png)

설치가 끝나면 Ubuntu 배포판을 업데이트하고 업그레이드하는 것과 같은 좋은 실천 동작들을 수행해야 합니다. 다음 명령어를 사용하여 이를 실행할 수 있습니다(사용자 이름과 비밀번호가 요청될 것입니다):

```bash
sudo apt update
sudo apt upgrade -y
```

<div class="content-ad"></div>

이제 여러 추가 패키지가 설치됩니다. 그리고 이로써 귀하의 로컬 PC에 Ubuntu를 설치하는 작업이 완료되었습니다.

다음은 Ollama입니다. 이를 통해 LLMs를 설치하고 실행할 수 있습니다.

참고: Ollama를 설치하기 전에 이미 Linux 환경에 있는지 확인하세요. Ubuntu를 시작하려면 다음 명령을 입력하십시오:

```js
wsl -d Ubuntu
```

<div class="content-ad"></div>

설치는 콘솔에서 다시 실행되며 다음 명령어로 실행할 수 있습니다:

```js
curl -fsSL https://ollama.com/install.sh | sh
```

성공적으로 설치되면 이 화면이 표시되어야 합니다. 여기서 중요한 점은 프로세스가 완료된 후 비디오 카드가 인식되어야 한다는 것입니다 (빨간색으로 표시됨). 

<img src="/assets/img/2024-05-20-RunningLargeLanguageModelsonyourPCprivacyandbeyond_2.png" />

<div class="content-ad"></div>

비디오 카드는 NVIDIA에서 제공하는 것이 좋습니다. 큰 언어 모델 작업에 있어서 결정적인 부분입니다. 머신에 설치된(또는 적합한) 카드가 없는 경우 모델은 CPU 전력을 사용하게 되어 작업이 훨씬 느리고 정확도가 떨어지게 됩니다.

Ollama를 설치한 후에는 하나 이상의 대형 언어 모델(LLM)을 설치해야 합니다. 저는 llama2:13b를 선택하여 Meta에서 제공하는 130억 파라미터 모델을 설치했습니다. 이 모델은 제 개인 GPT에서 사용하고 제 데이터로 훈련시킬 모델입니다. 모델 크기는 7.4GB이므로 제 12GB (RTX 4070 FE) 비디오 카드에 적합합니다.

![Running Large Language Models on your PC: Privacy and Beyond](/assets/img/2024-05-20-RunningLargeLanguageModelsonyourPCprivacyandbeyond_3.png)

설치는 콘솔과 리눅스 환경에서 다시 진행됩니다. 아래 명령어로 실행할 수 있습니다:

<div class="content-ad"></div>

```md
오레카 풀라마 2:13b

모델을 다운로드하고 하드 드라이브에 저장하는 프로세스가 시작됩니다. 프로세스를 시작하기 전에 충분한 여유 공간이 있는지 확인하세요. 공간이 부족한 경우,  llama:7b와 같은 더 작은 모델을 다운로드할 수도 있습니다. 이는 동일한 명령으로 수행할 수 있습니다: "llama:13b"를 "llama:7b"로 교체하면 됩니다. 7b 모델은 4.7GB이며, llama:13b 대신 사용할 수 있습니다.

성공적인 설치 후 다음 화면을 볼 수 있어야 합니다:

![image](/assets/img/2024-05-20-RunningLargeLanguageModelsonyourPCprivacyandbeyond_4.png)
```

<div class="content-ad"></div>

모델이 이미 하드 드라이브에 있으면 상호 작용을 시작할 수 있습니다. 다음 명령어로 콘솔에서 수행할 수 있어요:

```js
ollama run llama:13b
```

Enter 키를 누르면 다음 메시지가 표시됩니다:

 메시지를 전송하세요 (도움말을 보려면 /?)

<div class="content-ad"></div>

이제 모델이 준비되어 입력을 받을 준비가 되었습니다. 리눅스 환경에서 모델을 실험하고 탐색하여 기능을 느끼고 전파를 연습할 수 있습니다. 상호 작용은 다음과 같아야 합니다:

![image](/assets/img/2024-05-20-RunningLargeLanguageModelsonyourPCprivacyandbeyond_5.png)

이제 1단계인 리눅스 및 LLM 설치를 마치며 마무리합니다. 이제 인터넷 연결 없이도 로컬 PC에서 LLM을 실행하고 사용할 수 있습니다.

다음 글에서는 컨테이너를 위해 Docker를 설치하고 모델을 위한 웹 인터페이스를 설치하고 모델을 훈련 및 최적화하는 데 도움이 되는 기타 Python 라이브러리와 도구를 검토할 것입니다.

<div class="content-ad"></div>

다음에 또 봐요!

Stan