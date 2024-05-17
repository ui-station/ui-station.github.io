---
title: "앱 아키텍처 프레젠테이션 레이어"
description: ""
coverImage: "/assets/img/2024-05-17-AppArchitecturePresentationlayer_0.png"
date: 2024-05-17 18:36
ogImage: 
  url: /assets/img/2024-05-17-AppArchitecturePresentationlayer_0.png
tag: Tech
originalTitle: "App Architecture: Presentation layer"
link: "https://medium.com/proandroiddev/app-architecture-presentation-layer-0d704ede2564"
---


![Presentation Layer](/assets/img/2024-05-17-AppArchitecturePresentationlayer_0.png)

오늘은 우리 아키텍처의 프레젠테이션 레이어를 살펴보겠습니다. 이 레이어에는 UI 관련 로직과 사용자가 볼 수 있고 상호 작용할 수 있는 모든 것이 포함됩니다. 또한 이 레이어는 애플리케이션 데이터를 사용자가 읽을 수 있는 형태로 해석하고 반대로 사용자 상호 작용을 앱의 데이터 변경으로 변환하는 것을 담당합니다.

![UI Layer](/assets/img/2024-05-17-AppArchitecturePresentationlayer_1.png)

이 안내서에서는 UI 레이어를 구현하고 구성하는 방법을 보여줄 것입니다. UI 레이어를 구축하기 위해 사용할 수 있는 많은 라이브러리, 프레임워크 및 패턴이 있습니다. 오늘은 Android Fragment + Jatpack Compose + Orbit-MVI + Kotlin + Flow + Coroutines + Koin (DI) 기술 스택을 기반으로 모든 것을 구축할 것입니다. 이것은 최적의 조합 중 하나입니다. 저는 Router, Router Container, ViewModel, Screen, Navigator와 같은 UI 구성 요소에 초점을 맞출 것입니다.

<div class="content-ad"></div>

# 라우터

**라우터**는 다음과 같은 기능을 수행하는 메인 UI 유닛입니다:

- ViewModel, Navigator 및 화면 구성 요소와 함께 UI 요소를 보유합니다.
- UI 상태를 소비합니다.
- Side Effects(일회성 액션)를 처리합니다.
- 사용자 상호 작용을 ViewModel로 전달합니다.

라우터 뒤에 있는 주요 아이디어는 UI 상태를 생성하고 관리하는 방법을 알고 있으며 탐색할 수 있는, 자체 지속 가능한 UI 유닛을 캡슐화하는 것입니다.

<div class="content-ad"></div>

## 네이밍 규칙

Route 클래스는 해당하는 화면 이름에 따라 이름이 지정됩니다.

화면 이름 + Route

예를 들어: FareListRoute, ConformationRoute.

<div class="content-ad"></div>

위의 코드에서는 Router가 어떻게 보이는지 확인할 수 있습니다. 매개변수로 전달되는 ViewModel은 Koin(DI)에 의해 주입됩니다. 이와 함께, 이전 화면에서 전달된 Navigator와 ryderId를 데이터로 전달합니다. Koin의 멋진 기능 중 하나는 ryderId를 ViewModel 생성자에 주입할 수 있다는 것입니다.

ViewModel 섹션에서 다룰 것입니다. Router에서는 ViewModel이 보유한 상태를 수집하고, 해당 상태를 Screen에 매개변수로 전달합니다.

collectAsState는 ViewModel이 Orbit 라이브러리에서 구현하는 ContainerHost의 확장 기능입니다.

lifecycleState — 이 Flow 작업을 다시 시작하는 Lifecycle이 유지됩니다.

<div class="content-ad"></div>

Markdown으로 표 태그를 변경하세요.

```markdown
| Header 1 | Header 2 |
| -------- | -------- |
| Data 1   | Data 2   |
```

<div class="content-ad"></div>

## 명명 규칙

이 클래스들은 그들이 책임지고 있는 UI 구성 요소에 따라 이름이 지어집니다.

UI 구성 요소 이름 + Fragment.

예를 들어 FareListFragment.

<div class="content-ad"></div>

UI 구성 요소 이름 + 컨트롤러.

예를 들어 FareListController.

