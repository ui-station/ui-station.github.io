---
title: "앱에 대한 자부심  안드로이드에서 GraphQL 사용해보기"
description: ""
coverImage: "/assets/img/2024-06-19-PrideinYourAppTryingOutGraphQLonAndroid_0.png"
date: 2024-06-19 10:39
ogImage: 
  url: /assets/img/2024-06-19-PrideinYourAppTryingOutGraphQLonAndroid_0.png
tag: Tech
originalTitle: "Pride in Your App — Trying Out GraphQL on Android"
link: "https://medium.com/proandroiddev/pride-in-your-app-trying-out-graphql-on-android-69c4b515c463"
---


![이미지](/assets/img/2024-06-19-PrideinYourAppTryingOutGraphQLonAndroid_0.png)

모두들, 요즘은 프라이드 달이에요! LGBTIQA+ 커뮤니티 구성원으로서, 이번 달은 좋으면서도 스트레스를 받는 시기예요. 누군가 무슨 일을 일으키려 할지 예측할 수 없어요. 이번 달은 정말 몇몇 나쁜 사람들을 끌어모을 것 같아요. 그리고 유럽에서 논할 우파의 급부상까지... 시작도 못하겠네요.

오래전부터 안드로이드에서 GraphQL을 시도해보고 싶어했는데, 이 Pride Flag API를 발견하면서 이제 내가 그때가 된 것 같았어요. API(그리고 GraphQL)를 시도하고 블로그 글을 써서 프라이드를 기념하는 좋은 방법이죠!

GraphQL에 대해 조금 소개하자면: 저는 과거에서 GraphQL에 대해 이야기하며 공개 연설 경력을 시작했어요 (JavaScript도요. 제 밝혀지지 않은 비밀이죠.) 재미있었고 정말 좋았어요. 물론, 어떠한 기술을 사용할지 고려할 때 항상 장단점이 있지만, 그래도 정말 좋아했어요. 어느 밋업에서 "GraphQL에 대해 물어봐주세요"라는 문구가 적힌 티셔츠를 받았는데, 아마 그 저의 가장 멋진 티셔츠였을 거예요.

<div class="content-ad"></div>

안녕하세요!

안드로이드로 전환했을 때는 GraphQL을 안드로이드와 통합하는 방법을 배울 필요성을 느끼지 못했어요. 그래서 그동안 시도해보고 싶었는데, 이제야 좋은 방법을 찾아 해볼 기회를 찾았어요. 함께 시작해봐요!

# GraphQL

GraphQL이라는 단어를 처음 들어보셨다면, 이것은 그래프 형태로 API를 구축하는 쿼리 언어로 대안적인 방법입니다.

GraphQL API를 통해 필요한 것만 요청할 수 있습니다. 그래서 항상 모든 것을 반환하는 REST API와 달리 GraphQL API는 요청한 값만 반환합니다. 그리고 그래프 기반으로 데이터를 중첩하여 하나의 쿼리로 가져올 수 있어요. 예를 들어 작가, 그의 책, 그리고 책 속 캐릭터를 예로 들 수 있는데, REST API에서는 세 번의 요청이 필요하죠 (물론 API에 따라 달라질 수 있어요).

<div class="content-ad"></div>

GraphQL은 데이터를 쿼리, 변경 또는 구독하는 세 가지 작업을 제공합니다. REST와 비교하면, 쿼리는 GET 요청과 일치하고, 변경은 데이터를 변경하는 다른 모든 요처과 일치하며, 구독은 양방향으로 작동합니다 - 예를 들어, 웹소켓과 같이요.

이 블로그 포스트의 맥락에서는 데이터를 쿼리할 것입니다. 사용 가능한 작업 및 일반적인 GraphQL에 대해 더 알고 싶다면, GraphQL 문서로 이동하세요.

GraphQL을 사용하기 위해 우리는 Apollo Kotlin(이전 Apollo Android)을 사용할 것입니다. 이 라이브러리는 타입 안전하며 Kotlin 멀티플랫폼과 호환됩니다.

