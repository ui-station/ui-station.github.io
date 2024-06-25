---
title: "Coroutines, RxJava, Executor 뭐가 더 빠를까 각 작업별 성능 비교"
description: ""
coverImage: "/assets/img/2024-06-23-WhatisfasterandinwhichtasksCoroutinesRxJavaExecutor_0.png"
date: 2024-06-23 01:21
ogImage:
  url: /assets/img/2024-06-23-WhatisfasterandinwhichtasksCoroutinesRxJavaExecutor_0.png
tag: Tech
originalTitle: "What is faster and in which tasks? Coroutines, RxJava, Executor?"
link: "https://medium.com/proandroiddev/what-is-faster-and-in-which-tasks-coroutines-rxjava-executor-952b1ff62506"
---

<img src="/assets/img/2024-06-23-WhatisfasterandinwhichtasksCoroutinesRxJavaExecutor_0.png" />

저는 가끔 다중 스레딩 프레임워크 중 어떤 것이 제일 빠른지 궁금했던 적이 있었어요. 어느 날 운명이 저를 이 질문을 조사하도록 이끌었죠. 만약 여러분도 궁금하시다면, 전 테스트를 해보고 비교한 결과를 여러분과 공유해 보았습니다.

# 문제

우선, 왜 이를 해보게 되었는지부터 알아봅시다. 제게는 간단한 작업이 있었어요: 수백 개의 콜백을 호출하는 시스템이 있었죠. 모든 콜백은 가능한 빨리 완료되어야 했죠.

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

각 콜백에 소요되는 복잡성이나 시간을 알지 못했어요. 콜백은 객체를 생성하거나 서버나 데이터베이스에 긴 요청을 할 수도 있어요. 이 경우에는 데이터베이스나 서버에 요청하기 때문에 멀티스레딩을 사용해야 하죠.

이 문제를 해결하려고 할 때, 어떤 상황에서 어떤 멀티스레딩 프레임워크가 더 빠른지 정확히 모르는 것을 깨달았어요.

# 동기부여

하지만 당장 떠오르는 질문이 있죠: "아직 아무도 테스트해보지 않은 건가요?" 의외로 제 요구사항을 충족시키는 테스트를 찾을 수 없었어요.

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

일부 테스트는 Rx 대 Coroutines와 같이 두 기술 간의 간단한 비교였습니다. 다른 일부는 내 의견으로는 너무 구체적한 테스트 케이스를 가졌는데, 예를 들어 산술 연산이나 데이터베이스 요청만을 테스트하는 것 등이 그러했습니다.

어쨌든, 이에 대해 매우 만족스럽지 않았고 나만의 테스트를 하기로 결정했습니다.

멀티스레드 프레임워크의 모든 사용 사례를 커버할 수는 없다는 것은 합리적입니다. 내 작업과 관련이 있는 것들만 테스트할 것입니다... 그래서 이게 완벽한 테스트가 아니지만 꽤 철저할 것입니다.

# 도구

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

측정 도구로 시작해 봅시다. 안드로이드 개발자이기 때문에 안드로이드 도구를 사용하겠습니다. 이론적으로는 컴퓨터에서 JVM에서 멀티스레딩 테스트를 실행하는 것이 안드로이드 장치에서 실행하는 것과 다를 수 있지만, 실제로는 큰 차이가 없으며 전체 테스트 결과에 영향을 미치지 않습니다.

그리고 안드로이드 장치에서 코드 성능을 테스트하기 위해 사용할 수 있는 Jetpack Microbenchmark 도구가 있습니다.

Microbenchmark 측정 테스트는 보통의 인스트루먼테이션 테스트와 유사하지만, 차이점은 BenchmarkRule이라는 특정 Rule을 사용한다는 것뿐입니다.

