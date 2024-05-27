---
title: "Swift 동시성 뒤엉킨 것들 풀어내기"
description: ""
coverImage: "/assets/img/2024-05-27-SwiftConcurrencyUn-tangledup_0.png"
date: 2024-05-27 16:24
ogImage: 
  url: /assets/img/2024-05-27-SwiftConcurrencyUn-tangledup_0.png
tag: Tech
originalTitle: "Swift Concurrency: Un-tangled up!"
link: "https://medium.com/@thekrazyjames/swift-concurrency-un-tangled-up-0d27ae54cc7c"
---


```markdown
<img src="/assets/img/2024-05-27-SwiftConcurrencyUn-tangledup_0.png" />

Swift 5.5부터는 concurrent programming을 위해 async/await 접근 방식이 도입되어 비동기 작업 관리가 변경되었습니다. 이 방식은 올바르게 구현할 때 많은 이점을 제공하며 고려해야 할 사항도 몇 가지 있습니다.

# 이것은 무엇인가요?

Concurrency는 concurrent code를 처리하는 도구를 제공하는 라이브러리로, 이러한 도구는 쓰레드를 다룰 때 안전성을 제공하기 위한 것입니다. 이는 쓰레드를 관리할 때 발생하는 모든 종류의 문제를 포함하며, 흔한 문제 몇 가지를 들자면 다음과 같습니다:
```

<div class="content-ad"></div>

- 데이터 레이스(Data races). 여러 스레드가 동시에 동일한 데이터 조각에 액세스하여 조작하려고 할 때 발생합니다.
- 데드락(Deadlocks). 여러 스레드가 서로가 완료되기를 기다리고 계속 지장을 일으킬 때 발생합니다.
- 우선순위 역전(Priority inversion). 낮은 우선순위 작업이 높은 우선순위 작업의 실행을 차단할 때 발생합니다.

## 병렬성 vs 순차성

예제를 시작하기 전에, 이 주제 아래에서 두 가지 개념, 병렬성과 순차성을 이해해야 합니다.

- 병렬 코드는 다른 작업이 끝나기를 기다릴 필요 없이 동시에 실행될 수 있는 코드입니다.
- 순차 코드는 이전 결과가 완료되어야 다음 작업을 계속할 수 있는 코드입니다.

<div class="content-ad"></div>

# 구현 방법

## 비동기 및 대기

이 프레임워크는 두 가지 주요 키워드 async와 await를 사용합니다. 각각은 다른 목적으로 사용됩니다.

- Async는 비동기 작업을 표시하는 데 주로 사용되며, 작업 내에서 가능한 비동기 기능을 노출하는 데도 사용됩니다. 다시 말해, async 메서드 안에는 비동기 작업이 포함될 수 있습니다.
- Await은 현재 컨텍스트의 실행이 중지될 수 있는 가능성이 있는 중단 지점을 표시하는 데 사용되며, 그 시간 동안 리소스를 사용하여 다른 작업을 수행할 수 있습니다.

<div class="content-ad"></div>

예를 들어, 일반적인 데이터 가져오기 방법은 완료될 때까지 시간이 걸릴 수 있습니다. 왜냐하면 로컬이거나 클라우드인 데이터베이스에 도달해야 하기 때문이죠.

```js
func fetchData(completion: @escaping (Result<Data, Error>) -> Void) {
    let url = URL(string: "https://itunes.apple.com/search/media=music&entity=song&term=avicii")!

    URLSession.shared.dataTask(with: url) { data, response, error in
        if let error {
            return completion(.failure(error))
        }
        guard let data else {
            return completion(.failure(URLError(.unknown)))
        }
        return completion(.success(data))
    }.resume() // 항상 세션 작업을 재개하는 것을 기억하세요 :)
}

fetchData { result in
    switch result {
    case let .success(data):
        print("Itunes 정보를 가져왔습니다: \(data.description)")
    case let .failure(error):
        print("문제가 발생했습니다: \(error.localizedDescription)")
    }
}

// 출력: "Itunes 정보를 가져왔습니다: 80075 bytes"
```

