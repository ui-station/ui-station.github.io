---
title: "Godot을 평가하며"
description: ""
coverImage: "/assets/img/2024-05-18-OnEvaluatingGodot_0.png"
date: 2024-05-18 16:02
ogImage:
  url: /assets/img/2024-05-18-OnEvaluatingGodot_0.png
tag: Tech
originalTitle: "On Evaluating Godot"
link: "https://medium.com/@caseyyano/on-evaluating-godot-b35ea86e8cf4"
---

메가 크리트에서 진행한 내부 게임잼을 3주 동안 마무리했어요. 이번 게임잼은 Godot 엔진을 평가하기 위한 것이었어요.

# 엔진 변경의 이유는?

미래에서 오신 분들을 위해, 2023년 9월 12일 유니티가 런타임 요금을 발표했고 10일 후에 일부 요금을 철회했어요. 그 사이 기간 동안 혼란이 생겼는데, 이미 출시된 게임에 이 요금이 적용되는지 여부가 불분명했고, 런타임 요금의 정의가 모호했으며, 이 요금이 어떻게 감지되는지에 대한 우려가 있었고, 그들은 github 이용 약관(TOS) 페이지를 폐지했어요.

유니티는 나중에 이 github 페이지의 트래픽이 충분치 않아 제거한다고 밝히면서, 이 방법으로 TOS를 투명하게 유지한다는 것이 충분한 관심을 끌지 못했다고 말했어요. ???

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

![이미지](/assets/img/2024-05-18-OnEvaluatingGodot_0.png)

유니티는 불필요하게 이상한 후속 진술과 소극적인 설명을 더욱 깊게 파고들었고, 그들의 직원들은 다른 사람들처럼 혼란스러워하며 모두를 진정시키려고 최선을 다하고 있었습니다. 그것은 대재앙이었습니다.

초기 문제에 대해 더 읽어보거나 여기에 이어지는 내용을 확인할 수 있습니다.

유니티가 한 일은 정말 어리석은 일이었어요. 저는 그 엔진을 별로 좋아하지도 않아요. IPO 이후에 그들이 수수료를 부과하고 그들의 버그가 더 심해진 컴포넌트를 고치지 않는 이 "커뮤니티를 위한" 엔진이라고 말하면서 나는 매우 독립적인 인디 게임 개발자인데 말이죠.

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

메가 크릿 팀과 저는 약 일주일간 고돗을 평가한 후 어떠한 함정이 있는지 살펴보기 위해 두 주 동안 게임을 만드는 것이 가장 좋을 것으로 결정했습니다. "고돗으로 그런 건 안 된다"라든지 아키텍처적인 함정과 같은 것들이죠. 만약 창조자가 ECS에 대해 과도하게 열정적이라면 누가 알겠습니까?

# 그런데 왜 Godot일까요?

게임 엔진을 선택할 때 제가 고려하는 몇 가지 요구 사항이 있습니다. 가장 먼저 떠오르는 것들은 다음과 같습니다:

- 라이브러리 이상: SDL과 LWJGL에서 일한 경험이 있으므로 좀 더 안내를 받고 싶습니다. 자원을 로드하고 해제하거나 글꼴 처리, 디스플레이 처리에 대한 몇 가지 내부 API가 있으면 좋겠습니다. 그것들을 작성하기 싫어요; 게임을 만들고 싶어요!
- 정적 유형 언어: 확실히 시각적 스크립팅, 드래그 앤 드롭만 가능한 편집기, 동적 스크립팅 같은 것은 싫어요. C++, C, #Objective-C, Java를 알고 있지만 Python과 JavaScript를 알긴 하지만 그저 제겐 지저분하게 느껴집니다. 이 언어들로 작성하는 코드가 아니라, 회사 내에서 모든 것을 정적으로 변수 유형을 지정할 수 있는 세상이 어디 있겠어요? 내 토끼가 어디에 똥을 누르는 것을 강요하는데도 적용할 수 없는데 말이죠.
- 커뮤니티: Godot은 열정적인 대안으로 느껴집니다. 인기가 많다는 것은 더 일반적인 문제가 해결되고 토론되는 것을 의미합니다. 난 똑머니 없어요. 빠르게 문제를 해결할 수 없다면 무언가를 비틀어서 넣고 넘어가는데, 여전히 프로젝트에 대해 고품질로 느껴지는 솔루션을 찾고 구현하는 것을 선호할 거예요. 저희는 소규모로 운영되기 때문에 휠을 재발명할 필요가 없어요. 제가 직접 체적 구름을 구현하려 하겠냐구요? 저도 싫어요.
- 포팅: 나만의 포팅을 하고 싶어요! 'Slay the Spire'에는 LibGDX를 선택한 이유가 PC, Mac 및 Linux에서 실행할 수 있었기 때문이죠. 네, JavaVM에서 실행되며 여러 문제가 있지만 한 번 작성하고 어디서나 실행할 수 있는 거 아니겠어요? 아니요. 콘솔에서는 돌아가지 않고 Mac과 Windows 업데이트가 계속 문제가 되죠. 어쨌든, 포팅은 어려운 일이지만 Godot은 W4Games가 포팅 도구를 개발 중이고, 다른 회사들도 콘솔용으로 포팅을 진행 중이죠! 아주 멋지죠. 스위치용 Brotato와 Cassette Beasts 👀!?

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

게임 엔진을 선택하는 또 다른 이유도 있습니다. 일반 엔진 속도, 업데이트 정기성, 사용하기 즐거운 인터페이스, 소유주/설립자가 John Riccitiello-itis의 징후를 보이는지 여부 등이 있습니다.

그래서 Godot은 이러한 요구 사항을 충족시켰고, C#도 지원한다고 합니다. 멋져요. 게임을 만들어 봅시다.

# 댄싱 듀얼리스트(Dancing Duelists)는 무엇인가요?

음, 바로 앞에서 이야기한 젬 게임입니다! 코어 프레임워크에 너무 많은 요리사가 몰리는 것을 원치 않아서 젬에 추가적으로 한 주를 더 투자하여 우리의 프로그래머 한 명이 "큰 포트" 작업을 시작하게 되었습니다.

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

![Game content pipeline](/assets/img/2024-05-18-OnEvaluatingGodot_1.png)

So we worked on a deckbuilding autobattler because it’s familiar and it has a lot of bits and bobs which tend to be the types of games that we like making (content-heavy, deterministic).

# Game Content Pipeline

I think unlike most companies, we’re a design-first company AND both of our game designers (myself and Anthony) are technically proficient so we’re able to circumvent the making of several tools.

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

유니티에서 작업할 때도 우리는 Prefab을 콘텐츠로 만들거나 콘텐츠 데이터를 저장하는 수단으로 Scriptable Objects를 사용하는 것에 반대했습니다. 왜냐하면 우리는 IDE를 사용하는 것이 훨씬 빠르다고 생각하기 때문이죠. 우리는 코드에서 정의/매개변수를 찾아가거나 강력한 검색 기능을 사용하며 엔진이 코드 라인을 가리킴으로써 버그를 해결하는 것이 더 쉽다고 생각합니다.

이 콘텐츠-코드 아키텍처는 Slay the Spire와 같은 게임에서 사용하는 방식이며, 카드 게임을 만들어 보았을 때 Godot에서도 완벽하게 작동했습니다.

프로그래밍에 대해 두려워하는 비전공 디자이너라면 아래 내용을 살펴보세요. 우리가 Backflip이라는 카드를 구현하는 방법입니다. 저는 이 방법이 인스펙터나 특별한 데이터 형식을 사용하는 것보다 더 효율적이라고 생각합니다. 하지만 개인의 선호에 따라 다를 수 있습니다.

