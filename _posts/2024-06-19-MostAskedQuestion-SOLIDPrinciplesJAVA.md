---
title: "가장 많이 묻는 질문 - SOLID 원칙 JAVA"
description: ""
coverImage: "/assets/img/2024-06-19-MostAskedQuestion-SOLIDPrinciplesJAVA_0.png"
date: 2024-06-19 21:55
ogImage:
  url: /assets/img/2024-06-19-MostAskedQuestion-SOLIDPrinciplesJAVA_0.png
tag: Tech
originalTitle: "Most Asked Question -SOLID Principles JAVA"
link: "https://medium.com/@diptendud/most-asked-question-solid-principles-java-18fdbb62e3e9"
---

![SOLID Principles in JAVA](/assets/img/2024-06-19-MostAskedQuestion-SOLIDPrinciplesJAVA_0.png)

- **S — Single Responsibility Principle (SRP)**
  - Each unit of code should have only one responsibility.
  - A unit can be a class, module, function, or component.
  - Keeps code modular and reduces tight coupling.
  - Example: A class that handles user authentication should not also manage database connections.

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

# O — Open/Closed Principle (OCP)

- 코드 단위는 확장을 허용하고 수정을 제한해야 합니다.

- 기존 코드를 수정하는 대신 새 코드를 추가하여 기능 확장.

- React 프론트엔드와 같이 구성 요소 기반 시스템에서 유용합니다.
  예: 로깅 시스템에 새 로그 핸들러를 생성하여 기능을 추가하는 것이 기존 핸들러를 변경하는 것보다 좋습니다.

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

# L — 리스코프 치환 원칙 (LSP)

- 서브클래스는 기본 클래스로 대체 가능해야 합니다.
- 기본 클래스의 기능은 모든 서브클래스에서 사용할 수 있어야 합니다.

- 만약 서브클래스가 기본 클래스의 기능을 사용할 수 없다면, 그것은 기본 클래스에 있어서는 안 됩니다.
  예: `새` 클래스에 `참새`와 `펭귄` 서브클래스가 있는 경우, 만약 `새`에 `날다` 메소드가 있다면, `펭귄`은 날지 못하기 때문에 상속받으면 안 됩니다.

# I — 인터페이스 분리 원칙 (ISP)

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

- 몇 개의 일반 목적이 아닌 구체적인 인터페이스를 제공해주세요.

- 클라이언트는 사용하지 않는 메소드에 의존하면 안 됩니다.
  예시: `Vehicle` 인터페이스를 `Drivable`과 `Flyable`로 분리하여 자동차 클래스가 `Flyable`을 구현할 필요가 없도록합니다.

# D — 의존성 역전 원칙 (DIP)

- 구체적인 클래스가 아닌 추상화에 의존해주세요.

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

- 시스템의 부분 간 의존성을 분리시키기 위해 추상화를 사용하세요.

- 인터페이스나 추상화를 사용하여 코드 유닠 사이에 직접 호출을 피하세요.
  예: 특정 'FileLogger' 클래스 대신 로깅을 위해 'ILogger' 인터페이스를 사용하세요.
