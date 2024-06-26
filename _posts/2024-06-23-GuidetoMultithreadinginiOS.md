---
title: "iOS에서 멀티스레딩 사용하는 방법 안내"
description: ""
coverImage: "/assets/img/2024-06-23-GuidetoMultithreadinginiOS_0.png"
date: 2024-06-23 23:54
ogImage:
  url: /assets/img/2024-06-23-GuidetoMultithreadinginiOS_0.png
tag: Tech
originalTitle: "Guide to Multithreading in iOS"
link: "https://medium.com/@bhavesh.misri/guide-to-multithreading-in-ios-1cb31536a931"
---

혹시 PR 댓글로 "이것은 주요 큐에 들어가지 않는다" 또는 비슷한 내용을 받았는데 왜 그런지 전혀 모르겠다고요? 이 기사가 딱 맞는 거에요!

저희 모두가 디버그 네비게이터를 보면서 스택 트레이스를 살펴보며 꽤 많은 시간을 보냈다고 확신합니다. 스택 트레이스는 우리 프로그램을 실행하는 과정에서 실행된 스레드들의 백트레이스에 불과합니다. 스레드는 독립적으로 실행할 수 있는 명령어 집합이라고 볼 수 있습니다.

모든 응용 프로그램에는 최소한 하나의 스레드, 즉 주요 스레드가 있습니다. 이것은 스레드 계층 구조에서 가장 높은 위치에 있는 스레드로, 다른 스레드들에게 작업을 위임할 수 있습니다. 주요 스레드가 여러 다른 백그라운드 스레드에 작업을 위임하는 것을 우리는 멀티스레딩이라고 부릅니다. 멀티스레딩은 단순히 여러 스레드에서 작업을 동시에 수행하여 애플리케이션의 성능을 개선하는 것입니다.

![가이드 이미지](/assets/img/2024-06-23-GuidetoMultithreadinginiOS_0.png)

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

단일 코어 프로세서에서는 시간 조각내기로 동시성을 달성할 수 있지만 멀티스레딩과 동시성은 1950년대 멀티코어 프로세서가 등장하면서 인기를 얻었습니다. 단일 코어 프로세서와 달리 멀티코어 프로세서는 시간 조각내기에 관련된 문맥 전환 없이 병렬로 여러 스레드를 실행할 수 있습니다. 복수 스레드를 실행하는 이 능력을 사용하지 않으면 리소스를 최적으로 활용하지 않는다는 것을 의미하며 결과적으로 성능이 저하될 수 있습니다. 그리고 여기에서 Grand Central Dispatch(GCD)가 등장합니다.

# Grand Central Dispatch란?

iOS 애플리케이션을 개발한 적이 있다면 멀티스레딩을 사용하고 있음을 깨닫겠지만 (스택 추적이 그 증거입니다!), 실제로 스레드와 상호 작용하지는 않는 것을 알게 될 것입니다. 이는 GCD가 이를 백그라운드에서 처리하기 때문입니다. GCD는 어떤 스레드가 어떤 작업을 실행할지 관리하는 책임이 있는데, 우리는 단지 실행해야 할 작업을 지정하기만 하면 됩니다. 이는 GCD가 시스템 수준에서 작동하고 시스템의 리소스, 리소스를 최적으로 활용할 수 있는 방법, 그리고 효율적으로 작업을 수행할 수 있는 방법을 더 잘 알고 있기 때문입니다.

GCD는 디스패치 대기열 또는 일반적으로 대기열이라고도 하는 일부 관리하며, 여기에 우리의 작업을 올리고 예약하고 나중에 스레드 풀 내에서 실행할 수 있도록 분배합니다. 이 디스패치 대기열은 "직렬"일 수도 있고(즉, 작업을 순차적으로 실행), "동시적"일 수도 있고 작업을 병렬로 실행할 수 있습니다. 앞서 언급했듯이 동시성은 더 나은 성능으로 이어지므로 병행 대기열을 더 자주 사용할 것을 권장합니다.

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

# DispatchQueue.main.what…?

메인 대기열(main queue)은 애플리케이션의 주 스레드에서 작업을 실행하는 GCD에서 제공하는 전역적으로 사용 가능한 시리얼 대기열입니다.

하지만 주 스레드를 차단한다는 것은 무엇을 의미할까요? 그리고 이것이 사용자 경험에 실제로 영향을 미칠까요? 이미 알고 있듯이 주 스레드는 UI를 업데이트하는 데 사용됩니다. 이는 매우 리소스 집약적인 작업입니다. 대부분의 기기들이 이 작업을 매 초마다 여러 번 수행하기 때문에, 심지어 소수 초의 지연도 UI가 얼마나 부드러울지에 영향을 줄 수 있습니다. 애플리케이션이 UI를 여러 번 그릴 수 있게 하려면 필요한 리소스가 있어야 하는데, 만약 주 스레드에서 불필요한 작업을 수행한다면 이것은 불가능합니다.

이를 피하기 위해, 주 대기열 외에도 GCD는 백그라운드 대기열로도 알려지는 여러 전역 대기열을 제공합니다. 이 대기열은 주 스레드에 속하지 않는 작업, 즉 UI 업데이트가 아닌 모든 작업을 수행하는 데 사용됩니다!

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

