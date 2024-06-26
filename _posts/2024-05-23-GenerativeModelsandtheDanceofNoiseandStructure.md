---
title: "생성 모델과 소음과 구조의 댄스"
description: ""
coverImage: "/assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_0.png"
date: 2024-05-23 18:37
ogImage:
  url: /assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_0.png
tag: Tech
originalTitle: "Generative Models and the Dance of Noise and Structure"
link: "https://medium.com/towards-data-science/generative-models-and-the-dance-of-noise-and-structure-e72fe7494f4f"
---

르네상스 이탈리아의 주민들이, 인간 상상력과 이성에 대한 열정으로 불타는 현재 기술에 가장 놀라워했을만한 것은 무엇일까 고민하는 것을 즐깁니다. 비행 기계를 꿈꾸던 레오나르도 다빈치는 분명 공중을 유유히 달리는 Airbus 380을 볼 때 굳이 확카해 편안하게 의자에서 영화를 보며 와이파이 속도가 충분하지 않다고 불평하는 탑승자들을 볼 때 깊은 감명을 받았을 겁니다.

하지만 중세 시대에 마술처럼 보였을 기술 중에서도 제너레이티브 인공지능의 기이함이 그중에 상위에 속할만 합니다. 수십 년간 노고 끝에 만들어낸 '모나 리자' 초상화를 살펴온 레오나르도가 그의 스타일로 여성 초상화를 몇 초만에 그릴 수 있는 장치를 보여준다면 어떻게 반응했을지 상상해보세요. 보세요:

<img src="/assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_0.png" />

솔직히 말하자면, 이 여성은 실제 몬나 리자만큼 매혹적이고 신비로울 정도로 미소를 머금지 않습니다(심지어 더 자세히 관찰하면 다소 우스운 것 같습니다). 하지만 많은 사람들은 AI 생성물의 놀라운 사례를 만나봤습니다: 초실감 이미지부터 AI로 작성된 목소리의 믿기 힘든 딥 페이크까지 말이죠.

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

생성적 AI 모델은 꿈꾸는 사람들의 실리콘 버전입니다: 아무것도 없는 상황에서 무언가를 상상할 수 있으며, 소음으로부터 의미를 찾아냅니다. 그들은 질서와 무질서의 춤을 춰내는 법을 배웠습니다. 이미 인간의 창의성에 대한 우리의 생각을 변화시키고, 수천 가지의 새로운 응용 프로그램으로 문을 열었으며, 전통적인 산업들을 위협하고 새로운 산업을 만들어냈습니다.

그리고 우리는 이제 막 시작에 불과합니다. 대부분의 이러한 모델들은 아직 발전 초기 단계에 있습니다. ChatGPT의 글, DALL-E 및 Midjourney의 이미지, 그리고 최근에 Stability AI의 StableAudio와 같은 음악용 생성 모델들을 통해, 우리는 하루에 뇌에 입력되는 감각 신호의 많은 부분이 어떤 식으로든 AI에 의해 변경되거나 완전히 생성된 시대를 살아가고 있습니다.

![이미지](/assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_1.png)

본 기사에서는 이 마법 같은 블랙 박스의 뚜껑을 열어, 생성 모델의 여러 클래스의 기본 메커니즘(Helmholtz machines, Variational Autoencoders, Normalizing Flows, Diffusion Models, GANs 및 Transformer 기반의 언어 모델)에 대해 탐구하고, 그들의 내부 작동 원리를 밝히며, 뇌과학과 인지학에 대한 기원과 연결을 탐구합니다. 이 주제는 물론 한 기사로 다루기에는 너무 방대하기 때문에(계획대로 많이 커졌지만), 기술적인 세부 정보와 고수준 개요, 일관된 내러티브, 그리고 참고 자료를 균형있게 제공하려고 노력하여, 모두에게 흥미로운 내용이 될 것으로 기대합니다.

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

어디서부터 시작할까요?

많이 인용된 파인만의 명언으로 시작하는 것은 다소 진부할 수 있지만, 그의 주장에는 이치가 있습니다: 이해는 창조 행위와 관련이 있으며, 머신 러닝 초기부터 이해할 수 있는 모델을 만드는 것은 창조할 수 있는 모델을 만드는 것과 관련이 있었습니다. 튜링의 유명한 테스트 (이른바 모방 게임이라고도 함)는 이 아이디어의 변형으로 볼 수 있습니다: 지능을 속이는데 성공적이라면, 실제 것과 유사한 것을 발견했을 가능성이 높습니다.

가장 중요한 초기 생성 모델 중 두 가지는 볼츠만 머신과 헬멀홀츠 머신입니다.

