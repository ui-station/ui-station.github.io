---
title: "안정적인 확산에 대한 설명"
description: ""
coverImage: "/assets/img/2024-05-18-StableDiffusionExplained_0.png"
date: 2024-05-18 20:45
ogImage: 
  url: /assets/img/2024-05-18-StableDiffusionExplained_0.png
tag: Tech
originalTitle: "Stable Diffusion Explained"
link: "https://medium.com/@onkarmishra/stable-diffusion-explained-1f101284484d"
---


## Stable 확산이 어떻게 작동합니까? 텍스트에서 이미지 생성 기술 설명

![이미지](/assets/img/2024-05-18-StableDiffusionExplained_0.png)

크기가 큰 텍스트에서 이미지 모델은 텍스트 프롬프트로부터 이미지의 고품질 합성을 가능케하여 높은 성공을 거뒀습니다. 확산 모델은 텍스트에서 이미지 생성 작업에 적용되어 최첨단 이미지 생성 결과를 얻는 데 도움이 될 수 있습니다.

Stable 확산 모델은 이미지 생성을 위해 최첨단 결과를 달성했습니다. Stable 확산은 특정 유형의 확산 모델인 Latent 확산 모델에 기초합니다. 이 모델은 CompVis, LMU 및 RunwayML의 연구원 및 엔지니어들에 의해 제안된 'Latent 확산 모델을 사용한 고해상도 이미지 합성'에 기반합니다. 이 모델은 LAION-5B 데이터베이스의 하위 집합에서 512x512 이미지로 초기에 교육되었습니다.

<div class="content-ad"></div>

이는 주로 CLIP와 같은 사전 훈련된 언어 모델을 사용하여 텍스트 입력을 잠재 벡터로 인코딩함으로써 달성됩니다. 확산 모델은 텍스트로부터 이미지 데이터를 생성하는 데 최첨단 결과를 달성할 수 있습니다. 그러나 잡음 제거 프로세스는 고해상도 이미지를 생성할 때 매우 느리며 많은 메모리를 소비합니다. 따라서 이러한 모델을 훈련하고 추론에 사용하는 것이 어려운 과제입니다.

이에 따라 잠재 확산은 실제 픽셀 공간 대신 낮은 차원의 잠재 공간에서 확산 프로세스를 적용함으로써 메모리와 계산 시간을 줄일 수 있습니다. 잠재 확산에서 모델은 이미지의 잠재(압축) 표현을 생성하도록 훈련됩니다.

확산 모델의 훈련

Stable Diffusion은 수십억 장의 이미지로 훈련된 대규모 텍스트에서 이미지로 확산 모델입니다. 이미지 확산 모델은 이미지를 잡음 제거하여 출력 이미지를 생성하는 방법을 배웁니다. Stable Diffusion은 훈련 데이터에서 인코딩된 잠재 이미지를 입력으로 사용합니다. 또한 주어진 이미지 z0에 대해, 확산 알고리즘은 이미지에 점진적으로 잡음을 추가하여 소음이 섞인 이미지 zt를 생성합니다. 여기서 t는 잡음이 추가된 횟수를 나타냅니다. t가 충분히 크면 이미지는 순수한 잡음에 가까워집니다. 시간 단계 t, 텍스트 프롬프트, 이미지 확산 알고리즘 등의 입력 집합이 주어진 경우, 확산 알고리즘은 노이즈를 예측하는 네트워크를 학습합니다.

<div class="content-ad"></div>

잠재 확산에는 주로 세 가지 주요 구성요소가 있습니다:

- 자동 인코더 (VAE).
- U-Net.
- 텍스트 인코더, 예를 들어 CLIP의 텍스트 인코더.

1. 자동 인코더 (VAE)

VAE 모델은 인코더와 디코더 두 부분으로 구성됩니다. 잠재 확산 학습 중에 인코더는 512*512*3 이미지를 순방향 확산 과정을 위한 사이즈가 64*64*4인 낮은 차원의 잠재 표현으로 변환합니다. 우리는 이미지의 이러한 작은 인코딩된 버전을 잠재라고 부릅니다. 학습 각 단계에서 이러한 잠재에 더 많은 잡음을 적용합니다. 이미지의 인코딩된 잠재 표현은 U-Net 모델의 입력으로 작용합니다.

