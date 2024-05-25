---
title: "자바 스레드 로컬Thread Locals은 어떻게 동작하나요"
description: ""
coverImage: "/assets/img/2024-05-23-HowdoJavaThreadLocalswork_0.png"
date: 2024-05-23 12:39
ogImage: 
  url: /assets/img/2024-05-23-HowdoJavaThreadLocalswork_0.png
tag: Tech
originalTitle: "How do Java Thread Locals work?"
link: "https://medium.com/@viraj_63415/how-does-java-thread-locals-work-3278453ac34a"
---


<img src="/assets/img/2024-05-23-HowdoJavaThreadLocalswork_0.png" />

자바에서 쓰레드 로컬(Thread Locals)은 전체 쓰레드 범위를 가지는 변수입니다. 이 말은 쓰레드 어디서든지 이러한 변수를 설정하고, 동일한 쓰레드에서는 어디서든지 액세스할 수 있다는 것을 의미합니다. 한 쓰레드에서 설정된 값은 다른 쓰레드에서 접근할 수 없습니다.

자바 ThreadLocal 클래스에는 두 가지 유형이 있음을 알아야 합니다 — ThreadLocal 및 InheritableThreadLocal. 두 클래스 간의 차이를 살펴봅시다.

# ThreadLocal 클래스

<div class="content-ad"></div>

아래는 쓰레드 로컬 변수가 선언된 예시입니다. user 변수는 User 타입(Class 또는 Interface)의 변수를 보유하는 ThreadLocal 변수입니다. 여기서 user 변수가 public 및 static으로 선언되어 어디서든 코드 내에서 접근할 수 있도록 설정되었습니다.

```js
// Declare a Thread Local Variable user
public static final ThreadLocal<User> user 
                     = new ThreadLocal<>();
```

아래는 쓰레드를 위해 user를 설정하고 가져오는 방법입니다. 예시에서는 'bob'으로 설정된 User 객체에 user 변수를 설정하고, 동일한 쓰레드 내에서 get() 메소드를 호출하면 User 'bob'이 검색됩니다.

```js
// Sets the calling thread’s value for user
user.set(new User("bob"));

// Gets the calling thread’s value for user
User requestUser = user.get();
```

<div class="content-ad"></div>

사용자 변수가 코드베이스 전체에서 접근 가능하더라도 set(..) 메서드는 전달된 User 객체가 "호출" 스레드와 연관되도록 합니다. get() 메서드는 또한 "호출" 스레드와 연관된 User 객체를 검색하며, 다른 스레드에서 get() 메서드를 호출하더라도 bob이 아니라 다른 사용자(또는 null)를 검색하지 않습니다. 각 Java 스레드는 해당 스레드에 설정된 모든 스레드 로컬을 포함하는 ThreadLocal Map과 연결됩니다.

만약 아무것도 설정되지 않은 경우 get() 메서드를 호출하면 어떻게 될까요? 이 메서드는 단순히 null을 반환합니다.

그러나 람다 공급자로 초기 User 객체를 반환하는 Thread Local 객체를 생성할 수 있습니다. 아래 예제는 'anonymous'라는 User를 반환하는 공급자를 보여줍니다. 따라서 값이 설정되지 않은 ThreadLocal에 get() 메서드를 호출하면 이전에 값을 설정하지 않았을 때 공급자의 get() 메서드가 호출되며 사용자의 초기 값으로 설정됩니다.

```js
// 공급자와 함께 Thread Local 변수 user를 선언
public static ThreadLocal<User> user 
          = ThreadLocal.withInitial(
                () -> new User("anonymous"))

// 'Anonymous'를 반환
User requestUser = user.get();
```

<div class="content-ad"></div>

다음과 같이 remove() 메서드를 호출하여 이전에 설정된 값을 제거할 수도 있습니다.

```js
// 호출 스레드의 사용자 값 제거
user.remove();
```

이 방법은 기본적으로 스레드와 관련된 User 객체를 제거합니다. 더 중요한 것은 이 작업으로 다른 스레드에는 영향을 미치지 않는다는 점입니다.

스레드 로컬을 다이어그램 형식으로 시각화한다면(저는 이것을 좋아합니다), 다음과 같이 보일 것입니다. 두 스레드의 사용자 변수가 서로 다른 User 객체를 가리키는 것을 알 수 있습니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-HowdoJavaThreadLocalswork_1.png" />

# ThreadLocal 및 자식 스레드

이전 섹션까지의 논의는 주로 한 개의 Java 스레드와 관련되어 있었습니다. 만약 Java 스레드가 새로운 자식 스레드를 시작한다면 어떻게 될까요? 자식 스레드가 부모에서 정의된 Thread Local 변수에 자동으로 액세스할 수 있을까요?

답은 "아니요"입니다! 자식 스레드는 부모의 Thread Local에 액세스할 수 없으며 이에 대한 매우 좋은 이유가 있습니다. 만약 액세스 가능하다면, Thread Local 변수에 저장된 객체는 스레드 안전하게 작성되어야 할 것이며 이렇게 되면 여러 스레드가 동일한 사용자 객체에 액세스할 수 있을 것입니다. 이는 Java 엔지니어들에 의한 좋은 기본 디자인 결정입니다.

