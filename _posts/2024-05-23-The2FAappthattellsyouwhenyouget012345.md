---
title: "2단계 인증 앱이 012345를 받았을 때 알려주는 기능"
description: ""
coverImage: "/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_0.png"
date: 2024-05-23 14:53
ogImage:
  url: /assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_0.png
tag: Tech
originalTitle: "The 2FA app that tells you when you get `012345`"
link: "https://medium.com/gitconnected/the-2fa-app-that-tells-you-when-you-get-012345-4b04e90ce317"
---

이른 시기 2010년대에 성장한 모든 회복된 엣지로드와 같이 나는 4chan과 같은 이미지 보드의 전성기를 약간 그리워합니다. 그들은 나치들이 모든 것을 망쳐 버리기 전에 야생 서부 초기 인터넷의 마지막 요새였어요.

클래식한 밈 중 하나는 GET이었는데, 당신이 랜덤으로 생성된 게시물 ID가 흥미로운 숫자 시퀀스를 포함할 것으로 정확히 예상했을 때 자부심을 느끼게 됩니다.

![그림](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_0.png)

요즘에는 이제 모든 보통 사람들이 성장하고 직장을 찾은 지금, 옛날의 마법에 제일 가까이 올 수 있는 것은 이중 인증 코드뿐입니다.

<div class="content-ad"></div>

당신이 알고 있으면 좋아요.

은행, 이메일 또는 클라우드 서비스에 다시 인증해야 하는 번거로움. 787000 또는 123450 같이 정말 좋은 숫자를 받았을 때의 작은 기쁨.

영감을 받았어요.

이 MFA 코드들은 매 30초마다 갱신되는 공통 알고리즘을 사용해요. 우리가 6자리 인증 코드에서 가능한 더블, 트리플, 쿼드, 퀸텀플, 섹스텀플 중에 매우 일부만을 경험하고 있어요.

<div class="content-ad"></div>

내 독립 프로젝트들처럼, 내 주변에 하나의 명확한 비전이 있었으며 그 주위에 구축할 수 있었습니다:

내가 뭘 해야 하는지 알았어요.

### 컨셉 증명

이게 작동하는지 알아야 하는 움직이는 부품은 많이 필요하지 않아요.

<div class="content-ad"></div>

- 2FA 비밀 키를 입력해주세요.
- 로컬에서 6자리 2FA 코드를 생성합니다.
- 쿼드/퀸트/섹스트가 생성될 때 푸시 알림을 보냅니다.

## 최소 기능 제품

만약 멋진 2FA 번호가 생성될 때 알림을 받는 개념이 유지된다면, 몇 가지 주요 기능을 갖춘 실제 앱으로 발전시킬 수 있습니다:

- 카메라로 2FA 비밀을 캡처합니다.
- 여러 2FA 코드를 저장합니다.
- 더 많은 숫자 패턴을 구현합니다.
- 사용자가 알고 싶은 패턴을 선택할 수 있도록 합니다.

<div class="content-ad"></div>

내가 뭔가를 알고 있었다는 걸 알았어: 내가 이걸 설명한 사람들의 90%는 나를 멍청이로 생각했어. 나머지 10%는 순수한 창의성만을 보았어.

![이미지](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_1.png)

# Proof of Concept 구축

## TOTP

<div class="content-ad"></div>

TOTP 또는 시간 기반 일회용 비밀번호는 놀랍도록 간단한 개념입니다. 이는 두 가지 입력을 사용하는 인증 프로세스입니다:

- 인증 서비스 및 자신의 장치에 저장된 비밀 키
- 현재 시간 또는 더 정확히 말하면 유닉스 시간 이후 경과한 30초 간격의 수

이 알고리즘은 두 입력을 결정론적으로 해시하여 여러분이 알고 사랑하는 6자리 코드를 생성합니다. 이 해싱 알고리즘은 Apple의 CryptoKit에서 찾을 수 있는 매우 흔한 것입니다. Apple 포럼의 우리 친구들 덕분에 여기 TOTP 알고리즘의 전체 영광이 있습니다.

```js
// CodeGenerator.swift

private let secret = Data(base64Encoded: "AAAAAAAAAAAAAAAAAAAAAAAAAAA")!

func otpCode(date: Date = Date()) -> String {
    let digits = 6
    let period = TimeInterval(30)
    let counter = UInt64(date.timeIntervalSince1970 / period)
    let counterBytes = (0..<8).reversed().map { UInt8(counter >> (8 * $0) & 0xff) }
    let hash = HMAC<Insecure.SHA1>.authenticationCode(for: counterBytes, using: SymmetricKey(data: secret))
    let offset = Int(hash.suffix(1)[0] & 0x0f)
    let hash32 = hash
        .dropFirst(offset)
        .prefix(4)
        .reduce(0, { ($0 << 8) | UInt32($1) })
    let hash31 = hash32 & 0x7FFF_FFFF
    let pad = String(repeating: "0", count: digits)
    return String((pad + String(hash31)).suffix(digits))
}
```

<div class="content-ad"></div>

위의 텍스트를 친근한 분위기로 한국어로 번역하면 다음과 같습니다.

"작업이 올바르게 진행되었는지 확인하기 위해, 구글 계정에 2단계 인증을 설정하고 해당 앱에서 비밀을 알고리즘을 사용하여 표시했어요.

![이미지](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_2.png)

그리고, 마법처럼 (약간 번거로운 base32에서 base64로의 변환 후에), 구글이 내 2단계 인증을 승인했어요!

![이미지](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_3.png)"

<div class="content-ad"></div>

이제 2단계 인증의 기본적인 부분이 작동 중이니, 컨셉 증명 퍼즐의 마지막 조각인 알림 생성을 구현할 수 있습니다.

