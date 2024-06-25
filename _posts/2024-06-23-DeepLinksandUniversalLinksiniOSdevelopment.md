---
title: "iOS 개발에서 Deep Link와 Universal Link 차이점 이해하기"
description: ""
coverImage: "/assets/img/2024-06-23-DeepLinksandUniversalLinksiniOSdevelopment_0.png"
date: 2024-06-23 21:27
ogImage:
  url: /assets/img/2024-06-23-DeepLinksandUniversalLinksiniOSdevelopment_0.png
tag: Tech
originalTitle: "Deep Links and Universal Links in iOS development"
link: "https://medium.com/@m.majchrzycki/deep-links-and-universal-links-in-ios-development-f6d09f4cfcf6"
---

# 이것은 인기 있는 iOS 인터뷰 질문입니다: Swift에서 Deep Linking과 Universal Linking 간의 주요 차이점을 설명해주세요. 차이를 알고 있는 것이 좋습니다.

가끔 앱에서 처리할 수 있는 추가 작업이 필요할 때가 있습니다. 사용자에게 중요한 정보를 알리기 위해 푸시 알림을 사용할 수 있습니다. 그러나 앱을 시작하거나 데이터를 iOS 앱으로 전달하는 다른 방법도 있습니다. 오늘은 Deep Link와 Universal Link 간의 주요 차이점을 알려드리려고 합니다. Swift에서 어떻게 작동하는지 그리고 iOS 기기에서 어떻게 처리될 수 있는지 살펴보겠습니다.

Deep Link와 Universal Link를 사용하면 이메일(비밀번호 변경 링크), 텍스트 메시지 또는 웹사이트에서 앱을 시작할 수 있습니다.

앱 내에서 사용할 수 있는 추가 데이터를 포함할 수 있습니다. 링크를 통해 사용자에게 제공될 특정 뷰를 시작할 수 있습니다. 로그인, 비밀번호 변경 및 기타 작업을 자동화하는 데 매우 유용할 수 있습니다.

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

물론 딥 링크와 유니버설 링크 모두 제한 사항과 취약점이 있습니다.

## 딥 링크

딥 링크의 주요 기능은 구현이 쉽다는 것입니다.

백엔드 변경이나 새로운 엔드포인트 매핑이 필요하지 않습니다. 앱에 딥 링크를 추가하는 것은 시간이 많이 소요되지 않습니다.

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

하지만 사용 허가를 요청하거나 Android에서 문제가 발생하는 것과 같은 단점도 있습니다. Deep link는 주로 iOS용으로 설계되었기 때문에 이러한 문제가 발생할 수 있습니다.

다른 한편으로는 Apple은 새로운 iOS 버전에서 Deep link를 사용하지 않도록 권장하고 있습니다. 또한 보안 문제로 인해 Deep link 사용을 권장하지 않습니다.

# Deep links in Swift apps: Implementing URL Schemes

위 내용을 염두에 두며, Swift 앱에서 Deep link를 구현하는 방법은 무엇인가요?

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

<img src="/assets/img/2024-06-23-DeepLinksandUniversalLinksiniOSdevelopment_0.png" />

먼저 사용하려는 앱 스키마를 추가해야 합니다. 이를 위해 Xcode의 프로젝트 설정으로 이동한 다음 Info로 이동하세요. URL 유형이라는 섹션에서 새 URL 스키마를 추가할 수 있습니다. com.myAppAddress와 같은 URL 스킴을 포함해야 합니다.

이를 추가한 후 우리의 애플리케이션이 인식할 수 있는 간단한 URL 링크를 만들 수 있습니다. com.myAppAddress로 시작하고 ://myProfile과 같이 이어져야 합니다.

다음과 같이:

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

com.myAppAddress://reset_password com.myAppAddress://open_custom_view_controller

이것은 쉬웠어요. 이제 이 프로필들을 구현해야 합니다. 이를 AppDelegate 또는 SceneDelegate를 통해 할 수 있습니다. 우리의 코드는 이렇게 보일 것입니다:

```js
let myUrlScheme = “com.myAppAddress”
```

