---
title: "자바 프로그래밍 Java 21의 주요 기능을 깊이 탐구해 보기"
description: ""
coverImage: "/assets/img/2024-06-19-JavaprogrammingAdeepdiveintoJava21skeyfeatures_0.png"
date: 2024-06-19 21:50
ogImage:
  url: /assets/img/2024-06-19-JavaprogrammingAdeepdiveintoJava21skeyfeatures_0.png
tag: Tech
originalTitle: "Java programming: A deep dive into Java 21’s key features"
link: "https://medium.com/capital-one-tech/java-programming-a-deep-dive-into-java-21s-key-features-8776f75bc6b8"
---

## 자바의 계속되는 중요성: 언어 진화에 대한 깊은 이해

![image](/assets/img/2024-06-19-JavaprogrammingAdeepdiveintoJava21skeyfeatures_0.png)

나는 예전에는 자바의 팬이 아니었지만, 최근 몇 년 동안 더 많이 귀하와 자바 언어와 생태계를 귀하했습니다. 특히 새 개인 프로젝트에 자바 21을 사용하기로 결정한 후 더욱 그랬습니다. JVM의 세계로 소개되는 것은 스칼라로 시작했는데, 이는 객체 지향과 함수형 프로그래밍 개념을 조화롭게 결합한 간결한 언어로서 타입 안전성에 중점을 둔 것이 특징입니다.

그렇다면, 왜 스칼라를 사용하지 않았을까요? 자바를 선택한 이유 중 일부는 언어에 대한 새로운 흥미로운 개선 사항이 있었기 때문이며, 자바의 현재 상태인 생태계, 프레임워크 및 라이브러리를 좀 더 탐색하고 싶었기도 했습니다. JVM 플랫폼을 대상으로 한 다른 최신 언어들은 많지만, 그 중에서도 스칼라와 코틀린이 가장 많은 관심을 받은 것은 자바와 마찬가지로 강력하고 정적으로 타이핑된 프로그래밍 언어입니다.

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

## 자바 대 스칼라 대 코틀린 비교

스칼라와 코틀린 중에서는 코틀린이 자바 경험이 있는 개발자들에게 더 익숙한 문법을 갖고 있으며, 자바와 완전히 상호 운용 가능하면서 자바의 일부 문제점을 해결하기 위해 고안된 더 현대적이고 간결한 언어로 주목받고 있습니다. 현재는 안드로이드 개발 공간에서 코틀린이 더 인기가 있습니다.

한편, 스칼라는 데이터 엔지니어링 (아파치 스파크 등) 및 백엔드 응용 프로그램 개발에서 주로 인기를 누리고 있습니다. 스칼라는 덜 번잡한 문법을 갖고 있으며, 어떤 사람들은 특히 Scala3에 대해 파이썬의 스트레로이드 버전으로 비교할 수 있습니다. 더 견고한 타입 시스템을 갖추고 있어 컴파일 시간에 더 많은 오류를 제거하는 데 도움이 되는 특징이 있습니다.

## 개발자들은 자바를 높게 평가합니다

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

스칼라, 코틀린 및 자바의 채택 및 인기에 대해 알아보겠습니다. 스택 오버플로 조사 및 TIOBE 그리고 Redmonk 언어 인기 지수를 살펴볼 예정입니다.

다음은 스택 오버플로 결과를 기반으로 한 인기 언어 차트입니다. 여기서는 세 언어만을 선택하여 살펴보겠습니다.

![이미지](/assets/img/2024-06-19-JavaprogrammingAdeepdiveintoJava21skeyfeatures_1.png)

## Redmonk 언어 지수

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

2023년 1월 현재 Redmonk 지수를 확인해보세요. 이 지수는 Github와 Stack Overflow 데이터를 기반으로 합니다. Java가 상위에 랭크되어 있고 Scala와 Kotlin도 비교적 높은 순위를 차지하고 있네요.

![이미지](/assets/img/2024-06-19-JavaprogrammingAdeepdiveintoJava21skeyfeatures_2.png)

## TIOBE 프로그래밍 지수

