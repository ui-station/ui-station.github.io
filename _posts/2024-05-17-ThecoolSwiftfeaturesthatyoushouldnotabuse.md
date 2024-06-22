---
title: "악용하지 말아야 할 멋진 Swift 기능들"
description: ""
coverImage: "/assets/img/2024-05-17-ThecoolSwiftfeaturesthatyoushouldnotabuse_0.png"
date: 2024-05-17 18:48
ogImage: 
  url: /assets/img/2024-05-17-ThecoolSwiftfeaturesthatyoushouldnotabuse_0.png
tag: Tech
originalTitle: "The cool Swift features that you should not (ab)use"
link: "https://medium.com/@SaezChristopher/the-cool-swift-features-that-you-should-not-ab-use-61070b70fad9"
---



![ThecoolSwiftfeaturesthatyoushouldnotabuse](/assets/img/2024-05-17-ThecoolSwiftfeaturesthatyoushouldnotabuse_0.png)

iOS 개발자로서, 나는 많은 기존 코드에서 작업하거나 몇 가지 Swift 기능을 시도해 본 적이 있습니다. 그들을 사용하거나 악용 당하는 것을 보면서, 적어도 조심스럽게 사용해야 할 상위 6개의 Swift 언어 기능을 공유하고, 왜 그것들을 팀에서 금지해야 할지 생각했습니다. 간단히 살펴보겠습니다. 동의하지 않거나 다른 예제가 있는 경우 댓글에서 공유해주세요 💬.

# .init() 인스턴스화

.init()을 사용하는 것은 처음 봤을 때 몇 가지 경우에 편리할 수 있지만 (클래스/구조체 이름 바꿀 때 노이즈가 적음), 코드 검토 및 유지보수 시 가독성이 저하됩니다. 코드 검토에서는 호출자로 이동하여 객체의 유형을 확인해야 하며, 유지보수 중에는 객체를 확인하려면 이동해야 합니다. 팀의 생산성을 높이기 위해 사용하지 않아야 합니다.


<div class="content-ad"></div>

```js
//To avoid ❌
let example = .init(title: "title")
//To prefer ✅
let example = Example(title: "title")
```

Swiftlint을 사용하면 이 좋은 규칙을 강제로 적용할 수 있어요. 
Swiftlint를 사용하신다면, 프로젝트에서 사용해야 하는 최상의 Swiftlint 설정에 관한 기술적인 문서를 작성했어요 ⬇️

# $0, $1, 등...

$0는 매우 편리하지만 코드 리뷰에서는 코드를 덜 가독성 있게 만들 수 있어요(이 게시물이 말합니다). 


<div class="content-ad"></div>


// ❌를 피하기 위해
numbers.sort { $0 > $1 }
// ✅를 선호하기 위해
numbers.sort { leftNumber, rightNumber in leftNumber > rightNumber }


# 서브스크립트

서브스크립트 기능은 처음 봤을 때 매우 매력적으로 보입니다:

```js
struct TimesTable {
    let multiplier: Int
    
    subscript(index: Int) -> Int {
        return multiplier * index
    }
}

let table = TimesTable(multiplier: 3)
let result = table[5]  // 결과 = 15
print(result)
```

<div class="content-ad"></div>

서브스크립트가 그렇게 나쁘지는 않지만 테이블[5]을 읽는 일반적인 개발자들은 테이블이 배열이라고 생각하고 다섯 번째 항목에 접근하려는 것으로 생각할 것입니다. 이는 바로 그게 아니기 때문에 코드 리뷰나 파일 간, 모듈 간 사용 시 가독성이 떨어집니다. 논리적인 코드 난독화는 피해야 합니다. 이겪하고자 하는 점이 명확하지 않아서 널리 사용되지 않는 것 때문에 이것을 추천하지는 않습니다.

# 확장(Extensions) 남용

확장은 가독성과 모듈화를 높입니다. 디자인 상, 보호기능이 없는 특성이며 난잡한 코드나 불필요한 사용 또는 안티패턴으로 이어질 수 있습니다.

먼저, 이 규칙을 활성화하지 않았다면, 활성화해야 합니다. 이 규칙은 확장의 일부 불필요한 사용을 감지합니다. 예를 들어, ViewController의 델리게이트를 같은 파일에서 확장을 사용해 구현하지만 어떤 프로토콜 준수 제한도 없거나 그러한 것을 분할하는 경우 등을 감지합니다.

<div class="content-ad"></div>

```js
class MyViewController: UIViewController{
  private var IBOutlet textfield: UITextField
  
  textfield.delegate = self
...
}

// To Avoid❌
extension UIViewController: UITextViewDelegate {

}
```

