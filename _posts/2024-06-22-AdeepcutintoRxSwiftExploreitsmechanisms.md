---
title: "RxSwift 심층 탐구 메커니즘 분석"
description: ""
coverImage: "/assets/img/2024-06-22-AdeepcutintoRxSwiftExploreitsmechanisms_0.png"
date: 2024-06-22 23:24
ogImage: 
  url: /assets/img/2024-06-22-AdeepcutintoRxSwiftExploreitsmechanisms_0.png
tag: Tech
originalTitle: "A deep cut into RxSwift: Explore its mechanisms"
link: "https://medium.com/zen8labs/a-deep-cut-into-rxswift-explore-its-mechanisms-a10156db71ef"
---


# 목적

이 튜토리얼의 주요 목적은 RxSwift의 핵심 구성 요소에 대한 심층적인 이해를 제공하는 것입니다. RxSwift 뒷면의 구현에 주로 초점을 맞춰 설명합니다.

이 게시물을 읽은 후에는 관찰 가능한(observables)이 무엇인지, 구독(subscriptions)이 무엇인지, 관찰 가능한이 어떻게 작동하는지에 대한 명확한 이해가 있을 것으로 기대됩니다.

이 튜토리얼은 RxSwift에 대한 일부 경험이 있다고 가정하고 있습니다. 이는 이해하기 쉽게 만들어줄 것입니다. 저희 zen8labs에서는 RxSwift를 주로 멤버들에게 교육하고 있으며, Serg Dort의 멋진 튜토리얼 "Learn Rx by implementing Observable"을 주의 깊게 참고하도록 강력히 권장합니다.

<div class="content-ad"></div>

Serg Dort님께 이 유용한 튜토리얼을 작성해 주신 것에 대해 진심으로 감사드립니다. 이 튜토리얼은 RxSwift의 구현을 간단하게 설명하여 RxSwift의 주요 구성 요소와 그들이 어떻게 함께 작동하는지 이해하기 쉽게 만들었습니다.

한 번 다시, 이 튜토리얼을 주의 깊게 읽고 코드를 단계별로 따라 하시기를 부탁드립니다. 믿어 주세요, 정말 값어치가 있을 겁니다.

정말 솔직히 말씀드리자면, RxSwift의 내부 작동 방식을 알 필요는 없습니다. 단지 옵저버블이 요소를 발행할 때, 정지할 때, 오류가 발생할 때, 옵저버블이 발행할 수 있는 요소의 개수, 그리고 구독을 해제해야 하는 시점에 집중하면 됩니다. 이러한 것들을 잘 이해하면 어떤 문제도 없이 RxSwift를 사용할 수 있습니다.

하지만 제 경우에는 항상 사용 중인 것들이 어떤 일이 벌어지는지 이해하고 싶습니다. 제가 무엇을 올바르게 수행하고 있는지 확인하는 가장 좋은 방법입니다.

<div class="content-ad"></div>

# 질문들

간단한 예제를 살펴봅시다:

![이미지](/assets/img/2024-06-22-AdeepcutintoRxSwiftExploreitsmechanisms_0.png)

이것은 기본적인 예제입니다. 그러나 RxSwift의 중요한 개념들을 대부분 포함하고 있습니다.

<div class="content-ad"></div>

여기에는 많은 숨겨진 세부 사항이 있습니다.

이에 대한 몇 가지 질문이 있습니다:

- Observable은 추상 클래스이고 Disposable은 프로토콜입니다. 그래서 여기서 실제로 생성되는 유형은 무엇인가요?
- 이러한 오퍼레이터들은 실제로 어떻게 작동합니까?
- 데이터는 이러한 오퍼레이터들을 통해 어떻게 처리됩니까?
- 오퍼레이터들은 어떻게 함께 구성되는가요?
- 왜 우리는 로직을 실행하기 위해 구독해야 하죠?
- 구독은 언제 끝나나요?

<div class="content-ad"></div>

이러한 질문에 대답하려면 이 연산자들의 구현을 살펴보겠습니다.

## 구현

연산자의 경우:

![이미지](/assets/img/2024-06-22-AdeepcutintoRxSwiftExploreitsmechanisms_1.png)

<div class="content-ad"></div>

맵 연산자:

![맵 연산자](/assets/img/2024-06-22-AdeepcutintoRxSwiftExploreitsmechanisms_2.png)

필터 연산자:

![필터 연산자](/assets/img/2024-06-22-AdeepcutintoRxSwiftExploreitsmechanisms_3.png)

