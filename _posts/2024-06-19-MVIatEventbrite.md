---
title: "이벤트브라이트의 MVI"
description: ""
coverImage: "/assets/img/2024-06-19-MVIatEventbrite_0.png"
date: 2024-06-19 10:33
ogImage:
  url: /assets/img/2024-06-19-MVIatEventbrite_0.png
tag: Tech
originalTitle: "MVI at Eventbrite"
link: "https://medium.com/gitconnected/mvi-at-eventbrite-6c3b72addb63"
---

![이미지](/assets/img/2024-06-19-MVIatEventbrite_0.png)

저는 6개월 전에 Senior Android Engineer로 Eventbrite에 합류했습니다. 여기서 6개월을 일한 후, 발견한 한 가지는 이곳이 단순히 제품 중심의 회사가 아니라 기술 중심의 회사라는 것입니다. 저희가 가지고 있는 아키텍처와 함께 일할 수 있는 뛰어난 두뇌들이 매일 저를 흥분하게 만듭니다.

Eventbrite는 누구에게나 자신의 열정을 충전하고 삶을 더 풍부하게 만드는 이벤트를 만들고 공유하고 찾고 참석할 수 있는 글로벌 셀프 서비스 티케팅 플랫폼입니다. 음악 축제부터 마라톤, 회의, 지역 집회 및 기금 모금 행사, 게임 대회, 공연 대회 등까지 다양한 이벤트를 지원합니다. 우리의 미션은 실시간 체험을 통해 세계를 하나로 이어주는 것입니다.

저희 Eventbrite에는 두 가지 제품이 있습니다:

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

- 주최자 앱: 이 앱은 이벤트브라이트에서 이벤트를 개최하려는 창작자들을 위한 것입니다. 이 앱은 창작자들이 이벤트를 모바일에서 관리할 수 있도록 도와줍니다 — 이벤트 만들기 & 편집하기, 티켓 추적 & 판매하기, 그리고 게스트 체크인하기.
- 참가자 앱: 이 앱은 근처 이벤트에 참가하고 싶어하는 참가자들을 위한 것입니다. 이 플랫폼을 통해 티켓을 예약할 수 있습니다.

이벤트브라이트에서 운영 중인 안드로이드 앱은 MVI 아키텍처를 기반으로 합니다. 이 기사에서는 MVI 아키텍처가 무엇인지, MVVM과 어떻게 다른지, 그리고 그 장점에 대해 설명하겠습니다. 그리고 참가자 앱에서 이벤트를 확인하는 예시로 어떻게 구현할 수 있는지도 알려드리겠습니다.

# MVI[Model View Intent]

Model-View-Intent(MVI) 아키텍처 패턴은 주로 André Staltz가 개발한 자바스크립트 프레임워크인 Cycle.js에 속한다고 알려져 있습니다. 그러나 MVI는 다양한 개발자와 커뮤니티에 의해 채택되고 다양한 프로그래밍 언어 및 플랫폼에서 적용 및 조정되었습니다.

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

위 내용을 이해하기 위해 이 비디오도 확인해 볼 수 있어요.

안드로이드에서는 Hannes Dorfmann의 글 이후로 인정받게 되었습니다. 그들은 블로그에서 MVI 아키텍처에 대해 상세히 설명했습니다. 여기서 확인할 수 있어요.

- Model: 모델은 애플리케이션의 데이터와 비즈니스 로직을 나타냅니다. MVI에서는 모델이 변경 불가능하며 애플리케이션의 현재 상태를 나타냅니다.
- View: 뷰는 UI를 렌더링하고 사용자 입력에 반응하는 역할을 합니다. 그러나 MVVM 및 MVC와는 달리, MVI에서의 뷰는 수동 구성요소입니다. 뷰는 모델과 직접 상호작용하거나 데이터를 기반으로 결정하지 않습니다. 대신, 뷰모델로부터 상태 업데이트와 사용자 의도를 받습니다.
- Intent: 의도는 UI에서 발생하는 사용자 작업 또는 이벤트를 나타냅니다. MVI에서는 이러한 의도가 뷰에 의해 캡처되어 뷰모델에게 전송되어 처리됩니다.
- ViewModel: MVI에서의 뷰모델은 애플리케이션 상태와 비즈니스 로직을 관리합니다. 뷰로부터 사용자 의도를받아 처리하고 모델을 업데이트합니다. 그런 다음 뷰모델은 새로운 상태를 발행하고, 이를 뷰가 관찰하고 렌더링 합니다.

이번에는 Eventbrite 앱을 통해 MVI를 이해해 봅시다. Model — View — Intent 개념을 적용해 보겠습니다.

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

