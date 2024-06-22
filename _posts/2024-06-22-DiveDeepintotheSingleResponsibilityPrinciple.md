---
title: "단일 책임 원칙SRP에 깊이 파고들기 더 좋은 코드 작성 방법 알아보기"
description: ""
coverImage: "/assets/img/2024-06-22-DiveDeepintotheSingleResponsibilityPrinciple_0.png"
date: 2024-06-22 23:04
ogImage: 
  url: /assets/img/2024-06-22-DiveDeepintotheSingleResponsibilityPrinciple_0.png
tag: Tech
originalTitle: "Dive Deep into the Single Responsibility Principle"
link: "https://medium.com/@krishnapalsinhgohil/dive-deep-into-the-single-responsibility-principle-6f6c95182f02"
---


## SRP를 수용하는 것이 어떻게 코딩 방법을 변화시키고 확장 가능한 소프트웨어를 이끌어낼 수 있는지 알아보세요.

![SRP](/assets/img/2024-06-22-DiveDeepintotheSingleResponsibilityPrinciple_0.png)

# SOLID 원칙이란 무엇인가요?

모든 직업에는 준수해야 할 표준과 엄격한 규칙이 있으며, 이를 어기는 경우 중요한 결과가 발생할 수 있습니다.

<div class="content-ad"></div>

예를 들어, 건축가가 건물 규정을 준수하여 구조물의 안전과 안정성을 보장해야 하는 방법을 생각해보세요.

이러한 기준들이 왜 만들어졌을까요?

- 많은 사람들이 동일한 과정을 거쳤고 그 경험은 모범 사례에 대한 집단적인 이해를 기여합니다.
- 표준을 준수하는 것은 실수 가능성을 줄입니다. 전문가들이 따를 수 있는 명확하고 검증된 방법을 제공하여 일관성과 신뢰성을 보장합니다.
- 표준은 모든 잠재적인 문제 영역에 대한 지식을 포괄합니다. 이러한 문제를 다루고 완화하는 방식으로 정의되어 있습니다.

마찬가지로, 소프트웨어 개발에서,

<div class="content-ad"></div>

SOLID 원칙은 깨끗하고 유지보수가 쉽고 견고한 코드를 작성하기 위한 표준을 제공하기 위해 만들어졌어요.

- 이러한 원칙은 많은 개발자들의 경험과 소프트웨어 설계에서 가장 잘 작동하는 것에 대한 지식을 바탕으로 합니다.

SOLID 원칙을 준수함으로써, 개발자들은 오류를 최소화하고 잠재적인 문제를 해결하며 이해하기 쉽고 확장 및 유지보수가 쉬운 소프트웨어를 만들 수 있어요.

# 단일 책임 원칙(SRP)란 무엇인가요?

<div class="content-ad"></div>

- 이제 모든 개발자는 "책임"을 각자 다르게 해석할 수 있습니다
- 컴포넌트를 디자인할 때, 한 명의 개발자는 전체 컴포넌트의 생성을 단일 책임으로 간주할 수 있습니다
- 또 다른 개발자는 뷰 및 네트워크 호출을 별도의 관심사로 처리하도록 더 세분화된 책임 분할을 옹호할 수 있습니다

명확하게 하기 위해, 우리는 종종 SRP를 클래스가 하나의 변경 이유만을 가져야 한다는 개념으로 표현합니다.

간단히 말해서, 클래스가 여러 이유로 수정이 필요한 경우 SRP를 위반합니다.

## 이젠 ProfileVC가 이 원칙을 어기는지 살펴봅시다

<div class="content-ad"></div>

```swift
class ProfileVC: UIViewController {
  override func viewDidLoad() {
    fetchImageFromAPI()
  } 

  func fetchImageFromAPI() {
    APIService.callAPI(with: url) { response in 
      handleResponse(response: response)
    }
  }

  func handleResponse(response: Result<Data, Error>) {
    switch response {
    case .success(let data):
      processImage(data: data)
    case .failure(let error):
      print("Failed to fetch data: \(error)")
   }
  }

  func processImage(data: Data) {
    // process image
    displayDataOnUI(data: data)
  } 

  func displayDataOnUI(data: Data) {
    print("Updating UI with data")
  }

}
```

