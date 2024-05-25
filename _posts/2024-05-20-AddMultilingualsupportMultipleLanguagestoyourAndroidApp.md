---
title: "당신의 안드로이드 앱에 다국어 지원다국어 추가하기"
description: ""
coverImage: "/assets/img/2024-05-20-AddMultilingualsupportMultipleLanguagestoyourAndroidApp_0.png"
date: 2024-05-20 17:37
ogImage: 
  url: /assets/img/2024-05-20-AddMultilingualsupportMultipleLanguagestoyourAndroidApp_0.png
tag: Tech
originalTitle: "Add Multilingual support (Multiple Languages) to your Android App"
link: "https://medium.com/proandroiddev/add-multilingual-support-multiple-languages-to-your-android-app-4c0fd23cbdb8"
---



![다국어 지원 이미지](/assets/img/2024-05-20-AddMultilingualsupportMultipleLanguagestoyourAndroidApp_0.png)

여러 언어를 지원하는 것은 애플리케이션을 확장하고 대중에 도달하는 데 중요합니다. 인도의 약 25%와 유럽의 64%의 작업 성인 인구가 다국어를 구사하며 미국도 다국어 구사자가 약 194% 증가했습니다. (출처)

또한, 소비자의 65% 이상이 선호하는 언어로 콘텐츠를 소비하는 것으로 나타나므로 이는 Amazon, WhatsApp, Facebook 등 대부분의 선도적인 애플리케이션에서 이미 제공되는 중요한 기능으로 고려되어야 합니다.

그러니 다국어 세계를 위해 함께 만들어봅시다!


<div class="content-ad"></div>

## 1. 문자열 리소스를 체계화하세요.

코드에서 하드코딩된 문자열 값을 사용하지 마세요.

올바르게

```js
Text(stringResources(R.string.follow_me))
```

<div class="content-ad"></div>

Using strings.xml, we will have a common place for all our string resources and we can then support multiple languages by adding more strings.xml files.

## 2. Add Multiple Languages

<div class="content-ad"></div>

이미 모든 문자열 리소스를 저장할 수 있는 곳이 있습니다. 이제 모든 문자열 리소스를 다른 지원되는 언어로 번역하기만 하면 됩니다.

```js
//힌디어 문자열 리소스 파일 예제
<resources>
    <string name="subscribe_to_sagar_malhotra">सागर मल्होत्रा की सदस्यता लें</string>
    <string name="language">हिन्दी</string>
</resources>
```

이 작업을 수행하기 위해 "AndroidLocalize"라는 Android Studio 플러그인을 사용하고 있습니다.

![이미지](/assets/img/2024-05-20-AddMultilingualsupportMultipleLanguagestoyourAndroidApp_1.png)

<div class="content-ad"></div>

## 3. 언어 변경 트리거

어떤 UI를 사용하더라도(여기서는 ExposedDropDownMenu), 애플리케이션의 로캘을 변경했음을 OS에 알리기 위해 onClick 이벤트를 트리거해야 합니다. 이렇게 하면 OS도 특정 언어의 strings.xml 파일로 전환할 수 있습니다.

```js
onClick = {
    // 사용자가 선택한 로캘에 따라 앱 로캘 설정
    AppCompatDelegate.setApplicationLocales(
        LocaleListCompat.forLanguageTags(
            "hi"// 힌디어를 위한 ISO 코드
        )
    )
}
```

<div class="content-ad"></div>


귀하는 해당 언어의 ISO-639 코드를 전달하여 OS에 언어 환경 설정 변경을 알릴 필요가 있습니다.

## 3.1 문제 해결

현재 이 방법은 AppCompatActivity에만 작동하므로 귀하의 애플리케이션에 맞지 않을 수도 있습니다. 특정 Activity에 ComponentActivity를 확장하고 있는지 확인하십시오.

```js
class MainActivity : AppCompatActivity() { ... }
```

<div class="content-ad"></div>

AppCompatActivity를 확장한 후에도, 특정 활동을 위해 지원되는 테마를 변경해야 합니다.

```js
<style name="Theme.MultilingualApp" parent="Theme.AppCompat.Light.NoActionBar" />
```

## 4. 로케일 설정 저장

Android 12 이하 버전에서는 선택한 언어 환경 값을 수동으로 저장하거나 AndroidX가 로케일 환경을 스스로 처리하도록 AndroidManifest에서 이 구성을 사용해야 합니다.

<div class="content-ad"></div>

```bash
<service//Inside application tag
    android:name="androidx.appcompat.app.AppLocalesMetadataHolderService"
    android:enabled="false"
    android:exported="false">
    <meta-data
        android:name="autoStoreLocales"
        android:value="true" />
</service>
```

## 5. Android OS Per-App Language Preferences

In Android 13 and above, the Android OS also supports changing the Per-App Language preference from system settings.

<img src="/assets/img/2024-05-20-AddMultilingualsupportMultipleLanguagestoyourAndroidApp_2.png" />


<div class="content-ad"></div>

귀하의 응용 프로그램이 여러 언어도 지원한다는 것을 운영 체계에 알리려면 AndroidManifest 파일에 필요한 구성을 추가하십시오.

```js
// application 태그에 다음을 추가하세요
android:localeConfig="@xml/locale_config"
```

local_config.xml 파일에 모든 지원하는 언어를 정의하세요.

```js
<?xml version="1.0" encoding="utf-8"?>
<locale-config xmlns:android="http://schemas.android.com/apk/res/android">
    <locale android:name="en" />
    <locale android:name="gu" />
    <locale android:name="hi" />
    <locale android:name="ar-AE" />
</locale-config>
```

<div class="content-ad"></div>

이제 앱에서 시스템 설정에서 언어를 변경하는 것도 지원하게 됩니다.

## 6. 활동 재생성 피하기

앱 언어를 변경할 때 활동이 재생성되는 것을 눈치챌 수 있습니다. 이는 로캘을 변경하는 것도 구성 변경의 한 종류이기 때문에 기본적으로 활동이 구성 변경이 발생할 때마다 재생성됩니다.

AndroidManifest를 사용하여 이 기본 동작을 방지할 수 있습니다.

<div class="content-ad"></div>


// 특정 활동 태그에 다음을 추가하십시오
android:configChanges="layoutDirection|locale"


## 비디오:

이 기능이 보다 많은 관객에 도움이 되기를 바라며, 더 많은 유용한 콘텐츠를 위해 저를 팔로우하시기 바랍니다.
