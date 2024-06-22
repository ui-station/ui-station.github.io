---
title: "다중 터치 ML 모델 최신 트렌드 및 사용 방법"
description: ""
coverImage: "/assets/img/2024-06-23-Multi-touchMLModels_0.png"
date: 2024-06-23 01:46
ogImage: 
  url: /assets/img/2024-06-23-Multi-touchMLModels_0.png
tag: Tech
originalTitle: "Multi-touch ML Models"
link: "https://medium.com/@brandonknox_6151/multi-touch-ml-models-572f7cb27874"
---


## iOS 앱으로 Python 기반 ML 모델 이식하기

![image](/assets/img/2024-06-23-Multi-touchMLModels_0.png)

## 소개

Google과 Apple은 모바일 기기에서 AI 모델을 네이티브로 실행하는 주요 계획을 시작했습니다. 이는 원격 서버에서 모델을 실행하는 것에서 이동하여 AI 응용 프로그램에 대한 흥미로운 기회를 만들어내면서 사용자 개인정보를 유지하는 것입니다. 이러한 발전 중 많은 부분은 디바이스 제조업체가 기계 학습 작업에 사용되는 알고리즘을 처리하는 데 점점 효과적인 칩셋을 설계한 데서 나옵니다. 최근 트렌드를 고려하여, 나는 Python으로 작성된 ML 모델을 iOS에서 네이티브로 실행할 방법을 살펴보기로 결정했습니다. 이를 통해 전화기에서 더 복잡한 ML 구현으로 나아가는 계단으로 보고, 이러한 질문에 대답하기 위해 잘 알려진 데이터셋을 사용했습니다:

<div class="content-ad"></div>

- ML 모델을 iPhone에서 실행할 수 있는 방법은 무엇인가요?
- 그 모델과 상호작용하기 위한 직관적인 방법은 무엇인가요?
- 모바일 앱이 데이터와 기계 학습 모델 사이의 관계를 찾는 것을 기술적이고 비기술적인 사용자 모두에게 더 쉽게 만들 수 있을까요?

이 글은 이러한 질문들을 염두에 두고 iOS 앱을 어떻게 구축하는지에 대해 안내하겠습니다. 자세한 내용에 들어가기 전에 이 연습을 통해 얻은 주요 통찰을 공유하겠습니다.

- 모델을 적응시키는 "어떻게"에 대한 질문은 애플의 Core ML 도구를 사용하여 해결할 수 있습니다. 이 도구는 파이썬 모델을 애플의 칩셋에 최적화된 Core ML 형식으로 변환할 수 있습니다.
- 슬라이더는 모델 예측에 대해 특성 값을 조정하기 위한 직관적인 인터페이스로, 그러나 화면 공간을 많이 차지합니다.
- 분할 스크롤은 상대적으로 작은 공간에서 수치적 비교를 가능케 합니다.
- 이 앱은 데이터에 대한 배경을 가진 사람들에게 ML 모델을 설명하는 데 도움이 될 수 있지만, 사용자 인터페이스와 시각화에 대한 추가적인 조정으로 비기술적인 사용자를 돕을 수 있습니다.

이 글은 모바일 ML 모델을 만드는 데 사용된 일반적인 기술에 대해 이야기할 것입니다. 프로젝트에 대한 GitHub 링크도 제공됩니다. 다음은 다룰 주제의 요약입니다:

<div class="content-ad"></div>

- 모델이 훈련된 데이터의 개요
- Python에서 Core ML로 모델 변환하기
- 데이터를 iOS 앱으로 내보내기
- 모델과 데이터를 애플리케이션에 가져오기
- 모델 및 데이터와 상호 작용하는 인터페이스 구축 방법
- 결론

완성된 앱은 다음과 같습니다. 시작해봅시다!

![앱 스크린샷](/assets/img/2024-06-23-Multi-touchMLModels_1.png)

## 모델이 훈련된 데이터의 개요

<div class="content-ad"></div>

