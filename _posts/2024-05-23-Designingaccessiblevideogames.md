---
title: "접근성 있는 비디오 게임 디자인하기"
description: ""
coverImage: "/assets/img/2024-05-23-Designingaccessiblevideogames_0.png"
date: 2024-05-23 13:43
ogImage:
  url: /assets/img/2024-05-23-Designingaccessiblevideogames_0.png
tag: Tech
originalTitle: "Designing accessible video games"
link: "https://medium.com/user-experience-design-1/designing-accessible-video-games-f4c9824a9182"
---

비디오 게임은 오직 즐거움의 형태에 머무르는 것이 아닙니다; 그것들은 이야기 전달, 소셜 상호 작용, 그리고 몰입형 경험을 위한 강력한 매체입니다.

사람들이 여가 시간을 게이밍에 할애하는 이유는 많습니다. 시작하기에, 그들은 현실에서 벗어나 다른 사람들과 연결되는 방법입니다. 누군가가 왜 게임을 원할 수 있는 가능한 이유들을 모두 나열할 수 있지만, 그것은 그 자체로 한 섹션이 될 것입니다.

모든 사람은 게임의 독특하고 몰입적인 세계를 즐길 자격이 있지만, 불행하게도 그것이 많은 게임에는 해당되지 않습니다. 지난 몇 년 동안 접근성 있는 게임 디자인으로의 움직임이 있었지만, 아직 가야 할 길은 멀습니다.

많은 게임이 일부 접근성 설정과 기능을 구현하고 있지만, 몇몇 회사만이 시간, 예산, 그리고 능력이 The Last of Us Part II만큼 멋지게 접근 가능하도록하는 게임을 만들 수 있습니다.

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

위와 같이, 접근성에 대한 대화를 중요시하고 시작부터 구현하는 것이 중요합니다. 복잡한 설정 메뉴가 모든 게임에 가능하지는 않을 수 있음을 이해합니다, 특히 소규모 스튜디오의 경우에는 더 그렇습니다.

예산이 전무한 소규모 인디 스튜디오에서 근무하고 있는 사람으로서, 제가 이해한다는 것에 신뢰해 주세요.

게임을 접근성 있게 디자인하는 것은 모든 플레이어에 도움이 되며, 접근성을 고려한 기능은 모든 능력을 가진 플레이어들에게 널리 사용됩니다.

자막을 예로 들어 보겠습니다. 명백히 자막은 청각 장애가 있는 플레이어들에게 매우 유용한 도구이지만, 청각 장애가 없는 플레이어들도 자주 사용합니다.

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

노트: 이 기사는 접근성 있는 게임을 디자인하는 방법 중 일부만 다룹니다.

더 포괄적인 목록이 궁금하시다면 '게임 접근성 지침'을 확인해보세요.

여러분께서는 직접 조사를 하시고, 무엇보다도 다양한 능력을 가진 실제 사용자들로 게임을 테스트하는 것을 권장합니다.

# 어시스트 모드 vs 의도한 경험

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

일부 플레이어는 게임 자체의 도움을 필요로하여 제시된 도전들을 극복할 수 있습니다. 특정 게임들은 조준장치를 추가하여 플레이어가 조준, 조향, 내비게이션, 퍼즐 해결 또는 장애물 극복 등 다양한 방식으로 도움을 받을 수 있도록 합니다. 이렇게 함으로써, 플레이어는 게임의 난이도를 맞춤 설정하고 자신의 속도에 맞춰 게임 콘텐츠를 즐길 수 있습니다.

일부 게임 회사들 사이에서 이러한 보조가 어떻게 게임의 정신이나 목적을 망치는지에 대한 논쟁이 있습니다.

특정 프랜차이즈의 개발자들과 팬들은 종종 "프롬 소프트웨어의 '세키로: 섀도우 다이 트와이스(Sekiro: Shadows Die Twice)'나 Sloclap의 '시퍼(Sifu)'와 같은 게임에 쉬운 난이도나 보조 게임 설정을 추가하는 것을 반대하며, 예술적 방향성과 개발자의 목표인 형벌적인 경험을 만들려는 것을 인용하며 다른 난이도 모드 추가는 '망치게 될 것'이라고 주장합니다."

