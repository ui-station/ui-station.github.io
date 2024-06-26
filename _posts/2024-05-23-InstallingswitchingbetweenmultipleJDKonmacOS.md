---
title: "맥OS에서 여러 JDK를 설치하고 전환하는 방법"
description: ""
coverImage: "/assets/img/2024-05-23-InstallingswitchingbetweenmultipleJDKonmacOS_0.png"
date: 2024-05-23 15:17
ogImage:
  url: /assets/img/2024-05-23-InstallingswitchingbetweenmultipleJDKonmacOS_0.png
tag: Tech
originalTitle: "Installing , switching between multiple JDK on macOS"
link: "https://medium.com/@manvendrapsingh/installing-many-jdk-versions-on-macos-dfc177bc8c2b"
---

![JDK installation on macOS](/assets/img/2024-05-23-InstallingswitchingbetweenmultipleJDKonmacOS_0.png)

모든 운영 체제에는 macOS를 포함한 사전 설치된 JDK가 없습니다. 이 게시물에서는 macOS에 여러 JDK를 수동으로 설치하고 관리하는 방법을 살펴보겠습니다.

macOS에서 소프트웨어를 설치하는 잘 알려진 과정은 앱 아이콘을 클릭하거나 앱 아이콘을 Applications 폴더로 끌어다 놓는 것입니다. 이는 모든 설치 세부 정보를 멋진 앱 아이콘과 진행중인 바 아래에 숨깁니다.

그러나 우리 개발자들은 로그를 보고 명령줄 도구를 사용하는 것을 좋아합니다. 이를 위해 Linux 배포판은 yum이나 apt-get과 같은 패키지 관리자를 사용합니다. 그러나 모든 것이 Apple로 이어지듯이 가장 일반적인 무료 소프트웨어는 macOS에서 작동하지 않습니다. 여기서 HomeBrew가 구원의 역할을 합니다.

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

# HomeBrew

도구의 홈페이지에 따르면,

```js
macOS를 위한 누락 된 패키지 관리자입니다.
Homebrew는 macOS에 포함되지 않은 UNIX 도구를 설치하는 가장 쉽고 유연한 방법입니다.
```

Homebrew를 사용하면 명령 줄을 통해 소프트웨어를 설치 할 수 있으며 로그에서 많은 설치 정보를 볼 수 있습니다.

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

# Homebrew 설치하기

맥OS에 Homebrew를 설치하려면 맥OS의 Terminal 또는 iTerm 애플리케이션을 열고 아래 명령어를 실행하세요.

```js
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

이 작업이 완료되면 homebrew를 사용하여 사용 가능한 formulae 또는 cask를 한 줄로 설치할 수 있습니다. `brew install xxxx` 또는 `brew install --cask xxxx`와 같은 명령을 사용하세요.

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

# 다양한 JDK 버전 설치하기

먼저, Homebrew를 사용하여 사용 가능한 Java 버전을 찾아보겠습니다. 다음 명령어를 사용해주세요.

```bash
brew search --formulae java
```

아래에서 확인할 수 있듯이, java11과 java만 사용 가능합니다.

![이미지](/assets/img/2024-05-23-InstallingswitchingbetweenmultipleJDKonmacOS_1.png)

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

더 오래된 버전을 원하시면, openjdk.java 및 java11을 openjdk 및 openjdk@11의 별명으로 사용하여 검색할 수 있습니다.

![이미지](/assets/img/2024-05-23-InstallingswitchingbetweenmultipleJDKonmacOS_2.png)

이제 우리가 공식 이름을 알았으니, 하나의 명령어로 서로 다른 JDK를 설치할 수 있습니다. 최신 버전 및 java11을 설치해 보겠습니다.

터미널 또는 iTerm에 다음 2개의 명령어를 차례대로 실행해 주세요.

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
brew install openjdk
brew install openjdk@11
```

# 일부 수동 설정

이제 우리 Mac에는 자바 17과 자바 11이 모두 설치되어 있습니다.
Mac 프로그램에서 어떤 것을 사용할지 확인해 보겠습니다.

- 어떤 기기에서든 현재 자바 버전을 확인하는 가장 쉬운 방법은
  java -version을 사용하는 것입니다.
- macOS에 설치된 모든 자바 버전을 확인할 수도 있습니다. java_home /usr/libexec/java_home -V를 사용하세요.

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

그러나 위 2개의 명령어를 시도하면 다음 출력이 나옵니다.

<img src="/assets/img/2024-05-23-InstallingswitchingbetweenmultipleJDKonmacOS_3.png" />

어떤 이유에서인지 macOS는 Homebrew로 설치한 Java를 감지하지 못합니다. 이것은 Homebrew를 사용하여 패키지를 설치할 때 매번 나타나는 문제입니다.

- 패키지를 패키지 자체 디렉토리에 설치합니다.
  M1-Mac의 경우 /opt/homebrew/Cellar에
  Intel Mac의 경우 /usr/local/Cellar에
- /opt/homebrew/opt 아래에 심볼릭 링크도 생성합니다.

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

