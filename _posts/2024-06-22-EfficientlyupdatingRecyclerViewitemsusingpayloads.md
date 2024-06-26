---
title: "RecyclerView 항목을 payloads로 효율적으로 업데이트하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-EfficientlyupdatingRecyclerViewitemsusingpayloads_0.png"
date: 2024-06-22 22:47
ogImage:
  url: /assets/img/2024-06-22-EfficientlyupdatingRecyclerViewitemsusingpayloads_0.png
tag: Tech
originalTitle: "Efficiently updating RecyclerView items using payloads"
link: "https://medium.com/@domen.lanisnik/efficiently-updating-recyclerview-items-using-payloads-1305f65f3068"
---

요즘 대부분의 앱은 사용자에게 수직이나 수평 목록으로 정보를 표시합니다. 종종, 정보는 동적이며 조회수, 좋아요 수 등과 같이 자주 업데이트해야 하는 정보입니다. 또한 목록에는 네트워크에서 로드된 이미지가 포함될 수도 있습니다. 이것이 RecyclerView를 효율적으로 업데이트하는 것이 중요한 이유이며, 성능이 우수한 앱을 갖고 좋은 사용자 경험을 제공하는 중요한 측면입니다.

이 게시물은 얼마 전에 쓰여졌지만, 이미 다루어진 주제이고 Jetpack Compose가 현재 선호되는 UI 툴킷이기 때문에 발행할 지 말 지 고민하고 있었습니다. 그러나 이 정보가 누군가에게 도움이 될 수 있기를 바라며 게시하기로 결정했습니다.

## DiffUtil과 ListAdapter 사용하기

새 데이터로 RecyclerView를 효율적으로 업데이트하려면 notifyItemInserted(position: Int), notifyItemChanged(position: Int), notifyItemRemoved(position: Int) 등과 같은 함수를 호출해야 합니다 (docs에서는 효율적이지 않으며 최후의 수단으로만 사용해야 한다고 명시한 notifyDataSetChanged()는 피하시기 바랍니다). 이러한 함수를 호출하면 RecyclerView Adapter에 기본 데이터가 변경되었음을 알리고 뷰를 업데이트하여 새 상태를 반영해야 한다는 사실을 알리게 됩니다. 이러한 함수 호출은 RecyclerView가 모든 변경 사항을 애니메이션으로 처리하는 이점도 함께 제공됩니다.

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

수동으로 그 모든 작업을 하지 않도록 하기 위해 DiffUtil을 사용할 수 있습니다 (공식 문서). DiffUtil은 이전 데이터와 새 데이터를 비교하고 차이점을 계산한 다음 RecyclerView.Adapter에 변경 사항을 알려주어 새 상태를 반영하기 위해 수행해야 하는 변경 사항을 선언합니다. 이후 사용할 DiffUtil.ItemCallback의 예시가 여기 있습니다.

Function areItemsTheSame(oldItem: Item, newItem: Item): Boolean은 id 또는 uuid와 같은 고유 속성을 기준으로 두 항목이 동일한지 확인합니다. 항목이 다를 경우 어댑터는 이전 항목을 새 항목으로 바꿔야 한다는 것을 알게 됩니다. 이 함수가 true를 반환하면 함수 areContentsTheSame(oldItem: Item, newItem: Item): Boolean이 호출되는데, 여기서 데이터 모델의 다른 속성 중 어떤 것이 변경되었는지 확인할 수 있습니다.

중요한 점은 DiffUtil 결과를 백그라운드 스레드에서 계산하는 것이 권장된다는 것입니다. 더 큰 데이터 집합이 있는 경우에는 요구가 많을 수 있고 주 스레드를 차단할 수 있기 때문입니다. Coroutine 또는 RxJava를 사용하여 계산을 다른 스레드로 옮기거나 ListAdapter(공식 문서)를 사용하여 백그라운드 스레드에서 계산할 수 있습니다.

