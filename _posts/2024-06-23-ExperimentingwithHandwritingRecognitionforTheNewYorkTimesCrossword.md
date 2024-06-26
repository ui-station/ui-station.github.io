---
title: "뉴욕 타임즈 크로스워드에 손글씨 인식 적용 실험"
description: ""
coverImage: "/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_0.png"
date: 2024-06-23 01:26
ogImage:
  url: /assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_0.png
tag: Tech
originalTitle: "Experimenting with Handwriting Recognition for The New York Times Crossword"
link: "https://medium.com/timesopen/experimenting-with-handwriting-recognition-for-new-york-times-crossword-a78e08fec08f"
---

![이미지](/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_0.png)

작성자: 샤픽 쿠라이쉬

## 소개

2023년 MakerWeek에서 시작된 뉴욕 타임스 매년 개최하는 해커톤에서 iOS 및 안드로이드 모바일 엔지니어들이 각 플랫폼에서 뉴욕 타임스 크로스워드 앱에 직접 손으로 쓰는 기능을 탐구했습니다.

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

참여한 실험의 안드로이드 엔지니어로서 안드로이드 크로스워즈에 On Device ML을 구현하면서 플랫폼별 경험을 기대하고 있습니다.

참고: 이 탐색은 아직 출시되지 않은 후속 기능을 위한 것입니다.

## 초기 설정 및 요구 사항

![이미지](/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_1.png)

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

뉴욕 타임스 크로스워드는 앱에 내장된 사용자 정의 소프트웨어 키보드를 갖고 있어요. 사용자가 키보드에서 글자를 입력하면 해당 사각형에 나타나요.

손으로 쓰기를 가능하게 하기 위해, 우리가 먼저 해야 한 것은 실제로 사용자가 스타일러스나 손가락을 통해 텍스트를 수동으로 입력할 수 있도록 하는 것이었어요. 우리는 미니와 데일리 모두의 크로스워드 사각형마다 가져가서 '스케치박스'라고 불리는 사용자 정의 구성 요소로 변환했어요. 이 구성 요소는 사용자가 화면에 쓸 때 손가락이나 스타일러스로 그린 각 획을 캡처하고, 터치 및 드래그 이벤트를 감지하여 그린 글자 획을 표시하도록 특별히 설계되었어요.

우리의 스케치박스가 캔버스로부터 얻은 결과 글자 픽셀을 캡처한 후, 그 데이터를 우리의 선택한 기계 학습 알고리즘에 전송할 수 있었어요.

## 연필 타이밍

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

<img src="/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_2.png" />

실제 필기 감지에 들어가기 전에, 섬세하지만 중요한 한 가지를 다룰 필요가 있습니다.

사용자가 스케치박스에 쓰다 보면, 보통 손가락이나 스타일러스를 캔버스에서 떼곤 합니다, 특히 K, A, H 등과 같은 글자를 완성할 때 더욱 그렇습니다. 이것은 사용자가 각 획 사이에 정확히 언제 필기를 끝냈는지를 결정해야 한다는 것을 의미합니다. 예를 들어, 만약 사용자가 "K"의 줄기를 입력하면, 필기 도구를 캔버스에서 뗄 때 이 글자가 무엇일지 바로 감지하려고 하면 "I"로 해석될 수 있습니다.

그렇다면 우리는 획 사이에서 얼마나 기다려야 할까요?

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

저희 초기 구현에서는 뮤텍스와 유사한 입력 잠금 시스템 개념을 도입했습니다. 각 스트로크 사이에는 특정 조건에 따라 500에서 1000밀리초 사이의 값 실험을 진행했습니다. 스타일러스 잠금을 오랫동안 기다리고 싶지는 않았는데, 그렇게 되면 사용자 입력 경험이 떨어지고 버벅거릴 수 있습니다.

이것은 우리가 필기 메커니즘을 설계할 때 고려해야 했던 많은 복잡성 중 하나이며, 향후 수정할 수 있는 부분입니다.

