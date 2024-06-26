---
title: "iOS 인터뷰 질문-제7파"
description: ""
coverImage: "/assets/img/2024-05-27-iOSInterviewQuestions-Part7_0.png"
date: 2024-05-27 16:28
ogImage:
  url: /assets/img/2024-05-27-iOSInterviewQuestions-Part7_0.png
tag: Tech
originalTitle: "iOS Interview Questions-Part 7"
link: "https://medium.com/swift-interview-preparations/ios-interview-questions-part-7-86894abed8e8"
---

Swift, iOS, Xcode에 관한 인터뷰 질문

![iOS Interview Questions Part7](/assets/img/2024-05-27-iOSInterviewQuestions-Part7_0.png)

1. 옵셔널을 어떻게 생성할 수 있나요?

Swift에서 옵셔널은 타입 앞에 ?를 추가하여 만들 수 있습니다. 예를 들어

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
var name: String?

# 2. What is optional Binding?

Optional Binding은 옵셔널이 값을 포함하고 있는지 여부를 확인하는 데 사용됩니다. 값이 있는 경우 if let을 사용하여 상수나 변수에 그 값을 저장합니다. if 문 내에서 생성된 상수와 변수는 if 블록 내에서만 사용할 수 있습니다.

우리는 옵셔널 값을 가져와서 논 옵셔널 상수에 연결할 것입니다. 우리는 If let 구조 또는 Guard 문을 사용합니다.
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

# 3. 옵셔널 체이닝이란 무엇인가요?

옵셔널 체이닝은 현재 nil일 수 있는 옵셔널에서 속성, 서브스크립트 및 메소드를 조회하고 호출하기 위한 과정입니다. 옵셔널이 속성, 메소드 또는 서브스크립트 호출이 성공하면 해당 값이 반환됩니다. 옵셔널이 nil이면 모든 속성, 메소드 또는 서브스크립트 호출이 nil을 반환합니다. 여러 쿼리가 연결되어 있을 때 체인 중 하나의 링크가 nil이면 전체 체인이 안전하게 실패합니다.

스위프트 프로그래밍 언어에서 옵셔널 체이닝은 속성을 조회하고 호출하는 과정입니다. 여러 쿼리를 체이닝할 수 있지만 체인 중 하나의 링크가 nil이면 전체 체인이 실패합니다.

# 4. iOS 애플리케이션 개발에서 사용되는 디자인 패턴을 설명해 주세요.

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

본질적으로, 디자인 패턴은 전형적인 소프트웨어 문제를 해결하기 위해 반복적으로 적용할 수 있는 코드 솔루션입니다. 프로젝트에서 디자인 패턴을 사용하면 더 모듈식이고 확장 가능하며 최적화된 소프트웨어를 얻을 수 있습니다. 다른 사람들의 코드를 더 잘 이해할 수 있게 되어, 디자인 패턴을 즉시 인식할 수 있게 될 것입니다.

디자인 패턴은 더 나은 소프트웨어를 만들기 위한 지침으로 사용되지 않습니다. 디자인 패턴과 최선의 방법은 전혀 다르기 때문입니다. 또한, 도전에 접근하는 방법에 대한 지침을 제공하기 위한 것이 아닙니다. 대신, 전형적인 엔지니어링 및 아키텍처 컨셉에 대한 관찰된 일반적인 응답을 문서화하는 데 주로 사용됩니다. 일반적인 디자인 패턴으로는 Facade, Decorator, Factory Method, Singleton 등이 있습니다.

다음 디자인 패턴은 iOS 개발에서 주로 사용됩니다.

- 생성 디자인 패턴 — Builder, Factory, Singleton.
- 구조 디자인 패턴 — Decorator, Facade, Adapter.
- 행동 디자인 패턴 — Observer, Memento.

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

# 5. Swift에서 옵셔널 값을 해제하는 여러 방법은 무엇입니까?

