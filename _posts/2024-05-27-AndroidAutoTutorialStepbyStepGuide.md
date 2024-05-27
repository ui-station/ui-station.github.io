---
title: "안드로이드 오토 튜토리얼 단계별 가이드"
description: ""
coverImage: "/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_0.png"
date: 2024-05-27 16:33
ogImage:
  url: /assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_0.png
tag: Tech
originalTitle: "Android Auto Tutorial Step by Step Guide"
link: "https://medium.com/proandroiddev/android-auto-tutorial-step-by-step-guide-50bb6b73e2b8"
---


![Android Auto](/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_0.png)

# 안드로이드 오토란?

안드로이드 오토는 안드로이드 폰과 안드로이드 오토 앱을 가진 사용자들을 위한 운전자 최적화 앱 경험을 제공합니다. 호환되는 자동차에 연결된 안드로이드 스마트폰의 확장판으로, 일부 앱, 엔터테인먼트, 그리고 자동차 대시보드에 메시지를 표시할 수 있습니다. USB 또는 블루투스를 사용하여 기기를 연결할 수 있습니다.

![Android Auto Tutorial Step by Step Guide](/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_1.png)


<div class="content-ad"></div>

차량에 핸드폰을 연결하면 모든 Android Auto 호환 앱을 사용할 수 있습니다.

# Android Automotive OS는 무엇인가요?

Android Automotive OS는 차량에 내장된 안드로이드 기반 인포테인먼트 시스템입니다. 차량 시스템은 운전용으로 최적화된 독립형 안드로이드 장치입니다. Android Automotive OS를 사용하면 사용자가 앱을 핸드폰이 아닌 차량에 직접 설치할 수 있습니다.

![Android Auto Tutorial Step by Step Guide](/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_2.png)

<div class="content-ad"></div>

# 안드로이드 오토 및/또는 안드로이드 오토모티브 OS는 다음 유형의 앱을 지원합니다:

- 미디어 앱 — 오디오: 미디어 앱을 사용하면 사용자가 차 안에서 음악, 라디오, 오디오북 및 기타 오디오 콘텐츠를 찾아 재생할 수 있습니다.
- 메시징 앱: 메시징 앱을 통해 사용자는 수신 알림을 받고 텍스트 음성 변환을 사용하여 메시지를 소리내어 읽거나 음성 입력을 통해 답장을 보낼 수 있습니다.
- 내비게이션 앱: 운전 및 배송 서비스 제공 업체를 포함한 내비게이션 앱은 차원소를 제공하여 사용자가 목적지에 쉽게 도달할 수 있도록 도와줍니다.
- 관심지역(POI) 앱: POI 앱을 사용하면 사용자가 관심 지역을 발견하고 찾아갈 수 있으며 주차, 충전 및 연료 앱과 같은 관련 작업을 수행할 수 있습니다.
- 사물인터넷(IOT) 앱: IOT 앱을 사용하면 사용자가 차 안에서 연결된 기기에 관련 작업을 수행할 수 있습니다.
- 비디오 앱(주차 시에만 사용): 비디오 앱을 사용하면 차가 주차된 상태에서 스트리밍 비디오를 시청할 수 있습니다.
- 게임(주차 시에만 사용): 게임 앱을 사용하면 차가 주차된 상태에서 게임을 즐길 수 있습니다.

# Android Auto 장치를 탐색하고 에뮬레이터를 설정하기 위한 환경 구성

데스크톱 헤드 유닛(DHU)을 사용하면 개발 컴퓨터를 안드로이드 오토 헤드 유닛으로 에뮬레이션하여 안드로이드 오토 앱을 실행하고 테스트할 수 있습니다.

<div class="content-ad"></div>

DHU는 Windows, Mac OS 및 Linux 시스템에서 작동합니다.

아래 단계를 따라 Android Auto 에뮬레이터를 활성화하세요.

- Android 6.0 이상( API 레벨 23)을 실행하는 모바일 기기에서 개발자 모드를 활성화하세요.
- 앱을 컴파일하고 기기에 설치하세요.
- 기기에 Android Auto를 설치하세요. 이미 Android Auto가 설치되어 있는 경우, 최신 버전을 사용하는지 확인하세요.
- SDK Manager를 열고 SDK 도구 탭으로 이동한 다음 Android Auto 데스크톱 헤드유닛 에뮬레이터 패키지를 다운로드하세요.

![AndroidAutoTutorialStepbyStepGuide_3](/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_3.png)

<div class="content-ad"></div>

5. DHU는 SDK_LOCATION/extras/google/auto/ 디렉토리에 설치됩니다.

6. 리눅스 또는 맥 OS 시스템에서는 해당 디렉토리에서 다음 명령을 실행하여 DHU 실행 파일이 실행 가능한지 확인하세요:

```js
chmod +x ./desktop-head-unit
```

```js
./desktop-head-unit --usb
```

<div class="content-ad"></div>

7. 에뮬레이터가 작동을 시작하고 Android 장치에서 업데이트 관련 팝업이 표시되면 업데이트 옵션을 클릭하고 에뮬레이터를 다시 시작하세요.

![이미지](/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_4.png)

# Android Auto 디자인 템플릿

Android Auto에서는 사용자 정의 UI를 만들 수 없고 Android Auto 앱에 허용된 템플릿 세트를 사용할 수 있습니다.

<div class="content-ad"></div>

나에 따르면, 구글에서 제공하는 미리 정의된 UI 템플릿을 사용하면 운전 중에 안드로이드 오토 장치와 조화롭게 작동할 수 있어요.

## 사용 가능한 템플릿 목록:

- 탭 컨테이너 템플릿

