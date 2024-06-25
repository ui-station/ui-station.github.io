---
title: "Java 제네릭을 이용한 다형 함수"
description: ""
coverImage: "/assets/img/2024-06-19-PolymorphicfunctionsusingJavagenerics_0.png"
date: 2024-06-19 22:03
ogImage:
  url: /assets/img/2024-06-19-PolymorphicfunctionsusingJavagenerics_0.png
tag: Tech
originalTitle: "Polymorphic functions using Java generics"
link: "https://medium.com/gitconnected/polymorphic-functions-using-java-generics-76e8074bc9c6"
---

자바 함수형 프로그래밍 구축 요소

![이미지](/assets/img/2024-06-19-PolymorphicfunctionsusingJavagenerics_0.png)

이 기사는 제네릭 유형과 함수형 인터페이스를 사용하여 자바에서 다형성 함수를 작성하는 주제를 탐구합니다. 이 기본 사항은 종종 주니어 엔지니어에 의해 고려되지 않는다. 대신 Java 8+ Stream API와 같은 더 심화된 주제로 즉시 진입하는 것을 선호합니다. 그러나 이것들은 자바에서 함수형 프로그래밍 개념을 배우고 적용하며 더 깨끗하고 재사용 가능하며 선언적인 코드를 작성하는 데 필수적인 구축 요소입니다.

기사 말미에서, 저는 모든 것을 매우 유용하고 실용적인 예제로 하나로 조합할 것입니다. 이 기사가 당신에게 매우 유용하고 실질적인 예제가 될 것이라고 기대합니다. 이 기사가 당신에게 단단한 기초를 제공하고 댓글 섹션이나 스스로 더 탐구하도록 격려할 것입니다.

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

그러나 계속 읽기 전에 주의할 점은, 이 문서가 제네릭 및 함수형 인터페이스 개념 및 구문을 배우는 초보자용 완전한 자습서로 제공되는 것이 아니라는 것입니다. 이미 Java 공식 문서 및 다양한 자습서 웹사이트에 많은 이러한 자습서가 존재합니다. 우리는 기본 사항을 빠르게 살펴볼 것이며, 이 글의 주요 목적은 왜 그리고 어떻게 실제로 유용한지 깊게 이해하려는 것입니다.

## Java 제네릭의 목적

그렇다면 Java 제네릭의 목적은 무엇이며, 언제 유용할까요?

가장 유혹적인 말은 "공통 부모를 공유하는 클래스가 있는 재사용 가능한 코드를 작성하기 위해서"라고 말하는 것입니다. — 또는 이와 같은 말입니다. 경우에 따라, 그리고 어느 정도로 그렇습니다. 하지만 이것이 정확히 무엇을 의미하는지는 무엇일까요? 아래 코드 조각을 살펴봅시다. 제네릭 유형을 사용하는 좋은 이유가 될까요?

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

![image](/assets/img/2024-06-19-PolymorphicfunctionsusingJavagenerics_1.png)

답은: no입니다. 이 코드는 다음 코드 스니펫에서 강조하는 것처럼 Dog 추상화 자체로 쉽게 바꿀 수 있습니다. 이러한 경우에는 제네릭을 사용할 필요가 없습니다.

![image](/assets/img/2024-06-19-PolymorphicfunctionsusingJavagenerics_2.png)

그렇다면, Java 제네릭이 언제 진정으로 유용한가요? 먼저, 클래스 제네릭 유형과 메서드 제네릭 유형 두 가지 유형의 일반 유형이 있습니다.

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

클래스 제네릭 타입은 특히 여러 클래스 API 메소드들 사이에서 타입 일관성을 유지하는 데 유용합니다. 첫 번째 예시로는 List 인터페이스가 add나 get과 같은 여러 메소드에서 동일한 클래스 제네릭 타입 E를 시그니처에 재사용하는 것이 있습니다.

메소드 제네릭 타입은 Java 언어의 설계 제한을 극복하는 데 도움을 줍니다. 클래스 제네릭 타입을 사용하는 클래스는 무변(invariant)이기 때문에(공변(covariant)이 아님) 메소드 제네릭 타입은 여러 메소드 매개변수 및/또는 반환 타입에 걸쳐 특정 타입을 강제하는 데 매우 유용합니다.

실무에서 무변이 무엇을 의미하는지 예를 들어 설명하자면, List`String`은 String이 Object의 하위 타입이지만 List`Object`의 하위 타입이 아니라는 것입니다. 리스트가 무변이라는 것에 주목할 만한 점은 Java에서 배열은 공변적(covariant)이기 때문에(String[]는 Object[]를 확장합니다) 이 특정 목적을 위해 제네릭 메소드 타입을 사용할 필요가 없다는 것입니다.