# 의존성 및 스키마 가져오기

<div class="content-ad"></div>

Apollo Kotlin을 시작하려면 프로젝트에 몇 가지 종속성을 추가해야 합니다:

```js
// 프로젝트 수준의 build.gradle.kts

plugins {
  id("com.apollographql.apollo3").version("3.8.4")
}
```

그리고

```js
// 모듈 수준의 build.gradle.kts

dependencies {
  implementation("com.apollographql.apollo3:apollo-runtime:3.8.4")
}
```

<div class="content-ad"></div>

이 패키지들의 버전 번호는 반드시 동일해야 합니다.

의존성에 추가하여 생성된 모델을 위한 패키지 이름을 추가해야 합니다:

```js
// module-level build.gradle.kts 

apollo { 
    service("service") { 
        packageName.set("com.example") 
    } 
}
```

많은 프로젝트에서는 그레이들 파일을 동기화하게 됩니다. 그러나 이 프로젝트는 다릅니다; 여전히 GraphQL 쿼리의 스키마가 필요합니다. 여러 옵션이 있지만 외부 API를 통해 스키마를 내려받는 것이 가장 쉽습니다. 스키마를 src/main/graphql에 저장해야 하며, 이 작은 프로젝트의 경우에는 다음 명령어를 사용하여 이 작업을 수행합니다:

<div class="content-ad"></div>

```js
./gradlew :app:downloadApolloSchema --endpoint= 'https://pride.dev/api/graphql' --schema=app/src/main/graphql/schema.graphqls
```

스키마를 얻었다면 Gradle 파일을 동기화하고 데이터 쿼리를 계속합니다.

# 데이터 쿼리

이 작은 앱에서는 데이터를 읽고 싶습니다 — 따라서 GraphQL 용어로는 쿼리하려고 합니다. 모든 속성이 필요하지 않으므로 필요한 것만 포함한 쿼리를 정의합니다:

<div class="content-ad"></div>

```js
query FlagQuery {
  allFlags {
    name
    year
    svgUrl
  }
}
```

Apollo Kotlin을 사용하면 모든 쿼리에 이름이 지정되어 있어야 합니다. 이름이 없는 쿼리가 있으면 오류가 발생할 수 있어요. 믿어요, 제 경험이죠.

이 쿼리는 API에서 사용 가능한 모든 플래그를 가져옵니다. 플래그의 이름, 연도 및 SVG URL이 필요하기 때문에 쿼리에 이러한 속성을 작성합니다.

Android 프로젝트에 쿼리를 추가하려면 먼저 src/main/ 폴더에 파일을 만들어야 합니다. 이 폴더는 스키마가 있는 폴더와 동일합니다. 파일 이름을 FlagQuery.graphql로 지정하고 쿼리를 추가합니다. 앱이 빌드 될 때 자동으로 실행되는 작업을 통해 Apollo는 쿼리에 대한 모델을 생성합니다. 그러니 앱을 빌드하고 나면 쿼리 모델을 사용할 수 있을 거에요.

<div class="content-ad"></div>

이 시점에서 AndroidManifest에 인터넷 권한을 추가해야 한다는 것을 상기시키고 싶어요. 그렇게 하지 않으면, 다음 단계가 더 복잡해질 거에요. 믿어줘요, 알아요.

추가해야 하는 내용을 상기시키기 위해 여기에 있어요. 나처럼 검색 엔진을 사용하지 않아도 괜찮아요:

```js
// AndroidManifest.xml

<uses-permission android:name="android.permission.INTERNET" />
```

자, 이제 쿼리가 준비되었는데, 이것을 UI에 어떻게 연결할까요? 답은 GraphQL 클라이언트입니다. 데이터를 쿼리하는 데 사용할 수 있어요. 이 데모를 위해 간단하게 만들고 있으니, GraphQL 클라이언트를 정의하고 데이터를 뷰 모델에서 쿼리할 것이에요. 제작용 앱에서는 아마 레이어 구조가 더 복잡할 거에요.

