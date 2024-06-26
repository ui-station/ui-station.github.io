---
title: "기본 이동 플랫폼 구현 방법 Godot 4 C"
description: ""
coverImage: "/assets/img/2024-06-23-ImplementingabasicmovingplatformGodot4C_0.png"
date: 2024-06-23 22:15
ogImage:
  url: /assets/img/2024-06-23-ImplementingabasicmovingplatformGodot4C_0.png
tag: Tech
originalTitle: "Implementing a basic moving platform (Godot 4 C#)"
link: "https://medium.com/codex/implementing-a-basic-moving-platform-godot-4-c-30e072619b16"
---

비디오 게임은 캐릭터, 아바타 및 NPC에 관한 것이지만, 환경, 풍경 및 장식에서 멋진 동적 객체에 관한 것이기도 합니다! 전형적으로, 모험/플랫포머 게임을 만들 때 언젠가는 반드시 애니메이션된 플랫폼을 만들어야 할 것입니다.

그래서 오늘은 간단한 이동 플랫폼을 Godot 4/C#에서 어떻게 만드는지 살펴보겠습니다!

이 문서를 끝까지 읽으면 경로(Path) 및 PathFollow 노드를 사용하여 플랫폼 객체가 특정 경로를 따라 이동하도록 만드는 방법, 이 플랫폼이 루프되거나 반대 방향으로 이동하는 방법, 마지막으로 플랫폼의 충돌기(collider)가 시각적으로 제대로 따라갈 수 있도록 하는 방법을 알게 될 것입니다.

물론, 이 자습서에서는 3D 예시를 살펴보겠지만, 우리가 공부할 노드들의 2D 버전에서도 동일하게 작동합니다 :)

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

물론, 이 예시에 대한 데모 씬과 모든 에셋은 제 Github에서 확인할 수 있다 🚀 그리고 내 다른 Godot 튜토리얼들이 모두 있어!

🔶 더불어, 이 에셋들은 Kenney의 놀라운 무료 라이브러리에서 제공된 것이다!

이 튜토리얼은 비디오로도 제공되며, 텍스트 버전은 아래에 있다:

이렇게 말씀드렸으니, 이제 Godot 4와 C#에서 움직이는 플랫폼을 설정하는 방법을 알아보자!

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

어, 그리고 그리고: 여전히 Godot 4와 C# 게임 개발에 조금 낯설다면, 내 새로운 "짧은 글" eBook인 L’Almanach: 시작하기를 확인해보세요!

![이미지](https://miro.medium.com/v2/resize:fit:720/0*8zhDhZgu2yB4wJzv.gif)

이 빠르고 실용적인 안내서는 기초를 탐험하게 해주며, 시선을 설치하는 방법을 가르쳐줄 것입니다. C#로 코딩하고, UI를 설계하고, 심지어 당신만의 3목 게임을 한 걸음씩 만드는 방법까지 :)

따라서 100페이지 미만의 분량으로 Godot 4/C# 여정을 시작하고, 저렴한 가격으로 즐길 수 있다면, Gumroad 페이지를 꼭 확인해보세요...

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

# 경로 설정하기

알겠어요, 먼저 플랫폼을 경로를 따라 쉽게 이동시킬 수 있는 방법을 살펴봅시다.

Godot에서 이러한 유형의 경우에 매우 유용한 노드 유형이 실제로 몇 가지 있습니다: 경로(Path) 및 경로 따르기(PathFollow) 노드. 이전 시리즈 튜토리얼 중 2D 예제에서, 적들의 웨이브를 규칙적으로 생성하고 미리 정의된 경로를 따라 이동하는 방법을 살펴볼 때에 이에 대해 이야기했습니다:

오늘은 비슷한 작업을 3D로 수행하되, 객체의 이동을 좀 더 사용자 정의하고자 합니다. 그리고 아, 케니의 놀라운 무료 라이브러리에서 에셋을 사용할건데, 그것은 그의 새로운 미니 던전 키트입니다:

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

이 튜토리얼에서는 이미 데모 씬과 사용할 플랫폼 프리팹을 준비해 두었습니다. 이렇게 생겼죠:

그림에서 볼 수 있듯이, CollisionShape3D 하위 노드를 가진 간단한 물리 바디 노드와 시각적인 부분을 위한 몇 가지 메쉬가 포함되어 있습니다.

그러나 환경 객체로 Godot에서 예상하는 StaticBody3D가 루트 노드가 아닙니다. 대신, 레벨에서 개체가 이동하고 여전히 물리 객체의 나머지 부분과 충돌하도록 원하는 경우 훌륭한 노드 유형을 사용할 수 있습니다: AnimatableBody3D.

기본적으로 이 노드는 다양한 방식으로 이동할 수 있습니다(예: 코드를 통해, 여기와 같은 경로로, 애니메이션을 사용하거나 심지어 트윈 객체를 사용하여…), 그러나 이동과 관련하여 Sync To Physics 옵션을 활성화하면 Godot에게 이 동작을 씬의 다른 물리 객체와 동기화하라고 지시하여 여전히 충돌이나 트리거를 얻을 수 있습니다 :)

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

