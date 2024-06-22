---
title: "자바 Optional을 더 유창하게 활용하기"
description: ""
coverImage: "/assets/img/2024-06-19-UseJavaOptionalFluently_0.png"
date: 2024-06-19 22:06
ogImage: 
  url: /assets/img/2024-06-19-UseJavaOptionalFluently_0.png
tag: Tech
originalTitle: "Use Java Optional Fluently"
link: "https://medium.com/@viraj_63415/use-java-optional-fluently-315df1209bea"
---


Optional 클래스에는 filter(), map() 및 flatMap()과 같은 다양한 공개 메서드가 포함되어 있습니다. 함수형 및 유창한 프로그래밍에 유용합니다. 이 글에서는 Java Optional을 사용하여 함수형 및 유창한 프로그래밍 스타일을 탐구할 것입니다.

![Java Optional](/assets/img/2024-06-19-UseJavaOptionalFluently_0.png)

# Java Optional 기본 사항

Java Optional의 기본 사항을 다시 확인해 봅시다. 아래는 사용자 레코드의 간단한 예시로, 사용자 ID, 이름, 주소 및 사용자가 CEO인지 여부와 같은 데이터 속성이 포함되어 있습니다. 아래는 코드에서 Optional 클래스를 사용하는 예시입니다.

<div class="content-ad"></div>


// 사용자 레코드
record User(String uid, String name, 
            Address address, boolean ceo){

   public static final User DEFAULT_USER 
     = new User("0", "더미", null, false);

}

// 주소 레코드
record Address(String line, String zip) {
}

// User 객체를 생성하는 User 팩토리 클래스
public class Users {

  public static Optional<User> getUser(String uid) {

      User user = null;

      // user 변수를 설정하는 로직

      return Optional.ofNullable(user);
  }
}

// Optional 값을 반환하는 getUser 메서드를 호출하는 예시
Optional<User> oUser = Users.getUser(uid);
if (oUser.isPresent()) {

   // User를 처리합니다. 비어있지 않습니다
   String name = oUser.get().name();
}


위 예시에서 Users 클래스에는 getUser(...)라는 메서드가 있는데 이 메서드는 User 타입의 Optional을 반환합니다. 이는 호출자에게 반환된 User 값이 null일 수 있다는 것을 나타냅니다. Optional 값 자체가 null이 되어선 안 됩니다.

Users.getUser(...)의 호출자는 Optional의 isPresent() 메서드를 사용하여 유효한 값이 있는지 확인하고 있다면 사용자를 처리할 것입니다. Optional 클래스를 사용하는 이점은 호출자가 null 반환 값을 처리하는 방법을 고려하게끔 만든다는 점입니다. 이로써 Null Pointer Exception을 방지하고 코드를 견고하게 만듭니다. 본질적으로 이 아이디어는 호출자가 Exception을 처리할 수 있도록 강제하는 것과 유사합니다.

다수의 개발자가 사용할 라이브러리를 작성할 때 Java Optional을 반환하는 것이 권장됩니다. 모든 메서드 호출이 null을 반환할 수 있는 경우에 Optional을 반환하도록 설계하는 것은 목적이 아닙니다. 예를 들어 Java Streams API와 Spring Boot 라이브러리는 많은 곳에서 Java Optional을 반환합니다.


<div class="content-ad"></div>

대부분의 개발자들은 Java Optional 클래스의 이러한 최소한의 기능만 사용하는 경향이 있습니다(유용한 기능입니다). 그러나 Optional 클래스의 여러 메서드 중에는 Java Optional을 사용하는 데 매우 유용한 다양한 기능적 스타일의 메서드가 숨겨져 있습니다. 이러한 메서드를 사용하면 코드를 간결하고 가독성있게 만드는 데 매우 유용합니다.

Optional 객체를 함수형 스타일로 소비하는 방법을 살펴보겠습니다.

# Optional을 활용한 함수형 스타일

Optional 클래스는 ifPresent(..)라는 메서드를 제공하는데, 여기에 프로그래머가 포함된 객체를 처리하는 Lambda 함수(Consumer)를 제공할 수 있습니다. 아래는 예제입니다.

<div class="content-ad"></div>

```js
// 사용자를 처리하기 위해 Lamba 객체를 전달합니다
Optional<User> oUser = Users.getUser(uid);
oUser.ifPresent(user -> {
    
    // 사용자 처리
    String name = user.name();
    System.out.println(name);
});

// "Optional.ifPresent" 메서드 서명
void ifPresent(Consumer<? super T> action)
```

