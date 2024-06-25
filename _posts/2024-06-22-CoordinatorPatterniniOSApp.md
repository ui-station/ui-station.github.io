---
title: "iOS 앱에서 코디네이터 패턴 사용하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-CoordinatorPatterniniOSApp_0.png"
date: 2024-06-22 23:06
ogImage:
  url: /assets/img/2024-06-22-CoordinatorPatterniniOSApp_0.png
tag: Tech
originalTitle: "Coordinator Pattern in iOS App"
link: "https://medium.com/@bondarenkotatiana96/coordinator-pattern-in-ios-app-bbdee8946cc2"
---

# 코디네이터란 무엇인가요?

iOS 개발에서 코디네이터는 앱의 탐색 흐름을 처리하는 객체입니다. 때로는 "플로우 컨트롤러" 또는 "라우터"라고도 합니다.

코디네이터는 뷰 컨트롤러가 어떻게 표시되고, 푸시되며 해제되는지, 앱의 다른 섹션 및 뷰가 어떻게 연결되는지에 대한 로직을 처리합니다.

코디네이터는 보통 정의하는 클래스이며, 뷰 컨트롤러를 생성, 표시, 해제할 수 있는 메서드와 함께 정의됩니다. 앱의 흐름이 복잡한 경우에는 다른 코디네이터와 필요한 경우 통신할 수도 있습니다.

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

# MVVM+C 패턴이란 무엇인가요?

일반적인 iOS 개발(예: MVC 패턴)에서는 일반적으로 네비게이션의 책임이 뷰 컨트롤러에게 주어집니다. 그러나 더 복잡한 플로우(예: 뷰 컨트롤러 간의 강한 결합, 코드 재사용성의 부족 및 테스트 문제 등)에서 많은 문제가 발생할 수 있습니다.

MVVM+C(가끔 "Coordinator 패턴"이라고도 부릅니다)은 앱 내에서 책임을 분리하는 것을 촉진하는 디자인 패턴입니다. 특히 네비게이션 및 플로우 제어에 있어서 책임을 분리하도록 권장합니다.

간단히 설명하면, 이 패턴에서 ViewModel은 데이터를 UI 요소에 바인딩하고 데이터가 변경되는 즉시 UI를 변경합니다. ViewController의 책임은 UI에서 발생하는 이벤트를 ViewModel에 알리는 것이므로 ViewModel이 해당 이벤트에 적절히 반응할 수 있습니다. 그리고 Coordinator가 네비게이션 로직을 처리합니다.

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

이 패턴에 대한 훌륭한 기사가 있어요. 꼭 읽어보시길 추천합니다!

## 코디네이터를 사용하는 장점은 무엇인가요?

- 관심사 분리 (각 모듈이 단일 책임을 갖습니다)
- 거대한 ViewControllers와 ViewModels 피하기
- UI를 가능한 가장 덤하고 재사용 가능하게 유지
- RX-Swift, Combine과 같은 반응적인 프레임워크와 뛰어난 호환성을 보여줍니다!

## 코드 예시

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

여기에는 “MainCoordinator”가 네비게이션 스택에 “ViewController”를 푸시하는 간단한 예제가 있습니다.

“SecondViewController”로 이동하려면 해당하는 코디네이터에서 네비게이션을 처리하는 “goToSecondVC” 메서드를 호출하면 됩니다. 이렇게 하면 뷰 컨트롤러가 자체적으로 네비게이션을 처리할 필요가 없어집니다.

```js
// 먼저, 코디네이터를 위한 프로토콜을 정의해봅시다

protocol Coordinator {
    var childCoordinators: [Coordinator] { get set }
    var navigationController: UINavigationController { get set }

    func start()
}


// 그 다음, 코디네이터(MainCoordinator)를 생성해봅시다

class MainCoordinator: Coordinator {
    var childCoordinators = [Coordinator]()
    var navigationController: UINavigationController

    init(navigationController: UINavigationController) {
        self.navigationController = navigationController
    }

    func start() {
        let vc = ViewController.instantiate()
        vc.coordinator = self
        navigationController.pushViewController(vc, animated: false)
    }

    func goToSecondVC() {
        let secondVC = SecondViewController.instantiate()
        secondVC.coordinator = self
        navigationController.pushViewController(secondVC, animated: true)
    }
}

// 마지막으로, "AppDelegate"나 "SceneDelegate"에서
// 메인 코디네이터를 인스턴스화하고 시작하는 방법입니다

var mainCoordinator: MainCoordinator?

func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    let navController = UINavigationController()
    mainCoordinator = MainCoordinator(navigationController: navController)
    mainCoordinator?.start()

    window = UIWindow(frame: UIScreen.main.bounds)
    window?.rootViewController = navController
    window?.makeKeyAndVisible()
    return true
}
```

읽어 주셔서 감사합니다!

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

![Coordinator Pattern in iOS App](/assets/img/2024-06-22-CoordinatorPatterniniOSApp_0.png)