ML 모델을 학습하는 데 사용된 데이터에 대해 간단히 설명드리겠습니다. 우리는 인기 있는 Pima 인디언 당뇨병 데이터 세트를 사용할 것입니다. 이 데이터는 국립당뇨병 및 소화기 및 신장 질환 연구소에서 나온 것으로, 혈당, BMI, 연령 등과 같은 개인의 의학적 속성에 중점을 둔 더 큰 데이터베이스의 일부를 대표합니다. 대상은 적어도 21세 이상의 피마 인디언 여성입니다. 당뇨병 양성 진단을 받은 환자는 결과가 1이며, 당뇨병 음성 진단을 받은 환자는 결과가 0입니다. 데이터는 kaggle에서 수집되었습니다.

이 프로젝트에서는 Python을 사용하여 모델 구축 및 데이터 변환을 수행하고, iOS 애플리케이션에는 Swift를 사용할 것입니다. Python 코드에서 각 기능에 대해 생성된 탐색적 시각화 및 몇 가지 더 구체적인 결과들이 구축되기 전에 기본 랜덤 포레스트 분류기 모델을 만들 것입니다. 앱을 위해 몇 가지 염두에 두어야 할 사항들:

- 사용자가 모델 예측을 위해 현실적인 시작점을 갖도록 기본적으로 이러한 값으로 설정하는 경우에 나중에 앱 인터페이스를 만들 때 데이터의 각 기능에 대한 전반적인 중심 경향 측정치를 표시할 것입니다.
- 원래 데이터와의 모델 예측을 테스트할 수 있도록 몇 가지 변수 간의 관계를 관찰할 것입니다.

![image](/assets/img/2024-06-23-Multi-touchMLModels_2.png)

<div class="content-ad"></div>

위의 등고선 플롯에서 극값을 참고하여, 매우 가능성 있는 당뇨 진단이 있는 경우와 당뇨가 덜 가능성 있는 경우를 찾기 위해 데이터를 필터링할 수 있습니다. 아래는 110에서 130 사이의 글루코스 값, BMI가 30에서 35 사이인 경우 및 양성 당뇨병 분류에 대한 중앙값이 제공됩니다. 나중에 이러한 값들을 선택하여 우리 모델이 양성 분류를 예측하는지 확인할 것입니다.

우리 모델은 scikit-learn에서 가져온 기본 랜덤 포레스트 분류기입니다. 데이터에 표준 스케일러를 적용하는 전처리기로서 파이프라인을 사용함에 유의하세요. 현재 모델에 추가적인 최적화는 수행되지 않았으며, 제 현재 초점은 iOS 앱에 배포하는 방법을 보여주는 것입니다.

```js
clf = RandomForestClassifier(random_state=42, n_estimators=500)
pipe = Pipeline([('scaler', StandardScaler()), ('rf', clf)])
pipe.fit(X_train, y_train)
```

<div class="content-ad"></div>

모델은 조정 없이 첫 번째 시도에서 테스트 데이터에서 0.8의 정확도 점수를 보였어요. 당뇨병 양성 진단을 예측하는 데 어려움을 겪는 반면, 음성 진단을 예측하는 데는 더 어려움이 있어요. 특성 중요도를 확인할 때 혈중 포도당 수준이 가장 큰 영향을 주며, 이어서 BMI, 나이, 그리고 환자의 당뇨병 가계식 기능이 나타났어요. 사용자에게 직관적인 방식으로 이러한 특성을 전달할 수 있는지 앱을 개발하는 과정에서 이러한 점을 염두에 두겠어요. 다음으로는 Python 모델을 Core ML 파일로 내보내고 iOS 앱에서 시각화를 위한 데이터를 준비해볼게요.

## Python 모델을 Core ML로 내보내기

Python 코드에서 Core ML 도구를 가져와서 ct로 별칭을 만들어요. Core ML 도구에는 여러 모델을 지원하는 컨버터 클래스가 있으며, 원핫 인코딩과 같은 전처리기를 포함하고 있어요. convert 메서드를 호출하면 외부에 저장하고 Xcode와 같은 앱 개발 환경에서 가져올 수 있는 모델이 생성되어요:

```js
coreml_model = ct.converters.sklearn.convert(pipe, X_cols, 'Outcome')
coreml_model.save('DiabetesTest.mlmodel')
```

<div class="content-ad"></div>

Core ML 도구에 대한 문서를 읽어보세요. Python 라이브러리로 어떤 것이 지원되는지 확인할 수 있어요. PyTorch, TensorFlow와 같은 인기 있는 신경망 라이브러리 뿐만 아니라 scikit-learn, XGBoost, LibSVM과 같은 비신경망 라이브러리도 지원되요. 사용 중인 라이브러리가 지원되지 않는다면, 사용 가능한 것에 맞추기 위해 파이프라인을 다시 설계해야할 수도 있어요.

## 소스 데이터를 JSON으로 내보내기

API 데이터를 가져오는 것을 시뮬레이션하기 위해 .csv 파일을 JSON으로 변환하고 있어요. 이것은 반드시 최상의 방법은 아니지만, 일부 데이터 시각화에 우리 앱이 작동하려면 데이터가 이런 형식으로 포맷되어 있으면 편리할 거에요. iOS 앱에서 데이터를 인덱싱하는 데 도움이 되도록 내보내기 전에 데이터에 일부 수정을 가했어요:

```js
import json
out = pima.reset_index()
out = out.rename({'index': 'id'}, axis=1)
out = out.to_json(orient='records')
parsed = json.loads(out)
with open('data/diabetes.json', 'w') as f:
    f.write(out)
```

<div class="content-ad"></div>

## iOS 애플리케이션으로 모델 가져오기

이제 iOS 기기에 최적화된 형식으로 모델을 내보냈으니, 애플리케이션으로 불러와야 합니다. 이후에는 Apple의 Xcode 환경을 사용해 Swift로 개발하게 될 것입니다. Xcode에서는 파일 - "파일 추가..." 메뉴를 이용해 프로젝트에 파일을 추가할 수 있고, 그 후에는 Swift 코드로 참조하면 됩니다. 아래는 모델의 파일명이 DiabetesTest로 설정된 예시입니다.

```Swift
import CoreML
import CreateMLComponents

let config = MLModelConfiguration()
let model = try DiabetesTest(configuration: config)
```

모델을 불러왔는데, 이제 앱에서 어떻게 예측을 만들 수 있을까요? 모델은 prediction() 메서드를 가지고 있으며, 데이터의 모든 특징을 매개변수로 사용합니다. 호출하는 방법은 아래와 같습니다:

<div class="content-ad"></div>


let prediction = try model.prediction(Pregnancies: pregnancies, 
                                      Glucose: glucose, 
                                      BloodPressure: bloodPressure, 
                                      SkinThickness: skinThickness, 
                                      Insulin: insulin, 
                                      BMI: BMI, 
                                      DiabetesPedigreeFunction: diabetesPedigreeFunction, 
                                      Age: Age)
predictionValue = Int(prediction.Outcome)


## iOS 애플리케이션으로 JSON 데이터 가져오기

데이터를 로드해야 합니다. 이 작업은 모델을 훈련하는 데 사용한 각 개인의 건강 데이터를 시각화하는 데 도움이 됩니다. Pima라는 구조체를 만들어서 시작하겠습니다. 이 구조체는 Codable 및 Identifiable로 만들어지며, 각 특징이 나열됩니다.

```swift
struct Pima: Codable, Identifiable {
    let id: Int
    let Pregnancies: Float
    let Glucose: Float
    let BloodPressure: Float
    let SkinThickness: Float
    let Insulin: Float
    let BMI: Float
    let DiabetesPedigreeFunction: Float
    let Age: Float
    let Outcome: Float

    var BMIString: String { BMI.formatted(.number) }
    var GlucoseString: String { Glucose.formatted(.number) }
}
```

<div class="content-ad"></div>