만약 다크 소울즈(Dark Souls)이 쉬웠다면, 다크 솔울즈가 아니었을 것입니다. 이는 사실일 수 있지만, 접근성 옵션은 게임을 쉽게 만드는 것이 아니라 광범위한 대중에 渙근하게 하는 것을 목적으로 합니다.

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

게임이 제공하는 체험을 제한하거나 묽게 만드는 것이 아닙니다. 만약 당신의 게임이 숲을 통해 야생 동물을 쫓는 긴장감을 제공하길 원한다면, 그것이 플레이어 경험으로 추구해야 할 것입니다.

모든 실력 수준의 플레이어는 게임을 플레이하는 동안 도전과 재미를 동시에 찾고 있습니다. 에임 어시스트와 같은 기능을 도입하면 처음에는 플레이어가 속임수를 쓰는 것을 느낄 수도 있지만, 현실은 그렇지 않습니다. 에임 어시스트를 추가하는 예시에서는, 에임에 대한 신체적 제약을 겪는 개인들에게 기회를 제공하여 게임을 원활히 탐험하고, 멋진 느낌을 느낄 수 있게 합니다.

플레이어가 게임을 진행하는 데 도움이 필요하지 않은데도 어시스트 모드를 사용할까봐 걱정된다면, 경고 문구를 추가하는 것을 고려해보세요.

인기 있는 인디 플랫포머인 'Celeste'는 플레이어가 게임을 자신의 요구에 맞게 조정할 수 있도록 허용하면서도, 이것이 게임을 플레이하는 방식이 아니라는 경고를 제공하는 뛰어난 작업을 합니다.

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

게임 제작자 도구킷이 말하듯이, 접근성과 게임이 의도대로 플레이되는 것 사이의 균형을 맞추는 것은 하나의 중요한 요소, 소통에 달려 있어요.

우리가 제대로 소통한다면, 사용자들은 게임이 어떻게 플레이되어야 하는지 알게 될 거예요. 특정 보조 모드나 설정을 켜야 하는지 결정하는 것은 플레이어의 몫이에요. 이는 그들을 위해 디자인된 의도된 경험을 변경할 수 있어요.

난이도는 게임의 일부입니다. 그러나 플레이어가 물리적 또는 정신적으로 그것을 완료할 수 없는 정도로 게임을 너무 어렵게 만들면, 그들은 그것을 전혀 플레이할 수 없게 될 거에요.

# 멀티플레이어 게임에서의 접근성 균형 설정

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

지금, 지금. 방 안의 코끼리에 대해 얘기해 보죠: 멀티플레이어 게임. 멀티플레이 경험을 공정하게 유지하면서 접근성 높은 것으로 만드는 방법은 뭘까요?

APX 프레임워크는 이를 "하우스 룰"이라고 부릅니다.

플레이어들은 온라인에서 다른 사람들과 맞붙을 때 선택지를 받을 수 있습니다. 플레이어들은 특정 설정(에임 어시스트, 매크로 등), 게임 옵션(매치 길이, 게임 모드 등), 그리고/또는 특정 실력 수준의 플레이어들과만 플레이할지 선택할 수 있습니다.

![Designingaccessiblevideogames_0](/assets/img/2024-05-23-Designingaccessiblevideogames_0.png)

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

만약 플레이어들이 특정 설정으로 다른 사람들에게 이점을 준다고 느낀다면, 그들은 해당 설정을 사용하지 않는 플레이어들과 함께 대기열에 들 수 있습니다.

이렇게 함으로써 플레이어 간 의사 소통에 관한 문제도 해결할 수 있습니다. 예를 들어, 음성 채팅 없이 게임을 선호하는 플레이어들 또는 그러한 방식을 수용할 수 있는 플레이어들과 온라인 멀티플레이 매치를 우선적으로 매칭하는 옵션을 제공하세요. 의사소통에 크게 의존하는 협력 게임에서는 음성 대화를 할 수 없는 플레이어들로 인해 팀원들이 좌절할 수도 있습니다. 이러한 좌절은 게임에서 해당 플레이어를 제거하거나 미래 매치에서 제외시킬 수도 있습니다.

# 저장 — 자동 및 수동

