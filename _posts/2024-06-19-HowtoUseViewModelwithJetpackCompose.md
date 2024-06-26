---
title: "Jetpack Compose와 함께 ViewModel 사용하기"
description: ""
coverImage: "/assets/img/2024-06-19-HowtoUseViewModelwithJetpackCompose_0.png"
date: 2024-06-19 13:50
ogImage:
  url: /assets/img/2024-06-19-HowtoUseViewModelwithJetpackCompose_0.png
tag: Tech
originalTitle: "How to Use ViewModel with Jetpack Compose"
link: "https://medium.com/@betulnecanli/how-to-use-viewmodel-with-jetpack-compose-dc543f10bf6f"
---

이 기사는 ViewModel을 Jetpack Compose와 통합하여 데이터를 효율적으로 관리하고 표시하는 방법을 안내합니다.

## ViewModel 이해하기

ViewModel은 Android 아키텍처 컴포넌트의 일부로서 UI 관련 데이터를 라이프사이클에 맞게 관리하는 데 도움이 됩니다. 환경 변경을 살아남고 활동 또는 프래그먼트와 같은 전통적인 Android 컴포넌트보다 긴 수명 주기 동안 UI 관련 데이터를 보관하고 관리하는 데 이상적입니다.

## ViewModel 설정하기

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

먼저 프로젝트에 Jetpack Compose가 설정되어 있는지 확인하세요. 이곳에는 Kotlin을 사용하여 Jetpack Compose를 이용한 ViewModel의 기본 설정이 있습니다:

FoodCategoriesUiState: 이 sealed 인터페이스는 UI가 취할 수 있는 다양한 상태를 정의합니다: 로딩, 성공 (사진 목록 포함), 오류. 이를 통해 UI 상태를 구조적으로 관리하고 표현할 수 있습니다.

FoodCategoriesViewModel: 이 클래스는 ViewModel을 확장하며 Food Categories 사진을 표시하기 위한 UI 관련 데이터를 관리합니다.

foodCategoriesUiState: 이 속성은 mutableStateOf를 사용하여 초기화된 로딩 상태인 UI의 현재 상태를 나타냅니다. Jetpack Compose가 이를 관찰하고 UI를 업데이트할 수 있는 반응적 상태입니다. 외부 클래스가 직접 수정할 수 없도록 private로 설정되어 있습니다.

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

초기화 블록: FoodCategoriesViewModel을 초기화하면 getFoodCategories()가 호출되어 즉시 Food Categories 사진을 가져오는 프로세스가 시작됩니다.

getFoodCategories(): 이 함수는 viewModelScope에서 코루틴을 시작합니다. Food Categories 사진을 비동기적으로 가져오기 전에 foodCategoriesUiState를 로딩으로 업데이트합니다.
코루틴 내부:

- getFoodCategories() 메서드를 사용하여 foodCategoriesRepository에서 사진을 가져오려고 시도합니다.
- 성공하면(try 블록), 가져온 사진과 함께 foodCategoriesUiState를 성공으로 업데이트합니다.
- IOException 또는 HttpException이 발생하면(catch 블록), foodCategoriesUiState를 에러로 업데이트합니다.

팩토리 동반 객체: 이 객체는 FoodCategoriesViewModel 인스턴스를 인스턴스화하는 데 사용할 수 있는 ViewModelProvider.Factory를 제공합니다. ViewModel 인스턴스에 리포지토리와 같은 의존성이 필요한 경우에는 팩토리 패턴을 사용하는 것이 일반적입니다.

viewModelFactory: 이는 androidx.lifecycle.viewmodel 라이브러리에서 제공되는 함수입니다. ViewModelProvider.Factory를 만들어 ViewModel 인스턴스를 람다(초기화자)를 사용하여 초기화할 수 있게 해줍니다.

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

초기화 Lambda: 람다 내부에서는 FoodCategoriesApplication에서 FoodCategoriesRepository 종속성을 검색합니다. 이는 APPLICATION_KEY를 사용하여 수행되는데, 이는 맵 형태 구조(바로 이 구조)에서 애플리케이션 인스턴스를 검색하는 데 사용되는 키입니다.