![Tab Container Template](/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_5.png)

<div class="content-ad"></div>

아래는 마크다운 형식으로 변환한 표입니다.


- Tab bar with app icon and up to 4 tabs (no back button)
- Embedded template, which can be any of the following types: List, Grid, Search, Pane, or Message
- List or Grid Template

![Image](/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_6.png)


<div class="content-ad"></div>


![Image](/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_7.png)

- Message or Long Message Template

![Image](/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_8.png)

- Search Template


<div class="content-ad"></div>


![이미지](/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_9.png)

- Place List (map) Template
- Navigation Template

# 자동차용 미디어 앱을 만들기 위한 단계

- Manifest 파일에서 Android Auto 지원 선언하기


<div class="content-ad"></div>


![Step 10](/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_10.png)

![Step 11](/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_11.png)

- 미디어 브라우저 서비스를 선언하세요

![Step 12](/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_12.png)


<div class="content-ad"></div>

# 안드로이드 오토가 미디어 브라우저 서비스와 상호 작용하는 방법:

![AndroidAutoTutorialStepbyStepGuide_13](/assets/img/2024-05-27-AndroidAutoTutorialStepbyStepGuide_13.png)

- 사용자가 안드로이드 오토에서 앱을 실행하면, 안드로이드 오토가 onCreate() 메서드를 사용하여 앱의 미디어 브라우저 서비스에 연락합니다. onCreate() 메서드를 구현할 때는 MediaSessionCompat 객체와 콜백 객체를 생성하고 등록해야 합니다.
- 안드로이드 오토가 서비스의 onGetRoot() 메서드를 호출하여 콘텐츠 계층 구조에서 루트 미디어 항목을 가져옵니다. 모든 것은 루트에서 시작하며, MediaBrowserServiceCompat에 연결을 허용하려면 null이 아닌 BrowserRoot를 반환해야 합니다.
- 안드로이드 오토가 서비스의 onLoadChildren() 메서드를 호출하여 루트 미디어 항목의 자식 항목을 가져옵니다. 안드로이드 오토는 이러한 미디어 항목을 컨텐츠 항목의 최상위로 표시합니다. FLAG_PLAYABLE 및 FLAG_BROWSABLE 두 가지 사용 가능한 플래그가 있으며, 미디어 항목이 직접 재생될 수 있는지 또는 자체 자식 항목이 있는지를 나타냅니다.
- 사용자가 브라우저 가능한 미디어 항목을 선택하면, 선택한 메뉴 항목의 자식 항목을 다시 검색하기 위해 서비스의 onLoadChildren() 메서드가 호출됩니다.
- 사용자가 재생 가능한 미디어 항목을 선택하면, 안드로이드 오토는 해당 작업을 수행하기 위해 적절한 미디어 세션 콜백 메서드를 호출합니다.

예시: 음악 항목을 재생하게 됩니다.

<div class="content-ad"></div>

# 안드로이드 오토에서 미디어 앱을 지원하는 필수 단계:

## 표준 재생 작업 설정

안드로이드 오토는 PlaybackStateCompat 객체에서 활성화된 작업에 따라 재생 컨트롤을 표시합니다.

기본적으로 앱은 다음 작업을 지원해야 합니다:

<div class="content-ad"></div>

- ACTION_PLAY
- ACTION_PAUSE
- ACTION_STOP
- ACTION_PLAY_FROM_MEDIA
- ACTION_PLAY_FROM_SEARCH

If applicable to your app's content, you can also consider supporting the following actions:

- ACTION_SKIP_TO_PREVIOUS
- ACTION_SKIP_TO_NEXT

The MCT includes tests for the following media actions:

<div class="content-ad"></div>

- Play
- Play From Search
- Play From Media ID
- Play From URI
- Pause
- Stop
- Skip To Next
- Skip To Previous
- Skip To Queue Item
- Seek To

## 음성 명령 지원

당신의 미디어 앱은 안전하고 편리한 경험을 제공하기 위해 운전자에게 방해가 되지 않도록 도와주는 음성 명령을 지원해야 합니다. Android Auto가 음성 명령을 감지하고 해석하면 해당 음성 명령은 onPlayFromSearch()를 통해 앱으로 전달됩니다. 이 콜백을 받은 후 앱은 쿼리 문자열과 일치하는 내용을 찾아 재생을 시작합니다.

## 사용자 정의 재생 조작

<div class="content-ad"></div>

미디어 앱이나 메시징 앱, 내비게이션 주차 앱 등이라면 Android Auto 플랫폼을 지원하는 것이 좋은 것 같아요. 사용자가 운전 중에 스크린을 보지 않고도 음성 명령으로 어플을 사용할 수 있도록 하는 것이 유용할 거예요.

<div class="content-ad"></div>

지금까지 그럼 이만 마치겠습니다. 향후 글에서는 안드로이드 오토(Android Auto) 및 안드로이드 오토모티브(Android Automotive) 구현 예제 앱을 공유할 예정입니다.

UAMP 미디어 앱은 안드로이드 오토(Android Auto) 및 안드로이드 오토모티브(OS)를 모두 탐색할 수 있는 매우 유용한 저장소입니다.

이 글을 읽어 주셔서 감사합니다. 만약 이 게시물이 유용하고 흥미로웠다면 👏 클릭하고 추천해 주세요.

제 소셜 미디어 및 기타 플랫폼에서 저에게 연락하거나 최신 소식을 확인하세요: [https://linktr.ee/droiddikshit](https://linktr.ee/droiddikshit) 🤝

<div class="content-ad"></div>

참고 자료:

[Android Media app UAMP](https://github.com/android/uamp)