## 앱 제한 사항

저희의 주요 제한 사항은 모바일 장치에 있습니다.

실제로 2단계 인증 생성과 같은 백그라운드 프로세스를 영원히 실행할 수 없으며, 반드시 사용자 비밀을 백엔드 푸시 서버에 저장할 수 없습니다.

<div class="content-ad"></div>

그러므로 이 개념이 작동하도록하려면 약간 교묘해야합니다: 앞으로 2FA 코드를 미리 계산하고, 그들이 실제로 출현하는 시간에 전달을 예약해야합니다.

또한, iOS에서 동시에 64개의 푸시를 예약할 수 있으므로, 다음을 고려해야합니다:

- 사용자에게 앱을 다시 입력하도록 요청하는 알림 하나 또는 두 개를 저장합니다.
- 알림을 터치하여 사용자가 앱을 열도록 유도하여 2FA 코드를 재계산하도록합니다.

이제 POC가 작동하는 방법을 알았으니, 빌드를 시작합시다.

<div class="content-ad"></div>

## 첫 번째 GETs 찾기

우리의 보잘것없는 2FA 코드를 향상시켜 봅시다.

우리는 많은 코드를 미리 계산한 후, 각 코드가 GET인지를 확인하는 레귤러 표현식을 구현할 계획입니다.

제 아주 간단한 SwiftUI 뷰는 UICollectionView를 백업으로 사용하여 성능이 훌륭하도록 보장하기 위해 이러한 코드를 편리하게 표시할 수 있습니다 (ScrollView의 기본 VStack은 10,000개의 항목 이전에 멀쩡히 오작동하기 시작할 것입니다!).

<div class="content-ad"></div>

```swift
// ContentView.swift

struct ContentView: View {

    var body: some View {
        List {
            ForEach(makeOTPs(), id: \.self) {
                Text($0)
                    .font(.custom("Courier", size: 20))
                    .font(.title)
                    .kerning(4)
            }
            .frame(maxWidth: .infinity)
        }
    }

    func makeOTPs() -> [String] {
        (0..<10_000).map {
            otpCode(increment: $0)
        }
    }
}
```

잘 진행되고 있어요.

![image](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_4.png)

이제, 세 자리 숫자가 연속으로 나타나는 TOTP(예: 120333)를 확인하는 간단한 정규 표현식 평가기를 추가할 수 있어요.

<div class="content-ad"></div>

```swift
extension String {
    func checkThoseTrips() -> Bool {
        (try? /(\d)\1\1/.firstMatch(in: self)) != nil
    }
}
```

Text 뷰에 fontWeight 수정자를 추가하여 스크롤할 때 이 GET을 쉽게 감지할 수 있습니다.

```swift
Text($0)
    .fontWeight($0.checkThoseTrips() ? .heavy : .light)
```

에쟈! 그 여행을 확인하세요!

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_5.png" />

우리는 심지어 우리의 정규 표현식을 신성한 네 숫자를 감지하기 위해 기본 수정할 수도 있어요 — 이건 독자들에게 연습문제로 남길게요.

<img src="/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_6.png" />

## 전혀 소용없지만 재미있는 관찰

<div class="content-ad"></div>

우리의 부주의한 ForEach 구현으로 인해 다음 경고 메시지가 발생했습니다:

```js
ForEach<Array<String>, String, Text>: the ID 312678 occurs multiple
times within the collection, this will give undefined results!
```

실제로 이 경고를 수십 번 받았습니다!

<img src="/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_7.png" />

<div class="content-ad"></div>

우리가 10,000개의 OTP를 생성했기 때문에, 여러 개가 일치할 가능성이 매우 높습니다. 이는 생일 문제와 같은 원리이며, 가능한 일치 쌍의 수는 백만 이상이 됩니다.

## 희귀한 GET 생성하기

이제 몇 가지 흥미로운 코드를 계산해 보겠습니다.

여기서 중요한 점은 미리 계산하여 미래를 예측하는 것입니다: TOTP는 비밀과 날짜 입력의 결정적 해시이기 때문에, 우리는 미래의 오랜 일련의 날짜를 입력하여 특정 시간에 어떤 OTP 코드를 볼 수 있는지 확인할 수 있습니다.

<div class="content-ad"></div>

각 코드와 날짜를 반환하도록 OTP 생성 방식을 조정해보겠습니다:

```js
// TOTP.swift

struct OTP {
    let date: Date
    let code: String
}

func otpCode(date: Date = Date(), increment: Int = 0) -> OTP {
    let digits = 6
    let period = TimeInterval(30)
    let adjustedDate = date.addingTimeInterval(period * Double(increment))
    let counter = UInt64(adjustedDate.timeIntervalSince1970 / period)
    let counterBytes = (0..<8).reversed().map { UInt8(counter >> (8 * $0) & 0xff) }
    let hash = HMAC<Insecure.SHA1>.authenticationCode(for: counterBytes, using: SymmetricKey(data: secret))
    let offset = Int(hash.suffix(1)[0] & 0x0f)
    let hash32 = hash
        .dropFirst(offset)
        .prefix(4)
        .reduce(0, { ($0 << 8) | UInt32($1) })
    let hash31 = hash32 & 0x7FFF_FFFF
    let pad = String(repeating: "0", count: digits)
    let code = String((pad + String(hash31)).suffix(digits))
    return OTP(date: adjustedDate, code: code)
}
```

이를 테스트하기 위해 다수의 코드를 생성하고, GETs: quints (5개의 중복번호)를 검색해 보겠습니다.

```js
func interestingCodes() -> [OTP] {
    (0..<1_000_000)
        .map { otpCode(increment: $0) }
        .filter { $0.code.checkThoseQuints() }
}
```

