---
title: "코드 리뷰에서 논의하는 것 그만 하고 린트 규칙으로 강제해 보세요"
description: ""
coverImage: "/assets/img/2024-05-20-StopDebatinginCodeReviewsStartEnforcingwithLintRules_0.png"
date: 2024-05-20 17:41
ogImage:
  url: /assets/img/2024-05-20-StopDebatinginCodeReviewsStartEnforcingwithLintRules_0.png
tag: Tech
originalTitle: "Stop Debating in Code Reviews. Start Enforcing with Lint Rules."
link: "https://medium.com/proandroiddev/stop-debating-in-code-reviews-start-enforcing-with-lint-rules-6632c907ea94"
---

## Konsist를 사용하여 아키텍처와 최상의 실천 방법을 단위 테스트로 강화하는 방법

![Code Review Comments](/assets/img/2024-05-20-StopDebatinginCodeReviewsStartEnforcingwithLintRules_0.png)

이제 다음 코드 리뷰 댓글을 살펴보겠습니다:

물론 가장 중요한 것은:

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

안녕하세요! 이것들을 알고 계셨나요? 이것들은 모두 코드 리뷰에서 나타나는 문제들이에요! (네, 이 용어가 실제로 존재합니다. 안타깝게도 이 용어를 만든 사람은 저가 아닙니다.)

코드 스타일, 프로젝트 아키텍처, 또는 일반적으로 건강하고 유지보수가 용이한 코드베이스를 유지하기 위해 따라야 하는 모베스트 프렉티스에 대한 일정 정렬이 필요할 때, 우리는 그것을 강제하는 자동화를 구축해야 합니다. 그리고 그것이 바로 lint rule입니다.

개발자들이 새로운 기능을 개발할 때, 어떻게 진행해야 할지 자신감을 가져야 합니다. 그들은 아키텍처의 각 레이어, 각 클래스의 역할, 그리고 우리가 정립한 규칙을 이해할 수 있어야 합니다. 그리고 새 직원들은 가능한 빨리 코드베이스에 적응하여 헷갈리지 않고 진행할 수 있어야 합니다.

우리 팀과 함께 새로운 사례들을 발견하고 그것에 동의하며 조인하는 과정에서 주로 토론의 여지가 없는 표준들을 강제하는 좋은 프로세스가 이미 마련되어 있기 때문에요. 물론 이 프로세스는 계속해서 발전하며 우리가 시간이 지남에 따라 새로운 모베스트 프렉티스를 발견하고 이에 동의할 때마다 업데이트될 것입니다.

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

# 왜 Konsist를 사용해야 하나요? 이미 린터가 있지 않나요?

맞아요, 린터는 이미 여러 년 동안 사용해 왔어요! 심지어 안드로이드 스튜디오에는 기본으로 내장되어 있어요. 하지만 PSS에서 우리 안드로이드 앱에서 몇 가지 대안을 시도한 후, 하나를 깨달았어요: 모두가 높은 학습 곡선을 가지고 있었고, 아키텍처 규칙을 유지보수 가능한 방식으로 강제할 수 없었기 때문에 코드 리뷰 중에 위반 사항을 발견하는 데 특히 조심해야 했어요. 코드가 읽기 어려웠고, CI/CD 시스템과의 통합은 간단한 작업이 아니었고, 궁극적으로 아무도 정말 사용자 지정 린트 규칙을 작성하고 싶어하지 않았어요.

![image](/assets/img/2024-05-20-StopDebatinginCodeReviewsStartEnforcingwithLintRules_1.png)

다행히도 우리는 마침내 Konsist에 도달했어요. Konsist는 Lint 규칙을 단위 테스트 형식으로 작성하는 주요 원칙을 가진 비교적 새로운 프로젝트에요. 그것이 큰 차이를 만들어 주는 것이죠. 대부분의 개발자들이 이미 단위 테스트를 작성하는 데 익숙하기 때문에, 린트 규칙을 작성하는 데도 빨리 적응하게 될 거에요.

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