![이미지](/assets/img/2024-06-19-MVIatEventbrite_1.png)

이것은 Attendee 앱의 이벤트 세부 페이지입니다. 사용자는 보통 두 곳에서 이 페이지에 액세스할 수 있습니다. 이벤트 목록에서 그리고 검색에서.

이 페이지에는 이벤트의 이름, 날짜, 시간, 장소, 주최자, 이벤트에 대한 요약 등에 대한 세부 정보가 표시됩니다. 그리고 몇 가지 클릭이 있습니다: 좋아요, 좋아하지 않음, 공유, 창작자 팔로우, 티켓 구매 등이 있습니다.

MVI를 사용하여 단계별로 구현 방법을 이해해 보겠습니다.

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

# # 모델

## 뷰 상태

이벤트 세부 페이지에서 다음 상태를 가질 수 있습니다:

- 로딩
- 콘텐츠
- 오류

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

각 화면에 대한 3가지 기본 상태입니다.

```js
내부 봉인된 클래스 ViewState {

    @Immutable
    class Loading(val onBackPressed: () -> Unit = {}) : ViewState()

    @Immutable
    class Content(val event: UiModel) : ViewState()

    @Immutable
    class Error(val error: ErrorUiModel): ViewState()

}
```

화면의 초기 상태는 Loading입니다. 서버에서 이벤트 세부 정보를 가져 오는 동안 진행률 표시줄을 표시할 것입니다.

<img src="/assets/img/2024-06-19-MVIatEventbrite_2.png" />

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

Compose에서는 상태를 확인하고보기를로드합니다.

```kotlin
@Composable
internal fun Screen(
    state: State,
) {
    when (state) {
        is State.Loading -> Loading()
        is State.Error -> Error(state.error)
        is State.Content -> Content(state.event)
    }
}
```

따라서 이제 UI를 변경하려면 직접 변경하는 대신 상태와 통신하여 UI가 상태를 관찰하고 변경 사항을 반영하게합니다.

# # 의도

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

## 이벤트

![이미지](/assets/img/2024-06-19-MVIatEventbrite_3.png)

이벤트는 작업을 정의하는 sealed 클래스입니다.

```js
sealed class Event {
    data object Load : Event()
    class FetchEventError(val error: NetworkFailure) : Event()
    class FetchEventSuccess(val event: ListingEvent) : Event()
    class Liked(val event: LikeableEvent) : Event()
    class Disliked(val event: LikeableEvent) : Event()
    class FollowPressed(val user: FollowableOrganizer) : Event()
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

<img src="/assets/img/2024-06-19-MVIatEventbrite_4.png" />

하나씩 각 이벤트를 이해해봐요.

로드 이벤트:

로드는 초기 이벤트로, 프래그먼트에서 트리거됩니다. OnCreate에서 이벤트를 설정하고, 초기 이벤트인 로드를 가져와 ViewModel에서 처리하죠.

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
 재정의 하여 이벤트를 처리하는 suspend fun 핸들이벤트(event: Event) {
        when (event) {
            is Event.Load -> load()
        }
}
```

로드 기능에서는 서버에서 이벤트 세부정보를 가져옵니다. 이 API의 성공 또는 오류에 따라 UI 상태를 변경하고, UI가 이에 맞게 업데이트됩니다.

```kotlin
 getEventDetail.fetch(eventId)
                .fold({ error ->
                    state {
                       ViewState.Error(
                            error = error.toUiModel(events)
                    }
                }) { response ->
                    state { ViewState.Content(event.toUiModel(events, effect)) }
                }
```

