---
title: "iOS에서 Compose Multiplatform을 사용하는 앱 이제 베타 - 2024년 개발자 인사이트"
description: ""
coverImage: "/assets/img/2024-05-27-AppsusingComposeMultiplatformoniOSnowbetain2024developerinsights_0.png"
date: 2024-05-27 18:00
ogImage: 
  url: /assets/img/2024-05-27-AppsusingComposeMultiplatformoniOSnowbetain2024developerinsights_0.png
tag: Tech
originalTitle: "Apps using Compose Multiplatform on iOS (now beta!) in 2024 — developer insights"
link: "https://medium.com/@jacobras/apps-using-compose-multiplatform-on-ios-now-beta-in-2024-developer-insights-fe24b224d754"
---


```markdown
Compose Multiplatform for iOS가 오늘 공식적으로 베타로 승격되었어요, 하지만 기업들이 이미 장기간 생산에 성공적으로 사용하고 있어요! 몇몇 애플리케이션을 살펴보죠. iOS에서 Compose Multiplatform (비공식적으로 CMP로 약칭)를 사용한 경험을 설명해 달라고 여러 개발자들에게 물었더니 다음과 같은 이야기를 들었어요.

이것은 2023년에 Kotlin Multiplatform(KMP)을 사용하는 인기 애플리케이션들을 따르는 다음 단계이며, (포브스와 볼트와 같은) 더 많은 애플리케이션이 가입했지만, 이 게시물에서는 사용자 인터페이스를 공유하는 애플리케이션에 중점을 두었어요.

안드로이드와 iOS 버전 모두의 공개 애플리케이션에 대한 링크가 포함되어 있어요, 그래서 직접 시도해 볼 수 있어요(그리고 혹시 머티리얼 리플을 발견할 수 있을지도 😉).

```



<div class="content-ad"></div>

# 인스타박스 (내부 앱)

스웨덴 물류 회사 인스타박스의 개발자들은 수천 명의 사용자가 사용하는 내부 iOS 앱을 SwiftUI로 개발하기 시작했습니다. 그러나 iOS에서 실행되는 Compose 데모를 보고 프로토타입을 만들어 CTO에게 보여줬고, CTO가 좋아해서 계속해서 작업을 진행했습니다. 이미 Kotlin Multiplatform을 사용하여 앱을 구조화했기 때문에 그들은 단 두 주만에 프로토타입을 만들었습니다.

2023년 8월 Talking Kotlin 팟캐스트 에피소드에서 개발자들은 CMP와 함께 작업하는 선택과 프로세스에 대해 설명했습니다. 개발자들은 KMP와 함께 네이티브 API가 간단하게 사용될 수 있고, "그냥 [공통으로] 파일을 옮길 수 있다"는 점이 Kotlin Multiplatform의 장점이라고 특히 만족하고 있습니다.

인스타비 개발자 요한네스 스벤손

<div class="content-ad"></div>

KotlinConf 2024에서 "Compose Multiplatform on mobile at Instabee for over a year"이라는 주제로 발표가 있을 예정입니다. 녹화가 준비되면 이 기사를 업데이트하겠습니다.

스스로 다시 써야 했던 가장 큰 것은 탐색이었습니다. 그 당시에는 아직 멀티플랫폼이 아니었지만, 주요 개발자인 Johannes는 현재 멀티플랫폼 Compose Navigation을 곧 채택할 것으로 말했습니다. 현재 앱은 일부 커스텀 코드(예: 백 제스처 처리)와 함께 Voyager를 사용하여 탐색 중입니다.

# Markaz (1M+)

![이미지](/assets/img/2024-05-27-AppsusingComposeMultiplatformoniOSnowbetain2024developerinsights_1.png)  

<div class="content-ad"></div>

이 앱을 통해 사용자들은 온라인으로 제품을 구매하고 판매할 수 있어요. 비즈니스 로직부터 UI까지 Kotlin 코드를 공유해요. 이 앱은 파키스탄 시장을 대상으로 하고 있기 때문에 모든 국가에서 이용 가능하지 않을 수 있어요. 최근 업데이트에서는 iOS의 백 스와이프 제스처가 추가되었어요. 또한, Compose Multiplatorm에서 기본적으로 제공되는 iOS 룩 앤 필을 통해 네이티브 스크롤링과 같은 기능을 사용할 수 있어요.

마르카즈(Markaz) 개발자 카시프(Me\nhmood)

App Store: https://apps.apple.com/pk/app/markaz-resell-and-earn-money/id6470020517 Play Store: https://play.google.com/store/apps/details?id=com.markaz.app

# Wrike (1M+)

<div class="content-ad"></div>

<img src="/assets/img/2024-05-27-AppsusingComposeMultiplatformoniOSnowbetain2024developerinsights_2.png" />

오늘 KotlinConf에서 발표된 소식입니다: Wrike가 iOS에서 앱의 캘린더 부분에 Compose를 사용하고 있습니다. 리드 개발자는 키노트 비디오에서 다음과 같이 말했습니다 (게시물 맨 아래에 링크가 있습니다):

Wrike 기술 담당자 Alex Askerov