이제 TIOBE 지수를 살펴보겠습니다. 이 지수는 25개 다른 검색 엔진에서의 검색어를 고려합니다. Java 또한 상위 언어 중 하나로 랭크되어 있습니다. Scala와 Kotlin은 상위 10위 안에 들지 못해서 차트에 표시되지 않았지만, 2023년 기준 Scala는 15위, Kotlin은 36위에 랭크되어 있습니다.

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

![Java Programming](/assets/img/2024-06-19-JavaprogrammingAdeepdiveintoJava21skeyfeatures_3.png)

Java는 여전히 Kotlin 및 Scala와의 강력한 경쟁 속에서 매우 인기 있는 것을 볼 수 있습니다. 몇 년 동안 약간의 감소가 있었지만, 다른 프로그래밍 언어 및 생태계 (예: Python, Go, Rust)에 의해 조각이 빼앗겼습니다.

# Java 탐험: 역호환성에 대한 헌신

Java가 최근 몇 년간 무엇을 해 왔는지 및 강한 인기에 기여하는 다른 요소들을 살펴보겠습니다.

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

일부 인기는 Java의 DNA와 관련이 있습니다. Java의 DNA에는 강력한 하위 호환성을 유지하기로 한 의무가 포함되어 있습니다. 최근 몇 년간 투입적인 하위 호환성 파괴가 몇 차례 있었지만 이는 언어와 도구의 개선을 지원하기 위한 것입니다. 그러나 이러한 변경에 대해 평소보다 많은 고려와 타당한 이유가 있습니다. 이것이 Java가 기업 환경에서 인기를 얻는 큰 이유 중 하나입니다. JVM 플랫폼의 안정성이 더 많은 엔지니어가 생산적이며 비즈니스 문제 해결과 코드 출시에 집중할 수 있도록 해줍니다.

## 고려할 Java 관련 요소

Java 9부터 OpenJDK는 "준비되었을 때 출시"에서 6개월 주기로 변경되었습니다 - 3월과 9월에 각각 출시됩니다. 이 목표는 새로운 버전의 제공이 일부 향상이 준비되지 않아서 발생하는 지연을 완화하기 위한 것이었습니다. 이 작업을 수행하기 위해 미리보기 기능이라는 개념이 도입되었습니다. 특정 기능이 완전히 기능하나 여전히 파괴적인 변경의 영향을 받을 때, 해당 기능은 다음 버전에서 미리보기로 도입됩니다.

이를 통해 개발자 커뮤니티가 피드백을 제공하고 다음 정기 릴리스에 최종화될 구현을 형성하는 데 도움을 줄 수 있습니다. 몇 년에 한 번씩 Oracle은 LTS (장기 지원 릴리스) 릴리스로 태깅된 다음 다른 JDK 공급 업체도 따를 것입니다. 예를 들어, Amazon Web Services는 Corretto JDK라는 자체 JDK 빌드를 유지하며 이에 대해 AWS가 장기적인 지원을 제공할 것입니다.

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

<img src="/assets/img/2024-06-19-JavaprogrammingAdeepdiveintoJava21skeyfeatures_4.png" />

Java의 또 다른 핵심인 마지막 이동자 장점에 대해 이야기해봅시다. 스칼라와 같은 다른 JVM 언어들은 상당히 빠른 속도로 진화합니다. 저는 여전히 스칼라를 사용하며 즐기고 있지만 그 역사적인 호환성 및 도구 이야기는 많이 부족한 것 같습니다 (하지만 계속 발전 중입니다!). 한편, 스칼라는 언어와 컴파일러 디자인을 새로운 수준으로 밀어올리는 최신 기술을 제공하고 있습니다.

그러나 Java는 장기 게임을 펼치며, 산업이 어떻게 발전하는지 관찰하고, 다른 언어들이 무엇을 하고 있는지 평가하며, 잘 작동하는 부분을 골라내고 가장 유의미한 곳에서 언어를 개선합니다. Java 8 이후에 적용된 몇 가지 언어 개선 사항을 간략히 살펴보겠습니다.

# Java 10의 'var' 키워드가 언어의 불필요한 길이에 미치는 영향

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