몇몇 사람들은 어려운 도전에 맞서고 같은 레벨을 통과하기 위해 끊임없이 시간을 보내는 것을 좋아합니다 (FromSoftware 팬들에게 주목!).

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

다른 사람들은 그렇지 않을 수도 있습니다. 원하는 때에 게임을 저장하고 싶다는 것은 단순히 개인적인 취향일 수도 있고, 신체적, 정신적 스트레스를 덜기 위한 문제일 수도 있습니다.

신체적 장애를 가진 플레이어들은 힘든 동작이나 도전적인 상황을 반복하기 위해 게임을 저장해야 할 수도 있습니다. 가장 유명한 게임 메커니즘 중 하나를 예를 들어보겠습니다: 퀵타임 이벤트. 특히 버튼 누르기입니다.

(이상적으로는 버튼을 연타하는 부분을 우회하거나 그 부분을 완전히 제거할 방법을 추가하고 싶지만, 이 예시를 계속 진행해 봅시다.)

사용자가 방금 큰 무서운 괴물을 피하기 위해 "X"를 연타해야 했던 작업을 끝냈다고 상상해봅시다. 안도의 한숨을 내쉬며 그 다음 장면으로 나아갑니다. 은어, 저거는 못 봤네요; 건너 돌다가 절벽으로 떨어지고 큰 “게임 오버”가 화면에 나타납니다. 다시 시도하고, 이미 따가운 버튼 누르기 손이 아팠던 사용자는 그 섹션을 다시 해야 한다는 걸 깨달을 것입니다.

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

일부 플레이어들은 그 섹션을 다시 시작하면서 한숨을 쉴 수도 있지만, 다른 사람들은 그 섹션을 다시 완료할 물리적 또는 정신적 능력이 되지 않을 수도 있어요.

버튼을 연담하다 보니 플레이어에게 신체적으로 지친 상태가 되어 하루 쉬고 다음 날 다시 시도해야 할 수도 있어요. 다음 날이 왔지만, 다시 시도하여 실패하고 다시 쉬고 다음 날 다시 시도해야 할 수도 있어요. 즐겁지 않죠.

이 도전은 반드시 신체적인 것만은 아닐 수도 있어요.

게임의 특정 섹션에서 플레이어가 계속해서 듣거나 보고 싶어하지 않는 매우 감정적으로 자극하는 대화나 이미지가 있는 가능성도 있어요.

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

특정 섹션이 너무 자극적이어서 진행이 어려울 수 있습니다.

무엇이든 그 이유가 되더라도, 사용자들에게 저장할 수 있게 해주세요.

저는 개인적으로 자동 저장과 수동 저장 둘 다 구현하는 것을 추천드립니다.

왜냐하면 몇 가지 이유가 있습니다.

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

자동 저장은, 그냥 자동으로 됩니다. 사용자들은 자신이 죽거나 집의 전원이 어떤 이유로든 꺼진다고 해도 많은 진행 상황을 잃을 걱정을 할 필요가 없습니다.

하지만, 자동 저장에는 몇 가지 문제가 있습니다.

우리 아버지는 게임 The Callisto Protocol의 출시일에 대해 매우 흥분했습니다. 그는 잠깐 게임을 플레이하고 멋지다고 생각했지만, 가장 어려운 난이도로 플레이하더라도 실제 도전감이 전혀 느껴지지 않았다고 느꼈습니다.

그는 나를 불러 자신이 특정 부분을 진행하면서 적에게 죽고, 다시 부활해서 방금 전에 있었던 곳에서 10 피트 떨어진 곳에서 되살아났다는 것을 보라고 말했습니다.

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

"그는 말했다, '게임 전체가 이런 식이야. 내가 죽어도 걱정 안 해. 원래 있던 곳으로 돌아가는 데 10초가 걸리니까. 긴장감이 없어.'

게임을 자주 저장하는 것은 일부 플레이어에게 좋을 수 있지만, 다른 사람들의 경험을 방해할 수도 있어. 나는 일정 구간을 거치고 자동 저장하는 것을 선호하지만, 사용자가 수동으로 저장할 수 있는 옵션을 제공하는 게 좋다.

수동 저장 옵션이 없으면 짧은 간격으로만 게임을 플레이하거나 사전 예고 없이 갑자기 게임을 중단해야 하는 게이머에게 불리할 수 있다.