프래그먼트 클래스의 코드가 작은 이유는 모든 UI 로직이 Route에 캡슐화되어 있기 때문입니다. 이는 컨테이너 구현을 쉽게 변경할 수 있습니다.

이것이 왜 네비게이터의 주입 로직이 Router가 아닌 Fragment 안에 있는 이유입니다. 네비게이터는 네비게이션 로직을 구현하기 위해 Fragment NavController를 필요로하기 때문입니다. 이렇게 함으로써 Router와 컨테이너 구현이 분리되고, 우리가 컨테이너 구현을 쉽게 변경할 수 있게 해줍니다. 예를 들면 — Compose 네비게이션이나 Conductor 라이브러리의 Controller를 사용하는 것과 같이요.

<div class="content-ad"></div>

# 네비게이터

네비게이터는 다음을 담당합니다:

- 라우터에서의 탐색 로직을 캡슐화합니다.
- 특정 화면을 위한 탐색 API를 제한합니다.
- 각 화면을 위해 명확한 API를 정의합니다.

화면이 다른 화면으로 탐색해야 하는 경우, 해당 화면에 자체 네비게이터 클래스가 있어야 합니다. 이는 기본 ScreenNavigator에서 기본적인 뒤로 가기 동작을 가진 확장되어 있을 수 있으며, 다른 네비게이터와 Fragment NavController와 같은 플랫폼 의존적인 구성 요소를 포함할 수 있습니다.

<div class="content-ad"></div>

## 네이밍 규칙

네비게이터 클래스는 해당하는 화면 이름 뒤에 붙여지는 이름입니다:

화면 이름 + 네비게이터.

예를 들어 FareListNavigator.

<div class="content-ad"></div>

앱 아키텍처 프레젠테이션 계층 다이어그램을 살펴보는 걸로 하죠.

![다이어그램](/assets/img/2024-05-17-AppArchitecturePresentationlayer_2.png)

여기서 Core, Shared와 같은 핵심 모듈, Fare 및 Profile과 같은 기능 모듈, 그리고 App 모듈을 볼 수 있어요.

예를 들어, Fare 모듈이 있는데 그중 하나는 사용자의 프로필 화면으로 이동하는 버튼이 있는 기능이 있어요. 사용자 프로필 페이지는 Profile 모듈에 있죠. 이 네비게이션을 어떻게 구현하면 좋을까요?

<div class="content-ad"></div>

그걸 위해, 사용자 프로필 페이지로 이동하는 방법을 아는 ProfileSharedNavigator 인터페이스를 만들어야 하고, Shared 모듈에 유지해야 합니다.

우리 아키텍처에 따르면 Fare 모듈은 Shared에 의존하므로 FareListNavigator에서 ProfileSharedNavigator를 사용할 수 있습니다.

우리는 FareListNavigator에 ProfileSharedNavigator를 인수로 전달하고, 그곳에 있는 내비게이션 호출을 위임합니다.

ScreenNavigator는 이동하는 방법만을 알고 있는 베이스 클래스입니다.

<div class="content-ad"></div>

앱 모듈은 앱 내 모두에 대해 모든 정보를 알고 있습니다. 이 모듈의 주요 목적은 프로젝트의 모든 피처 모듈 사이에 있는 모든 의존성 주입 로직을 조직화하는 것입니다.

AppNavigator가 ProfileSharedNavigator 인터페이스의 실제 구현을 보유하고 있음을 확인할 수 있습니다. 이 인터페이스에 다양한 모듈에서 의존할 수 있으며, 의존성 역전 원칙 (DIP)을 따라 App 모듈에서 이를 구현할 수 있습니다.

# 상태

상태 파일에는 UI 상태 데이터 클래스가 포함되어 있으며, 사이드 이펙트 시그니처가 가장 적합합니다. 상태 클래스는 구성 변경을 통해 상태가 보존되길 원할 경우 Parcelable(선택 사항)할 수 있습니다. 모든 속성은 가능하다면 기본값을 가져야 합니다. Effect 클래스에는 UI에서의 일회성 작업이 포함되어 있습니다. 이는 네비게이션, 토스트, 스낵 바, 바텀 시트 또는 대화 상자와 같은 것입니다. Effect에 대해 자세히 알아보려면 Orbit Side Effect 문서를 읽어볼 수 있습니다.

<div class="content-ad"></div>