<img src="/assets/img/2024-05-23-InstallingswitchingbetweenmultipleJDKonmacOS_4.png" />

하지만 \*nix 시스템은 /usr/bin/java, /usr/lib/jvm 및 /usr/local/bin/java에서 Java를 찾습니다. Apple의 모든 것들과 마찬가지로 macOS는 다릅니다. Java를 /Library/Java/JavaVirtualMachines/에서 찾습니다.

이러한 JDK 설치법은 /Library/Java/JavaVirtualMachines/ 폴더 아래 필요한 softlink를 설정할 수 있었을 것입니다. 그러나 디자인상 이러한 JDK 설치법은 keg-only로 유지됩니다.

<img src="/assets/img/2024-05-23-InstallingswitchingbetweenmultipleJDKonmacOS_5.png" />

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

이 정보는 설치 로그에도 있습니다.

![이미지](/assets/img/2024-05-23-InstallingswitchingbetweenmultipleJDKonmacOS_6.png)

따라서 Mac 프로그램에서 설치된 Java를 감지하려면 해당 폴더에 몇 가지 소프트 링크를 만들어야 합니다. 설치 로그에 제공된 명령을 그대로 복사하여 붙여넣으십시오.

![이미지](/assets/img/2024-05-23-InstallingswitchingbetweenmultipleJDKonmacOS_7.png)

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

이 섹션의 시작 부분에서 실패한 명령을 실행하려고 하면 아래와 같은 출력이 나타납니다.

<img src="/assets/img/2024-05-23-InstallingswitchingbetweenmultipleJDKonmacOS_8.png" />

이제 macOS에서 감지된 2개의 JDK를 설치했으므로 두 가지 간을 빠르게 전환하는 방법을 살펴보겠습니다.

Java의 버전을 한 가지만 설치하는 경우에는 이미 끝났습니다. 그러나 Mac에 여러 가지 다른 버전의 Java를 설치하고자 하는 경우 위의 단계대로 각각 설치하고 아래 단계에 따라 그 사이를 전환할 수 있습니다.

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

# JDK 전환하기 (JAVA_HOME 및 java_home)

JAVA_HOME은 Java 프로그램이 Java 위치를 선택하도록 하는 환경 변수입니다. 따라서 다양한 Java 버전 간에 전환하려면 JAVA_HOME 값을 다른 위치로 변경해야 합니다.

또한 /usr/libexec/java_home 유틸리티가 있습니다. 이를 사용하여 다양한 버전 간에 전환합니다.

우리의 설치에서는 다음과 같이 매개 변수 -v (소문자 v)가 우리에게 제공하는 내용입니다.

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

`
![이미지](/assets/img/2024-05-23-InstallingswitchingbetweenmultipleJDKonmacOS_9.png)

- 이 명령어의 출력을 활용하여 JAVA_HOME을 아래와 같이 설정할 수 있습니다.
  export JAVA_HOME=`/usr/libexec/java_home -v 17`
- 그러나 명령어를 입력하는 것이 조금 길 수 있습니다. 그래서 전체 명령어를 대체할 수 있는 한 단어의 별칭을 만들어 보겠습니다.
  alias java-17=”export JAVA_HOME=`/usr/libexec/java_home -v 17`”
- 또한, 새 터미널을 열 때마다 이러한 별칭이 사용 가능하도록 해야 합니다. 이를 위해 ~/.zshrc 파일에 추가해야 합니다. ~/.zshrc 파일에 다음과 같이 2개의 별칭을 추가하세요. ~/.zshrc 파일이 존재하지 않는 경우, 파일을 생성하세요.

```js
alias java-17=”export JAVA_HOME=`/usr/libexec/java_home -v 17`; java -version”
alias java-11=”export JAVA_HOME=`/usr/libexec/java_home -v 11`; java -version”
```

모두 완료되었습니다.
원하는 때마다 터미널이나 iTerm에서 java-11 또는 java-17을 입력하세요.

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

![이미지](/assets/img/2024-05-23-InstallingswitchingbetweenmultipleJDKonmacOS_10.png)

# TL;DR (명령어 간단 요약)

설명이 아닌 단계만 원하는 분들을 위해 간단한 단계를 안내합니다.

- 시스템에 Homebrew가 없는 경우 Homebrew를 설치하세요.

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
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

- Homebrew 및 openjdk를 사용하여 필요한 만큼 JDK를 설치하세요.

```js
brew install openjdk@XX
```

- MAC에서 JDK에 액세스할 수 있도록 하려면, 소프트 링크나 실제 폴더를 /Library/Java/JavaVirtualMachines/에 추가하세요. 소프트 링크를 사용하려면 아래 명령어를 사용하세요.

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

$ sudo ln -sfn /opt/homebrew/opt/openjdkXXX/libexec/openjdkXXX.jdk /Library/Java/JavaVirtualMachines/openjdkXXX.jdk

- Add one more alias under `~/.zshrc` to quickly switch between JDK

alias java-XX="export JAVA_HOME=\`/usr/libexec/java_home -v XX\`; java -version"
