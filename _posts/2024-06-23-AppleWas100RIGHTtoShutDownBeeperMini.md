---
title: "애플이 Beeper Mini를 중단한 것은 100 옳았다 - 이유는 무엇일까"
description: ""
coverImage: "/assets/img/2024-06-23-AppleWas100RIGHTtoShutDownBeeperMini_0.png"
date: 2024-06-23 23:25
ogImage:
  url: /assets/img/2024-06-23-AppleWas100RIGHTtoShutDownBeeperMini_0.png
tag: Tech
originalTitle: "Apple Was 100% RIGHT to Shut Down Beeper Mini"
link: "https://medium.com/@michaelswengel/apple-was-100-right-to-shut-down-beeper-mini-9f3582667f39"
---

![Beeper](/assets/img/2024-06-23-AppleWas100RIGHTtoShutDownBeeperMini_0.png)

내가 미쳤다고 생각할지도 모르는 전환 속에, 나는 Apple이 Beeper가 iMessage를 Android로 가져오려는 시도를 차단한 것이 완전히 옳았다고 믿습니다. iMessage가 Android에서 작동하는 것이 나쁠 것이라고 하는 게 아니라, Beeper가 운영 방식이 내 책에서는 그리 괜찮았기 때문입니다.

Beeper의 블로그는 "안드로이드 및 iPhone 고객들은 고화질 이미지/비디오, 암호화, 이모티콘, 타이핑 상태, 읽은 표시 및 모든 최신 채팅 기능과 함께 채팅할 수 있기를 간절히 원한다"고 주장합니다.

iMessage는 이러한 기능을 제공합니다. 다만, Android 폰에는 그렇지 않습니다. Beeper의 해결책은 무엇인가요? iMessage를 Android로 가져오는 것입니다!

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

문제는 실행 방식에 있습니다.

iMessage는 Apple 기기인 iPhone, iPad, Mac 및 Apple Watch와 함께 사용하도록 만들어졌습니다. Android나 Android 기기와 함께 사용하거나 이용할 수 있도록 만들어지지 않았습니다.

그리고 iMessage를 작동시키려면 Beeper가 시스템을 속여야 합니다.

# Beeper Mini란 무엇인가요?

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

Beeper Mini는 안드로이드 앱으로, Apple의 iMessage 프로토콜을 역공학으로 메시지를 보내어 어떤 안드로이드 폰이라도 iMessage를 사용할 수 있도록 설계되었습니다.

Beeper는 기기에서 암호화 키를 생성하고 공개 키를 Apple에 업로드합니다. 그러나 iMessage가 있는 iPhone에서와 같이 Apple 푸시 알림 서비스를 사용하는 대신, Beeper는 Apple의 서버와 상호작용하고 새 메시지 알림을 사용자에게 통지하는 Beeper Push Notification(BPN) 서비스를 개발했습니다.

여기서 주목할 점은, 이것이 iMessage의 단순한 모방이 아니라는 것입니다. Beeper의 시스템은 권한 없이 실제로 Apple 서버를 사용하도록 설계되었습니다.

Beeper는 Android로 iMessage를 가져오려는 첫 번째 시도가 아닙니다. Nothing Chats는 성공적으로 - 잠시 동안 - Nothing 폰에서 iMessage를 작동시켜마지만 중단되었습니다.

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

정말로, Beeper는 똑똑한 시스템입니다. 그리고 그것은 작동하는 것처럼 보입니다. 하지만 그것이 옳다고는 할 수 없습니다.

# 애플에 의한 종료

Beeper Mini가 처음 출시된 후 얼마 지나지 않아 애플에 의해 종료되었습니다. 애플은 Beeper가 애플의 재산을 오용했다는 우려를 제기하면서 이를 타당하게 꼽았습니다.

분명히, Beeper가 iMessage를 사용하는 방식을 기준으로 볼 때, 이것이 iMessage의 규정 위반인 것처럼 보이지는 않습니다. 그것은 좋은 소식입니다.

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

하지만, 애플 시스템을 오용하는 것은 아닙니다.

일부 사람들은 애플이 Beeper Mini를 폐쇄한 주된 또는 유일한 동기가 iMessage의 독점성을 보존하는 것인지 주장해왔습니다. 애플은 진정으로 보안에 관심이 없고 안드로이드 사용자를 제외하기만을 원한다고 말하는 사람들도 있습니다.

