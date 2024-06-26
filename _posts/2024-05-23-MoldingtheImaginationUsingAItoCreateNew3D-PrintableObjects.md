---
title: "상상력을 형성하다 AI를 사용하여 새로운 3D 프린트 가능한 물체 만들기"
description: ""
coverImage: "/assets/img/2024-05-23-MoldingtheImaginationUsingAItoCreateNew3D-PrintableObjects_0.png"
date: 2024-05-23 16:25
ogImage:
  url: /assets/img/2024-05-23-MoldingtheImaginationUsingAItoCreateNew3D-PrintableObjects_0.png
tag: Tech
originalTitle: "Molding the Imagination: Using AI to Create New 3D-Printable Objects"
link: "https://medium.com/towards-data-science/molding-the-imagination-using-ai-to-create-new-3d-printable-objects-cf3682f8563b"
---

제가 Medium에서 쓴 글을 읽어보셨다면, AI를 활용한 실험을 좋아하는 것을 아시겠죠. 창의적인 노력에 AI를 활용하여 경험을 쓰곤 했습니다. 이미지 생성, 창의적 글쓰기, 음악 작곡과 같은 영역을 다뤄왔죠. 이번에는 처음으로 연구를 3차원으로 확장했습니다. 상용 및 오픈 소스 AI 도구를 사용하여 새로운 물리적 물체를 만들고 3D 프린터를 사용해 출력하는 방법을 연구했습니다. 이 글에서는 다양한 상용 및 오픈 소스 도구를 사용하여 4개의 다른 3D 메시를 디자인하고 출력하는 과정을 보여드릴 거에요. 첨부된 3D 갤러리에는 이 4개의 물체가 모두 담겨 있습니다.

# 개요

다음 장에서는 3D 물체 생성에 다양한 도구를 사용하여 실행한 4가지 실험을 안내하고 결과를 보여드릴 거에요. 첫 번째 실험은 상용 도구를 사용했습니다. 2D 이미지를 생성하는 Midjourney와 3D 메시를 추출하는 3dMaker.ai와 같은 웹사이트를 활용했습니다. 두 번째 실험에서는 OpenAI의 Shape-E [1]와 같은 오픈 소스 AI 모델을 활용했습니다. 세 번째는 MVDream [2]이라고 불리는 오픈 소스 모델을 사용하였고, 네 번째 실험에서는 MVDream과 threestudio [3]라는 다른 오픈 소스 프로젝트를 조합하여 사용했습니다.

모든 경우에, 텍스트 프롬프트로 시작하여 3D 메시를 생성하고, Blender(오픈 소스 데스크톱 애플리케이션)를 사용하여 3D 메시를 수정하고 정리했습니다. 그 후, 데스크톱 '슬라이서' 앱인 Ultimaker Cura와 PrusaSlicer를 사용하여 3D 메시를 준비하고 미리보기한 뒤, 지역 도서관에서 이들을 출력했습니다. 이 물체들을 출력해 보고 싶으시다면, 제 Thingiverse 프로필 페이지에서 확인해보세요.

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

4가지 예시를 살펴본 후에, AI를 사용하여 3D 객체를 생성하는 것이 사회적 영향과 윤리에 미치는 영향에 대해 다뤄볼 거에요. 다양한 서비스와 시스템으로부터 소유권 권리에 대한 정책을 논의할 거에요. 마지막으로 제 실험에서 배운 내용을 요약하겠습니다.

# Midjourney와 3dMaker.ai로 3D 객체 생성하기

첫 번째 실험으로, Midjourney를 사용해보았어요. Midjourney는 텍스트 프롬프트를 기반으로 이미지를 생성하는 상용 서비스예요. 이 서비스에 대한 자세한 정보는 이전에 작성한 내 기사를 확인해보세요.

2D 이미지를 만들기 위해, Midjourney 계정에 로그인하고 이 프롬프트를 입력했어요. "간단한 기하학적인 3D 프린팅된 조각상, 단색의 흰색 플라스틱, 작은 흰색 플라스틱 피달 위에 서 있는 상태, 회색 배경" 이렇게 작성해보았어요. 그 결과로 아래에 보이는 4개의 썸네일 이미지가 생성되었어요.

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

![이미지](/assets/img/2024-05-23-MoldingtheImaginationUsingAItoCreateNew3D-PrintableObjects_0.png)

네 네 이미지 네 개 모두 정말 좋아요. 시스템은 3D 프린팅에 대해 알고 있어요; 모든 형태가 흥미롭고 출력할 수 있어요. 왼쪽 상단의 "Figure 8" 이미지가 제일 좋았어요. 그런 다음에 Midjourney의 확대 기능을 사용해서 이미지의 큰 버전을 만들었어요.