```js
@get:Rule
val benchmarkRule = BenchmarkRule()

@Test
fun sampleTest() {
   benchmarkRule.measureRepeated {
       // 여기에서 측정할 것입니다
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

결과적으로 실행에 대한 정보가 포함된 JSON을 얻게 됩니다: 최소 및 최대 시간, 그리고 가장 중요한 중앙값 시간.

```js
    "benchmarks": [
        {
            "name": "sampleTest",
            "params": {},
            "className": "com.test.benchmark.ExampleBenchmark",
            "totalRunTimeNs": 85207217833,
            "metrics": {
                "timeNs": {
                    "minimum": 9.82149833E8,
                    "maximum": 1.019338584E9,
                    "median": 1.004151917E9,
                    "runs": [...]
                },
                "allocationCount": {
                    "minimum": 324.0,
                    "maximum": 324.0,
                    "median": 324.0,
                    "runs": [...]
                }
            },
            "sampledMetrics": {},
            "warmupIterations": 3200,
            "repeatIterations": 5000,
            "thermalThrottleSleepSeconds": 0
        }
    ]
```

이 JSON은 반복 횟수, 객체 할당 횟수 등을 포함한 기타 정보를 담고 있습니다. 현재는 이것이 우선 순위가 아닙니다.

# 벤치마크 테스트 케이스

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

프레임워크 간 주요 차이점은:

- 단일 스레드 생성에 소요되는 시간
- 프레임워크가 작업을 효율적이고 빠르게 스레드 간에 분배할 수 있는 능력

우리는 이러한 프레임워크 간 차이를 평가하기 위해 테스트 케이스 결과를 탐색할 것입니다.

이제 우리는 무엇을 테스트하고 싶은지 결정해야 합니다. 테스트 데이터부터 시작해봅시다.

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

# 테스트 데이터

별거 아닌 거에요. 0부터 99까지 범위의 100개 항목을 포함하는 ArrayList를 만들어봤어요.

```kotlin
private fun createTestList(): List<Int> {
   return List(100) { it }
}
```

그런 다음 각 항목에 대해 몇 가지 작업을 수행할 거예요.

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

이제 테스트 케이스를 살펴보겠습니다.

# 단일 스레드

단일 스레드 테스트 케이스부터 시작해 보죠. 이러한 테스트 케이스의 결과를 비교할 때는 프레임워크가 단일 스레드를 생성하는 데 걸리는 시간을 주의 깊게 살펴볼 것입니다. 하나의 스레드만 있는 경우, 프레임워크가 스레드를 초기화하고 생성하는 데 걸리는 시간을 확인할 수 있습니다.

## 직접 호출

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

첫 번째 테스트 케이스는 간단히 메서드를 직접 호출하는 것입니다. 어떤 프레임워크도 사용하지 않습니다.

```kotlin
@Test
fun directInvoke() {
   val list = createTestList()
   benchmarkRule.measureRepeated {
       list.forEach { action(it) }
   }
}
```

## RxJava

두 번째 테스트 케이스는 Rx입니다. Completable 내에서 작업을 수행할 것입니다. 각 작업에 대해 별도의 Completable이 생성될 것입니다. Scheduler는 모든 작업이 단일 스레드에서 실행되도록 보장하는 Scheduler.single이 될 것입니다.

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

그리고 작업 완료를 기다려야 하기 때문에 결과 Completable에서 blockingAwait를 호출합니다.

```kotlin
@Test
fun rxOne() {
   val list = createTestList()
   val scheduler = Schedulers.single()
   benchmarkRule.measureRepeated {
       val completables = list.map {
           Completable.fromAction {
               action(it)
           }.subscribeOn(scheduler)
       }
       Completable.merge(completables).blockingAwait()
   }
}
```

## 코틀린 코루틴

어딜 가나 젊고 유망한 코틀린 코루틴 프레임워크가 없었던 우리들이 어디에 있겠어요? 코루틴이 실행될 때까지 기다리려면 async를 통해 작업을 실행하고 완료를 기다리기 위해 await을 사용합니다. 따라서 각 작업을 위한 별도의 코루틴이 있을 것입니다. 모든 것을 단일 스레드에서 실행하도록 하려면 단순히 runBlocking을 사용하면 됩니다.

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

```kt
@Test
fun coroutineOne() {
   val list = createTestList()
   benchmarkRule.measureRepeated {
       runBlocking {
           list.map {
               async {
                   action(it)
               }
           }.forEach { it.await() }
       }
   }
}
```

## Kotlin Flow

Flow에서 비교해보겠습니다. 코루틴과 Rx를 직접적으로 비교하는 것은 좋은 방법이 아닐 수 있습니다. 둘 다 다른 방식으로 사용되고 다른 개념을 적용하기 때문에 멀티쓰레딩과 관련이 있다고 해도 다릅니다.

각 액션에 대한 Flow를 생성하고 모두 결합한 다음 간단히 수집합니다. 각 액션에 대해 별도의 Flow가 있을 것입니다. 한편, 디스패처를 어디에도 지정하지 않았기 때문에 모든 작업이 동일한 스레드에서 수행됩니다.

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

```kotlin
@Test
fun flowOne() {
   val list = createTestList()
   benchmarkRule.measureRepeated {
       runBlocking {
           val flows = list.map {
               flow {
                   val result = action(it)
                   emit(result)
               }
           }
           flows.merge().collect()
       }
   }
}
```

# 스레드 수는 CPU 스레드 수와 같음

물론, 한 스레드만 사용하는 것은 최선의 선택처럼 보이지 않습니다. 왜냐하면 멀티스레딩을 위한 프레임워크가 있기 때문이죠.

스레드 풀에 있는 스레드 수가 CPU 스레드 수와 같은 상황을 고려해 봅시다. 이상적으로는 이러한 테스트 케이스의 결과를 비교하여 두 번째 차이(프레임워크가 작업을 효율적으로 빠르게 스레드 간에 분배하는 능력)를 볼 수 있습니다. 각 작업에 대해 충분한 스레드가 없기 때문에 프레임워크가 그런 작업들을 어쨌든 분배해야 하게 될 것입니다.

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

프로세서 코어 간 작업의 균등한 분배를 기대해서는 안 됩니다. 운영 체제와 CPU가 결정하는 것은:

- 무엇이 실행되는지,
- 언제 실행되는지,
- 누가 실행하는지입니다.

현대의 "큰" CPU는 SMT/HyperThreading을 사용하여 각 CPU 코어마다 여러 논리 스레드를 생성할 수 있습니다. CPU 코어는 독립적으로 명령을 실행할 수 있는 물리적 단위입니다. 반면 스레드는 한 코어에서 실행할 수 있는 논리적 소프트웨어 단위입니다. 또한 이동형 CPU와 최신 인텔 CPU는 다양한 유형의 CPU 코어를 사용합니다. 대형 코어는 복잡한 작업에 적합하며, 중형 및 절전 코어는 성능에서 상당히 차이가 있습니다.

그래서, 우리는 이 풀을 적은 수의 스레드를 갖는 풀로 간주할 수 있습니다.

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

CPU 쓰레드 수를 결정하는 훌륭한 방법이 있어요:

```js
Runtime.getRuntime().availableProcessors();
```

제 테스트 기기에서는 이 값이 여덟이에요.

## RxJava

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

Rx를 시작해보겠습니다. Rx에는 원하는 동작을 구현하는 Scheduler.Computation이 있어요. 사실 코드는 단일 스레드를 사용할 때와 똑같이 유지됩니다. 유일한 차이점은 각 개별 Completable에 대해 Scheduler.computation()을 사용하여 subscribeOn을 만든다는 것이에요.

```kotlin
@Test
fun rxCPU() {
   val list = createTestList()
   val scheduler = Schedulers.computation()
   benchmarkRule.measureRepeated {
       val completables = list.map {
           Completable.fromAction {
               action(it)
           }.subscribeOn(scheduler)
       }
       Completable.merge(completables).blockingAwait()
   }
}
```

## Kotlin 코루틴

이제 코루틴을 살펴보죠. Dispatchers.Default라는 것을 가지고 있어요. 해당 문서에는:

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

기본적으로 "병렬성"의 최대 수준은 CPU 코어 수와 동일합니다. 우리가 필요한 것이 바로 그겁니다.

코드는 싱글 코어 버전과 유사합니다. 이제 액션들은 Dispatchers.Default 내부에서 withContext를 사용하여 실행됩니다.

```js
@Test
fun coroutineCPU() {
   val list = createTestList()
   val dispatcher = Dispatchers.Default
   benchmarkRule.measureRepeated {
       runBlocking {
           list.map {
               async {
                   withContext(dispatcher) {
                       action(it)
                   }
               }
           }.forEach { it.await() }
       }
   }
}
```

하지만 중요한 세부 사항이 있습니다... Dispatchers.Default를 좀 더 자세히 살펴보면이 생성자를 찾을 수 있습니다.

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

```kotlin
내부 객체 DefaultScheduler는 SchedulerCoroutineDispatcher를 상속합니다(
   CORE_POOL_SIZE, MAX_POOL_SIZE,
   IDLE_WORKER_KEEP_ALIVE_NS, DEFAULT_SCHEDULER_NAME
)
```

CPU 스레드 수인 CORE_POOL_SIZE와 MAX_POOL_SIZE(이 경우 200만이라고합니다) 두 개의 상수를 전달하는 것이 의심스럽습니다.

더 자세히 조사해보니 이러한 변수들은 CoroutineScheduler를 만들기 위해 사용된다는 것을 알 수 있습니다.

```kotlin
private fun createScheduler() = CoroutineScheduler(
   corePoolSize, maxPoolSize, idleWorkerKeepAliveNs, schedulerName
)
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

