---
title: "맥용 플러터 설정하기"
description: ""
coverImage: "/assets/img/2024-05-23-SettingupFlutterforMacOS_0.png"
date: 2024-05-23 15:18
ogImage: 
  url: /assets/img/2024-05-23-SettingupFlutterforMacOS_0.png
tag: Tech
originalTitle: "Setting up Flutter for MacOS"
link: "https://medium.com/@redmundnacario/setting-up-flutter-for-macos-9249a24ee5d8"
---


이 튜토리얼은 MacOS 기기에 플러터를 설치하는 방법을 안내해드립니다. 맥에서 플러터를 사용하는 장점은 안드로이드 및 iOS 앱을 모두 개발할 수 있는 능력입니다.

# 단계

- 다음 명령을 사용하여 터미널을 통해 플러터를 설치합니다:

```js
brew install --cask flutter
```

<div class="content-ad"></div>

다트와 플러터를 "/usr/local/bin"에 설치할 것입니다.

2. 설치를 확인하세요. 다음 명령을 사용하여 누락된 SDK 도구나 요구 사항이 있는지 확인할 수 있습니다.

```js
flutter doctor
```

만약 플러터, 다트 및 필요한 도구를 설치했다면 아래 표시된 이미지처럼 보일 것입니다. 초기에는 일부 도구가 누락될 수 있습니다. Step #3를 완료하면 확인 표시가 되어 있을 것입니다.

<div class="content-ad"></div>

![image](/assets/img/2024-05-23-SettingupFlutterforMacOS_0.png)

3. Android 및 IOS 앱 개발에 필요한 도구 설치하기. 아래의 다음 섹션을 참조하세요.

4. 선택한 IDE에 대한 확장 기능 설치하기.

- Flutter 확장 기능이 포함된 Visual Studio Code 1.77 이상.
- IntelliJ용 Flutter 플러그인이 포함된 Android Studio 2023.1(하지호그) 이상.
- IntelliJ 및 Android 플러그인이 모두 포함된 IntelliJ IDEA 2023.1 이상.

<div class="content-ad"></div>

# 안드로이드 개발을 위해

- 브라우저를 통해 Android Studio를 다운로드하여 설치하세요.

- 해당 링크로 이동하세요: https://developer.android.com/studio

2. 설치한 후에 Android Studio의 SDK 매니저로 이동하세요.

<div class="content-ad"></div>

3. SDK Tools 탭 아래로 이동하여 SDK 명령줄 도구를 활성화하세요. 적용 버튼을 눌러주세요.

![이미지](/assets/img/2024-05-23-SettingupFlutterforMacOS_1.png)

4. 터미널로 돌아가서 라이센스를 수락하는 명령어를 실행하세요.

```js
flutter doctor --android-licenses
```

<div class="content-ad"></div>

5. Flutter doctor를 다시 실행하여 설정이 제대로 되었는지 확인해보세요.

6. Android Emulator를 설정하세요.

   - Android Emulator는 이미 Android Studio에 포함되어 있습니다. 필요한 것은 여기에 나와 있는 단계를 따르는 것 뿐입니다: macOS에서 Flutter Android 앱 빌드 시작하기

   - 참고: 링크의 1단계에서 VM 가속기는 더 이상 MacOS에서 지원되지 않음을 유념해주세요.

<div class="content-ad"></div>

안녕하세요! 안드로이드 에뮬레이터를 설정하면 구성에 따라 가상 안드로이드 장치가 표시됩니다. 이 에뮬레이터에서는 플러터 앱을 실행할 수 있어요.

![이미지](/assets/img/2024-05-23-SettingupFlutterforMacOS_2.png)

# iOS 개발을 위해

개발 도구를 설치하세요.

<div class="content-ad"></div>

- Xcode (버전 15 이상) — 네이티브 Swift 또는 ObjectiveC 코드를 디버그하고 컴파일합니다.

a. MacBook에서 앱 스토어를 열고 Xcode를 검색하세요.

b. 그런 다음 "get"과 "install" 버튼을 클릭하세요. 이 작업을 계속하려면 Apple ID로 로그인해야 합니다. 설치에는 시간이 소요될 수 있습니다.

c. 터미널에서 이 라인을 실행하세요. 이렇게 하면 명령줄 도구가 설치된 Xcode 버전을 사용하도록 구성됩니다.

<div class="content-ad"></div>

```js
sudo sh -c 'xcode-select -s /Applications/Xcode.app/Contents/Developer && xcodebuild -runFirstLaunch'
```

터미널에서 “agree”를 입력하라는 안내가 표시됩니다.

c. 터미널에서 다음 줄을 실행하세요. xcode 라이선스 동의서에 서명하십시오.

```js
sudo xcodebuild -license
```

<div class="content-ad"></div>

터미널에서 "동의"를 입력하라는 프롬프트가 표시될 것입니다.

2. 코코아팟 (1.13 또는 그 이상) - 네이티브 앱에서 플러터 플러그인을 컴파일하는 데 사용됩니다.

  - 설치하려면 터미널에서 다음 줄을 실행하세요.

```js
sudo gem install cocoapods
```

<div class="content-ad"></div>

3. IOS 시뮬레이터 — 플러터 앱을 가상 IOS에서 표시합니다.

a. IOS 시뮬레이터 설치하기 — 시간이 걸릴 수 있습니다

```js
xcodebuild -downloadPlatform iOS
```

b. 시뮬레이터 실행하기

<div class="content-ad"></div>

```js
open -a Simulator
```

심레이터를 실행하면 가상 아이폰이 표시되는 창 또는 팝업이 열립니다.

<img src="/assets/img/2024-05-23-SettingupFlutterforMacOS_3.png" />

# 보너스:

<div class="content-ad"></div>

디버그하는 방법을 알려드릴게요!

- VSCode에서는 flutter 애플리케이션을 디버그하기 위해 개발자 도구로 이동할 수 있습니다. cmd + Shift + P를 누르고 "Flutter: Open DevTools"를 입력하면 디버깅에 사용할 수 있는 개발 도구 목록이 표시됩니다:

![이미지](/assets/img/2024-05-23-SettingupFlutterforMacOS_4.png)

출처:

<div class="content-ad"></div>

- [https://docs.flutter.dev/get-started/install/macos](https://docs.flutter.dev/get-started/install/macos)