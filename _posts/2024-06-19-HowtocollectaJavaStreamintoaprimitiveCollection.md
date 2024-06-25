---
title: "자바 스트림을 기본형 컬렉션으로 수집하는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-HowtocollectaJavaStreamintoaprimitiveCollection_0.png"
date: 2024-06-19 09:58
ogImage:
  url: /assets/img/2024-06-19-HowtocollectaJavaStreamintoaprimitiveCollection_0.png
tag: Tech
originalTitle: "How to collect a Java Stream into a primitive Collection"
link: "https://medium.com/javarevisited/how-to-collect-a-java-stream-into-a-primitive-collection-0a90e246c16e"
---

자바 스트림에 원시 컬렉션 브릿지를 사용하여 박싱을 피해보세요.

![이미지](/assets/img/2024-06-19-HowtocollectaJavaStreamintoaprimitiveCollection_0.png)

# 박싱 (Boxing)하는 건 그만두세요

자바의 박싱된 컬렉션 유형은 Set`Integer`나 List`Double`과 같은 유형으로, 컬렉션에있는 int나 double과 같은 원시 값들을 Integer나 Double과 같은 박싱된 래퍼에 저장해야 하는 유형입니다. 자바의 박싱된 래퍼와 컬렉션 유형이 메모리를 조용히 소모하고 시간을 낭비하며 에너지를 소비합니다. 단일 박싱된 컬렉션의 비용은 일반적으로 무시할 정도입니다. 불행히도, 박싱된 컬렉션이 조용히 수백만 개의 자바 힙을 오염시켜, 아무것도 모르는 한 두 마리의 북극곰의 빙을 녹일 수도 있습니다.

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

![Java Stream with Primitive Collections](/assets/img/2024-06-19-HowtocollectaJavaStreamintoaprimitiveCollection_1.png)

이전에 블로그에서 Java에서 박싱이 왜 나쁜지, Java 개발자로서 애플리케이션 및 라이브러리에서 메모리 및 성능 효율성을 개선하는 옵션에 대해 언급했습니다. 저의 박싱이 왜 나쁜지에 대해 이해하고 싶다면 아래의 블로그를 읽어보세요.

그렇다면 Java Stream의 훌륭한 세계와 Java의 효율적인 기본 컬렉션의 간극을 어떻게 좁힐 수 있을까요?

우리는 박싱한 상태로 있지 않고 몇 가지 해결책을 살펴보겠습니다.

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

# Java 기본형 스트림

자바는 세 가지 기본 유형(IntStream, LongStream, DoubleStream)에 대한 Stream 지원을 제공합니다. 우리는 mapToInt, mapToLong, mapToDouble을 사용하여 오브젝트 스트림을 이러한 기본 유형 중 하나인 스트림 유형으로 변환할 수 있습니다. 기본 스트림을 컬렉션으로 변환하려면 제한된 옵션이 있습니다.

기본적으로 가장 많이 사용되는 옵션은 기본 스트림을 List<Integer>와 같은 상자화된 컬렉션으로 다시 수집하는 것입니다. 이것이 바로 우리가 피하려고 하는 것입니다.

다른 옵션으로 기본 스트림을 기본 배열로 변환하는 것이 있습니다. 아쉽게도, 자바의 배열은 유용한 동작이 없습니다. 길이를 알려줄 뿐이며 요소에 대한 가변 액세스 권한을 제공하여 루프를 돌거나 변경할 수 있습니다. int[], long[], double[] 배열을 Arrays.stream() 메서드를 사용하여 다시 기본적인 스트림으로 감쌀 수 있습니다.

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

이러한 해결책은 int, long, double 유형에만 작동합니다. boolean, byte, char, short, float 유형은 어떻게 해야 할까요? Java 스트림을 기본 Collection 유형으로 매핑하는 솔루션이 모든 여덟 가지 Java 기본 유형을 지원할 수 있는 방법이 있습니까?

네, 있습니다.

# collect! 메서드 호출하기

Java 개발자들은 10년 동안 Java Stream을 사용해 왔으며 대부분은 기본 Stream과 Collector를 함께 사용할 수 없음에 직면해 왔을 것입니다. Java에는 기본 Collection 유형이 없기 때문에 기본 Collection Collector가 필요하지 않았습니다.

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

