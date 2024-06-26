---
title: "Unowned self 개념의 문제점"
description: ""
coverImage: "/assets/img/2024-06-23-TheCaseAgainstunownedself_0.png"
date: 2024-06-23 23:36
ogImage:
  url: /assets/img/2024-06-23-TheCaseAgainstunownedself_0.png
tag: Tech
originalTitle: "The Case Against [unowned self]"
link: "https://medium.com/gitconnected/the-case-against-unowned-self-b34103618684"
---

![2024-06-23-TheCaseAgainstunownedself_0.png](/assets/img/2024-06-23-TheCaseAgainstunownedself_0.png)

클래스, 클로저 또는 액터를 사용할 때마다 Swift는 힙(heap)에 정보를 저장합니다. 전달하는 변수는 실제로 메모리의 주소(즉, 참조)를 가리키는 포인터입니다.

옛날에는 Mac과 iOS 개발자들이 이 메모리를 수동으로 관리해야 했습니다. heap에 메모리 블록을 만들기 위해 alloc()을 사용하고 다른 참조를 추가하기 위해 retain()을 사용하며 메모리를 해제하기 위해 release()를 사용합니다.

2011년, Apple은 ARC(자동 참조 카운트)를 소개했으며, 이를 통해 컴파일러가 이러한 보일러플레이트 메모리 관리 코드를 자동으로 작성하도록 하여, 축적된 개발 시간을 절약했습니다.

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

ARC는 다양한 종류의 참조 개념을 소개했습니다.

## 강한 참조

이것은 기본 참조입니다. 포인터가 사용 중일 때 가리키는 메모리를 유지합니다.

강한 참조를 만드는 동안 힙 객체의 참조 카운트(또는 refCount)가 증가하고, refCount가 0이 되면 객체가 할당 해제되어 힙에 있는 메모리가 해제됩니다.

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

## 약한 참조

약한 참조는 힙 객체들이 서로를 가리키면서 유지주기 문제 없이 회피할 수 있게 해줍니다. 이를 통해 개발자들은 클로저와 델리게이트에서 클래스에 대한 참조를 생성하지만 불필요하게 객체들을 유지할 필요가 없어집니다.

약한 참조는 refCount가 증가하지 않으면서 메모리의 힙 객체를 가리킵니다. 만약 refCount가 제로가 되면 객체는 해제되고 약한 참조는 nil이 됩니다— 이는 클로저에서 메모리 누수를 방지합니다.

이 작동방식을 다루기 위해 약한 참조는 항상 optional로 래핑되어 있습니다.

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

```swift
// StoreViewModel.swift

func loadStorefront() {
    api.fetchInventory() { [weak self] inventory in
        self?.inventory = inventory
    }
}
```

이 예에서 인벤토리는 네트워크를 통해 가져오며, 데이터가 도착하면 클로저 콜백이 실행됩니다. self는 약한 참조로 캡처되어 StoreViewModel의 속성이 클로저에서 업데이트될 수 있음을 의미합니다. 사용자가 화면을 나가면 뷰 모델의 강한 참조 수가 0이 되면 런타임이 뷰 모델을 해제할 수 있으므로, 콜백을 기다리는 동안 메모리에 유지되지 않고 인벤토리가 반환될 때 아무 일도 일어나지 않습니다.

## Unowned references

리테인 사이클을 방지하기 위해 Swift에서 세 번째 종류의 참조인 unowned가 소개되었습니다. 이는 약한 참조와 유사하게 동작하지만 참조 대상 객체의 메모리가 항상 해당 객체를 참조하는 unowned 포인터보다 오래 존속할 것으로 가정합니다.

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

```swift
// StoreSingleton.swift

static let sharedInstance = StoreSingleton()

private init() {}

func configureStorefront() {
    api.fetchInventory() { [unowned self] inventory in
        self.inventory = inventory
    }
}
```

여기에서 우리는 "진정한 싱글톤" (private init으로)을 가지고 있기 때문에 객체의 수명이 클로저보다 더 오래 지속된다는 것을 확신할 수 있습니다. 클로저 캡처 목록의 [unowned self]는 리테인 사이클이 생성되는 것을 방지합니다 (그러나 싱글톤의 경우 리테인 사이클에 대해 걱정할 필요가 실제로 없습니다).

클로저는 약간 더 간단하며, unowned는 약한 참조와 비교하여 몇 가지 성능 이점을 제공합니다:

- 옵셔널 체이닝이나 언래핑 작업을 사용할 필요가 없습니다.
- unowned 참조는 힙 객체에 사이드 테이블을 만들지 않기 때문에 더 적은 메타데이터를 저장합니다.
- 약한 참조가 가리키는 메모리에 액세스하는 경우 포인터 간의 추가 점프 또는 간접 레이어가 하나 더 필요합니다.

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

# 성능이 좋다고 해도 이에는 한 가지 제약 사항이 있습니다: 만약 수명을 잘못 지정하고 클로저나 프로퍼티가 weak 참조보다 더 오래 존속된다면, 앱이 크래시할 수 있습니다.

## unowned는 가치가 있을까요?

당연히, 크래시는 상당히 나쁩니다. 그리고 Knuth가 종종 오인용된 것처럼 이른 최적화는 우리가 분명히 조심해야 할 유혹입니다. 그래서 성능을 약간 더 얻기 위해 unowned 참조를 마구 사용해서는 안 됩니다.