<div class="content-ad"></div>

테이블 태그를 Markdown 형식으로 변경해 주세요.


| header1 | header2 |
|---------|---------|
| data1   | data2   |
| data3   | data4   |


<div class="content-ad"></div>

(1) Producer 클래스는 실제로 Observable 클래스의 하위 클래스이므로 RxSwift 연산자는 기본적으로 Producer의 다른 하위 유형을 반환하는 메서드입니다.

(2) Producer에 구독할 때마다 SinkDisposer가 반환됩니다. 사실 Observable 클래스는 추상 유형이며 해당 인스턴스를 직접적으로 생성하지 않습니다. 대신, 우리는 연산자를 사용하여 observables을 만듭니다. 따라서 우리가 일상적으로 프로그래밍에서 사용하는 observables 대부분은 사실 Producer의 하위 클래스입니다. 따라서 observables에 구독할 때 대부분의 구독은 SinkDisposer입니다. 예시의 구독 또한 SinkDisposer입니다.

(3) Producer 클래스에는 "run"이라는 내부 추상 메서드가 있습니다. 이는 Producer의 하위 클래스가 비즈니스 로직을 구현해야 하는 곳입니다. "run" 메서드는 항상 sink와 subscription 두 가지를 반환합니다.

이제, 예시에서 네 가지 observables(ObservableSequence, Map, Filter, TakeCount)의 구현을 탐구하여 sink와 구독이 무엇인지에 대한 질문에 답해보겠습니다.

<div class="content-ad"></div>

ObservableSequence

![Image](/assets/img/2024-06-22-AdeepcutintoRxSwiftExploreitsmechanisms_6.png)

Map

![Image](/assets/img/2024-06-22-AdeepcutintoRxSwiftExploreitsmechanisms_7.png)

<div class="content-ad"></div>


Filter

![Filter Image](/assets/img/2024-06-22-AdeepcutintoRxSwiftExploreitsmechanisms_8.png)

Take

![Take Image](/assets/img/2024-06-22-AdeepcutintoRxSwiftExploreitsmechanisms_9.png)


<div class="content-ad"></div>

이상한가요?

이 네 가지 옵저버블의 run 메서드에 주목해 봅시다. 각 유형의 옵저버블에는 해당하는 Sink 클래스가 있다는 것을 알 수 있어요: ObservableSequence에는 ObservableSequenceSink, Map에는 MapSink, Filter에는 FilterSink, TakeCount에는 TakeCountSink가 있어요. 그리고 RxSwift의 다른 옵저버블에도 마찬가지예요.

게다가, 옵저버블 자체는 어떤 로직도 실행하지 않음을 알 수 있어요; 그저 옵저버블 로직을 위한 디자인 또는 템플릿으로 동작할 뿐이에요. 정작 모든 일을 하는 사람은 Sink라는 거죠.

이를 시각화해 보면, 옵저버블이 기계의 청사진이라고 상상해 보세요. 반면 Sink 인스턴스는 그 청사진을 기반으로 구축된 실제 기계에요. 사실 Sink는 RxSwift에서 일어나는 모든 마법 같은 일들이 일어나는 곳이에요. 그것은 우리 관찰자에 대한 참조를 유지하고, 옵저버블 로직을 실행하며, 결과 이벤트를 관찰자에게 전달해요. RxSwift에서 특정 유형의 옵저버블이 어떻게 작동하는지, 그것이 처리되는 데이터를 어떻게 살펴야 하는지를 정말로 이해하려면 해당 옵저버블의 Sink를 살펴보면 돼요.

<div class="content-ad"></div>

또 다른 중요한 점은 run 메서드에서 Observable을 구독할 때만 Sink 인스턴스가 생성된다는 것입니다. 이것이 Observable의 논리가 구독되어야만 실행되고, Observable을 구독할 때마다 논리가 다시 실행되는 이유를 설명합니다. 이는 새로운 Sink 인스턴스가 생성되기 때문에 발생하는 것입니다.

Producer의 run 메서드가 항상 반환하는 하나 더의 구성 요소는 subscription입니다. 그렇다면 subscription이란 무엇일까요?

이 질문에 답변하기 전에, RxSwift가 조합 가능하다는 점을 기억해 두세요. 즉, 여러 개의 각기 다른 유형의 Observable을 한 개의 Observable로 결합할 수 있다는 것이죠, 마치 다수의 객차로 구성된 기차와 유사합니다.

![image](/assets/img/2024-06-22-AdeepcutintoRxSwiftExploreitsmechanisms_10.png)

