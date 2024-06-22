---
title: "4단계로 iOS 앱에 실시간 활동 추가하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-AddLiveActivitiestoyouriOSappin4steps_0.png"
date: 2024-06-22 23:13
ogImage: 
  url: /assets/img/2024-06-22-AddLiveActivitiestoyouriOSappin4steps_0.png
tag: Tech
originalTitle: "Add Live Activities to your iOS app in 4 steps"
link: "https://medium.com/@blorenzop/live-activities-swift-6e95ee15863e"
---


## iOS에서 라이브 액티비티로 사용자 경험과 실시간 상호작용 향상

이미 라이브 액티비티를 앱에 통합하지 않았다면, 이제 강력한 잠재력을 고려해보는 것이 좋습니다.

라이브 액티비티는 호환되는 아이폰의 잠금 화면과 다이나믹 아일랜드에 자연스럽게 나타나는 전용 UI로, 이 기능을 활용하면 사용자가 앱의 주요 부분을 벗어나도 사용자와 상호작용을 유지할 수 있습니다.

푸시 알림을 보내지 않고 업데이트를 보내거나 실시간 정보를 표시할 수 있습니다. 그리고 가장 좋은 점은? 이를 추가하는 것이 놀랍도록 쉽습니다.

<div class="content-ad"></div>

여기에 그것을 하는 방법이 있어요.

![image1](/assets/img/2024-06-22-AddLiveActivitiestoyouriOSappin4steps_0.png)

