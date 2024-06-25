---
title: "AppDelegate, SceneDelegate, 그리고 SwiftUI 라이프사이클 이해하기"
description: ""
coverImage: "/assets/img/2024-05-20-UnderstandingAppDelegateSceneDelegateandSwiftUILifecycle_0.png"
date: 2024-05-20 16:21
ogImage:
  url: /assets/img/2024-05-20-UnderstandingAppDelegateSceneDelegateandSwiftUILifecycle_0.png
tag: Tech
originalTitle: "Understanding AppDelegate, SceneDelegate, and SwiftUI Lifecycle"
link: "https://medium.com/@hitesh.trivedi1987/understanding-appdelegate-scenedelegate-with-swiftui-lifecycle-46c19493a051"
---

<img src="/assets/img/2024-05-20-UnderstandingAppDelegateSceneDelegateandSwiftUILifecycle_0.png" />

AppDelegate는 무엇인가요?

iOS의 이전 버전에서, iOS 13 이전에는 AppDelegate라는 것이 있었습니다. 이것은 앱의 보스 역할을 했고, 앱이 시작하거나 중지되거나 백그라운드로 이동할 때 관리를 했습니다. 앱 시작 및 백그라운드 모드 같은 중요한 이벤트를 다뤘습니다.

```swift
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(_ application: UIApplication, didFinishLaunchingWithOptions
                     launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // 애플리케이션 시작 후 커스터마이징을 위한 오버라이드 포인트입니다.
        return true
    }

    func applicationWillResignActive(_ application: UIApplication) {
        /*
         애플리케이션이 비활성 상태로 이동할 때 전송됩니다. 일시적으로 인터럽트(예: 전화 통화 또는 SMS 메시지 수신)가 발생하거나 사용자가 애플리케이션을 종료하고 백그라운드 상태로 전환될때 발생할 수 있습니다.
         계속 중인 작업을 일시 중지하고 타이머를 비활성화하며 그래픽 렌더링 콜백을 무효화하는 데 이 메서드를 사용합니다. 게임은 게임 일시 중지에 이 방법을 사용해야 합니다.
         */
    }

    func applicationDidEnterBackground(_ application: UIApplication) {
        /*
        공유 리소스를 해제하고 사용자 데이터를 저장하며 타이머를 무효화하며 애플리케이션 상태 정보를 충분히 저장하여 나중에 종료됐을 때 애플리케이션을 현재 상태로 복원하는 데 이 메서드를 사용합니다.
        애플리케이션이 백그라운드 실행을 지원하는 경우, 이 방법은 사용자가 종료할 때 applicationWillTerminate: 대신 호출됩니다.
         */
    }

    func applicationWillEnterForeground(_ application: UIApplication) {
        /*
        백그라운드에서 활성 상태로 전환하는 일부로 호출됩니다;
        여기서 백그라운드 진입 때에 수행되었던 변경을 취소할 수 있습니다.
         */
    }

    func applicationDidBecomeActive(_ application: UIApplication) {
        /*
        애플리케이션이 비활성 상태일 때 일시 중지된 작업(또는 아직 시작되지 않은 작업)을 다시 시작합니다.
        애플리케이션이 이전에 백그라운드에 있었을 경우 선택적으로 사용자 인터페이스를 새로 고칩니다.
         */
    }

    func applicationWillTerminate(_ application: UIApplication) {
        /*
        애플리케이션이 종료되기 직전에 호출됩니다.
        적절하다면 데이터를 저장합니다. 또한 applicationDidEnterBackground:도 참조하십시오.
         */
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

SceneDelegate- 뭐야?

iOS 13가 출시되었을 때 Apple은 SceneDelegate라는 새로운 기능을 추가했어. 이건 주로 iPad를 위한 거였는데, 이를 통해 다중 창을 사용할 수 있게 됐어. AppDelegate의 일부 업무가 SceneDelegate로 넘어가게 됐어. 그래서 이제 두 개가 함께 앱을 관리하게 되었어.

AppDelegate.swift

```js
import UIKit

