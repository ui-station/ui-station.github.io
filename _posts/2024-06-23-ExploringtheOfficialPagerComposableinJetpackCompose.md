---
title: "Jetpack Compose의 공식 Pager 컴포저블 탐구하기"
description: ""
coverImage: "/assets/img/2024-06-23-ExploringtheOfficialPagerComposableinJetpackCompose_0.png"
date: 2024-06-23 21:00
ogImage: 
  url: /assets/img/2024-06-23-ExploringtheOfficialPagerComposableinJetpackCompose_0.png
tag: Tech
originalTitle: "Exploring the Official Pager Composable in Jetpack Compose"
link: "https://medium.com/@domen.lanisnik/exploring-the-official-pager-in-compose-8c2698c49a98"
---


1.4 버전이 출시되면서 Jetpack Compose가 공식적으로 페이징 레이아웃을 지원하게 되었습니다. 뷰 기반 시스템에서는 ViewPager 위젯 형태로 오랫동안 사용되어 왔지만, 개발자들은 Compose에서 유사한 효과를 얻기 위해 Accompanist 라이브러리 구현을 사용해야 했습니다.

새로운 컴포저블 두 개가 추가되었습니다. HorizontalPager와 VerticalPager. 사용법과 동작을 좀 더 자세히 살펴봅시다.

## HorizontalPager

HorizontalPager는 사용자 또는 프로그래밍 방식으로 좌우로 수평 스크롤할 수 있는 컴포저블입니다.

<div class="content-ad"></div>


