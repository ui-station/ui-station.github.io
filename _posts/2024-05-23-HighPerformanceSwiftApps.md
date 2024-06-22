---
title: "높은 성능의 Swift 앱"
description: ""
coverImage: "/assets/img/2024-05-23-HighPerformanceSwiftApps_0.png"
date: 2024-05-23 15:03
ogImage:
  url: /assets/img/2024-05-23-HighPerformanceSwiftApps_0.png
tag: Tech
originalTitle: "High Performance Swift Apps"
link: "https://medium.com/gitconnected/high-performance-swift-apps-dfedd3994090"
---

두 주 전에 Check ‘em: The Based 2FA 앱을 출시했어요.

그 컨셉은 정말 간단했어요: 정말 멋진 숫자가 나타날 때마다 알림을 보내주는 2단계 인증 앱이에요. 알림을 탭하여 그 숫자를 영구적으로 컬렉션에 추가할 수 있어요.

Reddit의 착한 사용자들은 저의 아이디어, 증명 개념, 출시까지를 즐겁게 받아주셨어요. 그래서 저를 프로그래밍 카테고리에서 세 번째로 올려놓게 해주셨어요.

![image](/assets/img/2024-05-23-HighPerformanceSwiftApps_0.png)

<div class="content-ad"></div>

Check ‘em은 수백만 개의 2FA 코드를 미래로 계산하고 각 숫자를 처리하여 흥미로운 코드인지 확인하고 푸시 알림(GETs)으로 흥미로운 코드를 예약합니다.

v1.0은 꽤 잘 작동하지만, 오늘은 성능에 집중할 거에요.

제 앱 성능 평가 프로세스는 세 가지 단계로 진행됩니다:

- 실제 기기에서 테스트하여 사용자를 직면한 문제를 식별합니다.
- 병목 현상을 식별하기 위해 Instruments를 사용하여 앱을 프로필링합니다.
- 이를 가이드로 사용하여 코드 개선을 구현합니다.

<div class="content-ad"></div>

이 문제 우선 접근 방식을 통해, 자전거 에관한 논의를 피하고 실제 병목 현상에 집중할 수 있어요.

이 방법을 통해 사용자들에게 혜택을 주지 않는 일반적인 코드 개선에 시간을 낭비하지 않아도 돼요 (그래서 싱글톤이 여전히 사용되는 이유입니다 — 죄송하지만 이대로 가야겠죠).

# 디바이스에서의 테스트

## 처리 속도

<div class="content-ad"></div>

큰 성능 문제는 바로 여기에 있어요: 2단계 인증 코드를 계산하는 동안 숫자를 처리할 때 발생합니다. 이 처리는 앱에 로그인할 때, 2단계 인증 계정을 변경하거나 GETs 선택 사항을 업데이트할 때마다 실행됩니다.

TOTP 계산 자체는 간단하지 않은 기능으로, 바이트 조작, 문자열 생성 및 암호 작업이 포함됩니다. 이 코드들은 그 후에 여러 가지 흥미로운 유형으로 확인되며, 가장 흥미로운 코드에 대한 푸시 알림을 예약합니다.

이 처리는 사용자가 일반 GETs(예: 004444 또는 123321과 같은 회문)를 활성화한 경우에는 꽤 빠릅니다. 이들 코드 중 몇 개를 하루에 발견할 때, 미래까지 계산할 필요가 없어서 64개의 알림(이OS에서의 한도)을 예약할 때까지 계속 실행됩니다.

사용자가 희귀한 GETs(예: 055555 또는 012340와 같은 근처 계수 순서)를 보고 싶어하는 경우, 총 처리 시간이 10초를 초과할 수 있습니다.

<div class="content-ad"></div>


