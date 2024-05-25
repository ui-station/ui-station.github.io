---
title: "Swift에서의 관점 지향 프로그래밍"
description: ""
coverImage: "/assets/img/2024-05-20-Aspect-OrientedProgramminginSwift_0.png"
date: 2024-05-20 17:49
ogImage: 
  url: /assets/img/2024-05-20-Aspect-OrientedProgramminginSwift_0.png
tag: Tech
originalTitle: "Aspect-Oriented Programming in Swift"
link: "https://medium.com/the-swift-cooperative/aspect-oriented-programming-in-swift-f2366350c527"
---


<img src="/assets/img/2024-05-20-Aspect-OrientedProgramminginSwift_0.png" />

# 소개

Aspect-oriented programming (AOP로 앞으로 표기함)은 코드 베이스에 교차하는 관심사를 확장 가능한 방식으로 추가하는 것에 관한 것입니다.

"Aspect Oriented Programming"이라는 용어를 처음 만났던 그 때를 아직 기억합니다. 많은 시간이 흘렀고, Swift가 아직 존재하지 않았으며, 우리는 기쁘게 Objective-C 코드를 작성하고 있었습니다. ARAnalytics는 AOP 패러다임을 채택하여 코드 베이스 전반에 걸쳐 분석을 추가하는 것을 간단하게 한 최초의 라이브러리 중 하나였습니다.

<div class="content-ad"></div>

코드를 더 자세히 살펴보면 Objective-C에서 AOP를 구현하는 것이 Peter Steinberger가 그의 Aspects 라이브러리에서 언급한 것처럼 메소드 스위즐링을 구현하는 것이라는 것을 알 수 있어요.

Objective-C와는 달리 Swift는 메시지를 가로채고 실행 중에 동작을 변경할 기능이 많지 않은 엄격하고 정적으로 타입이 지정된 언어에요. 실제로는, 이것은 우리의 코드에 교차 관심을 적용하는 영향을 최소화하기 위해 좋은 디자인의 기초를 설정해야 한다는 의미에요. 우리는 두 가지 주요 구성요소가 필요할 거에요:

- 지연 바인딩을 적용하고 필요에 따라 기반이 되는 구현을 변경할 수 있도록 하는 종속성 주입 기능이 필요해요.
- 런타임에서 우리가 원하는 동작을 변경할 수 있도록 해주는 가로채기 디자인 패턴인 데코레이터 패턴이 필요해요.

시작해봐요!

<div class="content-ad"></div>

# Swift에서의 AOP: 좋은 디자인을 향해서

우리 애플리케이션을 모델링하는 매우 일반적인 접근 방식은 특정 도메인 모델 주변의 모든 기능을 번들로 묶는 단일 “store/repository/service/aggregate”를 가지는 것입니다. 만약 애플리케이션이 할 일을 관리하는 것이라면, 어떤 형식의 “TodoService”가 있을 것입니다. 만약 TV 프로그램을 관리하는 앱이라면, “ShowService”가 있을 것입니다. 아이디어를 얻으셨죠. 이러한 접근 방식은 많은 중간 규모의 앱에 대해 완벽하게 유효한 것이지만, 다른, 더 복잡한 애플리케이션에는 문제가 될 수 있습니다. 소프트웨어의 대부분은 마찬가지로 모든 것이 의존합니다.

다음과 같이 보이는 ShowService를 상상해보십시오:

```js
protocol ShowService {
    func allShows() async -> [Show]
    func allEpisodes(for show: Show) async -> [Episode]
    func markEpisodeAsWatched(episode: Episode) async
    func markShowAsWatched(_ show: Show, until episode: Episode?) async
}
```

<div class="content-ad"></div>

우리가 말했던 대로 소프트웨어의 대부분은 주관적이며 특정 맥락에 따라 토론의 여지가 있음에도 불구하고, 우리를 안내하는 "원칙"들이 있으면 좋습니다. 그 원칙들은 보통 SOLID 원칙들입니다.

