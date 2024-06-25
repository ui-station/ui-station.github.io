---
title: "ML 이야기 MobileLlama3 모바일에서 Llama3를 로컬에서 실행하기"
description: ""
coverImage: "/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_0.png"
date: 2024-05-18 20:07
ogImage:
  url: /assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_0.png
tag: Tech
originalTitle: "[ML Story] MobileLlama3: Run Llama3 locally on mobile"
link: "https://medium.com/@tiwarinitin1999/ml-story-mobilellama3-run-llama3-locally-on-mobile-36182fed3889"
---

# 소개

2024년 4월, Meta가 새로운 오픈 언어 모델 패밀리인 Llama 3을 출시했습니다. 이전 모델을 발전시킨 Llama 3은 개선된 기능을 제공하며, 8B 및 70B의 사전 훈련된 버전과 명령어 튜닝된 변형을 제공합니다.

언어 모델의 지속적인 트렌드에서, 개발자들은 개인 정보 보호를 위해 API 대신 로컬 또는 오프라인 사용을 선호하고 있습니다. 올라마는 macOS 및 Linux OS에서 오프라인으로 LLMs를 실행할 수 있는 도구 중 하나로, 로컬 실행을 가능하게 합니다. 그러나 스마트폰의 제한된 하드웨어 성능으로 인해 모바일 기기에서 LLMs를 로컬로 실행하는 기능은 아직 제한적입니다.

하지만 이제는 다릅니다. MLC 덕분에 모바일 기기에서 이러한 대형 모델을 실행하는 것이 가능해졌습니다. 이 블로그는 MLC LLM을 사용하여 오프라인 추론을 위해 Llama3-8B-Instruction 모델을 모바일 폰에 직접 양자화, 변환 및 배포하는 완전한 튜토리얼을 제공합니다.

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

<img src="/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_0.png" />

시작하기 전에, 먼저 파이프라인을 이해해 봅시다.

<img src="/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_1.png" />

자, 더 이상 지체하지 말고, 단계별 구현을 위한 코드로 바로 넘어가 보겠습니다.

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

## 섹션 I: 원본 라마-3-8B-인스트럭트 모델을 MLC 호환 가중치로 양자화 및 변환하기

단계 0: 아래 저장소를 로컬 머신에 복제하고 Google Colab에 Llama3_on_Mobile.ipynb 노트북을 업로드하세요.

```js
# 저장소를 복제합니다.
git clone https://github.com/NSTiwari/Llama3-on-Mobile
```

단계 1: MLC-LLM 설치하기

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

모델 가중치를 변환하려면 MLC-LLM 라이브러리가 필요합니다. 노트북을 실행하는 데 특히 NumPy 버전 1.23.5가 필요하며, 다른 버전에서 변환 프로세스에 문제가 발생했습니다.

```js
!pip install --pre --force-reinstall mlc-ai-nightly-cu122 mlc-llm-nightly-cu122 -f https://mlc.ai/wheels
!pip install numpy==1.23.5
```

단계 2: 라이브러리 가져오기

```js
import mlc_llm
import torch
from huggingface_hub import snapshot_download
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

Step 3: HF 계정에 로그인하고 원본 Llama-3-8B-Instruct 모델 가중치를 다운로드하세요

```js
# HF 계정에 로그인합니다.
from huggingface_hub import notebook_login
notebook_login()

# Llama-3-8B-Instruct 모델을 다운로드합니다.
snapshot_download(repo_id="meta-llama/Meta-Llama-3-8B-Instruct", local_dir="/content/Llama-3-8B-Instruct/")
```

Step 4: GPU가 활성화되었는지 확인하세요

```js
!nvidia-smi

# CUDA가 사용 가능한지 확인합니다.
torch.cuda.is_available()

device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
device
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

Step 5: 모델 이름과 양자화 유형 구성

```js
MODEL_NAME = "Llama-3-8B-Instruct";
QUANTIZATION = "q4f16_1";
```

Step 6: Llama-3-8B-Insruct 모델을 MLC 호환 가중치로 변환

