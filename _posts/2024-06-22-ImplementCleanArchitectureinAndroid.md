---
title: "안드로이드에서 클린 아키텍처 구현하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-ImplementCleanArchitectureinAndroid_0.png"
date: 2024-06-22 22:42
ogImage:
  url: /assets/img/2024-06-22-ImplementCleanArchitectureinAndroid_0.png
tag: Tech
originalTitle: "Implement Clean Architecture in Android"
link: "https://medium.com/simform-engineering/clean-architecture-in-android-12d61c4f5318"
---

![Software Architecture](/assets/img/2024-06-22-ImplementCleanArchitectureinAndroid_0.png)

소프트웨어 아키텍처는 소프트웨어 작성을 위한 규칙 세트를 정의하며, 소프트웨어를 신뢰할 수 있고 확장 가능하게 구조화할 수 있도록 개발자를 지원합니다.

이는 확장 가능성, 성능, 보안, 코드 축소 등과 같은 다양한 품질을 결정하고, 설계 위험을 분석하고 완화하는 데 도움이 됩니다. 소프트웨어 아키텍처는 설계 및 구현 팀을 위한 청사진 역할을 합니다.

가장 널리 알려진 소프트웨어 아키텍처로는 다음이 있습니다:

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

- MVC (Model-View-Controller)
- MVP (Model-View-Presenter)
- MVVM (Model-View-ViewModel)

참고: MVVM 패턴은 시작하기에 좋지만 코드 복잡성이 증가할수록, 코드베이스 요구에 맞게 복잡성을 다룰 수있는 더 강력한 아키텍처가 필요합니다.

이때 Clean Architecture가 등장합니다!

# Clean Architecture

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

Clean Architecture은 주로 관심사 분리에 초점을 맞춥니다. Clean Architecture를 따를 때 레이어의 수가 명시되어 있지는 않습니다. 필요에 따라 레이어를 추가할 수 있습니다. 원칙은 내부 레이어가 외부 레이어 중 어느 것도 참조해서는 안 되며, 중앙쪽으로 갈수록 세부 사항은 추상화되어야 합니다.

상기 이미지에서 외부 레이어는 세부 구현을 포함하고 있으며, 내부 레이어는 추상화에 초점을 둡니다. 이 접근 방식은 비즈니스 레이어를 개발 중에 사용하는 실제 도구의 저수준 종속성으로부터 분리합니다.

소프트웨어는 개발 도구(사용하는 하드웨어 및 데이터베이스 유형)를 변경해도 비즈니스 로직을 정의하는 코드에 영향을 미치지 않아야 합니다. Clean Architecture는 이러한 일반적인 아이디어에 기반합니다.

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

클린 아키텍처 레이어를 구면으로 상상해볼 수 있습니다. 중심으로 갈수록 희미해지죠. 중심에는 특정 작업이 어떻게 처리되는지에 대한 구체적인 세부 정보가 없습니다. 소프트웨어가 따라야 하는 규칙/정책을 정의하기만 합니다 (이것이 소프트웨어의 비즈니스 로직입니다).

클린 아키텍처 책의 아래 댓글이 이 전체 포인트를 잘 요약하고 있습니다.

## 의존성 규칙

## 엔티티

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

각 요소들은 클래스, 객체, 데이터 구조 또는 결합을 통해 설명할 수 있는 업무 규칙입니다. 외부 레벨 세부사항(예: 작업 흐름)에 변화가 발생할 때, 이러한 규칙들이 가장 변경될 가능성이 적습니다.

## 사용 사례

사용 사례에는 응용 프로그램별 업무 규칙이 포함됩니다. 이러한 규칙들은 엔티티 레이어의 비즈니스 규칙을 응용프로그램별 운영 규칙으로 전환하는 데 도움을 줍니다.

## 인터페이스 어댑터

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

이 레이어는 사용 사례의 데이터를 GUI 프레임워크, 웹 사용자 인터페이스 등과 같은 프레임워크 레이어에 사용할 수 있도록 변환합니다.

## 프레임워크 및 드라이버

일반적으로 이 레이어에서는 주로 사용된 프레임워크 및 데이터베이스의 가장 외부의 상세 구현으로 코드를 작성할 필요가 없습니다.
이 레이어에는 대부분 내부 레이어에서의 코드를 연결하는 코드가 포함되어 있으며, 데이터를 가져오고 표현을 위해 변환하는 방법을 정의합니다.

## 클린 아키텍처의 장점

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

- 시스템 변경을 쉽게합니다.
- 비즈니스 로직은 코드베이스의 다른 레이어와 완전히 분리되어 있으며, 다른 레이어는 비즈니스 규칙에 의존하므로 시스템에 기능을 추가/업데이트하기 쉬워집니다.
- 코드는 디커플링되어 각 레이어를 블랙 박스로 다룰 수 있어 코드 테스트에 도움이 됩니다.
- 각 모듈은 세부 사항이 아닌 목적을 정의합니다. 이는 기술적 세부 사항에 심취하지 않고 앱이 무엇을 하는지 이해하는 데 도움이 됩니다.