그래서 이제, 플랫폼과 데모 씬이 이미 준비되어 있으니, 여기서 첫 번째 단계는 Path3D 노드 내부에 플랫폼이 따라갈 경로를 그리는 것입니다. 물론 이번에는 3D에 있기 때문에 세 가지 방향으로 포인트를 배치하고 수평이 아닌 경로를 만들 수 있습니다:

이제 경로를 그렸다면, 그 다음 단계는 PathFollow3D 자식 노드를 추가하고 플랫폼을 생성하는 것뿐입니다:

이후에는 PathFollow3D 노드의 Progress 또는 ProgressRatio 매개변수를 사용해 이동 경로를 따라 플랫폼이 움직이는 것을 볼 수 있습니다:

그런데 PathFollow3D 노드의 회전 모드를 회전 없음(None)으로 변경해야 합니다. 이동 중에 플랫폼이 회전하지 않게 하려면 Loop 옵션도 해제해야 합니다. 이렇게 하면 플랫폼의 움직임을 더 잘 제어할 수 있습니다(나중에 스크립트에서 처리할 것입니다):

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

물론, 코드를 통해 진행 상황 업데이트를 할 수 있다는 것도 알고 계실 거라 생각해요. 그래서 우리 PathFollow3D 노드에 새로운 C# 스크립트인 MovingPlatform.cs를 간단히 만들어봅시다.

여기서 우리는 아직 \_Process() 함수만 남길 거에요. 이 함수 안에서 오브젝트를 움직이도록 진행 값을 수정할 거랍니다.

```js
using Godot;

public partial class MovingPlatform : PathFollow3D
{
    public override void _Process(double delta)
    {}
}
```

일반적으로, 플랫폼에 대한 \_speed 값을 정의하고 내보내면, 그 값을 \_Process() 메서드에서 사용하여 ProgressRatio 값을 증가시킬 수 있어요. 이렇게 하면 경로의 끝에 도달하기 전까지 플랫폼 오브젝트를 이동시킬 수 있습니다. 우리는 간단히 정규화된 ProgressRatio 값이 1과 같은지를 확인하여 경로의 끝을 확인할 수 있어요.

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

```js
public partial class MovingPlatform : PathFollow3D
{
    [Export] private float _speed;

    public override void _Process(double delta)
    {
        if (ProgressRatio < 1)
            ProgressRatio += _speed * (float)delta;
    }
}
```

프로젝트를 다시 빌드한 후에는 인스펙터에서 원하는 대로 속도 값을 조정할 수 있습니다:

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

중요한 참고사항: 이 속도를 조정해야 할 필요가 있고, 튜토리얼을 진행하면서 우리의 플랫폼이 어떻게 움직이는지 수정하기도 할 수 있어요... 꼭 테스트하여 어떻게 보이는지 확인해 보세요 ;)

오케이 - 이 시점에서, 우리는 멋지게 보이고 우리가 기대한대로 경로를 따라 움직이는 멋진 움직이는 플랫폼을 가지고 있어요:

그러나 두 가지 큰 문제가 있어요:

- 첫째, 현재 구현에서는 플랫폼이 여정을 한 번만 수행하고 나서 끝에서 멈춰버려요 - 우리의 작은 데모처럼 어드벤처/플랫포머 게임에 이상적이지 않다는 것은 분명해요.
- 둘째, 실제로 플랫폼에는 어떤 충돌도 없어요! 그래서 내 장면에 플레이어 개체를 추가하고 플랫폼이 절벽 가까이로 이동할 때 플랫폼으로 이동하면 그냥 통과해 버리겠네요...

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

그럼 두 가지 문제를 해결하는 방법을 살펴보겠습니다.

# 좀 더 개선된 플랫폼 이동을 준비하기

## 기능 개요

우선, "원샷" 이동 문제를 해결해보겠습니다. 여기에 설정하고 싶은 것은 게임 디자이너 사용자들에게 몇 가지 옵션을 제공하는 시스템입니다:

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