다음 코드는 q4f16_1 양자화를 사용하여 Llama-3-8B-Instruct 모델을 양자화 및 샤딩하여 여러 청크로 변환합니다. 그런 다음 모델 가중치를 Llama-3-8B-Instruct-q4f16_1-android이란 디렉터리에 변환하고 저장합니다.

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
!python -m mlc_llm convert_weight /content/$MODEL_NAME/ --quantization $QUANTIZATION -o /content/$MODEL_NAME-$QUANTIZATION-android/
```

7단계: 토큰 파일 생성

이 코드 라인은 conv-template, context-window, prefill-chunk-size와 같은 매개변수를 사용하여 토큰 파일을 생성합니다. 이때 conv-template은 llama-3으로 설정되어 있으며, 이는 작업 중인 Llama-3 모델 변형을 나타냅니다.

```js
!python -m mlc_llm gen_config /content/$MODEL_NAME/ --quantization $QUANTIZATION \
    --conv-template llama-3 --context-window-size 8192 --prefill-chunk-size 1024  \
    -o /content/$MODEL_NAME-$QUANTIZATION-android/
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

8단계: Android 형식으로 모델 컴파일하기

여기서는 장치 매개변수를 사용하여 모델 가중치를 Android 호환 형식으로 컴파일하며, 이는 Llama3–8B-Instruct-q4f16_1-android.tar 파일을 생성합니다. 이 .tar 파일은 모델을 기기에 배포하기 위해 이후 단계에서 사용될 것입니다.

```js
!python -m mlc_llm compile /content/$MODEL_NAME-$QUANTIZATION-android/mlc-chat-config.json \
    --device android -o /content/$MODEL_NAME-$QUANTIZATION-android/$MODEL_NAME-$QUANTIZATION-android.tar
```

9단계: 모델을 Hugging Face에 올리기 🤗

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

마지막으로, 모델 가중치를 HF에 저장하세요. 이러한 가중치는 추론 중에 모바일 폰으로 다운로드될 것입니다.

```js
from huggingface_hub import whoami
from pathlib import Path

# 출력 디렉토리.
output_dir = "/content/" + MODEL_NAME + "-" + QUANTIZATION + "-android/"
repo_name = "Llama-3-8B-q4f16_1-android"
username = whoami(token=Path("/root/.cache/huggingface/"))["name"]
repo_id = f"{username}/{repo_name}"
```

```js
from huggingface_hub import upload_folder, create_repo

repo_id = create_repo(repo_id, exist_ok=True).repo_id
print(output_dir)

upload_folder(
    repo_id=repo_id,
    folder_path=output_dir,
    commit_message="Quantized Llama-3-8B-Instruct model for Android.",
    ignore_patterns=["step_*", "epoch_*"],
)
```

다음 HF 🤗 리포지토리에서 샤드된 모델 가중치 및 토크나이저를 직접 찾을 수 있습니다:
https://huggingface.co/NSTiwari/Llama-3-8B-q4f16_1-android

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

여기 완전한 Colab 노트북을 찾을 수 있습니다.

좋아요. 우리는 양자화와 모델 가중치 변환의 초기 단계를 완료했습니다. 이제 다음 섹션에서는 GCP 인스턴스에서 추가로 모델 가중치를 컴파일하기 위한 환경을 설정할 것입니다.

## Section II: 안드로이드용 빌드 파일 생성을 위한 GCP 환경 설정(옵션)

이 단계는 선택 사항이며 이미 Linux 또는 MacOS와 같은 UNIX 기반 시스템을 가지고 있다면 필요하지 않습니다.

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

윈도우 기기를 사용하고 있기 때문에 호환성 문제로 필요한 라이브러리와 종속성을 설치하는 것이 귀찮았어요. 그래서 귀차니즘을 피하기 위해 GCP에서 Linux VM 인스턴스를 렌트하기로 결정했어요.

저는 GCP VM 인스턴스에서 환경을 설정하고 Android Studio를 설치하는 단계를 안내하는 별도의 블로그를 작성했어요.

비슷한 문제를 겪고 계신 분이라면, 여기서 확인해보세요. 그렇지 않다면 건너뛰셔도 돼요.

## 섹션 III: 빌드 종속성 설치

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