<div class="content-ad"></div>

내 M1이 해싱 함수를 실행하는 동안 몇 번의 숫자 계산 후, 약 30초 동안 진행된 결과물은 몇몇 굉장히 확인 가능한 GET들로 이어졌어요.

![image](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_8.png)

## 우리의 알림 일정 설정하기

좋은 숫자를 볼 수 있어서 재미있긴 하지만, 만약 실제로 GETs를 실생활에서 진짜 인증을 위해 사용할 수 없다면, 이 앱 개념은 그냥 무작위 번호 생성기와 다를게 없죠.

<div class="content-ad"></div>

이제 흥미로운 숫자가 도착하는 시점을 알게 되었으니, 번호를 실시간으로 받을 수 있도록 푸시 알림을 대기열에 넣고 있어요:

```js
// NotificationScheduler.swift

private func createNotification(for otp: OTP) {
    let center = UNUserNotificationCenter.current()
    let content = UNMutableNotificationContent()
    content.title = "Quads GET!!"
    content.body = otp.code
    content.sound = UNNotificationSound.default
    let components = Calendar.current.dateComponents([.year, .month, .day, .hour, .minute, .second], from: otp.date)
    let trigger = UNCalendarNotificationTrigger(dateMatching: components, repeats: false)
    let request = UNNotificationRequest(identifier: UUID().uuidString, content: content, trigger: trigger)
    center.add(request) { (error) in
        // ...
    }
}
```

이 알림은 우리 뷰에서 사용하는 흥미로운 코드를 생성한 직후 예약되어 있어요. 그리고 잠시 후, 한꺼번에 2개의 멋진 푸시 알림을 받았답니다!

![이미지](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_9.png)

<div class="content-ad"></div>

이 통지가 실제로 나타나는 번호와 일치한다는 것을 확인하니, 더욱 흥미로워졌어요!

![이미지](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_10.png)

아래 앱은 무작위 번호 생성기를 넘어서, 이 코드가 실제로 내 Google 계정에 로그인하는 데 도움이 된단 걸 알게 되었어요.

## 흥미로움

<div class="content-ad"></div>

다양한 종류의 흥미로운 숫자를 결정하려면 흥미로움의 개념을 소개해야 합니다. 이는 반복되는 숫자, 연속하는 숫자, 수학적으로 흥미로운 숫자(예: 파이 또는 e), 회문을 포함할 수 있습니다.

이러한 종류의 흥미로운 숫자는 ... 우리가 생성하는 각 OTP에 대해 선택적으로 만들어진 열거형 케이스로 열거될 수 있습니다.

```js
// 흥미로움.swift

열거형 흥미로움 {

    케이스 섹스텀
    케이스 퀸츠
    케이스 쿼드

    이니셜라이저(code: String) {
        if code.checkThoseSexts() {
            self = .sexts
        // ...

    변수 타이틀: String {
        switch self {
        case .sexts: return "Sextuples GET!!!"
        // ...

    함수 본문(code: String) -> String {
        switch self {
        case .sexts: return "체크해 보세요 섹스텀: \(code)"
        // ...
```

<div class="content-ad"></div>

각 checkThose 메소드는 다른 정규식을 래핑하며, 우리는 가장 중요한 순서대로 실행합니다. 예를 들어, 섹스튜플은 쿼드보다 100배 더 드물다.

오랜만에 리팩터를 해서 우리는 개념 증명을 만들었습니다. 요약해보겠습니다:

- 앱은 (하드코딩된) 2FA 보안 키를 입력할 수 있게 해줍니다.
- 앱은 로컬에서 매 30초마다 6자리 2FA 코드를 생성합니다.
- 앱은 쿼드, 퀸트, 섹스가 생성될 때 푸시 알림을 예약합니다.

몇 일 동안 앱을 사용해보려고 쉬는 시간을 가질 것입니다. 내 손에 멋진 앱을 만들 수 있을 것 같다고 예상하고 있습니다.

<div class="content-ad"></div>

# 최소 기능 제품 구축

얼마 되지 않아, 몇 일 동안 제 아이디어의 핵심을 담은 앱, 즉 우선 증명 개념 버전을 사용해왔어요. 그리고 정말 좋아해요. 처음으로 여섯 번째 메시지를 받을 때까지 기다릴 수가 없네요.

이제는 뼈대에 고기를 붙여 완전히 다듬어진 2FA 앱을 구축할 때입니다. 이전에 설명한 대로, 실제로 4가지 주요 새로운 기능만 추가하면 됩니다:

- 2FA QR 코드를 스캔하여 안전하게 키체인에 저장하기
- 사용자 인터페이스에서 여러 2FA 계정을 표시하고 관리하기
- 사용자가 중요하게 여기는 번호 설정하기
- 더 많은 종류의 재미있는 기능 구현하기

<div class="content-ad"></div>

마지막으로, 기능적이 아닌 요구 사항: 매우 느린 코드 생성을 최적화하는 작업을 해야 합니다. 배치 처리 또는 로컬 지속성을 활용할 수도 있을 것 같아요.

## 인간 인터페이스 가이드라인

디자인에 대해 별다른 특별한 것을 할 계획은 없어요. 표준 애플 List 뷰 구성 요소를 사용하여 HIG를 따를 것이에요.

UX는 멋지고 간단하게 유지합시다: 주로 푸시 알림에 기능이 집중되어 있으며 매우 완벽해요. 이 말은 QR 스캐너와 설정을 툴바 버튼 뒤에 숨기고, 모달 플로우가 표시되도록 하는 것을 의미해요.

<div class="content-ad"></div>


![Scanning 2FA Secrets](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_11.png)

## Scanning 2FA Secrets