예를 들어, 특정 상태에 따라 애니메이션을 표시해야 하는 경우가 있습니다. 특정 동작을 반환하는 함수가 있습니다. 주요 대기열에서 애니메이션을 가져와 표시하는 대신, 전역 백그라운드 스레드에서 애니메이션을 가져오고 주요 스레드에서만 애니메이션을 표시하는 것이 합리적입니다.

```js
DispatchQueue.global(qos: .userInteractive).async { [weak self] in
  // 상태에 따라 애니메이션 가져오기
  // 주요 스레드를 차단하지 않도록 전체 백그라운드 스레드에서
  let animation = getAnimation(status)
  DispatchQueue.main.async { [weak self] in
    // UI 업데이트이므로 주요 스레드에 애니메이션 표시
    self?.showAnimation(animation)
  }
}
```

그리고 DispatchQueue.main.async에서 async는 무엇을 의미할까요? GCD는 동기적 또는 비동기적 방식으로 작업을 디스패치할 수 있습니다. sync를 사용하면 작업 실행을 완료한 후에만 호출자 함수에 제어를 반환합니다. 반면, async를 사용하면 작업 실행을 시작하고 호출자에게 즉시 제어를 반환하여 스레드를 차단하지 않고 실행합니다. 일반적으로 API 호출이나 CPU 집약적 작업을 수행할 때 async를 사용합니다. 또한, 주요 스레드에 대해 async를 사용했던 것처럼, 요구 사항에 따라 DispathQueue.global().async 또는 sync를 사용할 수 있습니다.

# 그렇다면 왜 Operation Queues가 필요할까요?

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

GCD는 멀티스레딩을 가능하게 하는 저수준 API인 것을 알고 있죠. 반면에 Operation queues는 GCD 위에서 구축된 추상화입니다. 이를 통해 우리는 작업에 우선 순위를 추가하고 그들 사이에 의존성을 설정할 수 있습니다. 디스패치 큐가 순수한 FIFO라면, operation queues는 그렇지 않습니다. 또한, 디스패치 큐가 직렬 또는 병렬일 수 있는 것을 기억하죠? Operation queues는 항상 병렬입니다. 우리는 의존성을 정의함으로써 특정 시퀀스를 설정할 수도 있지만, 순수하게 순차적일 수는 없습니다.

Operation queues를 구현하기 위해 OperationQueue 또는 NSOperatioQueue 클래스를 사용하고, 이러한 큐에 추가된 작업 또는 연산에는 Operation 또는 NSOperation 클래스를 사용합니다. 이러한 연산들은 디스패치 큐 작업과 마찬가지로 독립적으로 실행할 수 있는 명령의 단위입니다.

Operation queues의 실질적인 장점은 무엇일까요? 이러한 작업을 GCD로도 구현할 수는 없을까요?

- 다른 작업의 결과에 따라 작업을 수행해야 할 때, 디스패치 큐의 FIFO 동작을 극복하기 위해 operation queues를 사용할 수 있습니다.
- 디스패치 큐는 우선순위를 설정할 수 없습니다. 작업에 우선순위를 정해야 하는 경우에는 operation queues를 사용해야 합니다.
- Operation queues를 사용하면 이미 예약된 작업을 취소할 수 있습니다. GCD는 투명성 면에서 유명하지 않기 때문에 이는 디스패치 큐로는 쉽게 구현할 수 없습니다. 우회 방법은 있지만 GCD로는 이것을 하는 좋은 방법이 없습니다. 또한, 한번 디스패치 큐에서 작업이 실행을 시작하면 중지할 수 없습니다. Operation queues는 이미 실행 중인 작업을 강제로 중지하지는 않지만, 취소된 프로퍼티를 true로 설정합니다. 각각의 작업에는 준비, 실행, 완료 상태가 있습니다. 완료가 true로 설정되면 실행이 성공적으로 완료된 것입니다. 작업과 연결된 완료 블록이 있다면, 완료 플래그가 설정될 때 실행됩니다. 그러나 취소된 작업의 경우, 완료 플래그가 설정되기 전에 취소 플래그가 설정됩니다. 따라서 완료 블록을 적절히 수정하여 필요한 경우 취소된 시나리오를 처리할 수 있습니다. 이것은 GCD로는 불가능한 일입니다.

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

아래는 작업 대기열이 동작하는 예시입니다.

```swift
import Foundation
let queue = OperationQueue()
queue.maxConcurrentOperationCount = 3

let op1 = BlockOperation(block: {
  print("op1 실행 중")
})

let op2 = BlockOperation(block: {
  print("op2 실행 중")
})

let op3 = BlockOperation(block: {
  print("op3 실행 중")
})
let op4 = BlockOperation(block: {
  print("op4 실행 중")
})

op4.queuePriority = .veryHigh
op1.addDependency(op2)

queue.addOperation(op1)
queue.addOperation(op2)
queue.addOperation(op3)
queue.addOperation(op4)
queue.waitUntilAllOperationsAreFinished()
```

그리고 이로써 마무리합니다. 이것은 GCD와 Operation Queues를 사용한 스위프트에서의 멀티스레딩에 대한 간단한 소개였습니다. 읽어 주셔서 감사합니다. 질문이 있으시면 언제든지 물어보세요. 피드백은 언제나 환영이니 댓글을 남겨주세요!