만약 우리가 Java Stream을 매핑하고 기본 Collection을 수집하려면, Eclipse Collections 또는 기타 기본 Collection 라이브러리에 의존해야 합니다. 이를 통해 여덟 가지 Java 기본 유형에 대한 기본 Collection에 액세스할 수 있습니다. 이 의존성을 추가하기 전에 무엇을 얻을 수 있는지 알아봅시다.

## 옵션 1: Three Musketeer collect 호출

![image](/assets/img/2024-06-19-HowtocollectaJavaStreamintoaprimitiveCollection_2.png)

IntStream, LongStream, DoubleStream에 Three Musketeer 버전의 collect가 있습니다. 기본 collect 메서드의 Three Musketeers 이름은 다음과 같습니다:

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

- 공급자
- Obj(Int/Long/Double)Consumer
- BiConsumer

이 세 가지 함수형 인터페이스는 기본 스트림에 대한 collect 메서드를 대체하는 역할을 하는데, 이 collect 메서드는 기본형 Collector를 사용할 것이다. 세 명의 무사 collect 버전은 Eclipse Collections에서 기본형 컬렉션을 만드는 데 사용될 수 있는데, 다음 예시에서 확인할 수 있다.

## IntStream을 IntList로 변환

```js
@Test
public void intStreamToIntList()
{
    IntStream intStream =
            IntStream.of(1, 2, 3, 4, 5);

    // 세 명의 무사 collect
    IntList ints =
            intStream.collect(
                    IntLists.mutable::empty,   // 공급자
                    MutableIntList::add,       // ObjIntConsumer
                    MutableIntList::addAll);   // BiConsumer

    IntList expected =
            IntLists.mutable.of(1, 2, 3, 4, 5);
    Assertions.assertEquals(expected, ints);
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

## LongStream을 LongSet으로 수집하기

```java
@Test
public void longStreamToLongSet()
{
    LongStream longStream =
            LongStream.rangeClosed(1, 5);

    // Three Musketeer collect
    LongSet longs =
            longStream.collect(
                    LongSets.mutable::empty,   // 공급자
                    MutableLongSet::add,       // ObjIntConsumer
                    MutableLongSet::addAll);   // BiConsumer;

    LongSet expected =
            LongSets.mutable.of(1L, 2L, 3L, 4L, 5L);
    Assertions.assertEquals(expected, longs);
}
```

이 방법은 DoubleStream에서도 작동하며, Eclipse Collections에는 Three Musketeer collect를 사용할 수 있는 기본 List, Set, Bag이 있습니다.

그렇다면 Java Stream에서 다른 다섯 가지 기본 유형을 어떻게 수집할 수 있을까요?

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

## 옵션 2: 객체 스트림과 함께 기본 Collector 사용하기

![이미지](/assets/img/2024-06-19-HowtocollectaJavaStreamintoaprimitiveCollection_3.png)

맞아요. Java에는 기본 컬렉션 Collectors가 없습니다. 왜냐하면 Java에는 기본 컬렉션이 없기 때문입니다. 그러나 Eclipse Collections에는 기본 Collector 인스턴스가 있습니다. Java Stream과 기본 컬렉션 사이의 다리를 구축하는 데 도움이 되는 Collectors2란 유틸리티 클래스가 있습니다. Collectors2를 사용하여 어떻게 Object Stream을 모든 여덟 가지 기본 Java 유형(boolean, byte, char, short, int, float, long, double)에 대한 형식(Set, List, Bag)의 기본 컬렉션으로 변환할 수 있는지 살펴보겠습니다.

어떤 객체 유형의 Stream부터 시작하여 해당 Stream을 모든 여덟 가지 기본 유형에 대한 여러 기본 컬렉션으로 어떻게 변환할 수 있는지 살펴봅시다.

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

## Stream`String`을 BooleanBag으로 수집

이 예제는 String 스트림을 BooleanBag으로 변환한 후 true와 false의 발생 횟수를 세는 방법을 보여줍니다.

```js
@Test
public void streamToBooleanBag()
{
    Stream<String> stream =
            Stream.of("true", "false", "true", "false", "true");

    BooleanBag booleans = stream.collect(
            Collectors2.collectBoolean(
                    Boolean::parseBoolean,
                   BooleanBags.mutable::empty));

    Assertions.assertEquals(3, booleans.occurrencesOf(true));
    Assertions.assertEquals(2, booleans.occurrencesOf(false));
}
```

## Stream`String`을 ByteList로 수집

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

이 예제는 String Stream을 ByteList로 변환합니다.

```js
@Test
public void streamToByteList()
{
    Stream<String> stream = Stream.of("1", "2", "3", "4", "5");

    ByteList bytes = stream.collect(
            Collectors2.collectByte(
                    Byte::parseByte,
                    ByteLists.mutable::empty));

    ByteList expected = ByteLists.mutable.with(
            (byte) 1,
            (byte) 2,
            (byte) 3,
            (byte) 4,
            (byte) 5);
    Assertions.assertEquals(expected, bytes);
}
```

## Stream`Character`을 CharSet로 변환

이 예제는 Character 인스턴스의 Stream을 CharSet으로 변환합니다.

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
public void streamToCharSet()
{
    Stream<Character> stream =
            Stream.of('a', 'b', 'c', 'd', 'e');

    CharSet characters = stream.collect(
            Collectors2.collectChar(
                    Character::charValue,
                    CharSets.mutable::empty));

    CharSet expected =
            CharSets.mutable.with('a', 'b', 'c', 'd', 'e');
    Assertions.assertEquals(expected, characters);
}
```

