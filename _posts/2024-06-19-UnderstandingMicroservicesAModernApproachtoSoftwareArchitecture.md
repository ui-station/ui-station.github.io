---
title: "마이크로서비스 이해 소프트웨어 아키텍처에 대한 현대적인 접근법"
description: ""
coverImage: "/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_0.png"
date: 2024-06-19 12:34
ogImage:
  url: /assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_0.png
tag: Tech
originalTitle: "Understanding Microservices: A Modern Approach to Software Architecture"
link: "https://medium.com/@blogs4devs/hello-2230d29c939c"
---

마이크로서비스

마이크로서비스와 보안

사가 패턴: 코레오그래피와 오케스트레이션

AXON을 활용한 사가

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

Sage와 Eventuate를 이용한 트랜잭션 외부함으로 패턴 구현하기

마이크로서비스에서의 전파

# 소개

이 블로그에서는 소프트웨어 개발의 풍경을 변화시킨 혁신적인 접근 방식인 마이크로서비스 아키텍처의 매력적인 세계에 대해 탐구해 보겠습니다. Netflix와 같은 선두 기업들이 전통적인 방법론과 관련된 공통적인 도전 과제를 극복하기 위해 이 아키텍처를 채택했습니다. 마이크로서비스의 흥미진진한 영역으로 뛰어들기 전에, 먼저 거대한(monolithic) 아키텍처 및 그 한계를 이해하는 것이 중요합니다.

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