A couple of open-source libraries will save me a ton of time on cookie-cutter tasks. CodeScanner to supply simple SwiftUI QR code scanning, and KeychainAccess to easily store these 2FA account secrets in the keychain.

![Scanning 2FA Secrets](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_12.png)


<div class="content-ad"></div>

해당 스캐너 라이브러리는 카메라 접근을 사용하여 QR 코드를 쉽게 구문 분석할 수 있는 URL로 변환합니다. 아래와 같은 형식으로 변환됩니다:

```js
otpauth://totp/Google%3Atest%40gmail.com?secret=bv7exx7sltbcqffec1qyxscueydwsu5h&issuer=Google
```

이제 앱에 우리의 계정을 쉽게 추가할 수 있게 되었어요!

<img src="/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_13.png" />

<div class="content-ad"></div>

## 선호하는 숫자 선택하기

SwiftUI @AppStorage를 사용하여 List 및 몇 가지 Toggles와 함께 사용하면 쉽게 사용자 설정 화면을 구축할 수 있습니다.

![image](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_14.png)

나는 onDisappear에서 부모 뷰에게 다시 숫자 처리를 시작하고 알림을 다시 예약하라고 알리기 위해 클로저를 사용했습니다. 이것은 토글이 변경될 때마다 비싼 계산을 실행하는 대신 모든 것을 일괄 처리하는 가장 간단한 방법이었습니다.

<div class="content-ad"></div>

```swift
// CodeView.swift

var body: some View {
    // ...
    .sheet(isPresented: $showSettings) {
        SettingsView(onDisappear: {
            viewModel.recomputeNotifications()
        })
    }
}
```

## Belated Customer Research

안녕하세요, 저는 독립 개발자에요. 프로젝트 빌드 과정 중간에 이것을 할 수 있어요!

몇 가지 다른 2FA 앱을 다운로드해서 아이디어를 베낄 만한 것이 있는지 살펴보기로 결정했어요. 사실, 꽤 혼잡하고 경쟁력 있는 앱 시장을 기대했는데, 이 중 일부는 정말 형편없었어요.

<div class="content-ad"></div>

아래는 Markdown 형식으로 변환:


![이미지1](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_15.png)

정말이지, 그 중 50% 이상이 매우 공격적인 페이월을 설정해 놓고 사용하기 전에 이겼다... 무료 옵션이 완전히 제공되는 상황일 때 말이죠.

이 페이월이 넘쳐난다 해도, 좋은 아이디어를 빌려올 수 있었던 몇 가지를 기록해 두었어요.

![이미지2](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_16.png)


<div class="content-ad"></div>

## 여러 개의 2FA 계정

여러 개의 계정을 가진 사람들에겐 이것이 매우 중요하죠. 계정이 많으면 더 많은 GET 기회가 생기기도 하거든요!

내 키체인 코드를 업데이트했어요. 이제 여러 개의 QR 코드를 스캔할 수 있게 되었고, 계정 데이터(비밀 정보를 포함한)도 저장하게 되었어요. 이제 나의 다양한 계정으로 로그인하는 데 완벽하게 작동했어요!

![QR 코드 이미지](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_17.png)

<div class="content-ad"></div>

저는 적절한 내장 목록 기능을 구현하여 이제 우리가 더 이상 필요하지 않은 코드를 삭제할 수 있습니다.

경쟁사 분석을 하면서, 구글 인증 프로그램이 예전 아이폰에 추가한 2단계 인증 코드를 몇 년 전부터 계속 보관하고 있는 것을 발견했어요!

그때 제 데이터 계층에서 두 가지 실수를 발견했습니다.

- iCloud와 동기화하지 않았던 점
- 키패인 밖에 계정을 지속하려고 한 점

<div class="content-ad"></div>

먼저, iCloud에 대한 키체인을 동기화하면 계정이 다른 모든 Apple 기기에 나타납니다. Keychain Access 라이브러리를 사용하면 쉽게 할 수 있어요:

```js
// KeychainManager.swift

self.keychain = Keychain().synchronizable(true);
```

둘째, 저는 반짝이는 물건 증후군으로 고생했어요: SwiftData를 영속성 레이어로 사용하기 위해 서두르다 보니 Keychain만을 비밀로 사용하고 나머지 계정 메타데이터는 새로운 프레임워크를 통해 영속화하고 있었어요.

이것은 다른 장치에서 나의 계정을 가져올 수 없었다는 것을 의미해요 — 비밀만으로는 쓸모가 없어요!

<div class="content-ad"></div>

그래서 전 Account 객체 전체를 키체인에 저장해야 한다는 것을 깨달았어요.

새로운 접근 방식은 QR 코드 URL을 키체인에 그대로 저장하는 것입니다. 이제 Account 객체 자체는 일시적입니다; 앱을 로드할 때마다 URL에서 다시 계산됩니다.

이렇게 하면 로드할 때마다 Accounts가 로그인한 모든 iDevice에 나타날 수 있습니다! 이 일시적인 방식은 두 마리의 새끼를 한 방에 잡는 멋진 방법입니다. 이제 필요할 때 키체인에서 Accounts를 가져올 때 사용합니다:

```swift
// AccountManager.swift

func fetchAccounts() throws -> [Account] {
    try KeychainManager.shared.fetchAll()
        .compactMap { createAccount(from: $0) }
}

private func createAccount(from urlString: String) -> Account? {
    guard let url = URL(string: urlString),
          let account = SecretURLParser.shared.account2FA(from: url) else {
        return nil
    }
    return account
}
```

<div class="content-ad"></div>

일반 코딩 작업을 많이 하여 UI를 개선하고 코드를 잘 리팩토링하는 작업을 했어요. 그런데 개발 과정에서 흥미로운 것들도 몇 가지 있었답니다.

## 계정 아이콘 찾기