헬멀홀츠 머신은 특히 흥미롭습니다. 그 원칙은 독일 물리학자 헤르만 폰 헬멀홀츠의 미래를 예언하는 비전과 관련이 있습니다. 헬멀홀츠는 19세기 말에 지각이 객관적인 현실의 객관적 반영보다는 감각 데이터와 사전 지식으로부터의 무의식적 추론 과정으로 더 잘 설명된다는 것을 깨달았습니다. 인식은 본질적으로 확률적이며 잡음에 영향을 받으며, 우리의 기대와 편향으로 강력하게 형성됩니다. 그의 아이디어는 현대 신경과학에서 점점 더 중요해지고 있는데, 칼 프리스턴의 자유 테너지 원리(헬멀홀츠 머신을 영감의 근원으로 명시적으로 언급) 및 베이지안 뇌 가설을 통해 그 영향이 커지고 있습니다.

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

![Generative Models](/assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_2.png)

베이지안 관점에서, 뇌는 세상의 생성 모델 p(x,z)을 유지합니다. 여기서 x는 감각 관측이고 z는 뇌가 포착하려는 해당 감각 관측의 숨겨진 원인/잠재적 설명입니다. 세상과 세상의 모델에 대한 불확실성을 반영하는 것이며, 이를 보겠지만 모든 생성 모델은 확률적 잠재 변수 모델로 수립됩니다.

베이지안 언어로, 이러한 모델을 가정할 때, 이것은 잠재 원인에 대한 사전 분포 p(z) (뉴욕에 살 경우 사자를 관측할 기대가 개를 관측할 기대보다 작음), 관측치 p(x)에 대한 전체 가능성, 그리고 감각 관측과 숨겨진 원인 간의 관계로 요약됩니다.

x와 z 간의 관계를 분석하는 것이 생성 모델링의 핵심입니다.

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

중요한 두 가지 양 름은 사후 확률인 p(z∣x)와 가능도 p(x|z)로 나타나며, 이들은 베이즈의 유명한 법칙에 따라 서로 연관되어 있습니다.

우리는 관찰된 값을 기반으로 한 숨겨진 원인의 확률인 사후 확률 p(z∣x)을 얻습니다. 일반적으로 이에 접근할 수 없는데, 이는 계산 문제 때문인데, 베이즈의 법칙에 따르면 이를 계산하기 위해 p(x)가 필요하며, 이를 계산하려면 모든 가능한 숨겨진 원인을 찾아서 x를 어떻게 설명할지 확인해야 합니다.

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

세계 모델이 복잡한 경우, 이들은 고차원 적분이므로 효율적이지 않거나 아예 불가능할 수 있습니다.

사후 확률 추론은 많은 생성 모델의 근본적인 도전 과제입니다.

헬름홀츠 머신에서 사후 확률 p(z∣x)은 인식이라는 프로세스에서 데이터에서 직접 추정되며 근사 사후 확률 q(x|z)을 배우고 이를 가능한 한 진짜 p(z∣x)에 가깝게 일치시킴으로써 추정됩니다.

우도 p(x|z)를 추정하는 역방향은 보통 훨씬 쉽습니다. 특정 잠재 변수 z가 주어지면, 단지 우리에게 관찰 x가 얼마나 가능성 있는지만 알려줍니다. 이를 위해 일반적으로 어떤 적분도 필요하지 않고, 단순히 모델을 순방향으로 실행할 수 있습니다.

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

가능성은 생성 네트워크에 의해 매개변수화됩니다: 어떻게하면 z가 주어졌을 때 x를 생산할 수 있을까요? 숨겨진 원인으로 시작하면, 세계에 대한 영향은 어떻게 보일까요? 사람을 상상해보면, 그 사람의 얼굴이나 목소리는 어떻게 보일까요?

대부분의 생성 모델에서, 이것이 실제로 가장 관련이 있는 부분입니다 (이미지/텍스트/오디오를 생성함으로써). z에서 x로의 매핑이 어떻게 생겼는지 아는 즉시, z를 샘플링하고 생성 네트워크를 통해 보내어 샘플을 생성할 수 있습니다.

헬름홀츠 머신에서 이 두 방향은 신경 네트워크를 통해 매개변수화되며, 이는 인지 네트워크를 사용하여 생성된 샘플을 실제 세계 (깨어 있는 상태)와 비교하고 자체 창조물을 잠재적 상태로 다시 매핑하는 것 (꿈의 상태)을 변환하는 인간 인지와 유사한 프로세스에서 영감을 받은 비교기반 깨어 있는-잠자는 알고리즘에 의해 번갈아 학습됩니다.

인지 네트워크 z ← x: q(z|x)
생성 네트워크 z→ x: p(x|z)

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

잠재 공간의 구조는 학습된 모델의 해석을 돕는 데 종종 도움이 됩니다. 잠재 표현을 분리하고 해석 가능한 특징과 맞추는 것은 많은 실용적응용에서 관심을 끄는 것뿐만 아니라 보다 해석 가능한 모델을 얻기 위해서 더 일반적으로 중요합니다.

