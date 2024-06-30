---
title: "모바일 개발 플랫폼과 그들의 소프트웨어 아키텍처 패턴들 비교"
description: ""
coverImage: "/assets/img/2024-06-30-CommonMobileDevelopmentPlatformsandtheirSoftwareArchitecturePatterns_0.png"
date: 2024-06-30 19:22
ogImage: 
  url: /assets/img/2024-06-30-CommonMobileDevelopmentPlatformsandtheirSoftwareArchitecturePatterns_0.png
tag: Tech
originalTitle: "Common Mobile Development Platforms and their Software Architecture Patterns."
link: "https://medium.com/@oriohac/common-mobile-development-platforms-and-their-software-architecture-patterns-369e0f6e0517"
---


# 소개

모바일 개발은 올바른 방법으로, 적절한 도구와 필요한 소프트웨어 아키텍처로 각 과정을 구현할 때 더 쉬워집니다. 애플리케이션 개발에 사용되는 많은 모바일 개발 플랫폼이 있습니다. 일부는 네이티브 애플리케이션 개발용이고, 다른 일부는 크로스 플랫폼 애플리케이션 개발용입니다. 두 가지 주요 네이티브 개발 플랫폼은 Kotlin, Java, C++을 지원하는 Android Studio(안드로이드 개발을 위한 공식 통합 개발 환경)와 Objective-C 및 Swift를 지원하는 iOS 개발을 위한 공식 통합 개발 환경 인 Xcode입니다. 일부 주요 크로스 플랫폼 플랫폼으로는 Flutter, React Native, Xamarin, Ionic 등이 있습니다. Unity, Unreal Engine, Flutterflow 및 Appgyver 같은 게임 개발 플랫폼 및 노코드 개발 플랫폼도 있습니다.

![Common Mobile Development Platforms and their Software Architecture Patterns](/assets/img/2024-06-30-CommonMobileDevelopmentPlatformsandtheirSoftwareArchitecturePatterns_0.png)

## 목차

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

- 소개
- 네이티브 개발 플랫폼
- 안드로이드 스튜디오
- 소프트웨어 아키텍처 패턴
- 엑스코드
- 소프트웨어 아키텍처 패턴
- 크로스 플랫폼 개발 플랫폼
- 플러터
- 소프트웨어 아키텍처 패턴
- 일부 아키텍처의 장단점
- 개인 경험 및 HNG 인턴십 여정
- 결론

# 네이티브 개발 플랫폼

주요 모바일 애플리케이션 운영 체제 중 두 가지는 안드로이드 OS와 애플 iOS이며, 이 두 운영 체제를 위한 네이티브 개발 플랫폼은 안드로이드 스튜디오와 Xcode입니다.

# 안드로이드 스튜디오

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

안드로이드 스튜디오는 안드로이드 애플리케이션 개발을 위한 공식 IDE로, Kotlin, Java, C++를 지원합니다. (또한 dart, Kotlin Multiplatform Mobile KMM 및 Jetpack Compose의 사용을 허용합니다) 모바일 애플리케이션을 개발할 수 있습니다.

# 소프트웨어 아키텍처 패턴

안드로이드 개발에서 사용되는 몇 가지 소프트웨어 아키텍처 패턴은 MVC(Model-View-Controller), MVP(Model-View-Presenter)입니다.

MVC(Model-View-Controller):

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

- 모델: 데이터를 관리하고 애플리케이션의 로직을 관리합니다.
- 뷰: 사용자 인터페이스 구성 요소를 나타내며 사용자에게 표시되는 데이터를 표시합니다.
- 컨트롤러: 모델과 뷰 사이의 연결 역할을 합니다.

MVP (Model-View-Presenter):

- 모델: 이 경우 모델은 데이터를 관리합니다.
- 뷰: 뷰는 데이터를 표시하고 사용자 입력을 처리하는 역할을 담당합니다.
- 프레젠터: 모델로부터 데이터를 검색하고 뷰를 업데이트합니다.

단방향 데이터 흐름 (UDF):

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

- 이건 주로 데이터가 상태에서 유저 인터페이스로의 단일 방향으로 흐르는 Jetpack Compose에서 주로 사용됩니다.

## Xcode

Xcode은 iOS 앱 개발을 위한 공식 통합 개발 환경으로, Objective-C와 Swift를 지원합니다. 코드 편집기, 그래픽 인터페이스 디자이너, 디버거 및 iOS 시뮬레이터가 포함되어 있습니다.

## 소프트웨어 아키텍처 패턴

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

안드로이드와 마찬가지로 iOS에도 애플리케이션 개발에 구현된 동일한 소프트웨어 아키텍처 패턴이 있습니다. 이러한 아키텍처 중 일부는 MVC (Model View Controller), MVP (Model View Presenter) 및 MVVM (Model View ViewModel)입니다.

MVVM (Model View ViewModel):

이 패턴은 안드로이드 개발 플랫폼에서 다루지 않은 것처럼 Model, View 및 ViewModel이 있으며, 각 역할은 아래와 같습니다.