다음 코드 스니펫에서 Java의 공변성/무변성 관련하여 어떤 것이 허용되고, 어떤 것이 안 되는지 살펴볼 수 있습니다.

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

<img src="/assets/img/2024-06-19-PolymorphicfunctionsusingJavagenerics_3.png" />

그래서 요약하면, Java 제네릭의 주요 목적은 개발자가 여러 가지 유형에서 재사용 가능한 코드를 작성하고 일관된 계약(클래스 수준 또는 메소드 수준)을 제공하는 동안 유연하게 작동하도록 하는 것입니다.

## 제한된 제네릭 유형

제네릭 유형은 상한, 하한 또는 명시적 바운드가 없을 수 있습니다(Object가 상한인 경우).

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

한계가 없는 제네릭 유형을 탐색해 봅시다. 이 유형들은 어떤 객체 유형이든 가능하게 합니다. 주로 여러 매개변수 및/또는 반환 유형에 동일한 유형을 적용하는 데 사용됩니다. 아래 코드 예제에서 볼 수 있듯이 다른 간단한 동작도 정의할 수 있습니다.

![이미지](/assets/img/2024-06-19-PolymorphicfunctionsusingJavagenerics_4.png)

아마도 가장 흔한 제네릭 사용 사례 중 하나는 상위 경계 제네릭 유형을 사용하는 것입니다. 이것은 소개 섹션에서 언급한 "일반 부모" 상속 기반 예제입니다. 아래 예제에서 강조되어 있습니다.

![이미지](/assets/img/2024-06-19-PolymorphicfunctionsusingJavagenerics_5.png)

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

한 번 멈춰서 일반적인 유형이 우리에게 무엇을 하는지 두 가지 관점에서 이해해 봅시다: 호출자의 관점과 구현의 관점에서.

이 API를 사용하는 사용자로서, List`Dog`나 Dog 하위 유형(예: List`GermanSheppard`)을 사용하여 findDogByNameGeneric(...)을 호출할 수 있습니다.

메서드 구현 관점에서는 경계가 있는 일반 유형 `T extends Dog` 가 우리에게 강력한 보증을 제공하는데, 이는 우리가 받는 모든 목록이 요소가 부모 유형 Dog를 공유할 것이라는 것입니다. 이는 목록이 개발자에게 메서드 내에서 사용할 수 있는 요소를 생성할 수 있게 해줍니다(즉, 여기서는 Dog 및 특히 부모 인터페이스에서 정의된 getName()을 호출하는 경우).

상한이 있는 제네릭 유형 목록으로는 요소를 추가할 수 없습니다. 이것을 생각해 보면 이해할 수 있으며, 그 이유를 찾아보는 것은 시간을 들여서 한 번 곰곰히 생각할 가치가 있습니다.

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

[1초 후]

상한이 정의된 제네릭 타입 목록에 요소를 추가할 수 없는 이유는 메서드 구현 내에서 호출된 목록의 실제 타입을 알 수 없기 때문입니다. 아래 예시를 살펴봅시다.

![image](/assets/img/2024-06-19-PolymorphicfunctionsusingJavagenerics_6.png)

만약 상한이 정의된 타입에 추가할 수 있다면, List`Double` 입력 매개변수를 사용해 addSomething을 이론적으로 호출할 수 있고, 메서드 내에서 Double 목록에 Integer(또한 Number를 확장한)을 추가할 수 있습니다. 컴파일 시에 메서드가 어떤 타입으로 호출될지 알 수 없기 때문에, 언어가 목록의 타입 무결성을 잠재적으로 위반하지 못하도록 우리를 방해하는 것은 바로 이것입니다.

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

만약 우리가 일반 형식 소비자에 요소를 전달하려면(컬렉션이어야만 하는 것은 아님), 상위 바운드를 만들기 위해 super 키워드를 사용해야 합니다. 아래의 또 다른 예제를 살펴보고, 이전처럼 API 관점과 구현 관점에서 super가 우리에게 무엇을 보장해주는지 생각해봅시다.

![image](/assets/img/2024-06-19-PolymorphicfunctionsusingJavagenerics_7.png)

API 관점에서는 populateGermanSheppards 메서드의 두 번째 매개변수는 List`GermanSheppard` 또는 그 상위 형식인 List`Dog`일 수 있습니다. 이게 구현 관점에서 왜 도움이 되는지 생각해 보세요.

[또 다른 시간이 흘렀습니다]

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

