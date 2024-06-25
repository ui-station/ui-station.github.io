---
title: "일곱 가지 데모로 이해하는 코루틴 스코프, 컨텍스트 및 작업"
description: ""
coverImage: "/assets/img/2024-05-27-SevendemostounderstandcoroutinesscopecontextandJobs_0.png"
date: 2024-05-27 17:54
ogImage:
  url: /assets/img/2024-05-27-SevendemostounderstandcoroutinesscopecontextandJobs_0.png
tag: Tech
originalTitle: "Seven demos to understand coroutines: scope, context and Jobs"
link: "https://medium.com/proandroiddev/seven-demos-to-understand-coroutines-scope-context-and-jobs-e40a5092e58a"
---

코루틴을 이해하는 것은 패턴을 배우는 것이 아니라 구동 원리를 보는 것에서 나옵니다.

저번 포스트에서 안드로이드의 일반적인 코루틴 패턴을 해체하여 그 원리를 살펴보았습니다. 우리가 파고들수록, 모든 것을 가로지르는 세 가지 개념인 context, scopes, 그리고 Job이 있는 것을 보게 되었습니다.

그래서, 코루틴이 무엇이며, 올바르게 활용하는 데 중요한 열쇠라고 생각됩니다.

이를 염두에 두고, 그 세 가지를 살펴볼까요? 함께 재미있게 알아봅시다. 😉

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

(\* 약간의 유머가 있었어요)

# 데모 1: 코루틴을 실행하고 잊어버리기

코루틴 스코프는 코루틴을 넣는 상자입니다. 그들을 제한하는 목적으로 존재합니다.

코루틴을 취소하고 싶을 때 유용함을 알 수 있습니다 — 그냥 상자를 파괴하면, 상자 안의 모든 코루틴이 취소됩니다. 이것은 훌륭한 기능입니다. 왜냐하면 코루틴을 계속 추적할 필요가 없다는 것을 의미합니다. 그들의 생명주기는 "상자"에 의해 관리됩니다.

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

구체적인 예를 들어보겠습니다. 아래는 클릭할 때 배경색이 변경되는 버튼입니다:

![button](https://miro.medium.com/v2/resize:fit:1200/1*Xb48gAvABkH5LBhRstBB_g.gif)

이전 글에서 while(true)이 안전한 이유에 대해 설명했습니다. 다른 상황에서는 끔찍한 상황이겠지만요. 하지만 더 흥미로운 부분이 있는데요!

...바로 이겁니다: 9번 라인에서 코루틴을 간단히 실행시켰는데, 그냥 넘어갔습니다. 왜냐하면 해당 스코프에 안전하게 실행시켰기 때문입니다. 이 경우 rememberCoroutineScope()를 사용해 스코프를 얻었습니다. RandomColourButton이 화면에서 제거되면 해당 스코프가 취소되며 실행된 코루틴도 취소됩니다.

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

환경에서 스레드만 있는 개발자에게 온다면, 코루틴이 얼마나 큰 진전인지 과장할 수 없을 정도로 중요합니다. 스레드를 시작하고 그냥 두는 것은 상상도 못할 정도로 중요하지요. 모두를 꼼꼼하게 추적해야 하며, 이를 제대로 처리하지 않으면 이상한 버그와 자원 누수가 발생할 수 있습니다.

# 데모 2: launch()된 코루틴 취소하기

위 코드에서처럼 scope.launch '...'로 코루틴을 시작할 때, 실행 중인 코루틴을 나타내는 Job이 생성됩니다.

Job.cancel()을 사용하여 해당 코루틴을 취소할 수 있습니다 (부모 스코프나 그 안의 다른 코루틴에는 영향을 주지 않음).

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

위의 코드로 인해 번쩍이는 버튼이 2초 후에 번쩍임이 멈춥니다. 첫 번째 coroutine의 Job을 저장하고, 지연 후에 이를 취소하기 위해 두 번째 coroutine을 시작합니다.

# 데모 3: coroutine 내에서 coroutine 시작하기

Job은 하위 Job을 가질 수도 있습니다. 하위 Job을 만드는 간단한 방법은 단순히 coroutine 내에서 'launch'를 사용하는 것입니다.

Job이 취소되면 모든 하위 Job들도 함께 취소됩니다. 번쩍이는 버튼을 사용하여 해당 예제를 살펴보겠습니다.

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

이번에는 카운트를 증가시키는 또 다른 코루틴을 추가했습니다:

<img src="https://miro.medium.com/v2/resize:fit:1200/1*ESBEwgreP7EpxnqXm4cF9A.gif" />

랜덤 색상을 선택하는 코루틴의 자식으로 증가하는 카운트를 세는 코루틴이 시작됩니다:

번쩍임이 멈추면 카운트도 멈추는 것을 주목해보세요. 이는 12번 줄에서 시작된 코루틴이 10번 줄에서 시작된 코루틴의 자식이기 때문입니다. 부모가 지연 후에 취소되면 자식도 함께 취소됩니다.

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

코루틴의 세계에서, 이 부모-자식 관계를 규제하는 법칙들(코루틴을 잃어버리지 않도록 보장하는 동일한 법칙들)은 구조화된 동시성이라고 합니다.

# 데모 4: 컨텍스트에서 코루틴을 실행하고 작업을 지정하는 방법

Job에 코루틴을 실행할 다른 방법도 있습니다. launch 또는 async 호출에서 Job을 지정할 수 있습니다:

```js
launch(myJob) {
  ...
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

당연히 이 작업은 당신에게 달려 있습니다. 필요할 때 myJob을 주의 깊게 추적하여 이 코루틴의 생명 주기를 올바르게 제한해야 합니다.

이것이 동작하는 이유는 launch에 전달하는 인수가 CoroutineContext이며, 그 중에 job이 있는 요소이기 때문입니다.

코루틴 컨텍스트는 코루틴에 대한 메타데이터의 집합에 불과합니다. 이는 해당 코루틴에 대한 Job, 이름 및 디스패처를 포함합니다. 이는 코루틴이 어떻게 실행될지 코루틴 라이브러리에 설명하는 코루틴에 부착된 수하물 태그로 생각할 수 있습니다.

# 데모 5: 다른 스레드 풀로 코루틴 시작하기

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

"‘luggage tags’ 중 하나인 이 메타데이터는 코루틴에 첨부됩니다. 디스패처는 코루틴이 실행될 스레드나 스레드 풀을 결정하기 위해 사용됩니다.

따라서 위와 같은 방법을 사용하여 IO 디스패처의 스레드 풀에서 코루틴을 시작할 수 있습니다:

```js
launch(Dispatchers.IO) {
  ...
}
```

이는 디스패처를 Dispatchers.IO로 설정하는 코루틴 콘텍스트를 생성함으로써 작동합니다."

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

# 데모 6: 기타 코루틴 시작 옵션 (및 조합)

또는, 코루틴 컨텍스트의 요소를 더하여 plus 연산자를 사용할 수 있습니다. 다음은 "boo"라는 코루틴을 IO 디스패처 스레드 풀에 새 작업으로 시작하는 방법입니다:

```js
launch(Dispatchers.IO + Job() + CoroutineName("boo")) {
  ...
}
```

이 방법으로 코루틴 컨텍스트를 생성할 때 언급하지 않은 요소는 부모의 컨텍스트에서 상속됩니다. 즉, Dispatchers.IO에서 실행 중인 코루틴에서 Job()을 시작하면 시작된 하위 코루틴도 Dispatchers.IO에서 실행됩니다. 이는 하위 코루틴의 컨텍스트 디스패처가 부모로부터 상속되었기 때문입니다.

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

# 데모 7: 코루틴 스코프 사용하기 — 그리고 그로 인해 발생하는 일들

지금까지 확인한 내용은 다음과 같습니다:

- 데모 1: 코루틴 스코프는 코루틴을 넣을 수 있는 수명 제한 컨테이너입니다.
- 데모 3: 코루틴 Job은 코루틴을 넣을 수 있는 수명 제한 컨테이너입니다.

음. 그럼 차이는 무엇인가요?

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

사실, 차이가 없다는 것을 발견했습니다. 왜냐하면:

...코루틴 라이브러리 코드에서 볼 수 있듯이:

```js
public interface CoroutineScope {
    public val coroutineContext: CoroutineContext
}
```

그럼에도 불구하고, 본질적으로 같은 것에 대해 별도의 타입과 전체적으로 다른 이름을 가지고 있는 이유는 무엇일까요? 코틀린 언어의 전 프로젝트 리드인 로만 엘리자로프는 선박이 "로프"에 대해 여러 다른 이름을 가지고 있는 것과 유사성을 들어줍니다. 왜냐하면 그들은 사용에 따라 명명되기 때문입니다. 해저고리는 주요 돛을 올리기 위한 로프이고, 다운할은 그것을 내리기 위한 로프이며, 샤트는 돛을 다루기 위한 것입니다.

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

비유적으로 말하면, 코루틴 범위는 특정 유형의 코루틴 컨텍스트로 기명됩니다. 이는 코루틴의 수명을 제한하고 제어하는 데 사용됩니다.

실제로는 코루틴 범위는 특정 라이프사이클에 연결된 작업(Job)을 갖게 됩니다:

- viewModelScope는 뷰 모델이 지워질 때 작업이 취소됩니다.
- rememberCoroutineScope()는 컴포지션을 떠나는 시점에 작업이 취소됩니다.
- viewLifecycleOwner.lifecycleScope은 Android View의 라이프사이클이 종료될 때 작업이 취소됩니다.

그리고 이어서 이어지죠.

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

그래서 우리는 모두 데모 1로 돌아가서, 코루틴을 scope 내에 배치하면 안전하게 실행 및 무시할 수 있다는 것을 알 수 있었습니다. 이는 scope가 관련된 라이프사이클에 바운드된 Job을 가지고 있기 때문입니다.

# 요약하자면...

코루틴 컨텍스트, 스코프 및 Job의 개념은 코루틴을 사용하는 데 중요한 부분을 차지합니다. 우리는 컨텍스트가 코루틴과 관련된 메타데이터 목록과 같으며, 옵저버들에 정보를 제공하고 디스패처에게 실행 방법을 알려주는 것과 같다는 것을 보았습니다. 우리는 Job이 실행 중인 코루틴을 나타내며, 다른 코루틴에 대해 부모 역할을 할 수 있다는 것을 알았습니다. 그리고 이를 통해 코루틴 스코프를 이해할 수 있습니다. 즉, 부모 Job이 있는 컨텍스트입니다.

도움이 되었으면 좋겠습니다. 계속해서 궁금한 점을 아래에 질문해 주세요!

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

Tom Colvin은 20년 동안 소프트웨어 아키텍처를 담당해오고 있으며, 특히 안드로이드 작업을 좋아합니다. 그는 모바일 앱 전문가인 Apptaura의 공동 창업자이며, 컨설팅 기반으로 활동하고 있습니다.