친숙한 예로, 인간 얼굴 이미지의 생성 모델을 구축한다고 가정해 보겠습니다. Helmholtz 기계의 구조를 따라 이미지를 잠재 공간에 매핑합니다. 그런 다음 해당 잠재 공간에서 흥미로운 변화의 축을 발견해 볼 수 있습니다.

흥미로운 변화의 축 중 하나는 이미지에 표시된 사람의 나이와 관련이 있을 수 있습니다. 그런 다음, 잠재 공간에 제약 조건을 부여할 수 있습니다(나이별로 레이블이 지정된 데이터를 제공하여 지도 학습 설정에서 또는 나이를 식별하여 학습된 잠재 특징 내에서, 중요한 변화로 이어진다고 가정하는 비지도 학습 설정에서), 그래서 그 중 하나의 방향인 z_age가 이미지에 표시된 나이를 인코딩하도록 할 수 있습니다.

이러한 방향을 알면 이미지의 나이를 변경하는 데 사용할 수 있습니다. 또 다른 방향인 z_beard가 수염을 인코딩한다고 가정해 볼 때, 모델을 사용하여 이미지 x를 인코딩하고 an z를 얻은 다음, z를 z'=z+a*z_age+b*z_beard로 변환하여 전달합니다. 그리고 생성 모델 p(x|z')를 통해 다시 보내어 수염이 있는 노인 버전의 나를 볼 수 있습니다.

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

OpenAI의 GLOW와 같은 모델을 사용하면 해당 웹사이트에서 놀 수 있지만, FaceApp과 같은 응용 프로그램을 이미 알고 계실 가능성이 높습니다.

모든 생성 모델링은 이와 더나은 관련이 있는 변형 버전으로 귀결되며, Helmholtz 기계 시대 이후로 상당히 발전해 왔지만, 데이터의 기본 구조를 확률적 프레임워크에서 포착하고 재현하는 기본 아이디어는 유지되고 있습니다. 이제 이러한 개념을 사용하여 지난 10년간 AI 연구의 주목을 받은 생성 모델의 일부 일반적인 버전을 설명하겠습니다.

## 변이 오토인코더 (VAEs)

VAEs는 Kingma와 Rezende에 의해 2013년에 동시에 제안되었으며 (소음 제거, 압축, 시계열 데이터까지 다양한 응용 분야에서 발견됨).

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

시작하기에 가장 자연한 장소입니다. 헬름홀츠 머신과 정신적으로 가장 유사하기 때문입니다: 인식(인코더) 및 생성(디코더) 네트워크를 모두 사용합니다. 이미 언급했듯이, 인식 네트워크는 근사 밀도 q(z|x)를 통해 사후 밀도 p(z|x)를 근사합니다.

![image](/assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_5.png)

VAE는 음의 Evidence Lower Bound (ELBO)를 최소화하도록 훈련되어 있습니다. 이는 가능한한 참 사후 분포 p(z|x)에 가까운 근사 밀도 q(z|x)를 찾는 것으로 정리됩니다.

여기서는 간단히 분포 q(z|x)를 매개변수화하여 그래디언트 기반 메서드를 통해 훈련 가능하도록 합니다. 분포를 매개변수화하는 데는 다양한 세부사항이 존재할 수 있지만, 대략적으로 가우시안 근사로 간주하는 경우가 많습니다. 이는 평균 μ와 공분산 σ를 가지며 모델의 자유 매개변수를 구성합니다. 이 값은 신경망에 의해 직접 학습됩니다 (훈련 데이터 x를 NN에 넣으면 출력이 μ가 됩니다).

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

VAEs에서는 근사 사후 분포 q(z|x)를 사용하여 하나 또는 여러 개의 랜덤 샘플을 추출하고, 이를 ELBO에 대입합니다. ELBO는 q에서의 샘플로부터의 기댓값으로 정의됩니다.

![image](/assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_6.png)

(음의) ELBO는 손실을 구성하므로 우리는 그래디언트를 계산할 수 있고, 그 결과 경사 하강법이 마법을 부리게 됩니다.

사후 분포는 상당히 복잡할 수 있으므로, 잘 행동하는 그래디언트를 계산하는 것이 실제로 어려울 수 있습니다. 그들은 종종 높은 분산을 가지며 많은 샘플이 필요합니다. VAE의 핵심인 재모수화 기술은 이 문제를 빠르고 똑똑하게 우회하도록 도와줍니다. 샘플링을 두 개의 프로세스로 나누는 것입니다.

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