populateGermanSheppards의 구현 내에서 두 번째 입력 매개변수가 List`GermanSheppard` 또는 List`Dog` 중 하나인 것을 알 수 있습니다. 따라서 해당 목록에 GermanSheppard를 추가해도 목록의 유형 무결성을 위반하지 않습니다. 왜냐하면 입력 매개변수 목록이 List`GermanSheppard`인 경우 GermanSheppard를 추가하는 것이 단순히 유효하며, List`Dog`인 경우 GermanSheppard는 Dog이므로 유형이 여전히 유효합니다. 더불어 부모 클래스에 대해서도 동일한 원리가 적용됩니다.

매번 바운드된 제네릭 유형을 생각할 필요가 없도록, 간단히 PECS라는 머릿글자를 기억하면 됩니다. PECS는 Producer-Extends Consumer-Super의 약자로, 제네릭 메소드를 사용하여 상위 바운드 유형을 가진 요소를 생성/생산하는 요소들을 생산자로 정의합니다.

소비자는 요소를 받아들일 수 있는 능력과 관련이 있습니다. 예를 들어 List의 add 메소드는 요소를 받아들일 수 있기 때문에 소비자이며, 목록의 제네릭 유형을 소비자-슈퍼로 제한할 수 있습니다.

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

지금까지 우리가 논의한 것은 단순한 기본 구성 요소로, Java에서 일반적인 타입이 어떻게 실제로 작동하는지의 기본 설명입니다. 더 많은 내용이 있으며, 와일드카드 및 타입 이레이저에 대해 약간 읽어보고, 컬렉션을 중심으로하지 않는 더 많은 예시(예: Optional)를 살펴보면 일반 타입에 대해 더 익숙해질 수 있습니다.

그러나 순수 일반 타입을 강조하는 좋은 예제를 제공하기가 어려운데, 적어도 내게는 이러한 타입의 가장 크고 일반적으로 사용되는 장점은 함수형 인터페이스와 결합될 때입니다. 이에 대해 더 자세히 알아보겠습니다.

# 함수형 인터페이스 기본 사항

함수형 인터페이스는 재사용 가능한 코드를 작성할 때 우리가 가진 도구 중 하나입니다. 또한 좋은 개발자 경험을 제공하는 유연한 API를 구축하고 싶다면 바운드된 제네릭 타입을 사용하여 개선할 수 있습니다.

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

이론적으로 함수형 인터페이스는 오직 하나의 비디폴트 메소드만 가진 인터페이스를 말합니다(즉, 구현이 없는 메소드가 하나뿐인 경우). 이러한 인터페이스들은 Java 유형을 제공하고 람다 표현식이나 메소드 참조 같은 호출 가능한 코드 조각을 참조하는 데 사용됩니다. 이러한 참조를 다른 객체 유형처럼 사용할 수 있게 해줍니다. 함수를 참조할 수 있고, 호출하지 않고 다른 함수에 전달할 수 있습니다.

Java 8부터 Java JDK 내에 여러 유형의 함수형 인터페이스가 있습니다. 가장 기본적인 것은 Function`T, R`입니다. 이는 입력 T 집합을 가져와 R 집합의 결과를 출력하는 수학 함수와 동등합니다. 다만 여기서는 유형 인스턴스를 다루고 있을 뿐입니다. 함수형 프로그래밍에서는 이를 펑터라고도 부릅니다.

함수 외에도 다음과 같이 계속해서 사용되는 함수형 인터페이스 유형들이 있습니다(그들의 Bi[…]variant인 BiFunction`T, U, R`와 함께), 이미 알고 계신지 모르겠지만 익숙해지기를 권장드립니다:

- Predicate`T`: Function`T, Boolean`와 같은 역할을 합니다.
- Consumer`T`: T형 인자를 가지고 void(아무것도)를 반환하는 함수를 나타냅니다.
- Supplier`T`: 어떠한 인자도 받지 않고 T 유형 원소를 반환하는 함수를 나타냅니다.

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

다음은 많은 작업을 수행할 수 있는 메소드의 예시를 살펴봅니다. 원리적으로는 리스트의 요소를 반복하고 특정 조건에서 특정 작업을 수행합니다. 메소드 자체에서는 구체적인 내용은 정의되어 있지 않으며, 이는 꽤 추상적입니다.

![이미지](/assets/img/2024-06-19-PolymorphicfunctionsusingJavagenerics_8.png)