- Guard 문 (안전한 방법)
- 옵셔널 바인딩 — 안전
- 옵셔널 패턴 — 안전
- 강제 언래핑 — "!" 연산자를 사용하여, 안전하지 않음
- 옵셔널 체이닝 — 안전
- nil 병합 연산자 — 안전
- 암시적으로 언래핑된 변수 선언: 많은 경우 안전하지 않음

# 6. Nil 병합 연산자

??을 사용하여, 연산자의 왼쪽에 nil 값이 있으면 ?? 오른쪽의 값이 사용됩니다.

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

# 7. iOS의 코어 데이터란 무엇인가요?

코어 데이터는 애플이 제공하는 프레임워크로, 데이터를 저장, 검색, 삭제 및 수정하는 데 사용됩니다. 애플리케이션의 모델 레이어 객체를 관리하는 데 사용됩니다. 코어 데이터는 데이터베이스가 아닙니다. 코어 데이터의 데이터 모델 편집기를 사용하면 데이터 유형과 관계를 쉽게 정의하고 해당 클래스 정의를 생성할 수 있습니다.

코어 데이터는 ORM(객체 관계 매핑)이나 객체-관계 매퍼가 아닙니다. 또한 데이터베이스도 아닙니다. 대신 코어 데이터는 객체 그래프 관리자로, 객체 그래프를 디스크에 지속성 저장할 수 있는 기능을 갖추고 있습니다.

애플은 이렇게 말합니다: "코어 데이터를 사용하여 애플리케이션의 오프라인 사용을 위한 영구 데이터를 저장하고, 임시 데이터를 캐시하며, 앱에서 단일 장치에서 취소 기능을 추가하세요." 😮

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

코어 데이터는 지속성, 개별 또는 일괄적으로 변경 내용을 실행 취소 및 다시 실행, 백그라운드 데이터 작업, 뷰 동기화, 버전 및 이전 버전 마이그레이션 등의 기능을 제공합니다.

프로젝트를 만들 때 "코어 데이터 사용"란 체크 박스를 선택하여 코어 데이터 모델을 만들 수 있습니다.

코어 데이터 스택

데이터 모델 파일을 만든 후, 앱의 모델 레이어를 지원하는 협업 클래스를 설정하십시오. 이러한 클래스들은 총칭하여 코어 데이터 스택이라고 합니다.

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

다음은 Core Data 구성 요소 몇 가지가 있습니다:

- NSManagedObjectModel 인스턴스는 앱의 모델 파일을 나타내며 앱의 유형, 속성 및 관계를 설명합니다.
- NSManagedObjectContext 인스턴스는 앱의 유형의 인스턴스에 대한 변경 사항을 추적합니다.
- NSPersistentStoreCoordinator 인스턴스는 앱의 유형의 인스턴스를 저장소에서 저장하고 가져옵니다.
- NSPersistentContainer 인스턴스는 한 번에 모델, 컨텍스트 및 저장소 코디네이터를 설정합니다.

# 8. Core Data의 다른 데이터 유형

많은 앱이 다양한 유형의 정보를 영속적으로 저장하고 표시해야 합니다. Core Data는 일반적인 데이터베이스에서 사용되는 Date 나 Decimal 유형과 같은 속성뿐만 아니라 Transformable 유형으로 처리되는 비표준 속성을 포함하여 다양한 속성을 제공합니다.

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

# 9. 관리 대상 객체 컨텍스트란 무엇인가요?

관리 대상 객체 컨텍스트는 Core Data 응용 프로그램에서 단일 객체 공간 또는 스크래치 패드를 나타냽니다.

# 10. 정적 바인딩과 동적 바인딩의 차이는 무엇인가요?

정적 바인딩: 이벤트는 컴파일 시간에 발생합니다. 정적 바인딩에서 실행이 빠릅니다. 초기 바인딩이 있습니다. "컴파일 시간"에 해결됩니다. 메소드 오버로딩은 정적 바인딩의 예시입니다.
동적 바인딩: 이벤트는 실행 시간에 발생합니다. 동적 바인딩에서 실행이 느립니다. 지연 바인딩이 있습니다. "실행 시간"에 가상 바인딩으로 해결됩니다. 메소드 오버라이딩은 동적 바인딩의 예시입니다.

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