이는 원격 서버에서 정보를 가져오는 비동기 작업을 완료하기 위해 기다리는 일반적인 패턴입니다. 완료 핸들러는 한 번 작업이 완료되면 호출되는 익명 함수(클로저)입니다. 그러나 이러한 패턴은 중첩된 클로저로 코드베이스를 쉽게 더럽힐 수 있습니다(콜백 지옥).

비동기/대기 접근 방식을 사용하면 다음과 같이 개선될 수 있습니다:

<div class="content-ad"></div>

- 모든 시간이 걸릴 수 있는 작업 또는 다른 시간이 많이 소요되는 작업을 async로 표시하여 컴파일러가 가능한 비동기 작업을 알 수 있게 합니다.
- await로 가능한 중단 지점을 찾아보세요. 이 경우 URLSession은 이미 async/await 버전의 dataTask(with:)를 제공합니다. async context 내에서 사용할 수 있으며 완료까지 시간이 소요됩니다.
- 처리될 함수를 async context 내에서 호출하세요.

```js
func fetchData() async throws -> Data { // 1
    let url = URL(string: "https://itunes.apple.com/search/media=music&entity=song&term=avicii")!
    let (data, _) = try await URLSession.shared.data(from: url) // 2
    return data
}

Task { // 3
    do {
        let data = try await fetchData()
        print("Itunes info has been retreived: \(data.description)")
    } catch {
        print("Something went wrong: \(error.localizedDescription)")
    }
}
```

여기 몇 가지 향상된 점이 있습니다:

- 오류가 발생할 수 있는 가능성을 나타내기 위해 throws를 사용하세요.
- 이러한 오류를 처리하세요(예: do-catch 블록 사용)

<div class="content-ad"></div>

# 혜택 및 고려 사항은 무엇인가요?

이 기능은 많은 혜택을 제공하며, 구현할 때 고려해야 할 몇 가지 사항이 있습니다. async/await 방식을 사용하면

- 가독성 개선. 주요 키워드를 쉽게 식별하고 특정 작업에서 무슨 일이 일어나고 있는지 이해할 수 있어 중첩 코드를 줄이고 유지보수성을 향상시킵니다.
- 스레드 관리 오류 감소. 단순화된 접근 방식은 동기화 작업을 조작할 때 로직 오류를 줄이는 데 도움이 되어 신뢰성을 향상시킵니다.
- 성능 향상. 동시성 코드 작성시의 정확성이 향상되어 컴파일러가 실행 시간이 아닌 컴파일 시간에 문제를 감지할 수 있습니다.

## 고려 사항

<div class="content-ad"></div>

- 호환성. Async/Await을 사용하는 데 일부 우려 사항이 있는데, 이는 Swift 5.5부터 지원되며, 더 최신 버전은 사용법과 기능을 개선합니다.
- Actors. 여러 동시 작업에서 액세스해서는 안 되는 기능을 격리하기 위해 액터의 사용을 고려해보세요.
- 구조화된 및 구조화되지 않은 동시성. 그룹 및 오류 처리는 동시 작업의 사용 및 상호 작용을 조직화하는 데 도움이 되며, 반면 구조화되지 않은 작업은 자유롭지만 가능한 문제를 처리하기 위해 수동 지원이 필요합니다.

# 결론

Swift는 매년 업데이트로 발전하는 쓰레드 안전한 언어가 되고 있습니다. 더욱 명확한 문법을 제공하여 이해하고 유지하기 쉽게되었습니다. 이러한 라이브러리와 같은 특정 도구를 통해 개발을 개선할 수 있습니다. 이 모든 것은 모든 지원 플랫폼에서 사용할 수 있으며 특히 iOS, macOS 및 watchOS와 같은 플랫폼에서 운영 가능합니다.