ListAdapter는 AsyncListDiffer (공식 문서)를 사용하여 백그라운드 스레드에서 차이를 계산하는 논리를 포함하고 RecyclerView.Adapter를 확장하여 코드를 더욱 간결하게 만듭니다. 할 일은 새 데이터를 함수 submitList(list: List)를 사용하여 전달하는 것뿐입니다.

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

## 샘플 앱에서 모두 함께 사용해보기

지금까지 배운 내용을 모아 간단한 앱으로 만들어보겠습니다. 이 앱은 기사 목록을 표시하는데, 각 기사에는 제목, 부제목, URL에서 로드된 표지 이미지, 좌측 하단에 표시되는 코멘트 수가 포함되어 있습니다. 사용자는 각 기사 오른쪽 상단의 북마크 버튼을 눌러 해당 기사를 즐겨찾기에 추가할 수도 있습니다. 툴바에는 두 개의 버튼이 있습니다. 코멘트 수를 업데이트하는 새로고침 버튼과 애니메이션 데모 목적으로 기사의 순서를 임의로 변경하는 재정렬 버튼입니다.

아래는 소스 코드와 앱이 동작하는 모습을 보여주는 동영상입니다.

![앱 동작 예시](https://miro.medium.com/v2/resize:fit:640/1*kRBq0EXx36YlMoTCuulNuw.gif)

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

우리는 재정렬 애니메이션이 원하는 대로 동작하는 것을 볼 수 있습니다. 그러나 사용자가 기사를 북마크하거나 댓글 수를 새로고침할 때의 애니메이션을 살펴보겠습니다.

![이미지](https://miro.medium.com/v2/resize:fit:640/1*fWIvJ-K4PoONrlIZNzys1Q.gif)

## 기사가 업데이트될 때 "깜빡이" 효과가 나타나는 이유는 무엇인가요?

기본적으로 두 항목(우리의 경우 기사)이 동일하지만 다른 콘텐츠(우리의 경우 댓글 수 또는 기사 북마크 여부)를 가질 때 RecyclerView는 새 항목 보기를 렌더링한 다음 이전 항목 보기와 새 항목 보기 사이에 크로스 페이드를 수행하여 GIF에서 볼 수 있는 "깜빡임" 효과를 초래합니다.

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

특히 더 어두운 배경이 사용되거나 사용자 상호작용의 결과로 업데이트될 때 이 기능은 이상하게 보일 수 있습니다. 이는 북마크 또는 북마크 취소와 같은 사용자 상호작용으로 인한 업데이트가 발생할 때 더 문제가 됩니다.

## 성능은 어떻게 해결할까요?

"깜빡"이 발생하는 애니메이션 외에 다른 문제는 항목 뷰를 완전히 다시 바인딩한다는 점입니다. 이 방법은 효율적이지 않습니다. 우리는 댓글 수를 업데이트하거나 북마크 아이콘을 변경하기를 원할 뿐이지만, 대신 전체 항목 뷰를 다시 그리고 다시 렌더링하고 있습니다. 이는 이미 필요하지 않은 이미지를 다시로드 하는 것을 포함합니다.

## 이 문제를 어떻게 해결할 수 있을까요?

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

"“RecyclerViewanimation”을 비활성화하는 방법을 검색 중이라면 나타나는 해결책 중 하나는 RecyclerView. itemAnimator에 supportsChangeAnimation = false를 설정하거나 itemAnimator = null로 설정하는 것입니다.

그러나 이 방법은 항목의 순서가 변경되거나 새 항목이 예전 항목을 대체할 때 발생하는 모든 애니메이션도 비활성화되어 우리가 원하는 대상이 아닐 수 있습니다. 우리는 기존 항목의 속성이 변경될 때 교차 페이드 애니메이션을 제외한 모든 애니메이션을 유지하고 싶습니다. 또한, 이 해결책은 우리가 언급한 효율성/성능 문제를 다루지 않습니다.

## Payloads

페이로드를 사용하여 애니메이션 및 효율성 문제를 모두 해결할 수 있습니다. 페이로드는 이미 정의한 객체로, 이미 존재하는 항목 뷰를 완전히 다시 바인딩하는 대신 일부만 업데이트할 수 있게 해줍니다."

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

RecyclerView.Adapter의 함수들을 좀 더 자세히 살펴보면, onBindViewHolder(holder: ViewHolder, position: Int, payloads: MutableList`Any`)라는 추가 함수를 오버라이드할 수 있는데, 이 함수는 onBindViewHolder(holder: ViewHolder, position: Int) 함수를 오버로딩한 것으로 payloads라는 추가 인수가 있습니다. 이 함수에 대한 문서를 살펴보면 다음과 같이 설명되어 있습니다:

변화 페이로드를 어떻게 얻을 수 있을까요? DiffUtil.ItemCallback에서 사용 가능한 함수들을 좀 더 자세히 살펴보면 기존의 함수인 areItemsTheSame(oldItem: Item, newItem: Item): Boolean과 areContentsTheSame(oldtItem: Item, newItem: Item): Boolean 외에도 fun getChangePayload(oldItem: Item, newItem: Item): Any?라는 추가 함수를 오버라이드할 수 있습니다.
이 함수는 이전 항목과 새 항목이 동일하지만 내용이 다른 경우 호출됩니다. 항목의 어떤 속성이 다른지 감지하고, 항목 뷰를 부분적으로 업데이트할 수 있는 객체를 반환할 수 있게 해줍니다.

여기서 우리는 이전 항목과 새 항목을 비교하고, 댓글 수가 다른 경우에는 ArticleChangePayload.Comments의 인스턴스를 반환하여 나중에 어떤 뷰를 업데이트해야 하는지 알 수 있게 합니다. 북마크 상태에 대해서도 동일한 작업을 수행하고, ArticleChangePayload.Bookmark를 반환합니다. 그리고 다른 변경 사항의 경우에는 간단히 super 함수를 호출하여 null을 반환하게 하여 전체 재바인딩이 되도록 합니다.

이렇게하면 이제 onBindViewHolder 함수에서 payloads 인수를 확인할 수 있습니다. 이 것은 여러 스레드에서 병합된 여러 업데이트가 될 수 있기 때문에 리스트 형태로 제공됩니다. 문서에서 언급된 것처럼요:

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

각 리스트를 하나하나 처리할지 아니면 리스트에서 마지막 항목만 가져올지 결정할 수 있어요.

우리 경우에는 마지막 항목을 확인하고, 만약 그것이 ArticleChangePayload.Comments 유형이라면 댓글 수 TextView를 업데이트하고, ArticleChangePayload.Bookmark 유형이라면 북마크 이미지 버튼을 업데이트할 거에요. 또한 페이로드가 비어 있거나 알 수 없는 유형인 경우를 처리하는 것이 중요해요. 그런 경우에는 원래의 onBindViewHolder(holder: ViewHolder, position: Int) 함수를 호출하여 완전히 다시 바인딩해야 해요. 이는 오버로드된 onBindViewHolder 함수의 기본 구현입니다.

이 기능을 추가한 후에 이제 데이터가 변경된 부분만 뷰에 업데이트되고 깜박거림 효과가 없어졌다는 것을 확인할 수 있어요. 완벽해요.

![이미지](https://miro.medium.com/v2/resize:fit:640/1*OevReNMqnU9pngRAEUxmWQ.gif)

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

그리고 여기에 업데이트된 어댑터 구현이 있습니다.

## 결론

저희는 DiffUtil, ListAdapter 및 payloads를 사용하여 RecyclerView 콘텐츠를 가장 효율적으로 업데이트하는 방법을 살펴보았습니다. 우리가 글 목록을 보여주는 앱을 가지고 있고 좋아요 수나 조회수를 자주 업데이트하려는 경우, payloads를 사용하면 전체 항목을 다시 부풀리고 다시 그리는 대신 변경된 뷰만 효율적으로 업데이트할 수 있습니다.

샘플 앱의 소스 코드는 여기에서 확인할 수 있습니다: https://github.com/landomen/recyclerview-payloads-sample
