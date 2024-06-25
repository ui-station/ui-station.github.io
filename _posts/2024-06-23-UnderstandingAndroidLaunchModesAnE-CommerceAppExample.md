---
title: "안드로이드 런치 모드 이해하기 이커머스 앱 사례로 알아보는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-UnderstandingAndroidLaunchModesAnE-CommerceAppExample_0.png"
date: 2024-06-23 21:05
ogImage:
  url: /assets/img/2024-06-23-UnderstandingAndroidLaunchModesAnE-CommerceAppExample_0.png
tag: Tech
originalTitle: "Understanding Android Launch Modes: An E-Commerce App Example"
link: "https://medium.com/@aadi.balaji/understanding-android-launch-modes-an-e-commerce-app-example-f24bf3387fac"
---

안드로이드 애플리케이션을 개발할 때는 런치 모드를 이해하는 것이 중요합니다. 런치 모드는 액티비티의 동작과 태스크 스택에서의 관리를 위해 결정되는데요. 런치 모드는 새로운 액티비티 인스턴스가 생성되는 방식과 기존 액티비티들과의 상호작용을 결정합니다. 이번에는 전자 상거래 애플리케이션을 예시로 하여 이 런치 모드들을 알아보도록 하겠습니다.

## 런치 모드란?

런치 모드는 액티비티가 어떻게 시작되는지와 기존 액티비티들이 태스크 스택에서 어떻게 처리되는지를 결정하는 설정입니다. 안드로이드는 네 가지 런치 모드를 제공합니다:

- Standard
- SingleTop
- SingleTask
- SingleInstance

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

# 쇼핑객 이야기: 엠마의 여정

## 1. 표준 시작 모드

엠마의 경험: 엠마는 전자상거래 앱을 열고 제품을 살펴봅니다. 몇 가지 항목을 선택하고 장바구니에 추가합니다. 더 많은 제품을 탐색하는 동안 각 제품 상세 화면이 새로운 인스턴스로 열리므로 항목을 쉽게 비교할 수 있습니다.

사용 사례: 표준 시작 모드는 여러 인스턴스가 허용되는 활동에 적합하며, 다른 제품을 보는 것과 같은 활동에 적합합니다.

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

## 2. SingleTop Launch Mode

엠마의 경험: 제품을 장바구니에 추가한 후, 엠마는 상품을 검토하기 위해 장바구니 화면으로 이동합니다. SingleTop 런치 모드를 사용하면 스택의 맨 위에 이미 있는 경우 장바구니 활동이 새 인스턴스를 만들지 않습니다. 이 동작은 앱의 다른 부분에서 장바구니로의 원활한 전환이 보장됩니다.

사용 사례: 장바구니 화면과 같이 스택의 맨 위에 여러 인스턴스를 가져서는 안 되는 활동에 유용합니다.

## 3. SingleTask Launch Mode

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

Emma의 경험: Emma는 결제 화면을 확인하고 결제 화면으로 이동합니다. SingleTask 실행 모드를 사용하는 결제 활동은 작업 스택에 활동의 인스턴스가 하나만 존재함을 보장합니다. 이 동작은 기존 결제 화면을 최상위에 유지하여 명확하고 집중된 결제 프로세스를 유지합니다.

사용 사례: 새로운 작업의 루트여야 하는 활동에 적합, 예를 들어 결제 화면.

## 4. SingleInstance 실행 모드

Emma의 경험: Emma가 구매를 완료하자, 다음 구매에 할인을 제공하는 특별 프로모션을 발견합니다. 프로모션 알림을 탭하면 singleInstance 실행 모드를 사용하여 새 작업에 프로모션 화면을 엽니다. 이 동작은 프로모션 화면을 격리하여 주 앱 플로우와 독립적으로 실행되도록 보장합니다.

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

사용 사례: 특별 프로모션 화면과 같이 독립적으로 실행해야 하는 활동에 적합합니다.

## 결론

각 활동에 적합한 시작 모드를 이해하고 활용하면 전자상거래 애플리케이션의 탐색 및 사용자 경험을 크게 향상시킬 수 있습니다. 사용자 여정의 감정적 측면을 고려함으로써 개발자는 더 매력적이고 원할한 쇼핑 경험을 제공할 수 있습니다.

Emma의 여정에서 우리는 상품 탐색부터 구매 완료까지 다양한 시작 모드가 그녀의 쇼핑 경험에 어떤 영향을 미치는지 보았습니다. 개발자로서 이러한 시작 모드를 실험함으로써 우리는 엠마와 같은 사용자를 위한 애플리케이션을 만들어 즐거운 개인화된 쇼핑 경험을 제공할 수 있습니다.

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

만약 이 글이 유용하다면 동료들과 공유해보세요. 더 많은 안드로이드 개발 팁과 튜토리얼을 보고 싶다면 LinkedIn에서 저를 팔로우해주세요.

감사합니다! 즐거운 코딩 되세요!