이건 꽤 좋은 기능이에요. 하지만 최고의 오픈 소스 앱이 똑같은 일을 하길래, 적어도 그것만큼 좋아야 한다고 느꼈어요.

다행히도, 웹 사이트에서 FavIcon을 검색하고 여러 해상도로 다운로드할 수 있는 Google API가 있답니다.

<div class="content-ad"></div>

웹사이트를 어떻게 디자인하면 좋을지 고민 중이군요. QR 코드의 발행자 속성을 사용하여 .com 도메인을 시도하는 방법으로 좋은 결과를 얻었다고 하셨군요.

```swift
struct FavIcon {

    let url: URL

    init(issuer: String) {
        let domain = "\(issuer).com"
        let url = URL(string: "https://www.google.com/s2/favicons?sz=128&domain=\(domain)")!
        self.url = url
    }
}
```

아이콘의 빠른 로딩을 위해 CachedAsyncImage 라이브러리를 사용한 거군요. 이렇게 하면 성능이 더 빨라질 거에요.

![이미지](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_18.png)

<div class="content-ad"></div>

배경 제거를 처리하고 아이콘을 좀 더 돋보이게 만들기 위해 Metal 셰이더를 추가했어요.

여기 SwiftUI View 확장 부분이에요:

```js
//  View+ColorEffect.swift

import SwiftUI

extension View {

    func eraseBackground(backgroundColor: Color = Color(uiColor: UIColor.secondarySystemBackground)) -> some View {
        modifier(EraseBackgroundShader(backgroundColor: backgroundColor))
    }
}

struct EraseBackgroundShader: ViewModifier {

    let backgroundColor: Color

    func body(content: Content) -> some View {
        content
            .colorEffect(ShaderLibrary.eraseBackground(
                .color(backgroundColor)
            ))
    }
}
```

그리고 물론 MSL 셰이더 코드도 있습니다.

<div class="content-ad"></div>

```cpp
#include <metal_stdlib>
#include <SwiftUI/SwiftUI_Metal.h>
using namespace metal;

[[ stitchable ]]
half4 eraseBackground(
    float2 position,
    half4 color,
    half4 backgroundColor
) {

    if (color.r >= 0.95 && color.g >= 0.95 && color.b >= 0.95) {
        return backgroundColor;
    }

    return color;
}
```

여기에 그들이 어떻게 보이는지 있어요. 그들은 나쁘지 않지만 놀라운 것은 아니에요.

![image](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_19.png)

난 지나치게 엔지니어링을 시작했어. 이것에 핀을 꽂아 놓고 나중에 다시 생각해 봐요.



<div class="content-ad"></div>

## UI 디자인 개선

기본 2단계 인증 앱으로서 이미 꽤 잘 작동하고 있습니다.

대부분의 사람들을 앞선 존재가 되기 위해서는, 극도로 공격적인 유료 벽을 두지 않으면 되는구나 ($4.99 매주? 정말?!)

일부 기본 소프트웨어 개발 작업을 통해 타이밍, 기본 UI 및 데이터 저장 작업을 수행한 후, 이제는 정말로 아주 잘 작동하고 있습니다. - 기본 SwiftUI 구성 요소를 사용하는 것은 작업이 "그냥 작동"되도록하는 빛나는 방법입니다*.

<div class="content-ad"></div>



