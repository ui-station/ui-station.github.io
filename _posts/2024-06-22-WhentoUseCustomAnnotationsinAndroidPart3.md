---
title: "안드로이드에서 커스텀 애노테이션을 사용해야 할 때 Part 3"
description: ""
coverImage: "/assets/img/2024-06-22-WhentoUseCustomAnnotationsinAndroidPart3_0.png"
date: 2024-06-22 22:41
ogImage: 
  url: /assets/img/2024-06-22-WhentoUseCustomAnnotationsinAndroidPart3_0.png
tag: Tech
originalTitle: "When to Use Custom Annotations in Android: Part 3"
link: "https://medium.com/@sandeepkella23/when-to-use-custom-annotations-in-android-part-3-198b533f00df"
---


![이미지](/assets/img/2024-06-22-WhentoUseCustomAnnotationsinAndroidPart3_0.png)

친구야! 안드로이드에서 사용자 정의 주석을 더 멋지고 고급스럽게 활용해보자. 커피 한 잔 마시면서 아이디어를 얘기하는 것처럼 재미있는 예제로 안내해 드릴게요.

# 4. 자동화된 문서화

만약 코드가 스스로 문서를 작성할 수 있다면 어떨까요! 주석이 그렇게 도와줄 수 있어요. 메서드가 무엇을 하는지 설명하는 주석을 만들고 해당 주석을 사용하여 문서를 생성해보세요.

<div class="content-ad"></div>

먼저, 우리는 주석을 정의합니다:

```kotlin
@Target(AnnotationTarget.FUNCTION)
@Retention(AnnotationRetention.RUNTIME)
annotation class Doc(val description: String)
```

그런 다음, 이 주석을 우리의 클래스에서 사용합니다:

```kotlin
class DocumentedClass {

    @Doc(description = "이 메서드는 무거운 계산을 수행합니다.")
    fun heavyComputation() {
        Thread.sleep(1000)
        println("무거운 계산 완료!")
    }

    @Doc(description = "이 메서드는 가벼운 계산을 수행합니다.")
    fun lightComputation() {
        Thread.sleep(500)
        println("가벼운 계산 완료!")
    }
}
```

<div class="content-ad"></div>

이제, 이러한 주석을 기반으로 문서를 생성하는 프로세서를 만들어 봅시다:

```js
object DocumentationGenerator {

    fun generateDocumentation(obj: Any) {
        val methods = obj::class.java.declaredMethods
        for (method in methods) {
            method.getAnnotation(Doc::class.java)?.let {
                println("${method.name}: ${it.description}")
            }
        }
    }
}

fun main() {
    val documentedClass = DocumentedClass()
    DocumentationGenerator.generateDocumentation(documentedClass)
}
```

이 코드를 실행하면, 주석이 달린 메소드의 설명을 출력합니다. 와우! 자동화된 문서를 얻었습니다.

# 5. 런타임 처리

<div class="content-ad"></div>

앱에서 기능을 동적으로 전환하고 싶다면 사용자 지정 주석을 활용하여 쉽게 관리할 수 있습니다.

기능 토글을 위한 주석을 정의하세요:

```kotlin
@Target(AnnotationTarget.FUNCTION)
@Retention(AnnotationRetention.RUNTIME)
annotation class FeatureToggle(val featureName: String)
```

이를 클래스 내에서 사용하여 메서드를 표시하세요:

<div class="content-ad"></div>

```kotlin
class FeatureClass {

    @FeatureToggle("FeatureA")
    fun featureAMethod() {
        println("Feature A is enabled!")
    }

    @FeatureToggle("FeatureB")
    fun featureBMethod() {
        println("Feature B is enabled!")
    }
}
```

이러한 주석을 처리하는 프로세서를 생성합니다:

```kotlin
object FeatureToggleProcessor {

    private val enabledFeatures = setOf("FeatureA")

    fun processFeatures(obj: Any) {
        val methods = obj::class.java.declaredMethods
        for (method in methods) {
            method.getAnnotation(FeatureToggle::class.java)?.let {
                if (enabledFeatures.contains(it.featureName)) {
                    method.invoke(obj)
                } else {
                    println("Feature ${it.featureName} is disabled")
                }
            }
        }
    }
}

fun main() {
    val featureClass = FeatureClass()
    FeatureToggleProcessor.processFeatures(featureClass)
}
```

이 설정으로 핵심 로직을 수정하지 않고 기능을 동적으로 활성화 또는 비활성화할 수 있습니다. 멋지지 않나요?


<div class="content-ad"></div>

# 6. 다양한 사용 사례 다루기

사용자 지정 어노테이션은 응용 프로그램에서 권한 및 역할을 처리하는 데도 효과적입니다. 역할 기반 액세스 제어를 위한 어노테이션을 만들어 보겠습니다.

어노테이션 정의:

```js
@Target(AnnotationTarget.FUNCTION)
@Retention(AnnotationRetention.RUNTIME)
annotation class RequiresRole(val role: String)
```  

<div class="content-ad"></div>

필요한 역할에 주석을 달아주세요:

```js
class RoleClass {

    @RequiresRole("Admin")
    fun adminTask() {
        println("Admin task executed!")
    }

    @RequiresRole("User")
    fun userTask() {
        println("User task executed!")
    }
}
```

권한을 확인하는 역할 프로세서를 생성해보세요:

```js
object RoleProcessor {

    private val currentRole = "Admin"

    fun processRoles(obj: Any) {
        val methods = obj::class.java.declaredMethods
        for (method in methods) {
            method.getAnnotation(RequiresRole::class.java)?.let {
                if (currentRole == it.role) {
                    method.invoke(obj)
                } else {
                    println("Access denied for ${it.role}")
                }
            }
        }
    }
}

fun main() {
    val roleClass = RoleClass()
    RoleProcessor.processRoles(roleClass)
}
```

<div class="content-ad"></div>

이제 사용자가 올바른 역할을 가지고 있을 때만 귀하의 메서드가 실행됩니다. 더 이상 코드 전체에 흩어진 역할 확인 코드가 없습니다!

# 마무리

자, 안드로이드에서 커스텀 어노테이션에 대한 고급이면서도 매우 유용한 케이스들을 다뤘습니다. 자동 문서화 생성 및 런타임에서의 기능 토글부터 역할 기반 액세스 관리까지, 커스텀 어노테이션을 사용하면 코드가 더 깔끔하고 유지보수하기 쉬우며 더 재미있게 작업할 수 있습니다.

더 궁금한 사항이 있거나 다른 멋진 주제에 대해 알아보고 싶다면 언제든지 말씀해주세요. 즐거운 코딩 하시고, 계속해서 어노테이션을 즐기세요! 😊

<div class="content-ad"></div>

4를 어떻게 도와드릴까요?