그리고 문서에는 다음과 같이 나와 있습니다:

그래서 Dispatchers.Default는 CPU 스레드 수에 제한 받지 않습니다.

Dispatchers.Default와 Scheduler.Computation을 직접 비교하는 것은 잘못된 접근입니다. 경우에 따라 Dispatchers.Default는 추가적인 스레드를 사용할 수 있습니다. 다행히 문서는 비교를 더 공정하게 하기 위한 정보도 제공합니다. 단순히 LimitingDispatcher를 사용하면 됩니다. 이를 위해 Dispatchers.Default의 limitedParallelism 메서드를 CPU 스레드 수와 함께 호출하면 됩니다.

```js
@Test
fun coroutineCPULimit() {
   val list = createTestList()
   val threadCount = Runtime.getRuntime().availableProcessors()
   val dispatcher = Dispatchers.Default.limitedParallelism(threadCount)
   benchmarkRule.measureRepeated {
       runBlocking {
           list.map {
               async {
                   withContext(dispatcher) {
                       action(it)
                   }
               }
           }.forEach { it.await() }
       }
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

## 코틀린 Flow

flow에 대해 동일한 작업을 합시다. Dispatchers.Default를 사용하세요.

```js
@Test
fun flowCPU() {
   val list = createTestList()
   val dispatcher = Dispatchers.Default
   benchmarkRule.measureRepeated {
       runBlocking {
           val flows = list.map {
               flow {
                   val result = action(it)
                   emit(result)
               }.flowOn(dispatcher)
           }
           flows.merge().collect()
       }
   }
}
```

그리고 제한을 두고 Dispatchers.Default를 사용하세요.

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
@Test
fun flowCPULimit() {
   val list = createTestList()
   val threadCount = Runtime.getRuntime().availableProcessors()
   val dispatcher = Dispatchers.Default.limitedParallelism(threadCount)
   benchmarkRule.measureRepeated {
       runBlocking {
           val flows = list.map {
               flow {
                   val result = action(it)
                   emit(result)
               }.flowOn(dispatcher)
           }
           flows.merge().collect()
       }
   }
}
```

