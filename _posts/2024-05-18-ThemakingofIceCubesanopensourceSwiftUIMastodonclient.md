---
title: "아이스 큐브 만들기, 오픈 소스 SwiftUI 마스토돈 클라이언트"
description: ""
coverImage: "/assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_0.png"
date: 2024-05-18 15:38
ogImage: 
  url: /assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_0.png
tag: Tech
originalTitle: "The making of Ice Cubes, an open source, SwiftUI Mastodon client."
link: "https://medium.com/@dimillian/the-making-of-ice-cubes-an-open-source-swiftui-mastodon-client-45ebea5cf6b6"
---


## Ice Cubes 제작에 관한 시리즈 기사의 시작입니다.

# 1. 매스토돈

이 기사의 목표는 매스토돈을 믿냐 안 믿냐하는 것이 아닙니다. 매스토돈이 무엇인지 설명하거나 트위터와 비교할 시간도 없습니다. 그러나 한 가지 말씀 드릴 수 있습니다: 저는 매스토돈을 사용하고 있으며, 그것은 계속해서 사용될 것입니다. 이 링크를 사용하여 mastodon.social에서 저와 함께 할 수도 있습니다. 또는 iosdev.space 같은 다른 서버에 참여할 수도 있습니다. iOS, Swift, Apple 커뮤니티의 많은 사람들이 이미 이동했으며 여러 훌륭한 대화가 진행되고 있습니다.

![이미지](/assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_0.png)

<div class="content-ad"></div>

이 기사는 Ice Cubes 앱 제작에 대한 다수의 이야기 중 첫 번째입니다. 이 기사는 어플이 무엇이며, 그 배경 이야기, 그리고 코드베이스 개요에 중점을 둘 것입니다.

## 2. 제품: Ice Cubes

작년 크리스마스 때, 저는 진지하게 Mastodon을 사용하기 시작했고, 가볍게 할 일이 없으니까, 이 서비스에 대한 iOS 클라이언트를 개발하기도 시작했습니다. 전에 직접 트위터 클라이언트를 만들어보고 싶었지만, API 상태가 그다지 좋지 않았고, iOS 트위터 앱을 사랑하니 실제로는 못 했습니다. 먼저, Mastodon API가 정말 멋집니다. 문서가 훌륭하고 거의 모든 것에 대한 엔드포인트가 있습니다. 그리고 Mastodon 자체도 핵심 앱 (웹 프론트엔드, iOS, Android 등)을 만들 때 자체 API를 사용하기 때문에, 정말 비밀 API나 문지기가 없습니다.

또한 Ice Cubes의 일부를 작업할 때 매우 유용한 공식 iOS 앱 리포지토리에 대해 언급하고 감사의 마음을 전합니다, 특히 푸시 알림 부분에서.

<div class="content-ad"></div>

Ice Cubes에 대해서 궁금하신 이유가 있다면, 해당 이름은 어디에서 왔는지 궁금해하실 수도 있습니다. 모든 궁금증에 대한 답변은 여기 있습니다, 약속드립니다:

![Ice Cubes](/assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_1.png)

또한 이 앱은 많은 아이콘과 함께 제공됩니다. 일부는 midjourney가 제작하고 일부는 커뮤니티의 훌륭한 디자이너가 만든 것으로 현재 앱에 특별히 소개되어 있습니다!

![Ice Cubes Icons](/assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_2.png)

<div class="content-ad"></div>

'마스토돈'을 통해 나만의 소셜 네트워크 앱을 마침내 만들 수 있게 되었고, iOS 16 및 그와 함께 제공된 멋진 SwiftUI API를 활용할 수 있는 완벽한 시기였습니다. 그래서 이제 GitHub과 Xcode 프로젝트를 만들었어요. 평소처럼, 모든 소스 코드는 완전히 오픈 소스입니다.

약 한 달 후, 수천 명의 테스터들로부터 수많은 피드백과 테스트를 받은 결과, 공개 TestFlight를 통해 앱 스토어에 출시되었습니다.

제가 일찍, 아주 일찍 출시하기로 결정했어요. 겨우 MVP 수준인데도, 저는 만족스러운 MVP를 가지고 있었답니다. 1.0 버전이 작동했고, 공식 iOS 앱을 포함한 다른 클라이언트에서 할 수 없었던 많은 일들을 이미 할 수 있었죠. 게다가 한 가지 강력한 기능을 가졌는데, 바로 원격 타임라인 핀 지정과 읽기였습니다.