## Clipdrop로 배경 제거하기

2D 이미지에서 3D 메쉬를 만드는 다음 단계는 장면에서 배경을 제거하는 것이에요. 많은 AI 모델이 이를 할 수 있지만, stability.ai의 무료 서비스인 Clipdrop를 이용하면 잘 할 수 있어요. 웹 브라우저를 통해 접근할 수 있어요.

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

위의 이미지 시퀀스에서 확인할 수 있듯이 Clipdrop는 사용하기 매우 쉽습니다. 사이트에 접속하여 원본 이미지를 업로드하고 "배경 제거" 버튼을 클릭했습니다. 시스템이 그림자를 포함한 배경을 훌륭하게 제거하고 조각상의 모든 세부 사항을 유지했습니다. 그런 다음 생성된 이미지를 다운로드했습니다.

## 3dMaker.ai를 사용하여 2D 이미지로부터 3D 메쉬 작성

3D 모델을 출력하려면 Midjourney에서 만든 2D 이미지를 먼저 3D 메쉬로 변환해야 했습니다. 이를 수행할 수 있는 여러 오픈 소스 모델이 있지만, 상업용 서비스인 3dMaker.ai를 발견했고 수수료를 지불하면 잘 수행된다는 것을 알았습니다.

3D 메쉬를 생성하려면 3dMaker.ai에 들어가 계정을 생성하고 배경이 제거된 2D 이미지를 업로드했습니다. 3D 메쉬를 생성하는 두 가지 선택지가 있습니다: US$25의 표준 품질과 US$40의 고품질. 사이트에는 표준 품질이 경직면, 유기물질, 매우 세밀한 입력에 적합하고 고품질은 유기물, 정말 세밀한 입력에 적합하다고 합니다. 저는 표준 품질을 선택하고 "생성" 버튼을 클릭했습니다. 결과물은 약 30분이 걸렸고, OBJ 형식으로 모델을 다운로드했습니다. 결과는 훌륭했습니다! 원본 이미지의 세부 사항을 포착하고 이미지의 뒷부분을 정확하게 재구성했습니다.

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

대단하게도, 3D Maker AI는 조각물 앞면의 이미지 하나만을 기반으로 조각물 뒷면의 세부사항을 생성했습니다. 본문에서는 나중에 이 서비스의 또 다른 출력물 예시를 볼 수 있습니다.

## 3D 프린팅 서비스와 지역 도서관

이 실험의 마지막 단계는 3D 메시를 출력하는 것이었습니다. 저는 3D 프린터를 소유하고 있지 않았지만, 주변 보스턴 지역의 많은 공공 도서관이 무료 또는 저렴한 3D 프린팅 서비스를 제공한다는 사실에 기쁨을 느꼈습니다. 시민들은 이 프린터를 사용하여 휴대전화 케이스나 쿠키 커터 같은 유용한 물건 뿐만 아니라 작은 장식용품도 만들 수 있습니다.

도서관은 다양한 종류의 이용 방법을 제공합니다. 일부는 "메이커스페이스" 안에 3D 프린터를 갖추고 있으며, 시민들이 창조하고 발명하며 배우는 협업 작업 공간입니다. 다른 도서관은 온라인 프린팅 서비스를 제공하여 시민들이 3D 메시를 업로드해 출력을 요청할 수 있습니다. 또한 도서관은 인쇄 가능한 최대 크기나 프린팅 재료의 색상 선택과 같은 서비스에서도 차이가 있습니다.

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

## 3D 객체 출력

3D 매쉬를 출력하기 위해 먼저 파일을 준비해야 했습니다. 3D 프린팅의 표준 파일 형식은 STL이지만, 3dMaker.ai는 그 형식으로 내보내지 않습니다. 그래서 3D 모델링을 위한 오픈 소스 데스크톱 애플리케이션인 Blender를 사용하여 OBJ 파일을 읽고 STL로 내보냈습니다. 그런 다음 PrusaSlicer라는 슬라이서 앱을 실행하고 STL 파일을 가져와서 출력 미리보기를 확인했습니다.

저는 월섬 공립 도서관의 Makerspace에서 짙은 회색 필라멘트를 사용하여 이를 출력했습니다. 3D 프린팅에서 필라멘트는 융착 적층 모델링(FDM) 프린터용 열가소성 피드 스톡입니다. 다양한 재료의 스풀에서 제공되며 프린터 노즐을 통해 녹이고 추출하여 객체를 층층이 만듭니다. 월섬 도서관의 3D 프린터를 사용하여 객체를 무료로 인쇄할 수 있습니다. 저는 위의 샘플을 Prusa MK3S 프린터를 사용하여 0.6mm 노즐로 인쇄했습니다.