- 먼저, 플랫폼은 우리의 경로 상 각 지점에서 잠시 멈출 수 있고, 중간 웨이포인트로 사용할 수 있습니다. 이것은 실제로 비디오 게임에서 매우 일반적인 기능이며, 전반적인 경로 동안 플랫폼에 뛰어오르고 내리기가 쉬워집니다.
- 두 번째, 플랫폼이 경로의 끝에 도달하면 즉시 처음으로 순식간에 이동하거나(루프 옵션을 활성화한 것처럼), 같은 방식으로 경로를 따라 반대 방향으로 이동할 수 있습니다. 나는 사용자가 이 두 모드 사이를 쉽게 전환할 수 있도록 하고 싶어요.

이 모든 것을 위해, C# 스크립트를 개선하여 "웨이포인트" 개념을 이해하고 현재 이동 방향을 추적할 수 있도록 해야합니다.

그러나 먼저, 우리의 클래스에서 두 가지 새로운 내보낸 변수를 간단히 정의할 수 있습니다: \_pauseTimeAtWaypoints 와 \_jumpToStart:

```js
public partial class MovingPlatform : PathFollow3D
{
    [Export] private float _speed;
    [Export] private float _pauseTimeAtWaypoints = 0;
    [Export] private bool _jumpToStart = false;

    public override void _Process(double delta) { ... }
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

첫 번째 변수인 (\_pauseTimeAtWaypoints)은 부동 소수점입니다. 각 웨이포인트에서 기다릴 시간 (초)을 나타냅니다. 값이 0이면 현재 동작과 똑같이 동작하며, 플랫폼은 중간 웨이포인트를 무시합니다. 값이 0이 아니면, 각 웨이포인트에서 속도를 줄이고 잠시 멈춘 뒤 다음 위치에 도착하기 위해 다시 가속합니다.

두 번째 변수인 (\_jumpToStart)은 부울 값입니다. 값이 true이면 플랫폼은 끝에 도달하면 시작 지점으로 순간이동합니다. false이면 반대 방향으로 경로를 따라 이동하여 다시 시작점으로 되돌아갑니다.

이를 염두에 두고 이제 웨이포인트별 메커니즘을 추가하는 방법을 살펴보겠습니다.

## 웨이포인트 시스템 뒷받침하는 이론

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

여기서의 아이디어는 곡선 내의 다른 점들의 위치와 현재 웨이포인트 인덱스를 저장하여 플랫폼이 따라 가야 할 경로의 일부를 알아내는 것입니다.

핵심은 이 중간 지점들의 정규화된 위치를 사용하는 것입니다. 이렇게 하면 경로의 시작점(0)부터 끝점(1)까지의 값들을 갖게 됩니다:

그런 다음, 플랫폼의 현재 웨이포인트 인덱스에 따라, 어떤 세그먼트를 고려해야 하는지 알 수 있습니다. 그리고 첫 번째 "from" 지점과 이 하위 경로의 두 번째 "to" 지점 사이에 부동 소수점 값을 보간할 수 있을 것입니다:

이 보간은 선형일 수도 있고, 각 웨이포인트에서 멈추고 더 자연스러운 느낌을 원한다면 속도를 높이거나 감소시킬 수도 있습니다:

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

우리 플랫폼 이동을 매우 맞춤화할 수 있게 만들고, 동시에 디자이너들에게 정의하기 쉽게 만들 것입니다. 디자이너들은 중간 정거장을 만들기 위해 경로에 추가 점을 생성하기만 하면 될 거예요.

정말 중요한 부분은 플랫폼의 속도를 올바르게 결정하여 경로 상에서 일정한 것으로 유지하는 것입니다. 실제로, Godot PathFollow 노드의 ProgressRatio를 사용할 때 경로를 이동하는 데 필요한 상대 속도가 달라지는 문제가 발생할 수 있어요.

이 문제를 피하기 위해서, 우리는 기본 물리학 공식을 사용하고, 예상 여행 시간을 기반으로 보간을 진행하면 됩니다.

아마도 알고 있겠지만, 물체의 속도 S는 이동한 거리 D를 이동하는 데 걸린 시간 T로 나눈 것으로 계산될 수 있어요:

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

아래와 같이 테이블 태그를 Markdown 형식으로 변경해주세요.

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

- 이미 플랫폼 속도를 \_speed 변수로 검사자에 노출했기 때문에 사용자가 정의한 데이터입니다.
- 그리고 우리의 waypoint 비율 및 전체 곡선 길이 L을 기반으로 이동해야 하는 거리를 쉽게 식별할 수 있습니다:

![이미지](/assets/img/2024-06-23-ImplementingabasicmovingplatformGodot4C_2.png)

그 말은 우리는 이 시간 양 T를 카운트다운하는 카운터를 쉽게 설정할 수 있으며, 그 후 플랫폼의 위치를 우리의 곡선 부분 청크를 따라 선형 보간하여 처음 waypoint에 카운트다운이 시작될 때, 그리고 시간이 경과하여 두 번째 waypoint에서 위치하도록 할 수 있다는 것을 의미합니다.

좋아요 — 많은 이론이었죠! 그래서 실습으로 이 모든 것을 정리해보는 시간이군요 :)

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

# Waypoint 메커니즘 구현

우리의 스크립트로 돌아와서, 이 waypoint 메커니즘에 대한 몇 가지 추가 변수를 추가할 것입니다.

우리는 곡선의 총 길이, waypoint의 수, 현재 waypoint의 인덱스 및 waypoint 비율 목록을 추적해야 합니다. 이 비율은 부동 소수점(float)의 리스트로 저장됩니다.

```js
using Godot;
using System.Collections.Generic;