## ISP 위반

대부분의 대규모 인터페이스와 마찬가지로, ShowService가 인터페이스 분리 원칙을 위반할 가능성이 매우 높습니다. 이는 클라이언트가 필요하지 않은 메서드를 구현하도록 강제함으로써 나타납니다. 특히 목업을 구현할 때 XCTFail과 같은 메서드들을 사용하여 이를 확인하는 것이 매우 흔합니다.

```js
struct ShowServiceMock: ShowService {
    var shows: [Show]

    func allShows() -> [Show] {
        shows
    }
    
    func allEpisodes(for show: Show) -> [Episode] {
        XCTFail("호출되지 않아야 함")
        return []
    }

    func markEpisodeAsWatched(episode: Episode) {
        XCTFail("호출되지 않아야 함")
    }

    func markShowAsWatched(_ show: Show, until episode: Episode?) {
        XCTFail("호출되지 않아야 함")
    }
}
```

<div class="content-ad"></div>

## SRP 위반

ISP를 위반할 때, SRP를 위반하는 일도 상당히 흔합니다. SRP는 응집성과 한 가지 이유로만 변경해야 한다는 것과 관련이 있습니다. ShowService 인터페이스에 있는 메서드 수가 늘어날수록, 그 응집성과 SRP를 유지하기가 더욱 어려워질 것입니다.

## OCP 위반

TV 프로그램과 관련된 새로운 기능을 추가하려면 ShowService에 새로운 메서드를 추가해야 하며, 서비스의 모든 구현이 그 변경 사항을 수용하고 컴파일 오류를 수정해야 합니다.

<div class="content-ad"></div>

## 큰 인터페이스는 병목 현상을 유발하기 쉽습니다

하루의 끝에 이렇게 큰 인터페이스를 갖고 있다면, 해당 도메인 모델과 관련된 새로운 동작을 추가할 때 병목 현상이 발생할 것입니다. 어떠한 새로운 변경이라도 앱 전체에서 컴파일 오류 및 다시 컴파일을 유발합니다. 좋은 소프트웨어 디자인의 한 가지 주의사항은 자주 변경되지 않는 모듈에 의존하는 것입니다. 우리의 의존성 그래프에서 잎 모듈은 가장 안정된 것이어야 합니다. 왜냐하면 그들이 변경될 때마다 전체 그래프가 다시 컴파일되도록 강제할 것입니다. ShowService를 더 자세히 살펴보면, 매우 빈번하게 변경되는 매우 불안정한 모듈에 전체 앱이 의존한다는 점을 알 수 있습니다. 그런 해롭은 일종의 재앙이네요 😅. 참고로, 이러한 병목 현상은 백엔드 및 마이크로서비스 아키텍처에서도 매우 흔히 발생하며, "엔티티 서비스 안티패턴(The Entity Service Antipattern)"이라고 불립니다.

## 첫 번째 접근 방식: CQRS

첫 번째 접근 방식은 큰 인터페이스를 읽기와 쓰기를 위한 두 가지 구분된 인터페이스로 분리하는 것입니다. 이를 Command Query Responsibility Segregation(CQRS)이라고 합니다.

<div class="content-ad"></div>

```bash
// 읽기
protocol ShowQueryService {
    func allShows() async -> [Show]
    func allEpisodes(for show: Show) async -> [Episode]
}

// 쓰기
protocol ShowCommandService {
    func markEpisodeAsWatched(episode: Episode) async
    func markShowAsWatched(_ show: Show, until episode: Episode?) async
}
```

보통 이렇게 분리하는 것이 읽기와 쓰기에 필요한 기능이 매우 다르기 때문에 합리적입니다. 예를 들어 상태를 변경할 때(쓰기)만 적용되는 보안 정책이 있을 수 있습니다.