@main
class AppDelegate: UIResponder, UIApplicationDelegate {

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // 애플리케이션 시작 후 커스터마이징할 때 오버라이드 포인트
        return true
    }

    // MARK: UISceneSession 생명주기

    func application(_ application: UIApplication, configurationForConnecting connectingSceneSession: UISceneSession, options: UIScene.ConnectionOptions) -> UISceneConfiguration {
        // 새로운 씬 세션을 생성할 때 호출
        // 이 메소드를 사용하여 새 씬을 만들기 위한 구성을 선택해
        return UISceneConfiguration(name: "Default Configuration", sessionRole: connectingSceneSession.role)
    }

    func application(_ application: UIApplication, didDiscardSceneSessions sceneSessions: Set<UISceneSession>) {
        // 사용자가 씬 세션을 삭제했을 때 호출
        // 응용프로그램이 실행 중이 아닌 경우 어떤 세션이 삭제되었다면, application:didFinishLaunchingWithOptions 이후에 이 메소드가 호출될 거야
        // 폐기된 씬에 특정한 리소스를 해제하는 데 이 메소드를 사용해, 다시 돌아오지 않을 거니까
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

SceneDelegate.swift

```swift
import UIKit

class SceneDelegate: UIResponder, UIWindowSceneDelegate {

    var window: UIWindow?


    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        // 이 메서드를 사용하여 UIWindow 'window'를 선택적으로 구성하고 UIWindowScene 'scene'에 연결합니다.
        // Storyboard를 사용하는 경우 'window' 속성은 자동으로 초기화되어 scene에 연결됩니다.
        // 이 델리게이트는 연결되는 scene이나 세션이 새로운 것임을 의미하지 않습니다 (대신 'application:configurationForConnectingSceneSession' 참조).
        guard let _ = (scene as? UIWindowScene) else { return }
    }

    func sceneDidDisconnect(_ scene: UIScene) {
        // 시스템에 의해 해제되는 즉시 scene이 호출됩니다.
        // scene이 백그라운드로 들어가거나 해당 세션이 버려질 때 발생합니다.
        // 이 scene과 관련된 재생성 가능한 리소스를 해제합니다.
        // scene이 다시 연결될 수 있으므로 세션이 반드시 버려지지는 않습니다('application:didDiscardSceneSessions' 참조).
    }

    func sceneDidBecomeActive(_ scene: UIScene) {
        // scene이 비활성 상태에서 활성 상태로 전환된 경우에 호출됩니다.
        // scene이 비활성 상태였을 때 일시 중단되었던 작업을 다시 시작하는 데 이 메서드를 사용합니다.
    }

    func sceneWillResignActive(_ scene: UIScene) {
        // scene이 활성 상태에서 비활성 상태로 전환될 때 호출됩니다.
        // 일시적인 중단(예: 전화 통화 수신)으로 인해 발생할 수 있습니다.
    }

    func sceneWillEnterForeground(_ scene: UIScene) {
        // scene이 백그라운드에서 전경으로 전환될 때 호출됩니다.
        // 백그라운드로 전환할 때 발생한 변경 사항을 취소하는 데 이 메서드를 사용합니다.
    }

    func sceneDidEnterBackground(_ scene: UIScene) {
        // scene이 전경에서 백그라운드로 전환될 때 호출됩니다.
        // 데이터를 저장하고 공유 리소스를 해제하며 충분한 scene별 상태 정보를 저장하여
        // scene을 현재 상태로 복원하는 데 이 메서드를 사용합니다.
    }

}
```

차이점은 SceneDelegate가 여러 창을 다루는 반면에 AppDelegate는 여전히 평소 역할을 합니다. 따라서 이제 앱은 다른 창에서 다른 내용을 동시에 표시하는 것과 같이 더 많은 일을 할 수 있습니다.

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

그리고 SwiftUI는요?

SwiftUI를 사용하면 Apple의 새로운 앱 개발 방법으로, 더 이상 AppDelegate 또는 SceneDelegate가 필요하지 않을 수도 있어요. 대신에 Notification Event로 대부분의 작업을 처리할 수 있어요. 하지만 만약 프로젝트에 아직 AppDelegate 또는 SceneDelegate가 있다면, 코드를 깔끔하고 조직적으로 유지하는 데 도움이 될 거예요.

그래서, 이것이 다른 iOS 버전에서 앱의 주요 매니저들이 작동하는 방식이에요.

SwiftUI에서 AppDelegate를 구현하세요.

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

- AppDelegate.swift 파일을 만듭니다.

새 파일 추가로 이동 - `Cocoa Touch Class`를 선택합니다.

![이미지](/assets/img/2024-05-20-UnderstandingAppDelegateSceneDelegateandSwiftUILifecycle_1.png)

이름(AppDelegate)을 입력하고 NSObject의 subclass를 선택합니다.

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

![UnderstandingAppDelegateSceneDelegateandSwiftUILifecycle](/assets/img/2024-05-20-UnderstandingAppDelegateSceneDelegateandSwiftUILifecycle_2.png)

Click on “Next” will create Appdelegate.swift file which look like below

```swift
import UIKit
class AppDelegate: NSObject {

}
```

2. With following above process again we can create SceneDelegate.swift file.

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

![UnderstandingAppDelegateSceneDelegateandSwiftUILifecycle_3](/assets/img/2024-05-20-UnderstandingAppDelegateSceneDelegateandSwiftUILifecycle_3.png)

AppDelegate.swift 파일과 처음으로 나타날 것입니다.

```swift
import UIKit
class SceneDelegate: NSObject {

}
```

이제 AppDelegate.swift와 SceneDelegate.swift 파일에 적용해야 할 실제 변경 사항을 아래에 안내해드립니다.

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

AppDelegate.swift

```swift
class AppDelegate: NSObject, UIApplicationDelegate {

     func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey : Any]? = nil) -> Bool {
         return true
     }

     func application(_ application: UIApplication, configurationForConnecting connectingSceneSession: UISceneSession, options: UIScene.ConnectionOptions) -> UISceneConfiguration {

         let sceneConfig : UISceneConfiguration = UISceneConfiguration(name: nil, sessionRole: connectingSceneSession.role)
         sceneConfig.delegateClass = SceneDelegate.self
         return sceneConfig

     }
 }
```

UIApplicationDelegate은 iOS 개발에서 사용되는 프로토콜로, iOS 애플리케이션의 라이프사이클 이벤트를 관리하고 응답하는 일련의 메서드를 정의합니다.

이 프로토콜은 앱 시작, 종료, 백그라운드 또는 포그라운드 진입, 푸시 알림 수신 등과 같은 이벤트를 처리하기 위한 중앙 허브 역할을 합니다. 제공된 코드의 AppDelegate와 같이 이 프로토콜을 준수하는 클래스는 이러한 메서드를 구현하여 애플리케이션의 동작을 라이프사이클 이벤트에 따라 맞춤화할 수 있습니다.

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

- 첫 번째 함수인 application(\_:didFinishLaunchingWithOptions:)은 앱이 로딩을 마칠 때 호출됩니다. 이 함수는 실행이 성공적인지 여부를 나타내는 부울 값이 반환됩니다.
- 두 번째 함수인 application(\_:configurationForConnecting:options:)은 새로운 씬 세션을 생성할 때 호출됩니다. 이 함수는 씬의 속성을 구성하는 데 도움이 되는 UISceneConfiguration 객체를 반환합니다. 여기서 UISceneConfiguration 객체를 생성하고 이 씬 세션의 구성을 처리할 SceneDelegate 클래스를 해당 UISceneConfiguration 객체로 할당합니다.

SceneDelegate.swift

```js
import SwiftUI

class SceneDelegate: NSObject, UIWindowSceneDelegate {

    var window: UIWindow?

    func scene(_ scene: UIScene, willConnectTo
               session: UISceneSession, options
               connectionOptions: UIScene.ConnectionOptions) {
        guard let _ = scene as? UIWindowScene else {return}
    }

    func sceneDidBecomeActive(_ scene: UIScene) {

    }

    func sceneDidEnterBackground(_ scene: UIScene) {

    }

    func sceneWillEnterForeground(_ scene: UIScene) {

    }

    func sceneWillResignActive(_ scene: UIScene) {

    }
}
```

UIWindowSceneDelegate 프로토콜은 씬의 라이프사이클 이벤트에 응답하고 앱의 창 씬 동작을 관리할 수 있도록 해줍니다. 씬의 생성, 소멸, 상태 복원 및 씬 관련 이벤트를 처리하기 위한 구조화된 방법을 제공하여 견고하고 반응성 있는 iOS 애플리케이션을 만들 수 있습니다. 여기에 대한 설명:

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

1. scene(\_:willConnectTo:options:): 새 창 장면이 연결되기 전에 호출되는 메서드입니다. 일반적으로 장면의 초기 사용자 인터페이스를 설정하는 곳입니다.

2. sceneDidBecomeActive(\_:): 창 장면이 활성 상태가 되면 트리거됩니다. 이는 사용자 상호 작용을 위해 준비된 상태를 의미합니다. 여기서 애니메이션을 시작하거나 작업을 재개할 수 있습니다.

3. sceneWillResignActive(\_:): 창 장면이 활성 상태를 잃기 직전에 호출됩니다. 사용자가 전화를 받거나 다른 앱으로 전환할 때 발생할 수 있습니다.

4. sceneDidEnterBackground(\_:): 이 메서드는 창 장면이 백그라운드로 전환될 때 호출됩니다. 여기서 앱의 현재 상태를 저장하거나 진행 중인 작업을 일시 중지하는 데 사용할 수 있습니다.

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

5. sceneWillEnterForeground(\_:): 화면이 백그라운드에 있었다가 다시 활성화될 때 호출됩니다. 여기서 사용자가 돌아오면 사용자 인터페이스를 준비할 수 있습니다.

이러한 메서드를 사용하면 앱의 창 화면이 수명주기 동안의 동작을 사용자 정의하여 부드럽고 반응적인 사용자 경험을 보장할 수 있습니다.

지금 SwiftUI 앱 라이프사이클에 AppDelegate를 연결해보세요.

```swift
import SwiftUI

@main
struct SwiftUIDemoApp: App {

    @UIApplicationDelegateAdaptor(AppDelegate.self) private var appdelegate

    var body: some Scene {
        WindowGroup {
            ContentView()
        }
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

SwiftUI 앱에서는 App 구조체에서 AppDelegate 인스턴스에 접근할 수 있습니다. 이때 사용하는 프로퍼티 래퍼는 UIKit 앱에서 UIWindowSceneDelegate와 유사한 방식으로 작동합니다. 그러나 여기서 사용해야 하는 프로퍼티 래퍼는 @UIApplicationDelegateAdaptor입니다.

- @UIApplicationDelegateAdaptor(AppDelegate.self)은 AppDelegate 인스턴스에 액세스할 수 있는 프로퍼티 래퍼입니다.
- private var appDelegate는 AppDelegate 인스턴스에 대한 참조를 보유하는 프로퍼티입니다.

이 Github URL을 시도해보세요! 궁금한 점이 있으면 언제든지 물어보세요.

NEXT: SwiftUI를 사용하여 변수를 정의하고 키워드를 입력하는 방법에 대해 공유할게요.
