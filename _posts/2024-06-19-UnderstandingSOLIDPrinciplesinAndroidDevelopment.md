---
title: "안녕하세요 안드로이드 개발 중 SOLID 원칙을 이해하는 방법에 대해 소개해드리겠습니다 함께 살펴보시죠"
description: ""
coverImage: "/assets/img/2024-06-19-UnderstandingSOLIDPrinciplesinAndroidDevelopment_0.png"
date: 2024-06-19 22:27
ogImage: 
  url: /assets/img/2024-06-19-UnderstandingSOLIDPrinciplesinAndroidDevelopment_0.png
tag: Tech
originalTitle: "Understanding SOLID Principles in Android Development"
link: "https://medium.com/@aadi.balaji/understanding-solid-principles-in-android-development-04fd3eb18b64"
---


소프트웨어 개발에서 특히 안드로이드 개발에서는 설계 원칙을 준수하여 코드를 유지보수 가능하고 확장 가능하며 견고하게 유지하는 것이 중요합니다. 가장 중요한 설계 원칙 중 하나는 SOLID 원칙입니다.

각 원칙을 실시간 안드로이드 예시와 함께 살펴보고 프로젝트에 적용하는 방법을 이해하고 구현하는 데 도움이 될 것입니다.

## 1. 단일 책임 원칙 (SRP)

정의:

<div class="content-ad"></div>

클래스는 변경되는 이유가 하나여야 합니다. 즉, 하나의 작업 또는 책임만 가져야 합니다.

예시:

Android 앱에서 `UserManager` 클래스가 있어서 로그인, 로그아웃 및 사용자 데이터 가져오기와 같은 사용자 관련 작업을 담당한다고 상상해보세요. 만약 이 클래스에 UI 관련 코드도 포함한다면, SRP(Single Responsibility Principle)를 위반합니다. 대신, 역할을 분리해야 합니다: `UserManager`가 사용자 관련 로직을 처리하고 `UserUIHandler`와 같은 별도의 클래스가 UI 관련 작업을 관리해야 합니다.

## 2. 개방/폐쇄 원칙 (OCP)

<div class="content-ad"></div>

정의:

소프트웨어 엔티티는 확장에 열려 있지만 수정에는 닫혀 있어야 합니다.

예시:

예를 들어, 알림을 보내는 `NotificationSender` 클래스가 있다고 가정해보겠습니다. 이 클래스에 이메일 이외에 SMS와 같은 새로운 유형의 알림을 추가하려면 `NotificationSender`를 직접 수정해서는 안 됩니다. 대신, 각 유형의 알림에 대한 새로운 클래스를 생성하고 `NotificationSender` 클래스가 이러한 확장을 처리할 수 있도록 확실하게 해야 합니다.

<div class="content-ad"></div>

## 3. Liskov Substitution Principle (LSP)

정의:

하위 유형은 기본 유형을 대체하여 프로그램의 정확성에 영향을주지 않고 대체 가능해야합니다.

예시:

<div class="content-ad"></div>

베이스 클래스 `Bird`와 하위 클래스 `Penguin`을 고려해봅시다. 코드가 모든 `Bird`가 날 수 있을 것으로 예상하고 있다면, `Penguin`을 대체한다면 날지 못한다는 점 때문에 이 기대가 깨집니다. 이는 LSP를 위반하는 것입니다. LSP를 준수하기 위해 하위 클래스가 베이스 클래스가 약속한 것과 일관되게 동작하도록 하시기 바랍니다. 예를 들어, `Bird`에 `Penguin`이 날지 못하게 하고 수영하도록 오버라이드할 수 있는 `move`와 같은 메소드가 있는 클래스 계층 구조를 재설계해보세요.

## 4. 인터페이스 분리 원칙 (ISP)

정의:

클라이언트는 사용하지 않는 인터페이스에 의존하도록 강요되어서는 안 됩니다.

<div class="content-ad"></div>

예시:

만약 `Vehicle` 인터페이스에 `drive`, `fly`, `sail`과 같은 메서드가 있다면, 이 인터페이스를 구현하는 `Car` 클래스는 필요하지 않은 메서드에 대한 구현을 제공해야 합니다 (예: `fly` 및 `sail`). 대신에 `Vehicle`를 더 작고 구체적인 인터페이스로 나누어 `Drivable`, `Flyable`, `Sailable`과 같은 인터페이스를 만듭니다. 이렇게 하면 `Car`는 `Drivable` 인터페이스만 구현하며 ISP를 준수할 수 있습니다.

<div class="content-ad"></div>

고수준 모듈은 저수준 모듈에 의존해서는 안 됩니다. 둘 다 추상화에 의존해야 합니다.

예시:

`OrderProcessor` 클래스가 주문 확인을 보내기 위해 `EmailService`의 인스턴스를 직접 생성한다면 DIP를 위반합니다. 대신에 `NotificationService`와 같은 인터페이스를 사용하고, `OrderProcessor`가 이 인터페이스에 의존하도록 합니다. 그럼 `EmailService`는 `NotificationService`를 구현할 수 있습니다. 이렇게 하면 다른 알림 서비스로 쉽게 교체할 수 있어 DIP를 준수할 수 있습니다.

## 결론

<div class="content-ad"></div>

SOLID 원칙을 준수하면 Android 앱의 코드베이스를 깨끗하고 유지보수 가능하며 확장 가능하게 유지할 수 있습니다. 각 원칙은 소프트웨어 설계의 특정 측면을 다루며 코드를 이해하기 쉽고 확장 및 유지보수하기 쉽게 만듭니다. 프로젝트에 이러한 원칙을 적용하고 코드 품질에 미치는 차이를 경험해보세요!

Android 개발 여정에서 SOLID 원칙을 구현하는 데 도움이 필요하거나 더 궁금한 점이 있으시면 언제든지 문의해 주세요! 더 많은 통찰과 업데이트를 위해 LinkedIn에서 저를 팔로우해주세요.

감사합니다. 즐거운 코딩하세요!