여기서 코드는 ifPresent(..)를 호출하고 포함된 사용자 객체를 처리하기 위해 Consumer를 전달하고 있음을 볼 수 있습니다. 그러나 변수 oUser가 빈 Optional인 경우 - Consumer가 호출되지 않습니다. 다시 말하면, 전통적인 Java "if"문을 사용하는 함수 스타일입니다. ifPresent의 메서드 서명도 표시되어 있으며 Consumer 인터페이스를 사용합니다.

전형적인 람다 프로그래밍의 일환으로, 람다 대신 메소드 참조를 전달할 수 있습니다. 이렇게 하면 코드가 더 읽기 쉬워집니다.

```js
// 메소드 참조를 사용한 함수형 스타일
Optional<User> oUser = Users.getUser(uid);
oUser.ifPresent(Main::handleUser);


// Main.java의 handleUser 정적 메소드
public static void handleUser(User u) {

    // 사용자 처리
    String name = u.name();
    System.out.println(name);
}
```

<div class="content-ad"></div>

여기서 Main :: handleUser 메서드 참조가 매개변수로 전달되어 핸들러가 별도의 메서드로 격리되었습니다.

만약 호출자가 전통적인 if-then-else 문을 사용하려면 어떻게 할까요?

"ifPresentOrElse(..)" 메서드를 사용하여 가능합니다. 다음은 예시입니다

```js
// 함수형 스타일의 if-then-else. 
// 값이 존재하면 handleUser 호출
// 그렇지 않으면 AppException이 throw됨
oUser.ifPresentOrElse(Main::handleUser, () -> {
    throw new AppException("Some Error");
});

// "Optional.ifPresentOrElse"의 시그니처
void ifPresentOrElse(
  Consumer<? super T> action, Runnable emptyAction)
```

<div class="content-ad"></div>

사용자가 사용 가능하면 Method Reference로 표시된 첫 번째 매개변수가 호출됩니다. 그렇지 않은 경우 두 번째 매개변수로 표시된 Runnable을 나타내는 람다 함수가 호출됩니다. 이 방법은 Optional 객체에 적용된 전통적인 Java if-then-else의 기능적 동등물입니다.

Optional 클래스에는 비어있을 때 수행해야 하는 작업을 표현하는 데 유용한 여러 orXXX(..) 메서드가 있습니다.

## orXXX(..) 메서드

orElse() 메서드를 사용한 예제입니다.

<div class="content-ad"></div>

```js
// Optional이 비어있을 때 기본 사용자 반환
User user = oUser.orElse(User.DEFAULT_USER);
```

여기서 orElse(..) 메소드를 사용합니다. 해당 값이 있는 경우 user 변수에 할당되고, 그렇지 않으면 DEFAULT_USER가 할당됩니다. 이 방법은 Groovy와 같은 다른 언어의 Elvis 연산자에 해당하는 함수적 동등체입니다.

그러나 기본 사용자가 상수보다 조금 복잡하고 어떤 로직에 의해 검색되어야 하는 경우는 어떨까요?

Optional 클래스가 해결책을 제공합니다. orElseGet(..) 메소드를 사용할 수 있습니다.

<div class="content-ad"></div>

```js
// Supplier를 호출하여 기본 사용자를 반환합니다.
User user = oUser.orElseGet(() -> {
    // 사용자를 가져오기 위한 기본 로직
    return defUser;
});
```

이는 Optional oUser가 비어 있을 때, Supplier Lambda 함수가 호출되어 내부 로직을 이용하여 기본 사용자를 검색합니다. Optional에는 사용자 대신 "Optional of User"를 공급하는 Supplier를 사용하는 or(..) 메소드도 있습니다.

Optional이 비어 있을 때 예외를 throw하고 싶다면 어떻게 해야 할까요?

```js
// 가능한 경우 사용자 추출
// 그렇지 않으면 NoSuchElementException을 throw합니다.
User user = oUser.orElseThrow();
```

<div class="content-ad"></div>

orElseThrow() 메서드를 사용하여 사용자를 반환하거나, 가능한 경우 기본 NoSuchElementException 예외를 throw합니다.

만약 Optional이 비어있는 경우 사용자 정의 예외를 throw하려면 어떻게 해야 할까요?

```java
// 사용자 추출 가능한 경우 
// 그렇지 않으면 AppException throw
User user = oUser.orElseThrow(
        () -> new AppException("에러"));
```

우리는 매개변수로 람다(Supplier of Exception) 표현식을 전달하는 오버로드된 orElseThrow(...) 메서드를 사용합니다.

