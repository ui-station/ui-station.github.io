---
title: "WWDC 2024 Swift의 새로운 동시성 기능 탐구"
description: ""
coverImage: "/assets/img/2024-06-23-WhatsNewinWWDC2024ExploringSwiftConcurrency_0.png"
date: 2024-06-23 23:39
ogImage:
  url: /assets/img/2024-06-23-WhatsNewinWWDC2024ExploringSwiftConcurrency_0.png
tag: Tech
originalTitle: "What’s New in WWDC 2024: Exploring Swift Concurrency"
link: "https://medium.com/@Shubhransh-Gupta/whats-new-in-wwdc-2024-exploring-swift-concurrency-c39a25806e25"
---

Swift Concurrency는 처음 소개되었을 때부터 화제가 되었고, WWDC 2024에서 발표된 혁신적인 기술들로 더욱 발전하였습니다. Apple은 개발자들이 안전하고 효율적이며 고성능의 비동기 코드를 작성할 수 있도록 지원하고 있습니다. 이 블로그에서는 WWDC 2024에서 발표된 Swift Concurrency의 새로운 기능과 개선 사항에 대해 알아보겠습니다. 이를 통해 이러한 개념을 설명하는 코드 스니펫을 제시할 것입니다.

# Swift Concurrency 소개

프로그래밍에서 Concurrency는 여러 작업이 동시에 실행되는 것을 의미하며, 특히 계산이 많거나 외부 자원을 기다리는 작업을 하는 애플리케이션의 성능을 크게 향상시킬 수 있습니다. Swift Concurrency는 Swift 5.5에서 처음 소개되었으며, async/await, actors, 그리고 구조적 동시성을 도입하여 비동기 코드를 더욱 쉽고 안전하게 다룰 수 있도록 하였습니다.

# Swift Concurrency의 새로운 기능 (WWDC 2024)

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

# 1. 작업 그룹 업데이트

작업 그룹을 사용하면 동시에 실행되는 작업 그룹을 생성할 수 있습니다. WWDC 2024에서의 개선 사항은 더 나은 오류 처리와 더 유연한 작업 관리를 포함하고 있습니다.

## 코드 스니펫: 작업 그룹

```js
import Foundation
func fetchAllData() async throws -> [Data] {
    var results: [Data] = []
    try await withThrowingTaskGroup(of: Data?.self) { group in
        for url in ["https://api.example.com/data1", "https://api.example.com/data2"] {
            group.addTask {
                guard let url = URL(string: url) else { return nil }
                return try? Data(contentsOf: url)
            }
        }
        for try await result in group {
            if let data = result {
                results.append(data)
            }
        }
    }
    return results
}
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

이 예시에서는 여러 URL에서 동시에 데이터를 가져오기 위한 작업 그룹을 만듭니다. withThrowingTaskGroup은 이제 오류를 더 잘 처리하여 더 견고하고 오류에 강한 코드를 작성할 수 있습니다.

## 2. Async Streams

Async Streams는 라이브 데이터 피드와 같은 비동기 값 시퀀스를 처리하는 과정을 간소화합니다.

### 코드 조각: Async Streams

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

```swift
import Foundation
func fetchLiveUpdates() -> AsyncStream<String> {
    AsyncStream { continuation in
        let timer = Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { _ in
            continuation.yield("New update at \(Date())")
        }

        continuation.onTermination = { _ in
            timer.invalidate()
        }
    }
}

Task {
    for await update in fetchLiveUpdates() {
        print(update)
    }
}
```

여기서 AsyncStream은 1초마다 새 값을 발행하는 라이브 업데이트 스트림을 생성하는 데 사용됩니다. continuation.onTermination 블록은 스트림이 종료될 때 타이머가 무효화되도록 합니다.

# 3. 향상된 액터 모델

Swift의 Actor는 동시성 환경에서 공유 가능한 가변 상태에 안전한 액세스를 보장합니다. 2024 업데이트에는 더 효율적인 데이터 액세스 패턴과 기존 Swift 코드와의 상호 운용성이 포함되어 있습니다.

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

## 코드 조각: 향상된 Actor

```js
import Foundation