![image2](https://miro.medium.com/v2/resize:fit:460/1*RqsG45Gbo625UNKcIUElTQ.gif)

## 단계1 — 라이브 활동 콘텐츠 정의

<div class="content-ad"></div>

가장 처음으로 할 일은 라이브 활동에서 보여줄 정보를 정의하는 것입니다. 제한된 공간 때문에 사용자가 중요하게 생각할 데이터에 집중해야 합니다.

그 생각을 기억하고 다음으로 할 일은 ActivityAttributes 프로토콜을 구현하는 것입니다.

```js
public protocol ActivityAttributes : Decodable, Encodable {

    /// 라이브 활동의 동적 콘텐츠를 설명하는 연관 타입.
    ///
    /// `ContentState`로 인코딩된 라이브 활동의 동적 데이터는 4KB를 초과할 수 없습니다.
    associatedtype ContentState : Decodable, Encodable, Hashable
}
```

알 수 있듯이, 우리는 정보를 두 가지 범주로 나눌 수 있습니다:

<div class="content-ad"></div>

- 정적 → ActivityAttributes를 구현하는 모델의 속성입니다.
- 동적 → ContentState 타입에 저장됩니다.

우리 경우에는 다음과 같이 정보를 정리할 수 있습니다:

- 정적 → 주문 번호입니다. 사용자가 주문을 하면 번호가 바뀌지 않습니다.
- 동적 → 주문 상태로, 주문의 현재 단계를 나타냅니다.

```js
struct OrderAttributes: ActivityAttributes {
    
    struct ContentState: Codable, Hashable {
        enum OrderStatus: Float, Codable, Hashable {
            case inQueue = 0
            case aboutToTake
            case making
            case ready
            
            var description: String {
                switch self {
                case .inQueue:
                    return "주문이 대기 중입니다"
                case .aboutToTake:
                    return "주문을 받을 준비 중입니다"
                case .making:
                    return "주문을 준비 중입니다"
                case .ready:
                    return "주문이 수령 가능합니다!"
                }
            }
        }
        
        let status: OrderStatus
    }
    
    let orderNumber: Int
}
```

<div class="content-ad"></div>

## 단계 2 — UI 생성하기

라이브 활동 UI는 앱의 위젯 범위에 있습니다. 따라서 앱에 위젯 확장 기능이 없는 경우, 먼저 하나를 생성해야 합니다.

새로운 위젯 뷰를 생성하고 위젯 구현에서 ActivityConfiguration의 인스턴스를 반환합니다. 이전 단계에서 만든 ActivityAttributes 모델을 사용해야 합니다.

```js
struct CoffeeShopWidgetLiveActivity: Widget {
    
    var body: some WidgetConfiguration {
        ActivityConfiguration(for: OrderAttributes.self) { context in
            // 여기에 잠금 화면/배너 UI가 들어갑니다
            LiveActivityView(state: context.state)
        } dynamicIsland: { context in
            // 동적 아일랜드 구현이 이곳에 들어갑니다
            // 이 기사의 범위를 벗어난 내용입니다
             ...
        }
    }
}
```

<div class="content-ad"></div>

LiveActivityView 내에서는 Live Activity UI를 구성합니다.

```js
struct LiveActivityView: View { 
    let state: OrderAttributes.ContentState
   
    var body: some View {
        VStack {
            HStack {
                Image(systemName: "cup.and.saucer")
                ProgressView(value: state.status.rawValue, total: 3)
                    .tint(.black)
                    .background(Color.brown)
                Image(systemName: "cup.and.saucer.fill")
            }
            .padding(16)
            
            Text("\(state.status.description)")
                .font(.system(size: 18, weight: .semibold))
                .padding(.bottom)
            Spacer()
        }
        .background(Color.brown.opacity(0.6))
    }
}
```

마지막으로, Live Activity 위젯을 위한 위젯 번들에 추가해야 합니다. 이 경우, 위젯을 제공하지 않을 것이므로 Live Activity만 사용합니다.

```js
@main
struct CoffeeShopWidgetBundle: WidgetBundle {
    var body: some Widget {
        CoffeeShopWidgetLiveActivity()
    }
}
```

<div class="content-ad"></div>

## 단계 3 — 라이브 활동 초기화

라이브 활동과 상호 작용하는 데는 ActivityKit API를 사용해야 합니다.

먼저, ActivityContent의 생성자인 init(state:staleDate:relevanceScore:)를 사용하여 라이브 활동의 초기 내용을 만들어야 합니다.

- state: 라이브 활동에 대한 초기 ActivityAttributes.ContentState입니다.
- staleDate: 라이브 활동이 오래되었음을 OS에 알리기 위한 날짜입니다. staleDate가 지정되지 않으면, 8시간 후 OS가 라이브 활동을 종료합니다.
- relevanceScore: 여러 개의 라이브 활동이 있는 경우, relevanceScore는 다이나믹 아일랜드에 표시할 우선순위와 잠금 화면의 순서를 나타냅니다.

<div class="content-ad"></div>

그럼 Activity 메서드 request(attributes: content: pushType:)를 호출하여 새로운 라이브 액티비티를 요청할 수 있어요.

- attributes: Step 1에서 생성한 ActivityAttributes의 인스턴스입니다.
- content: 라이브 액티비티의 초기 콘텐츠입니다.
- pushType: 라이브 액티비티의 업데이트가 ActivityKit 푸시 알림에서 올 것인지를 나타냅니다. 업데이트 기능만 사용할 경우에는 nil을 전달할 수 있어요.

📣 중요한 알림: 새로운 Activity를 요청할 때 앱은 활성화 상태여야 해요.

```js
let orderAttributes = OrderAttributes(orderNumber: 1)    
let initialState = OrderAttributes.ContentState(status: .inQueue)
let content = ActivityContent(state: initialState, staleDate: nil, relevanceScore: 1.0)

do {
    let orderActivity = try Activity.request(
        attributes: orderAttributes,
        content: content,
        pushType: nil
    )
} catch {
    print(error.localizedDescription)
}
```

<div class="content-ad"></div>

## 단계 4 — 라이브 활동 업데이트

라이브 활동을 업데이트하려면 update(_:) 함수를 사용할 수 있습니다. 초기 상태를 생성한 것과 마찬가지로 업데이트로 ActivityContent를 작성할 수 있습니다.

```js
await orderActivity?.update(
    ActivityContent<OrderAttributes.ContentState>(
        state: state,
        staleDate: nil
    )
)
```

라이브 활동을 종료하려면 end(_:dismissalPolicy:) 함수를 사용합니다. 사용하는 종료 정책에 따라 라이브 활동은 사용자가 명시적으로 제거할 때까지 잠금 화면에 남게될 수 있습니다. 이것이 마지막 라이브 활동 상태를 전달하여 UI를 항상 최신 상태로 유지해야하는 이유입니다.

<div class="content-ad"></div>

사용 가능한 해제 정책 옵션은 세 가지입니다.

- default: 라이브 액티비티는 최대 4시간 동안 잠금 화면에 유지됩니다. (사용자가 제거할 때까지)
- immediate: 운영 체제가 즉시 라이브 액티비티를 제거합니다.
- after(_ date:): 라이브 액티비티를 해제할 날짜를 지정할 수 있습니다. (라이브 액티비티 종료 시간으로부터 4시간 이내여야 함)

# 다음 단계는 무엇인가요?

앱의 사용 사례를 신중하게 고려해보세요. 라이브 액티비티는 배송 앱이나 스포츠 이벤트와 같은 실시간 애플리케이션에 특히 적합하지만, 푸시 알림에 대한 매력적인 대안으로도 사용될 수 있어 사용자에게 더 매력적인 경험을 제공할 수 있습니다.

<div class="content-ad"></div>

더 풍부한 사용자 경험을 위해 라이브 활동에 딥 링크를 통합하여 사용자가 라이브 활동을 탭할 때 연결이 원활하도록 할 수 있습니다.

질문이 있으시면 언제든지 메시지 남겨주세요! 🙂

- 🤓 iOS 개발 팁과 통찰을 정기적으로 제공하는 제 트위터에 참여해보세요.
- 🚀 나의 예제 프로젝트를 모두 공유하는 GitHub를 확인해보세요.