## 네이밍 규칙

상태 클래스는 담당하는 UI 구성 요소 유형에 따라 명명됩니다. 규칙은 다음과 같습니다:

UI 구성 요소 이름 + State.

UI 구성 요소 이름 + Effect.

<div class="content-ad"></div>

예를 들어, FareListState 및 FareListEffect.

화면에 다른 로딩 상태가 있는 경우, 상태 클래스에서 명확하게 분리하는 것이 좋습니다. 화면에는 Idle, Loading, Refreshing, Success, Failure와 같은 몇 가지 콘텐츠 상태가 있을 수 있습니다. 초기 콘텐츠 로딩 후 서버에 요청을 보내어 사용자에게 로딩을 표시하고 싶을 때, status 필드를 사용하여 ScreenContentStatus.Loading을 설정하는 대신 별도의 showRequestLoading 속성을 사용하여 로딩 대화상자를 표시하는 것이 좋습니다. 하나의 필드를 재사용하여 다양한 로딩 케이스를 처리하려고 하지 말아야 합니다.

# 모델

프레젠테이션 레이어에는 도메인 레이어의 모델을 반영하지만 더 UI에 특화된 데이터 모델이 있습니다. 프레젠테이션 모델과 도메인 간의 매핑 로직은 ViewModel 클래스에 배치해야 합니다.

<div class="content-ad"></div>

- 프레젠테이션 레이어는 다른 레이어에게 UI 모델을 노출해서는 안 돼요.
- 프레젠테이션 모델은 Parcelable 및 Serializable과 같은 플랫폼별 직렬화 방식을 구현할 수 있어요.
- 프레젠테이션 모델은 변경 불가능해야 해요.

## 네이밍 규칙

모델 클래스는 그들이 책임지는 데이터 유형을 나타내는 이름으로 지어져요:

데이터 유형 + Model.

<div class="content-ad"></div>

예를 들어: Ryder, Fare.

## ViewModel

ViewModel은 비즈니스 로직 상태 보유자입니다. Android 개발에서 ViewModel은 비즈니스 로직에 액세스를 제공하고 응용 프로그램 데이터를 화면에 표시하기 위해 적합합니다. 또한 사용자 이벤트를 처리하고 데이터나 도메인 계층에서 화면 UI 상태로 데이터를 변환합니다.

현재 구현에서 androidx.lifecycle.ViewModel과 Orbit-MVI lib을 사용하고 있습니다. ViewModel은 Orbit 컨테이너를 보유하고 ContainerHost를 구현합니다. 무엇이 진행 중인지 더 잘 이해하기 위해 Orbit API 문서를 확인해보세요.

<div class="content-ad"></div>

## 네이밍 규칙

뷰모델 클래스는 그들이 책임지는 UI 구성 요소 유형에 따라 명명됩니다:

UI 구성 요소 이름 + ViewModel.

예를 들어, FareListViewModel.

<div class="content-ad"></div>

위의 코드에서 ViewModel의 예제를 확인할 수 있습니다. 거기에서 무슨 일이 일어나고 있는지 알아보겠습니다.

먼저 생성자부터 시작해 보겠습니다. 알 수 있듯이, 도메인 레이어에서 유스 케이스를 주입하고, 이전 화면에서 전달한 ryderId 및 ExceptionHandler를 주입합니다. ViewModel은 여러 개의 유스 케이스를 가질 수 있습니다.

아래와 같이 래퍼 클래스에 사용 사례를 넣고

그리고 ViewModel에 넣으려고 하지 마세요

<div class="content-ad"></div>

fetchFares 메소드에서 흥미로운 일이 더 많이 진행됩니다.

Orbit 라이브러리 API에 대해 간략히 설명하겠습니다. Intent 메소드는 Dispatcher.Default에서 람다를 실행합니다. Reduce 메소드는 Dispatcher.Main에서 람다를 실행합니다. 이는 상태를 줄이고 UI 상태를 업데이트합니다.

executeUseCase는 ViewModel의 확장 메소드로, 유증 실행하고 해당 결과를 kotlin.Result`R`으로 랩핑하는 역할을 합니다. Result 클래스의 확장 메소드인 onSuccess, onFailure 등의 메소드를 사용할 수 있게 합니다. 또한, 예외를 ViewModel 핸들러에 전달합니다.