다른 최신 언어에 이미 숙달된 개발자들이 Java를 배우고 있는 경우, 가장 싫어하는 점을 물어보면, 말이 많다는 것이 상위권에 오릅니다. Java 10이 출시되기 전까지는 할당의 왼쪽에 변수 유형을 명시적으로 선언해야 했습니다. Java 10에서는 실제 유형 대신 사용할 수 있는 새로운 특별한 "var" 키워드가 도입되었습니다. 컴파일 단계에서 Java 컴파일러가 할당의 오른쪽 표현식에서 추론된 실제 유형을 삽입할 것입니다.

```js
//Java 8
HashMap map = new HashMap();

DatabaseEngineColumnCacheImpl cache = new DatabaseEngineColumnCacheImpl();

Optional accessRole = user.getUserAccessRole();

//Java 10, 컴파일러에게 더 이상 실제 유형이 무엇인지 설득할 필요 없음
var map = new HashMap();

var cache = new DatabaseEngineColumnCacheImpl();

var accessRole = user.getUserAccessRole();
```

위는 몇 가지 중요치 않은 예제입니다. 그러나 이것은 코드를 읽을 때 말을 줄일 수 있습니다. 그러나 이것은 산업에서 새로운 것이 아닙니다. 다른 최신 정적 형식 프로그래밍 언어의 개발자들은 오랫동안 형식 추론을 즐겼습니다.

예를 들어, Scala는 2004년에 처음 만들어진 이후부터 더 진보된 형식 추론을 가지고 있습니다. 그럼에도 불구하고, 이것은 Java에서 매우 환영받는 기능입니다. 형식 추론은 로컬 변수 선언에만 제한되는데, 즉 메소드 본문에서, 실제로 그것이 가장 중요한 곳입니다.

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

## instanceof와 패턴 매칭으로 타입 체크 과제 극복하기

instanceof는 주어진 객체가 특정 타입인지 확인하기 위해 사용되는 언어 키워드입니다. 예를 들어, Object 타입의 객체가 주어졌을 때, 이 객체가 무엇인지 런타임에 실제 타입을 확인하여 해당 객체 타입에 특정한 작업을 실행해야 할 수 있습니다.

다소 극단적으로 보일 수 있는 예제가 있습니다. Shape 인터페이스와 특정 모양을 나타내는 여러 클래스가 있다고 가정해 봅시다. 코드의 어딘가에서 모양의 둘레에 관한 정보를 얻고 싶을 때, 각 하위 클래스가 구현해야 하는 둘레 계산 메서드가 인터페이스에 명시되어 있지 않고 이 코드를 리팩토링할 수 없다고 가정해 봅시다.

이 경우 해결책은 한 가지뿐입니다: 사각형 또는 원인지 확인하고 그에 따라 둘레를 계산하는 유틸리티 메서드를 구현하는 것입니다.

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
인터페이스 모양 {}

public class Rectangle implements Shape {
    final double length;
    final double width;
    public Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }
}

public class Circle implements Shape {
    final double radius;
    public Circle(double radius) {
        this.radius = radius;
    }
}