`ProfileVC`는 다음과 같은 책임을 갖습니다:
- 서비스에서 데이터를 가져오기 [위반]
- 응답 처리하기 [위반]
- 이미지 처리하기 [위반]
- 이미지 표시하기 [유효]

# ProfileVC에 단일 책임 원칙(SPR) 적용해보기

- ImageLoader


<div class="content-ad"></div>


// ImageLoader는 오직 한 가지 책임만을 가지고 있습니다.
// 네트워크에서 이미지를 로드하고 전달하는 것입니다.

class ImageLoader {

  func fetchImageFromAPI(completion: @escaping ResultCompletion) {
    APIService.callAPI(with: url) { response in 
      completion(response)
    }
  }

}


- ImageDecoder


// ImageDecoder
// 사실상, base64 이미지를 파싱하거나
// 유사한 작업을 통해 다음 해당 인스턴스로 전달할 것입니다.
// 디코딩 로직이 다른 구성 요소에서 수행되어야 함을 보여주기 위한 예시입니다.

class ImageDecoder {
  let result: ResultCompletion
  
  init(...) {
    self.result = result
  }
  
  func decodeImage(completion: ...) -> Void)  {
     switch result {
      case .success(let data):
        // 디코딩 처리
        completion(success(data))
      case .failure(let error):
        completion (.failure(error))
     }
  }

}


- ImageProcessor


<div class="content-ad"></div>

```js
// ImageProcessor  

class ImageProcessor {
  let imageData: Data
  
  init(...) {
    self.imageData = imageData
  }
  
  func processData(completion: ...) -> Void)  {
    // Process image and pass further to the chain 
  }

}
```

- ProfileVC

```js
// ProfileVC  

class ProfileVC: UIViewController {

  var load: (() -> Void)?

  func viewDidLoad() { 
    load()
  } 

  func displayImage(_ data: Data) -> Void)  {
    profileImage.image = UIImageFromData(data)
  }

}
```

ProfileVC now only has a display method that will present the changes to the user

<div class="content-ad"></div>

## 이제 조립이 시작됩니다, 실제 작업

여기에 모든 구성 요소가 결합됩니다

```js
// ProfileComposer  
// 이것은 이해하기 쉽도록 가장 간단한 조합 형태입니다
// 권장하는 방법은 디자인 패턴을 사용하는 것입니다. 

final class ProfileComposer {
  func makeProfileVC() {
    let profileVC = ProfileVC()

    profileVC.load = {
      imageLoader.loadImage() {
         imageDecoder.decodeImage() {
            imageProcessor.processImage() { data in
                profileVC.displayImage(data)
            }
         }
      }
    }

    // ProfileVC의 탐색 처리

  }
}
```

# 이점은 무엇인가요?

<div class="content-ad"></div>

## 유지 관리성, 재사용성 및 명확성

ImageLoader, ImageProcessor 및 ImageResponseHandler와 같은 개별 구성 요소로 기능을 분리하면 유지 관리성이 향상되고 재사용성이 높아지며 명확한 책임을 가지므로 코드 가독성이 향상됩니다.

## 추상화

ProfileVC는 특정 데이터 원본과 분리되어 있어 UI 표현에만 집중할 수 있도록 해 유지할 수 있습니다.

<div class="content-ad"></div>

## 향상된 테스트 용이성

구성 요소가 격리되면 외부 종속성없이 개별 기능의 심층 테스트 또는 모의적 사용이 가능해집니다.

## 결합도 감소

SRP를 따르면 구성 요소 간 종속성이 최소화되어 의도하지 않은 상호 작용의 위험을 낮추고 시스템의 유연성과 견고함을 향상시킵니다.