## 데이터 준비, 조건부 및 정규화

이미지를 텍스트로 변환하기 전에, 다양한 디바이스에서 다른 화면 크기와 해상도의 글자들이 입력되는 것을 고려해야 했습니다.

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

알고리즘이 정확한 학습을 위해 필요한 데이터의 가장 간단한 형태를 얻는 것을 포함한 중요한 전처리 단계가 포함되어 있습니다. 이미지 데이터의 경우 비필수적인 잡음과 "주저하는 지오메트리"를 제거하는 것을 의미합니다. 우리는 문자 데이터를 축소 및 이진화하고, 그런 다음 128x128 크기의 원시 입력 문자를 훨씬 작고 효율적이며 간소화된 28x28 이미지로 변환했습니다.

![image](/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_3.png)

그런 다음에야 우리는 라스터화된 캔버스 이미지 데이터를 우리의 교차 이력 앱이 이해하는 실제 문자로 어떻게 번역하는지에 대해 최종적으로 논의할 수 있었습니다.

필기 인식은 광학 문자 인식(OCR) 내의 클래식한 기계 학습 과제입니다. 특히 1998년 Yann LeCun 박사의 LeNet-5 아키텍처로, Modified National Institute of Standards and Technology (MNIST) 데이터셋에서 digit 인식이 크게 향상되었습니다. MNIST 데이터셋에는 1부터 9까지의 다양한 숫자 변형이 수천개 포함되어 있으며, digit 인식의 대표적인 표준 데이터베이스입니다.

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

아래는 만들려고 했던 시스템의 고수준 아키텍처 다이어그램입니다:

![아키텍처 다이어그램](/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_5.png)

선택한 기계 학습 알고리즘은 문자 데이터를 인식 클러스터로 가장 잘 분리해주도록 의도되었습니다. 아래에서 이상적인 형태로 구분한다면 시스템이 사용자가 'A'를 입력하려는지, 예를 들어 'C'를 입력하려는지 쉽게 판단할 수 있게 되었을 것입니다. 이러한 목적을 달성할 수 있도록 우리는 다층 합성곱 신경망 아키텍처를 사용하기로 결정하기 전에 여러 다른 옵션들을 탐색했습니다. 여기에서는 그에 대해 자세히 다루지 않겠습니다. 결과적으로, 우리가 선택한 Deep Convolutional Neural Network 아키텍처는 임무에 충분히 부합한다는 것을 입증했습니다.

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

![이미지](/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_6.png)

## 딥 컨볼루션 네트워크 구축하기

깊은 CNN(컨볼루션 신경망)은 현대 이미지 기반 머신 러닝 시스템의 중추입니다. 이미지 데이터의 일부를 검토하고 학습 메커니즘을 사용하여 중요한 특징을 지능적으로 찾아 이미지를 식별하고 분류하는 데 도움이 되는 특수한 종류의 신경망입니다.

![이미지](/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_7.png)

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

위의 예시에서 보면, 신경망은 새의 입력 이미지를 받아와 새와 관련된 다양한 질적 요소에 해당하는 구조물을 탐지합니다. 이는 프로그램을 처음부터 설계하기 어려운 사람이 이미지의 일부분이 빽, 눈, 부리의 가장자리, 내부 부리 기하학 등과 같은 다양한 특징을 감지할 수 있습니다.

가장 중요한 특징을 사용하여, 이는 새를 보고 어떤 종류의 새인지 판별할 수 있습니다. 또한, 새가 날아가거나 바라보는 방향과 같은 것들도 판별할 수 있습니다 - 단, 네트워크가 적절하게 훈련되어 있어야 합니다. 우리는 사용자가 우리의 크로스워드 앱에 입력하는 글자들에도 같은 원리를 적용합니다.