노트: 단위 테스트 작성에 어려움을 겪는다면, 포괄적인 단위 테스팅 가이드(Unit Testing Diet)를 확인해 보세요.

# Konsist로 lint 규칙 작성하기

Konsist에서 lint 규칙은 단위 테스트이므로, JUnit 5, JUnit 4 또는 Kotest를 사용하여 테스트 작성과 유사하게 작성할 수 있습니다.

저희 프로젝트에서는 테스트를 Given-When-Then 스타일로 구성하는 것을 선호하므로, lint 규칙에도 Kotest의 Behavior Spec을 사용해 동일한 방식으로 작성하겠습니다:

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

- 주어진 것: 이것은 우리 테스트의 설정입니다. 우리는 리엔트 규칙이 적용되어야 하는 파일이나 클래스를 지정할 것입니다.
- 하는 중: 유닛 테스트에서 이것은 일반적으로 결과를 평가하기 전에 수행되는 작업입니다. 리엔트 규칙은 특정 작업과 관련이 없기 때문에, 이 단계는 선택 사항입니다. 하지만, 우리는 여전히 이전 단계에서 지정한 파일에 대한 추가 필터링을 위해 사용할 수 있습니다 (예: 클래스의 속성, 생성자, 함수 등을 필터링).
- 그러면: 이것은 우리의 단언입니다. 우리는 강제하려는 리엔트 규칙을 위반하지 않음을 주장할 것입니다.

다음은 모든 ViewModel이 BaseViewModel을 확장하도록 강제하는 리엔트 규칙의 예시입니다:

```js
dependencies {
    testImplementation("com.lemonappdev:konsist:$konsistVersion")
    testImplementation("io.kotest:kotest-runner-junit5:$kotestVersion")
}
```

```js
class ViewModelsExtendBaseViewModel : BehaviorSpec() {

    init {
        Given("모든 프로덕션 코드 클래스") {
            val scope = Konsist.scopeFromProduction().classes()

            When("ViewModel이 있을 때") {
                val viewModels = scope.withNameEndingWith("ViewModel")

                Then("BaseViewModel을 확장하도록 한다") {
                    viewModels.assertTrue {
                        it.hasParentWithName("BaseViewModel")
                    }
                }
            }
        }
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

위 코드가 무엇을 하는지 분석해보겠습니다:

- 주어진 것: 먼저 대상으로 삼고자 하는 파일 또는 클래스를 나타내는 범위를 정의합니다. 이 경우에는 프로덕션 코드의 모든 클래스(테스트 클래스 제외)가 포함됩니다. 특정 모듈, 소스 세트, 패키지 또는 디렉토리를 대상으로 지정할 수도 있습니다. 사용 가능한 모든 옵션에 대해서는 문서를 참고할 수 있습니다.
- 조건부: 클래스를 필터링하여 이름이 `ViewModel`으로 끝나는 클래스만 가져옵니다.
- 결과: 필터링된 클래스들이 `BaseViewModel`를 확장하고 있는지 단언합니다.

그게 전부입니다! 상당히 간단하죠? 이제 우리는 유닛 테스트를 실행하는 방식으로 동일하게 Gradle이나 Android Studio를 사용하여 Lint 규칙을 실행할 수 있는 자체 Lint 규칙을 갖추게 되었습니다. 그리고 가장 좋은 점은 테스트 주도 개발(TDD)을 따를 수 있다는 것입니다. 코드 냄새를 확인하면 실패하는 테스트를 작성한 후 위반 사항을 수정하고 테스트(Lint 규칙)가 통과되는 것을 확인할 수 있습니다.

## 사용자 정의 메시지 추가하기

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

개발자에게 친화적인 방식으로 린트 규칙의 오류 메시지를 개선하려면 assertTrue 함수의 additionalMessage 매개변수를 사용하여 왜 이 릴 스규칙을 강제해야 하는지 설명하는 사용자 정의 메시지를 추가하는 것을 권장합니다:

```js
/* ... */