<div class="content-ad"></div>

하지만 때로는 그런 액세스가 유용할 때가 있습니다. 많은 사용자가 애플리케이션에 액세스하는 웹 애플리케이션과 같은 시나리오를 상상해보십시오. 요청 처리 중 사용자와 연결된 단일 Java 스레드가 있으며, 이 스레드의 Thread Local 객체에 사용자 객체가 저장되어 있다고 상상할 수 있습니다(이는 많은 응용 프로그램 서버 및 Spring Boot와 같은 프레임워크에서 수행됩니다). 그러나 생성된 자식 스레드도이 사용자 정보에 액세스하길 원할 수 있습니다.

이 시나리오에 대해 Java는 InheritableThreadLocal이라는 다른 클래스를 제공합니다.

# InheritableThreadLocal 클래스

이 클래스를 사용하는 구문은 사실상 ThreadLocal 클래스와 거의 동일합니다. 아래 예제에서는 InheritableThreadLocal 클래스에 대한 해당 메서드를 보여줍니다.

<div class="content-ad"></div>

```java
// 상속 가능한 쓰레드 로컬 변수 user를 선언합니다
public static final InheritableThreadLocal<User> user = new InheritableThreadLocal<>();

// 호출 중인 쓰레드의 user 값을 설정합니다
user.set(new User("bob"));

// 호출 중인 쓰레드의 user 값을 가져옵니다
User requestUser = user.get();

// 호출 중인 쓰레드의 user 값을 제거합니다
user.remove();
```

쓰레드 로컬 맵과 마찬가지로, 모든 쓰레드에는 상속 가능한 쓰레드 로컬 변수를 위한 맵이 있습니다. 여기서 큰 차이점은 자식 쓰레드가 생성될 때, 자식의 상속 가능한 쓰레드 로컬 맵이 부모로부터 복제된다는 것입니다. 따라서, 상속 가능한 쓰레드 로컬 변수는 자식 쓰레드에서도 접근할 수 있습니다.

만약 상속 가능한 쓰레드 로컬 변수를 다이어그램 형태로 시각화한다면, 다음과 같이 보일 것입니다. 상속 가능한 쓰레드 로컬 맵이 부모로부터 복제된 것을 볼 수 있습니다.

<img src="/assets/img/2024-05-23-HowdoJavaThreadLocalswork_2.png" />
```

<div class="content-ad"></div>

# 함정에 유의하세요!

위 다이어그램에서 명확히 볼 수 있듯이, 상속 가능한 쓰레드 로컬 변수의 장점은 단점이 될 수도 있습니다. 기본적으로, 자식 쓰레드가 생성될 때 상속 가능한 쓰레드 로컬 맵도 복제됩니다. 그러나 부모와 자식 쓰레드에서 동일한 "User" 객체를 가리킨다는 것을 알 수 있습니다.

이것은 User 객체가 여러 쓰레드에서 접근될 수 있고 스레드 안전하게 작성되어야 한다는 것을 의미합니다. 다시 말하면 - 단순한 ThreadLocal 클래스와 관련된 스레드 안전성이 InheritableThreadLocal 클래스를 사용할 때는 손실됩니다. 이것은 여러분의 디자인에 완벽히 적합할 수 있습니다 - 이에 문제는 없습니다.

그러나 더 안전한 접근 방식이 있을 수 있습니다. InheritableThreadLocal을 생성할 때 다음과 같이 childValue(..) 메서드를 지정할 수 있습니다. 사실, 아래 예시에서는 초기값과 child 값도 지정합니다.

<div class="content-ad"></div>

```java
public static final InheritableThreadLocal<User> user 
                   = new InheritableThreadLocal<>() {

   @Override
   protected User initialValue() { 
      return new User("anonymous"); 
   }

   @Override
   protected User childValue(User parentValue) { 
      return new User(parentValue.getId()); 
   }
};
```

위 변경 사항을 통해 상속 가능한 스레드 로컬 맵이 복제될 때 자식에 연관된 값은 childValue(..) 메소드를 사용하여 부모 값이 전달되어 설정됩니다. 상속 가능한 스레드 로컬마다 새로운 객체가 생성되므로, User 객체가 부모 및 자식 스레드 간에 공유되지 않습니다. 이 변경으로 스레드 안전성으로 돌아가지만 User 객체에는 읽기 전용으로 액세스할 수 있게됩니다(사실상 복사본을 생성함).

다시 말하지만, 다이어그램 형식으로 상속 가능한 스레드 로컬을 시각화하면, 이와 같이 보일 것입니다. 이제 부모와 자식 스레드의 사용자가 서로 다른 User 객체를 가리키는 것을 명확히 알 수 있습니다.

![Java Thread Locals 동작 방식](/assets/img/2024-05-23-HowdoJavaThreadLocalswork_3.png)
```

<div class="content-ad"></div>

자바 스레드 지역 변수에 대한 좋은 이해를 얻을 수 있기를 바랍니다. 

이 게시물이 도움이 되었다면 지원을 표시하기 위해 클로버 아이콘 👏을 몇 번 클릭해 주세요. 읽어 주셔서 감사합니다!