## Stream`Sh` collect to ShortBag

이 예제를 위해 Sh라는 Java 레코드를 만들었습니다... Sh는 Short보다 짧았어요. Sh는 int에서 캐스팅된 short 값을 보유합니다. Short처럼 Java에는 short를 나타내는 짧은 short 리터럴이 없으므로 int 리터럴을 short로 캐스트해야 합니다. Sh 레코드를 사용하면 한 곳에서 캐스팅할 수 있었어요.

```js
@Test
public void streamToShortBag()
{
    record Sh(short value)
    {
        public Sh(int intValue)
        {
            this((short) intValue);
        }
    };
    Stream<Sh> stream =
            Stream.of(new Sh(1), new Sh(2), new Sh(3), new Sh(4), new Sh(5));

    ShortBag shorts = stream.collect(
            Collectors2.collectShort(
                    Sh::value,
                    ShortBags.mutable::empty));

    ShortBag expected =
            ShortBags.mutable.with(
                    (short) 1,
                    (short) 2,
                    (short) 3,
                    (short) 4,
                    (short) 5);
    Assertions.assertEquals(expected, shorts);
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

## Stream`BigInteger`을 IntList로 수집하기

BigInteger에 대해 네 가지 싱글톤 값이 있다는 것은 이 예제 테스트를 작성할 때까지 알지 못했습니다. 이제 우리 모두 알게 되었네요.

```js
 @Test