viewModels.assertTrue(additionalMessage = MESSAGE) {
    it.hasParentWithName("BaseViewModel")
}

/* ... */

private companion object {
    private const val MESSAGE =
        "새 ViewModel을 만들 때 항상 BaseViewModel을 확장하여 라이프사이클 이벤트 및 제공하는 기타 기능을 활용하십시오."
}
```

## 기준 지정

이제 방금 작성한 테스트(린트 규칙)를 실행해 봅시다:

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

![Lint Rule Violation](/assets/img/2024-05-20-StopDebatinginCodeReviewsStartEnforcingwithLintRules_2.png)

Oops! It looks like we've encountered some violations of the lint rule in our project. This gives us two options:

- Either resolve the violations by refactoring our ViewModels to extend the BaseViewModel.
- Or add the violating classes to the baseline. The baseline is a list of files or classes that we intentionally exclude from this lint rule. This could include legacy code we're not ready to refactor yet or valid cases where the lint rule shouldn't be applied.

To apply a baseline, we can create a BASELINE array in the companion object and pass it as a parameter in the withoutName function to filter out those classes. In our case, we will add the BaseViewModel to the baseline since it can't extend itself.

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
/* ... */

val viewModels = scope.withNameEndingWith("ViewModel").withoutName(*BASELINE)

/* ... */

private companion object {
    private const val MESSAGE =
        "Always extend the BaseViewModel when creating a new ViewModel, to take advantage of the lifecycle events and other features it provides."

    private val BASELINE = arrayOf("BaseViewModel")
}
```

라이브러리 룰에서 클래스를 제외하는 대체 방법은 각 파일 또는 클래스에서 제외하려는 것에 @Suppress 어노테이션을 사용하는 것이 있습니다. 그러나 이러한 룰의 예외 사항을 한눈에 확인할 수 있도록 테스트에서 직접 BASELINE 배열을 사용하는 것을 권장합니다.

참고: 기술 부채를 해결하는 과정에서 베이스라인에 포함되는 클래스 수를 점차 감소시키는 것이 좋습니다. 그 목록이 계속 늘어나면 좋지 않은 신호입니다.

# 혜택 #1: 아키텍처 강제화

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

코드 리뷰의 아키텍처에 대한 코멘트는 가장 중요하면서도 가장 방해가 되는 부분 중 하나입니다. 개개인이 아키텍처를 재설계하는 데 몇 시간이 소요될 수 있을 뿐만 아니라, 이러한 코멘트들은 종종 "나중에 이것을 고칠 수 있을까요?" 라는 일반적인 반대 의견을 받는 경우가 많습니다 (그리고 결국 고쳐지지 않습니다).

여기서 Konsist의 강점이 드러납니다. 특히 다중 모듈 프로젝트에서 아키텍처 규칙을 강제하는 능력이죠. 몇 가지 예시를 살펴보겠습니다:

- ViewModels는 생성자에서 Repository를 주입해서는 안 됩니다. 이렇게 함으로써 ViewModels가 데이터를 보내거나 검색할 때 UseCase 레이어만과 통신하도록 보장됩니다:

```js
class ViewModelsDoNotInjectRepositories : BehaviorSpec() {

    init {
        Given("All classes in production code") {
            val scope = Konsist.scopeFromProduction().classes()

            When("There is a ViewModel") {
                val viewModels = scope.withNameEndingWith("ViewModel")

                Then("No Repository is listed in the constructor parameters") {
                    viewModels.withConstructor {
                        it.hasParameter { param ->
                            param.type.name.endsWith("Repository")
                        }
                    }.assertEmpty()
                }
            }
        }
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

- 저장소는 저장소 모듈 내에 있어야 합니다. 이는 우리 아키텍처의 다른 레이어와의 깔끔한 분리를 강제하는 것입니다:

```js
class RepositoriesResideInRepositoriesModule : BehaviorSpec() {

