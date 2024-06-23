---
title: "Unity 아키텍처 GameObject 컴포넌트 패턴 이해하기"
description: ""
coverImage: "/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_0.png"
date: 2024-06-23 22:21
ogImage: 
  url: /assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_0.png
tag: Tech
originalTitle: "Unity Architecture: GameObject Component Pattern"
link: "https://medium.com/@simon.nordon/unity-architecture-gameobject-component-pattern-34a76a9eacfb"
---


지난 블로그 글에서는 "스파게티 패턴"에 대해 살펴보았습니다. 이는 아무런 패턴이 없는 게임을 묘사하기 위해 사용되는 농담적인 용어입니다.

많은 신규 개발자들이 이를 선택하는 이유는 게임을 빠르고 쉽게 제작할 수 있다는 점 때문입니다. 하지만 이후에 자신의 지식을 확장하지 못하는 경우가 많습니다.

프로토타입을 만드는 데 뛰어난 장점이 있지만, 코드가 엉망으로 변하여 어수선하고 복잡한 코드 구조로 이어질 수 있습니다.

<div class="content-ad"></div>

그러한 코드베이스는 긴, 엉킨 메소드, 싱글톤 게임 매니저와 같은 "God Objects"에 지나치게 의존하며, 각 새로운 기능이 추가될 때마다 증가하는 끝없는 버그 나열로 악명높습니다.

다행히도, 이러한 문제 중 많은 것을 해결해 줄 수 있는 패턴 중 하나는 GameObject-Component Pattern이며, 이는 Unity의 원래 비전이자 Unity Editor의 공식 프레임워크입니다.

이 패턴은 큰 "God Objects"를 더 작고 모듈식 컴포넌트로 분해함으로써 이런 문제들을 해결합니다. 이렇게 하면 새 코드를 작성하지 않고도 GameObject에서 컴포넌트를 쉽게 추가, 수정 또는 제거할 수 있습니다. 본 이론에 따르면, 더 유연하고 유지 보수가 쉬운 코드베이스로 이끌어줄 것입니다.

![Unity Architecture GameObject Component Pattern](/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_0.png)

<div class="content-ad"></div>

그래서, 이 패턴이 정말 멋져 보인다면 왜 아무도 사용하지 않는 것 같은걸까요? 심지어 Unity조차 이를 버리고 Scriptable Objects와 ECS를 선호합니다.

나는 '스파게티 프로토타입'을 50시간 이상 리팩토링하여 GameObject Component를 모든 면에서 완전히 활용하고, 그 장점과 약점을 발견했습니다.

# 시간 0 — 게임 매니저의 종말

내 게시물을 따라오신다면 게임 매니저에 대한 절규를 들어볼 수 있을 텐데, 그들은 종종 스파게티 코드베이스에 대한 징후라는 점입니다. 그러므로 그것이 필요 없다는 것을 증명하기 위해 우리는 그것을 삭제할 겁니다.

![이미지](/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_1.png)

<div class="content-ad"></div>

그러나 매니저가 객체들에게 무엇을 해야 하는지 말하지 않고 게임의 흐름을 어떻게 제어할까요?

우리는 새로운 코딩 개념을 소개해야 합니다. 이벤트입니다.

이벤트는 응용 프로그램에서 제어 흐름의 방향을 반대로 전환할 수 있습니다. 다른 객체들에게 매니저가 지시하는 대신, 이러한 객체들은 매니저의 변화에 반응하는 방식으로 동작합니다.

![Unity Architecture](/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_2.png)

<div class="content-ad"></div>

이벤트를 사용하면 게임 매니저에서 모든 종속성을 제거하여 대부분의 기능을 제거할 수 있습니다. 그럼으로써, 남은 데이터를 몇 가지 간단한 구성 요소로 분해할 수 있습니다. 예를 들어, 게임 상태와 같은 핵심 이벤트를 담는 컨테이너입니다.