올바르지 않은 SOLID 확장 사용 예시:

```js
container.register(Buyable.self, NetworkManager())

class NetworkManager { 
  var user: User
}

protocol Buyable {
  func canBuy() -> Bool
}

In Buy.swift
extension NetworkManager: Buyable {
  @Inject var cart: CartStore

  func canBuy() -> Bool {
    if cart.hasEnough { return true} else { return false }
    //we can still access to user here...
  }

}

class CartViewModel {
  @Inject var buy: Buyable // Illusion to have a Buyable

  func addItem() {
    if buy.canBuy() { /* */ }

  }

}
```

우리는 Buyable이 Buy 객체의 프로토콜일 수 있다는 환상을 갖고 있지만, 전혀 그렇지 않습니다. 이를 서로 다른 파일에 걸쳐 사용하면서 매우 지저분한데다가 혼란스럽습니다. 하지만... 작동합니다. (코틀린에서는 이렇게 하는 것이 불가능합니다).


<div class="content-ad"></div>

# Property wrappers

테이블 태그를 Markdown 형식으로 변경하면 다음과 같습니다.

I love them, for writing cross native code, property wrappers is an awesome feature. First usecase from the swift community was to use them for improving the usage of Injection (like Swinject), can lead to read source code like a dagger-hilt style and it’s the equivalent of the delegated properties in Kotlin. But like Kotlin, it has the same drawbacks: readability / logic obfuscation / spagetthi architecture.

```js
@propertyWrapper
struct Sanitized {
    private(set) var value: String = ""
    
    var wrappedValue: String {
        get { value }
        set { value = newValue.trimmingCharacters(in: .whitespacesAndNewlines) }
    }
    
    init(wrappedValue initialValue: String) {
        self.wrappedValue = initialValue
    }
}

struct User {
    @Sanitized var username: String
}

// Exemple d'utilisation
var user = User(username: "   Chris   ")
user.
print(user.username) // Output: "Chris"
```

How can we really use this as a business rule implementation? If we want to add additional features to our string, we will have to add them inside our Sanitized property wrapper or write them outside to maintain a single responsibility principle. Why isn`t Sanitized a function of your User? It may create inconsistency. Property wrappers should be used wisely and could be replaced by small extensions 💡.

<div class="content-ad"></div>

# 게터/세터 재정의

이것은 정사각형/속성 감싸기와 거의 비슷합니다. didSet의 사용은 로직을 혼란스럽게 만들 수 있으며 이해하기 어렵게 만들 수 있습니다:

```js
struct Temperature {
    var celsiusValue: Double {
        didSet {
            print("무슨 일이 일어났어요")
        }
    }
    
    init(celsiusValue: Double) {
        self.celsiusValue = celsiusValue
    }
}

var temp = Temperature(celsiusValue: 20)
temp.celsiusValue = 25
//무슨 일이 일어났어요
```

<div class="content-ad"></div>

이건 명백해 보일지 모르지만 때로는 강제 언래핑 할 때 어떻게 해야할지 모르겠어요 (그리고 이것이 꼭 멋진 기능은 아닐 수 있어요) - 함수 호출이 확실히 동작할 것이라고 가정하는 것이 유혹스러울 수 있습니다 (하지만 옵셔널 값을 반환할 수도 있어요). 그렇다면, 만약 여러분의 코드가 아니라면 결국 실패할 수 있는 가능성을 가정해야 해요. 예를 들면:

```js
// SPM에서 UIFont를 로드하려고 하는 경우
registerFont("font.ttf") // font가 로드된 것으로 가정합니다

// ❌ 하지마세요
let font = UIFont(name: "font.ttf")!

// 하세요
guard let font = UIFont(name: "font.ttf") else {
    Crashlytics.record("font.ttf failed to be registered") // 충돌을 모니터링합니다
    return // 또는 fatalError() 또는 기본 폰트를 인스턴스화하세요
}
```

# 결론

고급 언어 기능 사용은 라이브러리나 구조적인 프레임워크/사용자 정의 아키텍처를 사용하는 것과 유사합니다: 현명하게 사용되어야 하며, 팀으로 수용되어야 합니다. 새로운 프로젝트에 참여하는 새로운 사람이 이 코드 라인을 읽었을 때, 쉽게 이해할 수 있는지 아닌지 고려해야 합니다. 학습은 함의하거나 빛나는 새로운 기능을 남용하지 않는 시점을 인식하거나 더 전통적인 방식으로 돌아가야 하는 시점을 받아들이는 것 또한 포함됩니다 😀.