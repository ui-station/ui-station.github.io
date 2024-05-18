---
title: "스위프트 플레이그라운드에서 머신 러닝 모델을 사용하는 방법"
description: ""
coverImage: "/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_0.png"
date: 2024-05-18 17:20
ogImage: 
  url: /assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_0.png
tag: Tech
originalTitle: "How to use a ML model in Swift Playgrounds"
link: "https://medium.com/@ramon217gomez/how-to-use-a-ml-model-in-swift-playgrounds-7bb96432d98e"
---


<img src="/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_0.png" />

많은 사람들처럼, 올해의 Swift Student Challenge 아이디어를 개발하면서 내가 많은 시간을 쏟으면서 내 인내심을 시험받는 여러 가지 장애물을 만났어. 그래서, 몇 밤을 잠 못 이루게 한 문제에 대한 해결책을 공유하고 싶어: Swift Playgrounds에서 CoreML을 구현하는 방법.

앱이 SSC에 참가하기 위한 요구 사항 중 하나는 Swift Playground여야 한다는 것을 아실 것입니다. 그러나 이러한 종류의 프로젝트는 장단점을 갖고 있어. Swift Playground는 Apple이 만든 대화형 개발 환경으로, 개발자와 프로그래밍 학습자들이 빠르고 쉽게 Swift 코드를 작성, 테스트, 시각화할 수 있게 해줘. Swift Playgrounds는 macOS용 Apple의 종합 개발 스위트인 Xcode와 iPad용 독립형 앱으로 모두 사용할 수 있어. 이 환경은 프로그래밍을 배우거나 Swift로 새로운 아이디어나 알고리즘을 실험하는 데 특히 유용해.

반면에 Core ML (Core Machine Learning)은 Apple이 개발한 프레임워크로, iOS, macOS, watchOS 및 tvOS 앱에 머신 러닝 모델을 통합할 수 있게 해줘. Core ML을 사용하면 개발자들은 사전 훈련된 머신 러닝 모델을 활용하여 이미지 인식, 자연어 처리, 데이터 분석 등의 작업을 인터넷 연결 없이 효율적이고 실시간으로 수행할 수 있어.

<div class="content-ad"></div>

이제 표준 Swift 프로젝트에서 Core ML을 사용하는 것은 매우 간단합니다. 우리 모델을 프로젝트에 추가하기만 하면 앱에서 사용할 수 있습니다. 그러나 이 글을 작성하는 시점에서 Swift Playground에서의 사용은 조금 복잡합니다. 후자의 형식에서는 앱에서 모델을 사용하려면 해당 모델이 앱에 의해 사용되도록 설정해야 하는 일련의 단계를 따라야 합니다. 이는 잘 문서화되어 있고 웹의 다양한 위치에서 찾을 수 있습니다. 그럼에도 불구하고, Xcode 15가 출시되면서 사용자들 사이에 널리 알려지지 않은 마지막 단계가 있습니다.

## 첫 번째 단계: 파일 가져오기

Swift Playgrounds 프로젝트에서 ML 모델을 사용하려면, 모델의 .mlmodel 파일을 추가할 임시 Swift 프로젝트를 먼저 준비해야 합니다. 이 프로젝트를 컴파일함으로써 두 가지를 달성할 수 있습니다: 해당 프로젝트의 클래스 파일과 컴파일된 모델 얻기. 이 두 가지는 Playground를 통해서는 불가능하기 때문에, 많은 사람들이 첫 시도에서 CoreML 모델을 성공적으로 구현하지 못한 것일 수 있습니다.

첫 번째 단계에서는, 단순히 임시 앱을 빌드하고, Xcode 파일 탐색기에서 .mlmodel 파일을 클릭한 다음 Model Class 옆에 우리 CoreML 모델의 이름을 클릭하세요.

<div class="content-ad"></div>

```markdown
![ML model class file](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_1.png)

This will take you to the class file of our CoreML model, which acts as the interface between the model and our app. It allows us to provide information and receive a processed response from it.

![Show in Finder](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_2.png)

While completing the remaining steps, we need to create a copy of this file and save it in another folder. Right-click on the file, choose "Show in Finder," and create a duplicate.
```

<div class="content-ad"></div>

```markdown
![ML model in Swift Playgrounds](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_3.png)

![ML model in Swift Playgrounds](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_4.png)

다음 단계는 CoreML 모델의 컴파일된 버전을 획득하는 것입니다. 이를 위해 먼저 응용 프로그램의 컴파일된 파일, 빌드에 액세스해야 합니다. 이를 찾으려면 Finder를 열고 명령 ⌘ + Shift + G를 사용하세요. 그런 다음 다음 경로로 이동하세요:

~/Library/Developer/Xcode/DerivedData
```