기본 CNN은 다음 기본적인 레이어들의 조합으로 이루어져 있습니다. 이러한 구조물을 지능적으로 혼합하여 적절한 매개변수와 함께 사용하면, 우리가 감지하지 못하는 이미지 요소는 거의 없습니다:

- 합성곱 레이어는 모델이 특징을 자동으로 추출할 수 있도록 합니다
- 맥스 풀링 레이어는 Conv 레이어에서 얻은 특징을 좁힙니다
- ReLU 레이어는 비선형성을 도입하고 복잡한 패턴 구별을 가능하게 합니다
- 드롭아웃 레이어는 네트워크의 과적합을 완화합니다

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

<img src="/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_8.png" />

## TensorFlow Lite 및 모델 적재

모델을 구축하는 것 외에도, 이를 기기에 전달하는 방법을 찾아야 했습니다. 저희는 Python으로 컴파일된 머신러닝 모델을 안드로이드 또는 iOS 기기에 설치하는 데 사용되는 모바일 프레임워크인 TensorFlow Lite를 선택했습니다.

<img src="/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_9.png" />

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

<img src="/assets/img/2024-06-23-뉴욕타임스크로스워드의필체인식실험_10.png" />

우리가 모델에 만족했을 때, 저희는 .tflite 파일로 컴파일하고 애플리케이션 안에 넣어서, 십자말 퍼즐 정사각형에서 오는 글씨 쓰기 이벤트를 청취하는 데 사용할 샤운트를 만들었습니다. 우리는 최종 결과에 만족할 때까지 서로 다른 모델과 구성을 여러 번 반복하여, 오직 100KB 가량의 학습된 파일로 변해 반영되었습니다 - 여기서는 공간을 고려해야 하는 모바일 애플리케이션에 적합합니다.

## 숫자 인식

전체 글자인식 문제에 접근하기 전, 우리는 간단히 시작하고 넘어갈 것으로 숫자인식 문제를 해결하기로 결정했습니다. 아래의 핵심 코드 섹션은 우리가 구현한 기본 CNN 설정을 제공합니다:

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

<img src="/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_11.png" />

## 첫 번째 Digit Based CNN 모델 실패: 무엇이 잘못되었을까요?

<img src="/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_12.png" />

MNIST에서 얻은 풍부한 교육 데이터에도 불구하고, 우리의 인식 결과는 좋지 않았습니다.

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

훈련 데이터가 "너무 완벽하다"라고 판단했습니다. 숫자는 서로의 작은 변형만 있었고, 대부분은 상자의 중앙에 있었습니다.

이 방법은 사람들이 교차 워드 퍼즐 칸에 데이터를 입력하는 방식이 아닙니다. 사람들은 서로 다른 필기 스타일과 문자를 칸 안팎에 비스듬히 배치하는 방법을 가지고 있습니다.

해결하기 위해 잘 알려진 머신러닝 기술인 데이터 증강을 사용해야 했습니다.

데이터 증강은 훈련 데이터의 비스듬하고 왜곡된 버전을 자동으로 생성하여 수동 조정과 비틀음이 필요하지 않도록 합니다. 이를 통해 초기 데이터 세트의 다양한 변형을 가능하게 하며 문자의 크게 비중심 버전을 포함할 수 있습니다.

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

![Experimenting with Handwriting Recognition for The New York Times Crossword](/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_13.png)

데이터 증강 기술을 적용하여, 우리는 데이터셋을 수천 개에서 중심에서 벗어나는 작은 이동, 회전, 확대/축소(어파인 변환 이라고도 함) 등을 적용하여 100만 개 이상의 샘플로 확장했습니다.

## 숫자에 대한 데이터 증강 된 모델 성공

![Experimenting Handwriting Recognition for The New York Times Crossword](/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_14.png)

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

위의 표를 마크다운 형식으로 변경하십시오.

## 풀 브론 글자 인식으로 이동하기

![image](/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_15.png)