마스토돈은 분산형 네트워크입니다. 다양한 주제와 특정 커뮤니티들이 있는 거의 모든 서버에 계정을 가질 수 있죠. '아이스 큐브' 앱의 아이디어는 앱에 어떤 서버 URL이든 추가하고 해당 지역 타임라인으로 쉽게 전환하여 그곳에서 콘텐츠를 읽고 공유할 수 있는 것입니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_3.png" />

최초 릴리스의 일환으로 핀 지정 및 원격 타임라인 읽기 기능이 출시되었고, 매일 긍정적인 피드백을 받았습니다. 다른 앱들이 이미 이를 구현한 것은 알고 있지만, 이를 핵심 기능으로 만들고 사용자 앞에 두면서 사용하기 쉽게 만든 점이 인지도를 상당히 높이는 데 도움이 되었습니다.

이제 버전 1.5.X를 릴리스하고 있으며, 매일 개선 사항, 기능, 버그 수정 등을 업데이트하고 있습니다.

<img src="/assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_4.png" />

<div class="content-ad"></div>

저장소에는 약 80명의 기여자, 400개 이상의 병합된 PR 및 15개 이상의 언어로 제공되는 응용 프로그램이 있습니다. 오픈 소스 및 Mastodon 커뮤니티가 놀라울 정도로 지원해주었습니다. 이것은 지금까지 가장 좋은 오픈 소스 프로젝트 중 하나이며 코드베이스에도 꽤 만족하고 있습니다.

이 게시물을 통해, Ice Cubes GitHub 저장소에서 우리에게 도움을 주신 모든 훌륭한 기여자들에게 감사의 말씀을 전하고 싶습니다. 버그 수정, 기능 구현, 다양한 언어로 번역하는 데 도움을 주신 여러분 없이는 이 응용 프로그램이 오늘날의 모습을 갖추지 못했을 것입니다!

작성 시점에서 이 응용 프로그램은 약 50,000번 다운로드되었으며 앱 스토어에서 1300개의 리뷰로 4.8의 평균 평점을 받았습니다. 꽤 좋아 보이나요?! 

![이미지](/assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_5.png)

<div class="content-ad"></div>

저에게서부터 여러분에게까지, 이것은 시작에 불과해요. Ice Cubes는 이제 대부분의 Mastodon 기능을 지원하며, 훌륭한 푸시 알림, 공유 확장 기능, 그리고 Safari 확장 프로그램을 제공하여 어떤 Mastodon 링크든 Ice Cubes에서 열 수 있도록 했어요. 이 앱은 iOS를 최우선으로 생각한 것으로, SwiftUI를 통해 완전히 구축되었으며 가능한 모든 네이티브 iOS 구성 요소를 사용했어요.

대단한 ToolbarTitleMenu와 마찬가지로 타임라인 탭 내비게이션 바에서 작동하는 것을 볼 수 있어요.

![이미지](/assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_6.png)

또한 List swipe actions 또한 가능해요. 이 기능은 어떤 상태 행 뷰에서 이용할 수 있어요.

<div class="content-ad"></div>


![image1](/assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_7.png)

It also uses NavigationStack throughout the app with programmatic navigation and a centralized router:

![image2](/assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_8.png)

These are just some examples of the SwiftUI and iOS features that the app showcases. Feel free to explore the codebase or run the app directly on your device!


<div class="content-ad"></div>

iPadOS 및 macOS에서도 아주 잘 작동합니다 (지금은 Apple Silicon iOS 앱으로 작동 중입니다). 언젠가는 진정한 네이티브 macOS 클라이언트를 개발하고 싶네요. 커스텀 사이드바와 더 큰 화면을 위한 보조 열을 사용하고 있습니다:

![이미지](/assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_9.png)

# 3. 코드베이스

## 프로젝트 구성

<div class="content-ad"></div>

지금 앱 개요를 이해했으니 이제 코드베이스에 진입해 봅시다. 우선, 이 앱은 Swift 패키지를 많이 활용합니다:

![image](/assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_10.png)

패키지는 도메인과 기능별로 나누어져 있습니다. 앱 자체에는 매우 적은 코드가 있으며, 모든 것이 각자의 패키지에 독립적으로 포함되어 있습니다. 이는 테스팅이 더 쉬워지며 (현재는 테스트가 거의 없지만), SwiftUI 미리보기를 통한 패키지 레벨에서의 빠른 작업, 빠른 빌드 시간 등을 가능하게 합니다.

## 아키텍처

<div class="content-ad"></div>

거기에는 미친 듯한 아키텍처가 없어요. 이 앱은 주요 관심 분리와 뷰 캡슐화가 잘 이루어진 베어본즈 MVVM을 사용하고 있어요. 여기 앱에서 가장 중요한 뷰인 StatusRowView의 예시를 보여드릴게요. 여기서는 게시물이 표시되요:

![StatusRowView](/assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_11.png)

이 앱은 주요 뷰 하나, 뷰 모델 하나를 가지고 있으며, 작고 명확한 하위 뷰들로 구성돼 있어요. 주요 뷰는 @StateObject를 사용해 뷰 모델을 보유하고 있으며, 뷰 모델은 필요한 곳에서 @ObservedObject 또는 단순한 let 변수로 전달돼요. 뷰 모델의 published 속성을 수신해야 할 때만 관찰이 필요해요. 목록을 스크롤하는 경우 상태를 최소한으로 업데이트하여 스크롤하는 동안 업데이트를 최소화하는 것이 아이디어에 포함돼 있어요(사실 상태 목록을 스크롤하는 경우 업데이트가 거의 없어요). 이는 앱의 1.5.X 버전에서 타임라인을 스크롤하는 동안 성능을 향상시키는 데 큰 역할을 했어요.

앱의 일부를 최적화한 Alex Grebenyuk에게도 큰 찬사를 보내고, 앱이 원격 이미지를 로드하고 표시하기 위해 사용하는 Nuke 라이브러리에 대해 더 나은 최적화를 실현한 점에 대해 감사드릴게요.

<div class="content-ad"></div>

## EnvironmentObject

앱은 EnvironmentObject 패턴을 많이 사용합니다:

```js
WindowGroup {
 appView
   .applyTheme(theme)
    .environmentObject(appAccountsManager)
    .environmentObject(appAccountsManager.currentClient)
    .environmentObject(quickLook)
    .environmentObject(currentAccount)
    .environmentObject(currentInstance)
    .environmentObject(userPreferences)
    .environmentObject(theme)
    .environmentObject(watcher)
    .environmentObject(pushNotificationsService)
}
```

어떤 개수의 EnvironmentObject를 주입하는 것에는 단점이 없습니다. 주입된 EnvironmentObject와 어떤 뷰가 연결되어 있는지, 그들 안에서 얼마나 많은 업데이트를 만드는지에 달려 있습니다. 공유 데이터 등을 여러 EnvironmentObject로 분리해도 됩니다. 보다 명확할수록 뷰에 좋습니다. 그것은 원하는 속성에만 연결할 수 있기 때문입니다.

<div class="content-ad"></div>

@EnvironmentObject을 뷰 상단에 포함하면 성능에 별도의 비용이 들지 않는다는 것을 알아두어야 해요. 그러나 이 환경 객체 내의 @Published 속성을 업데이트하면 뷰가 연결되어 있다면 뷰 업데이트가 트리거됩니다. 심지어 뷰 내에서 이 속성을 직접 관찰하거나 사용하지 않더라도 말이죠. 그러니 주의하세요.

저는 여러 객체와 사용자 정의 환경 값이 포함된 Env 패키지를 가지고 있어요. 이를 환경에서 주입하고 검색할 수 있어요.

![이미지](/assets/img/2024-05-18-ThemakingofIceCubesanopensourceSwiftUIMastodonclient_12.png)

제 경우에는 사용자 환경 설정 중 하나, 테마, 푸시 알림 관리, 스트림 감시 프로그램, 그리고 앱 어디서든 현재 계정 및 현재 인스턴스 정보에 액세스할 수 있는 뭔가를 다양하게 가지고 있어요.

<div class="content-ad"></div>

## 네비게이션

다음으로 네비게이션에 대해 이야기해 봅시다. SwiftUI에서는 항상 네비게이션이 중요한 문제이며 매우 초기부터 이 문제를 다루었습니다. 전체 NavigationStack으로 이동하기로 결정하였습니다. 이것은 프로그래밍 방식의 네비게이션과 NavigationLink를 지원합니다.

"하지만 iOS 16 전용이잖아!!!"라고 말하겠지만, 맞습니다. 그러나 여기서는 사이드 프로젝트/취미 앱을 만들고 있습니다. 그리고 최신 API를 사용하고 있습니다. 왜냐하면 가능하고 원하기 때문이죠.

그렇다면 중앙 앱 라우터는 어떻게 작동할까요? 가능한 경로의 목록을 정의했습니다:

<div class="content-ad"></div>