이것이 사실인지 아닌지 논쟁할 수 있습니다. 하지만 실제로 그렇다 하더라도, 애플이 자사 시스템과 지적 재산을 보호할 권리는 없을까요?

네, 정답은 네입니다.

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

여기서의 문제는 두 가지입니다:

- Beeper가 아이폰 간의 메시지를 주고받는 것으로 시스템을 속이기 위해 직접 Apple 서버에 연락하고 있습니다.
- Beeper는 "네이티브 iMessage 앱과 Apple 서버 간에 전송되는 트래픽을 분석하고 동일한 요청을 보내고 동일한 응답을 이해하는 자체 앱을 개발했다"고 주장합니다. 다시 말하면, 그들은 Apple의 지적 재산을 역공학으로 분석했습니다.

Beeper가 종료된 지 얼마 후에 약간의 수정을 거쳐 다시 온라인으로 돌아왔습니다.

현재 상황은 좋은 점과 나쁜 점이 혼재되어 있습니다 -- 어떤 사람에게는 작동되는 반면 다른 사람에게는 작동되지 않을 수 있습니다. 내 추측으로는, Apple이 비공식적으로 모두 종료하게 될 것으로 생각됩니다. 그리고 Beeper가 다시 돌아온다 하더라도, 그들이 앞으로 하게 될 일은 그냥 야생의 고양이와 마우스 장난일 것으로 예상합니다.

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

# “Apple just wants to keep iMessage exclusive.”

애플을 비판하는 사람들은 Beeper를 종료하기로 한 애플의 결정이 그 서비스 및 사용자의 보안을 위해서가 아닌 사악하게 행동했다고 주장합니다. 그들은 애플이 iMessage의 독점성을 보전하고 안드로이드 사용자를 생태계 밖으로 배제하기 위해 행동했다고 주장합니다.

일단 그게 사실이라고 가정해 봅시다. 가정해 봅시다.

그게 잘못된 건가요? 그게 문제가 될까요?

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

회사가 자신의 재산에 대해 원하는 대로 할 권리가 없습니까? Apple이 iPhone 사용자를 위한 독점 기능으로 자사의 재산 인 iMessage을 유지할 권리가 없습니까? 물론, 그 권리가 있습니다.

그러나 여기에는 더 많은 것이 작용할 수 있다고 말할 수도 있습니다.

Beeper는 제3자 응용 프로그램을 통해 Apple 서버로 메시지를 보내고, 이 응용 프로그램 자체가 역공학이 된 iMessage 프로토콜을 사용하여 작동합니다.

모든 면에서 그것은 걱정스럽습니다.

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

알았어요! 테이블 태그를 마크다운 형식으로 변경할게요.

| 번호 | 제품   | 가격 |
| ---- | ------ | ---- |
| 1    | 사과   | $1   |
| 2    | 바나나 | $2   |
| 3    | 체리   | $3   |

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

애플은 아직도 다른 애플 기기가 아닌 기기들과 통신하기 위해 SMS를 사용하고 있다는 사실이 맞아요. 그렇죠. 그리고 이것이 바뀌어야 한다는 사실도 맞아요 (그리고 곧 바뀔 예정이에요).

하지만 이게 변명이 될까요?

애플 시스템을 일부러 남용하는 것을 옹호할 이유가 될까요?

아마 그렇지 않아요. 그렇지 않다고 생각해요.

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

사실 안드로이드 및 iPhone 사용자들은 SMS를 사용하지 않고도 함께 채팅할 수 있는 방법이 있습니다.

메신저, WhatsApp, Signal 등을 통해 누군가에게 메시지를 보내려면 상대방도 해당 앱의 사용자여야 하지만, 그런 건 어렵지 않죠.

안드로이드 및 iOS 사용자들이 iMessage 사용자들이 오늘날 즐기는 많은 채팅 기능을 곧 누리게 될 때를 기대합니다.

애플의 시스템을 잘못 사용하는 것은 좋은 해결책이 아닙니다.

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

# "애플이 보안에 중요시한다면, 왜 아직도 SMS를 사용하나요?"

확실히 상대적으로 보안 수준이 낮은 SMS에서 전진할 수 있다면 좋을텐데, 다행히 Apple은 곧 SMS를 버리고 RCS를 선호하게 될 것입니다.