- 먼저, 표준 가우시안 N(0,1)에서 샘플 ϵ을 추출합니다.
- 다음으로, q(z∣x)의 평균 μ와 표준 편차 σ를 사용하여 ϵ을 변환하여 샘플 z=μ+σ×ϵ를 얻습니다.

리파라미터화 트릭에서 특히 우아하다고 생각하는 점은, 이것이 각 생성 프로세스의 두 가지 핵심 구성 요소를 분리한다는 것입니다. 그것이 일상적인 손글씨 숫자를 생성하는 것일 수도 있고, 성경 인용구에서 하늘과 땅을 창조하는 메타포적인 것일 수도 있습니다: 초기 샘플 ϵ의 "형태 없고 텅 빈" 노이즈로부터 주어지는 무작위 구성 요소가 복잡한 변환을 통해 의미를 얻고, 마침내 복호기를 통해 관측된 세계에서 패턴 x를 만듭니다.

![image](/assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_7.png)

직관적으로 ELBO는 재구성 항과 엔트로피 항으로 구성됩니다. 정보 이론의 세계에서 엔트로피는 정보 내용의 예측 불가능성이나 무작위성을 측정하므로, 엔트로피는 훈련을 자연스럽게 정규화하며 최적화하는 동안 구조와 노이즈를 교환합니다. VAE가 재구성에 너무 많은 초점을 맞추면 데이터를 지나치게 캡처하여 잠재 공간에서 훈련 데이터의 모든 작은 세부 사항(노이즈 포함)을 잡아낼 수 있습니다. 그러나, 엔트로피에 너무 많은 가중치를 부과하면 데이터의 세세한 점들을 캡처하지 못할 너무 단순한 잠재 공간에 빠질 수 있습니다.

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

근사 사후의 엔트로피는 그 공분산 구조 σ와 관련이 있습니다. 이는 초기 표본에서 남아 있는 "형태 없는 그리고 빈" 잡음(우리의 설명에 대한 불확실성을 인코딩한)의 얼마만큼이 남아 있는지 측정해줍니다. 전체 과정을 결정론적으로 만들고 싶다면, σ를 단순히 0으로 설정하여 모든 불확실성을 제거할 수 있습니다.

결정론적 우주에서는 진짜 잡음이 없습니다. 단지 우리의 모델이 포착하기에 충분히 강력하지 않은 것이거나 단순히 필요한 정보가 부족한 것만 있을 뿐입니다(잡음을 좋아하며 여기에서 이에 대한 전체 기사를 작성했습니다). George Box가 언급한 것처럼: "모든 모델은 틀렸으며, 어떤 것은 유용하다", VAE는 과신과 미신 사이의 균형을 찾아가는 법을 배우게 됩니다.

이 구조화 원칙은 VAE가 차원 축소(입력 데이터에 포함된 중요한 정보와 중요하지 않은 정보를 구분하는) 및 노이즈 제거와 같은 작업에서 자연스럽게 뛰어나게 성과를 내는 이유를 설명해줍니다. 앞에서 언급한 바와 같이, VAE는 잠재 공간의 구조화된 표현을 얻어 해석 가능한 특징을 이끌어내기도 합니다.

![이미지](/assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_8.png)

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

## 정규화 흐름 (NF)

누군가가 NF를 "스테로이드를 이용한 재매개화 트릭"이라고 표현한 적이 있는데, 이 설명을 정말 좋아합니다. 정규화 흐름은 VAE가 끝낸 곳을 맡아 인식을 흐름의 적용으로 정의합니다.

NF는 간단한 확률 분포를 반복적으로 변형하여 복잡하고 정교한 분포로 바꾸는 방식으로 작동합니다. VAU의 경우와 마찬가지로 NF는 보통 표준 가우스 분포인 N(0,1)를 시작으로 역변환 가능한 변형들을 통해 더 복잡한 형태의 분포로 변환합니다.

![NF Image](/assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_9.png)

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

VAE (Variational Autoencoders)는 고정된 분포와 학습된 변환(평균 및 분산)을 사용하여 난수와 구조를 분리합니다. NF (Normalizing Flows)는 동적으로 분포 자체를 조성합니다. 이는 Jacobian determinant를 추적하여 수행됩니다. 이것은 변환의 부피 변화를 나타냅니다. 즉, 공간이 얼마나 축소되거나 늘어나는지 확인하여 전체 잠재 공간이 일관된 방식으로 변형되도록합니다.

VAE의 경우와 마찬가지로, 형태가 없는 덩어리가 형태로 변형됩니다.

NF에 대해 멋진 두 가지는 다음과 같습니다: 역변환이 가능하므로 두 분포 간의 양방향 매핑을 허용합니다. 이것은 많은 상황에서 유용할 수 있습니다. 예를들면, 밀도를 추정하려고 할 때 (특히 다시 표준 가우시안 N(0,1)로 매핑하면 이전에 연구되지 않은 사후확률보다 계산이 쉬워질 수 있습니다.), 또는 이상 감지를 위해 데이터를 걸러내는 경우, 학습된 분포에서 낮은 가능성을 가진 데이터를 걸러냅니다.

