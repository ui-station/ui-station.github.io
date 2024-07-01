---
title: "2024 최신 PyTorch 치트 시트 "
description: ""
coverImage: "/assets/img/2024-07-01-PyTorchCheatSheet_0.png"
date: 2024-07-01 16:07
ogImage: 
  url: /assets/img/2024-07-01-PyTorchCheatSheet_0.png
tag: Tech
originalTitle: "PyTorch Cheat Sheet ✔"
link: "https://medium.com/@deasadiqbal/pytorch-cheat-sheet-f4bb14a4f541"
---


PyTorch에 대해 알아야 할 모든 것을 배워보세요.

![PyTorch Cheat Sheet](/assets/img/2024-07-01-PyTorchCheatSheet_0.png)

야심차고 데이터 고수! 챗봇, 이미지 인식 소프트웨어, 심지어 자율 주행 자동차 같은 멋진 AI 작품을 만들고 싶나요? PyTorch를 찾아보세요!

PyTorch는 Facebook의 AI 전문가들이 만든 슈퍼 멋진 오픈소스 라이브러리로, 기계 학습(ML)과 심층 학습(DL)에 손쉽게 뛰어들 수 있게 해줍니다. 이 치트 시트는 PyTorch의 기본을 모두 배울 수 있는 한 곳의 쇼핑처가 될 것이며, 금세 기계 학습 마에스트로가 될 수 있도록 도와줄 거에요! ✨

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

# PyTorch 불러오기

필요한 주요 라이브러리를 알려드리겠습니다:

```js
# 핵심 기능을 위한 최상위 패키지 불러오기
import torch

# 신경망 기능 불러오기
from torch import nn

# 함수형 프로그래밍 도구 불러오기
import torch.nn.functional as F

# 최적화 기능 불러오기
import torch.optim as optim

# 데이터셋 함수 불러오기
from torch.utils.data import TensorDataset, DataLoader

# 평가 지표 불러오기
import torchmetrics
```

- import torch: 이게 본 프로그램이에요! PyTorch의 모든 중심 기능을 불러오는데요, 텐서(다차원 배열)를 구축하고 조작할 수 있습니다.
- from torch import nn: 신경망 팬들 주목! 이 라이브러리를 사용하여 강력한 신경망을 만들 수 있는 기본 블록을 제공합니다.
- import torch.nn.functional as F: 멋있게 만들고 싶나요? 이 라이브러리는 새로운 신경망 레이어를 만들기 위한 함수형 프로그래밍 도구를 제공하여 코드에 유연성을 더해줍니다.
- import torch.optim as optim: 신경망을 훈련하는 것은 강아지를 훈련하는 것과 비슷해요 - 어떤 최적화가 필요해요! 이 라이브러리는 다양한 옵티마이저를 제공하여 신경망이 효율적으로 학습할 수 있도록 도와줍니다.
- import torch.utils.data as data: 머신러닝에서 데이터는 중요해요! 이 라이브러리는 데이터셋을 효율적으로 관리하는 도구를 제공하여 훈련을 쉽게 만들어줍니다.
- import torchmetrics: 모델이 얼마나 잘 수행되는지 잘 모르겠다구요? 이 라이브러리는 네트워크의 정확성과 효율성을 평가하는 메트릭 도구상자를 제공합니다.

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

# Tensor 사용하기

텐서는 PyTorch에서 기본적인 구성 요소로, AI 프로젝트용 레고 블록과 비슷한 역할을 합니다! 여기에서는 텐서를 만들고 조작하는 방법을 알아보겠습니다:

```js
# 리스트에서 tensor를 사용하여 텐서 생성
tnsr = torch.tensor([1, 3, 6, 10])

# 텐서 요소의 데이터 유형 가져오기: .dtype
tnsr.dtype # torch.int64가 반환됩니다.

# 텐서의 차원 가져오기: .size()
tnsr.shape # torch.Size([4])가 반환됩니다.

# 텐서의 메모리 위치 가져오기: .device
tnsr.device # cpu 또는 gpu가 반환됩니다.

# 모든 요소가 0인 텐서 만들기: zeros()
tnsr_zrs = torch.zeros(2, 3)

# 무작위 텐서 만들기: rand()
tnsr_rndm = torch.rand(size=(3, 4)) # 텐서는 3행 4열입니다.
```

# 데이터셋과 데이터로더

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

기계 학습에서 데이터는 왕이에요! 그런데 데이터를 모델에 효율적으로 전달하는 것은 다루기 어려운 일일 수 있어요. 그럴 때 데이터셋과 데이터로더가 등장합니다. 믿을 수 있는 드래곤처럼 당신을 지켜줄 거예요.