<div class="content-ad"></div>

여기서는 (3, 512, 512) 모양의 이미지를 (4, 64, 64) 모양의 잠재 이미지로 변환하여 메모리를 48배 더 적게 사용합니다. 이는 픽셀 공간 확산 모델과 비교했을 때 메모리 및 계산 요구 사항을 줄여줍니다. 따라서 16GB Colab GPU에서도 512 × 512 이미지를 매우 빠르게 생성할 수 있습니다.

디코더는 잠재 표현을 이미지로 다시 변환합니다. 역확산 과정에서 생성된 노이즈 제거된 잠재 이미지를 VAE 디코더를 사용하여 이미지로 변환합니다.

추론 중에는 노이즈가 제거된 이미지를 실제 이미지로 변환하기 위해 VAE 디코더만 필요합니다.

```python
from torchvision import transforms as tfms
from diffusers import AutoencoderKL

# Decode the latent representation into image space using the autoencoder model.
vae = AutoencoderKL.from_pretrained("CompVis/stable-diffusion-v1-4", subfolder="vae")

# Move to the GPU
vae = vae.to(torch_device)

# Convert PIL image to latents

def pil_to_latent(input_im):
    # Single image -> single latent in a batch (size: 1, 4, 64, 64)
    with torch.no_grad():
        latent = vae.encode(tfms.ToTensor()(input_im).unsqueeze(0).to(torch_device)*2-1) # Note scaling
    return 0.18215 * latent.latent_dist.sample()
```

<div class="content-ad"></div>

2. UNet

U-Net은 노이즈가 있는 잠재 변수의 더 많은 이미지 표현을 예측합니다. 여기서 노이즈가 있는 잠재 변수가 Unet의 입력으로 작용하며 UNet의 출력은 잠재 변수의 노이즈입니다. 이를 사용하여 우리는 노이즈를 노이즈가 있는 잠재 변수에서 뺌으로써 실제 잠재 변수를 얻을 수 있습니다.

Unet은 노이즈가 있는 잠재 변수(x)를 입력으로 사용하고 노이즈를 예측합니다. 우리는 또한 타임스텝(t)과 텍스트 임베딩을 가이드로 사용하는 조건부 모델을 사용합니다.

따라서, 모델은 다음과 같습니다:

<div class="content-ad"></div>

```python
from diffusers import UNet2DConditionModel

# "CompVis/stable-diffusion-v1-4" 모델을 사용하여 UNet 모델을 불러옵니다.
unet = UNet2DConditionModel.from_pretrained("CompVis/stable-diffusion-v1-4", subfolder="unet")

# GPU로 모델을 이동합니다.
unet = unet.to(torch_device)
# 노이즈 예측
noise_pred = unet(latents, t, encoder_hidden_states=text_embeddings)["sample"]
```

해당 모델은 기본적으로 UNet 모델을 사용하며, 인코더(12블록), 가운데 블록 및 스킵 연결 디코더(12블록)로 구성되어 있습니다. 이 25개 블록 중 8개 블록은 다운샘플링 또는 업샘플링 컨볼루션 레이어이고, 17개 블록은 각각 네 개의 ResNet 레이어와 두 개의 Vision Transformer(ViT)를 포함하는 주요 블록입니다. 여기서 인코더는 이미지 표현을 낮은 해상도 이미지 표현으로 압축하고, 디코더는 낮은 해상도 이미지 표현을 원래의 더 높은 해상도 이미지 표현으로 해석합니다. 이로써 노이즈가 적은 이미지를 생성합니다.

3. 텍스트-인코더