public static double getPerimeter(Shape shape) {
    if (shape instanceof Rectangle) {
        Rectangle r = (Rectangle) shape;
        return 2 * r.length + 2 * r.width;
    } else if (shape instanceof Circle) {
        Circle c = (Circle) shape;
        return 2 * c.radius * Math.PI;
    } else {
        throw new RuntimeException("Unknown shape");
    }
}
```

getPerimeter 메서드 구현을 살펴보면, 이미 타입을 확인했음에도 불구하고 여전히 다운캐스팅하고 연산을 수행하기 전에 새 변수를 선언해야 합니다. 이는 컴파일러가 여전히 shape를 Shape의 인스턴스로 보기 때문입니다.

instanceof의 패턴 매칭을 사용하면 if-else 블록의 범위 내에서 확인한 타입의 변수를 선언할 수 있습니다. Java 14에서 동일한 if-else 블록은 다음과 같이 보일 것입니다.

```js
  public static double getPerimeter(Shape shape) {
    if (shape instanceof Rectangle r) {
        return 2 * r.length + 2 * r.width;
    } else if (shape instanceof Circle c) {
        return 2 * c.radius * Math.PI;
    } else {
        throw new RuntimeException("Unknown shape");
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

이 컴파일러의 좋은 개선이에요. 점점 똑똑해지고 더 나아졌어요. instanceof를 위한 패턴 매칭은 자바의 나중 버전에서 계속 확장되었던 큰 노력이었고, 레코드 클래스에 대한 패턴 매칭도 포함되었는데, 다음으로 다루고 싶은 큰 기능입니다.

# 장황함에서 간결함으로, Java의 레코드가 데이터 클래스를 재정의해요

이 부분은 꽤 중요해요, 적어도 제게는요. Scala가 데이터 클래스를 사용해서 모델링하는 것이 얼마나 쉬운지 정말 좋아해요. Java는 도메인 모델링에 능숙하다는 것으로 유명하지만, 레코드가 도입되기 전에 데이터 컨테이너나 POJO를 정의하는 과정이 매우 장황했었고, 이에 따라 반복적인 코드를 직접 작성하기보다는 코드 생성을 수행하는 다양한 라이브러리가 생겼죠 (예: 롬복).

## 다음 예시를 살펴보죠.

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
public class Person {
    public final String id;
    public final String name;
    public final Integer age;

    public Person(String id, String name, Integer age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
}

var p1 = new Person("a1b", "Frank", 30)
var p2 = new Person("a1b", "Frank", 30)

p1.equals(p2) // false, oops?
```

우선, 다른 고수준 프로그래밍 언어와 비교하면 이것은 이미 간단한 데이터 클래스를 정의한 매우 억양있는 문법이라고 볼 수 있습니다. 더구나, 이와 같은 방식으로 데이터를 모델링하고 싶을 때는 객체를 내용에 기반하여 비교할 수 있으면 좋겠지만, 이 예시에서는 (Java에 처음 접하는 경우) p1과 p2가 동일하다고 가정할 수 있겠지만 실제로는 그렇지 않습니다.

이것은 Java에서 객체가 메모리의 참조이기 때문에 명시적으로 컴파일러에게 객체를 어떻게 동일한지 알려주지 않으면, equals()의 기본 전략은 메모리 주소를 비교하는 것입니다. 이것이 정확히 == 연산자가 하는 일입니다. 그렇다면, 우리의 Person 객체를 동일한 유형의 다른 인스턴스와 비교할 수 있게 하려면 어떻게 해야 할까요?

```java
public class Person {
    public final String id;
    public final String name;
    public final Integer age;

    public Person(String id, String name, Integer age) {
      this.id = id;
      this.name = name;
      this.age = age;
    }

    @Override
    public boolean equals(Object o) {
      if (o == this) return true;
      if (o == null || !(o instanceof Person)) return false;

      Person p = (Person) o;
      return Objects.equals(p.id, this.id) && Objects.equals(p.age, this.age) && Objects.equals(p.name, this.name);
    }

    @Override
    public int hashCode() {
      int hash = 7;
      hash = 31 * hash + Objects.hashCode(id);
      hash = 31 * hash + Objects.hashCode(name);
      hash = 31 * hash + age;
      return hash;
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

할 일이 상당히 많다는 것을 알게 되었네요. 객체를 비교하는 규칙을 정의하기 위해 equals 및 hashCode를 override해야 합니다. 롬복(Lombok)과 같은 라이브러리는 이러한 메서드를 생성해주므로 직접 작성하지 않아도 됩니다. 그러나 이제 Java에는 이를 고유하게 처리할 수 있는 방법이 있습니다.

Records가 등장합니다. Java 17부터(14부터 미리보기로 제공) record 키워드를 사용하여 클래스를 정의하는 새로운 방법이 있습니다. 이전 예제를 바탕으로 Records로 어떻게 개선할 수 있는지 살펴봅시다.

```js
record Person(String id, String name, Integer age) {};

var p1 = new Person("1ab", "Frank", 30);
var p2 = new Person("1ab", "Frank", 30);

p1.equals(p2); //true, 맞아요!

p1 == p2; //false, 여전히 두 개의 다른 인스턴스이기 때문에 예상대로입니다
```

## Java records의 'with' 탐색하기

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

이제 Java 컴파일러는 우리를 위해 bytecode를 즐겁게 생성해 줍니다. equals와 hashCode 메서드를 구현하기 위해 정의된 모든 매개변수를 고려합니다. 레코드 클래스는 toString 및 getter 메서드를 제공하지만 setter는 제공하지 않습니다. 레코드 클래스는 변경할 수 없도록 설계되어 있어 한 번 인스턴스가 생성되면 객체 멤버들을 읽기만 가능하고 값을 변경할 수 없습니다.

변하지 않음으로써, 동시성 프로그램의 버그를 줄이고 코드를 이해하기 쉽게 만들어줍니다. 현재 업데이트된 인스턴스를 만드는 것은 약간 번거로운 일이며, 다른 레코드로부터 모든 인수를 복사해야 한다는 것이 필요하지만 Brian Goetz 자신이 프로젝트 Amber의 일부인 "With for records"라는 새로운 기능을 통해 나중에 이 부분이 바뀔 것이라고 희망합니다. — Java 언어 설계자인 Brian Goetz의 Amber 프로젝트의 일부인 초안 중에 설명되어 있습니다. 결국 기존 레코드 객체의 한 명 이상의 멤버를 수정하여 새로운 인스턴스를 생성할 수 있는 기능이 추가될 것입니다. 예를 들어:

```js
 var p3 = p2 with { name = "Joe" };
```

이미 이를 해결하기 위해 도움이 되는 몇 가지 라이브러리가 있습니다. record-builder라는 이 github 프로젝트를 살펴볼 수 있습니다. 레코드 객체를 수정하는 제한사항을 해결하는 것 외에도 더 복잡한 데이터 클래스에 매우 유용한 빌더를 생성합니다.

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

자바 레코드에는 이 예시가 보여주는 것 이상의 기능이 있습니다. 레코드의 다른 기능은 사용자 정의 생성자 정의 능력이 있고 기본 데이터에 작용할 수 있는 일반적인 메소드를 정의할 수 있는 것입니다. 또한 레코드의 직렬화는 변경 불가능성에 의해 일반 클래스의 인스턴스의 직렬화보다 간단하고 안전합니다. 레코드는 또한 값을 분해하여 사용할 수 있으며, 다음 기능, 레코드용 패턴 매칭에 활용되는 것입니다.

# 레코드용 패턴 매칭

레코드용 패턴 매칭은 자바 21에서 최종화되었습니다. 프로젝트 Amber의 일부로 제공되는 패턴 매칭과 레코드는 새로운 프로그래밍 패러다임인 데이터 지향 프로그래밍을 가능케 합니다. DOP(Data Oriented Programming)은 문제를 변경할 수 없는 데이터 구조로 모델링하고 변경되지 않는 일반적인 목적 함수를 사용하여 계산을 수행하는 것을 강조합니다. 이미 들어보신 것 같네요. 그렇습니다, 함수형 프로그래밍에서 잘 알려진 개념입니다! DOP 패러다임을 촉진하는 기능의 도입은 OOP를 대체하려는 것이 아니라, 특정 작업을 더 우아하게 해결하는 데 도움을 주기 위한 것입니다. OOP는 대규모 코드 경계를 정의하고 지배하는 데 훌륭하지만, DOP는 데이터를 모델링하고 작업하는 데 더 유용합니다.

이게 전부가 아닌 걸로 갑시다. 패턴 매칭이란 무엇인가요? 클래스 생성자의 반대로 생각할 수 있습니다. 클래스 생성자는 데이터를 제공하여 객체를 생성할 수 있게 해주는 반면, 패턴 매칭은 객체 생성에 사용된 데이터를 분해하거나 추출할 수 있게 해줍니다. 실제로 이것이 어떻게 보이는지 살펴봅시다.

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

## 패턴 매칭 예제

이 예제에서는 레코드 클래스를 사용하여 서로 다른 트랜잭션 유형을 모델링하고, 목표는 트랜잭션 목록을 사용하여 계정 잔액을 계산하는 메서드를 작성하는 것입니다. 구매 유형인 경우 잔액을 증가시키고, 지불 유형인 경우 잔액을 감소시키며, 지불 취소된 경우에는 다시 잔액을 증가시키어야 합니다.

```js
 public interface Transaction {
    String id();
}

record Purchase(String id, Integer purchaseAmount) implements Transaction {}

record Payment(String id, Integer paymentAmount) implements Transaction {}

record PaymentReturned(String id, Integer paymentAmount, String reason) implements Transaction {}

List transactions = List.of(
    new Purchase("1", 1000),
    new Purchase("2", 500),
    new Purchase("3", 700),
    new Payment("1", 1500),
    new PaymentReturned("1", 1500, "NSF")
);
```

만약 우리가 트랜잭션을 정의하는 코드베이스에 액세스할 수 없거나, 이 코드 부분이 잔액을 계산하는 책임을 지지 않아야한다고 가정해 봅시다. 우리 코드에서 calculateAccountBalance를 구현하는 한 가지 방법은 다음과 같습니다.

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
public static Integer calculateAccountBalance(List transactions) {
  var accountBalance = 0;
  for (Transaction t : transactions) {
    if (t instanceof Purchase p) {
      accountBalance += p.purchaseAmount();
    } else if (t instanceof Payment p) {
      accountBalance -= p.paymentAmount();
    } else if (t instanceof PaymentReturned p) {
      accountBalance += p.paymentAmount();
    } else {
      throw new RuntimeException("Unknown transaction type");
    }
  }
  return accountBalance;
}
```

이 구현은 나쁘지 않습니다. 상당히 가독성이 높고, 처리해야 하는 거래 유형이 더 많아지면 다소 길어질 수 있습니다. 레코드용 패턴 매칭은 다음과 같은 구현을 향상시킵니다.

```java
public static Integer calculateAccountBalance(List transactions) {
  var accountBalance = 0;
  for (Transaction t: transactions) {
    switch(t) {
      case Purchase p -> accountBalance += p.purchaseAmount();
      case Payment(var id, var amt) -> accountBalance -= amt;
      case PaymentReturned(var id, var amt, var reason) -> accountBalance += amt;
      default -> throw new RuntimeException("Unknown transaction type");
    }
  }
  return accountBalance;
}
```

이 코드는 조금 더 깔끔하고 장황하지 않게 보입니다. switch 키워드에 주목하세요. Java에는 오랫동안 사용 가능했습니다. Java 17에서 switch는 람다와 익숙한 문법을 지원하는 표현식을 지원하도록 했고, Java 19 및 21에서는 switch가 레코드에 대한 패턴 매칭을 지원하도록 더욱 향상되었습니다.

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

Pattern matching을 사용할 때에는 첫 번째 경우에 나타난 것처럼 타입의 인스턴스를 참조하거나 두 번째와 세 번째 경우처럼 타입을 그 구성 요소로 분해할 수 있습니다. switch에 대한 패턴 매칭은 또한 새로운 when 키워드를 사용하여 부울 표현식에 따라 패턴을 일치시키는 것을 가능하게 합니다. 예를 들어, 동일한 타입을 여러 번 다른 조건으로 일치시키고 각 경우마다 다른 로직을 실행할 수 있습니다.

만약 여전히 패턴 매칭의 유용성에 확신이 없다면, 한 가지 더 있습니다. 어느 시점 후에 새로운 거래 유형을 도입했다고 가정해봅시다. Reversed purchase transactions을 모델링하기 위해 Credit이라고 부르는 새로운 거래 유형을 도입했다고 가정해봅시다. Transaction을 구현하는 새 레코드 타입을 추가함으로써 우리의 코드는 두 구현 모두에서 컴파일될 것입니다. 우리는 해당 타입을 처리할 수 없다는 문제를 런타임에서 만나게 되어 예외가 발생할 때까지 이를 발견하게 될 것입니다.

레코드에 대한 패턴 매칭의 사용 편의성은 Java 17에 다시 도입된 다른 언어 기능, sealed classes and interfaces ( JEP409)에 의해 더욱 개선되었습니다. 인터페이스를 sealed로 표시하면 Compiler에게 Transaction에 대한 구현이 한정적이라는 정보를 제공하므로, Compiler가 패턴 매칭에서 모든 경우가 처리되었는지를 확인할 수 있습니다. 구현 클래스는 sealed 인터페이스와 동일한 파일에 위치시키거나 인터페이스에서 permit 키워드를 사용하여 닫힌 유형 계층 구조를 지정할 수 있습니다(자세한 내용은 JEP를 참조하십시오).

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

이제 우리의 코드는 모든 경우를 처리하지 못했을 때 컴파일되지 않을 것입니다. 그러나 이를 보장하려면 보통 빠뜨릴 수 있는 부분을 처리하는 기본 케이스를 제거해야 합니다.

```js
 public sealed interface Transaction {
    String id();

    // define in here records extending interface
}


public static Integer calculateAccountBalance(List transactions) {
  var accountBalance = 0;

  for (Transaction t: transactions) {
    switch(t) {
      case Purchase p -> accountBalance += p.purchaseAmount();
      case Payment(var id, var amt) -> accountBalance -= amt;
      case PaymentReturned(var id, var amt, var reason) -> accountBalance += amt;
    }
  }

  return accountBalance;
}
```

이제 컴파일러는 다음과 같은 친절한 오류 메시지로 중단할 것입니다 — "컴파일 실패: switch 문이 모든 가능한 값을 다 다루지 않았습니다".

인터페이스에 sealed 키워드를 사용하는 것은 초기 구현시 if-else 블록을 사용하는 것만큼 유용하지 않았습니다. 따라서 여기서는 패턴 매칭이 더 유리합니다. 이 글에서 다루지 않은 sealed 인터페이스/클래스에는 더 많은 내용이 있지만, 언어의 여러 기능이 잘 결합되어 코드의 견고성을 향상시키는 방법을 보여주는 예 중 하나입니다.

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

지금 설명한 문제를 해결하기 위해 방문자 디자인 패턴을 사용할 수 있다는 사실을 언급할 가치가 있습니다. 그러나 이 패턴은 상당히 더 많은 복잡성과 코드 양을 도입합니다. Java 21은 봉인된 인터페이스와 패턴 매칭이 도입된 Java 17 이후의 기능을 더해 출시된 기능이 많은 버전입니다. 대규모 기업에서는 일반적으로 LTS 버전만 사용을 허용하므로 Java 21이 다음 LTS 릴리스로 선정된 것은 훌륭한 소식입니다.

이 게시물에서 언급하지 않은 많은 흥미로운 새로운 기능들이 있습니다. Virtual Threads인 프로젝트 룸은 아마도 Java 21에서 마무리된 가장 기대되는 기능일 것이지만, 이는 별도의 블로그 게시물이 필요한 큰 주제이기 때문에 다루기 어렵습니다. OpenJDK 웹사이트에서 최근 JDK 21 릴리스에 대해 더 많은 정보를 얻을 수 있습니다.

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

자바는 안정적이고 책임감 있는 방식으로 발전하는 경향이 있습니다. 소프트웨어 업계가 어떻게 변화하는지와 언어 기능을 유지하기 위해 필요한 기능을 주의 깊게 관찰하면서, 기능을 신중하게 구현하고 역호환성을 보장합니다.

버전 21에서 자바는 사용하기 즐거운 언어이며, 이는 자바를 산업에 더 오랜 기간 동안 강하게 뿌리내릴 수 있는 또 다른 중요한 이정표입니다.

Capital One의 소프트웨어 엔지니어링에 대해 더 알아보세요.

원문은 https://www.capitalone.com에서 확인할 수 있습니다.

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

작성자: 마신 코사코프스키, 선임 소프트웨어 엔지니어. 마신은 스몰 비즈니스 카드에서 새로운 카드 제품을 만드는 데 도움을 주는 선임 소프트웨어 엔지니어입니다. 데이터 엔지니어링, 분산 및 이벤트 기반 시스템에 열정을 갖고 있습니다. 여가 시간에는 하이킹과 마운틴 바이크를 즐깁니다.

공개 성명: © 2024 Capital One. 의견은 해당 개인 작성자의 것입니다. 이 게시물에서 별도로 언급되지 않는 한, Capital One은 언급된 회사들과 제휴하거나 보증하고 있지 않습니다. 사용되거나 표시된 모든 상표 및 기타 지적 재산은 각 소유자의 재산입니다. Capital One은 링크된 제 3자 사이트의 콘텐츠나 개인 정보 취급 방침에 대해 책임지지 않습니다.