public partial class MovingPlatform : PathFollow3D
{
    // ...

    private float _totalCurveLength;
    private int _nWaypoints;
    private int _curWaypointIdx;
    private List<float> _waypointRatios;

    public override void _Process(double delta) { ... }
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

그럼, \_Ready() 함수에서 경로의 곡선 데이터를 가져올 거에요:

```js
public partial class MovingPlatform : PathFollow3D
{
    // ...

    public override void _Ready()
    {
        Curve3D c = GetParent<Path3D>().Curve;
    }
}
```

이렇게 하면 곡선의 포인트 수를 가져와 각 포인트를 순회하면서 그 비율을 얻고, 마지막으로 곡선의 총 길이를 저장할 수 있어요:

```js
public override void _Ready()
{
    Curve3D c = GetParent<Path3D>().Curve;
    _nWaypoints = c.PointCount;
    _waypointRatios = new();
    _totalCurveLength = c.GetBakedLength();
    float curLength = 0;
    for (int i = 0; i < _nWaypoints; i++) {
        if (i > 0) {
            float r1 = c.GetClosestOffset(c.GetPointPosition(i - 1));
            float r2 = c.GetClosestOffset(c.GetPointPosition(i));
            curLength += r2 - r1;
        }
        _waypointRatios.Add(curLength / _totalCurveLength);
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

주석: GetClosestOffset() 및 GetPointPosition() 메서드를 이와 같이 중첩하여 사용하면 경로가 직선으로 구성되지 않은 경우에도 올바른 웨이포인트 비율을 얻을 수 있습니다 ;)

이 초기화 단계의 끝에는 ProgressRatio와 웨이포인트 인덱스를 0으로 재설정하여 곡선의 시작 부분에서 플랫폼 이동을 시작하는 것도 잊지 말아요:

```js
public override void _Ready()
{
    Curve3D c = GetParent<Path3D>().Curve;
    _nWaypoints = c.PointCount;
    _waypointRatios = new();
    _totalCurveLength = c.GetBakedLength();
    float curLength = 0;
    for (int i = 0; i < _nWaypoints; i++) {
        if (i > 0) {
            float r1 = c.GetClosestOffset(c.GetPointPosition(i - 1));
            float r2 = c.GetClosestOffset(c.GetPointPosition(i));
            curLength += r2 - r1;
        }
        _waypointRatios.Add(curLength / _totalCurveLength);
    }

    ProgressRatio = 0;
    _curWaypointIdx = 0;
}
```

그런 다음, 스크립트에 새로운 비공개 메서드 \_StartMove()를 만들고 \_Ready() 함수에서 이 메서드를 호출합시다. 이 함수는 경로 따라 플랫폼이 웨이포인트에서 다음으로 이동하도록 하는 기능을할 것입니다.

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

```js
public partial class MovingPlatform : PathFollow3D
{
    // ...

    public override void _Ready()
    {
        // ...
        _StartMove();
    }

    private void _StartMove() {}
}
```

우리는 이 메서드를 비트 단위로 구현할 것이기 때문에, 지금은 간단하게 점수를 하나씩 증가시키는 것으로 갈꺼에요:

```js
private void _StartMove()
{
    _curWaypointIdx += 1;
}
```

이제 우리는 이야기했던 이동 시간 계산기와 위치 보간을 처리하는 데 필요한 모든 것을 설정할 것입니다. 우리는 스크립트에게 어떤 구간의 경로에서 작업 중인지, 이동 시간이 얼마인지, 그리고 카운트다운의 어디에 있는지 알려주어야 해요...

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

그래서, 이 모든 것은 다음 변수들이 필요합니다:

- 첫째, 마지막 인덱스의 포인트와 관련된 비율인 \_moveFrom 값(즉, 관심 있는 경로 하위 청크의 첫 번째 포인트).
- 둘째, 현재 인덱스의 포인트와 관련된 비율인 \_moveTo 값(즉, 관심 있는 경로 하위 청크의 마지막 포인트).
- 셋째, \_moveTime 값으로, 우리가 말한 대로, 두 비율 사이의 차이를 취하여 총 커브 길이로 곱한 후 속력으로 나누어 계산됩니다.
- 넷째, 우리가 0으로 초기화하고 \_Process() 메서드에서 \_moveTime 값에 도달할 때까지 증가시킬 \_moveDelay 값입니다. 그러면 실제 시간 카운터가 될 것입니다.
- 마지막으로, 업데이트해야 할 시간 계수인지 여부를 쉽게 파악하기 위해 \_moving 부울도 설정해 봅시다.

```js
public partial class MovingPlatform : PathFollow3D
{
    // ...