<div class="content-ad"></div>

두 종류의 observable이 있습니다: Generation observables과 Transformation observables.

- Generation observables는 자체적으로 이벤트를 생성하는 observables입니다. 예를 들어, create, of, from... 연산자에서 반환된 observables입니다. Generation observable은 항상 모든 파워를 생성하는 기차 머리와 같은 역할을 합니다. 어떤 이벤트도 Generation observable에서 시작됩니다. 위의 ObservableSequence 구현을 살펴보면, 구독은 sink 인스턴스를 실행한 결과입니다. 구독은 Sink 실행을 관리하기 위해 할당된 리소스를 관리하는 Disposable 프로토콜을 구현한 형식입니다. Sink가 생성을 완료하면, 구독은 Sink가 할당한 모든 리소스를 해제하기 위해 삭제됩니다.
- Transformation observables는 원본으로부터 받은 이벤트를 observable로 변환하는 observables입니다. 예를 들어, map, filter, take... 연산자에서 반환된 observables입니다. Map, Filter, TakeCount 구현을 살펴봅시다. 각 클래스는 항상 기본적으로 이전 체인 내의 observable에 대한 참조인 원본 observable을 갖습니다. 구독은 sink를 원본 observable에 구독하는 결과입니다. 그리고 원본 observable이 사실상 다른 Producer인 경우, 구독도 SinkDisposer입니다. 이것은 RxSwift가 여러 연산자를 연결하는 방법입니다.

모든 것을 연결하면, 다음과 같이 예시를 시각화할 수 있습니다:

![이미지](/assets/img/2024-06-22-AdeepcutintoRxSwiftExploreitsmechanisms_11.png)

<div class="content-ad"></div>

다음은 우리 예시에서 발생한 일입니다:

1. 생성 단계:

<div class="content-ad"></div>

실제로 나오는 결과 observable은 TakeCount입니다. TakeCount는 Filter가 있는 observable 소스를 가지고 있습니다. Filter에는 observable 소스인 Map이 있습니다. Map은 observable 소스인 ObservableSequence를 가지고 있습니다. ObservableSequence는 자체적으로 이벤트를 생성할 수 있는 생성 observable입니다.

2. 구독 단계:

결과 observable에 구독하면 실제로 TakeCount observable에 구독하게 됩니다. TakeCountSink를 만들어 TakeCount의 로직을 실행하고 Filter observable을 해당 Sink에 구독하여 구독을 만듭니다. Sink 및 구독은 TakeCount에 구독을 할 때 반환된 SinkDisposer에 의해 관리됩니다. 예시에서 해당 구독은 바로 이 SinkDisposer입니다.

Filter, Map 및 ObservableSequence에 대해서도 동일한 동작이 발생합니다. 각 단계는 자체 Sink 및 SinkDisposer를 만들며, 각 SinkDisposer의 구독은 이전 단계의 SinkDisposer입니다. ObservableSequence는 생성 observable이므로 ObservableSequenceSink 자체를 실행하기 위해 할당된 자원을 관리하는 인스턴스로 구독하는 것이며, 그 외의 단계에서 발생하는 것과는 다릅니다.

<div class="content-ad"></div>

### 3. Running phase:
ObservableSequenceSink가 이벤트를 방출하기 시작하고, 그 observer는 MapSink입니다. MapSink는 ObservableSequenceSink에서 이벤트를 가져와서 작업을 처리하고(2로 곱함), 그 결과를 그 observer인 FilterSink에게 방출하고, 이런 식으로 계속됩니다... 마지막 싱크는 TakeCountSink이며, 이는 예시에서 값이 프린트되는 클로저에 이벤트를 방출합니다.

### 4. Dispose phase:
작업을 마치면 구독을 해제합니다. 실제로 TakeCount의 SinkDisposer입니다. 그러면 이 SinkDisposer가 TakeCountSink와 그 구독을 해제하고, 이는 Filter의 SinkDisposer의 구독인 것입니다. ObservableSequence의 마지막 SinkDisposer에 이르기까지 해제 프로세스가 계속됩니다.

<div class="content-ad"></div>

# 결론

이것이 RxSwift의 전체 그림입니다. 이 게시물이 RxSwift 내부에서 무엇이 발생했는지에 대해 더 깊은 이해를 가지는 데 도움이 되기를 바랍니다. 다른 주제에 관심이 있다면, 여기에서 유용한 기술 정보를 확인해보세요!

토안 누옌, 시니어 모바일 엔지니어