더 큰 장애물을 가진 플레이어들에게는 이 제한이 게임을 할 수 있게 여부를 결정할 수도 있다."

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

만약 누군가가 다시 하기 싫은 섹션을 완료한다면, 저장할 수 있어요. 도전을 좋아하는 플레이어들은 계속해서 진행할 수 있어요.

# 난이도 모드는 싫어

비디오 게임에서 난이도를 조절하는 것에 대해 이야기해봐요.

엄청나게 많은 게임들이 플레이어들이 난이도를 선택할 수 있는 옵션을 제공하지만, 그중에서 제대로 하는 게임은 매우 드데요.

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

어려움은 적의 체력, 플레이어 체력, 적의 수, 전리품 드랍 빈도, 플레이어 및 적의 공격력 등을 조절합니다.

이 모든 것은 괜찮은데, 더 자세히 살펴보기 시작하면 문제가 될 수 있어요.

예를 들어, 어떤 이용자는 게임의 전투 요소를 정말 좋아하지만 무슨 이유에서인지 게임의 파쿠르 부분을 완료하지 못할 수 있어요. 그들은 전투 부분에 높은 난이도를 원할지 몰라도 파쿠르 부분에는 쉬운 난이도를 원할 수 있어요. 또한 누군가는 액션을 좋아하지만 한 플랫폼을 건너는 데 2시간을 헤매다가, 이 경험이 그만한 가치가 없어지는 경우도 있을 거예요.

난이도 설정을 조절할 수 없다면, 상황은 어려움의 암흑일 수 있어요. 예를 들어, 쉬운 난이도에서 게임을 플레이하면 전투가 재미없어 보일 수 있지만, 어려운 난이도에서 플레이하면 플랫폼이 불편할 수 있어요.

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

장애를 가진 플레이어를 고려할 때 이 문제는 더욱 복잡해질 수 있습니다.

보편적인 컨트롤의 접근성이 부족하여 어려움을 겪을 수 있는 사람을 고려해 봅시다. 그들은 주로 쉬운 난이도 설정을 선택하여 명령을 단순화하고 게임 플레이 속도를 늦추게 됩니다. 이러한 선택은 어떤 접근성을 제공하기는 하지만 게임에서 찾는 전반적인 도전과 흥미를 떨어뜨릴 수도 있습니다.

많은 게이머들이 쉬운 난이도를 선택하는 것은 도전을 원치 않기 때문이 아니라, 그것이 접근 가능한 유일한 모드이기 때문일 수도 있습니다.

일반적으로 어려움 설정을 선택하는 것은 혼란과 스트레스를 초래할 수도 있습니다. 게임의 여러 요소를 몇 가지 옵션으로 일반화하여 선택할 수 있도록 하는 어려움 모드는 게임에서 각 난이도가 실제 게임 플레이에 어떤 의미를 갖는지 명확히 설명해 주지 않는 경향이 있습니다.

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

균형 잡힌 경험. 멋지네요… 하지만… 균형이 정확히 무슨 뜻이죠?

몬스터보다 내가 가진 체력이 동일한 수준이라는 걸 의미하는 건가요? 더 많은가요? 어떤 스탯이 영향을 받나요? 이 중 어떤 설정이 게임을 접근하기 쉽게 만들어줄까요?

전혀 모르겠네요.

그럼, 해결책은 무엇일까요?

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

그래요, 각 플레이어마다 독특하고 능력 수준 및 개인 성향에 맞는 맞춤형 경험을 얻을 수 있다는 것 같아요. 그렇다면… 그걸 한정된 난이도 옵션에 어떻게 넣을까요?

음, 번들 하나 말이죠.

구체적으로 말하면, 플레이어가 조절할 수 있는 설정들의 번들이죠.

어찌되었든, 난이도 모드는 그냥 서로 다른 설정들의 번들일 뿐이에요; 우리는 그것을 바꿀 수 있는 옵션을 플레이어에게 일반적으로 제공하지 않을 뿐이죠.

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

플레이어들이 적의 수, 시간 제한, 보상 획득량 등을 조절할 수 있는 기능을 제공한다면, 그들이 직접 원하는 난이도 모드를 만들 수 있어요.