- 데이터셋 구축: 데이터셋은 깔끔하게 구성된 귀하의 훈련 데이터 모음으로 상상해보세요. 데이터가 판다스 DataFrame에 저장된 경우, TensorDataset()을 사용하여 데이터셋을 만들 수 있어요. 이 함수는 두 개의 인수를 취합니다:

  - torch.tensor()를 사용하여 부동 소수점 숫자의 텐서로 변환된 특성(독립 변수).
  - 부동 소수점 텐서로 변환된 목표(종속 변수).

```js
# TensorDataset()을 사용하여 판다스 DataFrame에서 데이터셋 만들기
X = df[feature_columns].values
y = df[target_column].values
dataset = TensorDataset(torch.tensor(X).float(), torch.tensor(y).float())

# DataLoader()를 사용하여 데이터를 배치로 로드하기
dataloader = DataLoader(dataset, batch_size=n, shuffle=True)
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

# 전처리

신경망에 데이터를 입력하기 전에 몇 가지 준비작업이 필요합니다! 이를 전처리라고 합니다. PyTorch의 카테고리 변수(텍스트 레이블과 같은)를 다루기 위한 멋진 기능이 있습니다:

F.one_hot()을 사용한 원-핫 인코딩

“빨강,” “초록,” “파랑”과 같은 카테고리를 가진 데이터가 있다고 상상해보세요. 컴퓨터는 이러한 단어들을 처리하기 어려울 수 있습니다. 원-핫 인코딩은 이러한 카테고리를 특별한 종류의 텐서로 변환하는 트릭입니다. 이 텐서에는 각 가능한 카테고리에 대해 한 열이 있을 것입니다(이 경우 "빨강," "초록," "파랑"). 각 데이터 포인트에 대해, 해당 카테고리에 해당하는 열에 1이 있고, 나머지는 모두 0입니다.

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

다음은 PyTorch에서 F.one_hot()을 사용하는 방법입니다:

```js
# One-hot encode categorical variables with one_hot()
F.one_hot(torch.tensor([0, 1, 2]), num_classes=3) # 0과 1로 구성된 텐서 반환
```

# Sequential Model Architecture

이제 신경망 아키텍처를 구성해 봅시다! PyTorch의 nn.Sequential 클래스를 사용하면 레이어를 스택하는 것이 매우 쉬워집니다. 아래에서 설명하겠습니다.

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

선형 레이어: 네트워크의 주역! nn.Linear(m, n)을 사용하여 m개의 입력을 받고 n개의 출력을 생성하는 선형 레이어를 만들 수 있어요. 입력값을 가중치와 곱하고 편향을 더한 것으로 생각하면 돼요.

가중치와 편향 엿보기: 선형 레이어의 내부 동작에 궁금해하는가요? .weight를 사용하여 가중치 행렬에 접근하고 .bias로 편향 벡터를 확인할 수 있어요. 이들은 훈련 중에 업데이트되는 내용이에요!

활성화 함수: 네트워크에 복잡성을 더하는 비선형 히어로들이에요. 여기 인기 있는 몇 가지 선택사항이 있어요:

- nn.Sigmoid(): 0과 1 사이의 값을 압축하는 함수로 이진 분류(일대다)에 자주 사용돼요.
- nn.Softmax(dim=-1): 출력값을 합이 1이 되도록 확률로 변환시켜주는 함수로 다중 클래스 분류(다대다)에 이상적이에요.
- nn.ReLU(): 음수 값을 0으로 설정하는 ReLU(Rectified Linear Unit). 소멸 그래디언트를 방지하는 데 도움이 돼요.
- nn.LeakyReLU(negative_slope=0.05): ReLU와 유사하지만 0이 아닌 그래디언트를 위한 작은 양수 기울기를 허용하는 함수에요.

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

Dropout 레이어: 모델이 훈련 데이터를 너무 잘 기억할 때 발생하는 오버피팅을 nn.Dropout(p=0.5)로 극복하세요. 이는 훈련 중에 일정 비율의 활성화를 무작위로 제거하여 네트워크가 특정 기능에 너무 의존하지 않도록 유도합니다.

모델 구축: 원하는 순서대로 레이어를 nn.Sequential로 연결해보세요. 각 레이어의 입력 크기는 이전 레이어의 출력 크기와 일치해야 합니다! 예를 들면 다음과 같습니다.

```js
# Linear()을 사용하여 입력이 m이고 출력이 n인 선형 레이어를 만듭니다.
lnr = nn.Linear(m, n)

