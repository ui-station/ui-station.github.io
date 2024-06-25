---
title: "iOS에서 Chain of Responsibility 디자인 패턴"
description: ""
coverImage: "/assets/img/2024-06-19-ChainofResponsibilityDesignPatterniniOS_0.png"
date: 2024-06-19 11:07
ogImage:
  url: /assets/img/2024-06-19-ChainofResponsibilityDesignPatterniniOS_0.png
tag: Tech
originalTitle: "Chain of Responsibility Design Pattern in iOS"
link: "https://medium.com/@thekrazyjames/chain-of-responsibility-design-pattern-in-ios-2a2d5ae72ccb"
---

![Chain of Responsibility](/assets/img/2024-06-19-ChainofResponsibilityDesignPatterniniOS_0.png)

Chain of Responsibility(줄여서 CoR)는 행위 디자인 패턴으로, 클래스의 책임을 다른 시스템 부분에 위임하여 주어진 입력에 대해 더 나은 대응이 될 수 있는 곳으로 작업을 위임하는 디자인 패턴입니다.

예를 들어, 고객 서비스에 전화를 걸 때, 그들은 결국 고객이 제시한 상황을 더 잘 도와줄 수 있는 구체적인 영역으로 통화를 이동할 것입니다.

# 구현

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

이 패턴을 구현하려면 체인 링크 사이에서 공유될 인터페이스를 만들어야 합니다.

```js
protocol RequestHandler {
    var nextHandler: RequestHandler? { get set }
    func handleRequest(request: HTTPRequest)
}
```

이 프로토콜은 모든 가능한 핸들러에서 구현될 것이며, 필요한 경우 미래에 추가할 수 있도록 열려 있습니다. 이를 준수하는 객체는 체인 내의 다음 핸들러를 노출하고 요청을 관리하는 데 호출될 함수를 노출할 것입니다.

```js
class Authenticator: RequestHandler {
    var nextHandler: RequestHandler?

    func handleRequest(request: HTTPRequest) {
        print("요청 인증 중...")
        // 인증 로직 ...
        nextHandler?.handleRequest(request: request)
    }
}

class Logger: RequestHandler {
    var nextHandler: RequestHandler?

    func handleRequest(request: HTTPRequest) {
        print("요청 로깅 중...")
        // 로깅 로직 ...
        nextHandler?.handleRequest(request: request)
    }
}

let authenticator = Authenticator()
let logger = Logger()

authenticator.nextHandler = logger
let request = HTTPRequest(path: "/api/resource", method: .get)
authenticator.handleRequest(request: request)

// "요청 인증 중..."과 "요청 로깅 중..."을 출력합니다.
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

위 예시에서, 요청이 실패하면 해당 문제를 처리하여 처리할 수 있다면 다음 대리자에게 처리를 맡깁니다. 반대로, 요청이 완료되면 실행은 성공적으로 종료되고 반환됩니다.

다른 예시는 로컬로 캐시된 데이터를 관리하거나 원격으로 가져오는 경우입니다.

```js
protocol DataRequest { ... }

protocol DataProvider {
  var nextProvider: DataProvider? { get set }
  func fetch(request: DataRequest) async
}

class LocalProvider: DataProvider {
  var nextProvider: DataProvider? = nil

  init(nextProvider: DataProvider? = nil) {
    nextProvider = nextProvider
  }

  func fetch(request: DataRequest) async {
    print("로컬에서 가져오는 중")
    // 로컬로 가져오면 반환, 아니면...
    await nextProvider?.fetch(request: request)
  }
}

class RemoteProvider: DataProvider {
  var nextProvider: DataProvider? = nil

  init(nextProvider: DataProvider? = nil) {
    nextProvider = nextProvider
  }

  func fetch(request: DataRequest) async {
    print("원격에서 가져오는 중")
    // 원격에서 가져오면 반환, 아니면...
    await nextProvider?.fetch(request: request)
  }
}

let remoteProvider = RemoteProvider()
let databaseProvider = LocalProvider(nextProvider: remoteProvider)

Task {
  await databaseProvider.fetch(request: ...)
}
```

만약 로컬 저장소에서 데이터를 찾을 수 없는 경우, 클라우드에서 가져와야 합니다.

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

# 장점과 고려사항

이 패턴은 여러 가지 장점을 가지고 있습니다:

- 입력 및 수행할 고유한 작업에만 관심이 있기 때문에 단일 책임 원칙을 따릅니다. 다른 하위 시스템의 책임을 순서대로 위임합니다.
- 시스템의 일부를 쉽게 추가하거나 제거할 수 있으므로 개방/폐쇄 원칙을 준수합니다. 시스템의 다른 부분과 강하게 결합되지 않으며 동일한 순서에 새로운 가지를 쉽게 만들고 연결하여 무결성을 손상시키지 않고 클라이언트와의 상호 작용에 영향을 주지 않습니다.

고려해야 할 사항이 있습니다:

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

- "최적맞춤" 시스템이 입력을 사용하지 않는다면 일부 요청이 처리되지 않을 수 있습니다.

# 결론

위에서 언급한대로, 이 디자인 패턴을 사용하면 유연성과 확장성을 추가하기 위해 분리될 수 있는 고응집 모듈을 쉽게 분리할 수 있습니다. 이는 사용자 상호작용 이벤트 처리를 위한 GUI 프레임워크나 원격 또는 로컬 데이터 접근을 위한 리포지터리 레이어에서 볼 수 있습니다.