### 단계 1: Rust 설치하기

안드로이드로 HuggingFace 토크나이저를 크로스 컴파일하기 위해서는 Rust가 필요합니다. Rust를 설치하려면 아래 명령을 실행하세요.

![이미지](/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_2.png)

표준 설치를 계속하려면 옵션 1을 선택하세요.

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

![image](/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_3.png)

```js
sudo curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Step 2: Install NDK and CMake in Android Studio

Open Android Studio → Tools → SDK Manager → SDK Tools → Install CMake and NDK.

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

<img src="/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_4.png" />

제 3 단계: MLC LLM Python 패키지 및 TVM Unity 컴파일러 설치

<img src="/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_5.png" />

```js
# MLC-LLM Python 패키지와 TVM Unity 컴파일러 설치.
python3 -m pip install --pre -U -f https://mlc.ai/wheels mlc-llm-nightly mlc-ai-nightly

# 아래 명령어를 사용하여 설치 확인:
python3 -c "import mlc_llm; print(mlc_llm)"
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

**단계 4: CMake 설치하기**

![이미지](/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_6.png)

```js
# CMake 설치하기.
sudo apt-get install cmake
```

**단계 5: MLC-LLM 및 Llama3-on-Mobile 저장소 복제하기**

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

<img src="/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_7.png" />

<img src="/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_8.png" />

```js
cd /home/tiwarinitin1999/

# MLC-LLM 저장소를 복제합니다.
git clone https://github.com/mlc-ai/mlc-llm.git
cd mlc-llm

# 저장소의 서브모듈을 업데이트합니다.
git submodule update --init --recursive

# Llama3-on-Mobile 저장소를 복제합니다.
cd /home/tiwarinitin1999/
git clone https://github.com/NSTiwari/Llama3-on-Mobile.git
```

단계 6: 변환된 모델 가중치의 HuggingFace 저장소를 다운로드하세요.

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

MLCChat 디렉토리 내에 새 폴더 dist를 만들어주세요. dist 폴더 안에 prebuilt라는 하위 폴더를 생성해주세요.

![이미지](/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_9.png)

```js
cd /home/tiwarinitin1999/mlc-llm/android/MLCChat
mkdir dist
cd dist
mkdir prebuilt
cd prebuilt
```

그리고 prebuilt 폴더에 HF repository(섹션 1의 단계 9에서 생성된)를 클론해주세요.

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

<img src="/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_10.png" />

```js
# 퀀터이즈된 Llama3-8B-Instruct 가중치의 HF 리포지토리를 복제합니다.
git clone https://huggingface.co/NSTiwari/Llama-3-8B-q4f16_1-android.git
```

7단계: Llama3–8B-Instruct-q4f16_1-android.tar 파일을 복사합니다.

dist 폴더 내에 lib라는 새 폴더를 만들어서 Llama3–8B-Instruct-q4f16_1-android.tar 파일 (Section I의 단계 8에서 생성된 파일)을 lib 디렉토리로 복사합니다.

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

![image](/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_11.png)

```bash
cd /home/tiwarinitin1999/mlc-llm/android/MLCChat/dist
mkdir lib
cd lib/
```

![image](/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_12.png)

Step 8: mlc-package-config.json 파일 구성하기

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

MLCChat 폴더 내의 mlc-package-config.json 파일을 다음과 같이 구성하세요:

```js
{
    "device": "android",
    "model_list": [
        {
            "model": "Llama-3-8B-q4f16_1-android",
            "bundle_weight": true,
            "model_id": "llama-3-8b-q4f16_1",
            "model_lib": "llama-q4f16_1",
            "estimated_vram_bytes": 4348727787,
            "overrides": {
                "context_window_size":768,
                "prefill_chunk_size":256
            }
        }
    ],
    "model_lib_path_for_prepare_libs": {
        "llama-q4f16_1": "./dist/lib/Llama-3-8B-Instruct-q4f16_1-android.tar"
    }
}
```

![이미지](/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_13.png)

9단계: 경로에 환경 변수 설정하기

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

<html>
<img src="/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_14.png" />
</html>