저는 앞서 언급한 OpenAI의 GLOW는 또한 이 역변환이용해 잠재 공간에서 미소, 나이 또는 수염 등과 같은 특징을 조작하고 거의 실시간으로 변형된 이미지를 얻습니다.

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

두 번째 멋진 점은 다양한 기하학과 매니폴드에 대한 적응력입니다. 고전적인 예로는 구 형태에 적용되는 것이 있습니다. 이를 통해 NF(정규화 흐름)는 구 위에 존재하는 잠재적 표현을 형성할 수 있습니다. 어떤 주장에도 불구하고 지구는 아마도 구형일 것이므로, 구 형태는 지구의 날씨 시스템 시뮬레이션을 실행할 때 매우 유용합니다.

## 확산 모델

계속해서, 확산 모델은 최근 몇 년간 가장 성공적인 생성 모델 중 하나입니다. 이미 2015년에 Sohl-Dickstein 등에 의해 제안되었지만, 이미지 생성에서의 성공으로 인해 DALL-E, Midjourney 또는 Stable Diffusion을 위한 기초를 마련했습니다. 기본 아키텍처는 매우 다르지만, 개념적으로 VAE(변분 오토인코더) 및 정규화 흐름과 연관이 있습니다.

확산 모델은 생성 과정을 여러 단계로 분할합니다: 각 단계마다 학습 샘플에 노이즈가 적용됩니다. 모델의 목표는 해당 노이즈를 샘플에서 제거하는 방법을 학습하는 것입니다. 이전에 충분히 분명하게 했는지 모르지만, 이 노이즈는 확산 모델에서 다시 주요 역할을 수행합니다.

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

훈련 중에는 훈련 데이터에 반복적으로 노이즈가 추가됩니다. 이미지의 예시를 사용하면, 모델은 소음의 작은 수준을 제거하고 최종 세부 정보를 다듬는 방법을 배우거나, 왜곡된 이미지에서 모호한 모양을 명확히 하는 방법을 익힐 수 있습니다:

![image](/assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_10.png)

인식 과정은 인식 네트워크를 통해 직접적으로 모델링되지 않지만, 훈련 목표가 상당히 변경되는 동안, 노이즈 추가와 이에 따른 노이즈 감소 여부의 모니터링은 인식의 한 형태로 볼 수 있으며, 초기 노이즈 샘플은 p(z_0)에서 추출된 초기 상태로 간주될 수 있습니다.

새로운 샘플을 생성할 때, 모델은 순수한 노이즈로 시작하고, 노이즈 아래에 숨겨진 것이 무엇인지 이해하려고 시도할 때 새로운 것을 얻을 수 있습니다:

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

![Generative Models Image](/assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_11.png)

암튼, 아무것도 없는 상황에서 무언가를 환각하는 과정은 그것이 훈련된 데이터의 암시적으로 학습된 분포를 드러낸다.

확산 모델이 왜 그렇게 잘 작동하는지는 여전히 논의 중입니다. 이와 관련하여 에너지 기반의 연상 메모리 모델(Hopfield 네트워크를 통해 40년 전에 유명해짐)과 비교되었습니다.

또한 확산 모델은 Song 등에 의해 인기를 얻는 점수 기반 생성 모델링의 아이디어와 관련이 있습니다: 데이터 가능성을 직접 계산하는 전통적인 방법과 달리, 이러한 모델은 데이터 가능성의 기울기를 나타내는 점수를 근사화하는 데 중점을 둡니다.

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

직관적으로, 점수는 샘플이 더 가능성 있는 방향으로 변경되어야 하는지를 나타냅니다. 직접적으로 가능성을 계산하지 않고, 이러한 모델들은 종종 이전에 마주한 일부 계산적 도전 과제를 피하게 됩니다.

점수 함수는 다시 신경망으로 모델링될 수 있습니다. 이것을 나타내는 특히 흥미로운 방법 중 하나는 확률적 미분 방정식(SDEs)을 통해 나타내는 것입니다. 이는 Neural ODE와 유사하게, 신경망을 통해 미분 방정식을 나타내는 것입니다 (암시적 레이어라고도 알려져 있습니다).

이것은 확산 모델의 연속 시간 버전과 유사합니다. 노이즈로부터 시작하여, 점수 함수는 가능성 있는 샘플로 이동하도록 이끕니다 (이 기술을 개발하는 Stefano Ermon 박사의 연구실은 훌륭한 강의를 진행하며, 자세한 내용은 여기에 설명이 있습니다).

