---
title: "코틀린 채널 완벽 정복 초보부터 프로까지 - 1부"
description: ""
coverImage: "/assets/img/2024-06-23-MasteringKotlinChannelsFromBeginnertoPro-Part1_0.png"
date: 2024-06-23 21:02
ogImage:
  url: /assets/img/2024-06-23-MasteringKotlinChannelsFromBeginnertoPro-Part1_0.png
tag: Tech
originalTitle: "Mastering Kotlin Channels: From Beginner to Pro - Part 1"
link: "https://medium.com/@mortitech/mastering-kotlin-channels-from-beginner-to-pro-part-1-7368060d1391"
---

![그림](/assets/img/2024-06-23-MasteringKotlinChannelsFromBeginnertoPro-Part1_0.png)

코틀린 채널은 두 개 이상의 코루틴 간 통신을 가능하게 하는 강력한 동시성 구조입니다. 애플리케이션의 다른 부분 간에 협력하는 방법을 제공하여 서로 간섭하지 않고 데이터를 공유하고 함께 작업할 수 있습니다. 이 시리즈의 게시물에서는 코틀린 채널을 더 깊이 파고들어 이 강력한 도구를 습득하는 방법과 팁, 트릭, 그리고 최고의 실천 방법에 대해 살펴보겠습니다.

# Kotlin 채널이란?

코틀린 채널은 한 코루틴에서 다른 코루틴으로 데이터가 흐를 수 있는 파이프 라인으로 생각할 수 있습니다. 채널은 사실 코루틴이 메시지를 보내고 받을 수 있는 버퍼나 큐입니다. 한 코루틴은 채널에 데이터를 넣을 수 있고(send) 다른 코루틴은 그 데이터를 채널에서 가져올 수 있습니다.

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

채널은 코루틴을 위해 특히 구축된 Producer-Consumer 패턴의 구현입니다. 정보를 보내는 코루틴을 생산자(producer)라고 하고, 정보를 받는 코루틴을 소비자(consumer)라고 합니다. 하나 이상의 코루틴이 동일한 채널로 정보를 보낼 수 있고, 하나 이상의 코루틴이 데이터를 수신할 수 있습니다.

여러 코루틴이 동일한 채널에서 정보를 받을 때, 각 엘리먼트는 소비자 중 하나에 의해 한 번만 처리됩니다. 한 번 처리된 요소는 즉시 채널에서 제거됩니다.

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

코틀린 채널 사용 예제입니다:

```js
fun channel(
    coroutineScope: CoroutineScope,
) {
    val channel = Channel<String>() // 채널은 String 형식의 데이터를 교환합니다

    // 프로듀서가 코루틴 내에서 데이터를 전송하기 시작합니다
    coroutineScope.launch {
        Log.d("Channel", "채널에 데이터 1 전송")
        channel.send("데이터 1")
        Log.d("Channel","채널에 데이터 2 전송")
        channel.send("데이터 2")
        channel.close() // 데이터 전송이 완료되었으므로 채널을 닫습니다
    }

    // 컨슈머가 다른 코루틴 내에서 데이터를 수신하기 시작합니다
    coroutineScope.launch {
        channel.consumeEach {
            Log.d("Channel","받은 데이터: $it")
        }
        Log.d("Channel","완료!") // 채널이 닫힌 후에 호출됩니다
    }
}
```

이 예제에서는 두 개의 코루틴을 시작합니다. 첫 번째 코루틴은 channel.send() 함수를 사용하여 채널에 데이터 문자열을 전송합니다. 두 번째 코루틴은 channel.consumeEach() 함수를 사용하여 채널에서 데이터를 소비하는데, 이 함수는 채널에 전송된 모든 값들을 반복하는 편리한 방법입니다. 데이터를 전송한 후, 코루틴은 channel.close() 함수를 사용하여 채널을 닫습니다.