텍스트-인코더는 입력 프롬프트를 임베딩 공간으로 변환하여 U-Net에 입력으로 제공합니다. Unet을 노이즈 제거 프로세스에 학습시킬 때 노이즈가 많은 latents를 안내하는 역할을 합니다. 텍스트-인코더는 일반적으로 간단한 트랜스포머 기반 인코더로, 입력 토큰 시퀀스를 잠재적인 텍스트-임베딩 시퀀스로 매핑합니다. Stable Diffusion은 새로운 텍스트 인코더를 학습하지 않고 이미 학습된 텍스트 인코더인 CLIP을 사용합니다. 텍스트 인코더는 입력 텍스트에 해당하는 임베딩을 생성합니다.
```

<div class="content-ad"></div>

토큰화

```js
from transformers import CLIPTextModel, CLIPTokenizer

# 토크나이저 및 텍스트 인코더를로드하여 텍스트를 토큰화하고 인코딩합니다.
tokenizer = CLIPTokenizer.from_pretrained("openai/clip-vit-large-patch14")
text_encoder = CLIPTextModel.from_pretrained("openai/clip-vit-large-patch14")

# GPU로 이동
text_encoder = text_encoder.to(torch_device)

prompt = 'An astronaut riding a horse'
# 텍스트를 토큰 시퀀스로 변환합니다.
text_input = tokenizer(prompt, padding="max_length", max_length=tokenizer.model_max_length, truncation=True, return_tensors="pt")
input_ids = text_input.input_ids.to(torch_device)
```

Embedding 결과

```js
# 토큰에서 출력 임베딩 가져오기
output_embeddings = text_encoder(text_input.input_ids.to(torch_device))[0]
print('모양:', output_embeddings.shape)
```   

<div class="content-ad"></div>

다 모아보면, 모델은 추론 과정 중에 다음과 같이 작동합니다:

![image](/assets/img/2024-05-18-StableDiffusionExplained_1.png)

스케줄러

위에서 언급된 3가지 외에도 이미지에 노이즈를 추가하고 모델을 사용하여 노이즈를 예측하는 데 사용되는 스케줄러가 있습니다.

<div class="content-ad"></div>

```markdown
```js
diffusers에서 LMSDiscreteScheduler를 가져와주세요
scheduler = LMSDiscreteScheduler(beta_start=0.00085, beta_end=0.012, beta_schedule="scaled_linear", num_train_timesteps=1000)
```

위 코드는 모델을 훈련하는 데 사용되는 스케줄러를 설정합니다. 작은 단계 수에 대해 스케줄러를 설정하려면 다음과 같이 스케줄러를 설정하세요:

```js
# 샘플링 단계 수를 설정하세요:
scheduler.set_timesteps(15)
```

Stable Diffusion과 같은 Latent Diffusion Model은 다양한 창의적인 응용 프로그램을 가능하게 합니다:
```  
```

<div class="content-ad"></div>

- 텍스트에서 이미지로 변환
- 이미지에서 이미지 생성 - 시작점을 기반으로 새 이미지를 생성하거나 수정
- 이미지 업스케일링 - 작은 이미지를 큰 이미지로 확대
- 인페인팅 - 이미지의 특정 영역을 마스킹하여 해당 영역에 새로운 디테일을 생성하는 것

잠재 확산 모델은 훈련 비용과 추론을 줄여주어 대량의 고해상도 이미지 합성을 대중화 할 수 있는 잠재력이 있습니다.

다음 블로그에서는 새로운 개념이나 작업을 학습하는데 안정적인 확산을 세밀하게 조정하는 텍스트 역전법에 대해 이야기할 예정입니다.

참고:

<div class="content-ad"></div>

- 롬바흐, R., 블랫만, A., 로렌츠, D., 에셀, P., & 오머, B. (2022). 잠재 확산 모델을 사용한 고해상도 이미지 합성. IEEE/CVF 컴퓨터 비전과 패턴 인식 컨퍼런스 논문집 (pp. 10684–10695).
- 장, L., & 아그라와라, M. (2023). 텍스트-이미지 확산 모델에 조건부 제어 추가. arXiv 사전 인쇄 arXiv:2302.05543.
- [Hugging Face Diffusers 문서](https://huggingface.co/docs/diffusers/index)