난이도 모드에 몇 가지 사전 설정값을 사용자에게 제공하되, 이후 사용자가 직접 선호에 맞게 수정할 수 있도록 해보세요.

여러 난이도 모드를 제공하는 것 외에도, 사용자가 맞춤형 난이도를 선택할 수 있도록 하는 것도 좋아요.

난이도 설정이 정말 잘 된 게임 중 하나인 Pathologic 2 생존 게임이 있어요.

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

누군가는 가장 강렬하고 복잡한 퍼즐을 원할 수 있고, 또한 가장 쉬운 전투를 선호할 수도 있습니다. 또 다른 누군가는 퍼즐을 완전히 건너뛰고 가장 도전적인 전투 경험을 원할 수도 있죠.

플레이어가 자신만의 플레이 스타일에 가장 적합한 경험을 만들 수 있도록 옵션을 제공해주세요.

# 감각을 활용해보세요

중요한 정보를 플레이어에게 전달할 때 적어도 두 가지 다른 감각을 사용하는 것이 좋은 지침입니다.

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

예를 들어, 전리품이 나온 경우 시각적인 표시뿐만 아니라 청각적인 표시도 하세요. 플레이어가 피해를 입은 경우 시각적 피드백과 함께 햅틱 피드백을 제공하세요.

여러 감각을 통해 정보를 전달하는 좋은 예시는 게임 모탈 컴뱃(Mortal Kombat)에서 찾을 수 있습니다.

카를로스 바스케즈, 또는 스트리머 래틀헤드로 알려진 사람은 시각 장애인 프로 모탈 컴뱃 플레이어입니다. 그는 소리에 완전히 의존하면서 최고 수준의 경기에 참가할 수 있습니다.

TheGamer와의 인터뷰 중에 Rattlehead는 게임이 설계되는 동안 때로는 "우연한 접근 가능성"을 만들어낼 수 있다고 언급합니다. 이 우연한 접근 가능성은 게임을 플레이할 수 있게 만들어 줄 수도 있지만, 개발자들이 실제로 시각 장애인 게이머를 듣게 되면 게임이 완전히 새로운 수준으로 진화될 수 있습니다.

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

어떤 사람들은 2D 대전 게임과 같은 일부 종류의 게임만 시각 장애인 커뮤니티에 접근 가능하다고 주장합니다. 이는 사실이 될 수 있지만, 접근성을 처음부터 고려하지 않는다면 해당할 수 있습니다.

접근성을 고려하여 게임을 만든다면 복잡한 3D 게임도 누구에게나 플레이할 수 있게 할 수 있습니다. 이러한 사례 중 하나가 God of War: 라그나로크에 있습니다. 시갚을 사용하지 않고도 게임을 완주할 수 있습니다.

게임에서는 플레이어가 어디로 가야하는지 듣을 수 있는 핑 시스템과 적과 싸울 때 락온 시스템을 사용합니다.

Ross Minor의 게임 플레이를 시청해보면, 2022 게임 어워즈에서 접근성 혁신상을 수상하더라도 해당 게임이 완벽에 가깝지 않음을 알게 될 것입니다.

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

비디오 게임은 접근성 측면에서 많은 발전을 이뤄왔지만, 아직 가야 할 길이 많습니다.

God of War: Ragnarök과 같이 철저한 접근성 시스템을 갖춘 게임들을 공부하고, 그들이 제대로 한 부분과 개선할 수 있는 부분을 주목해 보는 걸 권장합니다.

이어서 청각 정보를 시각적으로 전달하는 것에 대해 이야기해 보겠습니다. 청각 장애가 있는 플레이어들은 햅틱스나 부가적인 시각적 표시를 통해 정보를 전달받아야 할 수도 있습니다.

시작하려면, 생존 게임인 Raft에서 나쁜 예시를 살펴보겠습니다.

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

(참고: 저는 Raft를 사랑하는 팬으로, 그것과 함께 많은 즐거움을 느꼈지만, 접근성 부분에는 많은 부족함이 있습니다.)

Raft의 주요 기능 중 하나는 상황에 따라 플레이어에게 다가와 그들의 뗏목/기지를 먹으려는 상어를 중심으로 돌아갑니다. 플레이어는 상어를 막기 위해 어떤 종류의 막대나 무기로 상어를 때려야 합니다.

