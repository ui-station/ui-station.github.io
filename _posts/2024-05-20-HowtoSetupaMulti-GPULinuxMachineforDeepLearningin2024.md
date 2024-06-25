---
title: "2024년을 위한 딥 러닝을 위한 멀티 GPU 리눅스 머신 설정하기"
description: ""
coverImage: "/assets/img/2024-05-20-HowtoSetupaMulti-GPULinuxMachineforDeepLearningin2024_0.png"
date: 2024-05-20 17:53
ogImage:
  url: /assets/img/2024-05-20-HowtoSetupaMulti-GPULinuxMachineforDeepLearningin2024_0.png
tag: Tech
originalTitle: "How to Setup a Multi-GPU Linux Machine for Deep Learning in 2024"
link: "https://medium.com/towards-data-science/how-to-setup-a-multi-gpu-linux-machine-for-deep-learning-in-2024-df561a2d3328"
---

## 다중 GPU로 딥 러닝

![image](/assets/img/2024-05-20-HowtoSetupaMulti-GPULinuxMachineforDeepLearningin2024_0.png)

딥 러닝 모델(특히 LLMs)이 점점 커지면, GPU 메모리(VRAM)가 더 많이 필요해집니다. 로컬에서 이들을 개발하고 사용하기 위해 다중 GPU 머신을 구축하거나 획득하는 것은 첫 번째 도전일 뿐입니다. 대부분의 라이브러리와 애플리케이션은 기본적으로 단일 GPU만 사용합니다. 따라서 머신에는 다중 GPU 설정을 활용할 수 있는 적절한 드라이버와 라이브러리가 필요합니다.

본 기사는 중요한 라이브러리를 갖춘 Nvidia 다중 GPU(Linux) 머신을 설정하는 방법에 대한 가이드를 제공합니다. 이를 통해 실험에 소요되는 시간을 아낄 수 있고 개발을 시작하는 데 도움이 될 것입니다.

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

마지막으로, 딥러닝을 위한 멀티 GPU 설정을 활용할 수 있는 인기 있는 오픈 소스 라이브러리로의 링크가 제공됩니다.

## 대상

딥러닝을 시작하기 위해 CUDA Toolkit, PyTorch 및 Miniconda를 설치하여 exllamaV2 및 torchtune과 같은 프레임워크를 사용할 것입니다.

©️ 이 이야기에서 언급된 모든 라이브러리 및 정보는 오픈 소스이거나 공개적으로 사용 가능합니다.

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

## 시작하기

![그림](/assets/img/2024-05-20-HowtoSetupaMulti-GPULinuxMachineforDeepLearningin2024_1.png)

터미널에서 nvidia-smi 명령어를 사용하여 기계에 설치된 GPU 수를 확인하십시오. 설치된 GPU의 목록이 출력되어야 합니다. 만약 명령어가 작동하지 않거나 불일치가 있는 경우 먼저 해당 Linux 버전용 Nvidia 드라이버를 설치하십시오. nvidia-smi 명령어가 기계에 설치된 모든 GPU 목록을 출력하도록 확인하십시오.

이 페이지를 따라 설치하지 않은 경우 Nvidia 드라이버를 설치하십시오:

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

우분투 22.04에 NVIDIA 드라이버를 설치하는 방법 - Linux 자습서 - Linux 구성 학습 - (출처: linuxconfig.org)

## 단계 1 CUDA-Toolkit 설치하기

💡 usr/local/cuda-xx 경로에 기존 CUDA 폴더가 있는지 확인하세요. 해당 폴더가 있다면 CUDA의 버전이 이미 설치된 것입니다. 원하는 CUDA 툴킷이 이미 설치되어 있는 경우 (터미널에서 nvcc 명령으로 확인하실 수 있습니다.) 다음 단계인 단계 2로 이동해주세요.

PyTorch 라이브러리용 필요한 CUDA 버전을 확인하세요: 로컬에서 시작하기 | PyTorch (저희는 CUDA 12.1을 설치하고 있습니다)

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

CUDA Toolkit 12.1 Downloads | NVIDIA Developer 에 방문하여 CUDA 12.1을 설치하기 위한 Linux 명령어를 얻을 수 있어요 (OS 버전과 해당하는 “deb (local)” 설치 프로그램 유형을 선택).

![이미지](/assets/img/2024-05-20-HowtoSetupaMulti-GPULinuxMachineforDeepLearningin2024_2.png)

선택한 옵션에 따라 기본 설치 프로그램의 터미널 명령어가 나타날 거에요. CUDA 툴킷을 설치하려면 해당 명령어를 Linux 터미널에서 복사하여 붙여넣기하여 실행하세요. 예를 들어, x86_64 Ubuntu 22 용으로 다음 명령어를 실행하려면 다운로드 폴더에서 터미널을 열고 다음 명령어를 실행하세요:

```js
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin
sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.1.0/local_installers/cuda-repo-ubuntu2204-12-1-local_12.1.0-530.30.02-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2204-12-1-local_12.1.0-530.30.02-1_amd64.deb
sudo cp /var/cuda-repo-ubuntu2204-12-1-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda
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

⚠️ CUDA 툴킷을 설치하는 동안, 설치 프로그램이 커널 업데이트를 요구할 수 있습니다. 터미널에서 커널 업데이트를 요청하는 팝업이 나타나면 esc 버튼을 눌러 취소하세요. 이 단계에서는 커널을 업데이트하지 마세요! — 이렇게 하면 Nvidia 드라이버가 손상될 수 있습니다 ☠️.

설치 후 리눅스 머신을 다시 시작하세요. nvcc 명령이 여전히 작동하지 않을 것입니다. CUDA 설치를 PATH에 추가해야 합니다. 나노 편집기를 사용하여 .bashrc 파일을 엽니다.

```js
nano /home/$USER/.bashrc
```

.bashrc 파일 맨 아래로 스크롤하여 다음 두 줄을 추가하세요:

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
export PATH="/usr/local/cuda-12.1/bin:$PATH"
export LD_LIBRARY_PATH="/usr/local/cuda-12.1/lib64:$LD_LIBRARY_PATH"
```

💡 참고로 설치된 CUDA 버전에 맞게 cuda-12.1을 필요에 따라 cuda-xx로 변경할 수 있습니다. 여기서 'xx'는 CUDA 버전을 나타냅니다.

변경 사항을 저장하고 nano 편집기를 닫으려면:

```js
변경 사항 저장 - 키보드에서 다음을 누르세요:

ctrl + o             --> 저장
엔터 또는 리턴 키     --> 변경 사항 수락
ctrl + x             --> 편집기 닫기
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

터미널을 닫고 다시 열어주세요. 그러면 nvcc --version 명령을 실행하면 설치된 CUDA 버전이 터미널에 표시될 겁니다.

## 단계 2 - Miniconda 설치

PyTorch를 설치하기 전에 Miniconda를 설치하고, 그 안에 PyTorch를 설치하는 것이 좋습니다. 또한 각 프로젝트마다 새로운 Conda 환경을 만드는 것이 편리합니다.

Downloads 폴더에서 터미널을 열고 다음 명령을 실행하세요.

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

```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh
```

# initiate conda

```bash
~/miniconda3/bin/conda init bash
~/miniconda3/bin/conda init zsh
```

터미널을 닫고 다시 열어주세요. 이제 conda 명령어를 사용할 수 있어요.

## 단계-3 PyTorch 설치하기

(선택 사항) — 프로젝트용 새로운 conda 환경을 생성하세요. `environment-name`을 원하는 이름으로 바꿀 수 있어요. 일반적으로 제 프로젝트 이름으로 지정해요. 💡 프로젝트 작업 전후에 conda activate `environment-name` 및 conda deactivate `environment-name` 명령어를 사용할 수 있어요.

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
conda create -n <environment-name> python=3.11

# 환경 활성화
conda activate <environment-name>
```

CUDA 버전에 맞게 PyTorch 라이브러리를 설치하세요. 아래 명령어는 우리가 cuda-12.1에 설치한 것입니다:

```js
pip3 install torch torchvision torchaudio
```

위 명령어는 PyTorch 설치 가이드 — 로컬에서 시작 | PyTorch 에서 확인할 수 있습니다.

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

![PyTorch Installation](/assets/img/2024-05-20-HowtoSetupaMulti-GPULinuxMachineforDeepLearningin2024_3.png)

PyTorch을 설치한 후 터미널에서 PyTorch에서 볼 수 있는 GPU의 수를 확인하세요.

```python
python

>> import torch
>> print(torch.cuda.device_count())
8
```

이 명령은 시스템에 설치된 GPU의 수를 출력해야 합니다 (내 경우엔 8개), 그리고 nvidia-smi 명령에 나열된 GPU 수와 일치해야 합니다.

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

바로 시작해도 괜찮아요! 여러 개의 GPU를 활용하는 딥 러닝 프로젝트에 대해 작업할 준비가 되었어요 🥳.

# 다음 단계는? Multi-GPU 설정을 활용한 딥 러닝 프로젝트로 시작해보세요 (LLMs)

1. 🤗 시작하려면 Hugging Face에서 인기 있는 모델을 복제해보세요:

2. 💬 추론에 대한 설치 (LLM 모델 사용)를 위해 exllamav2를 별도의 환경에 복제하고 설치하세요. 이렇게 하면 모든 GPU가 사용되어 더 빠른 추론이 가능합니다: (자세한 튜토리얼은 제 미디엄 페이지를 확인하세요)

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

3. 👨‍🏫 파인 튜닝이나 훈련을 위해 torchtune을 복제하고 설치할 수 있습니다. 지침을 따라 모델을 전체 미세 조정하거나 Lora 미세 조정할 수 있습니다. GPU를 최대한 활용하세요: (자세한 튜토리얼은 내 미디엄 페이지를 확인해주세요)

# 결론

이 안내서는 다중 GPU 딥 러닝에 필요한 기계 설정을 안내해줍니다. 이제 torchtune과 같은 다중 GPU를 활용하는 모든 프로젝트에 대해 작업을 시작할 수 있습니다. 빠른 개발을 위한 torchtune과 exllamaV2의 자세한 튜토리얼을 기대해주세요.
