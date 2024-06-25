---
title: "2024년을 위한 Kotlin Mobile의 최고의 10가지 클린 코드 팁"
description: ""
coverImage: "/assets/img/2024-06-19-MyTop10CleanCodeTipsforKotlinMobilein2024_0.png"
date: 2024-06-19 10:36
ogImage:
  url: /assets/img/2024-06-19-MyTop10CleanCodeTipsforKotlinMobilein2024_0.png
tag: Tech
originalTitle: "My Top 10 Clean Code Tips for Kotlin Mobile in 2024"
link: "https://medium.com/@sporentusjourney/my-top-10-clean-code-tips-for-kotlin-development-in-2024-e9639cd971cf"
---

<img src="/assets/img/2024-06-19-MyTop10CleanCodeTipsforKotlinMobilein2024_0.png" />

당신의 코드에 즉시 적용할 수 있는 팁입니다.

요즘에는 '클린 코드'가 좋은 프로그래밍 관행의 표식이자 동시에 인기 있는 유행어가 되었습니다. 수많은 지침과 잘 지어진 원칙들이 존재하는데, 때로는 서로 모순될 수도 있습니다. 예를 들어 SOLID 원칙을 따르면 테스트하기 쉬운 코드를 만들 수 있지만, 그것들을 지나치게 엄격하게 사용하면 코드가 너무 많은 작은 클래스로 나뉘어져 가독성이 떨어지고 확장 및 유지 보수가 어려워질 수 있습니다.

싱클레어 베이직부터 파스칼, 델파이, 얼랭을 거쳐 모바일 애플리케이션을 위해 Objective-C와 Java, 그리고 이제 Kotlin과 Swift로 이어지는 애플리케이션 개발의 오랜 이야기를 가지고 있습니다. 제가 진로 중 최근에 일한 여러 기술들을 살펴보면, 오늘부터 당신이 코드의 품질을 향상시키기 위해 사용할 수 있는 여러 기술을 채택했습니다.

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

위의 모든 원칙과 팁 중에서 꼭 기억해야 할 중요한 한 가지가 있습니다:

이것은 코드를 완벽하게 정리했지만 이를 출시하지 않았다면 모든 노력이 물거품이 될 수 있다는 것을 의미합니다.

이를 명심하고 앱을 개발하고 출시해 주세요. 계속해서 코드를 검토하고 재구성하며 개선해 나가세요. 다음 10가지 팁은 앱을 출시하는 데만 도움을 주는 것이 아니라 끊임없는 다듬기에 갇히지 않도록 도와줍니다.

# 1. 프로젝트 파일 구성하기

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

프로젝트의 파일 구조에 영향을 미치는 여러 가지 널리 논의된 코드 아키텍처가 있습니다. 가장 널리 인식되는 것 중 하나는 Clean Architecture입니다. 그러나 이는 Hexagonal이나 Onion과 같은 다른 아키텍처가 나쁘다는 것을 의미하는 것은 아닙니다. 기존의 아키텍처는 대부분의 개발자가 직면하는 일반적인 문제를 해결하지만, 아키텍처의 선택은 개발 요구 사항에 적합한 것과 팀이 효과적으로 지원할 수 있는 것에 따라 다릅니다.

주의해야 할 점은 일관성 있는 명명 규칙을 준수하고 파일 이름의 길이에 대해 두려워하지 말아야 합니다.

일관된 명명 패턴 채택이 중요합니다. 이름은 파일의 내용과 목적을 즉각적으로 파악할 수 있어야 합니다. 예를 들어:

- NetworkManager, NetworkHelper: 네트워크 관련 작업을 위한 이름입니다.
- MainScreen, OtherScreen: 구성 가능한 화면에 대한 이름입니다(또는 old-style의 MainActivity, MainFragment).
- MainViewModel, MenuViewModel: ViewModel 클래스에 대한 이름입니다.

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

이러한 명명 규칙은 MainActivity, MainScreen 및 MainViewModel과 같은 다른 구성 요소 간의 관계를 손쉽게 추적할 수 있도록 합니다. 서로 다른 패키지에 위치해 있더라도요.

## 효과적인 패키지 구조

한 번 더 강조하지만, 기존 아키텍처에서 선택할 수 있지만, 절대적으로 그 중 어느 것도 법이라는 것을 기억하세요.