```js
func application(_ app: UIApplication, open url: URL,
                 options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
    if let scheme = url.scheme,
        scheme.localizedCaseInsensitiveCompare(myUrlScheme) == .orderedSame,
        let view = url.host {
        var parameters: [String: String] = [:]
        URLComponents(url: url, resolvingAgainstBaseURL: false)?.queryItems?.forEach {
            parameters[$0.name] = $0.value
        }
        redirect(to: view, with: parameters)
    }
    return true
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

URL을 출력하면 다음과 같이 나옵니다:

url.scheme = "com.myAppAddress" url.host = "open_custom_view_controller" (또는 "://" 이후에 추가될 내용)

URL 스키마를 통해 매개변수를 추가할 수도 있습니다 (위의 코드를 참고하세요).

이 능력 덕분에 링크로부터 특정 정보를 가져올 수 있습니다. 예를 들어 비밀번호를 재설정하고 싶다면, 다음과 같은 링크가 될 것입니다:

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

위의 표를 Markdown 형식으로 변경해주세요.

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

이제, 웹 사이트와 앱 간 협력을 처리하기 위해 Apple이 현재 홍보하는 유니버설 링크로 넘어갑시다. 이것들은 보안이 더 우수하지만 구현이 더 어렵습니다.

링크를 생성하는 방법에도 차이가 있습니다. 우리는 앱 구현과 특정 URL 링크뿐만 아니라 서버 측에 특정 JSON 파일을 생성 및 유지해야 합니다.

![Universal Links](/assets/img/2024-06-23-DeepLinksandUniversalLinksiniOSdevelopment_1.png)

# 유니버설 링크: iOS 앱에 JSON 추가

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

Apple의 요구 사항으로 인해 JSON 파일을 작성해야합니다. 예를 들어:

```js
{
    “applinks”: {
        “apps”: [],
        “details”: [
            {
                “appID”: “teamID.appID.specific.address”,
                “paths”: [“*”],
            }
        ]
    }
}
```

위에서 보듯이, appID 필드에는 두 요소가 필요합니다:

- 앱의 teamID,
- 그리고 앱의 appID.

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

그러므로 teamID가 123456이고 appID가 com.myApp인 경우, JSON 파일에서 사용되는 결과는 123456.com.myApp가 될 것입니다.

paths 필드에서는 iOS 앱에서 처리될 경로를 추가할 수 있습니다. URL Schemes의 host와 같습니다. Apple은 더 많은 기능을 제공합니다. paths에서는 특정 문자열을 포함/제외할 수 있는 정규식 솔루션을 구현할 수 있습니다.

서버 측에 JSON 파일을 추가한 후에는 앱으로 이동해야 합니다.

먼저 프로젝트 설정에서 새로운 능력을 추가해야 합니다. Associated domains에 대해 이야기합니다. 활성화한 후에는 링크를 처리하는 URL을 추가해야 합니다. 예를 들어, app links:myApp.com과 같습니다.

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

이 작업이 완료되면 Safari에서 URL 주소를 클릭하면 우리 앱이 시작됩니다.

이제 딥 링크와 마찬가지로 수신된 링크를 처리해야 합니다. AppDelegate로 이동해보겠습니다. 새 함수를 추가해야 합니다.

```swift
public func application(_ application: UIApplication,
                        continue userActivity: NSUserActivity,
                        restorationHandler: @escaping ([Any]?) -> Void) -> Bool {
    if let url = userActivity.webpageURL {
        var view = url.lastPathComponent
        var parameters: [String: String] = [:]
        URLComponents(url: url, resolvingAgainstBaseURL: false)?.queryItems?.forEach {
            parameters[$0.name] = $0.value
        }
        redirect(to: view, with: parameters)
    }
    return true
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

# iOS에서의 깊고 범용적인 링크: 각주

보시다시피, URL Scheme과 유사하게, 본 솔루션에서는 앱에서 사용할 수 있는 매개변수가 있습니다. 하지만 이와 별도로, 유니버셜 링크와 URL Scheme의 주요 차이점은 전자가 사용자가 앱을 설치하지 않은 경우에도 사용할 수 있다는 것입니다. 그 경우에는 Safari가 링크를 열게 됩니다. 이는 기본 랜딩 페이지로 이어질 수도 있고, 우리 앱을 다운로드하기 위한 AppStore로 리디렉션될 수도 있습니다.

Swift 앱에서의 깊고 범용 링크에 도움이 될 수 있는 이 간단한 자습서가 도움이 되기를 바랍니다. 궁금한 사항이나 우려 사항이 있으면 언제든지 연락해 주세요!
