---
title: "루비 온 레일즈에서 GRASP 원칙 이해하기"
description: ""
coverImage: "/assets/img/2024-05-23-UnderstandingGRASPPrinciplesinRubyonRails_0.png"
date: 2024-05-23 12:45
ogImage:
  url: /assets/img/2024-05-23-UnderstandingGRASPPrinciplesinRubyonRails_0.png
tag: Tech
originalTitle: "Understanding GRASP Principles in Ruby on Rails"
link: "https://medium.com/@patrickkarsh/understanding-grasp-principles-in-ruby-on-rails-c7a02a0e05ff"
---


![Understanding GRASP Principles in Ruby on Rails](/assets/img/2024-05-23-UnderstandingGRASPPrinciplesinRubyonRails_0.png)

소프트웨어 엔지니어링에서, GRASP (General Responsibility Assignment Software Patterns) 원칙은 시스템 내의 여러 구성 요소에 책임을 할당하는 데 도움이 되는 지침 세트입니다. 이러한 원칙은 유지 관리 가능하고 확장 가능하며 견고한 애플리케이션을 만드는 데 중요합니다. 인기 있는 웹 애플리케이션 프레임워크인 Ruby on Rails의 맥락에서 GRASP 원칙을 적용하면 코드의 품질과 구조를 크게 향상시킬 수 있습니다. 본문에서는 GRASP 원칙과 Ruby on Rails 개발에서의 적용에 대해 탐구합니다.

## Information Expert

원칙: 책임을 완수하기 위해 필요한 정보를 갖고있는 클래스에 책임을 할당합니다.


<div class="content-ad"></div>

레일즈에서: 레일즈 애플리케이션에서 모델은 일반적으로 정보 전문가입니다. 애플리케이션의 엔티티와 관련된 비즈니스 로직 및 데이터를 포함합니다. 예를 들어, 주문의 총 가격을 계산해야 한다면, 이 로직은 주문 모델에 있어야 합니다. 왜냐하면 주문 모델은 라인 아이템과 가격에 액세스할 수 있기 때문입니다.

![이미지](/assets/img/2024-05-23-UnderstandingGRASPPrinciplesinRubyonRails_1.png)

## 생성자

원칙: 클래스 인스턴스를 만드는 책임을 집계, 포함 또는 밀접하게 사용하는 클래스에 할당합니다.

<div class="content-ad"></div>

레일즈에서: 이 원칙은 종종 레일즈 컨트롤러와 모델이 상호 작용하는 방식에 반영됩니다. 예를 들어, 새로운 사용자를 만들 때 UsersController는 User 모델을 인스턴스화하는 역할을 담당할 것입니다.

![이미지](/assets/img/2024-05-23-UnderstandingGRASPPrinciplesinRubyonRails_2.png)

## Controller

원칙: 시스템 이벤트 처리 책임을 전반적인 시스템 또는 특정 시나리오를 반영하는 컨트롤러 클래스에 할당합니다.

<div class="content-ad"></div>

루비온 레일스: 레일스의 MVC(Model-View-Controller) 아키텍처는 이 원칙을 내재적으로 지원합니다. 컨트롤러는 들어오는 HTTP 요청을 처리하고 모델과 상호 작용하여 뷰를 렌더링합니다. UsersController는 사용자 계정과 관련된 모든 작업을 처리할 것입니다.

![이미지](/assets/img/2024-05-23-UnderstandingGRASPPrinciplesinRubyonRails_3.png)

## 낮은 결합도

원칙: 클래스 간의 종속성을 줄여 시스템을 모듈식으로 만들고 유지보수하기 쉽게 만듭니다.

<div class="content-ad"></div>

레일즈에서: 이 원칙은 서비스 객체, 관심사 또는 데코레이터를 사용하여 비즈니스 로직을 모델과 컨트롤러 외부에 캡슐화하여 복잡성을 줄일 수 있습니다.

![이미지](/assets/img/2024-05-23-UnderstandingGRASPPrinciplesinRubyonRails_4.png)

## 높은 응집력

원칙: 클래스가 하나의 명확히 정의된 목적을 가지고 있으며 그 책임이 밀접하게 관련되어 있음을 보장합니다.

<div class="content-ad"></div>

레일즈에서는 관례를 통해 높은 응집력을 장려합니다. 모델은 데이터 관련 로직에 집중해야 하고, 컨트롤러는 요청 처리 로직에 집중하며, 뷰는 표현 로직에 집중해야 합니다. 이 분리를 명확히 유지하면 유지보수성이 향상됩니다.

![UnderstandingGRASPPrinciplesinRubyonRails_5](/assets/img/2024-05-23-UnderstandingGRASPPrinciplesinRubyonRails_5.png)

## 간접성

원칙: 중간 객체에 책임을 할당하여 다른 구성 요소 또는 서비스 간의 중개를 수행합니다.

<div class="content-ad"></div>

레일즈에서 미들웨어, 옵저버 또는 서비스 객체를 사용하면 간접적인 레이어를 도입할 수 있습니다. 예를 들어 레일즈의 Active Job은 백그라운드 작업 처리를 위한 추상화를 제공합니다.

![이미지](/assets/img/2024-05-23-UnderstandingGRASPPrinciplesinRubyonRails_6.png)

## 다형성

원칙: 동작의 변화를 처리하기 위해 다형적 동작을 사용하여 시스템을 더 유연하고 확장 가능하게 만드세요.

<div class="content-ad"></div>

레일즈에서: 다형성은 인터페이스나 추상 클래스를 사용하여 구현할 수 있습니다. 또한 레일즈는 다형성 연관을 지원하여 모델이 단일 연관을 사용하여 다른 모델 중 하나 이상에 속할 수 있게 합니다.

![이미지](/assets/img/2024-05-23-UnderstandingGRASPPrinciplesinRubyonRails_7.png)

## 순수 패브릭

원칙: 문제 도메인의 개념을 나타내지 않고 단지 높은 응집도와 낮은 결합도를 달성하기 위해 설계된 클래스를 생성하세요.

<div class="content-ad"></div>

루비온 레일즈에서는 서비스 객체가 순수한 패브릭 예제입니다. 이들은 모델이나 컨트롤러 내에 자연스럽게 들어 맞지 않는 복잡한 로직을 캡슐화합니다.

![이미지](/assets/img/2024-05-23-UnderstandingGRASPPrinciplesinRubyonRails_8.png)

## 보호된 변형

원칙: 원하는 요소를 캡슐화하여 변동 요소에 의한 다른 요소의 변화로부터 보호하세요.

<div class="content-ad"></div>

루비온 레일즈에서는 스트래티지 패턴과 SOLID 원칙을 준수하여 GRASP 원칙과 중첩되는 디자인 패턴으로 이를 달성할 수 있습니다.

![이미지](/assets/img/2024-05-23-UnderstandingGRASPPrinciplesinRubyonRails_9.png)

# 결론

GRASP 원칙은 소프트웨어 개발에 잘 근거한 디자인 결정을 내리기 위한 견고한 기반을 제공합니다. 루비온 레일즈 애플리케이션에 적용되면 이러한 원칙은 깔끔하고 조직적이며 유지보수 가능한 코드 기반을 유지하는 데 도움이 됩니다. GRASP 원칙을 준수함으로써, 레일즈 개발자는 기능적이고 확장 가능하며 이해하기 쉬운 애플리케이션을 구축할 수 있으며, 이는 궁극적으로 더 나은 소프트웨어 품질과 개발자 생산성으로 이어집니다.