제 프로젝트에서는 내게 쉽게 탐색할 수 있는 방식으로 구조를 사용했습니다. UI 패키지는 계층적으로 구성되어 있습니다:

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

![이미지](/assets/img/2024-06-19-MyTop10CleanCodeTipsforKotlinMobilein2024_1.png)

이렇게 관련된 구성 요소가 함께 그룹화되어 있으면 코드베이스를 빠르게 탐색하고 이해하기 쉬워집니다.

# 2. 하나, 둘… 리팩토링!

즉, 세 번 까지의 규칙입니다. 이 규칙은 코드를 리팩토링할 시간을 이해하는 데 도움이 됩니다.

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

- 무언가를 처음으로 코딩할 때는 그냥 시작하세요.
- 두 번째로 반복할 때는 주의 깊게 유지하세요.
- 세 번째는? 멈추고 리팩터링하세요.

이 규칙은 조기 추상화와 코드 중복의 함정을 피하면서 유연한 아키텍처를 구축하는 데 도움이 됩니다. 이 접근 방식은 지나치게 복잡한 아키텍처로 이어질 수 있는 과도한 엔지니어링이나 부족한 엔지니어링으로 해결책을 개발하는 과정에서 발생할 수 있는 문제점을 피하면서 유지보수 가능한 코드를 개발하는 데 도움이 됩니다.

## 3. 깊은 중첩 피하기: 화살표 머리모양 안티-패턴 대응

깊은 중첩은 여러 개의 반복문 및 조건문과 같은 제어 구조의 계층이 서로 중첩되어 있을 때 코드의 가독성과 명확성을 상당히 떨어뜨릴 수 있습니다. 코드를 이해하고 유지하는 것을 더 어렵게 만들 수 있습니다.

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

깊게 중첩된 예제는 복잡성을 보여줍니다:

```js
fun getUserRole(userInput: UserInput): String? {
    if (userInput.login.isNotEmpty()) {
        if (userInput.password.isNotEmpty()) {
            if (isUserExist(userInput.login)) {
                if (isPasswordValid(userInput.password)) {
                    return userRole(userInput.login)
                } else {
                    return UserRole.Unknown
                }
            }
            return UserRole.Unknown
        }
        return UserRole.Unknown
    }
    return UserRole.Unknown
}
```

이 코드 구조는 각 중첩된 레이어를 주의 깊게 탐색해야 하기 때문에 이해하고 수정하기 어렵습니다. 또한 종료 로직 및 반환 값과 관련된 버그 발생 가능성이 증가합니다.

조기 종료를 사용하여 코드를 리팩토링하면 구조를 단순화하고 가독성을 높일 수 있습니다:

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
getUserRole 함수는 사용자 입력을 받아와서 해당 사용자의 역할을 반환하는 함수입니다. 입력된 사용자 정보가 비어있는 경우 Unknown 역할을 반환하고, 사용자가 존재하지 않는 경우 NotExists 역할을 반환합니다. 입력된 비밀번호가 유효하지 않은 경우 Unauthorized 역할을 반환합니다. 그 외의 경우에는 해당 사용자의 역할을 반환합니다.

코드를 리팩토링하여 불필요한 복잡성을 제거하고 코드를 더 직관적이고 이해하기 쉽게 만들었습니다. (최적화가 필요한 미려한 코드는 아니지만, 이제 명확하게 보입니다).

대안으로 Kotlin의 when 구문을 사용할 수도 있습니다:

fun getUserRole(userInput: UserInput): String? {
    return when {
        userInput.login.isEmpty() -> UserRole.Unknown
        userInput.password.isEmpty() -> UserRole.Unknown
        !isUserExist(userInput.login) -> UserRole.NotExists
        !isPasswordValid(userInput.password) -> UserRole.Unauthorized
        else -> userRole(userInput.login)
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

깊은 중첩을 피하고 초기 종료 및 when 문과 같은 전략을 활용함으로써 코드를 더 접근 가능하고 유지 보수성이 높아지며 작업하기 쉬워집니다. 이 방식은 코드의 전반적인 가독성을 향상시키는 구조를 제공합니다. 또한 어떤 코드가 리팩토링이 필요한지 감지하는 데 도움이 될 수 있습니다.

# 4. 코드 설명서 작성

이것은 논란이 많은 주제입니다. 많은 사람들이 "코드는 유머와 같다. 설명이 필요하면 나쁜 것"이라고 말하며 일반적으로 주석이 바람직하지 않다고 생각하기 시작합니다.

그러나 몇 년 동안 유지 보수 가능한 코드를 제공하려면 문서화해야 합니다. 그러나 각 줄마다 주석을 다는 것은 좋은 방법이 아닙니다 :)

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

## 주의해야 할 때 코멘트를 작성해야 하는 경우

- 메소드나 프로퍼티의 이름이 명확하지 않은 경우에 문서화합니다. 복잡한 알고리즘이나 복잡한 로직을 설명합니다.
- 타사 라이브러리와의 통합 지점에 대해 문서화합니다. Retrofit을 문서화할 필요는 없지만, 인터셉터를 작성한다면 설명적인 이름을 지어 간단히 무엇을 하는지와 언제 추가해야 하는지에 대한 간단한 설명을 추가하는 것이 좋습니다.
- 때로는 코드에 특정 문제에 대한 비표준적인 해결책이나 회피책이 포함될 수 있습니다. 이슈 트래커나 버그 보고서에서 작업에 대한 링크를 추가하는 것이 좋을 수 있습니다.
- 코드 블록의 목적이나 의도를 설명하는 간략한 코멘트가 특히 크거나 복잡한 함수에서 좋을 수 있습니다.

가끔은 "기다려! 우리는 깔끔한 코드에 대해 이야기하고 있어. 함수가 복잡하거나 이해하기 어렵다는 것은 의도한 것이 아니잖아"라고 말할 수 있습니다.

깔끔한 코드가 중요하지만, 코드 명확성에 대한 절대적인 해결책은 아니며, 특히 데이터 암호화나 사용자 지정 하드웨어 통합과 같이 복잡한 시스템에서는 더욱 그렇습니다. 이러한 경우에 잘 쓰여진 코드조차 밀집하고 완전히 자명하지 않을 수 있습니다. 균형을 잡는 것이 중요합니다 - 코드를 보완하기 위해 코멘트를 사용하여 코드만으로는 충분하지 않은 부분에 통찰력을 제공합니다. 코드를 가능한 한 읽기 쉽게 만드는 데 중점을 둘 뿐만 아니라 복잡한 섹션을 명확하게 해주기 위해 코멘트가 필요한 경우를 인정하는 것입니다.

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

예를 들어:

```kotlin
data class HardwareConnection(
    val ip: String,
    val port: Int
) {
    /**
     * 명령을 보낸 후 연결을 닫습니다. 연결이 닫히지 않은 경우,
     * 디바이스는 마지막 명령을 보낸 후 60초 동안 차단됩니다.
     * 이 차단은 디바이스 측에서 발생합니다.
     */
    fun sendCommand(command: String) {
        val connection = HardwareSDK.connect(ip, port)
        connection.sendCommand(command)
        connection.close()
    }
}
```

그러나 너무 많이 설명하는 것은 코드를 혼란스럽게 만들고 가독성을 떨어뜨릴 수 있습니다. 명백한 문장에 주석을 추가하거나 기본적인 작업을 설명할 필요는 없습니다. 주석은 가치를 더해야 하며 이미 명확한 함수와 변수명에서 알 수 있는 내용을 반복해서 설명하는 것은 피해야 합니다. 각 주석이 코드를 압도하지 않으면서 명확한 목적을 제공하는 적절한 지점을 찾는 것이 중요합니다.

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

```kotlin
// 사용자의 나이를 저장하는 변수를 정의합니다
var userAge: Int = 25
```

또는

```kotlin
class UserManager(private val database: Database) {
    fun createUser(user: User) {
        // 사용자를 데이터베이스에 저장합니다
        database.save(user)
    }

    fun deleteUser(userId: String) {
        // 사용자를 데이터베이스에서 삭제합니다
        database.delete(userId)
    }
}
```

## 5. 전역 상태와 싱글톤의 한계를 설정하세요

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

앱을 개발할 때는 종단 없이 존재하는 값을 또는 인스턴스를 관리해야 하는 경우가 많습니다.

이 데이터를 관리하기 위해 싱글톤을 사용하는 것은 간단해 보일 수 있습니다. 그러나 명확한 코드는 사람이 이해하기 쉽다는 것뿐만이 아니라 빌드 시스템과 테스트에도 직관적이어야 합니다.

전역 상태는 여러 문제를 발생시킬 수 있습니다:

예측 불가능한 동작: 예를 들어, 컨텍스트 객체나 런타임 권한을 저장하는 경우, 이 값들은 시간이 지남에 따라 변경될 수 있습니다. 전역 상태는 이러한 상태를 항상 최신 상태로 유지할 것을 보장하지 않습니다.

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

복잡한 테스트: 전역 변수의 복잡한 상태를 모방하는 것은 어려울 수 있고, 테스트들이 서로 간섭할 수 있습니다. 이는 신뢰할 수 없거나 잘못된 테스트 결과로 이어질 수 있습니다.

추적이 어려움: 전역 상태가 언제 어떻게 변경되었는지 추적하기 어려울 수 있으며, 이는 디버깅을 복잡하게 만들 수 있습니다.

동시 액세스 문제: 이는 디버깅과 수정이 어려운 충돌을 일으킬 수 있습니다.

재생성/재초기화 문제: 안드로이드가 앱을 중지하고 다시 시작하기로 결정하는 경우, 전역 상태의 모든 변수가 제대로 초기화될 것을 보장할 수 없습니다. 이는 Kotlin의 비-널러블 필드에서 심지어 Null Pointer Exception이 발생할 수 있어 디버깅이 매우 어려워질 수 있습니다.

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

싱글톤 또는 글로벌 객체를 사용하는 대신, 앱의 구성 요소 사이에 클래스 인스턴스를 전달하거나 데이터를 직접 저장하지 않고 특정 소스에서 데이터를 검색하는 도우미/서비스 클래스를 사용하는 것을 고려해보세요. Koin 또는 Hilt와 같은 의존성 주입 프레임워크를 사용하면 객체 관리와 생성을 간편화할 수 있습니다.

가끔은 싱글톤을 완전히 피하는 것이 불가능할 수 있습니다. 이런 경우가 있을 때마다 데이터를 분석하여 실제로 전역 상태나 싱글톤이 필요한지 확인하세요. 예를 들어:

- 데이터가 특정 액티비티, 프래그먼트, 화면 또는 뷰 모델 라이프사이클 내에서만 필요한 경우 해당 라이프사이클에 연결해야 합니다.
- 앱 상태를 재시작 중에 유지해야 하는 경우, Helper/Service 및 공유 프레퍼런스 또는 데이터베이스의 조합을 고려해보세요.
- 백그라운드 프로세스로 주기적으로 새 데이터를 백엔드에서 가져와 UI로 전달해야 하는 경우, 이 데이터를 데이터베이스에 저장하고 콜드 플로 또는 핫 플로에 연결하여 업데이트가 도착할 때마다 업데이트를 전달할 수 있습니다. 이와 유사한 기법은 애플리케이션 전반의 이벤트에도 적용할 수 있습니다.

# 6. 복잡한 한 줄 코드 피하기

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

표 태그는 공간을 절약하거나 코드를 이해하기 어렵게 만드는 것만이 아니라요. 예를 들어 아래와 같이요.

```js
val result = someList.filter { it.hasChildren }.map { it.children }.takeIf { it.size() > 2 } ?: listOf(0)
```

각 작업을 개별적인 단계로 분리하면 코드를 더 읽기 쉽게 만들 수 있어요.

```js
val result = someColdFlowOrList
    .filter { it.hasChildren }
    .map { it.children }
    .takeIf { it.size() > 2 }
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

# 7. `it` 대신 값에 이름 사용하기

많은 경우 이름을 생략할 수 있지만, 여러 겹의 레이어가 있는 경우 클로저 매개변수에 이름을 지정하는 것이 좋습니다.

```kotlin
data class User(
    val name: String,
    val address: Address? = null,
    val age: Int? = null
)

data class Address(val city: String)

val user = User("Alex", Address("Stockholm"))

user.let { user ->
    user.address?.let { address ->
        println("City: ${address.city}")
    }
}
```

이렇게 하면 더 읽기 쉬워집니다.

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

```kotlin
user.let { user ->
    user.address?.let { address ->
        println("도시: ${address.city}")
    }
}
```

네, 안드로이드 스튜디오는 이러한 간단한 경우를 식별하는 데 도움을 줍니다. 그러나 이 경우에는 아무 말도 하지 않습니다.

```kotlin
user.let {
    println("이름: ${it.name}")
    it.address?.let {
        println("도시: ${it.city}")
    }
    it.age?.let {
        println("나이: ${it}")
    }
}
```

비교해보세요

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

```kotlin
user?.let { user ->
    println("이름: ${user.name}")
    user.address?.let { userAddress ->
        println("도시: ${userAddress.city}")
    }
    user.age?.let { userAge ->
        println("나이: $userAge")
    }
}
```

# 8. 해킹 스타일 또는 젠 코드를 피하세요

예를 들어, 내 프로젝트 중 하나에 이렇게 썼습니다. 저에게는 꽤 직관적이지만, 동료들이 이해하는 데 어려움을 겪습니다. 여기서 필터는 무엇을 하는 건가요? 동시에 수정될 가능성이 있나요?

```kotlin
filters
    .indexOfFirst { it.id == id }
    .takeIf { it != -1 }
    ?.let { index ->
        filters[index] = filters[index].copy(
            isActive = !filters[index].isActive
        )
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

덜 중첩되어 있어서 읽기가 더 쉬워질 거예요.

```js
val index = filters.indexOfFirst { it.id == id }
if (index != -1) {
    filters[index] = filters[index].copy(isActive = !filters[index].isActive)
}
```

병렬로 수정할 수 있는지 여부에 대해 의문이 없다는 걸 보여줘요.

# 9. IfNeeded 및 Maybe 함수 피하기

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

이러한 유형의 함수는 조건부 논리를 내부에 캡슐화하여 일부 부작용을 초래합니다.

보통 이러한 함수들은 데이터 일관성이 여러 비동기 데이터 스트림에 의존하는 복잡한 시나리오에서 나타나며, 코드 중복을 피하거나 논리 분기를 단순화하기 위해 일부 논리를 캡슐화해야 할 때 사용됩니다. 반면에 이러한 함수들은 코드를 예측하기 어렵게 만들 수 있으며 불필요한 종속성을 도입할 수도 있습니다.

예를 들어:

```js
class User(
    val name: String,
    val email: String,
    val age: Int,
    val emailMarketingEnabled: Boolean
)

class UserManager {
    private var user: User = User.empty()
    private val parentalControlManager = ParentalControlManager()
    private val marketingManager = MarketingManager()
    private val database = Database()

    fun updateUserProfileIfNeeded() {
        if (user.age < 18) {
            parentalControlManager.checkPermissions(user) {
                updateUser(user.copy(emailMarketingEnabled = true))
            }
        } else {
            marketingManager.sendPromotionalEmail(user)
            updateUser(user.copy(emailMarketingEnabled = true))
        }
    }

    fun updateUser(user: User) {
        database.update(user)
    }
}

class MainViewModel(private val userManager: UserManager) : ViewModel() {
// ...
   fun onActivatePromotionEmails() {
        userManager.updateUserProfileIfNeeded()
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

이 예시에서는 고전적인 논리 함정이 발생했습니다. 앱은 사용자를 업데이트해야 하지만 특정 경우에만 그렇게 해야 합니다. 여기서는 미성년자에 대한 경우입니다. 사용자가 만 18세 미만이라면 앱은 그들의 권한을 확인해야 합니다. 또한 사용자를 업데이트할 수 있는 성공적인 콜백이 있는 것으로 가정할 수 있습니다.

저는 이를 많이 보았습니다. 구성과 중재자 패턴 사이의 무언가가 있습니다. 모든 것이 한 곳에 모여 편리해 보입니다. 그러나 이러한 코드는 많은 문제를 발생시킵니다:

- updateUserProfileIfNeeded 메서드는 너무 많은 것을 담당합니다: 사용자의 연령 확인, 부모 관리 체크 처리, 사용자 프로필 업데이트, 마케팅 매니저와 상호 작용합니다.
- 사용자의 연령에 따라 다른 작업을 수행하는데, 이는 메서드의 이름이나 서명에서 즉시 파악되지 않습니다. 이 숨겨진 논리로 인해 메서드가 예측하기 어렵고 이해하기 어려워집니다.
- 사용자 객체가 연령 확인의 부작용으로 수정됩니다. 이와 같은 부작용은 버그를 발생시키고 코드를 유지보수하기 어렵게 만들 수 있습니다.
- updateUserProfileIfNeeded 메서드는 사용자 객체의 현재 상태에 의존합니다. 이는 전역 상태 또는 공유 상태의 예시로, 특히 다중 스레드 환경에서 예측할 수 없는 동작을 초래할 수 있어 시스템이 더 오류가 발생하기 쉬워집니다.
- 해당 메서드는 ParentalControlManager 및 MarketingManager와 강하게 결합되어 있습니다. 이 강한 결합은 UserManager 클래스를 테스트하고 유지보수하기 어렵게 만듭니다.
- updateUserProfileIfNeeded 메서드의 이름은 실제 기능을 명확히 전달하지 않습니다.

작은 조치로는 함수를 작은 함수로 분리하거나 관리자를 매개변수로 전달하는 것과 같은 것으로 문제를 해결할 수 없습니다. 이 클래스는 완전히 리팩토링되어 업데이트 기능만 포함하도록 해야 합니다. 권한 확인 및 마케팅 작업은 UserManager 바깥으로 이동되어야 합니다.

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
class ParentalControlManager {
    fun canUpdateProfile(user: User): Boolean {
        return user.age < 18 && hasPermissions(user)
    }
}

class UserManager {
    private var user: User = User.empty()
    private val database = Database()

    fun updateUser(user: User) {
        database.update(user)
    }

    fun activatePromotionEmails(user: User) {
        updateUser(
            user.copy(emailMarketingEnabled = true)
        )
    }
}

class MainViewModel(
    private val parentalControlManager : ParentalControlManager,
    private val marketingManager : MarketingManager,
    private val userManager : UserManager
    ) : ViewModel() {

// ...
   fun onActivatePromotionEmails() {
        if (parentalControlManager.canUpdateProfile(user)) {
              userManager.activePromotionEmails(user)
              marketingManager.sendPromotionalEmail(user)
        }
    }
}
```

그래서 여기에서 발생한 일들입니다:

- 부모 관리 관련 로직은 이제 ParentalControlManager 내로 캡슐화되었습니다.
- UserManager는 이제 사용자 업데이트 및 프로모션 이메일 활성화와 같은 사용자 관련 작업에만 초점을 맞추어 일관성이 높아졌습니다.
- ParentalControlManager와 MarketingManager의 로직을 UserManager에서 분리함으로써 결합도가 줄었습니다. UserManager는 더 이상 ParentalControlManager와 MarketingManager의 동작에 직접 의존하지 않습니다.
- canUpdateProfile 및 activatePromotionEmails와 같은 메서드 이름은 더 서술적이며 기능을 정확하게 반영합니다. 이는 가독성과 유지 보수성을 향상시킵니다.
- MainViewModel은 이제 parentalControlManager.canUpdateProfile(user)의 결과에 따라 프로필 업데이트가 가능한지 명확하게 확인합니다. 이로써 로직 흐름이 더 명확하고 예측 가능해집니다.
- 새로운 구조는 UserManager 내에서 부수 효과로 사용자 상태를 수정하는 일을 피합니다. 대신 사용자에 대한 변경사항이 명시적이고 투명하게 이루어집니다.
- ParentalControlManager, MarketingManager 및 UserManager는 더 이상 서로 의존하지 않으며 이러한 모든 객체가 MainViewModel에 전달됩니다. 이는 테스트를 개선하고 의존성 주입을 사용할 수 있도록 분명한 방식으로 객체 초기화를 허용하는 좋은 사례입니다.

# 10. 잘 자세요

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

더 나은 코드를 작성하기 위한 주요 팁은 휴식을 취하고 자신을 이해하는 것입니다. 충분한 휴식을 취하지 않으면 복잡한 코드 솔루션을 추적하기 어렵거나 작업을 향상시키기 위한 열망이 떨어질 수 있습니다. 업무-생활 균형을 유지하고 매일 7~8시간의 수면을 목표로 삼으며, 마음을 쉬게 해주는 활동을 할 것을 권장합니다. 화면이 아닌 좋은 책을 읽는 것도 좋은 방법입니다. 기억하세요, 당신의 정신적 건강은 기술적 기량만큼 중요합니다. 충분한 휴식과 건강한 상태의 개발자일수록 효율적이고 오류가 적으며 유지보수가 쉬운 코드를 작성할 가능성이 높습니다.