## Java Executor

코루틴의 모든 복잡성을 탐구해야 했기 때문에, 이러한 복잡성을 사용해볼까요? 따라서 Executor를 사용해보려고 합니다.

먼저, Executors.newFixedThreadPool을 사용하여 Executor를 생성해 보겠습니다. 이렇게 함으로써, 단순히 쓰레드 수로 제한된 Executor가 생성되며, 우리의 경우에는 CPU 코어의 수입니다.

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

measureRepeated 메소드 이전에 Executor를 생성하니 Executor를 만드는 것은 쉬운 작업이 아닙니다. 그런 다음, 작업을 수행하는 Executor에서 submit을 호출합니다. 완료를 기다리기 위해 get 메소드를 사용해 보겠습니다.

```js
@Test
fun executorFixedCPU() {
   val list = createTestList()
   val threadCount = Runtime.getRuntime().availableProcessors()
   val executorService = Executors.newFixedThreadPool(threadCount)
   benchmarkRule.measureRepeated {
       val futures = list.map { executorService.submit { action(it) } }
       futures.forEach { it.get() }
   }
}
```

Executors.newWorkStealingPool 메소드를 사용하여 관심 있는 두 번째 유형의 Executor를 생성할 것입니다. 실제로, 이 또한 제한된 수의 스레드로 Executor를 생성합니다. 그러나 메소드 이름에 "Stealing"이 포함된 것은 우연이 아닙니다. 이 Executor의 스레드는 현재 스레드가 비어 있어 다른 스레드에게 큐에 대기 중인 작업이 있는 경우 현재 스레드가 idle 상태가 되었을 때 다른 스레드로부터 작업을 훔칠 수 있습니다. 이는 최종적으로 공통 큐를 비울 수 있도록 도와줄 수 있습니다.

