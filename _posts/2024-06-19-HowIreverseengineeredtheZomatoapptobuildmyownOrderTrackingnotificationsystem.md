---
title: "제가 Zomato 앱을 역공학으로 분석해 나만의 주문 추적 알림 시스템을 만들었던 과정입니다"
description: ""
coverImage: "/assets/img/2024-06-19-HowIreverseengineeredtheZomatoapptobuildmyownOrderTrackingnotificationsystem_0.png"
date: 2024-06-19 13:40
ogImage:
  url: /assets/img/2024-06-19-HowIreverseengineeredtheZomatoapptobuildmyownOrderTrackingnotificationsystem_0.png
tag: Tech
originalTitle: "How I reverse engineered the Zomato app to build my own Order Tracking notification system"
link: "https://medium.com/@srivastavahardik/how-i-reverse-engineered-the-zomato-app-to-build-my-own-order-tracking-notification-system-22289a68dcb2"
---

(고지: 저는 독립 개발자/개인이며 Zomato 엔지니어 팀에서 일하고 있지 않습니다)

최종 제품이 어떻게 작동하는지 확인하려면 여기를 클릭하세요: [https://youtu.be/E89Etnvq6rY](https://youtu.be/E89Etnvq6rY)

코드를 확인하려면 여기를 클릭하세요: [https://github.com/oddlyspaced/zomato-notification/tree/main](https://github.com/oddlyspaced/zomato-notification/tree/main)

---

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

Zomato Android 앱을 매일 사용하는 사용자이자 앱 엔지니어로서, 나는 이 앱의 디자인과 어디에서나 음식 주문이 가능하다는 편의성을 사랑하게 되었습니다. 그러나 항상 음식 배달 상태를 확인하기 위해 앱을 열어야 한다는 점이 불편하다고 느꼈습니다. 앱은 훌륭한 주문 추적 화면을 제공하지만, iOS 버전의 Zomato 앱이 iOS 활동 인디케이터를 활용하여 제공하는 지속성과 쉬운 액세스를 제공하지 못한다는 점이 마음에 듭니다. 이에 영감을 받아 Zomato Android 앱을 역공학하여 주문 추적 경험을 향상시킬 수 있는 사용자 정의 솔루션을 구축하기로 결심했습니다. 이 글에서는 필요한 API 엔드포인트를 발견하고, 앱의 시스템 아키텍처를 설계하며, 반복적으로 Zomato 앱을 열 필요 없이 실시간 주문 추적 정보를 제공하는 지속적인 알림을 구현하는 과정을 공유하겠습니다.

# 파트 1: 앱 트래픽 이해

정보를 표시하는 방법을 이해하려면, 앱이 필요한 정보를 가져오는 방법을 파악해야 합니다. 앱과 서버 간의 네트워크 트래픽을 검사하는 것이 시작점으로 좋습니다. 이를 통해 앱으로 전송되는 정보에 대한 정확한 지식을 얻을 수 있습니다. 나중에 독립적으로 호출해야 하는 API 엔드포인트에 대한 정보를 제공해 줄 것입니다.

이를 위해 다음 도구를 활용했습니다:

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

- apktool
- Charles Proxy
- Android Emulator

앱을 디버깅하기 위한 설정 과정은 간단합니다. 이 글에서는 다루지 않겠습니다.

다음 글을 참고해주세요 :

# Part 2: 앱 트래픽 분석

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

환경 설정이 완료되었으므로 이제 트래픽을 분석하고 요청 내용을 확인할 수 있습니다. 주문 내역을 가져오는 요청을 찾으려면 앱의 주문 내역 페이지로 이동하여 동시에 Charles의 활동을 확인합니다. 이 페이지에 접속할 때마다 호출되는 특정 엔드포인트가 있는 것으로 보입니다. 바로 이것입니다:

```js
https://api.zomato.com/gw/order/history/online_order
```

<img src="/assets/img/2024-06-19-HowIreverseengineeredtheZomatoapptobuildmyownOrderTrackingnotificationsystem_0.png" />

이것에 대한 응답을 확인해 봅시다:

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

이 샘플의 요약된 응답입니다. 전체 샘플 응답은 여기 있습니다.

JSON 구조를 연구하면 결과 목록이 있고 레이아웃 구성 및 order_history_snippet_type_2 키가 있는 객체가 있음을 알 수 있습니다. order_history_snippet_type_2 객체에는 사용할 수 있는 정보가 포함되어 있습니다. click_action 객체에는 딥링크 문자열이 포함되어 있어 주문 ID를 추출하는 데 사용할 수 있습니다. 주문 ID는 다른 위치에도 있지만, 이것이 제 기준으로는 굉장히 간단한 장소로 보였습니다. 우리가 필요로 하는 다른 모든 정보가 포함된 top_container 및 bottom_container도 있습니다. 레스토랑 이름은 top_container의 title 객체 안에 있는 텍스트 항목에서 추출할 수 있습니다. 주문 ID는 딥링크에서 시작 태그를 대체하고 숫자를 유지하여 간단히 추출할 수 있습니다. 주문 상태는 top_container의 title 객체 안에 있는 텍스트 항목에서 추출할 수 있습니다. 주문 배달 시간은 bottom_container의 title 객체 안에 있는 텍스트 항목에서 추출할 수 있습니다.

마찬가지로 주문을 배치하고 앱의 주문 상세 페이지에서 트래픽 활동을 분석하면 다음 API Endpoint가 반복적으로 호출됩니다:

```js
https://api.zomato.com/v2/order/crystal_v2
```

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

이 엔드포인트의 응답에는 많은 개인 정보가 포함되어 있기 때문에 샘플 응답을 공유할 수 없습니다. 그러나 참고로 응답에서 다음 태그들은 다음 정보를 제공합니다 :

```js
response -> order_details -> res_name = 음식점 이름
response -> order_details -> tab_id = 주문 ID
response -> header_data -> pill_data -> left_data -> title -> text  = 예상 시간 (문자열) [예: 5분 후 도착 예정]
response -> header_data -> subtitle2 -> text = 텍스트 형식의 주문 상태 [예: 길을 가는 중, 곧 픽업될 예정 등]
response -> header_data -> pill_data -> right_data -> title -> text = 주문 예상 상태 [예: 제 시간, 지연됨]
```

# Part 3: 알림 디자인

데이터 가져오기 부분이 해결되었으므로, iOS의 활동 표시기에서 영감을 받은 알림 시스템을 구축하고 싶습니다. 이는 Zomato가 iOS 앱에서 활동 표시 서비스를 활용하는 방식입니다:

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

아래는 Figma 디자인으로 만든 Android 알림의 유사한 레이아웃입니다:

![Android 알림 디자인](/assets/img/2024-06-19-HowIreverseengineeredtheZomatoapptobuildmyownOrderTrackingnotificationsystem_2.png)

iOS 활동 지시기와 마찬가지로 앱에서 생성된 알림은 레스토랑 이름, 현재 주문 상태의 텍스트 표현, 배송 시간 예상 상태 및 실제 배송 전달 시간 예상을 표시합니다.

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

# 파트 3: 정보 흐름 처리 및 알림 관리

![이미지](/assets/img/2024-06-19-HowIreverseengineeredtheZomatoapptobuildmyownOrderTrackingnotificationsystem_3.png)

정기적으로 업데이트할 수 있는 알림을 갖기 위해 Foreground Service를 활용하고 주문 세부 정보를 계속 가져오도록 하는 기능이 필요합니다. 이 서비스를 "OrderTrackService"라고 부르겠습니다. 이 서비스의 주요 목적은 주문 ID를 수락하고 crystal_v2 API를 호출하여 주문 상태 정보를 가져온 다음 해당 정보를 알림에 표시하는 것입니다. 이 작업은 30초 간격으로 반복되며 주문 상태가 "배달 완료"가 될 때까지 실행됩니다.

알림을 처리하기 위해 주문 정보를 표시하는 사용자 정의 레이아웃을 만들 것입니다. 알림은 특정 ID로 표시됩니다. Android에서 동일한 ID로 알림을 다시 보내면 알림 패널에 이미 표시된 기존 알림이 자동으로 업데이트됩니다. 따라서 우리는 주문 정보를 가져와 기존 ID를 사용하여 알림을 다시 만들고 표시하는 이 논리를 활용합니다.

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

서비스를 제어하고 시작하기 위해서는 Activity도 필요합니다. 이 Activity의 주요 목적은 주문 내역을 가져와 목록으로 표시하는 것입니다. 이 목록을 통해 어떤 주문 ID를 OrderTrackService에 전달하여 알림을 처리할지 선택할 수 있습니다.

# 파트 4: 모두 함께 사용하기

디자인과 시스템이 명확하게 정해지면 이제 아이디어를 현실로 구현할 때입니다. 먼저 Jetpack Compose를 사용하여 MainActivity를 구현하여 시작했습니다. 메인 액티비티에는 세 개의 버튼이 있습니다. 첫 번째 버튼은 앱에 POST_NOTIFICATION 권한을 요청하는 기능을 합니다. 두 번째 버튼은 Foreground 서비스를 시작하는 기능을 하며, 마지막 버튼은 주문 내역을 가져오는 데 사용됩니다. "주문 가져오기" 버튼을 탭하면 앱은 online_order API를 호출하고 주문 상태가 "배달완료"가 아닌 결과를 필터링합니다. 필터링된 주문은 현재 활성화된 주문입니다.

API 엔드포인트를 호출하기 위해 retrofit 라이브러리를 구현하고 필요한 헤더와 함께 적절한 인터페이스를 만들었습니다. 코드 품질을 보장하기 위해 모든 것을 MVVM 패턴을 사용하여 구현했고 Retrofit 인스턴스를 처리하기 쉽도록 의존성 주입을 위해 Hilt를 사용했습니다.

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

OrderTrackService 클래스는 Service 클래스의 확장입니다. 우리는 여기서 알림 로직을 처리할 것입니다. MainActivity와 OrderTrackService 간의 통신은 Broadcast Intents를 통해 설정됩니다. MainActivity에 표시된 목록의 각 주문에 대응하는 버튼은 주문 ID가 Intent의 데이터 번들 일부로 포함된 Broadcast Intent를 보냅니다. 이 Broadcast Intent를 받으면 Foreground Service가 작동하여 crystal_v2 API를 호출하여 지정된 주문 ID에 대한 주문 세부 정보를 가져옵니다. 그런 다음 정보를 표시하는 알림을 생성합니다. 가져오기 및 게시 로직은 Kotlin Flow 내에서 작동하도록 제작되어 여러 알림을 독립적으로 처리할 수 있도록 지원하며 필요한 경우 반복적인 Flow를 시작합니다. Flow를 사용하면 원하는대로 작업을 일시 중지/반복할 수 있으므로 동일한 작업을 원하는 지연 시간(우리의 경우 여기서는 30초)으로 반복할 수도 있습니다.

```Markdown
<img src="/assets/img/2024-06-19-HowIreverseengineeredtheZomatoapptobuildmyownOrderTrackingnotificationsystem_4.png" />
```

# 최종:

이 사용자 지정 앱은 내 시간을 절약할 뿐만 아니라 주문 상태를 효율적으로 파악하는 더 나은 방법을 제공합니다. 개발 이후로 앱을 여러 번 들여다보지 않고도 주문 상태를 손쉽게 추적할 수 있어서 매우 편리합니다. 본문이 역공학 및 네트워크 호출의 구현 과정에 대한 통찰을 제공했기를 바랍니다.

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

이 프로젝트의 완전한 소스 코드를 GitHub 리포지토리에서 확인할 수 있어요:

[https://github.com/oddlyspaced/zomato-notification/tree/main](https://github.com/oddlyspaced/zomato-notification/tree/main)

자유롭게 탐색하고 실험하며, 더 많은 향상을 위한 아이디어가 있으면 프로젝트에 기여해주세요.