App Store: [여기를 클릭하여 App Store에서 확인하세요](https://apps.apple.com/ms/app/wrike-work-as-one/id890048871)
Play Store: [여기를 클릭하여 Play Store에서 확인하세요](https://play.google.com/store/apps/details?id=com.wrike)

<div class="content-ad"></div>

## Campus (100K+)

![Campus app](/assets/img/2024-05-27-AppsusingComposeMultiplatformoniOSnowbetain2024developerinsights_3.png)

캠퍼스 - 수업 일정 (원래 이름: Кампус - Расписание занятий)은 대학생들을 위한 앱으로, 수업 일정을 추적하는 데 사용됩니다. 이 앱은 주로 네이티브이지만 "과목" 섹션(앱의 두 번째 탭)에서 Compose Multiplatform을 사용합니다. 개발자는 2023년 중반 CMP를 통합했는데, 이는 알파 상태에 도달한 직후입니다.

IceRock (캠퍼스 개발자)의 CTO인 Aleksey Mikhailov

<div class="content-ad"></div>

앱 스토어: [https://apps.apple.com/ru/app/кампус-расписание-занятий/id1534975833](https://apps.apple.com/ru/app/кампус-расписание-занятий/id1534975833)  
플레이 스토어: [https://play.google.com/store/apps/details?id=ru.dewish.campus](https://play.google.com/store/apps/details?id=ru.dewish.campus)  

# Ashampoo Photo Organizer (예정)

![이미지](/assets/img/2024-05-27-AppsusingComposeMultiplatformoniOSnowbetain2024developerinsights_4.png)

Ashampoo Photos의 iOS 첫 버전은 SwiftUI로 작성되었습니다. Compose Multiplatform이 사용 가능해지자 개발자는 개발 방향을 변경했습니다. 현재 작성 중인 이 포스트에서 새 버전은 Compose UI로 구축되어 테스트 중이며 아직 사용할 수 없습니다. 그러나 Compose Multiplatform이 모바일 플랫폼뿐만 아니라 Windows(그리고 곧 MacOS)에서도 실행되므로 소프트웨어를 [여기](https://www.ashampoo.com/photo-organizer)에서 사용할 수 있습니다.

<div class="content-ad"></div>

아샴푸 개발자 Stefan Oltmann님

앱스토어: 곧 공개 예정
플레이 스토어: https://play.google.com/store/apps/details?id=com.ashampoo.photos

# 오픈 소스: KotlinConf (5천 명 이상), Twine RSS Reader (1만 명 이상) 및 FindTravelNow

이 목록의 마지막 세 개의 앱은 오픈 소스입니다! 정확히 어떻게 구축되었는지 궁금하다면 꼭 확인해보세요. 먼저 공식 KotlinConf 앱부터 차례로 살펴보겠습니다!

<div class="content-ad"></div>

![image](/assets/img/2024-05-27-AppsusingComposeMultiplatformoniOSnowbetain2024developerinsights_5.png)

KotlinConf  
App Store: [link](https://apps.apple.com/us/app/kotlinconf/id1299196584)  
Play Store: [link](https://play.google.com/store/apps/details?id=com.jetbrains.kotlinconf)  
Source code: [link](https://github.com/JetBrains/kotlinconf-app)

Then Twine, which is an RSS reader with 10K+ downloads:

![image](/assets/img/2024-05-27-AppsusingComposeMultiplatformoniOSnowbetain2024developerinsights_6.png)

<div class="content-ad"></div>

Twine — RSS 리더
앱 스토어: [링크](https://apps.apple.com/us/app/twine-rss-reader/id6465694958)
플레이 스토어: [링크](https://play.google.com/store/apps/details?id=dev.sasikanth.rss.reader)
소스 코드: [링크](https://github.com/msasikanth/twine)

그리고 마지막으로, FindTravelNow은 전 세계에서 저렴한 항공권을 찾기 위한 "메타서치 여행 애플리케이션"입니다:

![이미지](/assets/img/2024-05-27-AppsusingComposeMultiplatformoniOSnowbetain2024developerinsights_7.png)

FindTravelNow
앱 스토어: [링크](https://apps.apple.com/gr/app/findtravelnow/id6471192930)
플레이 스토어: [링크]https://play.google.com/store/apps/details?id=com.travelapp.findtravelnow)
소스 코드: [링크](https://github.com/mirzemehdi/FindTravelNow-KMM)

<div class="content-ad"></div>

# 마무리

이 라운드업이 크로스 플랫폼 개발로 나아가고 싶어하는 분들에게 영감을 주기를 바랍니다. 일반적으로 Kotlin Multiplatform처럼, 만약 시도해보고 중단하려고 한다면 멀티플랫폼 부분을 더 나아가지 않기로 결정해도 항상 작동하는 현대적인 Android 앱을 가지고 계실 것입니다.

시작하는 방법에 대한 더 많은 정보를 얻으려면 JetBrains 사이트를 확인해보세요: https://www.jetbrains.com/lp/compose-multiplatform/ 그리고 오늘의 KotlinConf 키노트에서 Compose iOS 베타 발표를 확인해보세요.

이 기사의 핵심 개발자 및 커뮤니티에 유용한 정보를 제공해 준 분들에게 특별히 감사드립니다 🚀.