하지만 상상할 수 있듯이, 이것은 큰 개선이 아닙니다. 변경의 영향을 더 잘 제한했지만 여전히 자주 변경될 두 가지 "큰 인터페이스"가 존재합니다. 더 좋은 방법이 있을 수 있습니다.

## 두 번째 접근 방법: 작은 서비스

<div class="content-ad"></div>

당신이 깨끗한 아키텍처와 유즈 케이스로 작업하는 데 익숙하다면, 이것들이 정확히 그것입니다.

일부 장단점이 있습니다. 각 메서드 당 프로토콜을 갖는 것은 인터페이스의 폭발을 일으킬 수 있습니다. 그러나 이것은 이전에 이야기한 일부 SOLID 원칙을 준수하는 주요 이점도 가지고 있습니다. 이제 ISP(인터페이스 분리 원칙)를 준수하고 있고 아마도 SRP(단일 책임 원칙)을 준수하고 있을 것입니다. 또한 새로운 기능을 추가할 때는 새로운 튜플(인터페이스, 구현)을 만들기만 하면 되므로 코드를 많이 수정하거나 재컴파일하는 영향을 최소화할 수 있습니다. 그러니 전체가 나쁜 것만은 아닙니다. 기억하세요, 모든 것은 의존합니다. 소프트웨어는 모든 것이 트레이드오프에 관한 것입니다.

<div class="content-ad"></div>

하지만 "Aspects"에 대해 생각해보면, 필요한 각 측면마다 프로토콜 당 하나의 구현을 해야 합니다. 예를 들어 "로그 기록 Aspect"를 구현하려면 AllShowsLoggingService와 MarkShowAsWatchedLoggingService 등이 필요합니다. 그래서 우리 앱에서 관심사를 구현하는 데 적합한 방법이 아닌 것 같습니다.

세 번째, 마지막 방법으로 가봅시다.

## 세 번째 방법: 통합된 서비스 인터페이스

AOP를 확장 가능한 방식으로 구현하려면 "aspects"를 적용할 모든 서비스에 대해 단일 인터페이스/시마가 필요합니다.

<div class="content-ad"></div>

```js
프로토콜 Service<Input, Output> {
    associatedtype Input
    associatedtype Output

    func callAsFunction(input: Input) async throws -> Output
}
```

편의성을 위해 다음도 추가해 봅시다.

```js
extension Service where Input == Void{
    func callAsFunction() async throws -> Output {
        try await callAsFunction(input: ())
    }
}
```

단일 인터페이스를 가지고 있어, 다음과 같은 것을 할 수 있습니다.```

<div class="content-ad"></div>

```kotlin
class AllShowsService: Service {
    suspend operator fun invoke(input: Void): List<Show> {
        // Implementation...
    }
}

class MarkShowAsWatchedService: Service {
    suspend operator fun invoke(input: Pair<Show, Episode?>) {
        // Implementation...
    }
}

typealias AllShowsServiceType = Service<Void, List<Show>>
typealias MarkShowAsWatchedServiceType = Service<Pair<Show, Episode?>, Void>

class ViewModel<AllShowsService: AllShowsServiceType, MarkShowAsWatchedService: MarkShowAsWatchedServiceType> {
    private val allShowsService: AllShowsService
    private val markShowAsWatchedService: MarkShowAsWatchedService

    init(allShowsService: AllShowsService, markShowAsWatchedService: MarkShowAsWatchedService) {
        this.allShowsService = allShowsService
        this.markShowAsWatchedService = markShowAsWatchedService
    }

    suspend fun markAllShowsAsWatchedButtonTapped() {
        try {
            val allShows = allShowsService(Void)
            coroutineScope {
                allShows.map { show ->
                    launch {
                        markShowAsWatchedService(Pair(show, null))
                    }
                }
            }
        } catch (e: Exception) {
            println("Some error happened $error")
        }
    }
}
```

지금은 ViewModel을 생성할 때 두 개의 입력값을 주입하면 됩니다:

- Service`Void, [Show]`
- Service`(show: Show, episode: Episode?), Void`

Service가 단일 인터페이스이기 때문에 앱 전체의 모든 서비스에 적용할 수 있는 각 측면마다 인터페이스의 구현만 있으면 됩니다.```