# 11. Entity 상속은 무엇인가요?

entity 상속은 클래스 상속과 유사하게 작동합니다. 때로는 많은 수의 엔티티가 있고 그 중 일부가 유사할 수 있습니다. 이 경우 공통 속성을 슈퍼타입으로 고려할 수 있고 이것을 부모 엔티티라고 말할 수 있습니다. 이는 하위 엔티티가 부모 엔티티를 상속할 때의 작업을 줄이는 데 도움이 됩니다.
예: 이름이라는 엔티티를 정의했고 그 속성으로 이름과 성을 가지고 있는데, 하위 엔티티인 직원과 고객은 이러한 속성을 상속할 수 있습니다.

# 12. Core Data에서는 멀티스레딩이 가능할까요?

네, Core Data에서 멀티스레딩이 가능합니다.

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

# 13. Deinit이란 무엇인가요?

DeInitializer는 클래스의 인스턴스가 확보 해제되기 직전에 호출됩니다. DeInitializer에서 사용하는 키워드는 초기화에 사용되는 Init 키워드와 유사한 deinit입니다. 중요한 점은 DeInitializer가 구조체 타입이 아닌 클래스 타입에만 사용 가능하다는 것입니다.

# 14. reuseIdentifier의 목적은 무엇인가요?

이미 할당된 객체의 재사용성입니다.

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

`reuseIdentifier`은 UITableView에서 모든 비슷한 행을 그룹화하는 데 사용됩니다.

### 15. UITableView를 처음으로로드할 때 몇 개의 UITableViewCells가 할당되나요? 테이블을 스크롤하면 몇 개가 더 할당되나요?

UITableView는 일반적으로 테이블에서 보이는 내용을 표시할만큼의 UITableViewCell 객체만 할당합니다. reuseIdentifier 덕분에 UITableView는 뷰로 스크롤되는 각 새로운 항목마다 새로운 UITableViewCell 객체를 할당하지 않아 렉이 걸리는 애니메이션을 피할 수 있습니다.

### 16. UITableViewCell 생성자에서 재사용 식별자를 왜 사용하나요?

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

만약 재사용할 식별자를 설정하지 않으면 TableView는 새로운 UITableViewCell을 할당하도록 강제합니다.
재사용 식별자의 사용 목적은 비슷한 행 내용을 UITableView에서 그룹화하는데 사용되지만 데이터 내용은 다를 수 있습니다.

## 17. 제네릭이란 무엇이며 어떤 문제를 해결하나요?

제네릭 코드는 재사용 가능하고 유연한 함수 및 어떤 유형이라도 작동할 수 있는 유형을 작성할 수 있도록 돕습니다. 제네릭은 Swift 언어의 가장 강력한 기능입니다.

## 18. Objective-C에서 @synthesize란 무엇인가요?

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

`@synthesize`는 컴파일러에게 변수에 대한 setter와 getter 프로퍼티를 생성하도록 하는 것이에요.

# 19. ARC란 무엇인가요? Swift에서 어떻게 메모리를 관리하는 데 도움이 되나요? 그 기능을 설명하기 위한 예시를 제시해 주세요.

ARC (Automatic Reference Count)는 iOS 애플리케이션에서 사용되며 애플리케이션 메모리를 추적하고 관리하는 데 도움을 줘요. Arc는 클래스 인스턴스가 더 이상 필요하지 않을 때 클래스 인스턴스가 보유한 메모리를 자동으로 해제해 줘요.