코드적으로는 이전과 동일합니다. 단, Executor를 만드는 방법이 다를 뿐입니다.

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

```kotlin
@Test
fun executorStealCPU() {
   val list = createTestList()
   val threadCount = Runtime.getRuntime().availableProcessors()
   val executorService = Executors.newWorkStealingPool(threadCount)
   benchmarkRule.measureRepeated {
       val futures = list.map { executorService.submit { action(it) } }
       futures.forEach { it.get() }
   }
}
```

# 각 작업에 대한 스레드

이제 거의 무제한(실제로는 많은 수, 많다고 할 정도)으로 스레드를 할당하는 가능성을 탐색해 보겠습니다. 100개의 작업만 있기 때문에 100개의 스레드를 할당할 수 있습니다. 더 이상 필요하지 않습니다.

## RxJava

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

Rx를 시작합니다. Scheduler.io는 이러한 역할을 맡습니다. Scheduler.io에는 스레드 캐시가 있습니다. 사용 가능한 빈 스레드가 있다면 캐시에서 하나를 가져옵니다. 그렇지 않으면 새로운 스레드를 생성합니다. 코드는 Scheduler.computation과 동일하지만 다른 스케줄러를 사용합니다.

```js
@Test
fun rxIo() {
   val list = createTestList()
   val scheduler = Schedulers.io()
   benchmarkRule.measureRepeated {
       val completables = list.map {
           Completable.fromAction {
               action(it)
           }.subscribeOn(scheduler)
       }
       Completable.merge(completables).blockingAwait()
   }
}
```

## Kotlin 코루틴

코루틴에서 유사한 작업은 Dispatchers.IO의 책임이므로 익숙한 코드에 삽입하기만 하면 됩니다.

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

```kotlin
@Test
fun coroutineIo() {
   val list = createTestList()
   val dispatcher = Dispatchers.IO
   benchmarkRule.measureRepeated {
       runBlocking {
           list.map {
               async {
                   withContext(dispatcher) {
                       action(it)
                   }
               }
           }.forEach { it.await() }
       }
   }
}
```

## Kotlin Flow

Repeat the same for Flow.

