---
title: "안드로이드 유닛 테스트 101 초보자를 위한 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-UnitTestingforAndroidABeginnersGuide_0.png"
date: 2024-06-23 20:57
ogImage: 
  url: /assets/img/2024-06-23-UnitTestingforAndroidABeginnersGuide_0.png
tag: Tech
originalTitle: "Unit Testing for Android : A Beginner’s Guide"
link: "https://medium.com/@asadleo1995/unit-testing-for-android-a-beginners-guide-f8681cba3c22"
---


유닛 테스트는 소프트웨어 개발의 중요한 단계입니다. 이것은 Test Driven Development (TDD)라고 불리는 개발 패러다임을 가져옵니다. 이러한 테스트는 일반적으로 애플리케이션의 비즈니스 로직을 테스트합니다.

# 왜 우리는 유닛 테스트를 작성할까요?

- 우리는 실수를 할 수 있습니다.
- 우리의 코드가 작동되기를 원합니다.
- 더 빠르게 개발하고 더 많은 확신과 더 적은 회귀를 가지기를 원합니다.

안드로이드 및 일반적으로 다양한 모바일 플랫폼에서 앱 테스트는 어려울 수 있습니다. 유닛 테스트를 구현하고 테스트 주도 개발(TDD)의 원칙을 따르는 것은 종종 직관에 어긋날 수 있습니다. 그럼에도 불구하고 테스트는 중요하며 당연하게 여기거나 무시해서는 안 됩니다.

<div class="content-ad"></div>

한 번 유닛 테스트의 기본 사항으로 이동해 보죠. 안드로이드에서 유당 테스트라고 하면 떠오르는 몇 가지 기본 사항부터 시작해보겠습니다.

# 패키지 구조

새로운 안드로이드 프로젝트를 생성하면 기본적으로 다음 세 가지 소스 세트를 얻게 됩니다. 이들은 다음과 같습니다:

![Unit Testing for Android - A Beginner's Guide](/assets/img/2024-06-23-UnitTestingforAndroidABeginnersGuide_0.png)

<div class="content-ad"></div>

- main: 앱 코드가 포함되어 있습니다.
- androidTest: 인스트루먼티드 테스트로 알려진 테스트가 포함되어 있습니다.
- test: 로컬 테스트로 알려진 테스트가 포함되어 있습니다.

로컬 테스트와 인스트루먼티드 테스트의 차이점은 실행되는 방식에 있습니다.

# 로컬 테스트 (test 소스 세트)

이러한 테스트는 개발 컴퓨터의 JVM에서 로컬로 실행되며 에뮬레이터나 물리적 장치가 필요하지 않습니다. 이로 인해 실행 속도가 빠르지만, 신뢰성은 낮아 실제와 다르게 동작할 수 있습니다.

<div class="content-ad"></div>

# Instrumented tests (androidTest source set)

이러한 테스트는 실제 또는 에뮬레이션된 Android 장치에서 실행되므로 실제 세계에서 발생할 사항을 반영하지만 훨씬 느립니다.

# Test runner

테스트 실행기는 테스트를 실행하는 JUnit 구성 요소입니다. 테스트 실행기가 없으면 테스트가 실행되지 않습니다. JUnit에서는 자동으로 제공되는 기본 테스트 실행기가 있습니다.

<div class="content-ad"></div>

```js
Android Studio를 사용하면 클래스를 테스트하는 테스트를 생성할 수 있는 도구를 제공합니다. 테스트할 클래스를 마우스 오른쪽 버튼으로 클릭하고 Generate ` Test를 선택하세요.

<img src="/assets/img/2024-06-23-UnitTestingforAndroidABeginnersGuide_1.png" />

Test를 클릭한 후에는 Test 클래스를 생성할 수 있습니다.
```

<div class="content-ad"></div>

![이미지](/assets/img/2024-06-23-UnitTestingforAndroidABeginnersGuide_2.png)

테스트 클래스에서 사용하는 몇 가지 주석을 살펴봅시다.

- @Test: 이 주석은 메서드를 테스트 케이스로 표시하는 데 사용됩니다. @Test 주석은 Java 컴파일러에게 클래스를 컴파일할 때 메서드를 테스트 케이스로 실행하도록 알려줍니다.
- @Before: 이 주석은 메서드를 설정 메서드로 표시하는 데 사용됩니다. @Before 주석은 Java 컴파일러에게 클래스의 각 테스트 케이스 전에 메서드를 실행하도록 알려줍니다. 각 테스트 케이스가 실행되기 전에 테스트 환경을 설정하는 데 유용합니다.
- @After: 이 주석은 메서드를 소멸 메서드로 표시하는 데 사용됩니다. @After 주석은 Java 컴파일러에게 클래스의 각 테스트 케이스 후에 메서드를 실행하도록 알려줍니다. 각 테스트 케이스가 실행된 후 테스트 환경을 정리하는 데 유용합니다.
- @Ignore: 이 주석은 메서드를 무시된 테스트 케이스로 표시하는 데 사용됩니다. @Ignore 주석은 Java 컴파일러에게 메서드를 테스트 케이스로 실행하지 말도록 알려줍니다. 아직 실행할 준비가 안 된 테스트 케이스를 일시적으로 무시하는 데 유용합니다.

단언문은 테스트의 핵심입니다. 코드 문장으로, 코드나 앱이 예상대로 작동했는지 확인합니다. 이 경우, 단언문은 assertEquals(4, 2 + 2)로 4가 2 + 2와 같은지를 확인합니다.

<div class="content-ad"></div>

# 테스트 전략

가독성이 좋은 테스트를 작성하는 몇 가지 다른 전략이 있습니다. 방금 작성한 테스트에서 이 두 가지 전략이 모두 보여집니다.

## 주어진 상황, When, Then

테스트의 구조를 생각하는 한 가지 방법은 주어진 상황, When, Then 테스트 니모닉을 따르는 것입니다. 이를 통해 테스트를 세 부분으로 나눌 수 있습니다: 

<div class="content-ad"></div>

- 주어진: 테스트에 필요한 객체 및 앱 상태를 설정하십시오. 이 테스트에서 "주어진" 부분이 무엇인가요.
- 실행: 테스트 중인 객체에 대해 실제로 작업을 수행하십시오.
- 결과: 이 부분은 실제로 작업을 수행할 때 일어나는 일을 확인하는 곳으로, 테스트가 통과했는지 실패했는지 확인합니다. 보통 여러 assert 함수 호출이 포함됩니다.

주의해 주실 점은 "준비, 실행, 확인" (AAA) 테스트 니모닉이 유사한 개념이라는 것입니다.

```js
@Test
fun getUserProfile_givenUserId_returnUser() {
    // 주어진
    val userId = 1
    val user = User(userId,"","TestUser")
    // 실행
    doReturn(Observable.just(user)).`when`(webservice).getMyProfile(userId)
    presenter.getUserProfile(anyString())
    // 결과
    verify(profileView).render(ProfileState.DataState(user))
}
```

# 종속성 구성

<div class="content-ad"></div>

일반적으로 종속성을 추가할 때 구현을 사용합니다. 앱을 세계와 공유할 준비가 되면, 테스트 코드나 앱의 종속성을 APK의 크기를 부풀리지 않는 것이 가장 좋습니다. Gradle 구성을 사용하여 라이브러리가 주 코드 또는 테스트 코드에 포함되어야 하는지 지정할 수 있습니다.

가장 일반적인 구성은 다음과 같습니다:

- implementation—이 종속성은 테스트 소스 세트를 포함한 모든 소스 세트에서 사용할 수 있습니다.
- testImplementation—이 종속성은 테스트 소스 세트에서만 사용할 수 있습니다.
- androidTestImplementation—이 종속성은 androidTest 소스 세트에서만 사용할 수 있습니다.

# 테스트 더블들

<div class="content-ad"></div>

이 문제의 해결책은 리포지토리를 테스트할 때 실제 네트워킹 또는 데이터베이스 코드를 사용하지 말고 대신 테스트 더블을 사용해야 합니다. 테스트 더블은 테스트를 위해 특별히 작성된 클래스의 버전을 말합니다. 이것은 테스트에서 사용되는 클래스의 실제 버전을 대체하는 것을 목적으로 합니다. 이는 스턴트 배우가 위험한 액션을 대신하는 것처럼 테스트 더블이 스턴트에 특화된 배우인 것과 유사합니다.

다음은 일부 테스트 더블의 유형입니다:

## 가짜(Fake)

클래스의 "작동" 구현을 갖는 테스트 더블이지만 테스트에는 적합하지만 프로덕션에는 적합하지 않은 방식으로 구현되어 있습니다.

<div class="content-ad"></div>

## 목

호출된 메소드를 추적하는 테스트 더블(Mock)입니다. 그런 다음 메소드가 올바르게 호출되었는지에 따라 테스트를 통과하거나 실패합니다. 객체를 모의(Mock)하면 해당 클래스의 빈 구현이 생성됩니다.

# 네이밍 규칙

테스트의 이름은 해당 테스트가 무엇을 하는지 이해하는 데 도움이 되어야 합니다. 네이밍 규칙은 다음과 같이 보여야 합니다:

<div class="content-ad"></div>

**subjectUnderTest_actionOrInput_resultState**

- Subject under test is the method or class that is being tested (getUserProfile).
- Next is the action or input (givenUserId).
- Finally you have the expected result (returnUser).

**Pro Tip**

When you have repeated setup code for multiple tests, you can use the @Before annotation to create a setup method and remove repeated code.

<div class="content-ad"></div>

이 기사를 읽어 주셔서 감사합니다. 도움이 되었다면 아래 👏을 클릭해서 기사를 칭찬해주세요. 제게 매우 큰 의미가 됩니다. 댓글이나 트위터, 링크드인을 통해 연락 주시면 감사하겠습니다. 코딩 즐기시고 즐거운 시간 보내세요!

커피 사주세요 ☕