<div class="content-ad"></div>

그러면 몇 가지를 구현해 보겠습니다! 확인하시겠지만, 모든 측면은 데코레이터 패턴을 따라 매우 유사합니다.

## 로깅 측면

서비스에 몇 가지 로깅 기능을 추가하는 것은 매우 일반적이고 유용한 측면입니다. 다음과 같이 간단히 구현할 수 있습니다:

```js
class LoggingService<Decoratee: Service>: Service {
    typealias Input = Decoratee.Input
    typealias Output = Decoratee.Output

    let decoratee: Decoratee
    
    init(decoratee: Decoratee) {
        self.decoratee = decoratee
    }

    func callAsFunction(input: Input) async throws -> Output {
        let output = try await decoratee(input: input)
        dump(output)
        return output
    }
}
```

<div class="content-ad"></div>

## 프리미엄 사용자 측면

애플리케이션에서 매우 유용한 교차 기능 중 하나는 사용자가 프리미엄 사용자인 경우에만 일부 작업을 수행할 수 있도록 허용하는 것일 수 있습니다. 사용자가 프리미엄 사용자인지 여부를 확인하는 방법을 추상화하기 위해 간단한 `async` 함수를 주입하여 PremiumService 데코레이터를 다음과 같이 만들 수 있습니다:

```js
class PremiumService<Decoratee: Service>: Service {
    struct Error: Swift.Error {}

    typealias Input = Decoratee.Input
    typealias Output = Decoratee.Output

    let decoratee: Decoratee
    let isPremiumUser: () async -> Bool

    init(decoratee: Decoratee, isPremiumUser: @escaping () async -> Bool) {
        self.decoratee = decoratee
        self.isPremiumUser = isPremiumUser
    }

    func callAsFunction(input: Input) async throws -> Output {
        guard await isPremiumUser() else {
            throw Error()
        }
        return try await decoratee(input: input)
    }
}
```

## 캐싱 측면

<div class="content-ad"></div>

또 다른 일반적인 측면은 캐싱 또는 메모이제이션입니다. 단순히 서비스 입력을 해시 가능한 것으로 제한하고 출력을 Codable로 정의함으로써 모든 서비스에 대해 구현할 수 있습니다.

```js
private(set) var cache: [AnyHashable: Data] = [:]

class CachingService<Decoratee: Service>: Service where Decoratee.Output: Codable, Decoratee.Input: Hashable {
    typealias Input = Decoratee.Input
    typealias Output = Decoratee.Output

    let decoratee: Decoratee

    init(decoratee: Decoratee) {
        self.decoratee = decoratee
    }

    func callAsFunction(input: Input) async throws -> Output {
        if let cachedData = cache[input] {
            return try JSONDecoder().decode(Output.self, from: cachedData)
        }

        let output = try await decoratee(input: input)
        cache[input] = try JSONEncoder().encode(output)
        return output
    }
}
```

## 지연 측면

네트워크 링크 조절기(Network Link Conditioner)는 다양한 유형의 네트워크 상황을 시뮬레이션하고 인터넷 연결을 매우 나쁘게 만드는 매우 유용한 도구입니다. 우리는 여기에 측면(Aspect)을 활용하여 정확히 그것을 할 수 있습니다.

<div class="content-ad"></div>

```js
class DelayService<Decoratee: Service>: Service {
    typealias Input = Decoratee.Input
    typealias Output = Decoratee.Output

    let decoratee: Decoratee
    let duration: UInt64

    init(decoratee: Decoratee, nanoseconds duration: UInt64) {
        self.decoratee = decoratee
        self.duration = duration
    }

    func callAsFunction(input: Input) async throws -> Output {
        try await Task.sleep(nanoseconds: duration)
        return try await decoratee(input: input)
    }
}
```