public void streamToIntList()
{
    Stream<BigInteger> stream =
            Stream.of(
                    BigInteger.ZERO,
                    BigInteger.ONE,
                    BigInteger.TWO,
                    BigInteger.TEN);

    IntList ints = stream.collect(
            Collectors2.collectInt(
                    BigInteger::intValueExact,
                    IntLists.mutable::empty));

    IntList expected =
            IntLists.mutable.with(0, 1, 2, 10);
    Assertions.assertEquals(expected, ints);
}
```

## Stream`BigDecimal`을 FloatSet으로 수집하기

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

이 예시는 BigDecimal의 스트림을 FloatSet으로 변환합니다.

```js
@Test
public void streamToFloatSet()
{
    Stream<BigDecimal> stream =
            Stream.of(
                    BigDecimal.ZERO,
                    BigDecimal.ONE,
                    BigDecimal.TWO,
                    BigDecimal.TEN);

    FloatSet floats = stream.collect(
            Collectors2.collectFloat(
                    BigDecimal::floatValue,
                    FloatSets.mutable::empty));

    FloatSet expected =
            FloatSets.mutable.with(0.0f, 1.0f, 2.0f, 10.0f);
    Assertions.assertEquals(expected, floats);
}
```

## Stream의 LocalDate를 LongBag으로 변환

이 예시는 LocalDate의 스트림을 LongBag으로 변환하여 LocalDate 인스턴스를 epochDay로 변환한 것을 포함합니다.

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
/**
 * LongBag으로의 스트림 처리
 */
@Test
public void streamToLongBag()
{
    LocalDate now = LocalDate.of(2024, Month.MAY, 31);
    Stream<LocalDate> stream =
            Stream.of(
                    now,
                    now.plusDays(1L),
                    now.plusDays(2L),
                    now.plusDays(3L));

    LongBag longs = stream.collect(
            Collectors2.collectLong(
                    LocalDate::toEpochDay,
                    LongBags.mutable::empty));

    LongBag expected =
            LongBags.mutable.with(19874L, 19875L, 19876L, 19877L);
    Assertions.assertEquals(expected, longs);
}

## Stream`BigDecimal`을 DoubleList로 변환

이 예제는 BigDecimal 스트림을 DoubleList로 변환합니다.

/**
 * DoubleList로의 스트림 처리
 */
@Test
public void streamToDoubleList()
{
    Stream<BigDecimal> stream =
            Stream.of(
                    BigDecimal.ZERO,
                    BigDecimal.ONE,
                    BigDecimal.TWO,
                    BigDecimal.TEN);

    DoubleList doubles = stream.collect(
            Collectors2.collectDouble(
                    BigDecimal::doubleValue,
                    DoubleLists.mutable::empty));

    DoubleList expected =
            DoubleLists.mutable.with(0.0d, 1.0d, 2.0d, 10.0d);
    Assertions.assertEquals(expected, doubles);
}

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

# 효율성은 여전히 우리의 문제입니다

저는 자바를 좋아하고, Project Valhalla가 미래 버전에서 완전히 구현되기를 기다리지 않을 수 없어요. 간결한 람다 표현식을 위해 10년을 기다렸고, 이제 Project Valhalla를 위해 10년을 기다리고 있어요. 지난 20년간 금융 서비스 분야에서 일한 경험상 자바 언어와 표준 라이브러리가 발전하여 나를 마주한 문제들을 해결하기 위해 기다릴 시간이 없었어요. 저와 동료들이 한 작업은 대부분 메모리 문제나 성능 압력에 의해 이뤄졌어요. 우리가 한 일을 모두가 HashSet`Integer`의 모든 인스턴스를 제거하여 행성을 구원하는 위대한 자선 사업의 일환으로 주장하지는 않을 거예요. 하지만 가끔은 세상의 모든 HashSet`Integer` 인스턴스를 제거하는 것을 꿈꾸곤 해요. :)

좋은 소식은 Valhalla를 기다릴 필요가 없다는 것이에요. 오늘날 자바에서 원시 타입 컬렉션을 필요로 하거나 전체적인 컴퓨팅 풋 프린트를 줄이기 위해 효율적인 코딩 스타일을 채택하고자 할 때 기다릴 필요가 없어요. 우리가 원할 때 더 효율적인 자바 코드를 작성하도록 도와주는 솔루션이 있어요. 이러한 솔루션 중 일부는 20년 동안 개발되어 왔어요. 새로운 유형이나 메서드를 배우고 제3자 라이브러리에 의존성을 추가하는 것에는 비용이 들어요. 혜택이 없다면 이의 의존성 비용을 지는 것에 의미가 없어요. 그러나 우리가 새로운 HashSet`Integer`를 입력할 때마다 생각해야 해요. 우리 애플리케이션에서 이 코드를 입력하거나 보면 꼭 북극곰에 대해 생각하고 싶어요.

Valhalla가 도착했을 때, 우리 모두가 그것을 배우고 적용해 애플리케이션에서 새로운 효율성을 활용하여 모두가 함께 풋프린트를 줄이고 애플리케이션 성능을 향상시킬 수 있었으면 좋겠어요. 효율성은 우리의 문제입니다. 오늘날 사용 가능한 솔루션을 채택하든, Project Valhalla를 기다리든, 효율적인 컬렉션 코드를 작성하는 문제는 우리의 문제입니다. 혹시 Valhalla가 어떠한 효율성을 도입하여 우리가 어떠한 노력도 하지 않아도 메모리 및 성능 절약을 마법같이 제공할 수 있다면 그건 물론 환영받을 일이겠지요!

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

읽어 주셔서 감사합니다! 새롭고 유용한 정보를 배우셨길 바랍니다!

저는 Eclipse Collections OSS 프로젝트의 창조자이자 기여자입니다. Eclipse Collections는 Eclipse Foundation에서 관리되고 있습니다. Eclipse Collections는 기여를 환영합니다.
```