또한 출력물을 화이트 필라멘트를 사용하여 우번 공립 도서관의 온라인 양식을 통해 출력했습니다. 그들은 11인치까지 가능한 프린트를 허용하지만, 제 프린트 시간 때문에 6.5인치로 출력했습니다. 10시간 이상 소요되는 작업은 허용하지 않습니다. 다른 도서관과 달리 우번은 작업에 사용된 소재에 대해 요금을 부과합니다. 그러나 월별 5달러의 신용을 발행합니다. 신용을 적용한 후 총 비용은 3.63달러입니다. 여기에 두 개의 출력물이 있습니다.

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

양쪽으로 출력된 물체는 모두 훌륭히 나왔어요! 월섬에서 출력된 것은 높이가 단 4.75인치이지만, 원본 미죄니 이미지에서 많은 세부사항을 보여줍니다. 상대적으로 작은 규모로 출력되어 몇 가지 계층화 물표가 보입니다. 월섬의 무료 출력 서비스는 최대 8시간 동안 물체를 생성하도록 제한되어 있어요. 우번에서 출력된 것은 높이가 6.5인치로 더 나아 보이며, 계층화 물표가 더 적어요. 하지만 조각의 표면에 몇 개의 어두운 자국이 있습니다.

# Shape-E로 3D 물체 생성하기

다음 실험으로, OpenAI에서 나온 오픈소스 AI 모델인 Shap-E[1]을 사용했어요. 이 모델은 텍스트 프롬프트로부터 3D 면을 생성하는데, Shap-E라는 이름은 텍스트 프롬프트로부터 2D 이미지를 생성하는 DALL-E AI 모델에 대한 재미있는 연상이에요. 이들의 논문에서 여러 예시 이미지를 확인할 수 있어요.

## Shap-E 모델

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

2023년 5월에 OpenAI는 Shap-E라는 텍스트-3D 모델을 출시했습니다. 이 시스템은 암시 함수의 매개변수를 생성하여 상세한 3D 비주얼을 렌더링할 수 있도록 3D 메쉬를 생성하는 데 사용되었습니다. 그들은 두 단계의 훈련 과정을 사용했는데, 먼저 3D 에셋을 매개변수에 매핑하고 그 결과를 조건부 확산 모델로 정제했습니다. 이 방식을 통해 텍스트 프롬프트로부터 다양한 3D 모델을 효율적으로 생성할 수 있습니다. 각 샘플은 GPU에서 대략 13초가 걸립니다. OpenAI는 훈련된 모델의 소스 코드와 가중치를 MIT 오픈소스 라이선스 하에 GitHub에 공개했습니다.

이 시스템은 OpenAI의 모델 카드에서 설명된 세 가지 AI 모델을 사용합니다.

- 송신기(transmitter) - 인코더와 해당 프로젝션 레이어로, 인코더 출력을 암시적 신경 표현으로 변환하는 데 사용됩니다.
- 디코더(decoder) - 송신기의 최종 프로젝션 레이어 구성 요소입니다. 이는 3D 에셋 인코딩을 위한 매개변수를 포함하지 않기 때문에 송신기보다 작은 체크포인트입니다. 이는 확산 결과를 암시적 신경 표현으로 변환하는 데 필요한 최소한의 모델입니다.
- 텍스트300M(text300M) - 텍스트 조건부 잠재 확산 모델입니다.

또한 이미지 조건부 잠재 확산 모델도 있지만, 이 프로젝트에서는 사용하지 않았습니다.

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

## 파이썬에서 Shap-E 실행하기

저는 Google Colab을 사용하여 프롬프트에서 3D 메쉬를 생성했어요. 여기에는 시스템을 초기화하는 Python 코드가 있어요.

```js
from shap_e.diffusion.gaussian_diffusion import diffusion_from_config
from shap_e.models.download import load_model, load_config
import torch
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
xm = load_model('transmitter', device=device)
model = load_model('text300M', device=device)
diffusion = diffusion_from_config(load_config('diffusion'))
```

이 코드는 전송기, 디코더 및 텍스트 확산 모델 세 개를 다운로드해요. GPU가 있다면 모든 모델이 GPU로 로드돼요.

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

## Shap-E를 사용하여 3D 개체 생성하기

저는 Shap-E에 텍스트 프롬프트를 보내어 3D 형태의 잠재 매개변수를 생성하는 데 이 코드를 사용했어요.