View에서 변경사항을 받음

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
내부 fun EventDetailScreen(
    상태: ViewState
) {
    when (상태) {
        is ViewState.Loading -> Loading()
        is ViewState.Error -> Error(상태.error)
        is ViewState.Content -> Content(상태.event)
    }
}
```

## Reducer

State Reducer은 함수형 프로그래밍에서 나온 개념으로 이전 상태를 입력으로 받아 이전 상태에서 새로운 상태를 계산합니다.

참석자가 크리에이터를 팔로우하는 기능을 예로 들어서 이해해봅시다. 사용자가 팔로우 버튼을 클릭했을 때 어떤 일이 일어날까요?

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

<img src="/assets/img/2024-06-19-MVIatEventbrite_5.png" />

먼저, **UiModel**이라는 객체가 있습니다. 이 객체에는 콘텐츠 상태가 포함되어 있으며, 이를 사용하여 UI에 데이터를 표시합니다.

```js
internal data class UiModel(
    val eventTitle: String,
    val date: String,
    val location: String,
    val summary: String,
    val organizerInfo: OrganizerState,
    val onShareClick: () -> Unit,
    val onFollowClick: () -> Unit
)
```

이제 한 단계씩 이해해 봅시다:

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

**작업 1: 사용자 클릭 리스너 구현 및 이벤트 트리거**

```js
onClick {
  events(EventDetailEvents.FollowPressed(followableOrganizer))
}
```

**작업 2: ViewModel에서 이벤트 처리**

이미 팔로우된 주최자인 경우 언팔로우하고, 그렇지 않은 경우 팔로우하세요.

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

만약 followableOrganizer가 팔로우되어 있다면 {
상태 { onUnfollow(::event, ::effect) }
} 그렇지 않으면 {
상태 { onFollow(::event, ::effect) }
}

액션 3: 리듀서

onUnfollow 및 onFollow는 리듀서에 의해 처리되며, 이 곳에서 이전 상태를 가져와 수정한 후 다시 뷰로 보내줍니다.

```kotlin
private fun getFollowContent(
        event: UiModel,
        newState: Boolean, // 팔로우 중 또는 언팔로우 중 표시
        events: (Event) -> Unit
) = ViewState.Content(
        event.copy(
                organizerState = with((event.organizerState as OrganizerState)) {
                    val hasChanged = newState != isFollowing
                    OrganizerState.Content(copy(

                            isFollowing = newState,
                            listeners = OrganizerListeners(
                                    onFollowUnfollow = {
                                        val followableUser = event.toFollowableModel(newState, it.toBookmarkCategory())
                                        events(Event.FollowPressed(followableUser))
                                    }
                            )
                    )
                    )
                }
        )
)
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

`getFollowContent` 함수는 View 상태를 반환합니다.

**액션 4:** 뷰 모델에서 View 상태를 반환합니다

```js
state { onUnfollow(::event, ::effect) }
```

**액션 5:** 이 변경사항을 관찰하여 View를 수정하세요

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

# 결론

마지막으로, Eventbrite에서 Model-View-Intent (MVI) 아키텍처를 채택한 결과, 안드로이드 앱이 향상되었을 뿐만 아니라 개발 프로세스가 간단해졌습니다. MVI를 받아들이면 상태 관리가 간소화되고 데이터 흐름이 개선되며 응용 프로그램 내에서 더 예측 가능하고 일관된 동작이 보장됩니다.

MVI의 주요 장점은 MVVM과 같은 전통적인 아키텍처에 비해 명확하게 드러납니다. MVI로 전환하면 상태 관리에 대한 명확하고 중앙 집중식 접근 방식을 누릴 수 있습니다. Model은 응용 프로그램의 불변 상태를 표현하고, View는 상태 업데이트에 따라 수동적으로 UI를 렌더링하며, Intent는 사용자 조작을 효율적으로 캡처합니다. 이 단방향 데이터 흐름은 데이터 및 이벤트의 흐름을 간소화하므로 앱의 동작을 이해하기 쉬워지며 MVVM에서 상태 변경을 관리하는 데 일반적으로 동반되는 복잡성이 줄어듭니다.

게다가, Eventbrite 앱에서 MVI를 구현한 결과, Event 상세 페이지 예제를 통해 그 실용성과 효과를 보여주었습니다. 명확한 상태를 정의하고 이벤트를 처리하며 새로운 상태를 계산하기 위해 reducer를 사용함으로써 더 효율적이고 유지보수가 용이한 코드 기반을 구축했습니다.

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

MVI 아키텍처 도입은 Eventbrite에서 강력하고 확장 가능한 안드로이드 앱을 구축할 뿐만 아니라 개발 프로세스를 간소화하는 선례를 제시했습니다. 관심사의 명확한 분리, 예측 가능한 데이터 흐름, 그리고 중앙 집중식 상태 관리는 모든 개발자가 프로젝트에 통합을 고려해야 할 가치 있는 패러다임으로 만들어 냈습니다. MVI를 사용하면 직관적이고 잘 구조화된 애플리케이션을 통해 우수한 사용자 경험을 창출하는 길이 더욱 분명해지고 달성 가능해집니다.

귀하의 피드백은 계속해서 개선해 나가기 위해 중요합니다. 계속해서 보내주세요.

이 글이 도움이 되었기를 바랍니다. 제안이나 코멘트가 있으면 댓글 섹션에서 알려주세요. 또는 karishma.agr1996@gmail.com 으로 연락 주셔도 됩니다.

당신의 좋아요는 다른 사람들이 이 글을 발견하는 데 도움이 됩니다 😃 .

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

![Image](https://miro.medium.com/v2/resize:fit:1200/1*ogke9Q7S3f_WKKfqc1y6_w.gif)