제 블로그를 읽고 있다면, 아마 Swift에 대해 알고 있는 분일 것입니다. 하지만 당신의 팀원들이 모두 그렇다고 확신할 수 있을까요?

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

금요일 오후 5시에 수행하는 모든 코드 리뷰가 'unowned' 키워드를 발견할 때마다 개체 소멸로 이어지는 많은 가지 코드 경로를 이해하는 데 신중하다는 것을 확신하실 수 있나요?

궁극적으로, 'unowned'는 프로그래밍 문제가 아니라 인간 문제입니다.

# 약한 참조의 성능 비용

우리는 'unowned'의 위험을 이해합니다. 약한 참조와 비교했을 때 이점에 대해 이야기해봅시다.

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

성능에 관한 이야기예요.

약한 참조를 옵셔널 언래핑하는 데 포함된 소수의 CPU 명령이 중요하다고 가정하지 않겠어요. 옵셔널은 사실 열거형이라는 비밀을 알고 계세요? 이 값 형식은 스택에 존재하죠. 이를 조작하는 오버헤드는 무시할 수준이에요. 왜냐하면 이들은 힙에 대한 쓰레드 안전한 잠긴 접근이 필요하지 않거든요.

메모리에 대해 이야기해볼까요? 힙 객체를 약한 참조하는 것은 처음에 사이드 테이블을 만듭니다. 이는 힙 객체의 메모리 레이아웃 외부에 저장된 가벼운 메타데이터입니다. 사이드 테이블에는 힙 객체로의 포인터, 약한 참조 카운트; 그리고 정수 오버플로된 강한 참조 및 unowned 참조의 refCounts가 포함돼 있어요. unowned 참조 자체에는 사이드 테이블이 필요하지 않아요.

이게 작은 비트들의 소수라는 느낌이 드시나요? 그렇게 생각하시면 맞아요. 추가 메모리량이 극히 소량이죠. 사실상 런타임은 약한 참조를 미리 nil 처리하려고 deinit에 추가 작업이 필요하지 않아요. 약한 참조가 폐기될 때까지 가벼운 사이드 테이블이 메모리에 남아 있어 힙 객체의 라이프사이클 상태를 체크해 nil 또는 객체를 반환합니다.

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

여기에는 약간의 오버헤드가 있습니다. 약한 참조는 이 쪽 테이블을 가리키고, 다시 이를 통해 실제 힙 객체를 가리킵니다. 이 간접 참조 때문에 약한 참조에 약간의 오버헤드가 추가되고, 비싼 CPU 캐시 미스가 발생할 위험이 있습니다.

한편, 언올드 참조는 메모리의 힙 객체를 직접 가리킵니다. 간접 참조가 줄어듦에 따라 덜한 오버헤드가 발생하지만, 런타임은 여전히 힙 객체의 라이프사이클 상태를 확인해야 합니다. 따라서 객체를 반환해야 하는지, 아니면 swift_abortRetainUnowned로 크래시를 발생시켜야 하는지 알 수 있습니다.

# 언올드를 사용하는 것이 합당한 경우

Swift 소스를 살펴보며 약한 참조와 언올드 참조의 구현을 이해하면, 언올드를 사용하는 이점이 적고, 치명적인 오류를 발생시킬 위험 때문에 이 성능 이점이 가치가 없는 경우가 많다는 것이 분명해집니다.

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

그러나, 컴퓨터 과학에서 모든 것은 트레이드오프에 관한 것이며, 이런 경우에는 트레이드오프가 합리적인 경우가 있습니다.

- 참조(reference)가 많고 실행 시간 성능이 중요한 병목 현상이 되는 경우, 예를 들어 많은 화면 객체를 포함하는 게임 엔진을 개발하는 개발자가 있는 경우.
- 참조(reference)의 수가 많고 메모리가 극도로 제한적해서 수 바이트를 저장하는 것이 관련이 있는 경우 — 예를 들어 코드 일부를 최적화하여 L1 CPU 캐시에 맞출 수 있는 경우.
- 다른 기여자에 의존하지 않는 인디 개발자가, 무엇을 하는 지 정확히 알고 있으며, 프로덕션에서의 충돌 비용을 감당할 준비가 되어 있는 경우.

그러나 하나의 클로저 콜백에서 [unowned self]를 사용하는 것이 몇 개의 시계 주기와 최악의 경우 몇 나노초의 캐시 미스를 위해 꽤 좋지 않은 트레이드오프라는 점에 동의해 주셨으면 합니다.

# 결론

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

ARC에서 잠재 쓰레기 참조 문제를 방지하는 데 기여하는 비소유 참조(unowned references)는 런타임 성능 향상의 조각을 제공하지만 잘못 사용하면 충돌 위험이 따릅니다.

매우 병목 상태인 시스템에 수많은 참조가 있는 경우 등 일부 상황에서는 사용할 만한 가치가 있지만, 단일 클로저 캡처 목록에 [unowned self]를 사용하는 것은 그중 하나가 아닙니다.

[weak self]를 계속 사용하고 unowned는 예외적인 경우로 취급해 주세요. 이는 잘못 사용하면 예외를 받아들일 수 있는 경우라는 의미입니다.

간단히 말해, 코드에서 unowned를 사용할 만큼 자신감이 있다면, 그냥 unowned(unsafe)를 사용하세요. 약간 더 성능적으로 우세합니다\*.