추가로, 제 개인적인 의견으로는 함수형 인터페이스를 사용하여 지나치게 추상화된 일반적인 함수를 작성하는 것도 코드 재사용성을 위해 코드 가독성을 너무 희생하는 경우 반대 패턴으로 간주해야 한다고 생각합니다. 둘 사이에는 균형이 필요하며, 재사용 가능한 일반적인 메소드에서 구체적이고 정당화할 수 있는 문제를 해결하도록 노력해야 합니다.

조건부 요소 소비자(conditionalElementsConsumer) 메소드 시그니처가 어떻게 보일지 생각해 보세요. 일반적인 타입을 완화하고 한정적인(혹은 제한된) 일반적인 타입을 사용한다면 어떨까요?

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

[a second of thinking passes]

PECS 약자에 대해 간단히 생각해 보면, 느슨한 서명은 다음 예시와 같은 형태여야 합니다.

![이미지](/assets/img/2024-06-19-PolymorphicfunctionsusingJavagenerics_9.png)

새 예시에서 이전의 제한된 제네릭 유형이 새로운 케이스를 지원하기에 충분히 유연하지 못했음을 알 수 있습니다. 이 간단한 기능 인터페이스를 사용해 개념을 설명하고 있지만, 실제 프로젝트에서는 타입이 일치하도록 바꿀 수 없는 서드파티 코드의 메서드 참조와 유사한 상황을 마주하게 될 수 있습니다. 경계를 통해 제네릭 유형을 느슨하게 처리하면 이를 해결하는 데 도움이 될 수 있습니다.

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

이 지점에서 또 다른 좋은 연습은 위의 예제를 생각하고 152번 라인에서 컴파일 되는 메소드 호출의 실제 T 유형을 파악해 보는 것입니다.

함수형 인터페이스의 기본을 더 깊이 들어가지는 않겠습니다. 다양한 다른 유형에 대해 이야기하는 많은 기사들과 람다 표현식, 메소드 참조에 대해 참조할 수 있는 함수형 인터페이스 유형도 있습니다.

그러나 이러한 기본 개념을 조합하여 실제 유용한 재사용 가능한 코드를 만들 수 있는 방법을 강조하는 데는 조금 더 나아갈 것입니다.

# 고차 함수를 통한 데코레이터

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

고차 함수는 다른 함수를 매개변수로 받거나 함수를 반환하는 함수형 프로그래밍 개념을 나타냅니다.

이를 생각하면, 실제로 호출하지 않고 일반 함수를 래핑하는 데코레이터와 유사한 디자인 패턴을 이론적으로 정의할 수 있습니다.

많이 접하게 되는 예시 중 하나는 확인된 예외를 throw하는 함수를 처리하는 것입니다. 이는 특히 Stream API 메소드 호출 내에서 문제가 될 수 있습니다. Stream API 메소드는 예외를 throw하지 않는 함수형 인터페이스를 입력 매개변수로 사용하기 때문입니다. 물론 Vavr과 같은 라이브러리를 사용할 수도 있지만, 사용 사례가 제한적이고 종속성 목록을 작게 유지하고 싶다면 직접 래퍼/핸들러를 구축하는 것이 더 나은 옵션이 될 수 있습니다. 아래 예시에서 (인기 있는) 상황을 확인해보세요.

![이미지](/assets/img/2024-06-19-PolymorphicfunctionsusingJavagenerics_10.png)

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

먼저, 위의 코드에서 발생한 컴파일 오류는 map 메서드에서 예상되는 메서드 시그니처 (즉, Function`T, R`)와 제공된 함수가 String을 가져와 List`String`을 반환하거나 IO Exception을 던질 수 있는 함수임을 고려할 때 발생합니다 (이러한 의미로는 JDK에 기본적으로 해당 함수형 인터페이스가 없음).

이러한 유형의 불일치를 다루는 비제네릭하고 간단한 방법은 발생한 예외를 별도의 메서드로 분리하여 거기서 예외를 처리하는 것입니다. 이렇게 하면 사실상 Stream::map 메서드에 대해 Function`String, List`String`` 입력 매개변수로 변환될 것이며 이는 컴파일이 될 것입니다. 그러나 제네릭 및 함수 인터페이스를 통해 코드 재사용할 수 있는 큰 기회가 있다는 것을 다음 예제에서 확인할 수 있습니다.

![Example](/assets/img/2024-06-19-PolymorphicfunctionsusingJavagenerics_11.png)