확산 모델에서 생성 과정은 또한 확률적이며, 각 단계에서 확률적 구성 요소를 추가합니다. 생성 프로세스가 여러 단계로 분할되기 때문에, 이는 사슬을 여러 단계 뒤로 되돌아가 과정을 다시 진행하여 샘플의 약간의 변형을 도입할 수 있게 합니다.

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

![Image 1](/assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_12.png)

일부 확산 모델 응용 프로그램(예: DALL-E 또는 Midjourney)에서 초기 상태는 순수한 무작위 샘플 z0이 아닌 시각과 언어 간의 공동 임베딩 p(z0|x)에 의해 제공됩니다. 여기서 x는 강력한 CLIP(대조적 언어-이미지 사전 훈련) 임베딩을 통해 제공된 예를 들어 텍스트 입력일 수 있습니다.

조건부 생성은 다양한 감각 모달리티를 일관된 프레임워크로 결합하기 때문에 모든 종류의 멀티모달 학습 설정에서 가치가 있습니다. 아마도 AI의 가장 흥미로운 발전 중 일부에서 중요한 부분이 될 것입니다.

![Image 2](/assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_13.png)

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

## 생성적 적대 신경망 (GANs)

GANs은 최근 10년간 가장 인기 있는 생성 모델 클래스 중 하나로, Ian Goodfellow와 친구들이 술 마시던 밤에 영감을 받아 만들어졌다.

GANs는 헬름홀츠 머신의 이중 네트워크 구조에서 더욱 멀어진다. 앞에서 말했듯이, p(z|x)를 근사하는 것이 생성 모델의 중심적인 과제인 경우가 많아서 GANs는 단순히 인식을 무시하려고 한다.

GANs는 p(z0)에서 무작위 노이즈 벡터를 추출하여 시작한다 (확산 모델에서처럼, 이 초기 벡터는 텍스트와 같은 다른 정보에 의해 조건이 달릴 수도 있다). 하지만, 이후에는 생성 네트워크만 학습하며 (여러 응용 프로그램에서 주로 관심을 두는 부분이기 때문에), 디스크리미네이터를 포함하여 훈련하고, 생성 모델에서 p(x|z)의 샘플을 훈련 데이터의 예제와 일치시키려고 노력한다.

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

생성 네트워크는 판별자를 속일 수 있는 데이터를 생성하도록 훈련됩니다. 이에 반해 판별자는 실제와 가짜 샘플을 구별하는 데 훈련됩니다.

GAN의 우아함은 이 경쟁적 상호 작용에 있습니다: 생성자는 판별자로부터의 피드백에 따라 데이터 생성 능력을 향상시키는 동안, 판별자 자체는 실제와 가짜를 구별하는 데 더 뛰어나게 됩니다. 이는 완벽한 제로섬 게임이며, 양쪽 네트워크를 계속해서 더 나아지도록 밀어줍니다 (여기에서 딥 페이크 감지에 대해 썼던 특정한 위험 요소들이 있습니다).

그러나 GAN은 독특한 과제를 안고 있으며, 특히 최근에 경쟁 모델인 확산 모델의 성공으로 인해 일부 인기를 잃은 것으로 볼 수 있습니다. GAN을 훈련하는 것은 일반적으로 불안정합니다. 생성자가 처음에 낮은 품질의 샘플을 생성하면, 판별자의 역할이 너무 쉬워져 생성자가 향상되기 어려워집니다. 반면, 판별자가 너무 강력해지면, 생성자의 성장을 저해하여 모드 붕괴로 이어질 수 있습니다. 이는 생성자가 가능한 출력의 일부만 생성하게 되는 현상을 의미합니다.

## 트랜스포머와 대형 언어 모델 (LLMs)

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

텍스트의 생성 모델링 환경을 완전히 혁신한 Transformers에 대해 언급하지 않을 수 없네요.

간단히 설명하면, 거의 모든 LLM은 유명한 2017 구글 논문에서 self-attention을 구현한 transformer 아키텍처의 변형에 기반을 두고 있어요. 이 구조는 입력 시퀀스 간의 복잡한 관계를 학습할 수 있게 해줍니다. 이는 텍스트에 특히 잘 작동합니다.

BERT와 같은 Transformer 변형들은 가려진 언어 모델링 설정에서 훈련됩니다. 일부 토큰이 가려지고 가려진 토큰을 인식할 수 있도록 훈련됩니다. 많은 다른 곳에서 설명되어 있겠지만, 이 아키텍처를 통해 LLM은 입력 시퀀스 간의 복잡한 관계를 학습할 수 있습니다.

생성 관점에서 transformer 기반 LLM은 각 단어 또는 구문이 이전 단어를 따라 나올 확률을 모델링합니다. 이는 다시한번, 입력 프롬프트에 종속적인 확률 분포 p(x|z)의 변형을 나타냅니다.

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