    private bool _moving;
    private float _moveFrom, _moveTo;
    private float _moveTime, _moveDelay;

    private void _StartMove()
    {
        _curWaypointIdx += 1;

        _moveFrom = _waypointRatios[_curWaypointIdx - 1];
        _moveTo = _waypointRatios[_curWaypointIdx];
        _moveTime = Mathf.Abs(_moveTo - _moveFrom) * _totalCurveLength / _speed;
        _moveDelay = 0;
        _moving = true;
    }
}
```

모든 준비가 되었으니, 이제 우리의 \_Process() 메서드로 돌아가서 현재 로직을 보간 시스템으로 교체해야 합니다.

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

먼저, 우리가 움직이지 않는다면, 거기서 일찍 반환할 수 있어요:

```js
public override void _Process(double delta)
{
    if (!_moving) return;
}
```

그렇지 않고 우리가 움직인다면, 두 가지 경우가 있어요:

- 만약 우리의 딜레이가 이동 시간보다 짧다면 — 이 경우에는 프레임 시간 델타를 더하여 카운터를 업데이트하고 플랫폼을 이동해야 해요
- 또는 우리가 이동 시간 임계값에 도달했다면, 이동을 멈춰야 하며, 선택적으로 웨이포인트에서 멈추고 곡선의 다음 부분으로 진행을 계속해야 해요

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

```js
public override void _Process(double delta)
{
    if (!_moving) return;

    if (_moveDelay < _moveTime) {
        _moveDelay += (float) delta;
        // move our platform!
    } else {
        // stop our movement, wait at waypoint, continue progress...
    }
}
```

이제 플랫폼을 이동시키기 위해 우리는 그것의 위치를 보간하여 정규화하고자 한다고 말했습니다. 이것은 Mathf.Lerp() 메서드 덕분에 쉽게 할 수 있습니다.

이 내장 함수는 보간할 "from" 및 "to" 값들을 가져와 마지막으로 우리가 t-값이라고 부르는 값을 취합니다. 이 세 번째 매개변수는 우리의 서브패스 위에서 얼마나 진행했는지를 결정하는 0에서 1까지의 값입니다.

간단히 시작하여 선형 이동을 만들기 위해 이 t-값을 \_moveDelay를 \_moveTime으로 나눈 값으로 설정합니다. (말그대로, 시간 카운터를 "정규화"하는 것입니다.)

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

```js
public override void _Process(double delta)
{
    if (!_moving) return;

    if (_moveDelay < _moveTime) {
        _moveDelay += (float) delta;
        float t = _moveDelay / _moveTime;
        ProgressRatio = Mathf.Lerp(_moveFrom, _moveTo, t);
    } else {
        // stop our movement, wait at waypoint, continue progress...
    }
}
```

마지막으로, 카운터가 완료되면 플랫폼이 반드시 웨이포인트에 정확히 위치하고, \_moving 부울 변수를 다시 false로 설정하고 직접 다음 웨이포인트로 이동하는 작은 함수를 호출해야합니다.

```js
public partial class MovingPlatform : PathFollow3D
{
    // ...

    public override void _Process(double delta)
    {
        if (!_moving) return;

        if (_moveDelay < _moveTime) {
            _moveDelay += (float) delta;
            float t = _moveDelay / _moveTime;
            ProgressRatio = Mathf.Lerp(_moveFrom, _moveTo, t);
        } else {
            ProgressRatio = _moveTo;
            _moving = false;
            _ReachWaypoint();
        }
    }