코틀린 채널의 close() 함수는 데이터 전송의 끝을 신호하는 데 사용됩니다. 개념적으로, 이는 채널을 닫는 것을 나타내는 특별한 토큰을 전송하는 것과 같습니다. 코루틴이 이 토큰을 수신하면 더 이상 데이터가 채널로 전송되지 않을 것임을 알 수 있습니다. 코루틴은 이 시점에서 반복을 중지하여 채널이 닫히기 전에 이전에 전송된 모든 요소를 수신하도록 보장합니다. 이를 통해 통신 세션의 종료를 처리하기가 더 쉬워지며, 코루틴이 종료되기 전에 모든 데이터가 처리되도록 보장됩니다.

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

위 코드의 출력은 다음과 같습니다:

```js
채널로 데이터 1을 보냈습니다
수신: 데이터 1
채널로 데이터 2을 보냈습니다
수신: 데이터 2
완료!
```

# ReceiveChannel

Kotlin 코루틴에서 ReceiveChannel은 코루틴에서 데이터를 수신하는 방법을 제공하는 채널 유형입니다. 데이터를 송신자에게 다시 보낼 수 없이 채널에서 데이터를 소비하고 싶을 때 사용됩니다.

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

소비자 코루틴에 ReceiveChannel을 사용함으로써 데이터를 받기만 하고 실수로 데이터를 전송하지 않도록 보장합니다. 이러한 관심사의 분리는 코드를 보다 유지 보수하기 쉽고 오류가 적은 상태로 유지하는 데 도움이 됩니다.

아래는 ReceiveChannel을 사용하는 예시입니다.

```js
@OptIn(ExperimentalCoroutinesApi::class)
fun receiveChannel(
    coroutineScope: CoroutineScope
) {
    var channel: ReceiveChannel<String> = Channel()

    // Producer Coroutine
    coroutineScope.launch {
        channel = produce {
            send("A")
            send("B")
            send("C")
            send("D")
            // 채널을 명시적으로 닫아줄 필요가 없습니다
        }
    }

    // Consumer Coroutine
    coroutineScope.launch {
        channel.consumeEach {
            Log.d(TAG, "Received $it")
        }
        // 소비자 코루틴 내에서 데이터를 다시 채널로 보내는 것은 불가능합니다
        // 왜냐하면 이것은 ReceiveChannel이기 때문입니다
        // channel.send("E")

        // 채널은 자동으로 닫힙니다
        Log.d(TAG, "Is producer closed: ${channel.isClosedForReceive}")
    }
}
```

위 코드에서 우리는 코루틴을 위한 ProducerScope를 만드는 편리한 방법인 produce 함수를 사용했습니다. produce 함수는 코루틴 내에서 데이터를 보낼 수 있게 해주며 새로운 코루틴에서 사용할 수 있는 ReceiveChannel을 반환합니다.

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

channel.send("E") 라인은 주석 처리되어 있습니다. 이는 컴파일 오류를 발생시킬 것이기 때문입니다. channel은 ReceiveChannel이므로 send() 메서드가 없습니다. 이는 소비자 코루틴이 ReceiveChannel을 사용할 때 생산자로 데이터를 다시 보낼 수 없음을 보여줍니다.

consumeEach 블록이 완료되면 produce 함수에 의해 채널이 자동으로 닫힙니다. 마지막으로 channel.isClosedForReceive 속성을 확인하여 결과가 true이므로 채널이 닫혔음을 알 수 있습니다.

위 코드의 출력은 다음과 같습니다:

Received A
Received B
Received C
Received D
Is producer closed: true

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

`Channel`과 `ReceiveChannel`의 차이

Kotlin 코루틴에서 `ReceiveChannel`과 일반 `Channel`의 주요 차이점은 `ReceiveChannel`은 채널에서 데이터를 소비하는 데만 사용될 수 있다는 것이며, 일반 `Channel`은 데이터를 보내고 받는 데 모두 사용될 수 있다는 것입니다.

`Channel`을 생성하면 코루틴 간에 데이터를 보내고 받을 수 있는 채널에 대한 참조가 생성됩니다. `Channel` 클래스는 `send()`와 `receive()`와 같은 함수를 제공하여 채널에서 데이터를 보내고 받을 수 있습니다.