그러나 대개 Transformer에는 명시적으로 숨겨진 변수 z가 없습니다. 왜냐하면 프롬프트와 맥락 자체가 이미 단어입니다. 대신, self-attention 메커니즘은 관측된 모든 단어 (x1, x2, ..., xt)에서 토큰 p(x_i|(x1,x2,…,xt))의 확률을 추출하며, 물론 훈련 데이터의 수십억 줄에서 보여진 모든 단어와 맥락에 대한 암묵적인 분포에서도 추출합니다.

노이즈는 Transformer 훈련의 직접적인 부분이 아니지만, LLMs는 자연스럽게 확률 구성 요소를 포함합니다. 이는 언어가 고유하게 결정되지 않기 때문에 매우 합리적입니다 (따라서 Markov 모델은 원래 러시아 시에 기초를 둔 것으로 개발되었으며, 이에 대해 자세히 논의하였음). 동일한 단락이 많은 다른 방식으로 표현될 수 있으므로, 응답 또는 샘플 생성 시, 주어진 맥락과 잘 맞는 여러 단어가 일반적으로 있으므로 가능한 계속 p(x_i|(x1,x2,…,xt))에 대한 분포가 있습니다.

다른 단어를 선택하는 확률은 이른바 온도 하이퍼파라미터로 조정할 수 있습니다. 창의적이거나 결정론적인 응답을 찾고 있는지에 따라, 이 매개변수에 의해 효과적으로 노이즈 수준을 제어할 수 있습니다. Chat-GPT와 같은 LLM은 응답 중에 특정 온도를 요청할 수 있게 해줍니다.

Chat-GPT가 저에게 높은 온도 설정으로 이 단락을 다시 말해 주었습니다: "Transformer의 소용돌이치는 은하에서 노이즈가 주연이 되지 않지만, LLMs는 언어의 불확실한 비트에 맞춰 춤을 추고 있습니다. 답변을 만드는 것은 한 마디의 진기한 단어를 찾는 것이 아니라 다양한 단어적 멜로디 p(x_i|(x1,x2,…,xt))를 이용해 잼을 더하고 있는 것입니다."

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

고온은 ChatGPT가 말 그대로 고조 되는 것처럼 들리게 만듭니다. 여기서의 "고온"은 볼츠만의 열역학 통계학 형식에 대한 비유로 사용됩니다. 이는 시스템의 상태가 온도와 상태의 에너지에 따라 지수 분포를 따른다는 것을 가정합니다:

![image](/assets/img/2024-05-23-GenerativeModelsandtheDanceofNoiseandStructure_14.png)

Transformer와의 비유는 우연이 아닙니다. 소프트맥스 함수는 키(key)와 쿼리(queries) 사이의 얻은 스케일링된 점곱 점수를 확률로 매핑할 때 self-attention 메커니즘에서 사용됩니다. 소프트맥스는 볼츠만 분포와 정확히 동일한 기능 형태를 가지며 양 또는 볼츠만 분포의 에너지들에 대한 정규화된 확률 분포로 매핑하는 데 두 경우 모두 사용됩니다.

열역학에서와 마찬가지로 온도는 엔트로피와 불확실성/잡음과 깊게 관련되어 있습니다. 볼츠만 분포에서는 온도가 증가함에 따라 서로 다른 에너지 상태에 대한 확률이 더 균일해집니다. 최대 균일성은 모든 상태가 동일하게 발생하기 때문에 최대 엔트로피로 이어집니다. LLMs에서는 이것이 의미하는 바는 앞으로 가능성이 있는 모든 단어가 동등한 확률로 예측된다는 것입니다. 하지만 고온에서도 생성된 텍스트가 완전히 무작위인 것은 아닙니다. 예시에서 볼 수 있듯이, 더 높은 온도로 조정된 경우라도 가장 가능성 있는 토큰의 선택은 여전히 주로 일관된 언어를 나타냅니다.

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

만약 이 기사로 한 가지 아이디어를 전달했다면, 그것은 노이즈가 모든 생성 모델에서 중요한 역할을 한다는 것입니다. 생성 모델링은 형태가 없는 노이즈를 가져다 구조를 불어넣는 예술입니다.

지난 몇 년 동안 많은 방법이 로마로 이끄는 것을 보여줬습니다. 목표, 데이터 유형 및 거대한 모델 크기(예: 트랜스포머) 및 그래디언트 기반 방법으로 훈련시킬 때 어떤 것이 가장 잘 작동하는지에 대한 현실적인 고려사항에 따라 다양한 모델이 의미를 갖을 수 있습니다.

