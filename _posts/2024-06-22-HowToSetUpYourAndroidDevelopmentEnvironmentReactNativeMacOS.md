---
title: " Mac OS에서 React Native를 사용한 Android 개발 환경 설정 방법"
description: ""
coverImage: "/assets/img/2024-06-22-HowToSetUpYourAndroidDevelopmentEnvironmentReactNativeMacOS_0.png"
date: 2024-06-22 23:31
ogImage:
  url: /assets/img/2024-06-22-HowToSetUpYourAndroidDevelopmentEnvironmentReactNativeMacOS_0.png
tag: Tech
originalTitle: "📱 How To Set Up Your Android Development Environment (React Native , Mac OS)"
link: "https://medium.com/@tiaeastwood/how-to-set-up-your-android-development-environment-react-native-mac-os-b2727b8b4f3f"
---

![Image](/assets/img/2024-06-22-HowToSetUpYourAndroidDevelopmentEnvironmentReactNativeMacOS_0.png)

새로운 기계를 설정하는 것에는 특별한 즐거움이 있다고 생각해요... 그러나 개발 환경을 다시 구성해야 한다는 것을 깨닫게 되면 조금 번거로울 수도 있죠! 제가 직접 단계를 거치면서 Android 개발 환경을 설정하는 방법에 대한 안내서를 작성해 보았어요 (특히 macOS에서 React Native 애플리케이션을 위해). 새로운 기계를 설정하려는 것이거나 간단히 상기시키고 싶을 때, 이 안내서가 도움이 되리라고 생각돼요. 더 이상 미루지 말고 지금 시작해볼까요?

# 단계 1: Homebrew 설치하기

Homebrew는 macOS용 패키지 매니저로 소프트웨어 설치를 간편하게 해줘요. 아직 설치하지 않았다면, 터미널을 열고 다음을 실행하세요:

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

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

# 단계 2: Node, Watchman 및 Java 설치

다음으로 Node 및 Watchman이 필요합니다. 또한 Java도 필요합니다. Node의 경우 NVM (Node Version Manager)을 사용하여 설치하는 것을 극력 드립니다. 이를 통해 다른 프로젝트에 필요한 다양한 노드 버전을 쉽게 관리할 수 있습니다.

- 이러한 모든 것을 설치하려면 Homebrew를 사용할 수 있습니다.

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

```js
brew install nvm
// 또는 nvm을 사용하지 않을 경우:
brew install node
brew install watchman
```

- NVM을 선택했다면, 이제 NVM을 통해 원하는 버전의 node를 설치하거나 최신 버전을 설치하세요.

```js
nvm install node
```

- 그런 다음 Java를 다운로드하고 설치하세요.

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

# 단계 3: 안드로이드 스튜디오 설치

https://developer.android.com/studio 에서 안드로이드 스튜디오를 다운로드하고 설치하세요. 설정 중에 다음을 선택하세요:

- Android SDK
- Android SDK 플랫폼
- Android 가상 장치

체크 박스가 보이지 않는 경우 나중에 안드로이드 스튜디오의 SDK 관리자를 통해 이러한 구성 요소를 설치할 수 있습니다.

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

# 단계 4: JAVA_HOME 변수 설정하기

- 터미널을 엽니다
- bash를 사용 중이라면:

```js
echo export "JAVA_HOME=\$(/usr/libexec/java_home)" >> ~/.bash_profile
```

- macOS Catalina 이상을 실행 중이라면 zsh를 사용 중이라고 가정하는 경우 대신 다음과 같이해야 합니다:

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

```js
echo export "JAVA_HOME=\$(/usr/libexec/java_home)" >> ~/.zshrc
```

- 그런 다음 source ~/.bash_profile 또는 source ~/.zshrc를 사용하여 셸을 다시 시작합니다.

# 단계 5: ANDROID_HOME 환경 변수 구성

React Native는 Android SDK의 위치를 알아야합니다. 이는 ANDROID_HOME 환경 변수를 설정하여 달성됩니다.

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

- 터미널을 엽니다.
- 파일을 만들거나 이미 있는 경우 파일을 엽니다:
- 만약 bash를 사용하는 경우: touch ~/.bash_profile
- 혹은 zsh를 사용하는 경우: touch ~/.zshrc
- 그런 다음, open -e ~/.bash_profile을 실행하여 TextEdit에서 파일을 엽니다.
- 다음 줄을 파일에 추가하고 저장하세요:

```js
export ANDROID_HOME=$HOME/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/tools/bin
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

# 단계 6: 가상 장치 생성 및 구성

앱을 실행할 Android Virtual Device(AVD)가 필요합니다.

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

- 안드로이드 스튜디오를 열어주세요.
- 상단 메뉴에서 Device Manager를 클릭해주세요.
- "장치 생성"을 클릭해주세요 (Play Store 구매를 테스트하려면 Play Store를 지원하는 장치를 선택해야 합니다).
- 장치 정의를 선택하고 "다음"을 클릭해주세요.
- 시스템 이미지를 선택하고 "다음"을 클릭해주세요.
- 구성을 확인하고 "완료"를 클릭해주세요.

# 7단계: React Native 설치

[React Native 문서](https://reactnative.dev/docs/environment-setup?guide=native)의 지침을 따라주세요 (저는 Bare React Native을 사용하고 있지만 Expo에 대한 지침도 있습니다).

# 8단계: 새 애플리케이션 생성

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

모두 준비되었습니다! 다음 명령을 실행하여 새로운 React Native 애플리케이션을 만들 수 있어요:

```js
react-native init MyNewApp

// 애플리케이션 이름으로 "MyNewApp"을(를) 교체하세요.
```

Android 가상 장치에서 React Native 애플리케이션을 실행하려면:

- AVD가 실행 중인지 확인하세요. Android Studio의 AVD 관리자에서 확인할 수 있어요.
- 터미널이나 코드 편집기에서 npm start 명령을 실행하세요.
- 그런 다음 Android에서 실행하려면 "a"를 입력하세요.

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

여기요! 지금 모두 잘 되고 있기를 바라요. 앱 만드는 즐거움이 가득하길!

언스플래시(Unsplash)에서 로브 햄프슨(Rob Hampsono) 사진으로 제공된 사진을 표지로 사용

원문 게시 위치: https://www.tiaeastwood.com.