![이미지](/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_3.png)

# 2시간 — 캡슐화

이러한 경향을 이어가며, 코드를 깨끗하게 유지하기 위해 클래스가 적절하게 캡슐화되어야 한다는 것이 중요하다는 것을 알았습니다.

<div class="content-ad"></div>

우리 코드를 개선하는 가장 간단한 방법은 serialize field 속성을 사용하는 것입니다. 이렇게 함으로써 클래스가 자신의 데이터에 대한 단일 권한 출처로 유지되도록 할 수 있습니다.

```js
// 나쁨. 캡슐화 깨짐.
public int currentHealth;

// 좋음. 캡슐화 유지.
[SerializeField]
private int currentHealth;

// 좋음. 읽기 전용 액세스 제공하면서 캡슐화 유지.
[field:SerializeField]
public int currentHealth { get; private set; }
```

유니티 이벤트는 또 다른 중요한 도구로, 에디터에 깊게 내장되어 있으며, 컴포넌트들 간의 결합을 느슨하게 할 수 있습니다.

예를 들어, 플레이어와 적이 동일한 Death Handler 컴포넌트를 사용하지만 전혀 새로운 코드 없이 새로운 동작을 만들어낼 수 있습니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_4.png" />

그러나 이 코드 의존성 부족은 두날날날날 모낫날剌몫몿 때날이카. Unity Events를 과도하게 사용하면 성능 문제가 발생할 수 있으며 에디터에서 시간이 오래 걸리는 수동 설정으로 이어질 수 있습니다.

이러한 이유로, 자주 발생하거나 중요한 게임 이벤트에 대해 고전적인 C# 동작을 사용했습니다.

마지막으로, Gold와 Achievements와 같은 구성 요소는 싱글톤 계정 관리자에 의존하는 대신 자체 저장 데이터를 처리하도록 했습니다.

<div class="content-ad"></div>

리팩토링은 튼튼한 시작을 했어요.

# 7시간 - 성능

대규모 게임에 대한 확장성과 유지 관리성이 중요하지만 성능도 그렇습니다. GameObject-Component 자체는 스파게티보다 빠르지 않습니다 (사실 느릴 수도 있음), 그러나 리팩토링의 정신에 따라 이러한 문제를 해결해야 합니다.

가장 중요한 기술은 Object Pooling의 사용입니다.

<div class="content-ad"></div>

총알, 체력 팩, 입자 및 적을 생성하고 제거하는 과정으로 인해 Garbage Collector가 엄청난 스트레스를 받고 fps에 영향을 줍니다.

그렇기 때문에 이러한 객체들을 파괴하는 대신 재활용하는 것이 해결책입니다.

```js
/* 컴포넌트는 풀에서 객체를 '빌려오고' 작업을 마치면 돌려놓을 수 있음.
   현재 사용 중인 객체가 없는 경우에만 새 객체가 생성됨. */
public class GameObjectPool : MonoBehaviour
{
  [SerializeField] private GameObject prefab;
  private readonly Queue<GameObject> _inactivePool = new();

  public GameObject Get()
  {
    return _inactivePool.Count > 0 ?
           _inactivePool.Dequeue() :
           Instantiate(prefab, null); 
  }
        
  public void Return(GameObject item)
  {
    item.SetActive(false);
    _inactivePool.Enqueue(item);
  }
}
```

이 기술은 발사체, 적, 입자 효과, 오디오 소스 및 모든 UI 요소(피해 수치와 같은)를 단일 캔버스로 통합함으로써 성능을 향상시켰습니다. 제어된 스트레스 테스트로 60FPS에서 100FPS로 최대 66%의 성능 향상을 달성했습니다.

<div class="content-ad"></div>

# 시간 10 — 의존성 그래프 수정하기.

스파게티 패턴에 대한 내 정의 중 하나는 구성 요소 사이에 교차하는 선들의 웹을 만들지 않고는 시각화할 수없는 의존성 그래프였습니다.