```js
from shap_e.diffusion.sample import sample_latents
from shap_e.util.notebooks import create_pan_cameras, decode_latent_images
import numpy as np
import random
prompt = "돌고래"
seed = 0
batch_size = 1
guidance_scale = 15
render_mode = 'nerf'
size = 512

torch.manual_seed(seed)
np.random.seed(seed)
random.seed(seed)

latents = sample_latents(
  batch_size=batch_size, model=model, diffusion=diffusion,
  guidance_scale=guidance_scale, model_kwargs=dict(texts=[prompt] * batch_size),
  progress=True, clip_denoised=True, use_fp16=True, use_karras=True,
    karras_steps=64, sigma_min=1e-3, sigma_max=160, s_churn=0)

cameras = create_pan_cameras(size, device)
for i, latent in enumerate(latents):
  images = decode_latent_images(xm, latent, cameras, rendering_mode=render_mode)
  display(gif_widget(images))
```

여기서 "돌고래"라는 텍스트 프롬프트를 사용하여 Shap-E를 통해 잠재 매개변수를 샘플링하는 방법을 확인할 수 있어요. 저는 랜덤 시드를 0으로 초기화했기 때문에 항상 같은 메쉬가 렌더링됩니다. 랜덤 시드를 1, 2 또는 다른 정수로 변경하면 메쉬의 다양한 변형이 생성됩니다. 이것이 최종 이미지입니다.

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

![image](/assets/img/2024-05-23-MoldingtheImaginationUsingAItoCreateNew3D-PrintableObjects_1.png)

이거 정말 기본적이고 공간이 많이 남아 있어요. 하지만 분명히 돌고래처럼 보여요. 여기 3D 메쉬를 내보내는 코드가 있어요.

```js
from shap_e.util.notebooks import decode_latent_mesh

for i, latent in enumerate(latents):
    t = decode_latent_mesh(xm, latent).tri_mesh()
    with open(f'example_mesh_{i}.ply', 'wb') as f:
        t.write_ply(f)
```

이 코드는 잠재변수를 사용하여 PLY라는 3D 객체를 위한 또 다른 파일 형식의 메쉬를 생성합니다. 이게 Blender에서 메쉬가 어떻게 보이는지에요.

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

![Molding the Imagination Using AI to Create New 3D-Printable Objects](/assets/img/2024-05-23-MoldingtheImaginationUsingAItoCreateNew3D-PrintableObjects_2.png)

아직도 돌고래처럼 보이지만 메쉬에는 몇 가지 문제가 있어요. 물체의 몸에 층층이 나뉜 단계가 보이고, 지느러미는 가로 슬라이스로 깨진 것처럼 나옵니다. 또한, 메쉬에는 인쇄된 물체로 보여줄 수 있는 받침대가 없어요. 층 나누기 문제를 해결하기 위해 Blender에서 Displacement modifier를 사용하여 불룸버를 두껍게 하고 부드럽게 하는 Smooth modifier를 사용했어요. 받침대를 만들기 위해 Shap-E를 다시 실행하여 "바다 물결 스타일의 원기둥 형태 받침대"를 작성하고 메쉬를 내보냈어요. 그런 다음 Blender에서 두 객체를 위치시키고 결합했어요. 여기 수정된 메쉬가 있어요.

![Molding the Imagination Using AI to Create New 3D-Printable Objects](/assets/img/2024-05-23-MoldingtheImaginationUsingAItoCreateNew3D-PrintableObjects_3.png)

돌고래의 측면을 매끄럽게 만들고 지느러미를 수정한 것을 볼 수 있어요. Shap-E가 받침대를 잘 렌더링해 주었는데, Blender에서 거의 수정이 필요했어요. 받침대의 기둥 바닥에 가는 얇은 원기둥만 추가했어요. 다음으로, 모델을 STL 파일로 저장했어요.

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

## 3D 객체 출력

돌고래 아래에 많은 음수 공간이 있기 때문에 슬라이서 앱은 상단 레이어를 인쇄할 수 있도록 임시 지원 기둥을 추가했습니다. 아래 이미지에서 확인할 수 있습니다.

또한 Waltham 공립 도서관의 Makerspace에서 이를 인쇄했습니다. 이를 Prusa MK3S 프린터에서 인쇄했습니다. 최종 작품은 다음과 같습니다.

![3D Object](/assets/img/2024-05-23-MoldingtheImaginationUsingAItoCreateNew3D-PrintableObjects_4.png)

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

이 것도 잘 나왔네요. 작게 인쇄되어 5인치만큼 넓어서 인쇄 과정의 계층적인 선을 볼 수 있어요. 또한 상단의 등지느러미는 매우 얇고 더 잘 정의되어야 합니다.

# MVDream으로 3D 객체 만들기

다음 실험으로 MVDream이라는 오픈 소스 프로젝트를 사용했어요. MV는 "다중 뷰"를 의미하는데 Stable Diffusion 텍스트-이미지 모델의 변형입니다. 이 모델은 텍스트 프롬프트를 제공하면 3D 객체의 여러 2D 뷰를 생성하는데, 저자들은 이 모델에 대해 논문에서 설명하고 있어요 [2].

