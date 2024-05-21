---
title: "코틀린의 Any, Unit, Nothing 이해하기"
description: ""
coverImage: "/assets/img/2024-05-20-UnderstandingKotlinsAnyUnitNothing_0.png"
date: 2024-05-20 17:40
ogImage: 
  url: /assets/img/2024-05-20-UnderstandingKotlinsAnyUnitNothing_0.png
tag: Tech
originalTitle: "Understanding Kotlin’s Any, Unit, Nothing"
link: "https://medium.com/@khush.panchal123/understanding-kotlins-any-unit-nothing-7bcaad73fbc1"
---



![Understanding Kotlin's Any, Unit, Nothing](/assets/img/2024-05-20-UnderstandingKotlinsAnyUnitNothing_0.png)

Kotlin은 Java에서 사용하던 것과는 매우 다른 독특한 유형을 제공합니다. 이 블로그에서는 Any, Unit, 그리고 Nothing과 같은 세 가지 유형을 탐색해 볼 것입니다.

## Any

```js
// 소스 코드
package kotlin
/**
 * 코틀린 클래스 계층 구조의 루트. 모든 코틀린 클래스는 [Any]를 슈퍼 클래스로 갖습니다.
 */
public open class Any {
    public open operator fun equals(other: Any?): Boolean
    public open fun hashCode(): Int
    public open fun toString(): String
}
```

<div class="content-ad"></div>

- 클래스 계층 구조의 루트입니다. 모든 Kotlin 클래스는 Any로부터 상속받습니다.
- Java의 Object에 해당됩니다.
- 우리가 오버라이드할 수 있는 세 가지 메소드를 제공합니다: equals(), hashCode(), toString(). 이것이 어떤 클래스에서 메소드를 오버라이드할 때 IDE에서 이 세 가지 옵션이 자주 제시되는 이유입니다.
- 기본적으로 Nullable이 아닙니다. 변수가 null을 저장할 수 있도록 필요하다면 Any?를 사용할 수 있습니다.

예제

```js
fun printAny(value: Any?) {
    println(value.toString())
}

fun main() {
    printAny("Hello, World!")  // 출력: Hello, World!
    printAny(123)  // 출력: 123
}
```

```js
// 디컴파일할 때 Any는 Java의 Object로 변환됩니다
public static final void printAny(@Nullable Object value) {
   String var1 = String.valueOf(value);
   System.out.println(var1);
}
```

<div class="content-ad"></div>

# Unit

```js
//SOURCE CODE
package kotlin
/**
 * `Unit` 객체만 있는 유형입니다. 이 유형은 Java의 `void` 유형에 해당합니다.
 */
public object Unit {
    override fun toString() = "kotlin.Unit"
}
```

- Java의 void와 동등하지만 void와 달리 Unit은 하나의 인스턴스만 있는 실제 클래스입니다 (싱글톤).
- 의미 있는 값을 반환하지 않는 함수를 나타냅니다.
- 함수의 반환 유형을 명시하지 않으면 Kotlin은 기본 반환 유형으로 Unit을 사용합니다.

예시

<div class="content-ad"></div>

```js
fun printMessage(message: String) { // Unit을 명시적으로 작성할 필요가 없습니다.
    println(message)
}

fun main() {
    printMessage("안녕, Unit!")  // 출력: 안녕, Unit!
}
```

```js
// 변환된 결과, Unit은 Java에서 void로 변환됩니다.
public static final void printMessage(@NotNull String message) {
   Intrinsics.checkNotNullParameter(message, "message");
   System.out.println(message);
}
```

예시: 함수형 타입

```js
fun runBlock(block: ()->Unit) {
    block()
}

fun main() {
    runBlock { println("여기") } // 출력: 여기
}
```

<div class="content-ad"></div>

여기서 () -` Unit은 함수 유형을 나타내며 Unit은 해당 함수 유형이 의미 있는 값을 반환하지 않음을 나타냅니다.

함수 유형을 지정할 때 Unit을 명시하는 것이 필수입니다.

# Nothing

```js
//SOURCE CODE
package kotlin
/**
 * Nothing에는 인스턴스가 없습니다. Nothing을 사용하여 "존재하지 않는 값"을 표현할 수 있습니다: 예를 들어,
 * 반환 유형이 Nothing인 함수의 경우, 이는 결코 반환하지 않음을 의미합니다 (항상 예외를 throw함).
 */
public class Nothing private constructor()
```

<div class="content-ad"></div>

- 아무 값이 없음을 나타내며 함수가 일반적인 방식으로 반환하지 않을 것을 나타냅니다.
- 사용자가 정의한 Kotlin 유형을 포함한 모든 형식의 하위 유형입니다.
- 예외를 throw하거나 무한 루프에 들어가기 때문에 결코 반환되지 않는 함수에서 사용됩니다.
- Nothing의 인스턴스를 만들거나 어떤 클래스도 상속할 수 없습니다.
- 반환 유형이 Nothing인 함수는 기본 반환 유형인 Unit조차 반환하지 않습니다.
- Kotlin의 Nothing 반환 유형은 반환하지 않는다는 것을 명확히 하여 잠재적인 버그로부터 우리를 보호합니다. 반환 유형이 Nothing인 아무 함수가 호출되면 컴파일러는 이 함수 호출을 넘어가지 않고 접근할 수 없는 코드 경고를 알려 줍니다.

예시

```js
fun fail(message: String): Nothing {
    throw IllegalArgumentException(message)
}

fun main() {
    // 이것은 예외를 throw하고 일반적으로 반환하지 않을 것입니다.
    fail("에러 발생!")
    println("안녕") // 컴파일러가 "접근할 수 없는 코드" 경고를 줍니다.
}
```

```js
// Decompile되었을 때, Nothing은 Java에서 Void가 됩니다
// Void는 java.lang 패키지의 일부이며, void Java 원시 유형을 래핑하는 객체에 대한 참조 역할을 합니다. 생성할 수 없습니다.
@NotNull
public static final Void fail(@NotNull String message) {
   Intrinsics.checkNotNullParameter(message, "message");
   throw (Throwable)(new IllegalArgumentException(message));
}
```

<div class="content-ad"></div>

소스 코드: 깃허브

# 연락처:

링크드인, 트위터

코딩 즐기세요! ✌️