    private void _ReachWaypoint()
    {
        _StartMove();
    }
}
```

그러나 이동을 다시 시작하기 전에 \_pauseTimeAtWaypoints가 null이 아닌 경우에는 추가 로직을 실행하기 전에 Godot에게 해당 시간만큼 기다리도록 지시하고 싶습니다.

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

그럼 이 값을 타임아웃으로 하는 미니 원샷 타이머를 생성해보겠습니다. 함수를 비동기 함수로 만들고(async 키워드 사용), 타이머 줄 앞에 await 키워드를 추가해봅시다:

```js
private async void _ReachWaypoint()
{
    if (_pauseTimeAtWaypoints > 0) {
        await ToSignal(
            GetTree().CreateTimer(_pauseTimeAtWaypoints),
            Timer.SignalName.Timeout);
    }
    _StartMove();
}
```

함께 이 코드를 \_Ready() 함수에도 복사해보세요. 이렇게 하면 플랫폼이 움직이기 전에 시스템이 아주 처음에 대기하도록 할 수 있습니다. \_Ready() 함수도 반드시 async로 만들어 주세요 ;)

```js
public async override void _Ready()
{
    // ...

    ProgressRatio = 0;
    _curWaypointIdx = 0;

    if (_pauseTimeAtWaypoints > 0) {
        await ToSignal(
            GetTree().CreateTimer(_pauseTimeAtWaypoints),
            Timer.SignalName.Timeout);
    }

    _StartMove();
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

이 시점에서 우리의 게임을 실행하면, 이전 시도와 매우 유사한 결과를 얻게 될 것입니다... 다만 모든 웨이포인트를 고려하고 있는 차이가 있습니다! 따라서 만약 정지 시간을 0보다 큰 값으로 설정하면, 플랫폼이 정말로 각 지점에서 잠시 멈추는 것을 볼 수 있을 거예요 :)

그러나 물론, 이 급격한 정지는 매우 자연스럽지 않아요. 이상적으로는 플랫폼이 웨이포인트에 도달할 때 속도를 줄이길 원할 거에요...

# 우리의 정지 부드럽게 만들기

움직임을 더 부드럽게 만들기 위해, 우리는 사용자 정의 이징 함수로 보조 보간 시스템을 강화할 거에요.

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

지금은 t 값을 그대로 사용하므로, 선형 이동이 발생합니다:

우리의 움직임을 좀 더 자연스럽게 만들기 위해서는 이 t 값을 특별한 계산 함수 안에 넣어 더 흥미로운 곡선으로 변환해야 합니다:

다행히도, easings.net이라는 훌륭한 웹사이트가 있어 다양한 즐거운 보간 곡선의 공식을 제공합니다!

일반적으로 여기서는 quart ease-in-out 함수를 선택할 것이며, 썸네일을 클릭해 코드를 가져오면 됩니다. C# 스크립트에서 다음과 같이 재구현할 수 있습니다:

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

```cs
public partial class MovingPlatform : PathFollow3D
{
    // ...

    private float _EaseInOutQuart(float x) {
        return x < 0.5f ? 8 * x * x * x * x : 1 - Mathf.Pow(-2 * x + 2, 4) / 2;
    }
}
```

여기서 t 값만 easing 함수 호출로 래핑해주면 됩니다:

```cs
public override void _Process(double delta)
{
    if (!_moving) return;

    if (_moveDelay < _moveTime) {
        _moveDelay += (float) delta;
        float t = _EaseInOutQuart(_moveDelay / _moveTime);
        ProgressRatio = Mathf.Lerp(_moveFrom, _moveTo, t);
    } else { ... }
}
```

그게 다에요! 게임을 다시 실행하면, 이제 플랫폼이 웨이포인트까지 멋지게 감속하고 잠시 멈춘 다음 다시 가속하여 다음 경로 청크로 이동합니다 :)

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

안녕하세요! 표 태그를 Markdown 형식으로 변경해보세요.

```js
public override void _Process(double delta)
{
    if (!_moving) return;

    if (_moveDelay < _moveTime) {
        _moveDelay += (float) delta;
        float t = _pauseTimeAtWaypoints > 0
            ? _EaseInOutQuart(_moveDelay / _moveTime)
            : _moveDelay / _moveTime;
        ProgressRatio = Mathf.Lerp(_moveFrom, _moveTo, t);
    } else { ... }
}
```

여기까지 왔어요 :)

우리의 플랫폼 이동 기능은 이제 선택적인 중지 및 제어 가능한 속도로 굉장히 멋져졌어요... 다만, 경로의 끝에 도달하면 코드가 고장나기 시작하는데요, 비율 목록 밖의 지점 인덱스를 찾으려고 하기 때문입니다!

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

우리는 우리의 이동의 마지막 단계를 처리해야 합니다: 시작 지점으로 되돌아 가는 것...

# 복귀 여정 다루기

이전에 말했듯이, 우리는 디자이너가 두 가지 다른 시스템 중에서 선택할 수 있도록 하고 싶습니다:

- 시작 지점으로 직접 순간 이동
- 또는 실제로 플랫폼이 반대 방향으로 돌아가는 경로를 다시 걸어가는 왕복 방식

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

첫 번째 경우는 구현하기가 매우 쉽습니다.

간단하게 \_StartMove() 함수로 돌아가서 현재 인덱스를 확인하고, 현재 인덱스가 점 수와 같으면 (즉, 마지막 지점에 도달했음을 의미함), 웨이포인트 인덱스를 1로 다시 설정하면 됩니다:

```js
private void _StartMove()
{
    _curWaypointIdx += 1;
    if (_curWaypointIdx == _nWaypoints) {
        _curWaypointIdx = 1;
    }

    _moveFrom = _waypointRatios[_curWaypointIdx - 1];
    _moveTo = _waypointRatios[_curWaypointIdx];
    _moveTime = Mathf.Abs(_moveTo - _moveFrom) * _totalCurveLength / _speed;
    _moveDelay = 0;
    _moving = true;
}
```

이렇게 하면 우리가 곡선의 첫 부분으로 즉시 돌아올 수 있습니다:

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

두 번째 경우에는 조금 더 작업이 필요합니다.

우선 물론, 어떤 방향으로 가고 있는지 알기 위해 새 변수가 필요합니다. 이를 전방방향에 +1을, 후방방향에 -1을 설정하여 설정합니다. (+1은 기본 설정) 아래와 같이:

```js
public partial class MovingPlatform : PathFollow3D
{
    // ...
    private int _platformDirection = 1; // 1: 전방, -1: 후방
}
```

이 변수를 \_StartMove() 로직에서 인덱스 증가 값으로 사용할 수 있을 뿐만 아니라, \_moveFrom 계산에서도 사용할 수 있습니다:

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

```js
private void _StartMove()
{
    _curWaypointIdx += _platformDirection;
    if (_curWaypointIdx == _nWaypoints) {
        _curWaypointIdx = 1;
    } else if (_curWaypointIdx == -1) {
    }

    _moveFrom = _waypointRatios[_curWaypointIdx - _platformDirection];
    _moveTo = _waypointRatios[_curWaypointIdx];
    _moveTime = Mathf.Abs(_moveTo - _moveFrom) * _totalCurveLength / _speed;
    _moveDelay = 0;
    _moving = true;
}
```

이렇게 하면 +1 또는 -1 값을 기준으로 리스트에서 이전 또는 다음 지점을 얻게 되어 플랫폼이 한 방향 또는 다른 방향으로 이동합니다.

하지만, 이로 인해 극값 확인이 약간 복잡해집니다. 실제로, 이제는 현재 인덱스가 곡선의 점 수와 같은지 확인할 뿐만 아니라 -1과 일치하는지도 확인해야 합니다.