숫자 인식이 크로스워드에서 크게 개선되었다는 것을 확인할 수 있습니다. 이는 우리 작업에서의 중요한 성과입니다!

이제 우리는 숫자를 해결했으니 다음 단계는 글자를 해결하는 것이었습니다. 이에는 26개의 소문자, 26개의 대문자 및 모든 변형이 포함됩니다. 이제 열 개의 숫자를 다루는 것이 아니라 62개의 문자를 처리해야 합니다.

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

EMNIST 데이터세트 (데이터 소스: http://arxiv.org/abs/1702.05373)를 사용할 수 있어요. 이는 MNIST 세트의 확장 버전으로, 문자뿐만 아니라 숫자와 구두점까지 포함하고 있어요. 이를 통해 모델을 더 잘 훈련할 수 있어요.

그러나 향상된 데이터세트를 사용하더라도 숫자에 특화된 모델만으로는 우리의 요구를 충족시키기에는 충분하지 않아요. 이는 직관적으로 놀랍지 않아요. 왜냐하면 숫자 인식 모델은 강력하지만, 문자를 보는 데 도입된 문자 구조의 확장된 다양성에 대해 충분히 "지능적"이지 않기 때문이죠.

## 매개변수 최적화를 통한 모델의 하이퍼 강화

우리 모델의 성능을 높이기 위해, 우리는 네트워크에 훨씬 더 많은 깊이를 추가했어요. 또한, 증강된 훈련/검증 데이터의 무작위 하위 집합을 사용하여 다양성을 확보하기 위해 Stratified K-Fold 교차 검증을 사용했어요.

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

우리의 레이어가 최적으로 설계되었는지 확인하기 위해 무작위 매개변수 검색과 향상된 통계적 테스트를 사용했습니다. 이를 통해 모델의 최적 하이퍼파라미터를 찾는 데 성공했습니다. 이는 이전에 올바른 매개변수를 추측했던 우리의 전략보다 발전된 것으로 나타났습니다.

테스트 단계에서 우리는 증강된 EMNIST 데이터셋에서 약 91%의 평균 검증 정확도를 달성했습니다. 이는 우리 모델이 작동할 것이라는 신뢰를 주었습니다.

마침내: 성공!

![이미지](/assets/img/2024-06-23-ExperimentingwithHandwritingRecognitionforTheNewYorkTimesCrossword_16.png)

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

기계 학습 모델 구축의 풍경을 탐험한 긴 여정 끝에 우리는 드디어 작동하는 크로스워드 모델에 도착했어요. 이는 정말 흥미로운 일이었죠. 우수한 결과를 이루었지만, 완벽한 크로스워드 경험을 위해 아직 해야 할 일이 많습니다. 부분적 글자 처리 및 불규칙한 간격으로 띄워진 글자들도 다루어야 합니다.

## 결론

안드로이드 크로스워드 앱에 필기 인식 기능을 구현하는 것은 실험적인 맥락에서도 흥미진진한 모험이었습니다. 필기 인식 외에도 “스크립 투 지우기” 감지와 같은 상호 작용 기능, 앱 내 자기 학습 메커니즘 가능성과 On-Device ML이 게임 앱에서 여는 다양한 문을 통해 가능성이 있습니다.

새로운 기술과 기존 제품에 대한 강화를 실험해 볼 기회를 가지는 것은 타임즈에서 엔지니어로 일하는 것이 독특하고 가치 있는 이유의 핵심입니다. 우리는 언젠가 현재 사용자들을 위한 게임 경험을 강화하고 새 구독자를 유치하는 기능으로 발전시키는 날이 오기를 희망합니다.

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

샤피크 쿠라이시는 뉴욕 타임스의 게임 팀에서 시니어 안드로이드 엔지니어로 일하고 있어요. 그는 머신 러닝과 인공 지능에 열정을 가지고 있어요. 업무 외에는 기타 연주, 글쓰기, 그리고 독특한 실험을 하는 것을 즐기고 있어요.