이러한 문제를 피하는 간단한 묘수는 구상 대신 구체적인 참조에 의존하는 대신 추상화에 의존하는 것입니다. 특히 이 경우에서는 상속을 사용하여.

![Unity Architecture Game Object Component Pattern](/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_5.png)

<div class="content-ad"></div>

게임 상태에 의존하는 구성 요소가 모두 GamePlayComponent 클래스에서 상속되도록 보장하면 이러한 동작을 체크하고도 직접적으로 참조하지 않고도 무차별적인 상태 관리를 할 수 있습니다.

이 기술은 사용자 인터페이스를 분리하는 데 다시 사용되었습니다. 스파게티 코드에서 흔한 문제는 게임이 올바르게 작동하려면 UI에 대한 강력한 의존성이 필요하다는 것입니다. 개발자로서 UI 없이도 스크립트, 콘솔 명령 또는 API를 통해 게임의 어떤 상태든 액세스할 수 있어야 합니다.

![이미지](/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_6.png)

![이미지](/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_7.png)

<div class="content-ad"></div>

# 20시간 — 싱글턴 제거하기

안녕하세요! 싱글턴은 악이 아니지만, 많은 개발자들이 과도하게 의존하고 있습니다. 대안을 제대로 탐색하기 위해 이 리팩토링에서는 싱글턴을 삭제하기로 결정했습니다.

이것은 씬에 직렬화된 객체에 대해서는 쉽게 할 수 있습니다. 그러나 동적으로 생성된 프리팹들은 도전을 제공합니다. 프리팹은 씬 객체를 참조할 수 없습니다. 이 문제를 해결하기 위해 팩토리 패턴을 사용해야 합니다.

![이미지](/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_8.png)

<div class="content-ad"></div>

공장은 생성될 때 프리팹의 종속성을 주입합니다. 이는 새로운 오브젝트 풀과 잘 작동합니다. 이로 인해 싱글톤이 필요 없어졌지만, 코드에 복잡성과 보일러플레이트가 추가되었습니다.

![이미지](/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_9.png)

특히 게임이 여러 씬을 사용하는 경우 이 문제가 악화됩니다. 여러 씬 간의 참조를 해결해야하기 때문에 DontDestroyOnLoad(), 코루틴 및 FindObjectOfType`T`를 결합하여 사용해야 합니다.

이 추가된 복잡성은 구성 기반 아키텍처의 복잡성과 결합되어 많은 예측할 수 없는 버그를 발생시켰습니다.

<div class="content-ad"></div>