![image](https://miro.medium.com/v2/resize:fit:800/1*AP2jKexaHqpaCoDt9zPh3Q.gif)

## VerticalPager

VerticalPager는 사용자 또는 프로그래밍적으로 수직으로 위아래로 스크롤할 수 있게 하는 조합성이다.

![image](https://miro.medium.com/v2/resize:fit:800/1*CMRnteybuokM-9AkJUqxRA.gif)


<div class="content-ad"></div>

## 더 자세히 살펴보기

페이지/아이템은 필요할 때만 게으르게 구성되어 배치됩니다. 이는 LazyColumn 및 LazyRow 컴포저블과 유사합니다. 위 모든 컴포저블은 내부적으로 LazyList 컴포저블을 사용합니다.

페이저 컴포저블 모두 내부 Pager 컴포저블을 다른 값으로 orientation 인자에 전달하는 래퍼입니다.

pageCount를 제외한 모든 인자는 선택적입니다:

<div class="content-ad"></div>

- pageCount: 페이저에서 표시할 총 페이지/아이템 수입니다.
- pageSize: 페이저 내의 페이지 크기를 정의합니다.
- pageSpacing: 페이저 내 두 페이지 사이의 간격을 정의합니다.
- contentPadding: 페이저 내에서 페이지가 정렬되는 방식을 지정합니다.
- beyondBoundsPageCount: 현재 보이는 페이지 이상으로 로드할 페이지 수를 지정합니다.
- state: 페이저를 제어하고 현재 선택된 페이지와 같은 다양한 속성을 관찰하는 PagerState 객체입니다.
- flingBehavior: 스크롤 제스처의 동작 방식을 정의합니다.
- reverseLayout: 레이아웃과 스크롤 방향을 반전시킵니다. 첫 번째 페이지가 마지막 페이지로 표시되고 페이저의 끝이 아닌 시작 쪽으로 스크롤해야 합니다.
- key: 항목을 나타내는 안정적이고 유일한 키입니다. 이를 사용하여 항목을 추가하거나 제거할 때에도 스크롤 위치를 유지할 수 있습니다.

이러한 인수에 다른 값을 전달하는 방법이 페이저의 동작에 어떻게 영향을 주는지 자세히 알아보겠습니다.

## 페이지 크기

pageSize: PageSize 인수를 사용하여 페이지 크기를 제어할 수 있습니다. 기본적으로 이 값은 PageSize.Fill로 설정되어 있어 HorizontalPager의 경우 전체 너비 또는 VerticalPager의 경우 전체 높이를 차지합니다.

<div class="content-ad"></div>


![image](https://miro.medium.com/v2/resize:fit:800/1*XeEa09R4_NnVH-yk0RA9AA.gif)

페이지 크기를 고정 크기로 정의하려면 PageSize.Fixed(dp)를 사용할 수 있습니다.

![image](https://miro.medium.com/v2/resize:fit:800/1*dpCJNBPMMZLEzQ2zD6267g.gif)

가끔은 사용자 정의 계산에 따라 크기를 정의해야 할 때가 있습니다. 이를 위해 PageSize 인터페이스를 확장하고 calculateMainAxisPageSize(availableSpace: Int, pageSpacing: Int) 함수를 구현할 수 있습니다. 아래 예제에서는 사용 가능한 너비의 80%를 차지하는 HorizontalPager가 있고, 페이지 크기를 사용 가능한 너비의 50%로 설정합니다. 이렇게 하면 한 번에 두 페이지를 볼 수 있습니다.


<div class="content-ad"></div>


![Image](https://miro.medium.com/v2/resize:fit:800/1*BS5XgH4dP9-4Il5SFaxqVA.gif)

## 페이지 간격

pageSpacing: Dp 인수를 사용하여 페이지 사이에 사용할 공간의 양을 정의할 수 있습니다. 이전에 pageSize를 PageSize.Fixed(dp)로 정의하고 간격으로 8.dp를 사용하는 예제를 사용하면 다음과 같은 결과를 얻을 수 있습니다:

![Image](https://miro.medium.com/v2/resize:fit:800/1*3aTc7YErEY0MKqgnbY0ijg.gif)


<div class="content-ad"></div>


![Image](https://miro.medium.com/v2/resize:fit:800/1*hwZ22hqTVXFfILxsNzDM_w.gif)

## 내용 패딩

contentPadding: PaddingValues 인수를 사용하면 페이저 안의 페이지 위치를 제어할 수 있습니다.

여기서 start 및 end 속성에 동일한 패딩을 설정하여 페이지를 페이저 중앙에 배치합니다. 또한 HorizontalPager의 경우 상단 및 하단에 동일한 수직 패딩을 적용합니다.


<div class="content-ad"></div>


![image](https://miro.medium.com/v2/resize:fit:800/1*MMgcNXPLncPODVgMQjgfpg.gif)

When you apply the start padding alone, the pages are shifted towards the end, causing a portion of the previous page to be visible.

![image](https://miro.medium.com/v2/resize:fit:800/1*nhxpvTwxRxvaItT9kFvMWw.gif)

When you apply the end padding alone, the pages are shifted towards the start, causing a portion of the next page to be visible.


<div class="content-ad"></div>


![image](https://miro.medium.com/v2/resize:fit:800/1*LiD4FxaDr2XQLp8nvItQRA.gif)

수직 페이저에도 동일한 원칙이 적용되지만, top, bottom 및 vertical padding 속성은 페이지의 정렬을 제어합니다. top padding을 적용하면 페이지가 아래쪽으로 이동됩니다. 그리고 bottom padding을 적용하면 페이지가 위쪽으로 이동됩니다.

한편 start, end 및 horizontal은 일반적인 padding만 적용됩니다.

![image](/assets/img/2024-06-23-ExploringtheOfficialPagerComposableinJetpackCompose_0.png)


<div class="content-ad"></div>

## BeyondBoundsPageCount

이 인수는 현재 표시된 페이지 주변에 심지어 보이지 않을 때도로드해야 하는 페이지/항목 수를 지정합니다. 기본값은 0으로 설정됩니다.

항목의 높이를 감싸고 각 항목에 맞게 크기를 조정하는 페이저를 원한다면, HorizontalPager에 beyondBoundsPageCount = 0으로 Modifier.wrapContentHeight()를 사용할 수 있습니다.

![이미지](https://miro.medium.com/v2/resize:fit:800/1*NDlSiw0m8Ts87Rgxu_yFNA.gif)

<div class="content-ad"></div>

그러나 페이저가 가장 높은 항목의 높이에 맞춰져야 하는 경우 beyondBoundsPageCount = pageCount로 지정할 수 있습니다.

![이미지](https://miro.medium.com/v2/resize:fit:800/1*a-4Jh2cpNCzfzKxg6J4RMw.gif)

큰 값을 설정하는 경우 성능이 저하될 수 있다는 문서를 참고하세요:

## 페이지 상태 변경 관찰

<div class="content-ad"></div>

가끔 페이지 변경에 반응하고 싶을 때가 있습니다. 분석 이벤트를 보내거나 어떤 로직을 발동하거나 다른 화면으로 이동하고 싶은 경우가 있습니다. 이를 위해 LaunchedEffect 내에서 PagerState 객체의 네 가지 속성을 snapshotFlowOf를 사용하여 관찰할 수 있습니다:

- currentPage: 현재 선택된 페이지 또는 스냅 위치에 가장 인접한 페이지입니다.
- targetPage: 스크롤 이동 결과로 선택해야 하는 페이지입니다.
- settledPage: currentPage와 유사하지만 애니메이션이 완료될 때까지 변경되지 않습니다.
- currentPageOffsetFraction: 현재 페이지가 스냅 위치에서 얼마나 벗어났는지를 나타내는 범위입니다. -0.5에서 0.5까지의 값으로, 페이저의 시작 쪽으로 스크롤하는 경우에는 음수가 사용되고, 끝 쪽으로 스크롤하는 경우에는 양수가 사용됩니다. 0.0은 현재 페이지가 완전히 스냅된 상태를 나타냅니다.

## 페이지 인디케이터 만들기

페이저와 함께 보통 페이지 인디케이터를 표시하여 현재 어떤 페이지를 보고 있는지와 전체 페이지 수를 표시하고 싶어합니다. 공식 페이지 인디케이터 컴포저블은 없지만, 상당히 간단히 만들 수 있습니다.

<div class="content-ad"></div>


![이미지](https://miro.medium.com/v2/resize:fit:800/1*VCFpbnnn1juWCifnTFF3Pg.gif)

가로 표시기를 적용하는 데 필요한 것은 개별 페이지 표시기를 배치하려면 행(수평 표시기) 또는 열(수직 표시기)을 사용하고, 디자인 요구사항에 따라 표시기를 렌더링하기 위해 상자 또는 아이콘 또는 이미지를 사용할 수 있습니다.

현재 페이지(pagerState.currentPage: Int), 선택될 페이지(pagerState.targetPage: Int)를 나타내는 타깃 페이지 및 스크롤 오프셋(pagerState.currentPageOffsetFraction: Float)을 얻기 위해 PagerState 객체를 사용할 수 있습니다. 이를 통해 선택을 계산하고 애니메이션을 적용할 수 있습니다.

현재 스크롤 오프셋에 따라 현재 선택된 페이지 표시기의 크기와 색상을 애니메이션하는 수평 페이저 표시기 예시를 제공합니다.


<div class="content-ad"></div>

## 특정 페이지로 스크롤하기

Pager에서 특정 페이지로 프로그래밍적으로 스크롤할 수 있습니다. PagerState 객체를 작성하여 pager에 전달한 다음에 pagerState.scrollToPage(page: Int) 또는 pagerState.animateScrollToPage(page: Int)을 CoroutineScope 내에서 호출하면 됩니다.

![이미지](https://miro.medium.com/v2/resize:fit:800/1*va9VoqeZ146bUcx_j4-Q5A.gif)

## 탭과 함께 Pager 사용하기

<div class="content-ad"></div>

자주 탭을 사용하는데 페이저와 함께 사용해야 할 때가 있습니다. 선택된 탭을 표시하고 스크롤하거나 특정 탭을 클릭하여 선택할 수 있도록 합니다. 이를 위해 TabRow 컴포저블과 HorizontalPager를 함께 사용하여 이를 구현할 수 있습니다.

![이미지](https://miro.medium.com/v2/resize:fit:800/1*PR4b0VlQgMfMYuEN08lOyQ.gif)

TabRow 컴포저블을 생성하고 pagerState.currentPage를 selectedTabIndex 인수로 전달해야 합니다. 이렇게 하면 페이저를 스크롤할 때 선택된 탭이 업데이트됩니다. 이 예제에서는 기본 탭 표시기 TabRowDefaults.Indicator를 사용하고 Modifier.tabIndicatorOffset 수정자를 적용하여 기본 선택된 탭 표시기를 그리고 애니메이션화합니다.

이렇게 설정하면 페이저를 스크롤하면 탭이 선택됩니다. 탭을 클릭하여 페이지를 선택하는 기능도 지원하려면 coroutine 스코프 내에서 pagerState.animateScrollToPage(selectedTabIndex) 함수를 호출해야 합니다.

<div class="content-ad"></div>

## 스크롤 동작 사용자 정의

우리는 페이저의 기본 스크롤 동작을 변경할 수 있습니다. 이를 위해 페이저에 사용자 정의 fling 동작인 SnapFlingBehavior를 제공해야 합니다. 또한 PagerState 객체를 생성해야 합니다.

pagerSnapDistance를 사용하여 플링 제스처가 얼마나 많은 페이지를 스크롤할 수 있는지 정의할 수 있습니다. 기본적으로 이 값은 한 페이지로 설정되어 있지만, PagerSnapDistance.atMost(pages: Int)를 사용하여 이를 무시하고 다른 값을 설정할 수 있습니다. 다음은 스냅을 2페이지로 설정하는 예시입니다.

![이미지](https://miro.medium.com/v2/resize:fit:800/1*J3ipnv3bHlSPmzUNZsbDfA.gif)

<div class="content-ad"></div>

lowVelocityAnimationSpec은 스크롤이나 천천히 움직일 때 사용되는 애니메이션을 정의합니다. 예제에서는 이를 5초로 설정하여, 천천히 움직인 후 다음 페이지를 선택하는 애니메이션이 5초 동안 진행됩니다.

highVelocityAnimationSpec은 스크롤이나 아주 빨리 움직일 때 사용되는 애니메이션을 정의합니다.

snapAnimationSpec은 최종적으로 포지션에 스냅되는데 사용되는 애니메이션을 정의합니다. 다음 페이지가 선택될만큼 충분히 스크롤하거나 현재 페이지를 선택한 채로 약간만 스크롤하는 경우를 말합니다. 예제에서는 이를 1초로 설정했습니다.

![이미지](https://miro.medium.com/v2/resize:fit:800/1*UzWgACMOPRWIqSihQhrm5Q.gif)

<div class="content-ad"></div>

## 반주자 페이저 이전

파트너 버전의 페이저가 이제는 사용이 중단되었습니다. Google은 기존의 com.google.accompanist.pager.HorizontalPager에서 androidx.compose.foundation.pager.HorizontalPager로 및 com.google.accompanist.pager.VerticalPager에서 androidx.compose.foundation.pager.VerticalPager로 코드베이스를 이전하는 가이드를 제공했습니다.

마이그레이션을 수행하는 방법에 대한 자세한 정보는 https://google.github.io/accompanist/pager/에서 확인하세요.

## 결론

<div class="content-ad"></div>

Jetpack Compose의 최신 버전이 릴리스되었습니다. 이 버전에는 사용하기 쉽고 사용자 정의할 수 있는 새로운 공식 Pager composable이 포함되어 있습니다.

우리는 이 새로운 composable의 다양한 속성을 살펴보고 pager의 동작에 어떤 영향을 미치는지 배웠습니다. 그런 다음, 자체 페이지 표시자를 만드는 방법과 탭과 함께 pager를 사용하는 방법을 배웠습니다.

이를 통해, 추가 라이브러리가 필요 없으며, 개발자가 빠르게 pager를 필요로 하는 디자인을 구현할 수 있도록 해야 합니다.

## 참고:

<div class="content-ad"></div>

- 공식 문서: https://developer.android.com/jetpack/compose/layouts/pager
- Accompanist pager 라이브러리: https://google.github.io/accompanist/pager/