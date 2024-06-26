---
title: "마법 같은 우체통에서 발생하는 HTTP Parameter Pollution HPP 공격"
description: ""
coverImage: "/assets/img/2024-05-20-HTTPParameterPollutionHPPAttacksinamagicalmailbox_0.png"
date: 2024-05-20 16:04
ogImage:
  url: /assets/img/2024-05-20-HTTPParameterPollutionHPPAttacksinamagicalmailbox_0.png
tag: Tech
originalTitle: "HTTP Parameter Pollution (HPP) Attacks in a magical mailbox!"
link: "https://medium.com/@l0ok/http-parameter-pollution-hpp-attack-in-a-magical-mailbox-c84e2dee4a02"
---

![Magic Mailbox](/assets/img/2024-05-20-HTTPParameterPollutionHPPAttacksinamagicalmailbox_0.png)

# H-PP란 무엇인가요?

마법 같이 작동하는 우체통을 상상해보세요. 마법사에게 영감을 주는 편지를 보내는 우체통이 있습니다. 보내는 각 편지에는 "쿠키 만들기"나 "방 청소하기"와 같이 마법사에게 특별한 일을 할 것을 알려주는 지시사항을 적습니다. 이 지시사항을 파라미터라고 하는 종이에 기록합니다. 마법사에게 두 가지 이상의 일을 시키고 싶을 때는 "쿠키 만들기"와 "방 청소하기"와 같이 여러 개의 파라미터를 적습니다.

그런데 어느 날 장난기 많은 친구가 장난을 치려고 합니다. 당신이 알아차리지 못하는 사이에 편지에 추가적인 지시 사항을 몰래 넣습니다. 그래서 "쿠키 만들기"와 "방 청소하기"만 있는 대신에 마법사가 "쿠키 만들기", "멍멍이 키우기", "방 청소하기"와 같이 추가 지시서를 받게 됩니다. 더 많은 지시사항 때문에 마법사가 헷갈리게 되고, 때로는 어떤 것을 따를지 모를 때도 있습니다.

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

## 이 음모적인 트릭은 HTTP 매개 변수 공격(HPP)과 비슷합니다. 이를 단계별로 분석해 보죠:

## HTTP란 무엇인가요?

HTTP (하이퍼텍스트 전송 프로토콜)은 인터넷을 위한 마법의 우편함 시스템과 비슷합니다. 이는 컴퓨터가 웹사이트와 소통하는 방법이죠. 웹사이트를 방문하거나 그곳에서 무언가를 하려고 할 때, 컴퓨터는 명령(매개 변수로 된 편지처럼)을 웹사이트 서버(마법사)에게 보냅니다.

## 매개 변수란 무엇인가요?

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

파라미터는 웹 사이트 서버에 보내는 명령입니다. 무엇을 해야 하는지 알려줍니다. 예를 들어, 웹 사이트에서 "고양이"를 검색하려면, 파라미터는 이렇게 보일 수 있습니다: search=고양이. 이것은 서버에게 고양이에 대한 정보를 찾으라고 말해주는 것입니다.

## HTTP 파라미터 오염(HPP)이란?

HTTP 파라미터 오염은 교활한 공격자 (장난스러운 친구와 같은)가 서버에 보내는 메시지에 추가적인 파라미터를 추가하는 것입니다. 이렇게하면 서버가 혼란스러워질 수 있습니다. 어떤 지침을 따를지 모를 수 있습니다. 여러 가지 모순된 지침을 받아 어떤 것을 따를지 모르는 것과 비슷합니다.

## HPP가 문제가 되는 이유?

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

1. Serve에 혼란: 마법사가 뒤죽박죽된 지시를 받는 것처럼, 서버도 혼란스러워서 제대로 작동하지 않을 수 있습니다. 의도하지 않은 일을 할 수도 있습니다.

2. 보안 위험: 공격자는 HPP를 사용하여 서버를 속여 민감한 정보를 드러내도록 할 수 있어, 서버에 접근할 수 없어야 할 곳으로 침입할 수 있습니다.

3. 원하지 않는 작업: 공격자는 서버가 중요한 데이터를 삭제하거나 개인 정보에 접근하도록 하는 등 나쁜 작업을 수행할 수 있습니다.

## HPP 공격은 어떻게 발생하나요?

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

1. 다중 매개변수 전송: 공격자는 동일한 이름을 가진 여러 매개변수를 서버로 보냅니다. 예를 들어, 단순히 search=cats가 아니라 search=cats&search=dogs와 같이 보낼 수 있습니다.

2. 서버 동작 악용: 서로 다른 서버는 이러한 추가 매개변수를 다른 방식으로 처리합니다. 일부 서버는 중복을 무시할 수도 있고, 일부는 첫 번째 것을 선택할 수도 있으며, 다른 서버는 마지막 것을 선택할 수도 있습니다. 이러한 예측 불가능성이 바로 공격자가 악용하는 부분입니다.

3. 악의적인 매개변수 삽입: 공격자는 추가 매개변수에 해로운 지시문을 포함합니다. 예를 들어, admin=true와 같이 서버를 속여 관리자로 인식하도록 시도할 수 있습니다.

## HPP에 대한 어떻게 방어할 수 있을까요?

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

1. 입력 유효성 검사: 서버가 모든 들어오는 매개변수를 확인하고 정리하여 필요한 것들만 받아들이고 올바르게 포맷되었는지 확인해주세요.

2. 매개변수 제한: 서버가 추가 매개변수를 무시하거나 중복을 일관되고 안전한 방식으로 처리하도록 설계해주세요.

3. 보안 테스트: 정기적으로 웹사이트와 서버를 취약점을 확인하기 위해 테스트해주세요, 특히 많은 매개변수를 다루는 방식을 확인해봐주세요.

## 결론

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

HTTP 매개변수 오염은 친구가 마법사에게 편지에 혼란스러운 추가 지시를 넣는 것과 같아요. 이는 마법사(서버)가 당신이 요청하지 않은 일을 하거나 심지어 나쁜 일을 할 수 있게 합니다. 이를 방지하기 위해 지시사항을 주의 깊게 확인하고 올바른 지시사항만 따르는 것이 중요해요.

따라서, 장난꾸러기로부터 마법 사서함을 보호하듯이, 웹사이트는 이런 간굿한 속임수로부터 서버를 보호하여 모든 것이 원할하고 안전하게 유지되도록 해야 해요!

## 일부 참고자료:

- OWASP: HTTP 매개변수 오염에 대한 테스트
- Portswigger: 서버측 매개변수 오염
- HackTricks: 매개변수 오염

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

다시 만나기까지, 행복한 마법을 부리세요!