반면에 `ReceiveChannel`을 생성하면 채널에서 데이터를 소비하는 데만 사용될 수 있는 채널에 대한 참조가 생성됩니다. `ReceiveChannel` 클래스는 `receive()` 및 `tryReceive()`와 같은 함수를 제공하여 채널에서 데이터를 받을 수 있습니다.

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

다시 말해서, Regular Channel은 상호 작용 채널로 coroutine 간에 데이터를 송수신하는 데 사용할 수 있고, ReceiveChannel은 단방향 채널로 채널에서 데이터를 받는 데만 사용할 수 있습니다.

# 파이프라인

수신 채널을 사용하여 파이프라인을 구현할 수 있습니다. 파이프라인은 채널에 의해 연결된 여러 단계로 구성되며 입력 데이터를 출력 데이터로 변환하는 데 함께 작동합니다.

파이프라인의 각 단계는 입력 채널에서 데이터를 소비하는 coroutine이며, 데이터에 대한 일부 계산을 수행한 후, 다음 단계에서 소비되는 출력 채널로 변환된 데이터를 전송합니다.

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

파이프라인 내 각 단계 사이의 입력 및 출력 채널은 각 단계가 데이터를 비동기적이고 독립적으로 처리할 수 있도록 하는 버퍼 역할을 합니다. 이를 통해 파이프라인은 대량의 데이터를 효율적으로 처리하고, 병렬로 계산을 다수의 코어나 스레드에 분산시킬 수 있습니다.

파이프라인은 각 단계가 데이터에 대해 특정 연산을 수행하는 상황에서 유용합니다. 예를 들어, 센서로부터의 데이터 스트림을 처리해야 하는 경우, 각 단계가 데이터에 대해 필터링, 평활화, 또는 평균 등의 특정 변환을 수행하는 파이프라인을 활용할 수 있습니다.

파이프라인 예시 1:

다음은 정수 스트림을 처리하는 파이프라인 예시입니다. 이 파이프라인은 짝수를 걸러내고 남은 홀수를 제곱하여 총합을 구합니다:

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
fun streamingNumbers(scope: CoroutineScope) {
    scope.launch {
        val numbers = produceNumbers(10)
        val result = pipeline(numbers)

        Log.d(TAG, result.receive().toString())
    }
}

// 숫자를 생성하여 파이프라인으로 보내는 함수
fun CoroutineScope.produceNumbers(count: Int): ReceiveChannel<Int> = produce {
    for (i in 1..count) send(i)
}

// 숫자를 처리하는 파이프라인
fun CoroutineScope.pipeline(
    numbers: ReceiveChannel<Int>
): ReceiveChannel<Int> = produce {
    // 짝수를 걸러냄
    val filtered = filter(numbers) { it % 2 != 0 }

    // 남은 홀수를 제곱함
    val squared = map(filtered) { it * it }

    // 제곱한 홀수들을 합침
    val sum = reduce(squared) { acc, x -> acc + x }

    send(sum)
}

fun CoroutineScope.filter(
    numbers: ReceiveChannel<Int>,
    predicate: (Int) -> Boolean
): ReceiveChannel<Int> = produce {
    numbers.consumeEach { number ->
        if (predicate(number)) send(number)
    }
}

fun CoroutineScope.map(
    numbers: ReceiveChannel<Int>,
    mapper: (Int) -> Int
): ReceiveChannel<Int> = produce {
    numbers.consumeEach { number ->
        send(mapper(number))
    }
}