# .weight로 레이어의 가중치를 가져옵니다
lnr.weight

# .bias로 레이어의 편향을 가져옵니다
lnr.bias

# Sigmoid()를 사용하여 이진 분류를 위한 시그모이드 활성화 레이어 생성
nn.Sigmoid()

# Softmax()를 사용하여 다중 클래스 분류를 위한 소프트맥스 활성화 레이어 생성
nn.Softmax(dim=-1)

# ReLU()를 사용하여 포화를 피하기 위한 계단식 활성화 레이어 생성
nn.ReLU()

# LeakyReLU()를 사용하여 포화를 피하기 위한 릭리 레키 크기를 설정
nn.LeakyReLU(negative_slope=0.05)

# Dropout()을 사용하여 규제하고 오버피팅을 방지하는 드롭아웃 레이어 생성
nn.Dropout(p=0.5)

# 레이어를 순차적으로 연결하여 모델 생성
model = nn.Sequential(
    nn.Linear(n_features, i),
    nn.Linear(i, j),   # 입력 크기는 이전 레이어의 출력과 일치해야 합니다
    nn.Linear(j, n_classes),
    nn.Softmax(dim=-1) # 활성화 레이어가 마지막에 옵니다
)
```

# 모델 훈련 및 손실 계산

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

이제 데이터를 준비했고 모델을 구축했으니, 모델 훈련을 시작해봅시다!

```js
# 입력 데이터에 모델을 피팅하고 모델은 Sequential()을 통해 생성된 변수입니다.
prediction = model(input_data).double()

# 타겟 값들을 얻습니다.
actual = torch.tensor(target_values).double()

# 평균 제곱 오차 손실을 계산합니다. 회귀에는 MSELoss()를 사용합니다.
mse_loss = nn.MSELoss()(prediction, actual) # 텐서(x)를 반환합니다.

# 강건한 회귀를 위한 L1 손실을 계산합니다. SmoothL1Loss()를 사용합니다.
l1_loss = nn.SmoothL1Loss()(prediction, actual) # 텐서(x)를 반환합니다.

# 이진 분류를 위한 바이너리 크로스 엔트로피 손실을 계산합니다. BCELoss()를 사용합니다.
bce_loss = nn.BCELoss()(prediction, actual) # 텐서(x)를 반환합니다.

# 다중 클래스 분류를 위한 크로스 엔트로피 손실을 계산합니다. CrossEntropyLoss()를 사용합니다.
ce_loss = nn.CrossEntropyLoss()(prediction, actual) # 텐서(x)를 반환합니다.

# .backward()를 사용하여 역전파로 기울기를 계산합니다.
loss.backward()
```

# 옵티마이저 사용하기

신경망을 훈련하는 것은 운동 선수를 훈련하는 것과 비슷합니다. 올바른 도구와 기술이 필요합니다! 이때 옵티마이저가 필요합니다.

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
# SGD() 함수를 사용하여 확률적 경사 하강 옵티마이저를 생성하고 학습률 및 모멘텀 설정
optimizer = optim.SGD(model.parameters(), lr=0.01, momentum=0.95)

# .step()을 사용하여 뉴런 매개변수 업데이트
optimizer.step()
```

파이토치에서는 optim 모듈을 사용하여 서로 다른 옵티마이저를 생성하여 신경망의 가중치와 편향을 조정하는 방법을 각각 지정할 수 있습니다. 이번에는 확률적 경사 하강 (SGD)라는 일반적인 옵티마이저를 사용하는 방법을 알아보겠습니다:

- 옵티마이저 생성: optim.SGD()를 사용하여 SGD 옵티마이저를 생성합니다. 다음 두 가지 중요한 인수를 전달합니다:

    - model.parameters(): 이를 통해 옵티마이저에게 모델에서 업데이트해야 할 매개변수(가중치와 편향)를 알려줍니다.
    - lr=0.01: 이는 학습률로, 옵티마이저가 각 단계에서 매개변수를 얼마나 조정할지를 제어합니다. 작은 학습률은 조심스러운 업데이트를 유도하고, 큰 학습률은 학습 속도를 빠르게 할 수 있지만 (또한 잠재적으로 불안정성을 유발할 수 있음) 빠른 학습을 할 수 있습니다.


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

# 훈련 루프

