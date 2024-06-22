---
title: "Swift에서 weak와 unowned의 차이점 예제 포함"
description: ""
coverImage: "/assets/img/2024-06-22-DifferencesbetweenweakandunownedinSwiftwithexamples_0.png"
date: 2024-06-22 23:14
ogImage: 
  url: /assets/img/2024-06-22-DifferencesbetweenweakandunownedinSwiftwithexamples_0.png
tag: Tech
originalTitle: "Differences between weak and unowned in Swift with examples"
link: "https://medium.com/@quasaryy/differences-between-weak-and-unowned-in-swift-with-examples-d6a54357dd1c"
---


<img src="/assets/img/2024-06-22-DifferencesbetweenweakandunownedinSwiftwithexamples_0.png" />

# 소개

Swift에서는 메모리 관리가 ARC(Automatic Reference Counting)를 통해 이루어집니다. weak 및 unowned 참조의 주요 개념을 이해하는 것은 메모리 누수와 강한 참조 순환을 방지하는 데 매우 중요합니다. 이러한 참조들이 어떻게 다르며 올바르게 사용하는 방법을 알아보겠습니다.

# weak 참조

<div class="content-ad"></div>

약한 참조는 한 객체가 다른 객체 없이 존재할 수 있을 때 사용됩니다. 이는 객체의 참조 카운트를 증가시키지 않아 강력한 참조 순환을 방지하는 데 도움이 됩니다.

약한 참조의 특징:
- 가리키는 객체가 해제될 수 있기 때문에 항상 옵셔널 변수(var)로 선언됩니다. 이 경우에 참조는 nil이 됩니다.
- 델리게이트와 클로저를 사용할 때 메모리 누수를 방지하는 데 특히 유용합니다.

약한 참조를 사용한 예시:

<div class="content-ad"></div>

```swift
class Department {
    var manager: Employee?

    deinit {
        print("Department is being deinitialized")
    }
}

class Employee {
    weak var department: Department?

    deinit {
        print("Employee is being deinitialized")
    }
}

var department: Department? = Department()
var manager: Employee? = Employee()

department?.manager = manager
manager?.department = department

department = nil
manager = nil
// Prints: "Employee is being deinitialized" and then "Department is being deinitialized"

```

이 예시에서, Department와 Employee간의 연결을 끊음으로써 메모리 누수를 피할 수 있습니다.

## 클로저에서 weak 사용

클로저에서 weak를 사용하는 것은 강한 참조 순환을 방지하기 위해 종종 필요한데, 특히 클로저가 self, 즉 클래스 인스턴스를 캡처할 때입니다.


<div class="content-ad"></div>

ViewController 클래스는 비동기 작업을 수행하는 클래스입니다. 이 작업이 완료된 후에 코드가 실행되도록 하고, 동시에 강한 참조 순환에 의한 메모리 누수를 방지해야 합니다.

weak 키워드를 사용한 예시:

```js
class ViewController: UIViewController {
    var dataLoader: DataLoader?

    func fetchData() {
        dataLoader?.loadData(completion: { [weak self] result in
            guard let self = self else { return }

            switch result {
            case .success(let data):
                self.updateUI(with: data)
            case .failure(let error):
                self.showErrorMessage(error)
            }
        })
    }

    private func updateUI(with data: Data) {
        // 사용자 인터페이스 업데이트
    }

    private func showErrorMessage(_ error: Error) {
        // 에러 메시지 표시
    }
}

class DataLoader {
    func loadData(completion: @escaping (Result<Data, Error>) -> Void) {
        // 데이터 로드 코드
    }
}
```

위 예시에서:

<div class="content-ad"></div>

- ViewController에는 DataLoader의 loadData 메서드를 호출하는 fetchData 메서드가 있습니다.
- loadData에 전달된 클로저 내에서 [weak self]를 사용하여 ViewController의 인스턴스인 self에 강한 참조를 방지합니다. 이는 DataLoader가 클로저를 오랫동안 유지할 수 있기 때문에 중요합니다. 예를 들어 비동기 작업 중에 발생할 수 있습니다.
- 클로저 내에서 self에 안전하게 액세스하기 위해 guard let self = self else 'return'을 사용합니다. 만약 ViewController가 클로저가 실행되기 전에 해제되면 self는 nil이 되어 클로저 내의 코드가 실행되지 않아 잠재적인 오류나 충돌을 방지합니다.

# unowned 참조

unowned 참조는 weak와 유사하지만 두 가지 주요 차이점이 있습니다: 옵셔널이 아니며, 가리키는 객체가 해제될 때 nil이 되지 않습니다.

## unowned 참조의 특징:

<div class="content-ad"></div>

- 다른 객체가 해제되기 전까지 한 객체를 해제하지 않을 때 사용됩니다.
- 객체가 해제된 후 비소유 참조에 접근하면 충돌이 발생합니다.
- 상수(let)와 함께만 작동합니다.

비소유 참조를 사용한 예시:

```js
class Customer {
    let name: String
    var card: CreditCard?
    
    init(name: String) {
        self.name = name
    }

    deinit {
        print("\(name) 해제 중")
    }
}

class CreditCard {
    let number: UInt64
    unowned let customer: Customer

    init(number: UInt64, customer: Customer) {
        self.number = number
        self.customer = customer
    }

    deinit {
        print("카드 #\(number) 해제 중")
    }
}

var john: Customer? = Customer(name: "John")
john!.card = CreditCard(number: 1234_5678_9012_3456, customer: john!)

john = nil
// 출력: "John 해제 중" 그리고 "카드 #1234567890123456 해제 중" 
```

여기서 Customer가 해제된 후 관련된 CreditCard 객체도 해제되어 메모리 누수를 방지합니다.

<div class="content-ad"></div>

## 약한 참조와 미소유 참조 비교

- 선언: 약한 참조는 항상 옵셔널이지만, 미소유 참조는 상수로 값이 옵셔널이 아닙니다.
- 강한 참조 순환: 둘 다 강한 참조 순환을 방지하지만, 각각 다른 상황에서 사용됩니다.
- 안전성: 약한 참조는 객체가 해제될 때 자동으로 nil이 되어 안전합니다. 미소유 참조는 객체가 파괴되면 크래시가 발생할 수 있습니다. 무엇을 하는지 잘 알아야 합니다.

# 결론

Swift에서 약한 참조와 미소유 참조의 차이를 이해하는 것은 안전하고 효율적인 메모리 관리를 위해 중요합니다. 두 참조 사이의 선택은 애플리케이션의 구조와 객체 간 관계에 따라 다릅니다. 항상 메모리 누수를 확인하여 신뢰성과 성능을 보증할 수 있도록 코드를 테스트하세요.