<div class="content-ad"></div>

먼저 클라이언트를 정의합니다:

```js
// GraphQLClient.kt

const val API_ENDPOINT = "https://pride.dev/api/graphql"

val apolloClient = ApolloClient.Builder()
    .serverUrl(API_ENDPOINT)
    .build()
```

뷰 모델에서는 먼저 UI에서 사용할 상태를 정의합니다:

```js
// FlagsViewModel.kt

data class FlagsUiState(
    val loading: Boolean = true,
    val flags: List<FlagQuery.AllFlag> = emptyList(),
    val currentFlag: FlagQuery.AllFlag? = null,
)

class FlagsViewModel : ViewModel() {
    private var _state = MutableStateFlow(FlagsUiState())
    val state = _state.asStateFlow()
    ...
}
```

<div class="content-ad"></div>

API에서 받은 모든 플래그를 저장하고 상세 페이지에서 현재 플래그를 표시하려고 합니다. 이를 위해 상태에 플래그 및 현재 플래그 속성이 있습니다. 두 속성 모두 (FlagQuery.AllFlag)은 Apollo Kotlin에 의해 생성된 형식입니다.

모든 플래그를 쿼리하려면 ViewModel 내부에 다음과 같은 함수를 정의하세요:

```js
// FlagsViewModel.kt

suspend fun getFlags() {
    val flagsQuery = apolloClient
        .query(FlagQuery())
        .execute()

    _state.update { currentState ->
        currentState.copy(
            loading = false,
            flags = flagsQuery.data?.allFlags ?: emptyList(),
        )
    }
}
```

따라서 플래그를 가져오려면 apolloClient.query(...)를 호출하는 중단 함수가 필요하며, 이 함수 내에서 이전에 정의한 FlagQuery와 함께 실행해야합니다. 성공하면 데이터 속성에서 데이터를 반환하고 해당 데이터를 사용하여 상태를 업데이트할 수 있습니다.

<div class="content-ad"></div>

저희는 현재 상태에 플래그를 저장하고 싶다고 언급했었습니다. 이를 설정하는 것은 GraphQL 매직 없이 간단한 함수입니다:

```js
// FlagsViewModel.kt

fun setFlag(flag: FlagQuery.AllFlag) {
    _state.update {
        it.copy(
            currentFlag = flag,
        )
    }
}
```

# UI에 대해 간략히

UI 측면에서는 매우 간단한 작업을 수행하고 있습니다: 플래그 목록을 표시한 다음 상세 뷰로 이동합니다. GraphQL과 관련이 없으므로 데이터를 상태로 전달하는 것은 이 블로그 포스트의 범위를 벗어납니다.

<div class="content-ad"></div>

여기에 경험이 어떻게 보이는지 보여주는 비디오가 있어요:

UI 코드를 확인하고 싶다면, Flag Explorer 리포지토리로 이동해보세요.

# 마무리

이 블로그 포스트에서는 GraphQL과 안드로이드 프로젝트에서의 활용 방법에 대해 논의했습니다. 예를 들어, Pride Flag API에서 프라이드 플래그를 가져오는 작은 앱을 만들었습니다.

<div class="content-ad"></div>

프로젝트에 GraphQL을 추가하는 것은 생각보다 꽤 간단했습니다. 어떤 이유에서인지 더 복잡할 거라고 생각했는데요. 글 처음에 언급한 대로 저는 GraphQL에 대해 호감을 가지고 있어요, 그래서 실제 프로젝트에서 사용해보면 멋질 것 같아요. 앞으로 어떤 일이 일어날지 기대되네요!

GraphQL에 대해서 어떻게 생각하세요? 사용해보신 적이 있나요?

# 블로그 포스트에서의 링크

- 프라이드 플래그 API
- GraphQL 문서
- 아폴로 코틀린
- 플래그 익스플로러