- Model: 데이터 표현 및 비즈니스 로직을 제공합니다.
- View: 데이터를 보이게 하고 최종 사용자와 상호 작용합니다.
- ViewModel: 뷰에 제시될 데이터를 관리하고 UI 관련 데이터를 처리합니다.

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

# 크로스 플랫폼 개발 플랫폼

일부 주요 크로스 플랫폼 플랫폼은 Flutter, React Native, Xamarin 및 Ionic입니다. 이러한 플랫폼은 코드를 한 번 작성하고 다른 플랫폼에서 실행하는 개념을 허용합니다.

# Flutter

Flutter는 Google에서 만든 크로스 플랫폼 애플리케이션 개발 플랫폼으로, Dart 언어를 사용하는 단일 코드 베이스로 작성하여 Android, iOS, Web 및 데스크톱과 같은 플랫폼으로 컴파일할 수 있습니다.

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

# 소프트웨어 아키텍처 패턴

## BLoC (Business Logic Component) Pattern

BLoC(비즈니스 로직 컴포넌트)는 플러터에서 널리 사용되는 아키텍처 패턴으로, 사용자 인터페이스와 비즈니스 로직을 분리하는 데 중점을 둡니다. 데이터 흐름을 처리하기 위해 스트림과 시크를 사용합니다. 플러터에서 '위젯'으로 알려진 사용자 인터페이스 구성 요소는 BLoC 레이어와 상호 작용하여 데이터를 받아오고 이벤트를 보냅니다. BLoC 레이어는 로직을 보유하고 이벤트를 처리하며 앱 상태를 업데이트합니다. 이 패턴은 재사용성과 테스트 가능성을 향상시키며 응용 프로그램을 보다 반응적으로 만듭니다.

## Provider Pattern

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

플러터에서 널리 사용되는 또 하나의 패턴은 Provider 패턴입니다. 이 패턴은 의존성 주입 개념을 기반으로 하며 상태를 쉽게 관리할 수 있는 방법을 제공합니다. Provider 클래스는 데이터 원본 역할을 하며 다른 위젯들은 이 데이터에 대한 액세스 및 업데이트를 할 수 있습니다. Provider 패턴을 사용하면 개발자들은 여러 화면 간에 상태를 쉽게 공유하고 관리할 수 있어서 코드의 모듈성을 향상시키고 번거로운 코드를 줄일 수 있습니다.

# 일부 아키텍처의 장단점

# MVC 아키텍처의 장점

- 애플리케이션 개발이 빨라집니다.
- 여러 개발자가 협업하고 함께 작업하기 쉽습니다.
- 애플리케이션 업데이트가 쉽습니다.
- 애플리케이션에 올바르게 작성된 다수의 수준이 있기 때문에 디버깅이 쉽습니다.

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

# MVC 아키텍처의 단점

- 어느 순간 확장하기 어려워질 수 있습니다.
- MVC 아키텍처를 이해하기 어려울 수 있습니다.
- 메소드에 엄격한 규칙을 따라야 합니다.

# MVVM 아키텍처의 장점

- 개발하기 쉽습니다.
- 테스트가 쉽습니다.
- 유지보수가 쉽습니다.

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

# MVVM 아키텍처의 단점

- 디버그하기 어려움
- 복잡함

# Business Logic Component (BLoC)의 장점

- UI와 로직을 분리하기 쉬움
- 코드를 테스트하기 쉬움
- 코드를 재사용하기 쉬움
- 성능이 좋음

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

# 비즈니스 로직 컴포넌트 (BLoC)의 단점

- 더 많은 보일러플레이트

# 개인 경험 및 HNG 인턴십 여정

기술을 사랑하고 창작하는 것을 좋아하는 사람으로서, Android 네이티브 개발부터 시작해 Kotlin과 XML을 사용하여 플러터를 사용한 이동 앱 개발을 배우는 도전에 나섰습니다. 그러나 자학한 사람이기 때문에 이동 개발에 대한 이해를 더 많이 필요로 했던 중요한 영역 중 몇 가지를 놓칠 수도 있다고 생각했습니다. 그래서 글로벌 시장에 준비되었는지 알고 싶어졌고, 그 이유로 HNG11 인턴십에 합류하기로 결정했습니다. HNG11 인턴십은 이동 개발 능력을 확인하고 글로벌 시장에 준비되었는지 판단할 수 있도록 도와주는 급속한 성장 인턴십입니다.

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

인턴십은 시기 적절한 것입니다. 멘토의 안내나 이겨야 할 마감 기한이 없을 때 관심이 흩어질 수 있습니다.

## 결론

모바일 개발은 올바른 도구와 지식, 각 모바일 개발 플랫폼의 올바른 아키텍처에 대한 이해와 함께 진행되면 간단하고 쉬워집니다. HNG 인턴십을 통해 모바일 개발 여정에서 선두에 서 있기 위한 이러한 요구 사항을 충족시키고 전략에 앞서 나아갈 수 있도록 준비할 것입니다.