플레이어는 상어가 나타날 때 상어가 나뭇결을 물거나 철퍼지는 소리를 듣고 알 수 있습니다.

플레이어가 청각 장애가 있거나 청력이 떨어지거나 시끄러운 환경에서 플레이하거나 소리 없이 게임을 플레이할 때는 직접 상어를 바라봐야만 상어가 있는지 알 수 있는 방법이 없습니다. 이 문제는 플레이어가 특히 큰 기지를 건설하고 뗏목의 모든 구석을 보는 방법이 없을 때 더 큰 문제가 됩니다. 이 문제는 소리가 어디에서 시계가 오는지 표시하는 시각적 지시기를 사용하여 해결할 수 있습니다.

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

포트나이트는 시각적 오디오를 활용한 게임의 좋은 예시입니다. 플레이어 주위에 나타나는 링은 다양한 소리가 어디에서 나오는지를 표시합니다. 발소리, 총 소리, 전리품 등이 링에 표시되어 플레이어가 특정 방향에서 무슨 일이 일어나고 있는지 알 수 있습니다.

이 설정은 청각 장애가 있는 사용자, 소리 없이 플레이하는 사용자 또는 소음이 있는 환경에서도 사용자들이 공평하게 게임을 즐길 수 있도록 도와줍니다.

각각의 감각을 통해 정보를 전달하는 것 뿐만 아니라 정보가 명확히 표시되도록 하는 것도 중요합니다.

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

시각 장애를 가진 게이머들을 위해 경험을 맞춤 설정하는 것은 배경 세부사항을 없애는 것을 포함할 수도 있습니다. 이 조정은 그들이 전경의 중요한 물체들을 쉽게 구별하고 집중할 수 있도록 돕습니다. 이에 대한 훌륭한 예시는 The Last of Us: Part II에서 볼 수 있습니다.

![이미지](/assets/img/2024-05-23-Designingaccessiblevideogames_2.png)

상호작용 가능한 항목들과 적들과 같은 중요한 요소들이 강조되고 배경은 회색조로 처리됩니다. 플레이어들은 화면에서 중요한 정보를 더 쉽게 파악할 수 있습니다.

신경 다양성을 가진 일부 플레이어들에게는 특정 요소를 제거하여 시각적 장면을 조정하는 것이 필수적일 수 있습니다. 이 수정은 그들이 받는 시각적 자극을 줄이고, 그들의 독특한 요구에 맞는 편안한 환경을 만들어냅니다.

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

게이머들은 오디오 설정을 조절하여 볼륨 레벨을 조절하거나 다른 오디오 채널을 관리하기를 원할 수 있습니다. 예를 들어, 플레이어는 음악을 줄이고 특정 효과음을 올려서 게임 진행을 완전히 이해할 수 있을 수도 있습니다. 이 유연성은 게임의 소리 신호를 통해 중요한 정보를 신뢰할 수 있게 흡수할 수 있도록 보장합니다.

# 함께 하면 더 즐겁습니다

친구나 가족과 함께 플레이할 수 있는 기능을 고려해보세요.

어떤 플레이어들은 게임의 일부를 다른 사람에게 맡길 필요가 있을 수도 있습니다. 예를 들어, 플레이어는 버튼을 누를 수 있지만 조이스틱을 사용할 수 없을 수도 있습니다. 다른 사람이 다른 컨트롤러를 이용하여 함께 들어와 조이스틱을 통해 모든 움직임을 제어할 수 있다면, 다른 플레이어는 버튼을 전부 누를 수 있습니다.

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

예를 들어 Xbox는 Xbox Copilot을 통해 이 문제를 깔끔하게 해결했습니다. Copilot을 켜면 두 명의 플레이어가 각각 다른 컨트롤러를 사용하여 게임을 조종할 수 있습니다. 이는 "플레이어들이 게임을 플레이하는 데 필요한 조작을 서로 나눠서 맡도록 할 수 있게 해주는데, 이는 한 명의 플레이어가 게임을 플레이하는 데 필요한 모든 행동을 처리할 수 없는 상황이거나 플레이어들이 공동 경험을 원할 때 유용합니다."