<div class="content-ad"></div>

여기서 더 좋아집니다. Optional에는 메서드 체인을 구축하는 데 유용한 메서드가 많이 있습니다.

# Optional을 활용한 Fluent Style

플루언트 스타일 프로그래밍은 특정 사용 사례를 표현하고 구현하는 데 메서드 체이닝을 사용합니다. 이러한 종류의 프로그래밍은 자바 스트림을 사용할 때 볼 수 있습니다. 이 스타일을 쉽게 인식할 수 있도록 다음과 비슷한 가짜 코드가 있습니다.

```js
ReturnType output = object
                       .method1(..) 
                       .method2(..)
                       ...
                       .methodN(..);
```

<div class="content-ad"></div>

Optional에는 filter(..), map(..), flatMap(..)과 같은 몇 가지 메서드가 있습니다. 이러한 메서드는 유창한 스타일 개발에 사용할 수 있습니다. 이러한 메서드는 동등한 스트림 메서드와 유사하지만 하나의 값에 작용합니다. 각각 살펴보겠습니다.

# map() 메서드

map() 메서드는 한 유형의 Optional을 다른 유형의 Optional로 변환합니다. 다음 코드를 고려해보세요. 이 코드는 명령형 스타일(일반적인 if문)로 사용자의 이름을 추출합니다.

```js
// 사용자의 이름 가져오기
String name = null;
Optional<User> oUser = Users.getUser(uid);
if (oUser.isPresent()) {
    name = oUser.get().name();
}
```

<div class="content-ad"></div>

위의 코드에서는 사용자 ID(uid)에 해당하는 사용자 객체를 가져와서 해당 사용자의 이름을 추출하고 있습니다. 변수 name은 null이거나 유효한 문자열일 수 있습니다. Optional 세계에서는 이를 Optional`String`으로 표현하는 것이 가장 완벽한 방법입니다. 따라서 실제 요구사항은 Optional`User`를 Optional`String`(이름)으로 변환하는 것입니다. Optional.map()은 이 작업을 우리를 위해 수행해 주며, 아래와 같이 유창한 스타일을 사용하여 보여줍니다.

```js
// Optional<User> -> Optional<String>
Optional<String> name
        = Users.getUser(uid)
               .map(User::name);
```

보시다시피, 우리는 optional에서 map() 메서드를 호출합니다. map() 메서드는 "User 개체를 가져와 사용자 이름을 반환하는 함수"를 인수로 취합니다. Method reference 'User::name'은 해당 함수를 표현하기 위해 전달됩니다. 따라서 여기서는 이름을 추출하기 위해 더 유창한 API를 사용하였고, 최종 결과물은 눈에 즐거운 것입니다.

# filter() 메서드

<div class="content-ad"></div>

이제 사용자가 회사의 CEO인 경우에만 유효한 이름을 가져와야 한다고 요구 사항을 약간 변경합시다. 기억하세요, 사용자 객체에 사용자가 CEO인지 여부를 나타내는 플래그가 있습니다. 명령문 스타일을 사용하여 메소드 코드는 다음과 같이 보일 것입니다.

```js
// User -> name of CEO
String name = null;
Optional<User> oUser = Users.getUser(uid);
name = oUser.filter(User::ceo)
              .map(User::name)
              .orElse(null);
```

여기서는 사용자가 CEO인 경우에만 이름 필드를 설정합니다. 따라서 사용자가 CEO인지 여부를 확인하기 위해 user.ceo() 메소드를 사용한 추가적인 확인이 필요합니다. 이 명령문 스타일 프로그래밍의 문제는 원하지 않는 코드로 이어지는 여러 개의 "if" 문이 있어서 코드가 복잡해집니다.

우리는 위의 코드를 filter()와 map()의 멋진 조합으로 대체할 수 있습니다.

<div class="content-ad"></div>

```js
// CEO만 허용하려면 filter를 사용하세요
Optional<String> name
        = Users.getUser(uid)
               .filter(User::ceo)
               .map(User::name);
```

여기서 Optional 사용자 객체를 얻은 후, filter(..) 메서드가 사용자가 CEO인지 확인합니다. filter() 메서드는 Predicate를 취하며, 런타임에서 이 Predicate가 true를 반환하면 filter는 다시 Optional 사용자 객체를 반환합니다. 그렇지 않으면 빈 Optional을 반환합니다. 다음 단계는 map() 메서드를 호출하는 것인데, 이는 사용자 이름을 나타내는 Optional 문자열을 반환할 것입니다.

