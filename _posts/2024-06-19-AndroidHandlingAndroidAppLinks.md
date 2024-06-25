---
title: "안드로이드  안드로이드 앱 링크 처리하기"
description: ""
coverImage: "/assets/img/2024-06-19-AndroidHandlingAndroidAppLinks_0.png"
date: 2024-06-19 10:30
ogImage:
  url: /assets/img/2024-06-19-AndroidHandlingAndroidAppLinks_0.png
tag: Tech
originalTitle: "Android — Handling Android App Links"
link: "https://medium.com/@moboinfo/android-handling-android-app-links-51a33114f85d"
---

# 안드로이드 앱 링크는 사용자를 앱 내의 링크 특정 콘텐츠로 직접 이동시킵니다

![AndroidHandlingAndroidAppLinks_0.png](/assets/img/2024-06-19-AndroidHandlingAndroidAppLinks_0.png)

# 다양한 종류의 링크 이해하기

- 딥 링크
- 웹 링크
- 안드로이드 앱 링크

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

# 1. 딥 링크

# 딥 링크는 사용자를 앱의 특정 부분으로 직접 이동하는 모든 스키마의 URI입니다

```js
<activity
 android:name=".MyMapActivity"
 android:exported="true"
 …>
 <intent-filter>
 <action android:name="android.intent.action.VIEW" />
 <category android:name="android.intent.category.DEFAULT" />
 <category android:name="android.intent.category.BROWSABLE" />
 <data android:scheme="geo" />
 </intent-filter>
</activity>
```

# 2. 웹 링크

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

# 웹 링크는 HTTP 및 HTTPS 스키마를 사용하는 딥 링크입니다. 안드로이드 12 이상에서 웹 링크(안드로이드 앱 링크가 아닌)를 클릭하면 항상 웹 브라우저에서 콘텐츠가 표시됩니다.

# 🧑‍💻

```js
<intent-filter>
  <action android:name="android.intent.action.VIEW" />
  <category android:name="android.intent.category.DEFAULT" />
  <category android:name="android.intent.category.BROWSABLE" />
  <data android:scheme="http" />
  <data android:host="myownpersonaldomain.com" />
</intent-filter>
```

# 3. 안드로이드 앱 링크

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

# Android App Links, Android 6.0(API 레벨 23) 이상에서 사용할 수 있으며, HTTP 및 HTTPS 스키마를 사용하는 웹 링크이며 autoVerify 속성을 포함합니다.

## ⚡ 사용자가 Android 앱 링크를 클릭하면 설치되어 있으면 즉시 앱이 열리며 명확성 제거 대화 상자가 나타나지 않습니다.

## ⚡ 사용자가 앱을 기본 핸들러로 설정하고 싶지 않다면, 앱 설정에서 이 동작을 재정의할 수 있습니다.

## 🧑‍💻

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
<intent-filter android:autoVerify="true">
  <action android:name="android.intent.action.VIEW" />
  <category android:name="android.intent.category.DEFAULT" />
  <category android:name="android.intent.category.BROWSABLE" />
  <data android:scheme="http" />
  <data android:scheme="https" />
  <data android:host="myownpersonaldomain.com" />
</intent-filter>
```

# 👍 Android 앱 링크는 다음과 같은 이점을 제공합니다:

# 🤔 안전하고 특정: Android 앱 링크는 소유한 웹 사이트 도메인으로 링크된 HTTP URL을 사용하기 때문에 다른 앱이 여러분의 링크를 사용할 수 없습니다.

# 🤔 신뢰할 수 있는 사용자 경험: Android 앱 링크는 웹 사이트와 앱에서 동일한 콘텐츠에 대해 단일 HTTP URL을 사용하므로 앱이 설치되지 않은 사용자는 앱이 아닌 웹 사이트로 이동하게 됩니다. 404 오류나 오류가 발생하지 않습니다.

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

# 참고 링크:

- [앱 링크 소개](About App Links)
- [딥 링크 생성](Create Deep Links)

# [www.moboinfo.com](www.moboinfo.com)

Linkedin: [https://www.linkedin.com/in/devprithvi/](https://www.linkedin.com/in/devprithvi/)

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

깃허브: https://github.com/devprithvi

유튜브: https://www.youtube.com/@moboinfo

#딥링크 #안드로이드 #안드로이드딥링크