하나의 작업을 위해 2개 이상의 유증을 실행해야 하는 상황에 직면하면 다음 옵션을 고려해보시기 바랍니다:

<div class="content-ad"></div>

- 새 사용 사례를 만들고 모든 로직을 그곳에 넣으세요. 필요한 사용 사례를 결합하세요.
- 여러 사용 사례에서 결과를 기다려야하고 그 결과를 결합해야한다면:

asPresentation() 메소드는 도메인 계층의 데이터 모델을 프레젠테이션 계층의 모델로 매핑하는 역할을 합니다. 계층간 데이터를 전달하는 방법은 여기에서 읽을 수 있습니다.

# 화면

화면 파일에는 UI 구성 구현이 모두 포함되어 있으며, 각 화면 상태에 대한 Compose 미리 보기가 포함되어 있습니다. 상태에는 비어 있는 상태, 오류 상태, 로딩 상태, 콘텐츠 상태 등이 있습니다.

<div class="content-ad"></div>

## 네이밍 규칙

화면 클래스는 해당 UI 구성 요소 유형을 책임지고 있으므로 그에 따라 명명됩니다:

UI 구성 요소 이름 + Screen.

예를 들어 FareListScreen.

<div class="content-ad"></div>

UI를 Compose를 사용하여 빌드할 때 따르는 것이 좋은 몇 가지 규칙이 있습니다.

- 상태를 가지지 않는 Composable을 상태를 가지는 Composable보다 선택하세요. 이에 대해 더 알아보려면 여기를 읽어보세요.
- 모든 콜백을 최상위 화면 Composable에 전달하고 모든 사용자 상호 작용을 라우터 수준의 ViewModel에 전달하세요.
- UI 구성 요소의 다른 상태에 대한 Composable 미리보기를 만드세요.

TopAppBar Composable 함수를 작성해야 한다고 상상해보세요. 타이틀을 매개변수로 전달하는 두 가지 방법이 있습니다. String으로 타이틀을 전달하거나 @Composable () -> Unit 함수로 전달하는 방법이 있습니다.

옵션 1.

<div class="content-ad"></div>

옵션 2를 항상 선택하세요. 그렇게 하면 Composable 함수가 더 맞춤 설정 가능하고 강력해집니다.

## 화면 미리보기

화면 미리보기를 실제 시나리오와 최대한 비슷하게 보이도록 하려면 상태를 만들기 위해 무작위 데이터가 필요합니다. 이를 위해 FareModelFake 클래스를 만들어서 FareModel과 동일한 패키지에 넣을 수 있습니다.

<div class="content-ad"></div>

```js
 model/
├─FareModel
├─FareModelFake
```

FareModelFake 클래스에는 미리보기에 사용할 수 있는 가짜 데이터가 포함된 FareModel이 있습니다.

# 패키징 규칙

```js
presentation/
├─ fare/
│ ├─ component/
│ │ ├─ FareList
│ │ ├─ FareItem
│ ├─ model/
│ │ ├─ FareModel
│ │ ├─ FareModelFake
│ ├─ FareListFragment
│ ├─ FareListNavigator
│ ├─ FareListRoute
│ ├─ FareListScreen
│ ├─ FareListState
│ ├─ FareListViewModel
├─ confirmation/
│ ├─ component/
│ │ ├─ ConfirmationItem
│ ├─ model/
│ │ ├─ ConfirmationModel
│ │ ├─ ConfirmationModelFake
│ ├─ ConfirmationFragment
│ ├─ ConfirmationNavigator
│ ├─ ConfirmationRoute
│ ├─ ConfirmationScreen
│ ├─ ConfirmationState
│ ├─ ConfirmationViewModel
```

<div class="content-ad"></div>

# 마무리

프레젠테이션 레이어를 구현하는 다양한 방법이 있습니다. 오늘은 프레젠테이션 레이어를 구현하는 방법에 대해 몇 가지 아이디어를 공유했습니다. 이 접근 방식을 따르거나 여러분의 구현에 몇 가지 아이디어를 활용할 수 있습니다.

GitHub에서 샘플 프로젝트를 확인할 수 있습니다.

다음 앱 아키텍처 주제에 대한 소식을 기다려주세요.