fun reduce(
    numbers: ReceiveChannel<Int>,
    accumulator: (Int, Int) -> Int
): Int = runBlocking {
    var result = 0
    for (number in numbers) {
        result = accumulator(result, number)
    }
    result
}
```

이 예제에서 pipeline 함수는 filter, map 및 reduce 세 단계를 연결하여 새로운 파이프라인을 생성합니다. filter 단계는 짝수를 걸러내고, map 단계는 남은 홀수를 제곱하며, reduce 단계는 제곱된 홀수 값을 합산합니다.

각 단계는 입력 채널에서 데이터를 소비하고 filter, map 및 reduce 함수를 사용하여 출력 채널로 데이터를 생성하는 별개의 코루틴으로 구현됩니다. 파이프라인 함수는 파이프라인의 출력 채널을 나타내는 새로운 ReceiveChannel을 반환합니다.

파이프라인 예제 2 — 이미지 처리:

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

이미지 스트림을 처리하는 파이프라인 예제를 보여드리겠습니다. 이미지를 리사이징, 압축하고 저장하는 과정을 거칩니다:

```js
fun processImages(
    coroutineScope: CoroutineScope
) {
    coroutineScope.launch {
        val images = produceImages(listOf(
            "https://via.placeholder.com/300x300.png",
            "https://via.placeholder.com/500x500.png",
            "https://via.placeholder.com/800x800.png"
        ))
        val resized = resizeImages(images, 400)
        val compressed = compressImages(resized, 80)
        storeImages(compressed, Paths.get("output/"))
    }
}

fun CoroutineScope.produceImages(urls: List<String>): ReceiveChannel<ByteArray> = produce {
    for (url in urls) {
        val bytes = URL(url).readBytes()
        send(bytes)
    }
}

fun CoroutineScope.resizeImages(
    images: ReceiveChannel<ByteArray>, size: Int
): ReceiveChannel<ByteArray> = produce {
    images.consumeEach { image ->
        // ImageResizer can a util class to resize the image
        val resizedImage = ImageResizer.resize(image, size)
        send(resizedImage)
    }
}

fun CoroutineScope.compressImages(
    images: ReceiveChannel<ByteArray>, quality: Int
): ReceiveChannel<ByteArray> = produce {
    images.consumeEach { image ->
        // ImageCompressor can a util class to compress the image
        val compressedImage = ImageCompressor.compress(image, quality)
        send(compressedImage)
    }
}

suspend fun storeImages(images: ReceiveChannel<ByteArray>, directory: Path) {
    Files.createDirectories(directory)
    var index = 1
    for (image in images) {
        val file = directory.resolve("image${index++}.jpg")
        FileOutputStream(file.toFile()).use { output ->
            output.write(image)
        }
    }
}
```

이 예제에서 processImages 함수는 produceImages 함수를 사용하여 URL 목록에서 이미지 데이터의 스트림을 생성하는 ReceiveChannel을 생성합니다. 그런 다음 이 채널을 resizeImages 함수에 전달하여 이미지를 지정된 크기로 조정한 후 compressImages 함수에 출력 채널을 전달하여 이미지를 지정된 품질로 압축합니다. 마지막으로 compressImages 함수의 출력 채널을 디스크에 압축된 이미지를 저장하는 storeImages 함수에 전달합니다.

파이프라인의 각 단계는 입력 채널에서 데이터를 소비하고 resizeImages, compressImages 및 storeImages 함수를 사용하여 출력 채널에 데이터를 생성하는 별도의 코루틴으로 구현되어 있습니다.

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

resizeImages 및 compressImages 함수에서 사용되는 ImageResizer 및 ImageCompressor 클래스는 이미지 데이터에 대해 이러한 작업을 수행할 수있는 가상 클래스 예시입니다.

이 파이프라인은 이미지를 크기 조정, 압축 및 저장하는 편리하고 효율적인 방법을 제공합니다. 이 파이프라인은 추가 단계를 포함하거나 다양한 종류의 이미지 처리 작업을 처리하는 데 쉽게 확장할 수 있습니다.

# 다음은 무엇인가요?

이 시리즈의 다음 부분에서는 Kotlin 채널의 세계에 더욱 심층적으로 파고들어 다양한 유형의 채널과 실제 응용 프로그램을 탐구할 것입니다. 제2편을 마치면 Kotlin 채널을 사용하여 효율적이고 확장 가능한 동시성 응용 프로그램을 구축하는 방법을 포괄적으로 이해하게 될 것입니다. 기대해 주세요!

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

다음 부분은 여기서 찾을 수 있습니다.