기계 학습에서 "사전"이란 특정 입력을 받기 전에 모델이 가지고 있는 지식을 말해요. MVDream의 다중 뷰 확산 모델은 특정 3D 표현에 관계없이 3D 사전 역할을 하는데요. 이는 텍스트 프롬프트를 기반으로 다양한 각도에서 3D 표현을 생성할 수 있어 다른 3D 데이터 형식에 구속되지 않아 다양한 3D 표현을 유연하게 생성할 수 있습니다. 아래는 MVDream이 특정 프롬프트로 생성한 몇 가지 예시 객체입니다.

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

이미지는 MVDream이 텍스트 프롬프트로부터 다양한 각도의 일관된 상세 렌더링을 보여주는 멀티뷰 3D 객체를 생성할 수 있는 능력을 보여줍니다. 우주 비행사를 탄 호스와 조각된 독수리를 비롯한 예시는 모델의 실용적인 응용을 보여주며, 정확성을 유지하면서 다양한 3D 이미지를 생성하는 데 모델을 사용하는 사례를 보여줍니다.

저는 다음의 Python 코드를 사용하여 Google Colab에서 MVDream을 실행했습니다.

```python
from mvdream.camera_utils import get_camera
from IPython.display import display

prompt = """a 3d-printed Cubist-styled sculpture of a male bust,
  in light-gray plastic, on a simple light-gray pedestal,
  dark-gray background"""

num_views = 4
seed = 12
set_seed(seed)

img = t2i(model, prompt=prompt, uc=uc, sampler=sampler, step=100, scale=10,
  batch_size=num_views, ddim_eta=0.0, device=device, camera=camera,
  num_frames=num_views, image_size=256, seed=seed)

images = np.concatenate(img, 1)
pil_image = Image.fromarray(images, 'RGB')
display(pil_image)
```

위 코드는 텍스트 프롬프트로부터 3D 객체의 다수의 2D 뷰를 생성하기 위해 MVDream을 사용한 방법을 보여줍니다. 생성되는 객체의 원하는 속성을 지정하고 (남성 버스트의 큐비즘 스타일 조형물) 모델을 구성하여 네 가지 다른 뷰를 생성하도록 설정했습니다. 그런 다음 이러한 뷰를 처리하여 그림을 조합하여 IPython의 display 함수를 사용하여 측면에 나란히 표시한 이미지를 만들었습니다. 결과는 여기에서 확인할 수 있습니다.

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

3D 프린팅된 큐비즘 스타일의 남성 상반신을 보여주는 네 각도의 이미지가 있습니다. 각각의 뷰는 서로 일관성이 있으며, 텍스트 프롬프트를 정확하게 해석하고 시각화하는 모델의 능력을 강조합니다. 조각의 질감은 3D 프린트와 유사한 입자를 시사하며, 기단 위에 배치된 것은 전시 준비가 된 외관을 제공합니다.

## 3dMaker.ai를 사용하여 네 개의 2D 이미지에서 3D 메시 만들기

한번 더, 3D Maker AI 서비스를 사용하여 네 개의 이미지에서 3D 메시를 생성했습니다. 여기 Blender에서 렌더링된 메시의 네 가지 뷰가 있습니다.

3D Maker AI의 출력물은 원본 형태에 근접하지만 몇 가지 눈에 띄는 차이가 있습니다. MVDream의 출력물은 더 명확하고 각진 평면을 보여줍니다, 특히 얼굴의 경계 부분에 있어서 이는 프롬프트에 언급된 큐비즘 스타일의 특성입니다. 3D Maker AI 서비스에서 렌더링된 메시에서는 머리 형태가 더 부드럽게 나타나며, 얼굴의 특징과 머리의 평면 사이에 더 점진적인 전환을 보여줍니다. 예술적 효과를 내기 위해 Prusa 슬라이서의 다각형 축소 설정을 사용하여 머리를 보다 각진 형태로 만들었습니다. 결과물을 다음과 같습니다.

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

잘 보이지는 않지만, 머리에는 위에 표시된 버전보다 더 명확한 각진 삼각형이 많이 있습니다. 나는 이러한 예술적 선택을 한 것은 조각이 더 입체적 스타일로 보이게 하기 위함이었다.

## 3D 모델 인쇄하기

나는 이 메쉬를 워터타운 무료 공공 도서관의 해치 메이커 스페이스에서 인쇄했다. 그들은 다른 슬라이서인 Unitmaker Cura를 사용하지만, 프린터는 같은 Prusa i3 MK3를 사용했다. 나는 빌드 플레이트에 연결된 나무 서포트를 사용했는데, 이것은 완성된 조각에서 부착 지점의 양을 최소화했다.