```js
private void _StartMove()
{
    _curWaypointIdx += _platformDirection;
    if (_curWaypointIdx == _nWaypoints) {
        _curWaypointIdx = 1;
    } else if (_curWaypointIdx == -1) {
    }

    _moveFrom = _waypointRatios[_curWaypointIdx - _platformDirection];
    _moveTo = _waypointRatios[_curWaypointIdx];
    _moveTime = Mathf.Abs(_moveTo - _moveFrom) * _totalCurveLength / _speed;
    _moveDelay = 0;
    _moving = true;
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

그 이유는 우리가 경로의 양 끝에 다다를 수 있기 때문이에요 ;)

이전 체크에서는 \_jumpToStart 플래그를 사용하여 이전 코드를 분리한 후 시작할 거에요. 만약 이 플래그가 false인 경우(즉, 플랫폼을 실제로 경로를 따라 시작점 쪽으로 이동하길 원하는 경우), 우리는 방향 변수를 -1로 변경하고 현재 인덱스를 감소시킬 거예요:

```js
private void _StartMove()
{
    _curWaypointIdx += _platformDirection;
    if (_curWaypointIdx == _nWaypoints) {
        if (_jumpToStart) _curWaypointIdx = 1;
          else {
              _platformDirection = -1;
              _curWaypointIdx -= 2;
          }
    }  else if (_curWaypointIdx == -1) {
    }

    _moveFrom = _waypointRatios[_curWaypointIdx - _platformDirection];
    _moveTo = _waypointRatios[_curWaypointIdx];
    _moveTime = Mathf.Abs(_moveTo - _moveFrom) * _totalCurveLength / _speed;
    _moveDelay = 0;
    _moving = true;
}
```

이 값에 2를 줄이는 것이 필요한데, 1이 아닌 2를 감소시켜야 하는 이유는 이미 코드가 다른 방향으로 이동해야 한다고 "인식"하기 전에 증가시켰기 때문에 이 추가적인 오프셋을 고려해야하기 때문이에요.

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

비슷하게, 만약 우리의 인덱스가 -1에 도달하면 방향을 다시 +1로 설정하고 인덱스에 2를 더해야 합니다:

```js
private void _StartMove()
{
    _curWaypointIdx += _platformDirection;
    if (_curWaypointIdx == _nWaypoints) {
        if (_jumpToStart) _curWaypointIdx = 1;
          else {
              _platformDirection = -1;
              _curWaypointIdx -= 2;
          }
    }  else if (_curWaypointIdx == -1) {
        _platformDirection = 1;
        _curWaypointIdx += 2;
    }

    _moveFrom = _waypointRatios[_curWaypointIdx - _platformDirection];
    _moveTo = _waypointRatios[_curWaypointIdx];
    _moveTime = Mathf.Abs(_moveTo - _moveFrom) * _totalCurveLength / _speed;
    _moveDelay = 0;
    _moving = true;
}
```

좋아요, 정말 멋지네요! 이제 여러 순환 옵션을 처리하고 조정 가능성이 높은 완전한 이동 체계를 갖고 있습니다. 인스펙터에서 쉽게 사용자 정의할 수 있어요 :)

이제 튜토리얼을 마무리하면서, 마지막 문제를 해결하는 방법을 살펴보겠습니다: 우리의 무형 플랫폼 바닥 문제!

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

# 충돌 수정

알겠어요 — 정말 기능하는 플랫폼 시스템을 얻으려면 해결해야 할 마지막 문제는 플랫폼을 통과해 버리는 우리 플레이어 아바타입니다:

이전에 언급한 대로, 우리의 플랫폼은 AnimatableBody3D 노드이고 우리의 플레이어 아바타는 이전 에피소드에서 논의했던 것처럼 CharacterBody3D입니다...

그래서 작동해야 하지 않나요? 문제가 뭘까요?

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

사실 일어나고 있는 것은, 우리 플랫폼이 PathFollow 노드의 도움으로 경로를 따라 이동하지만 콜라이더는 따라오지 않는다는 것이죠!

이것을 확인하는 방법은 디버그 메뉴로 이동하여 콜라이더 디버그를 활성화하는 것이죠:

이것은 PathFollow 노드를 사용하여 AnimatableBody 노드를 이동시킬 때 알려진 주의 사항입니다. 그러나 다행히 쉬운 해결 방법이 있습니다!

RemoteTransform3D 노드를 사용하면 됩니다.

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

요컨대이 노드는 매우 간단합니다. 다른 노드의 변환 속성을 동기화하여 소스의 위치, 회전 및/또는 크기를 쉽게 대상 노드에 적용할 수 있습니다.

따라서 충돌을 해결하는 데 있어 요령은 PathFollow 노드의 하위 요소로 플랫폼을 직접 넣고 이렇게 이동하는 대신, 해당 충돌기의 위치를 제대로 업데이트하지 않고 충돌을 무효화하는 것이 아니라, PathFollow 노드의 자식으로 RemoteTransform3D 노드를 사용하고, 해당 위치를 다른 곳에 있는 플랫폼으로 적용하는 것입니다.

따라서 장면 트리를 업데이트하는 것 외에도, 물론 RemoteTransform3D 노드의 인스펙터에서 Remote Path 속성으로 플랫폼을 대상으로 지정해야 합니다.

업데이트 섹션에서는 위치에만 관심이 있는 경우 회전 및 크기 동기화를 끌 수도 있습니다:

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

여기서 마칩니다! 이제 플랫폼은 이전과 똑같이 움직이지만, 걸어보면 충돌이 발생하여 플레이어가 바닥을 통과하지 않게 되었어요 :)

# 결론

그럼 이제 기본 이동 플랫폼 시스템을 Godot과 C#에서 설정하는 방법을 알게 되셨습니다! 이 튜토리얼이 마음에 들었기를 바라고, 보간법, 이징 또는 Godot의 경로와 RemoteTransform3D 노드와 같은 개념을 이해하는 데 도움이 되었기를 희망합니다.

이 튜토리얼을 즐겼다면, 기사에 박수를 치고 다음 기사를 놓치지 않도록 팔로우해주시기 바랍니다. 또한, 학습하고 싶은 Godot 트릭에 대한 아이디어를 남겨주시고 망설이지 마세요!

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

언제나 읽어주셔서 감사합니다!

더 많은 콘텐츠와 다양한 작가의 기사를 보시려면 Medium 회원이 되어 팔로우해주세요!

잘 지내세요 :)
