---
title: "Java 추상 클래스 완벽 이해하기"
description: ""
coverImage: "/assets/img/2024-06-22-UnderstandingAbstractClassesinJava_0.png"
date: 2024-06-22 23:03
ogImage:
  url: /assets/img/2024-06-22-UnderstandingAbstractClassesinJava_0.png
tag: Tech
originalTitle: "Understanding Abstract Classes in Java"
link: "https://medium.com/@chyesith/understanding-abstract-classes-in-java-60854f3e967c"
---

![Understanding Abstract Classes in Java](/assets/img/2024-06-22-UnderstandingAbstractClassesinJava_0.png)

# 소개

대학교에서 5년 전에 자바에서 추상 클래스에 대해 처음 배웠을 때, 그것들이 혼란스러웠습니다. 처음에는 인터페이스와 같은 목적을 제공한다고 생각했습니다. 그러나 코딩 프로젝트를 통해 더 많은 경험을 쌓으면서, 추상 클래스와 인터페이스가 서로 다른 목적을 제공한다는 것을 깨달았습니다. 이 간단한 기사에서는 전형적인 Animal 클래스를 예로 들어 이러한 개념을 설명하겠습니다.

# 자바에서 추상 클래스란 무엇인가요?

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

자바에서 추상 클래스는 독립적으로 인스턴스화할 수 없으며 하위 클래스로 사용되는 클래스입니다.

추상 메서드를 포함할 수 있으며, 이는 구현이 없이 선언된 메서드를 의미합니다. 추상 클래스에는 구현된 메서드도 포함될 수 있습니다.

# 언제 추상 클래스를 사용해야 할까요?

예를 들어보겠습니다. 두 개의 클래스인 Dog와 Cat을 가정해보세요. 이 두 클래스 모두 makeSound 메서드가 필요한 경우를 상상해보세요.

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

```java
public class Cat {

    public void makeSound() {
        System.out.println("Meowww");
    }
}
// make sound method가 Cat, Dog, Elephant와 같은 각 클래스마다 있어야 하는 특정한 메서드이므로, 여기서 추상 클래스가 필요합니다.

Animal이라는 추상 클래스를 만들 수 있습니다. 클래스를 추상으로 만들기 위해서는 그냥 class 키워드 앞에 abstract 키워드를 추가하면 됩니다. 이 추상 클래스 안에는 추상 메서드를 가질 수 있습니다.

다음은 선언하는 방법입니다.
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

```java
public abstract class Animal {

   abstract void makeSound();
}
```

# 추상 클래스에 대한 주요 포인트

- abstract 키워드: 메소드를 추상으로 선언하려면 abstract 키워드를 사용합니다. 추상 메소드는 본문이 없으며 하위 클래스에서 구현됩니다.
- 객체 생성 불가: 추상 클래스의 객체를 생성할 수 없습니다. 예를 들어, Animal 객체를 생성하는 것은 의미가 없습니다. 왜냐하면 일반적인 동물의 소리를 정의할 수 없기 때문입니다. 이것이 Animal 클래스가 추상 클래스인 이유입니다. 추상 클래스는 다른 클래스에 의해 확장되도록 의도되어 있습니다.
- 서브클래스 구현: 추상 클래스 Animal의 서브클래스는 추상 메소드에 대한 구현을 제공해야 합니다. 예를 들어,

```java
public class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Bow Bow");
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

```java
public class Cat extends Animal {
  @Override
  public void makeSound() {
    System.out.println("Meowww");
  }
}
```

이 하위 클래스에서는 makeSound 메서드가 구현되어 강아지와 고양이에 대한 특정 동작을 제공합니다.

# 추상 클래스 vs. 인터페이스

추상 클래스와 인터페이스는 둘 다 추상화를 달성하는 데 사용될 수 있지만, 주요 차이점이 있습니다.

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

추상 클래스

- 추상 메서드(구현이 없는)와 구체 메서드(구현이 있는)를 모두 가질 수 있습니다.
- 서로 다른 클래스들이 일부 기본적인 내용과 기능을 공유할 때 사용됩니다.
- 예를 들어, Dog 및 Cat 클래스가 일부 공통 메서드나 필드를 공유하는 경우, 해당 공통 메서드나 필드를 포함하는 Animal 추상 클래스를 확장할 수 있습니다.
- 클래스는 한 번에 하나의 추상 클래스만 확장할 수 있습니다.

인터페이스

- 여러 클래스가 구현할 수 있는 계약 또는 메서드 세트를 정의하는 데 사용됩니다. 이러한 클래스들이 공통의 기본을 공유하지 않더라도 구현할 수 있습니다.
- 예를 들어, Bird, Plane 및 Superhero와 같은 다른 클래스들이 모두 fly() 메서드를 필요로 하는 경우, 이러한 클래스들이 Flyable 인터페이스를 구현할 수 있습니다.
- 클래스는 여러 인터페이스를 구현할 수 있습니다.

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

# 결론

추상 클래스를 언제 그리고 어떻게 사용해야 하는지 이해하면 더 유연하고 유지보수가 쉬운 코드를 작성할 수 있습니다. 추상 클래스는 직접적으로 인스턴스화할 수 없으며, 추상 메서드에 대한 구체적인 구현을 제공하는 서브클래스에 의해 확장되어야 합니다. 공통 기능이 있는 클래스의 경우 추상 클래스를 사용하고, 관련이 없는 클래스 간에 공통 동작을 정의해야 할 때는 인터페이스를 사용하세요.

다음에 만나요! 계속해서 자바 스킬을 향상시키고 발전시키세요!
