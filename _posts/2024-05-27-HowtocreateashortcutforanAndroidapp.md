---
title: "안드로이드 앱을 위한 바로 가기 생성 방법"
description: ""
coverImage: "/assets/img/2024-05-27-HowtocreateashortcutforanAndroidapp_0.png"
date: 2024-05-27 16:13
ogImage: 
  url: /assets/img/2024-05-27-HowtocreateashortcutforanAndroidapp_0.png
tag: Tech
originalTitle: "How to create a shortcut for an Android app."
link: "https://medium.com/@charles.raj412/how-to-create-a-shortcut-for-an-android-app-in-android-studio-kotlin-950c3b9182ce"
---


```markdown
![image](/assets/img/2024-05-27-HowtocreateashortcutforanAndroidapp_0.png)

안녕 친구들,

안드로이드 애플리케이션에서 특정 작업이나 동작에 대한 바로 가기를 만드는 방법을 보여드릴 것을 기대합니다.

먼저 의존성을 추가해보겠습니다.
```

<div class="content-ad"></div>

앱/build.gradle.kts 파일에 아래 종속성을 추가해주세요.

```kotlin
implementation ("androidx.core:core:1.13.1")
implementation ("androidx.core:core-google-shortcuts:1.1.0")
```

그런 다음, 앱/src/main/AndroidManifest.xml 파일로 이동해서 아래의 메타 태그를 `activity` 태그 안에 추가해주세요.

```xml
<meta-data android:name="android.app.shortcuts"
           android:resource="@xml/shortcuts"/>
```

<div class="content-ad"></div>

이제 res/xml 폴더 안에 @xml/shortcuts 파일을 만들어야 해요. 아래 xml 파일에는 앱을 위한 정적 바로 가기가 정의되어 있습니다. 정적 영구 바로 가기를 만들고 싶다면 이 xml 파일에 바로 가기를 추가하면 돼요.

그리고 해당 액티비티로 이동하기 위한 Intent 도 추가해야 해요. targetPackage 이름이 앱의 패키지 이름과 일치하는지 확인해 주세요.

```js
android:targetPackage="com.task.master"
```

그렇게 하면 안드로이드 OS가 앱을 찾을 수 있어요. 그리고 바로 가기에 고유한 Id를 부여했는지 확인해 주세요.

<div class="content-ad"></div>

```markdown
<shortcuts xmlns:android="http://schemas.android.com/apk/res/android">

    <shortcut
        android:shortcutId="shortcut_1"
        android:enabled="true"
        android:icon="@drawable/tm_logo"
        android:shortcutShortLabel="@string/ShortCutShortLabel_1"
        android:shortcutLongLabel="@string/ShortCutLongLabel_1"
        android:shortcutDisabledMessage="@string/ShortCutDisableLabel_1"
        >

        <intent
            android:action="android.intent.action.VIEW"
            android:targetPackage="com.task.master"
            android:targetClass="com.task.master.presentation.ui.activities.MainActivity"
            >
                <extra
                    android:name="Type"
                    android:value="Create"
                    />
        </intent>
        <categories android:name="android.shortcut.conversation"/>
        <capability-binding android:key="action.intent.CREATE_MESSAGE"/>
    </shortcut>

    <shortcut
        android:shortcutId="shortcut_2"
        android:enabled="true"
        android:icon="@drawable/tm_logo"
        android:shortcutShortLabel="@string/ShortCutShortLabel_2"
        android:shortcutLongLabel="@string/ShortCutLongLabel_2"
        android:shortcutDisabledMessage="@string/ShortCutDisableLabel_2"
        >

        <intent
            android:action="android.intent.action.VIEW"
            android:targetPackage="com.task.master"
            android:targetClass="com.task.master.presentation.ui.activities.MainActivity"
            >
                <extra
                    android:name="Type"
                    android:value="View"
                    />
        </intent>
        <categories android:name="android.shortcut.conversation"/>
        <capability-binding android:key="action.intent.CREATE_MESSAGE"/>
    </shortcut>

</shortcuts>
```

안드로이드 앱에 파일 및 코드를 추가한 후 xml/shortcuts.xml에 정의한 바로가기가 표시됩니다.

이제 액티비티에서 동적으로 바로 가기를 추가하는 방법을 보여드리겠습니다. ShortcutInfoCompat을 사용하여 동적으로 바로 가기를 추가할 수 있고 ShortcutManagerCompat을 사용하여 해당 바로 가기를 푸시할 수 있습니다. 아래 코드를 확인하여 동적 바로 가기를 추가하는 방법을 알아보세요.

```markdown
  val context = this.baseContext
        val shortcut = ShortcutInfoCompat.Builder(context, "dynamic_Id")
            .setShortLabel("Dynamic Item")
            .setLongLabel("Dynamic Item")
            .setIcon(IconCompat.createWithResource(context, R.drawable.profile_icon))
            .setIntent(
                Intent(context,MainActivity::class.java).apply {
                    action = Intent.ACTION_VIEW
                    putExtra("Type", "")
                }
            )
            .build()
        ShortcutManagerCompat.pushDynamicShortcut(context, shortcut)
```

<div class="content-ad"></div>

이번 단계 이후에는 모든 활동에서 바로 가기를 추가할 수 있으며 서버 응답에 따라 응용 프로그램에 바로 가기를 추가할 수도 있습니다.

결론

바로 가기를 사용하여 사용자 상호 작용을 개선하고 사용자가 특정 섹션으로 빠르게 이동할 수 있습니다.

소스 코드는 여기에서 찾을 수 있습니다

<div class="content-ad"></div>

감사합니다