데이터를 가져오는 것은 JSON 디코딩이 필요할 거예요. 주요 단계의 요약은 아래에 있어요. 디코딩된 데이터는 클래스 데이터 객체에 저장돼요. 이 객체는 프로젝트 전체에서 공유할 거예요:

```js
let bundlePath = Bundle.main.path(forResource: name,
                                  ofType: "json"),
let jsonData = try String(contentsOfFile: bundlePath).data(using: .utf8)
let decodedData = try JSONDecoder().decode([Pima].self,
                                           from: jsonData)
self.pima = decodedData
```

## 모델 실험을 위한 인터페이스 구축

이제 데이터를 불러왔으니, 앱 인터페이스 구성 요소를 만들어 볼 수 있어요. 이 구성 요소에는 다음이 포함돼요:

<div class="content-ad"></div>

- 혈당, BMI, 연령 및 기타 측정치의 값을 수정할 수 있는 슬라이더. 환자의 당뇨병 가능성을 나타낼 예측이 값이 변경될 때마다 이루어집니다.
- 당뇨병 분류 결과를 동반하는 차트로, 소스 데이터에서 두 그룹에 속한 환자 수를 표시합니다.
- 데이터의 스크롤 가능한 뷰로, 선택한 값을 원본 데이터세트의 값과 비교하거나 양성 및 음성 분류 간을 비교할 수 있습니다.

이러한 요소들의 조합은 다양한 배경을 가진 사람들이 모델과 소스 데이터를 실험을 통해 이해할 수 있도록 의도되었습니다. 예를 들어, 혈당 슬라이더를 조절하여 혈당이 증가함에 따라 산점도에 오렌지색 점의 증가와 이웃 막대 차트의 더 높은 오렌지색 막대가 나타나며, 더 많은 양성 케이스를 나타냅니다. 다른 기능들, 특히 연령과 BMI와 실험을 통해 이들 값과 양성 예측 사이의 관계를 비교하면 유사한 발견이 이뤄질 것입니다. 각 슬라이더 변경 시 모델은 예측을 수행하고 데이터를 필터링하여 현재 뷰와 함께 필터링된 데이터를 제공합니다.

우리의 슬라이더를 구현하기 위해, 각 기능에 대해 데이터의 최소/최대 값을 범위로 설정할 수 있습니다. 슬라이더가 이동하고 값이 변경될 때마다, 차트에서 읽은 공유된 필터링된 데이터를 업데이트하고 현재 슬라이더 값을 사용하여 모델에 의한 예측을 실행합니다. 혈당 슬라이더를 사용한 예시는 아래와 같습니다:

![image](https://miro.medium.com/v2/resize:fit:590/1*ekxFVO-UDWH_2I9FQjQiPw.gif)

<div class="content-ad"></div>

이 예시에서는 데이터 개요 섹션에서 관찰된 값들과 유사한 값을 사용하여 Glucose의 범위를 110에서 130으로 보여주는 컨투어 플롯이 있습니다. 이 플롯은 이 범위에서 당뇨병 양성 진닝의 높은 양을 나타냈습니다. 이러한 글루코스 값으로 업데이트한 후 모델이 양성 분류를 지정하는 것을 보는 건 격려가 됩니다!

시각화를 만들려면 Charts 라이브러리가 필요하며, 우리는 당뇨병이 없는 환자를 나타내는 파란색, 당뇨병이 있는 환자를 나타내는 주황색으로 사용자 지정 색상을 가진 산점도를 보여줍니다. 막대 차트는 두 가지 당뇨병 결과를 따로 그룹화하고 현재 슬라이더 값에 따라 총계를 계산합니다. 다음은 막대 차트에 대한 코드 스니펫입니다:

```js
// 현재 선택 항목에 따른 총 수 카운트 찾기       
let aggregatedData = Dictionary(grouping: filteredTable, by: { $0.Outcome })
    .map { (outcome, items) in
        (category: outcome, count: items.count)
    }

// 막대 차트 생성
let barChart = Chart(aggregatedData, id: \.category) { item in
    BarMark(
        x: .value("카테고리", item.category),
        y: .value("수", item.count)
    )
    .annotation(position: .top, alignment: .center) {
        Text("\(item.count)")
            .font(.caption)
            .foregroundColor(.black)
    }
    .foregroundStyle(by: .value("카테고리", item.category))
}
```

이 시점에서 사용자에게 혈당, BMI 및 기타 기능의 값 선택이 실제 환자 데이터와 일치하는지를 보여줄 수 있습니다. 또한 우리의 모델이 그러한 선택을 기반으로 예측을 하는 방법을 동시에 보여줄 수 있습니다. 그러나 혈당, BMI 및 기타 기능에 대한 "정상" 값이 무엇인지에 대한 사용자에게 더 많은 컨텍스트를 제공하는 방법은 무엇일까요? 앱 설계 시에 이 문제를 몇 가지 마주쳤습니다:

<div class="content-ad"></div>

- 화면 공간이 제한되어 있어 숫자들과 요약 통계의 벽이 잘 표시되지 않아요.
- 최소 두 세트의 데이터를 동시에 비교할 수 있어야 하며 사용자가 비교하려는 데이터 포인트를 쉽게 선택할 수 있어야 합니다.
- 사용자가 소스 데이터와 일치하지 않는 값을 실수로 선택하면 해당 값과 일치시킬 수 없게 됩니다.

1번과 2번 문제에 대한 해결책은 스플릿 스크롤 뷰를 생성하는 것이었습니다. 뷰의 반은 하나의 데이터 세트에 투여되고, 다른 반은 비교할 대상에 투여됩니다. 두 가지를 빠르게 동시에 스크롤하여 값들을 비교할 수 있으므로 노력을 많이 들이지 않고도 비교할 수 있습니다. 이를 위해 새로운 두 가지 뷰를 생성했고, 앱 상단 오른쪽의 Stats 버튼을 클릭하여 이동할 수 있습니다. 첫 번째 뷰는 현재 선택 사항과 필터링된 데이터의 중앙/최소/최대 값을 비교할 수 있도록 합니다. 두 번째 뷰는 긍정적 결과로 필터링된 데이터와 부정적 결과로 필터링된 데이터 간의 비교를 제공합니다.

"이움 매체 데이터"라고 표시된 추가 슬라이더를 추가하여 사용자가 데이터 포인트와 가깝지 않은 값을 선택할 때 대응했습니다. 이 슬라이더를 조정하면 사용자가 선택한 값의 위와 아래로 필터 범위가 증가하여 현재 선택 사항 주변에 점점 더 넓은 범위로 데이터를 검색합니다. 왼쪽 끝까지 슬라이드하면 모든 데이터 포인트가 표시됩니다. 다른 뷰의 중앙/최소/최대 계산은 이 데이터 선택을 기반으로 합니다.

<div class="content-ad"></div>

<img src="https://miro.medium.com/v2/resize:fit:590/1*VEDrLQddT4Pi2rGqGK8G5A.gif" />

## 결론

우리는 직관적인 스마트폰 인터페이스를 활용하여 데이터와 머신러닝 모델을 다루는 직관적인 방법을 만들 수 있습니다. Apple의 Core ML 도구를 사용하면 우리는 파이썬으로 모델을 개발하고 이를 iOS 네이티브 모델 형식으로 변환한 다음 앱에 가져올 수 있습니다. 우리의 앱은 사용자가 기능 값들을 수정할 수 있는 기능을 제공하여 모델을 훈련시키는 데 사용된 데이터 세트를 탐색하고 모델의 예측 출력을 볼 수 있게 합니다. 이 공간에서 탐색할 수 있는 많은 가능성이 있으며, 모델과 다른 인터페이스를 계속해서 만들면 다른 사람들에게 결과를 전달하는 새로운 방법을 열 수 있습니다!

어떤 모델 인터페이스를 만들어 보고 싶으신가요?

<div class="content-ad"></div>

## 링크

GitHub 저장소

애플의 Core ML 도구

피마 인디언 당뇨병 데이터베이스