```js
public enum RouterDestinations: Hashable {
  case accountDetail(id: String)
  case accountDetailWithAccount(account: Account)
  case accountSettingsWithAccount(account: Account, appAccount: AppAccount)
  case statusDetail(id: String)
  case statusDetailWithStatus(status: Status)
  case remoteStatusDetail(url: URL)
  case conversationDetail(conversation: Conversation)
  case hashTag(tag: String, account: String?)
  case list(list: Models.List)
  case followers(id: String)
  case following(id: String)
  case favoritedBy(id: String)
  case rebloggedBy(id: String)
  case accountsList(accounts: [Account])
}
```

여기서 시트에 대한 동일한 내용을 확인할 수 있습니다:

```js
public enum SheetDestinations: Identifiable {
  case newStatusEditor(visibility: Models.Visibility)
  case editStatusEditor(status: Status)
  case replyToStatusEditor(status: Status)
  case quoteStatusEditor(status: Status)
  case mentionStatusEditor(account: Account, visibility: Models.Visibility)
  case listEdit(list: Models.List)
  case listAddAccount(account: Account)
  case addAccount
  case addRemoteLocalTimeline
  case statusEditHistory(status: String)
  case settings
  case accountPushNotficationsSettings
  case report(status: Status)
  case shareImage(image: UIImage, status: Status)
}

그리고 저는 RouterPath라고 불리는 ObservableObject를 가지고 있어요.
```

<div class="content-ad"></div>

```swift
@MainActor
public class RouterPath: ObservableObject {
  public var client: Client?
  public var urlHandler: ((URL) -> OpenURLAction.Result)?

  @Published public var path: [RouterDestinations] = []
  @Published public var presentedSheet: SheetDestinations?

  public init() {}

  public func navigate(to: RouterDestinations) {
    path.append(to)
  }
}
```

나중에 각 탭은 NavigationStack이 있는 곳 어디에서든 주입될 것입니다.

```swift
struct NotificationsTab: View {
  @StateObject private var routerPath = RouterPath()

  var body: some View {
    NavigationStack(path: $routerPath.path) {
      NotificationsListView()
        .withAppRouter()
        .withSheetDestinations(sheetDestinations: $routerPath.presentedSheet)
    }
    .environmentObject(routerPath)
  }
}
```

보시다시피, RouterPath StateObject의 path를 NavigationStack에 연결합니다. 이렇게 하면 NavigationStack이 해당 path에 대한 프로그래밍 방식 조작을 다룰 것입니다. 또한 NavigationLink에서의 변경 사항을 반영하도록 업데이트합니다. NavigationLink를 자주 사용하지 않아요. 제약이 많이 따르기 때문에 많은 수정 작업과 navigate(to:) 함수를 자주 사용합니다.


<div class="content-ad"></div>

안녕하세요! 테이블 태그를 마크다운 형식으로 변경하려면 이렇게 하시면 됩니다.

위의 예시 코드를 마크다운 형식으로 변경한 것입니다. 도움이 되었길 바랍니다.

<div class="content-ad"></div>

당신의 의견으로 보입니다.

아바타를 터치하면 사용자 프로필로 이동하고 싶다고 상상해보세요. 이렇게 하면 됩니다:

```js
AvatarView(url: notification.account.avatar)
.contentShape(Rectangle())
.onTapGesture {
  routerPath.navigate(to: .accountDetailWithAccount(account: notification.account))
}
```

그리고 어떤 시트를 표시할 때도 똑같이 작동합니다. 이것은 NavigationStack 때문이 아니라, 전역 시트 라우터를 뷰 계층 구조에 등록했기 때문입니다 (withSheetDestinations). 시트를 표시하려면 이렇게 하면 됩니다:

<div class="content-ad"></div>

```md
Button {
   routerPath.presentedSheet = .addAccount
} label: {
   Image(systemName: "person.badge.plus")
}
```

그럼 이만!

이제 Ice Cubes 내에서 라우터가 작동하는 방법에 대해 알게 되셨습니다. 그리고 코드는 오픈 소스이므로 GitHub에서 전체 코드를 살펴볼 수 있습니다.

이번 Ice Cubes에 관한 첫 번째 글은 여기서 마치겠습니다만, 다음에 읽고 싶은 내용에 대해 댓글이나 GitHub, Mastodon 등으로 연락해주시면 기쁠 것입니다!


<div class="content-ad"></div>

읽어 주셔서 감사합니다! 이 글이 마음에 들었으면 좋겣지만 언제든지 놀러 오세요. 더 깊게 알아볼 내용이 많이 있어요! 🚀

PS: 만약 함께하고 싶다면 말스토돈(Mastodon) 소셜 인스턴스에 가입할 수 있는 초대 링크를 여기 남겨드릴게요: [https://mastodon.social/invite/Tqz5hxRc](https://mastodon.social/invite/Tqz5hxRc)