![image](https://miro.medium.com/v2/resize:fit:1400/1*e66r8CS6JsTQmQzq_wIytA.gif)

Rarity increases exponentially with each tier. With only ultra-rare GETs enabled, such as sexts (e.g. 666666) or counting sequences (e.g. 012345), my screamingly powerful A17 chip takes take over a minute. This increases even more if you only choose one or two ultra-rare options.

As expected, this is by far the biggest performance bottleneck.

## Time-to-first code


<div class="content-ad"></div>

앱이 상당히 간단해서 Swift로 1500줄 정도밖에 안 되기 때문에 잠재적 성능 문제의 표면적인 영역은 꽤 제한적입니다.

한 가지 조금 불편한 문제는 앱을 새로 실행할 때마다 발생합니다. 자체적인 런칭이 번개처럼 빠른데, 처음 2FA 코드가 나타나기까지 약간의 시간이 걸립니다.

사용자가 이 앱을 일상에서 2FA 코드로 사용했으면 좋겠기 때문에 유용한 코드가 즉시 나타나면 이 앱의 기능적 사용 사례를 위한 사용자 경험을 향상시킬 것입니다.

![GIF](https://miro.medium.com/v2/resize:fit:1400/1*D1B8yFO7YH8fN6FHT0_NQA.gif)

<div class="content-ad"></div>

# Instruments를 이용한 프로파일링

이제 두 가지 주요 사용자 성능 문제를 식별했으므로 Xcode Instruments를 사용하여 자세한 분석을 수행할 수 있습니다. 이 프로파일링을 통해 코드의 병목 현상을 정확히 감지할 수 있습니다.

## Instruments 설정

성능 프로파일링에 새로 오신 분들을 위해 Instruments는 별도의 앱으로, Xcode 개발자 도구 메뉴에서 열 수 있습니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-HighPerformanceSwiftApps_1.png" />

저희의 주요 도구인 Time Profiler는 CPU 코어를 모니터링하는 도구입니다. 이 도구는 초당 1000번씩 이 코어들을 샘플링하며 실행 중인 함수들의 스택 추적을 기록합니다. 결과적으로 나타나는 보고서에는 코드의 어떤 부분이 컴퓨팅 자원을 독식하는지 보여줍니다.

<img src="/assets/img/2024-05-23-HighPerformanceSwiftApps_2.png" />

이제 모든 설정이 완료되었으니 조사를 시작할 수 있습니다.

<div class="content-ad"></div>

## 처리 속도

우리 앱을 열고 알림 재계산을 모니터링하면, Check 'em이 흥미로운 코드를 찾을 때 약 20초 정도 소요되는 비동기 프로세스가 실행되는 것을 볼 수 있습니다.

이를 이해하기 쉽게하기 위해 'Call Tree' 메뉴에서 토글할 수 있는 설정이 3가지 있습니다:

- '스레드별 분리'는 기능 호출을 같은 그룹으로 묶는 대신 프로세스를 스레드별로 분리합니다. 이렇게 하면 병렬로 실행되는 프로세스가 개별적으로 시간 프로필이 표시됩니다.
- 'Call Tree 반전'은 측정된 되추적(traceback)을 반전시킵니다. 즉, CPU에서 실행되는 저수준 iOS 시스템 호출이 아닌 우리 자신의 기능을 호출 스택 상단에 표시할 수 있습니다.
- '시스템 라이브러리 숨기기'는 시스템 라이브러리 프로세스를 제거하여 보고서를 정리하고 자신의 코드를 쉽게 찾을 수 있도록 합니다.

<div class="content-ad"></div>

이제 우리는 예상대로 백그라운드 스레드에서 발생하는 모든 처리 작업을 쉽게 볼 수 있습니다.

우리의 TOTP 생성은 복잡하지만 매우 명확한 알고리즘을 사용합니다. 만약 이를 가속화하는 방법을 개척했다면, 나는 썬더한 어딘가에서 요트 위에서 이 기사를 쓰고 있을 것입니다.

내가 말하려는 것은, 우리가 이 TOTP 생성 과정을 가속화할 수 있는 새롭고 반짝이는 알고리즘을 찾기는 어렵다는 것입니다. 따라서, 우리는 먼저 OTP.init() 메서드보다 느리게 동작하는 모든 것을 개선하고 싶습니다.

우리는 증거를 찾았습니다. 지금까지 가장 느린 계산은 checkThoseSexts() 메서드인데, 이 메서드는 엄청난 계산 비용이 드는 것으로 보이는 정규식을 감싸고 있습니다.

<div class="content-ad"></div>

```js
extension String {
    func checkThoseSexts() -> Bool {
        (try? /(\d)\1\1\1\1\1/.firstMatch(in: self)) != nil
    }
}
```

이런 극도의 부하를 격는 대체 방법으로의 정규식 교체가 성능을 거의 두 배로 향상시킬 수 있을 것으로 생각됩니다.

이 분석을 통해 더 깨달은 것이 있습니다: 이러한 과중 처리는 단일 백그라운드 스레드에서 직렬로 실행되며, 모든 중요한 작업이 고우선순위 인배체 Task로부터 생성된 \_dispatch_workloop_worker_thread에 전적으로 할당됩니다.

<div class="content-ad"></div>

단일 CPU 코어가 TOTP를 생성하고 그들의 흥미로움을 확인하는 역할만 한다는 뜻입니다. 만일 똑똑하게 처리한다면, 이 프로세스는 병렬화될 수 있을 것입니다.

## 첫 번째 코드 작성 시간

다른 사용자가 직면하는 문제는 앱을 열고 유용한 2단계 인증 코드가 표시되기까지의 상대적으로 느린 시간입니다.

코드에서 찾아야 하는 정확한 시작점과 끝점을 알고 있다면 — 앱을 실행하여 처음 나타나는 코드까지 — print() 문과 타임스탬프를 사용하여 이 문제를 프로파일링할 수 있습니다.

<div class="content-ad"></div>

```js
07:35:54.2640 - 앱 초기화
07:35:56.0280 - 코드 표시

```

현재 앱은 앱을 초기화한 후 유용한 2FA 코드를 보는 데 매우 큰 1.764초가 소요됩니다. 출시부터 코드 생성까지의 코드 경로를 따르면 더 명확한 그림이 나타납니다:

```js
07:50:10.5640 앱 초기화
07:50:11.1980 나타날 때
07:50:11.2290 계정 설정
07:50:12.1630 계정 새로고침
07:50:12.1640 코드 표시
```

여기에는 두 번의 비교적 긴 대기 시간이 표시됩니다:

<div class="content-ad"></div>

- 메인 뷰에서 앱 초기화와 onAppear 작동 사이의 시간
- 뷰 모델에서 계정 설정 및 TOTP 코드를 얻기 위해 이를 새로 고칠 때의 시간

키체인 작업을 통해 계정을 가져오고 이어지는 코드 생성 단계는 실제로 상당히 빠르기 때문에 총 시간에 큰 영향을 미치지 않습니다.

이제 우리는 주요 성능 병목 현상을 확인했으므로, 정확한 코드 개선을 시작할 수 있게 되었습니다.

# 코드 개선

<div class="content-ad"></div>

Check ‘em의 기본 실행에서 두 가지 성능 문제가 발견되었습니다:

- 수백만 개의 TOTP 처리 및 흥미로운지 확인
- 초기 2FA 코드의 느린로딩

우리의 자세한 분석을 토대로 우리는 다음과 같은 3가지 구체적인 개선점을 발견했습니다:

- 흥미로움을 평가하기 위해 사용되는 regex를 더 효율적인 알고리즘으로 대체합니다.
- TOTP와 흥미를 계산하는 병렬성을 도입합니다.
- 초기 코드 생성을 런칭 후 빨리 실행하도록 변경합니다.

<div class="content-ad"></div>

이 앱에서 얼마나 많은 속도를 뽑아낼 수 있는지 확인해봅시다!

## 효율적인 알고리즘

Instruments를 사용하여 첫 번째 큰 병목 현상은 느린 정규식으로 발견되었습니다. 총 처리 시간의 50% 이상을 차지합니다.

계산에서는 극히 드물게 발생하는 GET만 사용하여 우리의 결과를 벤치마킹해 봅시다.

<div class="content-ad"></div>

흔하지 않으니까 이 CPU는 이 흥미로운 GET을 찾기 위해 TOTP를 100배 더 많이 연산해야 해요. 그래서 64개의 흥미로운 GET을 찾는 데는 훨씬 더 오래 걸려요.

우리는 시간을 경신해야 합니다!

이 백그라운드 스레드에서 처리에 47초가 걸렸어요. 여기 정규식에 대한 제 첫 번째 대안을 제시할게요. 간단한 문자열 매치 방법을 사용했어요.

```js
func checkThoseSexts() -> Bool {
    checkRepeatedDigits(count: 6)
}

// 쿼드와 퀸트를 확인할 수 있도록 잘 구성되어 있어요
private func checkRepeatedDigits(count: Int) -> Bool {
    (0...9).map {
        String(repeating: String($0), count: count)
    }.contains(where: { self.contains($0) })
}
```

<div class="content-ad"></div>

한 번의 단일 기능 변경으로 섹츄플 확인 작업 자체가 10배 이상 빠르게 동작하여 총 연산 시간이 21초로 줄었습니다. 그래 동작이 두 배 가까이 빨라졌어요!

병목 지점에 집중하는 힘을 보여준 거죠.

저는 멋진 코드를 작성했지만 실제로 상당히 비효율적이에요. 000000부터 999999까지 10개의 문자열을 할당하고, 이후에는 섹츄플용 TOTP를 확인할 때마다 O(n) 문자열 일치 작업을 수행하고 있습니다. 따라서 이 작업은 여전히 TOTP 생성 자체에 이은 두 번째로 무겁고 이루어지고 있어요.

더 간편한 방법이 있습니다. 수 많은 숫자 목록을 하드코딩하는 방식이죠. 이를 통해 비용이 많이 드는 문자열 할당을 반복하는 일을 피할 수 있어요. 우리는 퀘트, 퀸트, 그리고 카운팅을 찾는 무거운 작업에도 동일한 방법을 적용할 수 있어요.

<div class="content-ad"></div>

```js
// Interestingness.swift

func checkThoseSexts() -> Bool {
    Self.sexts.contains(self)
}

private static let sexts: Set<String> = [
    "000000",
    "111111",
    "222222",
    "333333",
    "444444",
    "555555",
    "666666",
    "777777",
    "888888",
    "999999"
]

func checkThatCounting() -> Bool {
    Self.counting.contains(self)
}

private static let counting: Set<String> = [
    "012345",
    "123456",
    "234567",
    "345678",
    "456789",
    "567890"
]
```

We’ve handily eliminated the biggest bottleneck: The checkThoseSexts method has gone from a cumulative time of 27.54s to just 899ms — a 30x increase in speed.

Now, this gives me a bright idea.

There are only 1 million possible 6-digit TOTPs. Perhaps, as an upper bound, 1 in 100 are interesting*.


<div class="content-ad"></div>

만 가지 숫자는 그리 크지 않아요. 이들을 사전(dictionary)과 같은 효율적 데이터 구조에 저장하고, 메모리에 보관하여 어렵지 않게 처리할 수 있습니다. 이렇게 하면 메모리에서 관심 있는 정보를 O(1) 연산으로 조회할 수 있어요.

게다가, 이 방법은 사용자로부터 연산을 내 맥북에게 넘기고 환경을 보호할 수 있어요.

하지만, 이것은 밝은 아이디어이긴 하지만 병목 현상이 아니에요! 그래서 지금은 안타깝지만 이것을 저지를 것 같아요. TOTP 계산이 처리 시간의 대부분을 차지하고 있거든요.

이것은 CPU 코어를 활용하는 적절한 시기입니다.

<div class="content-ad"></div>

# 병렬 처리

현재 모든 계산은 단일 백그라운드 스레드에서 수행됩니다. 이를 어떻게 개선할 수 있을지 고민 중이었어요.

자연스러운 아이디어는 TOTP 계산을 '청크 단위'로 분할하고 각 청크를 다른 스레드에 넣는 것일 것입니다. 그런데 이 청킹을 어떻게 적용할 수 있을까요?

일별, 주별 또는 월별로 그렇게 할 수는 없어요. 왜냐하면 우리가 사용하지 못할 정도로 많은 계산을 실행할 수도 있습니다 — iOS에서는 한 번에 최대 64개의 알림을 예약할 수밖에 없거든요.

<div class="content-ad"></div>

복잡성을 최소화하는 방법을 찾아봅시다.

CPU 아키텍처를 가이드로 삼아보죠: 현대 iPhone 프로세서에는 6개의 코어가 포함되어 있습니다. 그러므로 동시에 6개의 청크를 사용하는 것을 목표로 합시다. taskGroup을 사용하여 이 6개의 병렬 프로세스를 설정할 수 있습니다.

```js
// CodeViewModel.swift

otpComputationTask = Task.detached(priority: .high) {
    await withTaskGroup(of: Void.self) { group in
        (0..<6).forEach { startingIncrement in
            group.addTask {
                CodeGenerator.shared.generateCodes(
                    accounts: accounts,
                    startingIncrement: startingIncrement
                )
            }
        }
    }
}
```

이후, 이 6개의 병렬 프로세스를 실행하여 미래로부터 매 6번째 TOTP 코드를 계산할 수 있습니다. 각 프로세스는 총 흥미로운 숫자 중 1/6을 찾으면 종료됩니다.

<div class="content-ad"></div>

```swift
// CodeGenerator.swift

func generateCodes(accounts: [Account], startingIncrement: Int) {
    var increment = startingIncrement
    var interestingCodesCount = 0
    while Double(interestingCodesCount) < (Double(Constants.localNotificationLimit - 2) / 6).rounded(.up) {
        // TOTP calculation ...
        increment += 6
    }
}
```

이것은 더 빠르게 처리되는 것 같았지만 완전히 UI가 멈춰 버렸어요. TOTP 코드를 처리하는 동안 앱이 거의 멈추고 응답하지 않았어요.

이는 6개의 고우선순위 프로세스를 한 번에 강제로 일어나게 해서, 메인 스레드가 이 비용이 많이 드는 계산과 렌더링 주기를 공유해야 했기 때문이에요.

이는 6개의 코어를 가진 많은 오래된 아이폰에서 더욱 나빠질 것입니다. 그래서, 프로세스의 수를 CPU 코어당 하나로 제한하고, UI 스레드를 위한 하나의 코어를 여분으로 남겨두는 것이 좋겠어요.

`

<div class="content-ad"></div>

```js
ProcessInfo.processInfo.processorCount - 1
```

이야~ 이제 꼭 좋아졌네요! 이렇게 하면 5개의 무거운 작업 쓰레드와 한 개의 아주 차분한 UI 쓰레드가 나올 거예요. 홈으로 돌아가서 손 편지를 쓸 만큼의 지루한 시간은 전혀 없어요.

병렬 처리를 통해 우리가 가장 멀리 호송시키던 쓰레드의 처리속도를 최악 6.74초로 낮춰버렸어요. 이전의 15.37초 대비 56% 감소했답니다.

우리는 5개의 코어를 사용하고 있어요. 왜 이건 깔끔하게 5배 더 빨라지지 않는 걸까요?

<div class="content-ad"></div>

## 수학과 배우들

우리의 방식에는 여전히 문제가 있습니다: 각각의 n개 스레드는 독립적으로 흥미로운 숫자를 찾습니다. 그 숫자는 64/n을 찾을 때까지 계속 찾고 있습니다.

가장 빠른 스레드는 다음 2주 이내에 64/n개의 코드를 모두 찾을 수 있고, 가장 느린 스레드는 운이 나쁘면 다음 달까지 앞으로 계속해서 계산할 수 있습니다 — TOTPs는 30초 간격으로 배치되어 있으며 코드의 흥미로움은 무작위로 분포됩니다.

이 문제는 두 가지 측면으로 나뉩니다:

<div class="content-ad"></div>

- 먼저, 일부 스레드에서 숫자를 너무 많이 세고, 다른 스레드에서는 너무 적게 세어 잠재적인 숫자들을 놓치고 있습니다.
- 둘째, 스레드가 처리하는 시간이 매우 다르기 때문에 각 스레드가 정확히 동일한 시간을 소요하는 것에 비해 CPU를 효율적으로 활용하지 못하고 있습니다.

이에, 각 스레드가 독립적으로 실행되어 다음 미계산 코드를 계산하도록 하는 대신, 오프셋에서 작업하는 대신 다음 미계산 30초 날짜 증가를 사용하도록 보장하고 상태를 안전하게 공유할 수 있는 방법이 필요합니다.

Swift는 이 문제에 Actors로 깔끔한 해결책을 제공하며, Actors는 상태에 대한 직렬 액세스를 강제합니다.

```js
// CodeIncrementor.swift

actor CodeIncrementor {

    private var _increment: Int = 0

    func increment() -> Int {
        defer { _increment += 1 }
        return _increment
    }
}
```

<div class="content-ad"></div>

첫 번째 병렬화 시도와 비교했을 때, 각 스레드가 독립적으로 모든 5번째 TOTP를 계산하는 것을 허용했던 방법과 달리, 이 접근 방식은 프로세스 간의 조정이 필요합니다.

이 조정은 추가 오버헤드를 발생시킵니다. 각 프로세스는 개별적으로 약간 느려지며, 이는 액터의 시리얼 익스큐터에서 increment()를 호출하기 위해 기다려야 할 수 있기 때문입니다. 한 번에 한 프로세스만이 이를 호출할 수 있습니다.

결과적으로 increment()가 호출될 때마다, 스레드는 올바른 오프셋을 안전하게 얻을 수 있으므로, 우리는 다음에 계산되지 않은 TOTP 코드만 계산하여 그 흥미로움을 평가할 수 있습니다.

```js
// CodeGenerator.swift

func generateCodes(accounts: [Account], incrementor: CodeIncrementor) async {
    // ...
    while Double(await incrementor.codes) < (Double(Constants.localNotificationLimit - 2)) {
        let increment = await incrementor.increment()
        // 날짜 증분에서 TOTP 생성 ...
    }
}
```

<div class="content-ad"></div>

이것이 얼마나 성능을 발휘하는지 측정해 봅시다!

이 액터 기반 접근 방식은 한 번에 여러 개의 스레드를 생성합니다. Swift 동시성은 CPU 코어 당 대략 하나의 스레드가 실행되도록 하여 초기 병렬성 목표에 부합하며, 시스템 인식 협력 스레드 풀을 사용하여 스레딩을 관리합니다.

기기의 instruments에서 제공된 총 누적 실행 시간은 각 스레드별로 합산된 것입니다. 이는 물론 사용자가 기다리는 시간을 대변하는 것은 아닙니다. 적절한 측정을 위해 다시 한 번 신뢰할 수 있는 타임스탬프를 사용하여, 초희귀 GET에 대한 흥미로움 계산의 총 속도를 측정해 봅시다.

## 병렬성이 없는 원래 스피드

<div class="content-ad"></div>


23:10:55.2940 Computation 시작
23:11:09.9220 Computation 완료


총 14.628초 소요됐습니다.

## 청킹과 (n-1) 쓰레드를 사용한 속도


23:07:00.4700 Computation 시작
23:07:09.5980 Computation 완료


<div class="content-ad"></div>

9.128 seconds in total.

## Actor 협조를 사용한 속도

```js
23:01:38.7080 계산이 시작됨
23:01:46.5300 계산이 완료됨
```

7.822 seconds in total.

<div class="content-ad"></div>

이것은 액터를 사용하여 추가 쓰레드 조절 오버헤드를 감당할 가치가 있다는 것을 보여줍니다. 이 프로세스는 더 정확하고 CPU 코어를 더 효율적으로 활용하며, 전체적으로 이 계산 단계를 47% 빠르게 만듭니다!

# 이전 코드 생성

우리의 성능 프로파일링의 마지막 단계에서, print 문과 타임스탬프를 사용하여, 우리의 유저들에게 유용한 2단계 인증 코드가 나타나기 전에 두 가지 큰 대기 상태를 발견했습니다: 메인 뷰에서 onAppear를 기다리는 것과 코드 자체를 refresh() 하는 것을 기다리는 것입니다.

```js
// CodeView.swift

@State private var viewModel = CodeViewModel()
@State private var timer = Timer.publish(every: 1, tolerance: 0, on: .current, in: .common).autoconnect()

var body: some View {
    // ...
    .onReceive(timer) { _ in
        viewModel.refresh()
    }
    .onAppear {
        refreshUI()
    }
}
```

<div class="content-ad"></div>

먼저 onAppear가 나타나도록 기다렸다가 뷰 모델에서 계정을 설정하고, 1초 타이머가 작동할 때 다시 기다립니다.

이는 매우 간단한 수정입니다: 뷰 수명주기 이벤트에서 계정 가져오기 및 TOTP 계산을 분리합니다.

```js
// CodeViewModel.swift

final class CodeViewModel {

    init() {
        timestamp("View model init")
        configureAccounts()
        refresh()
    }

// ...
```

앱 로드와 코드 생성 간의 시간이 1.764초에서 0.400초로 대폭 줄어듭니다.

<div class="content-ad"></div>

```js
20:41:35.6890 앱 초기화
20:41:36.0830 뷰 모델 초기화
20:41:36.0890 코드 표시됨
```

이제 코드들이 거의 즉시 화면에 나타납니다.

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*Z78ZtydZwUrguRDZel0d1w.gif)

# 결론



<div class="content-ad"></div>

Check ‘em — The Based 2FA App은 제가 사이드 프로젝트로 만들었던 열정적인 아이디어였는데, 정말 즐거운 시간을 보냈어요.

제가 기대하지 못한 것은 두 번째(v1.1) 릴리스의 중심에 극도로 흥미로운 성능 최적화 퍼즐을 만날 것이었다는 것이에요.

지금은 앱이 최고로 돌아가고 있어요:

- 2FA 코드가 시작하자마자 나타나요.
- 흥미로운 코드를 찾는 알고리즘이 훨씬 빠릅니다.
- 강력한 멀티 코어 CPU가 병렬 계산으로 완전히 활용되고 있어요.