Fluent 스타일 개발을 사용하면 코드의 가독성이 뚜렷하게 향상된 것을 볼 수 있습니다. Fluent 스타일의 프로그래밍은 사용에 익숙해지려면 약간의 연습이 필요합니다.

# flatMap() 메서드


<div class="content-ad"></div>

이제 flatMap() 메서드에 대해 이야기해 보겠습니다 - 이는 map() 메서드의 변형입니다. 이 메서드를 이해하기 위해 우리의 사용 사례를 약간 수정해 보겠습니다. 사용자의 이름 대신 사용자의 우편번호를 추출하고 싶다고 가정해 봅시다. 이 경우 명령형 프로그래밍에서의 첫 번째 시도는 다음과 같을 것입니다.

```js
// User -> zip
String zip = null;
Optional<User> oUser = Users.getUser(uid);
if (oUser.isPresent()) {
    User user = oUser.get();
    Address address = user.address();
    if (address != null) {
        zip = address.zip();
    }
}
```

이 코드는 작동하지만 명백한 이유로 이상적이지 않습니다. 이러한 계단식 if 문을 map() 메서드를 사용하여 우아하게 제거할 수 있습니다 - 아래에서 확인할 수 있습니다.

```js
Optional<String> zip
        = Users.getUser(uid)
               .map(User::address)
               .map(Address::zip);
```

<div class="content-ad"></div>

그러면 두 개의 메소드 — getAddress()와 getZip()를 추가하여 User와 Address의 디자인을 아래와 같이 변경한다면 어떨까요?

```js
// Record User
record User(String uid, String name, 
            Address address, boolean ceo) {

    public static final User DEFAULT_USER 
       = new User("0", "Dummy", null, false);

    public Optional<Address> getAddress() {
        return Optional.ofNullable(address);
    }
}

// Record Address
record Address(String line, String zip) {

    public Optional<String> getZip() {
        return Optional.ofNullable(zip);
    }
}
```

만약 map 메소드에 "Optional 타입을 반환하는 함수"를 전달하면, 반환된 결과는 "Optional`Optional`타입"이 됩니다. flatMap(..)의 사용은 연속적인 옵셔널을 플래트하게 만들어 주어 Optional`타입`으로 만들면 간편하게 작업할 수 있습니다.

따라서 우리의 fluent pipeline에서 address()와 zip() 메소드 대신 getAddress()와 getZip() 메소드를 사용하는 경우, 우리는 다음과 같은 결과를 얻게 될 것입니다.

<div class="content-ad"></div>

```js
Optional<String> zip = Users.getUser(uid)
                         .flatMap(User::getAddress)
                         .flatMap(Address::getZip);
```

`flatMap(..)` 메소드의 사용에 주목하세요. Method Reference `User::getAddress`는 `Optional<Address>`를 반환하기 때문에 `flatMap(..)`은 단순히 `Optional<Address>`를 반환할 것이며, `map(..)`은 `Optional<Optional<Address>>`를 반환했을 것입니다.

# `stream()` 메소드

마지막으로, `Optional`은 `stream()` 메소드도 가지고 있는데, 이 메소드는 0 또는 1개의 값을 가지는 스트림을 생성합니다. 만약 `Optional`이 비어있다면 0개의 요소를 가진 스트림을 반환하고, 유효한 값이 있는 경우 1개의 요소를 가진 스트림을 반환할 것입니다. 그런 다음 사용 가능한 스트림 메소드 중 하나를 사용하여 스트림을 함수형 스타일로 조작할 수 있습니다.

<div class="content-ad"></div>

만약 Optional.stream() 메소드를 언제 사용해야 하는지에 대해 더 알고 싶다면, 제 Medium 기사를 읽어보세요.

Java Optional을 사용할 때 따를 몇 가지 기본 규칙을 알고 싶다면, 아래의 Medium 기사를 읽어보세요.

또한 Optional 클래스에 대한 Javadoc을 살펴보기 위해 아래의 링크를 이용해 주세요.

이 글이 도움이 되었다면, 아래의 👏 버튼을 여러 번 클릭하여 지원을 보여주세요. 읽어주셔서 감사합니다!

<div class="content-ad"></div>

# Summary

Optional은 가장 간단한 형태로 사용될 수 있지만, 개발자가 유창한 스타일의 코드를 작성하는 데 도움이 되는 여러 메서드가 있습니다. 많은 경우 더 간결하고 가독성이 높으며 견고한 코드를 작성할 수 있습니다.

사용을 고려해 보세요!