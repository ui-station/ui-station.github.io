---
title: "Combine의 퍼블리셔와 구독자 프로토콜 이해하기 Swift 개발자를 위한 가이드"
description: ""
coverImage: "/assets/img/2024-06-27-UnderstandingPublisherandSubscriberProtocolsinCombineSwift_0.png"
date: 2024-06-27 19:36
ogImage: 
  url: /assets/img/2024-06-27-UnderstandingPublisherandSubscriberProtocolsinCombineSwift_0.png
tag: Tech
originalTitle: "Understanding Publisher and Subscriber Protocols in Combine Swift"
link: "https://medium.com/@tejaswinimr702/understanding-publisher-and-subscriber-protocols-in-combine-swift-01c86d46e402"
---


<img src="/assets/img/2024-06-27-UnderstandingPublisherandSubscriberProtocolsinCombineSwift_0.png" />

# 소개

Swift 프로그래밍 세계에서 비동기 데이터 스트림을 관리하는 것은 복잡한 작업일 수 있습니다. Apple의 Combine 프레임워크는 반응형 프로그래밍을 통해이 프로세스를 간소화합니다. 이 글은 실제 세계의 비유와 간단한 예제를 사용하여 Combine에서의 Publisher 및 Subscriber 프로토콜을 해석하여 이러한 개념을 모든 사람이 이해할 수 있도록 만들 것입니다.

# Combine이란 무엇인가요?

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

# Publisher Protocol

Combine에서의 Publisher는 시간에 따른 값의 시퀀스를 생성하는 데 책임이 있습니다. 세 가지 유형의 이벤트를 발생시킬 수 있습니다:

- 값: 방송되는 데이터.
- 완료: 방송이 끝났음을 나타내는 신호.
- 실패: 오류가 발생했음을 나타내는 신호.

실제 예시: 매월 구독하는 잡지(발행인)에 가입한다고 상상해보세요. 매월 새 호가 도착합니다(값). 결국 구독이 종료될 수 있습니다(완료) 또는 잡지가 폐업할 수도 있습니다(실패).

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
import Combine

let magazinePublisher = Just("Monthly Swift Programming Magazine")
magazinePublisher.sink { completion in
    switch completion {
    case .finished:
        print("구독이 완료되었습니다")
    case .failure(let error):
        print("에러로 인해 구독이 실패했습니다: \(error)")
    }
} receiveValue: { value in
    print("받은 이슈: \(value)")
}
```

이 예제에서 Just는 하나의 값만을 보내고 완료합니다.

# Subscriber Protocol

Subscriber는 publisher가 방출하는 값을 청취하고 반응하는 프로토콜입니다. Subscriber는 publisher에 구독하고 받은 값, 완료, 실패를 처리합니다.


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

실제 예시:

구독자로서, 잡지(값)를 받아서 읽고, 그 다음 호를 기다리게 됩니다. 구독이 종료되면(완료), 새로운 호를 기대하지 않게 됩니다. 잡지가 사업을 종료하면(실패), 알려드립니다.

코드 예시:

```js
import Combine

class MagazineSubscriber: Subscriber {
    typealias Input = String
    typealias Failure = Never

    func receive(subscription: Subscription) {
        subscription.request(.unlimited) // 모든 호 요청
    }

    func receive(_ input: String) -> Subscribers.Demand {
        print("호 수신 중: \(input)")
        return .none // 추가적인 요구사항 없음
    }

    func receive(completion: Subscribers.Completion<Never>) {
        print("구독 상태: \(completion)")
    }
}

let subscriber = MagazineSubscriber()
magazinePublisher.subscribe(subscriber)
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

# 발행자 및 구독자 통합

발행자를 결합하면 보다 복잡한 데이터 스트림을 처리할 수 있습니다. 예를 들어 여러 잡지에서 알림을 받아 하나의 스트림으로 병합하는 경우가 있습니다.

실제 예시:

당신은 “Tech Monthly”와 “Science Weekly” 두 잡지를 구독했습니다. 어느 한 쪽에서 새 호가 나올 때마다 알림을 받고자 합니다.

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

아래는 Markdown 형식을 이용한 코드 예시입니다.

```js
import Combine

let techPublisher = Just("Tech Monthly - Issue 1")
let sciencePublisher = Just("Science Weekly - Issue 1")

let combinedPublisher = Publishers.Merge(techPublisher, sciencePublisher)
combinedPublisher.sink { value in
    print("Received combined issue: \(value)")
}
```

여기서 Merge는 두 개의 publisher를 결합하여 하나의 publisher로 만들어주어 두 잡지로부터 통지를 받을 수 있습니다.

# 현대 애플리케이션에서의 실시간 활용

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

## 네트워크 요청 및 UI 업데이트

Combine은 특히 네트워크 요청에서 비동기 데이터를 처리하고 UI 요소를 반응적으로 업데이트해야 하는 시나리오에서 유용합니다. 예를 들어, 서버에서 사용자 데이터를 가져와 데이터가 도착할 때 UI를 업데이트해야 하는 앱을 상상해보세요:

```swift
import Combine

// 시뮬레이션된 네트워크 요청 퍼블리셔
let userDataPublisher = URLSession.shared.dataTaskPublisher(for: URL(string: "https://api.example.com/user")!)
    .map { $0.data }
    .decode(type: User.self, decoder: JSONDecoder())

// 사용자 데이터로 UI를 업데이트하는 서브스크라이버
class UserProfileViewController: UIViewController {
    private var cancellable: AnyCancellable?

    override func viewDidLoad() {
        super.viewDidLoad()

        cancellable = userDataPublisher
            .receive(on: DispatchQueue.main)
            .sink(receiveCompletion: { completion in
                // 완료 처리 (성공 또는 실패)
                print("사용자 데이터 요청 완료: \(completion)")
            }, receiveValue: { user in
                // 받은 사용자 데이터로 UI 업데이트
                nameLabel.text = user.name
                emailLabel.text = user.email
            })
    }
}
```

이 예시에서:

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

- URLSession.shared.dataTaskPublisher는 사용자 데이터를 비동기적으로 가져옵니다.
- sink은 userDataPublisher를 구독하여 사용자 데이터를 받아 UI를 업데이트합니다.

# Combine을 사용하는 장점

Combine을 사용하면 여러 가지 이점이 있습니다:

- 비동기 코드 간소화: 비동기 이벤트 처리를 더 간편하게 만듭니다.
- 코드 가독성 향상: 선언적 구문이 코드의 가독성과 유지 관리를 향상시킵니다.
- SwiftUI와의 완벽한 통합: SwiftUI와 원활하게 통합되어 반응형 UI 업데이트를 가능하게 합니다.

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

# 결론

콤바인의 Publisher 및 Subscriber 프로토콜을 이해하는 것은 비동기 이벤트를 효과적으로 관리하는 데 중요합니다. 이 개념을 숙지하고 연산자를 사용해 Publisher를 결합하면 견고하고 효율적인 Swift 애플리케이션을 만들 수 있습니다.

# 추가 자료

- Apple의 콤바인 문서
- 콤바인 튜토리얼: 시작하기
- 콤바인 Swift 힌디어 자습서: Publisher 및 Subscriber 프로토콜