의존성 주입: 이 팩토리 패턴을 사용함으로써 FoodCategoriesViewModel은 종속성(foofCategoriesRepository)이 주입된 상태로 인스턴스화될 수 있습니다. 이는 관심사의 분리를 촉진하고 종속성의 목 표현을 주입함으로써 단위 테스트를 용이하게 합니다.

📖 FoodCategoriesViewModel 클래스는 Jetpack Compose와 통합하여 Food Categories 사진 검색이 repository(foofCategoriesRepository)로부터 어떻게 상태를 관리하는지에 관여합니다. 이는 UI (foofCategoriesUiState)가 데이터를로드하는 중인지, 성공적으로 가져 왔는지 또는 가져오는 동안 오류가 발생했는지를 반영하도록 보장합니다. 이 설정은 Kotlin 코루틴(viewModelScope.launch)을 사용하여 비동기 작업과 Jetpack Compose의 반응형 상태 관리(mutableStateOf)를 통해 상태 변경에 따라 UI 업데이트를 제공합니다.

## HomeScreen

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

👉 홈 화면 Composable 기능:

- foodCategoriesUiState: FoodCategoriesViewModel에서 관리되는 UI의 현재 상태를 나타냅니다.
- retryAction: FoodCategoriesViewModel에서 전달되는 람다 함수로, 오류가 발생한 경우 사진을 다시 가져오는 처리를 담당합니다.
- modifier: 레이아웃과 모양 속성을 지정할 수 있게 해주는 Compose Modifier입니다.

👉 When 표현식:

foodCategoriesUiState에 따라 다음이 실행됩니다:

- Loading: 로딩 인디케이터를 표시하는 LoadingScreen을 호출합니다.
- Success: 사진 목록(foodCategoriesUiState.photos)을 전달하여 그리드 형태의 사진을 보여주는 PhotosGridScreen을 호출합니다.
- Error: 에러 메시지와 다시 시도하는 버튼이 함께 표시되는 ErrorScreen을 호출합니다.

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

## ViewModel을 Jetpack Compose와 통합하기

👉 FoodCategoriesApp 콤포저블 함수:
@Composable: 이 함수가 Jetpack Compose를 사용하여 콤포저블 UI 요소를 정의한다는 것을 나타냄.
Scaffold: 상단 바, 컨텐츠 영역 및 선택적인 플로팅 액션 버튼을 포함하는 앱을 위한 기본 레이아웃 구조를 제공.
Surface: 내용을 그리기 위한 컨테이너로, 지금의 경우 사용 가능한 공간 전체를 차지함(fillMaxSize() 수정자).
viewModel: androidx.lifecycle.viewmodel.compose에서 ViewModel 인스턴스(FoodCategoriesViewModel)를 제공하는 공장(FoodCategoriesViewModel.Factory)을 사용하여 가져오는 콤포저블 함수.
HomeScreen: FoodCategoriesViewModel에서 foodCategoriesUiState 및 retryAction을 전달 받는 HomeScreen.kt에 정의된 다른 콤포저블 함수.

👉 ViewModel 통합:
viewModel(factory = FoodCategoriesViewModel.Factory): 이 줄은 앞에서 정의된 Factory를 사용하여 FoodCategoriesViewModel의 인스턴스를 가져옴. 이는 음식 카테고리 사진을 가져와 표시하는 관련 상태를 관리함.
FoodCategoriesViewModel 인스턴스(foodCategoriesViewModel)는 UI 상태(foodCategoriesUiState)를 관리하며 리포지토리에서 음식 카테고리 사진을 가져오는 메서드(getFoodCategories)를 제공함.

요약하면, FoodCategoriesApp은 Jetpack Compose의 Scaffold와 Surface를 사용하여 앱의 기본 구조를 설정하고, UI 상태 및 데이터 가져오기를 관리하기 위해 FoodCategoriesViewModel을 통합함. 반면에 HomeScreen은 FoodCategoriesViewModel에서 관리되는 다양한 상태(로딩, 성공, 오류)에 기반한 UI 구성 요소를 정의함. 함께 사용하여 Jetpack Compose와 ViewModel 아키텍처를 사용하여 안드로이드 앱에서 동적이고 반응적인 UI를 구축하는 방법을 보여줌.

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

![How to Use ViewModel with Jetpack Compose](/assets/img/2024-06-19-HowtoUseViewModelwithJetpackCompose_0.png)
