---
title: "매월 1,338달러 수익을 올리는 iOS 앱 만드는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_0.png"
date: 2024-06-23 01:43
ogImage: 
  url: /assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_0.png
tag: Tech
originalTitle: "Build an iOS App with a revenue 1,338$   mo"
link: "https://medium.com/@fakiho/build-an-ios-app-with-a-revenue-1-338-mo-196dbab78a31"
---


완전한 튜토리얼이 함께 하면서 한 단계씩 안내해 드릴게요

![iOS 앱 빌드 수익 내기](/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_0.png)

# 월 수익

오랜 시간 동안 이 앱에 작업해온 결과, 매달 수익이 약 1300달러 정도 나오고 있습니다. 개발자들과 경험을 공유하고, 저와 같은 수익을 얻을 수 있도록 도와주고 싶어요.

<div class="content-ad"></div>

마스 1부터 7까지의 수익, 광고 수익은 제외합니다.

![Image](/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_1.png)

여기서 전체 수익을 확인할 수 있습니다.

![Image](/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_2.png)

<div class="content-ad"></div>

## 시작하기 전에

이 튜토리얼은 고급 Swift를 배우고 싶은 소프트웨어 개발자를 대상으로 설계되었습니다. 이 튜토리얼은 Swift에 대한 충분한 이해와 어떻게 앱을 처음부터 만드는지에 대한 지식을 제공할 것입니다. 저는 개발자들이 깔끔한 코드로 자신만의 앱을 만드는 데 도움을 주고 싶어합니다. 대부분의 튜토리얼이 앱을 만드는 데 정말 좋은 참고 자료가 아니라는 것을 감안할 때, 여기서는 MVVM 아키텍처를 따르고 인앱 구매를 통합하는 방법에 대한 완전한 튜토리얼을 볼 수 있을 것입니다.

이 튜토리얼을 진행하기 전에 컴퓨터 프로그래밍 용어와 프로그래밍 언어의 지식을 가지고 있어야 합니다.

따라서, 초보자이고 Swift에 대해 많이 알지 못하는 경우에도 따라올 수 있지만, 더 많이 Swift에 대해 알고 시작하는 것이 가장 좋습니다. 왜냐하면 우리는 기본적인 Swift 세부 정보에 대해 다루지 않을 것이기 때문입니다.

<div class="content-ad"></div>

![image1](/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_3.png)

# 애플리케이션 아키텍처

소프트웨어를 개발할 때 디자인 패턴 뿐만 아니라 아키텍처 패턴 또한 중요합니다. 소프트웨어 공학에서는 다양한 아키텍처 패턴이 있습니다. 모바일 소프트웨어 공학에서 가장 널리 사용되는 것은 MVVM, Clean architecture 및 Redux 패턴입니다.

![image2](/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_4.png)

<div class="content-ad"></div>

## 여기에서 사용된 아키텍처 개념들

