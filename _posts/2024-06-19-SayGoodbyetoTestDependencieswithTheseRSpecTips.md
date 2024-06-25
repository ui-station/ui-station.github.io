---
title: "이 RSpec 팁들로 테스트 종속성을 작별하세요"
description: ""
coverImage: "/assets/img/2024-06-19-SayGoodbyetoTestDependencieswithTheseRSpecTips_0.png"
date: 2024-06-19 22:18
ogImage:
  url: /assets/img/2024-06-19-SayGoodbyetoTestDependencieswithTheseRSpecTips_0.png
tag: Tech
originalTitle: "Say Goodbye to Test Dependencies with These RSpec Tips!"
link: "https://medium.com/@patrykrogedu/say-goodbye-to-test-dependencies-with-these-rspec-tips-68dd88d07e23"
---

![image](/assets/img/2024-06-19-SayGoodbyetoTestDependencieswithTheseRSpecTips_0.png)

개발자가 직면하는 일반적인 문제 중 하나는 테스트 의존성을 관리하는 것입니다.

여기서 테스트 더블이 구원을 줍니다. 테스트 더블은 실제 객체 대신 사용되며 해당 객체의 동작을 제어할 수 있습니다.

# RSpec 3에서 응답 구성하기

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

# 테스트 더블 생성하기

테스트 더블은 실제 객체 대신에 테스트에서 사용되며, 그들의 동작을 제어할 수 있게 해줍니다. 목, 스텁, 스파이를 포함해 여러 종류의 테스트 더블이 있습니다. 여기서는 그들의 응답을 구성하는 데 중점을 둘 것입니다.

# 메서드가 값 반환하도록 허용하기

특정 값을 반환하도록 메서드를 구성하는 데 allow를 사용할 수 있습니다:

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

<img src="/assets/img/2024-06-19-SayGoodbyetoTestDependencieswithTheseRSpecTips_1.png" />

# 예외 발생시키기

예외 처리를 테스트하려면 메소드를 구성하여 오류를 발생시킬 수 있습니다:

<img src="/assets/img/2024-06-19-SayGoodbyetoTestDependencieswithTheseRSpecTips_2.png" />

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

# 블록에 양보하기

가끔은 더블을 블록에 값을 양보해야 할 때가 있습니다. 반복자나 콜백을 다룰 때 유용합니다:

![이미지](/assets/img/2024-06-19-SayGoodbyetoTestDependencieswithTheseRSpecTips_3.png)

# 여러 값을 반환하기

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

연이어 호출할 때 다른 값을 반환하는 방법이 필요하다면 and_return에 여러 인수를 전달할 수 있습니다:

![image](/assets/img/2024-06-19-SayGoodbyetoTestDependencieswithTheseRSpecTips_4.png)

## 부분 더블 구성

부분 더블은 실제 객체의 메서드를 모의(Mock)하거나 스텁(Stub)하는 동시에 객체의 나머지 동작을 유지하는 것을 가능하게 합니다. 객체의 특정 부분 동작을 테스트하고 싶을 때 전체 객체를 대체하지 않고 유용합니다.

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

# 원본 메서드 호출하기

일부 호출에 대해 부분 더블에게 원본 메서드를 호출하도록 지시할 수 있습니다:

![image](/assets/img/2024-06-19-SayGoodbyetoTestDependencieswithTheseRSpecTips_5.png)

# 원본 메서드 감싸기

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

메소드의 동작을 수정하면서 원본 구현을 호출할 수도 있어요:

![Image](/assets/img/2024-06-19-SayGoodbyetoTestDependencieswithTheseRSpecTips_6.png)

## 고급 사용자 정의

## 간헐적 동작

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

보다 복잡한 동작을 시뮬레이션하기 위해 네트워크 장애와 같은 부분적인 특성을 정의하기 위해 블록을 사용하세요:

![이미지](/assets/img/2024-06-19-SayGoodbyetoTestDependencieswithTheseRSpecTips_7.png)

# 팁과 꿀팁

- 구체적으로 설정하세요: 테스트 더블을 구성할 때 제한 사항을 가능한 한 명확하게 지정하여 테스트가 의미 있는지 확인하세요.
- 부분적인 더블을 현명하게 사용하세요: 부분적인 더블은 강력하지만 테스트 사이의 강하게 결합된 테스트로 이어질 수 있습니다. 전체 객체를 교체하지 않고 특정 상호 작용을 테스트하기 위해 사용하세요.
- 유지보수 가능한 테스트 유지하세요: 명확하고 간결한 테스트를 작성하여 테스트 스윗을 유지하세요. 지나치게 복잡한 테스트 로직을 피하세요.
