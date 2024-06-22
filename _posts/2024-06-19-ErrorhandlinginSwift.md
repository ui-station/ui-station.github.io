---
title: "Swift에서의 오류 처리"
description: ""
coverImage: "/assets/img/2024-06-19-ErrorhandlinginSwift_0.png"
date: 2024-06-19 11:08
ogImage: 
  url: /assets/img/2024-06-19-ErrorhandlinginSwift_0.png
tag: Tech
originalTitle: "Error handling in Swift"
link: "https://medium.com/@blorenzop/error-handling-in-swift-f9ca87490e26"
---


## 사용자에게 경고하기 위해 do-catch 문과 사용자 정의 오류를 던지는 방법 (코드 예제와 함께)

에러 처리는 모든 앱의 기본적인 부분입니다.

사용자가 수행하는 모든 조치가 성공하는 것은 아닙니다. 일부는 실패할 것이며, 우리의 앱은 사용자에게 일어난 일을 쉽게 알려주고 문제를 피하기 위해 무엇을 시도할 수 있는지 알려줘야 합니다.

간단한 throw 및 do-catch 문을 사용하여 간단한 에러 처리를 어떻게 달성할 수 있는지 보여드릴게요.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-에러핸들링인Swif_0.png" />

## 에러 정의 및 던지기

에러를 표현하려면 Error 프로토콜을 준수하는 타입을 사용해야 합니다. 이는 아무런 제약이 없는 빈 프로토콜이며, 우리가 가장 잘 적합한 타입으로 에러를 정의할 수 있다는 것을 의미합니다.

일반적으로 enum이 시작하기에 가장 좋은 방법입니다. 네트워크 에러를 처리하고 싶다고 가정해보면, 아래 enum을 정의할 수 있습니다👇

<div class="content-ad"></div>

```js
열거형 NetworkError: 오류 {
    case unexpected
    case invalidURL(_ url: URL)
    case apiError(statusCode: Int)
}
```

백엔드 API로 요청을 수행할 때 오류가 발생하면 NetworkError를 던질 수 있습니다.

```js
열거형 ApiMethod {...}
구조체 ApiRequest {...}

func createRequest(from url: String, method: ApiMethod) throws -> ApiRequest {
  guard let URL = URL(string: url) else {
    throw NetworkError.invalidURL(url)
  }
  ...
}
```

## 오류 처리

<div class="content-ad"></div>

우리는 사용자 정의 오류를 갖고 있고, 이를 throw하고 있습니다. 하지만, 이를 어떻게 잡을 수 있을까요?

다른 많은 언어와 마찬가지로, Swift에는 do-catch 문이 있습니다. 만약 do 절 내에서 오류가 throw되면, 실행은 catch 절로 전달되고 오류는 로컬 상수 error에 저장됩니다.

```js
do {
  let result = try createRequest(from: "//myapi.com/", method: .GET)
} catch {
  print(error)
  // 적절한 처리를 수행
}
```

가끔씩 우리는 잡은 오류 형식(또는 오류 값)에 따라 특정 작업을 트리거하길 원합니다. 이를 패턴을 사용하여 수행할 수 있습니다.

<div class="content-ad"></div>

```js
do {
  let result = try makeRequest(to: "//myapi.com/", method: .GET)
} catch NetworkError.invalidUrl(let url) {
  // ...
} catch {
  print(error)
  // 
}
```

## 에러 전파하기

던져진 함수는 함수 내에서 발생한 에러를 호출자의 범위로 전파할 수 있습니다.

```js
struct User: Decodable {
    let name: String
    let age: Int
}

func decodeJSON<T: Decodable>(_ jsonString: String) throws -> T {
    let jsonData = Data(jsonString.utf8)
    let decodedObject = try JSONDecoder().decode(T.self, from: jsonData)
    return decodedObject
}

func getUserFromJSON(_ jsonString: String) -> User? {
    do {
        let user: User = try decodeJSON(jsonString)
        return user
    } catch {
        print(error)
        return nil
    }
}
```

<div class="content-ad"></div>

## try? & try!

가끔은 던지는 함수로 발생한 오류에 대해 신경 쓰지 않을 수 있습니다. 이런 상황에서는 do-catch 문을 사용하지 않고 try? 지시어를 사용할 수 있습니다.

try?를 사용하면 호출하는 함수가 옵셔널이 됩니다. 즉, 반환 값을 가진 함수를 호출하는 경우 값은 nil이 됩니다.

```js
let request = try? createRequest(from: "malformedurl", method: .GET)
// request = nil
```

<div class="content-ad"></div>

한편, 때때로 특정한 던지기 함수가 런타임에서 실패하지 않을 것을 확신할 수 있습니다. 이런 경우에는 try!를 사용할 수 있습니다. 그리고 try?와 마찬가지로 do-catch 클로저를 사용할 필요가 없습니다.

```js
let request = try! createRequest(from: "https://myapi.com/", method: .GET)
```

## 비동기 코드