- Clean Architecture [여기를 클릭하여 참고](https://blog.cleancoder.com/uncle-bob/2012/08/13-the-clean-architecture.html)
- 고급 iOS 앱 아키텍처 [여기를 클릭하여 참고](https://www.raywenderlich.com/8477-introducing-advanced-ios-app-architecture)
- MVVM
- 데이터 바인딩
- 의존성 주입
- SwiftUI 및 UIKit 뷰 구현: 동일한 ViewModel 재사용 (최소 Xcode 11 필요)

## 요구 사항

- Xcode 버전 11.2.1 이상, Swift 5.0 이상

<div class="content-ad"></div>

# VPN

VPN은 가상 사설 네트워크를 의미합니다. 다가오는 기사에서 VPN 서버를 설치하는 방법을 알아볼 것입니다. 앱을 개발하기 위해 iOS에서 사용할 수 있는 VPN 프로토콜을 정의할 것입니다. 애플 문서에 따르면 개인 VPN을 위해 IKEv2 및 IPsec 프로토콜을 지원할 수 있습니다. 네트워크 확장 및 능력에 대해 알아볼 것을 제안합니다.

이 앱 개발을 시작했을 때 안드로이드와 iOS 두 플랫폼을 대상으로 지정하고 싶었습니다. 따라서 둘 다 작동할 수 있는 VPN 프로토콜을 사용하고 싶었고 오랜 시간을 투자해 조사하고 시도한 결과 IKEv2를 사용하는 것이 최선임을 알게 되었습니다.

IKEv2가 무엇인지 이곳에서 확인할 수 있습니다.

<div class="content-ad"></div>

# 코딩을 시작해봐요 🎉 💻

<img src="/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_5.png" />

이렇게 마크다운 형식으로 코드를 작성하면 가독성이 높아지고 좋아요! 코딩을 시작하기 전에 앱에 무엇을 원하는지에 대해 조금 이야기해봐요! 간단하게 3개의 씬에서 작업하고 스토리보드를 사용할 거에요 🤒

- OnboardigViewController
- DashboardViewController
- InAppPurchaseProViewController

<div class="content-ad"></div>

<img src="/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_6.png" />

## 코딩

새 프로젝트를 만들고 원하는 이름을 지정할 수 있지만, 이 튜토리얼에서는 VPN Guard와 Secure VPN이라는 2개의 이름이 있습니다. VPN Guard는 여기서 참조로 사용할 Secure VPN을 기반으로 합니다.

프로젝트를 명확하게 유지하는 가장 좋은 방법은 폴더를 구성하는 것이니, 우선 그 폴더들을 생성해 봅시다.

<div class="content-ad"></div>


![Screenshot 1](/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_7.png)

After grouping all the layers we have: Domain, Presentation, and Data Layers.

![Screenshot 2](/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_8.png)

The domain layer is totally isolated, the innermost part of the onion. This layer can be reused in other projects.


<div class="content-ad"></div>

프레젠테이션 레이어에는 뷰 컨트롤러와 뷰 모델, XIB, SwiftUI 뷰 등 UI가 포함되어 있습니다. 뷰는 한 개 또는 여러 개의 유즈 케이스를 실행하는 뷰모델에 의해 조정됩니다. 프레젠테이션 레이어는 도메인 레이어에만 의존합니다.

데이터 레이어에는 리포지토리 구현과 한 개 또는 여러 개의 데이터 소스가 포함됩니다. 리포지토리는 로컬 또는 외부의 다른 데이터 소스에서 데이터를 조정하는 역할을 합니다. 프레젠테이션 레이어와 마찬가지로 도메인 레이어에만 의존하며 매핑 및 디코딩 로직을 포함할 수도 있습니다.

![이미지](/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_9.png)

iOS에서 아키텍처 패턴에 대해 더 알고 싶으시면 여기에 댓글을 남겨주시면 더 자세한 내용의 아키텍처 패턴에 대한 다른 이야기를 올릴게요.

<div class="content-ad"></div>

## 대시보드 뷰 컨트롤러

이제 대시보드 디자인을 구축할 수 있습니다. 가운데에 버튼이 있을 것입니다. 사용자가 해당 버튼을 탭하면 VPN 서버와의 연결을 설정할 것입니다.

UILabel은 연결 상태를 표시할 것입니다.

상단의 UIImage/UILabel은 사용자가 무료 또는 프로인지 보여줍니다.

<div class="content-ad"></div>

가운데에 VPN 서버의 국가 이름과 함께 국기 아이콘을 표시할 수 있습니다. 이 위에는 파이어베이스에서 가져온 VPN 서버 목록을 트리거하는 UIButton이 있을 것입니다.

현재 공개 IP 주소를 표시하는 UILabel입니다.


![이미지](/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_10.png)


AppDIContainer와 연결된 스토리보드와 인스턴스화할 viewController를 연결하기 위해 StoryboardInstantiable 프로토콜을 만들 것입니다.

<div class="content-ad"></div>

`instantiateViewController` 메서드는 fileName을 확인하며 이는 스토리보드 `DashboardViewController.swift`, `DashboardViewController.storyboard`의 동일한 이름이어야 하고 해당 스토리보드에서 ViewController를 생성하고 반환합니다.

따라서, 우리의 DashboardViewController는 StoryboardInstantiable 프로토콜을 준수하며 DashboardViewController를 반환할 클래스 메서드를 정의할 것입니다.

```swift
final class func create(viewModel: DashboardViewModel) -> DashboardViewController {
```

```swift
let view = DashboardViewController.instantiateViewController()
    return view
}
```

<div class="content-ad"></div>

우리는 ViewModel을 할당하기 위해 이 함수를 사용할 것입니다.

연결 버튼을 viewController와 연결하고 클로저 내에서 액션 아웃렛으로 설정한 후에, 다음의 코드 라인을 추가합니다:

```js
viewModel.connectDisconnect()
```

이렇게 하면 물론 ViewModel이 없다는 오류가 발생할 것입니다. 걱정 마세요. 우리의 목표는 DashboardViewController용 ViewModel을 생성하는 것이기 때문에, viewController 내부에서 정의를 해볼 겁니다.

<div class="content-ad"></div>


```js
private(set) var viewModel: DashboardViewModel!
```

그리고 이것은 DashboardViewModel 파일을 생성합니다. 이전에 말한대로 ViewModel은 하나 이상의 사용 사례를 가지므로, 뷰 모델의 책임은 서버에 연결/연결 해제를 트리거하는 것에 대해 고려해야 합니다.

DashboardViewModel을 위해서 우리는 입력/출력을 정의합니다.

이렇게 함으로써, 이 viewModel이 무엇을 할지 전체 아이디어를 가지게 될 것이며 나중에 필요하다면 새로운 기능을 추가할 수 있는 기회를 얻게 됩니다. 현재 VM의 로직이 복잡하기 때문에 새로운 것을 만들어도 기능은 변경되지 않을 것입니다.


<div class="content-ad"></div>

DashboardViewModelRoute는 이름에서 알 수있듯이 라우트를 포함하는 enum입니다.

DashboardViewModelLoading은 서버에 연결하거나 API에서 데이터를 가져올 때 현재 상태를 View에 알릴 때 사용하는 enum입니다.

여기서 viewModel을 엿볼 수 있습니다.

## 사용 사례

<div class="content-ad"></div>

이제 대시보드 뷰컨트롤러와 그 뷰모델이 준비되었으니, 사용 사례를 시작할 때입니다. 사용 사례는 도메인 레이어에 추가됩니다. 따라서 NetworkVPNUseCase를 만듭니다.

VPN의 주요 사용 사례는 연결, 연결 해제, 구성 로드, 상태 가져오기이며, 서버를로드하기 위해 API를 사용하는 경우 서버 가져오기를 추가할 수 있습니다.

다음과 같이 원하는 모든 경우를 포함하는 프로토콜을 정의합니다:

NetworkVPNUseCase에 준수하는 DefaultNetworkVPNUseCase라는 final 클래스를 만듭니다. 아직 Data 레이어에 주입될 Repository가 누락되어 있습니다. 이 Repository에는 NetworkVPNUseCase의 동일한 경우가 있을 것입니다. 따라서 도메인에 이를 추가하겠습니다.

<div class="content-ad"></div>

경로: Domain/interfaces/Repositories/

DefaultNetworkVPNUseCase로 돌아가서 레포지토리의 private 속성을 선언합니다.

```js
private(set) var vpnManager: DVPNRepository
```

여기서 사용 사례 클래스의 최종 모습을 찾을 수 있습니다.

<div class="content-ad"></div>

알다시피, 도메인 레이어는 격리되어 있고 내부에서는 제3자를 사용하지 않습니다. 따라서 NEVPNStatus에서 매핑할 VPN 상태 NetworkVPNStatus를 처리하기 위해 특별한 enum을 만들었습니다.

이제 NetworkVPNUseCase를 완료한 후에는 DashboardViewModel로 돌아가서 사용 사례를 주입하면 됩니다. 곧 다시 그 부분으로 돌아갈 겁니다.

## 데이터

데이터 레이어 내에서 VPNRepository 및 VPNManager를 생성하여 연결 설정, 구성 로드 및 연결 해제 과정을 모두 처리할 것입니다.

<div class="content-ad"></div>

## 1- IPSec/IKEv2 연결

먼저 앱에 새로운 기능을 추가해야 합니다. 따라서 프로젝트를 선택하여 로그인 및 기능 설정으로 이동하세요.

![앱이미지](/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_11.png)

왼쪽 상단의 기능을 클릭하고 개인 VPN을 추가하세요.

<div class="content-ad"></div>

아래는 테이블 형식을 Markdown 형식으로 바꿔주세요.

| 이미지 |
|:---:|
| <img src="/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_12.png" /> |
| Keychain 공유 추가 |
| <img src="/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_13.png" /> |
| Keychain에는 keychain 그룹 도메인을 추가하여 필요한 내용을 추가해주세요. |

<div class="content-ad"></div>

<img src="/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_14.png" />

## 설정

다행히도 애플은 타사 라이브러리 없이 IPsec/IKEv2를 사용하여 VPN에 연결할 수 있는 좋은 API 세트를 제공합니다.

코딩을 시작하기 전에 어떻게 작동하는지 설명해 드릴게요. DefaultVPNManager는 다음을 수행할 것입니다:

<div class="content-ad"></div>

1. 환경 설정 불러오기

2. 새로운 값으로 환경 설정 변경하기 (사용자 이름, 비밀번호, 호스트 등)

3. 환경 설정 저장하기

4. 연결 시작하기

<div class="content-ad"></div>

설정이 없는 경우에도 먼저 선호도를 로드한다는 것이 이상하게 들릴 수 있습니다. 그러나 이것이 애플이 일을 하는 방법입니다. 만약 로드하기 전에 설정을 저장했다면, 프로세스가 실패할 것입니다.

비밀번호와 공유 키는 키체인에 저장됩니다. 나중에 키체인에 관한 기사를 쓸 예정이니, 준비되면 언제 인터넷에 올릴지 물어봐주세요 🙂 혹은 트위터에서 저를 팔로우해주세요 🐦(아직 제 계정은 새로운 상태입니다).

![image](/assets/img/2024-06-23-BuildaniOSAppwitharevenue1338mo_15.png)

DefaultVPNManager는 Data/VPNManager/Repository에 위치한 VPNRepository를 준수할 것입니다. 이 프로토콜은 속성과 함수를 보유할 것입니다.

<div class="content-ad"></div>

NEVPNManager은 여기에서 필요한 것입니다. VPN 구성을 생성하고 관리할 수 있는 능력을 제공합니다. Apple 문서를 여기에서 확인하세요. 🍏

NEVPNManager의 인스턴스를 가지려면 NEVPNManager 프로토콜의 확장을 만들었습니다. 이것은 코딩을 하는 동안 우리의 삶을 좀 더 쉽게 해줄 것입니다.

매니저를 호출할 때 NEVPNManager에 대한 참조를 계산된 값으로 얻게 됩니다. 더 많은 세부 정보는 여기에서 Swift의 속성에 대해 확인하세요.

상태는 VPN의 현재 상태를 반환하며, 이것이 왜 새로운 enum을 생성하여 도메인 레이어 내에서 NetworkExtension을 사용하지 않을 이유입니다.

<div class="content-ad"></div>

`registerNotification()` 함수는 VPN 상태가 변경될 때 알림을 받습니다. NotificationCenter를 사용하는 이유는 `vpnManager.connection.startVPNTunnel()` 메서드가 성공해도 실제로 연결이 성립된 것은 아니고 VPN 터널 설정이 성공적으로 시작된 것을 의미하기 때문입니다. 따라서 실제 상태를 얻기 위해 우회 처리를 해야 했습니다. 곧 몇 분 안에 작동 방식을 확인하실 수 있을 거에요.

DefaultVPNManager로 돌아가서 여기서 확인할 수 있습니다. `#if targetEnvironment(simulator)`와 같은 컴파일 조건이 있는 것을 알게 될 거에요. 아쉽게도 시뮬레이터에서 VPN이 작동하지 않기 때문에 UI 디버깅 중 충돌을 방지하기 위해 추가했습니다.

또한, `manager.loadFromPreferences`와 `manager.saveToPreferences` 콜백은 비동기적으로 동작합니다.

구성을 저장할 때, 먼저 사용 중인 프로토콜을 정의해야 합니다. 제 경우, 어플리케이션은 IPSec 및 IKEv2 둘 다 허용할 수 있습니다. 따라서 여기서 서버의 프로토콜 유형을 확인하여 해당 유형에 맞게 연결 및 구성을 시도하고 있습니다.

<div class="content-ad"></div>

IPSec에 대해:

```js 
경우 .IPSec:
```

```js
let p = NEVPNProtocolIPSec()
```

```js
p.useExtendedAuthentication = true
```

<div class="content-ad"></div>

```javascript
p.localIdentifier = account.localID ?? "VPN"
```

```javascript
p.remoteIdentifier = account.remoteID
```

```javascript
if account.pskEnabled {
```

```javascript
p.authenticationMethod = .sharedSecret
```

<div class="content-ad"></div>

```js
p.sharedSecretReference = account.getPSKRef()
```

```js
} else {
```

```js
p.authenticationMethod = .none
```

```js
}
```

<div class="content-ad"></div>

```js
pt = p
```

IKEv2의 경우:

```js
case .IKEv2:
```

```js
let p = NEVPNProtocolIKEv2()
```

<div class="content-ad"></div>

```json
p.useExtendedAuthentication = true
```

```json
p.localIdentifier = account.localID
```

```json
p.remoteIdentifier = account.remoteID
```

```json
if (account.pskEnabled) {
```

<div class="content-ad"></div>

```js
p.authenticationMethod = .sharedSecret
```

```js
p.sharedSecretReference = account.getPSKRef()
```

```js
p.passwordReference = account.getPasswordRef()
```

```js
} else {
```

<div class="content-ad"></div>

```js
p.authenticationMethod = .none
```

```js
}
```

```js
p.deadPeerDetectionRate = .medium
```

```js
pt = p
```

<div class="content-ad"></div>

configOnDemand에 대해 지금은 해당 사항이 아니기 때문에 구성하지 않을 거에요.

이제 개인 VPN을 구성한 후에는 도메인 레이어 리포지토리를 데이터 레이어에 주입할 시간이에요. 이를 위해 /Data/Repository 안에 createDefaultVPNRepository final 클래스를 만들 것이에요.

우리는 DefaultVPNRepository를 DVPNRepository에 준수하도록 만들 것이며, 이를 사용할 수 있게 될 거에요. 전체 코드는 여기에서 확인할 수 있어요.

이제 DashboardViewModel 설정을 완료할 준비가 되었어요.

<div class="content-ad"></div>

# 대시보드 뷰모델 파트 2:

뷰모델 안에서 다음과 같이 private 속성을 설정하였습니다:

```js
private var networkVPNUseCase: NetworkVPNUseCase
```

```js
init(networkVPNUseCase: NetworkVPNUseCase) {
   ...
}
```

<div class="content-ad"></div>

지금은 VPNManager에서 설정한 VPN 상태를 가져오기 위해 observer를 등록했어요.

```js
NotificationCenter.default.addObserver(self, selector: #selector(statusDidChange(_:)), name: NSNotification.Name.NEVPNStatusDidChange, object: nil)
```

statusDidChange는 상태가 변경될 때 레이블을 업데이트할 거예요.

viewDidLoad에서는 ViewModel에게 구성을 로드하도록 알릴 거예요. DefaultDashboardViewModel 쪽에서는 이렇게 될 거예요:

<div class="content-ad"></div>

```js
func viewDidLoad() {
      self.observeStatus()
      self.networkVPNUseCase.loadVPNConfig {}
}
```

대시보드뷰컨트롤러의 connect 버튼을 누르면 ViewModel에게 연결 설정이 끊겨 있는지를 확인하고, 현재 상태가 연결된 상태인 경우에는 연결을 끊어야 한다고 알려줍니다.

```js
func didConnect() {
    networkVPNUseCase.connect(configuration: vpnAccount.value)
}
```

```js
func didDisconnect() {
    self.networkVPNUseCase.disconnect()
}
```

<div class="content-ad"></div>

이 논리는 어디에서 처리될까요? MVVM에서는 뷰가 어떤 종류의 논리도 처리해서는 안 되며, 현재 상태/동작을 ViewModel에만 알리고 VM이 적절한 동작을 수행해야 합니다. 이 논리를 수행하는 방법은 다음과 같습니다:

```js
func connectDisconnect() {
    if status.value == .connected || status.value == .connecting {
        self.networkVPNUseCase.disconnect()
    } else if (status.value == .disconnected || status.value == .invalid) {
        self.networkVPNUseCase.connect(configuration: vpnAccount.value)
    }
}
```

기본적으로 우리는 NSNotification.Name.NEVPNStatusDidChange를 통해 받은 상태를 확인하여 연결 또는 연결 해제합니다.

<div class="content-ad"></div>

이 부분에서 놓친 부분은 ViewModel을 바인딩하는 것 뿐입니다.

따라서 ViewController 내에서 viewDidLoad에서 bind(viewModel)를 수행합니다.

여기서 bind()는 옵저버의 키를 설정하는 private 함수입니다.

```js
private func bind(_ viewModel: DashboardViewModel) {
```

<div class="content-ad"></div>


viewModel.loadingType.observe(on: self, observerBlock: { [weak self] in self?.handleLoading($0)})



viewModel.status.observe(on: self, observerBlock: { [weak self] in self?.handleConnectionStatus($0)})



viewModel.route.observe(on: self, observerBlock: { [weak self] in self?.handleRouting($0)})



viewModel.premiumStatus.observe(on: self, observerBlock: { [weak self] in self?.handlePurchaseStatus($0)})


<div class="content-ad"></div>

```js
viewModel.vpnAccount.observe(on: self, observerBlock: {[weak self] in self?.handleVPNSelection($0)})
```

```js
viewModel.currentIP.observe(on: self, observerBlock: {[weak self] in self?.handleIPUpdates($0)})
```

```js
viewModel.loadRequestAd.observe(on: self, observerBlock: {[weak self] in if $0 { self?.interstitial.load(GADRequest())}})
```

```js
}
```

<div class="content-ad"></div>

Rx 대신 Observable 대신 RxSwift 라이브러리를 사용할 수 있어요.

![image](https://miro.medium.com/v2/resize:fit:1200/1*BwR548YaWSxEhs7RjlHzPw.gif)    

# 다음 파트가 곧 업데이트될 예정입니다

## ✌️이 튜토리얼을 계속 진행하고 싶다면 여기나 Twitter에서 팔로우해주세요. 궁금한 점이 있으면 언제든지 물어보세요, 최선을 다해 답변해드릴게요 ❤️

<div class="content-ad"></div>

# 👉 [파트 2]