![image](https://miro.medium.com/v2/resize:fit:1400/1*3BLCk1jFCCHxxns9MfhzHQ.gif)

GameObject Component는 컴포넌트 간 상호 작용에서 행동이 발생하도록 설계되었습니다. 불행하게도 버그도 발생합니다.

스파게티 패턴과는 달리, 매니저 클래스를 따라가면서 문제를 찾을 수 없습니다. 버그는 특정한 코드 줄에서 발생하는 것이 아니라, 컴포넌트들의 상호 작용에 이상이 있어서 발생하는 것입니다.

문제를 해결하려고 10시간 동안 노력한 후에도 전혀 진전이 없는 것 같았습니다.


<div class="content-ad"></div>


![Image](https://miro.medium.com/v2/resize:fit:1400/1*mXQ-sSlR0wSAtb5KPmrJQA.gif)

# 30시간째 — 절망의 구렁이와 마주치다

알게 된 대로, 게임 오브젝트 컴포넌트는 스파게티 코드에 비해 많은 초기 비용이 필요하다. 스파게티 코드로는 게임이 선형적으로 진행되어 경험이 매우 보상적인 결과를 가져온다.

반면, 게임 오브젝트 컴포넌트는 모든 필요한 구성 요소가 실제로 테스트될 수 있을 때까지 존재해야 한다. 'Health.cs' 스크립트가 충분한지 여부를 알기 어렵다. 그 외의 모든 구성 요소와 함께 사용될 때까지는.


<div class="content-ad"></div>

이 모든 일은 동적으로 생성된 오브젝트를 다루고, 컴포넌트를 풀링하고 재활용하면서 함께 작동하도록 시도하다 보니 결과를 보지 못하는 등 너무 압도되었어요.

그렇게 해서 2개월 동안 개발을 중단했죠.

<div class="content-ad"></div>

게임을 조각조각 다듬는 내 계획은 망가졌어요. 그래서 다른 일들로 넘어가고 게임 오브젝트 컴포넌트는 쓴맛을 남겼어요.

# 시간 40 —조합 이해하기

두 달 동안의 휴식 후, 깨달음이 찾아왔을 때 프로젝트를 끝내려는 결심을 내렸어요. 스파게티 코드 사용을 그만 두었지만, 여전히 스파게티 브레인을 사용하고 있었어요.

게임오브젝트-컴포넌트의 본질은 컴포넌트를 공유하는 데 있어요. '플레이어'와 '적'을 구분하는 것은 이 아키텍처의 정신을 위반해요. 그래서 플레이어와 적이 동일한 컴포넌트에 의존하도록 확실히 했어요.

<div class="content-ad"></div>


![이미지](/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_12.png)

이전에 '플레이어 이동'과 '적 체력'이 있었습니다. 목표는 구성 요소를 단순화하여 '식별성'이 없어도 존재할 수 있도록하는 것입니다.

![이미지](/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_13.png)

10시간의 개발을 버리고 합성 마인드셋으로 리팩토링을 시작했습니다. 이것은 아키텍처에 대한 점점 쌓이는 경험과 결합되어 이 어려움을 극복하고 핵심 게임을 다시 작동시킬 수 있었습니다.


<div class="content-ad"></div>

# 50시간째 — 보상

게임을 리팩토링하는 데 처음부터 만드는 시간보다 더 오랜 시간이 걸렸어요. 코드는 더 깔끔해졌지만 정말 그만한 가치가 있었을까요?

![이미지](/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_14.png)

지난 포스트에서 언급했듯이 프로젝트 막바지에 진행 상황 바를 구현하는 데 5시간이 걸렸는데, 이번에는 단 25분만에 완료했어요.

<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 변경했습니다.

<div class="content-ad"></div>

게임 오브젝트-컴포넌트는 처음에는 개발 속도가 느렸지만, 유지보수성과 확장성에서 그 가치를 입증합니다. 유니티 에디터와의 원활한 통합과 코드의 내재적 "깔끔함"은 부정할 수 없는 장점입니다.

![GameObject-Component](/assets/img/2024-06-23-UnityArchitectureGameObjectComponentPattern_15.png)

그렇다면 최고의 아키텍처인가요?

아니요.

<div class="content-ad"></div>

이 패턴은 주의 깊은 계획, Unity에 대한 심층적인 이해 및 정교한 종속성 관리를 필요로 합니다. 짜증나는 버그 발생과 혜택 대비 비용이 높다는 이유 때문에 많은 개발자들이 이를 사용하지 않는 것일 수 있습니다.

그렇지만, 이 패턴에서 요소들을 통합하면 스파게티 코드 기반 프로젝트를 크게 향상시킬 수 있으며, 최종적인 리팩터링이 덜 괴로운 경험을 할 수 있습니다.

만약 해당 하이브리드의 예시를 보고 싶다면, 게임 SoulStone Survivors의 역컴파일을 확인해보세요.

또는 해당 패턴과 스파게티 패턴의 전체 소스 코드를 github에서 확인할 수 있습니다.

<div class="content-ad"></div>

그것에 대해 더 나은 해결책이 존재하는지에 대해 고민하게 만듭니다. 

Scriptable Object Pattern에 대하여 자세히 살펴보고 리팩터링할 때 발견할 것입니다.