    init {
        Given("프로덕션 코드의 모든 클래스") {
            val scope = Konsist.scopeFromProduction().classes()

            When("Repository가 존재할 때") {
                val repositories = scope.withNameEndingWith("Repository")

                Then("그 Repository는 repositories 모듈에 속해 있어야 합니다") {
                    repositories.assertTrue {
                        it.resideInModule("data/repositories")
                    }
                }
            }
        }
    }
}
```

- 도메인 레이어 모듈은 DTO를 가져오지 않아야 합니다. 이는 도메인 레이어를 응용 프로그램 레이어와 분리하여 깨끗한 아키텍처와 도메인 주도 설계에 부합합니다.

```js
class DomainLayerDoesNotImportDTOs : BehaviorSpec() {

    init {
        Given("도메인 레이어 모듈") {
            val scope = Konsist.scopeFromDirectory("domain").files

            Then("DTO를 가져오지 않습니다") {
                scope.assertFalse {
                    it.text.contains("com.perrystreet.dto")
                }
            }
        }
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

- 디자인 시스템은 도메인 모델을 가져오지 않아야 합니다. 이렇게 하면 디자인 시스템이 우리 앱의 기능과 독립적이며 중립적이라는 것을 보장할 수 있습니다:

```js
class DesignSystemDoesNotImportDomainModels : BehaviorSpec() {

    init {
        Given("디자인 시스템 모듈") {
            val scope = Konsist.scopeFromDirectory("design-system").files

            Then("도메인 모델을 가져오지 않습니다") {
                scope.assertFalse {
                    it.text.contains("com.perrystreet.domain.models")
                }
            }
        }
    }
}
```

# 혜택 #2: 버그 방지

일반적으로 코드베이스에서 버그를 만나면, 우리는 다음 질문을 스스로에게 하게 됩니다:

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

이메일에 마음이 아프셨을 수도 있을 것 같아요! 소중한 피드백 감사드립니다. 그러나, 이메일을 수십 번이나 보내실 필요는 없어요. 결과를 기다리고 계셨다는 걸 알기에 충분해요. 걱정마세요! 결과가 나올 때까지 조금만 기다려보세요. 결과가 나올 때 바로 알려드릴게요. 편안하게 기다려주세요! 🕒😊

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

우리 프로젝트에서 최근에 실제로 겪은 버그를 보호해주는 린트 규칙을 살펴보겠습니다.

Retrofit에서 다음과 같은 함수를 고려해봅시다. 이 함수는 서버에 id와 name을 @Field 매개변수로 보내는 POST 요청을 수행합니다.

```js
@POST(PATH)
fun postProfile(@Field("id") id: Long, @Field("name") name: String)
```

그러나 실행을 시도하면 앱이 다음과 같은 오류로 중단될 것입니다:

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
java.lang.IllegalArgumentException: @Field 매개변수는 form 인코딩과 함께만 사용할 수 있습니다. (매개변수 #1)
    postProfile 메서드에 대해
```

@Field 주석은 데이터를 form-URL로 인코딩하여 보내며, 함수에 @FormUrlEncoded 주석이 필요합니다. 그렇지 않으면 앱이 다운될 수 있습니다:

```js
@POST(PATH)
@FormUrlEncoded
fun postProfile(@Field("id") id: Long, @Field("name") name: String)
```

이 문제를 방지하기 위한 린트 규칙을 만드는 것이 얼마나 완벽한 사용 사례인가요!

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
class RetrofitFieldParamsUseFormUrlEncoded: BehaviorSpec() {

    init {
        Given("프로덕션 코드의 모든 함수를 가져옵니다.") {
            val scope = Konsist.scopeFromProduction().functions()

            When("@POST 주석이 있는 함수가 있는 경우") {
                val functions = scope.withAnnotationNamed("POST")

                And("@Field 매개변수가 하나 이상 있는 경우") {
                    val functionsWithFieldParams = functions.withParameter {
                        it.hasAnnotationWithName("Field")
                    }

                    Then("@FormUrlEncoded 주석이 있는 경우") {
                        functionsWithFieldParams.assertTrue {
                            it.hasAnnotationWithName("FormUrlEncoded")
                        }
                    }
                }
            }
        }
    }
}

```

여기에 코드의 요약이 있습니다:

- Given: 우리는 프로덕션 코드에서 모든 함수를 가져올 것입니다.
- When: @POST 주석이 있는 함수를 필터링합니다.
- And: 추가적인 필터를 적용하여 @Field 주석이 하나 이상 있는 함수를 가져올 것입니다.
- Then: 이러한 필터링된 함수들이 @FormUrlEncoded 주석도 가지고 있는지 확인합니다.

# Konsist와 함께한 프로젝트 구조

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

이전에 본 바와 같이 Konsist 린트 규칙은 단위 테스트와 같습니다. 즉, 이를 실행하려면 해당 규칙을 프로젝트의 테스트 소스 세트에 배치해야 합니다. 나머지 단위 테스트와 함께 두어야 합니다.

그러나 특히 멀티 모듈 프로젝트에서 작업 중이라면 더 나은 접근 방식이 있습니다. 린트 규칙을 다른 단위 테스트에서 분리하기 위해 모든 린트 규칙을 담을 전용 모듈을 만들 수 있습니다:

![이미지](/assets/img/2024-05-20-StopDebatinginCodeReviewsStartEnforcingwithLintRules_4.png)

그리고 모듈 구조 및 build.gradle.kts 파일은 이렇게 보일 것입니다:

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

md
![이미지](/assets/img/2024-05-20-StopDebatinginCodeReviewsStartEnforcingwithLintRules_5.png)

```js
plugins {
    id("kotlin")
}

java {
    sourceCompatibility = JavaVersion.VERSION_17
    targetCompatibility = JavaVersion.VERSION_17
}

tasks.test {
    useJUnitPlatform()
}

dependencies {
    testImplementation("com.lemonappdev:konsist:$konsistVersion")
    testImplementation("io.kotest:kotest-runner-junit5:$kotestVersion")
}
```

참고: 여기에 모듈의 나머지를 종속성으로 추가할 필요가 없습니다. Konsist는 기본적으로 전체 프로젝트에 액세스할 수 있으며 명시적인 종속성이 필요하지 않습니다.

이 접근 방식으로 다른 이점은 코드 리뷰에서 리뷰하는 대신 리뷰어가 관례 규칙을 집행할 수 있다는 것입니다. 단위 테스트와 별도로 출력을 실행할 수 있습니다. `:'module':test` Gradle 작업을 사용하여 린트 규칙을.

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

최종적으로 중복 코드를 줄이기 위해 린트 규칙을 작성할 때, 흔히 사용하는 범위를 포함하는 KonsistUtils.kt 파일을 생성하는 것을 권장합니다:

```js
object KonsistUtils {

    val productionCode
        get() = Konsist.scopeFromProduction()

    val classes
        get() = productionCode.classes()

    val interfaces
        get() = productionCode.interfaces()

    val viewModels
        get() = classes.withNameEndingWith("ViewModel")

    val useCases
        get() = classes.withNameEndingWith("UseCase")

    val repositories
        get() = classes.withNameEndingWith("Repository")

    val domainModule
        get() = Konsist.scopeFromDirectory("domain")

    val designSystemModule
        get() = Konsist.scopeFromDirectory("design-system")

    /* ... */
}
```

# CI/CD에서 린트 규칙 실행하기

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

그래서, Konsist 모듈을 설정하고 사용자 정의 린트 규칙을 작성하는 데 많은 노력을 기울인 끝에, 한 가지가 남았습니다: 만약 린트 규칙을 위반하면 병합할 풀 리퀘스트를 차단하는 방법은 무엇일까요?

이미 유닛 테스트를 위한 파이프라인을 설정했다면, 이 작업은 매우 간단할 것입니다. 필요한 것은 이전에 만든 Konsist 모듈의 유닛 테스트를 실행할 추가 단계뿐입니다.

GitHub Actions 또는 Bitrise를 사용한 예제 구성을 살펴보겠습니다:

## GitHub 액션 구성

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

아래의 GitHub Action은 저장소를 체크아웃하고 Java와 Gradle을 설정한 다음, konsist 모듈에서 모든 단위 테스트(린트 규칙)를 실행하는 Gradle 작업을 실행합니다. 이 작업은 풀 리퀘스트가 올라올 때 트리거됩니다:

```js
name: Run Konsist lint rules
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  run-lint-rules:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17

      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v3

      - name: Run Konsist lint rules Gradle task
        run: ./gradlew :konsist:test
```

GitHub Action을 설정한 후, 해당 동작을 레포지토리 설정의 브랜치 보호 규칙에서 필수 상태 체크로 지정해 둘 수 있습니다. 이렇게 하면 린트 규칙을 위반하는 경우 풀 리퀘스트가 병합되지 못하도록 막을 수 있습니다.

## Bitrise 구성

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

만약 Bitrise를 사용 중이라면, 단순히 워크플로에 추가할 추가 단계로 :konsist:test Gradle 작업을 실행하면 됩니다:

![이미지](/assets/img/2024-05-20-StopDebatinginCodeReviewsStartEnforcingwithLintRules_6.png)

마지막으로, 모든 모듈에서 Konsist 린트 규칙을 포함하여 병렬로 모든 단위 테스트를 실행하려면, 이들을 하나의 Gradle 작업에 결합하여 상위 레벨 build.gradle.kts 파일에 정의할 수 있습니다:

```js
tasks.register("runAllTests") {
    dependsOn(
        ":app:test",
        ":domain:test",
        ":other-module:test",
        // ...
        ":konsist:test"
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

# Summary

린트 규칙은 코드베이스의 구조와 팀의 수립된 최상의 및 필수적인 실천 방법에 대한 서면 약정으로 작용합니다. 그것들은 모호성을 제거하여 코드베이스를 일관되게 유지하는 데 도움을 주며, 우리의 프로젝트를 미묘한 버그로부터 보호하고 코드 작성 및 리뷰 속도를 크게 높일 수 있습니다.

Konsist를 통해, 우리는 안드로이드에서 린트 규칙 작성을 단순하고 접근 가능하게 만들어주는 오랜 기다림이 이루어졌습니다. 이 도구는 확실히 사용자 테스트로 재해석하는 탁월한 아이디어로 사용자 정의 릴 규칙 작성에 있어 이정표를 세우게 됩니다.

프로젝트를 지원하는 방법을 알아보려면 Konsist 기여 가이드라인을 꼭 확인해주세요.

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

## PS. iOS는 어떻게 되었나요?

우리 팀은 iOS에서 SwiftLint를 사용해왔습니다. 이 도구는 파일 이름 패턴 일치를 사용하여 정규식 기반 규칙을 작성할 수 있는 강력한 기능을 갖추고 있어 대부분의 구조적인 규칙을 작성할 수 있게 해줍니다. 그러나 모든 규칙을 작성할 수 있는 것은 아닙니다. 이에 대해 곧 블로그 포스트를 통해 더 많은 내용을 알려드릴 예정입니다.

# 저자 소개

스텔리오스 프란티스카키스는 Perry Street Software의 스탭 엔지니어입니다. 해당 회사는 LGBTQ+ 데이팅 앱 SCRUFF와 Jack'd를 제작하여 전 세계 3000만 명 이상의 회원을 보유하고 있습니다.