```kotlin
@Test
fun flowIo() {
   val list = createTestList()
   val dispatcher = Dispatchers.IO
   benchmarkRule.measureRepeated {
       runBlocking {
           val flows = list.map {
               flow {
                   val result = action(it)
                   emit(result)
               }.flowOn(dispatcher)
           }
           flows.merge().collect()
       }
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

## 자바 Executor

안타깝게도 Executor는 그다지 좋은 API가 없어요. 따라서 우리는 그에게 100개의 스레드를 할당할 거예요. 100개의 작업만 있으면 충분하니까 더 많이 필요하지 않을 거에요.

```kotlin
@Test
fun executorFixedIo() {
   val list = createTestList()
   val executorService = Executors.newFixedThreadPool(100)
   benchmarkRule.measureRepeated {
       val futures = list.map { executorService.submit { action(it) } }
       futures.forEach { it.get() }
   }
}
```

newWorkStealingPool도 같은 방식으로 할 거에요.

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

```kotlin
@Test
fun executorStealIo() {
   val list = createTestList()
   val executorService = Executors.newWorkStealingPool(100)
   benchmarkRule.measureRepeated {
       val futures = list.map { executorService.submit { action(it) } }
       futures.forEach { it.get() }
   }
}
```

이것은 모든 테스트 케이스를 프레임워크별로 나열한 표입니다.

<img src="/assets/img/2024-06-23-WhatisfasterandinwhichtasksCoroutinesRxJavaExecutor_1.png" />

그리고 마지막으로, 테스트를 실행합니다.

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

# 테스트

하하, 조금 속았죠. 먼저, 행동의 카테고리를 고려해 봅시다.

이 중에 단 5가지가 있습니다:

- 산술 — 간단한 산술 연산;
- listsManipulation — 객체 조작;
- 저장 — 저장소와의 작업 시뮬레이션;
- 네트워크 — 네트워크와의 작업 시뮬레이션;
- 혼합 — 이전 스크립트 모두의 조합, 원본 작업에서 행동의 복잡성을 알 수 없기 때문에.

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

간단한 것부터 시작해 봅시다.

# 산술

제가 여러분에게 상기시키고 싶은 점은 0부터 99까지의 각 항목에 대해 작업을 수행할 것이라는 것입니다. 이 경우 연산을 조금 더 복잡하게 만들기 위해 숫자를 Float로 변환하고 해당 숫자를 그 자신의 값의 제곱으로 올립니다. 0의 제곱부터 99의 제곱까지입니다. 물론 어느 시점에서는 Float의 한계에 도달하겠지만, 괜찮습니다.

```js
private fun arithmetic(seed: Int): Int {
   return seed.toFloat().pow(seed.toFloat()).toInt()
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

일반적으로, 이 범주로 분류할 수 있는 작업에는 생성자를 사용하여 객체를 생성하거나 속성에 액세스하는 것이 포함됩니다. 이러한 작업은 완료하는 데 오랜 시간이 걸리지 않습니다.

결과는 무엇인가요?

![그림](/assets/img/2024-06-23-WhatisfasterandinwhichtasksCoroutinesRxJavaExecutor_2.png)

예상대로, directInvoke가 최고의 결과를 보였습니다. 가장 가까운 경쟁 상대보다 70배 빨랐습니다. 이는 이러한 작업이 매우 간단하며 멀티스레딩 프레임워크를 사용하는 데 추가적인 오버헤드가 작업 자체 비용의 10배 더 높을 수 있다는 이치를 따랐기 때문입니다.

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

그러나 뜻밖의 충격은 두 번째와 세 번째 자리에서 일어났습니다. 두 자리 다 Executors로 고정된 스레드 수를 가진 것들이었어요. 처음에는 directInvoke가 제일일 것으로 예상했는데, 그 다음에는 단일 스레드의 테스트 케이스, 그리고 CPU, 그리고 마지막으로 IO가 실행될 것으로 기대했어요. 그런데 고정된 스레드 수를 가진 Executors가 이러한 로직을 완전히 무너뜨리는 모습을 볼 수 있었어요. 한편으로는 Executors.newWorkStealingPool을 이용하여 만든 Executor는 "무한" 스레드 수를 가질 때 성능이 더 좋아 보입니다.

Dispatchers.Default를 한정하고 한정하지 않았을 때의 시간이 다른 것을 볼 수 있습니다. 그래서 한정을 한 이유가 있습니다.

하나의 동작을 처리하는 데 가장 많은 오버헤드를 가진 것은 Flow입니다. 모든 경쟁자들보다 느립니다. 그리고 코루틴은 한 스레드에서 Rx보다 나은 경우가 있지만, 스레드 수가 증가할수록 Rx가 선두 주자로 우뚝 서는 것을 볼 수 있습니다.

요약하면, 이 카테고리의 동작에서 directInvoke가 가장 효과적임을 알 수 있습니다. 따라서 이러한 작업에는 멀티스레딩 프레임워크를 사용하지 않는 것이 더 나은 방법입니다. 대신에 하나의 스레드나 적은 수의 스레드를 가진 풀을 사용하세요.

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

# 리스트 조작

자, 이제 더 복잡한 내용을 살펴보겠습니다 — 객체 조작입니다. 객체를 리스트에 추가하면 객체를 다루기가 더 쉬워집니다. 이 카테고리의 작업에는 예를 들어 POJO 매핑이 포함될 수 있습니다.

작업 내에서 우리는 매개변수로 받은 숫자와 같은 크기를 갖는 새로운 리스트를 만들기만 하면 됩니다. 다음으로 몇 가지 작업을 수행하고, 리스트를 맵으로 변환한 다음 다시 몇 가지 작업을 수행한 후 이 컬렉션을 필터링합니다... 일반적으로 리스트를 조작합니다.

이곳에서의 복잡성 기본은 실제 작업 그 자체가 아닙니다. 이것은 정규 불변 리스트이며, 시퀀스가 아닙니다. 이것은 각 작업 후 새로운 리스트가 생성된다는 것을 의미합니다.

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

```kotlin
private fun listsManipulation(seed: Int): Int {
    List(seed) { it }
        .map { it.toFloat() }
        .map { it + 0.3f }
        .associateBy { it.toString() }
        .mapValues { it.value * it.value }
        .filter { it.value > 5f }
    return seed
}
```

어땠어요?

![image](/assets/img/2024-06-23-WhatisfasterandinwhichtasksCoroutinesRxJavaExecutor_3.png)

directInvoke는 빠르게 선두에서 물러났어요. 객체 조작 같은 기본 작업조차 어려움이 있군요.

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

Executor를 생성하여 리드가 됩니다. Executors.newWorkStealingPool을 사용하면 더 효율적입니다. 간단한 대안인 Executors.newFixedThreadPool보다 더욱 효율적입니다.

Rx는 조금 앞으로 나아갑니다. 앞에는 Scheduler.computation이 있습니다. 원칙적으로 이 작업에 대한 우선 선택지입니다. 왜냐하면 더 적은 자원을 사용하기 때문입니다.

단일 스레드 테스트 케이스는 목록의 끝에 있지만, directInvoke는 여전히 중간에 남아 있습니다.

flowCPU와 coroutineCPU는 목록의 맨 아래에 있습니다. 이러한 테스트 케이스들은 CPU 코어의 수와 같은 수의 스레드를 가지고 있으며 제한이 없습니다. 테스트를 다시 실행했지만 결과는 동일했습니다. 버그가 있을 수 있다고 생각하여 코루틴을 업데이트했지만 변한 점은 없습니다.

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

총론적으로, 이 복잡성 수준에서 CPU 테스트 케이스가 실행 시간 측면에서 IO보다 빠릅니다.

# 저장공간

데이터베이스나 작은 파일에 액세스하는 것은 블로킹 작업이기 때문에 단순히 스레드를 휴면 상태로 두기만 하면 됩니다. 휴면 시간은 500에서 1,490 마이크로초 또는 0.5에서 1.5 밀리초 사이입니다. 그러나 흔한 Thread.sleep 메서드는 "바쁜 대기(busy waiting)"로 인해 정확하지 않은 결과를 나타냅니다. 그래서 LockSupport.parkNanos를 사용하여 스레드를 휴면 상태로 만들겠습니다.

```js
private fun storage(seed: Int): Int {
   val timeInMicroseconds = 500 + 10 * seed.toLong()
   LockSupport.parkNanos(TimeUnit.MICROSECONDS.toNanos(timeInMicroseconds))
   return seed
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

마침내 결과가 나왔어요.

![image](/assets/img/2024-06-23-WhatisfasterandinwhichtasksCoroutinesRxJavaExecutor_4.png)

IO 테스트 케이스를 먼저 실행한 후 CPU를 실행하고 마지막으로 싱글 스레드를 실행했어요. 텍스트북에서 직접 나온 것처럼 들리죠? "IO 스레드 풀이 IO 작업에 가장 좋아요."

rxIO 테스트 케이스가 flowsIO 및 coroutinesIO보다 더 빠르다는군요. 아마 쓰레드를 생성하는 데 필요한 자원이 가장 적게 사용되는 것 같아요만... 그런 작업에서는 그렇게 중요한 점은 아니에요. 차이는 딱 한 밀리초뿐이에요. 프레임워크 간의 구별은 거의 사라진 것 같아요.

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

# 네트워크

음, 네트워크 케이스를 테스트하지 않을 수가 없어요. 모든 것은 이전 테스트랑 똑같죠. 이번에는 쓰레드가 잠시 동안 0에서 99밀리초 사이에 쉬도록 만들었어요. 이 테스트에서는 "바쁜 대기"에 의한 지연이 더 이상 중요한 역할을 하지 않아요. 그래서 우리는 Thread.sleep을 사용할 거예요.

```js
private fun network(seed: Int): Int {
   TimeUnit.MILLISECONDS.sleep(seed.toLong())
   return seed
}
```

다음 결과에 주의하세요.

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

아래는 테이블 태그를 Markdown 형식으로 변경하십시오.

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

```kotlin
private fun mixed(seed: Int): Int {
   return when {
       seed % 5 == 0 -> network(seed)
       seed % 3 == 0 -> storage(seed)
       seed % 2 == 0 -> listsManipulation(seed)
       else -> arithmetic(seed)
   }
}
```

가장 오랜 시간이 걸리는 작업은 코드에서 확인할 수 있듯이 90밀리초입니다.

그래서 여기에 결과가 있습니다.

![Image](/assets/img/2024-06-23-WhatisfasterandinwhichtasksCoroutinesRxJavaExecutor_6.png)

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

일반적으로, 세 개의 블록으로 분할하는 패턴이 동일하게 나타납니다: IO, CPU, One.

차이점은 Executors.newWorkStealingPool로 생성된 Executor가 작은 작업들을 추가하여 선두를 선점한다는 것입니다. 이는 작업을 "도둑질"하기 때문으로 보입니다.

# 결론

파일 시스템과 네트워크 작업을 처리할 때는 멀티스레딩의 역할이 거의 사라진 것을 알 수 있습니다. 대부분의 멀티스레딩 사용은 IO 작업을 위한 것입니다. 따라서 성능을 기반으로한 멀티스레딩 프레임워크 선택은 이상합니다. 편의성 및 중요한 요소 등의 기준을 바탕으로 선택하는 것이 더 나을 것입니다.

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

그러나 0.5밀리초 동안 스레드를 차단하는 것보다 더 간단한 작업을 수행한다면 작은 스레드 풀을 사용하는 것이 좋습니다. 예를 들어 CPU 코어 수를 풀의 크기로 사용할 수 있습니다.

Executors와 Rx는 이러한 작업에 가장 적합합니다.

예외는 산술, 객체 생성 등과 같이 매우 간단한 작업에 해당합니다. 이러한 경우에는 모든 작업을 완료하는 데 필요한 시간보다 프레임워크에 의한 스레드 생성에 필요한 시간이 더 많이 걸리므로 멀티스레딩을 사용하지 않는 것이 좋습니다.

문제를 해결하기 위해 제가 따랐던 단계는 다음과 같습니다:

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

- 모든 간단한 작업을 별도의 작업 풀에 분리하여 directInvoke를 사용하여 실행했습니다.
- 다른 모든 작업은 Dispatchers.IO를 사용하여 코루틴에서 실행되었는데, 이는 동기 및 비동기 코드를 동시에 작성하기 쉽게 해줬습니다.

그리고 어떻게 생각하세요? 현재 어떤 프레임워크를 사용하고 있나요?