하지만 여러 번 읽어본 바에 의하면, Apple이 Beeper를 차단한 것은 사실상 iMessage 독점성을 보호하기 위한 조치일 뿐이며, 여전히 SMS를 사용한다는 점 때문에 보안에 중점을 두지 않고 있다는 주장을 볼 수 있습니다.

다시 말해, 정말로 보안과 개인 정보 보호를 중요시한다면, 먼저 SMS를 대체할 것에 집중해야 한다는 것입니다.

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

하지만 그게 어떻게 작동하는지 아시나요? 이건 빨간 물고기라고 할만한 거예요.

우리는 집 침입을 변론할 때 창문 잠금에 실패한 집 주인을 꺼내들어 가장하지 않아요. 창문을 잠그지 않아서 잘못했나요? 네. 그럼 침입은 여전히 잘못인가요? 네.

두 가지가 모두 맞아요. 둘 다 나쁘지만, 사실이에요.

# 여기서 객관적으로 생각해 보기

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

어떤 사람들이 나를 애플 열광자로 생각할 수 있다는 걸 방지하기 위해 먼저 말하지만, 전 그렇지 않아요. 쿠퍼티노에서 보여준 소비자에게 해로운 여러 가지 결정들에 대해 공개적으로 비판했어요. 그 중 일부는 다른 것보다 더 화나게 만들기도 했죠.

나는 소비자들에게 해로운 것으로 생각되는 결정들을 눈치 없이 비판하는 데 전혀 문제가 없고 때로는 절대적으로 악의적이라고 생각되는 결정들도 있어요. 오랫동안 나의 글을 따라온 사람들은 그것을 알 것이에요.

그러나 한 발 물러나서 명확한 머리로 생각해보면 분명해요: 여기서 나쁜 사람은 애플이 아니에요.

Beeper는 애플의 프로토콜을 역공학적으로 재구성하여 복사했고 (이것이 첫 번째 문제입니다), 이를 사용하여 애플의 서버와 통신하며 사실상 사용자를 대신해 애플 기기인 척 했어요. (여기가 두 번째 문제입니다.)

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

너아 둘 다 괜찮은 게 아니야.

일단, Beeper가 하려고 했던 것이 나쁜 것은 아니야. iMessage는 훌륭하고, 안드로이드 사용자가 공식적으로 사용할 수 있으면 좋겠어. 하지만 지금까지 그런 일은 일어나지 않았어.

아무도 채팅에서 "초록색 버블"이 되어서 부끄러워하거나 나쁜 기분을 가져야 하는 일은 없어야 해 — 네, 그렇게 되는 경우도 있지. 나는 그것이나 그런 행동을 옹호하고 있지 않아.

하지만 애플의 지적 재산권을 역공학하고 애플 시스템을 속이기 위해 사용하는 것은 해결책이 될 수 없어 — 비록 그 취지가 좋다고 해도.

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

# 어떻게 변경해야 할까요?

전체적으로 iPhone을 사용하고 iMessage를 즐겨 사용하는 것을 알려드리고 싶어요. 그래서 이 서비스를 좋아합니다. 이 서비스를 통해 일상적인 모든 문자 메시지 교류에 사용할 수 있다면 좋을텐데, 몇 가지 좋은 기능을 제공하기 때문이에요. 예를 들어, 누군가가 내 메시지를 확인했거나 응답을 입력 중이거나 하는 것을 볼 수 있어요. 이런 기능들은 당연하게 여기고 사용하고 있습니다.

하지만 이런 기능들이 2023년에 모두에게 제공되지 않는다는 것은 안타깝네요. 그래서 Android 사용자들도 이런 기능을 사용할 수 있도록 만들어지면 좋겠어요.

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

현대화된 문자 메시징이 필요한 시대입니다: RCS. 그 불충분한 기능을 가진 SMS는 마치 1990년대의 것처럼 느껴집니다. 아마 그래서 iMessage가 많은 사람들에게 매력적으로 느껴지는 이유일지도 모르겠네요?

제가 여기서 확실히 말씀드리고 싶은 것은, 저는 애플의 구식한 문자 메시징 방식을 결코 옹호하는 것이 아닙니다. 그들은 이제야 현대화되어야 했다고 생각해요.

하지만, 그래도 Beeper의 행동을 변명할 수는 없어요.