다음은 코드 링크입니다: [https://github.com/Blogs4Devs/Microservices](https://github.com/Blogs4Devs/Microservices).

# Monolithic architecture

전통적인 모놀리식 아키텍처에서는 전체 애플리케이션이 하나의 프로세스로 실행되며, 모든 애플리케이션 구성 요소가 서로 연결되어 의존하며 하나의 단일 단위로 묶여 있습니다. 이는 사용자 인터페이스, 비즈니스 로직 및 데이터 액세스 레이어가 모두 단일 프로그램의 일부라는 것을 의미합니다.

![img](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_0.png)

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

# 모놀리식 애플리케이션의 단점:

- 확장성 문제:

모놀리식 애플리케이션 내의 특정 서비스가 많은 호출을 받아 확장해야 하는 경우 전체 애플리케이션을 확장해야 합니다. 이는 전체 애플리케이션의 추가 인스턴스를 실행해야 하는 것을 의미하며, 이는 리소스를 많이 소비하고 비효율적입니다. 과부하된 구성 요소만 확장하는 대신 전체 시스템을 확장해야 하므로 불필요하게 리소스를 사용하게 됩니다.

- 배포 속도가 느림:

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

대규모 응용 프로그램에서는 작은 변경사항도 전체 응용 프로그램을 다시 컴파일하고 배포해야 합니다. 이로 인해 배포 주기가 크게 느려지며 전체 시스템을 테스트하고 통째로 배포해야 하므로 지속적인 배포가 어려워질 수 있습니다.

- 기술 채택에 대한 장벽:

대규모 응용 프로그램은 일반적으로 한 가지 언어 또는 프레임워크로 작성되어 있어 전체 개발 팀이 해당 특정 기술을 알고 있어야 합니다. 이는 특정 작업에 더 적합한 새로운 기술이나 언어를 채택하는 능력을 제한할 수 있습니다.

- 코드 변경 및 테스트가 어려운 문제:

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

모놀리식 애플리케이션에서는 전체 시스템의 모든 로직이 얽혀있어 코드베이스가 복잡하고 관리하기 어렵습니다. 애플리케이션의 한 부분에 변경이 있을 때 다른 부분에 예상치 못한 영향을 미치는 경우가 많아 테스트와 유지보수가 어려워집니다. 이 복잡성은 버그를 분리하고 수정하기도 어렵게 만들어서 개발 주기를 늘릴 수 있습니다.

이러한 단점을 이해하면 많은 기관이 더 큰 유연성, 확장성 및 유지보수 편의성을 제공하는 마이크로서비스 아키텍처로 전환하려는 이유가 분명해집니다.

# 마이크로서비스 아키텍처

마이크로서비스 아키텍처는 애플리케이션을 작은, 느슨하게 결합된 서비스로 분해하여 각각이 특정 비즈니스 기능을 담당하게 합니다. 이러한 서비스들은 독립적으로 개발, 배포 및 확장할 수 있어 모놀리식 아키텍처보다 여러 이점을 제공합니다.

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

<img src="/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_1.png" />

다음은 마이크로서비스 아키텍처의 주요 이점 요약입니다:

- 느슨한 결합:

마이크로서비스는 각각 물리적으로 분리되어 있기 때문에 느슨하게 결합되어 있습니다. 이 분리로 인해 각 서비스는 개별적으로 개발, 배포 및 확장될 수 있어 다른 서비스에 영향을 미치지 않고 독립적으로 변경이 가능합니다.

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

- 독립성 팀:

다른 팀은 서로 독립적으로 다른 마이크로서비스에 작업할 수 있습니다. 이 상대적 독립성은 병렬 개발을 가능케하며 전체 개발 프로세스를 가속화하고 효율성을 향상시킬 수 있습니다.

- 테스트 및 배포의 용이성:

마이크로서비스 아키텍처는 테스트와 배포를 쉽게 할 수 있게 해줍니다. 각 서비스가 별도의 단위이기 때문에 독립적으로 테스트하고 배포할 수 있어 테스트의 복잡성을 줄이고 배포 주기를 가속화할 수 있습니다.

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

- 지속적 배포:

마이크로서비스는 지속적인 배포 방법에 잘 맞습니다. 각 서비스는 전체 시스템에 영향을 미치지 않고 지속적으로 업데이트되고 배포될 수 있어 더 자주 릴리스하고 빠른 업데이트가 가능해집니다.

이제, 마이크로서비스 아키텍처를 구성하는 주요 구성 요소를 살펴보겠습니다.

![이미지](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_2.png)

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

# 게이트웨이

프로덕션 환경에서는 마이크로서비스와의 직접 통신이 허용되지 않습니다. 대신, 모든 마이크로서비스는 중개자(일반적으로 미들웨어 또는 API 게이트웨이)와 상호 작용하는 단일 응용 프로그램으로 그룹화되어 처리되어야 합니다. 이 중개자는 로드 밸런서 역할을 하여 들어오는 요청을 마이크로서비스에 골고루 분배하여 부하를 균형 있게 유지하고 최상의 성능을 보장합니다. 미들웨어가 단일 장애 지점(SPOF)이 될 수 있지만, 이러한 위험은 중복 및 고가용성 설정을 통해 강력하고 신뢰할 수 있는 작동을 보장함으로써 완화됩니다.

게이트웨이를 구성하는 두 가지 방법이 있습니다 :

- 정적 구성: 소수의 마이크로서비스를 다룰 때, 미들웨어를 정적으로 구성하여 특정 경로를 지정된 IP 주소로 라우팅할 수 있습니다. 예를 들어, /path1는 주소 1로, /path2는 주소 2로 이동할 수 있습니다. 이 구성에서는 마이크로서비스의 IP 주소를 알아야 하며, 마이크로서비스가 중지되거나 IP 주소가 변경되면 설정을 업데이트해야 합니다.
- 동적 구성: 여러 마이크로서비스가 동적으로 시작 및 중지되는 환경에서는 정적 구성만으로는 충분하지 않습니다. 여기서는 발견 서비스를 통한 동적 구성이 필수적입니다.

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

# 발견 서비스

발견 서비스는 디렉터리처럼 작동합니다 (DNS 또는 SOAP의 UDDI와 유사함). 마이크로서비스는 시작할 때 발견 서비스에 등록됩니다. 그런 다음 게이트웨이는 마이크로서비스의 현재 IP 주소를 확인하기 위해 서비스 레지스트리에 쿼리하도록 구성되어 라우팅 구성을 자동으로 업데이트할 수 있습니다.

# 구성 서비스

- 콜드 구성: 각 마이크로서비스는 자체 구성 파일(예: application.properties)을 갖습니다. 이러한 파일에 대한 수정은 해당 마이크로서비스를 다시 시작해야 합니다. 또한, 구성 설정이 여러 마이크로서비스 간에 공유되는 경우 각 개별 구성 파일을 따로 업데이트해야 합니다. 동일한 마이크로서비스의 여러 인스턴스가 실행 중인 경우 변경 사항을 각 인스턴스에 개별적으로 적용해야 하므로 불편하고 실수를 유발할 수 있는 프로세스가 됩니다.
- 핫 구성: 중앙 집중식 구성 서비스는 단일 전역 구성 파일을 유지합니다. 이 파일의 변경 사항은 마이크로서비스에 자동으로 전파되어 다시 시작할 필요가 없습니다. 일반적으로 이 구성은 Git과 같은 버전 관리 리포지토리를 통해 관리됩니다. 구성 매개변수가 변경되면 커밋이 수행되고 특정 변경 사항만 마이크로서비스로 전송됩니다. 전체 구성 파일이 아니라 특정 변경 사항만 전송되므로 모든 마이크로서비스에서 효율적이고 일관된 구성 관리가 보장됩니다.

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

# 커뮤니케이션 모델

- 동기식 통신: 누군가에게 길을 물어본다고 상상해봅니다. 동기식 통신에서는 "공원에 어떻게 가요?" 라고 물어보고 그 후 그들이 답변할 때까지 그 자리에 기다립니다. 답변을 듣고 나서야만 앞으로 나아갑니다. 이는 마이크로서비스가 서로와 대화하는 방식과 유사합니다. 한 서비스가 무언가를 요청하고, 답변을 기다리며, 그것을 받은 후에만 다음 단계로 넘어갑니다. 이 방법은 빠르고 직관적이지만, 상대 서비스가 응답하는 데 오랜 시간이 걸리면 느릴 수 있습니다.

![이미지1](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_3.png)

![이미지2](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_4.png)

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

- 비동기 통신: 이제, 지시를 기다리는 대신 "공원에 가고 있어. 만나야 할 일이 있으면 알려줘"라는 메모를 남기는 상황을 상상해봐. 그럼 너는 그냥 걷어가서 다른 일을 계속 한다. 나중에 답변이 있는지 확인하기 위해 메모를 확인한다. 이것은 메시지 브로커와 함께 하는 비동기 통신과 비슷하다. 한 서비스가 응답을 기다리지 않고 다른 서비스로 메시지를 보낸다. 메시지는 중개인(브로커)에게 보내지고, 그 이후 브로커가 다른 서비스에 전달한다. 보내는 서비스는 기다리지 않고 작업을 계속할 수 있다. 이것은 즉각적인 답변이 필요하지 않고 메시지가 도착했을 때 처리할 수 있는 경우에 좋다.

![이미지](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_5.png)

![이미지](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_6.png)

우리는 Reactive 프로그래밍에 관한 다가오는 블로그에서 이 두 가지 모델을 자세히 살펴볼 것이다.

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

# 데모

이 데모에서는 Spring 프레임워크를 사용하여 간단한 마이크로서비스 시스템을 구축하는 방법을 살펴보겠습니다. 특히 Spring Boot와 Spring Cloud에 초점을 맞출 것입니다. 이러한 도구들은 마이크로서비스를 쉽게 구축하고 관리할 수 있도록 설계되었습니다.

![image](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_7.png)

이 데모에서는 두 개의 마이크로서비스인 고객 서비스와 계정 서비스가 있을 것입니다. 각 마이크로서비스는 간단한 Spring Boot 애플리케이션으로, 공통 종속성인 Lombok, Spring Data JPA, H2 Database, Spring Web을 사용해야 합니다.

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

- 고객 서비스: 고객을 관리합니다.

다음은 Customer 모델입니다.

![Customer Model](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_8.png)

- 계정 서비스: 고객에게 속한 각 계정을 관리합니다. 각 마이크로서비스는 자체 데이터베이스를 갖기 때문에, 계정 서비스 데이터베이스는 각 계정에 대해 고객 ID만 저장합니다. 이 고객 ID는 고객 서비스 데이터베이스에 존재해야 합니다.

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

# 참조 무결성 보장하기

참조 무결성은 테이블 간의 관계가 일관적으로 유지되도록 보장합니다. 이 시나리오에서의 적용 방법은 다음과 같습니다:

계정 추가:

- 새 계정을 추가하려면 계정 서비스 데이터베이스에 고객 ID만 저장합니다.
- 참조 무결성을 보장하려면 고객 ID가 고객 서비스 데이터베이스에 존재하는지 확인해야 합니다.
- 이를 위해 계정을 추가하기 전에 고객 ID가 유효한지 확인하기 위해 고객 서비스를 호출해야 합니다.

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

다음은 Account 모델입니다.

![Account Model](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_9.png)

@Transient는 데이터베이스에 저장되지 말아야 하는 필드를 나타내는 주석입니다. 이 경우 accounts 데이터베이스에 customer 테이블이 없고 외래 키도 없기 때문에 customer 필드는 고객 서비스를 쿼리하여 수동으로 생성됩니다.

# Discovery service

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

지금 새로운 스프링 부트 응용 프로그램을 시작하려고 하는데, 이번에는 이 종속성을 추가해야 합니다.

![dependency](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_10.png)

그런 다음 메인 클래스에 @EnableEurekaServer 주석을 추가해야 합니다.

![annotation](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_11.png)

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

그런 다음, 우리는 이 구성을 추가합니다.

<img src="/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_12.png" />

그리고 애플리케이션을 시작합니다.

마이크로서비스가 유레카 서버에 등록할 수 있도록 하려면, 각 마이크로서비스에 이 종속성을 추가해야 합니다.

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

![Understanding Microservices: A Modern Approach to Software Architecture](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_13.png)

기본적으로 서비스는 서비스가 실행 중인 서버의 서비스 이름과 서버 이름으로 등록됩니다. 그러나 현실적인 시나리오에서는 서버 이름 대신 IP 주소가 필요합니다. 따라서 각 마이크로서비스 수준에 이러한 속성을 추가해야 합니다.

![Understanding Microservices: A Modern Approach to Software Architecture](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_14.png)

defaultZone 구성은 Eureka 인스턴스의 기본 위치를 http://localhost:8761/eureka URL로 지정합니다. 그러나 실제로는 적절한 IP 주소를 사용해야 합니다.

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

이제, 마이크로서비스를 실행하고 Eureka 대시보드에 액세스하면 각 마이크로서비스의 주소와 포트를 확인할 수 있습니다.

![이미지](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_15.png)

# 게이트웨이

새로운 스프링 부트 애플리케이션을 만들고 이 종속성을 추가합니다.

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

![이미지](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_16.png)

- 정적 구성:

![이미지](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_17.png)

- 프리디케이트: 요청을 라우팅하는 데 사용되는 조건입니다. 예를 들어, 요청 경로에 /customer가 포함되어 있으면 게이트웨이는 해당 마이크로서비스로 라우팅합니다. 프리디케이트는 요청 경로, 헤더 또는 쿼리 매개변수와 같은 다양한 기준을 바탕으로 할 수 있습니다.
- URI: 이는 게이트웨이가 요청을 보내는 엔드포인트입니다. 예를 들어, URI가 http://localhost:8080/api로 설정된 경우 게이트웨이는 해당 주소로 요청을 전달합니다.

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

이제 localhost:8888/customers를 입력하면 게이트웨이가 localhost:8082/customers로 요청을 리디렉션합니다.

![이미지](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_18.png)

![이미지](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_19.png)

- 동적 구성:

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

그냥 이 bean 하나 추가해주세요.

![이미지](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_20.png)

그리고 이 구성요소도 추가해주세요.

![이미지](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_21.png)

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

그리고 CORS를 구성해야 합니다.

![이미지](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_22.png)

이제 URL을 게이트웨이포트/대문자의 서비스이름 형식으로 입력한 후 특정 경로를 이어서 입력합니다.

예를 들어: http://localhost:8888/ACCOUNT-SERVICE/accounts

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

게이트웨이는 해당 서비스의 경로를 찾기 위해 서비스 디스커버리에 해당 서비스의 이름만으로 요청을 전달합니다.

![이미지1](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_23.png)

![이미지2](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_24.png)

지금까지 살펴본 것처럼, 우리가 계정을 조회할 때 고객 정보도 함께 얻습니다. 그렇다면 계정 서비스는 어떻게 고객 서비스에서 해당 정보를 검색할까요?

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

![Understanding Microservices](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_25.png)

특정 계정에 대한 정보를 얻기 위해 계정 서비스에 요청을 보낼 때, 먼저 해당 계정을 데이터베이스에서 찾습니다. 그런 다음, 계정과 연결된 고객 ID를 검색하고 해당 고객에 대한 정보를 얻기 위해 고객 서비스에 요청을 보냅니다. 최종적으로 결과를 구성하여 클라이언트에게 보냅니다.

내부 요청을 보내기 위해 다양한 라이브러리를 사용할 수 있으며, 가장 흔한 것은 OpenFeign입니다.

다음 종속성을 추가합니다.

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

![UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_26](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_26.png)

OpenFeign은 HTTP 요청을 간편하게 만들어주는 선언적 프레임워크입니다. 복잡한 코드를 작성하는 대신 인터페이스를 정의하고 상호 작용하려는 서비스의 이름을 지정하고 사용하려는 엔드포인트를 나열하기만 하면 됩니다.

![UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_27](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_27.png)

Feign을 사용하여 요청을 보내려면 Account 서비스의 주 클래스에 @EnableFeignClients 주석을 추가해야 합니다.

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

이제 우리는 인터페이스를 주입하고 해당 함수를 사용하여 고객 서비스에 요청을 보내는 방법을 알아봅니다.

![image](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_28.png)

요청을 보내기 전에, 우리는 Discovery Service에 서비스의 주소를 요청합니다 (Gateway를 우회합니다). 예를 들어 findCustomerById를 호출하는 경우에 문제가 발생할 수 있고 서비스가 차단될 수 있습니다. 이를 해결하기 위해 "서킷 브레이커"를 사용합니다.

서킷 브레이커는 전력 시스템의 전기 회로 차단기와 유사하게 작동합니다. 이는 지속적으로 구성 요소(예: 원격 서비스 호출)를 모니터링하고 반복된 실패 또는 성능 저하를 감지하면 해당 실패하는 구성 요소에 대한 호출을 일시적으로 차단하여 '회로를 열게' 합니다. 이 기간 동안 서킷 브레이커는 트래픽을 대체로 리다이렉트할 수 있습니다(예: 백업 시스템 또는 대체 기능).

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

소프트웨어 아키텍처에서 회로 차단기 패턴의 이점은 다음과 같습니다:

- 향상된 탄력성: 실패를 격리시켜 시스템의 다른 부분으로 전파되는 것을 방지합니다.
- 지연 시간 단축: 빠르게 트래픽을 백업이나 Fallback로 리디렉션하여 최종 사용자의 지연 시간을 줄입니다.
- 과부하 보호: 실패하는 구성 요소로의 새 요청을 일시적으로 제한함으로써 불필요한 과부하를 방지합니다.

회로 차단기는 일반적으로 세 가지 주요 상태에서 작동합니다: Closed, Open, Half-Open:

- Closed 상태:
  - 구성 요소로의 정상 트래픽 흐름을 허용합니다.
  - 구성 요소의 동작을 계속 모니터링합니다.
- Open 상태:
  - 회로 차단기가 비정상적인 실패 또는 성능 저하를 감지할 때 진입합니다.
  - 실패하는 구성 요소로의 트래픽을 적극적으로 차단합니다.
  - 더 이상의 실패 전파를 방지합니다.

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

Half-Open 상태:

- 오픈 상태에서 정의된 기간이 지난 후, 회로 차단기는 문제가 발생한 구성 요소가 회복되었는지 테스트하기 위해 Half-Open 상태로 전환될 수 있습니다.
- 제한된 수의 테스트 요청을 통과시키도록 합니다.
- 이러한 요청이 성공하면, 회로 차단기는 구성 요소가 작동 중이라는 것을 나타내는 Closed로 돌아갑니다.
- 실패가 계속되면, 보호를 연장하기 위해 다시 Open 상태로 돌아갑니다.
- Half-Open 상태에서는 이전에 실패한 구성 요소가 회복되었고 다시 트래픽을 신뢰할 수 있는지를 평가하기 위해 회로 차단기 메커니즘 자체에 의해 일반적으로 테스트 요청이 생성됩니다.

![](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_29.png)

Circuit Breaker 패턴을 구현하기 위해서는 필요한 종속성을 추가하는 것부터 시작해야 합니다.

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

![Understanding Microservices: A Modern Approach to Software Architecture](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_30.png)

예외 상황 발생 시 기본 데이터 또는 캐싱된 데이터로 응답하겠습니다. 일정 기간이 지난 후에는 해당 서비스와의 통신을 재개할 것입니다. 서비스가 응답하고 클로즈드 서킷 모드로 전환되면, 그렇지 않을 경우 오픈 서킷 모드로 전환하겠습니다.

![Understanding Microservices: A Modern Approach to Software Architecture](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_31.png)

간단한 예제를 살펴보죠: 모든 서비스를 시작한 후 고객 서비스를 중단할 것입니다.

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

![Understanding Microservices: A Modern Approach to Software Architecture](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_32.png)

We can see that the account service is still working, and the customer data returned is just default data.

![Understanding Microservices: A Modern Approach to Software Architecture](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_33.png)

Config-Service

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

마이크로서비스마다 자체 설정이 있는 경우 변경 사항이 발생하면 해당 서비스를 다시 시작해야 합니다. 또한, 대부분의 설정이 모든 서비스에서 동일하며, 각 구성 파일에 반복된 항목이 있습니다.

이 문제의 해결책은 모든 구성을 저장하고 관리할 수 있는 중앙 집중식 구성 서비스를 사용하는 것입니다.

먼저 간단한 스프링 부트 애플리케이션을 만들고 이 종속성을 추가할 것입니다.

![image](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_34.png)

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

효율적으로 설정을 관리하려면 모든 설정 파일을 저장할 Git 저장소를 만들어야 합니다. 이 저장소는 로컬에 있을 수도 있고(이 경우 구성 서버와 같은 기기에 있어야 함), 또는 원격 GitHub 저장소일 수 있습니다.

- Application.properties: 이 파일에는 모든 마이크로서비스 간에 공유되는 설정이 포함됩니다.
- 서비스별 구성: 각 마이크로서비스는 해당하는 이름(예: customer-service.properties)의 구성 파일을 가져야 합니다.

마이크로서비스의 이름은 해당 내부 구성 파일에 명시되어야 합니다. 마이크로서비스가 시작되면 구성 서버에 해당 구성 파일을 요청하는 요청을 보냅니다. 구성 서버는 올바르게 응답하기 위해 마이크로서비스의 이름을 알아야 합니다(포트도 마찬가지).

우리는 각 마이크로서비스에 이 구성을 추가해야 합니다.

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

![UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_35](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_35.png)

구성이 변경되면 구성 서비스에서 관련된 마이크로서비스로 요청이 전송됩니다 (/actuator/refresh로의 POST 요청), 그것에게 다시 시작하지 않고 구성을 새로 고쳐 달라는 것을 요청합니다. 그럼 마이크로서비스는 업데이트된 구성을 구성 서버에서 요청하고, 저장된 버전과 비교해서 변경된 부분만을 보내줍니다.

그래서 우리는 각 마이크로서비스에 Actuator 종속성을 추가해야 합니다.

![UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_36](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_36.png)

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

모든 마이크로서비스 간에 공유되도록 application.properties 파일 내에서 config 리포지토리에 이 구성을 추가해야 합니다. 이 구성은 액추에이터 엔드포인트를 활성화합니다.

![Actuator Configuration](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_37.png)

구성 파일을 위한 Git 리포지토리는 여기에 있습니다.

![Configuration Git Repository](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_38.png)

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

마지막으로, 설정 서비스의 구성 파일인 application.properties 내부에서 Git 리포지토리의 위치를 로컬 변수로 지정해야 합니다. 이는 구성 파일을 로컬 폴더에 포함하거나 구성 파일을 원격 리포지토리로 푸시하는 경우 GitHub 리포지토리의 URL일 수 있습니다.

![이미지](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_39.png)

# 마이크로서비스 아키텍처 도커화

이제, 마이크로서비스를 실행하려면 발견 서비스를 먼저 실행한 다음 구성 서비스와 게이트웨이를 실행해야 합니다. 이러한 서비스들이 올바르게 시작되면 다른 마이크로서비스를 시작할 수 있습니다. 다수의 마이크로서비스를 처리할 때 각각을 필요한 순서대로 수동으로 시작하는 것은 어려울 수 있습니다. 해결책은 모든 서비스와 종속성을 지정하여 올바른 순서로 시작되도록 보장하는 Docker Compose 파일을 사용하는 것입니다.

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

먼저 각 마이크로서비스에 이 도커파일을 추가해줍니다.

![도커파일](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_40.png)

도커파일을 간단하게 만들기 위해서는 Docker Compose를 실행하기 전에 마이크로서비스를 먼저 빌드해야 합니다. 권장하는 방법은 컨테이너를 실행할 때 애플리케이션을 빌드하는 겁니다.

다음은 도커 컴포즈입니다.

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

![이미지](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_41.png)

Building Images:

- Running `docker-compose up --build` builds Docker images for each service from their respective Dockerfiles.
- This ensures that each service starts with the latest configurations.

Health Checks:

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

- Docker Compose는 각 컨테이너의 상태를 모니터링하기 위해 건강 검사(healthcheck)를 사용합니다.
- 일반적으로 건강 검사는 컨테이너 내에서 특정 엔드포인트 (/actuator/health)를 쿼리하여 올바르게 작동하는지 확인합니다.

의존성 관리 (Depends On):

- 서비스는 시작 순서를 제어하기 위해 종속성(dependes_on)을 지정합니다.
- 이를 통해 다른 서비스에 의존하는 서비스가 해당 의존성이 시작될 때까지 기다립니다.

이제 환경 변수를 사용하기 위해 구성 파일을 변경해야 합니다.

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

예를 들어

![UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_42](/assets/img/2024-06-19-UnderstandingMicroservicesAModernApproachtoSoftwareArchitecture_42.png)

Docker Compose를 사용할 때는 DISCOVERY_SERVICE_URL 환경 변수를 활용합니다. 수동으로 실행하는 경우에는 일반적으로 localhost:8761/eureka를 사용합니다.

이로써 이 블로그를 마치겠습니다. 다음에 다시 만나요! 👍
