---
title: "Swift 테스팅 프레임워크 소개"
description: ""
coverImage: "/assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_0.png"
date: 2024-06-19 13:59
ogImage: 
  url: /assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_0.png
tag: Tech
originalTitle: "Introduction to Swift Testing Framework"
link: "https://medium.com/simform-engineering/introduction-to-swift-testing-framework-aab7f6ecfb7d"
---


## Swift 개발 혁신: WWDC 2024에서 공개된 테스팅 도구

![이미지](/assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_0.png)

2024년 WWDC에서 가장 흥미로운 도구 중 하나는 Swift Testing으로, Swift 코드의 테스트를 이전보다 강력하게 만듭니다. 이를 통해 개발자들은 최소한의 코드로 높은 품질의 제품을 자신 있게 제공할 수 있습니다.

Swift Testing에는 동시성을 지원하는 기능, 병렬 테스트 실행, 그리고 테스트 매크로 도입과 같은 혁신적인 기능이 포함되어 있습니다.

<div class="content-ad"></div>

# 목차

- 전제 조건
- Swift 테스트 대상 추가 방법
- 구성 요소
- 매개변수화된 테스트
- XCTest와 Swift Test 비교
- XCTest에서의 이주 팁
- 결론

# 전제 조건

# Swift 테스트 대상 추가 방법?

<div class="content-ad"></div>

Swift 테스팅을 시작하려면 새 프로젝트를 생성할 때 XCTest UI 테스트와 함께 Swift Testing을 선택해야 합니다.

![이미지](/assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_1.png)

이제 해당 테스트 대상에 테스트 케이스를 작성할 수 있습니다.

# 구성 요소

<div class="content-ad"></div>

테스트가 가독성 있게 작성되어 있는지 확인하는 것이 중요합니다, 특히 코드베이스가 복잡해지는 경우 더욱 그렇습니다. Swift Testing은 이를 돕기 위해 몇 가지 기능을 추가했으므로 표현력 있는 테스트를 작성하는 것이 더 쉬울 것입니다.

총 4개의 구성 요소가 있습니다. 각각에 대한 자세한 정보를 아래에서 찾을 수 있습니다.

1. 테스트 함수

- 전역 함수 또는 클래스 메소드일 수 있습니다.
- async 또는 throws로 표시할 수 있습니다.

<div class="content-ad"></div>


![이미지](/assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_2.png)

2. 기대값

- #expect 매크로:

![이미지](/assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_3.png)


<div class="content-ad"></div>

만약 실패가 발생하면, 실패에 대한 자세한 정보가 표시되어 어디서 잘못된 것인지 이해할 수 있습니다.

![image](/assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_4.png)

- `#expect(.throws: (any Error).self)` 매크로:

만약 이 블록에서 어떤 오류도 throw하지 않는다면 실패합니다. 그렇지 않으면 성공합니다.

<div class="content-ad"></div>

아래의 테스트 케이스를 확인해보세요:

![test image](/assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_5.png)

위의 예에서, 오류를 던지는 함수는 테스트를 성공적으로 통과했지만, 오류를 던지지 않는 함수는 테스트에 통과하지 못했습니다.

- #require 매크로:

<div class="content-ad"></div>

해당 테이블 태그를 마크다운 형식으로 변경해주세요.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_7.png" />

4. 테스트 스위트

설정 및 정리 로직은 각각 init()와 deinit()을 사용하여 구현됩니다. init()은 테스트 실행 전에 호출되고, deinit()은 테스트 실행 후에 호출됩니다.

각 테스트 함수는 독립적으로 자체 인스턴스에서 실행되므로 실수로 데이터를 공유하지 않습니다.

<div class="content-ad"></div>

여기서 특정 테스트 이름을 가진 테스트 네비게이터에 두 개의 스위트를 볼 수 있습니다. 부 스위트는 부모 스위트 아래에 계층적으로 표시됩니다.

![Test Navigator](/assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_8.png)

# 매개변수화 테스트

매개변수화 테스트는 중복 코드나 과도한 개별 테스트 케이스 생성 없이 철저한 테스트 커버리지를 보장하는 데 도움이 됩니다.

<div class="content-ad"></div>

매개변수화된 테스트를 실습해 봅시다.

![이미지](/assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_9.png)

위는 Swift 테스트에서 매개변수화된 테스트의 잘못된 구문을 나타냅니다. @Test 어노테이션에서 인수를 사용해야 합니다.

위 예시에서 세 가지 인수가 제공되었습니다. 두 가지는 올바르고 하나는 잘못되었지만, 전체 테스트 케이스는 실패로 표시될 것입니다. 그러나 테스트 탐색기에서 실패의 원인이 된 인수를 볼 수 있습니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_10.png" />

# XCTest와 Swift Test 비교

Swift Tests와 XCTest의 구성 요소 비교:

1. 테스트 함수

<div class="content-ad"></div>


![이미지](/assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_11.png)

2. 기대

두 프레임워크의 기대는 매우 다릅니다.

![이미지](/assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_12.png)


<div class="content-ad"></div>

테스트가 실패하면 테스트가 중지되는 방식도 두 프레임워크에서 차이가 있습니다.

3. 테스트 스위트

![이미지](/assets/img/2024-06-19-IntroductiontoSwiftTestingFramework_13.png)

## XCTest에서의 이주를 위한 팁

<div class="content-ad"></div>

- XCTest와 Swift Test는 단일 타겟으로 작성할 수 있습니다. 새로운 타겟을 만드는 것은 필수적이지 않습니다.
- 여러 메소드가 유사한 구조를 가질 때는 하나의 테스트로 결합하고 해당 함수를 매개변수화할 수 있습니다.
- 개별 XCTest 클래스를 하나의 전역 @Test 함수로 결합합니다.
- Swift 테스트에서는 함수 이름에 테스트 접두사를 더 이상 사용할 필요가 없습니다. 함수에서 제거할 수 있습니다.
- Swift 테스팅에서는 XCTAssertion을 사용하지 않고, XCTest에 있는 #expect나 #require와 같은 테스팅 매크로를 피해야 합니다.

# 결론

Swift Testing은 매크로와 함께 표현력 높은 API를 제공하여 코드를 최소로 사용하여 테스트 동작을 선언할 수 있습니다. #expect API는 제공된 표현식을 유효성 검사하는 데 Swift 표현식과 연산자를 사용합니다. 매개변수화된 테스트는 유사한 구조의 코드를 수용하여 중복 코드를 줄이는 데 도움이 됩니다. 또한 Swift Testing은 병렬 테스트를 지원하여 전체 테스트 효율성과 생산성을 향상시킵니다. 그러나 직렬화된 테스트를 활성화하려면 직렬화된 특성을 사용해야 합니다.