actor BankAccount {

   private(set) var balance: Double = 0.0

   func deposit(amount: Double) {
        balance += amount
   }

   func withdraw(amount: Double) -> Bool {
        if balance >= amount {
            balance -= amount
            return true
        } else {
            return false
        }
   }
}

let account = BankAccount()
Task {
    await account.deposit(amount: 100.0)
    let success = await account.withdraw(amount: 50.0)
    print("출금 성공: \(success), 잔액: \(await account.balance)")
}
```

이 예제에서는 입금과 출금을 안전하게 처리하기 위해 BankAccount actor를 정의했습니다. await 키워드는 각 작업이 완료될 때까지 다음 작업이 시작되지 않도록 보장하여 데이터 무결성을 유지합니다.

# 4. 작업 취소 개선 결과

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

작업 취소는 리소스를 관리하고 앱 반응성을 향상시키는 데 중요합니다. 최신 업데이트로 작업을 취소할 때 더 나은 제어와 예측 가능한 동작을 제공합니다.

## 코드 스니펫: 작업 취소

```js
import Foundation

func performTask() async {
    let task = Task {
        for i in 1...10 {
            if Task.isCancelled {
                print("작업이 취소되었습니다")
                return
            }
            print("작업 실행 중: \(i)")
            try await Task.sleep(nanoseconds: 1_000_000_000)
        }
    }

    // 취소 시뮬레이션
    await Task.sleep(nanoseconds: 3_000_000_000)
    task.cancel()
}

Task {
    await performTask()
}
```

이 코드에서는 작업이 1초마다 메시지를 출력하는 루프를 실행합니다. 작업이 취소되면 (여기서 3초 뒤에 시뮬레이션됨), 취소 여부를 확인하고 정상적으로 종료됩니다.

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

# 5. 향상된 오류 처리

Swift Concurrency의 개선된 오류 처리 기능은 비동기 코드에서 오류를 처리하고 전파하는 더 나은 메커니즘을 제공하여 더 견고한 애플리케이션을 만들 수 있게 돕습니다.

## 코드 스니펫: 향상된 오류 처리

```swift
import Foundation
enum DataError: Error {
    case invalidURL
    case requestFailed
}
func fetchData(from urlString: String) async throws -> Data {
    guard let url = URL(string: urlString) else {
        throw DataError.invalidURL
    }
    do {
        let (data, _) = try await URLSession.shared.data(from: url)
        return data
    } catch {
        throw DataError.requestFailed
    }
}

Task {
    do {
        let data = try await fetchData(from: "https://api.example.com/data")
        print("Data received: \(data)")
    } catch {
        print("Failed to fetch data: \(error)")
    }
}
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

이 예제는 Swift의 async/await와 함께 에러 처리 메커니즘을 사용하여 비동기 함수에서 에러를 효과적으로 처리하는 방법을 보여줍니다.

# 결론

WWDC 2024에서 소개된 Swift Concurrency의 개선 사항은 개발자가 효율적이고 안전한 비동기 코드를 작성하는 데 더욱 쉽게 만들어 줍니다. 작업 그룹, async 스트림, actor 모델, 작업 취소 및 에러 처리에 대한 개선 사항을 통해 Swift는 현대 애플리케이션 개발을 위한 강력한 언어로 발전하고 있습니다.

이러한 새로운 기능을 프로젝트에 통합하여 더 빠르고 견고한 응용 프로그램을 만들 수 있습니다. 항상 실험을 진행하고 Swift Concurrency의 기능을 탐색하여 개발 워크플로우에서 그 강력함을 최대한 발휘해보세요. 즐거운 코딩 되세요!

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

안녕하세요! 아래는 테이블 태그를 Markdown 형식으로 변경하였습니다.

![이미지](/assets/img/2024-06-23-WhatsNewinWWDC2024ExploringSwiftConcurrency_0.png)

퀵 커넥트:

[LinkedIn 프로필](https://www.linkedin.com/in/shubhransh-gupta)
