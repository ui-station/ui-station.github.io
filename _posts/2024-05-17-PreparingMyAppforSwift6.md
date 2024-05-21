---
title: "Swift 6를 위한 내 앱 준비하기"
description: ""
coverImage: "/assets/img/2024-05-17-PreparingMyAppforSwift6_0.png"
date: 2024-05-17 17:56
ogImage: 
  url: /assets/img/2024-05-17-PreparingMyAppforSwift6_0.png
tag: Tech
originalTitle: "Preparing My App for Swift 6"
link: "https://medium.com/better-programming/preparing-my-app-for-swift-6-7bc04555f8f7"
---



![2024-05-17-PreparingMyAppforSwift6_0](/assets/img/2024-05-17-PreparingMyAppforSwift6_0.png)

# "Swift 6 모드"가 뭔가요?

Swift 6는 더 이상 2023년에 출시되지 않을 것으로 Doug Gregor가 Swift 언어 워크그룹에서 명확히 밝혔습니다. 그러나 애플이 이미 Swift 6 일부 기능을 5.8에서 제공했다는 것을 알고 계셨나요? 네, 사실입니다. 이전에 Xcode 14.3로 제공된 Swift 일부 기능은 기본적으로 비활성화되어 있습니다. Swift 6 출시 시에 다시 활성화될 예정이며, 출시까지 한 해 이상 소요될 수도 있습니다.

이러한 기능은 Swift에 몇 가지 파괴적인 변화를 소개합니다. 예를 들어, 널리 알려진 API의 이름을 바꾸거나 동작을 조정하거나, 컴파일러에 새로운 안전성 확인을 추가하는 식입니다. 그러나 이러한 변경 사항을 코드 기반을 개선하기 위해 언젠가는 모두 활성화될 것이며, 우리는 이러한 변경 사항과 잘 호환되도록 프로젝트를 업데이트해야 할 것입니다.


<div class="content-ad"></div>

내 프로젝트에서 모든 기능을 켜는 것이 좋을 것 같아요. 그러면 코드베이스에 어떤 변경 사항이 있는지 확인할 수 있거든. 물론, BareSlashRegexLiterals와 같은 새로운 기능도 활용할 수 있죠. /.../와 같은 정규식 리터럴 구문을 간결하게 사용할 수 있게 해주는 것이죠.

다행히도, Swift 5.8부터 이러한 옵션을 활성화하는 통합된 방법이 생겼어요. 다른 Swift Flags에서 -enable-upcoming-feature를 Swift에 전달하기만 하면 되는데요. 이걸 Xcode 프로젝트의 빌드 설정에서 제공해주면 되요. 하지만 사용 가능한 기능을 알아야 하기도 해요. 그런데 좋은 개요를 제공하는 곳을 찾기 어려웠어요. 이 통합 옵션을 도입한 제안에는 그러한 목록이 포함되어 있지만, 나중에 추가된 새로운 옵션들과 같은 것이 업데이트되지 않아요. 예를 들어 SE-0384와 같은 것도 그렇죠. 그래서 현재 모든 지원되는 옵션 목록을 신뢰할 수 있는 곳이 어디에 있는지 아시는 분?

Swift는 오픈 소스이기 때문에 가장 신뢰할 수 있는 곳은 Swift GitHub 저장소네요! UPCOMING_FEATURE라는 이름의 Swift 진화 제안 번호와 해당하는 Swift 버전이 포함된 Features.def 파일이 있어요:

```js
UPCOMING_FEATURE(ConciseMagicFile, 274, 6)
UPCOMING_FEATURE(ForwardTrailingClosures, 286, 6)
UPCOMING_FEATURE(BareSlashRegexLiterals, 354, 6)
UPCOMING_FEATURE(ExistentialAny, 335, 6)
```

<div class="content-ad"></div>

다음은 각 항목에 대한 간단한 설명이 포함된 옵션입니다:

- ConciseMagicFile:
#file을 #filePath가 아닌 #fileID로 변경합니다.
- ForwardTrailingClosures:
후방 스캔 일치 규칙을 제거합니다.
- ExistentialAny:
존재 타입에 대해 any를 필요로 합니다.
- BareSlashRegexLiterals:
/.../ 정규식 리터럴 구문을 사용할 수 있게 합니다.

왜인지 두 옵션이 없어 보입니다 (조사 중):

- StrictConcurrency:
완전한 동시성 검사 수행합니다.
- ImplicitOpenExistentials:
추가 사례에서 암시적 열기를 수행합니다.

<div class="content-ad"></div>

나중에 더 많은 옵션이 함께 제공됩니다. 예를 들어 ImportObjcForwardDeclarations가 있습니다.

내 코드를 적절한 동시성 지원을 위해 추가로 확인하기로 결정했기 때문에 -warn-concurrency도 전달하기로 선택했습니다(실제로 작동한다면 StrictConcurrency와 동일해야 합니다) 그리고 -enable-actor-data-race-checks를 전달하기로 했습니다.

# 내 프로젝트 이전하기

내 프로젝트에서 나와 같이 5.8 옵션 모두를 활성화하고 싶다면, 다음 텍스트 블록을 복사(⌘C)하여 Xcode 프로젝트의 "Build Settings" 탭으로 이동하고 "Other Swift Flags"를 검색한 다음 해당 옵션을 에디터에서 선택하고 붙여넣기(⌘V)하십시오:

<div class="content-ad"></div>