나무 서포트는 이 3D 조각에서 특히 기묘하게 보인다. 그러나 소프트웨어는 이 조각을 만들 때 이마의 벌어짐을 지지하는 방법이 필요했다. 완성된 조각의 부착 지점이 최소화되었기 때문에 서포트를 쉽게 제거할 수 있었다. 여기 최종 조각이다.

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

![image](/assets/img/2024-05-23-MoldingtheImaginationUsingAItoCreateNew3D-PrintableObjects_5.png)

이거 잘 나왔네요. 3D 프린팅된 머리는 각진 형태와 면으로 십자법 Cubist 스타일을 보여줍니다. 균일한 파란색으로 주조된 이 조각은 디테일보다 기하학적 형태를 강조하며, 예리한 평면이 얼굴 윤곽을 정의합니다. 간단한 베이스 위에 놓여 있습니다.

# threestudio와 MVDream으로 3D 객체 생성하기

MVDream 모델은 텍스트 프롬프트에서 3D 객체의 여러 뷰를 렌더링하는 데 효과적이지만, 3D 메쉬를 생성하지는 않습니다. 여기서 threestudio가 필요합니다. threestudio는 사용자가 MVDream을 포함한 다양한 텍스트-3D 및 이미지-3D 구성 요소를 실험할 수 있는 모듈식 프레임워크를 제공하는 오픈 소스 프로젝트입니다. 저자들이 논문에서 말한 내용을 살펴보세요.

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

위 논문에서 가져온 플로우 다이어그램이에요. 여기 주요 구성 요소들이 보여져요.

AI를 활용해 텍스트나 이미지에서 3D 콘텐츠를 생성하는 threestudio 파이프라인은 몇 가지 주요 구성 요소로 구성되어 있어요. 이 프로세스는 최적화를 위해 무작위 카메라 매개변수를 생성하는 것으로 시작되며, 외부 및 내부 특성 및 조명 조건을 포함하고 있어요. Geometry는 암시적 서명된 거리 함수(SDF), 암시적 밀도 필드 등과 같은 표현을 통해 3D 물체나 장면을 정의해요. 재료는 특정 조건 하에서 물체의 외관을 결정하며, 확산 및 물리 기반 렌더링(PBR) 유형을 활용하고 있어요. 배경 생성 옵션에는 Neural Environment Maps, Textured Maps, 또는 Solid Colors가 포함되어 있어요. 렌더링은 다양한 래스터라이저가 처리하며, Geometry와 재료를 고려해 최종 이미지를 생성해요. DeepFloyd-IF 및 Stable Diffusion과 같은 확산 모델의 지침은 텍스트나 이미지 입력을 사용하여 원하는 3D 콘텐츠를 생성하기 위한 최적화 프로세스를 안내해요. 이 구조화된 접근 방식을 통해 텍스트나 시각적 입력에서 3D 표현을 모듈식으로 생성할 수 있어요.

## threestudio와 MVDream을 사용하여 3D 오브젝트 만들기

저는 threestudio를 MVDream과 함께 구글 Colab에서 실행했어요, 하지만 모델은 16기가 이상의 VRAM을 가진 GPU가 필요했어요. 그래서 A100 GPU에 액세스하기 위해 Colab Pro 구독이 필요했어요.

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

threestudio와 MVDream 확장 프로그램을 설치한 후, 다음 코드를 사용하여 텍스트 프롬프트에서 3D 객체를 생성했습니다.

```js
prompt = """기하학적 모양을 가진 3D 프린트 추상 조각품,
  단순한 베이스에 올려진 연한 회색 플라스틱으로 만들어주세요"""

!python launch.py --config custom/threestudio-mvdream/configs/mvdream-sd21.yaml \
--train --gpu 0 system.prompt_processor.prompt="$prompt" seed=42
```

나는 프롬프트를 정의하고 launch.py 스크립트를 실행하여, MVDream을 사용하는 구성 파일을 통해 작업을 진행했습니다. 이 작업은 시스템을 "훈련"하는 것을 의미하며, 텍스트 프롬프트를 기반으로 3D 기하학을 정의하는 체크포인트 파일을 생성하기 위해 최적화 루프를 실행합니다. 일관된 출력을 얻기 위해 시드값을 42로 설정했습니다. 시드 숫자를 변경하면 다양한 변형이 생성됩니다. 이 스크립트 실행에는 약 40분이 걸렸습니다.

## 훈련된 체크포인트에서 3D 매쉬 생성하기

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