에러를 던지는 것은 비동기 코드와 잘 작동합니다. 함수 선언에서 throws 키워드 앞에 async 키워드를 넣어주어야 합니다.

<div class="content-ad"></div>

비동기 함수를 호출할 때는 try await (키워드 순서 스와이프)를 사용합니다.

```js
func performRequest(with request: ApiRequest) async throws -> ApiResult {...}

// ----------------

do {
  let request = try makeRequest(to: "https://myapi.com/users", method: .GET)
  let apiRestul = try await performRequest(with: request)
} catch NetworkError.invalidUrl(let url) {
  // ...
} catch {
  print(error)
  // 
}
```

# 사용자에게 알림

에러 구조를 구축했으니, 이제 사용자들에게 무엇이 잘못되었는지 알려줄 필요가 있습니다.

<div class="content-ad"></div>


<img src="https://miro.medium.com/v2/resize:fit:592/1*cfrDQghry5EPpPhMSFFDaA.gif" />

각각의 오류에 대한 설명 은 오류가 처음에 발생한 이유입니다.
사용자가 사용자를 피할 수 있는 가능한 조치입니다.

우리는 Error enum에 해당 정보를 변수로 추가할 수 있습니다. 그러나 이미 모든 이 정보를 그룹화하기 위한 프로토콜이 있습니다: LocalizedError. 우리의 사용 사례에서 우리는 failureReason 및 recoverySuggestion 변수 만 구현하면 됩니다.


<div class="content-ad"></div>

```swift
enum OrderError: LocalizedError {
  case unexpected
  case outOfCoffee(coffee: Coffee)
  case minimumNotMet(currentPrice: Float)
  
  var failureReason: String? {
    switch self {
    case .unexpected:
      return "주문 처리 중 문제가 발생했습니다."
    case .outOfCoffee(let coffee):
      return "\(coffee.name)가 다 떨어졌어요."
    case .minimumNotMet:
      return "앱에서 주문하려면 최소 $10 이상 사용해야 합니다."
    }
  }
  
  var recoverySuggestion: String? {
    switch self {
    case .unexpected:
      return "몇 분 후에 다시 시도해주세요."
    case .outOfCoffee:
      return "다른 종류의 커피로 변경해보세요."
    case .minimumNotMet(let currentPrice):
      return "주문에 $\(10-currentPrice) 더 추가해주세요."
    }
  }
}
```

여기에서 작업이 완료됩니다. 그러나 앱에 통합된 로깅 솔루션이 있다면, 오류 구조를 확장하여 추가 정보를 추가할 수 있습니다.

```swift
import Foundation
import HKLogger

struct AppError: Error {
  let type: LocalizedError
  var userMessage: String {
    return "\(type.failureReason ?? ErrorConstants.defaultError) \n\n \(type.recoverySuggestion ?? ErrorConstants.defaultAction)"
  }
  // 필요한 만큼 많은 속성을 추가
  
  init(type: LocalizedError, debugInfo: String? = nil) {
      self.type = type
      guard let debugInfo else { return }
      HKLogger.shared.error(message: debugInfo)
  }
}
```

이렇게 함으로써 로깅 오류 로직을 한 곳에 중앙 집중화할 수 있습니다.


<div class="content-ad"></div>

마침내 사용자 정의 AppError를 사용하여 경고를 표시하는 간단한 View 확장을 만들 수 있습니다.

```js
extension View {
    func alert(isPresented: Binding<Bool>, withError error: AppError?) -> some View {
        return alert(
            "Ups! :(",
            isPresented: isPresented,
            actions: {
                Button("Ok") {}
            }, message: {
                Text(error?.userMessage ?? "")
            }
        )
    }
}

// ---------------------------------------------------

struct OrderConfirmationView: View {
  @StateObject private var viewModel = ConfirmationViewModel()

  var body: some View {
    VStack(alignment: .leading) {
      // ...
      Button("주문하기") {
        viewModel.tryToPlaceOrder(order)
      }
    }
    .alert(isPresented: $viewModel.showError, withError: viewModel.appError)
  }
}

// ---------------------------------------------------

@Observable
final class ConfirmationViewModel: ObservableObject {
    var appError: AppError?
    var showError = false
    
    func tryToPlaceOrder(_ order: Order) {
        do {
            try OrdersManager.shared.add(order)
        } catch let error as AppError {
            showError = true
            appError = error
        } catch {
            appError = AppError(type: OrderError.unexpected)
        }
    }
}

// ---------------------------------------------------

final class OrdersManager {
  // ...
  func add(_ order: Order) throws {
    if order.price >= 10 {
      orders.append(order)
    } else {
      throw AppError(type: OrderError.minimumNotMet(currentPrice: order.price))
    }
  }
}
```

질문이 있으신가요? 언제든지 메시지를 남겨주세요! 🙂

- 🤓 iOS 개발 팁과 통찰력에 관한 콘텐츠를 X에서 함께 확인해보세요.
- 🚀 모든 예제 프로젝트를 공유하는 내 GitHub를 확인해보세요 (이것도 포함됩니다 😉)