## Clean Architecture의 단점

- 단순 프로젝트에는 선호되지 않습니다. 프로젝트가 복잡하지 않다면, 단순한 아키텍처(MVC 또는 순수 MVVM)로 달성할 수 있는 파일을 불필요하게 만들고 코드를 작성하게 됩니다.
- 학습 곡선이 가파릅니다.
- 책임을 적절한 레이어로 나누는 방법을 이해하는 것은 이 아키텍처에서 각 레이어가 어떻게 작동하는지 명확한 개념이 없다면 지루할 수 있습니다.

# Clean Architecture으로 안드로이드 개발하기

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

안녕하세요! 안드로이드 플랫폼에서 간단한 TODO 앱을 클린 아키텍처로 만들었어요. 완성된 코드는 GitHub에서 확인할 수 있어요.

TODO 앱을 클린 아키텍처로 만드는 것은 아키텍처의 모든 측면을 다루는 것은 아니라는 점을 유의해 주세요.

기본 구현을 이해하고 더 복잡한 기능으로 나아가는 것이 중요한 포인트에요.

이 방법으로 사용된 레이어 구조는 다음과 같아요:

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

![이미지](/assets/img/2024-06-22-ImplementCleanArchitectureinAndroid_2.png)

동일한 계층 구조가 디렉토리 구조에 반영됩니다.

계층 구조는 구현에 따라 다를 수 있습니다. 그러나 코드 베이스를 구성할 때는 누가 누구에게 의존하는지에 중점을 두어야 합니다.

코드 구조에 복잡성과 차이점을 고려하여 레이어를 추가할 수 있습니다. Clean Architecture에는 몇 개의 레이어가 있어야 하는지에 대한 엄격한 규칙은 없습니다.

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

마크다운 포맷으로 표 태그를 변경하세요.

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

도메인 계층은 응용 프로그램/모듈 개념을 설명하는 핵심 엔티티로 구성됩니다. 이러한 엔티티는 외부 계층과 독립적이며 외부 계층(데이터, 유스 케이스 등)에서 언급해서는 안됩니다.

여기에는 작업(Task)이라는 기본 작업과 해당 속성을 정의하는 엔티티가 있습니다.

## 데이터

이 계층은 데이터와의 상호 작용을 정의합니다. 데이터 작업을 정의하기 위해 TaskDataSource를 사용합니다. 그런 다음 여러 데이터 소스를 하나의 리포로 결합하고 리포지토리를 통해 데이터에 액세스하도록 TaskRepository를 정의했습니다.

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

여기서는 단일 데이터 원본만 사용합니다. 기본적으로 TaskDataSource와 TaskRepository 메소드와 일대일 매핑되어 있어 처음봤을 때는 중복된 것처럼 보일 수 있습니다. 그러나 애플리케이션이 확장되고 인터넷에서 데이터를 다운로드하거나 로컬 데이터베이스 또는 사용자 파일에 저장된 데이터와 같이 여러 위치에서 데이터 소스를 가질 때 리포지토리 패턴이 유용할 수 있습니다.

TaskRepository는 아래에서 논의된 유스케이스에서 사용되고 있습니다.

## 유스케이스

이 계층은 데이터를 처리할 수 있는 작업을 정의하고 사용 사례를 형성합니다. 프레젠테이션 계층은 직접 이러한 유스케이스를 사용하여 데이터를 표시하거나 조작합니다.

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

해당 데이터에 대해 다양한 작업을 수행하기 위해 정의된 사용 사례들이 있습니다:

![Use Cases](/assets/img/2024-06-22-ImplementCleanArchitectureinAndroid_5.png)

## 프레임워크

이 레이어는 데이터 레이어에서 정의된 데이터 소스를 구현하고 플랫폼별 구현을 제공합니다.

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

저희 구현에서는 TaskDataSourceImpl을 통해 Room 데이터베이스를 사용하여 TaskDataSource를 구현했습니다.

## 프레젠테이션

이 계층은 코드에서 표시 로직을 정의하고 플랫폼별 뷰가 데이터를 사용자에게 제시하는 방식을 정의합니다. 여기에는 사용 사례 계층에 정의된 사용 사례를 호출하고 안드로이드 뷰를 사용하여 그것들을 사용자에게 제시하는 뷰 모델이 있습니다.

그게 다입니다!

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

# 마무리

안드로이드 개발에서 Clean Architecture의 개념과 구현 방법에 대해 명확해졌기를 바라요.

GitHub에서 샘플 TODO 앱의 전체 코드를 찾을 수 있어요. 코드 구조에 대한 제안이 있으면 PR을 제출해주세요. 기여해 주실 것을 환영합니다.

즐거운 코딩 되세요!

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

참고문헌