```js
# 모델을 훈련 모드로 설정합니다.
model.train()
# 손실 기준 및 옵티마이저 설정
loss_criterion = nn.MSELoss()
optimizer = optim.SGD(model.parameters(), lr=0.01, momentum=0.95)
# 훈련 세트의 데이터 청크를 반복합니다.
for data in dataloader:
    # 그라디언트를 0으로 설정합니다.
    optimizer.zero_grad()
    # 현재 데이터 청크의 특징과 타깃을 가져옵니다.
    features, targets = data
    # "순방향 패스"를 실행하여 모델을 데이터에 맞춥니다.
    predictions = model(data)
    # 손실 계산
    loss = loss_criterion(predictions, targets)
    # 역전파를 사용하여 그래디언트 계산
    loss.backward()
    # 모델 매개변수 업데이트
    optimizer.step()
```

# 평가 루프

굉장한 PyTorch 모델을 훈련한 후, 이제 얼마나 잘 수행되는지 확인해보세요!

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
# 모델을 평가 모드로 설정
model.eval()

# 정확도 메트릭 생성
metric = torchmetrics.Accuracy(task="multiclass", num_classes=3)
# 검증 세트에서 데이터 청크 루프
for i, data in enumerate(dataloader, 0):
    # 현재 데이터 청크의 특징과 타겟 가져오기
    features, targets = data
    # 모델을 데이터에 맞추기 위해 "포워드 패스" 실행
    predictions = model(data)
    # 배치의 정확도 계산
    accuracy = metric(output, predictions.argmax(dim=-1))
# 전체 검증 데이터에 대한 정확도 계산
accuracy = metric.compute()
print(f"All 데이터에 대한 정확도: {accuracy}")
# 다음 데이터셋(학습 또는 검증)을 위해 메트릭 재설정
metric.reset()
```

# 전이 학습과 파인튜닝

머신 러닝에서 사전 훈련된 모델을 활용하여 우리 자신의 프로젝트를 빠르게 시작할 수 있습니다. 이를 전이 학습이라고하며, 그 내에서 파인튜닝은 강력한 기술입니다. PyTorch가 여러분을 돕는 방법을 살펴보겠습니다:

나중을 위한 레이어 저장: 모델의 특정 레이어를 훈련시키고 나중에 다시 사용하고 싶다면, torch.save()를 사용하여 레이어의 가중치와 편향을 파일로 직렬화할 수 있습니다. 이는 미래 AI 프로젝트를 위한 청사진을 저장하는 것으로 생각할 수 있습니다!


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
torch.save(layer, 'layer.pth')  # 'layer.pth' 파일에 레이어를 저장합니다.
```

저장된 레이어 불러오기: 이전에 저장한 레이어를 가져와야 하는 경우가 있습니다. torch.load()를 사용하여 다시 불러올 수 있습니다! 새 모델에 사전 훈련된 레이어를 통합하는 데 유용합니다.

```js
new_layer = torch.load('layer.pth')  # 'layer.pth' 파일에서 레이어를 불러옵니다.
```

동결된 파워로 파인튜닝! 예를 들어, 사전 훈련된 모델이 있고 특정 작업을 위해 최종 레이어만 파인튜닝하려는 경우가 있습니다. PyTorch를 사용하면 .requires_grad = False를 사용하여 이전 레이어의 가중치를 동결시킬 수 있습니다. 이렇게 하면 훈련 중에 업데이트되지 않고 마지막 레이어에 중점을 두고 학습 프로세스를 진행할 수 있습니다.


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

```python
for name, param in model.named_parameters():
    if name == "0.weight":
        param.requires_grad = False  # 필요에 따라 레이어 번호를 조정하여 레이어 0의 가중치를 고정시킵니다.

# 이제 최종 레이어만 업데이트되도록 모델을 학습할 수 있습니다!
```

전이 학습과 파인 튜닝을 마스터하면, 사전 학습된 지식을 활용하여 학습 시간을 단축시키고, 여러분을 머신러닝 효율성 챔피언으로 만들 수 있습니다!

읽어 주셔서 감사합니다. 내 컨텐츠가 마음에 드시고 저를 지원하고 싶다면, Patreon에서 저를 지원하는 것이 가장 좋은 방법입니다 —

<img src="/assets/img/2024-07-01-PyTorchCheatSheet_1.png" />


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

- 내 YouTube 채널 구독하기.
- 내 웹사이트 방문하기.
- LinkedIn과 Github에서 나와 연락하기! 나는 거기에서 무료로 놀라운 콘텐츠를 공유하고, 기술과 AI를 활용하여 더 생산적이고 효과적으로 일할 수 있도록 돕습니다.
- 머신러닝 및 딥러닝 도움이 필요하신가요? 내 Fiverr 및 Upwork 서비스를 확인해보세요!