![Image](https://miro.medium.com/v2/resize:fit:1400/1*TUkFn9ejk93WczTL02Jlrg.gif)

저는 경쟁사 조사를 통해 찾은 tap-to-copy와 같은 편의 기능 몇 가지를 구현했어요.

@ScaledMetric와 ViewThatFits 같은 접근성 도구를 활용하여 시각적 요구에 관계없이 앱이 원할하게 작동하도록 했어요. Apple의 기본 SwiftUI 구성 요소와 색상에 밀접하게 따라가면서 무료로 라이트 모드도 구현되었어요.

```js
// AccountView.swift

@ScaledMetric(relativeTo: .largeTitle) private var iconSize: CGFloat = 36

private var icon: some View {
    CachedAsyncImage(url: FavIcon(issuer: account.issuer).url, content: {
        $0
            .resizable()
            .aspectRatio(contentMode: .fit)

    }, placeholder: {
        Text(String(account.issuer.first?.uppercased() ?? account.name.first?.uppercased() ?? ""))
            .font(.largeTitle)
            .monospaced()
    })
    .frame(width: iconSize, height: iconSize, alignment: .center)
}

private var code: some View {
    ViewThatFits {
        HStack(alignment: .center, spacing: 16) {
            codeText
        }
        VStack(alignment: .leading, spacing: 4) {
            codeText
        }
    }
}
```

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_20.png" />

## 앱을 더 흥미롭게 만들기

진정한 핵심 가치 제안을 개선하기 위해 흥미로운 옵션을 더 많이 구현했습니다:

- 000000과 같은 섹스투플렛 및 쿼드투플렛
- 012345와 같은 순서대로 계속되는 수열
- 300000처럼 백만단위의 수
- 000001과 같은 일의 자리, 000010과 같은 십의 자리
- 원주율 파이(314159)와 같은 수학 상수
- 플랑크 상수(6.6x10⁻³⁴)와 같은 물리 상수
- 012210과 같은 회문
- 121212와 123123과 같은 반복된 이차, 삼차 수열

<div class="content-ad"></div>

다음은 Markdown 형식으로 표 태그를 변경한 코드입니다.

```js
func checkThatCounting() -> Bool {
    let characters = Array(self)
    for i in 1..<characters.count {
        if let prevDigit = Int(String(characters[i - 1])),
           let currentDigit = Int(String(characters[i])),
           currentDigit != prevDigit + 1 {
            return false
        }
    }
    return true
}

func checkThatPalindrome() -> Bool {
    self == String(self.reversed())
}

func checkThoseRepeatedThrees() -> Bool {
    self.prefix(3) == self.suffix(3)
}

func checkThoseHunderedThousands() -> Bool {
    suffix(5) == "00000"
}
```

## 확률 이론

이제 설정 UI를 업데이트하여 흔함, 드물음, 초 희귀함으로 정렬하거나 반복, 상수, 순서 또는 라운드 숫자와 같은 유형으로 정렬할 수 있습니다.

<div class="content-ad"></div>

`<img src="/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_21.png" />`

각 희귀 수준의 확률을 어떻게 계산하나요?

012345와 같은 완벽한 숫자 시퀀스의 경우, 1백만 개의 가능한 번호 조합 중 6가지만 가능합니다 (567890까지).

30초에 1백만 개의 조합을 곱하고 가능한 시퀀스인 6으로 나누면, 각 계정당 평균적으로 완벽한 카운팅 시퀀스가 발생할 수 있다는 것은 약 5백만 초마다 발생할 수 있다는 것을 의미합니다 — 평균적으로 58일마다 한 번씩입니다.

<div class="content-ad"></div>

이 정도라니, 정말 희귀한 것이죠.

하지만 123321과 같은 회문 수는 만들 수 있는 3자리 수가 1000개에 달합니다. 이는 평균적으로 매일 0.34번씩 볼 수 있다는 뜻이에요! 훨씬 더 흔하죠.

중간에는 141414와 같이 반복되는 숫자들이 있습니다. 이런 경우 가능한 숫자는 00부터 99까지의 100개인데요, 그래서 이들은 평균적으로 3.5일에 한 번씩 발생합니다. 그래서, 정말 희귀하지만, 극도로 희귀하지는 않은 편이에요.

이 중 일부인 쿼드와 같은 일련 번호들은 조금 더 많은 계산이 필요한데, 그래서 수천만 개의 OTP(일회용 비밀번호)를 생성하고 각 흥미로운 종류별로 발생 빈도를 세어 상대적 빈도를 감을 수 있게 했어요.

<div class="content-ad"></div>

## 성능 향상

앱은 모든 일반적인 흥미로운 코드를 활성화한 상태에서만 아주 빠르게 64개의 흥미로운 2FA 코드를 처리할 수 있지만, 초희귀한 GET만 원할 때는 처리 시간이 오래 걸립니다.

수백만 개의 잠재적인 OTP를 처리하는 동안, 유효한 흥미로운 코드를 발견하자마자 알림을 반환하고 예약하는 것이 필요합니다.

오래된 친구인 Combine 프레임워크는 깔끔한 해결책을 제공해줍니다!

<div class="content-ad"></div>

```swift
// CodeGenerator.swift

var codeSubject = PassthroughSubject<OTP, Never>()

func generateCodes(accounts: [Account]) {
    // ...
    codeSubject.send(otp)
}
```

또한 사용자가 중간에 설정을 변경하는 경우를 대비하여 작업을 취소하고 다시 시작할 수 있도록 일부 작업을 사용했습니다. 작업을 분리함으로써 암호 해독 및 문자열 분석 작업을 UI 스레드에서 유지하지 않을 수 있습니다.

```swift
// CodeViewModel.swift

private var otpComputationTask: Task<Void, Never>?
private var notificationSchedulingTask: Task<Void, Never>?

func recomputeNotifications() {
    handleNotificationScheduling()
    handleOTPComputation()
}

private func handleNotificationScheduling() {
    notificationSchedulingTask?.cancel()
    notificationSchedulingTask = Task.detached(priority: .high) {
        guard await NotificationScheduler.shared.isAuthorized() else { return }
        NotificationScheduler.shared.cancelNotifications()
        for await (code, count) in CodeGenerator.shared.codeSubject.values {
            try? await NotificationScheduler.shared.scheduleNotification(for: code)
        }
    }
}

private func handleOTPComputation() {
    let accounts = accounts
    otpComputationTask?.cancel()
    otpComputationTask = Task.detached(priority: .high) {
        guard await NotificationScheduler.shared.isAuthorized() else { return }
        CodeGenerator.shared.generateCodes(accounts: accounts)
    }
}
```

이제 일정이 시퀀스대로 아니라 하나의 큰 덩어리로 나오지 않고 꽤 부드럽게 작동합니다!



<div class="content-ad"></div>



Scheduled repeatedTwos: 292929 @ 2024-02-25 23:33:30 +0000
Scheduled repeatedTwos: 878787 @ 2024-02-26 06:03:30 +0000
Scheduled quints: 666660 @ 2024-02-26 10:54:00 +0000
Scheduled quints: 255555 @ 2024-02-26 21:11:00 +0000
Scheduled repeatedTwos: 606060 @ 2024-02-26 23:27:00 +0000
Scheduled sexts: 666666 @ 2024-04-16 23:22:00 +0000
Scheduled boltzmannConstant: 141023 @ 2024-04-19 02:05:00 +0000
Scheduled counting: 012345 @ 2024-04-20 04:51:30 +0000
Scheduled planksConstant: 661034 @ 2024-04-20 05:38:00



## 앱 아이콘

이 거래 그냥 사라지고 싶었어요. 앱 아이콘에 진짜로 체크하려면 이제 목멸에 달렸거든요. 정말 완벽해요.

![image](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_22.png)


<div class="content-ad"></div>

하지만 제 친구가 Lionsgate Films의 친구들이 조금 소송을 제기할 수도 있다고 지적했습니다.

하지만 난 그걸 가져야 했어!

어쨌든 희망이 있다면:


![이미지](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_23.png)


<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 변경하세요.

<div class="content-ad"></div>

그냥 개인정보를 꼭 보호하세요!

<div class="content-ad"></div>

달리(DALL-E)는 정말 손등을 그리는 걸 좋아하지 않았어요. 저는 노력해 봤지만요.

# 마지막 손질

그 개념은 증명되었어요. 어플이 잘 작동하고 있어요! Check 'em의 즐거움을 세상에 보여주기 전에 약간의 마무리와 작은 기능을 추가할 시간이에요.

첫 릴리스를 만들기 전에 V1에서 구현할 수 있는 새로운 기능과 버그 수정 사항들을 목록으로 만들었어요.

<div class="content-ad"></div>

```js
// 높은 우선순위 -
// TODO: - 주문 추가 - 저장된 URL에 쿼리 항목으로 정렬 추가
// TODO: - 새로 고침 시 햅틱 진동
// TODO: - 푸시 알림 요청은 설정 화면에 진입했을 때에만
// TODO: - 알림 활성화를 위한 설정 링크 추가
// TODO: - 버그 - 뷰 모델 계정에서 스캔된 중복을 무시 - 이미 있는 경우 계정에 스캔을 추가하지 않음
// TODO: - 설정 화면을 열 때 처리 작업 취소
// TODO: - 푸시 알림 딥 링크 - GET이 여전히 존재할 때 앱 리뷰 프롬프트로 이동
// TODO: - 매우 드문 GET가 전송되지 않는 것 같음?? 시뮬레이터에서 로컬로 발생하지 않지만 quints는 괜찮음 - 큐에 들어가는 것처럼 보임
// TODO: - 버그 - 두 번째 로드에서 진행 상황 보기가 나타나지 않음
// TODO: - 버그 - 뷰 모델 계정에서 스캔된 중복을 무시 - 이미 있는 경우 계정에 스캔을 추가하지 않음
// TODO: - 버그 - 2개의 동시 계산이 있을 때 백분율이 계속 변동하는 버그 발생
// TODO: - QR 및 설정에 TipKit 추가

// 낮은 우선순위 -
// TODO: - 상태 복원을 위해 @SceneStorage 사용 - 키패인 작업을 기다리지 않아도 되도록
// TODO: - 한 단계 또는 뒤로 이동
// TODO: - 딥 링크를 사용하여 "컬렉션" 화면 생성 - 보관된 항목으로 본 GET 수집 (키패인의 사전으로)
```

당연히, 제게 제품 관리자가 없기 때문에 최저 우선 순위의 작업을 즉시 시작했습니다: 딥 링크로 컬렉션을 구축하는 것 - 내 드문 GET가 헛되이 낭비되는 것을 원하지 않아요!

## 컬렉션

이 부분은 원본 개념 증명에서 식별한 문제에 도움이 됩니다: 사용자들이 알림과 상호 작용하도록 유도하여 앱에 재진입하도록 해야합니다.

<div class="content-ad"></div>

컬렉션 뷰를 만드는 것은 조금 까다로울 수 있어요. 왜냐하면 몇 가지 부분을 고려해야 되거든요:

- 사용자가 알림을 탭하고 앱으로 딥 링크 할 수 있게 합니다.
- 탭한 코드의 흥미로운 부분을 안전하게 저장합니다.
- 이것들을 컬렉션 화면에 렌더링합니다.

알림에 딥 링크를 추가하는 것은 꽤 간단했어요.

```js
// Notifications.swift

// ...
content.userInfo = ["deepLink": "checkem://\(otp.code)"]
```

<div class="content-ad"></div>

그러나 약간 거슬리지만, 알림을 처리하기 위해 AppDelegate를 만들어야 했어요 — SwiftUI는 아직 완전히 자체적으로 이를 다루지 못합니다.

```swift
// AppDelegate.swift

func userNotificationCenter(_ center: UNUserNotificationCenter,
                            didReceive response: UNNotificationResponse,
                            withCompletionHandler completionHandler: @escaping () -> Void) {

    let userInfo = response.notification.request.content.userInfo

    if let deepLinkString = userInfo["deepLink"] as? String,
       let deepLinkURL = URL(string: deepLinkString) {
        guard let code = deepLinkURL.code else { return }
        try? CollectionManager.shared.save(code: code)
    }

    completionHandler()
}
```

마지막으로, Keychain에 저장된 코드들의 긴 쉼표로 구분된 목록을 게으르게 추가했어요.

```swift
// KeychainManager.swift

func storeCollectionItem(code: String) throws {
    var collection = try keychain.get(Constants.collectionKey) ?? ""
    if !collection.isEmpty {
        collection.append(",")
    }
    collection.append(code)
    try keychain.set(collection, key: Constants.collectionKey)
}
```

<div class="content-ad"></div>

빠르게 출시하기 위한 욕망의 결과인 것이지, 신중하게 고려된 엔지니어링 결정의 결과는 아닙니다. 사용자가 Keychain 항목 당 4kB의 소프트 제한에 가까워지는 경우 후회할 수 있는 결정이었습니다(하드 제한은 대략 16MB 이므로 괜찮을 것 같아요!).

이 작업은 빠르게 성과를 거두었는데, 컬렉션 화면은 빠르게 내 소중한 GET들로 가득 차기 시작했어요!

![이미지](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_27.png)

원래는 사용자가 알림을 탭할 때까지 컬렉션을 숨겼었는데, 사용자가 '모두 수집하기'에 도전하도록 하는 것이 더 매력적이라고 깨달았어요.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_28.png" />

## 진동

iOS 17 sensoryFeedback API를 사용하면 매우 섬세한 진동을 재생할 수 있습니다. 사실 너무 섬세해서 저는 마음에 들지 않았어요. 그래서 Carbn에서 진동 엔진을 뽑아내어 여기에 재사용했어요.

기존 새로고침 코드에 정말 가혹한 부작용을 추가했어요:

<div class="content-ad"></div>

```swift
// CodeView.swift

.onReceive(timer) { _ in
    let didChange = viewModel.refresh()
    if didChange {
        HapticEngine.shared.play(haptic: .refresh)
    }
}
```

집에서는 시도하지 마세요, 친구들!

## 이미지 로딩 버그

CachedAsyncImage 라이브러리에서 FavIcons가 존재하지 않음에도 불구하고 열심히 로딩되는 버그가 있습니다. 이로 인해 희미한 지구 모양이 나타납니다... 하지만 이대로 릴리스할 것 같습니다.

<div class="content-ad"></div>

대부분의 경우 90% 정도는 잘 작동하며, 제가 개발한 Third-party SwiftUI 라이브러리 중 하나를 바꾸는 대신 배포하는 게 더 좋을 것 같아요.

![이미지](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_29.png)

## 중복 버그

배포 전에 나머지 버그들 중 일부는 더 주의 깊게 살펴보았지만, 이 문제는 특히 나쁩니다. 누군가가 QR 코드를 두 번 스캔하면 동일한 계정의 이상한 중복이 발생할 수 있습니다.

<div class="content-ad"></div>

```js
// TODO: - 버그 - 뷰 모델 계정에서 스캔된 중복을 무시하도록 해주세요 - 이미 있는 경우 계정에 스캔을 추가하지 마세요
```

시간을 절약할 수 있는 라이브러리를 뜯어내고 교체하는 대신, 이 버그는 한 줄의 코드 수정으로 해결되었습니다.

```js
// CodeViewModel.swift

func create(account: Account, url: URL) throws {
    guard !accounts.contains(where: { $0.name == account.name }) else { return }
    // ...
}
```

2FA 계정을 이름을 기준으로 하는 키체인이기 때문에, 이 수정은 매우 합리적입니다.

<div class="content-ad"></div>

## 코드가 로드되지 않음

코드가 대기열에 들어가지 않는 다른 문제를 발견했어요.

```js
// TODO: - 초희귀한 GET가 전송되지 않나요?? 에뮬레이터에서 로컬에서 발생시키질 못하겠는데 퀸트는 잘 됩니다 - 들어가야 할 것 같아요
```

알고 보니 @AppStorage가 실제로 작동하는 방식을 잘못 이해했던 것 같아요 — 기본값은 실제로 사용자 기본 설정에 저장하는 대신 UI에만 적용됩니다.

<div class="content-ad"></div>

```swift
// SettingsView.swift

@AppStorage("sexts") private var sexts: Bool = true
```

첫 번째 앱 로드 시 UserDefaults를 채우는 함수가 이 문제를 해결했습니다.

```swift
// CheckEmApp.swift

@main
struct CheckEmApp: App {

    init() {
        initializeDefaultsIfRequired()
    }

    // ...

    func initializeDefaultsIfRequired() {
        guard UserDefaults.standard.object(forKey: "sexts") == nil else { return }
        CodeGenerator.shared.initializeDefaults()
    }
```

## TipKit


<div class="content-ad"></div>

새로운 iOS 17 TipKit를 사용하여 조금 더 개선했어요. 사용자가 앱을 처음 로드할 때 무엇을 해야 하는지 간단히 이해할 수 있게 도와주는 기능이 추가되었답니다.

![app screenshot](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_30.png)

이 새로운 API로 구현하는 것이 의외로 간단했어요.

```js
// CodeView.swift

@ViewBuilder
private var tips: some View {
    TipView(QRTip()).tipImageSize(CGSize(width: tipImageSize, height: tipImageSize))
    TipView(SettingsTip()).tipImageSize(CGSize(width: tipImageSize, height: tipImageSize))
    TipView(CollectionTip()).tipImageSize(CGSize(width: tipImageSize, height: tipImageSize))
}
```

<div class="content-ad"></div>

## 상점 상품 목록

출시할 준비가 끝났다고 생각해요.

![상품 목록](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_31.png)

AppScreens를 통해 상점 상품 목록을 설정 중이에요. 저희의 캣츠가 등장하는 Check 'em의 진정한 힘을 보여주는 두 번째 스크린샷을 확인해보세요.

<div class="content-ad"></div>

---

![이미지](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_32.png)

진짜요?

![이미지](/assets/img/2024-05-23-The2FAappthattellsyouwhenyouget012345_33.png)

알겠어요, 제가 세계에서 가장 자유주의자적인 사람은 아니지만, 시장을 1% 증가시키기 위해 여러 가지 번거로움을 겪는 건 싫어요. 좀 더 나은 방법이 있지 않을까요!

<div class="content-ad"></div>

(내 프랑스 독자 여러분 죄송해요)

간단히 말해서, 우리는 앱 스토어 커넥트에 설정을 완료했고 버튼을 누르기 ready해요!

## 결론

저의 여정을 따라 읽어주셔서 감사합니다!

<div class="content-ad"></div>

이 프로젝트는 정말 재미있었어요! 패턴을 찾는 것을 좋아하는 내 내재적인 IT 열정을 만졌을 뿐만 아니라, 멋진 처리, 스레딩, 최적화 문제를 다루어 볼 수 있었어요!

다음 단계로, v1.1 릴리스에 대한 성능에 완전히 초점을 맞춘 상태입니다. 이제 OTP(일회용 암호)를 더 빠르게 처리하고 빠르게 불러올 거예요!

이 앱을 좋아하시면, 보고 싶은 숫자에 대한 의견을 주세요! 마지막으로, Android 버전을 원하는 분들이 계신다면, 제 소스 코드를 공유해드리고 함께 개발할 수 있습니다.