난이도가 있는 섹션을 완수하거나 도전적인 퍼즐을 해결하거나 감정적인 콘텐츠를 다룰 때 다른 사람이 도와주는 것은 플레이어가 진전하는 데 큰 도움이 됩니다.

이것은 장애를 가진 사용자들뿐만 아니라, 능숙하지 못한 어린 가족 구성원과 함께 게임을 완주할 수 있는 상황도 고려해보세요.

이것은 플레이어 사이에 공동 경험을 만들어내는 협동 요소를 추가하는 재미있는 방법입니다. 게임이 협동 모드가 아니라면, 일반적으로 한 명이 플레이하는 경험이었던 것을 커플, 친구, 가족끼리 협력하여 공유할 수 있는 방법이 될 수도 있습니다.

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

# 매크로

마이크로소프트에 따르면, 매크로는 "작업을 자동으로 수행하기 위해 하나의 명령으로 그룹화된 일련의 명령과 지시"입니다.

비디오 게임의 맥락에서, 이는 하나의 버튼을 동시에 또는 순서대로 여러 작업을 수행하도록 매핑하는 것이 될 수 있습니다.

매크로가 흔한 게임인 월드 오브 워크래프트(WoW)를 살펴봅시다.

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

WoW은 전투 중 사용할 능력과 액션을 다량 제공하는 것으로 유명합니다. 게임 속에서 여러 막대에 시전할 주문들이 넘치게 되면서 플레이어들 중 일부는 여러 가지 주문들로 가득 찬 액션 바를 가지게 되었습니다.

아래는 너무 많은 것들이 액션 바에 할당된 극단적인 예시의 스크린샷이지만, 요점은 전달됩니다.

![WoW screenshot](/assets/img/2024-05-23-Designingaccessiblevideogames_3.png)

매크로를 사용하면 단일 키가 한 번의 버튼 누름으로 여러 주문을 실행할 수 있도록 할 수 있습니다.

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

버튼을 10번 누르는 작업이 1번으로 줄어들 수 있어요.

게이밍에서 매크로의 주요 장점 중 하나는 개별 플레이어에게 맞춤형 솔루션을 제공할 수 있다는 점입니다.

예를 들어, 신체적 장애가 있는 게이머들은 복잡한 키 조합이나 마우스 이동을 실행하는 데 어려움을 겪을 수 있습니다. 매크로를 사용하면 신체적 스트레인을 줄일 수 있어 게이머가 더 오래 플레이하고 더 즐거운 경험을 할 수 있도록 도와줍니다.

게다가, 매크로 기능은 kognitive 어려움이나 처리 속도에 영향을 주는 상황에 있는 플레이어들의 반응 시간을 크게 향상시킬 수 있어요. 특정 작업들을 자동화함으로써 플레이어는 더 효율적으로 명령을 실행할 수 있어 게임의 속도에 맞춰 가는 데 도움을 줄 수 있어요.

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

이 향상된 반응 시간은 접근성을 높이는 데 훌륭하지만, 멀티플레이 경험에서 부당한 이점에 대한 명백한 우려를 제기합니다. 모든 게임에서 플레이어가 매크로를 사용하는 것이 현실적으로 가능하지는 않을 수 있지만, 고려해 볼 것을 권장합니다.

접근성에서 매크로의 역할에 관심이 있다면 Laura K Buzz의 이 비디오를 확인해보세요.

# 대체 입력 장치 및 키 할당 재설정

일반적인 컨트롤러를 대체 입력 장치로 교체하거나 키 할당을 재설정하는 것이 게임을 접근성 있게 만드는 중요한 부분입니다.

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

활동 제약이 있는 플레이어들에게는 적응형 컨트롤러, 스위치, 또는 동작 센서와 같은 대체 입력 장치를 연결할 수 있는 능력이 플레이 여부를 결정할 수도 있어요.

발판부터 QuadStick와 같은 멋진 기술들이 많이 있어요. QuadStick은 입으로 작동하는 비디오 게임 컨트롤러입니다.