최첨단 생성 모델의 거대함과 훈련 데이터의 복잡성은 해석에 도전을 초래했습니다. 확산 모델 및 트랜스포머는 잠재 변수 모델로 구성되지 않아 거대한 블랙박스처럼 느껴질 수 있으며, 실제 세계에 미치는 영향에 대한 우려가 증가함에 따라 해석이 크게 지연될 수 있습니다.

그러나 우리는 아직 그 안에 일부 구조를 발견하여 배울 수 있을지도 모릅니다. Max Tegmark 등의 새 논문에서와 같이, LLMs에서 공간 및 시간의 중간 표현을 발견하고 해석 가능한 세계 모델의 발생과 유사한 것으로 만드는 방법을 설명합니다. 다른 사람들은 LLMs의 행동을 이해하기 위해 인지 심리학 도구를 창의적으로 적용하며, 사람의 복잡성을 이해하려는 노력과 유사합니다.

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

최근 팟캐스트 에피소드에서 Marc Andreessen은 제너레이티브 모델이 제너레이티브 모델로부터 제공된 합성 데이터에서 유의미하게 훈련되고 개선될 수 있는지라는 문제를 1조 달러 가치의 문제로 묘사했습니다. 이 핵심적으로 무료 데이터에서 훈련하는 것은 많은 가능성을 제공할 것이며, 훈련 데이터를 비실가로 구성하는 것에 의존하지 않고 제너레이티브 모델을 계속 조정할 수 있는 형태의 '셀프 플레이'를 제공합니다(DeepMind가 AlphaGo와 AlphaFold에서 이미 성공적으로 사용하고 있습니다).

Andreessen은 이 문제를 신호와 잡음 사이의 정보 이론적 관점과 연관시켰는데, 쉽게 말해, 모델에 우리가 입력한 것보다 더 많은 정보가 어떻게 있을 수 있는지를 다룹니다.

제너레이티브 모델은 훈련 데이터에서 본 것만 모방한다는 주장이 사실인가요? 훈련 과정과 모델 정의의 잡음이 훈련 데이터 이상의 일반화로 이어지는 방법에 어떠한 측면에서 기여할까요(최근 무료 점심 정리 이론에 관한 제 글에서 관련 질문들을 고려했습니다)? 결국, 잡음은 일반화를 높이기 위해 머신 러닝 모델에서 널리 사용됩니다.

잡음은 망상과 창의력 둘 다로 이끌 수 있으며, 대체 사실의 망상과 이전에 없던 대체 관점을 창조합니다. 제너레이티브 모델에서는 데이터의 조합 가능성 속에 있는 "정보"가 있음을 주장할 수도 있습니다. 제너레이티브 모델은 이 조합적 공간을 탐색하는 새롭고 매력적인 방법을 제공합니다.

마크 트웨인의 말처럼 이제모 아이디어가 새로운 것이 아니라는 인용문의 정신에서 우리는 다시 한번 성경으로 돌아갈 수 있습니다 (AI 기사에서 두 번 인용할 것으로 예상하지 않았지만):

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

유사한 소음과 구조 사이의 상호작용이 인간의 창의력에서도 일어난다는 주장이 있습니다. 최근에 제가 창의성과 정신적 시각화에 관한 기사에서 탐구한 바에 따르면, 뇌에서 자유롭고 구조화되지 않은 마음의 방황 활동(default mode network이라고 하는 '상상 네트워크'로 Scott Barry Kaufman이 부르는 것)은 종종 가장 놀라운 예술 작품과 천재성 중 일부로 형성되는 자극을 제공할 수 있습니다. 이후 보다 논의된, 집중적인 연습과 기술에 의해 형성되는 것입니다.

가장 혁신적인 예술과 과학 작품들도 이미 우리에게 일부는 익숙한 언어로 이해되어야 합니다. 위트겐슈타인은 언급했듯이, 사적인 언어는 없습니다. 생성 모델은 우리의 언어를 구사하고 우리가 가장 소중히 여기는 모든 사물의 p(x)를 근사하는 방법을 배우고 있으며 소음과 구조를 교환함으로써 이 분포 내 및 그 외의 끝없는 새로운 패턴을 드러내고 있습니다. 이들의 창의력은 우리 자신의 창의력을 영감을 줄 수 있습니다.

생성 모델이 이미 우리의 감각적인 입력을 형성하기 시작하고 있고, 세계와 우리의 마음을 인식하는 방식 및 창의력과 천재성에 관한 사고의 경계를 물어내고 밀어내고 있다는 점을 고려한다는 것은 부분적으로 겁이 나기도 하지만 동시에 흥미로운 일입니다.

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

저는 Chat-GPT가 마치 레오나르도 다 빈치일 것처럼 꿈꾸는 말로 이 기사를 마무리하는 것이 더 좋다고 생각해요:

읽어 주셔서 감사합니다!

제 글이 마음에 드시면, 이야기를 메일로 받아보기 위해 구독해 주세요.