학습 최적화를 실행하는 동안 시스템은 형성 중인 모양의 상태를 보여주는 이미지를 렌더링합니다. 여기는 프롬프트에서 나온 결과물 "간단한 기단에 놓인 연한 회색 플라스틱의 지오메트릭 모양이 있는 3D 프린트 추상 조각"입니다.

![3D object](/assets/img/2024-05-23-MoldingtheImaginationUsingAItoCreateNew3D-PrintableObjects_6.png)

와우, 이거 정말 멋지네요! MVDream을 사용한 threestudio 시스템은 안전하게 놓인 세 개의 삼각형 형태가 기둥 위에 쌓인 조각을 만들어 냈습니다. 표면의 질감은 낡은 느낌이 듭니다.

## 3D 객체 출력

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

다음 단계는 형태를 정의하는 3D 메쉬를 내보내는 것입니다. 이 과정에서 사용한 명령어는 다음과 같습니다.

```js
!python launch.py --config "$save_dir/../configs/parsed.yaml" --export \
--gpu 0 resume="$save_dir/../ckpts/last.ckpt" \
system.exporter_type=mesh-exporter \
system.geometry.isosurface_method=mc-cpu \
system.geometry.isosurface_resolution=256 \
system.exporter.save_texture=False system.exporter.fmt=obj
```

이 Python 스크립트는 이전에 생성된 체크포인트 파일을 사용하여 내보내기 명령을 실행합니다. 메쉬 내보내기 도구를 사용하도록 지시했습니다. "MC" 방법은 렌더링의 "마칭 큐브" 방법으로, 더 자세한 모델을 얻기 위해 더 높은 해상도의 메쉬를 생성합니다. 또한 텍스처 매핑이 아닌 OBJ 파일만을 원하는 것을 표시했습니다. 이 단계를 실행하는 데 81초가 걸렸습니다. 이곳에 블렌더에서 렌더링된 결과 3D 메쉬가 있습니다.

위의 렌더링과 유사하지만 좀 더 울퉁불퉁하고 아래쪽에 추가 재질이 조금 더 있습니다. Blender에서 메쉬를 정리하여 대부분의 기둥을 제거하고 대신으로 테이퍼된 큐브를 추가했습니다.

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

## 3D 모델 인쇄하기

저는 이 메시를 워터타운의 해치 메이커스페이스에서 Prusa i3 MK3를 사용하여 인쇄했습니다. 메시의 하단에 일반 서포트를 사용했습니다.

위 이미지들은 Cura Slicer에 사용한 설정, 진행 중인 제작과정, 그리고 서포트를 제거하기 전의 완성품을 보여줍니다. 아래는 완성된 물체의 사진입니다.

<img src="/assets/img/2024-05-23-MoldingtheImaginationUsingAItoCreateNew3D-PrintableObjects_7.png" />

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

이것도 잘 나왔어요. 질감이 꽤 울퉁불퉁하고 서포트의 잔해들이 보이지만, 전반적으로 좋아 보여요. 다시 인쇄한다면 빌드플레이트에만 연결되는 나무 서포트를 사용할 것 같아요.

이제 AI를 사용하여 네 개의 3D 오브젝트를 만드는 방법에 대해 안내했으니, 3D 오브젝트 생성 시스템의 사회적 영향과 사용한 시스템의 소유권에 대해 논의하겠습니다.

# 3D 오브젝트 생성 시스템의 사회적 영향

3D 오브젝트 생성 기술의 발전은 기술 분야에서 중요한 한 걸음을 나아가는 것으로, 개인들이 아이디어를 물리적 모델로 변환할 수 있도록 합니다. 상용 및 오픈소스 도구에 의해 용이해진 이 과정은 창의적 표현과 제조에 새로운 차원을 제공합니다. 그러나 그 기회와 함께 사회적 영향을 고려하는 것이 중요합니다. 이러한 기술은 혁신이 긍정적이고 부정적인 결과를 가져올 수 있는 명확한 예시를 제공하며, 사용과 영향을 신중히 고려해야 한다는 필요성을 강조합니다.

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

## Shap-E의 사회적 영향

OpenAI의 논문에서는 개발한 모델의 행동에 영향을 줄 것으로 예상되는 학습 데이터셋의 편향에 대해 논의했습니다. 예를 들어, OpenAI는 텍스트-3D 모델 내의 편향을 조사하기 위해 몸매나 색상과 같은 구체적인 세부사항이 명시되지 않은 모호한 캡션을 제공했습니다. 그 결과, 모델이 생성한 일부 샘플이 모호한 프롬프트에 대한 응답으로 일반적인 성역할 스테레오타입을 나타내는 것을 관찰했습니다. OpenAI의 논문 [1]의 부록 C에서 자세히 알아볼 수 있습니다.

