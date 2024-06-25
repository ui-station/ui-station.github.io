---
title: "마스터링 디스패치 큐Dispatch Queues in Swift 이해, 구현, 그리고 제한들"
description: ""
coverImage: "/assets/img/2024-05-23-MasteringDispatchQueuesinSwiftUnderstandingImplementationandLimitations_0.png"
date: 2024-05-23 13:09
ogImage:
  url: /assets/img/2024-05-23-MasteringDispatchQueuesinSwiftUnderstandingImplementationandLimitations_0.png
tag: Tech
originalTitle: "Mastering Dispatch Queues in Swift: Understanding, Implementation, and Limitations"
link: "https://medium.com/@vinodh_36508/mastering-dispatch-queues-in-swift-understanding-implementation-and-limitations-4ed37916fe8a"
---

![Image](/assets/img/2024-05-23-MasteringDispatchQueuesinSwiftUnderstandingImplementationandLimitations_0.png)

디스패치 큐는 Apple의 Grand Central Dispatch (GCD)의 일부로 2009년 소개되었으며, 스레드의 대안으로 작업을 비동기적으로 수행할 수 있게 합니다. 우리는 그들의 기능과 작업 큐, 스레드와 비교한 차이를 탐색해보겠습니다.

## 기본 사용법

디스패치 큐를 하나의 작업 라인으로 생각해보세요. 이렇게 생성할 수 있습니다:

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
let queue = DispatchQueue(label: "my.queue")
```

이제, 해당 큐에서 어떤 작업을 실행하고 싶다면 다음과 같이 작업을 추가하면 됩니다:

```swift
queue.async {
    print("여기서 일부 작업을 수행 중입니다!")
}
```

# 디스패치 큐 유형

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

## 시리얼 큐

기본적으로 디스패치 큐를 생성할 때 특성을 지정하지 않으면 시리얼 큐를 얻게 됩니다. 시리얼 큐에 대기열에 있는 작업들은 한 번에 하나씩 순차적으로 실행되며, 마치 한 줄로 움직이는 자동차와 같습니다. 예를 들어:

```js
let serialQueue = DispatchQueue(label: "my.serial.queue")
serialQueue.async {
 print("작업 1")
}
serialQueue.async {
 print("작업 2")
}
```

## 병렬 큐

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

한편, 동시 큐는 작업들이 동시에 실행되도록 허용합니다. 이는 여러 차로로 이어진 고속도로와 같이 작동하여 차(작업)들이 나란히 이동할 수 있도록 합니다. 이와 같이 동시 큐를 만들 수 있습니다:

```js
let concurrentQueue = DispatchQueue(label: "my.concurrent.queue", attributes: .concurrent)
concurrentQueue.async {
 print("동시 작업 1")
}
concurrentQueue.async {
 print("동시 작업 2")
}
```

## 지연된 실행

Dispatch 큐는 작업의 지연된 실행을 가능하게 합니다. 특정 시간 이후에 작업을 시작할 수 있습니다:

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
let delayedQueue = DispatchQueue(label: "delayed.queue")
delayedQueue.asyncAfter(deadline: .now() + 1) {
    print("1초 후에 시작하는 작업입니다!")
}
```

## 작업 우선순위

GCD(Grand Central Dispatch) 큐는 작업 우선순위를 설정할 수 있도록 해줍니다. 이는 다른 작업들과 얼마나 빨리 실행되어야 하는지를 결정합니다:

```swift
let highPriorityQueue = DispatchQueue(
    label: "high.priority.queue",
    qos: .userInitiated
)
let highPriorityTask = DispatchWorkItem {
    print("이것은 높은 우선순위 작업입니다!")
}
highPriorityQueue.async(execute: highPriorityTask)
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

## 작업 취소

필요할 경우 작업을 취소할 수 있지만, 반드시 스레드와 작업 큐와 같이 작업 내에서 취소 여부를 확인하는 것이 중요합니다.

```js
var task: DispatchWorkItem!
task = DispatchWorkItem {
 guard !task.isCancelled else {
 print("작업이 취소되었습니다.")
 return
 }
 // 일부 작업 수행
}
queue.async(execute: task)
// 일정 시간 후 취소하려면
DispatchQueue.global().asyncAfter(deadline: .now() + 3) {
 task.cancel()
}
```

## 데이터 공유

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

디스패치 큐는 큐에 사용자 정의 데이터를 연결하는 DispatchSpecificKey를 제공하여 동일한 컨텍스트에서 실행되는 작업들 간에 데이터 공유를 허용합니다.

```swift
let id = UUID()
let specificKey = DispatchSpecificKey<UUID>()
queue.setSpecific(key: specificKey, value: id)
if let retrievedId = DispatchQueue.getSpecific(key: specificKey) {
 print("Found the ID: \(retrievedId)")
}
```

물론! 이제 우리는 디스패치 큐에서 타겟팅 개념을 코드 예제와 함께 살펴보면서 큐 간에 어떻게 데이터가 흐르는지 이해해보겠습니다.

# 큐 타겟팅과 디테일 이해하기

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

## 특정 사항 설정 및 액세스

동일한 대기열에서 실행되는 작업 내에서 대기열을 만들고 특정 사항(사용자 지정 데이터)을 설정하고 해당 특정 사항에 액세스하는 방법을 고려해보세요:

```js
let queue1 = DispatchQueue(label: "queue1")
let idKey = DispatchSpecificKey<Int>()
let dateKey = DispatchSpecificKey<Date>()
queue1.setSpecific(key: idKey, value: 42)
queue1.setSpecific(key: dateKey, value: Date())
queue1.async {
 print("queue1", "id", DispatchQueue.getSpecific(key: idKey))
 print("queue1", "date", DispatchQueue.getSpecific(key: dateKey))
}
```

## 새로운 대기열에서 특정 사항을 잃는 경우

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

기존 큐 내에서 새 큐를 생성하더라도 자동으로 세부사항을 상속받지 않습니다:

```js
queue1.async {
 let queue2 = DispatchQueue(label: "queue2")
 queue2.setSpecific(key: idKey, value: 1729)
 queue2.async {
 print("queue2", "id", DispatchQueue.getSpecific(key: idKey))
 print("queue2", "date", DispatchQueue.getSpecific(key: dateKey))
 }
}
```

## 특정 사항 상속을 위한 큐 지정

지정은 새 큐에서 특정 정보 손실 문제를 해결합니다.

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
let queue2 = DispatchQueue(label: "queue2", target: queue1)
queue2.setSpecific(key: idKey, value: 1729)
queue2.async {
 print("queue2", "id", DispatchQueue.getSpecific(key: idKey))
 print("queue2", "date", DispatchQueue.getSpecific(key: dateKey))
}
```

## 지정된 대상 대기열을 사용하여 병렬 작업 실행하기

두 개의 독립적인 작업, 데이터베이스 쿼리와 네트워크 요청이 있다고 가정하고 이를 병렬로 실행하려고 합니다:

```js
func response(for request: URLRequest, queue: DispatchQueue) -> HTTPURLResponse {
 let group = DispatchGroup()
 let databaseQueue = DispatchQueue(label: "database-request", target: queue)
 databaseQueue.async(group: group) {
 makeDatabaseQuery()
 }
 let networkQueue = DispatchQueue(label: "network-request", target: queue)
 networkQueue.async(group: group) {
 makeNetworkRequest()
 }
 group.wait()
 return .init()
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

## 특정 사항 상속 유지

새 대기열이 특정 사항을 상속받도록하려면 부모 대기열을 인수로 전달하십시오:

```js
response(for: .init(url: .init(string: "https://www.testurl.com")!), queue: queue)
```

## 대기열 생성 최적화

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

한개의 동시 서버 대기열을 만들고 새 대기열을 이 속성을 상속하도록 타깃팅하여 최적화하세요:

```js
let serverQueue = DispatchQueue(label: "server", attributes: .concurrent)
// 각 요청에 대해
let queue = DispatchQueue(label: "request-\(requestId)", attributes: .concurrent, target: serverQueue)
queue.setSpecific(key: requestIdKey, value: requestId)
queue.async {
    response(for: .init(url: .init(string: "https://www.testurl.com")!)
}
```

# 제약 사항:

Swift의 디스패치 대기열은 스레드와 작업 대기열의 강점을 결합하여 비동기 작업, 우선순위 관리, 취소 및 특정 데이터 저장을 제공합니다. 그러나 아직도 주의를 요하는 일부 도전이 존재합니다.

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

- 특정 상속을 위한 큐 전달

현재 새 큐 내에서 특정 사항을 상속하려면 부모 큐를 명시적으로 전달해야 하며, 암시적 데이터 흐름의 목적이 무력화됩니다:

```js
response(for: .init(url: .init(string: "https://www.testurl.com")!), queue: requestQueue)
```

이 접근 방식은 계층별로 데이터를 전달하지 않고 실행 컨텍스트 전체에서 매끄럽게 데이터를 공유하는 목표에 모순됩니다.

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

2. 취소 및 스레드 관리 상속
   디스패치 큐 사이에 구체적인 사항을 상속할 수는 있지만, 하나의 작업 항목의 취소는 자식 작업 항목으로 전파되지 않습니다. 제대로 처리되지 않을 경우에는 여전히 스레드가 증가할 수 있으며, 이는 잠재적으로 리소스 문제로 이어질 수 있습니다.

3. 큐 굶주림과 강렬한 작업
   단일 작업 단위를 위해 많은 큐를 생성하거나 CPU 집약적 작업을 단일 큐에서 실행하는 것은 스레드 굶주림으로 이어질 수 있습니다. 디스패치 큐는 작업 항목간 협력을 위한 도구가 부족하여 CPU 이용률을 공정하게 제어하는 데 어려움을 겪습니다.

4. 협력적인 동시 코드 부재
   GCD는 강력하지만, 협력적인 동시 코드를 작성하기 위한 기능이 부족합니다. 작업 항목은 휴식 시간에 다른 리소스를 활용할 수 있게 허용하지 않으면 CPU 시간을 경쟁하게 됩니다.

5. 데이터 레이스 완화
   GCD는 데이터 레이스를 방지하기 위한 바리어 같은 동기화 도구를 제공하나, 명시적 처리가 필요하며 전통적인 NSLock 같은 잠금과 비교해 속도가 느릴 수 있습니다.

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

# Summary

요약하자면, GCD는 강력한 병렬 처리 도구를 제공하지만, 암시적 데이터 흐름, 취소 상속, CPU 자원 관리, 협력적 병렬 코드 및 효과적인 데이터 경쟁 처리와 같은 문제를 해결하는 것은 여전히 개선이 필요한 부분입니다. GCD 도구는 도움이 되지만 병렬 처리 모델에 깊게 통합되지는 않아, 이러한 세밀한 문제들을 효과적으로 처리하는 책임은 개발자에게 있습니다.

다음 단계: Swift에서 병렬 처리를 마스터하고 싶나요? 다음 챕터인 '병렬 처리 마스터링: Task'을 살펴보세요.

# Series Navigation

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

- 제1부: 스위프트에서 쓰레드 탐구
- 제2부: 스위프트의 Operation Queues 탐구: 한계와 함께 쓰레딩 향상
- 제3부: 스위프트에서 Dispatch Queues 숙달하기: 이해, 구현 및 한계
- 제4부: 병행성 마스터링: Task
- 제5부: 스위프트 병행성: @Sendable 및 Actors로 데이터 보호하기