게다가 여러 입력 장치를 사용할 수 있는 옵션은 맞춤형이고 유동적인 게이밍 경험을 촉진해요. 플레이어들은 자신의 편안함과 능력에 가장 잘 맞는 장치 조합을 선택할 수 있어서 자기 주도성과 포용성을 유발해요. 이는 장애가 있는 개인들의 접근성을 향상시키는 뿐만 아니라 다양한 게임 취향을 가진 광범위한 관객들에게도 맞춤화됩니다.

모든 종류의 대체 컨트롤러와 설정을 사용하는 플레이어들로 테스트하는 것이 중요해요. 모든 것이 올바르게 작동하는지 확인하기 위해요.

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

플레이어들이 자신의 기기를 사용할 수 있는 것 외에도, 제어를 적절하게 다시 매핑할 수 있어야 합니다.

키 바인딩 다시 매핑을 통해 개인들은 편리하고 관리하기 쉬운 키나 버튼에 명령을 할당할 수 있습니다. 예를 들어, 플레이어는 "점프"를 발판으로 또는 움직임 제어를 별도의 조이스틱으로 다시 매핑해야 할 수도 있습니다.

다시 매핑은 모든 플레이어에게 혜택을 주는데, 왜냐하면 각자가 선호하고 익숙한 제어 방식이 있기 때문입니다. 특히 PC에서 게임을 열 때마다, 처음으로 하는 일 중 하나는 설정에 들어가서 쉽게 접근할 수 없는 키 바인딩을 다시 매핑하는 것입니다.

이제 모든 이 키 다시 매핑과 사용자 정의 설정을 고려해주시고, 플레이어의 설정이 세션 간에 유지되도록 해주세요. 누군가가 게임에 들어가서 자신의 플레이 스타일과 고유한 요구를 맞추기 위해 설정을 완벽하게 조정하는 데 한 두 시간을 소비한다면, 돌아왔을 때 모든 게 완벽하게 유지되어야 합니다. 아무도 그 모든 작업을 다시 해야할 필요가 없습니다.

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

# 아직 나가지 마세요

제가 다룬 내용은 게임 접근성을 위한 초반 작업에 불과해요.

이 글에서는 적절한 자막 및 캡션 추가, 색맹을 위한 디자인, 컨트롤 감도 조절 등을 다루지 않았습니다.

아래 링크된 멋진 자료들을 확인해보는 것을 강력히 권유합니다. 게다가 AbleGamer의 APX (Accessible Player Experiences) 인증 실무자가 되기 위한 2일 과정도 적극 추천해요. (저는 어떠한 방식으로도 후원받고 있지 않아요. 진심으로 이 과정을 좋아했습니다.)

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

## 더 읽을거리

- 접근성은 쉽지 않아요: 게임을 모두에게 알맞게 만드는 것에 대해 '쉬운 모드' 논의에서 빠뜨리는 것 →
  "난이도가 높은 게임을 더 접근성 있게 만드는 것은 난이도 수준을 넘어서 하는 일입니다."
- 접근 가능한 플레이어 경험 (APX) 패턴 →
  독특하고 플레이어가 자신의 Bedz어에 맞게 조절할 수 있는 플레이 경험이 확보되도록 하는 도전과 접근 패턴 목록. 정말 확인해보세요.
- 접근 가능한 비디오 게임 디자인 →
  이 기사를 좋아하신다면, 다른 기사도 마음에 드실 거에요.
- BBC 자막 가이드 라인 →
  여러분 모두에게 자막을 읽을 수 있고 접근할 수 있게 만드는 방법을 배워보세요.
- 게임 접근성 지침 →
  게임을 접근성 있게 디자인하는 데 가장 필요한 도구/자료 중 하나에요.
- 게임 제작자의 도구킷(TK) - 비디오 게임 접근성 재생 목록 →
  게임 개발자라면 꼭 봐야 할 GA 재생 목록으로, 접근성이 있는 비디오 게임을 디자인하는 방법이 가득 담겨 있어요. 이 동영상은 꼭 시청해야 할 필수영상입니다.

여러분들의 생각이나 경험이 있으면, 알려주세요! 이 게시물에 응답하거나 LinkedIn에서 알려주세요. UX나 비디오 게임 관련해서 어떤 주제라도 얘기 나누는 것을 즐깁니다.

✨ 누군가에게 UX 디자이너가 필요하다면, 저에게 연락해주세요!