```js
//:configuration = Debug
OTHER_SWIFT_FLAGS = -enable-upcoming-feature BareSlashRegexLiterals -enable-upcoming-feature ConciseMagicFile -enable-upcoming-feature ExistentialAny -enable-upcoming-feature ForwardTrailingClosures -enable-upcoming-feature ImplicitOpenExistentials -enable-upcoming-feature StrictConcurrency -warn-concurrency -enable-actor-data-race-checks

//:configuration = Release
OTHER_SWIFT_FLAGS = -enable-upcoming-feature BareSlashRegexLiterals -enable-upcoming-feature ConciseMagicFile -enable-upcoming-feature ExistentialAny -enable-upcoming-feature ForwardTrailingClosures -enable-upcoming-feature ImplicitOpenExistentials -enable-upcoming-feature StrictConcurrency -warn-concurrency -enable-actor-data-race-checks

//:completeSettings = some
OTHER_SWIFT_FLAGS
```

<img src="https://miro.medium.com/v2/resize:fit:1400/0*Klutl2JrkB5biMDB.gif" />

만약 저와 같이 SwiftPM 모듈화된 앱을 사용하거나 Swift 패키지를 작업 중이라면, 각 타겟에 .enableUpcomingFeature의 배열을 swiftSettings를 통해 전달해야 할 필요가 있습니다:

```js
let swiftSettings: [SwiftSetting] = [
   .enableUpcomingFeature("BareSlashRegexLiterals"),
   .enableUpcomingFeature("ConciseMagicFile"),
   .enableUpcomingFeature("ExistentialAny"),
   .enableUpcomingFeature("ForwardTrailingClosures"),
   .enableUpcomingFeature("ImplicitOpenExistentials"),
   .enableUpcomingFeature("StrictConcurrency"),
   .unsafeFlags(["-warn-concurrency", "-enable-actor-data-race-checks"]),
]

let package = Package(
   // ...
   targets: [
      // ...
      .target(
         name: "MyTarget",
         dependencies: [/* ... */],
         swiftSettings: swiftSettings
      ),
      // ...
   ]
```

<div class="content-ad"></div>

파일 상단에 있는 도구 버전을 5.8로 업그레이드하는 것을 잊지 마세요:

```js
// swift-tools-version:5.8
```

그 이유는 Swift가 프로젝트 대상에 지정한 옵션을 자동으로 프로젝트에 가져온 모듈로 전달하지 않기 때문입니다. 이것은 좋은 뉴스입니다. 왜냐하면 이렇게 함으로써 프로젝트에 포함하는 Swift 패키지를 조정할 필요가 없으며 여전히 앱 코드에서 이러한 기능을 사용할 수 있습니다. 그리고 그 반대도 마찬가지입니다. 패키지 작성자는 이러한 기능을 프로젝트에 적용할 수 있고, 이로 인해 소비되는 프로젝트의 코드에 영향을 미치지 않습니다.

이것들을 켜고 빌드한 후에 네가지 유형의 문제를 발견했습니다:

<div class="content-ad"></div>

1. 몇 군데에 any 키워드를 추가해야 했는데 Xcode가 Fix-It으로 도와주었어요:

![이미지 1](/assets/img/2024-05-17-PreparingMyAppforSwift6_1.png)

![이미지 2](/assets/img/2024-05-17-PreparingMyAppforSwift6_2.png)

2. 어떤 이유로 .sheet 수정자가 있는 뷰들에 대해 많은 오류를 받았어요. 에러 메시지에는 "Generic parameter `Content` could not be inferred"와 "Missing argument for parameter `content` in call"이란 내용이 있었죠. 이 메시지들은 그리 도움이 되지 않았어요. 그래서 일단 .sheet 내용을 Textview로 대체해봤지만 도움이 되지 않았어요. 그래서 한 가지씩 선택 옵션을 끄다가 ForwardTrailingClosures를 끈 채로 에러가 없어지는 것을 확인하고 그대로 끈 채로 유지했어요. 미래의 Swift 버전에서 더 나은 에러 메시지가 생성되어 이 문제가 해결될 것을 희망하고 있어요. 급한 일은 아니니까요. 나중에 수정할 수 있을 거예요. 현재 조사할 시간이 없습니다.

<div class="content-ad"></div>


![PreparingMyAppforSwift6_3](/assets/img/2024-05-17-PreparingMyAppforSwift6_3.png)

3. 일부 함수를 TCA WithViewStore를 반환하도록 표시해야 했는데, 다시 한 번, Xcode가 Fix-It으로 도와주었습니다.

![PreparingMyAppforSwift6_4](/assets/img/2024-05-17-PreparingMyAppforSwift6_4.png)

4. 많은 곳에서 "Non-sendable type '…' passed in call to main actor-isolated function cannot cross actor boundary"라는 경고가 표시되어서, 이러한 타입들을 Sendable 프로토콜을 준수하도록 만들었습니다 (Sendable에 대해 자세히 알아보세요).


<div class="content-ad"></div>

RemafoX 앱에 약 35,000줄의 Swift 코드가 있는데, 나머지는 모두 잘 빌드되었다. 전체 프로세스는 내 시간의 3시간 미만이 걸렸어요.

이제 내 프로젝트는 Swift의 미래를 대비해서 준비된 상태입니다 🎉 그리고 새로운 Regex 리터럴 기능을 사용할 수 있어요 (let regex = /.*@.*/). 또한, 나중에 Swift 6로 마이그레이션해야 할 새 코드를 도입할 수 없어요, 바로 오류를 받게 될 거니까. 💯

귀하는 어떠세요? 귀하의 프로젝트에서는 어떤 예정된 기능을 사용하고 싶으신가요?