<div class="content-ad"></div>

이 디렉터리 내에서 임시 앱 이름으로된 폴더를 찾아 들어가야 해요.

![Folder 5](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_5.png)

폴더 안으로 들어가서 Build/Products/Debug-iphonesimulator로 이동해주세요.

![Folder 6](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_6.png)

<div class="content-ad"></div>

![그림1](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_7.png)

![그림2](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_8.png)

여기서 우리는 임시 앱 이름으로 된 파일을 찾을 수 있습니다. 이 파일에 마우스 오른쪽 버튼을 클릭하고 "Package Contents 표시"를 선택합니다.

![그림3](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_9.png)

<div class="content-ad"></div>

여기서 우리가 원하던 것을 찾을 수 있습니다: 컴파일된 ML 모델, .mlmodelc 파일(c는 컴파일된 형태를 의미합니다). 클래스 파일과 마찬가지로 이 파일의 사본을 만들어 쉽게 접근할 수 있는 위치에 저장하세요.

![이미지](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_10.png)

이 두 파일을 가지고 있다면, 모델을 변경하거나 업데이트할 일이 없는 한 임시 앱에 대해서 잊을 수 있습니다. 모델을 변경하려면 이전 단계를 반복해야 합니다. 이제 우리는 플레이그라운드 프로젝트로 넘어가 마지막 단계를 수행하겠습니다.

## 두 번째 단계: 플레이그라운드 설정하기

<div class="content-ad"></div>

이제 프로젝트를 구하는 데 도움이 된 단계로 넘어갑시다. 모델을 사용하려면 플레이그라운드를 마무리하려면 몇 가지 최종 조정이 필요합니다. 먼저, 클래스 파일을 프로젝트 내에 배치하십시오. 추가로, ML 모델을 넣을 폴더를 만들고 다른 것은 아무것도 넣지 마십시오. 저는 편의상 MLFile이라는 이름을 붙였습니다.

![이미지](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_11.png)

이제 Xcode를 닫고 Finder에서 프로젝트 위치로 이동해야 합니다. 파일에서 마우스 오른쪽 버튼을 클릭하고 "패키지 내용 보기"를 선택하십시오.

![이미지](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_12.png)

<div class="content-ad"></div>

이것은 Playgrounds 프로젝트의 내용을 보여줄 것입니다. 그 안에서 Package.swift 파일을 찾아 열어봅니다.

![Package.swift 파일](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_13.png)

이 파일 안에는 프로젝트 구성 정보가 있습니다.

![프로젝트 구성](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_14.png)

<div class="content-ad"></div>

targets 섹션 내에서 .executableTarget() 메서드로 이동하여 아래 내용을 매개변수로 추가해주세요:

```js
resources: [
                .process("Resources"),
                .copy("<ModelFolderName>/<YourModelName>.mlmodelc")
            ]
```

저의 프로젝트에서는 다음과 같이 나타날 겁니다:

![이미지](/assets/img/2024-05-18-HowtouseaMLmodelinSwiftPlaygrounds_15.png)

<div class="content-ad"></div>

## 이제 모두 준비되었습니다!

좋아요! 이제 Swift Playground App 프로젝트 내에서 우리의 ML 모델을 사용할 수 있게 되었습니다. 이 변경이 필요한 이유는 Playground가 일반 프로젝트와 다르게 작동하기 때문입니다. 우리가 리소스를 사용할 때 Playground는 프로젝트의 구조를 무시하고, 만들어 둔 폴더 구조를 존중하는 대신에 포함된 파일을 추출하여 모두 한데 모읍니다. 이는 동일한 이름의 파일이 다른 폴더에 있더라도 컴파일 중 충돌을 일으킬 수 있다는 것을 의미합니다. 이는 우리의 Swift Package 내 .process() 메서드의 덕분입니다. .copy()를 구현함으로써, 우리가 지정한 폴더나 파일에 대해 폴더 구조가 존중되도록 보장합니다.

이제 우리 프로젝트가 요구하는 CoreML 구현을 계속하면 됩니다. 이 해결책이 여러분께 몇 시간의 불면의 밤을 덜어주고 프로젝트를 더 나은 수준으로 이끌 수 있기를 바랍니다. 행운을 빕니다!

도움이 필요하거나 중간에 질문이 생기면 언제든지 연락해 주세요!