위의 코드 예제를 조금 설명해보겠습니다. 먼저, 제시된 3가지 옵션 중 어느 것을 사용하더라도 더 이상 컴파일 오류가 발생하지 않음을 관찰할 수 있습니다. 이제 map 메서드는 호출 시 예상대로 Function`Path, List`String``를 받는다고 볼 수 있는 mapSafeFunction을 수신하며, 또한 ExceptionalFunction 인터페이스를 정의할 때 코드가 완벽하게 컴파일되고 Files::readAllLines 메서드 참조가 어떤 컴파일 오류도 없이 허용됩니다. 즉, 이 문제는 예외 처리가 되지 않았던 것이 아니라 함수형 인터페이스 타입이 일치하지 않았기 때문임을 결론짓습니다.

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

수학 함수에 대해 생각해보면, 입력을 받아 출력을 제공하며 예외를 throw하지 않습니다. 마찬가지로 여기서는 예외를 throw하는 함수를 throw하지 않는 함수로 변환하려고 합니다. 그렇다면 예외가 발생했을 때 어떻게 해야 할까요?

이것은 완전히 개인적인 선택이었지만, 예외가 발생한 경우 결과가 없을 수도 있다는 사실을 나타내기 위해 생성된 함수가 Optional을 반환하도록 만들기를 원했습니다. 이것이 ExceptionalFunction`T, R`의 입력에 대한 반환 타입이 Function`T, Optional`R``로 되어 있는 이유입니다. 이전 Optional::map 작업이 flatmap 작업으로 변경되어 코드 전체에 매우 잘 맞는 점에 주목하면 좋습니다(Javadoc 여기를 참조하세요). 예외가 발생하면 Optional 체인이 빈 결과를 제공하며 코드는 전반적으로 매우 깨끗하고 순조롭게 따라갈 수 있습니다.

(부기적으로, 예외를 완전히 억제하거나 런타임 예외로 다시 던지거나 null을 반환하는 API 디자인 선택은 개발자 경험 관점에서 객관적으로 잘못된 접근법이라고 생각합니다. 주된 주장은 계약이 그냥 틀렸다는 것이며, 예기치 않은 동작이 API를 통해 추론될 수 없다는 점이며, 코드를 읽어야만 이해할 수 있다는 것입니다.)

mapSafeFunction의 구현은 Files::readAllLines 또는 예외를 throw하는 기타 함수에 적용할 수 있습니다. 이 메서드는 전체 프로젝트 전반에 걸쳐 매우 재사용 가능하며 해결하는 문제가 매우 인기가 있습니다.

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

메소드 mapSafeFunction의 구현은 특별하거나 복잡한 것은 없습니다. 우리는 예외 처리 함수를 가져와 try/catch 예외 처리 논리를 처리하고 원래 함수를 호출하는 함수를 반환합니다. 추가적으로 Consumer`Exception`은 예외 로깅이나 처리 논리를 다루기 위한 유연한 API를 제공합니다. 호출자가 slf4j를 사용하여 예외를 로깅하거나, 콘솔에 로깅하거나, 소비자 내에서 런타임 예외를 다시 던지는 것을 막는 것은 없습니다. 이 방법은 덜 번거롭고 명시적이지 않은 API에 기본값을 제공하기위해 의도적으로 오버로드되었습니다.

동일한 패턴에 대한 유사한 사용 사례는 트랜잭션 관리 일 수도 있습니다. 함수가 호출되기 전에 트랜잭션을 시작하고, 끝에서 커밋하거나 예외가 발생했을 경우 롤백하는 전반적인 개념은 동일합니다.

# 결론

함수형 프로그래밍(또는 단순히 Java Stream API)을 이해하려면 함수형 인터페이스를 이해해야 하며, 이는 다시 제네릭 유형에 의존합니다. 이러한 주제들은 서로 긴밀하게 연결되어 있으며, 개인적인 관찰에 따르면 후자는 종종 간과됩니다.

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

이 기사의 개념들은 하나씩 쉽게 이해할 수 있지만 함께 사용하면 사용할 수 있는 코드가 매우 재사용 가능하다는 것을 알 수 있습니다, 특히 예외 기능 데코레이터의 예시에서 확인할 수 있습니다. 더 많은 응용 프로그램이 있으며, 이 중 한 가지가 제 개인적인 취향입니다.

이 기사가 유익했고 이러한 개념을 실험하고 일상 프로젝트에 적용해 보는 호기심을 자극했기를 바랍니다. 이러한 기본적인 자바 개념은 자바에서 함수형 프로그래밍 패턴을 배우는 데 필요한 기본 블록으로 기능하지만, 이에 대해 더 알아보도록 하겠습니다.

향후 자바 개념 설명, 프레임워크 및 기타 기술 관련 콘텐츠에 구독하고 좋아요를 눌러주시기 바랍니다!