## MVDream의 사회적 영향

MVDream 창조자들은 자신들의 모델의 사회적 영향에 대해 논문에서 논의했습니다.

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

텍스트에서 3D 모델로의 사회적 영향을 탐색하는 과정에서, AI의 혁신 잠재력을 활용하고 도입과 함께 따르는 윤리적, 문화적, 경제적 결과에 대해 신중하게 균형을 맞추는 것이 중요함을 상기시킵니다.

# 3D 객체 생성 시스템의 소유권

소유권은 매체 생성의 중요한 측면으로, 각 도구의 서비스 약관을 심사하여 창조자에게 부여된 권리를 이해하는 것이 필요합니다.

## 중간 사용자의 소유권

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

Midjourney 최근에 서비스를 통해 생성된 이미지의 소유 정책을 변경했습니다. 회사는 사용자가 생성한 이미지를 소유하기 위해 유료 구독을 요구했었지만, 개인 사용자에 대해서는 이 정책이 완화되었습니다. 아래는 업데이트된 정책 내용입니다.

따라서, 개인 사용자는 자신이 생성한 이미지를 소유하게 됩니다. 그러나 매출이 100만 미국달러 이상인 회사에서 근무하는 경우에는 이미지 소유를 위해 매월 60달러를 지불해야 합니다. 가격에 대한 자세한 내용은 여기를 참조하세요.

## 3dMaker.AI 사용자의 소유권

3dMaker.AI를 사용할 때의 소유권은 간단합니다. 사이트 FAQ에는 “3dMaker.AI에서 생성된 모델은 100% 당신의 것입니다.” 라고 명시되어 있습니다. 이를 해석하기 위해 로펌을 고용할 필요가 없죠! 😊

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

# 요약

나의 프로젝트에서는 상용 및 오픈 소스 AI 도구를 사용하여 아이디어를 3D 프린트 가능한 객체로 변환하는 실험을 진행했습니다. Midjourney를 통해 2D 이미지를 생성하고, 이를 3dMaker.ai로 3D 모델로 변환하는 것으로 시작하여, 디지털 상상에서 물리적 창조물까지의 창조 과정을 탐구했습니다. Shape-E, MVDream, threestudio와 같은 오픈 소스 모델은 직접 텍스트를 3D로 변환할 수 있게 하여 가능성을 더욱 확장시켰습니다.

이 과정에는 Blender에서 생성된 모델을 개선하고 출력을 준비한 후 지역 도서관의 3D 프린터를 사용하여 실제로 만들어내는 과정이 포함되었습니다. 이 여정은 AI 및 3D 프린팅 분야의 기술적 진보를 선보이며, 이러한 신흥 도구의 사회적 영향을 고려하고 소유권 권리를 이해하는 중요성을 강조했습니다.

3D 객체를 만들고 출력하는 과정을 통해, 프로젝트는 분야에서의 혁신과 접근성의 조화를 강조하며, 창의적 과정에서 AI를 사용함으로써 윤리적 및 실용적 영향을 고민하게 했습니다.

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

# 소스 코드

내가 이 프로젝트의 소스 코드를 GitHub에 올렸어. 그리고 3D 디자인은 Sketchfab와 Thingiverse에 올렸어. 나는 코드와 디자인을 Creative Commons Attribution Sharealike 라이센스에 따라 공개하고 있어.

![image](/assets/img/2024-05-23-MoldingtheImaginationUsingAItoCreateNew3D-PrintableObjects_8.png)

# 감사의 말

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

이 기사를 검토하고 피드백을 제공해 준 Jennifer Lim에게 감사드립니다. 또한 이 기사에 사용된 오브젝트를 인쇄하는 데 도움을 준 Waltham Public Library Makerspace 스태프, Watertown Free Public Library의 Hatch Makerspace, 그리고 Woburn Public Library 스태프에게도 감사드립니다.

# 참고문헌

[1] Heewoo Jun과 Alex Nicho, Shap·E: 조건부 3D 암시적 함수 생성 (2023)

[2] Yichun Shi 외, MVDream: 3D 생성을 위한 다중 뷰 확산 (2023)

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

[3] Ying-Tian Liu et al., threestudio: a modular framework for diffusion-guided 3D generation (2023)

[4] Rombach et al., High-Resolution Image Synthesis with Latent Diffusion Models (2022)

# 부록 A: 3D 갤러리

본문에 나온 3D 객체입니다. 여기서 상호작용하거나 STL 파일을 다운로드해보세요.

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

## Figure 8 조각

## 기둥 위의 돌고래

## 로우 폴리 큐비스트 헤드

## 삼각형을 활용한 추상적인 기하학적 조각
