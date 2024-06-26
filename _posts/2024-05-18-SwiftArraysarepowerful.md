---
title: "Swift 배열은 강력합니다"
description: ""
coverImage: "/assets/img/2024-05-18-SwiftArraysarepowerful_0.png"
date: 2024-05-18 15:42
ogImage:
  url: /assets/img/2024-05-18-SwiftArraysarepowerful_0.png
tag: Tech
originalTitle: "Swift Arrays are powerful"
link: "https://medium.com/@paulwall_21/swift-arrays-are-powerful-1624a371ea1b"
---

<img src="/assets/img/2024-05-18-SwiftArraysarepowerful_0.png" />

프로그래밍에서 배열은 흔히 사용되는 요소입니다. 특히 Swift 배열은 사용하기 쉽고 무척 강력합니다. Swift에서 무료로 제공되는 메서드의 양은 상당히 놀라울 정도이며 종종 간과됩니다. Swift 배열에 대한 설명서를 확인해보세요. 목록이 상당히 길어요. 저는 지금까지 작업한 모든 코드베이스 중에서 이러한 메서드 중 소수만 사용된 것을 본적이 있습니다. 필요한 기능을 얻기 위해 확장을 생성하는 것에 4~5개의 메서드가 사용될 가능성이 있습니다.

이 기사에서는 예측자(predicates)의 사용법과 우리의 사용자 지정 데이터 유형으로 모든 이러한 메서드를 사용하는 데 어떻게 도움이 되는지 설명하겠습니다. 또한 코드에서 표준 메서드뿐만 아니라 다양한 메서드를 사용하는 것이 왜 유익한지 확인하실 수 있을 것입니다.

# 사용자 지정 예측자 사용하기

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

문서를 살펴보면 대부분의 메소드에는 사용자 정의 "조건자"를 사용할 수 있는 API에 대한 추가 클로저가 있음을 알 수 있습니다. 이 기능은 강력하면서도 활용도가 낮습니다. 종종, 우리는 Int나 String과 같은 기본 유형이 아니라 앱의 데이터를 나타내는 사용자 지정 데이터 유형과 작업합니다. 사용자 정의 조건자를 사용하면 사용자 지정 데이터 유형에 대해 이러한 메소드를 사용하고 사용자 정의할 수 있습니다. min 메소드 사용 예제를 살펴보겠습니다:

정수 배열을 사용할 때 Swift는 "min" 정수를 찾는 것을 스스로 처리할 수 있습니다. 그러나 우리가 만든 사용자 지정 데이터 유형을 사용한다면 어떻게 될까요? 바로 여기에 조건자가 필요합니다:

이 조건자에서 "min"이 적용될 대상을 지정할 수 있습니다. 이 경우에는 우리가 만든 사용자 지정 데이터 유형의 price 속성입니다. Swift 배열의 대부분의 메소드는 이러한 경우에 대한 조건자를 허용합니다. 정말 멋지죠!

# 더 표현력을 강조하세요

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

원하는 대로 코드를 작동시키는 것은 전투의 일부일 뿐입니다. 또 다른 중요한 개념은 코드를 가독성 있게 만드는 것입니다. 다른 사람들이 코드를 읽을 때 합리적인 시간 내에 이해할 수 있도록 코드를 읽기 쉬우게 만들어야 합니다. 몇 주 동안 코드를 보지 않은 후에도 코드를 읽게 될 수도 있습니다. 코드에 가독성을 더하는 노력과 시간을 할애하는 가치가 있습니다. Arrays로부터 많은 메소드를 사용하여 코드가 더 표현력 있고 이해하기 쉬워지도록 도와줍니다. 여기 간단한 예제가 있습니다:

이 메소드는 코드의 주석에 설명된 대로 동작합니다. 모든 단위 테스트를 통과할 것입니다. 이를 어떻게 더 나아지게 할 수 있을까요? 만약 이것을 처리하기 위해 스위프트 배열의 다른 메소드를 사용한다면 어떨까요:

`first` 메소드를 사용함으로써 가독성을 더 높일 수 있습니다. 이제 독자는 다음과 같이 읽을 것입니다: "이 Predicate와 일치하는 첫 번째 버스". 뿐만 아니라 코드에서 무슨 일이 일어나고 있는지 더 잘 설명하는 것이 좋습니다. 모두에게 큰 이점이 됩니다!

필터 메소드를 사용한 또 다른 일반적인 예제를 살펴봅시다.

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

다시, 이 코드는 일이 잘 됩니다. 필터 메서드는 유통 기한이 지난 모든 식품 항목을 반환합니다. 이 방법은 작동하지만 좀 더 설명적인 방법을 살펴봅시다:

여기서는 removeAll 메서드를 사용합니다. 이렇게 읽을 수 있습니다: "오늘 이전에 유통 기한이 지난 모든 식품 항목을 제거합니다". 이 코드는 훨씬 가독성이 높고 간단합니다.

# 개요

Swift 배열에는 배열에서 필요한 다양한 작업을 수행하는 데 도움이 되는 다양한 메서드가 있습니다. 이는 이제 코드에 대해 더 정확하고 설명적으로 작성할 수 있기 때문에 훌륭합니다. 사용자 정의 데이터 유형에서도 프리디케이트를 사용하여 사용자 정의 방식으로 작동할 수 있습니다. 이러한 메서드를 알아보고 더 나은 코드를 작성해보세요!