```swift
class Person {
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

명함: 문자열

init(이름: 문자열) '

self.name = name

print(“\(이름)이(가) 초기화되고 있습니다.”)

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

' deinit '

print("\"\(name) is being deinitialized.\"")

'

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

var person1: Person? = Person(name: “Alice”)

var person2: Person? = person1 // person1 and person2 now both reference the same Person instance

person1 = nil // The reference count of the Person instance decreases to 1

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

person2 = nil // Person 인스턴스의 참조 카운트가 0으로 감소하고 인스턴스가 해제됩니다.

이 예에서 person1과 person2가 동일한 Person 인스턴스에 할당될 때 참조 카운트가 2가 됩니다. person1과 person2가 모두 nil로 설정되면 참조 카운트가 0으로 떨어지고 Person 인스턴스가 해제되어 deinitializer가 트리거됩니다.

# 20. 라이브 렌더링이란?

인터페이스 빌더에서 사용되는 속성인 IBDesignable 및 IBInspectable은 특정 속성(색상/폭/그림자 및 테두리와 같은)이 스토리보드에서 라이브로 구성될 수 있도록 도와주며 인터페이스 빌더에서 해당 요소를 직접 렌더링하는 데 사용됩니다.

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

# 21. ‘assign’와 ‘retain’ 키워드의 차이점을 설명해주세요.

Retain — 객체 할당 시 retain을 호출해야 함을 명시합니다. 객체의 소유권을 가져갑니다.

Assign — 세터가 단순 할당을 사용한다는 것을 명시합니다. float, int와 같은 스칼라 유형의 속성에 사용됩니다.

# 22. iBeacons이란 무엇인가요?

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

iBeacon.com에서는 iBeacon을 Apple의 기술 표준으로 정의하며, 이를 통해 Mobile Apps이 물리적 세계에서 비콘에서 신호를 수신하고 그에 맞게 반응할 수 있습니다. iBeacon 기술을 통해 Mobile Apps은 마이크로 로컬 스케일에서 위치를 이해하고 위치에 기반한 하이퍼 컨텍스트 콘텐츠를 사용자에게 제공할 수 있습니다. 기술은 Bluetooth Low Energy를 기반으로 합니다.

Apple의 Bluetooth Low Energy(BLE) 무선 기술인 iBeacon은 iPhones 및 기타 iOS 사용자가 위치 기반 정보와 서비스를 받는 새로운 방법입니다.

# 23. Autorelease란 무엇인가요?

객체에 autorelease 메시지를 보내면 해당 객체는 로컬 AutoReleasePool에 추가됩니다. 그렇기 때문에 더 이상 해당 객체를 신경 쓸 필요가 없습니다. AutoReleasePool이 소멸될 때 (주 실행 루프에서), 해당 객체는 릴리스 메시지(참조 횟수가 하나 감소)를 받으며, RetainCount가 0이되면 가비지 수집기가 해당 객체를 제거합니다.

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

릴리스: `release` 메시지를 객체에 보낼 때 보유 카운트가 하나 감소합니다.

## 24. autorelease pool이란 무엇인가요?

객체에 `autorelease`가 보내질 때마다, 해당 객체는 가장 내부에 있는 autorelease 풀에 추가됩니다. 풀이 비워질 때, 풀에 있는 모든 객체에 대해 간단히 `release` 메시지를 보냅니다. `@autoreleasepool` 블록을 활용합니다.

autorelease 풀은 `release` 메시지를 "나중에"까지 보낼 수 있게 해주는 편의 기능입니다. 이 "나중에"는 여러 곳에서 발생할 수 있지만, Cocoa GUI 애플리케이션에서 가장 흔한 시점은 현재 런 루프 사이클의 끝입니다.

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

# 25. 블록에서 변수에 액세스하는 방법은?

\_\_block 저장 유형을 사용하면 됩니다.

만약 이 글을 즐겁게 읽으셨다면 공유하고 박수를 두드려주세요 👏🏻👏🏻👏🏻👏🏻👏🏻

또한 저와 연락할 수도 있습니다 📲

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

마크다운 형식으로 테이블 태그를 변경해 주세요.

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

아무 의견, 질문 또는 추천이 있으시면 아래 댓글 섹션에 자유롭게 남겨주세요💬

💁🏻‍♀️ 즐거운 코딩하세요!

감사합니다😊

Swiftfy를 팔로우하여 더 많은 업데이트를 받아보세요!

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

더 많은 기사를 보고 싶다면 참고하세요:
