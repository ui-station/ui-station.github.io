---
title: "Jetpack Compose로 Bottom Navigation Bar 구현하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-JetpackComposeBottomNavigationBar_0.png"
date: 2024-06-23 21:05
ogImage: 
  url: /assets/img/2024-06-23-JetpackComposeBottomNavigationBar_0.png
tag: Tech
originalTitle: "Jetpack Compose Bottom Navigation Bar"
link: "https://medium.com/@jpmtech/jetpack-compose-bottom-navigation-bar-3e1e8749fb2c"
---


이 튜토리얼에서는 깔끔한 API 디자인을 갖고 재사용이 쉬운 하단 네비게이션 바를 구축하는 데 Material 3를 사용할 것입니다.

![하단 네비게이션 바 이미지](/assets/img/2024-06-23-JetpackComposeBottomNavigationBar_0.png)

시작하기 전에 제 글을 좋아하고 👏 박수를 보내주시면 더 많은 사람들이 이 유용한 내용을 배울 수 있도록 도와주세요!

# 구글이 말합니다

<div class="content-ad"></div>

개발자 문서에 중요한 점 하나를 강조할게요. 네비게이션 바에는 "단일 목적지를 나타내는 세 개에서 다섯 개의 NavigationBarItem을 포함해야 한다."고 명시되어 있어요. 그래서 만약 5개 이상의 항목이나 3개 미만의 항목을 표시하고 싶다면, 다른 네비게이션 패턴을 찾아보는 것이 좋을 거예요.

Google이 네비게이션 바에 대해 어떤 내용을 알려주는지 더 알고 싶다면, 여기에서 개발자 문서를 확인해보세요: [https://developer.android.com/reference/kotlin/androidx/compose/material3/package-summary#navigationbar](https://developer.android.com/reference/kotlin/androidx/compose/material3/package-summary#navigationbar)

# 초기 설정

이 튜토리얼에서는 SDK 버전 34를 기반으로 작성하기로 선택했어요. 네비게이션 바를 통해 탐색하기 위해 추가해야 할 의존성이 하나 있을 거에요.

<div class="content-ad"></div>

```js
// App Build.gradle 파일의 deopendencies에 다음을 추가하세요
implementation("androidx.navigation:navigation-compose:2.7.5")
```

의존성을 추가한 후에는 gradle 파일을 동기화하는 것을 잊지 마세요(Android Studio 상단에 "지금 동기화"라는 버튼이 있는 바가 나타날 것입니다).

# Jetpack Compose에서 내비게이션 바 만들기

조금 중복되는 소리일 수 있지만, 안드로이드 개발에서의 bottom navigation bar가 Scaffold 구성요소의 "bottomBar"에 들어간다고 생각하면 됩니다.

<div class="content-ad"></div>

네비게이션 바의 각 항목은 동일한 기본 구성 요소 세트를 가지고 있기 때문에, 탭 바로 아래에 선언된 탭의 제목과 일치하는 NavHost 안에 있는 compose() 객체의 이름을 정확히 맞춤으로써 TabBarItem과 해당 뷰 사이의 연결을 누락하지 않고 스펠링 오류를 방지합니다.

개발자분들을 위한 부가 정보로, 애플의 TabView를 잘 아시는 경우 Android는 하단 네비게이션 바를 사용할 때 기본적으로 내장된 네비게이션을 제공하지 않습니다. 뷰 구성 요소로의 탐색을 직접 추가해야 합니다. Android 개발자의 경우, Apple은 TabView를 사용할 때 뷰로 이동하려는 것을 자동으로 인식하여 추가적인 탐색을 요구하지 않습니다. 두 구현을 옆으로 비교하고 싶다면, 새 탭에서 Apple TabView 튜토리얼을 열어보세요.

코드를 더 읽기 쉽고 따라가기 쉽도록 만들기 위해, NavigationBar API의 몇 가지 작은 구성 요소를 자체 사용자 지정 compose 컴포넌트로 분리하였습니다. 이러한 구성 요소를 분리해서 사용할 필요는 없지만, 코드의 본질적인 부분을 더 쉽게 이해할 수 있는 방법을 제공합니다.

<div class="content-ad"></div>

위의 코드에서는 세 개의 탭에 텍스트 composable을 표시하도록 선택했을 수 있습니다. 이것은 강좌의 TabView와 네비게이션 부분에 중점을 두기 위해 수행되었지만 이것은 다른 composable 유형으로 교체 될 수 있습니다. 이를 보여주기 위해 'More' 탭을 클릭했을 때 표시되는 사용자 정의 MoreView를 만들었습니다.

Markdown 형식으로 코드를 나타냈습니다.

```kotlin
// 여기에 코드가 와야합니다
```

이 기사가 유용했다면, 저를 팔로우하거나 기사에 박수를 치거나 공유하여 다른 사람이 쉽게 찾을 수 있도록 돕는 것을 고려해 주시기 바랍니다.감사합니다!

<div class="content-ad"></div>

해당 주제에 대한 질문이 있거나 같은 작업을 수행할 수 있는 다른 방법을 아시는 경우, 글에 답변하거나 친구와 공유하여 의견을 얻으셔도 좋습니다.

네이티브 모바일 개발에 대해 더 알고 싶다면, 다른 문서들을 확인해보세요: https://medium.com/@jpmtech

네이티브 모바일 개발로 제작된 앱들을 확인하고 싶다면, 제 앱들을 여기에서 확인하세요: https://jpmtech.io/apps

제 작품을 살펴봐 주셔서 감사합니다!