또는 예를 들어 항상 오류를 강제할 수 있는 다른 측면을 추가할 수도 있습니다.

## 측면은 조립 가능합니다.

모든 측면을 준비한 후에는 간단히 뷰 모델을 수정하여 필요한 서비스 유형을 주입하고 여러 측면으로 꾸밀 수 있습니다.


<div class="content-ad"></div>

```js
let viewModel = ViewModel( allShowsService: DelayService(decoratee: LoggingService(decoratee: AllShowsService()), nanoseconds: 5 * NSEC_PER_SEC), markShowAsWatchedService: LoggingService(decoratee: MarkShowAsWatchedService()) )
```

이렇게 보시다시피 서로 다른 측면을 연쇄시켰습니다. 조립 가능한 요소들이죠.

하지만 중요한 점은 데코레이터 추가는 어플리케이션의 메인 모듈에서 할 수 있다는 것입니다. 여기서 전체 오브젝트 그래프를 구성할 것으로 가정하고, 일종의 구성 루트 패턴을 통해 서비스의 동작을 변경할 수 있습니다. 이렇게 함으로써, 모듈 중 어느 것도 건드리거나 다시 컴파일할 필요 없이 서비스의 행동을 변경할 수 있게 됩니다.

## 편의성```

<div class="content-ad"></div>

데코레이터를 연결하는 것은 조금 귀찮을 수 있지만, 확장 기능을 활용하여 작업성을 개선하고 더 관용적으로 조합할 수 있습니다.

```js
extension Service {
    var withLogging: LoggingService<Self> {
        LoggingService(decoratee: self)
    }

    func withDelay(nanoseconds duration: UInt64) -> DelayService<Self> {
        DelayService(decoratee: self, nanoseconds: duration)
    }
}

// 이렇게 하는 대신
DelayService(decoratee: LoggingService(decoratee: AllShowsService()), nanoseconds: 5 * NSEC_PER_SEC),

// 이제 이렇게 할 수 있습니다
AllShowsService()
    .withLogging
    .withDelay(nanoseconds: 5 * NSEC_PER_SEC)
```

## 데코레이터 자동화

Sourcery는 많은 보일러플레이트 코드를 자동화하는 훌륭한 메타프로그래밍 도구입니다. 데코레이터는 이 도구를 적용하기에 좋은 후보입니다. 특히 동일한 측면을 많은 다른 인터페이스에 적용하려는 경우에 유용합니다. 여기를 확인해보세요.```

<div class="content-ad"></div>

# 결론

이러한 작업에 Objective-C의 동적 특성이 그립다는 것은 사실이지만, Swift에서 쉽게 AOP를 적용할 수 있는 런타임 지원의 부족은 좋은 설계 원칙을 적용하여 해결할 기회로 보고 있습니다.

AOP를 활성화하는 것이 코드를 더 좋게 만든다는 보장은 없습니다. 마찬가지로 테스트 가능한 코드를 가지고 있다고 해서 좋은 코드라는 보장도 없습니다. 오히려 반대의 경우가 더 맞습니다. 코드를 테스트할 수 없는 것은 문제입니다. 테스트할 수 있는 것이라고 해서 그게 무조건 좋은 것은 아닙니다. AOP도 비슷한 맥락에서 볼 수 있습니다.

우리는 코드에서 AOP 기능을 활성화하기 위한 흥미로운 연습을 했습니다. 하지만 우리가 추가한 복잡성을 잊어서는 안 됩니다. 미래에 더 잘 적응하기 위해 추가된 복잡성과 느슨한 결합 사이의 균형을 찾는 것은 개발자로서 가장 어려운 일 중 하나입니다. 이 글은 그저 도구에 불과합니다. 항상 올바른 도구를 올바르게 사용하세요.