```js
export ANDROID_NDK=/home/tiwarinitin1999/Android/Sdk/ndk/27.0.11718014
export TVM_NDK_CC=$ANDROID_NDK/toolchains/llvm/prebuilt/linux-x86_64/bin/aarch64-linux-android24-clang
export TVM_HOME=/home/tiwarinitin1999/mlc-llm/3rdparty/tvm
export JAVA_HOME=/home/tiwarinitin1999/Downloads/android-studio/jbr
export MLC_LLM_HOME=/home/tiwarinitin1999/mlc-llm
```

Step 10: 안드로이드 빌드 파일 생성

마지막으로, 아래 명령을 실행하여 on-device 배포를 위한 Llama3-8B-Instruct 모델의 .JAR 파일을 빌드하십시오.

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

```sh
cd /home/tiwarinitin1999/mlc-llm/android/MLCChat
python3 -m mlc_llm package
```

![Screenshot](/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_15.png)

명령어가 성공적으로 실행된 후, 아래와 같은 결과를 확인하실 수 있습니다.

![Screenshot](/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_16.png)

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

위 명령은 다음 파일을 생성합니다:

![이미지](/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_17.png)

소스 디렉토리의 출력 폴더 내용을 대상 디렉토리로 복사하세요:

소스 디렉토리:
/home/tiwarinitin1999/mlc-llm/android/MLCChat/dist/lib/mlc4j/output

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

목적지 디렉토리:
/home/tiwarinitin1999/Llama3-on-Mobile/mobile-llama3/MobileLlama3/dist/lib/mlc4j/output

이제, home/tiwarinitin1999/Llama3-on-Mobile/mobile-llama3/MobileLlama3/dist/lib/mlc4j/src/main/assets 폴더에 있는 mlc-app-config.json 파일을 다음과 같이 구성하세요:

```js
{
  "model_list": [
    {
      "model_id": "llama-3-8b-q4f16_1",
      "model_lib": "llama-q4f16_1",
      "model_url": "https://huggingface.co/NSTiwari/Llama-3-8B-q4f16_1-android",
      "estimated_vram_bytes": 4348727787
    }
  ]
}
```

설정 파일의 model_url 키는 모바일폰에서 HF 저장소로부터 모델 가중치를 다운로드합니다.

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

<img src="/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_18.png" />

모든 구성이 설정되었습니다.

## 섹션 IV: 안드로이드 스튜디오에서 앱 빌드하기

안드로이드 스튜디오에서 MobileLlama3 앱을 열고 어느 정도 시간을 들여 빌드하도록 합시다.

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

[![image](/assets/img/2024-05-18-MLStoryMobileLlama3RunLlama3locallyonmobile_19.png)]
(https://miro.medium.com/v2/resize:fit:700/1*wdN1DDl127dzmjIal0FHig.gif)

모바일 앱이 성공적으로 빌드되면 APK를 모바일 폰에 설치하세요. 바로 설치할 수 있는 APK가 여기에 있습니다.

오프라인 사용을 위해 Llama3–8B-Instruct 모델을 모바일 기기에 구동하는 데 성공한 것을 축하드립니다. 이 기사에서 가치 있는 통찰을 얻었기를 기대합니다.

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

위의 GitHub 저장소에서 전체 프로젝트를 확인할 수 있습니다.
https://github.com/NSTiwari/Llama3-on-Mobile

작품을 좋아하셨다면 저장소에 ⭐을 남겨주시고, 동료 온디바이스 AI 개발자들 사이에 소식을 전파해주세요. 앞으로 더욱 흥미로운 프로젝트와 블로그를 기대해주세요.

## 감사의 글

MobileLlama3는 MLC-LLM을 영감을 받아 제작되었으며, 이 프로젝트를 오픈 소스로 만들어주신 MLC-LLM에게 감사드립니다.

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

## 참고 자료 및 자원

- Llama-3-8B-Instruct 모델을 양자화하고 변환하는 Colab 노트북
- MobileLlama3 GitHub 저장소
- 변환된 가중치를 위한 HuggingFace 저장소
- Meta사의 Llama3 모델들
- MLC-LLM
- MLC-LLM용 Android SDK