```js
public sealed class Backflip : CardModel
{
    public override string Title => "Backflip";
    public override string Description => "Deal 2 damage.\nGain 2 HP.";
    protected override string PortraitPath => "backflip.png";

    public override async Task OnPlay(FighterClashState owner, FighterClashState target)
    {
        await FighterCmd.Damage(target, 2, owner, this);
        await FighterCmd.GainHp(owner, 2, owner);
    }
}
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

게임 콘텐츠를 실행하기 전에 화면 간 로직, UI, 애니메이션, VFX, 오디오, 폰트 등을 위한 프레임워크를 설정해야 했습니다. 이것은 대부분의 시간을 잠에서 차지했어요. 대부분의 엔진들에서는 매니저, 팩토리 설정, 게임 내에서 경합 조건을 방지하는 최적의 방법이 있기 때문에 게임 콘텐츠를 통합하고 문제 해결하는 과정을 가능한 즐겁게 만들어야 합니다.

# 화면 및 객체

화면을 로드하고 언로드하는 것을 싫어해요. 불필요하게 느리며, 이해하기 어려운 방식으로 "정리"되며, 서로 다른 데이터를 전달하기 위한 흥미로운 방법을 고민해야 합니다. 2D 게임처럼 작은 것을 로드하고 언로드해야 할 경우, 소수의 에셋을 언로드하고 새로운 것을 로드할 수 있어요. 자신감이 있다면 미리 비동기로 로드할 수도 있어요. Unity와 Godot은 여기에서 유연하며, 부모/자식 관계로 객체를 시각화하고 씬 다시로드를 사용하지 않도록 할 수 있어요. 현대적인 게임 엔진의 장점 중 하나에요. 때로는 객체를 놓고 가끔 씬 트리의 시각화 없이 빼먹을 때도 있어요. 그리고 Unity에는 씬과 프리팹이 있어요.

Godot에서는 씬과 프리팹이 하나로 통합되어 tscn 형식으로 되어 있고 git과 호환되어요. 정말 대단하죠. 우리를 괴롭히는 Unity YAML이 있다는 것을 생각하게 했고, perforce가 실제로 유용한지에 대해 고민하게 했죠 (사실은 아니에요). 지옥 같은 프리팹 병합의 날들은 사라졌어요. 하지만 씬에서 많은 변경 사항이 있는 경우 약간의 문제가 생길 수 있어요. 그래도 큰 문제는 없어요.

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

이 장면 내의 객체를 노드라고 합니다. 따라서 GameObject은 노드입니다. 많은 컴포넌트를 연결할 수는 없지만 대부분의 요구 사항을 처리해 주는 노드의 확장이 많이 있습니다. 그렇지 않다면, 기본 노드 클래스 위에 자체적으로 작성해야 합니다. 나는 심중지기적이어서 이것은 괜찮습니다. Unity의 프랑켄슈타인 구성 요소로 가득 찬 GameObject들은 항상 나를 불안하게 만들었습니다. 하루 종일 인스펙터를 스크롤해야 하는 것은 고통스럽습니다. 이것은 논란이 될 수도 있지만, 전체 모니터를 사용하여 비디오 게임을 만들고 싶고, 빠르고 아름답고 alt-tab을 그리 많이 누르기 싫습니다.

그럼에도 불구하고 때로는 노드의 다양한 노브를 조작해야 하지만, 각 노드의 제한된 매개변수들이 우리를 체크하도록 유지한다는 것을 발견했습니다.

# UI 레이아웃

노드에 관해서 말씀드리자면, 부모 노드로의 조합 및 다양한 지점에 고정시키는 것은 게임 UI를 배치하고 다양한 종횡비와 화면 크기에 대한 호환성을 향상시키는 현대적인 방법입니다. 자체 엔진을 개발하는 개발자들이 UI 작업을 싫어하는 이유가, 이러한 종류의 시스템을 구현하고 싶어하지 않기 때문입니다. 이것은 귀찮으며, 게임이 더 이상 이상한 종횡비를 지원할 때 아무것도 달성한 기분이 들지 않습니다.

Unity와 Godot은 회전 중심, 고정점 및 다양한 컨테이너가 있어서 항목을 목록이나 그리드로 구성하는 데 도움이 됩니다. 때로는 매우 혼란스러울 수 있습니다. 특정 변수가 잠겨있을 때는 매우 매우 명확해야 한다고 생각합니다. 그러나 그렇지 않습니다. 최악의 경우에는 "당신의 부모가 당신에게 화난 것이기 때문에 그렇게 할 수 없습니다."라는 메시지가 표시됩니다.

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

요기에는 학습 곡선이 있어서 Unity가 UI Toolkit을 통해 전체를 다시 설계하기로 결정했어요. 기본적으로 비디오 게임에서 UI를 만드는 것은 앞으로 영원히 재앙이 될 것입니다. 왜냐하면 UI를 만드는 것을 즐기는 사람은 우리와 같이 6명 정도밖에 없고 Unity를 이끄는 사람은 아마도 프론트엔드 웹 개발자일 가능성이 높아요. 이제 모든 인디 개발자들이 그들의 방식을 배워야 한다는 점에 대해 애도를 표합니다.

어쨌든, Godot과 Unity 모두 UI를 위한 특수 노드들이 좋지 않아요. 맞아요. 그것들은 항상 좋지 않았고 앞으로도 그대로일 겁니다. 게임에는 많은 복잡한 UI 문제가 있으며 일반적인 템플릿을 빌리면 게임이 엉망이 될 거예요. UI는 사용자 인터페이스의 줄임말입니다. 플레이어가 인터페이스(게임)와 상호 작용하는 방식을 의미합니다.

새로운 개발자들은 기본값을 사용할 거에요. 버튼이 색상을 거의 눈에 띄지 않게 변경했을 때 "아, 그건 게을러서 UI를 만드는 Unity 게임이구나." 마우스 커서가 실제로 텍스트의 벡터 모양에 닿아야만 글자 위로 커서를 올렸을 때 감지되는 경우? "아, 그건 플래시 게임이야." 2023년에 어떻게 플래시 게임을 하는 거죠?

그뿐만 아니라 Unity의 추악한 "Font" 구성 요소, 기본 버튼 및 UI 컨트롤에 대해 언급하기도 싫어요.

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

![2024-05-18-OnEvaluatingGodot_2](/assets/img/2024-05-18-OnEvaluatingGodot_2.png)

TextMeshPro는 정말 대단해요. 그것이 그립네요. 제발, 자동 크기 조정된 최소/최대 설정 글꼴 텍스트를 깔끔한 직사각형 가운데 정렬할 수 있게 해주세요. 우리는 예쁜 텍스트를 만들기 위해 직접 스크립트를 작성해야 했어요. 폰트와 텍스트에 대해 매우 신경 써요.

![2024-05-18-OnEvaluatingGodot_3](/assets/img/2024-05-18-OnEvaluatingGodot_3.png)

그래도, Godot에서 MSDF를 볼 수 있어 기쁘네요. 멋진 기술이죠! 모서리가 너무 날카롭고 크기를 잘 조절해요. 그들이 전혀 작동하지 않을 때가 제외하고요 😢

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

![이미지](/assets/img/2024-05-18-OnEvaluatingGodot_4.png)

# 실제로 C#을 지원합니까?

어떨 때는 C#에서 특정 API에 접근할 수 없을 수도 있습니다. 시장에서 오브젝트를 찾기 어려울 수도 있습니다. 코드를 컴파일할 때 항상 업데이트되거나 불안정할 수도 있습니다. 아마도 워크플로우가 좋지 않아서 IDE에서 Godot 편집기로 alt-tab을 누를 때마다 스크립트를 10초씩 컴파일해야 하는지도 모릅니다. 이러한 것들은 GDScript의 전도자가 C# 이야기를 할 때 제 무리한 두려움 중 일부였습니다.

위에서 언급한 것도 있고 다시 한 번 언급하겠습니다. 저는 정적 타입 언어를 좋아합니다. 이러한 허물없는 동적 타입 var 를 원하지 않으며, 이 부분을 아무도 변경할 수 없습니다. 명시적이고 명백한 방식을 좋아합니다. 관련성이 있다면, 우리는 CSV 대신 JSON을 사용합니다. 같은 원리입니다.

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

이제 C# 통합에 기분이 좋아지고 있습니다. 제가 선택한 IDE(JetBrains Rider)를 사용하는 설정이 쉬웠어요. 자동 완성이 잘 되고 IDE에서 게임을 기본적으로 실행할 수 있어 좋았어요. 게임을 Godot으로 전환하고 실행할 때 빠릅니다. 정말 빠르죠. Unity에서 항상 꿈에서도 괴로웠던 5~10초간의 "스크립트 컴파일 중..." 팝업도 사라졌습니다.

# 빠른가요? 네

스크립트 컴파일 하는 것이 없어지고 Godot은 Unity보다 훨씬 가볍습니다. 고급 그래픽이나 멋진 조명 기술을 다루려고 한다면 정말 대답을 드릴 수 없어요. 하지만 쉐이더, 입자 시스템, 재질 등을 다룰 수 있어요. 아마 가장 최신 기술은 아니겠지만... 전 밝은 장난감보다는... 독립 개발자니까요.

Godot은 작고 게임을 실행하고 매개 변수를 변경하고 다시 실행하는 등 디버그 도구를 계속 사용하지 않고 게임을 실행하는 느낌이 좋아요.

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

게임이 실행 중일 때 반영되는 노드들의 변화도 가능해요! 유니티를 배우면서 "와, 이거 너무 현대적이다." 라는 순간이 있었는데, Godot에서도 이것이 잘 동작하고 있다는 것을 보니 기뻤어요. Unity와 비교하면 몇 가지 제한 사항이 있지만 전반적으로 정말 멋진 기능들이에요.

속도에 관한 이야기로 돌아와서, 프로젝트를 열 때 더 빨라지고 작동에 어떤 바보같은 인증도 필요하지 않아요. 프로젝트를 실행하는 속도도 더 빨라졌어요. 스크립트 작업 흐름도 더 빠르고 빌드를 내보내는 것도 더 빨라요. 정말 빠르죠!

부록으로, 인터넷에서 raycast2d의 성능에 대한 불만이 있었어요. 많이 사용할 계획이라면 조금 관련 정보를 좀 더 찾아보길 권해요.

# TexturePacking/TextureAtlas/SpriteAtlas

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

좀 아쉽네요. 그게 전부에요. 적어도 TexturePacker로 생성된 파일을 읽을 수 있어서 다행이에요(세 번째 자료 압축 도구의 이름). 약간 짜즯다고 생각하시겠지만요. 하지만 유니티는 매번 게임을 실행할 때마다 패킹을 해야 한다니까, 실은 그것도 바보 같아요. 아마 어디든 이 작업 흐름이 이렇게 훌륭하지 않은 것일지도 몰라요?

NinePatch 텍스처는 TextureAtlases로 설정하는 게 좀 귀찮아요. 그렇게 복잡한 문제는 아닌 것 같아서, 아마 기능 요청을 제출해볼까 합니다.

# Dancing Duelists 디자인 미스

그럼, Dancing Duelists로 돌아가서요. 프로젝트 범위를 줄이고 제한을 도입하여 시간 제약으로 완전히 탐구하지 못한 게임 디자인 영역 및 제한 사항을 살펴보려고 생각했습니다.

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

- 덱이 너무 무작위로 설정됩니다: 초기 디자인은 플레이어가 원하는 순서대로 덱을 설정하여 플레이하도록 허용했습니다. 덱은 맨 위부터 자동으로 플레이됩니다. 이는 진정성 있는 사용자 경험을 만들기 위해 심각한 작업이 필요하며 균형을 맞춰야 할 많은 복잡성을 도입합니다. 우리는 게임을 빠르게 플레이테스트할 수 있는 것으로 기대하지 않았기 때문에 문제가 될 수 있는 콘텐츠를 반복적으로 플레이테스트 세션을 실행하여 개선할 수 없었습니다. 이러한 무작위성은 점수판을 더 간단하게 유지합니다.
- 턴 순서 문제: 누가 먼저 움직일까요? 동점이면 어떻게 할까요? 멀티플레이라면 누가 먼저 가야 할까요? 속도 스텟? 동전 던지기? 당신이 먼저 가고 그들이 먼저 가는 것은 어떨까요? 솔직히 이것은 자동전투게임에서 공정성을 위해 해결해야 할 복잡한 문제입니다. 따라서 우리는 항상 플레이어가 먼저 가는 PvE 게임을 선택했습니다. 위에서 언급한 것처럼, 이는 것을 간단하게 만들지만 게임이 얇아지게 됩니다.
- 비슷한 빌드: 이상적으로는 각 전투용 파이터마다 맞춤식 카드 풀을 만들어서 2-4가지 일반 아키타입을 향해 빌드할 수 있도록 할 것입니다. 우리는 멀티풀, 공유 풀, 그리고 아마도 보석 시스템에도 비슷한 것을 사용할 수 있게 만들 것입니다. 이렇게 많은 콘텐츠를 만들고 균형을 맞추고, 이러한 아키타입을 탐험하는 것은 거대한 작업일 것입니다. 죄송하지만, 이번 점수판에는 이번 점프를 위한 여러 카드가 들어 있습니다.

더 많은 제약 사항이 있지만, 이러한 절하로 인해 짧은 시간 안에 게임을 출시할 수 있었던 것 같습니다. 게임이 전체 잠재력을 발휘하지 못하는 것은 안타깝지만, 게임 엔진을 탐색하기 위해 실행한 점프에 너무 감정적으로 연연하지 않는 것이 좋습니다.

# 멀티플레이어?! 아쉽게도

자동전투게임이 일반적으로 멀티플레이어 여부에 대해 좀 많이 고민했습니다. 그러나 몇 가지 디자인 문제를 해결하지 못했기 때문에 결국 그것에 대해 진전할 수 없었습니다. 이는 게임을 플레이하고 덱을 저장한 다음 이러한 덱과 대결해야 한다는 것을 의미했습니다.

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

플레이 테스트에서 이 데이터를 얻는 것은 예상보다 순조롭지 않아서 일부 자동화를 구축했어요. 아마도 "머신 런어지?", "AI?", "신경망 뭐라뭐라?"라고 묻고 계실지도 모르겠네요. 대답은 정말이지 그렇지 않아요. 우리가 사용한 건 무차별 공격이었어요. 이게 괜찮았던 이유는 게임이 "턴 순서 문제"로 비대칭이며, 각 전투 시작 시 덱이 무작위로 섞이는 것이 많은 변화를 준다는 것이죠.

우리는 Godot이 빠르게 작동한다고 말했지만, 그에 비해 용량을 적게 차지한다는 것도 중요합니다.

그래서 우리는 무차별 공격으로 카드를 선택하게 하고, 그것들을 우리가 만든 작은 플레이어 덱과 싸우게 했어요. 그들이 이기면 해당 덱을 저장하고 게임에 다시 통합시켰죠. 우리는 각 라운드 당 전투별 대략 20개의 고유한 덱을 원했고, 결국 약 1,200개의 고유한 상대를 만들어냈어요. 이들은 플레이어가 만든 덱을 이기는 최소 강도 요구 사항을 충족합니다. 말이 많아 보일 수 있지만, 파일 크기는 2MB에 불과해요. 이런 덱들은 JSON 파일로 저장돼요. 여기 예시 파일이 있습니다: jazzy_jasper_R2_1697423308.json

```js
{
 “name”: “player”,
 “round”: 2,
 “strength”: 3,
 “autogenerated”: true,
 “character”: “CHARACTER_MODEL.JAZZY_PACIFIST”,
 “trinkets”: [
 “TRINKET_MODEL.CIRCULAR_BREATHING”,
 “TRINKET_MODEL.GOTHIC_WARDROBE”
 ],
 “cards”: [
 “CARD_MODEL.GROOVE”,
 “CARD_MODEL.GROOVE”,
 “CARD_MODEL.GROOVE”,
 “CARD_MODEL.SMOOTH_SOLO”,
 “CARD_MODEL.POLYRHYTHM”,
 “CARD_MODEL.HEADSHOT”,
 “CARD_MODEL.LEG_DAY”,
 “CARD_MODEL.ASTEROID”,
 “CARD_MODEL.FIRE_BLAST”,
 “CARD_MODEL.MOONWALK”,
 “CARD_MODEL.ALACRITY”
 ]
}
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

최종적으로 우리는 현재 전투사와 승리할 때마다 난이도가 증가하는 것을 도입하기 위해 의도된 강도 변수를 활용하지 않았습니다.

# 마무리

해냈어요! 딱 3주 만에 여러분이 지금 이곳에서 다운로드하고 플레이할 수 있는 실제 비디오 게임을 만들었어요: https://megacrit.itch.io/dancing-duelists

![이미지](/assets/img/2024-05-18-OnEvaluatingGodot_5.png)

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

입력, SDK 통합, 종횡비 처리, 로컬라이제이션, 하드웨어 호환성, 써드 파티 플러그인(Spine2D와 FMOD을 사용합니다), 그리고 수정 가능성과 같은 더 많은 주제가 더 있지만, 좋은 비디오 게임을 만드는 것은 단거리 경주가 아니라 장거리 마라톤이에요. 그럼, 여기서 마무리 지을게요. 전 하느님(Godot 전문가)은 아니지만, 진짜 2D 비디오 게임을 만들기 위한 도구는 있는 것 같아요.
